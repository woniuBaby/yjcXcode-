# 能力 SDK 插件标准化 / 流程化长期计划

更新日期：2026-07-13  
项目范围：`modo-native-ability-ios` / `HuaYuann` / `HYWidget` 关联接入  
状态：进行中  
负责人接力说明：如果当前会话、账号或额度中断，下一位接手者先读本文件，再从“下一步”继续。

当前断点：2026-07-13 已完成 33 / 33 个插件可恢复级主流程文档。能力 SDK 插件标准化主文档应落在 `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin`；`docs/小组件`、`docs/控制组件`、`docs/integrations/live-activity` 属于专项接入 / 宿主集成文档，不再归入插件 `references`。后续不要再给每个插件新增空泛检查小节；代码核对只用于发现差异，最终产出必须是协议、mock、错误码、依赖、验证和恢复步骤。

恢复能力判断：如果现在删除 `Plugin/Widget` 代码，仅按当前 `docs/nt+ability/Plugin/Widget/` 可以恢复主要插件入口、API 清单、WidgetCommon 调用链、mock 方向、`query`、`getInfo`、公会竞赛和最小测试链路；但还不能保证完全等价恢复。缺口是字段细表、Swift Manager 存储细节、App Group 文件结构、错误码全集、mock 完整 JSON 和旧联调材料向标准文档的提炼。

---

## 目标

这份文档的目标不是记录某一次代码改动，而是把 NativeBility 能力 SDK 的插件建设变成可检查、可恢复、可复制的流程。

核心目标：

- 新增一个插件时，有固定流程可以照着走。
- 修改一个插件时，知道必须同步哪些协议、mock、文档和验证。
- 删除一个插件时，至少能根据文档、清单、接口说明和接入示例高度还原。
- 做局部能力改造时，能先局部检测，再分步骤检测，最后进入全局能力检测。
- 任意 AI 助手或开发者接手时，不需要重新猜测项目上下文。

---

## 长期原则

- 能力相关代码变更必须同步检查文档：插件 API、注册名、请求 / 响应结构、mock 数据、错误码、回调字段、Widget / Control 数据协议、接入示例。
- 文档不是“改完补一句”，而是插件可恢复能力的一部分。
- 每个插件最终都应该有一张“能力卡片”，记录它的边界、入口、数据结构、依赖、验证方式和恢复步骤。
- 能力卡片必须标注复杂度：少分支能力可以轻量记录，多分支能力必须拆分支矩阵。
- 先局部试点，再批量推进；不要一口气全局重写导致不可验证。
- 新增、删除、重命名字段时必须用 `rg` 扫旧字段、旧类型、旧 API 名称和旧示例。
- 后续按批次自动推进，不要每做一步都问用户；只有遇到破坏性操作、业务代码变更、归属冲突或大规模重命名时才暂停确认。

---

## 自动推进协议

后续执行这项长期工程时，默认按下面规则自主推进。

### 不需要逐步询问的事项

- 读取代码和文档。
- 生成插件清单、分支数统计、文档覆盖状态。
- 对已有文档先做代码核对；若与当前代码相符，只补缺口和恢复信息，不重写整份文档。
- 在对应插件目录补 PRD、实现文档、测试用例。
- 将历史参考资料归入对应插件的 `references/`。
- 更新本长期计划的进度、下一步、断点。
- 更新 AI 规则日志。
- 使用 `rg` / `find` / `sed` 做静态验证。

### 必须暂停确认的事项

- 删除文件或目录。
- 修改业务代码。
- 移动大量文件并可能影响外部链接。
- 某份文档可能归属多个插件，且无法从代码目录或插件名判断。
- 需要安装依赖、联网、跑耗时构建或使用管理员权限。
- 发现现有文档和代码行为冲突，且无法判断应以哪边为准。

### 批次粒度

每次推进一个小批次，避免半途卡住：

- 低分支插件：每批 3-5 个。
- 中分支插件：每批 1-2 个。
- 多分支插件：每批 1 个，先补主文档，再补 provider / branch 文档。

每批固定产出：

- 插件文档覆盖表。
- 新增或补齐的 PRD / implementation / test-cases。
- references 归档说明。
- 可恢复等级：完整 / 部分 / 不足。
- 本文件“当前进度”和“下一步”更新。
- 最小验证结果。

