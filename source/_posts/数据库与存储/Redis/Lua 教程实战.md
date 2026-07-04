---
title: Lua 教程实战
date: 2024-10-30 20:02:16
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/Redis/Lua%20%E6%95%99%E7%A8%8B%E5%AE%9E%E6%88%98/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/Redis/Lua%20%E6%95%99%E7%A8%8B%E5%AE%9E%E6%88%98/preview.jpg
tags:
  - Redis
  - Lua
categories: 数据库与存储
---



# 简介



Lua 是一个由 C 语言编写的小巧的脚本语言，其设计目的是为了通过灵活嵌入应用程序中从而为应用程序提供灵活的扩展和控制功能。在所有的脚本引擎中，Lua 的速度是最快的，Lua 是作为嵌入式脚本的最佳选择

<br>

应用场景有：

1. 游戏开发
2. 独立应用脚本
3. Web 应用脚本
4. 扩展和数据库插件如 MySQL Proxy 和 MySQL WorkBench
5. 安全系统，如入侵检测系统
6. Nginx + Lua 开发高性能 Web 应用，限流、防 SQL 注入、请求过滤、黑白名单限制等

<br>

它的特性有：

* 轻量级: 它用标准 C 语言编写并以源代码形式开放，编译后仅仅一百余 K，可以很方便的嵌入别的程序里
* 可扩展: Lua 提供了非常易于使用的扩展接口和机制：由宿主语言 (通常是 C 或 C++) 提供这些功能，Lua 可以使用它们，就像是本来就内置的功能一样
* 支持面向过程编程和函数式编程
* 自动内存管理；只提供了一种通用类型的表（table），用它可以实现数组，哈希表，集合，对象
* 语言内置模式匹配；闭包；函数也可以看做一个值；提供多线程（协同进程，并非操作系统所支持的线程）支持
* 通过闭包和 table 可以很方便地支持面向对象编程所需要的一些关键机制，比如数据抽象，虚函数，继承和重载等



------

# 环境安装



**Linux 安装**

```SH
curl -L -R -O https://www.lua.org/ftp/lua-5.4.7.tar.gz
tar zxf lua-5.4.7.tar.gz
cd lua-5.4.7
make all test
make install

或者使用安装工具

sudo yum install epel-release
sudo yum install lua
```

<br>

**Mac 安装**

```
curl -L -R -O https://www.lua.org/ftp/lua-5.4.7.tar.gz
tar zxf lua-5.4.7.tar.gz
cd lua-5.4.7
make all test
make install

或者使用安装工具

brew install lua
```

<br>

**Windows 安装**

window 下可以使用一个叫 "SciTE" 的 IDE 环 境来执行 lua 程序，下载地址为：

Github 下载地址：https://github.com/rjpcomputing/luaforwindows/releases

Google Code下载地址 : https://code.google.com/p/luaforwindows/downloads/list



------

# 基本语法



**注释**

单行注释以两个连字符 `--` 开头，后面跟随的内容将被解释器忽略

```LUA
-- 单行注释
```

多行注释以 `--[[` 开始，并以 `]]` 结束，适用于需要对大段代码进行说明的情况

```LUA
--[[
 多行注释
 多行注释
 --]]
```

Lua 不支持嵌套注释，这意味着在一个多行注释内不能再嵌套另一个多行注释

<br>

**标识符**

Lua 标识符用于定义一个变量，函数获取其他用户定义的项。标识符以一个字母 A 到 Z 或 a 到 z 或下划线 **_** 开头后加上 0 个或多个字母，下划线，数字（0 到 9）

最好不要使用下划线加大写字母的标识符，因为 Lua 的保留字也是这样的

Lua 不允许使用特殊字符如 @, $, 和 % 来定义标识符。 Lua 是一个区分大小写的编程语言。因此在 Lua 中 Runoob 与 runoob 是两个不同的标识符

一般约定，以下划线开头连接一串大写字母的名字（比如 _VERSION）被保留用于 Lua 内部全局变量

<br>

**全局变量**

在默认情况下，变量总是认为是全局的

全局变量不需要声明，给一个变量赋值后即创建了这个全局变量，访问一个没有初始化的全局变量也不会出错，只不过得到的结果是：nil

```SH
> print(b)
nil
> b=10
> print(b)
10
>
```

如果想删除一个全局变量，只需要将变量赋值为 nil

```LUA
b = nil
print(b)      --> nil
```

这样变量 b 就好像从没被使用过一样。换句话说, 当且仅当一个变量不等于 nil 时，这个变量才存在



------

# 数据类型



Lua 是动态类型语言，变量不要类型定义,只需要为变量赋值。 值可以存储在变量中，作为参数传递或结果返回

Lua 中有 8 个基本类型分别为：nil、boolean、number、string、userdata、function、thread 和 table

| 数据类型 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| nil      | 这个最简单，只有值 nil 属于该类，表示一个无效值（在条件表达式中相当于 false） |
| boolean  | 包含两个值：false 和 true                                    |
| number   | 表示双精度类型的实浮点数                                     |
| string   | 字符串由一对双引号或单引号来表示                             |
| userdata | 表示任意存储在变量中的 C 数据结构                            |
| function | 由 C 或 Lua 编写的函数                                       |
| thread   | 表示执行的独立线路，用于执行协同程序                         |
| table    | Lua 中的表（table）其实是一个关联数组，数组的索引可以是数字、字符串或表类型。在 Lua 里，table 的创建是通过 "构造表达式" 来完成，最简单构造表达式是 {}，用来创建一个空表 |

<br>

* nil

  nil 类型表示一种没有任何有效值，它只有一个值 -- nil

  对于全局变量和 table，nil 还有一个删除作用，给全局变量或者 table 表里的变量赋一个 nil 值，等同于把它们删掉

  ```Lua
  tab1 = { key1 = "val1", key2 = "val2", "val3" }
  for k, v in pairs(tab1) do
      print(k .. " - " .. v)
  end
   
  tab1.key1 = nil
  for k, v in pairs(tab1) do
      print(k .. " - " .. v)
  end
  ```

