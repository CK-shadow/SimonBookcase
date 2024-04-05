---
title: Sass 预处理语言
date: 2024-03-10 20:36:21
tags: 
  - 前端
  - 预处理语言
  - Sass
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Sass%20%E9%A2%84%E5%A4%84%E7%90%86%E8%AF%AD%E8%A8%80/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Sass%20%E9%A2%84%E5%A4%84%E7%90%86%E8%AF%AD%E8%A8%80/preview.jpg
---

# Sass 预处理语言

## 简介

### CSS 预处理语言



CSS 不是一种编程语言，仅仅只能用来编写网站样式，在 web 初期时，网站的搭建还比较基础，所需要的样式往往也很简单。但是随着用户需求的增加以及网站技术的升级，CSS 一成不变的写法也渐渐不再满足于项目。没有类似 js 这样的编程语言所有的变量，常量以及其他的编程语法，CSS 的代码难免会显得臃肿以及难以维护。但是又没有 CSS 的替代品，于是 CSS 预处理器就作为 CSS 的扩展，出现在了前端技术中

CSS 预处理器用一种专门的编程语言，进行 Web 页面样式设计，然后再编译成正常的CSS 文件，以供项目使用。CSS 预处理器为 CSS 增加一些编程的特性，无需考虑浏览器的兼容性问题。 比如说: Sass (SCSS) 、LESS、Stylus、Turbine、Swithch CSS、CSS Cacheer、DT CSS等。都属于 css 预处理器。其中比较优秀的: Sass、LESS，Stylus



------

### Sass



英文单词（Syntactically Awesome Stylesheets）的简写，可翻译为“语法上很棒的样式表”

网址: https://sass-lang.com/

中文网址: https://www.sass.hk/

Sass 是一种动态样式语言，由 Ruby 开发者设计和开发，Sass 语法属于缩排语法

比 CSS 多出好些功能(如变量、嵌套、运算、混入(Mixin)、继承、指令、颜色处理、函数等)，更容易阅读

Sass 的工作方式是，在 Sass 源文件中写代码，然后由 Sass 程序 (Sass 编译器/转译器)将其转换为最终浏览器能认识的 CSS 文件



------

### Less



Less 也是一种动态样式语言，受 Sass 影响较大,对 CSS 赋予了动态语言的特性，如变量，继承，运算， 函数等。Less 既可以在客户端上运行 （支持 IE 6+,Webkit, Firefox）（借助less.js），也可在服务端运行（借助 Nodejs）

网址: https://lesscss.org

中文网址: https://less.bootcss.com/



------

### Stylus

2010 年产生于 Node 社区， 主要用来给 Node 项目进行 CSS 预处理支持，人气不如前两者。Stylus 被称为是一种革命性的新语言，提供一个高效、动态的使用表达方式来生成 CSS，以供浏览器使用。Stylus 同时支持缩进和 CSS 常规样式书写规则。需要安装 node

Stylus 的语法花样多一些，它的文件扩展名是“.styl”，Stylus 也接受标准的CSS 语法，但是他也像 Sass 老的语法规则，使用缩进控制，同时 Stylus 也接受不带大括号和分号的语法

网址: https://stylus-lang.com/

中文网站: https://www.stylus-lang.cn/

中文网: https://www.zhangxinxu.com/ig/stylus/



------

### 安装与使用



Sass 基于 Ruby 语言开发而成，因此安装 Sass 前需要安装 Ruby。mac 下自带 Ruby 所以无需要装，而 Windows 则需要安装 Ruby
或者使用 VS Code 安装 Easy Sass 插件，编写完成后会自动编译成 CSS 文件



------

### 语法格式



Sass 有两种语法格式。首先是 SCSS (Sassy CSS) —— 也是本文示例所使用的格式 —— 这种格式仅在 CSS3 语法的基础上进行拓展，所有 CSS3 语法在 SCSS 中都是通用的，同时加入 Sass 的特色功能。此外，SCSS 也支持大多数 CSS hacks 写法以及浏览器前缀写法 (vendor-specific syntax)，以及早期的 IE 滤镜写法。这种格式以 .scss 作为拓展名

