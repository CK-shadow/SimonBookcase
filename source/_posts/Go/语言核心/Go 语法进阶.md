---
title: Go 语法进阶
date: 2025-09-05 03:38:55
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/preview.jpg
tags:
  - Go
categories: Go
---



# 接口



接口是一个非常重要的概念，它描述了一组抽象的规范，而不提供具体的实现。对于项目而言会使得代码更加优雅可读，对于开发者而言也会减少很多心智负担，代码风格逐渐形成了规范，于是就有了现在人们所推崇的面向接口编程

Go 在 1.18 最大的变化就是加入了泛型，新接口定义就是为了泛型而服务的，不过一点也不影响之前接口的使用，同时接口也分为了两类:

1. 基本接口 (Basic Interface)：只包含方法集的接口就是基本接口
2. 通用接口 (General Interface)：只要包含类型集的接口就是通用接口



------

## 基本接口



这是一个接口的实例：

```go
type Person interface {
	Say(string) string
	Walk(int)
}
```

这是一个 Person 接口，有两个对外暴露的方法 Walk 和 Say，在接口里，函数的参数名变得不再重要，当然如果想加上参数名和返回值名也是允许的

<br>

仅仅只有接口是无法被初始化的，因为它仅仅只是一组规范，并没有具体的实现，不过可以被声明

```go
func main() {
   var person Person
   fmt.Println(person) // nil
}
```

<br>

先来看一个例子，一个建筑公司想一种特殊规格的起重机，于是给出了起重机的特殊规范和图纸，并指明了起重机应该有起重和吊货的功能，建筑公司并不负责造起重机，只是给出了一个规范，这就叫接口，于是公司 A 接下了订单，根据自家公司的独门技术造出了绝世起重机并交给了建筑公司，建筑公司不在乎是用什么技术实现的，也不在乎什么绝世起重机，只要能够起重和吊货就行，仅仅只是当作一台普通起重机来用，根据规范提供具体的功能，这就叫实现。只根据接口的规范来使用功能，屏蔽其内部实现，这就叫面向接口编程。过了一段时间，绝世起重机出故障了，公司 A 也跑路了，于是公司 B 依据规范造了一台更厉害的巨无霸起重机，由于同样具有起重和吊货的功能，可以与绝世起重机无缝衔接，并不影响建筑进度，建筑得以顺利完成，内部实现改变而功能不变，不影响之前的使用，可以随意替换，这就是面向接口编程的好处

接下来会用 Go 描述上述情形

```go
// 起重机接口
type Crane interface { 
	JackUp() string
	Hoist() string
}

// 起重机A
type CraneA struct {
	work int //内部的字段不同代表内部细节不一样
}

func (c CraneA) Work() {
	fmt.Println("使用技术A")
}
func (c CraneA) JackUp() string {
	c.Work()
	return "jackup"
}

func (c CraneA) Hoist() string {
	c.Work()
	return "hoist"
}

// 起重机B
type CraneB struct {
	boot string
}

func (c CraneB) Boot() {
	fmt.Println("使用技术B")
}

func (c CraneB) JackUp() string {
	c.Boot()
	return "jackup"
}

func (c CraneB) Hoist() string {
	c.Boot()
	return "hoist"
}

type ConstructionCompany struct {
	Crane Crane // 只根据Crane类型来存放起重机
}

func (c *ConstructionCompany) Build() {
	fmt.Println(c.Crane.JackUp())
	fmt.Println(c.Crane.Hoist())
	fmt.Println("建筑完成")
}

func main() {
	// 使用起重机A
	company := ConstructionCompany{CraneA{}}
	company.Build()
	fmt.Println()
	// 更换起重机B
	company.Crane = CraneB{}
	company.Build()
}
```

输出

```
使用技术A
jackup
使用技术A
hoist
建筑完成

使用技术B
jackup
使用技术B
hoist
建筑完成
```

上面例子中，可以观察到接口的实现是隐式的，也对应了官方对于基本接口实现的定义：方法集是接口方法集的超集，所以在 Go 中，实现一个接口不需要 implements 关键字显式的去指定要实现哪一个接口，只要是实现了一个接口的全部方法，那就是实现了该接口。有了实现之后，就可以初始化接口了，建筑公司结构体内部声明了一个 Crane 类型的成员变量，可以保存所有实现了 Crane 接口的值，由于是 Crane 类型的变量，所以能够访问到的方法只有 JackUp 和 Hoist，内部的其他方法例如 Work 和 Boot 都无法访问

<br>

之前提到过任何自定义类型都可以拥有方法，那么根据实现的定义，任何自定义类型都可以实现接口，下面举几个比较特殊的例子

```go
type Person interface {
   Say(string) string
   Walk(int)
}

type Man interface {
   Exercise()
   Person
}
```

Man 接口方法集是 Person 的超集，所以 Man 也实现了接口 Person，不过这更像是一种 "继承"

```go
type Number int

func (n Number) Say(s string) string {
	return "bibibibibi"
}

func (n Number) Walk(i int) {
	fmt.Println("can not walk")
}
```

类型 Number 的底层类型是 int，虽然这放在其他语言中看起来很离谱，但 Number 的方法集确实是 Person 的超集，所以也算实现

```go
type Func func()

func (f Func) Say(s string) string {
	f()
	return "bibibibibi"
}

func (f Func) Walk(i int) {
	f()
	fmt.Println("can not walk")
}

func main() {
	var function Func
	function = func() {
		fmt.Println("do somthing")
	}
	function()
}
```

同样的，函数类型也可以实现接口



------

## 空接口



```go
type Any interface{

}
```

Any 接口内部没有方法集合，根据实现的定义，所有类型都是 Any 接口的的实现，因为所有类型的方法集都是空集的超集，所以 Any 接口可以保存任何类型的值

```go
func main() {
	var anything Any

	anything = 1
	println(anything)
	fmt.Println(anything)

	anything = "something"
	println(anything)
	fmt.Println(anything)
	
	anything = complex(1, 2)
	println(anything)
	fmt.Println(anything)

	anything = 1.2
	println(anything)
	fmt.Println(anything)

	anything = []int{}
	println(anything)
	fmt.Println(anything)

	anything = map[string]int{}
	println(anything)
	fmt.Println(anything)
}
```

输出

```go
(0xe63580,0xeb8b08)
1
(0xe63d80,0xeb8c48)
something
(0xe62ac0,0xeb8c58)
(1+2i)
(0xe62e00,0xeb8b00)
1.2
(0xe61a00,0xc0000080d8)
[]
(0xe69720,0xc00007a7b0)
map[]
```

通过输出会发现，两种输出的结果不一致，其实接口内部可以看成是一个由 (val,type) 组成的元组，type 是具体类型，在调用方法时会去调用具体类型的具体值

<br>

```go
interface{}
```

这也是一个空接口，不过是一个匿名空接口，在开发时通常会使用匿名空接口来表示接收任何类型的值，例子如下：

```go
func main() {
   DoSomething(map[int]string{})
}

func DoSomething(anything interface{}) interface{} {
   return anything
}
```

在后续的更新中，官方提出了另一种解决办法，为了方便起见，可以使用 any 来替代 interace{}，两者是完全等价的，因为前者仅仅只是一个类型别名，如下：

```go
type any = interface{}
```



------

## 通用接口



通用接口就是为了泛型服务的，只要掌握了泛型，就掌握了通用接口



------

# 泛型



最初的 Go 是没有泛型这一说法的，但自从诞生以来，社区关于 Go 呼声最高的事情就是希望加入泛型。终于 Go 在 1.18 版本加入了对泛型的支持，不过有一点怪



------

## 示例



在开始之前，先来看一个简单的例子

```go
func Sum(a, b int) int {
   return a + b
}
```

这是一个功能十分简单的函数，作用就是将两个 int 类型的整数相加并返回结果，倘若想要传入两个 float64 类型的浮点数求和的话，显然是不可以的，因为类型不匹配。一种解决办法就是再定义一个新的函数，如下：

```go
func SumFloat64(a, b float64) float64 {
	return a + b
}
```

那么问题来了，如果开发一个数学工具包，计算所有数字类型的两数之和，难道要每一个类型都要编写一个函数吗？显然是不太可能的，或者也可以使用 any 类型加反射来判断，如下：

```go
func SumAny(a, b any) (any, error) {
	tA, tB := reflect.ValueOf(a), reflect.ValueOf(b)
	if tA.Kind() != tB.Kind() {
		return nil, errors.New("disMatch type")
	}

	switch tA.Kind() {
	case reflect.Int:
	case reflect.Int32:
		...
	}
}
```

但是这样写会显得十分复杂，而且性能低下。但是 Sum 函数的逻辑都是一模一样的，都只不过是将两个数相加而已，这时候就需要用到了泛型，所以为什么需要泛型，泛型是为了解决执行逻辑与类型无关的问题，这类问题不关心给出的类型是什么，只需要完成对应的操作就足够。所以泛型的写法如下：

```go
func Sum[T int | float64](a, b T) T {
   return a + b
}
```

* 类型形参：T 就是一个类型形参，形参具体是什么类型取决于传进来什么类型
* 类型约束：int | float64 构成了一个类型约束，这个类型约束内规定了哪些类型是允许的，约束了类型形参的类型范围
* 类型实参：`Sum[int](1,2)`，手动指定了 int 类型，int 就是类型实参

第一种用法，显式的指明使用哪种类型，如下：

```go
Sum[int](2012, 2022)
```

第二种用法，不指定类型，让编译器自行推断，如下：

```go
Sum(3.1415926, 1.114514)
```

看到这里后，应该对为什么要使用泛型，以及泛型解决了哪种问题有了一个大概的了解。将泛型引入项目后，开发上确实会比较方便，随之而来的是项目复杂度的增加，毫无节制的使用泛型会使得代码难以维护，所以应该在正确的地方使用泛型，而不是为了泛型而泛型



------

## 泛型结构



这是一个泛型切片，类型约束为 int | int32 | int64

```go
type GenericSlice[T int | int32 | int64] []T
```

这里使用时就不能省略掉类型实参

```go
GenericSlice[int]{1, 2, 3}
```

<br>

这是一个泛型哈希表，键的类型必须是可比较的，所以使用 comparable 接口，值的类型约束为 V int | string | byte

```go
type GenericMap[K comparable, V int | string | byte] map[K]V
```

使用

```go
gmap1 := GenericMap[int, string]{1: "hello world"}
gmap2 := make(GenericMap[string, byte], 0)
```

<br>

这是一个泛型结构体，类型约束为 T int | string

```go
type GenericStruct[T int | string] struct {
   Name string
   Id   T
}
```

使用

```go
GenericStruct[int]{
   Name: "jack",
   Id:   1024,
}
GenericStruct[string]{
   Name: "Mike",
   Id:   "1024",
}
```

<br>

这是一个泛型切片形参的例子

```go
type Company[T int | string, S []T] struct {
   Name  string
   Id    T
   Stuff S
}

//也可以如下
type Company[T int | string, S []int | string] struct {
	Name  string
	Id    T
	Stuff S
}
```

使用

```go
Company[int, []int]{
   Name:  "lili",
   Id:    1,
   Stuff: []int{1},
}
```

<br>

在泛型结构体中，更推荐这种写法

```go
type Company[T int | string, S int | string] struct {
	Name  string
	Id    T
	Stuff []S
}
```



------

## 泛型结构注意点



* 泛型不能作为一个类型的基本类型

  以下写法是错误的，泛型形参 T 是不能作为基础类型的

  ```go
  type GenericType[T int | int32 | int64] T
  ```

  虽然下列的写法是允许的，不过毫无意义而且可能会造成数值溢出的问题，虽然并不推荐

  ```go
  type GenericType[T int | int32 | int64] int
  ```

* 泛型类型无法使用类型断言

  对泛型类型使用类型断言将会无法通过编译，泛型要解决的问题是类型无关的，如果一个问题需要根据不同类型做出不同的逻辑，那么就根本不应该使用泛型，应该使用 interface{} 或者 any

  ```go
  func Sum[T int | float64](a, b T) T {
     ints,ok := a.(int) // 不被允许
     switch a.(type) { // 不被允许
     case int:
     case bool:
        ...
     }
     return a + b
  }
  ```

* 匿名结构不支持泛型

  匿名结构体是不支持泛型的，如下的代码将无法通过编译

  ```go
  testStruct := struct[T int | string] {
     Name string
     Id T
  }[int]{
     Name: "jack",
     Id: 1  
  }
  ```

* 匿名函数不支持自定义泛型

  以下两种写法都将无法通过编译

  ```go
  var sum[T int | string] func (a, b T) T
  sum := func[T int | string](a,b T) T{
      ...
  }
  ```

  但是可以使用已有的泛型类型，例如闭包中

  ```go
  func Sum[T int | float64](a, b T) T {
  	sub := func(c, d T) T {
  		return c - d
  	}
  	return sub(a,b) + a + b
  }
  ```

* 不支持泛型方法

  方法是不能拥有泛型形参的，但是 receiver 可以拥有泛型形参。如下的代码将会无法通过编译

  ```go
  type GenericStruct[T int | string] struct {
     Name string
     Id   T
  }
  
  func (g GenericStruct[T]) name[S int | float64](a S) S {
     return a
  }
  ```



------

## 类型集



在 1.18 以后，接口的定义变为了类型集 (type set)，含有类型集的接口又称为 General interfaces 即通用接口

类型集主要用于类型约束，不能用作类型声明，既然是集合，就会有空集，并集，交集，接下来将会讲解这三种情况

<br>

**并集**

接口类型 SignedInt 是一个类型集，有符号整数类型的并集就是 SignedInt，反过来 SignedInt 就是它们的超集

```go
type SignedInt interface {
   int8 | int16 | int | int32 | int64
}
```

基本数据类型如此，对待其它通用接口也是如此

```go
type SignedInt interface {
	int8 | int16 | int | int32 | int64
}

type UnSignedInt interface {
	uint8 | uint16 | uint32 | uint64
}

type Integer interface {
	SignedInt | UnSignedInt
}
```

<br>

**交集**

非空接口的类型集是其所有元素的类型集的交集，翻译成人话就是：如果一个接口包含多个非空类型集，那么该接口就是这些类型集的交集，例子如下

```go
type SignedInt interface {
   int8 | int16 | int | int32 | int64
}

type Integer interface {
   int8 | int16 | int | int32 | int64 | uint8 | uint16 | uint | uint32 | uint64
}

type Number interface {
	SignedInt
	Integer
}
```

例子中的交集肯定就是 SignedInt

```go
func Do[T Number](n T) T {
   return n
}

Do[int](2)
DO[uint](2) //无法通过编译
```

<br>

**空集**

空集就是没有交集，例子如下，下面例子中的 Integer 就是一个类型空集

```go
type SignedInt interface {
	int8 | int16 | int | int32 | int64
}

type UnsignedInt interface {
	uint8 | uint16 | uint | uint32 | uint64
}

type Integer interface {
	SignedInt
	UnsignedInt
}
```

因为无符号整数和有符号整数两个肯定没有交集，所以交集就是个空集，下方例子中不管传什么类型都无法通过编译

```go
Do[Integer](1)
Do[Integer](-100)
```

<br>

**空接口**

空接口与空集并不同，空接口是所有类型集的集合，即包含所有类型

```go
func Do[T interface{}](n T) T {
   return n
}

func main() {
   Do[struct{}](struct{}{})
   Do[any]("abc")
}
```

<br>

**底层类型**

当使用 type 关键字声明了一个新的类型时，即便其底层类型包含在类型集内，当传入时也依旧会无法通过编译

```go
type Int interface {
   int8 | int16 | int | int32 | int64 | uint8 | uint16 | uint | uint32 | uint64
}

type TinyInt int8

func Do[T Int](n T) T {
   return n
}

func main() {
   Do[TinyInt](1) // 无法通过编译，即便其底层类型属于Int类型集的范围内
}
```

有两种解决办法，第一种是往类型集中并入该类型，但是这毫无意义，因为 TinyInt 与 int8 底层类型就是一致的，所以就有了第二种解决办法

使用 ~ 符号，来表示底层类型，如果一个类型的底层类型属于该类型集，那么该类型就属于该类型集，如下所示

```go
type Int interface {
   ~int8 | ~int16 | ~int | ~int32 | ~int64 | ~uint8 | ~uint16 | ~uint | ~uint32 | ~uint64
}
```

修改过后就可以通过编译了

```go
func main() {
   Do[TinyInt](1) // 可以通过编译，因为TinyInt在类型集Int内
}
```



------

## 类型集注意点



* 带有方法集的接口无法并入类型集

  只要是带有方法集的接口，不论是基本接口，泛型接口，又或者是通用接口，都无法并入类型集中，同样的也无法在类型约束中并入。以下两种写法都是错误的，都无法通过编译

  ```go
  type Integer interface {
  	Sum(int, int) int
  	Sub(int, int) int
  }
  
  type SignedInt interface {
     int8 | int16 | int | int32 | int64 | Integer
  }
  
  func Do[T Integer | float64](n T) T {
  	return n
  }
  ```

* 类型集无法当作类型实参使用

  只要是带有类型集的接口，都无法当作类型实参

  ```go
  type SignedInt interface {
  	int8 | int16 | int | int32 | int64
  }
  
  func Do[T SignedInt](n T) T {
     return n
  }
  
  func main() {
     Do[SignedInt](1) // 无法通过编译
  }
  ```

* 类型集中的交集问题

  对于非接口类型，类型并集中不能有交集，例如下例中的 TinyInt 与 ~int8 有交集

  ```go
  type Int interface {
     ~int8 | ~int16 | ~int | ~int32 | ~int64 | ~uint8 | ~uint16 | ~uint | ~uint32 | ~uint64 | TinyInt // 无法通过编译
  }
  
  type TinyInt int8
  ```

  但是对于接口类型的话，就允许有交集，如下例

  ```go
  type Int interface {
     ~int8 | ~int16 | ~int | ~int32 | ~int64 | ~uint8 | ~uint16 | ~uint | ~uint32 | ~uint64 | TinyInt // 可以通过编译
  }
  
  type TinyInt interface {
  	int8
  }
  ```

* 类型集不能直接或间接的并入自身

  以下示例中，Floats 直接的并入了自身，而 Double 又并入了 Floats，所以又间接的并入了自身

  ```go
  type Floats interface {  // 代码无法通过编译
     Floats | Double
  }
  
  type Double interface {
     Floats
  }
  ```

* comparable 接口无法并入类型集

  同样的，也无法并入类型约束中，所以基本上都是单独使用

  ```go
  func Do[T comparable | Integer](n T) T { //无法通过编译
     return n
  }
  
  type Number interface { // 无法通过编译
  	Integer | comparable
  }
  
  type Comparable interface { // 可以通过编译但是毫无意义
  	comparable
  }
  ```



