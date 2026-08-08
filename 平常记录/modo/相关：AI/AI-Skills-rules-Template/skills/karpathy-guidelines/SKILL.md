---
name: karpathy-guidelines
description: 减少 LLM 常见编码失误的行为准则。在编写、审查或重构代码时使用——避免过度设计、做最小改动、显式说明假设、定义可验证的成功标准。源自 Andrej Karpathy 对 LLM 编码问题的观察，由社区整理为可复用 skill。
license: MIT
---

# Karpathy Guidelines（Karpathy 行为准则）

> **来源说明：** 内容整理自 [Andrej Karpathy 2026-01 关于 LLM 编码失误的观察](https://x.com/karpathy/status/2015883857489522876)，由 [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) 等社区仓库封装为 Agent Skill / Cursor Rule。**并非 Karpathy 本人维护或背书**，而是对其观点的工程化落地。

## 何时使用

- 编写、审查、重构代码时，作为**常驻行为底线**参考
- 感觉 agent 改动范围过大、过度抽象、静默做假设、缺少验收标准时
- 与 Superpowers 工作流 skill（TDD、systematic-debugging、verification-before-completion 等）**叠加使用**，不替代它们

## 与本地 skill 的关系

| 类型 | 代表 | 关系 |
| --- | --- | --- |
| 行为准则 | **karpathy-guidelines**（本 skill） | 始终适用的编码原则 |
| 工作流 skill | Superpowers（brainstorming、TDD、debugging 等） | 按场景触发，提供具体步骤 |
| 项目规则 | `AGENTS.md`、`.cursor/rules/*.mdc` | 项目上下文与工具约束 |

**不冲突：** Karpathy 管「怎么写代码」，Superpowers 管「遇到某类任务走什么流程」。两者互补；若表述相近（如「最小改动」），以**更具体、更贴近当前任务**的指令为准。

**权衡：** 本准则偏向谨慎而非速度。对 trivial 小改，可酌情简化。

---

## 1. Think Before Coding（先想清楚再写）

**Don't assume. Don't hide confusion. Surface tradeoffs.**

编码前：
- 明确说出你的假设；不确定就问
- 存在多种理解时，列出来让用户选——不要静默替用户决定
- 有更简单方案就说出来；必要时提出异议
- 不清楚就停下来，指出困惑点，提问

## 2. Simplicity First（简单优先）

**Minimum code that solves the problem. Nothing speculative.**

- 不做需求之外的功能
- 不为只用一次的代码抽象
- 不加未被要求的「灵活性」或「可配置性」
- 不为不可能发生的场景写错误处理
- 写了 200 行能 50 行搞定，就重写

自问：「资深工程师会不会觉得过度复杂？」若是，就简化。

## 3. Surgical Changes（手术式改动）

**Touch only what you must. Clean up only your own mess.**

编辑已有代码时：
- 不「顺手」改相邻代码、注释或格式
- 不重构没坏的东西
- 匹配现有风格，即使你个人偏好不同
- 发现无关死代码，**提及**即可，不要擅自删除

你的改动产生孤儿代码时：
- 删除**因你的改动**而不再使用的 import / 变量 / 函数
- 不删除改动前就存在的死代码，除非用户要求

检验标准：diff 里每一行变更都应能直接追溯到用户请求。

## 4. Goal-Driven Execution（目标驱动执行）

**Define success criteria. Loop until verified.**

把任务转成可验证目标：
- 「加校验」→「先写无效输入的测试，再让它们通过」
- 「修 bug」→「先写能复现的测试，再让它通过」
- 「重构 X」→「重构前后测试都通过」

多步任务先列简短计划：

```
1. [步骤] → verify: [检查点]
2. [步骤] → verify: [检查点]
3. [步骤] → verify: [检查点]
```

成功标准越清晰，越能独立迭代；模糊标准（「弄好就行」）只会反复要用户澄清。

---

## 生效标志

若 diff 里无关改动变少、因过度设计导致的返工变少、澄清问题出现在实现之前而非犯错之后——说明本准则在起作用。

## 上游同步

修改四条原则时，请同步更新：

- `skills/karpathy-guidelines/SKILL.md`（本文件）
- `rules/cursor/karpathy-guidelines.mdc`
- `rules/common/karpathy-guidelines.md`
