---
title: Go 语法基础
date: 2025-07-18 02:02:02
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80/preview.jpg
tags:
  - Go
categories: Go
---



# 入门

## 简介



Go 语言诞生于 2007 年 9 月 20 日，由三位大佬共同创造，他们分别是：

* Ken Thompson，图灵奖获得者，Unix 系统创始人，B 语言创始人，C 语言创始人
* Rob Pike，Plan9 操作系统作者之一，UTF-8 发明者之一，Go 语言设计领头人
* Robert Griesemer，JS V8引擎研发者，三人之中最年轻

最开始，Go 只有一个名字：Go，golang 的叫法仅仅是因为那会域名 Go 被抢注了，在以前是 golang.org，现在的官网域名是 go.dev

Go 团队经过了三年的设计与研发，于 2012 年 3 月发布了第一个正式版本 Go1（这个时候 Go 的工具链和运行时都还是 C 编写的，直到 Go1.5 才完成自举），此后每一年发布两个小版本更新，一直运营和维护到现在



------

## 特性



* 语法简单

  Go 语言在自由度和灵活度上做了取舍，以此换来了更好的维护性和平滑的学习曲线

* 部署友好

  Go 静态编译后的二进制文件不依赖额外的运行环境，编译速度也非常快

* 交叉编译

  Go 仅需要在编译时简单设置两个参数，就可以编译出能在其它平台上运行的程序

* 天然并发

  Go 语言对于并发的支持是纯天然的，仅需一个关键字，就可以开启一个异步协程

* 垃圾回收

  Go 有着优秀的 GC 性能，大部分情况下 GC 延时都不会超过 1 毫秒

* 丰富的标准库

  从字符串处理到源码 AST 解析，功能强大且丰富的标准库是 Go 语言坚实的基础

* 完善的工具链

  Go 有着完善的开发工具链，涵盖了编译，测试，依赖管理，性能分析等方方面面

<br>

Go 语言抛弃了继承，弱化了 OOP，类，元编程，泛型，Lamda 表达式等这些特性，拥有良好的性能和较低的上手难度，它适合用于云服务开发，应用服务端开发，以及网络编程。它自带 GC，不需要开发者手动管理内存，静态编译和交叉编译这两点对于运维而言也十分友好

Go 语言的缺点同样也有很多，比如令人诟病的错误处理，残缺的泛型，标准库虽然很丰富但内置的数据结构却没几个，interface{} 类型满天飞，没有枚举类型，除此之外，Go 开发团队非常固执己见，不善于听取社区意见等等


------

## 安装



