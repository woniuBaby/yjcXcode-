# Modo-AppShare API 参考

> 插件 ID：`Modo-AppShare` · JS 入口：`@/js_sdk/app-share/index.js`  
> 字段命名与 uni-app x `uni_modules/app-share/utssdk/interface.uts` 中 `ShareOptions` **完全一致**。

目录：[对接指南.md](./对接指南.md) · [README.md](./README.md)

---

## 1. JS 方法

| 方法 | 说明 |
|------|------|
| `shareImage(options)` | 发起分享（异步，结果走事件） |
| `onShareEvent(callback)` | 监听 `ModoAppShareEvent` |
| `offShareEvent(callback)` | 取消监听 |
| `probeShareEnvironment()` | 探测环境（Promise，原生 `probeEnvironment`） |
| `handleShareOpenURL(url)` | 从 App 层转发 URL Scheme 回调 |
| `handleShareOpenUniversalLink(url)` | iOS Universal Link 回调 |
| `isPluginAvailable()` | 是否已加载原生插件 |
| `formatShareEventLog(evt)` | 格式化事件为日志字符串 |

```javascript
import {
  shareImage,
  onShareEvent,
  offShareEvent
} from '@/js_sdk/app-share/index.js'
```

---

## 2. shareImage(options) 公共参数

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `platform` | `string` | ✅ | `wx` / `qq` / `douyin` / `alipay` |
| `contentType` | `string` | ✅ | 固定 `"1"`（图片/卡片类分享） |
| `opentype` | `string` | 视平台 | 见下文各平台说明，默认 `"1"` |
| `shareContent` | `string` | 视平台 | 微信/QQ：图片 URL；支付宝：落地页 URL（http/https）；抖音：图片 URL 或链接 |
| `shareTitle` | `string` | 否 | 卡片标题 |
| `shareDes` | `string` | 否 | 卡片描述 |
| `linkUrl` | `string` | 否 | 落地页；支付宝 **优先于** `shareContent` |
| `imageUrl` | `string` | 否 | 缩略图/封面 URL |
| `imageUrlList` | `string[]` | 否 | 缩略图列表；QQ/支付宝取首项作备选 |

### 平台专用参数

| 字段 | 平台 | 说明 |
|------|------|------|
| `wechatAppId` | wx | 微信开放平台 AppId |
| `wechatUniversalLink` | wx | Universal Link（推荐填写） |
| `tencentAppId` | qq | QQ 互联 AppId（纯数字，勿传 `tencent101998280`） |
| `tencentUniversalLink` | qq | QQ Universal Link（可选） |
| `douyinClientKey` | douyin | 抖音 Client Key |
| `douyinPickFromAlbum` | douyin | `opentype=2` 时是否弹相册选图 |
| `douyinLocalIdentifiers` | douyin | 相册 `localIdentifier`，两步选图分享 |
| `alipayAppId` | alipay | 支付宝 AppId；URL Scheme = `ap` + AppId |

---

## 3. opentype 对照

| platform | opentype | 含义 |
|----------|----------|------|
| `wx` | `1` | 分享到微信好友 |
| `wx` | `2` | 分享到朋友圈（有 `shareTitle` 时可能走网页卡片） |
| `qq` | `1` | 分享到 QQ 好友（图片） |
| `qq` | `2` | 分享到 QQ 空间（纯图片可能不支持，见错误码） |
| `douyin` | `0` | 仅初始化 SDK，不跳转 |
| `douyin` | `2` | OpenSDK 日常分享（uni 基座内易闪退，慎用） |
| `douyin` | `8` | **系统分享面板**（推荐，用户手动选抖音） |
| `alipay` | `0` | 仅 `registerApp`，不跳转 |
| `alipay` | `1` | 分享链接卡片到支付宝会话 |

---

## 4. 各平台示例

### 微信 wx

```javascript
shareImage({
  platform: 'wx',
  contentType: '1',
  opentype: '1',
  wechatAppId: 'wx4268b80776894552',
  wechatUniversalLink: 'https://sdk.chaotian.cn/cnbonbon/',
  shareContent: 'https://example.com/cover.png',
  shareTitle: '标题',
  shareDes: '描述'
})
```

### QQ qq

