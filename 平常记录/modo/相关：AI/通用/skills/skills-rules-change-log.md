# Skills / Rules 变更日志

用于记录 AI 工作流相关配置的变更，包括 `AGENTS.md`、`.codex/skills/`、`.cursor/skills/`、OpenSpec 配置、项目级规则和通用使用约定。

## 2026-08-08

### 明确个人 AI 日志为可选本机路径

标签：`#workflow` `#rules` `#agents-md`

变更对象：

- HuaYuann / NativeBility 的 `AGENTS.md`、`.cursor/rules/ability-doc-sync.mdc`
- NativeBility `docs/nt+ability/Plugin/README.md`

变更内容：

- 强调该路径仅维护者本机个人记录，不是仓库交付物。
- 目录不存在则跳过：不创建、不写入、不要求同事配置，不阻塞开发任务。

### 桌面 AI 工作区迁入「相关：AI」并统一路径

标签：`#workflow` `#codex` `#cursor` `#rules` `#agents-md` `#skills`

变更对象：

- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/ai-workspace-memory`（自桌面整夹迁入）
- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template`（自桌面整夹迁入）
- `/Users/lin/Documents/HuaYuann/AGENTS.md`
- `/Users/lin/Documents/HuaYuann/.cursor/rules/ability-doc-sync.mdc`
- `/Users/lin/Documents/modo-native-ability-ios/NativeBility/AGENTS.md`
- `/Users/lin/Documents/modo-native-ability-ios/NativeBility/.cursor/rules/ability-doc-sync.mdc`
- `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`

变更内容：

- `yjcXcode/平常记录` 已更名为 `记录/平常记录`；个人 AI 日志根目录统一为 `/Users/lin/Desktop/记录/平常记录/modo/相关：AI`。
- 桌面 `ai-workspace-memory`、`AI-Skills-rules-Template` 整夹迁入上述目录，与 `通用/` 并列，不合并内容。
- 已执行 `ai-workspace-memory/sync/install.sh`，重建 `~/.codex/memories` 软链到新路径。
- 同步更新仓库内 README / shared 文档 / memories 中的旧绝对路径，以及项目 AGENTS / Cursor rules / Plugin README 中的日志路径。

验证情况：

- `readlink ~/.codex/memories` 指向新路径。
- 项目与 `相关：AI` 下已无 `yjcXcode/平常记录` 或桌面旧绝对路径残留（`rg`）。

## 2026-07-13

### HuaYuann：明确桌面 AI 日志为本机个人工作流

标签：`#HuaYuann` `#codex` `#cursor` `#rules` `#agents-md` `#workflow`

变更对象：

- `/Users/lin/Documents/HuaYuann/AGENTS.md`
- `/Users/lin/Documents/HuaYuann/.cursor/rules/ability-doc-sync.mdc`
- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/skills/skills.md`
- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`

变更内容：

- 保留“能力 / SDK 改动必须同步检查文档”的项目规则。
- 明确桌面 AI 日志目录是当前用户本机的个人工作流目录，仅在目录存在且可写时同步。
- 同事机器没有该目录或不在当前用户本机环境时，可以跳过桌面同步，不阻塞代码、文档或验证任务。

验证情况：

- 已更新 Codex 项目规则、Cursor 规则、桌面索引与长期计划接力规则。

## 2026-07-07

### HuaYuann：iOS 低版本 / 小组件闪退问题沉淀

标签：`#HuaYuann` `#iOS` `#Widget` `#dyld` `#文档`

变更对象：

- `/Users/lin/Desktop/花园-iOS低版本小组件与闪退问题说明.md`（新建）

变更内容：

- 整理 iOS 15 启动 dyld 闪退（WidgetCommon 动态 framework + JSONDecoder 符号缺失）根因与静态链接修复。
- 记录主 App iOS 12、桌面小组件 iOS 16.1 版本策略及 iOS 12/13/15 用户影响。
- 记录 pod install / Archive（WidgetCommon 双份产物）踩坑与最终 Podfile + podspec 配置。

变更原因：

- 昨晚排查结论需沉淀到桌面文档，便于团队查阅与后续 Cocos 重导工程回归。

影响范围：

- 文档类，不改代码。

验证情况：

- 文档已写入桌面；仓库内 `docs/ios-widget-ios15-crash.md` 仍为工程侧详细说明。