* boolean

  boolean 类型只有两个可选值：true 和 false，Lua 把 false 和 nil 看作是 false，其他的都为 true，数字 0 也是 true

* number

  Lua 默认只有一种 number 类型 -- double 类型

* string

  字符串由一对双引号或单引号来表示，也可以用 2 个方括号 "[[]]" 来表示"一块"字符串

  ```LUA
  string1 = "this is string1"
  string2 = 'this is string2'
  
  html = [[
  <html>
  <head></head>
  <body>
      <a href="http://www.runoob.com/">菜鸟教程</a>
  </body>
  </html>
  ]]
  ```

  在对一个数字字符串上进行算术操作时，Lua 会尝试将这个数字字符串转成一个数字：

  ```
  > print("2" + 6)
  8.0
  > print("2" + "6")
  8.0
  > print("2 + 6")
  2 + 6
  > print("-2e2" * "6")
  -1200.0
  > print("error" + 1)
  stdin:1: attempt to perform arithmetic on a string value
  stack traceback:
          stdin:1: in main chunk
          [C]: in ?
  ```

  字符串连接使用的是 .. ，使用 # 来计算字符串的长度，放在字符串前面

  ```
  > print("a" .. 'b')
  ab
  > print(157 .. 428)
  157428
  
  > len = "www.runoob.com"
  > print(#len)
  14
  > print(#"www.runoob.com")
  14
  ```

* table

  在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。也可以在表里添加一些数据，直接初始化表:

  ```LUA
  -- 创建一个空的 table
  local tbl1 = {}
   
  -- 直接初始表
  local tbl2 = {"apple", "pear", "orange", "grape"}
  ```

  不同于其他语言的数组把 0 作为数组的初始索引，在 Lua 里表的默认初始索引一般以 1 开始

  ```LUA
  -- table_test2.lua 脚本文件
  local tbl = {"apple", "pear", "orange", "grape"}
  for key, val in pairs(tbl) do
      print("Key", key)
  end
  ```

  ```
  Key    1
  Key    2
  Key    3
  Key    4
  ```

  table 不会固定长度大小，有新数据添加时 table 长度会自动增长，没初始的 table 都是 nil

* function

  在 Lua 中，函数是被看作是第一类值，函数可以存在变量里:

  ```lua
  -- function_test.lua 脚本文件
  function factorial1(n)
      if n == 0 then
          return 1
      else
          return n * factorial1(n - 1)
      end
  end
  print(factorial1(5))
  factorial2 = factorial1
  print(factorial2(5))
  ```

  function 可以以匿名函数的方式通过参数传递:

  ```LUA
  -- function_test2.lua 脚本文件
  function testFun(tab,fun)
          for k ,v in pairs(tab) do
                  print(fun(k,v));
          end
  end
  
  
  tab={key1="val1",key2="val2"};
  testFun(tab,
  function(key,val)--匿名函数
          return key.."="..val;
  end
  );
  ```

* thread

  在 Lua 里，最主要的线程是协同程序（coroutine）。它跟线程（thread）差不多，拥有自己独立的栈、局部变量和指令指针，可以跟其他协同程序共享全局变量和其他大部分东西

  线程跟协程的区别：线程可以同时多个运行，而协程任意时刻只能运行一个，并且处于运行状态的协程只有被挂起（suspend）时才会暂停

* userdata

  userdata 是一种用户自定义数据，用于表示一种由应用程序或 C/C++ 语言库所创建的类型，可以将任意 C/C++ 的任意数据类型的数据（通常是 struct 和 指针）存储到 Lua 变量中调用



------

# 变量



变量在使用前，需要在代码中进行声明，即创建该变量

编译程序执行代码之前编译器需要知道如何给语句变量开辟存储区，用于存储变量的值

Lua 变量有三种类型：全局变量、局部变量、表中的域

Lua 中的变量全是全局变量，哪怕是语句块或是函数里，除非用 local 显式声明为局部变量

局部变量的作用域为从声明位置开始到所在语句块结束

变量的默认值均为 nil

<br>

**赋值语句**

赋值是改变一个变量的值和改变表域的最基本的方法

```LUA
a = "hello" .. "world"
t.n = t.n + 1
```

Lua 可以对多个变量同时赋值，变量列表和值列表的各个元素用逗号分开，赋值语句右边的值会依次赋给左边的变量

```LUA
a, b = 10, 2*x       <-->       a=10; b=2*x
```

遇到赋值语句 Lua 会先计算右边所有的值然后再执行赋值操作，所以我们可以这样进行交换变量的值：

```LUA
x, y = y, x                     -- swap 'x' for 'y'
a[i], a[j] = a[j], a[i]         -- swap 'a[i]' for 'a[j]'
```

当变量个数和值的个数不一致时，Lua 会一直以变量个数为基础采取以下策略：

```
a. 变量个数 > 值的个数             按变量个数补足nil
b. 变量个数 < 值的个数             多余的值会被忽略
```

多值赋值经常用来交换变量，或将函数调用返回给变量：

```LUA
a, b = f()
```

f() 返回两个值，第一个赋给 a，第二个赋给 b

应该尽可能的使用局部变量，这样既可以避免命名冲突，而且访问局部变量的速度比全局变量更快

<br>

**索引**

对 table 的索引使用方括号 []。Lua 也提供了 . 操作

```LUA
t[i]
t.i                 -- 当索引为字符串类型时的一种简化写法
gettable_event(t,i) -- 采用索引访问本质上是一个类似这样的函数调用
```



------

# 循环



Lua 语言提供了以下几种循环处理方式：

* while 循环

  在条件为 true 时，让程序重复地执行某些语句。执行语句前会先检查条件是否为 true

  ```LUA
  while(condition)
  do
     statements
  end
  ```