------

## 泛型在队列中的应用



用泛型实现一个简单的队列，首先声明队列类型，队列中的元素类型可以是任意的，所以类型约束为 any

```go
type Queue[T any] []T
```

总共只有四个方法 Pop ，Peek，Push，Size，代码如下：

```go
type Queue[T any] []T

func (q *Queue[T]) Push(e T) {
	*q = append(*q, e)
}

func (q *Queue[T]) Pop(e T) (_ T) {
	if q.Size() > 0 {
		res := q.Peek()
		*q = (*q)[1:]
		return res
	}
	return
}

func (q *Queue[T]) Peek() (_ T) {
	if q.Size() > 0 {
		return (*q)[0]
	}
	return
}

func (q *Queue[T]) Size() int {
	return len(*q)
}
```

在 Pop 和 Peek 方法中，可以看到返回值是 _ T，这是具名返回值的使用方式，但是又采用了下划线 _ 表示这是匿名的，这并非多此一举，而是为了表示泛型零值。由于采用了泛型，当队列为空时，需要返回零值，但由于类型未知，不可能返回具体的类型，借由上面的那种方式就可以返回泛型零值。也可以声明泛型变量的方式来解决零值问题，对于一个泛型变量，其默认的值就是该类型的零值，如下：

```go
func (q *Queue[T]) Pop(e T) T {
    var res T
	if q.Size() > 0 {
		res = q.Peek()
		*q = (*q)[1:]
		return res
	}
	return res
}
```

<br>

Go 的一大特点就是编译速度非常快，编译快是因为编译期做的优化少，泛型的加入会导致编译器的工作量增加，工作更加复杂，这必然会导致编译速度变慢，事实上当初 Go1.18 刚推出泛型的时候确实导致编译更慢了，Go 团队既想加入泛型又不想太拖累编译速度，开发者用的顺手，编译器就难受，反过来编译器轻松了（最轻松的当然是直接不要泛型），开发者就难受了，现如今的泛型就是这两者之间妥协后的产物



------

# 迭代器



在 Go 中，用于迭代特定数据结构的关键字为 for range，之前已经介绍过它的一些应用，它仅能作用于语言内置的几个数据结构：

* 数组
* 切片
* 字符串
* map
* 整型

这样的话使用起来非常的不灵活，没有拓展性，对于自定义类型几乎不支持，不过好在 Go1.23 版本更新以后，for range 关键字支持了 range over func，这样一来自定义迭代器也就成为了可能



------

## 初识



下面这是一个闭包求解斐波那契数列的例子，它的实现代码如下：

```go
func Fibonacci(n int) func() (int, bool) {
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

可以把它改造成迭代器，如下所示，可以看到代码量要减少了一些

```go
func Fibonacci(n int) func(yield func(int) bool) {
	a, b, c := 0, 1, 1
	return func(yield func(int) bool) {
		for range n {
			if !yield(a) {
				return
			}
			a, b = b, c
			c = a + b
		}
	}
}
```

Go 的迭代器是 range over func 风格，我们可以直接用 for range 关键字来进行使用，使用起来也要比原来更方便

```go
func main() {
    n := 8
	for f := range Fibonacci(n) {
		fmt.Println(f)
	}
}
```

输出如下

```text
0
1
1
2
3
5
8
13
```

如上所示，迭代器就是一个闭包函数，它接受一个回调函数作为参数，你甚至可以在里面看到 yield 这种字眼，写过 python 的人应该都很熟悉，它与 python 中的生成器很类似。Go 的迭代器并没有新增任何关键字，语法特性，在上述示例中 yield 也只是一个回调函数，它并非关键字，官方取这个名字是为了方便理解



------

## 推送式迭代器



这是官方对于 Go 迭代器的解释：迭代器是一个函数，它将序列中的元素逐个传递给回调函数，通常称为 yield

从中可以明确的一点，迭代器就是一个函数，它接受一个回调函数作为参数，在迭代过程中会将序列中的元素逐个传递给回调函数 yield。在之前示例中是按照下面的方式使用迭代器的：

```go
for f := range Fibonacci(n) {
    fmt.Println(f)
}
```

根据官方定义，上面迭代器 Backward 的例子使用就等同于下面这段代码

```go
Fibonacci(n)(func(f int) bool {
    fmt.Println(f)
    return true
})
```

循环体的 body 就是迭代器的回调函数 yiled，当函数返回 true 迭代器会继续迭代，否则就会停止

<br>

此外，iter 标准库中也定义了迭代器的类型 iter.Seq，它的类型就是函数

```go
type Seq[V any] func(yield func(V) bool)

type Seq2[K, V any] func(yield func(K, V) bool)
```

iter.Seq 的回调函数只接受一个参数，那么在迭代时 for range 仅有一个返回值，如下：

```go
for v := range iter {
	// body
}
```

iter.Seq2 的回调函数接受两个参数，那么在迭代时 for range 就有两个返回值，如下：

```go
for k, v := range iter {
	// body
}
```

虽然标准库中没有定义 0 个参数的 Seq，但这也是完全允许的，它相当于

```go
func(yield func() bool)
```

使用起来如下所示

```go
for range iter {
	// body
}
```

回调函数的参数数量只能是 0 至 2 个，多了会无法通过编译

<br>

简而言之，for range 中的循环体就是迭代器中的 yield 回调函数，for range 返回几个值，相应的 yeild 函数就有几个入参，每一轮迭代时，迭代器都会调用 yield 函数，也就是执行循环体中的代码，主动将序列中的元素传递给 yield 函数，这种主动传递元素的迭代器我们一般称之为推送式迭代器（pushing iterator），比较典型的例子就是其他语言中的 foreach，比如 js

```javascript
let arr = [1, 2, 3, 4, 5];
arr.filter(e => e % 2 === 0).forEach(e => {
    console.log(e)
});
```

在 Go 中的表现形式就是由 range 返回被迭代的元素

```go
for index, value := range iterator() {
	fmt.Println(index, value)
}
```

在某些语言（比如 Java）中它还有另一个叫法：数据流处理



------

## 拉取式迭代器



推送式迭代器（pushing iterator）是由迭代器来控制迭代的逻辑，用户被动获取元素，相反的拉取式迭代器（pulling iterator）就是由用户来控制迭代逻辑，主动的去获取序列元素。一般而言，拉取式迭代器都会有特定的函数如 next()，stop() 来控制迭代的开始或结束，它可以是一个闭包或者结构体

```go
scanner := bufio.NewScanner(file)
for scanner.Scan() {
    line, err := scanner.Text(), scanner.Err()
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(line)
}
```

如上所示，Scanner 通过方法 Text()来获取文件中的下一行文本，通过方法 Scan() 来表示迭代是否结束，这也是拉取式迭代器的一种模式。Scanner 采用结构体来记录状态，而在 iter 库定义的拉取式迭代器采用闭包来记录状态，我们通过 iter.Pull 或 iter.Pull2 函数就可以将一个标准的推送式迭代器转换为拉取式迭代器，iter.Pull 与 iter.Pull2 的区别就是后者的返回值有两个，签名如下

```go
func Pull[V any](seq Seq[V]) (next func() (V, bool), stop func()) 

func Pull2[K, V any](seq Seq2[K, V]) (next func() (K, V, bool), stop func())
```

它们都接受一个迭代器作为参数，然后会返回两个函数 next()和 stop()，用于控制迭代的继续和停止

```go
func next() (V, bool)

func stop()
```

next 会返回被迭代的元素，和一个表示当前值是否有效的布尔值，当迭代结束时 next 函数会返回元素的零值和 false。stop 函数会结束迭代过程，当调用者不再使用迭代器后，就必须使用 stop 函数来结束迭代。顺带一提，在多个协程调用同一个迭代器的 next 函数是错误的做法，因为它并非并发安全

闭包与拉取式迭代器的用法都大差不差，它们的思想都是一样的，后者还会因为各种各样的处理导致性能上的拖累。老实说这么做确实多此一举，它的应用场景确实不是很多，不过 iter.pull 是为了 iter.Seq 而存在的，也就是为了将推送式迭代器转换成拉取式迭代器的而存在的，如果你仅仅只是想要一个拉取式迭代器，还专门为此去实现一个推送式迭代器来进行转换，要这样做的话不妨考虑下自己实现的复杂度和性能，就像这个斐波那契数列的例子一样，绕了一圈又回到原点，唯一的好处可能就是符合官方的迭代器规范



------

## 错误处理



在迭代时发生了错误怎么办？可以将其传递给 yield 函数让 for range 返回，让调用者来进行处理，就像下面这个行迭代器的例子一样

```go
func ScanLines(reader io.Reader) iter.Seq2[string, error] {
	scanner := bufio.NewScanner(reader)
	return func(yield func(string, error) bool) {
		for scanner.Scan() {
			if !yield(scanner.Text(), scanner.Err()) {
				return
			}
		}
	}
}
```

可以看到它的第二个返回值是 error 类型，使用起来如下：

```go
for line, err := range ScanLines(file) {
    if err != nil {
        fmt.Println(err)
        break
    }
    fmt.Println(line)
}
```

<br>

这样处理起来就跟普通的错误处理没什么区别，拉取式迭代器也是同理

```go
next, stop := iter.Pull2(ScanLines(file))
defer stop()
for {
    line, err, ok := next()
    if err != nil {
        fmt.Println(err)
        break
    } else if !ok {
        break
    }
    fmt.Println(line)
}
```

<br>

如果发生了 panic，就像平常一样使用 recovery 即可

```go
defer func() {
    if err := recover(); err != nil {
        fmt.Println("panic:", err)
        os.Exit(1)
    }
}()

for line, err := range ScanLines(file) {
    if err != nil {
        fmt.Println(err)
        break
    }
    fmt.Println(line)
}
```

拉取式迭代器依然同理



------

## 标准库



有很多标准库也支持了迭代器，最常用的就是 slices 和 maps 标准库，下面介绍几个比较实用的功能：

* slices.All 会将切片转换成一个切片迭代器

  ```go+
  func main() {
  	s := []int{1, 2, 3, 4, 5}
  	for i, n := range slices.All(s) {
  		fmt.Println(i, n)
  	}
  }
  ```

* slices.Values 会将切片转换成一个切片迭代器，但是不带索引

  ```go
  func main() {
  	s := []int{1, 2, 3, 4, 5}
  	for n := range slices.Values(s) {
  		fmt.Println(n)
  	}
  }
  ```

* slices.Chunk 函数会返回一个迭代器，该迭代器会以 n 个元素为切片推送给调用者

  ```go
  func main() {
  	s := []int{1, 2, 3, 4, 5}
  	for chunk := range slices.Chunk(s, 2) {
  		fmt.Println(chunk)
  	}
  }
  ```

* slices.Collect 函数会将切片迭代器收集成一个切片

  ```go
  func main() {
  	s := []int{1, 2, 3, 4, 5}
  	s2 := slices.Collect(slices.Values(s))
  	fmt.Println(s2)
  }
  ```

* maps.Keys 会返回一个迭代 map 所有键的迭代器，配合 slices.Collect 可以直接收集成一个切片

  ```go
  func main() {
  	m := map[string]int{"one": 1, "two": 2, "three": 3}
  	keys := slices.Collect(maps.Keys(m))
  	fmt.Println(keys)
  }
  ```

* maps.All 可以将一个 map 转换为成一个 map 迭代器

  ```go
  func main() {
  	m := map[string]int{"one": 1, "two": 2, "three": 3}
  	for k, v := range maps.All(m) {
  		fmt.Println(k, v)
  	}
  }
  ```

* maps.Collect 可以将一个 map 迭代器收集成一个 map

  ```go
  func main() {
  	m := map[string]int{"one": 1, "two": 2, "three": 3}
  	m2 := maps.Collect(maps.All(m))
  	fmt.Println(m2)
  }
  ```



------

# 类型

## 静态强类型



Go 是一个静态强类型语言，静态指的是 Go 所有变量的类型早在编译期间就已经确定了，在程序的生命周期都不会再发生改变，尽管 Go 中的短变量声明有点类似动态语言的写法，但其变量类型是由编译器自行推断的，最根本的区别在于它的类型一旦推断出来后不会再发生变化，动态语言则完全相反。所以下面的代码完全无法通过编译，因为 a 是 int 类型的变量，不能赋值字符串

```go
func main() {
	var a int = 64
	a = "64"
	fmt.Println(a) // cannot use "64" (untyped string constant) as int value in assignment
}
```

强类型则指的是在程序中执行严格的类型检查，如果出现类型不匹配的情况时，会立即告诉程序员不应该这么做，而不是像动态语言一样去尝试推断可能的结果。所以下面的代码无法通过编译，因为两者类型不同，无法进行运算

```go
func main() {
	fmt.Println(1 + "1") // invalid operation: 1 + "1" (mismatched types untyped int and untyped string)
}
```



------

## 类型后置



Go 为什么要把类型声明放在后面而不是前面，很大程度上是从 C 语言吸取了教训，拿官方的一个例子展示效果，这是一个函数指针

```c
int (*(*fp)(int (*)(int, int), int))(int, int)
```

说实话不认真看很难知道这是一个什么类型，在 Go 中类似的写法如下

```go
f func(func(int,int) int, int) func(int, int) int
```

Go 的声明方式始终遵循名字在前面，类型在后面的原则，从左往右读，大概第一眼就可以知道这是一个函数，且返回值为 func(int,int) int。当类型变得越来越复杂时，类型后置在可读性上要好得多，Go 在许多层面的设计都是为了可读性而服务的



------

## 类型声明



在 Go 中通过类型声明，可以声明一个自定义名称的新类型，声明一个新类型通常需要一个类型名称以及一个基础类型，简单的例子如下：

```go
type MyInt int64
```

在上述类型声明中，通过 type 关键字声明了一个基础类型为 int64 名为 MyInt 的类型。在 Go 中，每一个新声明的类型都必须有一个与之对应的基础类型，且类型名称不建议与已有的内置标识符重复

```go
type MyInt int64

type MyFloat64 float64

type MyMap map[string]int

// 可以通过编译，但是不建议使用，这会覆盖原有的类型
type int int64
```

通过类型声明的类型都是新类型，不同的类型无法进行运算，即便基础类型是相同的

```go
type MyFloat64 float64

var f1 MyFloat64
var f float64
f1 = 0.2
f = 0.1
fmt.Println(f1 + f)
```

```go
invalid operation: f1 + f (mismatched types MyFloat64 and float64)
```



------

## 类型别名



类型别名与类型声明则不同，类型别名仅仅只是一个别名，并不是创建了一个新的类型，简单的例子如下：

```go
type Int = int
```

两者是都是同一个类型，仅仅叫的名字不同，所以也就可以进行运算，所以下例自然也就可以通过编译

```go
type Int = int
var a Int = 1
var b int = 2
fmt.Println(a + b)
```

```text
3
```

<br>

类型别名对于一些特别复杂的类型有很大的用处，例如现在有一个类型 map[string]map[string]int，这是一个二维 map，现有一个函数参数是 map[string]map[string]int 类型，如下：

```go
func PrintMyMap(mymap map[string]map[string]int) {
   fmt.Println(mymap)
}
```

这种情况下，就没有必要使用类型声明了，因为前者是声明了一个新的类型，无法作为该函数的参数，使用类型别名后的例子如下

```go
type TwoDMap = map[string]map[string]int

func PrintMyMap(mymap TwoDMap) {
   fmt.Println(mymap)
}
```

使用类型别名后看起来会简洁一些

内置类型 any 就是 interface{} 的类型别名，两者完全等价，仅仅叫法不一样



------

## 类型转换



在 Go 中，只存在显式的类型转换，不存在隐式类型转换，因此不同类型的变量无法进行运算，无法作为参数传递。类型转换适用的前提是知晓被转换变量的类型和要转换成的目标类型，例子如下：

```go
type MyFloat64 float64

