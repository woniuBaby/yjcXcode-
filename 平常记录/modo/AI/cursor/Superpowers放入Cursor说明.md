# Superpowers 放入 Cursor 说明

## Superpowers 可以放到 Cursor 中吗

可以。

`superpowers-zh` 本质是一组 AI 编程 skills / rules / workflow 资料，可以安装到 Cursor 支持的配置目录里，让 Cursor 在写代码、调试、审查、规划时参考这些工作方法。

## 放进去以后有什么用

它不是一个独立 App，也不是普通插件按钮。它更像给 Cursor 里的 AI 增加一组“做事方法”：

- 需求不清楚时先头脑风暴
- 大需求先写计划
- 实现时按步骤执行
- 复杂 bug 用系统化调试
- 重要变更做代码审查
- 完成前做验证
- 需要时使用 TDD

## 放到哪里

Cursor 常见有两类位置：

| 位置 | 作用 |
| --- | --- |
| `~/.cursor/skills/` | 个人 skills，所有项目都能用 |
| `项目/.cursor/skills/` | 项目 skills，只在当前项目里用 |

如果是你个人长期使用，建议放：

```bash
~/.cursor/skills/
```

如果是某个团队项目共同使用，建议放：

```bash
项目根目录/.cursor/skills/
```

## 推荐安装方式

进入具体项目目录后运行：

```bash
cd your-project
npx superpowers-zh
```

安装程序会根据你选择的 AI 工具，把对应内容放到工具识别的位置。

不要在用户主目录 `~` 里直接运行，避免装错位置。

## 手动放入的大致结构

如果手动整理，结构通常类似：

```text
.cursor/
└── skills/
    ├── brainstorming/
    │   └── SKILL.md
    ├── writing-plans/
    │   └── SKILL.md
    ├── executing-plans/
    │   └── SKILL.md
    ├── tdd/
    │   └── SKILL.md
    └── code-review/
        └── SKILL.md
```

每个 skill 目录里最重要的是 `SKILL.md`，AI 会读这个文件来知道什么时候使用这项能力。

## 和 Cursor Rule 的区别

| 类型 | 适合内容 |
| --- | --- |
| Cursor Rule | 短规则、项目约束、固定命令、禁止事项 |
| Cursor Skill | 较长的 SOP、某类任务的完整流程 |
| Superpowers | 一整套现成 Skills，直接增强 Cursor 的 AI 工作方式 |

例子：

- “不要擅自 commit”适合放 Rule。
- “如何做系统化调试”适合放 Skill。
- “一整套规划、调试、TDD、审查能力”适合用 Superpowers。

## 建议

如果只是先试用：

1. 找一个测试项目。
2. 在项目根目录运行 `npx superpowers-zh`。
3. 选择 Cursor。
4. 看它生成的 `.cursor/skills/` 或相关配置。
5. 用几次后，再决定是否放到个人 `~/.cursor/skills/`。

如果你希望所有项目都使用，再安装到个人 Cursor skills 目录。
