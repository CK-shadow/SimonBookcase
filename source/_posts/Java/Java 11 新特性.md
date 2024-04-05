---
title: Java 11 新特性
date: 2023-09-01 19:34:33
tags: Java
categories: 
  - Java
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%2011%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%2011%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
---



# Java 11 新特性

## 简介



北京时间 2018 年 9 月 26 日，Oracle 官方宣布 Java 11 正式发布。这是 Java 大版本周期变化后的第一个长期支持版本，非常值得关注。从官网下载即可，最新的 Java 11 将带来 ZGC、Http Client 等重要特性，一共包含 17 个 JEP。其实，更新不止 17 个，只是我们更关注以下 17 个更新

![image-20230827111027665](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%2011%20%E6%96%B0%E7%89%B9%E6%80%A7/image-20230827111027665.png)



JDK 11 将是一个企业不可忽视的版本。从时间节点上看，JDK 11 的发布正好处在 JDK 8 免费更新到期的前夕，同时 JDK 9 和 JDK 10 也陆续成为“历史版本”

对于企业来说，选择 JDK 11 将意味着长期的、可靠的、可预测的技术路线图。其中免费的 OpenJDK 11 确定将得到 OpenJDK 社区的长期支持，LTS 版本将是可以放心选择的版本

从 JVM GC 的角度，JDK 11 引入了两种 GC，其中包括也许是划时代意义的 ZGC，虽然其目前还是实验特性，但从能力上看，这是 JDK 的一个巨大突破，为特定生产环境的苛刻需求提供了一个可能的选择。例如，对部分企业核心存储的产品，如果能保证不超过 10 ms 的 GC 暂停，可靠性会上一个大台阶，这是我们过去的 GC 调优几乎做不到的，是能与不能的问题



按照官方的说法，新的发布周期会严格遵循时间点，将于每年的 3 月份和 9 月份发布。所以 Java 11 的版本号是 Java 18.9（LTS）

不过与 Java 9 和 Java 10 这两个被称为“功能性的版本”不同，Java 11 不仅提供了长期支持服务，还将作为 Java 平台的参考实现。Oracle 知道 2023 年 9 月都会为 Java 11 提供技术支持，而补丁和安全警告等扩展支持将持续到 2026 年



------

## 新增的字符串方法



|         描述         |                  举例                  |
| :------------------: | :------------------------------------: |
| 判断字符串是否为空白 |         "".isBlank();  // true         |
|     去除首尾空格     |     " Java11 ".strip(); // "Java"      |
|     去除尾部空格     | " Java11 ".stripTrailing(); // " Java" |
|     去除首部空格     | " Java11 ".stripLeading(); // "Java "  |
|      复制字符串      |  "Java".repeat(3); // "JavaJavaJava"   |
|       行数统计       |    "A\nB\nC".lines().count(); // 3     |



------

## Optional 新增方法



|     新增方法      |        描述         |
| :---------------: | :-----------------: |
| boolean isEmpty() | 判断 value 是否为空 |



------

## 局部变量类型推断升级



在 var 上添加注解的语法格式，在 JDK 10 中是不能实现的，而在 JDK 11 中则加入了这样的语法

```java
// 错误的语法格式，必须要加上类型
Consumer<String> consumer1 = (@Deprecated t) -> System.out.println(t.toUpperCase());

// 正确的格式，使用 var 的好处是在 lambda 表达式时给参数加上注解
Consumer<String> consumer2 = (@Deprecated var t) -> System.out.println(t.toUpperCase());
```



------

## 全新的 HTTP 客户端 API



HTTP，用于传输网页的协议，早在 1997 年就被采用在目前的 1.1 版本中。直到 2015 年，HTTP2 才成为标准

HTTP/1.1 和 HTTP/2 的主要区别是如何在客户端和服务器之间构建和传输数据。HTTP/1.1 依赖于请求/响应周期，HTTP/2 允许服务器 Push 数据，可以发送比客户端请求更多的数据，这使得它可以优先处理并发送对于首先加载网页至关重要的数据

Java 9 开始引入一个处理 HTTP 请求的 HTTP Client API ，该 API 支持同步和异步，而在 Java 11 已经正式成为可用状态，可以在 java.net 的包中找到这个 API。它将替代仅适用于 blocking 模式 HttpURLConnection（HttpURLConnection 是在 HTTP 1.0 的时代创建的，并使用了协议无关的方法），并提供对 WebSocket 和 HTTP/2 的支持



```java
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder(URI.create("localhost:8080/test")).build();
BodyHandler<String> responseBodyHandler = BodyHandlers.ofString();
HttpResponse<String> response = client.send(request, responseBodyHandler);
String body = response.body();
System.out.println(body);
```



------

## 更简化的编译运行程序



在我们的认知里面，要运行一个 java 源代码必须先编译，再运行，两步执行动作

```
// 编译
javac test.java
// 运行
java test
```

而在 Java 11 中，通过一个命令就可以了

```
java test.java
```

需要注意的是，使用一个命令编译运行源文件，源文件中的第一个类必须包含主方法，并且不可以使用其它源文件的自定义类，可以使用本文件的自定义类



------

## ZGC



GC 是 Java 主要优势之一，然而，当 GC 停顿时间太长，就会开始影响应用的响应时间。消除或者减少 GC 停顿时长，Java 将对更广泛的应用场景是一个更有吸引力的平台。此外，现代操作系统中可用内存的不断增长，用户和程序员希望 JVM 能够以高效的方式充分利用这些内存，并且无需长时间的 GC 暂停时间

ZGC，这应该是 JDK 11 最为瞩目的特性，没有之一，但是后面带了 Experimental，说明还不建议用到生产环境

ZGC 是一个并发，基于 region，压缩型的垃圾收集器，只有 root 扫描阶段会 STW，因此 GC 停顿时间不会随着堆的增长和存活对象的增长而变长



优势：

* GC 暂停时间不会超过 10 ms
* 既能处理几百兆的小堆，也能处理几个 T 的大堆
* 和 G1 相比，对应用吞吐能力的影响不会超过 15%
* 为未来的 GC 功能和利用colord 指针以及 Load Barriers 优化奠定基础
* 初始只支持 64 位系统



ZGC 的设计目标是：支持 TB 级内存容量，暂停时间低于 10 ms，对整个程序吞吐量的影响不超过 15%。将来还可以拓展实现机制，以支持不少令人兴奋的功能，例如多层堆或压缩堆
