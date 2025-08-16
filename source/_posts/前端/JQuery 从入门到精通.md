---
title: JQuery 从入门到精通
date: 2025-01-01 00:00:01
tags: 前端
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/JQuery%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/JQuery%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/preview.jpg
---



# JQuery 从入门到精通

## 简介

### 什么是 JQuery



JQuery 是一个快速、小型且功能丰富的 JavaScript 库。它使用户能够方便地遍历 HTML Documents、操作 DOM、处理事件、实现动画效果和提供 Ajax 交互。JQuery 设计的宗旨是“写更少的代码，做更多的事情”，它封装了 JavaScript 常用的功能代码，提供一种简便的 JavaScript 设计模式，优化 HTML 文档操作、事件处理、动画设计和 Ajax 交互



------

### 为什么要使用 JQuery



JQuery 具有以下优势：

* 占用空间少：缩小并压缩后的 JQuery 文件只有 30KB
* 跨浏览器：JQuery 兼容各种主流浏览器，如 IE、Chrome、Firefox、Safari、Opera 等
* 链式操作：JQuery 可以链式操作同一个对象多次操作，如`obj.html().css().text()`
* 完善的 Ajax 支持：JQuery 的 ajax 封装得非常好，不需要考虑复杂浏览器的兼容性和 XMLHttpRequest 对象的创建和使用的问题



------

### 如何引入



要引入 JQuery 库，需要在 HTML 文件中使用`<script>`标签。以下是引入 JQuery 库的几种常见方法：

1. 从 CDN 引入：

   ```HTMl
   <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
   ```

2. 从本地文件引入：

   ```HTML
   <script src="path/to/jquery.min.js"></script>
   ```

   

------

### 不同版本的区别



JQuery 包括 1.x、2.x、3.x 等多个版本。以下简单介绍它们之间在功能、性能、兼容性等方面的主要区别：

1. 兼容性
   * 1.x 版本：主要兼容老版本的浏览器，包括 IE6、IE7 等。如果需要兼容 IE6 及更高版本的浏览器，可以选择使用 1.x 版本
   * 2.x 版本：舍弃了对低版本浏览器的兼容性，主要针对现代浏览器。同时，该版本文件大小较小，提高了性能
   * 3.x 版本：完全不兼容旧版浏览器，只支持最新的现代浏览器
2. 性能与优化
   * 2.x 版本相较于 1.x 版本，主要是去除了对 IE6、7、8 的支持，从而减小了文件体积并提高了性能
   * 3.x 版本对部分方法进行了优化，提高了性能。并且完全移除了一些早期版本中已被弃用的方法和特性
3. 插件与库的兼容性
   * 由于 JQuery 的不同版本之间存在不向后兼容的情况，因此当新版本的 JQuery 发布后，原有的基于 JQuery 的插件可能需要重新开发以保持兼容性。选择 1.x 版本可能更能保证与各种插件的兼容性
4. 特性
   * 1.x 版本是 JQuery 的早期版本，已经具备了对 CSS 选择符、事件处理和 AJAX 交互的稳健支持
   * 2.x 和 3.x 版本在特性上并没有显著增加，主要是对旧特性的移除和优化
5. 官方维护与更新
   * 1.x 版本是使用最广泛的，官网只做 BUG 维护，不再新增功能
   * 2.x 和 3.x 是同时更新迭代的，其中 3.x 是官方主要维护的版本



------

## 使用方法

### 核心函数



JQuery 提供了一个核心函数，即 `$`，这是最常用的函数，也是最基础的部分。这个函数接受一个选择器字符串作为参数，并返回一个 JQuery 对象，这个对象包含了与选择器匹配的所有元素

例如：

```JS
$(selector)
```

其中，`selector` 可以是一个 CSS 选择器字符串，例如 `"#myId"` 或 `".myClass"`，也可以是一个 HTML 元素

JQuery 还有一些其他的核心函数和概念，比如 `.each()` 方法（用于迭代 JQuery 对象中的元素），`.ready()` 方法（用于在 DOM 完全加载后执行代码），以及 `.get()` 方法（用于获取 JQuery 对象中元素的原生 DOM 元素）



------

### 核心对象



执行 jQuery 函数返回的就是 jQuery 对象



------

## JQuery 选择器

### 基本选择器