## 2026-07-01

### HuaYuann + 桌面模板：新增 karpathy-guidelines

标签：`#codex` `#cursor` `#skills` `#rules` `#agents-md` `#workflow`

变更对象：

- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/karpathy-guidelines/SKILL.md`
- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/rules/cursor/karpathy-guidelines.mdc`
- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/rules/common/karpathy-guidelines.md`
- `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/README.md`
- `/Users/lin/Documents/HuaYuann/.codex/skills/karpathy-guidelines/SKILL.md`
- `/Users/lin/Documents/HuaYuann/AGENTS.md`
- `~/.cursor/skills/karpathy-guidelines/SKILL.md`

变更内容：

- 引入社区整理的 Karpathy 编码行为准则（四条原则：先想清楚、简单优先、手术式改动、目标驱动）。
- 桌面模板库补齐 skill、Cursor rule 素材、AGENTS.md 摘要及 Skill vs Rule 使用说明。
- HuaYuann Codex 安装 `.codex/skills/karpathy-guidelines/`，并在 `AGENTS.md` 中增加编码时遵循引用。
- 全局 Cursor 个人 skill 目录同步安装。

变更原因：

- 减少 LLM 过度改动、过度抽象、静默假设等常见编码失误。
- 与 Superpowers 工作流 skill 互补，不替代 TDD / debugging / verification 等流程 skill。

影响范围：

- Codex 在 HuaYuann 编码任务中会参考 karpathy-guidelines；完整条文按需从 skill 加载。
- Cursor 侧可通过 `~/.cursor/skills/karpathy-guidelines` 按需触发；项目级常驻需另行复制 `.mdc` rule。
- 与现有 Superpowers、product-requirements 无命名或职责冲突。

验证情况：

- 已确认 HuaYuann `.codex/skills/karpathy-guidelines/SKILL.md` 存在。
- 已确认 `AGENTS.md` 新增 karpathy-guidelines 引用行。
- 未执行构建或测试（仅 AI 工作流配置变更）。

## 2026-06-08

### HuaYuann：补充 Codex 项目规则并清理 Cursor Skills

标签：`#codex` `#cursor` `#skills` `#rules` `#agents-md` `#workflow`

变更对象：

- `/Users/lin/Documents/HuaYuann/AGENTS.md`
- `/Users/lin/Documents/HuaYuann/.cursor/skills/`
- `/Users/lin/Documents/HuaYuann/.codex/skills/`

变更内容：

- 新增项目级 `AGENTS.md`，作为 Codex 在 `HuaYuann` 仓库中的默认行为规则。
- `AGENTS.md` 使用中文通用规则，不绑定当前打开的样板文件或某个具体业务模块。
- 明确 Codex 侧 skills 以 `.codex/skills` 为主。
- 清理 `.cursor/skills`，仅保留 `product-requirements`。
- 在 `AGENTS.md` 中新增“Skills / Rules 日志”规则：以后新增或修改 AI 工作流规则时，同步记录到 `/Users/lin/Desktop/记录/平常记录/modo/相关：AI`。

变更原因：

- 避免 Codex 和 Cursor skills 双份混用导致来源不清。
- 让 Codex 在当前项目中有稳定的项目级规则。
- 将 AI 工作流规则变更沉淀到独立日志目录，方便后续复盘和迁移。

影响范围：

- Codex 后续在 `HuaYuann` 仓库内会优先参考 `AGENTS.md`。
- Cursor 侧项目 skills 仅保留 `product-requirements`，其他 Superpowers / OpenSpec 相关 skill 不再保留在 `.cursor/skills`。
- `.codex/skills` 未清理，仍作为 Codex 当前技能来源。

验证情况：

- 已检查 `.cursor/skills`，确认只剩 `product-requirements/SKILL.md` 和 `product-requirements-template.md`。
- 已检查 `AGENTS.md`，确认没有残留 iOS、Widget、Podfile、Xcode、Swift、HYWidget 等样板文件相关假设。
- 未执行构建或测试，因为本次仅修改 AI 工作流规则与日志。

### ControlWidgetCore：降低控制中心新增按钮接入成本

标签：`#sdk` `#ios` `#control-widget` `#skills` `#workflow`

变更对象：

