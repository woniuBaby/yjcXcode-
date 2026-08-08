# Cursor：Rule / Skill 大白话说明

> 一句话：**都是「提前写给 AI 的说明书」**，区别只在于 **放哪、什么时候自动拿出来、写多长**。

---

## 一、先想成三叠纸

```
┌─────────────────────────────────────┐
│  第 1 叠：User Rules（你本人的习惯）   │  ← Cursor 设置里，所有项目都生效
├─────────────────────────────────────┤
│  第 2 叠：项目 Rules（这个仓库的规矩）  │  ← .cursor/rules/，只有打开这个项目时
├─────────────────────────────────────┤
│  第 3 叠：Skills（某类任务的完整手册）  │  ← 做某件事时才翻出来读
└─────────────────────────────────────┘
```

AI 聊天时会 **把相关的几叠一起参考**，不是互斥关系。

---

## 二、Rule 是什么？（规矩 / 提醒条）

**大白话：** 像贴在工位上的 **便签**。

- 短
- 常挂着，或改某类文件时自动出现
- 告诉 AI：别乱 commit、离线打包要跑哪个脚本、代码别写太啰嗦

### 个人 Rule vs 项目 Rule

| | 个人 | 项目 |
|---|------|------|
| **放哪** | Cursor 设置 → Rules | 仓库 `.cursor/rules/` |
| **谁用** | 你所有项目 | 克隆仓库的人都有 |
| **例子** | 「别擅自 git commit」 | 「离线 iOS 基座流程」 |

### uni-non-x-demo 项目里已有的 Rule

| 文件 | 作用 |
|------|------|
| `.cursor/rules/offline-ios-base.mdc` | 离线 iOS 自定义基座：prepare → Archive → install ipa |

**何时自动生效：** 编辑 `nativeplugins/**`、`scripts/*offline*`、`docs/*基座*` 等匹配文件时。

**完整文档：** `docs/ios/基座/离线iOS打包-完整协作说明.md`

---

## 三、Skill 是什么？（一整本操作手册）

**大白话：** 像 **SOP 手册**，只有干某件大事时才翻开。

- 可以长一点、分步骤
- 不会一直占着 AI 的上下文
- 适合：先写 PRD 再编码、拆 PR、Cursor SDK 接入

### 个人 Skill vs 项目 Skill

| | 个人 | 项目 |
|---|------|------|
| **放哪** | `~/.cursor/skills/xxx/` | 仓库 `.cursor/skills/xxx/` |
| **谁用** | 你所有项目 | 团队共享 |
| **例子** | product-requirements | （可按需新建，如 ios-offline-custom-base） |

### 目录结构（Skill）

```
skill-name/
├── SKILL.md          # 必需，主说明
├── reference.md      # 可选，详细文档
└── scripts/          # 可选，固定脚本
```

**不要**往 `~/.cursor/skills-cursor/` 里写——那是 Cursor 内置技能目录。

---

## 四、Rule 和 Skill 差在哪？（最重要）

| | **Rule** | **Skill** |
|---|----------|-----------|
| **比喻** | 便签 | 手册 |
| **长度** | 短 | 可长 |
| **什么时候用** | 常自动（改某类文件就带上） | 做某类任务时才读 |
| **离线 iOS 打包** | ✅ 已有（项目 Rule） | 可选，不是必须 |

**不用两个都建。** 离线打包用 Rule 就够；只有觉得「AI 老记不住、步骤特别多」时，再补 Skill。

---

## 五、「个人 / 项目」记一句就够

- **个人** = 放 `~/.cursor/`，**所有项目**通用（写作习惯、PRD 流程）
- **项目** = 放 git 仓库 `.cursor/`，**只有这个仓库**用（离线 iOS 基座）

---

## 六、我当前有哪些？（2026-06 整理）

### 项目 Rules（uni-non-x-demo）

| 文件 | 用途 |
|------|------|
| `offline-ios-base.mdc` | 离线基座协作流程 |

### 个人 Skills