|             语法              |    名称    |             描述             |      示例      |               示例内容                |
| :---------------------------: | :--------: | :--------------------------: | :------------: | :-----------------------------------: |
|              #id              | id 选择器  |  根据给定的 ID 匹配一个元素  |  $("#myDiv");  |        选择 id 为 myDiv 的元素        |
|            element            | 元素选择器 | 根据给定的元素名匹配所有元素 |   $("div");    |          选择所有的 div 元素          |
|            .class             |  类选择器  |     根据给定的类匹配元素     | $(".myClass"); | 选择所有 class 属性为 myClass 的元素  |
|               *               | 通用选择器 |         匹配所有元素         |    $("*");     |             选择所有元素              |
| selector1,selector2,selectorN | 并集选择器 |    选择符合任一条件的元素    | $('div,span'); |      选择所有的 div 和 span 元素      |
|  selector1selector2selectorN  | 交集选择器 |    选择符合左右条件的元素    | $('div.box');  | 选择所有 class 属性为 box 的 div 元素 |



------

### 层级选择器



|        语法         |      名称      |                  描述                  |       示例        |                        示例内容                        |
| :-----------------: | :------------: | :------------------------------------: | :---------------: | :----------------------------------------------------: |
| ancestor descendant | 后代元素选择器 |  在给定的祖先元素下匹配所有的后代元素  |   $('ul span');   |                 选中 ul 下所有的 span                  |
|   parent > child    |  子元素选择器  |    在给定的父元素下匹配所有的子元素    |  $('ul > span');  |              选中 ul 下所有的子元素 span               |
|     prev + next     |   兄弟选择器   | 匹配所有紧接在 prev 元素后的 next 元素 |  $('.box + li');  |             选中 class 为 box 的下一个 li              |
|   prev ~ siblings   |   兄弟选择器   |  配 prev 元素之后的所有 siblings 元素  | $('ul .box ~ *'); | 选中 ul 下 li 的 class 为 box 的元素后面的所有兄弟元素 |



------

### 过滤选择器



|  分类  |        语法        |                      描述                      |            示例            |                示例内容                |
| :----: | :----------------: | :--------------------------------------------: | :------------------------: | :------------------------------------: |
|  基本  |       :first       |                 获取第一个元素                 |      $('div:first');       |             选择第一个 div             |
|        |       :last        |                获取最后一个元素                |      $('.box:last');       |    选择最后一个 class 为 box 的元素    |
|        |     :eq(index)     |            匹配一个给定索引值的元素            |  $('li:eq(1),li:eq(2)');   |       选择第二个和第三个 li 元素       |
|        |     :gt(index)     |          匹配所有大于给定索引值的元素          |    $('li:gt(0):lt(2)');    |       选择第二个和第三个 li 元素       |
|        |     :lt(index)     |          匹配所有小于给定索引值的元素          |    $('li:lt(3):gt(0)');    |       选择第二个和第三个 li 元素       |
|        |       :even        |   匹配所有索引值为偶数的元素，从 0 开始计数    |                            |                                        |
|        |        :odd        |   匹配所有索引值为奇数的元素，从 0 开始计数    |                            |                                        |
|        |   :not(selector)   |         去除所有与给定选择器匹配的元素         |    $('div:not(.box)');     |   选择所有 class 属性不为 box 的 div   |
|  内容  |  :contains(text)   |             匹配包含给定文本的元素             | $('li:contains("BBBBB")'); |         选择内容为 BBBBB 的 li         |
|        |   :has(selector)   |        匹配含有选择器所匹配的元素的元素        |                            |                                        |
|        |       :empty       |      匹配所有不包含子元素或者文本的空元素      |                            |                                        |
|        |      :parent       |          匹配含有子元素或者文本的元素          |                            |                                        |
| 可见性 |      :hidden       | 匹配所有不可见元素，或者 type 为 hidden 的元素 |      $('li:hidden');       |             选择隐藏的 li              |
|        |      :visible      |               匹配所有的可见元素               |                            |                                        |
|  属性  |    [attribute]     |             匹配包含给定属性的元素             |      $('li[title]');       |      送择有 title 属性的 li 元素       |
|        | [attribute=value]  |        匹配给定的属性是某个特定值的元素        |   $('li[title=hello]');    | 选择所有属性 title 为 hello 的 li 元素 |
|        | [attribute!=value] |       匹配给定的属性是以包含某些值的元素       |                            |                                        |
|        | [attribute*=value] |   复合属性选择器，需要同时满足多个条件时使用   |                            |                                        |



------

### 表单选择器



|   语法    |                      描述                       |
| :-------: | :---------------------------------------------: |
|  :input   | 匹配所有 input, textarea, select 和 button 元素 |
|   :text   |              匹配所有的单行文本框               |
| :password |                 匹配所有密码框                  |
|  :radio   |                匹配所有单选按钮                 |
| :checkbox |                 匹配所有复选框                  |
|  :submit  |                匹配所有提交按钮                 |
|  :reset   |                匹配所有重置按钮                 |
|  :button  |                  匹配所有按钮                   |



