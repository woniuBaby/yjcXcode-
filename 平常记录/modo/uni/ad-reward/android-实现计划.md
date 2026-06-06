# 穿山甲 ad-reward · Android 实现计划

> 目标：在 `unidemo` 老 uni 工程中，实现与 **iOS 当前模式** 同等的能力与 Demo UI。  
> 业务参考：`uniapp_plugins_demo/uni_modules/ad-reward/utssdk/app-android/`（Kotlin 已完整实现）。

文档目录：[README.md](./README.md)

---

## 一、要对齐的 iOS 现状

### Demo UI（`pages/ad-reward/platform`）

| 入口 | iOS 现状 | Android 目标 |
|------|----------|--------------|
| **原生组件方案（推荐）** | `demo-native.nvue` | `demo-native.nvue`（同文件，条件编译或共用） |
| **WebView 对照方案** | `demo.vue?platform=ios` | `demo.vue?platform=android` |
| 平台页文案 | 三卡片 + SDK 说明 | 与 iOS 一致，Android badge 改为「已支持」 |

### Demo 页区块（两页顺序一致）

1. SDK（init / start）
2. 激励视频（load / show / destroy）
3. Banner
4. 信息流 Feed
5. 事件日志（固定高度 180px，内容增多自动滚到底部）
6. 其他类型（仅 `.vue`：开屏 / 全屏 / 插屏 / Draw）

### 能力矩阵

| 能力 | iOS | Android 目标 |
|------|-----|--------------|
| `initAdSdk` / `startAdSdk` | ✅ | ✅ 移植 `CsjAdBridge` |
| 激励视频 module API | ✅ | ✅ 移植 `CsjRewardHandler` |
| Banner/Feed **nvue 原生组件** | ✅ `modo-ad-banner` / `modo-ad-feed` | ✅ 新建 `UniComponent` |
| Banner/Feed overlay（`.vue`） | ✅ 对照 | ✅ 移植 `CsjAdContainerRegistry` + 测量逻辑 |
| 开屏 / 全屏 / 插屏 / Draw | ⏳ iOS 占位 | ✅ x 工程已有，一并移植 |
| 事件 `ModoAdRewardEvent` | ✅ | ✅ 同 JSON 格式 |
| JS `js_sdk/ad-reward/index.js` | 双端共用 | **无需改 API 面** |

---

## 二、架构对照

```text
                js_sdk/ad-reward/index.js
                          │
          ┌───────────────┴───────────────┐
          ▼                               ▼
   ModoAdRewardModule (iOS)      ModoAdRewardModule (Android)
   DCUniModule                    UniModule
          │                               │
   ModoAdBridge                   CsjAdBridge (Kotlin)
          │                               │
   Handler / Component            Handler / Component
          │                               │
   BUAdSDK (ObjC)                 TTAdSdk (Kotlin)
```

| iOS 文件 | Android 对应（x 工程已有） |
|----------|---------------------------|
| `ModoAdBridge.m` | `CsjAdBridge.kt` |
| `ModoAdRewardHandler.m` | `CsjRewardHandler.kt` |
| `ModoAdBannerHandler.m` | `CsjBannerHandler.kt` |
| `ModoAdFeedHandler.m` | `CsjFeedHandler.kt` |
| `ModoAdSlotHost.m` | `CsjAdContainerRegistry.kt` + 坐标绑定 |
| `ModoAdBannerComponent.m` | **新建** `ModoAdBannerComponent.kt` |
| `ModoAdFeedComponent.m` | **新建** `ModoAdFeedComponent.kt` |
| `ModoAdEventEmitter.m` | `CsjAdEventEmitter.kt` |

---

## 三、分步实施

### 步骤 0：SDK 与目录

1. 从穿山甲平台下载 **Android GroMore SDK**（与 x 工程版本对齐）
2. AAR 放入 `nativeplugins/Modo-AdReward/android/libs/`
3. 拷贝 x 工程 `csj/*.kt` → `android/src/main/java/com/modo/uni/adreward/csj/`
4. 拷贝 `AndroidManifest.xml` 合并项、`res/xml/ad_reward_file_paths.xml`