Go语言下载：[Downloads - The Go Programming Language](https://go.dev/dl/)

![img](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80/download.png)

Stable Version 指的是目前处于维护状态的两个稳定版本，Archived Version 指的是不再维护的历史版本，前往[更新日志](https://golang.halfiisland.com/release.html)了解更多关于维护规则以及历史版本的信息

<br>

**Windows**

对于 windows 平台而言，有 installer 和 archive 两种类型可选，前者就是安装包，只需要点点点

<br>

**Linux**

以 ubuntu 为例，复制想要的版本的链接，下载到本地

```sh
wget https://golang.google.cn/dl/go1.21.1.linux-amd64.tar.gz
```

解压到指定目录

```SH
tar -C ~/go -xzf go1.21.1.linux-amd64.tar.gz
```

在 `$HOME/.bashrc` 文件中设置环境变量

```bash
export GOROOT=$HOME/go
export GOPATH=$HOME/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

完成后查看安装版本，确认正确安装

```SH
go version
go version go1.21.1 linux/amd64
```



------

## Hello World



```go
package main  // 声明 main 包，表明当前是一个可执行程序

import "fmt"  // 导入内置 fmt 

func main(){  // main 函数，是程序执行的入口
    fmt.Println("Hello World!")  // 在终端打印 Hello World!
}
```

<br>

package 关键字代表的是当前 go 文件属于哪一个包，启动文件通常是 main 包，启动函数通常是 main 函数，在自定义包和函数时命名应当尽量避免与之重复

import 是导入关键字，后面跟着的是被导入的包名

func 是函数声明关键字，用于声明一个函数



------

# 基本语法

## 包



在 Go 中，程序是通过将包链接在一起来构建的，也可以理解为最基本的调用单位是包，而不是 go 文件。包其实就是一个文件夹，包内共享所有源文件的变量，常量，函数以及其他类型。包的命名风格建议都是小写字母，并且要尽量简短

<br>

例如创建一个 example 包，包下有如下函数

```JAVA
package example

import "fmt"

func SayHello() {
   fmt.Println("Hello")
}
```

<br>

可以在 main 函数中调用它

```go
package example

import "fmt"

func SayHello() {
   fmt.Println("Hello")
}
```

也可以起别名

```go
package main

import e "example"

func main() {
   e.SayHello()
}
```

批量引入时，可以用 () 来表示

```go
package main

import (
   "fmt"
   "math"
)

func main() {
   fmt.Println(math.MaxInt64)
}
```

或者说只导入不调用，通常这么做是为了调用该包下的 init 函数

```go
package main

import (
   "fmt"
    _ "math" // 下划线表示匿名导入
)

func main() {
   fmt.Println(1)
}
```

<br>

在 Go 中，导出和访问控制是通过命名来进行实现的，如果想要对外暴露一个函数或者一个变量，只需要将其名称首字母大写即可，例如 example 包下的 SayHello 函数

```go
package example

import "fmt"

// 首字母大写，可以被包外访问
func SayHello() {
   fmt.Println("Hello")
}
```

如果想要不对外暴露的话，只需将名称首字母改为小写即可

```go
package example

import "fmt"

// 首字母小写，外界无法访问
func sayHello() {
   fmt.Println("Hello")
}
```

对外暴露的函数和变量可以被包外的调用者导入和访问，如果是不对外暴露的话，那么仅包内的调用者可以访问，外部将无法导入和访问，该规则适用于整个 Go 语言

<br>

Go 中约定，一个包内名为 internal 包为内部包，外部包将无法访问内部包中的任何内容，否则的话编译不通过



------

## 注释



Go 支持单行注释和多行注释，注释与内容之间建议隔一个空格

```go
// 这是main包
package main

// 导入了fmt包
import "fmt"

/*
*
这是启动函数main函数
*/
func main() {
	// 这是一个语句
	fmt.Println("Hello World!")
}

```



------

## 标识符和运算符



标识符就是一个名称，用于包命名，函数命名，变量命名等等，命名规则如下：

* 只能由字母，数字，下划线组成
* 只能以字母和下划线开头
* 严格区分大小写
* 不能与任何已存在的标识符重复，即包内唯一的存在
* 不能与 Go 任何内置的关键字冲突

<br>

下面是 Go 语言中支持的运算符号的优先级排列

```text
Precedence    Operator
    5             *  /  %  <<  >>  &  &^
    4             +  -  |  ^
    3             ==  !=  <  <=  >  >=
    2             &&
    1             ||
```

有一点需要稍微注意下，Go 语言中没有选择将 ~ 作为取反运算符，而是复用了 ^ 符号，当两个数字使用 ^ 时，例如 a^b ，它就是异或运算符，只对一个数字使用时，例如 ^a ，那么它就是取反运算符。Go 也支持增强赋值运算符，如下

```go
a += 1
a /= 2
a &^= 2
```

<br>

Go 语言中没有自增与自减运算符，它们被降级为了语句 statement ，并且规定了只能位于操作数的后方，所以不用再去纠结 i++ 和 ++i 这样的问题

```text
a++ // 正确
++a // 错误
a-- // 正确
```

还有一点就是，它们不再具有返回值，因此 a = b++ 这类语句的写法是错误的



------

## 风格



关于编码风格这一块 Go 是强制所有人统一同一种风格，Go 官方提供了一个格式化工具 gofmt，通过命令行就可以使用，该格式化工具没有任何的格式化参数可以传递，仅有的两个参数也只是输出格式化过程，所以完全不支持自定义，也就是说所有通过此工具的格式化后的代码都是同一种代码风格



------

# 数据类型

## 布尔类型



布尔类型只有真值和假值

| 类型 | 描述                      |
| ---- | ------------------------- |
| bool | true 为真值，false 为假值 |

在 Go 中，整数 0 并不代表假值，非零整数也不能代表真值，即数字无法代替布尔值进行逻辑判断，两者是完全不同的类型



------

## 整型



Go 中为不同位数的整数分配了不同的类型，主要分为无符号整型与有符号整型

| 类型    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| uint8   | 无符号 8 位整型                                              |
| uint16  | 无符号 16 位整型                                             |
| uint32  | 无符号 32 位整型                                             |
| uint64  | 无符号 64 位整型                                             |
| int8    | 有符号 8 位整型                                              |
| int16   | 有符号 16 位整型                                             |
| int32   | 有符号 32 位整型                                             |
| int64   | 有符号 64 位整型                                             |
| uint    | 无符号整型 至少 32 位                                        |
| int     | 整型 至少 32 位                                              |
| uintptr | 价于无符号 64 位整型，但是专用于存放指针运算，用于存放死的指针地址 |



------

## 浮点型



浮点数，主要分为单精度浮点数与双精度浮点数

| 类型    | 描述        |
| ------- | ----------- |
| float32 | 32 位浮点数 |
| float64 | 64 位浮点数 |



------

## 字符类型



Go 语言字符串完全兼容 UTF-8

| 类型   | 描述                                               |
| ------ | -------------------------------------------------- |
| byte   | 等价  uint8 可以表达 ANSCII 字符                   |
| rune   | 等价  int32 可以表达 Unicode 字符                  |
| string | 字符串即字节序列，可以转换为 []byte 类型即字节切片 |



------

## 派生类型



包括包括：指针类型（Pointer）、数组类型、结构化类型(struct)、Channel 类型、函数类型、切片类型、接口类型（interface）、Map 类型等



------

## 零值



官方文档中零值称为 zero value，零值并不仅仅只是字面上的数字零，而是一个类型的空值或者说默认值更为准确

| 类型                                 | 零值                         |
| ------------------------------------ | ---------------------------- |
| 数字类型                             | 0                            |
| 布尔类型                             | false                        |
| 字符串类型                           | ""                           |
| 数组                                 | 固定长度的对应类型的零值集合 |
| 结构体                               | 内部字段都是零值的结构体     |
| 切片，映射表，函数，接口，通道，指针 | nil                          |



------

## nil



源代码中的 nil，可以看出 nil 仅仅只是一个变量

```go
var nil Type
```

Go 中的 nil 并不等同于其他语言的 null，nil 仅仅只是一些类型的零值，并且不属于任何类型，所以 nil == nil 这样的语句是无法通过编译的



------

# 常量



常量的值无法在运行时改变，一旦赋值过后就无法修改，其值只能来源于：

* 字面量
* 其他常量标识符
* 常量表达式
* 结果是常量的类型转换
* iota

常量只能是基本数据类型，不能是

* 除基本类型以外的其它类型，如结构体，接口，切片，数组等
* 函数的返回值

常量的值无法被修改，否则无法通过编译



------

## 初始化



常量的声明需要用到 const 关键字，常量在声明时就必须初始化一个值，并且常量的类型可以省略，例如

```go
const name string = "Jack" // 字面量

const msg = "hello world" // 字面量

const num = 1 // 字面量

const numExpression = (1+2+3) / 2 % 100 + num // 常量表达式
```

<br>

如果仅仅只是声明而不指定值，将会无法通过编译

```go
// missing init expr for name
const name string
```

<br>

批量声明常量可以用 () 括起来以提升可读性，可以存在多个 ()达到分组的效果

```go
const (
   Count = 1
   Name  = "Jack"
)

const (
   Size = 16
   Len  = 25
)
```

<br>

在同一个常量分组中，在已经赋值的常量后面的常量可以不用赋值，其值默认就是前一个的值，比如

```go
const (
	A = 1
	B // 1
	C // 1
	D // 1
	E // 1
)
```



------

## iota



iota 是一个内置的常量标识符，通常用于表示一个常量声明中的无类型整数序数，一般都是在括号中使用

```go
const iota = 0 
```

<br>

```go
const (
   Num = iota // 0
   Num1 // 1
   Num2 // 2
   Num3 // 3
   Num4 // 4
)
```

也可以这么写

```go
const (
   Num = iota*2 // 0
   Num1 // 2
   Num2 // 4
   Num3 // 6
   Num4 // 8
)
```

还可以

```go
const (
   Num = iota << 2*3 + 1 // 1
   Num1 // 13
   Num2 // 25
   Num3 = iota // 3
   Num4 // 4
)
```

<br>

通过上面几个例子可以发现，iota 是递增的，第一个常量使用 iota 值的表达式，根据序号值的变化会自动的赋值给后续的常量，直到用新的 iota 重置，这个序号其实就是代码的相对行号，是相对于当前分组的起始行号

```go
const (
	Num  = iota<<2*3 + 1 // 1 第一行
	Num2 = iota<<2*3 + 1 // 13 第二行
	_ // 25 第三行
	Num3 //37 第四行
	Num4 = iota // 4 第五行
	_ // 5 第六行
	Num5 // 6 第七行
)
```

例子中使用了匿名标识符 _ 占了一行的位置，可以看到 iota 的值本质上就是 iota 所在行相对于当前 const 分组的第一行的差值。而不同的 const 分组则相互不会影响



------

## 枚举



Go 语言没有为枚举单独设计一个数据类型，不像其它语言通常会有一个 enum 来表示。一般在 Go 中，都是通过自定义类型 + const + iota 来实现枚举，下面是一个简单的例子

```go
type Season uint8

const (
	Spring Season = iota
	Summer
	Autumn
	Winter
)
```

<br>

这些枚举实际上就是数字，Go 也不支持直接将其转换为字符串，但我们可以通过给自定义类型添加方法来返回其字符串表现形式，实现 Stringer 接口即可

```go
func (s Season) String() string {
	switch s {
	case Spring:
		return "spring"
	case Summer:
		return "summer"
	case Autumn:
		return "autumn"
	case Winter:
		return "winter"
	}
	return ""
}
```

<br>

这样一来就是一个简单的枚举实现了。也可以通过官方工具 Stringer 来自动生成枚举

不过它有以下缺点：

* 类型不安全，因为 Season 是自定义类型，可以通过强制类型转换将其他数字也转换成该类型
* 繁琐，字符串表现形式需要自己实现
* 表达能力弱，因为 const 仅支持基本数据类型，所以这些枚举值也只能用字符串和数字来进行表示



------

# 变量



变量是用于保存一个值的存储位置，允许其存储的值在运行时动态的变化。每声明一个变量，都会为其分配一块内存以存储对应类型的值



------

## 声明



在 Go 中的类型声明是后置的，变量的声明会用到 var 关键字，格式为 var 变量名 类型名，变量名的命名规则必须遵守标识符的命名规则

```go
var intNum int
var str string
var char byte
```

当要声明多个相同类型的变量时，可以只写一次类型

```go
var numA, numB, numC int
```

当要声明多个不同类型的变量时，可以使用 () 进行包裹，可以存在多个 ()

```go
var (
	name    string
	age     int
	address string
)

var (
	school string
	class int
) 
```

一个变量如果只是声明而不赋值，那么变量存储的值就是对应类型的零值



------

## 赋值



赋值会用到运算符 =，例如

```go
var name string
name = "jack"
```

也可以声明的时候直接赋值

```go
var name string = "jack"
```

或者这样也可以

```go
var name string
var age int
name, age = "jack", 1
```

<br>

第二种方式每次都要指定类型，可以使用官方提供的语法糖：短变量初始化，可以省略掉 var 关键字和后置类型，具体是什么类型交给编译器自行推断

```go
name := "jack" // 字符串类型的变量。
```

虽然可以不用指定类型，但是在后续赋值时，类型必须保持一致，下面这种代码无法通过编译

```go
a := 1
a = "1"
```

还需要注意的是，短变量初始化不能使用 nil，因为 nil 不属于任何类型，编译器无法推断其类型

```go
name := nil // 无法通过编译
```

短变量声明可以批量初始化

```go
name, age := "jack", 1
```

短变量声明方式无法对一个已存在的变量使用，比如

```go
// 错误示例
var a int
a := 1

// 错误示例
a := 1
a := 2
```

但是有一种情况除外，那就是在赋值旧变量的同时声明一个新的变量，比如

```go
a := 1
a, b := 2, 2
```

这种代码是可以通过编译的，变量 a 被重新赋值，而 b 是新声明的

<br>

在 Go 语言中，有一个规则，那就是所有在函数中的变量都必须要被使用，比如下面的代码只是声明了变量，但没有使用它

```go
func main() {
	a := 1
}
```

那么在编译时就会报错，提示这个变量声明了但没有使用

```
a declared and not used
```

这个规则仅适用于函数内的变量，对于函数外的包级变量则没有这个限制，下面这个代码就可以通过编译

```go
var a = 1

func main() {
	
}
```



------

## 匿名



用下划线可以表示不需要某一个变量

```go
Open(name string) (*File, error)
```

比如 os.Open 函数有两个返回值，只想要第一个，不想要第二个，可以按照下面这样写

```go
file, _ := os.Open("readme.txt")
```

未使用的变量是无法通过编译的，当不需要某一个变量时，就可以使用下划线 _ 代替



------

## 交换



在 Go 中，如果想要交换两个变量的值，不需要使用指针，可以使用赋值运算符直接进行交换，语法上看起来非常直观，例子如下

```go
num1, num2 := 25, 36
num1, num2 = num2, num1
```

三个变量也是同样如此

```go
num1, num2, num3 := 25, 36, 49
num1, num2, num3  = num3, num2, num1
```



思考下面这一段代码，这是计算斐波那契数列的一小段代码，三个变量在计算后的值分别是什么

```go
a, b, c := 0, 1, 1
a, b, c = b, c, a+b
```

答案是 1， 1， 1

因为 Go 会将 a, b, c 三个数的值分别计算好再赋给它们，就等同于下面这段代码

```go
a, b, c = 1, 1, 0+1
```



------

## 比较



变量之间的比较有一个大前提，那就是它们之间的类型必须相同，Go 语言中不存在隐式类型转换，像下面这样的代码是无法通过编译的

```go
func main() {
	var a uint64
	var b int64
	fmt.Println(a == b)
}
```

所以必须使用强制类型转换

```go
func main() {
	var a uint64
	var b int64
	fmt.Println(int64(a) == b)
}
```

<br>

在没有泛型之前，早期 Go 提供的内置 min，max 函数只支持浮点数，到了 1.21 版本，Go 才终于将这两个内置函数用泛型重写，现在可以使用 min 函数比较最小值，使用 max 函数比较最大值

```go
minVal := min(1, 2, -1, 1.2)
maxVal := max(100, 22, -1, 1.12)
```

它们的参数支持所有的可比较类型，Go 中的可比较类型有

* 布尔
* 数字
* 字符串
* 指针
* 通道 （仅支持判断是否相等）
* 元素是可比较类型的数组（切片不可比较）
* 字段类型都是可比较类型的结构体（仅支持判断是否相等）

<br>

除此之外，还可以通过导入标准库 cmp 来判断，不过仅支持有序类型的参数，在 Go 中内置的有序类型只有数字和字符串

```go
import "cmp"

func main() {
	cmp.Compare(1, 2)
	cmp.Less(1, 2)
}
```



------

## 代码块



在函数内部，可以通过花括号建立一个代码块，代码块彼此之间的变量作用域是相互独立的。例如下面的代码

```go
func main() {
	a := 1
	
	{
		a := 2
		fmt.Println(a)
	}
	
	{
		a := 3
		fmt.Println(a)
	}
	fmt.Println(a)
}
```

输出是

```
2
3
1
```

<br>

块与块之间的变量相互独立，不受干扰，无法访问，但是会受到父块中的影响

```go
func main() {
	a := 1

	{
		a := 2
		fmt.Println(a)
	}

	{
		fmt.Println(a)
	}
	fmt.Println(a)
}
```

输出是

```go
2
1
1
```



------

# 条件控制



在 Go 中，条件控制语句总共有三种 if，switch，select。select 相对前两者而言比较特殊，并发的时候再做介绍



------

## if  else



if else  至多两个判断分支，语句格式如下

```go
if expression {

}

或者

if expression {

}else {

}
```

if 语句也可以包含一些简单的语句，例如：

```go
func main() {
	if x := 1 + 1; x > 2 {
		fmt.Println(x)
	}
}
```



------

## else if



else if  语句可以在 if else 的基础上创建更多的判断分支，语句格式如下：

```go
if expression1 {

}else if expression2 {

}else if expression3 {

}else {

}
```



------

## switch



switch 语句也是一种多分支的判断语句，语句格式如下：

```go
switch expr {
	case case1:
		statement1
	case case2:
		statement2
	default:
		default statement
}
```

通过 fallthrough 关键字来继续执行相邻的下一个分支

```go
func main() {
   num := 2
   switch {
   case num >= 0 && num <= 1:
      num++
   case num > 1:
      num--
      fallthrough // 执行完该分支后，会继续执行下一个分支
   case num < 0:
      num += num
   }
   fmt.Println(num)
}
```



------

## label



标签语句，给一个代码块打上标签，可以是 goto，break，continue 的目标。例子如下：

```go
func main() {
	A: 
		a := 1
	B:
		b := 2
}
```

单纯的使用标签是没有任何意义的，需要结合其他关键字来进行使用



------

## goto



goto 将控制权传递给在同一函数中对应标签的语句，示例如下：

```go
func main() {
   a := 1
   if a == 1 {
      goto A
   } else {
      fmt.Println("b")
   }
A:
   fmt.Println("a")
}
```

在实际应用中 goto 用的很少，跳来跳去的很降低代码可读性，性能消耗也是一个问题



------

# 循环控制



Go 中，有仅有一种循环语句：for，Go 抛弃了 while 语句，for 语句可以被当作 while 来使用



------

## for



语句格式如下

```go
for init statement; expression; post statement {
	execute statement
}
```

当只保留循环条件时，就变成了 while

```go
for expression {
	execute statement
}                
```

这是一个死循环，永远也不会退出

```go
for {
	execute statement
}
```



------

## for range



for range 可以更加方便的遍历一些可迭代的数据结构，如数组，切片，字符串，映射表，通道。语句格式如下：

```go
for index, value := range iterable {
	// body
}
```

index 为可迭代数据结构的索引，value 则是对应索引下的值，例如使用 for range 遍历一个字符串

```go
func main() {
   sequence := "hello world"
   for index, value := range sequence {
      fmt.Println(index, value)
   }
}
```

<br>

for range 也可以迭代一个整型值，字面量，常量，变量都是有效的

```go
// 以下三种写法都会输出从 0 到 9 的值
for i := range 10 {
    fmt.Println(i)
}

n := 10
for i := range n {
    fmt.Println(i)
}

const n = 10
for i := range n {
	fmt.Println(i)
}
```



------

## break



break 关键字会终止最内层的 for 循环，结合标签一起使用可以达到终止外层循环的效果，例子如下：

```go
func main() {
	for i := 0; i < 10; i++ {
		for j := 0; j < 10; j++ {
			if i <= j {
				break
			}
			fmt.Println(i, j)
		}
	}
}
```



------

## continue



continue 关键字会跳过最内层循环的本次迭代，直接进入下一次迭代，结合标签使用可以达到跳过外层循环的效果，例子如下：

```go
func main() {
	for i := 0; i < 10; i++ {
		for j := 0; j < 10; j++ {
			if i > j {
				continue
			}
			fmt.Println(i, j)
		}
	}
}
```



------

# 输入输出

## 输出



**stdout**

因为标准输出本身就是一个文件，所以可以直接将字符串写入到标准输出中

```go
package main

import "os"

func main() {
	os.Stdout.WriteString("hello world!")
}
```

<br>

**print**

Go 有两个内置的函数 print，println，他们会将参数输出到标准错误中，仅做调试用，一般不推荐使用

```go
package main

func main() {
	print("hello world!\n")
	println("hello world")
}
```

<br>

**fmt**

最常见的用法是使用 fmt 包，它提供了 fmt.Println 函数，该函数默认会将参数输出到标准输出中

```go
package main

import "fmt"

func main() {
	fmt.Println("hello world!")
}
```

它的参数支持任意类型，如果类型实现了 String 接口也会调用 String 方法来获取其字符串表现形式，所以它输出的内容可读性比较高，适用于大部分情况，不过由于内部用到了反射，在性能敏感的场景不建议大量使用

<br>

**bufio**

bufio 提供了可缓冲的输出方法，它会先将数据写入到内存中，积累到了一定阈值再输出到指定的 Writer 中，默认缓冲区大小是 4KB。在文件 IO，网络 IO 的时候建议使用这个包

```go
func main() {
	writer := bufio.NewWriter(os.Stdout)
	defer writer.Flush()
	writer.WriteString("hello world!")
}
```

也可以把它和 fmt 包结合起来用

```go
func main() {
	writer := bufio.NewWriter(os.Stdout)
	defer writer.Flush()
	fmt.Fprintln(writer, "hello world!")
}
```

<br>

**格式化**

Go 中的格式化输出功能基本上由 fmt.Printf 函数提供

```go
func main() {
	fmt.Printf("hello world, %s!", "jack")
}
```



------

## 输入



**read**

可以像直接读文件一样，读取输入内容，如下

```go
func main() {
	var buf [1024]byte
	n, _ := os.Stdin.Read(buf[:])
	os.Stdout.Write(buf[:n])
}
```

这样用起来太麻烦了，一般不推荐使用

<br>

**fmt**

可以使用 fmt 包提供的几个函数，用起来跟 C 差不多

```go
// 扫描从os.Stdin读入的文本，根据空格分隔，换行也被当作空格
func Scan(a ...any) (n int, err error) 

// 与Scan类似，但是遇到换行停止扫描
func Scanln(a ...any) (n int, err error)

// 根据格式化的字符串扫描
func Scanf(format string, a ...any) (n int, err error)
```

读两个数字

```go
func main() {
	var a, b int
	fmt.Scanln(&a, &b)
	fmt.Printf("%d + %d = %d\n", a, b, a+b)
}
```

读取固定长度的数组

```go
func main() {
	n := 10
	s := make([]int, n)
	for i := range n {
		fmt.Scan(&s[i])
	}
	fmt.Println(s)
}
```

<br>

**bufio**

在有大量输入需要读取的时候，就建议使用 bufio.Reader 来进行内容读取

```go
func main() {
    reader := bufio.NewReader(os.Stdin)
    var a, b int
    fmt.Fscanln(reader, &a, &b)
    fmt.Printf("%d + %d = %d\n", a, b, a+b)
}
```

<br>

**scanner**

bufio.Scanner 与 bufio.Reader 类似，不过它是按行读取的

```go
func main() {
	scanner := bufio.NewScanner(os.Stdin)
	for scanner.Scan() {
		line := scanner.Text()
		if line == "exit" {
			break
		}
		fmt.Println("scan", line)
	}
}
```



------

# 切片和数组



在 Go 中，数组和切片两者看起来长得几乎一模一样，但功能有着不小的区别，数组是定长的数据结构，长度被指定后就不能被改变，而切片是不定长的，切片在容量不够时会自行扩容



------

## 数组



如果事先就知道了要存放数据的长度，且后续使用中不会有扩容的需求，就可以考虑使用数组，Go 中的数组是值类型，而非引用，并不是指向头部元素的指针

数组作为值类型，将数组作为参数传递给函数时，由于 Go 函数是传值传递，所以会将整个数组拷贝

<br>

**初始化**

数组在声明是长度只能是一个常量，不能是变量

```go
// 正确示例
var a [5]int

// 错误示例
l := 1
var b [l]int
```

<br>

可以通过以下方式来初始化一个数组

```go
// 初始化一个长度为 5 的整型数组
var nums [5]int

// 也可以用元素初始化
nums := [5]int{1, 2, 3}

// 还可以通过 new 函数获得一个指针
nums := new([5]int)
```

以上几种方式都会给 nums 分配一片固定大小的内存，区别只是最后一种得到的值是指针

<br>

**使用**

只要有数组名和下标，就可以访问数组中对应的元素

```go
fmt.Println(nums[0])
```

同样的也可以修改数组元素

```go
nums[0] = 1
```

还可以通过内置函数 len 来访问数组元素的数量

```go
len(nums)
```

内置函数 cap 来访问数组容量，数组的容量等于数组长度，容量对于切片才有意义

```go
cap(nums)
```

<br>

**切割**

切割数组的格式为 arr[startIndex:endIndex]，切割的区间为左闭右开，例子如下：

```go
nums := [5]int{1, 2, 3, 4, 5}
nums[1:] // 子数组范围[1,5) -> [2 3 4 5]
nums[:5] // 子数组范围[0,5) -> [1 2 3 4 5]
nums[2:3] // 子数组范围[2,3) -> [3]
nums[1:3] // 子数组范围[1,3) -> [2 3]
```

数组在切割后，就会变为切片类型

```go
func main() {
	arr := [5]int{1, 2, 3, 4, 5}
	fmt.Printf("%T\n", arr) // [5]int
	fmt.Printf("%T\n", arr[1:2]) // []int
}
```

若要将数组转换为切片类型，不带参数进行切片即可，转换后的切片与原数组指向的是同一片内存，修改切片会导致原数组内容的变化

```go
func main() {
	arr := [5]int{1, 2, 3, 4, 5}
	slice := arr[:]
	slice[0] = 0
	fmt.Printf("array: %v\n", arr) // array: [0 2 3 4 5]
	fmt.Printf("slice: %v\n", slice) // slice: [0 2 3 4 5]
}
```

如果要对转换后的切片进行修改，建议使用下面这种方式进行转换

```go
func main() {
	arr := [5]int{1, 2, 3, 4, 5}
	slice := slices.Clone(arr[:])
	slice[0] = 0
	fmt.Printf("array: %v\n", arr) // array: [1 2 3 4 5]
	fmt.Printf("slice: %v\n", slice) // slice: [0 2 3 4 5]
}
```



------

## 切片



切片在 Go 中的应用范围要比数组广泛的多，它用于存放不知道长度的数据，且后续使用过程中可能会频繁的插入和删除元素

<br>

**初始化**

切片的初始化方式有以下几种

```go
var nums []int // 值
nums := []int{1, 2, 3} // 值
nums := make([]int, 0, 0) // 值
nums := new([]int) // 指针
```

可以看到切片与数组在外貌上的区别，仅仅只是少了一个初始化长度。通常情况下，推荐使用 make 来创建一个空切片，只是对于切片而言，make 函数接收三个参数：类型，长度，容量。举个例子解释一下长度与容量的区别，假设有一桶水，水并不是满的，桶的高度就是桶的容量，代表着总共能装多少高度的水，而桶中水的高度就是代表着长度，水的高度一定小于等于桶的高度，否则水就溢出来了。所以，切片的长度代表着切片中元素的个数，切片的容量代表着切片总共能装多少个元素，切片与数组最大的区别在于切片的容量会自动扩张，而数组不会

切片的底层实现依旧是数组，是引用类型，可以简单理解为是指向底层数组的指针

通过 var nums []int 这种方式声明的切片，默认值为 nil，所以不会为其分配内存，而在使用 make 进行初始化时，建议预分配一个足够的容量，可以有效减少后续扩容的内存消耗

<br>

**使用**

切片的基本使用与数组完全一致，区别只是切片可以动态变化长度，下面看几个例子

切片可以通过 append 函数实现许多操作，函数签名如下，slice 是要添加元素的目标切片，elems 是待添加的元素，返回值是添加后的切片

```go
func append(slice []Type, elems ...Type) []Type
```

首先创建一个长度为 0，容量为 0 的空切片，然后在尾部插入一些元素，最后输出长度和容量

```go
nums := make([]int, 0, 0)
nums = append(nums, 1, 2, 3, 4, 5, 6, 7)
fmt.Println(len(nums), cap(nums)) // 7 8 可以看到长度与容量并不一致
```

新 slice 预留的 buffer 容量 大小是有一定规律的。 在 golang1.18 版本更新之前网上大多数的文章都是这样描述 slice 的扩容策略的： 当原 slice 容量小于 1024 的时候，新 slice 容量变成原来的 2 倍；原 slice 容量超过 1024，新 slice 容量变成原来的 1.25 倍。 在 1.18 版本更新之后，slice 的扩容策略变为了： 当原 slice 容量 (oldcap) 小于 256 的时候，新 slice(newcap)容量为原来的 2 倍；原 slice 容量超过 256，新 slice 容量 newcap = oldcap+(oldcap+3*256)/4

<br>

**插入元素**

切片元素的插入也是需要结合 append 函数来使用，现有切片如下

```go
nums := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

从头部插入元素

```go
nums = append([]int{-1, 0}, nums...)
fmt.Println(nums) // [-1 0 1 2 3 4 5 6 7 8 9 10]
```

从中间下标 i 插入元素

```go
nums = append(nums[:i+1], append([]int{999, 999}, nums[i+1:]...)...)
fmt.Println(nums) // i=3，[1 2 3 4 999 999 5 6 7 8 9 10]
```

从尾部插入元素，就是 append 最原始的用法

```go
nums = append(nums, 99, 100)
fmt.Println(nums) // [1 2 3 4 5 6 7 8 9 10 99 100]
```

<br>

**删除元素**

切片元素的删除需要结合 append 函数来使用，现有如下切片

```go
nums := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

从头部删除 n 个元素

```go
nums = nums[n:]
fmt.Println(nums) //n=3 [4 5 6 7 8 9 10]
```

从尾部删除 n 个元素

```go
nums = nums[:len(nums)-n]
fmt.Println(nums) //n=3 [1 2 3 4 5 6 7]
```

从中间指定下标 i 位置开始删除 n 个元素

```go
nums = append(nums[:i], nums[i+n:]...)
fmt.Println(nums)// i=2，n=3，[1 2 6 7 8 9 10]
```

删除所有元素

```go
nums = nums[:0]
fmt.Println(nums) // []
```

<br>

**拷贝**

切片在拷贝时需要确保目标切片有足够的长度，例如

```go
func main() {
	dest := make([]int, 0)
	src := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	fmt.Println(src, dest)
	fmt.Println(copy(dest, src))
	fmt.Println(src, dest)
}
```

```go
[1 2 3 4 5 6 7 8 9] []
0                     
[1 2 3 4 5 6 7 8 9] []
```

将长度修改为 10，输出如下

```go
[1 2 3 4 5 6 7 8 9] [0 0 0 0 0 0 0 0 0 0]
9                                        
[1 2 3 4 5 6 7 8 9] [1 2 3 4 5 6 7 8 9 0]
```

<br>

**遍历**

切片的遍历与数组完全一致，for 循环

```go
func main() {
   slice := []int{1, 2, 3, 4, 5, 7, 8, 9}
   for i := 0; i < len(slice); i++ {
      fmt.Println(slice[i])
   }
}
```

for range 循环

```go
func main() {
	slice := []int{1, 2, 3, 4, 5, 7, 8, 9}
	for index, val := range slice {
		fmt.Println(index, val)
	}
}
```



------

## 多维切片



这是官方文档对二维切片的解释：[Effective Go - 二维切片](https://go.dev/doc/effective_go#two_dimensional_slices)

```go
var nums [5][5]int
for _, num := range nums {
   fmt.Println(num)
}
fmt.Println()
slices := make([][]int, 5)
for _, slice := range slices {
   fmt.Println(slice)
}
```

输出结果为：

```
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]

[]
[]
[]
[]
[]
```

可以看到，同样是二维的数组和切片，其内部结构是不一样的。数组在初始化时，其一维和二维的长度早已固定，而切片的长度是不固定的，切片中的每一个切片长度都可能是不相同的，所以必须要单独初始化，切片初始化部分修改为如下代码即可

```go
slices := make([][]int, 5)
for i := 0; i < len(slices); i++ {
   slices[i] = make([]int, 5)
}
```

最终输出结果为：

```
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]

