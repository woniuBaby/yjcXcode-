# Apple IAP（`App\Pay\IOS`）

`App\Pay\IOS` 实现 **Apple App Store 内购**的中台预下单、收据验单（legacy `verifyReceipt`）、**App Store Server API** 按 `transactionId` 验单发货，以及订阅客户端入口编排。与中台订单通过 `uc_order_id`、`mo_order_info_ext.extend`（Apple `product_id`）、StoreKit 2 **`appAccountToken`**（与 `uc_order_id` 去连字符后一致）关联。

**图示（非 normative）**：[flow.md](./flow.md)。

**边界**：

- 订阅 **Server Notification V2** 发货、`mo_subscriptions_notice` 分支见 [paid-subscription-plan/spec.md](../paid-subscription-plan/spec.md)（`IosSubscriptionV2Service` / `IosSubscriptionService`）。
- Apple **CONSUMPTION_REQUEST** 消耗回传见 `openspec/specs/ios-consumption/`。
- 聚合下单 `OrderInitService::init` / V5 与 iOS 专用 `payInit` 并存；本 spec 仅覆盖 `App\Pay\IOS` 与 `IOSPayController` / `IOSTransactionController` 路径。

## 端到端流程

```
┌──────────────────────────────────────────────────────────────────┐
│ 1. 中台预下单（iOS 专用）                                           │
│    POST /pay/ios/pay/init 或 /pay/ios/pay/init/v1（登录 token）      │
│    → 签名校验 → mo_order_info + mo_order_info_ext(extend=productId) │
│    v1 额外返回 appAccountToken（UUID 形态，源自 uc_order_id）          │
└────────────────────────────┬─────────────────────────────────────┘
                             ▼
┌──────────────────────────────────────────────────────────────────┐
│ 2. 用户在 App Store 完成 IAP                                        │
└────────────────────────────┬─────────────────────────────────────┘
                             ▼
┌──────────────────────────────────────────────────────────────────┐
│ 3. 验单 + 发货（三入口，客户端按 SDK 代际选用）                        │
│    A) 收据：POST /pay/ios/send/goods（checkStr + orderNo[]）         │
│    B) 一次性 TransactionId：POST /pay/order/success/ios              │
│    C) 订阅 TransactionId：POST /pay/order/subs/ios                   │
│    → paySuccessAfter → NotifyPayResultManagerNew                    │
└────────────────────────────┬─────────────────────────────────────┘
                             ▼
┌──────────────────────────────────────────────────────────────────┐
│ 4.（可选）Apple 退款推送 POST/GET /pay/ios/order/refund（免登录）    │
└──────────────────────────────────────────────────────────────────┘
```

### Requirement: 与中台订单关联

`IOSAdapter::initOrder` MUST 生成 `uc_order_id`（`UuidTool::create()`），并将客户端充值项 `orderId` 写入 `mo_order_info_ext.extend`（Apple **product_id**）。

发货匹配 MUST 满足下列之一：

| 路径 | 关联方式 |
|------|----------|
| 收据 `send/goods` | `in_app[].product_id` === `mo_order_info_ext.extend`，且 `uc_order_id` 在请求的 `orderNo[]` 内 |
| TransactionId 一次性 | `appAccountToken`（去 `-`、小写）=== `orderNo`；`decodedTransactionInfo.productId` === `extend` |
| TransactionId 订阅 | `appAccountToken` 定位首单后走 `IosSubscriptionV2Service`（见 paid-subscription-plan） |

`transaction_id` 发货成功后 MUST 写入 `mo_ios_inapp_order`，防重复以该表为准。

#### Scenario: cpOrderId 幂等初始化

- **WHEN** `payInit` 时 `cp_order_id` 已存在
- **THEN** MUST 返回已有 `uc_order_id`，不重复插入

#### Scenario: 凭证与订单 product 不匹配

- **WHEN** 收据有效、`orderNo[]` 存在但 `product_id` 与 `extend` 均无法配对
- **THEN** MUST 记日志；MAY 企业微信告警「凭证有效但匹配失败」

### Requirement: iOS 预下单（payInit）

`IOSService::payInit` MUST：

1. 经 `MoPayConfigDao` 按 `game_id`、`orderId`（充值项）、`platform`、`area` 解析配置；失败 MAY 按 `currency` 二次查询
2. 使用 `IOSAdapter::toSignatureTreeMap` + `SignatureTool::commonSignature` 校验 `signature`（不一致 → `PAY_SIGN_NOT_MATCH`）
3. 非测试环境 MUST 校验 `timestamp` 在 5 分钟内（`orderFiveMinuteSwitchKey` 存在时跳过）
4. `IOSAdapter::initOrder` 写入订单草稿；事务内插入 `mo_order_info` 与 `mo_order_info_ext`
5. Redis 缓存 `uc_order_id` → `gameConfigKey`（TTL 10 分钟）

`isSubscribePay === true` 且提供 `changeSubscribePayStatus_url`、`subscribePay_url` 时 MUST 调用 `IosSubscriptionService::initSave`。