### 推荐批次顺序

1. 已有低分支插件补齐：`Activity`、`Network`、`Event`、`BiReport`、`Permission`。
2. 当前已暴露但标准文档缺失的低分支插件：`Control`。
3. 其它单适配器插件：`Log`、`UMP`、`TRTC`、`VoiceTranslation`、`RealtimeVoiceTrans`、`RiskPerception`、`MusicEngine`、`Map`、`Media`、`Player`、`FileFuc`、`FaceRecognition`、`AccountVerification`、`file`。
4. 中分支插件：`Basic`、`Pay`、`Push`、`Reward_video`、`Storage`、`game`、`entry`、`DeepLink`。
5. 多分支插件：`Login`、`Share`、`Report`。

### 每批执行模板

```text
1. 扫代码目录：Plugin/<PluginName>
2. 扫文档目录：docs/nt+ability/Plugin/<PluginName>
3. 统计 adapter / API / version / mock / 错误码
4. 对已有文档做代码核对，优先修正不符处；不要把核对结论写成独立交付
5. 判断复杂度：L1 / L2 / L3
6. 补齐或修正三份标准文档
7. 有历史资料则归入 references，并在标准文档中引用
8. 扫旧字段、旧路径、旧示例残留
9. 更新长期计划和日志
10. 汇报本批结果与下一批建议
```

---

## 能力复杂度分级

不同能力的文档粒度不能一刀切。

少分支能力：

- 示例：灵动岛、小组件、控制组件中只有少量固定入口的能力。
- 特征：API 数量少，状态机简单，mock 场景有限，消费端路径清晰。
- 文档要求：一张能力卡片可以覆盖主流程、数据协议、mock、验证和恢复步骤。

多分支能力：

- 示例：登录能力，可能同时包含手机号、验证码、Apple、微信、游客、Token 刷新、切换账号、注销、失败重试等十多个分支。
- 特征：同一插件内有多个业务入口或多个登录渠道，回调结构、错误码、权限依赖、第三方 SDK、边界状态差异明显。
- 文档要求：不能只写一份总说明，必须增加“分支矩阵”，逐分支记录 API、入参、回调、错误码、mock、验证和恢复步骤。

复杂度判定建议：

| 等级 | 判定条件 | 文档要求 |
| --- | --- | --- |
| L1 少分支 | 1-3 个核心 API，状态少，消费端单一 | 能力卡片 + 示例 JSON + 最小验证 |
| L2 中分支 | 4-8 个 API 或多种状态组合 | 能力卡片 + 分支矩阵 + 重点 mock |
| L3 多分支 | 9 个以上 API / 分支，或依赖第三方登录、支付、权限链路 | 能力卡片 + 完整分支矩阵 + 错误码表 + mock 覆盖表 + 恢复步骤 |

多分支能力的验收标准：

- 每个分支都能在文档中找到入口、调用条件、入参、出参、错误码和 mock。
- 每个分支都标明是否依赖第三方 SDK、系统权限、AppDelegate / SceneDelegate 回调或本地存储。
- 删除或重建插件时，可以按分支逐个恢复，而不是只恢复主流程。
- 全局检测时按分支扫描旧 API、旧字段、旧错误码和旧文档示例。

---

## 插件可恢复标准

一个插件如果被删除，想要“高度还原”，至少需要以下信息完整存在：

- 插件名称、注册名、所属目录、核心文件列表。
- 对外 API 列表：API 名、入参、出参、异步回调、错误码、边界条件。
- 前端 / 服务端 / 宿主调用示例。
- `query` 或能力发现接口中如何暴露该插件。
- mock 数据：正常态、空态、异常态、边界态。
- 数据协议：字段含义、类型、必填 / 可选、特殊值含义。
- 本地存储、App Group、权限、Info.plist、entitlements、第三方 SDK 等依赖。
- UI / Widget / Control 等消费端如何读取这份数据。
- 最小验证命令或检查步骤。
- 删除时需要清理的文件、引用、文档和旧协议残留。
- 重建步骤：按什么顺序创建文件、注册 API、补 mock、补文档、跑验证。