[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
[0 0 0 0 0]
```



------

## 扩展表达式



切片与数组都可以使用简单表达式来进行切割，但是拓展表达式只有切片能够使用，该特性于 Go1.2 版本添加，主要是为了解决切片共享底层数组的读写问题，主要格式为如下，需要满足关系 low<= high <= max <= cap，使用拓展表达式切割的切片容量为 max-low

```go
slice[low:high:max]
```

low 与 high 依旧是原来的含义不变，而多出来的 max 则指的是最大容量，例如下方的例子中省略了 max，那么 s2 的容量就是 cap(s1)-low

```go
s1 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9} // cap = 9
s2 := s1[3:4] // cap = 9 - 3 = 6
```

那么这么做就会有一个明显的问题，s1 与 s2 是共享的同一个底层数组，在对 s2 进行读写时，有可能会影响的 s1 的数据，下列代码就属于这种情况

```go
s1 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9} // cap = 9
s2 := s1[3:4]                          // cap = 9 - 3 = 6
// 添加新元素，由于容量为6.所以没有扩容，直接修改底层数组
s2 = append(s2, 1)
fmt.Println(s2)
fmt.Println(s1)
```

最终输出为

```
[4 1]
[1 2 3 4 1 6 7 8 9]
```

可以看到明明是向 s2 添加元素，却连 s1 也一起修改了，拓展表达式就是为了解决此类问题而生的，只需要稍微修改一下就能解决该问题

```go
func main() {
   s1 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9} // cap = 9
   s2 := s1[3:4:4]                        // cap = 4 - 3 = 1
   // 容量不足，分配新的底层数组
   s2 = append(s2, 1)
   fmt.Println(s2)
   fmt.Println(s1)
}
```

现在得到的就是正常的了

```
[4 1]
[1 2 3 4 5 6 7 8 9]
```



------

## clear



在 Go1.21 新增了 clear 内置函数，clear 会将切片内所有的值置为零值

```go
package main