`payOpenId` 非空时 MUST 经 `PayOpenIdRiskControlIosService::checkAndRecord`；`allow_send_goods !== true` MUST `PAY_RISK_CONTROL`。

实现 MUST `new IOSService()` 或通过容器获取；`IOSAdapter` 可注入。

#### Scenario: payInitV1 返回 appAccountToken

- **WHEN** `POST /pay/ios/pay/init/v1` 成功
- **THEN** 响应 `data` MUST 为 `{ appAccountToken, orderNo }`，其中 `appAccountToken` 为将 32 位 `uc_order_id` 格式化为 8-4-4-4-12 UUID 字符串

#### Scenario: 国内花之舞旧版本拦截

- **WHEN** 国内环境且 `appId` 为花之舞且 `pkVersion` 为 `v1.2.0` / `v1.2.1`
- **THEN** MUST 返回错误提示强制升级，不创建订单

### Requirement: 收据验单（legacy verifyReceipt）

`IOSPayController::sendGoods` → `IOSService::sendGoods` MUST：

1. 校验 `sendGoodsSignCheck`：`sign` === `UPPER(md5(LOWER(md5(LOWER('cert=' + checkStr + '&token=' + token)))))`
2. 经 token 解析 `openId`，MAY 校验支付白名单
3. 调用 `orderSuccess`；`SharedSecretErrException` MUST 吞掉不抛给客户端；其它异常 MUST 入 `PaySendLoopByiOS` 并返回 `PAY_APPLE_VALID_TIME_OUT`

`IOSAdapter::check` MUST：

- POST `https://buy.itunes.apple.com/verifyReceipt`；`status === 21007` 时改打 `https://sandbox.itunes.apple.com/verifyReceipt`
- `status === 21004` MUST 抛 `SharedSecretErrException`
- `status === 0` 且 `receipt.bundle_id` === 游戏配置 `packageId` 时返回 `receipt.in_app`
- 请求体含 `password`（`appleSharedSecret`）当 SharedSecret 非空

`IOSService::orderSuccess` 对每笔待发货 MUST 过滤：已发货、超 7 天、已有 `notify_result`、存在 `mo_subscriptions_notice` 首单、锁占用、`cancellation_date`、7 天外 `purchase_date_ms`、`mo_ios_inapp_order` 已存在 `transaction_id`。沙盒时 MUST `setIsSandBox` 并按包配置 `isArraign` 设置提审标记。

匹配 MAY 先通过 `getMoacOrderNoToTransactionInfo`（App Store Server API + `appAccountToken`）预关联，再按 `product_id` 与 `extend` 配对。

下列包名/游戏 MUST 直接返回成功且不验单（票据发货关闭）：`packageName === cn.lbwdhysj.gf.ios`、特定 `appId`、游戏配置 `iOSVerifyReceiptClose === true`。

#### Scenario: 收据验单成功

- **WHEN** `check` 返回非空 `in_app` 且完成订单配对
- **THEN** MUST 对每笔调用 `paySuccessAfter(transaction_id)` 并 MAY 派发 `PayAfterEvent`

#### Scenario: 客户端补单白名单

- **WHEN** `clientSideSend === 1` 且 openId 在 `mo_pay_white_list`
- **THEN** MAY 将 `notify_num` 置为 `10002` 并关闭白名单页

### Requirement: TransactionId 一次性验单

`POST /pay/order/success/ios` → `IOSTransactionService::orderSuccess` MUST：

1. `IOSTransactionRequest` 校验 `transactionId`、`orderNo[]`、`packageName` 等
2. Redis 锁 `iosTransactionLockKey(transactionId)`（NX，10s）；失败抛 `LockWaitTimeoutException`（Controller 吞掉）
3. 拒绝 `mo_ios_inapp_order` 已存在、`ucOrderValid` 失败、存在 `mo_subscriptions_notice` 的订阅首单
4. `IOSTransactionAdapter::check`：经 `mo_pay_config_ext`（`app_type = APPLEPAY`）调 `IosOrderQueryService::queryTransaction`
5. MUST 拒绝含 `expiresDate` 的订阅型交易；`isRevoked()` MUST 失败
6. `appAccountToken` 存在时 MUST 与 `orderNo`（去连字符小写）一致；`productId` MUST 等于 `mo_order_info_ext.extend`
7. 成功 MUST `paySuccessAfter(transactionId)`；`HttpReqTimeoutException` MUST 入 `PaySendLoopByIOSTransactionId`

Service/Adapter MUST `new` 实例化，禁止容器复用（见 [architecture/spec.md](../architecture/spec.md)）。

#### Scenario: App Store API 查询重试

- **WHEN** `IosQueryTransactionException` 且重试次数 &lt; 5
- **THEN** MUST sleep 2s 后重试 `check`

### Requirement: TransactionId 订阅客户端入口

