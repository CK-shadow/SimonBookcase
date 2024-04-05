---
title: Less 预处理语言
date: 2024-01-09 14:37:03
tags: 
  - 前端
  - 预处理语言
  - Less
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Less%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Less%20%E6%95%99%E7%A8%8B/preview.jpg
---



# Less 预处理语言

## Less 的安装与使用

### CSS 的短板



作为前端学习者的我们都要学些 CSS，它作为前端开发的三大基石之一，时刻引领着 Web 的发展潮向。而 CSS 作为一门标记性语言，可能给初学者第一印象就是简单易懂，毫无逻辑，不像编程该有的样子。在语法更新时，每当新属性提出，浏览器的兼容又会马上变成绊脚石，可以说 CSS 短板不容忽视

问题的诞生往往伴随着技术的兴起，在 Web 发展的这几年，为了让 CSS 富有逻辑性，短板不那么严重，涌现出了 一些神奇的预处理语言。它们让 CSS 彻底变成一门可以使用变量、循环、继承、自定义方法等多种特性的标记语言，逻辑性得以大大增强



------

### 预处理语言的诞生



其中常用的有三门语言：Sass、Less 、Stylus

1. Sass 诞生于 2007 年，Ruby 编写，其语法功能都十分全面，可以说它完全把 CSS 变成了一门编程语言。另外在国内外都很受欢迎，并且它的项目团队很是强大 ，是一款十分优秀的预处理语言
2. Stylus 诞生于 2010 年，来自 Node.js 社区，语法功能也和 Sass 不相伯仲，是一门十分独特的创新型语言
3. Less 诞生于 2009 年，受 Sass 的影响创建的一个开源项目。它扩充了 CSS 语言，增加了诸如变量、混合(mixin)、函数等功能，让 CSS 更易维护、方便制作主题、扩充



------

### Less 介绍



Less 是一门 CSS 预处理语言，它扩展了 CSS 语言，增加了变量、Mixin、函数等特性，使 CSS 更易维护和扩展

Less 扩充了 CSS 语言,增加了诸如变量、混合(mixin)、运算、函数等功能。 Less 既可以运行在服务器端（Node.js 和 Rhino 平台）也可以运行在浏览器客户端



在 Node.js 环境中使用 Less：

```sh
npm install -g less
```

在浏览器环境中使用 Less：

```html
<link rel="stylesheet/less" type="text/css" href="styles.less”/>
<script src="//cdnjs.cloudflare.com/ajax/1ibs/less.js/3.11.1/less.min.js" ></script>
```

在 VS Code 中使用 Less：安装 Easy Less 插件



------

### Less 的使用



**注释**

Less 的注释有两种：

```less
// 单行注释，编译后不会出现在 CSS 文件上

/* 多行注释，编译后会出现在 CSS 文件上 */
```



**Less 变量**

我们常常在 CSS 中 看到同一个值重复多次，这样难易于代码维护。 理想状态，应是下面这样：

```css
const bgColor="skyblue";
$(".post-content").css("background-color",bgcolor);
$("#wrap").css("background-color",bgcolor);
$(".arctive").css("background-color",bgCglor);
```

只要我们修改 bgcolor 这一个变量，整个页面的背景颜色都会随之改变。而 Less 中的变量十分强大，可化万物，值得一提的是，其变量是常量 ，所以只能定义一次，不能重复使用



**值变量**

```
/* Less*/
@color: #999;
@bgColor: skyblue;//不要添加引号
@width: 50%;
#wrap {
	color: @color
	;background: @bgColor;
	width: @width;
}

/*生成后的 CSS*/
#wrap {
	color: #999;
	background: skyblue;
	width: 50%;
}
```

以 @ 开头定义变量，并且使用 直接键入 @ 名称。在平时作中，我们就可以把常用的变量封装到一个文件中，这样利于代码组织维护



------

## Less 变量

### 选择器变量



可以让选择器变成动态的

