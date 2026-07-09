# App 内购买优惠代码（Offer Codes）接入与测试说明

本文档说明 Apple **App 内购买优惠代码**（Offer Codes）与 NativeBility `iosPay`（`PluginAdapter_pay_inpurchase`）的关系，涵盖 Connect 配置、客户端改动范围、沙盒测试、以及「苹果通知服务端」与「App 通知服务端」的分工。

> **与游戏自有兑换码区分**：工程里的 `sys_redemptionCode` 等属于**游戏业务 CDKey**，不走 StoreKit。优惠代码必须在 App Store Connect 配置，兑换后产生 **Apple IAP 交易**，验单方式与普通内购相同。

## 参考链接

| 主题 | 链接 |
|------|------|
| Connect：创建优惠与代码 | [为 App 内购买项目创建优惠代码](https://developer.apple.com/cn/help/app-store-connect/manage-in-app-purchases/create-offer-codes-for-in-app-purchases) |
| StoreKit：App 内支持 | [Supporting offer codes in your app](https://developer.apple.com/documentation/storekit/supporting-offer-codes-in-your-app) |
| 沙盒账户管理 | [管理沙盒 Apple 账户设置](https://developer.apple.com/help/app-store-connect/test-in-app-purchases/manage-sandbox-apple-account-settings) |
| Server Notifications | [Enabling App Store Server Notifications](https://developer.apple.com/documentation/StoreKit/enabling-app-store-server-notifications) |
| 现有 `iosPay` 实现 | [ios-pay.md](./ios-pay.md) |

---

## 1. 优惠代码是什么

优惠代码（Offer Code）是 Apple 提供的**字母数字码**，用于对 App 内购买（含自动续订订阅、消耗型、非消耗型、非续订订阅）提供**免费或降价**等优惠。

### 1.1 代码类型（Connect）

| 类型 | 特点 | 典型用途 |
|------|------|----------|
| **一次性代码** | 随机生成，每码仅可兑一次；可下载 CSV（含兑换 URL） | 限量活动、竞赛奖品、印刷在物料上的码 |
| **自定代码** | 自定义命名（如 `SUMMER25`），可设总兑换上限 | 大规模营销、固定口令 |
| **沙盒代码** | 仅测试用的一次性码 | 开发/QA 沙盒联调 |

限制摘要（以 Connect 帮助为准）：

- 每个 App 同时最多 **10** 个有效优惠（offer）
- 每季度每 App 最多创建 **100 万** 个优惠代码（正式环境）
- 沙盒测试码每季度最多 **10,000** 个
- 代码有效期最长 **6** 个月；创建后最多约 **1 小时** 才可被兑换

### 1.2 兑换入口

| 方式 | 是否需要改 iOS 代码 |
|------|---------------------|
| 兑换 URL（一次性码 CSV 内） | 否 |
| 在 **App Store** 中输入代码 | 否 |
| 在 **App 内**输入代码（系统 Sheet） | **是**（需 StoreKit API） |

App 内兑换 API（系统版本要求见下文）：

- StoreKit 2 / UIKit：`AppStore.presentOfferCodeRedeemSheet(in:)`
- SwiftUI：`offerCodeRedemption(isPresented:onCompletion:)`
- 旧版（**仅自动续订订阅**，iOS 14.2+）：`[SKPaymentQueue presentCodeRedemptionSheet]`

### 1.3 系统版本与商品类型

| 商品类型 | App 内兑换最低系统 |
|----------|-------------------|
| 自动续订订阅 | iOS 14.2+（可用旧版 `presentCodeRedemptionSheet`） |
| 消耗型 / 非消耗型 / 非续订订阅 | iOS **16.3+**（需新 API） |

---

## 2. App Store Connect 配置步骤

1. **App** → **App 内购买项目** → 选择目标商品。
2. **优惠** → **创建优惠**（参考名称创建后不可改）。
3. 配置适用顾客群体、国家或地区、付费优惠或免费、价格。
4. 在优惠详情下创建：
   - **一次性代码** / **自定代码**（正式分发；App 需「可分发」、IAP「已批准」）
   - **沙盒代码**（测试；每批 10～10,000 个）

注意：

- 优惠创建后**不能编辑**适用人群；需调整时请新建优惠。
- 停用优惠后，未兑换的码立即失效；已兑换用户不受影响。

---

## 3. 与 `iosPay` 的关系：要不要改 iOS 代码

当前 `iosPay` 实现见 [ios-pay.md](./ios-pay.md)：

- StoreKit **1**：`SKPaymentTransactionObserver`
- **未实现** `presentCodeRedemptionSheet` / `presentOfferCodeRedeemSheet`
- 验单依赖 **收据 Base64**（`checkStr`）+ `transactionId` 等字段

### 3.1 按场景判断

| 目标 | iOS 是否必须改 |
|------|----------------|
| 仅站外兑换（App Store / 兑换链接），用户再打开 App | **可不实现** App 内兑换 UI；须保证 Observer + `queryPurchases` + 验单 + `consume` 能处理**无 `orderNo`** 的交易 |
| 游戏内「输入优惠码」入口 | **必须新增** Native API（封装系统兑换 Sheet）+ 游戏 UI 调用 |
| 运营/对账识别「来自优惠码」 | 建议在**服务端**解析 JWS / Server Notifications 的 `offerIdentifier`、`offerType`；客户端可选透传 |

### 3.2 优惠码交易的客户端注意点

兑换成功后，StoreKit 产生与普通购买相同的交易，`iosPay` 会进入 `paymentQueue:updatedTransactions:`。

与主动 `pay` 的差异：

| 点 | 说明 |
|----|------|
| **通常无 `orderNo`** | 未先走 `pay` 预下单；服务端须支持按 `transactionId` + 收据验单 |
| **`productType` 可能为 `fail`** | `buildOrderPayloadWithTransaction` 在无 `payData` 且非续费时默认为 `fail`；上层/服务端勿仅凭此字段当失败 |
| **完成时机** | 普通 `pay` 购买在 `Purchased` 后等服务端 `consume` 再 `finish`；优惠码交易同样应走验单 → 发货 → `consume` |
| **续费类** | 无主动 `pay` 时可能走 `ios_renew` 事件补发 |

### 3.3 建议改动清单（按优先级）

| 优先级 | 层级 | 建议 |
|--------|------|------|
| P0 | 服务端 | 收据/JWS 验单；处理 Server Notifications；`transactionId` 幂等发货 |
| P0 | 游戏 JS | 启动/回前台 `queryPurchases`；支持无 `orderNo` 的待验单 |
| P1 | NativeBility | 可选 API：`presentOfferCodeRedeemSheet` / `presentCodeRedemptionSheet` |
| P1 | Native | 优惠码交易标记（如 `productType: offerCode`），避免误判 `fail` |
| P2 | Native | 长期考虑 StoreKit 2 `Transaction.updates`（站外兑换、App 未运行时补单更稳） |

---

## 4. 兑换成功后的通知：苹果 vs App

用户若在 **App Store** 完成兑换再跳转/打开 App，**两条通知链路并存，职责不同，不能二选一**。

### 4.1 时序概览

```
用户 → App Store 输入码 / 打开兑换链接
         ↓
    Apple 生成 IAP 交易
         ↓
    ┌────────────────────────────────────┐
    │ ① Apple → 你的服务端（异步）          │
    │    App Store Server Notifications V2 │
    └────────────────────────────────────┘
         ↓
    用户打开 App
         ↓
    ┌────────────────────────────────────┐
    │ ② App → 你的服务端（验单发货）        │
    │    StoreKit 交易 + checkStr          │
    └────────────────────────────────────┘
         ↓
    consume → finishTransaction
```

### 4.2 苹果通知服务端（需先在 Connect 配置 URL）

配置 **App Store Server Notifications** 后，Apple **主动 POST** 到你的服务端，**不经过 App**。

常见 `notificationType`（优惠码场景）：

| 场景 | 常见类型 |
|------|----------|
| 已有订阅用户兑换订阅优惠 | `OFFER_REDEEMED` |
| 订阅首购 / 重新订阅 | `SUBSCRIBED` |
| 消耗型 / 非消耗型 / 非续订订阅 | `ONE_TIME_CHARGE` |

解码 JWS 后可查 `offerIdentifier`、`offerType`（优惠码一般为 **3**）。

特点：

- 异步，可能早于或晚于用户打开 App
- 用户**未打开 App** 也能感知购买（适合对账、订阅状态同步）
- 沙盒需在 Connect 单独配置 **Sandbox** 通知 URL

### 4.3 App 通知服务端（`iosPay` 当前主路径）

用户进入 App 后：

1. `SKPaymentTransactionObserver` 收到 `Purchased`，或
2. 启动/回前台调用 `queryPurchases` 拉取待验单

App 将 `transactionId`、`checkStr`（收据）等发给**自有服务端**验单 → 游戏发货 → `consume`。

特点：

- 负责用户**当场**拿到游戏内权益
- 优惠码往往**没有**预建 `orderNo`

### 4.4 推荐做法

| 能力 | 作用 |
|------|------|
| Server Notifications | 后台记录、订阅同步、防漏单兜底 |
| App 验单 + `queryPurchases` | 用户打开 App 时即时发货 |
| **`transactionId` 幂等** | 防止苹果通知与 App 验单重复发货 |

若未配置 Server Notifications，则**仅 App 验单一条链路**在工作，需尤其保证冷启动补单。

---

## 5. 沙盒测试指南

### 5.1 前置条件

- **真机**（优惠码联调不建议依赖模拟器）
- Connect 创建**沙盒测试员**，设备 **设置 → App Store → 沙盒账户** 登录
- 测试包：Xcode Run 或 TestFlight，Bundle ID 与 Connect 商品一致
- 目标 IAP 已在 Connect 创建（沙盒码不强制 App 上架，但商品需存在）

### 5.2 创建沙盒码

1. Connect → App → App 内购买项目 → 商品 → **优惠** → 选择优惠。
2. **沙盒代码** → 创建（10～10,000 个/批）。
3. 下载 CSV，等待最多约 **1 小时** 后可兑。

### 5.3 兑换方式（推荐顺序）

#### 方式 A：系统沙盒入口（不依赖 App 内兑换 UI）

适用于验证「码 → 交易 → 收据」主链路。

1. 沙盒账号登录。
2. **设置 → App Store → 沙盒账户** → **管理** / **发起交易（Initiate Transaction）**。
3. 选择 **Offer Codes（优惠代码）**。
4. 输入沙盒码。

#### 方式 B：App 内兑换

需 App 已接 `presentCodeRedemptionSheet` 或 StoreKit 2 Sheet；在 App 内打开系统兑换界面输入沙盒码。

#### 方式 C：兑换 URL

使用 CSV 中的唯一兑换链接，沙盒账号在 Safari 等环境打开（需能安装/打开测试 App）。

### 5.4 兑换后在 `iosPay` 侧验证

建议按下列顺序自检：

| 步骤 | 检查项 |
|------|--------|
| 1 | 冷启动 App，确认 `onInit` 已注册 `SKPaymentTransactionObserver` |
| 2 | 完成沙盒兑换 |
| 3 | 是否触发 `updatedTransactions`（`Purchased`） |
| 4 | 调用 `queryPurchases`，是否出现待验单（含 `transactionId`、`checkStr`） |
| 5 | 服务端沙盒验单并发货 |
| 6 | 客户端 `consume` 后，队列与本地 KeyChain 缓存是否清理 |
| 7 | （订阅）清沙盒购买历史后重测 eligibility |

### 5.5 沙盒与正式环境差异

| 项目 | 沙盒行为 |
|------|----------|
| 同一沙盒账号兑换次数 | 不限制重复测试 |
| 消耗型/非消耗型/非续订订阅 人群条件 | **忽略** eligibility |
| 自动续订订阅 人群条件 | **仍校验**；重测需[清空沙盒购买历史](https://developer.apple.com/help/app-store-connect/test-in-app-purchases/manage-sandbox-apple-account-settings) |
| 店面 | 按沙盒账号店面校验码是否可用 |

### 5.6 常见问题

| 现象 | 可能原因 |
|------|----------|
| 码刚创建不能兑 | 等待最多约 1 小时 |
| 无效/过期 | 码停用、过期、店面不匹配、IAP 未就绪 |
| 兑了 App 无反应 | Observer 未注册；需 `queryPurchases` / `refreshTransactionReceipt` |
| 订阅第二次失败 | 清沙盒购买历史 |
| App 内 Sheet 不出现 | 未接 API；或非订阅商品系统版本 &lt; 16.3 |
| 服务端无记录 | 未配 Sandbox Server Notifications URL |

---

## 6. 测试用例摘要（验收清单）

### 6.1 Connect / 运营

- [ ] 优惠、价格、地区、适用人群配置正确
- [ ] 沙盒码已生成并可下载 CSV
- [ ] （可选）Production / Sandbox Server Notifications URL 已配置

### 6.2 站外兑换 → 打开 App

- [ ] 设置内沙盒 Offer Codes 兑换成功
- [ ] 打开 App 后 `queryPurchases` 能拉到待验单
- [ ] 服务端验单成功且 `transactionId` 幂等
- [ ] `consume` 后无重复发货

### 6.3 无 orderNo

- [ ] 服务端不依赖 `orderNo` 也能发货
- [ ] 客户端未把 `productType=fail` 误判为支付失败

### 6.4 订阅类（若适用）

- [ ] 收到预期 Server Notification 类型
- [ ] 清购买历史后可重复测 eligibility

### 6.5 App 内兑换（若已接 Sheet）

- [ ] iOS 版本满足商品类型要求
- [ ] Sheet 兑换后同样走验单 → `consume`

---

## 7. 服务端识别优惠码（附录）

### 7.1 App Store Server API / Notifications

解码 `JWSTransaction` / 通知载荷时关注：

- `offerIdentifier`
- `offerType`：**3** 表示优惠码（Offer Code）

### 7.2 StoreKit 2（若未来迁移）

客户端可检查 `Transaction.offer.type == .code`。

---

## 8. 文档维护

| 项 | 说明 |
|----|------|
| 关联实现 | `NativeBility/nt+ability/Plugin/Pay/PluginAdapter_pay_inpurchase.m` |
| 关联文档 | [ios-pay.md](./ios-pay.md)、[pay-implementation.md](../pay-implementation.md) |
| 变更记录 | 初版：整理 Offer Codes 接入范围、沙盒测试、双通道通知分工 |
