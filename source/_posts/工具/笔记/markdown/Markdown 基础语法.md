---
title: markdown 基础语法
date: 2020-03-05 18:47:41
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%B7%A5%E5%85%B7/%E7%AC%94%E8%AE%B0/markdown/markdown%20%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%B7%A5%E5%85%B7/%E7%AC%94%E8%AE%B0/markdown/markdown%20%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/preview.jpg
tags: 
  - 工具
  - 笔记
  - markdown
categories: 
  - 工具
---



# markdown 基础语法

## 简介

### 核心定义



Markdown 是一款轻量级纯文本标记语言，由 John Gruber 与 Aaron Swartz 在 2004 年发布，核心设计目标是「让用户用易读易写的纯文本格式写作，无需分心复杂排版，同时可无缝转换为 HTML、PDF、Word 等格式」

它的核心特点是语法极简、全平台兼容、生态全覆盖，是目前技术圈、内容创作圈通用的标准写作格式，也是用 Obsidian 写笔记、GitHub 写项目文档、搭建个人博客的核心基础



------

### 核心优势



1. **纯文本原生，彻底告别格式混乱**

   所有内容都是纯文本，不会出现 Word/ Pages 跨设备、跨版本排版错乱的问题；文件体积极小，可永久存储，无格式兼容风险。

2. **完美适配程序员工作流**

   天然支持 Git 版本控制，你写的技术笔记、项目 README、接口文档，都可以用 Git 做变更追踪、团队协作，和 Java 项目的开发流程完全契合。

3. **学习成本极低，上手即用**

   核心常用语法不超过 10 个，几分钟就能掌握，写作时不用鼠标点选排版按钮，键盘全程操作，不打断写作思路。

4. **全生态全覆盖，一次编写多处复用**

   Obsidian、IDEA、VS Code、GitHub、Gitee、掘金、知乎、CSDN、Hexo/VitePress 等所有技术平台 / 工具 100% 原生支持，同一篇笔记 / 文档，不用修改就能直接发布到博客、同步到代码仓库、在笔记软件里渲染。

5. **技术写作专属能力拉满**

   原生支持代码高亮、表格、流程图、数学公式，写 Java 语法讲解、接口参数说明、架构设计、算法笔记时，比富文本工具效率高 10 倍。

6. **无限扩展，兼顾轻量与进阶**

   基础语法极简，进阶可无缝兼容 HTML、CSS、Mermaid 绘图、LaTeX 公式，既能写简单的日常笔记，也能写专业的技术白皮书、项目文档。



------

## 标题

通过`#`的数量区分标题层级，`#`后必须加空格，共支持 6 级标题，对应 HTML 的 H1-H6，是结构化笔记的核心基础

```markdown
# 一级标题（H1，单篇笔记建议仅使用1个，作为核心主题）
## 二级标题（H2，章节主标题）
### 三级标题（H3，章节子标题）
#### 四级标题（H4，细分模块）
##### 五级标题（H5，细分条目）
###### 六级标题（H6，备注/补充内容）
```



------

## 段落

要创建段落，使用空白行将一行或多行文本进行分隔。 不要用空格（spaces）或制表符（ tabs）缩进段落

```markdown
I really like using Markdown.
I think I'll use it to format all of my documents from now on.
```

首行缩进两个字符有个历史原因，由于以前打印纸张很贵，首行缩进两个字符可以清晰的分段。而现在通过空一行的方式，是一种更优雅的分段方式。而 Markdown 就采取的这种方式

几乎每个 Markdown 应用程序都支持两个或多个空格进行换行，称为结尾空格的方式，但这是有争议的，因为很难在编辑器中直接看到空格，并且很多人在每个句子后面都会有意或无意地添加两个空格

由于这个原因，可能要使用除结尾空格以外的其它方式来换行。幸运的是，几乎每个 Markdown 应用程序都支持另一种换行方式：HTML 的  `<br>`  标签

为了兼容性，请在行尾添加“结尾空格”或 HTML 的  `<br>` 标签来实现换行。最佳实践是，段落内不换行



------

## 引用

### 创建块引用

要创建块引用，请在段落前添加一个  `>` 符号

```markdown
> Dorothy followed her through many of the beautiful rooms in her castle.
```

**>** + 文本内容 （不需要空格)



------

### 多个段落的块引用

块引用可以包含多个段落。为段落之间的空白行添加一个  `>` 符号

```markdown
> Dorothy followed her through many of the beautiful rooms in her castle.
>
> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.
```



------

### 嵌套块引用

块引用可以嵌套。在要嵌套的段落前添加一个  `>>` 符号

```markdown
> Dorothy followed her through many of the beautiful rooms in her castle.
>
>> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.
```