`POST /pay/order/subs/ios` → `IOSSubscriptionService::orderSuccess` MUST 经 `IOSSubscriptionAdapter::check` 取得交易后，构造 synthetic payload（`notificationType` 为 `SUBSCRIBED` 或 `DID_RENEW`）并调用 `IosSubscriptionV2Service::orderSuccess`。

订阅发货细则、Redis 锁、`mo_ios_inapp_order` 写入 MUST NOT 在本 spec 重复，见 [paid-subscription-plan/spec.md](../paid-subscription-plan/spec.md)。

#### Scenario: 订阅续订 reason

- **WHEN** `transactionReason === RENEWAL`
- **THEN** synthetic `notificationType` MUST 为 `DID_RENEW`

### Requirement: 凭证上传

`POST /pay/order/ios/uploadPayOrder`（免登录）MUST 仅持久化/上传客户端凭证，不替代验单发货；实现见 `OrderInfoController::iosUploadPayOrder` + `IOSUploadPayOrderStruct`。

#### Scenario: 联调凭证留存

- **WHEN** 客户端上传 checkStr 备份
- **THEN** MUST 命中 upload 路由且不要求登录 token

### Requirement: Apple 配置

| 用途 | 来源 |
|------|------|
| 预下单金额/币种 | `mo_pay_config` |
| 收据 SharedSecret、bundleId | 游戏配置 JSON（`packageId`、`appleSharedSecret`） |
| Server API（TransactionId） | `mo_pay_config_ext`：`appid`=teamId、`secret`=keyId、`extend.privateKey` |

`IosOrderQueryService` MUST 在 `teamId`、`keyId`、`bundleId`、`privateKey` 齐全时方可查询。

### Requirement: HTTP 入口

| 方法 | 路径 | Controller | 中间件 |
|------|------|------------|--------|
| POST | `/pay/ios/pay/init` | `IOSPayController::payInit` | 登录 token |
| POST | `/pay/ios/pay/init/v1` | `IOSPayController::payInitV1` | 登录 token |
| POST | `/pay/ios/send/goods` | `IOSPayController::sendGoods` | 登录 token |
| POST | `/pay/order/success/ios` | `IOSTransactionController::verifyTransaction` | 登录 token |
| POST | `/pay/order/subs/ios` | `IOSTransactionController::subscription` | 登录 token |
| POST | `/pay/order/ios/uploadPayOrder` | `OrderInfoController::iosUploadPayOrder` | 免登录 |
| GET/POST | `/pay/ios/order/refund` | `RefundOrderController::iosRefundPush` | 免登录 |

`payInit` / `sendGoods` MUST `RedisVisitSleepTool::visitSleep` 防连点。

#### Scenario: sendGoods 缺 checkStr

- **WHEN** `checkStr` 为空
- **THEN** MUST 直接返回成功列表，不调用验单

### Requirement: 退款推送

`iosRefundPush` MUST 支持：

- **V1**：`notification_type === REFUND` + `unified_receipt.latest_receipt_info` 中带 `cancellation_date` 的记录 → 写 `mo_refund_order` / `mo_not_found_third_order`
- **V2**：`signedPayload` → `refundIosOrderV2`（JWS 解码）

退款入库 MUST NOT 在本 spec 展开字段表；与发货解耦。

#### Scenario: 未找到 transaction 的退款

- **WHEN** V1 退款找不到 `mo_ios_inapp_order`
- **THEN** MAY 插入 `mo_not_found_third_order` 并企业微信告警

### Requirement: 失败重试

| 队列 | 触发 |
|------|------|
| `PaySendLoopByiOS` | `sendGoods` / 收据 `orderSuccess` 非 SharedSecret 异常 |
| `PaySendLoopByIOSTransactionId` | TransactionId 路径 `HttpReqTimeoutException` |

#### Scenario: 验单超时入队

- **WHEN** Guzzle 调 Apple `verifyReceipt` 超时
- **THEN** `sendGoods` MUST 返回 `PAY_APPLE_VALID_TIME_OUT` 且 payload 入 iOS 轮询集合

### Requirement: 实例化与分层

`IOSService`、`IOSTransactionService`、`IOSSubscriptionService` MUST 每次 `new` 使用；`IOSAdapter` / `IOSTransactionAdapter` / `IOSSubscriptionAdapter` 由 Service 注入或内部 new。Controller MUST NOT 在 Service 层使用 FormRequest。

## 代码锚点

| 类 | 职责 |
|----|------|
| `IOSService` | `payInit`、`sendGoods`、`orderSuccess`（收据） |
| `IOSAdapter` | 预下单、`verifyReceipt` |
| `IOSTransactionService` | TransactionId 一次性发货 |
| `IOSTransactionAdapter` | Server API 验单 |
| `IOSSubscriptionService` | TransactionId 订阅入口 → V2 |
| `IosOrderQueryService` | App Store Server API 封装 |
| `IOSPayController` | init / sendGoods |
| `IOSTransactionController` | success/ios、subs/ios |

冲突时：**本 spec > 实现代码**（实现变更须同步 spec）。