* for 循环

  重复执行指定语句，重复次数可在 for 语句中控制

  ```LUA
  for var=exp1,exp2,exp3 do  
      <执行体>  
  end  
  ```

  泛型 for 循环通过一个迭代器函数来遍历所有值，类似 java 中的 foreach 语句

  ```LUA
  --打印数组a的所有值  
  a = {"one", "two", "three"}
  for i, v in ipairs(a) do
      print(i, v)
  end 
  ```

* repeat ... until 循环

  Lua 编程语言中 repeat...until 循环语句不同于 for 和 while循环，for 和 while 循环的条件语句在当前循环执行开始时判断，而 repeat...until 循环的条件语句在当前循环结束后判断

  ```LUA
  repeat
     statements
  until( condition )
  ```



------

# 流程控制



Lua 提供了以下控制结构语句：

* if 语句

  ```LUA
  if(布尔表达式)
  then
     --[ 在布尔表达式为 true 时执行的语句 --]
  end
  ```

* if ... else 语句

  ```LUA
  if(布尔表达式)
  then
     --[ 布尔表达式为 true 时执行该语句块 --]
  else
     --[ 布尔表达式为 false 时执行该语句块 --]
  end
  
  ```

* if else if 语句

  ```LUA
  if (布尔表达式) then
      -- 当 (布尔表达式) 为 true 时执行
  elseif (布尔表达式) then
      -- 当 condition1 为 false 但 condition2 为 true 时执行
  else
      -- 当上面所有条件都为 false 时执行
  end
  ```

  

------

# 运算符



**算术运算符**

设定 A 的值为10，B 的值为 20：

| 操作符 | 描述                     | 实例               |
| ------ | ------------------------ | ------------------ |
| +      | 加法                     | A + B 输出结果 30  |
| -      | 减法                     | A - B 输出结果 -10 |
| *      | 乘法                     | A * B 输出结果 200 |
| /      | 除法                     | B / A 输出结果 2   |
| %      | 取余                     | B % A 输出结果 0   |
| ^      | 乘幂                     | A^2 输出结果 100   |
| -      | 负数                     | -A 输出结果 -10    |
| //     | 整除运算符（>= lua 5.3） | 5//2 输出结果 2    |

<br>

**关系运算符**

设定 A 的值为10，B 的值为 20：

| 操作符 | 描述                                                         | 实例                |
| ------ | ------------------------------------------------------------ | ------------------- |
| ==     | 等于，检测两个值是否相等，相等返回 true，否则返回 false      | (A == B) 为 false   |
| ~=     | 不等于，检测两个值是否相等，不相等返回 true，否则返回 false  | (A ~= B) 为 true    |
| >      | 大于，如果左边的值大于右边的值，返回 true，否则返回 false    | (A > B) 为 false    |
| <      | 小于，如果左边的值大于右边的值，返回 false，否则返回 true    | (A < B) 为 true     |
| >=     | 大于等于，如果左边的值大于等于右边的值，返回 true，否则返回 false | (A >= B) 返回 false |
| <=     | 小于等于， 如果左边的值小于等于右边的值，返回 true，否则返回 false | (A <= B) 返回 true  |

<br>

**逻辑运算符**

设定 A 的值为 true，B 的值为 false：

| 操作符 | 描述                                                         | 实例                 |
| ------ | ------------------------------------------------------------ | -------------------- |
| and    | 逻辑与操作符。 若 A 为 false，则返回 A，否则返回 B           | (A and B) 为 false   |
| or     | 逻辑或操作符。 若 A 为 true，则返回 A，否则返回 B            | (A or B) 为 true     |
| not    | 逻辑非操作符。与逻辑运算结果相反，如果条件为 true，逻辑非为 false | not(A and B) 为 true |

<br>

**其它运算符**

| 操作符 | 描述                             | 实例                                                         |
| ------ | -------------------------------- | ------------------------------------------------------------ |
| ..     | 连接两个字符串                   | a..b ，其中 a 为 "Hello " ， b 为 "World", 输出结果为 "Hello World" |
| #      | 一元运算符，返回字符串或表的长度 | \#"Hello" 返回 5                                             |



------

# 字符串



Lua 语言中字符串可以使用以下三种方式来表示：

```LUA
-- 单引号间的一串字符
local str1 = 'This is a string.'

-- 双引号间的一串字符
local str2 = "Hello"

-- [[ 与 ]] 间的一串字符
local multilineString = [[
This is a multiline string.
It can contain multiple lines of text.
No need for escape characters.
]]
```

<br>

**字符串操作**

| 方法                                                 | 作用                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| string.upper(argument)                               | 字符串全部转为大写字母                                       |
| string.lower(argument)                               | 字符串全部转为小写字母                                       |
| string.gsub(mainString,findString,replaceString,num) | 在字符串中替换<br />mainString 为要操作的字符串， findString 为被替换的字符，replaceString 要替换的字符，num 替换次数（可以忽略，则全部替换） |
| string.find (str, substr, [init, [plain]])           | 在一个指定的目标字符串 str 中搜索指定的内容 substr，如果找到了一个匹配的子串，就会返回这个子串的起始索引和结束索引，不存在则返回 nil<br />init 指定了搜索的起始位置，默认为 1，可以一个负数，表示从后往前数的字符个数<br />plain 表示是否使用简单模式，默认为 false，true 只做简单的查找子串的操作，false 表示使用使用正则模式匹配 |
| string.reverse(arg)                                  | 字符串反转                                                   |
| string.format(...)                                   | 返回一个类似 printf 的格式化字符串                           |
| string.char(arg) 和 string.byte(arg[,int])           | char 将整型数字转成字符并连接， byte 转换字符为整数值(可以指定某个字符，默认第一个字符) |
| string.len(arg)                                      | 计算字符串长度，与 # 等价，计算的是字节数而不是字符数        |
| string.rep(string, n)                                | 返回字符串 string 的 n 个拷贝                                |
| ..                                                   | 链接两个字符串                                               |
| string.gmatch(str, pattern)                          | 返回一个迭代器函数，每一次调用这个函数，返回一个在字符串 str 找到的下一个符合 pattern 描述的子串。如果参数 pattern 描述的字符串没有找到，迭代函数返回 nil |
| string.match(str, pattern, init)                     | string.match() 只寻找源字串 str 中的第一个配对. 参数 init 可选, 指定搜寻过程的起点, 默认为 1<br />在成功配对时, 函数将返回配对表达式中的所有捕获结果; 如果没有设置捕获标记, 则返回整个配对字符串. 当没有成功的配对时, 返回 nil |