------

## JQuery 工具、属性和 CSS

### JQuery 常用工具方法



| 工具方法                  | 描述                                   |
| ------------------------- | -------------------------------------- |
| $.each(object,[callback]) | 通用例遍方法，可用于例遍对象和数组     |
| $.type(obj)               | 检测 obj 的数据类型                    |
| $.isArray(obj)            | 测试对象是否为数组                     |
| $.isFunction(obj)         | 测试对象是否为函数                     |
| $.isNumeric(value)        | 测试对象是否为数字                     |
| $.parseJSON(json)         | 接受一个 JSON 字符串，返回解析后的对象 |



**$.each()**

```JS
// 1、$.each()：遍历数组或对象中的数据
var obj = {
    name: 'Tom',
    setName: function (name) {
        this.name = name;
    }
}
$.each(obj, function (key, value) {
    console.log(key, value); // name Tom   setName ƒ (name) {}
});
```



**$.trim()**

```JS
// 2、$.trim()：去除字符两边的空格
var str = '    ddd ';
console.log($.trim(str)); // ddd
```



**$.type()**

```JS
// 3、$.type(obj)：得到数据的类型
console.log($.type($), $.type($())); // function object
```



**$.isArray()**

```JS
// 4、$.isArray(obj)：判断是否是数组
console.log($.isArray($('body')), $.isArray([])); // false true
```



**$.isFunction()**

```JS
// 5、$.isFunction(obj)：判断是否是函数
console.log($.isFunction($), $.isFunction($())); // true false
```



**$.isNumberic()**

```JS
// 6、$.isNumberic(obj)：判断是否是数字
console.log($.isNumeric('a'), $.isNumeric('2'), $.isNumeric(2)); // false true true
```



**$.parseJSON()**

```JS
// 7、$.parseJSON(json)：解析json字符转换为js对象/数组
var jsonObj = '{"name":"Tom", "age": 18}';
console.log($.parseJSON(jsonObj)); // {name: "Tom", age: 18}
var jsonArr = '[{"name":"Tom", "age": 18}, {"name":"Jack", "age": 28}]';
console.log($.parseJSON(jsonArr)); // (2) [{…}, {…}]
```



------

### 多 Tab 点击切换



**HTML 代码**

```HTML
<ul id="tab">
    <li id="tab1" value="1">10元套餐</li>
    <li id="tab2" value="2">30元套餐</li>
    <li id="tab3" value="3">50元包月</li>
</ul>
<div id="container">
    <div id="content1">
        10元套餐详情：<br> 每月套餐内拨打100分钟，超出部分2毛/分钟
    </div>
    <div id="content2" style="display:none">
        30元套餐详情：<br> 每月套餐内拨打300分钟，超出部分1.5毛/分钟
    </div>
    <div id="content3" style="display:none">
        50元包月详情：<br> 每月无限量随心打
    </div>
</div>
```





**JQuery 代码**

```JS
var $containers = $('#container>div');
var curIndex = 0;
$('#tab>li').click(function () { // 隐式遍历
    // 隐藏上一次
    $containers[curIndex].style.display = 'none';
    // 显示当前的
    curIndex = $(this).index();
    $containers[curIndex].style.display = 'block';
});
```



**实现效果**

![9e8622e2-728b-45e7-b37a-968e59ec50be](C:\Users\A\AppData\Local\Temp\9e8622e2-728b-45e7-b37a-968e59ec50be.gif)



------

### JQuery 操作属性



| 属性          | 描述                                     |
| ------------- | ---------------------------------------- |
| attr()        | 设置或返回被选元素的属性值               |
| removeAttr()  | 从每一个匹配的元素中删除一个属性         |
| prop()        | 获取在匹配的元素集中的第一个元素的属性值 |
| removeProp()  | 用来删除由 .prop() 方法设置的属性集      |
| addClass()    | 为每个匹配的元素添加指定的类名           |
| removeClass() | 从所有匹配的元素中删除全部或者指定的类   |
| toggleClass() | 如果存在（不存在）就删除（添加）一个类   |
| html()        | 取得第一个匹配元素的 html 内容           |
| text()        | 取得所有匹配元素的内容                   |
| val()         | 获得匹配元素的当前值                     |



**attr()、removeAttr()**

