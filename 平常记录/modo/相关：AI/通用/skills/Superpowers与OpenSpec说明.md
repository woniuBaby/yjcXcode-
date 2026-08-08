# Superpowers 与 OpenSpec 说明

整理时间：2026-06-08

## 一句话区别

| 项目 | 一句话 | 更像什么 | 主要解决什么问题 |
| --- | --- | --- | --- |
| superpowers-zh | 给 AI 编程工具安装一组“工作方法技能” | AI 的工作习惯包 / SOP 技能包 | 让 AI 不要一上来就乱写代码，而是会头脑风暴、写计划、TDD、调试、审查、验证 |
| OpenSpec-cn | 给项目引入“规格驱动”的变更流程 | 需求/设计/任务的轻量规范系统 | 让每个功能先形成 proposal、spec、design、tasks，再实现和归档 |

## superpowers-zh 是什么

仓库：<https://github.com/jnMetaCode/superpowers-zh>

`superpowers-zh` 是 AI 编程 skills 框架 `superpowers` 的中文增强版。它把一组常见 AI 编程工作方法做成可安装的 skills，适配 Claude Code、Cursor、Codex、Gemini CLI、Windsurf、Kiro 等多种 AI 编程工具。

它里面的技能更偏“AI 怎么工作”：

- 头脑风暴：先问清需求，不急着写代码
- 编写计划：把需求拆成可执行步骤
- 执行计划：按步骤做，每步验证
- 测试驱动开发：先写测试，再实现
- 系统化调试：定位、分析、假设、修复
- 代码审查：让 AI 自己或子 Agent 做 review
- 完成前验证：完成前必须有验证证据
- Git worktree：并行开发、隔离分支
- 中文特色能力：中文代码审查、中文 Git 工作流、中文技术文档、中文提交规范等

### 用在哪里

适合装到具体 AI 编程工具的技能目录里，用来提升 AI 协作质量。

常见安装位置：

| 工具 | 典型位置 |
| --- | --- |
| Codex | `.codex/skills/` |
| Cursor | `.cursor/skills/` |
| Claude Code | `.claude/skills/` |
| Gemini CLI | `.gemini/skills/` |
| OpenCode | `.opencode/skills/` |

推荐用法：

```bash
cd your-project
npx superpowers-zh
```

注意：不要在用户主目录 `~` 里直接跑，应该在具体项目目录里跑，避免把配置装到不该装的位置。

## OpenSpec-cn 是什么

仓库：<https://github.com/studyzy/OpenSpec-cn>

`OpenSpec-cn` 是 OpenSpec 的中文汉化版，npm 包名是 `@studyzy/openspec-cn`。它是一套轻量的 spec 框架，用来把 AI 编程从“聊天里随口说需求”变成“先有规格，再开发”。

它更偏“项目怎么管理需求和变更”：

- 每个功能变更有独立目录
- 先生成 `proposal.md`：为什么要做、改什么
- 再生成 `specs/`：需求和场景
- 必要时生成 `design.md`：技术方案
- 再生成 `tasks.md`：实现清单
- 实现完成后归档，并更新项目 specs

典型流程：

```text
/opsx:propose add-dark-mode
/opsx:apply
/opsx:archive
```

### 用在哪里

适合放到“项目需求变更比较多、多人协作、需要沉淀规格”的代码项目里。

它不是简单的 skills 集合，而是一套项目级规范流程。初始化后通常会在项目里生成 `openspec/` 相关目录和 AI 指令，让后续功能都围绕 spec 走。

安装与初始化：

```bash
npm install -g @studyzy/openspec-cn@latest
cd your-project
openspec-cn init
```

之后可以让 AI 使用：

```text
/opsx:propose <你想要构建的内容>
```

## 两者怎么搭配

| 场景 | 推荐 |
| --- | --- |
| 只是想让 Codex/Cursor 更会做事 | 用 superpowers-zh |
| 要做一个新功能，但需求还不清楚 | 先用 superpowers-zh 的头脑风暴/计划能力 |
| 项目长期维护，需要每个功能有规格记录 | 用 OpenSpec-cn |
| 大功能、多人协作、怕需求散在聊天里 | OpenSpec-cn 更合适 |
| 小修小补、临时 debug、代码 review | superpowers-zh 更轻 |
| 两个都用 | OpenSpec 管“需求制品”，superpowers 管“AI 工作方法” |

## 对 Codex 的建议

如果是在 Codex 里用：

1. `superpowers-zh` 可以作为 Codex 的 skills 来源，适合提升日常编码、调试、review、验证流程。
2. `OpenSpec-cn` 更适合在具体业务项目里初始化，而不是只放在这个笔记目录里。
3. 小项目或一次性需求：先不用 OpenSpec，直接让 Codex 规划后实现即可。
4. 中大型需求：先让 Codex 按 OpenSpec 生成 proposal/spec/design/tasks，再开始写代码。

## 当前资料链接

- superpowers-zh：<https://github.com/jnMetaCode/superpowers-zh>
- OpenSpec-cn：<https://github.com/studyzy/OpenSpec-cn>
- 公司内部 Superpowers 分享：<https://modoglobal.feishu.cn/wiki/RWxjw57nEiiM5wkqewac7Voenlf>

备注：飞书链接当前访问返回内部错误，可能需要登录或公司权限；本文主要依据两个 GitHub 仓库公开说明整理。