另一种也是最早的 Sass 语法格式，被称为缩进格式 (Indented Sass) 通常简称 "Sass"，是一种简化格式。它使用 “缩进” 代替 “花括号” 表示属性属于某个选择器，用 “换行” 代替 “分号” 分隔属性，很多人认为这样做比 SCSS 更容易阅读，书写也更快速。缩进格式也可以使用 Sass 的全部功能，只是与 SCSS 相比个别地方采取了不同的表达方式。这种格式以 .sass 作为拓展名

任何一种格式可以直接导入 (@import) 到另一种格式中使用，或者通过 sass-convert 命令行工具转换成另一种格式



------

## CSS 功能扩展

### 嵌套规则



Sass 允许将一套 CSS 样式嵌套进另一套样式中，内层的样式将它外层的选择器作为父选择器，例如：

```scss
// SASS
#main p {
  color: #00ff00;
  width: 97%;

  .redbox {
    background-color: #ff0000;
    color: #000000;
  }
}
```

```CSS
/* 编译后的 CSS */
#main p {
  color: #00ff00;
  width: 97%;
}

#main p .redbox {
  background-color: #ff0000;
  color: #000000;
}
```

嵌套功能避免了重复输入父选择器，而且令复杂的 CSS 结构更易于管理



------

###  父选择器 &



在嵌套 CSS 规则时，有时也需要直接使用嵌套外层的父选择器，例如，当给某个元素设定 hover 样式时，或者当 body 元素有某个 classname 时，可以用 & 代表嵌套规则外层的父选择器

```scss
// SASS
a {
  font-weight: bold;
  text-decoration: none;
  &:hover { text-decoration: underline; }
  body.firefox & { font-weight: normal; }
}
```

```CSS
/* 编译后的 CSS */
a {
  font-weight: bold;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

body.firefox a {
  font-weight: normal;
}
```

编译后的 CSS 文件中 & 将被替换成嵌套外层的父选择器，如果含有多层嵌套，最外层的父选择器会一层一层向下传递

& 必须作为选择器的第一个字符，其后可以跟随后缀生成复合的选择器

当父选择器含有不合适的后缀时，Sass 将会报错



------

### 属性嵌套



有些 CSS 属性遵循相同的命名空间 (namespace)，比如 font-family, font-size, font-weight 都以 font 作为属性的命名空间。为了便于管理这样的属性，同时也为了避免了重复输入，Sass 允许将属性嵌套在命名空间中

```scss
// SASS
.funky {
  font: {
    family: fantasy;
    size: 30em;
    weight: bold;
  }
}
```

```CSS
/* 编译后的 CSS */
.funky {
  font-family: fantasy;
  font-size: 30em;
  font-weight: bold;
}
```



命名空间也可以包含自己的属性值

```scss
// SASS
.funky {
  font: 20px/24px {
    family: fantasy;
    weight: bold;
  }
}
```

```CSS
/* 编译后的 CSS */
.funky {
  font: 20px/24px;
  font-family: fantasy;
  font-weight: bold;
}
```



------

### 注释



Sass 支持标准的 CSS 多行注释 /* */，以及单行注释 //，前者会被完整输出到编译后的 CSS 文件中，而后者则不会



------

## SassScript 



在 CSS 属性的基础上 Sass 提供了一些名为 SassScript 的新功能。 SassScript 可作用于任何属性，允许属性使用变量、算数运算等额外功能

通过 interpolation，SassScript 甚至可以生成选择器或属性名，这一点对编写 mixin 有很大帮助



------

###  变量 $



SassScript 最普遍的用法就是变量，变量以美元符号开头，赋值方法与 CSS 属性的写法一样

```SCSS
// SASS
$width: 5em;
#main {
    width: $width;
}
```

```CSS
/* 编译后的 CSS */
#main {
  width: 5em;
}
```



变量支持块级作用域，嵌套规则内定义的变量只能在嵌套规则内使用（局部变量），不在嵌套规则内定义的变量则可在任何地方使用（全局变量）。将局部变量转换为全局变量可以添加 !global 声明

