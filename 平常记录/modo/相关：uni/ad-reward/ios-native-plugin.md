# 穿山甲 GroMore · 老 uni-app iOS 原生插件

> 工程：`unidemo` · 插件 ID：`Modo-AdReward`  
> API 对齐 uni-app x `uni_modules/ad-reward` · JS 封装：`js_sdk/ad-reward/index.js`

---

## 文档索引

| 文档 | 说明 |
|------|------|
| [README.md](./README.md) | 广告文档目录 |
| 本文 | iOS 插件接入、API、Demo 路由、实现状态、基座流程 |
| [vue与nvue-嵌入式广告方案.md](./vue与nvue-嵌入式广告方案.md) | **为何 Banner/Feed 必须用 nvue**；`.vue` overlay 弊端 |
| [android-实现计划.md](./android-实现计划.md) | Android 端同等功能 / UI 的实现清单 |
| [离线 iOS 基座](../离线iOS打包-完整协作说明.md) | Archive 前准备、安装 ipa、HBuilderX 自定义基座 |

---

## 一、Demo 页面（当前模式）

入口：`pages/ad-reward/platform`

| 入口 | 页面 | 技术 | 用途 |
|------|------|------|------|
| **iOS · .nvue 原生组件方案**（推荐） | `pages/ad-reward/demo-native.nvue` | nvue + `modo-ad-banner` / `modo-ad-feed` | 激励 + Banner + Feed；嵌入式广告不抖、高度自适应 |
| **iOS · .vue WebView 方案**（对照） | `pages/ad-reward/demo.vue?platform=ios` | `.vue` + module API + overlay | 全广告类型按钮；Banner/Feed 仅 overlay 对照，**不推荐上线** |
| **iOS · 自绘物料**（废弃） | `pages/ad-reward/material-demo` | 实验 | 模版位不提供物料，见 [vue与nvue 方案 §零](./vue与nvue-嵌入式广告方案.md) |
| **Android** | `pages/ad-reward/demo.vue?platform=android` | nvue 组件 | 见 Android 文档 |

两页 Demo **区块顺序一致**：SDK → 激励视频 → Banner → 信息流 → 事件日志 → 其他类型（仅 `.vue` 有开屏/全屏/插屏/Draw）。

---

## 二、SDK 与 ID

