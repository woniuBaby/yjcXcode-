# AI Skills / Rules Template

这个目录是 AI 工作流模板库，不直接区分 Codex / Cursor。

需要给某个项目使用时，再把这里的 `skills` 和 `rules` 复制到目标工具对应的位置。

## 目录

```text
AI-Skills-rules-Template/
├── skills/
│   ├── Superpowers/           # superpowers-zh 官方完整版，20 个 skills
│   ├── karpathy-guidelines/   # Karpathy 行为准则（编码底线，常驻参考）
│   ├── openspec-cn/           # OpenSpec-cn 轻量 skill
│   ├── product-requirements/  # 先需求/产品/设计/测试文档，再编码
│   └── offline-ios-base/      # 离线 iOS 自定义基座流程
└── rules/
    ├── common/                # 通用规则，按需转成 AGENTS.md / Cursor Rule
    │   └── karpathy-guidelines.md
    ├── codex/                 # Codex 专用规则
    └── cursor/                # Cursor 专用规则
        └── karpathy-guidelines.mdc
```

## karpathy-guidelines 是什么

[andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) 是社区根据 **Andrej Karpathy 2026-01 关于 LLM 编码失误的推文观察**整理的一套**编码行为准则**（非 Karpathy 本人维护）。核心是四条原则：

| 原则 | 要点 |
| --- | --- |
| Think Before Coding | 显式假设、并列歧义、简单方案要提出 |
| Simplicity First | 最小代码、拒绝过度抽象与 speculative 功能 |
| Surgical Changes | 只改请求相关行、匹配现有风格 |
| Goal-Driven Execution | 可验证成功标准、步骤带检查点 |

### 与本地 skill 会冲突吗？

**不会。** 分工不同：

- **karpathy-guidelines**：编码时的**行为底线**（类似常驻 user rule）
- **Superpowers**：按场景触发的**工作流**（brainstorming、TDD、debugging、verification 等）
- **项目 rules**（`AGENTS.md`、`.cursor/rules`）：项目上下文与工具约束

若表述相近（如「最小改动」与 Superpowers 的 verification-before-completion），以更具体、更贴近当前任务的指令为准。二者互补叠加。

### Skill 和 Rule 都要装吗？怎么触发？

| 形式 | Codex | Cursor | 是否自动 |
| --- | --- | --- | --- |
| **Skill** | `.codex/skills/karpathy-guidelines/` | `~/.cursor/skills/` 或 `.cursor/skills/` | **按需**：agent 根据 `description` 判断是否读取；写代码类任务通常会触发，纯问答不一定 |
| **Rule** | `AGENTS.md` 中引用或节选 | `.cursor/rules/karpathy-guidelines.mdc`（`alwaysApply: true`） | **常驻**：每次对话都在上下文中 |

**建议：**

- **只装 Skill**：够用，但依赖 agent 自行判断，不是 100% 保证每次编码都加载全文。
- **Skill + Rule/AGENTS 引用**（推荐）：Skill 放完整条文，Rule/`AGENTS.md` 写一句「编码时遵循 karpathy-guidelines」——常驻提醒 + 按需展开细节。
- **只装 Rule**：也能常驻生效，但失去 skill 的按需完整加载；一般不如两者搭配。

**你不需要在需求里写「使用 karpathy-guidelines」**；装好后 agent 应自动遵循。若发现 agent 仍过度改动，补装 Rule 或加强 `AGENTS.md` 引用即可。

## 安装到 Codex 项目

```bash
mkdir -p /目标项目/.codex/skills
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/Superpowers/* /目标项目/.codex/skills/
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/karpathy-guidelines /目标项目/.codex/skills/karpathy-guidelines
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/openspec-cn /目标项目/.codex/skills/openspec-cn
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/product-requirements /目标项目/.codex/skills/product-requirements
```

可选：将 `rules/common/karpathy-guidelines.md` 节选合并进项目 `AGENTS.md`。

## 安装到 Cursor 项目

**Skill（按需触发 / 个人技能库）：**

```bash
mkdir -p /目标项目/.cursor/skills
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/Superpowers/* /目标项目/.cursor/skills/
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/karpathy-guidelines /目标项目/.cursor/skills/karpathy-guidelines
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/openspec-cn /目标项目/.cursor/skills/openspec-cn
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/product-requirements /目标项目/.cursor/skills/product-requirements
```

**Rule（项目级常驻，推荐与 skill 二选一或同时使用）：**

```bash
mkdir -p /目标项目/.cursor/rules
cp /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/rules/cursor/karpathy-guidelines.mdc /目标项目/.cursor/rules/
```

全局个人 skill（所有 Cursor 项目可用）：

```bash
mkdir -p ~/.cursor/skills
cp -R /Users/lin/Desktop/记录/平常记录/modo/相关：AI/AI-Skills-rules-Template/skills/karpathy-guidelines ~/.cursor/skills/karpathy-guidelines
```

## 注意

- `skills/Superpowers/` 下面是 20 个 skill 目录，不要再额外套一层 `Superpowers` 放进 `.codex/skills` 或 `.cursor/skills`。
- `skills/karpathy-guidelines/` 是单独一个 skill；`rules/cursor/karpathy-guidelines.mdc` 是对应的 Cursor 常驻 rule（`alwaysApply: true`）。修改原则时请同步两处。
- `skills/openspec-cn/` 是单独一个 skill，可以整体复制进去。
- `skills/product-requirements/` 是单独一个 skill，用于新增能力、较大需求、对外行为变更、兼容性调整等场景；它会要求先整理需求文档、产品文档、程序设计文档和测试用例文档，再进入编码。
- `rules/` 只是规则素材库，具体放哪里取决于工具：
  - Codex：通常写入 `AGENTS.md`（可参考 `rules/common/karpathy-guidelines.md`）。
  - Cursor：通常写入 `.cursor/rules/*.mdc`（如 `rules/cursor/karpathy-guidelines.mdc`）。
