# Typora Markdown 语法

---



----

## 零、Typora 官方指导

<http://support.typora.io/Markdown-Reference/>

---



## 一、标题和目录

## 1.1 标题

`# 1 级标题`

`## 2 级标题`

`### 3 级标题`

`#### 4 级标题`

`##### 5 级标题`

`###### 6 级标题`

## 1.2 目录

`[TOC]`

---

## 二、段落和换行

一个段落只是一个或者多个连续的文本行。

在 Markdown 源代码中，段落由多个空白行分隔。

在 Typora 中，你只需要按`回车键`来创建一个新的段落

按`Shift`+`Enter`创建一个换行符（Typora 特有)

行尾留下**两个空格**或者插入`<br>`

---

## 三、引用

Markdown 使用电子邮件风格 `>` 字符进行块引用。

> 这是一个包含两段的 blockquote。这是第一段。

> 这是第 二 段



---

## 四、列表与任务列表

## 4.1 列表

#### 4.1.1 无序列表

输入`* list item1`创建一个无序列表，`*`可以使用`+`或者`-`替代

* Monday
* Tuesday
* Wednesday

#### 4.1.2 有序列表

输入 `1. list item1`将创建一个有序列表

1. one
2. two
3. three
4. four

### 4.2 任务列表

> 任务列表是标有 `[]` 或者 `[x]` 的列表，例如：

- [ ] a task list item
- [ ] list syntax required

- [x] completed

---

## 五、强调

### 5.1 加粗

Markdown 将两个连续 `*` 和 `_`视为加粗的指标，

### 5.2 斜体

Markdown 将一个 `*`和 `_`视为斜体标志

### 5.3 删除线

GFM 添加了标准 Markdown 语法没有的 下划线语法。

`~~`

~~Mistaken text.~~

### 5.4 下划线

下划线由原始的 HTML 提供。

<u> Underline </u>

### 5.5 emoji 表情：

输入emoji语法：`:smile`

:smiley_cat:

---

## 六、代码块

Typora 只支持 GitHub Flavored Markdown 中的栅栏。

不支持原始代码块中的标记。

输入，==```==然后按下`enter`键

```python
print("Hello world!")
```

---

## 七、数学公式

可以使用**MathJax**渲染**Latex**数学表达式。

输入：`$$`然后按下`Enter`键，将触发一个接收 `Tex/LaTex`源码的输入范围。
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
$$

---

## 八、表格

输入`|第一个标题|第二个标题| 然后按下 `,`Enter`键，将创建一个有两列的表格。

| First Header | Second Header |
| :----------: | :-----------: |
|    数据1     |     数据2     |

> 通过冒号`:`在标题行中，可以定义文本对齐方式
>
> - 左边冒号，左对齐
> - 右边冒号，右对齐
> - 两边冒号，中间对齐

```
| Left-Aligned | Center-Aligned | Right-Aligned |
|:------------ | :-------------:| ------------: |
| col 1        | col 2          | col 3         |
```



---

## 九、图片与链接

### 9.1 图片

图片和链接，看起来一样，但是图片需要在链接前加上 `!`

图片语法：

```
![](/home/img.png)
![](/home/img.png "Optical title")
```

Typora 关于图片的文档：[http://support.typora.io//Images/](https://link.jianshu.com/?t=http://support.typora.io//Images/)

### 9.2 链接

### 9.2.1 链接

Markdown 支持两种风格的链接：

1. 内联
2. 引用

在这两种样式中，链接文本由`[方括号]`分隔。

要创建内联链接，请在链接文本的关闭括号后**立即**使用一组常规括号。

在括号内，将链接所在的网址与链接的可选标题一起放在引号中。

```
This is [an example](http://example.com/"Title") inline link.

[This link](http://example.net) has no title attribute.
```

### 9.2.2 内部链接

你可以将标题设置为一个连接，我们会创建一个书签，允许你点击标题后，跳转到文章中指定的部分，例如：

Ctrl + Click[This link](#块元素)会跳转到块元素标题的位置 ，要查看如何写入，请移动光标或单击该链接，按⌘键将元素展开为markdown源。

#### 9.2.3 参考链接

参考链接使用第二组方括号，您可以在其中放置您选择的标签，来识别链接：

```
This is [an example][id] reference-style link.

Then, anywhere in the document,you define your link label like this, on a line by itself:
[id]:http://example.com/ "Optical Title Here"
```

This is [an example][Google] reference-style link.

隐式链接名称快捷方式允许您省略链接的名称，在这种情况下，将链接文本本身用作名称。 只需使用一组空白方括号 - 例如将Google“Google”链接到google.com网站，您可以简单地写：

```
[Google][]
And then define the link:

[Google]: http://google.com/
```



#### 9.2.4 URL 地址

Typora 允许插入 URL 作为链接，用尖括号`<>`包起来

<i@typora.io> 

Typora 也会自动链接标准的 URLs, 例如：<www.google.com>



---

## 十、上下标、高亮

### 10.1 开启设置

> 为了使 **Typora** 支持**上下标**与**高亮**，需要在`文件->偏好设置->Markdown`中开启以上支持。

### 10.2 上标和下标

- 使用 `~`来包裹下标内容。例如：`H~2~O`,H~2~O,`X~long text~`,X~long\ text~/
- 使用`^`包裹上标内容。例如：`X^2^`，X^2^

### 10.3 高亮

- 使用`==`包裹希望高亮的内容。例如：`==hightlight==`==highlight==





[Google]: www.google.com	"google"