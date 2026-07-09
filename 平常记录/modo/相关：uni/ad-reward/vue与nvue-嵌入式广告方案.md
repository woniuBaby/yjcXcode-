# 老 uni-app：Banner / Feed 嵌入式广告方案

> 适用：老 uni（非 uni-app x）在页面内**嵌入** Banner、信息流。  
> 文档目录：[README.md](./README.md)

---

## 零、产品结论（请先读）

| 方案 | 结论 |
|------|------|
| **穿山甲模版广告自绘物料**（拆图/文/视频自己画 UI） | ❌ **不可行**。官方明确：模版渲染只返回**完整 View**，不提供单独素材。`material-demo` / `loadBannerMaterial` 为实验，**勿上线**。 |
| **nvue + `<modo-ad-banner>` / `<modo-ad-feed>`** | ✅ 模版嵌入的**标准技术路径**（需页面为 nvue 或 subNVue 等原生上下文）。 |
| **老项目页面必须是 `.vue`、不能整页改 nvue`** | ⚠️ 嵌入式模版 Banner/Feed **无稳定方案**；`bindAdSlot` overlay 仅对照（快滑抖）。激励/插屏等全屏类不受影响。 |
| **`.vue` + overlay** | ⚠️ 技术上可试，**不推荐产品使用**。 |

---

## 一、当前 Demo 页面

| 页面 | 路径 | 渲染引擎 | Banner / Feed 实现 |
|------|------|----------|-------------------|
| 推荐 | `pages/ad-reward/demo-native.nvue` | **原生渲染（Weex）** | `<modo-ad-banner>` / `<modo-ad-feed>` |
| 对照 | `pages/ad-reward/demo.vue` | **WKWebView** | `bindAdSlot` + 原生浮层 overlay |
| 已废弃 | `pages/ad-reward/material-demo` | `.vue` 自绘物料实验 | 模版位无 meta，见 §零 |

平台入口 `pages/ad-reward/platform`；Android 见 [android-实现计划.md](./android-实现计划.md)。

---

## 二、核心问题：`.vue` 里的 `<view>` 不是原生 View

老 uni 的普通 `.vue` 页面运行在 **WKWebView** 中：

```html
<view id="banner-slot" class="ad-slot"></view>
```

这是 **DOM 节点**，不是 iOS 的 `UIView`，也不是 Android 的 `ViewGroup`。

穿山甲 Banner / Feed 模板广告最终必须是 **原生 View**（iOS：`BUNativeExpressBannerView` / `BUNativeExpressAdView`）。SDK **无法**把它直接 `addSubview` 到 WebView 里的某个 `<view>` 上。

因此插件若要在 `.vue` 里“看起来”放进灰框，只能走 **间接方案**。

---

## 三、`.vue` 方案：overlay（已验证不可接受）

### 做法

```text
1. JS 用 uni.createSelectorQuery 测量 DOM 灰框的 boundingClientRect
2. 把 left/top/width/height 传给原生 bindAdSlot
3. 原生在 UIWindow（或 VC.view）上创建浮层 UIView，位置对齐 Web 坐标
4. 广告 load/show 后 addSubview 到浮层
5. onPageScroll 时反复 bindAdSlot(layoutOnly) 同步位置
```

### 为何看起来能“放进灰框”

- 灰框只是 Web 里的占位样式（背景色 `#f0f0f0`）
- 真正的广告 View 在 **WebView 上方** 的另一层 UIKit 视图里
- 滚动时靠 JS 测量 + 原生改 frame **假装**跟着灰框走

### 弊端（实测）

| 问题 | 说明 |
|------|------|
| **快速滑动严重抖动** | Web 滚动与 UIKit 布局不同步；`onPageScroll` + 异步 `boundingClientRect` 存在帧延迟 |
| **坐标系 fragile** | 需处理 WebView 偏移、安全区、导航栏、`windowTop` 等；易算错或重复偏移 |
| **与 DOM 生命周期脱节** | `v-if` 销毁灰框后，浮层可能残留；reload 时易出现 `9040008` |
| **Feed 体验差** | 高度变化、 dislike、缩放等难以与 Web 布局联动 |
| **维护成本高** | 每种页面结构、横竖屏、键盘弹起都要单独调 |

本工程在 iOS 真机多次验证：**慢滑尚可，快速上下滑明显抖**，判定 **pass**，不作为产品方案。

### 仍保留在 demo.vue 的原因

- 展示 **module API**（`loadBannerAd` / `bindAdSlot`）的调用链
- 与 nvue 方案 **对照**
- 激励视频等 **全屏类广告** 不依赖容器，在 `.vue` 中完全可用

---

## 四、`.nvue` 方案：原生组件（推荐）

### 做法

插件注册 DCloud **原生组件**（`DCUniComponent`）：

```html
<modo-ad-banner codeId="104104931" :autoLoad="true" @sizechange="onBannerSizeChange" />
```

原生侧：

