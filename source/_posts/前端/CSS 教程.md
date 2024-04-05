---
title: CSS 教程
date: 2023-08-11 11:44:12
tags: 
  - 前端
  - CSS
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/preview.jpg
---



# CSS 教程

## 简介

### 简介



CSS 的全称为：层叠样式表 ( Cascading Style Sheets ) 

CSS 也是一种标记语言，用于给 HTML 结构设置样式，例如：文字大小、颜色、元素宽高等等

简单理解： CSS 可以美化 HTML , 让 HTML 更漂亮

核心思想： HTML 搭建结构， CSS 添加样式，实现了：结构与样式的分离



------

### 编写位置



**行内样式**

写在标签的 style 属性中，（又称：内联样式），示例：

```html
<h1 style="color:red;font-size:60px;">Hello World</h1>
```

style 属性的值不能随便写，写要符合 CSS 语法规范，是 名:值; 的形式

行内样式表，只能控制当前标签的样式，对其他标签无效

行内样式书写繁琐、样式不能复用、并且没有体现出结构与样式分离的思想，不推荐大量使用，只对当前元素添加简单样式时，才偶尔使用



**内部样式**

写在 html 页面内部，将所有的 CSS 代码提取出来，单独放在 style 标签中，示例：

```html
<style>
    h1 {
        color: red;
        font-size: 40px;
    }
</style>
```

style 标签理论上可以放在 HTML 文档的任何地方，但一般都放在 head 标签中

此种写法：样式可以复用、代码结构清晰，但并没有实现结构与样式完全分离，且多个 HTML 页面无法复用样式



**外部样式**

写在单独的 .css 文件中，随后在 HTML 文件中引入使用，示例：

```html
<link rel="stylesheet" href="./xxx.css">
```

link 标签要写在 head 标签中