var f1 MyFloat64
var f float64
f1 = 0.2
f = 0.1
fmt.Println(float64(f1) + f)
```

```text
0.30000000000000004
```

通过显式的将 MyFloat64 转换为 float64 类型，才能进行加法运算。类型转换的另一个前提是：被转换类型必须是可以被目标类型代表的（Representability），例如 int 可以被 int64 类型所代表，也可以被 float64 类型代表，所以它们之间可以进行显式的类型转换，但是 int 类型无法被 string 和 bool 类型代表，因为也就无法进行类型转换

<br>

即便两个类型可以相互代表，类型转换的结果也不总是正确的，看下面的一个例子：

```go
var num1 int8 = 1
var num2 int32 = 512
fmt.Println(int32(num1), int8(num2))
```

```text
1 0
```

num1 被正确的转换为了 int32 类型，但是 num2 并没有。这是一个典型的数值溢出问题，int32 能够表示 31 位整数，int8 只能表示 7 位整数，高精度整数在向低精度整数转换时会抛弃高位保留低位，因此 num1 的转换结果就是 0。在数字的类型转换中，通常建议小转大，而不建议大转小

<br>

在使用类型转换时，对于一些类型需要避免歧义，例子如下：

```go
*Point(p) // 等价于 *(Point(p))
(*Point)(p)  // 将p转换为类型 *Point
<-chan int(c)    // 等价于 <-(chan int(c))
(<-chan int)(c)  // 将c转换为类型  <-chan int
(func())(x)      // 将x转换为类型 func()
(func() int)(x)  // 将x转换为类型 func() int
```



------

## 类型断言



类型断言通常用于判断某一接口类型的变量是否属于某一个类型，示例如下

```go
var b int = 1
var a interface{} = b
if intVal, ok := a.(int); ok {
   fmt.Println(intVal)
} else {
   fmt.Println("error type")
}
```

```text
1
```

由于 interface{} 是空接口类型，空接口类型可以代表所有的类型，但是 int 类型无法代表 interface{} 类型，所以无法使用类型转换。而类型断言就可以判断其底层类型是否为想要的类型，类型断言语句有两个返回值，一个是类型转换过后的值，另一个是转换结果的布尔值



------

## 类型判断



在 Go 中，switch 语句还支持一种特殊的写法，通过这种写法可以根据不同的 case 做出不同的逻辑处理，使用的前提是入参必须是接口类型，示例如下：

```go
var a interface{} = 2
switch a.(type) {
    case int: fmt.Println("int")
    case float64: fmt.Println("float")
    case string: fmt.Println("string")
}
```

```text
int
```



------

# 错误

## 简介



在 Go 中的异常有三种级别：

* error：部分流程出错，需要处理
* panic：很严重的问题，程序应该在处理完问题后立即退出
* fatal：非常致命的问题，程序应该立即退出

准确的来说，Go 并没有异常，更多的是通过错误来体现，同样的，Go 中也并没有 try-catch-finally 这种语句，Go 创始人希望能够将错误可控，他们不希望干什么事情都需要嵌套一堆 try-catch，所以大多数情况会将其作为函数的返回值来返回，例如下方代码例子：

```go
func main() {
	// 打开一个文件
	if file, err := os.Open("README.txt"); err != nil {
		fmt.Println(err)
        return
	}
    fmt.Println(file.Name())
}
```

这段代码的意图很明显，打开一个名为 README.txt 的文件，如果打开失败函数将会返回一个错误，输出错误信息，如果错误为 nil 的话那么就是打开成功，输出文件名

<br>

看起来似乎是要比 try-catch 简洁一些，那如果有特别多的函数调用，将会到处都充斥着 if err != nil

正因如此，外界对于 Go 最诟病的点就在错误处理上，Go 源代码里 if err != nil 就占了相当一部分。Rust 同样也是返回错误值，但没有人会去说它这一点，因为它通过语法糖的方式解决了这类问题，与 Rust 相比之下，Go 的语法糖不能说很多，只能说是几乎没有

<br>

不过我们看待事物要辩证的来看，凡事都是有好有坏的，Go 的错误处理的优点有几个

* 心智负担小：有错误就处理，不处理就返回
* 可读性：因为处理的方式非常简单，大部分情况下都很容易读懂代码
* 易于调试：每一个错误都是通过函数调用的返回值产生的，可以一层一层往回找到，很少会出现突然冒出一个错误却不知道是从哪里来的这种情况

不过缺点也不少

* 错误中没有堆栈信息（需要第三方包解决或者自己封装）
* 丑陋，重复代码多（看个人喜好）
* 自定义错误是通过 var 来声明的，它是一个变量而不是常量（确实不应该）
* 变量遮蔽问题

社区中有关于 Go 错误处理的提案和讨论自从 Go 诞生以来就从未停止过，有这么一句玩笑话：如果你能接受 Go 的错误处理，那么你就是一个合格的 Gopher 了



------

## error



error 属于是一种正常的流程错误，它的出现是可以被接受的，大多数情况下应该对其进行处理，当然也可以忽略不管，error 的严重级别不足以停止整个程序的运行。error 本身是一个预定义的接口，该接口下只有一个方法 Error()，该方法的返回值是字符串，用于输出错误信息

```go
type error interface {
   Error() string
}
```

error 在历史上也有过大改，在 1.13 版本时 Go 团队推出了链式错误，且提供了更加完善的错误检查机制，接下来都会一一介绍

<br>

**创建**

创建一个 error 有以下几种方法，第一种是使用 errors 包下的 New 函数：

```go
err := errors.New("这是一个错误")
```

第二种是使用 fmt 包下的 Errorf 函数，可以得到一个格式化参数的 error：

```go
err := fmt.Errorf("这是%d个格式化参数的的错误", 1)
```

下面是一个完整的例子

```go
func sumPositive(i, j int) (int, error) {
   if i <= 0 || j <= 0 {
      return -1, errors.New("必须是正整数")
   }
   return i + j, nil
}
```

大部分情况，为了更好的维护性，一般都不会临时创建 error，而是会将常用的 error 当作全局变量使用，例如下方节选自 os\erros.go 文件的代码

```go
var (
	ErrInvalid = fs.ErrInvalid // "invalid argument"

	ErrPermission = fs.ErrPermission // "permission denied"
	ErrExist      = fs.ErrExist      // "file already exists"
	ErrNotExist   = fs.ErrNotExist   // "file does not exist"
	ErrClosed     = fs.ErrClosed     // "file already closed"

	ErrNoDeadline       = errNoDeadline()       // "file type does not support deadline"
	ErrDeadlineExceeded = errDeadlineExceeded() // "i/o timeout"
)
```

可以看到它们都是被 var 定义的变量

<br>

**自定义错误**

通过实现 Error() 方法，可以很轻易的自定义 error，例如 erros 包下的 errorString 就是一个很简单的实现

```go
func New(text string) error {
   return &errorString{text}
}

// errorString结构体
type errorString struct {
   s string
}

func (e *errorString) Error() string {
   return e.s
}
```

因为 errorString 实现太过于简单，表达能力不足，所以很多开源库包括官方库都会选择自定义 error，以满足不同的错误需求

<br>

**传递**

在一些情况中，调用者调用的函数返回了一个错误，但是调用者本身不负责处理错误，于是也将错误作为返回值返回，抛给上一层调用者，这个过程叫传递，错误在传递的过程中可能会层层包装，当上层调用者想要判断错误的类型来做出不同的处理时，可能会无法判别错误的类别或者误判，而链式错误正是为了解决这种情况而出现的

```go
type wrapError struct {
   msg string
   err error
}

func (e *wrapError) Error() string {
   return e.msg
}

func (e *wrapError) Unwrap() error {
   return e.err
}
```

wrappError 同样实现了 error 接口，也多了一个方法 Unwrap，用于返回其内部对于原 error 的引用，层层包装下就形成了一条错误链表，顺着链表上寻找，很容易就能找到原始错误。由于该结构体并不对外暴露，所以只能使用 fmt.Errorf 函数来进行创建，例如

```go
err := errors.New("这是一个原始错误")
wrapErr := fmt.Errorf("错误，%w", err)
```

使用时，必须使用 %w 格式动词，且参数只能是一个有效的 error

<br>

**处理**

错误处理中的最后一步就是如何处理和检查错误，errors 包提供了几个方便函数用于处理错误

* errors.Unwrap() 函数用于解包一个错误链，其内部实现也很简单

  ```go
  func Unwrap(err error) error {
     u, ok := err.(interface { // 类型断言，是否实现该方法
        Unwrap() error
     })
     if !ok { //没有实现说明是一个基础的error
        return nil
     }
     return u.Unwrap() // 否则调用Unwrap
  }
  ```

  解包后会返回当前错误链所包裹的错误，被包裹的错误可能依旧是一个错误链，如果想要在错误链中找到对应的值或类型，可以递归进行查找匹配，不过标准库已经提供好了类似的函数

* errors.Is 函数的作用是判断错误链中是否包含指定的错误，例子如下

  ```go
  ar originalErr = errors.New("this is an error")
  
  func wrap1() error { // 包裹原始错误
     return fmt.Errorf("wrapp error %w", wrap2())
  }
  
  func wrap2() error { // 原始错误
     return originalErr
  }
  
  func main() {
     err := wrap1()
     if errors.Is(err, originalErr) { // 如果使用if err == originalErr 将会是false
        fmt.Println("original")
     }
  }
  ```

  所以在判断错误时，不应该使用 == 操作符，而是应该使用 errors.Is()

* errors.As() 函数的作用是在错误链中寻找第一个类型匹配的错误，并将值赋值给传入的 err。有些情况下需要将 error 类型的错误转换为具体的错误实现类型，以获得更详细的错误细节，而对一个错误链使用类型断言是无效的，因为原始错误是被结构体包裹起来的，这也是为什么需要 As 函数的原因

<br>

不过官方提供的 errors 包其实并不够用，因为它没有堆栈信息，不能定位，一般会比较推荐使用官方的另一个增强包

```text
github.com/pkg/errors
```

```go
import (
	"fmt"
	"github.com/pkg/errors"
)

func Do() error {
	return errors.New("error")
}

func main() {
	if err := Do(); err != nil {
		fmt.Printf("%+v", err)
	}
}
```

```text
some unexpected error happened
main.Do
        D:/WorkSpace/Code/GoLeran/golearn/main.go:9
main.main
        D:/WorkSpace/Code/GoLeran/golearn/main.go:13
runtime.main
        D:/WorkSpace/Library/go/root/go1.21.3/src/runtime/proc.go:267
runtime.goexit
        D:/WorkSpace/Library/go/root/go1.21.3/src/runtime/asm_amd64.s:1650
```

通过格式化输出，就可以看到堆栈信息了，默认情况下是不会输出堆栈的。这个包相当于是标准库 errors 包的加强版，同样都是官方写的，不知道为什么没有并入标准库



------

## panic



panic 中文译为恐慌，表示十分严重的程序问题，程序需要立即停止来处理该问题，否则程序立即停止运行并输出堆栈信息，panic 是 Go 是运行时异常的表达形式，通常在一些危险操作中会出现，主要是为了及时止损，从而避免造成更加严重的后果。不过 panic 在退出之前会做好程序的善后工作，同时 panic 也可以被恢复来保证程序继续运行

下方是一个向 nil 的 map 写入值的例子，肯定会触发 panic

```go
func main() {
   var dic map[string]int
   dic["a"] = 'a'
}
```

```text
panic: assignment to entry in nil map
```

只要任一协程发生 panic，如果不将其捕获的话，整个程序都会崩溃

<br>

**创建**

显式的创建 panic 十分简单，使用内置函数 panic 即可，函数签名如下

```go
func panic(v any)
```

panic 函数接收一个类型为 any 的参数 v，当输出错误堆栈信息时，v 也会被输出。使用例子如下

```go
func main() {
	initDataBase("", 0)
}

func initDataBase(host string, port int) {
	if len(host) == 0 || port == 0 {
		panic("非法的数据链接参数")
	}
    // ...其他的逻辑 
}
```

当初始化数据库连接失败时，程序就不应该启动，因为没有数据库程序就运行的毫无意义，所以此处应该抛出 panic

```text
panic: 非法的数据链接参数
```

<br>

**善后**

程序因为 panic 退出之前会做一些善后工作，例如执行 defer 语句

```go
func main() {
   defer fmt.Println("A")
   defer fmt.Println("B")
   fmt.Println("C")
   panic("panic")
   defer fmt.Println("D")
}
```

```text
// 输出为
C
B
A
panic: panic
```

并且上游函数的 defer 语句同样会执行，例子如下

```go
func main() {
   defer fmt.Println("A")
   defer fmt.Println("B")
   fmt.Println("C")
   dangerOp()
   defer fmt.Println("D")
}

func dangerOp() {
   defer fmt.Println(1)
   defer fmt.Println(2)
   panic("panic")
   defer fmt.Println(3)
}
```

```text
C
2
1
B
A
panic: panic
```

当发生 panic 时，会立即退出所在函数，并且执行当前函数的善后工作，例如 defer，然后层层上抛，上游函数同样的也进行善后工作，直到程序停止运行

<br>

当子协程发生 panic 时，不会触发当前协程的善后工作，如果直到子协程退出都没有恢复 panic，那么程序将会直接停止运行

```go
var waitGroup sync.WaitGroup

func main() {
	demo()
}

func demo() {
	waitGroup.Add(1)
	defer func() {
		fmt.Println("A")
	}()
	fmt.Println("C")
	go dangerOp()
	waitGroup.Wait() // 父协程阻塞等待子协程执行完毕
	defer fmt.Println("D")
}
func dangerOp() {
	defer fmt.Println(1)
	defer fmt.Println(2)
	panic("panicB")
	defer fmt.Println(3)
	waitGroup.Done()
}
```

```text
C
2
1
panic: panicB
```

可以看到 demo()中的 defer 语句一个都没有执行，程序就直接退出了

<br>

**恢复**

当发生 panic 时，使用内置函数 recover() 可以及时的处理并且保证程序继续运行，必须要在 defer 语句中运行，使用示例如下

```go
func main() {
   dangerOp()
   fmt.Println("程序正常退出")
}

func dangerOp() {
   defer func() {
      if err := recover(); err != nil {
         fmt.Println(err)
         fmt.Println("panic恢复")
      }
   }()
   panic("发生panic")
}
```

调用者完全不知道 dangerOp() 函数内部发生了 panic，程序执行剩下的逻辑后正常退出，所以输出如下

```text
发生panic
panic恢复
程序正常退出
```

但事实上 recover() 的使用有许多隐含的陷阱。例如在 defer 中再次闭包使用 recover

```go
func main() {
	dangerOp()
	fmt.Println("程序正常退出")
}

func dangerOp() {
	defer func() {
		func() {
			if err := recover(); err != nil {
				fmt.Println(err)
				fmt.Println("panic恢复")
			}
		}()
	}()
	panic("发生panic")
}
```

闭包函数可以看作调用了一个函数，panic 是向上传递而不是向下，自然闭包函数也就无法恢复 panic，所以输出如下

```text
panic: 发生panic   
```

<br>

除此之外，还有一种很极端的情况，那就是 panic() 的参数是 nil

```go
func main() {
   dangerOp()
   fmt.Println("程序正常退出")
}

func dangerOp() {
   defer func() {
      if err := recover(); err != nil {
         fmt.Println(err)
         fmt.Println("panic恢复")
      }
   }()
   panic(nil)
}
```

这种情况 panic 确实会恢复，但是不会输出任何的错误信息

<br>

总的来说recover函数有几个注意点

1. 必须在 defer 中使用
2. 多次使用也只会有一个能恢复 panic
3. 闭包 recover 不会恢复外部函数的任何 panic
4. panic 的参数禁止使用 nil



------

## fatal



fatal 是一种极其严重的问题，当发生 fatal 时，程序需要立刻停止运行，不会执行任何善后工作，通常情况下是调用 os 包下的 Exit 函数退出程序，如下所示

```go
func main() {
	dangerOp("")
}

func dangerOp(str string) {
	if len(str) == 0 {
		fmt.Println("fatal")
		os.Exit(1)
	}
	fmt.Println("正常逻辑")
}
```

```text
fatal
```

fatal 级别的问题一般很少会显式的去触发，大多数情况都是被动触发



------

# 文件



Go语言提供文件处理的标准库大致以下几个：

* os 库，负责 OS 文件系统交互的具体实现
* io 库，读写 IO 的抽象层
* fs 库，文件系统的抽象层



------

## 打开



常见的两种打开文件的方式是使用 os 包提供的两个函数，Open 函数返回值一个文件指针和一个错误

```go
func Open(name string) (*File, error)
```

后者 OpenFile 能够提供更加细粒度的控制，函数 Open 就是对 OpenFile 函数的一个简单封装

```go
func OpenFile(name string, flag int, perm FileMode) (*File, error)
```

<br>

第一种使用方法，直接提供对应的文件名即可，代码如下

```go
func main() {
   file, err := os.Open("README.txt")
   fmt.Println(file, err)
}
```

文件的查找路径默认为项目 go.mod 文件所在的路径，由于项目下并没有文件 README.txt，所以自然会返回一个错误

```text
<nil> open README.txt: The system cannot find the file specified.
```

因为 IO 错误的类型有很多，所以需要手动的去判断文件是否存在，同样的 os 包也为此提供了方便函数，修改后的代码如下

```go
func main() {
	file, err := os.Open("README.txt")
	if os.IsNotExist(err) {
		fmt.Println("文件不存在")
	} else if err != nil {
		fmt.Println("文件访问异常")
	} else {
		fmt.Println("文件读取成功", file)
	}
}
```

再次运行输出如下

```text
文件不存在
```

这种函数读取的文件仅仅只是只读的，无法被修改

<br>

通过 OpenFile 函数可以控制更多细节，例如修改文件描述符和文件权限

下面是一个以读写模式打开一个文件的代码例子，权限为 0666，表示为所有人都可以对该文件进行读写，且不存在时会自动创建

```go
func main() {
	file, err := os.OpenFile("README.txt", os.O_RDWR|os.O_CREATE, 0666)
	if os.IsNotExist(err) {
		fmt.Println("文件不存在")
	} else if err != nil {
		fmt.Println("文件访问异常")
	} else {
		fmt.Println("文件打开成功", file.Name())
		file.Close()
	}
}
```

```text
文件打开成功 README.txt
```

<br>

倘若只是想获取该文件的一些信息，并不想读取该文件，可以使用 os.Stat() 函数进行操作，代码示例如下

```go
func main() {
	fileInfo, err := os.Stat("README.txt")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(fmt.Sprintf("%+v", fileInfo))
	}
}
```

```
&{name:README.txt FileAttributes:32 CreationTime:{LowDateTime:3603459389 HighDateTime:31016791} LastAccessTime:{LowDateTime:3603459389 HighDateTime:31016791} LastWriteTime:{LowDateTime:3603459389 HighDateTime:31016791} FileSizeHigh
:0 FileSizeLow:0 Reserved0:0 filetype:0 Mutex:{state:0 sema:0} path:README.txt vol:0 idxhi:0 idxlo:0 appendNameToPath:false}
```

<br>

打开一个文件后永远要记得关闭该文件，通常关闭操作会放在 defer 语句里

```go
defer file.Close()
```



------

## 读取



当成功的打开文件后，便可以进行读取操作了，关于读取文件的操作，*os.File 类型提供了以下几个公开的方法

```go
// 将文件读进传入的字节切片
func (f *File) Read(b []byte) (n int, err error) 

// 相较于第一种可以从指定偏移量读取
func (f *File) ReadAt(b []byte, off int64) (n int, err error) 
```

大多数情况第一种使用的较多。针对于第一种方法，需要自行编写逻辑来进行读取时切片的动态扩容，代码如下：

```go
func ReadFile(file *os.File) ([]byte, error) {
	buffer := make([]byte, 0, 512)
	for {
		// 当容量不足时
		if len(buffer) == cap(buffer) {
			// 扩容
			buffer = append(buffer, 0)[:len(buffer)]
		}
		// 继续读取文件
		offset, err := file.Read(buffer[len(buffer):cap(buffer)])
		// 将已写入的数据归入切片
		buffer = buffer[:len(buffer)+offset]
		// 发生错误时
		if err != nil {
			if errors.Is(err, io.EOF) {
				err = nil
			}
			return buffer, err
		}
	}
}
```

<br>

除此之外，还可以使用两个方便函数来进行文件读取，分别是 os 包下的 ReadFile 函数，以及 io 包下的 ReadAll 函数。对于 os.ReadFile 而言，只需要提供文件路径即可，而对于 io.ReadAll 而言，则需要提供一个 io.Reader 类型的实现

```go
func main() {
	bytes, err := os.ReadFile("README.txt")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(string(bytes))
	}
}
```

```go
func main() {

   file, err := os.OpenFile("README.txt", os.O_RDWR|os.O_CREATE, 0666)
   if err != nil {
      fmt.Println("文件访问异常")
   } else {
      fmt.Println("文件打开成功", file.Name())
      bytes, err := io.ReadAll(file)
      if err != nil {
         fmt.Println(err)
      } else {
         fmt.Println(string(bytes))
      }
      file.Close()
   }
}
```



------

## 写入



os.File 结构体提供了以下几种方法以供写入数据

```go
// 写入字节切片
func (f *File) Write(b []byte) (n int, err error)