------

# 数组



Lua 中并没有专门的数组类型，而是使用一种被称为 table 的数据结构来实现数组的功能

在 Lua 索引值是以 1 为起始，但也可以指定 0 开始

<br>

数组的辑结构是线性表，可以使用索引访问数组元素：

```LUA
-- 创建一个数组
local myArray = {10, 20, 30, 40, 50}

-- 访问数组元素
print(myArray[1])  -- 输出 10
print(myArray[3])  -- 输出 30
```

<br>

计算数组的长度（即数组中元素的个数），可以使用 # 操作符：

```LUA
local myArray = {10, 20, 30, 40, 50}

-- 计算数组长度
local length = #myArray

print(length) -- 输出 5
```

<br>

一维数组可以用 for 循环出数组中的元素，lua 索引默认从 1 开始

```LUA
-- 创建一个数组
local myArray = {10, 20, 30, 40, 50}

-- 循环遍历数组
for i = 1, #myArray do
    print(myArray[i])
end
```



------

# 迭代器



在 Lua 中迭代器是一种支持指针类型的结构，它可以遍历集合的每一个元素

<br>

**泛型 for 迭代器**

泛型 for 迭代器提供了集合的 key/value 对，语法格式如下：

```LUA
for k, v in pairs(t) do
    print(k, v)
end
```

```LUA
array = {"Google", "Runoob"}

for key,value in ipairs(array) 
do
   print(key, value)
end
```



------

# table



table 是 Lua 的一种数据结构用来帮助我们创建不同的数据类型，如：数组、字典等

Lua table 使用关联型数组，可以用任意类型的值来作数组的索引，但这个值不能是 nil

Lua table 是不固定大小的，可以根据自己需要进行扩容

Lua 也是通过 table 来解决模块（module）、包（package）和对象（Object）的。 例如 string.format 表示使用 "format" 来索引 table string

<br>

构造器是创建和初始化表的表达式。表是 Lua 特有的功能强大的东西。最简单的构造函数是 {}，用来创建一个空表。可以直接初始化数组：

```LUA
-- 初始化表
mytable = {}

-- 指定值
mytable[1]= "Lua"

-- 移除引用
mytable = nil
-- lua 垃圾回收会释放内存
```

<br>

**常用方法**

* table.concat (table [, sep [, start [, end]]]):

  concat 是 concatenate(连锁, 连接)的缩写. table.concat() 函数列出参数中指定 table 的数组部分从 start 位置到 end 位置的所有元素, 元素间以指定的分隔符 (sep) 隔开

* table.insert (table, [pos,] value):

  在 table 的数组部分指定位置 (pos) 插入值为 value 的一个元素. pos 参数可选, 默认为数组部分末尾

* table.remove (table [, pos])

  返回 table 数组部分位于 pos 位置的元素. 其后的元素会被前移. pos 参数可选, 默认为 table 长度, 即从最后一个元素删起

* table.sort (table [, comp])

  对给定的 table 进行升序排序



------

# 模块和包



**声明**

模块类似于一个封装库，从 Lua 5.1 开始，Lua 加入了标准的模块管理机制，可以把一些公用的代码放在一个文件里，以 API 接口的形式在其他地方调用，有利于代码的重用和降低代码耦合度

Lua 的模块是由变量、函数等已知元素组成的 table，因此创建一个模块很简单，就是创建一个 table，然后把需要导出的常量、函数放入其中，最后返回这个 table 就行。以下为创建自定义模块 module.lua，文件代码格式如下：

```LUA
-- 文件名为 module.lua
-- 定义一个名为 module 的模块
module = {}
 
-- 定义一个常量
module.constant = "这是一个常量"
 
-- 定义一个函数
function module.func1()
    io.write("这是一个公有函数！\n")
end
 
local function func2()
    print("这是一个私有函数！")
end
 
function module.func3()
    func2()
end
 
return module
```

由上可知，模块的结构就是一个 table 的结构，因此可以像操作调用 table 里的元素那样来操作调用模块里的常量或函数

上面的 func2 声明为程序块的局部变量，即表示一个私有函数，因此是不能从外部访问模块里的这个私有函数，必须通过模块里的公有函数来调用

<br>

**引入**

Lua 提供了一个名为 require 的函数用来加载模块。要加载一个模块，只需要简单地调用就可以了。例如：

```LUA
require("<模块名>")

-- 或者

require "<模块名>"
```

也可以给加载的模块定义一个别名变量，方便调用：

```LUA
-- test_module2.lua 文件
-- module 模块为上文提到到 module.lua
-- 别名变量 m
local m = require("module")
 
print(m.constant)
 
m.func3()
```

<br>

**加载机制**

对于自定义的模块，模块文件不是放在哪个文件目录都行，函数 require 有它自己的文件路径加载策略，它会尝试从 Lua 文件或 C 程序库中加载模块

require 用于搜索 Lua 文件的路径是存放在全局变量 package.path 中，当 Lua 启动后，会以环境变量 LUA_PATH 的值来初始这个环境变量。如果没有找到该环境变量，则使用一个编译时定义的默认路径来初始化

文件路径以 ";" 号分隔，最后的 2 个 ";;" 表示新加的路径后面加上原来的默认路径

假设 package.path 的值是：

```
/Users/dengjoe/lua/?.lua;./?.lua;/usr/local/share/lua/5.1/?.lua;/usr/local/share/lua/5.1/?/init.lua;/usr/local/lib/lua/5.1/?.lua;/usr/local/lib/lua/5.1/?/init.lua
```