------

## 列表

### 有序列表

要创建有序列表，请在每个列表项前添加数字并紧跟一个英文句点。数字不必按数学顺序排列，但是列表应当以数字 1 起始

```markdown
1. First item
2. Second item
3. Third item
4. Fourth item
1. First item
2. Second item
3. Third item
    1. Indented item
    2. Indented item
4. Fourth item
```



------

### 无序列表

要创建无序列表，请在每个列表项前面添加破折号 (-)、星号 (*) 或加号 (+) 。缩进一个或多个列表项可创建嵌套列表

```markdown
- First item
- Second item
- Third item
- Fourth item
* First item
* Second item
* Third item
* Fourth item
+ First item
+ Second item
+ Third item
+ Fourth item
- First item
- Second item
- Third item
    - Indented item
    - Indented item
- Fourth item
```



------

## 代码及代码块

如果要表示为代码的单词或短语中包含一个或多个反引号，则可以通过将单词或短语包裹在双反引号 (````) 中

```markdown
Use `code` in your Markdown file.
```

也可以使用代码块，用三个反引号起来的内容即为代码块

~~~markdown
```
    <html>
      <head>
      我是一段 HTML 示例
      </head>
      <body>
	      <div>我包含一些内容</div>
      </body>
    </html>
```
~~~



------

## 分割线和删除线

要创建分隔线，就在单独一行上使用三个或多个星号 (`***`)、破折号 (`---`) 或下划线 (`___`) ，并且不能包含其他内容

```markdown
***

---

_________________
```

以上三个分隔线的渲染效果看起来都一样

可以通过在单词中心放置一条水平线来删除单词。结果看起来~~像这样~~。此功能可以指示某些单词是一个错误，要从文档中删除。若要删除单词，就在单词前后使用两个波浪号 `~~`

```markdown
~~世界是平坦的。~~ 我们现在知道世界是圆的。
```

呈现的输出如下所示：

~~世界是平坦的。~~ 我们现在知道世界是圆的



------

## 链接

### 链接书写

链接文本放在中括号内，链接地址放在后面的括号中，链接 title 可选

超链接 Markdown 语法代码：`[超链接显示名](超链接地址 "超链接title")`

对应的 HTML 代码：`<a href="超链接地址" title="超链接title">超链接显示名</a>`

```markdown
这是一个链接 [Markdown语法](https://markdown.com.cn)。
```

渲染效果如下：

