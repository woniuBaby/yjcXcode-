## Typora基本技巧

### 常用快捷键

- 加粗： `Ctrl + B`
- 撤销： `Ctrl + Z`
- 字体倾斜 ：`Ctrl+I`
- 下划线：`Ctrl+U`
- 多级标题： `Ctrl + 1~6`
- 有序列表：`Ctrl + Shift + [`
- 无序列表：`Ctrl + Shift + ]`
- 降级快捷键 ：`Tab`
- 升级快捷键：`Shift + Tab`
- 插入链接： `Ctrl + K`
- 插入公式： `Ctrl + Shift + M`
- 行内代码： `Ctrl + Shift + K`
- 插入图片： `Ctrl + Shift + I`
- 返回Typora顶部：`Ctrl+Home`
- 返回Typora底部 ：`Ctrl+End`
- 创建表格 ：`Ctrl+T`
- 选中某句话 ：`Ctrl+L`
- 选中某个单词 ：`Ctrl+D`
- 选中相同格式的文字 ：`Ctrl+E`
- 搜索: `Ctrl+F`
- 搜索并替换 ：`Ctrl+H`
- 删除线 ：`Alt+Shift+5`
- 引用 ：`Ctrl+Shift+Q`
- 生成目录：`[TOC]+Enter`

注：一些实体符号需要在实体符号之前加” \ ”才能够显示

---

### 表格

|      |      |
| ---- | ---- |
|      |      |

10:00 上午

---



### 序列

开头*/+/- 加空格+文字，可以创建无序序列，换行键换行，shift+tab跳出
开头1.加空格+后接文字，可以创建有序序列例：

- 第一个无序序列
- 第二个无序序列
- ······

1. 第一个有序序列

2. 第二个有序序列

3. ······

   as

shift+tab跳出

---

### 更改字体、大小、颜色
Markdown语法

```
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=red>我是红色</font>
<font color=#008000>我是绿色</font>
<font color=Blue>我是蓝色</font>
<font size=5>我是尺寸</font>
<font face="黑体" color=green size=5>我是黑体，绿色，尺寸为5</font>

<font color=red>, </font>
```

效果如下：

<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=red>我是红色</font>
<font color=#008000>我是绿色</font>
<font color=Blue>我是蓝色</font>
<font size=5>我是尺寸</font>
<font face="黑体" color=green size=5>我是黑体，绿色，尺寸为5</font>



---



### 区域元素

```text
YAML FONT Matters
```

在文章的最上方输入---，按换行键产生，然后在里面输入内容即可。

### 标题

开头#的个数表示，空格+文字。标题有1~6个级别，#表示开始，按换行键结束

```text
# 一级标题 快捷键为 Ctrl + 1
## 二级标题 快捷键为 Ctrl + 2
......
###### 六级标题 快捷键为 Ctrl + 6
```

---



### 字体

斜体以**或__括住

```text
*这是斜体字体1*_这是斜体字体2_
```

*这是斜体字体1*
*这是斜体字体2*

加粗
开头`**`，结尾`**`。
或者开头`__`,结尾`__`(两个短横线)。

```text
**这是加粗字体1** __这是加粗字体2__
```

这是加粗字体1
这是加粗字体2

删除线
开头`~~`，结尾`~~`。

```text
~~这是错误文字~~
```

这是错误文字

下划线使用HTML标签<u>下划线</u>

```text
<u>下划线</u>
```

下划线

高亮
`==内容==`，需要自己在偏好设置里面打开这项功能

```text
==高亮==
高亮
```

---

### 代码

行内输入代码块快捷键： `Ctrl + Shift + K`

1. 开头```+语言名，开启代码块，换行键换行，光标下移键跳出
   示例：

```text
print("hello,python!"")
```

1. 用两个`在正常段落中表示代码
   例如：

```text
Use the `printf()`function.
```

Use the `printf()`function.

---

### 表格

开头|+列名+|+列名+|+换行键，创建一个2*2表格，`Ctrl+Enter`可建立新行。

示例：

```text
|第一列|第二列|
```

`|+列名+|+列名+|+ 换行 `



***

### 分割线

输入 `***` 或者 `---`,按换行键换行，即可绘制一条水平线。

```text
***---
```

上下是水平线

---

### 引用

开头>表示，空格+文字，按换行键换行，双按换行跳出

```text
> 引注1
> ···
> 引注2
>还有一行，双按换行键跳出引注模式
```



> 实战效果
>
> 可以的

---

### 链接

链接
输入网址，单击链接，展开后可编辑
ctr+单击，打开链接
例如：[https://www.baidu.com](https://link.zhihu.com/?target=https%3A//www.baidu.com)

常用链接方法

```text
文字链接 [链接名称](http://链接网址)网址链接 <http://链接网址>
```

示例效果：百度

高级使用:

`[谷歌!][google]`

---

### URLs

用<>括住url，可手动设置url对于标准URLs，可自动识别

```text
<www.baidu.com>
```

<www.baidu.com>

---