那么调用 require("module") 时就会尝试打开以下文件目录去搜索目标

```
/Users/dengjoe/lua/module.lua;
./module.lua
/usr/local/share/lua/5.1/module.lua
/usr/local/share/lua/5.1/module/init.lua
/usr/local/lib/lua/5.1/module.lua
/usr/local/lib/lua/5.1/module/init.lua
```

如果找过目标文件，则会调用 package.loadfile 来加载模块。否则，就会去找 C 程序库

<br>

**C 包**

Lua 和 C 是很容易结合的，使用 C 为 Lua 写包

与 Lua 中写包不同，C 包在使用以前必须首先加载并连接，在大多数系统中最容易的实现方式是通过动态连接库机制

Lua 在一个叫 loadlib 的函数内提供了所有的动态连接的功能。这个函数有两个参数: 库的绝对路径和初始化函数。所以典型的调用的例子如下:

```LUA
local path = "/usr/local/lua/lib/libluasocket.so"
local f = loadlib(path, "luaopen_socket")
```

loadlib 函数加载指定的库并且连接到 Lua，然而它并不打开库（也就是说没有调用初始化函数），反之他返回初始化函数作为 Lua 的一个函数，这样我们就可以直接在 Lua 中调用他

如果加载动态库或者查找初始化函数时出错，loadlib 将返回 nil 和错误信息。可以修改前面一段代码，使其检测错误然后调用初始化函数：

```LUA
local path = "/usr/local/lua/lib/libluasocket.so"
-- 或者 path = "C:\\windows\\luasocket.dll"，这是 Window 平台下
local f = assert(loadlib(path, "luaopen_socket"))
f()  -- 真正打开库
```

一般情况下我们期望二进制的发布库包含一个与前面代码段相似的 stub 文件，安装二进制库的时候可以随便放在某个目录，只需要修改 stub 文件对应二进制库的实际路径即可

将 stub 文件所在的目录加入到 LUA_PATH，这样设定后就可以使用 require 函数加载 C 库了



------

# 协同程序



Lua 协同程序 (coroutine) 与线程比较类似：拥有独立的堆栈，独立的局部变量，独立的指令指针，同时又与其它协同程序共享全局变量和其它大部分东西

协同程序可以理解为一种特殊的线程，可以暂停和恢复其执行，从而允许非抢占式的多任务处理

协同是非常强大的功能，但是用起来也很复杂

<br>

**基本语法**

协同程序由 coroutine 模块提供支持

使用协同程序，你可以在函数中使用 coroutine.create 创建一个新的协同程序对象，并使用 coroutine.resume 启动它的执行。协同程序可以通过调用 coroutine.yield 来主动暂停自己的执行，并将控制权交还给调用者

| 方法                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| coroutine.create()  | 创建 coroutine，返回 coroutine， 参数是一个函数，当和 resume 配合使用的时候就唤醒函数调用 |
| coroutine.resume()  | 重启 coroutine，和 create 配合使用                           |
| coroutine.yield()   | 挂起 coroutine，将 coroutine 设置为挂起状态，这个和 resume 配合使用能有很多有用的效果 |
| coroutine.status()  | 查看 coroutine 的状态，coroutine 的状态有三种：dead，suspended，running |
| coroutine.wrap()    | 创建 coroutine，返回一个函数，一旦你调用这个函数，就进入 coroutine，和 create 功能重复 |
| coroutine.running() | 返回正在跑的 coroutine，一个 coroutine 就是一个线程，当使用 running 的时候，就是返回一个 coroutine 的线程号 |

```LUA
-- 创建了一个新的协同程序对象 co，其中协同程序函数打印传入的参数 i
co = coroutine.create(
    function(i)
        print(i);
    end
)
-- 使用 coroutine.resume 启动协同程序 co 的执行，并传入参数 1。协同程序开始执行，打印输出为 1
coroutine.resume(co, 1)   -- 1

-- 通过 coroutine.status 检查协同程序 co 的状态，输出为 dead，表示协同程序已经执行完毕
print(coroutine.status(co))  -- dead
 
print("----------")

-- 使用 coroutine.wrap 创建了一个协同程序包装器，将协同程序函数转换为一个可直接调用的函数对象
co = coroutine.wrap(
    function(i)
        print(i);
    end
)
 
co(1)
 
print("----------")
-- 创建了另一个协同程序对象 co2，其中的协同程序函数通过循环打印数字 1 到 10，在循环到 3 的时候输出当前协同程序的状态和正在运行的线程
co2 = coroutine.create(
    function()
        for i=1,10 do
            print(i)
            if i == 3 then
                print(coroutine.status(co2))  --running
                print(coroutine.running()) --thread:XXXXXX
            end
            coroutine.yield()
        end
    end
)

-- 连续调用 coroutine.resume 启动协同程序 co2 的执行
coroutine.resume(co2) --1
coroutine.resume(co2) --2
coroutine.resume(co2) --3

-- 通过 coroutine.status 检查协同程序 co2 的状态，输出为 suspended，表示协同程序暂停执行
print(coroutine.status(co2))   -- suspended
print(coroutine.running())
 
print("----------")
```

<br>

**线程和协同程序的区别**

线程与协同程序的主要区别在于，一个具有多个线程的程序可以同时运行几个线程，而协同程序却需要彼此协作的运行。在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只有在明确的被要求挂起的时候才会被挂起。协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同

<br>

主要区别归纳如下：

