# 05 基础概念与 APP 连调

## 1. 这篇文档解决什么

面向首次联调同学，说明：

- WidgetKit 核心概念（Entry / Timeline / Provider）。
- 当前工程 **v3 按栏** 数据如何进 App Group、如何驱动种花 UI。
- 与 JS、`PluginAdapter_widget` Mock 的对接方式。
- 网络图、颜色、中英文、排查入口。

无需设计图；按本文步骤可在真机看到 Medium 三栏联动。

---

## 2. WidgetKit 基本概念

### 2.1 Widget Extension

小组件跑在 **HYWidgetExtension** 进程，不共享游戏内存。标准链路：

```text
主 App / JS → Native 插件 → WidgetMgr 写 App Group
→ reloadTimelines → GardenFlowerProvider.getTimeline
→ GardenFlowerLoader → GardenFlowerCardView
```

### 2.2 Entry

```text
Entry = date（展示时刻） + GardenFlowerDisplayModel（三栏展示模型）
```

同一 Timeline 可有多条 Entry：例如「种植中 @ now」「完成 @ endDate」。

### 2.3 Timeline

```text
Timeline = [Entry...] + policy
```

种花实现（`GardenFlowerLoader.timeline`）：

- 合并种植/花艺/水滴的**到期时间点**生成额外 Entry。
- `policy`：有未来条目用 `.atEnd`，否则 `.after(now + 30s)` 或 30min 兜底。

### 2.4 TimelineProvider

`GardenFlowerWidgets.swift` 内 `GardenFlowerProvider`：

| 方法 | 用途 |
|------|------|
| `placeholder` | 添加小组件预览 |
| `getSnapshot` | 快捷预览；`isPreview` 可用 Mock |
| `getTimeline` | 真机展示，走 `GardenFlowerLoader` |

---

## 3. 当前工程模块地图

```text
WidgetCommon/
  WidgetGardenSnapshot.swift   # v3 类型、解析、merge
  WidgetCommon.swift           # WidgetMgr、Pearl、Guild、Bridge
  WidgetTextItem.swift         # text + style.color

WidgetUI/
  WidgetUIDesktopBundle.swift  # @main 注册 6 Widget
  FlowerGarden/                # 种花全链路
  PearlCollect/                # 珍珠
  GuildCompetition/            # 公会
  Shared/
    WidgetDesignTokens.swift   # WidgetTokens 默认色/字号
    WidgetSectionColumn.swift    # 竖栏 + 圆环 + 倒计时视图
```

种花 **无 Large**；Extension 入口只有 Small / Medium 两个 `GardenFlowerWidget`。

---

## 4. v3 协议与栏位（联调必背）

### 4.1 写入包（唯一合法）

```json
{
  "type": "flower_plant",
  "data": {
    "status": 1,
    "topTxt": { "text": "荷月·池光夏梦" },
    "bottomTxt": { "text": "{$time}", "style": { "color": "#E85D4A" } },
    "img": { "normal": "widget_flower_planting", "completed": "widget_flower_done" },
    "timeBar": { "thumb": 120, "track": 0 },
    "sectionUpdatedAtMs": 1716000000000
  }
}
```

| 字段 | 含义 |
|------|------|
| `status` | 0 空闲 / 1 进行中 / 2 完成 |
| `topTxt` / `bottomTxt` | 标题、底栏；`{$time}` 表示倒计时占位 |
| `img.normal` / `completed` | Asset 名或 http key |
| `timeBar.thumb` | 种植/花艺：剩余秒；水滴恢复：见下 |
| `timeBar.track` | 水滴：每滴间隔秒（种植/花艺常为 0） |
| `bar` | 仅水滴：`thumb` 当前滴数，`track` 上限 |
| `sectionUpdatedAtMs` | 本栏写入时刻（ms），倒计时锚点 |

`type` 枚举：`flower_plant` | `flower_sale` | `flower_water_drop` | `flower_competition`。

### 4.2 磁盘快照（merge 后）

路径：`{AppGroup}/widget/widget_snapshot.json`

```json
{
  "schemaVersion": 3,
  "updatedAtMs": 1716000000000,
  "flowerPlant": { ... },
  "flowerSale": { ... },
  "flowerWaterDrop": { ... },
  "flowerCompetition": null
}
```

每次 `request`/`update` 只改对应键，**不**整文件覆盖其它栏。

### 4.3 UI 映射（Medium）

| 栏位 type | 屏幕位置 | 组件 |
|-----------|----------|------|
| `flower_plant` | 左 | `WidgetSectionColumn` + 种植图 |
| `flower_sale` | 中 | 花艺出售 |
| `flower_water_drop` | 右 | 水滴圆环 `WidgetWaterRing` |

Small 仅 `flower_plant`，`compactCountdownOnly = true`。

映射代码：`GardenFlowerMapping.swift`（`WidgetGardenSectionPayload` → `GardenFlowerSectionState`）。

---

## 5. APP 连调流程（推荐顺序）

### 5.1 前置