```javascript
shareImage({
  platform: 'qq',
  contentType: '1',
  opentype: '1',
  tencentAppId: '101998280',
  tencentUniversalLink: 'https://sdk.chaotian.cn/qq_conn/101998280/',
  shareContent: 'https://example.com/cover.png',
  shareTitle: '标题',
  imageUrl: 'https://example.com/cover.png',
  imageUrlList: ['https://example.com/cover.png']
})
```

### 支付宝 alipay

```javascript
// 初始化
shareImage({
  platform: 'alipay',
  contentType: '1',
  opentype: '0',
  alipayAppId: '2021006151641708'
})

// 链接卡片
shareImage({
  platform: 'alipay',
  contentType: '1',
  opentype: '1',
  alipayAppId: '2021006151641708',
  linkUrl: 'https://sdk.chaotian.cn/cnbonbon/',
  shareTitle: '标题',
  shareDes: '描述',
  imageUrl: 'https://example.com/thumb.png'
})
```

### 抖音 douyin

```javascript
// 推荐：系统分享
shareImage({
  platform: 'douyin',
  contentType: '1',
  opentype: '8',
  douyinClientKey: 'aw9vnkb1pbdt3gaw',
  shareContent: 'https://example.com/cover.png'
})
```

---

## 5. 事件 ModoAppShareEvent

须在 **App 端** 用 `onShareEvent` 监听（内部 `plus.globalEvent`，不可用 `uni.$on`）。

```javascript
onShareEvent((evt) => {
  // evt 字段：
  // status: 'success' | 'fail' | 'cancel' | 'trace'
  // code:    平台错误码或业务码
  // message: 描述
  // platform: 'wx' | 'qq' | 'douyin' | 'alipay'
  // ok:      boolean（success 时为 true）
})
```

| status | 含义 |
|--------|------|
| `success` | 分享成功 |
| `fail` | 失败 |
| `cancel` | 用户取消（如支付宝 `-2`） |
| `trace` | 调试跟踪（多见于抖音） |

### 常见 code（节选）

| code | 平台 | 含义 |
|------|------|------|
| `0` | wx / alipay | 成功 |
| `wechat_not_install` | wx | 未安装微信 |
| `wechat_unsupport` | wx | 微信版本不支持 OpenApi |
| `qq_send_1` | qq | QQ 未安装或 AppId/Scheme 配置问题 |
| `qq_image_load_fail` | qq | 图片下载失败 |
| `10001` | qq | QZone 不支持纯图片，改 `opentype=1` |
| `debug_init_ok` | alipay | 初始化成功 |
| `alipay_not_install` | alipay | 未安装支付宝 |
| `-4` | alipay | 开放平台 Bundle ID 与安装包不一致 |
| `9040003` | 通用 | 缺少必填参数 |
| `9040005` | 通用 | 平台未实现（如 Android 暂未接） |

---

## 6. URL 回调（iOS）

主工程 `App.vue` 已转发；业务包需保证：

```javascript
// App.vue onShow / newintent
import { handleShareOpenURL, handleShareOpenUniversalLink } from '@/js_sdk/app-share/index.js'
```

| 回调 | 用途 |
|------|------|
| `handleShareOpenURL(url)` | 微信 `wx*`、QQ `tencent*`、支付宝 `ap*`、抖音等 Scheme |
| `handleShareOpenUniversalLink(url)` | 微信 / QQ Universal Link |

---

## 7. 原生模块方法（一般无需直接调用）

| 原生方法 | JS 封装 |
|----------|---------|
| `shareImage:` | `shareImage()` |
| `probeEnvironment:` | `probeShareEnvironment()` |
| `handleOpenURL:` | `handleShareOpenURL()` |
| `handleOpenUniversalLink:` | `handleShareOpenUniversalLink()` |

---

## 8. 与 uni-x 差异

| 项 | uni-x | 老 uni（本插件） |
|----|-------|------------------|
| 引入 | `@/uni_modules/app-share` | `@/js_sdk/app-share/index.js` |
| 同步回调 | `success` / `fail` on options | 暂用 `onShareEvent` 统一监听 |
| 类型 | `ShareOptions` in `.uts` | 同上字段，JS 对象 |

完整类型定义见：`uniapp_plugins_demo/uni_modules/app-share/utssdk/interface.uts`