href 说明引入的文档来自于哪里，rel ( relation ：关系）说明引入的文档与当前文档之间的关系

外部样式的优势：样式可以复用、结构清晰、可触发浏览器的缓存机制，提高访问速度 ，实现了结构与样式的完全分离



优先级规则：行内样式 > 内部样式 = 外部样式

内部样式、外部样式，这二者的优先级相同，且后面的会覆盖前面的



------

### 语法规范



CSS 语法规范由两部分构成：

* 选择器：找到要添加样式的元素
* 声明块：设置具体的样式（声明块是由一个或多个声明组成的），声明的格式为： 属性名: 属性值;

![image-20231204223902714](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231204223902714.png)

示例：

```css
/* 给h1元素添加样式 */ 
h1 {
    /* 设置文字颜色为红色 */ 
    color: red; 
    /* 设置文字大小为40px */ 
    font-size: 40px
}
```



------

## 选择器

### 基础选择器



**通配选择器**

作用：可以选中所有的 HTML 元素

语法：

```css
* { 
	属性名: 属性值; 
}

/* 示例 */ 
* {
	color: orange; 
    font-size: 40px;
}
```



**元素选择器**

作用：为页面中某种元素统一设置样式

语法：

```css
标签名 { 
	属性名: 属性值;
}

/* 选中所有h1元素 */ 
h1 {
	color: orange; 
	font-size: 40px;
}
```



**类选择器**

作用：根据元素的 class 值，来选中某些元素

语法：

```css
.类名 { 
	属性名: 属性值;
}

/* 选中所有class值为speak的元素 */
.speak { 
	color: red;
}
```

class 值，是我们自定义的，按照标准，不要使用纯数字、不要使用中文、尽量使用英文与数字的组合，若由多个单词组成，使用 - 做连接，例如： left-menu

一个元素的 class 属性，能写多个值，要用空格隔开



**ID 选择器**

作用：根据元素的 id 属性值，来精准的选中某个元素

语法：

```css
#id值 { 
	属性名: 属性值;
}

/* 选中id值为earthy的那个元素 */ 
#earthy { 
	color: red; 
	font-size: 60px;
}
```



------

### 复合选择器



**交集选择器**

作用：选中同时符合多个条件的元素

语法：选择器1选择器2选择器3...选择器n {}

示例：

```css
/* 选中：类名为beauty的p元素 */ 
p.beauty { 
	color: blue;
} 

/* 选中：类名包含rich和beauty的元素 */ 
.rich.beauty { 
	color: green;
}
```

用的最多的交集选择器是：元素选择器配合类名选择器，例如： p.beauty



**并集选择器**

作用：选中多个选择器对应的元素，又称分组选择器

语法：选择器1, 选择器2, 选择器3, ... 选择器n {}

示例：

```css
/* 选中id为peiqi，或类名为rich，或类名为beauty的元素 */ 
#peiqi, 
.rich, 
.beauty { 
	font-size: 40px; 
	background-color: skyblue; 
	width: 200px;
}
```

并集选择器，我们一般竖着写，任何形式的选择器，都可以作为并集选择器的一部分 



**后代选择器**

作用：选中指定元素中，符合要求的后代元素

语法：选择器1 选择器2 选择器3 ...... 选择器n {}

示例：

```css
/* 选中ul中的所有li */
ul li {
    color: red;
}

/* 选中ul中所有li中的a */
ul li a {
    color: orange;
}

/* 选中类名为subject元素中的所有li */
.subject li {
    color: blue;
}

/* 选中类名为subject元素中的所有类名为front-end的li */
.subject li.front-end {
    color: blue;
}
```



**子代选择器**

作用：选中指定元素中，符合要求的子元素（儿子元素）

语法：选择器1 > 选择器2 > 选择器3 > ...... 选择器n {}

```css
/* div中的子代a元素 */ 
div>a { 
	color: red;
} 

/* 类名为persons的元素中的子代a元素 */ 
.persons>a{ 
	color: red;
}
```



**相邻兄弟选择器**

作用：选中指定元素后，符合条件的相邻兄弟元素

语法： 选择器1+选择器2 {} 

示例：

```css
/* 选中div相邻的兄弟p元素 */ 
div+p { 
	color:red;
}
```



**通用兄弟选择器**

作用：选中指定元素后，符合条件的所有兄弟元素

语法： 选择器1~选择器2 {}

示例：

```css
/* 选中div后的所有的兄弟p元素 */ 
div~p { 
	color:red;
}
```



**属性选择器**

作用：选中属性值符合一定要求的元素

语法：

1. [属性名] 选中具有某个属性的元素
2. [属性名="值"] 选中包含某个属性，且属性值等于指定值的元素
3. [属性名^="值"] 选中包含某个属性，且属性值以指定的值开头的元素
4. [属性名$="值"] 选中包含某个属性，且属性值以指定的值结尾的元素
5. [属性名*=“值”] 选择包含某个属性，属性值包含指定值的元素

示例：

```css
/* 选中具有title属性的元素 */ 
div[title]{color:red;}

/* 选中title属性值为simon的元素 */ 
div[title="simon"]{color:red;}

/* 选中title属性值以a开头的元素 */
div[title^="a"]{color:red;}

/* 选中title属性值以u结尾的元素 */ 
div[title$="u"]{color:red;}

/* 选中title属性值包含g的元素 */ 
div[title*="g"]{color:red;}
```



**伪类选择器**

* 动态伪类选择器
  * :link 超链接未被访问的状态
  * :visited 超链接访问过的状态
  * :hover 鼠标悬停在元素上的状态
  * :active 元素激活的状态
  * :focus 获取焦点的元素
* 结构伪类选择器
  * :first-child 所有兄弟元素中的第一个
  * :last-child 所有兄弟元素中的最后一个
  * :nth-child(n) 所有兄弟元素中的第 n 个
  * :first-of-type 所有同类型兄弟元素中的第一个
  * :last-of-type 所有同类型兄弟元素中的最后一个
  * :nth-of-type(n) 所有同类型兄弟元素中的 第n个
* 否定伪类
  * :not(选择器) 排除满足括号中条件的元素
* UI 伪类
  * :checked 被选中的复选框或单选按钮
  * :enable 可用的表单元素（没有 disabled 属性）
  * :disabled 不可用的表单元素（有 disabled 属性）
* 目标伪类
  * :target 选中锚点指向的元素



------

### 选择器的优先级



通过不同的选择器，选中相同的元素 ，并且为相同的样式名设置不同的值时，就发生了样式的冲突。 到底应用哪个样式，此时就需要看优先级了

简单规则：行内样式 > ID选择器 > 类选择器 > 元素选择器 > 通配选择器



详细规则：计算方式：每个选择器，都可计算出一组权重，格式为： (a,b,c)

a : ID 选择器的个数

b : 类、伪类、属性选择器的个数。

c : 元素、伪元素选择器的个数

比较规则：按照从左到右的顺序，依次比较大小，当前位胜出后，后面的不再对比



行内样式权重大于所有选择器

!important 的权重，大于行内样式，大于所有选择器，权重最高



------

## 三大特性



**层叠性**

如果发生了样式冲突，那就会根据一定的规则（选择器优先级），进行样式的层叠（覆盖）



**继承性**

元素会自动拥有其父元素、或其祖先元素上所设置的某些样式，优先继承离得近的



**优先级**

!important > 行内样式 > ID选择器 > 类选择器 > 元素选择器 > * > 继承的样式



------

## 常用属性

### 像素



我们的电脑屏幕是，是由一个一个“小点”组成的，每个“小点”，就是一个像素（px）。像素点越小，呈现的内容就越清晰、越细腻



------

### 颜色的表示



**表达方式一：颜色名**

直接使用颜色对应的英文单词，编写比较简单

例如：

```
红色：red 
绿色：green
```

颜色名这种方式，表达的颜色比较单一，所以用的并不多



**表达方式二：rgb 或 rgba**

使用红、黄、蓝 这三种光的三原色进行组合。r 表示红色，g 表示 绿色，b 表示 蓝色，a 表示透明度

例如：

```css
/* 使用 0~255 之间的数字表示一种颜色 */ 
color: rgb(255, 0, 0);/* 红色 */ 
color: rgb(0, 255, 0);/* 绿色 */ 

/* 混合出任意一种颜色 */ 
color:rgb(138, 43, 226) /* 紫罗兰色 */ 
color:rgba(255, 0, 0, 0.5);/* 半透明的红色 */

/* 也可以使用百分比表示一种颜色（用的少） */ 
color: rgb(100%, 0%, 0%);/* 红色 */ 
color: rgba(100%, 0%, 0%,50%);/* 半透明的红色 */
```

对于 rbga 来说，前三位的 rgb 形式要保持一致，要么都是 0~255 的数字，要么都是 百分比



**表达方式三：HEX 或 HEXA**

HEX 的原理同与 rgb 一样，依然是通过：红、绿、蓝色进行组合，只不过要用 6位（分成3组） 来表达，格式为 #rrggbb

例如：

```css
color: #ff0000;/* 红色 */ 
color: #00ff00;/* 绿色 */ 

/* 如果每种颜色的两位都是相同的，就可以简写*/ 
color: #ff9988;/* 可简为：#f98 */

/* 但要注意前三位简写了，那么透明度就也要简写 */ 
color: #ff998866;/* 可简为：#f986 */
```

每一位数字的取值范围是： 0 ~ f ，即：（ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, a, b, c, d, e, f ）



**表达方式四：HSL 或 HSLA**

HSL 是通过：色相、饱和度、亮度，来表示一个颜色的，格式为： hsl(色相,饱和度,亮度)

色相：取值范围是 0~360 度，具体度数对应的颜色如下图

![image-20231205221847363](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231205221847363.png)



------

### 字体属性



**字体大小**

font-size，控制字体的大小，例如：

```css
div { 
	font-size: 40px; 
}
```



**字体族**

font-family，控制字体类型，例如：

```css
div { 
	font-family: "STCaiyun","Microsoft YaHei",sans-serif 
}
```



**字体风格**

font-style，控制字体是否为斜体，常用值如下：

1. normal ：正常（默认值）
2. italic ：斜体（使用字体自带的斜体效果）
3. oblique ：斜体（强制倾斜产生的斜体效果）

```css
div { 
	font-style: italic; 
}
```



**字体粗细**

font-weight，控制字体的粗细，常用值如下：

1. lighter ：细 
2. normal ： 正常 
3. bold ：粗 
4. bolder ：很粗 （多数字体不支持）

```
div { 
	font-weight: bold;
} 

/* 也可以使用 0 ~ 1000 的数值表示，数值越大，字体越粗 */ 
div {
	font-weight: 600; 
}
```



如果有多个字体属性，可以使用 font 属性把上述样式合成一个属性表示，各个属性间用空格隔开。需要注意的是，字体大小、字体族必须都写上，且字体族必须是最后一位、字体大小必须是倒数第二位



------

### 文本属性



**文本颜色**

color，有 4 种方式表示（颜色名、rgb 或 rgba、HEX 或 HEXA 以及 HSL 或 HSLA）

```css
div { 
	color: rgb(112,45,78);
}
```



**文本间距**

字母间距： letter-spacing

单词间距： word-spacing （通过空格识别词）

属性值为像素（ px ），正值让间距增大，负值让间距缩小



**文本修饰**

text-decoration，控制文本的各种装饰线，常用值如下：

1. none ： 无装饰线（常用）
2. underline ：下划线（常用）
3. overline ： 上划线
4. line-through ： 删除线
5. dotted ：虚线
6. wavy ：波浪线

```css
a { 
	text-decoration: underline ; 
}
```



**文本缩进**

text-indent，控制文本首字母的缩进

```css
div { 
	text-indent:40px;
}
```



**文本水平对齐**

text-align，控制文本的水平对齐方式，常用值如下：

1. left ：左对齐（默认值）
2. right ：右对齐
3. center ：居中对齐

```css
div { 
	text-align: center;
}
```



**行高**

line-height，控制一行文字的高度，表示方式如下：

1. normal ：由浏览器根据文字大小决定的一个默认值
2. 像素( px )
3. 数字：参考自身 font-size 的倍数（很常用）
4. 百分比：参考自身 font-size 的百分比

```css
div { 
	line-height: 60px; 
	line-height: 1.5; 
	line-height: 150%;
}
```



**vertical-align**

vertical-align，用于指定同一行元素之间，或表格单元格内文字的垂直对齐方式，常用值如下：

1. baseline （默认值）：使元素的基线与父元素的基线对齐
2. top ：使元素的顶部与其所在行的顶部对齐
3. middle ：使元素的中部与父元素的基线加上父元素字母 x 的一半对齐
4.  bottom ：使元素的底部与其所在行的底部对齐

需要注意的是，vertical-align 不能控制块元素



------

### 列表属性



列表相关的属性，可以作用在 ul 、 ol 、 li 元素上

| CSS 属性名          | 功能               | 属性值                                                       |
| ------------------- | ------------------ | ------------------------------------------------------------ |
| list-style-type     | 设置列表符号       | 常用值如下： <br />none ：不显示前面的标识（很常用！） <br />square ：实心方块 <br />disc ：圆形 <br />decimal ：数字 <br />lower-roman ：小写罗马字 <br />upper-roman ：大写罗马字 <br />lower-alpha ：小写字母 <br />upper-alpha ：大写字母 |
| list-style-position | 设置列表符号的位置 | inside ：在 li 的里面 <br />outside ：在 li 的外边           |
| list-style-image    | 自定义列表符号     | url(图片地址)                                                |
| list-style          | 复合属性           | 没有数量、顺序的要求                                         |



------

### 表格属性



边框相关属性（其他标签也能用）：

| CSS 属性名   | 功能         | 属性值                                                       |
| ------------ | ------------ | ------------------------------------------------------------ |
| border-width | 边框宽度     | CSS 中可用的长度值                                           |
| border-color | 边框颜色     | CSS 中可用的颜色值                                           |
| border-style | 边框风格     | none 默认值 <br />solid 实线 <br />dashed 虚线 <br />dotted 点线 <br />double 双实线 |
| border       | 边框复合属性 | 没有数量、顺序的要求                                         |



表格独有属性（只有 table 标签才能使用）：

| CSS 属性名      | 功能                 | 属性值                                                       |
| --------------- | -------------------- | ------------------------------------------------------------ |
| table-layout    | 设置列宽度           | auto ：自动，列宽根据内容计算（默认值）<br />fixed ：固定列宽，平均分 |
| border-spacing  | 单元格间距           | CSS 中可用的长度值。 生效的前提：单元格边框不能合并          |
| border-collapse | 合并单元格边框       | collapse ：合并 <br />separate ：不合并                      |
| empty-cells     | 隐藏没有内容的单元格 | show ：显示，默认 <br />hide ：隐藏<br/>生效前提：单元格不能合并 |
| caption-side    | 设置表格标题位置     | top ：上面（默认值）<br /> bottom ：在表格下面               |



------

### 背景属性



| 属性名              | 功能             | 属性值                                                       |
| ------------------- | ---------------- | ------------------------------------------------------------ |
| background-color    | 设置背景颜色     | 符合 CSS 中颜色规范的值<br />默认背景颜色是 transparent      |
| background-image    | 设置背景图片     | url(图片的地址)                                              |
| background-repeat   | 设置背景重复方式 | repeat ：重复，铺满整个元素，默认值<br />repeat-x ：只在水平方向重复<br />repeat-y ：只在垂直方向重复<br />no-repeat ：不重复 |
| background-position | 设置背景图位置   | 通过关键字设置位置： 写两个值<br />用空格隔开 水平： left 、 center 、 right <br />垂直: top 、 center 、 bottom <br />如果只写一个值，另一个方向的值取 center<br /><br /><br />通过长度指定坐标位置：<br/>以元素左上角，为坐标原点，设置图片左上角的 位置。<br/>两个值，分别是 x 坐标和 y 坐标。 只写一个值，会被当做 x 坐标， y 坐标取 center |
| background          | 复合属性         | 没有数量和顺序要求                                           |



------

### 鼠标属性



| 属性名 | 功能               | 属性值                                                       |
| ------ | ------------------ | ------------------------------------------------------------ |
| cursor | 设置鼠标光标的样式 | pointer ：小手 <br />move ：移动图标 <br />text ：文字选择器 <br />crosshair ：十字架 <br />wait ：等待 <br />help ：帮助 |



------

## 盒子模型

### 长度单位



1. px ：像素
2. em ：相对元素 font-size 的倍数
3. rem ：相对根字体大小，html 标签就是根
4. % ：相对父元素计算

CSS 中设置长度，必须加单位，否则样式无效



------

### 元素的显示模式



**块元素**

特点：

1. 在页面中独占一行，不会与任何元素共用一行，是从上到下排列的
2. 默认宽度：撑满父元素
3. 默认高度：由内容撑开
4. 可以通过 CSS 设置宽高

典型块元素：

1. 主体结构标签： html、body
2. 排版标签： h1 ~ h6 、 hr 、 p 、 pre 、 div
3. 列表标签： ul 、 ol 、 li 、 dl 、 dt 、 dd
4. 表格相关标签： table 、 tbody 、 thead 、 tfoot 、 tr 、 caption
5. form 与 option



**行内元素**

特点：

1. 在页面中不独占一行，一行中不能容纳下的行内元素，会在下一行继续从左到右排列
2. 默认宽度：由内容撑开
3. 默认高度：由内容撑开
4. 无法通过 CSS 设置宽高

典型行内元素：

1. 文本标签： br>、 em 、 strong 、 sup 、 sub 、 del 、 ins
2. a 与 label



**行内块元素**

特点：

1. 在页面中不独占一行，一行中不能容纳下的行内元素，会在下一行继续从左到右排 列
2. 默认宽度：由内容撑开
3. 默认高度：由内容撑开
4. 可以通过 CSS 设置宽高

典型行内块元素：

1. 图片： img
2. 单元格： td 、 th
3. 表单控件： input 、 textarea 、 select 、 button
4. 框架标签： iframe



------

### 修改元素的显示模式



通过 CSS 中的 display 属性可以修改元素的默认显示模式，常用值如下：

| 值           | 描述                     |
| ------------ | ------------------------ |
| none         | 元素会被隐藏             |
| block        | 元素将作为块级元素显示   |
| inline       | 元素将作为内联元素显示   |
| inline-block | 元素将作为行内块元素显示 |



------

### 盒子模型的组成



CSS 会把所有的 HTML 元素都看成一个盒子，所有的样式也都是基于这个盒子

1. margin（外边距）： 盒子与外界的距离
2. border（边框）： 盒子的边框
3. padding（内边距）： 紧贴内容的补白区域
4. content（内容）：元素中的文本或后代元素都是它的内容



![image-20231206223511161](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231206223511161.png)

盒子的大小 = content + 左右 padding + 左右 border

外边距 margin 不会影响盒子的大小，但会影响盒子的位置



------

### Content



| CSS 属性名 | 功能                   | 属性值 |
| ---------- | ---------------------- | ------ |
| width      | 设置内容区域宽度       | 长度   |
| max-width  | 设置内容区域的最大宽度 | 长度   |
| min-width  | 设置内容区域的最小宽度 | 长度   |
| height     | 设置内容区域的高度     | 长度   |
| max-height | 设置内容区域的最大高度 | 长度   |
| min-height | 设置内容区域的最小高度 | 长度   |



所谓的默认宽度，就是不设置 width 属性时，元素所呈现出来的宽度

总宽度 = 父的 content — 自身的左右 margin

内容区的宽度 = 父的 content — 自身的左右 margin — 自身的左右 border — 自身的左右 padding



------

### Padding



| 属性名         | 功能     | 属性值                    |
| -------------- | -------- | ------------------------- |
| padding-top    | 上内边距 | 长度                      |
| padding-right  | 右内边距 | 长度                      |
| padding-bottom | 下内边距 | 长度                      |
| padding-left   | 左内边距 | 长度                      |
| padding        | 复合属性 | 长度，可以设置 1 ~ 4 个值 |



padding 复合属性的使用规则：

1. padding: 10px; 四个方向内边距都是 10px
2. padding: 10px 20px; 上 10px ，左右 20px 。（上下、左右）
3. padding: 10px 20px 30px; 上 10px ，左右 20px ，下 30px 。（上、左右、下）
4. padding: 10px 20px 30px 40px; 上 10px ，右 20px ，下 30px ，左 40px 。（上、右、 下、左）



padding 的值不能为负数

行内元素的左右内边距是没问题的，上下内边距不能完美的设置

块级元素、行内块元素，四个方向内边距都可以完美设置



------

### Border



| 属性名                                                       | 功能                                                   | 属性值                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| border-style                                                 | 边框线风格 <br />border-style 复合了四个方向的边框风格 | none ： 默认值 <br />solid ： 实线 <br />dashed ： 虚线 <br />dotted ： 点线 <br />double ： 双实线<br/>...... |
| border-width                                                 | 边框线宽度 <br />复合了四个方向的边框宽度              | 长度，默认 3px                                               |
| border-color                                                 | 边框线颜色 <br />复合了四个方向的边框颜色              | 颜色，默认黑色                                               |
| border                                                       | 复合属性                                               | 值没有顺序和数量要求                                         |
| border-left <br />border-left-style <br />border-left-width <br />border-left-color<br/><br />border-right <br />border-right-style <br />border-right-width <br />border-right-color<br/><br/>border-top <br />border-top-style <br />border-top-width <br />border-top-color<br/><br />border-bottom <br />border-bottom-style <br />border-bottom-width <br />border-bottom-color | 分别设置各个方向的边框                                 | 同上                                                         |



border-style 、 border-width 、 border-color 其实也是复合属性



------

### Margin



| 属性名        | 功能                                                 | 属性值         |
| ------------- | ---------------------------------------------------- | -------------- |
| margin-left   | 左外边距                                             | CSS 中的长度值 |
| margin-right  | 右外边距                                             | CSS 中的长度值 |
| margin-top    | 上外边距                                             | CSS 中的长度值 |
| margin-bottom | 下外边距                                             | CSS 中的长度值 |
| margin        | 复合属性，可以写 1~4 个值，规律同 padding （顺时针） | CSS 中的长度值 |



**注意事项**

子元素的 margin ，是参考父元素的 content 计算的。（因为是父亲的 content 中承装着子元素）

上 margin 、左 margin ：影响自己的位置；下 margin 、右 margin ：影响后面兄弟元素的位置

块级元素、行内块元素，均可以完美地设置四个方向的 margin ；但行内元素，左右 margin 可以完美设置，上下 margin 设置无效

margin 的值也可以是 auto ，如果给一个块级元素设置左右 margin 都为 auto ，该块级元素会在父元素中水平居中

margin 的值可以是负值



**margin 塌陷问题**

解释：第一个子元素的上 margin 会作用在父元素上，最后一个子元素的下 margin 会作用在父元素上

解决方案：

1. 方案一： 给父元素设置不为 0 的 padding
2. 方案二： 给父元素设置宽度不为 0 的 border
3. 方案三：给父元素设置 css 样式 overflow:hidden



**margin 合并问题**

解释：上面兄弟元素的下外边距和下面兄弟元素的上外边距会合并，取一个最大的值，而不是相加

解决方案：无需解决，布局的时候上下的兄弟元素，只给一个设置上下外边距就可以了



------

### 处理内容溢出



| 属性名     | 功能                         | 属性值                                                       |
| ---------- | ---------------------------- | ------------------------------------------------------------ |
| overflow   | 溢出内容的处理方式           | visible ：显示，默认值 <br />hidden ：隐藏 <br />scroll ：显示滚动条，不论内容是否溢出 <br />auto ：自动显示滚动条，内容不溢出不显示 |
| overflow-x | 水平方向溢出内容的处理方式   | 同 overflow                                                  |
| overflow-y | 垂直方向溢出内容给的处理方式 | 同 overflow                                                  |



overflow-x 、 overflow-y 不能一个是 hidden ，一个是 visible ，是实验性属性，不 建议使用

overflow 常用的值是 hidden 和 auto ，除了能处理溢出的显示方式，还可以解决很多 疑难杂症



------

### 隐藏元素的方式



**方式一：visibility**

visibility 属性默认值是 show ，如果设置为 hidden ，元素会隐藏

元素看不见了，还占有原来的位置（元素的大小依然保持）

**方式二：display 属性**

设置 display:none ，就可以让元素隐藏

彻底地隐藏，不但看不见，也不占用任何位置，没有大小宽高



------

### 样式的继承



有些样式会继承，元素如果本身设置了某个样式，就使用本身设置的样式；但如果本身没有设置某个样式，会从父元素开始一级一级继承（优先继承离得近的祖先元素）

会继承的 css 属性：字体属性、文本属性（除了 vertical-align）、文字颜色等

不会继承的 css 属性：边框、背景、内边距、外边距、宽高、溢出方式等

一个规律：能继承的属性，都是不影响布局的，简单说：都是和盒子模型没关系的



------

### 布局小技巧



1. 行内元素、行内块元素，可以被父元素当做文本处理

   即：可以像处理文本对齐一样，去处理：行内、行内块在父元素中的对齐。 例如： text-align 、 line-height 、 text-indent 等

2. 如何让子元素，在父亲中水平居中：

   * 若子元素为块元素，给父元素加上： margin:0 auto
   * 若子元素为行内元素、行内块元素，给父元素加上： text-align:center

3. 如何让子元素，在父亲中垂直居中

   * 若子元素为块元素，给子元素加上： margin-top ，值为：(父元素 content －子元素盒子 总高) / 2
   * 若子元素为行内元素、行内块元素： 让父元素的 height = line-height ，每个子元素都加上： verticalalign:middle; 若想绝对垂直居中，父元素 font-size 设置为 0



------

### 元素之间的空白问题



产生原因：行内元素、行内块元素，彼此之间的换行会被浏览器解析为一个空白字符

解决方案：

1. 去掉换行和空格（不推荐）
2. 给父元素设置 font-size:0 ，再给需要显示文字的元素，单独设置字体大小（推荐）



------

### 行内块的幽灵空白问题



产生原因：行内块元素与文本的基线对齐，而文本的基线与文本最底端之间是有一定距离的

解决方案：

1. 给行行内块设置 vertical ，值不为 baseline 即可，设置为 middel 、 bottom 、 top 均可
2. 若父元素中只有一张图片，设置图片为 display:block
3. 给父元素设置 font-size: 0 。如果该行内块内部还有文本，则需单独设置 fontsize



------

## 浮动

### 元素浮动后的特点



脱离文档流

不管浮动前是什么元素，浮动后：默认宽与高都是被内容撑开（尽可能小），而且可以设置宽高

不会独占一行，可以与其他元素共用一行

不会 margin 合并，也不会 margin 塌陷，能够完美的设置四个方向的 margin 和 padding

不会像行内块一样被当做文本处理（没有行内块的空白问题）



------

### 浮动产生的问题及解决方案



**元素浮动产生的影响**

对兄弟元素的影响： 后面的兄弟元素，会占据浮动元素之前的位置，在浮动元素的下面；对前面的兄弟无影响

对父元素的影响： 不能撑起父元素的高度，导致父元素高度塌陷；但父元素的宽度依然束缚浮动的元 素



**解决方案**

1. 给父元素指定高度

2. 给父元素也设置浮动，带来其他影响

3. 给父元素设置 overflow:hidden

4. 在所有浮动元素的最后面，添加一个块级元素，并给该块级元素设置 clear:both

5. 给浮动元素的父元素，设置伪元素，通过伪元素清除浮动，原理与方案四相同。===> 推荐使用

   ```css
   .parent::after { 
   	content: ""; 
   	display: block; 
   	clear:both;
   }
   ```



布局中的一个原则：设置浮动的时候，兄弟元素要么全都浮动，要么全都不浮动



------

## 定位

### 相对定位



**如何设置相对定位**

给元素设置 position:relative 即可实现相对定位

可以使用 left 、 right 、 top 、 bottom 四个属性调整位置

相对定位的参考点是相对自己原来的位置



**相对定位的特点**

不会脱离文档流，元素位置的变化，只是视觉效果上的变化，不会对其他元素产生任何影响

定位元素的显示层级比普通元素高，无论什么定位，显示层级都是一样的

left 不能和 right 一起设置， top 和 bottom 不能一起设置

相对定位的元素，也能继续浮动，但不推荐这样做

相对行为的元素，也能通过 margin 调整位置，但不推荐这样做

绝大多数情况下，相对定位，会与绝对定位配合使用



------

### 绝对定位



**如何设置绝对定位**

给元素设置 position: absolute 即可实现绝对定位

可以使用 left 、 right 、 top 、 bottom 四个属性调整位置



**绝对定位的参考点在哪里**

参考它的包含块

对于没有脱离文档流的元素：包含块就是父元素

对于脱离文档流的元素：包含块是第一个拥有定位属性的祖先元素（如果所有祖先都没定位，那包含块就是整个页面）



**绝对定位元素的特点：**

1. 脱离文档流，会对后面的兄弟元素、父元素有影响
2. left 不能和 right 一起设置， top 和 bottom 不能一起设置
3. 绝对定位、浮动不能同时设置，如果同时设置，浮动失效，以定位为主
4. 绝对定位的元素，也能通过 margin 调整位置，但不推荐这样做
5. 无论是什么元素（行内、行内块、块级）设置为绝对定位之后，都变成了定位元素（定位元素指的是默认宽、高都被内容所撑开，且能自由设置宽高的元素）



------

### 固定定位



**如何设置为固定定位**

给元素设置 position: fixed 即可实现固定定位

可以使用 left 、 right 、 top 、 bottom 四个属性调整位置



**固定定位的参考点**

参考它的视口



**固定定位元素的特点**

1. 脱离文档流，会对后面的兄弟元素、父元素有影响
2. left 不能和 right 一起设置， top 和 bottom 不能一起设置
3. 固定定位和浮动不能同时设置，如果同时设置，浮动失效，以固定定位为主
4. 固定定位的元素，也能通过 margin 调整位置，但不推荐这样做
5. 无论是什么元素（行内、行内块、块级）设置为固定定位之后，都变成了定位元素



------

### 粘性定位



**如何设置为粘性定位**

给元素设置 position:sticky 即可实现粘性定位

可以使用 left 、 right 、 top 、 bottom 四个属性调整位置，不过最常用的是 top 值



**粘性定位的参考点在哪里**

离它最近的一个拥有“滚动机制”的祖先元素，即便这个祖先不是最近的真实可滚动祖先



**粘性定位元素的特点**

1. 不会脱离文档流，它是一种专门用于窗口滚动时的新的定位方式
2. 最常用的值是 top 值
3. 粘性定位和浮动可以同时设置，但不推荐这样做
4. 粘性定位的元素，也能通过 margin 调整位置，但不推荐这样做

粘性定位和相对定位的特点基本一致，不同的是：粘性定位可以在元素到达某个位置时将其固定



------

### 定位的层级



定位元素的显示层级比普通元素高，无论什么定位，显示层级都是一样的

如果位置发生重叠，默认情况是：后面的元素，会显示在前面元素之上

可以通过 css 属性 z-index 调整元素的显示层级

z-index 的属性值是数字，没有单位，值越大显示层级越高

只有定位的元素设置 z-index 才有效

如果 z-index 值大的元素，依然没有覆盖掉 z-index 值小的元素，那么请检查其包含块的层级



------

### 定位的特殊应用



**让定位元素的宽充满包含块**

块宽想与包含块一致，可以给定位元素同时设置 left 和 right 为 0

高度想与包含块一致， top 和 bottom 设置为 0



**让定位元素在包含块中居中**

方案一：

```css
left:0; 
right:0; 
top:0; 
bottom:0; 
margin:auto;
```

方案二：

```css
left: 50%; 
top: 50%; 
margin-left: 负的宽度一半; 
margin-top: 负的高度一半;
```



------

## 布局

### 版心



在 PC 端网页中，一般都会有一个固定宽度且水平居中的盒子，来显示网页的主要内容，这是网页的版心

版心的宽度一般是 960 ~ 1200 像素之间

版心可以是一个，也可以是多个



------

### 常用布局名词



| 位置               |                            |
| ------------------ | -------------------------- |
| 顶部导航条         | topbar                     |
| 页头               | header 、 page-header      |
| 导航               | nav 、 navigator 、 navbar |
| 搜索框             | search 、 search-box       |
| 横幅、广告、宣传图 | banner                     |
| 主要内容           | content 、 main            |
| 侧边栏             | aside 、 sidebar           |
| 页脚               | footer 、 page-footer      |



------

### 重置默认样式



很多元素都有默认样式，比如：
1. p 元素有默认的上下 margin
1.  h1~h6 标题也有上下 margin ，且字体加粗
1. body 元素有默认的 8px 外边距
1. 超链接有默认的文字颜色和下划线
1. ul 元素有默认的左 pading 
1. .......

在早期，元素默认样式，能够让我们快速的绘制网页，但如今网页的设计越来越复杂，内容越来越 多，而且很精细，这些默认样式会给我们绘制页面带来麻烦；而且这些默认样式，在不同的浏览器 上呈现出来的效果也不一样，所以我们需要重置这些默认样式



**方案一：使用全局选择器**

```css
* {
	margin: 0; 
	padding: 0; 
	......
}
```

此种方法，在简单案例中可以用一下，但实际开发中不会使用，因为 * 选择的是所有元素，而并不是所有的元素都有默认样式；而且我们重置时，有时候是需要做特定处理的，比如：想让 a 元素的文字是灰色，其他元素文字是蓝色



**方案二：reset.css**

选择到具有默认样式的元素，清空其默认的样式

经过 reset 后的网页，好似“一张白纸”，开发人员可根据设计稿，精细的去添加具体的样式



**方案三：Normalize.css**

Normalize.css 是一种最新方案，它在清除默认样式的基础上，保留了一些有价值的默认样式。 官网地址：http://necolas.github.io/normalize.css/

相对于 reset.css ， Normalize.css 有如下优点：

1. 保护了有价值的默认样式，而不是完全去掉它们
2. 为大部分 HTML 元素提供一般化的样式
3. 新增对 HTML5 元素的设置
4. 对并集选择器的使用比较谨慎，有效避免调试工具杂乱

Normalize.css 的重置，和 reset.css 相比，更加的温和，开发时可根据实际情况进行选择



------

## 私有前缀



**什么是私有前缀**

如下代码中的 -webkit- 就是私有前缀

```css
div { 
	width:400px; 
	height:400px; 
	-webkit-border-radius: 20px;
}
```



**为什么要有私有前缀**

W3C 标准所提出的某个 CSS 特性，在被浏览器正式支持之前，浏览器厂商会根据浏览器的内核， 使用私有前缀来测试该 CSS 特性，在浏览器正式支持该 CSS 特性后，就不需要私有前缀了

例如：

```
-webkit-border-radius: 20px; 
-moz-border-radius: 20px; 
-ms-border-radius: 20px; 
-o-border-radius: 20px; 
border-radius: 20px;
```

查询 CSS3 兼容性的网站：https://caniuse.com/



**常见浏览器私有前缀**

Chrome 浏览器： -webkit-

Safari 浏览器： -webkit-

Firefox 浏览器： -moz-

Edge 浏览器： -webkit-



------

## CSS3 新增属性

### 盒子相关属性



**box-sizing 怪异盒模型**

使用 box-sizing 属性可以设置盒模型的两种类型

| 可选值      | 含义                                                 |
| ----------- | ---------------------------------------------------- |
| content-box | width 和 height 设置的是盒子内容区的大小。（默认值） |
| border-box  | width 和 height 设置的是盒子总大小。（怪异盒模型）   |



**resize 调整盒子大小**

使用 resize 属性可以控制是否允许用户调节元素尺寸

| 值         | 含义                            |
| ---------- | ------------------------------- |
| none       | 不允许用户调整元素大小。 (默认) |
| both       | 用户可以调节元素的宽度和高度    |
| horizontal | 用户可以调节元素的宽度          |
| vertical   | 用户可以调节元素的高度          |



**box-shadow 盒子阴影**

语法：

```
box-shadow: h-shadow v-shadow blur spread color inset;
```

| 值       | 含义                                 |
| -------- | ------------------------------------ |
| h-shadow | 水平阴影的位置，必须填写，可以为负值 |
| v-shadow | 垂直阴影的位置，必须填写，可以为负值 |
| blur     | 可选，模糊距离                       |
| spread   | 可选，阴影的外延值                   |
| color    | 可选，阴影的颜色                     |
| inset    | 可选，将外部阴影改为内部阴影         |

示例：

```css
/* 写两个值，含义：水平位置、垂直位置 */ 
box-shadow: 10px 10px;

/* 写三个值，含义：水平位置、垂直位置、颜色 */ 
box-shadow: 10px 10px red;

/* 写三个值，含义：水平位置、垂直位置、模糊值 */ 
box-shadow: 10px 10px 10px;

/* 写四个值，含义：水平位置、垂直位置、模糊值、颜色 */ 
box-shadow: 10px 10px 10px red;

/* 写五个值，含义：水平位置、垂直位置、模糊值、外延值、颜色 */ 
box-shadow: 10px 10px 10px 10px blue;

/* 写六个值，含义：水平位置、垂直位置、模糊值、外延值、颜色、内阴影 */ 
box-shadow: 10px 10px 20px 3px blue inset;
```



**opacity不透明度**

opacity 属性能为整个元素添加透明效果， 值是 0 到 1 之间的小数， 0 是完全透明， 1 表示完全不透明



------

### 背景属性



**background-origin**

作用：设置背景图的原点

语法：

* padding-box ：从 padding 区域开始显示背景图像。—— 默认值
* border-box ： 从 border 区域开始显示背景图像
* content-box ： 从 content 区域开始显示背景图像



**background-clip**

作用：设置背景图的向外裁剪的区域

语法：

* border-box ： 从 border 区域开始向外裁剪背景。 —— 默认值
* padding-box ： 从 padding 区域开始向外裁剪背景
* content-box ： 从 content 区域开始向外裁剪背景
* text ：背景图只呈现在文字上

若值为 text ，那么 backgroun-clip 要加上 -webkit- 前缀



**background-size**

作用：设置背景图的尺寸

语法

* 用长度值指定背景图片大小，不允许负值

  ```css
  background-size: 300px 200px;
  ```

* 用百分比指定背景图片大小，不允许负值

  ```css
  background-size: 100% 100%;
  ```

* auto ： 背景图片的真实大小。 —— 默认值

* contain ： 将背景图片等比缩放，使背景图片的宽或高，与容器的宽或高相等，再将完整背景图片包含在容器内，但要注意：可能会造成容器里部分区域没有背景图片

  ```css
  background-size: contain;
  ```

* cover ：将背景图片等比缩放，直到完全覆盖容器，图片会尽可能全的显示在元素上，但要注意：背景图片有可能显示不完整。—— 相对比较好的选择

  ```css
  background-size: cover;
  ```



**backgorund 复合属性**

语法：

```css
background: color url repeat position / size origin clip
```

origin 和 clip 的值如果一样，如果只写一个值，则 origin 和 clip 都设置；如果设置了两个值，前面的是 origin ，后面的 clip

size 的值必须写在 position 值的后面，并且用 / 分开



**多背景图**

CSS3 允许元素设置多个背景图片

```css
/* 添加多个背景图 */
background: url(../images/bg-lt.png) no-repeat, 
			url(../images/bg-rt.png) no-repeat right top, 
			url(../images/bg-lb.png) no-repeat left bottom, 
			url(../images/bg-rb.png) no-repeat right bottom;
```



------

### 边框属性



**边框圆角**

作用：在 CSS3 中，使用 border-radius 属性可以将盒子变为圆角

语法：

```css
/* 同时设置四个角的圆角 */
border-radius:10px;
```



------

### 文本属性



**文本阴影**

在 CSS3 中，我们可以使用 text-shadow 属性给文本添加阴影

语法：

```css
text-shadow: h-shadow v-shadow blur color;
```

| 值       | 描述                             |
| -------- | -------------------------------- |
| h-shadow | 必需写，水平阴影的位置。允许负值 |
| v-shadow | 必需写，垂直阴影的位置。允许负值 |
| blur     | 可选，模糊的距离                 |
| color    | 可选，阴影的颜色                 |

默认值： text-shadow:none 表示没有阴影



**文本换行**

在 CSS3 中，我们可以使用 white-space 属性设置文本换行方式

常用值如下：

| 值       | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| normal   | 文本超出边界自动换行，文本中的换行被浏览器识别为一个空格。（默认值） |
| pre      | 原样输出，与 pre 标签的效果相同                              |
| pre-wrap | 在 pre 效果的基础上，超出元素边界自动换行                    |
| pre-line | 在 pre 效果的基础上，超出元素边界自动换行，且只识别文本中的换行，空格 会忽略 |
| nowrap   | 强制不换行                                                   |



**文本溢出**

在 CSS3 中，我们可以使用 text-overflow 属性设置文本内容溢出时的呈现模式

常用值如下：

| 值       | 含义                                            |
| -------- | ----------------------------------------------- |
| clip     | 当内联内容溢出时，将溢出部分裁切掉。 （默认值） |
| ellipsis | 当内联内容溢出块容器时，将溢出部分替换为 ...    |

要使得 text-overflow 属性生效，块容器必须显式定义 overflow 为非 visible 值， white-space 为 nowrap 值



**文本描边**

注意：文字描边功能仅 webkit 内核浏览器支持

* -webkit-text-stroke-width ：设置文字描边的宽度，写长度值 
* -webkit-text-stroke-color ：设置文字描边的颜色，写颜色值
* -webkit-text-stroke ：复合属性，设置文字描边宽度和颜色



------

### Web 字体



**基本用法**

可以通过 @font-face 指定字体的具体地址，浏览器会自动下载该字体，这样就不依赖用户电脑上的字 体了

语法：

```css
 /* 简写模式 */
@font-face { 
	font-family: "情书字体"; 
	src: url('./方正手迹.ttf');
}
```

```css
@font-face { 
	font-family: "simon"; 
	font-display: swap; 
	src: url('webfont.eot'); /* IE9 */ 
	src: url('webfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */ 
	url('webfont.woff2') format('woff2'), 
	url('webfont.woff') format('woff'), /* chrome、firefox */ 
	url('webfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari,Android*/ 
	url('webfont.svg#webfont') format('svg'); /* iOS 4.1- */
}
```



**定制字体**

中文的字体文件很大，使用完整的字体文件不现实，通常针对某几个文字进行单独定制

可使用阿里 Web 字体定制工具：https://www.iconfont.cn/webfont



**字体图标**

相比图片更加清晰

灵活性高，更方便改变大小、颜色、风格等

兼容性好， IE 也能支持

字体图标的具体使用方式，每个平台不尽相同，最好参考平台使用指南，我们是以使用最多的阿里图标库作为演示

阿里图标官网地址：https://www.iconfont.cn/



------

## 2D 变换

### 位移



2D 位移可以改变元素的位置，具体使用方式如下：

1. 先给元素添加转换属性 transform

2. 编写 transform 的具体值，相关可选值如下：

   | 值         | 含义                                                         |
   | ---------- | ------------------------------------------------------------ |
   | translateX | 设置水平方向位移，需指定长度值；若指定的是百分比，是参考自身宽度的百分比 |
   | translateY | 设置垂直方向位移，需指定长度值；若指定的是百分比，是参考自身高度的百分比 |
   | translate  | 一个值代表水平方向，两个值代表：水平和垂直方向               |

   

位移与相对定位很相似，都不脱离文档流，不会影响到其它元素

与相对定位的区别：相对定位的百分比值，参考的是其父元素；定位的百分比值，参考的是其自身

浏览器针对位移有优化，与定位相比，浏览器处理位移的效率更高

位移对行内元素无效

位移配合定位，可实现元素水平垂直居中

```css
.box { 
	position: absolute; 
	left: 50%; top: 50%; 
	transform: translate(-50%, -50%);
}
```



------

### 缩放



2D 缩放是指：让元素放大或缩小，具体使用方式如下：

1. 先给元素添加转换属性 transform

2. 编写 transform 的具体值，相关可选值如下：

   | 值     | 含义                                                         |
   | ------ | ------------------------------------------------------------ |
   | scaleX | 设置水平方向的缩放比例，值为一个数字， 1 表示不缩放，大于 1 放大，小于 1 缩小 |
   | scaleY | 设置垂直方向的缩放比例，值为一个数字， 1 表示不缩放，大于 1 放大，小 于 1 缩小 |
   | scale  | 同时设置水平方向、垂直方向的缩放比例，一个值代表同时设置水平和垂直缩放；两个值分别代表：水平缩放、垂直缩放 |



scale 的值，是支持写负数的，但几乎不用，因为容易让人产生误解

借助缩放，可实现小于 12px 的文字



------

### 旋转



2D 旋转是指：让元素在二维平面内，顺时针旋转或逆时针旋转，具体使用方式如下：

1. 先给元素添加转换属性 transform

2. 编写 transform 的具体值，相关可选值如下：

   | 值     | 含义                                                         |
   | ------ | ------------------------------------------------------------ |
   | rotate | 设置旋转角度，需指定一个角度值( deg )，正值顺时针，负值逆时针 |

   

------

### 变换原点



元素变换时，默认的原点是元素的中心，使用 transform-origin 可以设置变换的原点

修改变换原点对位移没有影响， 对旋转和缩放会产生影响

如果提供两个值，第一个用于横坐标，第二个用于纵坐标

如果只提供一个，若是像素值，表示横坐标，纵坐标取 50% ；若是关键词，则另一个坐标取 50%

1. transform-origin: 50% 50% ， 变换原点在元素的中心位置，百分比是相对于自 身。—— 默认值
2. transform-origin: left top ，变换原点在元素的左上角 
3. transform-origin: 50px 50px ， 变换原点距离元素左上角 50px 50px 的位置
4. transform-origin: 0 ，只写一个值的时候，第二个值默认为 50% 。



------

## 3D 变换

### 开启3D空间



重要原则：元素进行 3D 变换的首要操作：父元素必须开启 3D 空间

使用 transform-style 开启 3D 空间，可选值如下：

* flat ： 让子元素位于此元素的二维平面内（ 2D 空间）—— 默认值
* preserve-3d ： 让子元素位于此元素的三维空间内（ 3D 空间）



------

### 设置景深



景深是指定观察者与 z=0 平面的距离，能让发生 3D 变换的元素，产生透视效果，看来更加立体

使用 perspective 设置景深，可选值如下：

* none ： 不指定透视 ——（默认值）
* 长度值 ： 指定观察者距离 z=0 平面的距离，不允许负值

注意： perspective 设置给发生 3D 变换元素的父元素



------

### 透视点位置



所谓透视点位置，就是观察者位置；默认的透视点在元素的中心

用 perspective-origin 设置观察者位置（透视点的位置），例如：

```css
/* 相对坐标轴往右偏移400px， 往下偏移300px（相当于人蹲下300像素，然后向右移动400像素看元素） */ 
perspective-origin: 400px 300px;
```

注意：通常情况下，我们不需要调整透视点位置



------

### 位移



3D 位移是在 2D 位移的基础上，可以让元素沿 z 轴位移，具体使用方式如下：

* 先给元素添加转换属性 transform

* 编写 transform 的具体值， 3D 相关可选值如下：

  | 值       | 含义                                                         |
  | -------- | ------------------------------------------------------------ |
  | rotateX  | 设置 x 轴旋转角度，需指定一个角度值( deg )，面对 x 轴正方向：正值顺时针，负值逆时针 |
  | rotateY  | 设置 y 轴旋转角度，需指定一个角度值( deg )，面对 y 轴正方向：正值顺时针，负值逆时针 |
  | rotate3d | 前 3 个参数分别表示坐标轴： x , y , z ，第 4 个参数表示旋转的角度，参数不允许省略<br /> 例如： transform: rotate3d(1,1,1,30deg) ，意思是： x 、 y 、 z 分别旋转 30 度 |

  



------

### 缩放



3D 缩放是在 2D 缩放的基础上，可以让元素沿 z 轴缩放，具体使用方式如下：

1. 先给元素添加转换属性 transform

2. 编写 transform 的具体值， 3D 相关可选值如下：

   | 值      | 含义                                                         |
   | ------- | ------------------------------------------------------------ |
   | scaleZ  | 设置 z 轴方向的缩放比例，值为一个数字， 1 表示不缩放，大于 1 放大，小于 1 缩小 |
   | scale3d | 第1个参数对应 x 轴，第2个参数对应 y 轴，第3个参数对应 z 轴，参数不允许省略 |

   

------

## 过渡



过渡可以在不使用 Flash 动画，不使用 JavaScript 的情况下，让元素从一种样式，平滑过渡为另一种样式



**transition-property**

作用：定义哪个属性需要过渡，只有在该属性中定义的属性（比如宽、高、颜色等）才会以有过渡效果

常用值：

* none ：不过渡任何属性
* all ：过渡所有能过渡的属性
* 具体某个属性名 ，例如： width 、 heigth ，若有多个以逗号分隔

不是所有的属性都能过渡，值为数字，或者值能转为数字的属性，都支持过渡，否则不支持过渡

常见的支持过渡的属性有：颜色、长度值、百分比、 z-index 、 opacity 、 2D 变换属 性、 3D 变换属性、阴影



**transition-duration**

作用：设置过渡的持续时间，即：一个状态过渡到另外一个状态耗时多久

常用值：

* 0 ：没有任何过渡时间 —— 默认值
* s 或 ms ：秒或毫秒
* 列表 ：如果想让所有属性都持续一个时间，那就写一个值，如果想让每个属性持续不同的时间那就写一个时间的列表



**transition-delay**

作用：指定开始过渡的延迟时间，单位： s 或 ms



**transition-timing-function**

作用：设置过渡的类型

常用值：

* ease ： 平滑过渡 —— 默认值

* linear ： 线性过渡

* ease-in ： 慢 → 快

* ease-out ： 快 → 慢

* ease-in-out ： 慢 → 快 → 慢

* step-start ： 等同于 steps(1, start)

* step-end ： 等同于 steps(1, end)

* steps( integer,?) ： 接受两个参数的步进函数。第一个参数必须为正整数，指定函数的 步数。第二个参数取值可以是 start 或 end ，指定每一步的值发生变化的时间点。第二个参数默认值为 end

* cubic-bezie ( number, number, number, number)： 特定的贝塞尔曲线类型

  在线制作贝赛尔曲线：https://cubic-bezier.com



**transition 复合属性**

如果设置了一个时间，表示 duration ；如果设置了两个时间，第一是 duration ，第二个是 delay ；其他值没有顺序要求

```css
transition:1s 1s linear all;
```



------

## 动画



**动画的基本使用**

* 第一步：定义关键帧（定义动画）

  1. 简单方式定义：

     ```css
     /*写法一*/
     @keyframes 动画名 {
         from {
             /*property1:value1*/
             /*property2:value2*/
         }
     
         to {
             /*property1:value1*/
         }
     }
     ```

  2. 完整方式定义：

     ```css
     @keyframes 动画名 {
         0% {
             /*property1:value1*/
         }
     
         20% {
             /*property1:value1*/
         }
     
         40% {
             /*property1:value1*/
         }
     
         60% {
             /*property1:value1*/
         }
     
         80% {
             /*property1:value1*/
         }
     
         100% {
             /*property1:value1*/
         }
     }
     ```

* 第二步：给元素应用动画，用到的属性如下：

  1. animation-name ：给元素指定具体的动画（具体的关键帧）

  2. animation-duration ：设置动画所需时间

  3. animation-delay ：设置动画延迟

     ```css
     .box {
         /* 指定动画 */
         animation-name: testKey;
         /* 设置动画所需时间 */
         animation-duration: 5s;
         /* 设置动画延迟 */
         animation-delay: 0.5s;
     }
     ```

     

**动画的其他属性**

* animation-timing-function ，设置动画运行的类型，值与过渡的 transition-timing-function 一致
* animation-iteration-count ，指定动画的播放次数，常用值如下：
  * number ：动画循环次数
  * infinite ： 无限循环
* animation-direction ，指定动画方向，常用值如下：
  * normal ： 正常方向 (默认)
  * reverse ： 反方向运行
  * alternate ： 动画先正常运行再反方向运行，并持续交替运行
  * alternate-reverse ： 动画先反运行再正方向运行，并持续交替运行
* animation-fill-mode ，设置动画之外的状态
  * forwards ： 设置对象状态为动画结束时的状态
  * backwards ： 设置对象状态为动画开始时的状态
* animation-play-state ，设置动画的播放状态，常用值如下：
  * running ： 运动 (默认)
  * paused ： 暂停



------

## 多列布局



作用：专门用于实现类似于报纸的布局

常用属性如下：

* column-count ：指定列数，值是数字
* column-width ：指定列宽，值是长度
* columns ：同时指定列宽和列数，复合属性；值没有数量和顺序要求
* column-gap ：设置列边距，值是长度
* column-rule-style ：设置列与列之间边框的风格，值与 border-style 一致
* column-rule-width ：设置列与列之间边框的宽度，值是长度
* column-rule-color ：设置列与列之间边框的颜色
* coumn-rule ：设置列边框，复合属性
* column-span 指定是否跨列；值: none 、 all



------

## 伸缩盒模型

### 简介



2009 年， W3C 提出了一种新的盒子模型 —— Flexible Box （伸缩盒模型，又称：弹性盒子）

它可以轻松的控制：元素分布方式、元素对齐方式、元素视觉顺序 .......

截止目前，除了在部分 IE 浏览器不支持，其他浏览器均已全部支持

伸缩盒模型的出现，逐渐演变出了一套新的布局方案 —— flex 布局

传统布局是指：基于传统盒状模型，主要靠： display 属性 + position 属性 + float 属性

flex 布局目前在移动端应用比较广泛，因为传统布局不能很好的呈现在移动设备上



------

### 容器与项目



* 伸缩容器： 开启了 flex 的元素，就是伸缩容器

  给元素设置： display:flex 或 display:inline-flex ，该元素就变为了伸缩容器

  display:inline-flex 很少使用，因为可以给多个伸缩容器的父容器，也设置为伸缩容器

  一个元素可以同时是伸缩容器和伸缩项目

* 伸缩项目：伸缩容器所有子元素自动成为了伸缩项目

  仅伸缩容器的子元素成为了伸缩项目，孙子元素、重孙子元素等后代，不是伸缩项目

  无论原来是哪种元素（块、行内块、行内），一旦成为了伸缩项目，全都会“块状化”



------

### 主轴与侧轴



主轴： 伸缩项目沿着主轴排列，主轴默认是水平的，默认方向是：从左到右（左边是起点，右边是终点）

侧轴： 与主轴垂直的就是侧轴，侧轴默认是垂直的，默认方向是：从上到下（上边是起点，下边是终点）



------

### 主轴方向



属性名： flex-direction

常用值如下：

* row ：主轴方向水平从左到右 —— 默认值
* row-reverse ：主轴方向水平从右到左
* column ：主轴方向垂直从上到下
* column-reverse ：主轴方向垂直从下到上

![image-20231212233130178](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231212233130178.png)

注意：改变了主轴的方向，侧轴方向也随之改变



------

### 主轴换行方式



属性名： flex-wrap

常用值如下：

* nowrap ：默认值，不换行

  ![image-20231212233348276](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231212233348276.png)

* wrap ：自动换行，伸缩容器不够自动换行

  ![image-20231212233425879](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231212233425879.png)

* wrap-reverse ：反向换行

  ![image-20231212233443888](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231212233443888.png)



flex-flow

flex-flow 是一个复合属性，复合了 flex-direction 和 flex-wrap 两个属性。 值没有顺序要求

```css
flex-flow: row wrap;
```



------

### 主轴对齐方式



属性名： justify-content

常用值如下：

* flex-start ：主轴起点对齐。—— 默认值
* flex-end ：主轴终点对齐
* center ：居中对齐
* space-between ：均匀分布，两端对齐（最常用）
* space-around ：均匀分布，两端距离是中间距离的一半
* space-evenly ：均匀分布，两端距离与中间距离一致



![image-20231212234610083](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231212234610083.png)



------

### 侧轴对齐方式



**一行的情况**

所需属性： align-items

常用值如下：

* flex-start ：侧轴的起点对齐
* flex-end ：侧轴的终点对齐
* center ：侧轴的中点对齐
* baseline : 伸缩项目的第一行文字的基线对齐
* stretch ：如果伸缩项目未设置高度，将占满整个容器的高度。—— （默认值）

![image-20231212235141777](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231212235141777.png)



**多行的情况**

所需属性： align-content

常用值如下：

* flex-start ：与侧轴的起点对齐
* flex-end ：与侧轴的终点对齐
* center ：与侧轴的中点对齐
* space-between ：与侧轴两端对齐，中间平均分布
* space-around ：伸缩项目间的距离相等，比距边缘大一倍
* space-evenly : 在侧轴上完全平分
* stretch ：占满整个侧轴。—— 默认值

![image-20231213000334292](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231213000334292.png)

![image-20231213000350585](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/CSS%20%E6%95%99%E7%A8%8B/image-20231213000350585.png)



------

### flex 实现水平垂直居中



方法一：父容器开启 flex 布局，随后使用 justify-content 和 align-items 实现水平垂直居中

```css
.outer {
    width: 400px;
    height: 400px;
    background-color: #888;
    display: flex;
    justify-content: center;
    align-items: center;
}

.inner {
    width: 100px;
    height: 100px;
    background-color: orange;
}
```



方法二：父容器开启 flex 布局，随后子元素 margin: auto

```css
.outer {
    width: 400px;
    height: 400px;
    background-color: #888;
    display: flex;
}

.inner {
    width: 100px;
    height: 100px;
    background-color: orange;
    margin: auto;
}
```



------

### 伸缩性



**flex-basis**

概念： flex-basis 设置的是主轴方向的基准长度，会让宽度或高度失效

主轴横向，宽度失效；主轴纵向，高度失效

作用：浏览器根据这个属性设置的值，计算主轴上是否有多余空间，默认值 auto ，即伸缩项目的宽或高



**flex-grow（伸）**

概念： flex-grow 定义伸缩项目的放大比例，默认为 0 ，即纵使主轴存在剩余空间，也不拉伸 （放大）

规则：

* 若所有伸缩项目的 flex-grow 值都为 1 ，则它们将等分剩余空间（如果有空间的话）
* 若三个伸缩项目的 flex-grow 值分别为： 1 、 2 、 3 ，则分别瓜分到： 1/6 、 2/6 、 3/6 的空间



**flex-shrink（缩）**

概念： flex-shrink 定义了项目的压缩比例，默认为 1 ，即如果空间不足，该项目将会缩小



**flex复合属性**

flex 是复合属性，复合了： flex-grow 、 flex-shrink 、 flex-basis 三个属性，默认值为 0 1 auto

* 如果写 flex:1 1 auto ，则可简写为： flex:auto
* 如果写 flex:1 1 0 ，则可简写为： flex:1
* 如果写 flex:0 0 auto ，则可简写为： flex:none
* 如果写 flex:0 1 auto ，则可简写为： flex:0 auto —— 即 flex 初始值



------

### 项目排序与单独对齐



order 属性定义项目的排列顺序。数值越小，排列越靠前，默认为 0



通过 align-self 属性，可以单独调整某个伸缩项目的对齐方式

默认值为 auto ，表示继承父元素的 align-items 属性