// 写入字符串
func (f *File) WriteString(s string) (n int, err error)

// 从指定位置开始写，当以os.O_APPEND模式打开时，会返回错误
func (f *File) WriteAt(b []byte, off int64) (n int, err error)
```

如果想要对一个文件写入数据，则必须以 O_WRONLY 或 O_RDWR 的模式打开，否则无法成功写入文件。下面是一个以 os.O_RDWR|os.O_CREATE|os.O_APPEND|os.O_TRUNC 模式打开文件，且权限为 0666 向指定写入数据的例子

```go
func main() {
	file, err := os.OpenFile("README.txt", os.O_RDWR|os.O_CREATE|os.O_APPEND|os.O_TRUNC, 0666)
	if err != nil {
		fmt.Println("文件访问异常")
	} else {
		fmt.Println("文件打开成功", file.Name())
		for i := 0; i < 5; i++ {
			offset, err := file.WriteString("hello world!\n")
			if err != nil {
				fmt.Println(offset, err)
			}
		}
		fmt.Println(file.Close())
	}
}
```

由于是以 os.O_APPEND 模式打开的文件，所以在写入文件时会将数据添加到文件尾部，执行完毕后文件内容如下

向文件写入字节切片也是类似的操作

<br>

对于写入文件的操作标准库同样提供了方便函数，分别是 os.WriteFile 与 io.WriteString

```go
func main() {
	err := os.WriteFile("README.txt", []byte("hello world!\n"), 0666)
	if err != nil {
		fmt.Println(err)
	}
}
```

```go
func main() {
   file, err := os.OpenFile("README.txt", os.O_RDWR|os.O_CREATE|os.O_APPEND|os.O_TRUNC, 0666)
   if err != nil {
      fmt.Println("文件访问异常")
   } else {
      fmt.Println("文件打开成功", file.Name())
      for i := 0; i < 5; i++ {
         offset, err := io.WriteString(file, "hello world!\n")
         if err != nil {
            fmt.Println(offset, err)
         }
      }
      fmt.Println(file.Close())
   }
}
```

<br>

函数 os.Create 函数用于创建文件，本质上也是对 OpenFile 的封装

```go
func Create(name string) (*File, error) {
   return OpenFile(name, O_RDWR|O_CREATE|O_TRUNC, 0666)
}
```

在创建一个文件时，如果其父目录不存在，将创建失败并会返回错误



------

## 复制



对于复制文件而言，需要同时打开两个文件，第一种方法是将原文件中的数据读取出来，然后写入目标文件中，代码示例如下

```go
func main() {
    // 从原文件中读取数据
	data, err := os.ReadFile("README.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
    // 写入目标文件
	err = os.WriteFile("README(1).txt", data, 0666)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("复制成功")
	}
}
```

<br>

另一种方法是使用 os.File 提供的方法 ReadFrom，打开文件时，一个只读，一个只写

```go
func main() {
	// 以只读的方式打开原文件
	origin, err := os.OpenFile("README.txt", os.O_RDONLY, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer origin.Close()
	// 以只写的方式打开副本文件
	target, err := os.OpenFile("README(1).txt", os.O_WRONLY|os.O_CREATE|os.O_TRUNC, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer target.Close()
	// 从原文件中读取数据，然后写入副本文件
	offset, err := target.ReadFrom(origin)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("文件复制成功", offset)
}
```

这种复制方式需要先将源文件的全部内容读取到内存中，再写入目标文件，文件特别大的时候不建议这么做

<br>

另一种方法就是使用 io.Copy 函数，它则是一边读一边写，先将内容读到缓冲区中，再写入到目标文件中，缓冲区默认大小为 32KB

```go
func main() {
	// 以只读的方式打开原文件
	origin, err := os.OpenFile("README.txt", os.O_RDONLY, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer origin.Close()
	// 以只写的方式打开副本文件
	target, err := os.OpenFile("README(1).txt", os.O_WRONLY|os.O_CREATE|os.O_TRUNC, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer target.Close()
	// 复制
	written, err := io.Copy(target, origin)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(written)
	}
}
```

也可以使用 io.CopyBuffer 来指定缓冲区大小



------

## 重命名



重命名也可以理解为移动文件，会用到 os 包下的 Rename 函数

```go
func main() {
	err := os.Rename("README.txt", "readme.txt")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("重命名成功")
	}
}
```

该函数对于文件夹也是同样的效果



------

## 删除



删除操作相较于其他操作要简单的多，只会用到 os 包下的两个函数

```go
// 删除单个文件或者空目录，当目录不为空时会返回错误
func Remove(name string) error

// 删除指定目录的所有文件和目录包括子目录与子文件
func RemoveAll(path string) error 
```

```go
func main() {
	// 删除当前目录下所有的文件与子目录
	err := os.RemoveAll(".")
	if err != nil {
		fmt.Println(err)
	}else {
		fmt.Println("删除成功")
	}
}
```

```go
func main() {
	// 删除当前目录下指定文件
	err := os.Remove("README.txt")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("删除成功")
	}
}
```



------

## 刷新



os.Sync 这一个函数封装了底层的系统调用 Fsync，用于将操作系统中缓存的 IO 写入落实到磁盘上

```go
func main() {
	create, err := os.Create("test.txt")
	if err != nil {
		panic(err)
	}
	defer create.Close()

	_, err = create.Write([]byte("hello"))
	if err != nil {
		panic(err)
	}

    // 刷盘
	if err := create.Sync();err != nil {
		return
	}
}
```



------

# 反射



反射是一种在运行时检查语言自身结构的机制，它可以很灵活的去应对一些问题，但同时带来的弊端也很明显，例如性能问题等等。在 Go 中，反射与 interface{} 密切相关，很大程度上，只要有 interface{} 出现的地方，就会有反射。Go 中的反射 API 是由标准库 reflect 包提供的



------

## 接口



在开始之前先简单的了解一下位于 runtime 包下的两个接口。在 Go 中，接口本质上是结构体，Go 在运行时将接口分为了两大类，一类是没有方法集的接口，另一个类则是有方法集的接口。对于含有方法集的接口来说，在运行时由如下的结构体 iface 来进行表示

```go
type iface struct {
   tab  *itab // 包含 数据类型，接口类型，方法集等
   data unsafe.Pointer // 指向值的指针
}
```

而对于没有方法集接口来说，在运行时由 eface 结构体来进行表示，如下

```go
type eface struct {
   _type *_type // 类型
   data  unsafe.Pointer // 指向值的指针
}
```

而这两个结构体在 reflect 包下都有与其对应的结构体类型，iface 对应的是 nonEmptyInterface

```go
type nonEmptyInterface struct {
	itab *struct {
		ityp *rtype // 静态接口类型
		typ  *rtype // 动态具体类型
		hash uint32 // 类型哈希
		_    [4]byte
		fun  [100000]unsafe.Pointer // 方法集
	}
	word unsafe.Pointer // 指向值的指针
}
```

而 eface 对应的是 emptyInterface

```go
type emptyInterface struct {
   typ  *rtype // 动态具体类型
   word unsafe.Pointer // 指向指针的值
}
```

Go 语言是一个百分之百的静态类型语言，静态这一词是体现在对外表现的抽象的接口类型是不变的，而动态表示是接口底层存储的具体实现的类型是可以变化的



------

## 桥梁



在 reflect 包下，分别有 reflect.Type 接口类型来表示 Go 中的类型，reflect.Value 结构体类型来表示 Go 中的值

```go
type Type interface {
    ...
    
    Name() string

	PkgPath() string

	Size() uintptr

	String() string

	Kind() Kind
    
    ...
}

type Value struct {
    
   typ *rtype

   ptr unsafe.Pointer

   flag
    
}
```

上面的代码省略了很多细节，先只需要了解这两个类型的存在即可。Go 中所有反射相关的操作都是基于这两个类型，reflect 包提供了两个函数来将 Go 中的类型转换为上述的两种类型以便进行反射操作，分别是 reflect.TypeOf 函数与 reflect.ValueOf 函数

```go
func TypeOf(i any) Type 

func ValueOf(i any) Value 
```

可以看到两个函数的参数类型都是 any，也就是 interface{} 的别名。如果想要进行反射操作，就需要先将其类型转换为 interface{}，这也是为什么前面提到了只要有反射就离不开空接口。不严谨的说，空接口就是连接 Go 类型系统与反射的桥梁，下图很形象的描述了其过程

![image-20241202215926772](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/image-20241202215926772.png)



------

## 核心



在 Go 中有三个经典的反射定律，结合上面所讲的内容也就非常好懂，分别如下：

* 反射可以将 interface{} 类型变量转换成反射对象
* 反射可以将反射对象还原成 interface{} 类型变量
* 要修改反射对象，其值必须是可设置的

这三个定律便是 Go 反射的核心，当需要访问类型相关信息时，便需要用到 reflect.TypeOf，当需要修改反射值时，就需要用到 reflect.ValueOf



------

## 类型



reflect.Type 代表着 Go 中的类型，使用 reflect.TypeOf() 函数可以将变量转换成 reflect.Type。代码示例如下

```go
func main() {
	str := "hello world!"
	reflectType := reflect.TypeOf(str)
	fmt.Println(reflectType) // string
}
```

<br>

**Kind**

对于 Type 而言，Go 内部使用 reflect.Kind 来表示 Go 中的基础类型，其本质上是无符号整型 uint

```go
type Kind uint
```

reflect 包使用 Kind 枚举出了 Go 中所有的基础类型，如下所示

```go
const (
   Invalid Kind = iota
   Bool
   Int
   Int8
   Int16
   Int32
   Int64
   Uint
   Uint8
   Uint16
   Uint32
   Uint64
   Uintptr
   Float32
   Float64
   Complex64
   Complex128
   Array
   Chan
   Func
   Interface
   Map
   Pointer
   Slice
   String
   Struct
   UnsafePointer
)
```

Kind 类型仅仅实现了 Stringer 接口的 String()方法，该类型也仅有这一个方法，String() 方法的返回值来自于一个其内部的 map，如下所示

```go
var kindNames = []string{
   Invalid:       "invalid",
   Bool:          "bool",
   Int:           "int",
   Int8:          "int8",
   Int16:         "int16",
   Int32:         "int32",
   Int64:         "int64",
   Uint:          "uint",
   Uint8:         "uint8",
   Uint16:        "uint16",
   Uint32:        "uint32",
   Uint64:        "uint64",
   Uintptr:       "uintptr",
   Float32:       "float32",
   Float64:       "float64",
   Complex64:     "complex64",
   Complex128:    "complex128",
   Array:         "array",
   Chan:          "chan",
   Func:          "func",
   Interface:     "interface",
   Map:           "map",
   Pointer:       "ptr",
   Slice:         "slice",
   String:        "string",
   Struct:        "struct",
   UnsafePointer: "unsafe.Pointer",
}
```

```go
type Type interface{
    Kind() Kind
}
```

通过 Kind，可以知晓空接口存储的值究竟是什么基础类型，例如

```go
func main() {
    // 声明一个any类型的变量
	var eface any
    // 赋值
	eface = 100
    // 通过Kind方法，来获取其类型
	fmt.Println(reflect.TypeOf(eface).Kind()) // int
}
```

<br>

**Elem**

```go
type Type interface{
    Elem() Type
}
```

使用 Type.Elem() 方法，可以判断类型为 any 的数据结构所存储的元素类型，可接收的底层参数类型必须是指针，切片，数组，通道，映射表其中之一，否则会 panic。下面是代码示例

```go
func main() {
	var eface any
	eface = map[string]int{}
	rType := reflect.TypeOf(eface)
    // key()会返回map的键反射类型
	fmt.Println(rType.Key().Kind()) // int
	fmt.Println(rType.Elem().Kind()) // string
}
```

指针也可以理解为是一个容器，对于指针使用 Elem() 会获得其指向元素的反射类型，代码示例如下

```go
func main() {
	var eface any
    // 赋值指针
	eface = new(strings.Builder)
	rType := reflect.TypeOf(eface)
    // 拿到指针所指向元素的反射类型
	vType := rType.Elem()
    // 输出包路径
	fmt.Println(vType.PkgPath()) // strings
    // 输出其名称
	fmt.Println(vType.Name()) // Builder
}
```

对于数组，切片，通道用使用起来都是类似的

<br>

**Size**

```go
type Type interface{
    Size() uintptr
}
```

通过 Size 方法可以获取对应类型所占的字节大小，示例如下

```go
func main() {
	fmt.Println(reflect.TypeOf(0).Size()) // 8
	fmt.Println(reflect.TypeOf("").Size()) // 16
	fmt.Println(reflect.TypeOf(complex(0, 0)).Size()) // 16
	fmt.Println(reflect.TypeOf(0.1).Size()) // 8
	fmt.Println(reflect.TypeOf([]string{}).Size()) // 24
}
```

<br>

**Comparable**

```go
type Type interface{
    Comparable() bool
}
```

通过 Comparable 方法可以判断一个类型是否可以被比较，例子如下

```go
func main() {
	fmt.Println(reflect.TypeOf("hello world!").Comparable()) // true
	fmt.Println(reflect.TypeOf(1024).Comparable()) // true
	fmt.Println(reflect.TypeOf([]int{}).Comparable()) // false
	fmt.Println(reflect.TypeOf(struct{}{}).Comparable()) // true
}
```

<br>

**Implements**

```go
type Type interface{
    Implements(u Type) bool
}
```

通过 Implements 方法可以判断一个类型是否实现了某一接口

```go
type MyInterface interface {
	My() string
}

type MyStruct struct {
}

func (m MyStruct) My() string {
	return "my"
}

type HisStruct struct {
}

func (h HisStruct) String() string {
	return "his"
}

func main() {
	rIface := reflect.TypeOf(new(MyInterface)).Elem()
	fmt.Println(reflect.TypeOf(new(MyStruct)).Elem().Implements(rIface)) // true
	fmt.Println(reflect.TypeOf(new(HisStruct)).Elem().Implements(rIface)) // false
}
```

<br>

**ConvertibleTo**

```go
type Type interface{
    ConvertibleTo(u Type) bool
}
```

使用 ConvertibleTo 方法可以判断一个类型是否可以被转换为另一个指定的类型

```go
type MyInterface interface {
	My() string
}

type MyStruct struct {
}

func (m MyStruct) My() string {
	return "my"
}

type HisStruct struct {
}

func (h HisStruct) String() string {
	return "his"
}

func main() {
	rIface := reflect.TypeOf(new(MyInterface)).Elem()
	fmt.Println(reflect.TypeOf(new(MyStruct)).Elem().ConvertibleTo(rIface)) // true
	fmt.Println(reflect.TypeOf(new(HisStruct)).Elem().ConvertibleTo(rIface)) // false
}
```



------

## 值



reflect.Value 代表着反射接口的值，使用 reflect.ValueOf() 函数可以将变量转换成 reflect.Value。代码示例如下

```go
func main() {
	str := "hello world!"
	reflectValue := reflect.ValueOf(str)
	fmt.Println(reflectValue) // hello world
}
```

<br>

**Type**

```go
func (v Value) Type() Type
```

Type 方法可以获取一个反射值的类型

```go
func main() {
   num := 114514
   rValue := reflect.ValueOf(num)
   fmt.Println(rValue.Type()) // int
}
```

<br>

**Elem**

```go
func (v Value) Elem() Value 
```

获取一个反射值的元素反射值

```go
func main() {
   num := new(int)
   *num = 114514
   // 以指针为例子
   rValue := reflect.ValueOf(num).Elem()
   fmt.Println(rValue.Interface()) // 114514
}
```

<br>

**指针**

获取一个反射值的指针方式有两种

```go
// 返回一个表示v地址的指针反射值
func (v Value) Addr() Value

// 返回一个指向v的原始值的uinptr 等价于 uintptr(Value.Addr().UnsafePointer())
func (v Value) UnsafeAddr() uintptr

// 返回一个指向v的原始值的uintptr 
// 仅当v的Kind为 Chan, Func, Map, Pointer, Slice, UnsafePointer时，否则会panic
func (v Value) Pointer() uintptr

// 返回一个指向v的原始值的unsafe.Pointer
// 仅当v的Kind为 Chan, Func, Map, Pointer, Slice, UnsafePointer时，否则会panic
func (v Value) UnsafePointer() unsafe.Pointer 
```

示例如下

```go
func main() {
   num := 1024
   ele := reflect.ValueOf(&num).Elem()
   fmt.Println("&num", &num)
   fmt.Println("Addr", ele.Addr())
   fmt.Println("UnsafeAddr", unsafe.Pointer(ele.UnsafeAddr()))
   fmt.Println("Pointer", unsafe.Pointer(ele.Addr().Pointer()))
   fmt.Println("UnsafePointer", ele.Addr().UnsafePointer())
}
```

<br>

**设置值**

```go
func (v Value) Set(x Value)
```

倘若通过反射来修改反射值，那么其值必须是可取址的，这时应该通过指针来修改其元素值，而不是直接尝试修改元素的值

```go
func main() {
   // *int
   num := new(int)
   *num = 114514
   rValue := reflect.ValueOf(num)
    // 获取指针指向的元素
   ele := rValue.Elem()
   fmt.Println(ele.Interface()) // 114514
   ele.SetInt(11)
   fmt.Println(ele.Interface()) // 11
}
```

<br>

获取值

```go
func (v Value) Interface() (i any)
```

通过 Interface() 方法可以获取反射值原有的值

```go
func main() {
   var str string
   str = "hello"
   rValue := reflect.ValueOf(str)
   if v, ok := rValue.Interface().(string); ok {
      fmt.Println(v) // hello
   }
}
```



------

## 函数



通过反射可以获取函数的一切信息，也可以反射调用函数

<br>

**信息**

通过反射类型来获取函数的一切信息

```go
func Max(a, b int) int {
   if a > b {
      return a
   }
   return b
}

func main() {
   rType := reflect.TypeOf(Max)
   // 输出函数名称,字面量函数的类型没有名称
   fmt.Println(rType.Name())
   // 输出参数，返回值的数量
   fmt.Println(rType.NumIn(), rType.NumOut()) // 2 1
   rParamType := rType.In(0)
   // 输出第一个参数的类型
   fmt.Println(rParamType.Kind()) // int
   rResType := rType.Out(0)
   // 输出第一个返回值的类型
   fmt.Println(rResType.Kind()) // int
}
```

<br>

**调用**

通过反射值来调用函数

```go
func (v Value) Call(in []Value) []Value 
```

```go
func main() {
   // 获取函数的反射值
   rType := reflect.ValueOf(Max)
   // 传入参数数组
   rResValue := rType.Call([]reflect.Value{reflect.ValueOf(18), reflect.ValueOf(50)})
   for _, value := range rResValue {
      fmt.Println(value.Interface()) // 50
   }
}
```



------

## 结构体



假设有如下结构体

```go
type Person struct {
	Name    string `json:"name"`
	Age     int    `json:"age"`
	Address string `json:"address"`
	money   int
}

func (p Person) Talk(msg string) string {
	return msg
}
```

<br>

**访问字段**

reflect.StructField 结构的结构如下;

```go
type StructField struct {
	// 字段名称
	Name string
	// 包名
	PkgPath string
	// 类型名
	Type      Type      
	// Tag
	Tag       StructTag 
	// 字段的字节偏移
	Offset    uintptr   
	// 索引
	Index     []int    
	// 是否为嵌套字段
	Anonymous bool      
}
```

访问结构体字段的方法有两种，一种是通过索引来进行访问，另一种是通过名称

通过索引访问的例子如下

```go
func main() {
	rType := reflect.TypeOf(new(Person)).Elem()
	// 输出结构体字段的数量
	fmt.Println(rType.NumField())
	for i := 0; i < rType.NumField(); i++ {
		structField := rType.Field(i)
		fmt.Println(structField.Index, structField.Name, structField.Type, structField.Offset, structField.IsExported())
	}
}
```

通过名称访问的例子如下

```go
func main() {
   rType := reflect.TypeOf(new(Person)).Elem()
   // 输出结构体字段的数量
   fmt.Println(rType.NumField())
   if field, ok := rType.FieldByName("money"); ok {
      fmt.Println(field.Name, field.Type, field.IsExported())
   }
}
```

<br>

**修改字段**

倘若要修改结构体字段值，则必须传入一个结构体指针，下面是一个修改字段的例子

```go
func main() {
	// 传入指针
	rValue := reflect.ValueOf(&Person{
		Name:    "",
		Age:     0,
		Address: "",
		money:   0,
	}).Elem()

	// 获取字段
	name := rValue.FieldByName("Name")
	// 修改字段值
	if (name != reflect.Value{}) { // 如果返回reflect.Value{}，则说明该字段不存在
		name.SetString("jack")
	}
	// 输出结构体
	fmt.Println(rValue.Interface()) // {jack 0  0}
}
```

对于修改结构体私有字段而言，需要进行一些额外的操作，如下

```go
func main() {
	// 传入指针
	rValue := reflect.ValueOf(&Person{
		Name:    "",
		Age:     0,
		Address: "",
		money:   0,
	}).Elem()

	// 获取一个私有字段
	money := rValue.FieldByName("money")
	// 修改字段值
	if (money != reflect.Value{}) {
		// 构造指向该结构体未导出字段的指针反射值
		p := reflect.NewAt(money.Type(), money.Addr().UnsafePointer())
		// 获取该指针所指向的元素，也就是要修改的字段
		field := p.Elem()
		// 修改值
		field.SetInt(164)
	}
	// 输出结构体
	fmt.Printf("%+v\n", rValue.Interface())
}
```

<br>

**访问 Tag**

获取到 StructField 后，便可以直接访问其 Tag

```go
// 如果不存在，ok为false
func (tag StructTag) Lookup(key string) (value string, ok bool)

// 如果不存在，返回空字符串
func (tag StructTag) Get(key string) string
```

```go
func main() {
   rType := reflect.TypeOf(new(Person)).Elem()
   name, ok := rType.FieldByName("Name")
   if ok {
      fmt.Println(name.Tag.Lookup("json")) // name true
      fmt.Println(name.Tag.Get("json")) // false
   }
}
```

<br>

**访问方法**

访问方法与访问字段的过程很相似，只是函数签名略有区别。reflect.Method 结构体如下

```go
type Method struct {
	// 方法名
	Name string
	// 包名
	PkgPath string
	// 方法类型
	Type  Type 
	// 方法对应的函数，第一个参数是接收者
	Func  Value 
	// 索引
	Index int
}
```

访问方法信息示例如下

```go
func main() {
	// 获取结构体反射类型
	rType := reflect.TypeOf(new(Person)).Elem()
	// 输出方法个数
	fmt.Println(rType.NumMethod())
	// 遍历输出方法信息
	for i := 0; i < rType.NumMethod(); i++ {
		method := rType.Method(i)
		fmt.Println(method.Index, method.Name, method.Type, method.IsExported())
	}
}
```

<br>

**调用方法**

调用方法与调用函数的过程相似，而且并不需要手动传入接收者，例子如下

```go
func main() {
   // 获取结构体反射类型
   rValue := reflect.ValueOf(new(Person)).Elem()
   // 输出方法个数
   fmt.Println(rValue.NumMethod())
   // 遍历输出方法信息
   talk := rValue.MethodByName("Talk")
   if (talk != reflect.Value{}) {
      // 调用方法，并获取返回值
      res := talk.Call([]reflect.Value{reflect.ValueOf("hello,reflect!")})
      // 遍历输出返回值
      for _, re := range res {
         fmt.Println(re.Interface())
      }
   }
}
```



------

## 创建



通过反射可以构造新的值，reflect 包同时根据一些特殊的类型提供了不同的更为方便的函数

<br>

**基本类型**

以 string 为例

```go
func main() {
   rValue := reflect.New(reflect.TypeOf(*new(string)))
   rValue.Elem().SetString("hello world")
    fmt.Println(rValue.Elem().Interface()) // hello world
}
```

<br>

**结构体**

```go
type Person struct {
   Name    string `json:"name"`
   Age     int    `json:"age"`
   Address string `json:"address"`
   money   int
}

func (p Person) Talk(msg string) string {
   return msg
}

func main() {
   // 创建结构体反射值
   rType := reflect.TypeOf(new(Person)).Elem()
   person := reflect.New(rType).Elem()
   fmt.Println(person.Interface()) // { 0  0}
}
```

<br>

**切片**

```go
func main() {
   // 创建切片反射值
   rValue := reflect.MakeSlice(reflect.TypeOf(*new([]int)), 10, 10)
   // 遍历赋值
   for i := 0; i < 10; i++ {
      rValue.Index(i).SetInt(int64(i))
   }
   fmt.Println(rValue.Interface()) // [0 1 2 3 4 5 6 7 8 9]
}
```

<br>

**Map**

```go
func main() {
   //构建map反射值
   rValue := reflect.MakeMapWithSize(reflect.TypeOf(*new(map[string]int)), 10)
   // 设置值
   rValue.SetMapIndex(reflect.ValueOf("a"), reflect.ValueOf(1))
   fmt.Println(rValue.Interface()) // map[a:1]
}
```

<br>

**管道**

```go
func main() {
   // 创建管道反射值
   makeChan := reflect.MakeChan(reflect.TypeOf(new(chan int)).Elem(), 0)
   fmt.Println(makeChan.Interface())
}
```

<br>

**函数**

```go
func main() {
    // 传入包装类型和函数体
	fn := reflect.MakeFunc(reflect.TypeOf(new(func(int))).Elem(), func(args []reflect.Value) (results []reflect.Value) {
		for _, arg := range args {
			fmt.Println(arg.Interface())
		}
		return nil
	})
	fmt.Println(fn.Type())
	fn.Call([]reflect.Value{reflect.ValueOf(1024)})
}
```



------

## 完全相等



reflect.DeepEqual 是反射包下提供的一个用于判断两个变量是否完全相等的函数，签名如下

```go
func DeepEqual(x, y any) bool
```

该函数对于每一种基础类型都做了处理，下面是一些类型判断方式

* 数组：数组中的每一个元素都完全相等
* 切片：都为 nil 时，判为完全相等，或者都不为空时，长度范围内的元素完全相等
* 结构体：所有字段都完全相等
* 映射表：都为 nil 时，为完全相等，都不为 nil 时，每一个键所映射的值都完全相等
* 指针：指向同一个元素或指向的元素完全相等
* 接口：接口的具体类型完全相等时
* 函数：只有两者都为 nil 时才是完全相等，否则就不是完全相等



------

# 并发



Go 语言对于并发的支持是纯天然的，这是这门语言的核心所在，其上手难度相对较小，开发人员不太需要关注底层实现就能做出一个相当不错的并发应用，提高了开发人员的下限



------

## 协程



协程（coroutine）是一种轻量级的线程，或者说是用户态的线程，不受操作系统直接调度，由 Go 语言自身的调度器进行运行时调度，因此上下文切换开销非常小，这也是为什么 Go 的并发性能很不错的原因之一。协程这一概念并非 Go 首次提出，Go 也不是第一个支持协程的语言，但 Go 是第一个能够将协程和并发支持的相当简洁和优雅的语言

在 Go 中，创建一个协程十分的简单，仅需要一个 go 关键字，就能够快速开启一个协程，go 关键字后面必须是一个函数调用。例子如下

```go
func main() {
	go fmt.Println("hello world!")
	go hello()
	go func() {
		fmt.Println("hello world!")
	}()
}

func hello() {
	fmt.Println("hello world!")
}
```

以上三种开启协程的方式都是可以的，但是其实这个例子执行过后在大部分情况下什么都不会输出，协程是并发执行的，系统创建协程需要时间，而在此之前，主协程早已运行结束，一旦主线程退出，其他子协程也就自然退出了。并且协程的执行顺序也是不确定的，无法预判的

<br>

最简单的做法就是让主协程等一会儿，需要使用到 time 包下的 Sleep 函数，可以使当前协程暂停一段时间，例子如下

```go
func main() {
	fmt.Println("start")
	for i := 0; i < 10; i++ {
		go fmt.Println(i)
	}
    // 暂停1ms
	time.Sleep(time.Millisecond)
	fmt.Println("end")
}
```

但是顺序可能还是乱的，因此让每次循环都稍微的等一下。例子如下

```go
func main() {
   fmt.Println("start")
   for i := 0; i < 10; i++ {
      go fmt.Println(i)
      time.Sleep(time.Millisecond)
   }
   time.Sleep(time.Millisecond)
   fmt.Println("end")
}
```

上面的例子中结果输出很完美，那么并发的问题解决了吗，不，一点也没有。对于并发的程序而言，不可控的因素非常多，执行的时机，先后顺序，执行过程的耗时等等，倘若循环中子协程的工作不只是一个简单的输出数字，而是一个非常巨大复杂的任务，耗时的不确定的，那么依旧会重现之前的问题

<br>

因此 time.Sleep 并不是一种良好的解决办法，幸运的是 Go 提供了非常多的并发控制手段，常用的并发控制方法有三种：

* channel：管道
* WaitGroup：信号量
* Context：上下文

三种方法有着不同的适用情况，WaitGroup 可以动态的控制一组指定数量的协程，Context 更适合子孙协程嵌套层级更深的情况，管道更适合协程间通信。对于较为传统的锁控制，Go 也对此提供了支持：

* Mutex：互斥锁

* RWMutex ：读写互斥锁



------

## 管道



channel，译为管道，Go 对于管道的作用如下解释：Do not communicate by sharing memory; instead, share memory by communicating

即通过消息来进行内存共享，channel 就是为此而生，它是一种在协程间通信的解决方案，同时也可以用于并发控制，先来认识下 channel 的基本语法。Go 中通过关键字 chan 来代表管道类型，同时也必须声明管道的存储类型，来指定其存储的数据是什么类型，下面的例子是一个普通管道的模样

```go
var ch chan int
```

这是一个管道的声明语句，此时管道还未初始化，其值为 nil，不可以直接使用

<br>

**创建**

在创建管道时，有且只有一种方法，那就是使用内置函数 make，对于管道而言，make 函数接收两个参数，第一个是管道的类型，第二个是可选参数为管道的缓冲大小。例子如下

```go
intCh := make(chan int)
// 缓冲区大小为1的管道
strCh := make(chan string, 1)
```

在使用完一个管道后一定要记得关闭该管道，使用内置函数 close 来关闭一个管道，该函数签名如下

```go
func close(c chan<- Type)
```

一个关闭管道的例子如下

```go
func main() {
	intCh := make(chan int)
	// do something
	close(intCh)
}
```

有些时候使用 defer 来关闭管道可能会更好

<br>

**读写**

对于一个管道而言，Go 使用了两种很形象的操作符来表示读写操作：

* ch <-：表示对一个管道写入数据
* <- ch：表示对一个管道读取数据

<- 很生动的表示了数据的流动方向，来看一个对 int 类型的管道读写的例子

```go
func main() {
    // 如果没有缓冲区则会导致死锁
	intCh := make(chan int, 1)
	defer close(intCh)
    // 写入数据
	intCh <- 114514
    // 读取数据
	fmt.Println(<-intCh)
}
```

上面的例子中创建了一个缓冲区大小为 1 的 int 型管道，对其写入数据 114514，然后再读取数据并输出，最后关闭该管道。对于读取操作而言，还有第二个返回值，一个布尔类型的值，用于表示数据是否读取成功

```go
ints, ok := <-intCh
```

管道中的数据流动方式与队列一样，即先进先出（FIFO），协程对于管道的操作是同步的，在某一个时刻，只有一个协程能够对其写入数据，同时也只有一个协程能够读取管道中的数据

<br>

**无缓冲**

对于无缓冲管道而言，因为缓冲区容量为 0，所以不会临时存放任何数据。正因为无缓冲管道无法存放数据，在向管道写入数据时必须立刻有其他协程来读取数据，否则就会阻塞等待，读取数据时也是同理，这也解释了为什么下面看起来很正常的代码会发生死锁

```go
func main() {
	// 创建无缓冲管道
	ch := make(chan int)
	defer close(ch)
	// 写入数据
	ch <- 123
	// 读取数据
	n := <-ch
	fmt.Println(n)
}
```

无缓冲管道不应该同步的使用，正确来说应该开启一个新的协程来发送数据，如下例

```go
func main() {
	// 创建无缓冲管道
	ch := make(chan int)
	defer close(ch)
	go func() {
		// 写入数据
		ch <- 123
	}()
	// 读取数据
	n := <-ch
	fmt.Println(n)
}
```

<br>

**有缓冲**

当管道有了缓冲区，就像是一个阻塞队列一样，读取空的管道和写入已满的管道都会造成阻塞。无缓冲管道在发送数据时，必须立刻有人接收，否则就会一直阻塞。对于有缓冲管道则不必如此，对于有缓冲管道写入数据时，会先将数据放入缓冲区里，只有当缓冲区容量满了才会阻塞的等待协程来读取管道中的数据。同样的，读取有缓冲管道时，会先从缓冲区中读取数据，直到缓冲区没数据了，才会阻塞的等待协程来向管道中写入数据。因此，无缓冲管道中会造成死锁例子在这里可以顺利运行

尽管可以顺利运行，但这种同步读写的方式是非常危险的，一旦管道缓冲区空了或者满了，将会永远阻塞下去，因为没有其他协程来向管道中写入或读取数据

<br>

利用管道的阻塞条件，可以很轻易的写出一个主协程等待子协程执行完毕的例子

```go
func main() {
   // 创建一个无缓冲管道
   ch := make(chan struct{})
   defer close(ch)
   go func() {
      fmt.Println(2)
      // 写入
      ch <- struct{}{}
   }()
   // 阻塞等待读取
   <-ch
   fmt.Println(1)
}
```

<br>

**注意点**

以下几种情况使用不当会导致管道阻塞：

* 当对一个无缓冲管道直接进行同步读写操作都会导致该协程阻塞
* 当读取一个缓冲区为空的管道时，会导致该协程阻塞
* 当管道的缓冲区已满，对其写入数据会导致该协程阻塞
* 当管道为 nil 时，无论怎样读写都会导致当前协程阻塞

<br>

以下几种情况还会导致 panic：

* 当管道为 nil 时，使用 close 函数对其进行关闭操作会导致 panic
* 对一个已关闭的管道写入数据会导致 panic

<br>

**单向管道**

双向管道指的是既可以写，也可以读，即可以在管道两边进行操作。单向管道指的是只读或只写的管道，即只能在管道的一边进行操作。手动创建的一个只读或只写的管道没有什么太大的意义，因为不能对管道读写就失去了其存在的作用。单向管道通常是用来限制通道的行为，一般会在函数的形参和返回值中出现，例如用于关闭通道的内置函数 close 的函数签名就用到了单向通道

```go
func close(c chan<- Type)
```

close 函数的形参是一个只写通道，所以单向通道的语法如下：

* 箭头符号 <- 在前，就是只读通道，如 <-chan int
* 箭头符号 <- 在后，就是只写通道，如 chan<- string

当尝试对只读的管道写入数据时，将会无法通过编译

双向管道可以转换为单向管道，反过来则不可以。通常情况下，将双向管道传给某个协程或函数并且不希望它读取/发送数据，就可以用到单向管道来限制另一方的行为

<br>

**for range**

通过 for range 语句，可以遍历读取缓冲管道中的数据

```go
func main() {
	ch := make(chan int, 10)
	go func() {
		for i := 0; i < 10; i++ {
			ch <- i
		}
	}()
	for n := range ch {
		fmt.Println(n)
	}
}
```

通常来说，for range 遍历其他可迭代数据结构时，会有两个返回值，第一个是索引，第二个元素值，但是对于管道而言，有且仅有一个返回值，for range 会不断读取管道中的元素，当管道缓冲区为空或无缓冲时，就会阻塞等待，直到有其他协程向管道中写入数据才会继续读取数据。所以输出如下：

```text
0
1                                                           
2                                                           
3                                                           
4                                                           
5                                                           
6                                                           
7                                                           
8                                                           
9                                                           
fatal error: all goroutines are asleep - deadlock!
```

可以看到上面的代码发生了死锁，因为子协程已经执行完毕了，而主协程还在阻塞等待其他协程来向管道中写入数据，所以应该管道在写入完毕后将其关闭。修改为如下代码

```go
func main() {
   ch := make(chan int, 10)
   go func() {
      for i := 0; i < 10; i++ {
         ch <- i
      }
      // 关闭管道
      close(ch)
   }()
   for n := range ch {
      fmt.Println(n)
   }
}
```

写完后关闭管道，上述代码便不再会发生死锁。前面提到过读取管道是有两个返回值的，for range 遍历管道时，当无法成功读取数据时，便会退出循环。第二个返回值指的是能否成功读取数据，而不是管道是否已经关闭，即便管道已经关闭，对于有缓冲管道而言，依旧可以读取数据，并且第二个返回值仍然为 true。看下面的一个例子

```go
func main() {
	ch := make(chan int, 10)
	for i := 0; i < 5; i++ {
		ch <- i
	}
    // 关闭管道
	close(ch)
    // 再读取数据
	for i := 0; i < 6; i++ {
		n, ok := <-ch
		fmt.Println(n, ok)
	}
}
```

输出结果

```text
0 true
1 true 
2 true 
3 true 
4 true 
0 false
```

由于管道已经关闭了，即便缓冲区为空，再读取数据也不会导致当前协程阻塞，可以看到在第六次遍历的时候读取的是零值，并且 ok 为 false

关于管道关闭的时机，应该尽量在向管道发送数据的那一方关闭管道，而不要在接收方关闭管道，因为大多数情况下接收方只知道接收数据，并不知道该在什么时候关闭管道

<br>

**select**

select 在 Linux 系统中，是一种 IO 多路复用的解决方案，类似的，在 Go 中，select 是一种管道多路复用的控制结构。什么是多路复用，简单的用一句话概括：在某一时刻，同时监测多个元素是否可用，被监测的可以是网络请求，文件 IO 等。在 Go 中的 select 监测的元素就是管道，且只能是管道。select 的语法与 switch 语句类似

```go
func main() {
	// 创建三个管道
	chA := make(chan int)
	chB := make(chan int)
	chC := make(chan int)
	defer func() {
		close(chA)
		close(chB)
		close(chC)
	}()
	select {
	case n, ok := <-chA:
		fmt.Println(n, ok)
	case n, ok := <-chB:
		fmt.Println(n, ok)
	case n, ok := <-chC:
		fmt.Println(n, ok)
	default:
		fmt.Println("所有管道都不可用")
	}
}
```

与 switch 类似，select 由多个 case 和一个 default 组成，default 分支可以省略。每一个 case 只能操作一个管道，且只能进行一种操作，要么读要么写，当有多个 case 可用时，select 会伪随机的选择一个 case 来执行。如果所有 case 都不可用，就会执行 default 分支，倘若没有 default 分支，将会阻塞等待，直到至少有一个 case 可用。由于上例中没有对管道写入数据，自然所有的 case 都不可用，所以最终输出为 default 分支的执行结果。稍微修改下后如下：

```go
func main() {
   chA := make(chan int)
   chB := make(chan int)
   chC := make(chan int)
   defer func() {
      close(chA)
      close(chB)
      close(chC)
   }()
   // 开启一个新的协程
   go func() {
      // 向A管道写入数据
      chA <- 1
   }()
   select {
   case n, ok := <-chA:
      fmt.Println(n, ok)
   case n, ok := <-chB:
      fmt.Println(n, ok)
   case n, ok := <-chC:
      fmt.Println(n, ok)
   }
}
```

上例开启了一个新的协程来向管道 A 写入数据，select 由于没有默认分支，所以会一直阻塞等待直到有 case 可用。当管道 A 可用时，执行完对应分支后主协程就直接退出了。要想一直监测管道，可以配合 for 循环使用，如下

```go
func main() {
	chA := make(chan int)
	chB := make(chan int)
	chC := make(chan int)
	defer func() {
		close(chA)
		close(chB)
		close(chC)
	}()
	go Send(chA)
	go Send(chB)
	go Send(chC)
	// for循环
	for {
		select {
		case n, ok := <-chA:
			fmt.Println("A", n, ok)
		case n, ok := <-chB:
			fmt.Println("B", n, ok)
		case n, ok := <-chC:
			fmt.Println("C", n, ok)
		}
	}
}

func Send(ch chan<- int) {
	for i := 0; i < 3; i++ {
		time.Sleep(time.Millisecond)
		ch <- i
	}
}
```

这样确实三个管道都能用上了，但是死循环 +select 会导致主协程永久阻塞，所以可以将其单独放到新协程中，并且加上一些其他的逻辑

```go
func main() {
	chA := make(chan int)
	chB := make(chan int)
	chC := make(chan int)
	defer func() {
		close(chA)
		close(chB)
		close(chC)
	}()

	l := make(chan struct{})

	go Send(chA)
	go Send(chB)
	go Send(chC)

	go func() {
	Loop:
		for {
			select {
			case n, ok := <-chA:
				fmt.Println("A", n, ok)
			case n, ok := <-chB:
				fmt.Println("B", n, ok)
			case n, ok := <-chC:
				fmt.Println("C", n, ok)
			case <-time.After(time.Second): // 设置1秒的超时时间
				break Loop // 退出循环
			}
		}
		l <- struct{}{} // 告诉主协程可以退出了
	}()

	<-l
}

func Send(ch chan<- int) {
	for i := 0; i < 3; i++ {
		time.Sleep(time.Millisecond)
		ch <- i
	}
}
```

上例中通过 for 循环配合 select 来一直监测三个管道是否可以用，并且第四个 case 是一个超时管道，超时过后便会退出循环，结束子协程

<br>

上一个例子用到了 time.After 函数，其返回值是一个只读的管道，该函数配合 select 使用可以非常简单的实现超时机制，例子如下

```go
func main() {
	chA := make(chan int)
	defer close(chA)
	go func() {
		time.Sleep(time.Second * 2)
		chA <- 1
	}()
	select {
	case n := <-chA:
		fmt.Println(n)
	case <-time.After(time.Second):
		fmt.Println("超时")
	}
}
```

<br>

当 select 语句中什么都没有时，就会永久阻塞

```go
func main() {
	fmt.Println("start")
	select {}
	fmt.Println("end")
}
```

end 永远也不会输出，主协程会一直阻塞，这种情况一般是有特殊用途



------

## WaitGroup



sync.WaitGroup 是 sync 包下提供的一个结构体，WaitGroup 即等待执行，使用它可以很轻易的实现等待一组协程的效果。该结构体只对外暴露三个方法：

* Add 方法用于指明要等待的协程的数量

  ```go
  func (wg *WaitGroup) Add(delta int)
  ```

* Done 方法表示当前协程已经执行完毕

  ```go
  func (wg *WaitGroup) Done()
  ```

* Wait 方法等待子协程结束，否则就阻塞

  ```go
  func (wg *WaitGroup) Wait()
  ```

<br>

WaitGroup 使用起来十分简单，属于开箱即用。其内部的实现是计数器 + 信号量，程序开始时调用 Add 初始化计数，每当一个协程执行完毕时调用 Done，计数就 -1，直到减为 0，而在此期间，主协程调用 Wait 会一直阻塞直到全部计数减为 0，然后才会被唤醒。看一个简单的使用例子

```go
func main() {
	var wait sync.WaitGroup
	// 指定子协程的数量
	wait.Add(1)
	go func() {
		fmt.Println(1)
		// 执行完毕
		wait.Done()
	}()
	// 等待子协程
	wait.Wait()
	fmt.Println(2)
}
```

针对协程介绍中最开始的例子，可以做出如下修改

```go
func main() {
   var mainWait sync.WaitGroup
   var wait sync.WaitGroup
   // 计数10
   mainWait.Add(10)
   fmt.Println("start")
   for i := 0; i < 10; i++ {
      // 循环内计数1
      wait.Add(1)
      go func() {
         fmt.Println(i)
         // 两个计数-1
         wait.Done()
         mainWait.Done()
      }()
      // 等待当前循环的协程执行完毕
      wait.Wait()
   }
   // 等待所有的协程执行完毕
   mainWait.Wait()
   fmt.Println("end")
}
```

<br>

WaitGroup 通常适用于可动态调整协程数量的时候，例如事先知晓协程的数量，又或者在运行过程中需要动态调整。WaitGroup 的值不应该被复制，复制后的值也不应该继续使用，尤其是将其作为函数参数传递时，因该传递指针而不是值。倘若使用复制的值，计数完全无法作用到真正的 WaitGroup 上，这可能会导致主协程一直阻塞等待，程序将无法正常运行。例如下方的代码

```go
func main() {
	var mainWait sync.WaitGroup
	mainWait.Add(1)
	hello(mainWait)
	mainWait.Wait()
	fmt.Println("end")
}
func hello(wait sync.WaitGroup) {
	fmt.Println("hello")
	wait.Done()
}
```

错误提示所有的协程都已经退出，但主协程依旧在等待，这就形成了死锁，因为 hello 函数内部对一个形参 WaitGroup 调用 Done 并不会作用到原来的 mainWait 上，所以应该使用指针来进行传递



------

## Context



Context 译为上下文，是 Go 提供的一种并发控制的解决方案，相比于管道和 WaitGroup，它可以更好的控制子孙协程以及层级更深的协程。Context 本身是一个接口，只要实现了该接口都可以称之为上下文例如著名 Web 框架 Gin 中的 gin.Context。context 标准库也提供了几个实现，分别是：

* emptyCtx
* cancelCtx
* timerCtx
* valueCtx

<br>

**Context**

先来看看 Context 接口的定义，再去了解它的具体实现

```go
type Context interface {
   
   // 该方法具有两个返回值，deadline是截止时间，即上下文应该取消的时间
   // 第二个值是是否设置deadline，如果没有设置则一直为false
   Deadline() (deadline time.Time, ok bool)

   // 其返回值是一个空结构体类型的只读管道，该管道仅仅起到通知作用，不传递任何数据
   // 当上下文所做的工作应该取消时，该通道就会被关闭，对于一些不支持取消的上下文，可能会返回 nil
   Done() <-chan struct{}

   // 该方法返回一个error，用于表示上下关闭的原因
   // 当Done管道没有关闭时，返回nil，如果关闭过后，会返回一个err来解释为什么会关闭
   Err() error

   // 该方法返回对应的键值，如果 key 不存在，或者不支持该方法，就会返回 nil
   Value(key any) any
}
```

<br>

**emptyCtx**

顾名思义，emptyCtx 就是空的上下文，context 包下所有的实现都是不对外暴露的，但是提供了对应的函数来创建上下文。emptyCtx 就可以通过 context.Background 和 context.TODO 来进行创建。两个函数如下

```go
var (
	background = new(emptyCtx)
	todo       = new(emptyCtx)
)

func Background() Context {
	return background
}

func TODO() Context {
	return todo
}
```

可以看到仅仅只是返回了 emptyCtx 指针。emptyCtx 的底层类型实际上是一个 int，之所以不使用空结构体，是因为 emptyCtx 的实例必须要有不同的内存地址，它没法被取消，没有 deadline，也不能取值，实现的方法都是返回零值

emptyCtx 通常是用来当作最顶层的上下文，在创建其他三种上下文时作为父上下文传入。context 包中的各个实现关系如下图所示

![image-20241209235922178](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/image-20241209235922178.png)

<br>

**valueCtx**

valueCtx 实现比较简单，其内部只包含一对键值对，和一个内嵌的 Context 类型的字段

```go
type valueCtx struct {
   Context
   key, val any
}
```

其本身只实现了 Value 方法，逻辑也很简单，当前上下文找不到就去父上下文找

```go
func (c *valueCtx) Value(key any) any {
   if c.key == key {
      return c.val
   }
   return value(c.Context, key)
}
```

valueCtx 多用于在多级协程中传递一些数据，无法被取消，因此 ctx.Done 永远会返回 nil，select 会忽略掉 nil 管道

<br>

**cancelCtx**

cancelCtx 以及 timerCtx 都实现了 canceler 接口，接口类型如下

```go
type canceler interface {
    // removeFromParent 表示是否从父上下文中删除自身
    // err 表示取消的原因
	cancel(removeFromParent bool, err error)
    // Done 返回一个管道，用于通知取消的原因
	Done() <-chan struct{}
}
```

cancel 方法不对外暴露，在创建上下文时通过闭包将其包装为返回值以供外界调用，就如 context.WithCancel 源代码中所示

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) {
   if parent == nil {
      panic("cannot create context from nil parent")
   }
   c := newCancelCtx(parent)
   // 尝试将自身添加进父级的children中
   propagateCancel(parent, &c)
   // 返回context和一个函数
   return &c, func() { c.cancel(true, Canceled) }
}
```

cancelCtx 译为可取消的上下文，创建时，如果父级实现了 canceler，就会将自身添加进父级的 children 中，否则就一直向上查找。如果所有的父级都没有实现 canceler，就会启动一个协程等待父级取消，然后当父级结束时取消当前上下文。当调用 cancelFunc 时，Done 通道将会关闭，该上下文的任何子级也会随之取消，最后会将自身从父级中删除

<br>

**timerCtx**

timerCtx 在 cancelCtx 的基础之上增加了超时机制，context 包下提供了两种创建的函数，分别是 WithDeadline 和 WithTimeout，两者功能类似，前者是指定一个具体的超时时间，比如指定一个具体时间 2023/3/20 16:32:00，后者是指定一个超时的时间间隔，比如 5 分钟后。两个函数的签名如下

```go
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc)

func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

timerCtx 会在时间到期后自动取消当前上下文，取消的流程除了要额外的关闭 timer 之外，基本与 cancelCtx 一致。下面是一个简单的 timerCtx 的使用示例

```go
var wait sync.WaitGroup

func main() {
	deadline, cancel := context.WithDeadline(context.Background(), time.Now().Add(time.Second))
	defer cancel()
	wait.Add(1)
	go func(ctx context.Context) {
		defer wait.Done()
		for {
			select {
			case <-ctx.Done():
				fmt.Println("上下文取消", ctx.Err())
				return
			default:
				fmt.Println("等待取消中...")
			}
			time.Sleep(time.Millisecond * 200)
		}
	}(deadline)
	wait.Wait()
}
```

尽管上下文到期会自动取消，但是为了保险起见，在相关流程结束后，最好手动取消上下文



------

## 锁



Go 中 sync 包下的 Mutex 与 RWMutex 提供了互斥锁与读写锁两种实现，且提供了非常简单易用的 API，加锁只需要 Lock()，解锁也只需要 Unlock()。需要注意的是，Go 所提供的锁都是非递归锁，也就是不可重入锁，所以重复加锁或重复解锁都会导致 fatal。锁的意义在于保护不变量，加锁是希望数据不会被其他协程修改

倘若是递归锁的话，就可能会发生如下情况

```go
func DoSomething() {
	Lock()
    DoOther()
	Unlock()
}

func DoOther() {
	Lock()
	// do other
	Unlock()
}
```

DoSomthing 函数显然不知道 DoOther 函数可能会对数据做点什么，从而修改了数据，比如再开几个子协程破坏了不变量。这在 Go 中是行不通的，一旦加锁以后就必须保证不变量的不变性，此时重复加锁解锁都会导致死锁。所以在编写代码时应该避免上述情况，必要时在加锁的同时立即使用 defer 语句解锁

<br>

**互斥锁**

sync.Mutex 是 Go 提供的互斥锁实现，其实现了 sync.Locker 接口

```go
type Locker interface {
   // 加锁
   Lock()
   // 解锁
   Unlock()
}
```

使用互斥锁输出  1 到 10

```go
var wait sync.WaitGroup
var count = 0

var lock sync.Mutex

func main() {
	wait.Add(10)
	for i := 0; i < 10; i++ {
		go func(data *int) {
			// 加锁
			lock.Lock()
			// 模拟访问耗时
			time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
			// 访问数据
			temp := *data
			// 模拟计算耗时
			time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
			ans := 1
			// 修改数据
			*data = temp + ans
			// 解锁
			lock.Unlock()
			fmt.Println(*data)
			wait.Done()
		}(&count)
	}
	wait.Wait()
	fmt.Println("最终结果", count)
}
```

每一个协程在访问数据前，都先上锁，更新完成后再解锁，其他协程想要访问就必须要先获得锁，否则就阻塞等待

<br>

**读写锁**

互斥锁适合读操作与写操作频率都差不多的情况，对于一些读多写少的数据，如果使用互斥锁，会造成大量的不必要的协程竞争锁，这会消耗很多的系统资源，这时候就需要用到读写锁，即读写互斥锁，对于一个协程而言：

* 如果获得了读锁，其他协程进行写操作时会阻塞，其他协程进行读操作时不会阻塞
* 如果获得了写锁，其他协程进行写操作时会阻塞，其他协程进行读操作时会阻塞

Go 中读写互斥锁的实现是 sync.RWMutex，它也同样实现了 Locker 接口，但它提供了更多可用的方法，如下：

```go
// 加读锁
func (rw *RWMutex) RLock() 

// 尝试加读锁
func (rw *RWMutex) TryRLock() bool 

// 解读锁
func (rw *RWMutex) RUnlock() 

// 加写锁
func (rw *RWMutex) Lock()

// 尝试加写锁
func (rw *RWMutex) TryLock() bool 

// 解写锁
func (rw *RWMutex) Unlock()
```

其中 TryRlock 与 TryLock 两个尝试加锁的操作是非阻塞式的，成功加锁会返回 true，无法获得锁时并不会阻塞而是返回 false。读写互斥锁内部实现依旧是互斥锁，并不是说分读锁和写锁就有两个锁，从始至终都只有一个锁。下面来看一个读写互斥锁的使用案例

```go
var wait sync.WaitGroup
var count = 0

var rw sync.RWMutex

func main() {
	wait.Add(12)
	// 读多写少
	go func() {
		for i := 0; i < 3; i++ {
			go Write(&count)
		}
		wait.Done()
	}()
	go func() {
		for i := 0; i < 7; i++ {
			go Read(&count)
		}
		wait.Done()
	}()
	// 等待子协程结束
	wait.Wait()
	fmt.Println("最终结果", count)
}

func Read(i *int) {
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(500)))
	rw.RLock()
	fmt.Println("拿到读锁")
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
	fmt.Println("释放读锁", *i)
	rw.RUnlock()
	wait.Done()
}