| name | 路径 | 用途 |
|------|------|------|
| product-requirements | `~/.cursor/skills/product-requirements/` | 先写 PRD/设计/测试用例再编码 |

### 项目 Skills

**暂无**（`.cursor/skills/` 未创建）

### Cursor 内置 Skills（不用自己改）

在 `~/.cursor/skills-cursor/`，例如：create-skill、create-rule、babysit、split-to-prs、sdk、canvas 等。用到时 AI 会自动读。

### User Rules（Cursor 设置里，不在 git）

例如：只有明确要求才 commit、PR 用 gh、代码风格等。

---

## 七、从哪里看？

### Cursor 界面

**Settings → Rules**（部分版本也有 Skills 列表）

### 文件系统

```bash
# 项目 Rules
uni-non-x-demo/.cursor/rules/

# 个人 Skills
~/.cursor/skills/

# 内置 Skills（只读参考）
~/.cursor/skills-cursor/
```

---

## 八、怎么用？（三种说法）

1. **啥也不说**  
   改 `nativeplugins` 时，项目 Rule 可能自动提醒「要不要打基座」。

2. **直接下口令**  
   「打离线 SDK」「Archive 好了」→ AI 按 Rule / 文档跑脚本。

3. **点名某类大任务**  
   「按 product-requirements，先写 PRD」→ AI 去读对应 Skill。

**不用记路径**，说人话即可。

---

## 九、离线 HBuilderX / Archive 属于什么？

| 问题 | 答案 |
|------|------|
| 属于 Rule 还是 Skill？ | **项目 Rule**（不是 Skill） |
| 放哪？ | `.cursor/rules/offline-ios-base.mdc` |
| 还要建 Skill 吗？ | 可选；Rule 已覆盖口令流程 |

### 流程图

```
改代码
   │
   ├─ 只改 .vue / .js ──→ HBuilderX 运行自定义基座（不用 Archive）
   │
   └─ 改了 nativeplugins ──→ 说「打离线 SDK」
                              → prepare-offline-hello-archive.sh
                              → Xcode Archive 到桌面
                              → 说「Archive 好了」
                              → install-ios-debug-ipa.sh
                              → HBuilderX 运行 unpackage/debug/iOS_debug.ipa
```

### HBuilderX 控制台为什么可能没 log？

- `console.log` 是 **JS 日志**，在 **HBuilderX 控制台**，不在 Xcode。
- 离线 ipa 里可能嵌 **旧 www**；热更新失败时手机跑旧代码。
- 自检：首页 toast 是否出现 `www BUILD_TAG`（如 `20260603-app-share-only`）。
- Archive 前可先 `bash ./scripts/sync-www-to-hello.sh`，避免 ipa 内嵌旧 JS。

---

## 十、只记两句

1. **Rule = 短规矩**；uni-non-x-demo 里已有「离线基座」这一条。  
2. **Skill = 长手册**；个人有一个「先写文档再编码」，离线打包 **不必** 强行再建 Skill。

---

## 十一、新建 Skill 时怎么写 description（给 AI 发现用）

```yaml
---
name: ios-offline-custom-base
description: >-
  Runs uni-app iOS offline SDK custom base workflow: prepare-offline-hello-archive.sh,
  Xcode Archive, install-ios-debug-ipa.sh, HBuilderX custom base.
  Use when user says 打离线SDK, Archive好了, iOS_debug.ipa, or HBuilderX 自定义基座.
---
```

- `name`：小写+连字符，≤64 字符  
- `description`：第三人称，写清 **做什么 + 什么时候用**

---

## 十二、和 QQ 分享链接参数（补充）

用户从 QQ 点开分享卡片时，QQ 可能在 URL 上追加 `qq_aio_chat_type` 等参数——**是 QQ 客户端加的，不是分享插件加的**。

业务侧应对：

- 解析 URL 时 **忽略 QQ 参数**，只读自己的业务字段  
- 或用 **短链 / path 承载 id**，不要对「整段 URL」验签  
- 分享插件只负责把 `targetUrl` 原样交给 QQ SDK