如果上述内容缺失，说明插件“可恢复能力”不足，后续要补齐。

---

## 新增插件标准流程

新增一个能力插件时，按以下流程推进：

1. 明确需求边界
   - 插件解决什么问题。
   - 谁调用：前端、服务端、App、Widget、Control、Live Activity 或其他宿主。
   - 是否需要权限、App Group、后台能力、第三方 SDK。

2. 定义插件能力卡片
   - 插件名、注册名、目录、文件列表。
   - 能力复杂度：L1 / L2 / L3。
   - 分支矩阵：多分支能力必须填写。
   - API 清单、入参、出参、错误码、回调结构。
   - mock 数据和调试入口。

3. 编码接入
   - 新增 `PluginAdapter_xxx` 或对应插件文件。
   - 注册 API。
   - 补充 `query` 或能力发现入口。
   - 补 mock 数据。
   - 接入消费端读取逻辑。

4. 文档同步
   - 更新能力 SDK 文档。
   - 更新插件接入说明。
   - 更新调试 / 联调说明。
   - 更新字段协议和示例 JSON。

5. 验证
   - 静态扫描旧字段、旧 API 和旧示例。
   - 运行可执行的最小构建、语法检查或类型检查。
   - 如果无法运行构建，记录原因和未验证风险。

6. 日志与接力
   - 如涉及 AI 工作流规则，更新桌面 AI 日志。
   - 如果是长期任务，更新本文件的“进度”和“下一步”。

---

## 删除插件标准流程

删除一个插件前，先确认是否满足可恢复标准。

删除前检查：

- 能力卡片是否存在。
- API 协议是否已沉淀到文档。
- mock 数据是否有历史记录或文档示例。
- 消费端引用是否能完整定位。
- 是否有迁移方案或替代能力。
- 是否需要保留版本说明、兼容策略或废弃公告。

删除执行：

- 删除插件文件和注册入口。
- 删除 `query` / 能力发现中的暴露项。
- 删除 mock 数据。
- 删除消费端读取逻辑。
- 删除文档中的旧接入示例，或移动到废弃说明。
- 使用 `rg` 扫描旧插件名、旧注册号、旧字段、旧类型。

删除后验证：

- 插件目录无残留引用。
- 文档无旧 API 误导。
- mock 返回不再包含该插件。
- 相关构建、语法检查或静态扫描通过。

---

## 分阶段计划

### 阶段 1：局部试点

目标：先选一个已经在改的插件做标准化样板。

建议试点：`Widget` 插件 / 公会竞赛小组件。

已知相关路径：

- `/Users/lin/Documents/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin/Widget/PluginAdapter_widget.m`
- `/Users/lin/Documents/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin/Widget/PluginAdapter_widget.h`
- `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/小组件/`
- `/Users/lin/Desktop/gameProject/HYWidget/WidgetCommon/WidgetCommon/`
- `/Users/lin/Desktop/gameProject/HYWidget/WidgetUI/WidgetUI/`
- `/Users/lin/Downloads/widget.ts`

阶段 1 检查项：

- [x] 固化“能力改动必须检查文档”到 Codex / Cursor 规则。
- [x] 小组件公会竞赛从 `flower_competition` 调整为独立 `competition`。
- [x] `guildRequest` / `guildUpdate` mock 补充 `timeBar` / `taskBar`。
- [x] `query` 能看到公会竞赛组件。
- [x] 小组件文档同步更新。
- [ ] 为 `Widget` 插件补一张完整能力卡片。
- [ ] 补齐 `Widget` 插件删除 / 重建步骤。
- [ ] 对 `Widget` 插件做一次旧字段全量扫描并记录结果。

### 阶段 2：插件清单化

目标：把所有能力插件列出来，形成插件索引和状态表。

建议先生成清单：

```bash
find /Users/lin/Documents/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin -maxdepth 2 -type f -print
```

重点扫描：

```bash
rg -n "adapterTMessageForApi|registerApi|self.name|self.version|PluginCallBack|sucessWithResult|failWithMsg" /Users/lin/Documents/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin
```

阶段 2 输出物：

- [ ] 插件总清单。
- [ ] 每个插件的能力卡片。
- [ ] 每个插件的文档覆盖状态。
- [ ] 每个插件的 mock 覆盖状态。
- [ ] 每个插件的恢复等级：完整 / 部分 / 不足。