```JS
//1.读取第一个div的title属性
console.log($('div:first').attr('title')); // one
//2.给所有的div设置name属性（value 为atguigu）
$('div').attr('name', 'atguigu');
//3.移除所有div的title属性
$('div').removeAttr('title');
//4.给所有的div设置class='guiguClass'
$('div').attr('class', 'guiguClass');
```



**addClass()、removeClass()**

```JS
//5.给所有的div添加class='abc'
$('div').addClass('abc');
//6.移除所有div的guiguClass的class
$('div').removeClass('guiguClass');
```



**html()、val()**

```JS
//7.得到最后一个li的标签体文本
console.log($('ul>li:last').html()); // <span>BBBBB</span>
//8.设置第一个li的标签体为"<h1>mmmmmmmmm</h1>"
$('ul>li:first').html('<h1>mmmmmmmmm</h1>');
//9.得到输入框中的value值
console.log($(':text').val()); // guiguClass
//10.将输入框的值设置为atguigu
$(':text').val('atguigu');
```



**prop()、removeProp()**

```JS
//11.点击’全选’按留实现全选
var $checkbox = $(':checkbox');
$('button:first').click(function () {
    $checkbox.prop('checked', true);
});
//12.点击’全不选’按留实现全不选
$('button:last').click(function () {
    $checkbox.prop("checked", false);
});
```



------

### JQuery 操作 CSS



| CSS           | 描述                                                   |
| ------------- | ------------------------------------------------------ |
| css()         | 访问匹配元素的样式属性                                 |
| offset()      | 获取匹配元素在当前视口的相对偏移                       |
| position()    | 获取匹配元素相对父元素的偏移                           |
| scrollTop()   | 获取匹配元素相对滚动条顶部的偏移                       |
| scrollLeft()  | 获取匹配元素相对滚动条左侧的偏移                       |
| height()      | 取得匹配元素当前计算的高度值（px）                     |
| width()       | 取得第一个匹配元素当前计算的宽度值（px）               |
| innerHeight() | 获取第一个匹配元素内部区域高度（包括补白、不包括边框） |
| innerWidth()  | 获取第一个匹配元素内部区域宽度（包括补白、不包括边框） |
| outerHeight() | 获取第一个匹配元素外部高度（默认包括补白和边框）       |
| outerWidth()  | 获取第一个匹配元素外部宽度（默认包括补白和边框）       |



**css()**

```JS
// 1.得到第一个p标签的颜色
console.log($('p:first').css('color')); // rgb(0, 0, 255);
// 2.设置所有p标签的文本颜色为red
$('p').css('color', 'red');
// 3.设第2个p的字体颜色（#ffee11），背景（blue），宽（300px），高（30px）
$('p:eq(1)').css({
    color: '#ffee11',
    backgroundColor: 'blue',
    width: 300,
    height: 30
});
```



**offset和position**

```JS
// 1.点击btn1
$('#btn1').click(function () {
    // 打印div1相对于页面左上角的位置
    var offset1 = $('.div1').offset();
    console.log(offset1.left, offset1.top); // 10 20
    // 打印div2相对于页面左上角的位置
    var offset2 = $('.div2').offset();
    console.log(offset2.left, offset2.top); // 10 70

    // 打印div1相对于父元素左上角的位置
    var position1 = $('.div1').position();
    console.log(position1.left, position1.top); // 10 20
    // 打印div2相对于父元素左上角的位置
    var position2 = $('.div2').position();
    console.log(position2.left, position2.top); // 0 50
});

// 2.点击btn2
$('#btn2').click(function () {
    // 设置div2相对于页面的左上角的位置
    $('.div2').offset({left: 0, top: 0});
});
```



**scrollTop和scrollLeft**

```JS
// 1.得到div或页面滚动条的坐标
$('#btn1').click(function () {
    console.log($('div').scrollTop()); // 400
    console.log($(document.documentElement).scrollTop() + $(document.body).scrollTop()); // 200
});
// 2.让div或页面的滚动条滚动到指定位置
$('#btn2').click(function () {
    $('div').scrollTop(1000);
    $('html,body').scrollTop(100);
});
```



------

## JQuery 对象的过滤与查找

### 对象的过滤



| 过滤方法   | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| eq()       | 获取第 N 个元素                                              |
| first()    | 获取第一个元素                                               |
| last()     | 获取最后一个元素                                             |
| hasClass() | 检查当前的元素是否含有某个特定的类，如果有，则返回 true      |
| filter()   | 筛选出与指定表达式匹配的元素集合                             |
| not()      | 删除与指定表达式匹配的元素                                   |
| is()       | 根据选择器、DOM 元素或 jQuery 对象来检测匹配元素集合，如果其中至少有一个元素符合这个给定的表达式就返回 true |
| has()      | 保留包含特定后代的元素，去掉那些不含有指定后代的元素         |