import (
    "fmt"
)

func main() {
    s := []int{1, 2, 3, 4}
    clear(s)
    fmt.Println(s)
}
```

输出

```
[0 0 0 0]
```

如果想要清空切片，可以

```go
func main() {
	s := []int{1, 2, 3, 4}
    s = s[:0:0]
	fmt.Println(s)
}
```

限制了切割后的容量，这样可以避免覆盖原切片的后续元素



------

# 字符串



在 Go 中，字符串本质上是一个不可变的只读的字节数组，也是一片连续的内存空间



------

## 字面量



普通字符串由 "" 双引号表示，支持转义，不支持多行书写，下列是一些普通字符串

```go
"这是一个普通字符串\n"
"abcdefghijlmn\nopqrst\t\\uvwxyz"
```

```
这是一个普通字符串
abcdefghijlmn
opqrst  \uvwxyz
```

<br>

原生字符串由反引号表示，不支持转义，支持多行书写，原生字符串里面所有的字符都会原封不动的输出，包括换行和缩进

```go
`这是一个原生字符串，换行
	tab缩进，\t制表符但是无效,换行
	"这是一个普通字符串"
	
	结束
`
```

```
这是一个原生字符串，换行
        tab缩进，\t制表符但是无效,换行
        "这是一个普通字符串"

        结束
