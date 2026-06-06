# 控制组件文档

iOS 18+ **控制中心 Control Widget** 在 NativeBility / **ControlWidgetCore** 中的接入说明。与 [桌面小组件](../小组件/README.md)、灵动岛 **无代码依赖**；Extension 建议 **分开 Target**。

---

## 架构总览

```text
用户点击控制中心按钮
  ↓
扩展进程：宿主 AppIntent.perform
  ↓ ControlWidgetCoreHostedJump（可选写 App Group + OpenURLIntent）
系统用 URL Scheme 拉起主 App
  ↓
主 App：ControlWidgetCoreBootstrap / HYControlWidgetMainAppWire（防 strip）
  ↓
游戏：control/default → query → getInfo（按 intentName 分支）
  ↓
业务（如自动收花）：游戏层 + 服务端（非 Extension 内执行）
```

| 层级 | 模块 | 职责 |
|------|------|------|
| **SDK** | Pod `ControlWidgetCore`（`HYWidget/ControlWidgetCore`） | App Group 写盘、`HostedJump.perform`、`Bootstrap` wire |
| **宿主** | 如 `HuaYuann/build/ios/proj/HYControlWidget/` | **宿主 `AppIntent` + `ControlWidget`**、URL、文案、`intentName` |
| **游戏** | NativeBility `PluginAdapter_control` | `query` / `getInfo` / `delegate` 读盘并消费 |

---

## 文档清单

### 产品与方案

| 文件 | 内容 |
|------|------|
| [01-产品与能力说明.md](./01-产品与能力说明.md) | 自动收花等产品目标、边界、用户流程、验收 |
| [02-程序设计与接入说明.md](./02-程序设计与接入说明.md) | **已实现** App Group 协议、`control` 插件 API、TTL；游戏层状态机建议 |
| [03-测试用例.md](./03-测试用例.md) | 真机：跳转、query/getInfo、TTL、误触发、收花回归 |
| [04-基础概念与创建接入步骤.md](./04-基础概念与创建接入步骤.md) | ControlWidget / AppIntent / TTL；Xcode 创建步骤 |

### 实施与排障（花园已验证，**优先**）

| 文件 | 内容 |
|------|------|
| [05-控制中心顺序链路.md](./05-控制中心顺序链路.md) | 点击顺序、零件白话、新增按钮步骤 |
| [06-最小接入与配置.md](./06-最小接入与配置.md) | 新 App 清单、Deployment、plist |
| [07-花园工程跳转主App与排障.md](./07-花园工程跳转主App与排障.md) | HuaYuann 文件对照、排障表、URL |

### SDK 源码旁文档

| 路径 | 说明 |
|------|------|
| `HYWidget/ControlWidgetCore/README.md` | Pod API |
| `HYWidget/ControlWidgetCore/控制中心-宿主双Target-Intent-必读-防踩坑.md` | **真机不跳 App 时先读** |
| `HYWidget/ControlWidgetCore/控制中心与Intent白话说明.md` | Intent / wire / OpenURLIntent 概念 |

---

## 当前推荐架构（已验证）

**真机要点**：`ControlWidgetButton(action:)` **必须绑宿主工程内的 `AppIntent`**，`perform` 内调 `ControlWidgetCoreHostedJump`。勿在 Bundle 里仅用 `ControlWidgetCoreOpenAppControl`（绑 Pod `ControlWidgetCoreButtonIntent`）——常见 **完全不拉起主 App**。

花园按钮示例：`jump`（打开并写盘）、`jumpWrite`（打开并写盘，游戏可读 `intentName` 分支）。

---

## 关键技术点 / 难点（速查）

| 难点 | 说明 | 处理 |
|------|------|------|
| **纯 Pod Intent 不跳 App** | App Intents 元数据须在宿主+扩展对齐 | 宿主双 Target `AppIntent` + `Control`；见 SDK 必读 |
| **`AppIntent.title` 字面量** | 不能 `rawValue` / 计算属性 | 与 `HYControlWidgetIntentName` case 同名，如 `"jump"` |
| **打开主 App** | 不能只空 `perform` | `HostedJump` → `OpenURLIntent` + 主 App `CFBundleURLTypes` |
| **wire / Release strip** | Debug 跳、Release 不跳 | `MainAppWire` + `ControlWidgetCoreBootstrap`；`_ = Intent.self` |
| **App Group 读写不一致** | 扩展写了、主 App query 为 false | 主/扩展 `NTControlCenterAppGroupSuite` 一致；日志 `[control]` |
| **磁盘标记消费** | `query` 读后删子键 | 同一次启动先 `query` 再 `getInfo`；调试 `query` 用 `{}` |
| **TTL 30s** | 旧点击误触发 | `PluginAdapter_control` 过期 purge；勿长期留 `ControlWidget` |
| **控件缓存** | 改 kind/Intent 后行为不变 | 用户删除控制中心旧控件再添加 |
| **与桌面小组件混 Target** | 部署版本、Bundle 职责混乱 | 控制中心单独 Extension，Deployment **18.0** |

---

## 已实现 Native API（摘要）

插件：`control` / `default`

| API | 返回要点 |
|-----|----------|
| `query` | `{ fromControlCenter: bool, matchedIntentName: string }`；命中则**消费磁盘** |
| `getInfo` | `{ intentName, ts, isControlOpen }` 或 `{}`；可用 query 内存缓存 |
| `delegate` | 清除 `ControlWidget` 目录（可指定 `intentName`） |

App Group：`UserDefaults(suiteName:)` → 根键 `ControlWidget` → 子键 `intentName` → `{ ts, intentName, isControlOpen }`。

---

## 花园工程路径

| 路径 | Target |
|------|--------|
| `build/ios/proj/HYControlWidget/HYControlWidgetOpenApp.swift` | 主 App + **HYControlWidgetExtension** |
| `build/ios/proj/HYControlWidget/HYControlWidgetBundle.swift` | 仅扩展 `@main` |

`build/` 可能被 `.gitignore` 忽略，以本机 Xcode 工程为准；见 [07](./07-花园工程跳转主App与排障.md)。