```less
/* Less*/
@mySelector: #wrap;
@wrap: wrap;
@{mySelector}{ 
	//变量名 必须使用大括号包裹color: #999;
	width: 50%;
}

.@{wrap}{
	color:#ccc;
}

#@{wrap}{
	color:#666;
}
```

```css
/* 生成后的 CSS */
#wrap {
  width: 50%;
}
.wrap {
  color: #ccc;
}
#wrap {
  color: #666;
}
```



------

### 属性变量



可以减小代码的书写量

```less
/* Less */
@border-style: border-style;
@solid: solid;

#wrap {
    // 变量名，必须使用大括号包裹
    @{border-style}: @solid
}
```

```css
/* 生成的 CSS */
#wrap {
  border-style: solid;
}
```



------

### URL 变量



目录接口改变时，修改其变量即可

```less
/* less */
@images: "../images";

body {
    background: url("@{images}/background.png");
}
```

```css
/* 生成的 CSS */
body {
  background: url("../images/background.png");
}
```



------

### 声明变量



有点类似于下面的混合方法：

* 结构: @name: {属性:值;}
* 使用：@name();

```less
/* less */
@background: {background-color: red;}
#main {
    @background();
}

@Rules: {
    width: 200px;
    height: 200px;
    border: 1px solid green;
}
#con {
    @Rules();
}
```

```css
/* 生成的 CSS */
#main {
  background-color: red;
}
#con {
  width: 200px;
  height: 200px;
  border: 1px solid green;
}

```



------

### 变量运算



Less 的变量运算十分强大

* 加减法时，以第一个数据的单位为基准
* 乘除法时，单位一定要统一

```less
/* less */
@width: 300px;
@color: #222;
#wrap {
    width: @width - 20;
    height: @width - (20 * 5);
    margin: @width * 0.5;
    color: @color * 2;
    background-color: #222 + #111;
}
```

```css
/* 生成的 CSS */
#wrap {
  width: 280px;
  height: 200px;
  margin: 150px;
  color: #444444;
  background-color: #333333;
}
```



------

### 用变量去定义变量



```less
/* less */
@fnord: "I am fnord";
@var: "fnord";

#wrap::after {
    // 将 @var 替换为其值：fnord
    content: @@var;
}
```

```css
/* 生成的 CSS */
#wrap::after {
  content: "I am fnord";
}
```



------

## Less 嵌套

### & 的使用



& 表示上一层选择器的名字，例如此处代表的便是 header

```less
/* less */
#header {
    &:after {
        content: "Less is more";
    }
    .title {
        font-weight: bold;
    }
    &_content {
        margin: 20px;
    }
}
```

```css
/* 生成的 CSS */
#header:after {
  content: "Less is more";
}
#header .title {
  font-weight: bold;
}
#header_content {
  margin: 20px;
}
```



------

### 媒体查询



在以往的工作中，使用媒体查询时需要把一个个元素分开写

```css
#wrap {
  width: 300px;
}

@media screen and (max-width:768px) {
  #wrap {
    width: 100px;
  }
}
```



Less 提供了一个十分便捷的方式

```less
/* less */
#main {
    @media screen {
        @media (max-width:768px) {
            width: 100px;
        }
    }
    @media ty {
        width: 2000px;
    }
}
```

```CSS
/* 生成的 CSS */
@media screen and (max-width: 768px) {
  #main {
    width: 100px;
  }
}
@media ty {
  #main {
    width: 2000px;
  }
}
```



------

## 混合方法

### 无参方法



```less
/* less */
.card { // 等价于 .card()
    background-color: aliceblue;
}

#wrap {
    .card; // 等价于 .card()
}
```

```CSS
/* 生成的 CSS */
.card {
  background-color: aliceblue;
}
#wrap {
  background-color: aliceblue;
}
```



其中 . 和 # 都可以作为方法前缀，() 写不写都可以，但是为了与类名和 id 选择器分开，最好还是写上

而且加了 () 之后编译生成的 CSS 文件内容也不会包含方法具体内容，减少文件大小



