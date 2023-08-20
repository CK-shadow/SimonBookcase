---
title: Java 9 新特性
date: 2023-08-20 23:12:46
tags: JAVA
categories: 
  - JAVA
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%209%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%209%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
---



# Java 9 新特性

## 简介



经过 4 次跳票，历经曲折的 Java 9 终于在 2017 年 9 月 21 日发布

从  Java 9 开始，Java 的计划发布周期是 6 个月，下一个 Java 的主版本将于 2018 年 3 月发布，命名为 Java 18.3，紧接着再过 6 个月将发布 Java 18.9。这意味着 Java 的更新从传统的以特性驱动的发布周期，转变为以时间驱动的（6 个月为周期）的发布模式，并逐步的将 Oracle JDK 原商业特性进行开源

针对企业客户的需求，Oracle 将以 3 年为周期发布长期支持版本（Long Term Support，LTS）

Java 9 提供了超过 150 项新功能特性，包括备受期待的模块化系统、可交互的 REPL 工具 Jshell，JDK 编译工具，Java 公共 API 和私有代码，以及安全增强、扩展提升、性能管理改善等。可以说 Java 9 是一个庞大的系统工程，完全做了一个整体改变



------

## 模块化系统



谈到 Java 9 大家第一个想到的往往就是 Jigsaw 项目。众所周知，Java 已经发展超过二十年，Java 和相关生态在不断丰富的同时也越来越暴露出一些问题：

* Java 运行环境的膨胀和臃肿。每次 JVM 启动的时候，至少会有 30~60 MB 内存加载，主要原因是 JVM 需要加载 rt.jar，不管其中的类是否被 classloader 加载，第一步整个 jar 都会被加载到内存中（而模块化可以根据模块的需要加载程运行需要的 class）
* 当代码库越来越大，创建复杂，盘根错节的“意大利面条代码”的几率呈指数级增长。不同版本的类库交叉依赖导致让人头疼的问题，这些都阻碍了 Java 开发和运行的效率
* 很难真正的对代码进行封装，而系统并没有真正对不同部分（也就是 jar 文件）之间的依赖关系有个明确的概念。每一个公共类都可以被类路径之下任何其它的公共类访问到，这样就导致无意中使用了并不想公开的 API



本质上讲，用模块来管理各个 package，通过声明某个 package 暴露。模块（module）的概念，其实就是在 package 外面再包一层，不声明默认就是隐藏。因此，模块化使代码组织上更安全，因为它可以指定哪些部分需要隐藏，哪些部分可以暴露



实现目标：

* 模块化的主要目的在于减少内存的开销
* 只需必要模块，而非全部 jdk 模块，可简化各种类库和大型应用的开发和维护
* 改进 Java SE 平台，使其可以使用不同大小的计算设备
* 改进其安全性，可维护性，提高性能





模块将由通常的类和新的模块声明文件（module-info.java）组成。该文件是位于 Java 代码结构的顶层，该模块描述符明确定义了我们的模块需要什么依赖关系，以及哪些模块被外部使用。在 exports 子句中未提及的所有的包默认情况下将封装在模块中，不能在外部使用



正常情况下，如果我们的项目中有多个 module，那么 module A 是不能引用 module B 中的 class 的

如果 module A 需要引用 module B 中的 class，那么可以先在 module B 中创建 module-info.java，并将需要暴露的 class 声明出去

![image-20230817232323106](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%209%20%E6%96%B0%E7%89%B9%E6%80%A7/image-20230817232323106.png)

并添加如下内容

```java
module 模块名 {
    
    exports "需要导出的 package 名"
    
}
```

再在 module A 中同样创建一个 module-info.java 文件，添加如下内容

```java
module 模块名 {
    
    requires "需要引入的模块名"
    
}
```

这样就能引用 module B 中暴露出来的 package 了



------

## jshell 命令





像 Python 和 Scala 之类的语言早就有了交互式编程环境 REPL（read - evaluate - print - loop）了，以交互式的方式对语句和表达式进行求值。开发者只需要输入一些代码，就可以在编译前获得对程序的反馈。而之前的 Java 版本想要执行代码，必须创建文件、声明类、提供测试方法方可实现



Java 9 中终于有了 REPL 工具：jshell。让 Java 可以像脚本语言一样运行，从控制台启动 jshell，利用 jshell 在没有创建类的情况下直接声明变量，计算表达式和执行语句。即开发时直接在命令行中运行 Java 代码，而无需创建 Java 文件，无需跟人解释 `public static void main()` 这句废话

jshell 也可以从文件中加载语句或者将语句保存到文件中

jshell 也可以是 tab 键进行自动补全和自动添加分号



------

## 接口的私有方法





Java 8 规定接口中的方法除了抽象方法之外，还可以定义静态方法和默认的方法。一定程度上，拓展了接口的功能，此时的接口更像是一个抽象类

在 Java 9 中，接口更加的灵活与强大，连方法的访问修饰符都可以声明为 private 了，此时的方法不会成为对外暴露的 api 的一部分



------

## 钻石操作符



在 Java 8 中，如下操作是会报错的：

```java
Comparator<Object> comparator = new Comparator<>() {
	@Override
	public int compare(Object o1, Object o2) {
		return 0;
	}
}
```