```



------

## 访问



因为字符串本质是字节数组，所以字符串的访问形式跟数组切片完全一致，例如访问字符串第一个元素

```go
func main() {
   str := "this is a string"
   fmt.Println(str[0]) 
}
```

输出是字节而不是字符

```
116
```

切割字符串

```
func main() {
   str := "this is a string"
   fmt.Println(string(str[0:4])) // this
}
```

尝试修改字符串元素

```go
func main() {
   str := "this is a string"
   str[0] = 'a' // 无法通过编译
   fmt.Println(str)
}
```

虽然没法修改字符串，但是可以覆盖

```go
func main() {
   str := "this is a string"
   str = "that is a string"
   fmt.Println(str)
}
```

```
that is a string
```



------

## 转换



在 Go 中，字符串转换为切片是深拷贝，而切片转换为字符串则是浅拷贝

<br>

字符串可以转换为字节切片，而字节切片或字节数组也可以转换为字符串，例子如下：

```go
func main() {
   str := "this is a string"
   // 显式类型转换为字节切片
   bytes := []byte(str)
   fmt.Println(bytes)
   // 显式类型转换为字符串
   fmt.Println(string(bytes))
}
```

字符串的内容是只读的不可变的，无法修改，但是字节切片是可以修改的

```go
func main() {
	str := "this is a string"
	fmt.Println(&str)
	bytes := []byte(str)
    // 修改字节切片
	bytes = append(bytes, 96, 97, 98, 99)
    // 赋值给原字符串
	str = string(bytes)
	fmt.Println(str)
}
```

将字符串转换成字节切片以后，两者之间毫无关联，因为 Go 会新分配一片内存空间给字节切片，再将字符串的内存复制过去，对字节切片进行修改不会对原字符串产生任何影响，这么做是为了内存安全

在这种情况下，如果要转换的字符串或字节切片很大，那么性能开销就会很高。不过你也可以通过 unsafe 库来实现无复制转换，不过背后的安全问题需要自己承担，比如下面的例子，b1 和 s1 的地址是一样的

```go
func main() {
	s1 := "hello world"
	b1 := unsafe.Slice(unsafe.StringData(s1), len(s1))
	fmt.Printf("%p %p", unsafe.StringData(s1), unsafe.SliceData(b1)) // 0xe27bb2 0xe27bb2
}
```



------

## 拷贝



类似数组切片的拷贝方式，字符串拷贝其实是字节切片拷贝，属于深拷贝，使用内置函数 copy

```go
func main() {
   var dst, src string
   src = "this is a string"
   desBytes := make([]byte, len(src))
   copy(desBytes, src)
   dst = string(desBytes)
   fmt.Println(src, dst)
}
```

也可以使用 strings.clone 函数，但其实内部实现都差不多

```go
func main() {
   var dst, src string
   src = "this is a string"
   dst = strings.Clone(src)
   fmt.Println(src, dst)
}
```



------

## 拼接



字符串的拼接使用 + 操作符

```go
func main() {
   str := "this is a string"
   str = str + " that is a int"
   fmt.Println(str)
}
```

也可以转换为字节切片再进行添加元素

```go
func main() {
   str := "this is a string"
   bytes := []byte(str)
   bytes = append(bytes, "that is a int"...)
   str = string(bytes)
   fmt.Println(str)
}
```

以上两种拼接方式性能都很差，一般情况下可以使用，但如果对应性能有更高要求，可以使用 strings.Builder

```go
func main() {
   builder := strings.Builder{}
   builder.WriteString("this is a string ")
   builder.WriteString("that is a int")
   fmt.Println(builder.String())
}
```

```
this is a string that is a int
```



------

## strings



Go 提供了官方的 strings 标准库，[strings package - strings - Go Packages](https://pkg.go.dev/strings)

以下是一些常用的方法：

```go
strings.Builder // 与 Java 的 StringBuilder 类似
ToUpper(s string) string 
ToLower(s string) string // 将字符串转为大写（或小写），该函数会返回一个新的字符串
== // 与 Java 不同，Go 使用 == 比较两个字符串的时候比较的是内容而不是内存地址，所以比较的话直接使用 == 即可
qualFold(s, t string) bool // 不区分大小写比较
Replace(s, old, new string, n int) string // 替换字符串，参数分别是原字符串、需要替换的字符串、替换后的字符串、需要替换即几个，小于 0 表示全部
Split(s, sep string) []string // 分割字符串
TrimSpace(s string) string // 去掉头尾的空格
HasPrefix(s, prefix string) bool
HasSuffix(s, prefix string) bool // 判断字符串是否以指定内容开头（或结尾）
Join(elems []string, sep string) string // 将字符串切片中的元素以指定字符串连接生成新字符串
Contains(s, substr string) bool // 查找子串是否存在于指定的字符串中
```



------

# 映射表



一般来说，映射表数据结构实现通常有两种，哈希表 (hash table) 和搜索树(search tree)，区别在于前者无序，后者有序。在 Go 中，map 的实现是基于哈希桶(也是一种哈希表)，所以也是无序的



------

## 初始化



在 Go 中，map 的键类型必须是可比较的，比如 string ，int 是可比较的，而 []int 是不可比较的，也就无法作为 map 的键。初始化一个 map 有两种方法，第一种是字面量，格式如下：

```go
map[keyType]valueType{}
```

举几个例子

```go
mp := map[int]string{
   0: "a",
   1: "a",
   2: "a",
   3: "a",
   4: "a",
}

mp := map[string]int{
   "a": 0,
   "b": 22,
   "c": 33,
}
```

第二种方法是使用内置函数 make，对于 map 而言，接收两个参数，分别是类型与初始容量，例子如下：

```go
mp := make(map[string]int, 8)