**first()**

```JS
var $li = $('ul>li');
// 1.ul下li标签第一个
// $li[0].style.backgroundColor = 'red';
$li.first().css('background-color', 'red');
```



**last()**

```JS
// 2.ul下li标签的最后一个
// $li[$li.length - 1].style.backgroundColor = 'red';
$li.last().css('background-color', 'red');
```



**eq()**

```JS
// 3.ul下li标签的第二个
// $li[1].style.backgroundColor = 'red';
$li.eq(1).css('background-color', 'red');
```



**filter()**

```JS
// 4.ul下li标签中title属性为hello的
$li.filter('[title=hello]').css('background-color', 'red');
```



**not()**

```JS
// 5.ul下li标签中title属性不为hello的
// $li.filter('[title!=hello]').css('background-color', 'red');
$li.not('[title=hello]').css('background-color', 'red');
```



**has()**

```JS
// 6.ul下li标签中有span子标签的
$li.has('span').css('background-color', 'red');
```



**hasClass()、is()**

```JS
// 7.ul下li标签中class属性为box2的
// if ($li.filter('[class=box2]').hasClass('box2')) {
//     $li.filter('[class=box2]').css('background-color', 'red');
// }
if ($li.filter('[class=box2]').is('.box2')) {
    $li.filter('[class=box2]').css('background-color', 'red');
}
```



------

### 对象的查找



| 查找方法       | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| children()     | 取得一个包含匹配的元素集合中每一个元素的所有子元素的元素集合 |
| find()         | 搜索所有与指定表达式匹配的元素。这个函数是找出正在处理的元素的后代元素的好方法 |
| siblings()     | 取得一个包含匹配的元素集合中每一个元素的所有唯一同辈元素的元素集合 |
| next()         | 取得一个包含匹配的元素集合中每一个元素紧邻的后一个同辈元素的元素集合 |
| nextAll()      | 查找当前元素之后所有的同辈元素                               |
| nextUntil()    | 查找当前元素之后所有的同辈元素，直到遇到匹配的那个元素为止   |
| prev()         | 取得一个包含匹配的元素集合中每一个元素紧邻的前一个同辈元素的元素集合 |
| prevAll()      | 查找当前元素之前所有的同辈元素                               |
| prevUntil()    | 查找当前元素之前所有的同辈元素，直到遇到匹配的那个元素为止   |
| offsetParent() | 返回第一个匹配元素用于定位的父节点                           |
| parentsUntil() | 查找当前元素的所有的父辈元素，直到遇到匹配的那个元素为止     |



**children()**

```JS
var $ul = $('ul');
// 1.ul标签的第2个span子标签
$ul.children('span:eq(1)').css('background-color', 'red');
```



**find()**

```JS
// 2.ul标签的第2个span后代标签
$ul.find('span:eq(1)').css('background-color', 'red');
```



**parent()、offsetParent()**

```JS
// 3.ul标签的父标签
$ul.parent().css('background-color', 'red');
```



**prev()、prevAll()、next()、nextAll()**

```JS
// 4.id为cc的li标签的前一个li标签
$('#cc').prev('li').css('background-color', 'red');
```



**siblings()**

```JS
// 6.id为cc的li标签的所有兄弟li标签
$('#cc').siblings('li').css('background-color', 'red');
```



------

## JQuery 文档、事件、动画

### 文档处理



|          | 文档处理方法   | 描述                                                         |
| -------- | -------------- | ------------------------------------------------------------ |
| 内部插入 | append()       | 向每个匹配的元素内部追加内容                                 |
|          | appendTo()     | 把所有匹配的元素追加到另一个指定的元素元素集合中             |
|          | prepend()      | 向每个匹配的元素内部前置内容                                 |
|          | prependTo()    | 把所有匹配的元素前置到另一个、指定的元素元素集合中           |
| 外部插入 | after()        | 在每个匹配的元素之后插入内容                                 |
|          | before()       | 在每个匹配的元素之前插入内容                                 |
|          | insertAfter()  | 把所有匹配的元素插入到另一个、指定的元素元素集合的后面       |
|          | insertBefore() | 把所有匹配的元素插入到另一个、指定的元素元素集合的前面       |
| 包裹     | wrap()         | 把所有匹配的元素用其他元素的结构化标记包裹起来               |
|          | unwrap()       | 这个方法将移出元素的父元素                                   |
|          | wrapAll()      | 将所有匹配的元素用单个元素包裹起来                           |
|          | wrapInner()    | 将每一个匹配的元素的子内容(包括文本节点)用一个 HTML 结构包裹起来 |
| 替换     | replaceWith()  | 将所有匹配的元素替换成指定的 HTML 或 DOM 元素                |
|          | replaceAll()   | 用匹配的元素替换掉所有 selector 匹配到的元素                 |
| 删除     | empty()        | 删除匹配的元素集合中所有的子节点                             |
|          | remove()       | 从 DOM 中删除所有匹配的元素                                  |
|          | detach()       | 从 DOM 中删除所有匹配的元素                                  |
| 克隆     | clone()        | 克隆匹配的 DOM 元素并且选中这些克隆的副本                    |