------

### 默认参数方法



* Less 可以使用默认参数，如果没有传参数，那么将使用默认参数
* @arguments 犹如 JS 中的 arguments，代指全部参数
* 入参必须带着单位

```less
/* less */
.border(@a:10px, @b:50px, @c:30px, @color:#000) {
    border: solid 1px @color;
    box-shadow: @arguments; // 代指全部参数
}

#main {
    .border(0px, 5px, 30px, red); // 必须带着单位
}

#wrap {
    .border(0px);
}

#content {
    .border(); // 等价于 .border
}
```

```CSS
/* 生成的 CSS */
#main {
  border: solid 1px red;
  box-shadow: 0px 5px 30px red;
}
#wrap {
  border: solid 1px #000;
  box-shadow: 0px 50px 30px #000;
}
#content {
  border: solid 1px #000;
  box-shadow: 10px 50px 30px #000;
}
```



------

### 方法的匹配模式



类似于面向对象中的多态

```LESS
/* Less */
.triangle(top,@width:20px,@color:#000){
    border-color:transparent  transparent @color transparent ;
}
.triangle(right,@width:20px,@color:#000){
    border-color:transparent @color transparent  transparent ;
}

.triangle(bottom,@width:20px,@color:#000){
    border-color:@color transparent  transparent  transparent ;
}
.triangle(left,@width:20px,@color:#000){
    border-color:transparent  transparent  transparent @color;
}
.triangle(@_,@width:20px,@color:#000){
    border-style: solid;
    border-width: @width;
}
#main{
    .triangle(left, 50px, #999)
}
```

```CSS
/* 生成的 CSS */
#main {
  border-color: transparent transparent transparent #999;
  border-style: solid;
  border-width: 50px;
}
```



* 第一个参数 left 要会找到方法中匹配程度最高的，如果匹配程度相同，将全部选择，并存在着样式覆盖替换
* 如果匹配的参数是变量，则将会匹配，如 @_



------

### 方法的命名空间



```Less
/* Less */
#card(){
    background: #723232;
    .d(@w:300px){
        width: @w;

        #a(@h:300px){
            height: @h;//可以使用上一层传进来的方法
        }
    }
}
#wrap{
    #card > .d > #a(100px); // 父元素不能加 括号
}
#main{
    #card .d();
}
#con{
    //不得单独使用命名空间的方法
    //.d() 如果前面没有引入命名空间 #card ，将会报错

    #card; // 等价于 #card();
    .d(20px); //必须先引入 #card
}
```

```CSS
/* 生成的 CSS */
#wrap{
  height:100px;
}
#main{
  width:300px;
}
#con{
  width:20px;
}
```



* 在 CSS 中 > 选择器，选择的是儿子元素，就是必须与父元素有直接血源的元素
* 在引入命令空间时，如使用 > 选择器，父元素不能加括号
* 不得单独使用命名空间的方法，必须先引入命名空间，才能使用其中方法
* 子方法可以使用上一层传进来的方法



------

### 方法的筛选条件



Less 没有 if else，可是它有 when

```Less
/* Less */
#card{

    // and 运算符 ，相当于 与运算 &&，必须条件全部符合才会执行
    .border(@width,@color,@style) when (@width>100px) and(@color=#999){
        border:@style @color @width;
    }

    // not 运算符，相当于 非运算 !，条件为 不符合才会执行
    .background(@color) when not (@color>=#222){
        background:@color;
    }

    // , 逗号分隔符：相当于 或运算 ||，只要有一个符合条件就会执行
    .font(@size:20px) when (@size>50px) , (@size<100px){
        font-size: @size;
    }
}
#main{
    #card>.border(200px,#999,solid);
    #card .background(#111);
    #card > .font(40px);
}
```

```CSS
/* 生成后的 CSS */
#main{
  border:solid #999 200px;
  background:#111;
  font-size:40px;
}
```