编译时会报错：Cannot user "<>" with anonymous inner classes

而在 Java 9 中，将能够与匿名内部类共同使用钻石操作符



------

##  try-with-resource 改进



Java 8 中，可以使用 try-with-resource 实现资源的自动关闭，但是要求执行后必须关闭的所有资源必须在 try 子句中初始化，否则编译不通过。如下例所示：

```java
try (InputStreamReader reader = new InputStreamReader(System.in)) {
	// 文件流操作
} catch (IOException e) {
	e.printStackTrace();
}
```



而在 Java 9 中，我们可以在 try 子句中使用已经初始化的实例。需要注意的是，该实例会自动转为常量，声明为 final，不可修改

```java
InputStreamReader reader = new InputStreamReader(System.in);
try (reader) {
	// 文件流操作
} catch (IOException e) {
	e.printStackTrace();
}
```



------

## String 存储结构的变更



到了 Java 9，String 中字符串的存储不再用 char 数组了，改用 byte 数组

不仅将 char 数组改为 byte 数组，而且新增了一个 coder 的成员变量

在程序中，绝大多数字符串只包含英文字母数字等字符，使用 Latin-1 编码，一个字符占用一个 byte。如果使用 char，一个 char 要占用两个 byte，会占用双倍的内存空间

但是，如果字符串中使用了中文等超出 Latin-1 表示范围的字符，使用 Latin-1 就没办法表示了。这时 JDK 会使用 UTF-16 编码，那么占用的空间和旧版（使用 char[]）是一样的

coder 变量代表编码的格式，目前 String 支持两种编码格式 Latin-1 和 UTF-16。Latin-1 需要用一个字节来存储，而 UTF-16 需要使用 2 个字节或者 4 个字节来存储



------

## 快速创建只读集合



在 Java 8 及以前，如果我们要创建一个只读、不可改变的集合，必须构造和分配它，然后添加元素，最后包装成一个不可修改的集合

```java
List<String> nameList = new ArrayList<>();
nameList.add("A");
nameList.add("B");
nameList.add("C");
nameList = Collections.unmodifiableList(nameList);
```



而在 Java 9 中，我们可以使用集合工厂方法来创建只读集合，以上代码可以这么写

```java
List<String> nameList = List.of("A", "B", "C");
```

如果是 Set 的话

```java
Set<String> nameSet = Set.of("A", "B", "C");
```

还可以用来操作 Map

```java
Map<Integer, String> map = Map.of(1, "A", 2, "B", 3, "C");
```



------

## InputStream 的 transferTo 方法



InputStream 终于有了一份非常有用的方法：transferTo，可以用来将数据直接传送到 OutputStream，这是在处理原始数据流时一种非常有用的方法，如下：

```java
ClassLoader loader = this.getClass().getClassLoader();
try (InputStream input = loader.getResourceAsStream("hello.txt");
	 OutputStream output = new FileOutputStream("src\\hello1.txt")) {
	 // 把输入流中的所有数据自动复制到输出流中
	input.transferTo(output);
} catch (IOException e) {
	e.printStackTrace();
}
```



------

## 增强的 Stream API



Java 的 Stream API 是 Java 标准库最好的改进之一，让开发者能够快速运算，从而能够有效的利用数据并行计算。Java 8 提供的 Stream 能够利用多核架构实现声明式的数据处理

在 Java 9 中，Stream API 变得更好，Stream 接口中添加了 4 个新的方法：takeWhile、dropWhile、ofNullable 和 iterate 方法新的重载方法，可以提供一个 Predicate 来指定什么时候结束迭代

除了 Stream 本身的拓展，Optional 和 Stream 之间的结合也得到了改进。现在可以通过 Optional 的新方法 stream() 将一个 Optional 对象转换为一个（可能是空的）Stream 对象



* takeWhile 的使用

  用于从 Stream 中获取一部分元素，接收一个 Predicate 来进行选择。在有序的 Stream 中，takeWhile 返回从头开始的尽量多的元素

  ```java
  List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6);
  list.stream().takeWhile(x -> x > 3).forEach(System.out::println);
  ```

* dropWhile 的使用

  dropWhile 与 takeWhile 相反，返回剩余的元素

  ```java
  List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6);
  list.stream().dropWhile(x -> x > 3).forEach(System.out::println);
  ```

* ofNullable 的使用

  Java 8 中 Stream 不能为单个 null，否则会报空指针异常。而 Java 9 中的 ofNullable 方法允许我们创建一个单元素 Stream，可以包含一个非空元素，也可以创建一个空的 Stream

  ```java
  Stream<String> stream2 = Stream.ofNullable(null);
  ```

* iterate 重载方法的使用

  ```java
  // 原来的控制终止方式
  Stream.iterate(1, i -> i + 1).limit(10).forEach(System.out::println);
  // Java 9 的控制终止方式
  Stream.iterate(1, i -> i < 10, i -> i + 1).forEach(System.out::println);
  ```

  

------

## Optional 获取 Stream 的方法



```java
List<String> list = Arrays.asList("A", "B", "C");
Optional<List<String>> optional = Optional.ofNullable(list);
Stream<List<String>> stream = optional.stream(); 
```