**append、appendTo**

```JS
// 1.向id为ul1的ul下添加一个span（最后）
// $('#ul1').append('<span>append添加的span</span>');
$('<span>appendTo添加的span</span>').appendTo($('#ul1'));
```



**prepend、prependTo**

```JS
// 2.向id为ul1的ul下添加一个span（最前）
// $('#ul1').prepend('<span>prepend添加的span</span>');
$('<span>prependTo添加的span</span>').prependTo($('#ul1'));
```



**before、insertBefore**

```JS
// 3.在id为ul1的ul下的li（title为hello）的前面添加span
// $('#ul1').children('li[title=hello]').before('<span>before添加的span</span>');
$('<span>insertBefore添加的span</span>').insertBefore($('#ul1').children('li[title=hello]'));
```



**after、insertAfter**

```JS
// 4.在id为ul1的ul下的li（title为hello）的后面添加span
// $('#ul1').children('li[title=hello]').after('<span>after添加的span</span>');
$('<span>insertAfter添加的span</span>').insertAfter($('#ul1').children('li[title=hello]'));
```



**replaceWith、replaceAll**

```JS
// 5.将id为ul2的ul下的li（title为hello）全部替换为p
// $('#ul2').children('li[title=hello]').replaceWith('<p>replaceWith替换的p</p>');
$('<p>replaceWith替换的p</p>').replaceAll($('#ul2').children('li[title=hello]'));
```



**empty、remove、detach**

```JS
// 6.移除id为ul2的ul下的所有li
$('#ul2').children('li').empty(); // ul2下li的内容被清空
```



------

### 事件处理



|          | 事件方法           | 描述                                                         |
| -------- | ------------------ | ------------------------------------------------------------ |
| 页面载入 | ready()            | 当 DOM 载入就绪可以查询及操纵时绑定一个要执行的函数          |
| 事件处理 | on()               | 在选择元素上绑定一个或多个事件的事件处理函数                 |
|          | off()              | 在选择元素上移除一个或多个事件的事件处理函数                 |
|          | bind()             | 为每个匹配元素的特定事件绑定事件处理函数                     |
|          | unbind()           | bind() 的反向操作，从每一个匹配的元素中删除绑定的事件        |
|          | one()              | 为每一个匹配元素的特定事件（像 click）绑定一个一次性的事件处理函数 |
|          | trigger()          | 在每一个匹配的元素上触发某类事件                             |
|          | triggerHandler()   | 这个特别的方法将会触发指定的事件类型上所有绑定的处理函数。但不会执行浏览器默认动作，也不会产生事件冒泡 |
| 事件委派 | delegate()         | 指定的元素（属于被选元素的子元素）添加一个或多个事件处理程序，并规定当这些事件发生时运行的函数 |
|          | undelegate()       | 删除由 delegate() 方法添加的一个或多个事件处理程序           |
| 事件切换 | hover()            | 一个模仿悬停事件（鼠标移动到一个对象上面及移出这个对象）的方法 |
|          | toggle()           | 用于绑定两个或多个事件处理器函数，以响应被选元素的轮流的 click 事件 |
| 事件     | focus()、focusin() | 当元素获得焦点时，触发 focus、focusin 事件                   |
|          | blur()、focusout() | 当元素失去焦点时，触发 blur、focusout 事件                   |
|          | change()           | 当元素的值发生改变时，会发生 change 事件                     |
|          | click()            | 触发每一个匹配元素的 click 事件                              |
|          | dblclick()         | 当双击元素时，会发生 dblclick 事件                           |
|          | error()            | 当元素遇到错误（没有正确载入）时，发生 error 事件            |
|          | mousedown()        | 当鼠标指针移动到元素上方，并按下鼠标按键时，会发生 mousedown 事件 |
|          | mouseup()          | 当在元素上放松鼠标按钮时，会发生 mouseup 事件                |
|          | mouseenter()       | 当鼠标指针穿过元素时，会发生 mouseenter 事件                 |
|          | mouseleave()       | 当鼠标指针离开元素时，会发生 mouseleave 事件                 |
|          | mouseover()        | 当鼠标指针位于元素上方时，会发生 mouseover 事件              |
|          | mouseout()         | 当鼠标指针从元素上移开时，发生 mouseout 事件                 |
|          | mousemove()        | 当鼠标指针在指定的元素中移动时，就会发生 mousemove 事件      |
|          | keypress()         | 当键盘或按钮被按下时，发生 keypress 事件                     |
|          | keydown()          | 当键盘或按钮被按下时，发生 keydown 事件                      |
|          | keyup()            | 当按钮被松开时，发生 keyup 事件                              |
|          | resize()           | 当调整浏览器窗口的大小时，发生 resize 事件                   |
|          | scroll()           | 当用户滚动指定的元素时，会发生 scroll 事件                   |
|          | select()           | 当 textarea 或文本类型的 input 元素中的文本被选择时，会发生 select 事件 |
|          | submit()           | 当提交表单时，会发生 submit 事件                             |
|          | unload()           | 在当用户离开页面时，会发生 unload 事件                       |



