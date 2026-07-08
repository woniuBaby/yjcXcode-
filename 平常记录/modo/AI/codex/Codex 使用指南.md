# Codex 使用指南

cursor 使用codex,添加到右边
```
Preferences: Open User Settings
 "workbench.activityBar.orientation": "vertical",
```





---



这份文档记录日常使用 Codex 时最常用的快捷方式、提问方式和开发协作方式。目标是：少解释背景，让 Codex 更快定位文件、理解意图并完成修改。

## 1. `@` 和 `/` 是什么？

不同编辑器或 AI 工具对 `@`、`/` 的支持不完全一样。

- `@文件名`：很多工具支持用 `@` 引用文件、文件夹、符号或上下文。例如 `@Podfile`、`@HYWidgetBundle.swift`。
- `/命令`：很多工具支持用 `/` 触发内置命令、技能或模式。例如 `/review`、`/fix`、`/chinese-documentation`，具体可用命令取决于当前工具。
- 在 Codex 里，如果 `@` 拖拽或引用没有自动生效，最稳的方式是直接粘贴路径。

推荐写法：

```text
文件：build/ios/proj/Podfile
请帮我看 target 'HuaYuanLAExtension' 这段配置。
```

## 2. 快速添加文件路径

### 从编辑器复制相对路径

在 VS Code / Cursor 左侧文件树中右键文件或文件夹，选择：

```text
Copy Relative Path / 复制相对路径
```

然后发给 Codex：

```text
build/ios/proj/Podfile
```

这是最推荐的方式，因为路径短、准确、节省上下文。

### 从 Finder 复制完整路径

在 macOS Finder 中选中文件或文件夹，按：

```text
Option + Command + C
```

会复制完整路径，例如：

```text
/Users/lin/Documents/HuaYuann/build/ios/proj/Podfile
```

如果文件在当前项目内，可以改成相对路径发给 Codex：

```text
build/ios/proj/Podfile
```

### 不知道路径时

可以给关键词，让 Codex 搜索：

```text
帮我找到包含 target 'HuaYuanLAExtension' 的文件。
```

或：

```text
帮我找项目里所有 ActivityUI 的引用。
```

## 3. 贴代码片段时怎么更容易定位？

只贴代码片段也可以，但最好带上文件路径。

推荐：

```text
文件：build/ios/proj/Podfile

这段代码：
target 'HuaYuanLAExtension' do
  ...
end

需求：把 remote 模式的 pod 改成指定分支。
```

不推荐只贴：

```text
pod 'ActivityUI'
```

因为项目里可能有很多相同引用，Codex 需要额外搜索和确认。

## 4. 常用提问模板

### 解释代码

```text
解释一下 build/ios/proj/Podfile 里 HuaYuanLAExtension 这个 target 的作用。
```

### 修改代码

```text
修改 build/ios/proj/Podfile：
把 HuaYuanLAExtension 在 remote 模式下的 ActivityUI 改成使用 develop 分支。
```

### 找引用

```text
帮我找一下项目里 ActivityCoreDate 在哪些地方被引用。
```

### 排查报错

```text
报错日志在 build/ios/proj/filtered_crash.txt。
请帮我分析崩溃原因，并指出可能相关的代码位置。
```

### 做代码审查

```text
请 review 我当前修改，重点看是否有构建风险、逻辑问题和遗漏测试。
```

### 让 Codex 自己读文件

```text
请读取 build/ios/proj/Podfile 和 build/ios/proj/HYWidget/HYWidgetBundle.swift，
帮我判断 Widget 和 Pod 依赖之间有没有配置问题。
```

## 5. 开发时推荐的协作方式

### 小改动

直接说文件、目标、期望结果：

```text
文件：build/ios/proj/Podfile
目标：给 HuaYuanLAExtension 增加 ActivityKit 依赖。
```

### 中等改动

先让 Codex 看结构，再改：

```text
先帮我看一下 build/ios/proj 下面和 Widget 相关的文件，
然后告诉我应该改哪些地方，再开始修改。
```

### 大改动

先让 Codex 写计划：

```text
我要重构 Widget 的依赖接入方式。
请先给一个实现计划，不要马上改代码。
```

### Bug 排查

提供 3 类信息最有效：

- 报错日志或现象；
- 相关文件路径；
- 最近改过什么。

示例：

```text
现在 iOS 构建失败，日志在 build/ios/proj/filtered_crash.txt。
最近改过 build/ios/proj/Podfile。
请先定位原因，再给修复方案。
```

## 6. 修改代码时要不要贴完整文件？

通常不用。

优先级如下：

1. 给文件路径 + 需求；
2. 给文件路径 + 相关片段 + 需求；
3. 不知道路径时，给关键词让 Codex 搜索；
4. 只有在文件不在当前工作区，或者内容还没保存时，才需要贴完整代码。

## 7. 如何让 Codex 更少误解？

尽量补齐这 4 件事：

- 文件路径：例如 `build/ios/proj/Podfile`；
- 修改对象：例如 `target 'HuaYuanLAExtension'`；
- 修改目标：例如「remote 模式使用指定分支」；
- 验证方式：例如「跑 pod install」或「只检查语法，不执行安装」。

模板：

```text
文件：xxx
位置：xxx
目标：xxx
验证：xxx
```

## 8. 常见任务怎么说？

### 只看不改

```text
只分析，不要修改文件。
```

### 先问我再改

```text
先给方案，等我确认后再改。
```

### 直接改

```text
直接修改，完成后告诉我改了哪些文件，并运行必要验证。
```

### 不运行耗时命令

```text
不要运行 pod install，只检查 Podfile 写法。
```

### 可以运行验证

```text
修改后可以运行相关测试或构建检查。
```

## 9. 路径和权限注意事项

Codex 默认最方便处理当前工作区内的文件，例如：

```text
/Users/lin/Documents/HuaYuann
```

如果文件在工作区外，例如桌面、下载目录或另一个项目，可能需要授权才能写入。读取和修改当前工作区内的文件最顺畅。

如果要把外部文件交给 Codex 处理，推荐：

- 把文件复制到当前项目里；
- 或提供完整路径；
- 或明确说「这个路径可以读/写」。

## 10. 一个高效示例

```text
文件：build/ios/proj/Podfile
位置：target 'HuaYuanLAExtension'
目标：
1. local 模式继续使用本地 path；
2. remote 模式给 ActivityCoreDate 和 ActivityUI 都加上 :branch => 'develop'；
3. 不要改其他 target。

修改后请检查 Podfile 语法。
```

这样的描述通常可以直接进入修改，不需要来回确认。

