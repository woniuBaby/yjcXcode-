# Cursor Rules

这里放 Cursor 专用规则素材。

项目级 Cursor Rule 通常放在：

```text
项目根目录/.cursor/rules/*.mdc
```

## 已有规则

| 文件 | 说明 |
| --- | --- |
| `karpathy-guidelines.mdc` | Karpathy 编码行为准则，`alwaysApply: true`，与 `skills/karpathy-guidelines/SKILL.md` 内容同步 |

安装示例：

```bash
mkdir -p /目标项目/.cursor/rules
cp karpathy-guidelines.mdc /目标项目/.cursor/rules/
```

在 Cursor **Settings → Rules** 中可确认 `karpathy-guidelines` 是否生效。