mp := make(map[string][]int, 10)
```

map 是引用类型，零值或未初始化的 map 可以访问，但是无法存放元素，所以必须要为其分配内存

```go
func main() {
   var mp map[string]int
   mp["a"] = 1
   fmt.Println(mp)
}
```

```
panic: assignment to entry in nil map 
```



------

### 访问



访问一个 map 的方式就像通过索引访问一个数组一样

```go
func main() {
	mp := map[string]int{
		"a": 0,
		"b": 1,
		"c": 2,
		"d": 3,
	}
	fmt.Println(mp["a"])
	fmt.Println(mp["b"])
	fmt.Println(mp["d"])
	fmt.Println(mp["f"])
}
```

```
0
1
3
0
```

通过代码可以观察到，即使 map 中不存在 "f" 这一键值对，但依旧有返回值。map 对于不存的键其返回值是对应类型的零值，并且在访问 map 的时候其实有两个返回值，第一个返回值对应类型的值，第二个返回值一个布尔值，代表键是否存在，例如：

```go
func main() {
   mp := map[string]int{
      "a": 0,
      "b": 1,
      "c": 2,
      "d": 3,
   }
   if val, exist := mp["f"]; exist {
      fmt.Println(val)
   } else {
      fmt.Println("key不存在")
   }
}
```

对 map 求长度

```go
func main() {
   mp := map[string]int{
      "a": 0,
      "b": 1,
      "c": 2,
      "d": 3,
   }
   fmt.Println(len(mp))
}
```



------

## 存值



map 存值的方式也类似数组存值一样，例如：

```go
func main() {
   mp := make(map[string]int, 10)
   mp["a"] = 1
   mp["b"] = 2
   fmt.Println(mp)
}
```

存值时使用已存在的键会覆盖原有的值

```go
func main() {
   mp := make(map[string]int, 10)
   mp["a"] = 1
   mp["b"] = 2
   if _, exist := mp["b"]; exist {
      mp["b"] = 3
   }
   fmt.Println(mp)
}
```

但是也存在一个特殊情况，那就是键为 math.NaN() 时

```go
func main() {
	mp := make(map[float64]string, 10)
	mp[math.NaN()] = "a"
	mp[math.NaN()] = "b"
	mp[math.NaN()] = "c"
	_, exist := mp[math.NaN()]
	fmt.Println(exist)
	fmt.Println(mp)
}
```

```go
false
map[NaN:c NaN:a NaN:b]
```

通过结果可以观察到相同的键值并没有覆盖，反而还可以存在多个，也无法判断其是否存在，也就无法正常取值。因为 NaN 是 IEE754 标准所定义的，其实现是由底层的汇编指令 UCOMISD 完成，这是一个无序比较双精度浮点数的指令，该指令会考虑到 NaN 的情况，因此结果就是任何数字都不等于 NaN，NaN 也不等于自身，这也造成了每次哈希值都不相同。关于这一点社区也曾激烈讨论过，但是官方认为没有必要去修改，所以应当尽量避免使用 NaN 作为 map 的键



------

## 删除



```go
func delete(m map[Type]Type1, key Type)
```

删除一个键值对需要用到内置函数 delete，例如

```go
func main() {
   mp := map[string]int{
      "a": 0,
      "b": 1,
      "c": 2,
      "d": 3,
   }
   fmt.Println(mp)
   delete(mp, "a")
   fmt.Println(mp)
}
```

```
map[a:0 b:1 c:2 d:3]
map[b:1 c:2 d:3]
```

需要注意的是，如果值为 NaN，甚至没法删除该键值对

```go
func main() {
   mp := make(map[float64]string, 10)
   mp[math.NaN()] = "a"
   mp[math.NaN()] = "b"
   mp[math.NaN()] = "c"
   fmt.Println(mp)
   delete(mp, math.NaN())
   fmt.Println(mp)
}
```

```
map[NaN:c NaN:a NaN:b]
map[NaN:c NaN:a NaN:b]
```



------

## 遍历



通过 for range 可以遍历 map，例如

```go
func main() {
   mp := map[string]int{
      "a": 0,
      "b": 1,
      "c": 2,
      "d": 3,
   }
   for key, val := range mp {
      fmt.Println(key, val)
   }
}
```

```
c 2
d 3
a 0
b 1
```

可以看到结果并不是有序的，也印证了 map 是无序存储。值得一提的是，NaN 虽然没法正常获取，但是可以通过遍历访问到，例如

```go
func main() {
   mp := make(map[float64]string, 10)
   mp[math.NaN()] = "a"
   mp[math.NaN()] = "b"
   mp[math.NaN()] = "c"
   for key, val := range mp {
      fmt.Println(key, val)
   }
}
```

```
NaN a
NaN c
NaN b
```



------

## 清空



在 go1.21 之前，想要清空 map，就只能对每一个 map 的 key 进行 delete

```go
func main() {
	m := map[string]int{
		"a": 1,
		"b": 2,
	}
	for k, _ := range m {
		delete(m, k)
	}
	fmt.Println(m)
}
```

但是 go1.21 更新了 clear 函数，就不用再进行之前的操作了，只需要一个 clear 就可以清空

```go
func main() {
	m := map[string]int{
		"a": 1,
		"b": 2,
	}
	clear(m)
	fmt.Println(m)
}
```

输出

```
map[]
```



------

# Set



Set 是一种无序的，不包含重复元素的集合，Go 中并没有提供类似的数据结构实现，但是 map 的键正是无序且不能重复的，所以也可以使用 map 来替代 set

```
func main() {
	set := make(map[int]struct{}, 10)
	for i := 0; i < 10; i++ {
		set[rand.Intn(100)] = struct{}{}
	}
	fmt.Println(set)
}
```

```
ap[0:{} 18:{} 25:{} 40:{} 47:{} 56:{} 59:{} 81:{} 87:{}]
```



------

## 并发



map 并不是一个并发安全的数据结构，Go 团队认为大多数情况下 map 的使用并不涉及高并发的场景，引入互斥锁会极大的降低性能，map 内部有读写检测机制，如果冲突会触发 fatal error。例如下列情况有非常大的可能性会触发 fatal

```go
func main() {

   group.Add(10)
   // map
   mp := make(map[string]int, 10)
   for i := 0; i < 10; i++ {
      go func() {
         // 写操作
         for i := 0; i < 100; i++ {
            mp["helloworld"] = 1
         }
         // 读操作
         for i := 0; i < 10; i++ {
            fmt.Println(mp["helloworld"])
         }
         group.Done()
      }()
   }
   group.Wait()
}
```

```
fatal error: concurrent map writes
```

在这种情况下，需要使用 sync.Map 来替代



------

# 指针



Go 保留了指针，在一定程度上保证了性能，同时为了更好的 GC 和安全考虑，又限制了指针的使用



------

## 创建



关于指针有两个常用的操作符，一个是取地址符 &，另一个是解引用符 *。对一个变量进行取地址，会返回对应类型的指针，例如：[]()

```go
func main() {
   num := 2
   p := &num
   fmt.Println(p)
}
```

指针存储的是变量 num 的地址

```go
0xc00001c088
```

<br>

解引用符则有两个用途，第一个是访问指针所指向的元素，也就是解引用，例如

```go
func main() {
	num := 2
	p := &num
	rawNum := *p
	fmt.Println(rawNum)
}
```

p 是一个指针，对指针类型解引用就能访问到指针所指向的元素。还有一个用途就是声明一个指针，例如：

```go
func main() {
   var numPtr *int
   fmt.Println(numPtr) // <nil>
}
```

*int 即代表该变量的类型是一个 int 类型的指针，不过指针不能光声明，还得初始化，需要为其分配内存，否则就是一个空指针，无法正常使用。要么使用取地址符将其他变量的地址赋值给该指针，要么就使用内置函数 new 手动分配，例如：

```go
func main() {
   var numPtr *int
   numPtr = new(int)
   fmt.Println(numPtr)
}
```

更多的是使用短变量

```go
func main() {
   numPtr := *new(int)
   fmt.Println(numPtr)
}
```

new 函数只有一个参数那就是类型，并返回一个对应类型的指针，函数会为该指针分配内存，并且指针指向对应类型的零值，例如：

```go
func main() {
   fmt.Println(*new(string))
   fmt.Println(*new(int))
   fmt.Println(*new([5]int))
   fmt.Println(*new([]float64))
}
```

```
0          
[0 0 0 0 0]
[]   
```



------

## 禁止指针运算



在 Go 中是不支持指针运算的，也就是说指针无法偏移，下面这是一段 C++ 代码：

```go
int main() {
    int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    int *p = &arr[0];
    cout << &arr << endl
         << p << endl
         << p + 1 << endl
         << &arr[1] << endl;
}
```

```
0x31d99ff880
0x31d99ff880
0x31d99ff884
0x31d99ff884
```

可以看出数组的地址与数字第一个元素的地址一致，并且对指针加一运算后，其指向的元素为数组第二个元素。Go 中的数组也是如此，不过区别在于指针无法偏移，例如：

```go
func main() {
   arr := [5]int{0, 1, 2, 3, 4}
   p := &arr
   println(&arr[0])
   println(p)
   // 试图进行指针运算
   p++
   fmt.Println(p)
}
```

这样的程序将无法通过编译，报错如下

```
main.go:10:2: invalid operation: p++ (non-numeric type *[5]int)
```



------

## new 和 make



在前面已经很多次提到过内置函数 new 和 make，两者有点类似，但也有不同

```go
// 返回值是类型指针
// 接收参数是类型
// 专用于给指针分配内存空间
func new(Type) *Type
```

```go
// 返回值是值，不是指针
// 接收的第一个参数是类型，不定长参数根据传入类型的不同而不同
// 专用于给切片，映射表，通道分配内存
func make(t Type, size ...IntegerType) Type
```

下面是一些例子：

```go
new(int) // int指针
new(string) // string指针
new([]int) // 整型切片指针
make([]int, 10, 100) // 长度为10，容量100的整型切片 
make(map[string]int, 10) // 容量为10的映射表
make(chan int, 10) // 缓冲区大小为10的通道
```



------

# 函数



在 Go 中，函数是一等公民，函数是 Go 最基础的组成部分，也是 Go 的核心



------

## 声明



函数的声明格式如下：

```go
func 函数名([参数列表]) [返回值] {
	函数体
}
```

声明函数有两种办法，一种是通过 func 关键字直接声明，另一种就是通过 var 关键字来声明，如下所示：

```go
func sum(a int, b int) int {
	return a + b
}