### 阶段 3：全局能力检测

目标：检查整个能力 SDK 是否存在协议、文档、mock、消费端不一致。

全局检测方向：

- API 注册名和文档 API 名是否一致。
- mock 返回结构和真实协议是否一致。
- `query` / 能力发现入口是否完整。
- 文档示例字段是否仍存在于代码中。
- 代码字段是否都有文档说明。
- Widget / Control / Live Activity 消费端是否仍引用旧字段。
- 删除或重命名后的旧字符串是否清理干净。

常用扫描：

```bash
rg -n "flower_competition|progressA|progressB|remainingTaskCount|totalTaskCount" /Users/lin/Documents/modo-native-ability-ios /Users/lin/Desktop/gameProject/HYWidget
```

```bash
rg -n "query|mock|adapterTMessageForApi|guildRequest|guildUpdate" /Users/lin/Documents/modo-native-ability-ios/NativeBility/docs
```

阶段 3 输出物：

- [ ] 全局能力一致性报告。
- [ ] 文档缺口清单。
- [ ] mock 缺口清单。
- [ ] 废弃字段 / 废弃 API 残留清单。
- [ ] 修复优先级。

---

## 能力卡片模板

后续每个插件建议补一份能力卡片，可放在能力仓库文档目录，也可先放在本 AI 记录目录。

```markdown
# [插件名] 能力卡片

## 基本信息

- 插件名：
- 注册名：
- 所属目录：
- 核心文件：
- 当前版本：
- 调用方：
- 消费端：

## API 清单

| API | 入参 | 出参 | 回调 | 错误码 | 说明 |
| --- | --- | --- | --- | --- | --- |
|     |     |     |     |     |      |

## 复杂度与分支矩阵

- 复杂度等级：L1 / L2 / L3
- 分支数量：
- 是否需要完整分支矩阵：是 / 否

| 分支 | 触发条件 | API / opt | 入参差异 | 出参 / 回调差异 | 错误码 | Mock | 验证 |
| --- | --- | --- | --- | --- | --- | --- | --- |
|     |          |           |          |                 |        |      |      |

## 数据协议

| 字段 | 类型 | 必填 | 特殊值 | 说明 |
| --- | --- | --- | --- | --- |
|     |     |      |        |      |

## Mock 数据

- 正常态：
- 空态：
- 异常态：
- 边界态：

## 文档覆盖

- 产品 / 能力说明：
- 程序设计 / 接入说明：
- 联调说明：
- 排查说明：

## 恢复步骤

1. 创建文件：
2. 注册 API：
3. 补 mock：
4. 补 query / 能力发现：
5. 接入消费端：
6. 补文档：
7. 跑验证：

## 验证命令

```bash

```

## 风险与注意事项

- 
```

---

## 当前进度