**click、on、bind**

```JS
// 1.给.out绑定点击监听（用两种方法绑定）
// $('.outer').click(function () {
//   alert('click outer');
// });
// $('.outer').on('click', function () {
//     alert('click outer');
// });
$('.outer').bind('click', function () {
    alert('click outer');
});
```



**mouseenter、mouseleave、hover**

```JS
// 2.给.inner绑定鼠标移入和移出的事件监听
// $('.inner')
//     .mouseenter(function () {
//         alert('mouse enter');
//     })
//     .mouseleave(function () {
//         alert('mouse leave');
//     });
$('.inner').hover(
    function () {
        alert('mouse enter');
    }, function () {
        alert('mouse leave');
    });
```



**mouseover、mouseout**

```JS
$('.outer')
    .bind('mouseover', function () {
    console.log('mouse over');
})
    .bind('mouseout', function () {
    console.log('mouse out');
});
```



**off、unbind**

```JS
// 3.点击btn1解除.inner上的所有事件监听
$('#btn1').on('click', function () {
    // $('.inner').off();
    $('.inner').unbind();
});

// 4.点击btn2解除.inner上的mouseenter事件
$('#btn2').on('click', function () {
    // $('.inner').off('mouseenter');
    $('.inner').unbind('mouseenter');
});
```



**stopPropagation、preventDefault**

```JS
// 6.点击.inner区域，外部点击监听不响应
$('.inner').on('click', function (event) {
    alert('click inner');
    event.stopPropagation(); // 停止冒泡
});

// 7.点击链接，如果当前时间是偶数不跳转
$('#test4').on('click', function (event) {
    if (Date.now() % 2 === 0) {
        event.preventDefault(); // 阻止默认行为
    }
})
```



------

### 事件委托（委派/代理）



**事件委托**

将多个子元素的事件监听委托给父辈元素处理，监听回调是加在了父辈元素上

当操作任何一个子元素时，事件会冒泡到父辈元素

父辈元素不会直接处理事件，而是根据`event.target`得到发生事件的子元素，通过这个子元素调用回调函数



**事件委托的好处**

* 添加新的子元素，自动有事件响应处理
* 减少事件监听的数量：n==>1



**事件委托API**

* 设置事件委托：`$(parentSelector).delegate(childrenSelector, eventName, callback)`
* 移除事件委托：`$(parentSelector).undelegate(eventName)`



![1f6994cf-e9d7-446c-9452-051a981ff8e9](C:\Users\A\Desktop\1f6994cf-e9d7-446c-9452-051a981ff8e9.gif)

```JS
// 点击li背景就会变为红色
$('ul').delegate('li', 'click', function () {
    this.style.backgroundColor = 'red';
});

// 点击btn1就添加一个li
$('#btn1').on('click', function () {
    $('ul').append('<li>新增的li...</li>');
});

$('#btn2').on('click', function () {
    $('ul').undelegate('click');
});
```





------

### 动画