```bash
# 参考 x 工程
uniapp_plugins_demo/uni_modules/ad-reward/utssdk/app-android/
```

### 步骤 1：UniModule 桥接

新建 `ModoAdRewardModule.kt`（或 Java），继承 `io.dcloud.feature.uniapp.common.UniModule`：

- `package.json` → `"class": "com.modo.uni.adreward.ModoAdRewardModule"`
- 导出方法与 iOS `ModoAdRewardModule.m` **同名**：`initAdSdk`、`startAdSdk`、`loadRewardAd`…
- 回调事件：`mUniSDKInstance.fireGlobalEventCallback("ModoAdRewardEvent", map)`

可先只通 **激励**，再逐个广告类型。

### 步骤 2：激励视频（与 iOS Demo 联调）

1. 移植 `CsjRewardHandler.kt`
2. `demo.vue?platform=android` 点加载/展示
3. 确认日志区有 `load` / `show` / `reward` 事件

### 步骤 3：开屏 / 全屏 / 插屏 / Draw

按 x 工程 Handler 逐个注册到 `CsjAdBridge`：

| Handler | x 工程文件 |
|---------|------------|
| 开屏 | `CsjSplashHandler.kt` |
| 全屏 | `CsjFullScreenHandler.kt` |
| 插屏 | （x 工程对应 Handler） |
| Draw | `CsjDrawHandler.kt` |

`.vue` Demo 已有按钮，Android 实现后可直接点测。

### 步骤 4：Banner / Feed · nvue 原生组件（推荐，对齐 iOS）

老 uni Android 原生组件：继承 `io.dcloud.feature.uniapp.ui.component.UniComponent`（或当前文档要求的基类）。

#### 4.1 注册组件

`package.json` → `android.plugins` 增加：

```json
{ "type": "component", "name": "modo-ad-banner", "class": "com.modo.uni.adreward.ModoAdBannerComponent" },
{ "type": "component", "name": "modo-ad-feed", "class": "com.modo.uni.adreward.ModoAdFeedComponent" }
```

#### 4.2 组件行为（对齐 iOS）

| 项 | 要求 |
|----|------|
| 属性 | `codeId`、`autoLoad` |
| 容器 | 组件根 `FrameLayout` |
| load 时机 | `autoLoad=true` 且 SDK ready；否则 retry |
| show | 模板 Banner/Feed render 后 `container.addView(adView)` |
| 事件 | render 成功 → fire `sizechange`，`{ height, width }` |
| 销毁 | 组件 unload 时 remove ad、释放引用 |

#### 4.3 Demo

- `demo-native.nvue` 在 Android 真机运行（与 iOS 同一文件）
- 验证：列表快速滑动不抖、高度自适应

### 步骤 5：Banner / Feed · `.vue` overlay（对照）

移植 x 工程逻辑：

1. `bindAdSlot(slot, rect)` → `CsjAdContainerRegistry.bind(slot, container)`
2. JS 侧已有 `bindAdSlotBySelector`（与 iOS 相同）
3. `loadBannerAd` / `showBannerAd` 使用 registry 中的容器

**说明**：与 iOS 一样，此路径在 WebView 快速滑动会抖，仅作对照；文档见 [vue与nvue-嵌入式广告方案.md](./vue与nvue-嵌入式广告方案.md)。

Android WebView 测坐标时注意：

- `uni.createSelectorQuery` 的 `boundingClientRect`
- 状态栏 / 沉浸式 / 软键盘
- 与 iOS 相同：**不推荐产品使用**

### 步骤 6：manifest 与离线打包（本工程实际方式）

1. `manifest.json` 勾选 **Modo-AdReward**
2. Android 权限：网络、存储等（合并 `android/AndroidManifest.xml`）
3. **离线打包**（非 HBuilderX「制作自定义基座」）：
   - HBuilderX 发行 → 生成本地打包资源 `unpackage/resources/__UNI__9F05C09/www/`
   - 拷贝到 Android Studio 工程 `assets/apps/__UNI__9F05C09/www/`（如 `HBuilder-Integrate-AS/simpleDemo`）
   - 原生插件 Kotlin/AAR 在 `modoAdReward` 模块；`dcloud_uniplugins.json` 注册 module + `modo-ad-banner` / `modo-ad-feed`
   - Android Studio **Run / 打 APK**