func Write(i *int) {
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
	rw.Lock()
	fmt.Println("拿到写锁")
	temp := *i
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
	*i = temp + 1
	fmt.Println("释放写锁", *i)
	rw.Unlock()
	wait.Done()
}
```

<br>

**条件变量**

条件变量，与互斥锁一同出现和使用，所以有些人可能会误称为条件锁，但它并不是锁，是一种通讯机制。Go 中的 sync.Cond 对此提供了实现，而创建条件变量的函数签名如下：

```go
func NewCond(l Locker) *Cond 
```

可以看到创建一个条件变量前提就是需要创建一个锁，sync.Cond 提供了如下的方法以供使用

```go
// 阻塞等待条件生效，直到被唤醒
func (c *Cond) Wait()

// 唤醒一个因条件阻塞的协程
func (c *Cond) Signal()

// 唤醒所有因条件阻塞的协程
func (c *Cond) Broadcast()
```

条件变量使用起来非常简单，将上面的读写互斥锁的例子稍微修改下即可

```go
var wait sync.WaitGroup
var count = 0

var rw sync.RWMutex

// 条件变量
var cond = sync.NewCond(rw.RLocker())

func main() {
	wait.Add(12)
	// 读多写少
	go func() {
		for i := 0; i < 3; i++ {
			go Write(&count)
		}
		wait.Done()
	}()
	go func() {
		for i := 0; i < 7; i++ {
			go Read(&count)
		}
		wait.Done()
	}()
	// 等待子协程结束
	wait.Wait()
	fmt.Println("最终结果", count)
}

