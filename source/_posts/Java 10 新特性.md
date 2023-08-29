---
title: Java 10 新特性
date: 2023-08-29 20:51:01
tags: JAVA
categories: 
  - JAVA
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%2010%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%2010%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
---



# Java 10 新特性

## 简介



2018 年 3 月 21 日，Oracle 官方宣布 Java 10 正式发布

需要注意的是 Java 9 和 Java 10 都不是 LTS 版本，和过去的 Java 大版本升级不同，这两个只有半年左右的开发期和维护期。而未来的 Java 11，也就是 18.9 LTS，才是 Java 8 之后第一个 LTS 版本

JDK 10 一共定义了 109 个新特性，其中包含 12 个 JEP（对于程序员来讲，真正的新特性其实就 1 个），还有一些新的 API 和 JVM 规范以及 Java 语言规范上的改动

JDK 10 的 12 个 JEP，可参阅官方文档：https://openjdk.org/projects/jdk/10/



------

## 局部变量类型推断



开发者经常抱怨 Java 中引用代码的程度。局部变量的显示类型声明，常常被认为是非必须的，给一个好听的名字经常可以很清楚的表达应该怎样继续



作为 Java 开发者，在声明一个变量时，我们总是习惯了敲打两次变量类型，第一次用于声明变量类型，第二次用于构造器

```java
LinkedHashSet<Integer> set = new LinkedHashSet<>();
```

变量的声明类型书写复杂且长，尤其是加上泛型的使用

```java
Iterator<Map.Entry<Integer, String>> iterator = set.iterator();
```

有时会声明一些变量，但是只会使用一次

```java
URL url = new URL("www.baidu.com");
URLConnection connection = url.openConnection():
```

尽管 IDE 会自动帮我们补全这些代码，但变量总是跳来跳去，可读性还是会收到影响，因为变量类型由各种长度不同的字符组成。而且，有时开发人员会尽量避免声明中间变量，因为太多的类型声明只会分散注意力，不会带来额外的好处



局部变量类型推断适用于以下情况：

```java
@Test
public void test() {
	// 1. 局部变量的初始化
	var list = new ArrayList<>();
	
	// 2. 增强 for 循环中的索引
	for(var v : list) {
		System.out.println(v);
	}
	
	// 3. 传统 for 循环中
	for(var i = 0; i < 10; i++) {
		System.out.println(i);
	}
}
```



------

## 新增创建不可变集合的方法



自 Java 9 开始，JDK 为集合（List、Set、Map）都添加了 of（Java 9 新增）和 copyOf（Java 10 新增）方法，它们两个都用来创建不可变集合，来看一下他们的区别：

```java
// 示例 1
var list1 = List.of("A", "B", "C");
var copy1 = List.copyOf(list1);
System.out.println(list1 == copy1); // true

// 示例 2
var list2 = new ArrayList<String>();
var copy2 = List.copyOf(list2);
System.out.println(list2 == copy2); // false
```

如果 copyOf 的参数值是一个不可变集合，那么 copyOf 的返回值即为当前的集合。否则的话就返回一个新的集合，但这个集合是只读的