4. 测试 ID：`common/ad-reward-defaults.js` → `AD_REWARD_ANDROID_DEFAULTS`

### 步骤 7：platform 页与文档

1. `platform.vue` Android 卡片改为「已支持」并链到 `demo-native` + `demo?platform=android`
2. 可选：Android 也显示「nvue 推荐 / vue 对照」两入口（与 iOS 对称）

---

## 四、测试 ID（Android Demo 默认）

见 `AD_REWARD_ANDROID_DEFAULTS`：

| 字段 | 值 |
|------|-----|
| appId | `5829982` |
| appName | `超甜` |
| rewardCodeId | `104105702` |
| bannerCodeId | `104105921` |
| feedCodeId | `104103962` |

包名：`cn.modocommunity.android`（与 `HBuilder-Integrate-AS` 离线工程一致）

正式包须替换为媒体平台申请 ID；包名与穿山甲后台一致。

---

## 五、验收清单

### Module API

- [ ] `initAdSdk` / `startAdSdk` 成功，事件 `sdk` / `start`
- [ ] 激励 load → show → reward → close
- [ ] 开屏 / 全屏 / 插屏 / Draw 各至少走通一次

### nvue 原生组件（P0）

- [ ] `demo-native.nvue` Banner 加载、展示、销毁
- [ ] Feed 加载、展示、销毁
- [ ] `@sizechange` 后高度更新
- [ ] 快速滑动列表 **无明显抖动**
- [ ] SDK 未 init 时组件等待 retry，不闪退

### `.vue` 对照（P1）

- [ ] `bindAdSlot` + load/show 能出广告（慢滑）
- [ ] 文档标明 overlay 不可作为正式方案

### UI 一致性

- [ ] `platform` 三入口文案与 iOS 一致
- [ ] `demo.vue` 与 `demo-native.nvue` 区块顺序一致
- [ ] 日志区 180px 固定高、新日志自动滚动

### 工程

- [ ] `package.json` plugins 含 module + 2 components
- [ ] 自定义基座可安装运行
- [ ] 事件 JSON 与 iOS / `interface.uts` 一致

---

## 六、工作量预估

| 阶段 | 内容 | 依赖 |
|------|------|------|
| P0 | Module + 激励 + SDK 初始化 | x Kotlin 拷贝 |
| P0 | nvue Banner/Feed 组件 | P0 + 研究 UniComponent |
| P1 | 开屏 / 全屏 / 插屏 / Draw | x Handler |
| P1 | `.vue` overlay bindAdSlot | x ContainerRegistry |
| P2 | 离线打包 / 多渠道 / ProGuard 规则 | 穿山甲文档 |

---

## 七、风险与注意

1. **GroMore 版本**：Android ADN adapter 须与瀑布流配置一致  
2. **ProGuard**：混淆规则按穿山甲文档 keep  
3. **nvue 组件名**：须与 iOS 一致（`modo-ad-banner`），便于同一套 Demo 模板  
4. **不要**在 `.vue` 强推嵌入式 Banner——与 iOS 结论相同，见 [vue与nvue-嵌入式广告方案.md](./vue与nvue-嵌入式广告方案.md)  
5. **JS 层**：`js_sdk/ad-reward/index.js` 已双端共用，Android 只需保证原生方法名一致  

---

## 八、相关路径

| 项 | 路径 |
|----|------|
| uni 工程 | `/Users/lin/Documents/uni_app/mdunidemo/unidemo` |
| x Android 参考 | `uniapp_plugins_demo/uni_modules/ad-reward/utssdk/app-android/` |
| 插件 Android 目录 | `nativeplugins/Modo-AdReward/android/` |
| JS 封装 | `js_sdk/ad-reward/index.js` |
| Demo 默认 ID | `common/ad-reward-defaults.js` |
| iOS 插件文档 | [ios-native-plugin.md](./ios-native-plugin.md) |