* 比较运算有： > >= = =< <
* \= 代表的是等于
* 除去关键字 true 以外的值都被视为 false



------

### 不定参数



如果希望你的方法接受数量不定的参数，可以使用... ，犹如 ES6 的扩展运算符

```Less
/* Less */
.boxShadow(...){
    box-shadow: @arguments;
}
.textShadow(@a,...){
    text-shadow: @arguments;
}
#main{
    .boxShadow(1px,4px,30px,red);
    .textShadow(1px,4px,30px,red);
}
```

```CSS
/* 生成后的 CSS */
#main{
  box-shadow: 1px 4px 30px red;
  text-shadow: 1px 4px 30px red;
}
```



------

### 方法使用 !important



使用方法非常简单，在方法名后加上关键字即可

```Less
/* Less */
.border{
    border: solid 1px red;
    margin: 50px;
}
#main{
    .border() !important;
}
```

```CSS
/* 生成后的 CSS */
#main {
    border: solid 1px red !important;
    margin: 50px !important;
}
```



------

### 循环方法



Less 并没有提供 for 循环功能，而是使用递归去实现。 下面是官网中的一个 Demo，模拟了生成栅格系统

```Less
/* Less */
.generate-columns(4);

.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);
  }
  .generate-columns(@n, (@i + 1));
}
```

```CSS
/* 生成后的 CSS */
.column-1 {
  width: 25%;
}
.column-2 {
  width: 50%;
}
.column-3 {
  width: 75%;
}
.column-4 {
  width: 100%;
}
```



------

### 属性拼接方法



`+_` 代表的是空格；`+` 代表的是逗号



**逗号拼接**

```Less
/* Less */
.boxShadow() {
    box-shadow+: inset 0 0 10px #555;
}
.main {
  .boxShadow();
  box-shadow+: 0 0 20px black;
}
```

```CSS
/* 生成后的 CSS */
.main {
  box-shadow: inset 0 0 10px #555, 0 0 20px black;
}
```



**空格拼接**

```Less
/* Less */
.Animation() {
  transform+_: scale(2);
}
.main {
  .Animation();
  transform+_: rotate(15deg);
}
```

```CSS
/* 生成的 CSS */
.main {
  transform: scale(2) rotate(15deg);
}
```



------

## 继承



extend 是 Less 的一个伪类。它可继承所匹配声明中的全部样式



### extend 关键字的使用



```Less
/* Less */
.animation{
    transition: all .3s ease-out;
    .hide{
      transform:scale(0);
    }
}
#main{
    &:extend(.animation);
}
#con{
    &:extend(.animation .hide);
}
```

```CSS
/* 生成后的 CSS */
.animation,#main{
  transition: all .3s ease-out;
}
.animation .hide , #con{
    transform:scale(0);
}
```



------

### all 全局搜索替换



使用选择器匹配到的全部声明

```less
/* Less */
#main{
  width: 200px;
}
#main {
  &:after {
    content:"Less is good!";
  }
}
#wrap:extend(#main all) {}
```

```CSS
/* 生成的 CSS */
#main,#wrap{
  width: 200px;
}
#main:after, #wrap:after {
    content: "Less is good!";
}
```



------

### 减少代码重复



从表面看来，extend 与方法最大的差别，就是 extend 是同个选择器共用同一个声明，而方法是使用自己的声明，这无疑增加了代码的重复性

方法示例与上面的 extend 进行对比：

```Less
/* Less */
.Method{
  width: 200px;
  &:after {
      content:"Less is good!";
  }
}
#main{
  .Method;
}
#wrap{
  .Method;
}
```

```CSS
/* 生成的 CSS */
#main{
  width: 200px;
  &:after{
    content:"Less is good!";
  }  
}
#wrap{
  width: 200px;
  &:after{
    content:"Less is good!";
  }  
}
```



------

## 函数



Less 具备多种类型的函数，包括判断、数学计算、类型判断、颜色计算等，函数众多，官网可查看全部内容 http://lesscss.cn/functions/#functions-overview