| 日期 | 范围 | 状态 | 说明 |
| --- | --- | --- | --- |
| 2026-07-10 | HuaYuann / Codex 规则 | 已完成 | `AGENTS.md` 增加能力文档同步规则。 |
| 2026-07-10 | HuaYuann / Cursor 规则 | 已完成 | `.cursor/rules/ability-doc-sync.mdc` 增加能力文档同步规则。 |
| 2026-07-10 | 小组件 / 公会竞赛 | 已完成局部改造 | `competition` 独立于种植小组件，`guildRequest` / `guildUpdate` mock 和文档已同步。 |
| 2026-07-10 | 长期计划文档 | 当前新增 | 本文件用于后续接力和全局检测。 |
| 2026-07-10 | 能力复杂度分级 | 已补充 | 增加 L1 / L2 / L3 和多分支能力分支矩阵要求，登录类能力按 L3 处理。 |
| 2026-07-10 | Widget 插件文档 | 已开始 | 已按代码目录 `Plugin/Widget` 轻量补齐 PRD、实现、测试文档，覆盖当前 API、mock、query、公会竞赛和恢复要点。 |
| 2026-07-10 | 散落专项文档归档 | 已撤回 | 旧方向曾把 `docs/小组件`、`docs/控制组件`、`integrations/live-activity` 归到插件 `references`；2026-07-13 需求变更后已复原，不再按此方向推进。 |
| 2026-07-10 | Control 插件标准文档 | 已完成首版 | 已补 `control-prd.md`、`control-implementation.md`、`control-test-cases.md`，覆盖 getInfo/delegate、App Group 协议、iOS 18、ControlWidgetCore 依赖、恢复步骤和测试。 |
| 2026-07-10 | 插件清单与第一批粗扫 | 旧记录，不能作为完成标准 | 新增 `plugin-inventory.md`；曾覆盖 `Activity`、`Network`、`Event`、`BiReport`、`Permission` 的主链路，但缺少可恢复细节。 |
| 2026-07-10 | 第二批 L1 粗扫 | 旧记录，不能作为完成标准 | 曾覆盖 `Log`、`UMP`、`TRTC`、`VoiceTranslation`、`RealtimeVoiceTrans` 的主链路，但缺少可恢复细节。 |
| 2026-07-10 | 第三批 L1 粗扫 | 旧记录，不能作为完成标准 | 曾覆盖 `RiskPerception`、`MusicEngine`、`Map`、`Media`、`Player` 的主链路，但缺少可恢复细节。 |
| 2026-07-10 | 第四批 L1 与异常目录粗扫 | 旧记录，不能作为完成标准 | 曾覆盖 `FileFuc`、`FaceRecognition`、`Max`、历史 `file`、`AccountVerification`；异常目录已标记，但仍需可恢复细节。 |
| 2026-07-10 | 第五批 L2 主文档粗扫 | 旧记录，不能作为完成标准 | 曾覆盖 `Push`、`Storage`、`Pay`、`Reward_video`、`DeepLink`；`Pay` / `Reward_video` 版本差异已修正，但 provider 细节不足。 |
| 2026-07-10 | 第六批 L2 主文档粗扫 | 旧记录，不能作为完成标准 | 曾覆盖 `Basic`、`entry`、`game`；`Basic` 版本差异已修正，`game` 标记为接近 L3 的高复杂度插件。 |
| 2026-07-10 | 第七批 L3 主文档粗扫 | 旧记录，不能作为完成标准 | 曾覆盖 `Login`、`Share`、`Report`；`Report` 补入 `appsflyer` 分支，`Login` 清单补入 `taptap` 分支，但仍需逐 provider 可恢复文档。 |
| 2026-07-10 | 插件文档整体进度 | 粗扫 33 / 33 | 统计口径仅表示插件主链路扫过；不代表删除代码后可恢复。后续剩余为逐 provider 字段、错误码、mock、恢复步骤深查。 |
| 2026-07-11 | 文档产出口径纠偏 | 已修正 | 插件文档中已移除空泛检查小节；后续不再把代码核对结论当交付物，必须补协议、mock、错误码、依赖、验证和恢复步骤。 |
| 2026-07-11 | Max / Pay 可恢复补齐 | 已补主流程 | `Max` 已补 AppLovin MAX 初始化、隐私同意、load/play、delegate、Firebase waterfall 和恢复步骤；`Pay` 已补互斥 adapter、StoreKit、KeyChain 缓存、续费事件、`chuxin` / `muu` wrapper 和恢复步骤。 |
| 2026-07-11 | L1 第一批返工 | 已补主流程 | 按清单顺序补 `Activity`、`BiReport`、`Event`、`Network`、`Permission` 的删除后恢复步骤和主流程可恢复信息。 |
| 2026-07-11 | L1 第二批返工 | 已补主流程 | 按清单顺序补 `Control`、`FaceRecognition`、`FileFuc`、`Log`、`Map` 的删除后恢复步骤和主流程可恢复信息。 |
| 2026-07-11 | L1 第三批返工 | 已补主流程 | 按清单顺序补 `Media`、`MusicEngine`、`Player`、`RealtimeVoiceTrans`、`RiskPerception` 的删除后恢复步骤和主流程可恢复信息。 |
| 2026-07-11 | L1 第四批返工 | 已补主流程 | 按清单顺序补 `TRTC`、`UMP`、`VoiceTranslation`、`Widget`、`file`；其中 `file` 标记为历史声明可恢复、完整业务不可恢复。下一批先处理结构异常 `AccountVerification`，再进入 L2。 |
| 2026-07-11 | 结构异常 AccountVerification | 已补主流程 | 已补异常目录、模块名 `oauth`、adapter `zhifubao`、`AFServiceSDK`、`OAuthScheme`、`simpleOAuth`、返回结构、恢复步骤和恢复验证。下一批进入 L2。 |
| 2026-07-13 | L2 Basic | 已补主流程 | 已补三 adapter 注册矩阵、`loadPipVideo` / `playPipVideo`、`MDPipManage`、WebView、配置、通知、`chuxin` / `muu`、mock、恢复步骤和恢复验证。下一批继续 `Push` / `Storage`。 |
| 2026-07-13 | L2 Push | 已补主流程 | 已补双 adapter 注册、`apple/init` token 返回、`applePushIdEvent`、`localPush/init` 全量重建、LocalPush 模型、`PushManager` 排程、mock 现状、恢复步骤和恢复验证。当前可恢复级进度 `25 / 33`，约 `75.8%`，剩余 `8` 个插件；下一批继续 `Storage`。 |
| 2026-07-13 | L2 Storage | 已补主流程 | 已补三 adapter 注册、`default` 的 `NSUserDefaults storage_` 前缀、`memory` 内存字典、`file` 实际 KeyChain、`Msg_storage` 错误码、mock 覆盖、恢复步骤和恢复验证。当前可恢复级进度 `26 / 33`，约 `78.8%`，剩余 `7` 个插件；下一批继续 `DeepLink` 或 `Reward_video` 剩余 provider。 |
| 2026-07-13 | L2 DeepLink | 已补主流程 | 已补 `deeplink/fetchDeferred`、`Result_fetchDeferred`、`adjust` 的 `AdjustFetchDeferred` 通知等待链、`fb` 禁用保留实现、`google` / `tiktok` 禁用空实现、`Msg_deeplink`、mock 现状、恢复步骤和恢复验证。当前可恢复级进度 `27 / 33`，约 `81.8%`，剩余 `6` 个插件；下一批继续 `Reward_video` 剩余 provider。 |
| 2026-07-13 | L2 Reward_video | 已补主流程 | 已补 `topon`、`ironsource`、`muu` 三 provider；新增 `ironsource` 的 LevelPlay 初始化、load/play/adRevenue、错误码和缺依赖分支；新增 `muu` 的 `ON_MUU_vedioFinish` 事件回流、成功失败结构、mock 现状、恢复步骤和恢复验证。当前可恢复级进度 `28 / 33`，约 `84.8%`，剩余 `5` 个插件；下一批继续 `entry` / `game`。 |
| 2026-07-13 | L2 entry | 已补主流程 | 已补 `rn` / `egret` / `cocos` 三 provider；新增 base 共用逻辑、`fetch` 入参映射、`fetchProgressInfoBefore` / `fetchProgressInfo`、`reBoot` status 返回、`cocos` 禁用状态、mock 现状、恢复步骤和恢复验证。当前可恢复级进度 `29 / 33`，约 `87.9%`，剩余 `4` 个插件；下一批继续 `game`。 |
| 2026-07-13 | L2 game | 已补主流程 | 已补 `egret` / `rn` / `cocos` / `unity` 四 adapter 注册矩阵、插件级 API 清单、事件总线、zip/缓存、论坛、相册、设备 ID、客服、mock 现状、恢复步骤和恢复验证。当前可恢复级进度 `30 / 33`，约 `90.9%`，剩余 `3` 个插件；下一批进入 L3，从 `Login` 开始。 |
| 2026-07-13 | L3 Login | 已补主流程 | 已补 15 个 provider：`apple`、`twitter`、`fb`、`vk`、`wx`、`qq`、`oneclick`、`zhifubao`、`google`、`tiktok`、`gamecenter`、`chuxin`、`line`、`muu`、`taptap`；新增插件源码文件清单、启用门槛矩阵、唯一 mock 分支、provider 恢复步骤和恢复验证。当前可恢复级进度 `31 / 33`，约 `93.9%`，剩余 `2` 个插件；下一批继续 `Report`。 |
| 2026-07-13 | L3 Report | 已补主流程 | 已补当前注册 10 个 adapter 与 1 个源码保留 adapter：`report`、`reyun`、`toutiao`、`adjust`、`firebase`、`gravity`、`oceanengine`、`chuxin`、`gdt_action`、`muu`，以及未注册的 `appsflyer`；补齐禁用/未注册状态、唯一 mock 分支、回调差异、第三方依赖和恢复步骤。当前可恢复级进度 `32 / 33`，约 `97.0%`，剩余 `1` 个插件；下一批继续 `Share`。 |
| 2026-07-13 | L3 Share | 已补主流程 | 已补 13 个 adapter：`fb`、`wx`、`system`、`taptap`、`xhs`、`weibo`、`qq`、`chuxin`、`twitter`、`line`、`instagram`、`muu`、`vk`；修正旧清单漏掉 `vk`、`fb` / `muu` 版本差异、`taptap` 初始化误写，新增 `vk-share.md`，补齐 mock 现状、事件/callback 差异、业务分支和恢复步骤。当前可恢复级进度 `33 / 33`，约 `100.0%`，剩余 `0` 个插件。 |
| 2026-07-13 | 散落旧 README 清理 | 已撤回 | 旧方向曾删除 `docs/控制组件/README.md`、`docs/小组件/README.md`、`docs/integrations/live-activity/README.md`；后续已按用户新需求复原，插件可恢复级进度保持 `33 / 33`。 |
| 2026-07-13 | `requirements` / `integrations` 目录判定 | 需求变更，暂停归并 | 用户确认 `requirements` / `integrations` 保持原有目录内容和分类口径，暂不迁入插件文档；已恢复 `docs/README.md` 与 `docs/integrations/README.md` 的原说明。后续不要再把这两个目录作为插件归档清理目标。 |
| 2026-07-13 | 灵动岛 / 小组件 / 控制中心专项文档复原 | 已完成 | 已把 `Plugin/Activity/references/live-activity` 复原到 `docs/integrations/live-activity`，`Plugin/Widget/references/小组件` 复原到 `docs/小组件`，`Plugin/Control/references/控制组件` 复原到 `docs/控制组件`；插件目录不再保留这些专项 references，并修正相关相对链接。 |
| 2026-07-10 | Widget 插件能力卡片 | 待继续 | 后续可继续把三份文档提炼成单独能力卡片或补 provider/字段细表。 |
| 2026-07-10 | 全插件清单 | 已完成首版 | 已生成 `plugin-inventory.md`，后续继续补齐待核对模块名和分支矩阵。 |