func Read(i *int) {
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(500)))
	rw.RLock()
	fmt.Println("拿到读锁")
	// 条件不满足就一直阻塞
	for *i < 3 {
		cond.Wait()
	}
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
	fmt.Println("释放读锁", *i)
	rw.RUnlock()
	wait.Done()
}

func Write(i *int) {
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
	rw.Lock()
	fmt.Println("拿到写锁")
	temp := *i
	time.Sleep(time.Millisecond * time.Duration(rand.Intn(1000)))
	*i = temp + 1
	fmt.Println("释放写锁", *i)
	rw.Unlock()
	// 唤醒所有因条件变量阻塞的协程
	cond.Broadcast()
	wait.Done()
}
```

在创建条件变量时，因为在这里条件变量作用的是读协程，所以将读锁作为互斥锁传入，如果直接传入读写互斥锁会导致写协程重复解锁的问题

可以看到 rlocker 也只是把读写互斥锁的读锁操作封装了一下，实际上是同一个引用，依旧是同一个锁。读协程读取数据时，如果小于 3 就会一直阻塞等待，直到数据大于 3，而写协程在更新数据后都会尝试唤醒所有因条件变量而阻塞的协程

对于条件变量，应该使用 for 而不是 if，应该使用循环来判断条件是否满足，因为协程被唤醒时并不能保证当前条件就已经满足了



------

## sync



Go 中很大一部分的并发相关的工具都是 sync 标准库提供的，sync 包下还有一些其他的工具可以使用

<br>

**Once**

当在使用一些数据结构时，如果这些数据结构太过庞大，可以考虑采用懒加载的方式，即真正要用到它的时候才会初始化该数据结构。如下面的例子

```go
var wait sync.WaitGroup