- `/Users/lin/Desktop/gameProject/HYWidget/ControlWidgetCore/ControlWidgetCore/ControlWidgetCore.swift`
- `/Users/lin/Documents/HuaYuann/build/ios/proj/HYControlWidget/HYControlWidgetOpenApp.swift`

变更内容：

- SDK 新增 `ControlWidgetCoreHostOpenAppControl<Intent: AppIntent>`，由 SDK 统一生成 `StaticControlConfiguration`、按钮标题、图标和展示名，但按钮 action 仍绑定宿主双 Target 的 `AppIntent`。
- 花园样例将两个按钮各自独立的 `AppIntent` 合并为一个通用 `HYControlWidgetOpenAppIntent`。
- `jump` / `jumpWrite` 的按钮参数集中到 `HYGardenControlDefinitions`。
- `HYControlWidgetMainAppWire` 只需要强引用一个通用宿主 Intent。

变更原因：

- 控制中心打开主 App 的场景仍需要宿主双 Target `AppIntent`，不能完全放进 Pod。
- 新增按钮时不应继续复制一整套 `Intent + Control + wire`。
- 目标是把新增按钮流程压缩为：新增一条 definition，复制一个很薄的 Control 包装，并在 Bundle 中注册。

影响范围：

- 现有 `jump` 和 `jumpWrite` 行为保持：仍写 App Group，并通过业务 URL 打开主 App。
- SDK 增加了宿主 Intent 版 Control 模板，未删除旧的 `ControlWidgetCoreOpenAppControl`。
- 真机稳定性策略保持不变：控制中心按钮不直接绑定纯 SDK Intent。

验证情况：

- 已做静态搜索，确认旧的 `HYControlWidgetOpenJumpIntent` / `HYControlWidgetOpenJumpWriteIntent` 引用已移除。
- 已执行 `xcodebuild -list -project build/ios/proj/HuaYuann.xcodeproj`，工程可被读取，并列出 `HYControlWidgetExtension` target。
- 尝试执行 `xcodebuild build -target HYControlWidgetExtension`，但当前沙箱环境在写 Xcode DerivedData / Clang module session 时失败，未到 Swift 类型检查阶段；因此没有声明构建通过。

## 2026-07-03 - modo-native-ability-ios 能力 SDK 文档与版本同步规则

