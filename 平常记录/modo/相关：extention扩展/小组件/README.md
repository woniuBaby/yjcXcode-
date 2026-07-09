# 桌面小组件文档

本目录描述当前 **HYWidget** 桌面小组件落地方案（种花 v3、珍珠采集、公会竞赛），与早期「扁平快照 / 大号种花 / 双 SDK」方案无关内容已移除。

## 架构总览

```text
游戏 / JS
  ↓ widget/default（request | update | end | query | …）
PluginAdapter_widget（NativeBility）
  ↓ v3 校验：{ type, data } 或 sections[]
WidgetCommon
  ├─ WidgetMgr          → widget/widget_snapshot.json（种花 schema v3）
  ├─ PearlCollectMgr    → widget/pearl_snapshot.json
  ├─ GuildCompetitionMgr → widget/guild_competition_snapshot.json
  └─ WidgetKitBridge    → reloadTimelines（6 个 kind）
Widget Extension（HYWidgetExtension）
  └─ WidgetUI
       ├─ FlowerGarden/     种花 Small + Medium
       ├─ PearlCollect/     珍珠 Small + Medium
       ├─ GuildCompetition/ 公会 Small + Medium
       └─ Shared/           通用竖栏、设计 Token、图片加载
```

**不是两个 SDK**，而是三层：

| 层 | 模块 | 谁用 |
|----|------|------|
| 能力入口 | NativeBility `PluginAdapter_widget` | 主 App / JS |
| 共享能力 | `WidgetCommon`（Pod） | 主 App + Extension |
| 展示 UI | `WidgetUI`（Pod） | 仅 Widget Extension |

主 App 与 Extension **必须同一 App Group**；Extension **只读**快照，不负责写业务数据。

## 文档清单

| 文件 | 内容 |
|------|------|
| [01-产品与能力说明.md](./01-产品与能力说明.md) | 产品目标、种花/珍珠/公会边界、与灵动岛关系、验收 |
| [02-程序设计与接入说明.md](./02-程序设计与接入说明.md) | 分层、v3 协议、快照文件、Pod/Target、插件 API |
| [03-刷新机制与排查.md](./03-刷新机制与排查.md) | Timeline、reload、倒计时/水滴滚动、日志与排障 |
| [04-测试用例.md](./04-测试用例.md) | 真机用例、v3 三栏、多业务回归 |
| [05-基础概念与APP连调.md](./05-基础概念与APP连调.md) | Entry/Timeline 概念、连调步骤、字段与 UI 对应 |

代码侧说明：

| 路径 | 说明 |
|------|------|
| `HYWidget/WidgetCommon/` | 快照、解析、刷新桥 |
| `HYWidget/WidgetUI/` | 各业务 Widget + `WidgetUIDesktopBundle` |
| `HYWidget/WidgetUI/FlowerGarden/README.md` | 种花模块文件索引 |
| `NativeBility/.../PluginAdapter_widget.m` | JS 入口与 Mock |

## 已注册的 Widget kind

| kind | 业务 | 尺寸 |
|------|------|------|
| `HYGardenFlowerSmall` | 种花 | Small（仅种植栏） |
| `HYGardenFlowerMedium` | 种花 | Medium（种植 \| 花艺 \| 水滴） |
| `HYPearlCollectSmall` / `Medium` | 珍珠 | Small / Medium |
| `HYGuildCompetitionSmall` / `Medium` | 公会竞赛 | Small / Medium |

种花 **无 Large**；写种花快照后 `reloadGardenWidgets()` 会刷新上表 **全部 6 个 kind**（函数名历史遗留，实际 reload 珍珠/公会一并刷新）。

## 与灵动岛的关键差异

| 维度 | 灵动岛 | 桌面小组件 |
|------|--------|------------|
| 框架 | ActivityKit | WidgetKit |
| 更新 | 活动内 update / 推送 | 主 App 写快照 + `reloadTimelines` + Timeline |
| 数据 | App Group | App Group（独立 JSON，**不与 activity 自动同步**） |
| Extension | Live Activity | Widget Extension（`WidgetUIDesktopBundle`） |

## 关键技术点（速查）

1. **仅 v3 写入**：`request`/`update` 必须 `{ "type", "data" }`（或调试 `sections[]`）；旧扁平字段返回 `fail`。
2. **按栏 merge**：同一份 `widget_snapshot.json` 内 `flowerPlant` / `flowerSale` / `flowerWaterDrop` / `flowerCompetition` 独立更新，互覆盖单栏字段而非整文件盲写（实现见 `WidgetMgr.applySectionPacket`）。
3. **倒计时锚点**：`sectionUpdatedAtMs` + `timeBar.thumb`（剩余秒）或水滴 `timeBar.track`（间隔秒）；非旧版全局 `endAtMs` 扁平字段。
4. **Timeline 补帧**：种植结束、花艺出售结束、水滴逐滴恢复等由 `GardenFlowerLoader` 生成未来 `Entry`，到点切换无需再 push。
5. **无快照占位文案**：Extension 按系统首选语言（`zh`→中文，其它→英文）；有快照后以 `topTxt`/`bottomTxt` 为准（见 `GardenFlowerWidgetLocale`）。
6. **服务端配色**：`ITextItem.style.color` 可改标题/底栏静态字色与倒计时色；无值时用 `WidgetTokens.Text` 默认色。
7. **图片**：`img.normal` / `http` 路径作 App Group UserDefaults key 映射本地文件（`WidgetImageLoader`）；栏内插图以 Asset 为主。
8. **刷新节流**：`WidgetReloadThrottler` 默认最短间隔约 1s，避免密集 `reload` 被系统合并丢弃。