这是一个链接  [Markdown 语法](https://markdown.com.cn/)



------

### 链接的 title

链接 title 是当鼠标悬停在链接上时会出现的文字，这个 title 是可选的，它放在圆括号中链接地址后面，跟链接地址之间以空格分隔

```markdown
这是一个链接 [Markdown语法](https://markdown.com.cn "最好的markdown教程")。
```

渲染效果如下：

这是一个链接  [我给同一个链接增加了 标题 Markdown 语法](https://markdown.com.cn/)



------

### 网址和 Email 地址

使用尖括号可以很方便地把 URL 或者 email 地址变成可点击的链接

```markdown
<https://markdown.com.cn>
<fake@example.com>
```

渲染效果如下：

[https://markdown.com.cn](https://markdown.com.cn/)

[fake@example.com](mailto:fake@example.com)



------

### 带格式的链接

强调链接, 在链接语法前后增加星号。 要将链接表示为代码，请在方括号中添加反引号

```markdown
I love supporting the **[EFF](https://eff.org)**.
This is the *[Markdown Guide](https://www.markdownguide.org)*.
See the section on [`code`](#code).
```

渲染效果如下：

I love supporting the **[EFF](https://eff.org/)**.

This is the *[Markdown Guide](https://www.markdownguide.org/)*.

See the section on [`code`](https://markdown.com.cn/basic-syntax/links.html#code).



------

## 图片

### 添加图片

要添加图片，用感叹号 ( `!` ), 然后在方括号增加替代文本，图片链接放在圆括号里，括号里的链接后可以增加一个可选的图片标题文本

插入图片 Markdown 语法代码：`![图片alt](图片链接 "图片title")`

对应的 HTML 代码：`<img src="图片链接" alt="图片alt" title="图片title">`

```markdown
![这是图片](/assets/img/philly-magic-garden.jpg "Magic Gardens")
```



------

### 链接图片

给图片增加链接，请将图像的 Markdown 括在方括号中，然后将链接添加在圆括号中

```markdown
[![沙漠中的岩石图片](/assets/img/shiprock.jpg "Shiprock")](https://markdown.com.cn)
```



------

## 任务列表

任务列表可以创建带有复选框的项目列表。在支持任务列表的 Markdown 应用程序中，复选框将显示在内容旁边。要创建任务列表，请在任务列表项之前添加破折号 - 和方括号 [ ]，并在 [ ] 前面加上空格。要选择一个复选框，请在方括号 [x] 之间添加 x 

```markdown
- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media
```

展示效果如下：

- Write the press release
- Update the website
- Contact the media



------

## 表格

### 创建表格



要添加表，用三个或多个连字符（`---`）创建每列的标题，并使用管道（`|`）分隔每列。可以选择在表的任一端添加管道

```markdown
| 行/列 | 列名2 | 列明3 |
| ----- | ----- | ----- |
| 行名1 |       |       |
| 行名2 |       |       |
| 行名3 |       |       |
```

呈现的输出如下所示：

| 行/列 | 列名2 | 列明3 |
| --- | --- | --- |
| 行名1 |     |     |
| 行名2 |     |     |
| 行名3 |     |     |

单元格宽度可以变化，如下所示。呈现的输出将看起来相同

```markdown
| Syntax | Description |
| --- | ----------- |
| Header | Title |
| Paragraph | Text |
```



------

### 表格内容对齐



可以通过在标题行中的连字符的左侧，右侧或两侧添加冒号（`:`），将列中的文本对齐到左侧，右侧或中心

```markdown
| Syntax      | Description | Test Text     |
| :---        |    :----:   |          ---: |
| Header      | Title       | Here's this   |
| Paragraph   | Text        | And more      |
```

呈现的输出如下所示：

| Syntax    | Description |   Test Text |
| :-------- | :---------: | ----------: |
| Header    |    Title    | Here's this |
| Paragraph |    Text     |    And more |



------

## 脚注



脚注可以添加注释和参考，而不会使文档正文混乱。当创建脚注时，带有脚注的上标数字会出现在添加脚注参考的位置。读者可以单击链接以跳至页面底部的脚注内容

要创建脚注参考，请在方括号（`[^1]`）内添加插入符号和标识符。标识符可以是数字或单词，但不能包含空格或制表符。标识符仅将脚注参考与脚注本身相关联 - 在输出中，脚注按顺序编号

在括号内使用另一个插入符号和数字添加脚注，并用冒号和文本（`[^1]: My footnote.`）。不必在文档末尾添加脚注。可以将它们放在除列表，块引号和表之类的其他元素之外的任何位置

* **`[^脚注代号]`** ( 脚注代号会直接显示在渲染界面 )
* 脚注代号可以随便命名，不过推荐使用  **数字序号**
* **`[^脚注代号]`** + **:** + **空格** + 脚注内容

```markdown
Here's a simple footnote,[^1] and here's a longer one.[^bignote]

[^1]: This is the first footnote.

[^bignote]: Here's one with multiple paragraphs and code.

    Indent paragraphs to include them in the footnote.

    `{ my code }`

    Add as many paragraphs as you like.
```



------

## HTML



几乎所有支持 Markdown 的地方都支持 HTML，HTML 可以理解为 Markdown 的超集，可以做出任何炫酷的样式和排版。常用的包括在 Markdwon 中实现：

* 颜色：`<font color="red">红色文本</font>`
* 文本对齐： `<p style="text-align: right">右对齐文本</p>`
* 上下标：`10<sup>-6</sup>`，`H<sub>2</sub>O`
* 嵌入视频：`<iframe src="视频地址"/>`
* 第三方 api 嵌入：`<img src="https://contrib.rocks/image?repo=PKM-er/Pkmer-Docs"/>`
* 可合并的表格
* …

总而言之，几乎可以实现任意文本格式和排版。与之相对的，HTML 的可读性并不是很好，通常是用于网页开发，在笔记中如果不在意这种对人来说不那么优雅的语法，完全可以写 HTML 做笔记



------

## 实体字符



在 Markdown 中，字符  `<`、`>`、`"`、`'` 和反引号是特殊字符。它们是 Markdown 语法自身的一部分，如果想将这些字符包含进文本中， 一种方法是转义，即加斜杠 `\<`，另一种办法是使用实体字符即字符引用

必须使用字符引用 —— 表示字符的特殊编码，它们可以在那些情况下使用。每个字符引用以符号&开始，以分号 (;) 结束



**常用实体字符**

| 原义字符  | 等价字符引用    |
| ----- | --------- |
| <     | `&lt;`    |
| >     | `&gt;`    |
| ”     | `&quot;`  |
| ’     | `&apos;`  |
| &     | `&amp;`   |
| 版权符号© | `&copy;`  |
| 商标™   | `&trade;` |
| 空格    | `&nbsp;`  |
| 全角空格  | `&amp;`   |