| 渠道 | 说明 |
|------|------|
| 官方入口 | [穿山甲媒体平台](https://www.csjplatform.com/) |
| 下载路径 | **接入中心 → 广告变现 → SDK → GroMore 聚合变现** |
| 集成文档 | [GroMore iOS 集成文档](https://www.csjplatform.com/supportcenter/28704) |
| 本地拷贝脚本 | `scripts/copy-pangle-ios-sdk.sh` → `nativeplugins/Modo-AdReward/ios/Frameworks/` |

| 名称 | 字段 | 含义 |
|------|------|------|
| 应用 ID | `appId` | SDK 初始化 |
| 应用名称 | `appName` | 与平台登记一致 |
| 聚合广告位 ID | `codeId` | GroMore 广告位，**通常以 `1` 开头**；勿填 ADN 代码位 |
| Bundle ID | — | 须与平台、Xcode 一致：`cn.modocommunity.ios` |

**本 Demo iOS 默认 ID**（见 `common/ad-reward-defaults.js` → `AD_REWARD_IOS_DEFAULTS`）：

| 类型 | codeId / appId |
|------|----------------|
| appId | `5827649` |
| 激励 | `104105016` |
| Banner | `104104931` |
| Feed | `104105114` |

---

## 三、JS API

```javascript
import {
  initAdSdk, startAdSdk,
  loadRewardAd, showRewardAd, destroyRewardAd,
  bindAdSlot, bindAdSlotBySelector,
  loadBannerAd, showBannerAd, destroyBannerAd,
  loadFeedAd, showFeedAd, destroyFeedAd,
  onAdEvent, offAdEvent, formatAdEventLog,
  isPluginAvailable
} from '@/js_sdk/ad-reward/index.js'
```

| 方法 | 说明 | iOS 状态 |
|------|------|----------|
| `initAdSdk` / `startAdSdk` | GroMore 初始化 | ✅ |
| `load/show/destroyRewardAd` | 激励视频 | ✅ |
| `bindAdSlot` / `bindAdSlotBySelector` | WebView overlay 槽位（`.vue` 对照用） | ✅ |
| `load/show/destroyBannerAd` | module + overlay | ✅（overlay 不推荐） |
| `load/show/destroyFeedAd` | module + overlay | ✅（overlay 不推荐） |
| 开屏 / 全屏 / 插屏 / Draw | module API | ⏳ 占位 `9040005` |

**事件**：原生 `fireGlobalEvent`，事件名 `ModoAdRewardEvent`；须用 `plus.globalEvent` 监听（见 `onAdEvent`）。

**事件 JSON**：`{ adType, phase, ok, errCode, errMsg, data }`

---

## 四、原生组件（Banner / Feed · 推荐）

在 **nvue** 页面使用（`.vue` 无法稳定承载 `DCUniComponent`）：

```html
<modo-ad-banner
  class="banner"
  :style="{ height: bannerHeight + 'px' }"
  :codeId="bannerCodeId"
  :autoLoad="bannerAutoLoad"
  @sizechange="onBannerSizeChange"
/>
```

```html
<modo-ad-feed
  class="feed"
  :style="{ height: feedHeight + 'px' }"
  :codeId="feedCodeId"
  :autoLoad="feedAutoLoad"
  @sizechange="onFeedSizeChange"
/>
```

| 属性 | 说明 |
|------|------|
| `codeId` | GroMore 广告位 ID |
| `autoLoad` | `true` 时在组件就绪且 SDK ready 后自动 load |
| `debugText` | 调试文案（可选） |

| 事件 | 说明 |
|------|------|
| `sizechange` / `sizeChange` | `{ height, width }` 广告 render 后的实际尺寸，用于高度自适应 |

**插件注册**（`nativeplugins/Modo-AdReward/package.json`）：

- `modo-ad-banner` → `ModoAdBannerComponent`
- `modo-ad-feed` → `ModoAdFeedComponent`

详见 [vue 与 nvue 方案说明](./vue与nvue-嵌入式广告方案.md)。

---

## 五、插件目录

```
nativeplugins/Modo-AdReward/
├── package.json
├── ios/
│   ├── ModoAdRewardModule/     # DCUniModule 桥接
│   ├── ModoAdBannerComponent/  # nvue 原生组件
│   ├── ModoAdFeedComponent/
│   ├── csj/                    # 业务：Bridge、Handler、SlotHost、EventEmitter
│   ├── Frameworks/             # BUAdSDK、CSJMediation xcframework
│   └── Bundles/                # CSJAdSDK.bundle
└── android/                    # 待实现，见 Android 文档
```

对照 BUDemo：`union_platform_iOS_7.6.0.4/DomesticDemo/BUDemo`

---

## 六、自定义基座（必做）

修改 `nativeplugins/Modo-AdReward` 后 **必须** 重打 iOS 自定义基座；仅改 `.vue` / `.nvue` / `js_sdk` 可 HBuilderX 热更新。

```bash
cd /Users/lin/Documents/uni_app/mdunidemo/unidemo
./scripts/prepare-offline-hello-archive.sh   # 助手跑：集成 + Release 编译
# 你在 Xcode Archive → Development 导出 ipa
./scripts/install-ios-debug-ipa.sh "<ipa路径>"  # 助手跑：写入 unpackage/debug/iOS_debug.ipa
```

HBuilderX：**运行 → 运行到 iOS App 基座 → 使用自定义基座 → 本地基座**。

完整分工见 [离线 iOS 基座](../离线iOS打包-完整协作说明.md)。JS API 明细见 [API.md](./API.md)，接入见 [对接指南.md](./对接指南.md)。

---

## 七、manifest 配置

1. `manifest.json` → App 原生插件 → 勾选 **Modo-AdReward**
2. iOS 隐私：`NSUserTrackingUsageDescription`（插件 `package.json` 已声明）
3. 离线 Hello：`scripts/setup-offline-hello-plugins.py` 会写入 `dcloud_uniplugins` 与 Compile Sources

---

## 八、与 uni-app x 的关系

| 项目 | 技术 | 状态 |
|------|------|------|
| `uniapp_plugins_demo` | UTS `uni_modules/ad-reward` | Android ✅；iOS 占位 |
| `unidemo` | `nativeplugins` + Vue/nvue | iOS 激励 + Banner/Feed（nvue 组件）✅；Android 待封装 |

事件 JSON、方法名与 `uni_modules/ad-reward/utssdk/interface.uts` 保持一致，便于双端共用 Demo 与文档。

---

## 九、常见错误码

| errCode | 含义 |
|---------|------|
| `9040002` | SDK 未 init/start |
| `9040004` | 广告未 load 就 show |
| `9040005` | 该广告类型 iOS 尚未实现 |
| `9040008` | Banner/Feed overlay：`bindAdSlot` 未绑定或容器尺寸无效 |

`9040008` 在 nvue 原生组件方案下通常不应出现（不依赖 overlay）。
