# Modo-AdReward Uni 层调用文档

> 面向 **老 uni-app（Vue2/Vue3 + App）** 业务开发。  
> 插件 ID：`Modo-AdReward` · 穿山甲 GroMore 聚合 · JS 封装：`js_sdk/ad-reward/index.js`  
> 接入背景、manifest、基座见 [对接指南.md](./对接指南.md)。文档总目录：[../README.md](../README.md)。

---

## 目录

1. [环境与前置](#1-环境与前置)
2. [快速接入](#2-快速接入)
3. [Module API（JS 方法）](#3-module-apijs-方法)
4. [nvue 原生组件](#4-nvue-原生组件)
5. [事件监听](#5-事件监听)
6. [平台差异（iOS / Android）](#6-平台差异ios--android)
7. [典型场景示例](#7-典型场景示例)
8. [错误码与排查](#8-错误码与排查)
9. [参考路径](#9-参考路径)

---

## 1. 环境与前置

| 项 | 要求 |
|----|------|
| 运行端 | **APP-PLUS**（真机 App）；H5 / 小程序不支持 |
| 页面类型 | 激励等全屏广告：`.vue` / `.nvue` 均可 |
| Banner / Feed 嵌入 | **必须 `.nvue`**（推荐原生组件，勿用 overlay 上线） |
| manifest | `manifest.json` → App 原生插件 → 勾选 **Modo-AdReward** |
| 调试基座 | HBuilderX **自定义调试基座**；Android 离线包需 Rebuild 后安装新 APK |
| 穿山甲后台 | 使用 **GroMore 聚合广告位 ID**（`codeId`），勿填 ADN 子代码位 |

### 1.1 引入 JS 封装

```javascript
import {
  initAdSdk,
  startAdSdk,
  isAdSdkReady,
  isPluginAvailable,
  loadRewardAd,
  showRewardAd,
  destroyRewardAd,
  onAdEvent,
  offAdEvent,
  formatAdEventLog,
  // 按需：Banner/Feed overlay、开屏等
  bindAdSlot,
  bindAdSlotBySelector,
  loadBannerAd,
  showBannerAd,
  destroyBannerAd,
  loadFeedAd,
  showFeedAd,
  destroyFeedAd,
  loadSplashAd,
  showSplashAd,
  destroySplashAd,
  loadFullScreenAd,
  showFullScreenAd,
  destroyFullScreenAd,
  loadInterstitialAd,
  showInterstitialAd,
  destroyInterstitialAd,
  loadDrawAd,
  showDrawAd,
  destroyDrawAd
} from '@/js_sdk/ad-reward/index.js'
```

测试 ID 可参考：`common/ad-reward-defaults.js`（`AD_REWARD_IOS_DEFAULTS` / `AD_REWARD_ANDROID_DEFAULTS`）。

### 1.2 检查插件是否可用

```javascript
if (!isPluginAvailable()) {
  console.warn('未加载 Modo-AdReward，请检查 manifest 与自定义基座')
}
```

---

## 2. 快速接入

### 2.1 固定调用顺序

```text
initAdSdk(options)  →  startAdSdk()  →  loadXxxAd / nvue 组件 autoLoad
```

未 `startAdSdk` 就 load 会收到 `9040001` 等错误。

### 2.2 App 启动初始化（推荐 `App.vue`）

```javascript
// App.vue
import {
  initAdSdk,
  startAdSdk,
  onAdEvent,
  offAdEvent,
  isPluginAvailable,
  formatAdEventLog
} from '@/js_sdk/ad-reward/index.js'
import { AD_REWARD_IOS_DEFAULTS, AD_REWARD_ANDROID_DEFAULTS } from '@/common/ad-reward-defaults.js'

const adDefaults = uni.getSystemInfoSync().platform === 'ios'
  ? AD_REWARD_IOS_DEFAULTS
  : AD_REWARD_ANDROID_DEFAULTS

function onGlobalAdEvent(evt) {
  console.log(formatAdEventLog(evt))
  // 激励发奖：evt.adType === 'reward' && evt.phase === 'reward' && evt.ok
}

export default {
  onLaunch() {
    if (!isPluginAvailable()) return
    onAdEvent(onGlobalAdEvent)
    initAdSdk({
      appId: adDefaults.appId,
      appName: adDefaults.appName,
      debug: adDefaults.debug,           // 上线务必 false
      useMediation: adDefaults.useMediation
    })
    startAdSdk()
  },
  onUnload() {
    offAdEvent(onGlobalAdEvent)
  }
}
```

### 2.3 激励视频（任意页面）

```javascript
// 预加载
loadRewardAd({ codeId: '你的激励广告位ID' })

// 用户点击
showRewardAd()

// 页面卸载
destroyRewardAd()
```

---

## 3. Module API（JS 方法）

所有方法通过 `uni.requireNativePlugin('Modo-AdReward')` 调用；**请使用 `js_sdk` 导出函数**，不要直接拼原生方法名。

### 3.1 SDK

| 方法 | 参数 | 返回值 | 说明 |
|------|------|--------|------|
| `isPluginAvailable()` | — | `boolean` | 插件是否加载 |
| `initAdSdk(options)` | 见下表 | `boolean` 是否 invoke 成功 | 保存配置，不拉广告 |
| `startAdSdk()` | — | `boolean` | 启动穿山甲 SDK |
| `isAdSdkReady()` | — | `Promise<{ ready: boolean }>` | 查询是否已 start |

**`initAdSdk(options)`**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `appId` | string | 是 | 穿山甲应用 ID |
| `appName` | string | 否 | 应用名 |
| `debug` | boolean | 否 | 默认 `false`；上线必须 `false` |
| `useMediation` | boolean | 否 | 默认 `true`，GroMore 聚合 |
| `mediationLocalConfigAsset` | string | 否 | **仅 Android**：assets 下 GroMore 本地配置文件名 |

### 3.2 激励视频 `reward`

| 方法 | 参数 | 说明 |
|------|------|------|
| `loadRewardAd(options)` | `{ codeId, rewardName?, rewardAmount?, orientation? }` | `orientation`: `'vertical'`（默认）/ `'horizontal'` |
| `showRewardAd()` | — | 需先 load 成功 |
| `destroyRewardAd()` | — | 释放实例 |

**激励相关 `adType === 'reward'` 的 `phase`**

| phase | 含义 |
|-------|------|
| `load` | 加载完成 |
| `cached` | 视频缓存完成 |
| `show` | 开始展示 |
| `reward` | **可发奖**（业务发奖写在这里，`ok === true`） |
| `close` | 关闭 |
| `error` | 失败 |

### 3.3 Banner / Feed（Module + 浮层，仅 `.vue` 对照用）

> **产品环境请用 [§4 nvue 原生组件](#4-nvue-原生组件)**，不要用本节 overlay 上线。

| 方法 | 参数 | 说明 |
|------|------|------|
| `bindAdSlot(slot, rect)` | `slot`: `'banner'` \| `'feed'`；`rect`: `{ left, top, width, height, layoutOnly? }` | 绑定页面占位区域 |
| `bindAdSlotBySelector(vm, '#id', slot, opts?)` | `opts.layoutOnly` | 用 `selectorQuery` 测量后 bind |
| `loadBannerAd({ codeId })` | — | overlay Banner load |
| `showBannerAd()` | — | overlay Banner show |
| `destroyBannerAd()` | — | 销毁 |
| `loadFeedAd({ codeId })` | — | overlay Feed load |
| `showFeedAd()` | — | overlay Feed show |
| `destroyFeedAd()` | — | 销毁 |

### 3.4 开屏 / 全屏 / 插屏 / Draw

| 类型 | load | show | destroy |
|------|------|------|---------|
| 开屏 | `loadSplashAd({ codeId })` | `showSplashAd()` | `destroySplashAd()` |
| 全屏 | `loadFullScreenAd({ codeId })` | `showFullScreenAd()` | `destroyFullScreenAd()` |
| 插屏 | `loadInterstitialAd({ codeId })` | `showInterstitialAd()` | `destroyInterstitialAd()` |
| Draw | `loadDrawAd({ codeId })` | `showDrawAd()` | `destroyDrawAd()` |

**平台实现状态**

| 平台 | 激励 | 开屏/全屏/插屏/Draw |
|------|------|---------------------|
| iOS | ✅ | 接口已导出，当前返回 `9040005`（未实现） |
| Android | ✅ | ✅ |

---

## 4. nvue 原生组件

### 4.1 组件名与使用条件

| 组件标签 | 用途 | 页面 |
|----------|------|------|
| `<modo-ad-banner>` | Banner 模版 | `.nvue` |
| `<modo-ad-feed>` | 信息流模版 | `.nvue` |

前提：已 `initAdSdk` + `startAdSdk`；自定义基座含原生组件。

### 4.2 属性

| 属性 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `codeId` | String | 是 | — | GroMore **聚合广告位 ID** |
| `autoLoad` | Boolean | 否 | **true**（iOS；Android Banner 同） | `true` 且布局尺寸就绪后自动请求广告 |
| `autoShow` | Boolean | 否 | — | **仅 Android**；Feed 上用于 load 后触发 render；Banner 上与 `autoLoad` 等效，可不写 |
| `debugText` | String | 否 | — | 调试占位文案 |

**不要**向组件传 `left` / `top`；位置与宽高由 **uni 布局**（`:style`、`flex`、`750rpx`）决定。

### 4.3 组件事件（模板）

| 事件名 | 说明 |
|--------|------|
| `@sizechange` / `@sizeChange` | 广告渲染后回传宽高，用于更新占位高度 |

**回调参数（兼容 iOS / Android）**

```javascript
onBannerSizeChange(e) {
  const detail = e.detail || e   // Android 可能在 detail 内，iOS 可能在顶层
  const w = detail.width
  const h = detail.height
  if (h > 0) this.bannerHeight = h
}
```

### 4.4 推荐模板（Banner + Feed）

```html
<template>
  <scroller class="page">
    <modo-ad-banner
      v-if="showBanner"
      :style="{ width: '750rpx', height: bannerHeight + 'px' }"
      :codeId="bannerCodeId"
      :autoLoad="true"
      @sizechange="onBannerSizeChange"
    />

    <!-- 列表内容 … -->

    <modo-ad-feed
      v-if="showFeed"
      :style="{ width: '750rpx', height: feedHeight + 'px' }"
      :codeId="feedCodeId"
      :autoLoad="feedAutoLoad"
      :autoShow="feedAutoShow"
      @sizechange="onFeedSizeChange"
    />
  </scroller>
</template>

<script>
export default {
  data() {
    return {
      bannerCodeId: '',
      feedCodeId: '',
      bannerHeight: 150,
      feedHeight: 280,
      showBanner: true,
      showFeed: true,
      feedAutoLoad: false,
      feedAutoShow: false
    }
  },
  methods: {
    onBannerSizeChange(e) {
      const detail = e.detail || e
      if (detail.height > 0) this.bannerHeight = detail.height
    },
    onFeedSizeChange(e) {
      const detail = e.detail || e
      // Android：多为固定槽位高度；iOS：可能为 SDK 实际高度
      // #ifdef APP-PLUS
      if (uni.getSystemInfoSync().platform === 'android') {
        return  // 若业务固定 feedHeight，可不根据 SDK 改高度
      }
      // #endif
      if (detail.height > 0) this.feedHeight = detail.height
    },
    mountFeed() {
      this.feedHeight = 280
      this.feedAutoLoad = true
      this.feedAutoShow = true   // Android 需要；iOS 可省略
      this.showFeed = true
    }
  }
}
</script>
```

### 4.5 手动控制挂载时机（`v-if`）

与 Demo 一致：用户点击后再创建组件。

```javascript
data() {
  return {
    bannerMounted: false,
    bannerAutoLoad: false,
    bannerHeight: 150
  }
},
methods: {
  onUserClickLoadBanner() {
    this.bannerHeight = 150
    this.bannerAutoLoad = true
    this.bannerMounted = true
  },
  onDestroyBanner() {
    this.bannerMounted = false
    this.bannerAutoLoad = false
    this.bannerHeight = 150
  }
}
```

Banner：**load 与 show 在原生侧一体**（对齐 iOS），「展示」按钮与「加载」相同，均设 `bannerAutoLoad = true` 即可。

Feed（Android）：「加载」可只设 `feedAutoLoad = true`；「展示」需再设 `feedAutoShow = true`（或挂载时两个都为 `true`）。

### 4.6 组件内部全局事件

除 `@sizechange` 外，组件会通过 `ModoAdRewardEvent` 上报（与 Module 共用 `onAdEvent`）：

| adType | 常见 phase |
|--------|------------|
| `bannerComponent` | `load` / `render` / `wait` / `error` |
| `feedComponent` | `load` / `render` / `wait` / `error` |

---

## 5. 事件监听

### 5.1 订阅方式（必须）

```javascript
import { onAdEvent, offAdEvent, formatAdEventLog } from '@/js_sdk/ad-reward/index.js'

const handler = (evt) => {
  console.log(formatAdEventLog(evt))
}

// onLoad
onAdEvent(handler)

// onUnload
offAdEvent(handler)
```

- 事件名：`ModoAdRewardEvent`（常量见 `AD_EVENT_NAME`）
- **必须使用** `plus.globalEvent`（封装在 `onAdEvent` 内）
- **禁止**使用 `uni.$on` 监听广告事件

### 5.2 事件 payload 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `adType` | string | `sdk` / `reward` / `banner` / `feed` / `bannerComponent` / `feedComponent` / `splash` / … |
| `phase` | string | `init` / `start` / `load` / `show` / `render` / `reward` / `close` / `error` / `bind` / `wait` / … |
| `ok` | boolean \| number \| string | 是否成功（`true` / `1` / `'true'` 均视为成功） |
| `errCode` | string | 错误码 |
| `errMsg` | string | 错误描述 |
| `data` | object | 扩展数据（可选） |
| `raw` | string | 原始 JSON 字符串（可选） |

### 5.3 判断成功

```javascript
function isOk(evt) {
  const v = evt && evt.ok
  return v === true || v === 1 || v === '1' || v === 'true'
}
```

或使用封装：`formatAdEventLog(evt)`。

---

## 6. 平台差异（iOS / Android）

### 6.1 总览

| 能力 | iOS | Android | Uni 层建议 |
|------|-----|---------|------------|
| SDK / 激励 Module API | ✅ | ✅ | 同一套 `js_sdk` |
| nvue `modo-ad-banner` | ✅ | ✅ | 同一套模板；`autoLoad` 默认 true |
| nvue `modo-ad-feed` | ✅ load+render 一体 | ⚠️ 需 `autoShow` 才 render | Android 设 `autoShow=true` 或固定 `feedHeight` |
| Banner `sizechange` | SDK 实际高度 | SDK 实际高度 | `bannerHeight` 随 `@sizechange` 更新 |
| Feed `sizechange` | SDK 视图高度 | 多为**固定槽位**高度 | Android 可不根据事件改 `feedHeight` |
| 开屏/全屏/插屏/Draw | 占位 `9040005` | 已实现 | iOS 调用会失败事件，Android 可正常用 |
| `isRewardAdReady` | 原生有，**js_sdk 未封装** | 无 | 勿依赖；用 `onAdEvent` 的 `load`/`cached` |

### 6.2 `sizechange` 参数结构

| 平台 | 结构 |
|------|------|
| iOS | `{ width, height }` 多在事件对象顶层 |
| Android | 多在 `e.detail.{ width, height }` |

**统一写法**：`const detail = e.detail || e`

### 6.3 组件 `autoLoad` / `autoShow` 速查

| 组件 | 属性 | iOS | Android |
|------|------|-----|---------|
| Banner | `autoLoad` | 默认 true，load 后自动 render | 同左 |
| Banner | `autoShow` | 无 | 可选，等同再触发加载 |
| Feed | `autoLoad` | 默认 true | 默认 false，需显式 `:autoLoad="true"` |
| Feed | `autoShow` | 无 | **render 前需 true**（当前实现） |

---

## 7. 典型场景示例

### 7.1 任务页：点击看激励

```javascript
async onWatchAd() {
  loadRewardAd({ codeId: this.rewardCodeId })
  // 也可在 onAdEvent 里收到 load/cached 后再 show
  showRewardAd()
}

// onAdEvent 内
if (evt.adType === 'reward' && evt.phase === 'reward' && isOk(evt)) {
  this.grantCoins()
}
```

### 7.2 资讯列表：顶部 Banner + 中部 Feed（nvue）

```text
scroller
  → modo-ad-banner（bannerHeight 初值 150，sizechange 更新）
  → 列表项 …
  → modo-ad-feed（feedHeight 初值 280；Android 记得 autoShow）
```

### 7.3 仅 `.vue` 业务页需要 Banner（不推荐）

```javascript
import { bindAdSlotBySelector, loadBannerAd, showBannerAd } from '@/js_sdk/ad-reward/index.js'

async onReady() {
  await bindAdSlotBySelector(this, '#banner-slot', 'banner')
  loadBannerAd({ codeId: this.bannerCodeId })
  showBannerAd()
}

onPageScroll() {
  bindAdSlotBySelector(this, '#banner-slot', 'banner', { layoutOnly: true })
}
```

缺点：易抖、易 `9040008`；见 [vue与nvue-嵌入式广告方案.md](./vue与nvue-嵌入式广告方案.md)。

---

## 8. 错误码与排查

| errCode | 含义 | 处理 |
|---------|------|------|
| `9040001` | SDK 未 start 就 load | 先 `startAdSdk`，或监听 `sdk` + `start` |
| `9040002` | `appId` 为空或未 init | 检查 `initAdSdk` |
| `9040003` | `codeId` 或 slot 为空 | 检查广告位 ID / bind 参数 |
| `9040004` | 未 load 就 show | 先 load，等 `load` 成功再 show |
| `9040005` | 该类型 **iOS 未实现** | 换已实现类型或仅 Android 调用 |
| `9040006` | 拿不到 Activity / VC | 确保 App 前台页面调用 |
| `9040008` | overlay 槽位未 bind 或宽高为 0 | 改用 nvue 组件 |
| `9040010` | GroMore 本地配置异常 | 检查 Android assets 配置 |

**`isPluginAvailable() === false`**

- manifest 未勾选插件  
- 未使用自定义基座 / 未重装含插件的 APK  
- 修改原生插件后未重新打包

---

## 9. 参考路径

| 说明 | 路径 |
|------|------|
| 本文档 | `docs/ad-reward/API.md` |
| 对接与离线打包 | `docs/ad-reward/对接指南.md` |
| JS 封装源码 | `js_sdk/ad-reward/index.js` |
| 测试广告位 ID | `common/ad-reward-defaults.js` |
| **推荐 Demo** | `pages/ad-reward/demo-native.nvue` |
| `.vue` overlay 对照 | `pages/ad-reward/demo.vue` |
| 原生插件包 | `nativeplugins/Modo-AdReward/` |

---

## 附录：Module 方法一览（`js_sdk` 已导出）

```
isPluginAvailable
initAdSdk, startAdSdk, isAdSdkReady
loadRewardAd, showRewardAd, destroyRewardAd
loadSplashAd, showSplashAd, destroySplashAd
loadFullScreenAd, showFullScreenAd, destroyFullScreenAd
loadInterstitialAd, showInterstitialAd, destroyInterstitialAd
bindAdSlot, bindAdSlotBySelector
loadBannerAd, showBannerAd, destroyBannerAd
loadFeedAd, showFeedAd, destroyFeedAd
loadDrawAd, showDrawAd, destroyDrawAd
onAdEvent, offAdEvent, formatAdEventLog
```

**nvue 组件（模板直接使用，无需 js_sdk 导入）**

```
<modo-ad-banner />
<modo-ad-feed />
```

---

*文档版本：与 `Modo-AdReward` 插件 1.0.0 及当前 iOS / Android 实现对齐；Feed Android `autoShow` 行为变更时请同步更新 §6。*