var sum = func(a int, b int) int {
	return a + b
}
```

<br>

函数签名由函数名称，参数列表，返回值组成，下面是一个完整的例子，函数名称为 Sum，有两个 int 类型的参数 a，b，返回值类型为 int

```go
func Sum(a int, b int) int {
   return a + b
}
```

<br>



还有一个非常重要的点，即 Go 中的函数不支持重载，像下面的代码就无法通过编译

```go
type Person struct {
	Name    string
	Age     int
	Address string
	Salary  float64
}

func NewPerson(name string, age int, address string, salary float64) *Person {
	return &Person{Name: name, Age: age, Address: address, Salary: salary}
}

func NewPerson(name string) *Person {
	return &Person{Name: name}
}
```

Go 的理念便是：如果签名不一样那就是两个完全不同的函数，那么就不应该取一样的名字，函数重载会让代码变得混淆和难以理解。这种理念是否正确见仁见智，至少在 Go 中你可以仅通过函数名就知道它是干什么的，而不需要去找它到底是哪一个重载



------

## 参数



Go 中的参数名可以不带名称，一般这种是在接口或函数类型声明时才会用到，不过为了可读性一般还是建议尽量给参数加上名称

```go
type ExWriter func(io.Writer) error 

type Writer interface {
	ExWrite([]byte) (int, error)
}
```

<br>

对于类型相同的参数而言，可以只需要声明一次类型，不过条件是它们必须相邻

```go
func Log(format string, a1, a2 any) {
	...
}
```

<br>

变长参数可以接收 0 个或多个值，必须声明在参数列表的末尾，最典型的例子就是 fmt.Printf 函数

```go
func Printf(format string, a ...any) (n int, err error) {
	return Fprintf(os.Stdout, format, a...)
}
```

值得一提的是，Go 中的函数参数是传值传递，即在传递参数时会拷贝实参的值。如果你觉得在传递切片或 map 时会复制大量的内存，大可不必担心，因为这两个数据结构本质上都是指针



------

## 返回值



下面是一个简单的函数返回值的例子，Sum 函数返回一个 int 类型的值

```go
func Sum(a, b int) int {
   return a + b
}
```

当函数没有返回值时，不需要 void，不带返回值即可

```go
func ErrPrintf(format string, a ...any) {
	_, _ = fmt.Fprintf(os.Stderr, format, a...)
}
```

Go 允许函数有多个返回值，此时就需要用括号将返回值围起来

```go
func Div(a, b float64) (float64, error) {
	if a == 0 {
		return math.NaN(), errors.New("0不能作为被除数")
	}
	return a / b, nil
}
```

Go 也支持具名返回值，不能与参数名重复，使用具名返回值时，return 关键字可以不需要指定返回哪些值

```go
func Sum(a, b int) (ans int) {
	ans = a + b
	return
}
```

和参数一样，当有多个同类型的具名返回值时，可以省略掉重复的类型声明

```go
func SumAndMul(a, b int) (c, d int) {
	c = a + b
	d = a * b
	return
}
```

不管具名返回值如何声明，永远都是以 return 关键字后的值为最高优先级

```go
func SumAndMul(a, b int) (c, d int) {
	c = a + b
	d = a * b
    // c，d将不会被返回
	return a + b, a * b
}
```



------

## 匿名函数



匿名函数就是没有签名的函数，例如下面的函数 func(a, b int) int，它没有名称，所以我们只能在它的函数体后紧跟括号来进行调用

```go
func main() {
   func(a, b int) int {
      return a + b
   }(1, 2)
}
```

在调用一个函数时，当它的参数是一个函数类型时，这时名称不再重要，就可以直接传递一个匿名函数，如下所示

```go
type Person struct {
	Name   string
	Age    int
	Salary float64
}

func main() {
	people := []Person{
		{Name: "Alice", Age: 25, Salary: 5000.0},
		{Name: "Bob", Age: 30, Salary: 6000.0},
		{Name: "Charlie", Age: 28, Salary: 5500.0},
	}

	slices.SortFunc(people, func(p1 Person, p2 Person) int {
		if p1.Name > p2.Name {
			return 1
		} else if p1.Name < p2.Name {
			return -1
		}
		return 0
	})
}
```

这是一个自定义排序规则的例子，slices.SortFunc 接受两个参数，一个是切片，另一个就是比较函数，不考虑复用的话，就可以直接传递匿名函数



------

## 闭包



闭包（Closure）这一概念，在一些语言中又被称为 Lamda 表达式，与匿名函数一起使用，闭包 = 函数 + 环境引用，看下面一个例子：

```go
func main() {
	grow := Exp(2)
	for i := range 10 {
		fmt.Printf("2^%d=%d\n", i, grow())
	}
}

func Exp(n int) func() int {
	e := 1
	return func() int {
		temp := e
		e *= n
		return temp
	}
}
```

```
2^0=1
2^1=2
2^2=4
2^3=8
2^4=16
2^5=32
2^6=64
2^7=128
2^8=256
2^9=512
```

Exp 函数的返回值是一个函数，这里将称成为 grow 函数，每将它调用一次，变量 e 就会以指数级增长一次。grow 函数引用了 Exp 函数的两个变量：e 和 n，它们诞生在 Exp 函数的作用域内，在正常情况下随着 Exp 函数的调用结束，这些变量的内存会随着出栈而被回收。但是由于 grow 函数引用了它们，所以它们无法被回收，而是逃逸到了堆上，即使 Exp 函数的生命周期已经结束了，但变量 e 和 n 的生命周期并没有结束，在 grow 函数内还能直接修改这两个变量，grow 函数就是一个闭包函数

<br>

利用闭包，可以非常简单的实现一个求费波那契数列的函数，代码如下

```go
func main() {
    // 10个斐波那契数
	fib := Fib(10)
	for n, next := fib(); next; n, next = fib() {
		fmt.Println(n)
	}
}

func Fib(n int) func() (int, bool) {
	a, b, c := 1, 1, 2
	i := 0
	return func() (int, bool) {
		if i >= n {
			return 0, false
		} else if i < 2 {
			f := i
			i++
			return f, true
		}

		a, b = b, c
		c = a + b
		i++

		return a, true
	}
}
```



------

## 延迟调用



defer 关键字可以使得一个函数延迟一段时间调用，在函数返回之前这些 defer 描述的函数最后都会被逐个执行，看下面一个例子：

```go
func main() {
	Do()
}

func Do() {
	defer func() {
		fmt.Println("1")
	}()
	fmt.Println("2")
}
```

输出

```
2
1
```

因为 defer 是在函数返回前执行的，所以可以在 defer 中修改函数的返回值

```go
func main() {
	fmt.Println(sum(3, 5))
}

func sum(a, b int) (s int) {
	defer func() {
		s -= 10
	}()
	s = a + b
	return
}
```

当有多个 defer 描述的函数时，就会像栈一样先进后出的顺序执行

```go
func main() {
	fmt.Println(0)
	Do()
}

func Do() {
	defer fmt.Println(1)
	fmt.Println(2)
	defer fmt.Println(3)
	defer fmt.Println(4)
	fmt.Println(5)
}
```

输出

```
0
2
5
4
3
1
```

延迟调用通常用于释放文件资源，关闭网络连接等操作，还有一个用法是捕获 panic，在错误处理时会讲到

<br>

虽然没有明令禁止，一般建议不要在 for 循环中使用 defer

在 Go 中，每创建一个 defer，就需要在当前协程申请一片内存空间。如果在 for 循环中是一个较为复杂的数据处理流程，当外部请求数突然激增时，那么在短时间内就会创建大量的 defer，在循环次数很大或次数不确定时，就可能会导致内存占用突然暴涨，这种一般称之为内存泄漏



------

# 结构体



结构体可以存储一组不同类型的数据，是一种复合类型。Go 抛弃了类与继承，同时也抛弃了构造方法，刻意弱化了面向对象的功能，Go 并非是一个传统 OOP 的语言，但是 Go 依旧有着 OOP 的影子，通过结构体和方法也可以模拟出一个类。下面是一个简单的结构体的例子：

```go
type Programmer struct {
	Name     string
	Age      int
	Job      string
	Language []string
}
```



------

## 声明



结构体的声明非常简单，例子如下：

```go
type Person struct {
   name string
   age int
}
```

结构体本身以及其内部的字段都遵守大小写命名的暴露方式。对于一些类型相同的相邻字段，可以不需要重复声明类型，如下：

```go
type Rectangle struct {
	height, width, area int
	color               string
}
```

在声明结构体字段时，字段名不能与方法名重复



------

## 实例化



Go 不存在构造方法，大多数情况下采用如下的方式来实例化结构体，初始化的时候就像 map 一样指定字段名称再初始化字段值

```go
programmer := Programmer{
   Name:     "jack",
   Age:      19,
   Job:      "coder",
   Language: []string{"Go", "C++"},
}
```

不过也可以省略字段名称，当省略字段名称时，就必须初始化所有字段，通常不建议使用这种方式，因为可读性很糟糕

```go
programmer := Programmer{
   "jack",
   19,
   "coder",
   []string{"Go", "C++"}}