* 调度方式：线程通常由操作系统的调度器进行抢占式调度，操作系统会在不同线程之间切换执行权。而协同程序是非抢占式调度的，它们由程序员显式地控制执行权的转移
* 并发性：线程是并发执行的，多个线程可以同时运行在多个处理器核心上，或者通过时间片轮转在单个核心上切换执行。协同程序则是协作式的，只有一个协同程序处于运行状态，其他协同程序必须等待当前协同程序主动放弃执行权
* 内存占用：线程通常需要独立的堆栈和上下文环境，因此线程的创建和销毁会带来额外的开销。而协同程序可以共享相同的堆栈和上下文，因此创建和销毁协同程序的开销较小
* 数据共享：线程之间可以共享内存空间，但需要注意线程安全性和同步问题。协同程序通常通过参数传递和返回值来进行数据共享，不同协同程序之间的数据隔离性较好
* 调试和错误处理：线程通常在调试和错误处理方面更复杂，因为多个线程之间的交互和并发执行可能导致难以调试的问题。协同程序则在调试和错误处理方面相对简单，因为它们是由程序员显式地控制执行流程的

总体而言，线程适用于需要并发执行的场景，例如在多核处理器上利用并行性加快任务的执行速度。而协同程序适用于需要协作和协调的场景，例如状态机、事件驱动编程或协作式任务处理。选择使用线程还是协同程序取决于具体的应用需求和编程模型



------

# 文件 I/O



Lua I/O 库用于读取和处理文件。分为简单模式（和 C 一样）、完全模式

* 简单模式（simple model）拥有一个当前输入文件和一个当前输出文件，并且提供针对这些文件相关的操作
* 完全模式（complete model） 使用外部的文件句柄来实现。它以一种面对对象的形式，将所有的文件操作定义为文件句柄的方法

简单模式在做一些简单的文件操作时较为合适。但是在进行一些高级的文件操作的时候，简单模式就显得力不从心。例如同时读取多个文件这样的操作，使用完全模式则较为合适

<br>

**打开文件**

打开文件操作语句如下：

```LUA
file = io.open (filename [, mode])
```

<br>

mode 的值有：

| 模式 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| r    | 以只读方式打开文件，该文件必须存在                           |
| w    | 打开只写文件，若文件存在则文件长度清为 0，即该文件内容会消失。若文件不存在则建立该文件 |
| a    | 以附加的方式打开只写文件。若文件不存在，则会建立该文件，如果文件存在，写入的数据会被加到文件尾，即文件原先的内容会被保留。（EOF符保留） |
| r+   | 以可读写方式打开文件，该文件必须存在                         |
| w+   | 打开可读写文件，若文件存在则文件长度清为零，即该文件内容会消失。若文件不存在则建立该文件 |
| a+   | 与 a 类似，但此文件可读可写                                  |
| b    | 二进制模式，如果文件是二进制文件，可以加上 b                 |
| +    | 表示对文件既可以读也可以写                                   |

<br>

**简单模式**

```LUA
-- 以只读方式打开文件
file = io.open("test.lua", "r")

-- 设置默认输入文件为 test.lua
io.input(file)

-- 输出文件第一行
print(io.read())

-- 关闭打开的文件
io.close(file)

-- 以附加的方式打开只写文件
file = io.open("test.lua", "a")

-- 设置默认输出文件为 test.lua
io.output(file)

-- 在文件最后一行添加 Lua 注释
io.write("--  test.lua 文件末尾注释")

-- 关闭打开的文件
io.close(file)
```

输出了 test.lua 文件的第一行信息，并在该文件最后一行添加了 lua 的注释

<br>

**完全模式**

通常需要在同一时间处理多个文件。我们需要使用 file:function_name 来代替 io.function_name 方法。以下实例演示了如何同时处理同一个文件:

```LUA
-- 以只读方式打开文件
file = io.open("test.lua", "r")

-- 输出文件第一行
print(file:read())

-- 关闭打开的文件
file:close()

-- 以附加的方式打开只写文件
file = io.open("test.lua", "a")

-- 在文件最后一行添加 Lua 注释
file:write("--test")

-- 关闭打开的文件
file:close()
```



------

# 错误处理



程序运行中错误处理是必要的，在我们进行文件操作，数据转移及 web service 调用过程中都会出现不可预期的错误。如果不注重错误信息的处理，就会造成信息泄露，程序无法运行等情况

任何程序语言中，都需要错误处理。错误类型有语法错误和运行错误。语法错误处理起来很简单，只需要改成正确的语法就行了

<br>

可以使用两个函数：assert 和 error 来处理错误。实例如下：

```LUA
local function add(a,b)
   assert(type(a) == "number", "a 不是一个数字")
   assert(type(b) == "number", "b 不是一个数字")
   return a+b
end
add(10)
```

执行以上程序会出现如下错误：

```
lua: test.lua:3: b 不是一个数字
stack traceback:
    [C]: in function 'assert'
    test.lua:3: in local 'add'
    test.lua:6: in main chunk
    [C]: in ?
```

实例中 assert 首先检查第一个参数，若没问题，assert 不做任何事情；否则，assert 以第二个参数作为错误信息抛出

<br>

Lua 中处理错误，可以使用函数 pcall（protected call）来包装需要执行的代码

```LUA
if pcall(function_name, ….) then
-- 没有错误
else
-- 一些错误
end
```



------

# 垃圾回收



Lua 采用了自动内存管理。 这意味着不用操心新创建的对象需要的内存如何分配出来， 也不用考虑在对象不再被使用后怎样释放它们所占用的内存

Lua 运行了一个垃圾收集器来收集所有死对象 （即在 Lua 中不可能再访问到的对象）来完成自动内存管理的工作。 Lua 中所有用到的内存，如：字符串、表、用户数据、函数、线程、 内部结构等，都服从自动管理

Lua 实现了一个增量标记 - 扫描收集器。 它使用这两个数字来控制垃圾收集循环： 垃圾收集器间歇率和垃圾收集器步进倍率。 这两个数字都使用百分数为单位 （例如：值 100 在内部表示 1 ）

垃圾收集器间歇率控制着收集器需要在开启新的循环前要等待多久。 增大这个值会减少收集器的积极性。 当这个值比 100 小的时候，收集器在开启新的循环前不会有等待。 设置这个值为 200 就会让收集器等到总内存使用量达到之前的两倍时才开始新的循环

