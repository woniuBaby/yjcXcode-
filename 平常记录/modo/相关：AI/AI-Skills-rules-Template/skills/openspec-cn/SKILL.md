---
name: openspec-cn
description: Use when the user wants specification-driven development, project requirements management, proposal/spec/design/tasks documents, OpenSpec-cn workflows, or a structured process before implementing medium or large features. Prefer this for multi-step product changes, team-facing requirements, and changes that should be documented before coding. Do not use for tiny fixes unless the user asks for OpenSpec.
---

# OpenSpec 中文规格流程

这个 skill 是 `OpenSpec-cn` 的轻量 Codex 版本，用来在合适的项目变更中采用“先规格，后实现”的流程。

## 什么时候使用

- 用户要求使用 OpenSpec / OpenSpec-cn。
- 新功能较大，需求需要沉淀。
- 变更影响多个模块、多人协作或后续需要追踪。
- 用户希望先产出 proposal、spec、design、tasks。

## 什么时候不要使用

- 简单错别字、样式微调、小 bug。
- 用户明确要求快速直接实现。
- 当前目录只是资料笔记，不是实际代码项目。

## 推荐流程

1. 明确 change id，例如 `add-dark-mode`。
2. 先写 `proposal.md`：为什么做、改什么、影响范围。
3. 必要时写 `design.md`：技术方案和取舍。
4. 写 `specs/`：需求、场景、验收标准。
5. 写 `tasks.md`：可执行任务清单。
6. 用户确认后再实现。
7. 完成后验证并归档。

## 与 Superpowers 的关系

- OpenSpec 管需求制品和项目变更流程。
- Superpowers 管 AI 的执行方法，例如计划、调试、测试、审查。
- 大功能可以先用 OpenSpec 产出规格，再用 Superpowers 的执行/验证方法落地。

## 参考资料

- GitHub: https://github.com/studyzy/OpenSpec-cn
- 本地说明: `skills/Superpowers与OpenSpec说明.md`