```SCSS
// SASS
#main {
    $width: 5em !global;
    width: $width;
}
  
#sidebar {
    width: $width;
}
```

```CSS
/* 编译后的 CSS */
#main {
  width: 5em;
}

#sidebar {
  width: 5em;
}
```



------

### 数据类型



SassScript 支持 6 种主要的数据类型：

* 数字，1, 2, 13, 10px
* 字符串，有引号字符串与无引号字符串，"foo", 'bar', baz
* 颜色，blue, #04a3f9, rgba(255,0,0,0.5)
* 布尔型，true, false
* 空值，null
* 数组 (list)，用空格或逗号作分隔符，1.5em 1em 0 2em, Helvetica, Arial, sans-serif
* maps, 相当于 JavaScript 的 object，(key1: value1, key2: value2)



------

### 运算



**数字运算**

所有数据类型均支持相等运算 == 或 !=，此外，每种数据类型也有其各自支持的运算方式，关系运算 <, >, <=, >= 也可用于数字运算

```LESS
// SASS
p {
    width: 1in + 8pt;
}
```

```CSS
/* 编译后的 CSS */
p {
  width: 1.11111in;
}
```



**除法运算**

/ 在 CSS 中通常起到分隔数字的用途，SassScript 作为 CSS 语言的拓展当然也支持这个功能，同时也赋予了 / 除法运算的功能。也就是说，如果 / 在 SassScript 中把两个数字分隔，编译后的 CSS 文件中也是同样的作用

以下三种情况 / 将被视为除法运算符号：

* 如果值，或值的一部分，是变量或者函数的返回值
* 如果值被圆括号包裹
* 如果值是算数表达式的一部分

如果需要使用变量，同时又要确保 / 不做除法运算而是完整地编译到 CSS 文件中，只需要用 #{} 插值语句将变量包裹



**颜色值运算**

颜色值的运算是分段计算进行的，也就是分别计算红色，绿色，以及蓝色的值

需要注意的是，如果颜色值包含 alpha channel（rgba 或 hsla 两种颜色值），必须拥有相等的 alpha 值才能进行运算，因为算术运算不会作用于 alpha 值



**字符串运算**

`+` 可用于连接字符串

注意，如果有引号字符串（位于 `+` 左侧）连接无引号字符串，运算结果是有引号的，相反，无引号字符串（位于 `+` 左侧）连接有引号字符串，运算结果则没有引号

在有引号的文本字符串中使用 `#{}` 插值语句可以添加动态的值，空的值被视作插入了空字符串



**布尔运算**

SassScript 支持布尔型的 `and` `or` 以及 `not` 运算



------

**数组运算**