|          | 动画          | 描述                                                         |
| -------- | ------------- | ------------------------------------------------------------ |
| 基本     | show()        | 显示隐藏的匹配元素                                           |
|          | hide()        | 隐藏显示的元素                                               |
|          | toggle()      | 用于绑定两个或多个事件处理器函数，以响应被选元素的轮流的 click 事件 |
| 滑动     | slideDown()   | 通过高度变化（向下增大）来动态地显示所有匹配的元素，在显示完成后可选地触发一个回调函数 |
|          | slideUp()     | 通过高度变化（向上减小）来动态地隐藏所有匹配的元素，在隐藏完成后可选地触发一个回调函数 |
|          | slideToggle() | 通过高度变化来切换所有匹配元素的可见性，并在切换完成后可选地触发一个回调函数 |
| 淡入淡出 | fadeIn()      | 通过不透明度的变化来实现所有匹配元素的淡入效果，并在动画完成后可选地触发一个回调函数 |
|          | fadeOut()     | 通过不透明度的变化来实现所有匹配元素的淡出效果，并在动画完成后可选地触发一个回调函数 |
|          | fadeTo()      | 把所有匹配元素的不透明度以渐进方式调整到指定的不透明度，并在动画完成后可选地触发一个回调函数 |
|          | fadeToggle()  | 通过不透明度的变化来开关所有匹配元素的淡入和淡出效果，并在动画完成后可选地触发一个回调函数 |
| 自定义   | animate()     | 用于创建自定义动画的函数                                     |
|          | stop()        | 停止所有在指定元素上正在运行的动画                           |
|          | finish()      | 停止当前正在运行的动画，删除所有排队的动画，并完成匹配元素所有的动画 |
|          | delay()       | 设置一个延时来推迟执行队列中之后的项目                       |



**fadeIn、fadeOut、fadeToggle**

```JS
var $div1 = $('.div1');
// 1.点击btn1，缓慢淡出
//  *无参
// $('#btn1').click(function () {
//     $div1.fadeOut();
// });
//  *有参
//    *字符参数
//    *数字参数
// $('#btn1').click(function () {
//     $div1.fadeOut('slow');
// });
$('#btn1').click(function () {
    $div1.fadeOut(1000);
});
// 2.点击btn2，缓慢淡入
$('#btn2').click(function () {
    $div1.fadeIn('slow');
});
// 3.点击btn3，淡出/淡入切换，动画结束时提示“动画结束”
$('#btn3').click(function () {
    $div1.fadeToggle('slow', 'linear', function () {
        alert('动画结束');
    });
});
```



**slideUp、slideDown、slideToggle**

```JS
var $div1 = $('.div1');
// 1.点击btn1，向上滑动
$('#btn1').click(function () {
    $div1.slideUp(1000);
});
// 2.点击btn3，向下滑动
$('#btn2').click(function () {
    $div1.slideDown('slow');
});
// 3.点btn3，向上/向下切换
$('#btn3').click(function () {
    $div1.slideToggle('slow', 'linear', function () {
        alert('动画结束');
    });
});
```



**show、hide、toggle**

```JS
var $div1 = $('.div1');
// 1.点击btn1，立即显示
$('#btn1').click(function () {
    $div1.show();
});
// 2.点击btn2，慢慢显示
$('#btn2').click(function () {
    $div1.show('slow');
});
// 3.点击btn3，慢慢隐藏
$('#btn3').click(function () {
    $div1.hide('slow');
});
// 4.点击btn4，切换显示/隐藏
$('#btn4').click(function () {
    $div1.toggle();
});
```



------

### 多库共存



问题：如果有 2 个库都有`$`，就存在冲突

解决：`jQuery`库可以释放`$`的使用权，让另一个库可以正常使用，此时`jQuery`库只能使用`jQuery`了

API：`jQuery.noconflict()`



```JS
//释放的使用衣
jQuery.noConflict();
//调用myLib中的s
$(); 
//要想使用jQuery的功能，只能使用jQuery
jQuery(function () {
    console.log('文档加载完成');
});
console.log('+++++');
```



------

### onload 与 ready



区别：`window.onload`与`$(document).ready()`

onload

* 包括页面的图片加载完后才会回调（晚）
* 只能有 一个监听回调

ready

* 等同于：`$(function(){})`
* 页面加载完就回调（早）
* 可以有 多个监听回调



```JS
// 1.直接打印img的宽度,观察其值
console.log('直接', $('#logo').width()); // 直接 0

// 2.在$(function(){})中打印img的宽度
$(function () {
    console.log('ready', $('#logo').width()); // ready 0
});

// 3.在window.onload中打印img的宽度
window.onload = function () {
    console.log('onload', $('#logo').width()); // onload 190
};

// 4.在img加载完成后打印宽度
$('#logo').on('load', function () {
    console.log('img load', $('#logo').width()); // img load 190
});
```