func main() {
	var slice MySlice
	wait.Add(4)
	for i := 0; i < 4; i++ {
		go func() {
			slice.Add(1)
			wait.Done()
		}()
	}
	wait.Wait()
	fmt.Println(slice.Len())
}

type MySlice struct {
	s []int
	o sync.Once
}

func (m *MySlice) Get(i int) (int, bool) {
	if m.s == nil {
		return 0, false
	} else {
		return m.s[i], true
	}
}

func (m *MySlice) Add(i int) {
	// 当真正用到切片的时候，才会考虑去初始化
	m.o.Do(func() {
		fmt.Println("初始化")
		if m.s == nil {
			m.s = make([]int, 0, 10)
		}
	})
	m.s = append(m.s, i)
}

func (m *MySlice) Len() int {
	return len(m.s)
}
```

<br>

**Pool**

sync.Pool 的设计目的是用于存储临时对象以便后续的复用，是一个临时的并发安全对象池，将暂时用不到的对象放入池中，在后续使用中就不需要再额外的创建对象可以直接复用，减少内存的分配与释放频率，最重要的一点就是降低 GC 压力。sync.Pool 总共只有两个方法，如下：

```go
// 申请一个对象
func (p *Pool) Get() any

// 放入一个对象
func (p *Pool) Put(x any)
```

并且 sync.Pool 有一个对外暴露的 New 字段，用于对象池在申请不到对象时初始化一个对象

```go
New func() any
```

```go
var wait sync.WaitGroup

// 临时对象池
var pool sync.Pool

// 用于计数过程中总共创建了多少个对象
var numOfObject atomic.Int64

// BigMemData 假设这是一个占用内存很大的结构体
type BigMemData struct {
   M string
}

func main() {
   pool.New = func() any {
      numOfObject.Add(1)
      return BigMemData{"大内存"}
   }
   wait.Add(1000)
   // 这里开启1000个协程
   for i := 0; i < 1000; i++ {
      go func() {
         // 申请对象
         val := pool.Get()
         // 使用对象
         _ = val.(BigMemData)
         // 用完之后再释放对象
         pool.Put(val)
         wait.Done()
      }()
   }
   wait.Wait()
   fmt.Println(numOfObject.Load()) // 5
}
```

即便开启了 1000 个协程，整个过程中也只创建了 5 个对象，如果不采用对象池的话 1000 个协程将会创建 1000 个对象，这种优化带来的提升是显而易见的，尤其是在并发量特别大和实例化对象成本特别高的时候更能体现出优势

在使用 sync.Pool 时需要注意几个点：

* 临时对象：sync.Pool 只适合存放临时对象，池中的对象可能会在没有任何通知的情况下被 GC 移除，所以并不建议将网络链接，数据库连接这类存入 sync.Pool 中
* 不可预知：sync.Pool 在申请对象时，无法预知这个对象是新创建的还是复用的，也无法知晓池中有几个对象
* 并发安全：官方保证 sync.Pool 一定是并发安全，但并不保证用于创建对象的 New 函数就一定是并发安全的，New 函数是由使用者传入的，所以 New 函数的并发安全性要由使用者自己来维护，这也是为什么上例中对象计数要用到原子值的原因
* 最后需要注意的是，当使用完对象后，一定要释放回池中，如果用了不释放那么对象池的使用将毫无意义

<br>

**Map**

sync.Map 是官方提供的一种并发安全 Map 的实现，开箱即用，使用起来十分的简单，下面是该结构体对外暴露的方法：

```go
// 根据一个key读取值，返回值会返回对应的值和该值是否存在
func (m *Map) Load(key any) (value any, ok bool)

// 存储一个键值对
func (m *Map) Store(key, value any)

// 删除一个键值对
func (m *Map) Delete(key any)

// 如果该key已存在，就返回原有的值，否则将新的值存入并返回，当成功读取到值时，loaded为true，否则为false
func (m *Map) LoadOrStore(key, value any) (actual any, loaded bool)

// 删除一个键值对，并返回其原有的值，loaded的值取决于key是否存在
func (m *Map) LoadAndDelete(key any) (value any, loaded bool)

// 遍历Map，当f()返回false时，就会停止遍历
func (m *Map) Range(f func(key, value any) bool) 
```

下面用一个简单的示例来演示下 sync.Map 的基本使用

```go
func main() {
	var syncMap sync.Map
	// 存入数据
	syncMap.Store("a", 1)
	syncMap.Store("a", "a")
	// 读取数据
	fmt.Println(syncMap.Load("a"))
	// 读取并删除
	fmt.Println(syncMap.LoadAndDelete("a"))
	// 读取或存入
	fmt.Println(syncMap.LoadOrStore("a", "hello world"))
	syncMap.Store("b", "goodbye world")
	// 遍历map
	syncMap.Range(func(key, value any) bool {
		fmt.Println(key, value)
		return true
	})
}
```



------

## 原子



在计算机学科中，原子或原语操作，通常用于表述一些不可再细化分割的操作，由于这些操作无法再细化为更小的步骤，在执行完毕前，不会被其他的任何协程打断，所以执行结果要么成功要么失败，没有第三种情况可言，如果出现了其他情况，那么它就是不是原子操作

<br>

好在大多情况下并不需要自行编写汇编，Go 标准库 sync/atomic 包下已经提供了原子操作相关的 API，其提供了以下几种类型以供进行原子操作

```go
atomic.Bool{}
atomic.Pointer[]{}
atomic.Int32{}
atomic.Int64{}
atomic.Uint32{}
atomic.Uint64{}
atomic.Uintptr{}
atomic.Value{}
```

其中 Pointer 原子类型支持泛型，Value 类型支持存储任何类型，除此之外，还提供了许多函数来方便操作。因为原子操作的粒度过细，在大多数情况下，更适合处理这些基础的数据类型

atmoic 包下原子操作只有函数签名，没有具体实现，具体的实现是由 plan9 汇编编写

<br>

每一个原子类型都会提供以下三个方法：

* Load()：原子的获取值
* Swap(newVal type) (old type)：原子的交换值，并且返回旧值
* Store(val type)：原子的存储值

不同的类型可能还会有其他的额外方法，比如整型类型都会提供 Add 方法来实现原子加减操作

<br>

atmoic 包还提供了 CompareAndSwap 操作，也就是 CAS，它是乐观锁的一种典型实现。乐观锁本身并不是锁，是一种并发条件下无锁化并发控制方式。之所以被称作乐观锁，是因为它总是乐观的假设共享数据不会被修改，仅当发现数据未被修改时才会去执行对应操作，而前面了解到的互斥量就是悲观锁，互斥量总是悲观的认为共享数据肯定会被修改，所以在操作时会加锁，操作完毕后就会解锁。由于无锁化实现的并发安全效率相对于锁要高一些，许多并发安全的数据结构都采用了 cAS 来进行实现，不过真正的效率要结合具体使用场景来看。看下面的一个例子：

```go
var lock sync.Mutex

var count int

func Add(num int) {
   lock.Lock()
   count += num
   lock.Unlock()
}
```

这是一个使用互斥锁的例子，每次增加数字前都会先上锁，执行完毕后就会解锁，过程中会导致其他协程阻塞，接下来使用 CAS 改造一下：

```go
var count int64

func Add(num int64) {
	for {
		expect := atomic.LoadInt64(&count)
		if atomic.CompareAndSwapInt64(&count, expect, expect+num) {
			break
		}
	}
}
```

对于 CAS 而言，有三个参数，内存值，期望值，新值。执行时，CAS 会将期望值与当前内存值进行比较，如果内存值与期望值相同，就会执行后续的操作，否则的话什么也不做。对于 Go 中 atomic 包下的原子操作，CAS 相关的函数则需要传入地址，期望值，新值，且会返回是否成功替换的布尔值

在 CAS 的例子中，首先会通过 LoadInt64 来获取期望值，随后使用 CompareAndSwapInt64 来进行比较交换，如果不成功的话就不断循环，直到成功。这样无锁化的操作虽然不会导致协程阻塞，但是不断的循环对于 CPU 而言依旧是一个不小的开销，所以在一些实现中失败达到了一定次数可能会放弃操作。但是对于上面的操作而言，仅仅只是简单的数字相加，涉及到的操作并不复杂，所以完全可以考虑无锁化实现

大多数情况下，仅仅只是比较值是无法做到并发安全的，比如因 CAS 引起 ABA 问题，就需要使用额外加入 version 来解决问题



------

# 模块



每一个现代语言都会有属于自己的一个成熟的依赖管理工具，例如 Java 的 Maven、Gradle，Python 的 Pip，NodeJs 的 Npm 等，一个好的依赖管理工具可以为开发者省去不少时间并且可以提升开发效率。然而 Go 在早期并没有一个成熟的依赖管理解决方案，那时所有的代码都存放在 GOPATH 目录下，对于工程项目而言十分的不友好，版本混乱，依赖难以管理，为了解决这个问题，各大社区开发者百家争鸣，局面一时间混乱了起来，期间也不乏出现了一些佼佼者例如 Vendor，直到 Go1.11 官方终于推出了 Go Mod 这款官方的依赖管理工具，结束了先前的混乱局面，并在后续的更新中不断完善，淘汰掉了曾经老旧的工具。时至今日，几乎所有的 Go 项目都在采用 Go Mod



## 设置代理



Go 虽然没有像 Maven Repo，PyPi，NPM 这样类似的中央仓库，但是有一个官方的代理仓库：[Go modules services (golang.org)](https://proxy.golang.org/)，它会根据版本及模块名缓存开发者下载过的模块。不过由于其服务器部署在国外，访问速度对于国内的用户不甚友好，所以我们需要修改默认的模块代理地址，目前国内做的比较好的有以下几家：

* [GOPROXY.IO - 一个全球代理 为 Go 模块而生](https://goproxy.io/zh/)
* [七牛云 - Goproxy.cn](https://goproxy.cn/)

以七牛云为例，执行如下命令来修改 Go 代理，其中的 direct 表示代理下载失败后绕过代理缓存直接访问源代码仓库

```bash
$ go env -w GOPROXY=https://goproxy.cn,direct
```

代理修改成功后，下载依赖就会非常的迅速



------

## 下载依赖



修改完代理后，接下来安装一个第三方依赖试试，Go官方有专门的依赖查询网站：[Go Packages](https://pkg.go.dev/)

在里面搜索著名的 Web 框架 Gin

![image-20241216004951731](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/image-20241216004951731.png)

这里会出现很多搜索结果，在使用第三方依赖时，需要结合引用次数和更新时间来决定是否采用该依赖，这里直接选择第一个

![image-20241216005043598](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/image-20241216005043598.png)

进入对应的页面后，可以看出这是该依赖的一个文档页面，有着非常多关于它的详细信息，后续查阅文档时也可以来这里

![image-20241216005111215](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/image-20241216005111215.png)这里只需要将它的地址复制下来，然后在之前创建的项目下使用 go get 命令，命令如下

```bash
$ go get github.com/gin-gonic/gin
```

过程中会下载很多的依赖，只要没有报错就说明下载成功，完成后查看 go.mod 文件，可以发现相较于之前多了很多东西，同时也会发现目录下多了一个名为 go.sum 的文件，这个后面再做介绍



当不再需要某一个依赖时，也可以使用 go get 命令来删除该依赖，这里以删除 Gin 为例子

```bash
$ go get github.com/gin-gonic/gin@none
go: removed github.com/gin-gonic/gin v1.9.0
```

在依赖地址后面加上 @none 即可删除该依赖，结果也提示了删除成功，此时再次查看 go.mod 文件会发现没有了 Gin 依赖

当需要升级最新版本时，可以加上`@latest`后缀，或者可以自行查询可用的Release版本号

```bash
$ go get -u github.com/gin-gonic/gin@latest
```



------

## 安装命令行



go install 命令会将第三方依赖下载到本地并编译成二进制文件，得益于 go 的编译速度，这一过程通常不会花费太多时间，然后 go 会将其存放在 $GOPATH/bin 或者 $GOBIN 目录下，以便在全局可以执行该二进制文件（前提是你将这些路径添加到了环境变量中）

在使用 install 命令时，必须指定版本号

```bash
$ go install github.com/go-delve/delve/cmd/dlv@latest
```



------

## 常用命令



上述所有的内容都只是在讲述 Go Mod 的基本使用，但事实上要学会 Go Mod 仅仅只有这些是完全不够的。官方对于模块的定义为：一组被版本标记的包集合。上述定义中，包应该是再熟悉不过的概念了，而版本则是要遵循语义化版本号，定义为：v(major).(minor).(patch) 的格式，例如 Go 的版本号 v1.20.1，主版本号是 1，小版本号是 20，补丁版本是 1，合起来就是 v1.20.1，下面是详细些的解释：

* major：当 major 版本变化时，说明项目发生了不兼容的改动，老版本的项目升级到新版本大概率没法正常运行
* minor：当 minor 版本变化时，说明项目增加了新的特性，只是先前版本的基础只是增加了新的功能
* patch：当 patch 版本发生变化时，说明只是有 bug 被修复了，没有增加任何新功能

<br>

| 命令                 | 说明                       |
| -------------------- | -------------------------- |
| `go mod download`    | 下载当前项目的依赖包       |
| `go mod edit`        | 编辑go.mod文件             |
| `go mod graph`       | 输出模块依赖图             |
| `go mod init`        | 在当前目录初始化go mod     |
| `go mod tidy`        | 清理项目模块               |
| `go mod verify`      | 验证项目的依赖合法性       |
| `go mod why`         | 解释项目哪些地方用到了依赖 |
| `go clean -modcache` | 用于删除项目模块依赖缓存   |
| `go list -m`         | 列出模块                   |

前往 [go mod cmd](https://golang.halfiisland.com/cmd.html#mod) 了解命令的更多有关信息



------

## 版本选择



Go 在依赖版本选择时，遵循最小版本选择原则。下面是一个官网给的例子，主模块引用了模块 A 的 1.2 版本和模块 B 的 1.2 版本，同时模块 A 的 1.2 版本引用了模块 C 的 1.3 版本，模块 B 的 1.2 版本引用了模块 C 的 1.4 版本，并且模块 C 的 1.3 和 1.4 版本都同时引用了模块 D 的 1.2 版本，根据最小可用版本原则，Go 最终会选择的版本是 A1.2，B1.2，C1.4 和 D1.2。其中淡蓝色的表示 go.mod 文件加载的，框选的表示最终选择的版本

![image-20241217010104245](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/Go%20%E8%AF%AD%E6%B3%95%E8%BF%9B%E9%98%B6/image-20241217010104245.png)



------

## go.mod



每创建一个 Go Mod 项目都会生成一个 go.mod 文件，因此熟悉 go.mod 文件是非常有必要的，不过大部分情况并不需要手动的修改 go.mod 文件

在文件中可以发现绝大多数的依赖地址都带有 github 等字眼，这是因为 Go 并没有一个公共的依赖仓库，大部分开源项目都是在托管在 Gitub 上的，也有部分的是自行搭建仓库，例如 google.golang.org/protobuf，golang.org/x/crypto。通常情况下，这一串网址同时也是 Go 项目的模块名称，这就会出现一个问题，URL 是不分大小写的，但是存储依赖的文件夹是分大小写的，所以 go get github.com/gin-gonic/gin 和 go get github.com/gin-gonic/Gin 两个引用的是同一个依赖但是本地存放的路径不同。发生这种情况时，Go 并不会直接把大写字母当作存放路径，而是会将其转义为! 小写字母，比如 github.com\BurntSushi 最终会转义为 github.com\!burnt!sushi

<br>

* module 

  module 关键字声明了当前项目的模块名，一个 go.mod 文件中只能出现一个 module 关键字,代表着当前模块名为 golearn，例如打开 Gin 依赖的 go.mod 文件可以发现它的 module 名

  ```mod
  module github.com/gin-gonic/gin
  ```

  Gin 的模块名就是下载依赖时使用的地址，这也是通常而言推荐模块名格式，域名 / 用户 / 仓库名

* Deprecation

  在 module 的上一行开头注释 Deprecated 来表示该模块已弃用，例如

  ```
  // Deprecated: use example.com/mod/v2 instead.
  module example.com/mod
  ```

* go

  go 关键字表示了当前编写当前项目所用到的 Go 版本，版本号必须遵循语义化规则，根据 go 版本的不同，Go Mod 会表现出不同的行为

  ```
  go 1.20
  ```

* require

  require 关键字表示引用了一个外部依赖，例如

  ```
  require github.com/gin-gonic/gin v1.9.0
  ```

  格式是 require 模块名 版本号，有多个引用时可以使用括号括起来

  ```
  require (
     github.com/bytedance/sonic v1.8.0 // indirect
  )
  ```

  带有 // indirect 注释的表示该依赖没有被当前项目直接引用，可能是项目直接引用的依赖引用了该依赖，所以对于当前项目而言就是间接引用

* 伪版本

  在上面的 go.mod 文件中，可以发现有一些依赖包的版本并不是语义化的版本号，而是一串不知所云的字符串，这其实是对应版本的 CommitID，语义化版本通常指的是某一个 Release。伪版本号则可以细化到指定某一个 Commit，通常格式为 vx.y.z-yyyyMMddHHmmss-CommitId，由于其 vx.y.z 并不一定真实存在，所以称为伪版本，例如下面例子中的 v0.0.0 并不存在，真正有效的是其后的 12 位 CommitID

  ```
  // CommitID一般取前12位
  github.com/chenzhuoyu/base64x v0.0.0-20221115062448-fe3a3abad311 // indirect
  ```

  同理，在下载依赖时也可以指定 CommitID 替换语义化版本号

  ```
  go get github.com/chenzhuoyu/base64x@fe3a3abad311
  ```

* exclude

  exclude 关键字表示了不加载指定版本的依赖，如果同时有 require 引用了相同版本的依赖，也会被忽略掉。该关键字仅在主模块中才生效。例如

  ```
  exclude golang.org/x/net v1.2.3
  
  exclude (
      golang.org/x/crypto v1.4.5
      golang.org/x/text v1.6.7
  )
  ```

* replace

  replace 将会替换掉指定版本的依赖，可以使用模块路径和版本替换又或者是其他平台指定的文件路径，例子

  ```
  replace golang.org/x/net v1.2.3 => example.com/fork/net v1.4.5
  
  replace (
      golang.org/x/net v1.2.3 => example.com/fork/net v1.4.5
      golang.org/x/net => example.com/fork/net v1.4.5
      golang.org/x/net v1.2.3 => ./fork/net
      golang.org/x/net => ./fork/net
  )
  ```

  仅 => 左边的版本被替换，其他版本的同一个依赖照样可以正常访问，无论是使用本地路径还是模块路径指定替换，如果替换模块具有 go.mod 文件，则其 module 指令必须与所替换的模块路径匹配

* retract

  retract 指令表示，不应该依赖 retract 所指定依赖的版本或版本范围。例如在一个新的版本发布后发现了一个重大问题，这个时候就可以使用 retract 指令



------

## go.sum



go.sum 文件在创建项目之初并不会存在，只有在真正引用了外部依赖后，才会生成该文件，go.sum 文件并不适合人类阅读，也不建议手动修改该文件。它的作用主要是解决一致性构建问题，即不同的人在不同的环境中使用同一个的项目构建时所引用的依赖包必须是完全相同的，这单单靠一个 go.mod 文件是无法保证的

正常情况下每一个依赖都会有两条记录，第一个是压缩包的哈希值，第二个是依赖包的 go.mod 文件的哈希值，记录格式为模块名 版本号 算法名称: 哈希值，有些比较古老的依赖包可能没有 go.mod 文件，所以就不会有第二条哈希记录。当这个项目在另一个人的环境中构建时，Go 会根据 go.mod 中指定的本地依赖计算哈希值，再与 go.sum 中记录的哈希值进行比对，如果哈希值不一致，则说明依赖版本不同，就会拒绝构建。发生这种情况时，本地依赖和 go.sum 文件都有可能被修改过，但是由于 go.sum 是经过 GOSUMDB 查询记录的，所以会倾向于更相信 go.sum 文件



------

## 私有模块



Go Mod 大多数工具都是针对开源项目而言的，不过 Go 也对私有模块进行了支持。对于私有项目而言，通常情况下需要配置以下几个环境配置来进行模块私有处理

* GOPROXY ：依赖的代理服务器集合
* GOPRIVATE ：私有模块的模块路径前缀的通用模式列表，如果模块名符合规则表示该模块为私有模块，具体行为与 GONOPROXY 和 GONOSUMDB 一致
* GONOPROXY ：不从代理中下载的模块路径前缀的通用模式列表，如果符合规则在下载模块时不会走 GOPROXY，尝试直接从版本控制系统中下载
* GONOSUMDB ：不进行 GOSUMDB 公共校验的模块路径前缀的通用模式列表，如果符合在下载模块校验时不会走 checksum 的公共数据库
* GOINSECURE ：可以通过 HTTP 和其他不安全协议检索的模块路径前缀的通用模式列表



------

# 测试

## 编写规范



在开始编写测试之前，首先需要注意几点规范，这样在后续的学习中会更加方便

* 测试包：测试文件最好单独放在一个包中，这个包通常命名为 test
* 测试文件：测试文件通常以 _test.go 结尾，例如要测试某一个功能，就将其命名为 function_test.go，如果想根据测试类型再划分的更细一些也可以将测试类型为作为文件前缀，例如 benchmark_marshaling_test.go，或者 example_marshaling_test.go
* 测试函数：每一个测试文件中都会有若干个测试函数用于不同的测试。对于不同的测试类型，测试函数的命名的风格也不同。例如示例测试是 ExampleXXXX，单元测试是 TestXXXX，基准测试是 BenchmarkXXXX，模糊测试是 FuzzXXXX，这样一来即便不需要注释也可以知晓这是什么类型的测试
* 当包名为 testdata 时，该包通常是为了存储用于测试的辅助数据，在执行测试时，Go 会忽略名为 testdata 的包

遵循上述的规范，养成良好的测试风格，可以为日后的维护省去不少的麻烦



------

## 执行测试



执行测试主要会用到 go test 命令，下面拿实际的代码举例，现在有待测试文件 /say/hello.go 代码如下

```go
package say