垃圾收集器步进倍率控制着收集器运作速度相对于内存分配速度的倍率。 增大这个值不仅会让收集器更加积极，还会增加每个增量步骤的长度。 不要把这个值设得小于 100 ， 那样的话收集器就工作的太慢了以至于永远都干不完一个循环。 默认值是 200 ，这表示收集器以内存分配的 "两倍" 速工作

如果你把步进倍率设为一个非常大的数字 （比你的程序可能用到的字节数还大 10% ）， 收集器的行为就像一个 stop-the-world 收集器。 接着你若把间歇率设为 200 ， 收集器的行为就和过去的 Lua 版本一样了： 每次 Lua 使用的内存翻倍时，就做一次完整的收集



------

# Nginx + Lua + MySQL



1. 安装插件

   Lua 提供的一些基础函数实现了对字符串、IO 流、日期等数据的操作，如果想实现更多的功能，则需要安装一些插件，比如此次案例中需要用到的 cjson 插件和 resty.mysql 插件

   OpenResty(又称：ngx_openresty) 是一个基于 NGINX 的可伸缩的 Web 平台，由中国人章亦春发起，提供了很多高质量的第三方模块

   因为 OpenResty 中已经集成了 cjson 和 resty.mysql 插件，所以我们安装 OpenResty 即可

   ```sh
   wget https://openresty.org/download/ngx_openresty-1.9.7.1.tar.gz   # 下载
   tar xzvf ngx_openresty-1.9.7.1.tar.gz       # 解压
   cd ngx_openresty-1.9.7.1/ 
   ./configure
   make 
   make install
   ```

2. 编写 Lua 脚本

   ```lua
   -- 指定输出的文件类型为 JSON
   ngx.header.content_type = "application/json;charset=utf-8"
   -- 引入库文件 JSON
   local cjson = require "cjson"
   -- 引入依赖库 mysql
   local mysql = require "resty.mysql"
   
   -- 配置数据库连接信息
   local props = {
       host = "数据库地址"，
       port = 3306,
       database = "数据库名"，
       user = "用户名",
       password = "用户密码"
   }
   
   -- 创建连接，设置超时时间和编码格式
   local db = mysql:new()
   db:set_timeout(10000)
   db:connect(props)
   db:query("SET NAMES utf8")
   
   -- SQL 语句
   local id = ngx.req.get_uri_args()["id"] -- 从请求路径中获得的参数
   local sql = "SELECT * FROM userinfo WHERE id = " ..id
   
   -- 执行 SQL 语句
   local result = db:query(sql)
   
   -- 关闭连接
   db:close()
   
   -- 将响应结果以 JSON 的方式返回
   ngx.sqy(cjson.encode(result))
   ```

3. 配置 Nginx

   ```
   -- 在 Nginx 添加如下配置
   server {
   	listen 80;
   	server_name localhost;
   	# 执行 Lua 脚本
   	location /mysql {
   		content_by_lua_file "Lua文件全路径"
   	} 
   }
   ```

4. 访问地址

   接下来，在浏览器中访问 http://nginx地址/mysql?id=1 即可

   ![image-20230530001918657](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/Redis/Lua%20%E6%95%99%E7%A8%8B%E5%AE%9E%E6%88%98/image-20230530001918657.png)



------

# SpringBoot + Redis + Lua 实现扣减库存



1. 引入依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.boot</groupId>
   	<artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 编写 Lua 脚本

   ```lua
   -- second.lua
   -- 从传入的参数数组中取出参数
   local stockKey = KEYS[1]
   --商品购买用户记录Key product_buyers_XXX
   local buyersKey = KEYS[2]
   --用户ID
   local uid = KEYS[3]
   --校验用户是否已经购买
   local result=redis.call("sadd" , buyersKey , uid )
   if(tonumber(result)==1)
   then 
       --没有购买过，可以购买
       local stock=redis.call("lpop" , stockKey )
       --除了nil和false，其他值都是真（包括0）
       if(stock)
       then 
           --有库存
           return 1
       else
           --没有库存
           return -1
       end
   else
       --已经购买过
       return -3
   end
   ```

3. RedisLuaUtil 工具类

   ```java
   @Slf4j
   @Service
   public class RedisLuaUtil {
   
       @Resource
       private StringRedisTemplate stringRedisTemplate;
   
       public String runLuaScript(String luaFileName,List<String> keyList) {
   		// Lua 脚本路径
           DefaultRedisScript<String> redisScript = new DefaultRedisScript<>();
           redisScript.setScriptSource(new ResourceScriptSource(new ClassPathResource("lua/"+luaFileName)));
   		// 脚本执行返回参数类型
           redisScript.setResultType(Integer.class);
   
           Integer result = null;
           String argsone = "none";
   
           try {
               result = stringRedisTemplate.execute(redisScript, keyList,argsone);
           } catch (Exception e) {
               logger.error("发生异常",e);
           }
   
           return result;
   
       }
   
   }
   ```

4. 调用 Util 方法

   ```java
       @Override
       public String skuSecond(String actId,String userId,int buyNum,String skuId,int perSkuLim,int perActLim) {
   
           //时间字串，用来区分秒杀成功的订单
           int START = 100000;
           int END = 900000;
           int rand_num = ThreadLocalRandom.current().nextInt(END - START + 1) + START;
           String order_time = TimeUtil.getTimeNowStr()+"-"+rand_num;
           List<String> keyList = new ArrayList();
           
           keyList.add(userId);
           keyList.add(String.valueOf(buyNum));
           keyList.add(skuId);
           keyList.add(String.valueOf(perSkuLim));
           keyList.add(actId);
           keyList.add(String.valueOf(perActLim));
           keyList.add(order_time);
   
           String result = redisLuaUtil.runLuaScript("second.lua",keyList);
           System.out.println("------------------lua result:"+result);
           return result;
       }
   ```

   



------

# Lua 扣减库存 + 风控校验