```objc
- (UIView *)loadView {
    return [[UIView alloc] init];  // 真实 UIView，由 uni 原生布局引擎排版
}
// render 成功后
[self.view addSubview:bannerView];
```

### 视图层级

```text
nvue 页面（原生渲染树）
└── modo-ad-banner / modo-ad-feed   ← uni 布局控制位置、宽高
    └── 插件 UIView 容器
        └── 穿山甲 SDK 原生广告 View
```

### 为何不会抖

| 对比项 | `.vue` overlay | `.nvue` 原生组件 |
|--------|----------------|------------------|
| 布局归属 | Web DOM + 独立浮层 | **同一原生渲染树** |
| 滚动 | Web 滚 + JS 同步坐标 | **原生列表/scroller 直接带动** |
| 容器 | 假绑定（坐标对齐） | **真容器**（插件 UIView 就是兄弟/子节点） |
| 高度变化 | 需手动重测 DOM | `@sizechange` 回传后改 style |

本工程 `demo-native.nvue` 已验证：**快速滑动无抖动**；Banner/Feed 可放在列表任意位置（含顶部 Banner + 中间 Feed）。

### 使用约束

1. **页面必须是 `.nvue`**（或支持原生 component 的 nvue 渲染上下文）
2. **不能**在普通 `.vue` 里写 `<modo-ad-banner>` 并期望稳定工作
3. 使用前须 **initAdSdk → startAdSdk**；组件内会等待 SDK ready 再 load
4. 修改 `nativeplugins` 后须 **重打自定义基座**

---

## 五、外部 uni 用户怎么接

### 嵌入式 Banner / Feed（推荐）

1. 新建 **nvue** 页面（或把广告区块放在 nvue 页）
2. 布局里直接放组件，**无需**传 `left/top`

```html
<modo-ad-banner
  class="banner"
  :style="{ height: bannerHeight + 'px' }"
  codeId="104104931"
  :autoLoad="true"
  @sizechange="onBannerSizeChange"
/>
```

```javascript
onBannerSizeChange(e) {
  const h = e.detail?.height || e.height
  if (h > 0) this.bannerHeight = h
}
```

3. 激励视频等全屏广告：同页调用 `loadRewardAd` / `showRewardAd` 即可（module API，与页面类型无关）

### 若业务页只能是 `.vue`

| 广告类型 | 是否可在 `.vue` 使用 |
|----------|---------------------|
| 激励 / 开屏 / 插屏 / 全屏 | ✅ module API，弹全屏或新 window |
| Banner / Feed **嵌入页面布局** | ❌ 应用 nvue 页或独立 nvue 子页面 |
| Banner / Feed overlay 对照 | ⚠️ 技术上可试，**不推荐**（抖动、9040008） |

可选折中：

- 主业务仍用 `.vue`
- 广告位单独用 **nvue 页面 / nvue 区块**（若工程支持 mix）
- 或 Banner 仅放 **固定底栏**（overlay 抖动略轻，仍非最佳）

---

## 六、高度自适应

模板广告 render 后尺寸可能与占位不同。插件在 render 成功时 fire `sizechange`：

```json
{ "height": 150, "width": 375 }
```

页面更新组件 `style.height` 即可；`demo-native.nvue` 已演示。

---

## 七、FAQ

**Q：灰框能不能改成原生 UIView？**  
A：在 `.vue` 里不能——灰框仍是 Web DOM。在 `.nvue` 里，`<modo-ad-banner>` **本身就是**原生 UIView 容器，不需要再套一层“灰框”占位（可用样式写背景色调试）。

**Q：能否把旧 demo.vue 原地改成 nvue？**  
A：可以，但会变为 nvue 页面（语法/样式部分需适配）。当前工程选择 **保留两个 Demo 页** 便于对照；产品接入时直接以 `demo-native.nvue` 为模板。

**Q：uni-app x 为什么没这个问题？**  
A：x 的 UTS 组件与原生一体；老 uni 的 `.vue` 仍是 WebView，这是架构差异，不是 SDK 差异。

**Q：Android 呢？**  
A：逻辑相同：`.vue` WebView 同样有 overlay 抖动问题；应用 **nvue + 原生组件** 或 Android 原生 View 嵌入。详见 [android-实现计划.md](./android-实现计划.md)。

---

## 八、相关代码位置

| 内容 | 路径 |
|------|------|
| nvue Demo | `pages/ad-reward/demo-native.nvue` |
| vue 对照 Demo | `pages/ad-reward/demo.vue` |
| Banner 组件 | `nativeplugins/Modo-AdReward/ios/ModoAdBannerComponent/` |
| Feed 组件 | `nativeplugins/Modo-AdReward/ios/ModoAdFeedComponent/` |
| overlay 槽位 | `nativeplugins/Modo-AdReward/ios/csj/ModoAdSlotHost.m` |
| JS bindAdSlot | `js_sdk/ad-reward/index.js` |
