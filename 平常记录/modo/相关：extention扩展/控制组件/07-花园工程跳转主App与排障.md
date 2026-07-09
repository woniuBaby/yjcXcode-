# 花园工程：控制中心跳转主 App — 实施与排障

> **真机不跳时先读**：`HYWidget/ControlWidgetCore/控制中心-宿主双Target-Intent-必读-防踩坑.md`  
> **理解链路先读**：[05-控制中心顺序链路](./05-控制中心顺序链路.md)

本文记录 **HuaYuann（花园）** 当前可工作的控制中心实现，便于排障与在新 Extension 中复用。

---

## 1. 目标与工程位置

| 项 | 说明 |
|----|------|
| **目标** | 控制中心点击 → 稳定拉起主 App；游戏可读 `intentName` |
| **系统** | iOS 18+ Control Widget + AppIntents |
| **扩展源码** | `build/ios/proj/HYControlWidget/` |
| **Xcode** | Target `HYControlWidgetExtension` + 主 App `HuaYuann` |
| **SDK** | Pod `ControlWidgetCore`（`HYWidget/ControlWidgetCore`） |

---

## 2. 当前文件与职责（以此为准）

| 文件 | Target | 作用 |
|------|--------|------|
| `HYControlWidgetOpenApp.swift` | **主 App + 扩展** | `HYControlWidgetIntentName`、`HYControlWidgetConfig`、`HYGardenControlDefinitions`；**宿主** `HYControlWidgetOpen*Intent` + `*Control`；`HYControlWidgetMainAppWire` |
| `HYControlWidgetBundle.swift` | **仅扩展** | `@main`，注册 `HYControlWidgetJumpControl()` 等 **宿主 Control** |
| `Info.plist`（扩展） | 扩展 | `NSExtension`、App Group suite 等 |
| 主 App `Info.plist` | 主 App | `CFBundleURLTypes`（scheme） |

**已废弃 / 勿再对照的旧名**（文档或历史分支可能出现）：

- `HYOpenMainAppIntent.swift`、`HYControlWidgetControl.swift` — 已合并为 `HYControlWidgetOpenApp.swift` 内类型
- 仅 `ControlWidgetCoreOpenAppControl` + Pod `ButtonIntent` 作为唯一入口 — 真机验证 **完全不拉起 App**
- `ControlWidgetCoreRegistry`、动态 `WidgetBundle` 拼按钮 — 已从 SDK 移除
- `PluginMgr` 内置控制中心 wire — 已迁至 `ControlWidgetCoreBootstrap`

---

## 3. 推荐实现要点（当前版）

1. **打开主 App**：宿主 `AppIntent.perform` 内调用  
   `ControlWidgetCoreHostedJump.perform(definition: …)`  
   → 可选写 App Group → `OpenURLIntent(url)`。
2. **按钮绑定**：`ControlWidgetButton(action: HYControlWidgetOpenJumpIntent())` 等 **宿主类型**，不是 `ControlWidgetCoreButtonIntent()`。
3. **双 Target**：`HYControlWidgetOpenApp.swift` 勾选 **HuaYuann + HYControlWidgetExtension**。
4. **wire**：`HYControlWidgetMainAppWire.wire()` 内  
   `_ = HYControlWidgetOpenJumpIntent.self`、`_ = HYControlWidgetOpenJumpWriteIntent.self`、`_ = ControlWidgetCoreButtonIntent.self`。
5. **`openAppWhenRun = true`** 可保留；**不要**只靠空 `perform`，必须 `OpenURLIntent`。

---

## 4. 常见问题

| 现象 | 优先排查 |
|------|----------|
| **完全不拉起主 App** | Bundle 是否用了 `ControlWidgetCoreOpenAppControl`；改回 **宿主 Intent + 宿主 Control**；删控制中心控件后重新添加 |
| 能进 App，`query` 为 false | App Group suite 不一致；`perform` 未写盘；`query` 参数勿带错误 `intentName`（调试用 `{}`） |
| Debug 正常 Release 不跳 | 主 App 是否链 Pod；`wire` 是否执行；Intent 是否双 Target |
| `title` 编译错误 | `AppIntent.title` 只能写字面量 `"jump"`，不能 `rawValue` / 计算属性 |
| `SWIFT_VERSION ''` | 主 App 加 Swift 后设 `SWIFT_VERSION`，链 `AppIntents.framework` |
| 改了代码控件不变 | 控制中心删除旧控件再添加；Clean Build |

---

## 5. URL Scheme（花园）

| 项 | 值 |
|----|-----|
| Scheme | `huayuan6746321359` |
| 示例 URL | `huayuan6746321359://openFromControlCenter` |
| 代码 | `HYControlWidgetConfig.openURL` |
| 主 App | `Info.plist` → `CFBundleURLTypes` 须一致 |

---

## 6. App Group 与游戏

- **写**：`ControlWidgetCoreHostedJump` → `writeToControlWidgetFolder`（`Jump` / `JumpWrite` 默认 `writeToGroup: true`）
- **读**：`control/default/query` → `getInfo` → 按 `intentName` 分支
- **结构**：`ControlWidget` → `jump` | `jumpWrite` → `{ ts, intentName, isControlOpen }`
- **消费**：`query` 命中后删子键；**30s** 过期 purge
- **suite**：plist `NTControlCenterAppGroupSuite`，或与 SDK 推导 `group.<主包BundleId>` 一致
- **调试**：`query` 用 `{}`；看 `[control]` 日志

---

## 7. Xcode 勾选清单

**扩展 `HYControlWidgetExtension`**

- [ ] Deployment **18.0**
- [ ] WidgetKit、SwiftUI、AppIntents
- [ ] `HYControlWidgetBundle.swift` 仅本 Target
- [ ] `HYControlWidgetOpenApp.swift` **不要**只勾扩展（须与主 App 双勾，见下）

**主 App `HuaYuann`**

- [ ] `HYControlWidgetOpenApp.swift` 勾选
- [ ] Pod `ControlWidgetCore`
- [ ] `CFBundleURLTypes`、App Group
- [ ] `AppIntents.framework`、`SWIFT_VERSION`

**双 Target 同一文件**

- [ ] `HYControlWidgetOpenApp.swift` → **HuaYuann + HYControlWidgetExtension**

---

## 8. Git / `build/` 目录

- 仓库 `.gitignore` 若忽略 `build/`，`HYControlWidget` 可能不进 Git；需对 `build/ios/proj/HYControlWidget/` 做 `!` 放行，或将源码迁到不被构建覆盖的目录（如 `native/engine/ios/`）并改 Xcode 引用。

---

## 9. 上线前自检

- [ ] 真机控制中心已添加控件，点击可进主 App
- [ ] `query`（`{}`）冷启动可得 `fromControlCenter` / 正确 `intentName`
- [ ] Archive / TestFlight 与 Debug 行为一致
- [ ] 改 URL / scheme 后主 App plist 已同步

---

## 10. 相关路径

| 路径 | 说明 |
|------|------|
| `build/ios/proj/HYControlWidget/` | 宿主控制中心源码 |
| `HYWidget/ControlWidgetCore/` | SDK |
| `NativeBility/docs/控制组件/` | 本目录文档 |

*文档与当前「宿主 Intent + SDK perform」实现一致；若调整架构请同步更新 [05-控制中心顺序链路](./05-控制中心顺序链路.md) 与本文件第 2 节。*