---

## 下一步

优先从局部开始，不直接全局大改；后续默认按“自动推进协议”批量处理，不需要每一步询问。

1. 当前 33 个插件已完成可恢复级主流程文档；下一阶段转入“抽样复核 + 字段样例细化”，不再按插件数量推进。
2. 优先回头细化 `Widget` 插件字段表，尤其是种花、珍珠、公会竞赛的 `{ type, data }` 结构；小组件专项接入资料保留在 `docs/小组件/`，不要迁入插件 `references`。
3. 抽样复核 L3：`Share` / `Report` / `Login`，检查 provider 文档能否支撑单分支删除后恢复。

接手者开始命令建议：

```bash
sed -n '1,260p' /Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md
rg -n "adapterTMessageForApi|guildRequest|guildUpdate|query" /Users/lin/Documents/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin/Widget /Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Widget
rg -n "整体进度|当前进度|剩余待处理|33 / 33|100.0%" /Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md
rg -n "adapterTMessageForApi|删除后恢复要点|删除后恢复验证|mock 现状" /Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Share
rg -n "query|mock|guildRequest|guildUpdate|competition" /Users/lin/Documents/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin/Widget /Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Widget
```

---

## 接力规则

每次继续这个长期工程时，至少更新三处：

- 本文件的“当前进度”。
- 本文件的“下一步”。
- 如果修改了 AI 工作流规则，再更新 `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/skills/skills-rules-change-log.md`。
- 上述桌面 AI 日志是当前用户本机个人工作流目录；若在同事机器或不存在该目录的环境中接手，可跳过桌面同步，不应因此阻塞能力文档或代码任务。

如果只修改业务代码或能力文档，不一定要更新 AI 规则日志，但必须在最终回复中说明文档检查和验证情况。