```



不过 Go 并不支持函数与方法重载，所以你无法为同一个函数或方法定义不同的参数。如果想以多种方式实例化结构体，要么创建多个构造函数，要么建议使用选项模式



------

## 选项模式



选项模式是 Go 语言中一种很常见的设计模式，可以更为灵活的实例化结构体，拓展性强，并且不需要改变构造函数的函数签名。假设有下面这样一个结构体

```go
type Person struct {
	Name     string
	Age      int
	Address  string
	Salary   float64
	Birthday string
}
```

声明一个 PersonOptions 类型，它接受一个 *Person 类型的参数，它必须是指针，因为要在闭包中对 Person 赋值

```go
type PersonOptions func(p *Person)
```

接下来创建选项函数，它们一般是 With 开头，它们的返回值就是一个闭包函数

```go
func WithName(name string) PersonOptions {
	return func(p *Person) {
		p.Name = name
	}
}

func WithAge(age int) PersonOptions {
	return func(p *Person) {
		p.Age = age
	}
}

func WithAddress(address string) PersonOptions {
	return func(p *Person) {
		p.Address = address
	}
}

func WithSalary(salary float64) PersonOptions {
	return func(p *Person) {
		p.Salary = salary
	}
}
```

实际声明的构造函数签名如下，它接受一个可变长 PersonOptions 类型的参数

```go
func NewPerson(options ...PersonOptions) *Person {
    // 优先应用options
	p := &Person{}
    for _, option := range options {
        option(p)
    }
	
	// 默认值处理
	if p.Age < 0 {
		p.Age = 0
	}
	......
	
    return p
}
```

这样一来对于不同实例化的需求只需要一个构造函数即可完成，只需要传入不同的 Options 函数即可

```go
func main() {
	pl := NewPerson(
		WithName("John Doe"),
		WithAge(25),
		WithAddress("123 Main St"),
		WithSalary(10000.00),
	)

	p2 := NewPerson(
		WithName("Mike jane"),
		WithAge(30),
	)
}
```

函数式选项模式在很多开源项目中都能看见，gRPC Server 的实例化方式也是采用了该设计模式。函数式选项模式只适合于复杂的实例化，如果参数只有简单几个，建议还是用普通的构造函数来解决



------

## 组合



在 Go 中，结构体之间的关系是通过组合来表示的，可以显式组合，也可以匿名组合，后者使用起来更类似于继承，但本质上没有任何变化。例如：

显式组合的方式

```go
type Person struct {
   name string
   age  int
}

type Student struct {
   p      Person
   school string
}

type Employee struct {
   p   Person
   job string
}
```

在使用时需要显式的指定字段 p

```go
student := Student{
   p:      Person{name: "jack", age: 18},
   school: "lili school",
}
fmt.Println(student.p.name)
```

而匿名组合可以不用显式的指定字段

```go
type Person struct {
	name string
	age  int
}

type Student struct {
	Person
	school string
}

type Employee struct {
	Person
	job string
}
```

匿名字段的名称默认为类型名，调用者可以直接访问该类型的字段和方法，但除了更加方便以外与第一种方式没有任何的区别

```go
student := Student{
   Person: Person{name: "jack",age: 18},
   school: "lili school",
}
fmt.Println(student.name)
```



------

## 指针



对于结构体指针而言，不需要解引用就可以直接访问结构体的内容，例子如下：

```go
p := &Person{
   name: "jack",
   age:  18,
}
fmt.Println(p.age,p.name)
```

在编译的时候会转换，其实还是需要解引用，不过在编码的时候可以省去，算是一种语法糖



------

## 标签



结构体标签是一种元编程的形式，结合反射可以做出很多奇妙的功能，格式如下：

```go
`key1:"val1" key2:"val2"`
```

标签是一种键值对的形式，使用空格进行分隔。结构体标签的容错性很低，如果没能按照正确的格式书写结构体，那么将会导致无法正常读取，但是在编译时却不会有任何的报错，下方是一个使用示例：

```go
type Programmer struct {
    Name     string `json:"name"`
    Age      int `yaml:"age"`
    Job      string `toml:"job"`
    Language []string `properties:"language"`
}
```

结构体标签最广泛的应用就是在各种序列化格式中的别名定义，标签的使用需要结合反射才能完整发挥出其功能



------

## 内存对齐



Go 结构体字段的内存分布遵循内存对齐的规则，这么做可以减少 CPU 访问内存的次数，相应的占用的内存要多一些，属于空间换时间的一种手段。假设有如下结构体

```go
type Num struct {
	A int64 // 占8个字节
	B int32 // 占4个字节
	C int16 // 占2字节
	D int8 // 占1字节
    E int32
}
```

整个结构体的内存占用似乎是 8+4+2+1+4=19 个字节吗，当然不是这样，根据内存对齐规则而言，结构体的内存占用长度至少是最大字段的整数倍，不足的则补齐。该结构体中最大的是 int64 占用 8 个字节，那么内存分布如下图所示

![image-20241123155827467](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80/image-20241123155827467.png)

所以实际上是占用24个字节，其中有5个字节是无用的

<br>

再来看下面这个结构体

```go
type Num struct {
	A int8
	B int64
	C int8
}
```

明白了上面的规则后，可以很快的理解它的内存占用也是 24 个字节，尽管它只有三个字段，足足浪费了 14 个字节

![image-20241123160118533](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80/image-20241123160118533.png)

但是可以调整字段，改成如下的顺序

```go
type Num struct {
	A int8
	C int8
	B int64
}
```

如此一来就占用的内存就变为了 16 字节，浪费了 6 个字节，减少了 8 个字节的内存浪费

![image-20241123160214546](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80/image-20241123160214546.png)

从理论上来说，让结构体中的字段按照合理的顺序分布，可以减少其内存占用。不过实际编码过程中，并没有必要的理由去这样做，它不一定能在减少内存占用这方面带来实质性的提升，但一定会提高开发人员的血压和心智负担，尤其是在业务中一些结构体的字段数可能多大几十个或者数百个，所以仅做了解即可



------

## 空结构体



空结构体没有字段，不占用内存空间，我们可以通过 unsafe.SizeOf 函数来计算占用的字节大小

```go
func main() {
   type Empty struct {}
   fmt.Println(unsafe.Sizeof(Empty{})) // 0
}
```

空结构体的使用场景有很多，比如之前提到过的，作为 map 的值类型，可以将 map 作为 set 来进行使用，又或者是作为通道的类型，表示仅做通知类型的通道



------

# 方法



方法与函数的区别在于，方法拥有接收者，而函数没有，且只有自定义类型能够拥有方法。先来看一个例子

```go
type IntSlice []int

func (i IntSlice) Get(index int) int {
	return i[index]
}
func (i IntSlice) Set(index, val int) {
	i[index] = val
}

func (i IntSlice) Len() int {
	return len(i)
}
```

先声明了一个类型 IntSlice，其底层类型为 []int，再声明了三个方法 Get，Set 和 Len，方法的长相与函数并无太大的区别，只是多了一小段 (i IntSlice) 。i 就是接收者，IntSlice 就是接收者的类型，接收者就类似于其他语言中的 this 或 self，只不过在 Go 中需要显示的指明

```go
func main() {
   var intSlice IntSlice
   intSlice = []int{1, 2, 3, 4, 5}
   fmt.Println(intSlice.Get(0))
   intSlice.Set(0, 2)
   fmt.Println(intSlice)
   fmt.Println(intSlice.Len())
}
```

方法的使用就类似于调用一个类的成员方法，先声明，再初始化，再调用



------

## 值接收者



接收者也分两种类型，值接收者和指针接收者，先看一个例子

```go
type MyInt int

func (i MyInt) Set(val int) {
   i = MyInt(val) // 修改了，但是不会造成任何影响
}

func main() {
   myInt := MyInt(1)
   myInt.Set(2)
   fmt.Println(myInt)
}
```

上述代码运行过后，会发现 myInt 的值依旧是 1，并没有被修改成 2。方法在被调用时，会将接收者的值传入方法中，上例的接收者就是一个值接收者，可以简单的看成一个形参，而修改一个形参的值，并不会对方法外的值造成任何影响，那么如果通过指针调用会如何呢

```go
func main() {
	myInt := MyInt(1)
	(&myInt).Set(2)
	fmt.Println(myInt)
}
```

遗憾的是，这样的代码依旧不能修改内部的值，为了能够匹配上接收者的类型，Go 会将其解引用，解释为`(*(&myInt)).Set(2)`



------

## 指针接收者



稍微修改了一下，就能正常修改 myInt 的值

```go
type MyInt int

func (i *MyInt) Set(val int) {
   *i = MyInt(val)
}

func main() {
   myInt := MyInt(1)
   myInt.Set(2)
   fmt.Println(myInt)
}
```

现在的接收者就是一个指针接收者，虽然 myInt 是一个值类型，在通过值类型调用指针接收者的方法时，Go 会将其解释为 (&myint).Set(2)。所以方法的接收者为指针时，不管调用者是不是指针，都可以修改内部的值

函数的参数传递过程中，是值拷贝的，如果传递的是一个整型，那就拷贝这个整型，如果是一个切片，那就拷贝这个切片，但如果是一个指针，就只需要拷贝这个指针，显然传递一个指针比起传递一个切片所消耗的资源更小，接收者也不例外，值接收者和指针接收者也是同样的道理。在大多数情况下，都推荐使用指针接收者，不过两者并不应该混合使用，要么都用，要么就都不用