数组不支持任何运算方式，只能使用 [list functions](http://sass-lang.com/docs/yardoc/Sass/Script/Functions.html#list-functions) 控制



------

### 函数



SassScript 定义了多种函数，有些甚至可以通过普通的 CSS 语句调用

```SCSS
// SASS
p {
  color: hsl(0, 100%, 50%);
}
```

通过 [Sass::Script::Functions](http://sass-lang.com/docs/yardoc/Sass/Script/Functions.html) 查看完整的 Sass 函数列表，参数名，以及如何自定义函数



------

### 插值语句 `#{}`



通过 `#{}` 插值语句可以在选择器或属性名中使用变量

```SCSS
// SASS
$name: foo;
$attr: border;
p.#{$name} {
  #{$attr}-color: blue;
}
```

```CSS
/* 编译后的 CSS */
p.foo {
  border-color: blue;
}
```



------

### 变量定义 `!default`



可以在变量的结尾添加 `!default` 给一个未通过 `!default` 声明赋值的变量赋值，此时，如果变量已经被赋值，不会再被重新赋值，但是如果变量还没有被赋值，则会被赋予新的值

变量是 null 空值时将视为未被 `!default` 赋值

```SCSS
// SASS
$content: "First content";
$content: "Second content?" !default;
$new_content: "First time reference" !default;

#main {
  content: $content;
  new-content: $new_content;
}
```

```CSS
/* 编译后的 CSS */
#main {
  content: "First content";
  new-content: "First time reference";
}
```





------

## @-Rules 与指令

### @import



Sass 拓展了 `@import` 的功能，允许其导入 SCSS 或 Sass 文件。被导入的文件将合并编译到同一个 CSS 文件中，另外，被导入的文件中所包含的变量或者混合指令 (mixin) 都可以在导入的文件中使用

Sass 在当前地址，或 Rack, Rails, Merb 的 Sass 文件地址寻找 Sass 文件，如果需要设定其他地址，可以用 `:load_paths` 选项，或者在命令行中输入 `--load-path` 命令

通常，`@import` 寻找 Sass 文件并将其导入，但在以下情况下，`@import` 仅作为普通的 CSS 语句，不会导入任何 Sass 文件

* 文件拓展名是 `.css`
* 文件名以 `http://` 开头
* 文件名是 `url()`
* `@import` 包含 media queries

如果不在上述情况内，文件的拓展名是 `.scss` 或 `.sass`，则导入成功。没有指定拓展名，Sass 将会试着寻找文件名相同，拓展名为 `.scss` 或 `.sass` 的文件并将其导入



Sass 允许同时导入多个文件，例如同时导入 rounded-corners 与 text-shadow 两个文件

```scss
@import "rounded-corners", "text-shadow";
```



如果需要导入 SCSS 或者 Sass 文件，但又不希望将其编译为 CSS，只需要在文件名前添加下划线，这样会告诉 Sass 不要编译这些文件，但导入语句中却不需要添加下划线

例如，将文件命名为 `_colors.scss`，便不会编译 `_colours.css` 文件

```scss
@import "colors";
```

上面的例子，导入的其实是 `_colors.scss` 文件

注意，不可以同时存在添加下划线与未添加下划线的同名文件，添加下划线的文件将会被忽略



大多数情况下，一般在文件的最外层（不在嵌套规则内）使用 `@import`，其实，也可以将 `@import` 嵌套进 CSS 样式或者 `@media` 中，与平时的用法效果相同，只是这样导入的样式只能出现在嵌套的层中

假设 example.scss 文件包含以下样式：

```SCSS
.example {
  color: red;
}
```

然后导入到 `#main` 样式内

```SCSS
#main {
  @import "example";
}
```

将会被编译为

```CSS
#main .example {
  color: red;
}
```



------

### @media



Sass 中 `@media` 指令与 CSS 中用法一样，只是增加了一点额外的功能：允许其在 CSS 规则中嵌套。如果 `@media` 嵌套在 CSS 规则内，编译时，`@media` 将被编译到文件的最外层，包含嵌套的父选择器。这个功能让 `@media` 用起来更方便，不需要重复使用选择器，也不会打乱 CSS 的书写流程

```scss
// SASS
.sidebar {
  width: 300px;
  @media screen and (orientation: landscape) {
    width: 500px;
  }
}
```

```CSS
.sidebar {
  width: 300px;
}

@media screen and (orientation: landscape) {
  .sidebar {
    width: 500px;
  }
}
```





`@media` 的 queries 允许互相嵌套使用，编译时，Sass 自动添加 `and`

```SCSS
// SASS
@media screen {
  .sidebar {
    @media (orientation: landscape) {
      width: 500px;
    }
  }
}
```



`@media` 甚至可以使用 SassScript（比如变量，函数，以及运算符）代替条件的名称或者值

```SCSS
// SASS
$media: screen;
$feature: -webkit-min-device-pixel-ratio;
$value: 1.5;

@media #{$media} and ($feature: $value) {
  .sidebar {
    width: 500px;
  }
}
```



------

### @extend



在设计网页的时候常常遇到这种情况：一个元素使用的样式与另一个元素完全相同，但又添加了额外的样式。通常会在 HTML 中给元素定义两个 class，一个通用样式，一个特殊样式

使用 `@extend` 可以告诉 Sass 将一个选择器下的所有样式继承给另一个选择器



有时，需要定义一套样式并不是给某个元素用，而是只通过 `@extend` 指令使用，尤其是在制作 Sass 样式库的时候，希望 Sass 能够忽略用不到的样式



------

### @at-root



@at-root 指令会将一条或多条规则置于文档根目录下，而不是嵌套在父选择器之下。它可以与单个内联选择器一起使用

```SCSS
// SASS
.parent {
  ...
  @at-root .child { ... }
}
```

```CSS
/* 编译后的 CSS */
.parent { ... }
.child { ... }
```



------

### @debug



@debug 指令会将 SassScript 表达式的值打印到标准错误输出流中。它有助于调试包含复杂 SassScript 的 Sass 文件



------

## 控制指令

### @if



当 `@if` 的表达式返回值不是 `false` 或者 `null` 时，条件成立，输出 `{}` 内的代码

`@if` 声明后面可以跟多个 `@else if` 声明，或者一个 `@else` 声明。如果 `@if` 声明失败，Sass 将逐条执行 `@else if` 声明，如果全部失败，最后执行 `@else` 声明

```SCSS
// SASS
$type: monster;
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
```

```CSS
/* 编译后的 CSS */
p {
  color: green;
}
```



------

### @for



`@for` 指令可以在限制的范围内重复输出格式，每次按要求（变量的值）对输出结果做出变动。这个指令包含两种格式：`@for $var from <start> through <end>`，或者 `@for $var from <start> to <end>`，区别在于 `through` 与 `to` 的含义：*当使用 `through` 时，条件范围包含 `<start>` 与 `<end>` 的值，而使用 `to` 时条件范围只包含 `<start>` 的值不包含 `<end>` 的值*。另外，`$var` 可以是任何变量，比如 `$i`；`<start>` 和 `<end>` 必须是整数值

```SCSS
// SASS
@for $i from 1 through 3 {
  .item-#{$i} { width: 2em * $i; }
}
```

```CSS
/* 编译后的 CSS */
.item-1 {
  width: 2em;
}

.item-2 {
  width: 4em;
}

.item-3 {
  width: 6em;
}
```



------

### @each



`@each` 指令的格式是 `$var in <list>`, `$var` 可以是任何变量名，比如 `$length` 或者 `$name`，而 `<list>` 是一连串的值，也就是值列表

`@each` 将变量 `$var` 作用于值列表中的每一个项目，然后输出结果

```SCSS
// SASS
@each $animal in puma, sea-slug, egret, salamander {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}
```

```CSS
/* 编译后的 CSS */
.puma-icon {
  background-image: url("/images/puma.png");
}

.sea-slug-icon {
  background-image: url("/images/sea-slug.png");
}

.egret-icon {
  background-image: url("/images/egret.png");
}

.salamander-icon {
  background-image: url("/images/salamander.png");
}
```



------

### @while



`@while` 指令重复输出格式直到表达式返回结果为 `false`。这样可以实现比 `@for` 更复杂的循环，只是很少会用到

```SCSS
// SASS
$i: 6;
@while $i > 0 {
  .item-#{$i} { width: 2em * $i; }
  $i: $i - 2;
}
```

```CSS
/* 编译后的 CSS */
.item-6 {
  width: 12em;
}

.item-4 {
  width: 8em;
}

.item-2 {
  width: 4em;
}
```



------

## 混合指令

### 定义混合指令 `@mixin`



混合指令（Mixin）用于定义可重复使用的样式，避免了使用无语意的 class，比如 `.float-left`。混合指令可以包含所有的 CSS 规则，绝大部分 Sass 规则，甚至通过参数功能引入变量，输出多样化的样式

混合指令的用法是在 `@mixin` 后添加名称与样式，比如名为 `large-text` 的混合通过下面的代码定义

```SCSS
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
```



混合也需要包含选择器和属性，甚至可以用 `&` 引用父选择器

```SCSS
@mixin clearfix {
  display: inline-block;
  &:after {
    content: ".";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
  }
  * html & { height: 1px }
}
```



------

### 引用混合样式 `@include`



使用 `@include` 指令引用混合样式，格式是在其后添加混合名称，以及需要的参数（可选）：

```SCSS
// SASS
.page-title {
  @include large-text;
  padding: 4px;
  margin-top: 10px;
}
```

```CSS
/* 编译后的 CSS */
.page-title {
  font-family: Arial;
  font-size: 20px;
  font-weight: bold;
  color: #ff0000;
  padding: 4px;
  margin-top: 10px;
}
```



也可以在最外层引用混合样式，不会直接定义属性，也不可以使用父选择器

```SCSS
// SASS
@mixin silly-links {
  a {
    color: blue;
    background-color: red;
  }
}
@include silly-links;
```

```CSS
/* 编译后的 CSS */
a {
  color: blue;
  background-color: red;
}
```



混合样式中也可以包含其他混合样式

混合样式中应该只定义后代选择器，这样可以安全的导入到文件的任何位置

```SCSS
@mixin compound {
  @include highlighted-background;
  @include header-text;
}
@mixin highlighted-background { background-color: #fc0; }
@mixin header-text { font-size: 20px; }
```



------

### 参数



参数用于给混合指令中的样式设定变量，并且赋值使用。在定义混合指令的时候，按照变量的格式，通过逗号分隔，将参数写进圆括号里。引用指令时，按照参数的顺序，再将所赋的值对应写进括号：

```SCSS
// SASS
@mixin sexy-border($color, $width) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}
p { @include sexy-border(blue, 1in); }
```

```CSS
/* 编译后的 CSS */
p {
  border-color: blue;
  border-width: 1in;
  border-style: dashed;
}
```



混合指令也可以使用给变量赋值的方法给参数设定默认值，然后，当这个指令被引用的时候，如果没有给参数赋值，则自动使用默认值：

```SCSS
// SASS
@mixin sexy-border($color, $width: 1in) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}
p { @include sexy-border(blue); }
h1 { @include sexy-border(blue, 2in); }
```

```CSS
/* 编译后的 CSS */
p {
  border-color: blue;
  border-width: 1in;
  border-style: dashed;
}

h1 {
  border-color: blue;
  border-width: 2in;
  border-style: dashed;
}
```



混合指令也可以使用关键词参数，上面的例子也可以写成：

```scss
p { @include sexy-border($color: blue); }
h1 { @include sexy-border($color: blue, $width: 2in); }
```

虽然不够简明，但是阅读起来会更方便。关键词参数给函数提供了更灵活的接口，以及容易调用的参数。关键词参数可以打乱顺序使用，如果使用默认值也可以省缺，另外，参数名被视为变量名，下划线、短横线可以互换使用



有时，不能确定混合指令需要使用多少个参数，比如一个关于 `box-shadow` 的混合指令不能确定有多少个 'shadow' 会被用到。这时，可以使用参数变量 `…` 声明（写在参数的最后方）告诉 Sass 将这些参数视为值列表处理

```scss
@mixin box-shadow($shadows...) {
  -moz-box-shadow: $shadows;
  -webkit-box-shadow: $shadows;
  box-shadow: $shadows;
}
.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}
```



参数变量也可以用在引用混合指令的时候 (`@include`)，与平时用法一样，将一串值列表中的值逐条作为参数引用：

```scss
@mixin colors($text, $background, $border) {
  color: $text;
  background-color: $background;
  border-color: $border;
}
$values: #ff0000, #00ff00, #0000ff;
.primary {
  @include colors($values...);
}
```



------

##  函数指令



Sass 支持自定义函数，并能在任何属性值或 Sass script 中使用：

```scss
$grid-width: 40px;
$gutter-width: 10px;

@function grid-width($n) {
  @return $n * $grid-width + ($n - 1) * $gutter-width;
}

#sidebar { width: grid-width(5); }
```

与 mixin 相同，也可以传递若干个全局变量给函数作为参数。一个函数可以含有多条语句，需要调用 `@return` 输出结果

自定义函数与 mixin 相同，都支持 variable arguments