import "fmt"

func Hello() {
  fmt.Println("hello")
}

func GoodBye() {
  fmt.Println("bye")
}
```

和测试文件 /test/example_test.go 代码如下

```go
package test

import (
  "golearn/say"
)

func ExampleHello() {
  say.Hello()
  // Output:
  // hello
}

func ExampleGoodBye() {
  say.GoodBye()
  // Output:
  // bye
}

func ExampleSay() {
  say.Hello()
  say.GoodBye()
  // Output:
  // hello
  // bye
}
```

执行这些测试有多种方法，比如想要执行 test 包下所有的测试用例，就可以直接在 test 目录下执行如下命令

```sh
go test ./
PASS
ok      golearn/test    0.422s
```

./ 表示当前目录，Go 会将 test 目录下的所有测试文件重新编译后，然后再将所有测试用例全都执行，从结果可以看出所有的测试用例都通过了。其后的参数也可以跟多个目录，例如下方的命令，显然项目的主目录并没有测试文件可供执行

```sh
go test ./ ../
ok      golearn/test
?       golearn [no test files]
```

当然也可以单独指定某一个测试文件来执行

```sh
$ go test example_test.go
ok      command-line-arguments  0.457s
```

或者可以单独指定某一个测试文件的某一个测试用例

```sh
go test -run ExampleSay
PASS
ok      golearn/test    0.038s
```

可以加上参数 -v，来使输出结果更加详细一点

```sh
go test ./ -v
=== RUN   ExampleHello
--- PASS: ExampleHello (0.00s)
=== RUN   ExampleGoodBye
--- PASS: ExampleGoodBye (0.00s)
=== RUN   ExampleSay
--- PASS: ExampleSay (0.00s)
PASS
ok      golearn/test    0.040s
```

<br>

下面是执行 Go 测试的常用参数

| 参数                          | 释义                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| `-o file`                     | 指定编译后的二进制文件名称                                   |
| `-c`                          | 只编译测试文件，但不运行                                     |
| `-json`                       | 以 json 格式输出测试日志                                     |
| `-exec xprog`                 | 使用`xprog`运行测试，等价于`go run`                          |
| `-bench regexp`               | 选中`regexp`匹配的基准测试                                   |
| `-fuzz regexp`                | 选中`regexp`匹配的模糊测试                                   |
| `-fuzztime t`                 | 模糊测试自动结束的时间，`t`为时间间隔，当单位为`x`时，表示次数，例如`200x` |
| `-fuzzminimizetime t`         | 模式测试运行的最小时间，规则同上                             |
| `-count n`                    | 运行测试 n 次，默认 1 次                                     |
| `-cover`                      | 开启测试覆盖率分析                                           |
| `-covermode set,count,atomic` | 设置覆盖率分析的模式                                         |
| `-cpu`                        | 为测试执行`GOMAXPROCS`                                       |
| `-failfast`                   | 第一次测试失败后，不会开始新的测试                           |
| `-list regexp`                | 列出`regexp`匹配的测试用例                                   |
| `-parallel n`                 | 允许调用了`t.Parallel`的测试用例并行运行，`n`值为并行的最大数量 |
| `-run regexp`                 | 只运行`regexp`匹配的测试用例                                 |
| `-skip regexp`                | 跳过`regexp`匹配的测试用例                                   |
| `-timeout d`                  | 如果单次测试执行时间超过了时间间隔`d`，就会`panic`。`d`为时间间隔，例 1s,1ms,1ns 等 |
| `-shuffle off,on,N`           | 打乱测试的执行顺序，`N`为随机种子，默认种子为系统时间        |
| `-v`                          | 输出更详细的测试日志                                         |
| `-benchmem`                   | 统计基准测试的内存分配                                       |
| `-blockprofile block.out`     | 统计测试中协程阻塞情况并写入文件                             |
| `-blockprofilerate n`         | 控制协程阻塞统计频率，通过命令`go doc runtime.SetBlockProfileRate`查看更多细节 |
| `-coverprofile cover.out`     | 统计覆盖率测试的情况并写入文件                               |
| `-cpuprofile cpu.out`         | 统计 cpu 情况并写入文件                                      |
| `-memprofile mem.out`         | 统计内存分配情况并写入文件                                   |
| `-memprofilerate n`           | 控制内存分配统计的频率，通过命令`go doc runtime.MemProfileRate`查看更多细节 |
| `-mutexprofile mutex.out`     | 统计锁竞争情况并写入文件                                     |
| `-mutexprofilefraction n`     | 设置统计`n`个协程竞争一个互斥锁的情况                        |
| `-trace trace.out`            | 将执行追踪情况写入文件                                       |
| `-outputdir directory`        | 指定上述的统计文件的输出目录，默认为`go test`的运行目录      |



------

## 示例测试



示例测试并不像其他三种测试一样是为了发现程序的问题所在，它更多的是为了展示某一个功能的使用方法，起到文档作用。示例测试并不是一个官方定义的概念，也不是一个硬性的规范，更像是一种工程上的约定俗成，是否遵守只取决于开发者。示例测试在标准库中出现的非常多，通常是官方所编写的标准库代码示例，例如标准库 context/example_test.go 中的 ExampleWithDeadline 测试函数，该函数中展现了 DeadlineContext 的基本使用方法：

```go
// This example passes a context with an arbitrary deadline to tell a blocking
// function that it should abandon its work as soon as it gets to it.
func ExampleWithDeadline() {
   d := time.Now().Add(shortDuration)
   ctx, cancel := context.WithDeadline(context.Background(), d)

   // Even though ctx will be expired, it is good practice to call its
   // cancellation function in any case. Failure to do so may keep the
   // context and its parent alive longer than necessary.
   defer cancel()

   select {
   case <-time.After(1 * time.Second):
      fmt.Println("overslept")
   case <-ctx.Done():
      fmt.Println(ctx.Err())
   }

   // Output:
   // context deadline exceeded
}
```

<br>

表面上看该测试函数就是一个普通的函数，不过示例测试主要是由 Output 注释来体现的，待测试函数只有一行输出时，使用 Output 注释来检测输出。首先创建一个 hello.go 的文化，写入如下代码：

```go
package say

import "fmt"

func Hello() {
  fmt.Println("hello")
}

func GoodBye() {
  fmt.Println("bye")
}
```

SayHello 函数就是待测函数，然后创建测试文件 example_test.go，写入如下代码

```go
package test

import (
  "golearn/say"
)

func ExampleHello() {
  say.Hello()
  // Output:
  // hello
}

func ExampleGoodBye() {
  say.GoodBye()
  // Output:
  // bye
}

func ExampleSay() {
  say.Hello()
  say.GoodBye()
  // Output:
  // hello
  // bye
}
```

函数中 Output 注释表明了检测函数输出是否为 hello，接下来执行测试命令看看结果

```sh
$ go test -v
=== RUN   ExampleHello
--- PASS: ExampleHello (0.00s)
=== RUN   ExampleGoodBye
--- PASS: ExampleGoodBye (0.00s)
=== RUN   ExampleSay
--- PASS: ExampleSay (0.00s)
PASS
ok      golearn/test    0.448s
```

<br>

从结果可以看出全部测试都已经通过，关于 Output 有以下几种写法，第一种是只有一行输出，意为检测该函数的输出是不是 hello

```go
// Output:
// hello
```

第二种是多行输出，即按顺序检测输出是否匹配

```go
// Output:
// hello
// bye
```

第三种是无序输出，即不按照顺序多行输出匹配

```go
// Unordered output:
// bye
// hello
```

需要注意的是，对于测试函数而言，仅当最后几行为 Output 注释才会被视为示例测试，否则就只是一个普通的函数，不会被 Go 执行



------

## 单元测试



对于单元测试而言，每一个测试用例的命名风格为 TestXXXX，且函数的入参必须是 t *testing.T，testing.T 是 testing 包提供的用于方便测试的结构体，提供了许多可用的方法，例如 t.Errorf 等同于 t.Logf，用于格式化输出测试失败的日志信息，其他常用的还有 t.Fail 用于将当前用例标记为测试失败，功能类似的还有 t.FailNow 同样会标记为测试失败，但是前者失败后还会继续执行，后者则会直接停止执行，如下方的例子，将预期结果修改为错误的结果：

```go
package tool_test

import (
  "golearn/tool"
  "testing"
)

func TestSum(t *testing.T) {
  a, b := 10, 101
  expected := 110

  actual := tool.SumInt(a, b)
  if actual != expected {
        // Errorf内部使用的是t.Fail()
    t.Errorf("Sum(%d,%d) expected %d,actual is %d", a, b, expected, actual)
  }
  t.Log("test finished")
}

func TestEqual(t *testing.T) {
  a, b := 10, 101
  expected := true

  actual := tool.Equal(a, b)
  if actual != expected {
        // Fatalf内部使用的是t.FailNow()
    t.Fatalf("Sum(%d,%d) expected %t,actual is %t", a, b, expected, actual)
  }
  t.Log("test finished")
}
```



------

## 基准测试



基准测试又称为性能测试，通常用于测试程序的内存占用，CPU 使用情况，执行耗时等等性能指标。对于基准测试而言，测试文件通常以 bench_test.go 结尾，而测试用例的函数必须为 BenchmarkXXXX 格式

下面以一个字符串拼接的例子的性能比较来当作基准测试的例子。首先创建文件 /tool/strConcat.go 文件，众所周知直接使用字符串进行 + 拼接性能是很低的，而使用 strings.Builder 则要好很多，在 /tool/strings.go 文件分别创建两个函数进行两种方式的字符串拼接

```go
package tool

import "strings"


func ConcatStringDirect(longString string) {
   res := ""
   for i := 0; i < 100_000.; i++ {
      res += longString
   }
}

func ConcatStringWithBuilder(longString string) {
   var res strings.Builder
   for i := 0; i < 100_000.; i++ {
      res.WriteString(longString)
   }
}
```

然后创建测试文件 /tool_test/bench_tool_test.go ，代码如下

```go
package tool_test

import (
  "golearn/tool"
  "testing"
)

var longString = "longStringlongStringlongStringlongStringlongStringlongStringlongStringlongString"

func BenchmarkConcatDirect(b *testing.B) {
  for i := 0; i < b.N; i++ {
    tool.ConcatStringDirect(longString)
  }
}

func BenchmarkConcatBuilder(b *testing.B) {
  for i := 0; i < b.N; i++ {
    tool.ConcatStringWithBuilder(longString)
  }
}
```

执行测试命令，命令中开启了详细日志和内存分析，指定了使用的 CPU 核数列表，且每个测试用例执行两轮，输出如下

```sh
go test -v -benchmem -bench . -run bench_tool_test.go -cpu=2,4,8 -count=2
goos: windows
goarch: amd64
pkg: golearn/tool_test
cpu: 11th Gen Intel(R) Core(TM) i7-11800H @ 2.30GHz
BenchmarkConcatDirect
BenchmarkConcatDirect-2                4         277771375 ns/op        4040056736 B/op    10000 allocs/op
BenchmarkConcatDirect-2                4         278500125 ns/op        4040056592 B/op     9999 allocs/op
BenchmarkConcatDirect-4                1        1153796000 ns/op        4040068784 B/op    10126 allocs/op
BenchmarkConcatDirect-4                1        1211017600 ns/op        4040073104 B/op    10171 allocs/op
BenchmarkConcatDirect-8                2         665460800 ns/op        4040077760 B/op    10219 allocs/op
BenchmarkConcatDirect-8                2         679774450 ns/op        4040080064 B/op    10243 allocs/op
BenchmarkConcatBuilder
BenchmarkConcatBuilder-2            3428            344530 ns/op         4128176 B/op         29 allocs/op
BenchmarkConcatBuilder-2            3579            351858 ns/op         4128176 B/op         29 allocs/op
BenchmarkConcatBuilder-4            2448            736177 ns/op         4128185 B/op         29 allocs/op
BenchmarkConcatBuilder-4            1688            662993 ns/op         4128185 B/op         29 allocs/op
BenchmarkConcatBuilder-8            1958            550333 ns/op         4128199 B/op         29 allocs/op
BenchmarkConcatBuilder-8            2174            552113 ns/op         4128196 B/op         29 allocs/op
PASS
ok      golearn/tool_test       21.381s
```

goos 代表是运行的操作系统，goarh 代表的是 CPU 架构，pkg 为测试所在的包，cpu 是一些关于 CPU 的信息。下面的每一个测试用例的结果由每一个基准测试的名称分隔，第一列 BenchmarkConcatDirect-2 中的 2 代表了使用的 CPU 核数，第二列的 4 代表了代码中 b.N 的大小，也就是基准测试中的循环次数，第三列 277771375 ns/op 代表了每一次循环所消耗的时间，ns 为纳秒，第四列 4040056736 B/op 表示每一次循环所分配内存的字节大小，第五列 10000 allocs/op 表示每一次循环内存分配的次数



------

## 模糊测试



模糊测试是 GO1.18 推出的一个新功能，属于是单元测试和基准测试的一种增强，区别在于前两者的测试数据都需要开发者手动编写，而模糊测试可以通过语料库来生成随机的测试数据，关于 Go 中的模糊测试可以前往[Go Fuzzing](https://go.dev/security/fuzz/)来了解更多概念。模糊测试的好处在于，相比于固定的测试数据，随机数据可以更好的测试程序的边界条件
