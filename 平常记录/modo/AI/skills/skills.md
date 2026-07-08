# Skills / Rules / Spec 总览

这个目录放和 AI 能力配置、工作流、规则、规格驱动相关的资料。

## 当前文件

| 文件 | 内容 |
| --- | --- |
| `AI知识点.md` | Cursor Rule / Skill 的大白话说明 |
| `Superpowers与OpenSpec说明.md` | Superpowers 与 OpenSpec 的区别、用途、安装位置 |
| `skills.md` | 当前索引文件 |
| `skills-rules-change-log.md` | 记录 skills、rules、AGENTS.md、OpenSpec 等 AI 工作流配置的变更日志 |

## 怎么理解这几类东西

| 类型 | 作用范围 | 适合放什么 |
| --- | --- | --- |
| Rule | 常驻或按文件触发的短规则 | 项目规范、禁止事项、固定命令 |
| Skill | 做某类任务时读取的操作手册 | TDD、调试、写 PRD、代码审查、发布流程 |
| Superpowers | 一组现成的 AI 编程 Skills | 给 Cursor / Codex / Claude Code 等工具增强工作方法 |
| OpenSpec | 项目级规格流程 | proposal、spec、design、tasks、archive |

## 推荐分类

- `skills/`：放 Rule / Skill / Superpowers / OpenSpec 这类通用方法论。
- `cursor/`：放 Cursor 这个工具本身的配置、安装、使用说明。
- `codex/`：放 Codex 账号、配置、使用说明。
- `通用/`：放所有 AI 工具都适用的提示、习惯、注意事项。

## 日志约定

当项目中新增或修改以下内容时，建议同步记录到 `skills-rules-change-log.md`：

- `AGENTS.md`
- `.codex/skills/`
- `.cursor/skills/`
- OpenSpec 配置或项目规格流程
- 其他 AI 工作流规则、安装方式、触发约定

建议每条日志包含：日期、项目、变更对象、变更原因、影响范围、验证情况和标签。
