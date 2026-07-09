# uniapp_plugins_demo 文档

老 uni-app 原生插件 Demo 的文档入口。工程路径默认指本仓库 `uniapp_plugins_demo`（离线 Xcode 集成脚本在 `mdunidemo/unidemo` 也有一份，逻辑相同）。

---

## 一、离线 iOS 自定义基座

| 文档 | 说明 |
|------|------|
| **[离线iOS打包-完整协作说明.md](./离线iOS打包-完整协作说明.md)** | **主文档**：你/助手分工、各 `.sh` 脚本、Archive、HBuilderX 基座 |
| [HBuilderX跑没变化-排查.md](./HBuilderX跑没变化-排查.md) | 运行后页面不更新、热更新失败 |

已删除/合并的过时文档：`离线打包-下一步.md`、`离线iOS基座-协作流程.md`、`offline-ios-config.md`、`自定义基座-iOS.md`、`archive-基座-你来做.md`（内容已并入上表主文档或不再需要）。

---

## 二、穿山甲广告 Modo-AdReward

| 文档 | 说明 |
|------|------|
| [ad-reward/README.md](./ad-reward/README.md) | 广告文档目录 |
| **[ad-reward/对接指南.md](./ad-reward/对接指南.md)** | **业务接入**：能力、manifest、基座、Demo |
| [ad-reward/API.md](./ad-reward/API.md) | JS Module API、nvue 组件、事件、错误码 |
| [ad-reward/ios-native-plugin.md](./ad-reward/ios-native-plugin.md) | iOS 原生实现与 Demo 路由 |
| [ad-reward/vue与nvue-嵌入式广告方案.md](./ad-reward/vue与nvue-嵌入式广告方案.md) | **必读**：模版广告、`.vue` 限制 |
| [ad-reward/android-实现计划.md](./ad-reward/android-实现计划.md) | Android 计划 |

**产品结论（简要）**

- 穿山甲 **模版** Banner/Feed：只提供**完整原生 View**，不能拆图/文做自绘；老项目 **`.vue` 整页改 nvue` 成本高** 时，嵌入式模版广告无理想方案（overlay 仅对照）。
- **激励 / 开屏 / 插屏** 等：`.vue` 可用 module API。
- `pages/ad-reward/material-demo`（自绘物料）为**已证伪的实验**，勿作产品方案。

---

## 三、三方分享 Modo-AppShare

| 文档 | 说明 |
|------|------|
| [app-share/README.md](./app-share/README.md) | 分享文档目录 |
| [app-share/对接指南.md](./app-share/对接指南.md) | 接入、基座、plist |
| [app-share/API.md](./app-share/API.md) | `shareImage` 参数、opentype、事件 |
| [app-share/实现计划.md](./app-share/实现计划.md) | 分平台实现记录 |

iOS 最低 **14.0**（相册选图分享抖音）。修改 `nativeplugins` 后重打基座，流程同 [离线iOS打包-完整协作说明.md](./离线iOS打包-完整协作说明.md)。

---

## 四、其它（按需）

| 目录 | 说明 |
|------|------|
| [photo/](./photo/) | 选图相关说明 |
| [oneclick/](./oneclick/) | 一键登录相关 |

---

## 五、脚本速查（`scripts/`）

| 脚本 | 何时用 |
|------|--------|
| `prepare-offline-hello-archive.sh` | 改 native 后、Archive **前**（助手） |
| `install-ios-debug-ipa.sh` | 拿到 Archive 的 ipa **后**（助手） |
| `setup-offline-hello.sh` | 仅当要把 www **打进 Hello**（常规**不用**） |
| `sync-www-to-hello.sh` | Xcode ⌘R 直跑 Hello 时同步 JS |
| `repack-ios-debug-ipa.sh` | ⚠️ 仅对比用，**不可**安装（破坏签名） |
| `copy-pangle-ios-sdk.sh` | 更新穿山甲 SDK |
| `run-all-copy-appshare-sdk.sh` | 更新分享 SDK |

详见 [离线iOS打包-完整协作说明.md](./离线iOS打包-完整协作说明.md) §五。