```LUA
-- 从入参中取得活动 id 和奖品 id，拼接成 Redis Key
local inventory_redis_key = 'Inventory:'..KEYS[1]..':'..KEYS[2]
local prize_issued_num_key = 'Issued:'..KEYS[1]..':'..KEYS[2]
local total_num = tonumber(KEYS[3]);
-- 查询奖品总库存和每日库存
local inventory = tonumber(redis.call('get', inventory_redis_key))
local issued_num = tonumber(redis.call('get', prize_issued_num_key))

-- 如果没有设置每日库存或每日库存为 0，，则返回库存不足
if inventory == nil or inventory == 0 then
    return 'NO_INVENTORY'
-- 如果不限日库存，则要求总库存大于已领库存，成功的话对已领库存 +1，并判断是否触发总库存监控
elseif inventory < 0 then
    if total_num < 0 then
        redis.call('set', prize_issued_num_key, tostring(issued_num + 1))
        return 'SUCCESS'
    elseif total_num > issued_num then
        redis.call('set', prize_issued_num_key, tostring(issued_num + 1))
        -- 判断是否触发总库存监控，如果是库存监控触发的最后一个值则触发报警，其余的则触发监控
        local total_trigger_list_key = 'Monitor:Total:'..KEYS[1]..':'..KEYS[2]
        local total_trigger_list = redis.call('lrange', total_trigger_list_key, 0, -1)
        if total_trigger_list == nil or #total_trigger_list <= 0 then
            return 'SUCCESS'
        end
        for index, value in ipairs(total_trigger_list) do
            if (issued_num + 1) == tonumber(value)  then
                if index == #total_trigger_list then
                    return 'TOTAL_WARNING'
                else
                    return 'TOTAL_MONITOR'
                end
            end
        end
        return 'SUCCESS'
    else
        return 'NO_INVENTORY'
    end
    -- 如果每日库存受限而总库存不受限，则判断每日库存是否足够，库存足够的话则对已领库存 +1 和每日库存 -1，并判断是否触发每日库存监控
elseif inventory > 0 and total_num < 0 then
    if inventory > 0 then
        redis.call('set', inventory_redis_key, tostring(inventory - 1))
        redis.call('set', prize_issued_num_key, tostring(issued_num + 1))
        -- 判断是否触发每日库存监控，如果是库存监控触发的最后一个值则触发报警，其余的则触发监控
        local daily_trigger_list_key = 'Monitor:Daily:'..KEYS[1]..':'..KEYS[2]
        local daily_trigger_list = redis.call('lrange', daily_trigger_list_key, 0, -1)
        if daily_trigger_list == nil or #daily_trigger_list <= 0 then
            return 'SUCCESS'
        end
        for index, value in ipairs(daily_trigger_list) do
            if (inventory - 1) == tonumber(value)  then
                if index == #daily_trigger_list then
                    return 'DAILY_WARNING'
                else
                    return 'DAILY_MONITOR'
                end
            end
        end
        return 'SUCCESS'
    else
        return 'NO_INVENTORY'
    end
    -- 如果每日库存和奖品总库存都是不限库存的话，则对已领库存 +1 即可
elseif inventory < 0 and total_num < 0 then
    redis.call('set', prize_issued_num_key, tostring(issued_num + 1))
    return 'SUCCESS'
    -- 如果二者都受限，则判断两个库存是否都足够，库存足够的话则对已领库存 +1 和每日库存 -1，并判断二者是否触发风控
else
    if inventory > 0 and total_num > issued_num then
        redis.call('set', inventory_redis_key, tostring(inventory - 1))
        redis.call('set', prize_issued_num_key, tostring(issued_num + 1))
        -- 判断是否触发总库存监控，不直接返回值，记录状态
        -- 不触发：0  总库存监控：1  总库存报警：5
        local total_monitor_status = 0;
        local total_trigger_list_key = 'Monitor:Total:'..KEYS[1]..':'..KEYS[2]
        local total_trigger_list = redis.call('lrange', total_trigger_list_key, 0, -1)
        if total_trigger_list ~= nil and #total_trigger_list > 0 then
            for index, value in ipairs(total_trigger_list) do
                if (issued_num + 1) == tonumber(value)  then
                    if index == #total_trigger_list then
                        total_monitor_status = 5
                    else
                        total_monitor_status = 1
                    end
                end
            end
        end
        -- 判断是否触发每日库存监控，不直接返回值，记录状态
        -- 不触发：0  每日库存监控：2  每日库存报警：9
        local daily_monitor_status = 0;
        local daily_trigger_list_key = 'Monitor:Daily:'..KEYS[1]..':'..KEYS[2]
        local daily_trigger_list = redis.call('lrange', daily_trigger_list_key, 0, -1)
        if daily_trigger_list ~= nil and #daily_trigger_list > 0 then
            for index, value in ipairs(daily_trigger_list) do
                if (inventory - 1) == tonumber(value)  then
                    if index == #daily_trigger_list then
                        daily_monitor_status = 9
                    else
                        daily_monitor_status = 2
                    end
                end
            end
        end
        -- 将两个 monitor_status 相加，根据其值的唯一性来判断触发的监控类型
        local monitor_status = total_monitor_status + daily_monitor_status
        if monitor_status == 0 then
            return 'SUCCESS'
        elseif monitor_status == 1 then
            return 'TOTAL_MONITOR'
        elseif monitor_status == 2 then
            return 'DAILY_MONITOR'
        elseif monitor_status == 3 then
            return 'TOTAL_MONITOR_AND_DAILY_MONITOR'
        elseif monitor_status == 5 then
            return 'TOTAL_WARNING'
        elseif monitor_status == 7 then
            return 'TOTAL_WARNING_AND_DAILY_MONITOR'
        elseif monitor_status == 9 then
            return 'DAILY_WARNING'
        elseif monitor_status == 10 then
            return 'TOTAL_MONITOR_AND_DAILY_WARNING'
        else
            return 'TOTAL_WARNING_AND_DAILY_WARNING'
        end
    else
        return 'NO_INVENTORY'
    end
end
```