- 项目：modo-native-ability-ios / HuaYuann 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/.cursor/rules/nativebility-agent.mdc`
  - `/Users/lin/Documents/modo-native-ability-ios/AGENTS.md`
- 变更原因：能力 SDK 代码变更容易漏同步文档与 `self.version`，需要 Cursor 与 Codex 两侧规则保持一致。
- 规则内容：新增或修改 `NativeBility/NativeBility/nt+ability/Plugin/**` 下能力 SDK 代码时，必须同步对应文档；涉及新增能力、对外行为、回调字段、错误码、兼容策略或第三方 SDK 接入行为时，相关 `self.version` 默认 patch +1。
- 影响范围：后续 NativeBility Plugin / PluginAdapter 开发、错误码调整、SDK 接入行为变更、能力文档维护。
- 验证情况：已通过 `git diff --check` 检查规则文件格式；未运行业务构建，因为本次仅修改 AI 工作流规则。
- 标签：#codex #cursor #skills #rules #agents-md #workflow

## 2026-07-10 - HuaYuann 能力文档同步规则固化

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/HuaYuann/AGENTS.md`
  - `/Users/lin/Documents/HuaYuann/.cursor/rules/ability-doc-sync.mdc`
- 变更原因：能力 SDK、插件 API、传输协议、mock 数据、WidgetCommon / WidgetUI、控制组件等对外能力行为变更后，容易漏同步文档，需要 Codex 与 Cursor 两侧都强制记住“能力改动必须检查并更新文档”。
- 规则内容：
  - 修改、新增或删除能力相关代码时，必须同步检查能力仓库 `docs/`、README、接入说明、测试用例、排查文档和接口定义。
  - 必须清理旧字段、旧类型、旧 API 名称、旧调用示例和旧返回结构。
  - 若确认无需文档更新，最终回复必须明确说明“已检查文档，无需更新”及原因。
  - 验证时使用 `rg` 扫描旧协议/旧字段残留。
- 路径修正：AI 工作流规则日志目录以 `/Users/lin/Desktop/记录/平常记录/modo/相关：AI` 为准；同步修正 `AGENTS.md` 中旧路径。
- 影响范围：后续 NativeBility 能力 SDK、Widget / Control 组件、插件 API、传输协议、mock 数据和接入文档维护。
- 验证情况：已读取并检查 `AGENTS.md` 与 `.cursor/rules/ability-doc-sync.mdc`；已用 `rg` 确认新增规则关键字可检索。未运行业务构建，因为本次仅修改 AI 工作流规则与日志。
- 标签：#codex #cursor #skills #rules #agents-md #workflow

## 2026-07-10 - 能力 SDK 插件标准化长期计划

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：能力 SDK 插件标准化 / 流程化是一项长期工程，需要一份可接力文档记录目标、步骤、进度、下一步和恢复标准，避免当前账号、会话或额度中断后丢失上下文。
- 文档内容：
  - 定义插件“可删除后高度还原”的最低信息标准。
  - 定义新增插件、删除插件的标准流程。
  - 将工作拆成局部试点、插件清单化、全局能力检测三个阶段。
  - 记录当前公会竞赛小组件改造进度和下一步 Widget 插件能力卡片任务。
- 影响范围：后续 NativeBility 能力 SDK 插件新增、修改、删除、文档补齐、mock 检查和全局一致性检测。
- 验证情况：已通过 `sed` / `rg` 检查长期计划文档关键章节与日志索引可读取；未运行业务构建，因为本次仅新增 AI 接力文档和日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility

## 2026-07-10 - 能力 SDK 复杂度分级与分支矩阵

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/小组件/05-基础概念与APP连调.md`
- 变更原因：不同能力的分支复杂度差异很大，灵动岛、小组件等少分支能力可以轻量记录，但登录等多分支能力可能包含十多个业务分支，必须显式要求分支矩阵，避免文档只覆盖主流程。
- 文档内容：
  - 长期计划新增 L1 / L2 / L3 能力复杂度分级。
  - 能力卡片模板新增“复杂度与分支矩阵”。
  - 小组件联调文档补充说明：小组件是少分支样例，登录类多分支能力应按分支拆入参、回调、错误码、mock 和验证。
- 影响范围：后续 NativeBility 能力 SDK 插件新增、删除、恢复、文档补齐和全局能力检测。
- 验证情况：已通过 `sed` / `rg` 检查新增章节和关键字可读取；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs

## 2026-07-10 - 能力 SDK 插件文档主线校正

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/common-plugin-architecture.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/小组件/05-基础概念与APP连调.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：插件标准化、复杂度分级、分支矩阵的主线应落在 `docs/nt+ability/Plugin`，不是 `docs/小组件` 或 `docs/控制组件` 专项接入文档。
- 变更内容：撤回小组件文档中的通用复杂度提示，将 L1 / L2 / L3 和多分支矩阵要求补到插件 README 与公共架构文档；桌面长期计划同步当前断点和下一步。
- 验证情况：已通过 `rg` 检查关键字与路径归位；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs

## 2026-07-10 - Widget 低分支插件文档补齐起步

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Widget/widget-prd.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Widget/widget-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Widget/widget-test-cases.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：确认插件文档目录应与代码目录 `NativeBility/NativeBility/nt+ability/Plugin/<PluginName>` 一一对应，并从低分支插件开始补齐文档。
- 变更内容：补充代码/文档目录映射规则；将 `Widget` 文档从旧 demo 平铺字段口径更新到当前 API、WidgetCommon、query、公会竞赛、mock、测试与恢复要点口径。
- 验证情况：已通过 `rg` 检查 `Widget` 文档中关键 API、`competition`、`WidgetCommon`、目录映射和桌面断点可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #widget

## 2026-07-10 - 专项文档归档到对应插件 references

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Widget/references/小组件/`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Control/references/控制组件/`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Activity/references/live-activity/`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/小组件/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/控制组件/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/integrations/live-activity/README.md`
- 变更原因：`docs/控制组件`、`docs/小组件`、`docs/integrations/live-activity` 散落在插件文档体系外，不符合“文档目录对应代码插件目录”的主线结构。
- 变更内容：将三处历史专项资料迁移到对应插件 `references/` 下，作为参考、日志和学习过程保留；旧目录保留 README 跳转入口；插件 README 增加 references 归档规则和 `Control` 入口。
- 断点说明：当前 `Widget` 标准文档可恢复主链路，但未达到完全等价重建，需要继续补字段细表、存储细节、错误码全集和 mock 完整 JSON。
- 验证情况：已通过 `find` / `rg` 检查迁移后文件位置、旧目录跳转 README、插件 README 和桌面断点可检索；未运行业务构建，因为本次仅调整文档结构与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #widget #control #activity

## 2026-07-10 - 能力 SDK 文档长期自动推进协议

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
- 变更原因：用户要求长期规划进程，不要每做一步都询问；需要把后续推进方式固化为默认批处理流程。
- 变更内容：新增“自动推进协议”，明确无需逐步询问的事项、必须暂停确认的事项、批次粒度、推荐批次顺序和每批执行模板；插件 README 增加长期计划入口与默认批处理说明。
- 影响范围：后续 NativeBility 能力 SDK 插件文档补齐、references 归档、恢复等级评估和断点同步。
- 验证情况：已通过 `rg` 检查“自动推进协议”“批次粒度”“必须暂停确认”等关键字可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs

## 2026-07-10 - Control 低分支插件标准文档首版

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Control/control-prd.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Control/control-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Control/control-test-cases.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：按长期自动推进协议，从低分支插件开始补齐标准三件套；`Control` 插件已有代码目录和 references，但缺少标准 PRD、实现、测试文档。
- 变更内容：补齐 `control/default/getInfo`、`delegate`、App Group `ControlWidget` 协议、iOS 18 启用条件、`ControlWidgetCoreHostWire.h` 编译期依赖、恢复步骤和测试用例。
- 验证情况：已通过 `rg` 检查 `control-prd`、`getInfo`、`delegate`、`ControlWidget`、`iOS 18`、`ControlWidgetCoreHostWire`、长期计划进度和插件 README 索引可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #control

## 2026-07-10 - 已有文档代码一致性检查规则

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
- 变更原因：已有插件文档可能已经基本可用，后续完善时应先检查是否与当前代码一致，避免浪费时间重写已有沉淀。
- 变更内容：自动推进协议增加“已有文档先做代码一致性检查”；若文档与代码相符，只补缺口和恢复信息，不重写整份文档。
- 验证情况：已通过 `rg` 检查“代码一致性检查”“不重写整份文档”等关键字可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs

## 2026-07-10 - 插件清单与第一批 L1 一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Activity/activity-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Network/network-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Event/event-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/BiReport/bi-report-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Permission/permission-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：用户要求先扫全量插件，区分单分支、多分支，并持续推进单分支批次。
- 变更内容：新增插件清单与 L1/L2/L3 分类；对第一批 `Activity`、`Network`、`Event`、`BiReport`、`Permission` 做已有文档与当前代码一致性检查，只补结论、恢复等级和缺口，不重写整份文档。
- 验证情况：已通过 `find` / `rg` 检查 `plugin-inventory.md`、第一批插件一致性小节、长期计划进度和 README 索引可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory

## 2026-07-10 - 第二批 L1 插件一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Log/log-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/UMP/ump-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/TRTC/trtc-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/VoiceTranslation/voice-translation-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/RealtimeVoiceTrans/realtime-voice-trans-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：继续按长期自动推进协议处理低分支插件，先检查已有文档是否与当前代码一致，节省重写成本。
- 变更内容：对 `Log`、`UMP`、`TRTC`、`VoiceTranslation`、`RealtimeVoiceTrans` 补代码一致性检查、恢复等级、模块名核对和后续缺口；长期计划推进到下一批 L1 插件。
- 验证情况：已通过 `rg` 检查第二批插件一致性小节、插件清单、长期计划进度可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory

## 2026-07-10 - 第三批 L1 插件一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/RiskPerception/risk-perception-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/MusicEngine/music-engine-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Map/map-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Media/media-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Player/player-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：继续按长期自动推进协议处理低分支插件，批量完成已有文档与当前代码的一致性检查。
- 变更内容：对 `RiskPerception`、`MusicEngine`、`Map`、`Media`、`Player` 补代码一致性检查、恢复等级、模块名核对和后续缺口；长期计划推进到剩余 L1 插件。
- 验证情况：已通过 `rg` 检查第三批插件一致性小节、插件清单、长期计划进度可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory

## 2026-07-10 - 第四批 L1 与异常目录一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/FileFuc/file-fuc-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/FaceRecognition/face-recognition-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Max/max-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/file/file-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/AccountVerification/account-verification-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：用户要求按插件数量显示进度百分比，并继续完成剩余低分支插件。
- 变更内容：对 `FileFuc`、`FaceRecognition`、`Max`、历史 `file`、`AccountVerification` 补代码一致性检查、恢复等级和结构风险；修正 `Max` adapter 版本为 `1.0.6`；在插件清单和长期计划中写入 66.7%（22 / 33）整体进度。
- 验证情况：已通过 `rg` 检查第四批插件一致性小节、整体进度、长期计划进度可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory #progress

## 2026-07-10 - 第五批 L2 主文档一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Push/push-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Storage/storage-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Pay/pay-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Reward_video/reward-video-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/DeepLink/deep-link-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：进入 L2 中分支插件，先对已有主文档做代码一致性检查，并按用户要求持续更新插件数量进度。
- 变更内容：对 `Push`、`Storage`、`Pay`、`Reward_video`、`DeepLink` 补主文档一致性检查、恢复等级和分支缺口；修正 `Pay/iosPay` 版本为 `1.0.5`、`Reward_video/topon` 版本为 `1.0.2`；整体进度更新为 81.8%（27 / 33），剩余 6 个插件。
- 验证情况：已通过 `rg` 检查第五批插件一致性小节、整体进度、长期计划进度可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory #progress

## 2026-07-10 - 第六批 L2 主文档一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Basic/basic-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/entry/entry-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/game/game-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：收尾 L2 中分支插件，并继续按插件数量更新整体进度。
- 变更内容：对 `Basic`、`entry`、`game` 补主文档一致性检查、恢复等级和后续缺口；修正 `Basic/default` 版本为 `1.0.19`；整体进度更新为 90.9%（30 / 33），剩余 3 个 L3 插件。
- 验证情况：已通过 `rg` 检查第六批插件一致性小节、整体进度、长期计划进度可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory #progress

## 2026-07-10 - 第七批 L3 主文档一致性检查

- 项目：HuaYuann / modo-native-ability-ios 关联接入
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/plugin-inventory.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/README.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Login/login-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Share/share-implementation.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/docs/nt+ability/Plugin/Report/report-implementation.md`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/通用/能力SDK插件标准化流程化长期计划.md`
- 变更原因：收尾 L3 多分支插件的主文档级代码一致性检查，并完成按插件数量统计的进度闭环。
- 变更内容：对 `Login`、`Share`、`Report` 补主文档一致性检查、恢复等级和 provider 深查缺口；`Report` 主矩阵补入 `appsflyer`；`Login` 清单同步 `taptap`；整体进度更新为 100%（33 / 33），剩余插件 0 个。
- 验证情况：已通过 `rg` 检查第七批插件一致性小节、整体进度、长期计划进度可检索；未运行业务构建，因为本次仅修改文档与日志。
- 标签：#codex #cursor #skills #rules #workflow #sdk #nativebility #docs #inventory #progress

## 2026-07-14 - NativeBility 仓库内置能力文档同步规则

- 项目：modo-native-ability-ios / NativeBility
- 变更对象：
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/AGENTS.md`
  - `/Users/lin/Documents/modo-native-ability-ios/NativeBility/.cursor/rules/ability-doc-sync.mdc`
  - `/Users/lin/Desktop/记录/平常记录/modo/相关：AI/skills/skills-rules-change-log.md`
- 变更原因：原“能力 SDK 改代码必须同步文档”的规则只放在 HuaYuann 主工程；用户确认主规则应内置到 NativeBility 能力 SDK 仓库，避免从能力仓库直接工作时规则失效。
- 变更内容：新增 NativeBility `AGENTS.md`，写入能力文档同步、插件文档改造、个人桌面日志和验证要求；新增 Cursor rule `ability-doc-sync.mdc`，覆盖 NativeBility 插件代码、docs、WidgetCommon / WidgetUI 能力接入；明确桌面日志是个人机器路径，目录不存在时同事可跳过。
- 验证情况：已通过 `rg` 检查 NativeBility 规则可检索；已执行 `git diff --check` 检查规则与日志无空白错误；未运行业务构建，因为本次仅修改 AI 工作流规则和文档。
- 标签：#codex #cursor #skills #rules #agents-md #nativebility #docs #workflow