1. 主工程 Pod 引入 `WidgetCommon` + `WidgetUI`，Extension target 同上。
2. entitlements App Group 与主 App 一致，例如 `group.com.xxx.huazhiwu`。
3. 启动时 `WidgetMgr.shared.configure(appGroupId:)`（珍珠/公会同理）。
4. 桌面添加 **种花 Medium**（测三栏）。

### 5.2 用 Native 调试面板 / Mock

`PluginAdapter_widget` 已注册测试数据（`adapterTMessageForApi`）：

| apiName | 作用 |
|---------|------|
| `request` | `sections` 一次写入种植+花艺+水滴（约 30s/20s/10s 倒计时） |
| `update` | 仅更新水滴栏（merge） |
| `end` | 清除三栏 |
| `pearlRequest` / `guildRequest` | 珍珠、公会冒烟 |

调用链与线上一致：`applyGardenWidgetData` → `WidgetMgr` → `reloadGardenWidgets`。

### 5.3 JS 侧（概念）

```javascript
// 伪代码：widget/default
native.call('widget', 'default', {
  opt: 'request',
  data: {
    type: 'flower_plant',
    data: { status: 1, timeBar: { thumb: 60, track: 0 }, sectionUpdatedAtMs: Date.now(), ... }
  }
});
```

多栏调试：

```javascript
data: {
  sections: [
    { type: 'flower_plant', data: { ... } },
    { type: 'flower_sale', data: { ... } },
    { type: 'flower_water_drop', data: { bar: { thumb: 2, track: 5 }, timeBar: { track: 30, thumb: 0 }, ... } }
  ]
}
```

**务必**每次推送带当前 `sectionUpdatedAtMs`，否则倒计时锚点错误。

### 5.4 验证写入

- 插件返回 `{ status: success }`。
- `widget/default` query 或主 App 调 `WidgetMgr.querySnapshot()`。
- 真机 Files（若可访问容器）或日志确认 `schemaVersion == 3`。

### 5.5 验证展示

1. 点 Mock `request` → Medium 三栏应在数十秒内先后进入完成/满滴等态。
2. Extension Console 过滤 **`HYWG`**（`GardenFlowerDebugLog`）。
3. 杀主 App，等待种植倒计时结束 → 应自动变完成（Timeline 路径 B）。

---

## 6. 网络图片

1. 主 App 将 `img.normal` 以 `http...` 下载到 App Group 目录。
2. 在 App Group `UserDefaults` 记录 `url → localPath`（与 `WidgetMgr.resolvedAppGroupSuiteName()` 一致）。
3. Extension `WidgetImageLoader` 读取本地文件展示。

栏内默认插图多为 **Asset 名**（如 `widget_flower_planting`），不经过下载。

---

## 7. 中英文占位

无快照或 `hasGardenSectionPayload == false` 时：

- 文案来自 `GardenFlowerWidgetCopy`（`GardenFlowerWidgetLocale.swift`）。
- 规则：**系统首选语言** 以 `zh` 开头 → 中文，否则英文。
- 有快照后一律用服务端 `topTxt`/`bottomTxt`，不看地区。

验英文：系统设置 → 语言 → English（无需改地区）。

---

## 8. 颜色与默认 Token

| 场景 | 颜色来源 |
|------|----------|
| 标题 `topTxt` | `style.color` → 否则 `WidgetTokens.Text.title` |
| 底栏静态字 | `bottomTxt.style.color` → 否则 `WidgetTokens.Text.subtitle` |
| 倒计时 `{$time}` | `bottomTxt.style.color` → 种植主题色 → `WidgetTokens.Text.countdown` |

服务端颜色格式：`#RRGGBB`（实现见 `WidgetTextItem` 解析）。

---

## 9. 数据变化 → UI 更新（对照表）

| 业务动作 | APP 侧 | 小组件侧 |
|----------|--------|----------|
| 开始种植 | `request` plant 栏 status=1 + thumb + sectionUpdatedAtMs | reload + Timeline 到点完成 |
| 种植完成 | status=2 或等 Timeline | 完成图/文案 |
| 花艺出售 | `flower_sale` 同上 | 中栏 |
| 水滴变化 | `flower_water_drop` bar + timeBar.track | 右栏圆环滚动 |
| 清空 | `end` 或删 JSON | 占位文案 |
| 仅改文案 | `update` 单栏 | reload 后即时 |

---

## 10. 联调检查清单

- [ ] App Group 主/扩展一致，且已 `configure`。
- [ ] 写入包含 `type` + `data`，非旧扁平字段。
- [ ] `sectionUpdatedAtMs` 为毫秒、接近当前时间。
- [ ] Medium 已添加；测 Small 时只看左栏。
- [ ] 倒计时用真机 iOS 16+。
- [ ] 日志可见 `HYWG timeline:` / `loadEntry:`。
- [ ] 不依赖灵动岛 Activity 自动更新。

---

## 11. 延伸阅读

- [02-程序设计与接入说明.md](./02-程序设计与接入说明.md) — API 与文件表
- [03-刷新机制与排查.md](./03-刷新机制与排查.md) — Timeline / reload 细节
- [04-测试用例.md](./04-测试用例.md) — 用例 ID
- `HYWidget/WidgetUI/FlowerGarden/README.md` — 源文件职责表
