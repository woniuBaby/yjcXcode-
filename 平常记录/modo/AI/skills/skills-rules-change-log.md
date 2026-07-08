# Skills / Rules 变更日志

用于记录 AI 工作流相关配置的变更，包括 `AGENTS.md`、`.codex/skills/`、`.cursor/skills/`、OpenSpec 配置、项目级规则和通用使用约定。

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

- `/Users/lin/Desktop/AI-Skills-rules-Template/skills/karpathy-guidelines/SKILL.md`
- `/Users/lin/Desktop/AI-Skills-rules-Template/rules/cursor/karpathy-guidelines.mdc`
- `/Users/lin/Desktop/AI-Skills-rules-Template/rules/common/karpathy-guidelines.md`
- `/Users/lin/Desktop/AI-Skills-rules-Template/README.md`
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
- 在 `AGENTS.md` 中新增“Skills / Rules 日志”规则：以后新增或修改 AI 工作流规则时，同步记录到 `/Users/lin/Desktop/yjcXcode/平常记录/modo/AI`。

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
