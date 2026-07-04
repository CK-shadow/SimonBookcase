---
title: JDK 9 ~ JDK 11 新特性
date: 2023-01-28 06:56:25
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%209%20~%20JDK%2011%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%209%20~%20JDK%2011%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
tags:
  - Java
  - JDK
categories: Java
---



# JDK 9

## 简介



经过 4 次跳票，历经曲折的 Java 9 终于在 2017 年 9 月 21 日发布

从  Java 9 开始，Java 的计划发布周期是 6 个月，下一个 Java 的主版本将于 2018 年 3 月发布，命名为 Java 18.3，紧接着再过 6 个月将发布 Java 18.9。这意味着 Java 的更新从传统的以特性驱动的发布周期，转变为以时间驱动的（6 个月为周期）的发布模式，并逐步的将 Oracle JDK 原商业特性进行开源

针对企业客户的需求，Oracle 将以 3 年为周期发布长期支持版本（Long Term Support，LTS）

Java 9 提供了超过 150 项新功能特性，包括备受期待的模块化系统、可交互的 REPL 工具 Jshell，JDK 编译工具，Java 公共 API 和私有代码，以及安全增强、扩展提升、性能管理改善等。可以说 Java 9 是一个庞大的系统工程，完全做了一个整体改变



---

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

![image-20230817232323106](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%209%20~%20JDK%2011%20%E6%96%B0%E7%89%B9%E6%80%A7/image-20230817232323106.png)

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



---

## JShell 交互式编程工具



像 Python 和 Scala 之类的语言早就有了交互式编程环境 REPL（read - evaluate - print - loop）了，以交互式的方式对语句和表达式进行求值。开发者只需要输入一些代码，就可以在编译前获得对程序的反馈。而之前的 Java 版本想要执行代码，必须创建文件、声明类、提供测试方法方可实现



Java 9 中终于有了 REPL 工具：jshell。让 Java 可以像脚本语言一样运行，从控制台启动 jshell，利用 jshell 在没有创建类的情况下直接声明变量，计算表达式和执行语句。即开发时直接在命令行中运行 Java 代码，而无需创建 Java 文件，无需跟人解释 `public static void main()` 这句废话

```sh
jshell> 1 + 2
$1 ==> 3

jshell> System.out.println("Hello JShell!")
Hello JShell!

jshell> Math.sqrt(16)
$3 ==> 4.0
```



默认情况下，JShell 会自动导入以下包：

- `java.io.*`
- `java.math.*`
- `java.net.*`
- `java.nio.file.*`
- `java.util.*`
- `java.util.concurrent.*`
- `java.util.function.*`
- `java.util.prefs.*`
- `java.util.regex.*`
- `java.util.stream.*`

如果需要导入其他包，可以使用`import`语句：

```SH
jshell> import java.time.LocalDate

jshell> LocalDate.now()
$14 ==> 2023-10-01
```



jshell 也可以从文件中加载语句或者将语句保存到文件中

jshell 也可以是 tab 键进行自动补全和自动添加分号



---

## 接口私有方法



JDK 8 引入了接口的默认方法和静态方法，解决了接口的扩展问题，但也带来了代码复用的问题。JDK 9 引入的接口私有方法，完美解决了这个问题

在 JDK 9 中，接口更加的灵活与强大，连方法的访问修饰符都可以声明为 private 了，此时的方法不会成为对外暴露的 api 的一部分

```Java
public interface Calculator {
    default int add(int a, int b) {
        validate(a, b);
        return a + b;
    }

    default int subtract(int a, int b) {
        validate(a, b);
        return a - b;
    }

    default int multiply(int a, int b) {
        validate(a, b);
        return a * b;
    }

    // 私有方法：提取重复的参数校验代码
    private void validate(int a, int b) {
        if (a < 0 || b < 0) {
            throw new IllegalArgumentException("参数不能为负数");
        }
    }
}
```



---

## 集合工厂方法



JDK 9 引入了一组新的集合工厂方法，用于快速创建不可变集合。这些方法提供了一种简洁、安全的方式来创建小型集合

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



---

## Stream API 增强



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

## Optional 类增强



JDK 8 引入的 Optional 类从根本上解决了空指针异常的问题，JDK 9 对 Optional 类进行了增强，新增了三个非常实用的方法

JDK 9 为 Optional 类新增了以下三个方法：

1. `ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)`：如果 Optional 包含非空值，则执行 Consumer 操作；否则执行 Runnable 操作
2. `or(Supplier<? extends Optional<? extends T>> supplier)`：如果 Optional 包含非空值，则返回当前 Optional；否则返回由 Supplier 提供的 Optional
3. `stream()`：将 Optional 转换为一个包含单个元素的流，如果 Optional 为空则返回空流

这些方法进一步完善了 Optional 类的功能，使得空值处理更加灵活



**ifPresentOrElse () 方法**

`ifPresentOrElse()`方法接收两个参数：一个 Consumer 和一个 Runnable。如果 Optional 包含非空值，则执行 Consumer 操作；否则执行 Runnable 操作

```Java
public class IfPresentOrElseDemo {
    public static void main(String[] args) {
        // 包含非空值的Optional
        Optional<String> optional1 = Optional.of("Hello");
        optional1.ifPresentOrElse(
            value -> System.out.println("值存在：" + value),
            () -> System.out.println("值不存在")
        );
        // 输出：值存在：Hello

        // 空的Optional
        Optional<String> optional2 = Optional.empty();
        optional2.ifPresentOrElse(
            value -> System.out.println("值存在：" + value),
            () -> System.out.println("值不存在")
        );
        // 输出：值不存在
    }
}
```



**or () 方法**

`or()`方法接收一个 Supplier 参数，该 Supplier 返回一个 Optional。如果当前 Optional 包含非空值，则返回当前 Optional；否则返回由 Supplier 提供的 Optional

```Java
public class OrDemo {
    public static void main(String[] args) {
        // 包含非空值的Optional
        Optional<String> optional1 = Optional.of("Hello");
        Optional<String> result1 = optional1.or(() -> Optional.of("Default"));
        System.out.println(result1.get()); // 输出Hello

        // 空的Optional
        Optional<String> optional2 = Optional.empty();
        Optional<String> result2 = optional2.or(() -> Optional.of("Default"));
        System.out.println(result2.get()); // 输出Default
    }
}
```

`or()`方法与`orElse()`和`orElseGet()`的区别：

- `orElse()`：返回一个具体的值
- `orElseGet()`：返回一个由 Supplier 提供的具体值
- `or()`：返回一个由 Supplier 提供的 Optional



**stream () 方法**

`stream()`方法将 Optional 转换为一个包含单个元素的流，如果 Optional 为空则返回空流。这使得 Optional 可以与 Stream API 无缝集成

```Java
public class OptionalStreamDemo {
    public static void main(String[] args) {
        // 将Optional转换为流
        Optional<String> optional1 = Optional.of("Hello");
        Stream<String> stream1 = optional1.stream();
        List<String> result1 = stream1.collect(Collectors.toList());
        System.out.println(result1); // 输出[Hello]

        // 空的Optional转换为空流
        Optional<String> optional2 = Optional.empty();
        Stream<String> stream2 = optional2.stream();
        List<String> result2 = stream2.collect(Collectors.toList());
        System.out.println(result2); // 输出[]
    }
}
```



---

## 全新的 HTTP Client API



JDK 9 引入了全新的 HTTP Client API，替代了传统的`HttpURLConnection`，支持 HTTP/2 和异步非阻塞调用，API 设计更加简洁和现代化



传统的`HttpURLConnection`存在以下严重问题：

1. API 设计糟糕：`HttpURLConnection`的 API 设计非常繁琐，使用起来非常麻烦
2. 不支持 HTTP/2：`HttpURLConnection`只支持 HTTP/1.1 协议，不支持 HTTP/2
3. 不支持异步调用：`HttpURLConnection`只支持同步调用，无法实现异步非阻塞
4. 功能有限：不支持 WebSocket，处理复杂的 HTTP 请求非常困难



HTTP Client API 是 JDK 9 新增的用于处理 HTTP 请求和响应的 API，位于`java.net.http`包中。它具有以下特性：

- 支持 HTTP/1.1 和 HTTP/2 协议
- 支持同步和异步调用
- 支持流式处理请求和响应
- 支持 WebSocket
- API 设计简洁、现代化



**传统 HttpURLConnection 的示例**：

```Java
// 传统HttpURLConnection发送GET请求
URL url = new URL("https://api.example.com/users");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");

int responseCode = conn.getResponseCode();
if (responseCode == 200) {
    BufferedReader in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
    String inputLine;
    StringBuffer response = new StringBuffer();
    while ((inputLine = in.readLine())!= null) {
        response.append(inputLine);
    }
    in.close();
    System.out.println(response.toString());
} else {
    System.out.println("请求失败，响应码：" + responseCode);
}
conn.disconnect();
```



**JDK 9 HTTP Client API 的解决方案**：

```
// 新的HTTP Client API发送GET请求
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
                                .uri(URI.create("https://api.example.com/users"))
                                .build();

HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

if (response.statusCode() == 200) {
    System.out.println(response.body());
} else {
    System.out.println("请求失败，响应码：" + response.statusCode());
}
```



---

## 进程 API 增强



JDK 9 新增了`ProcessHandle`接口和`ProcessHandle.Info`接口，用于获取和管理本地进程的信息。它们提供了以下功能：

- 获取当前进程的信息（PID、命令行参数、启动时间等）
- 获取所有本地进程的信息
- 销毁进程
- 监听进程的终止事件

传统的`Process`类只能获取进程的输入流、输出流和退出值，功能非常有限。新的进程 API 填补了这一空白



在 JDK 9 之前，Java 对本地进程的管理能力非常弱。例如，我们无法获取当前进程的 PID，无法获取其他进程的信息，也无法优雅地销毁进程。开发者不得不依赖操作系统的本地命令（如`ps`、`taskkill`等）来实现这些功能，这导致代码不可移植

**JDK 8 的示例**：

```
// JDK 8中获取当前进程的PID（非常繁琐且不可移植）
String processName = java.lang.management.ManagementFactory.getRuntimeMXBean().getName();
String pid = processName.split("@")[0];
System.out.println("当前进程PID：" + pid);
```

上述代码依赖于 JVM 的实现，不同的 JVM 可能有不同的格式，不可移植



**JDK 9 的解决方案**：

```Java
// JDK 9中获取当前进程的PID
long pid = ProcessHandle.current().pid();
System.out.println("当前进程PID：" + pid);
```

新的 API 简洁、标准且可移植



**获取当前进程信息**

```Java
public class CurrentProcessDemo {
    public static void main(String[] args) {
        // 获取当前进程的ProcessHandle
        ProcessHandle current = ProcessHandle.current();

        // 获取进程PID
        long pid = current.pid();
        System.out.println("PID：" + pid);

        // 获取进程信息
        ProcessHandle.Info info = current.info();

        // 进程命令
        System.out.println("命令：" + info.command().orElse("未知"));

        // 命令行参数
        System.out.println("命令行参数：" + info.arguments().map(Arrays::toString).orElse("未知"));

        // 启动时间
        System.out.println("启动时间：" + info.startInstant().orElse(Instant.MIN));

        // 运行时间
        System.out.println("运行时间：" + info.totalCpuDuration().orElse(Duration.ZERO));

        // 进程所有者
        System.out.println("所有者：" + info.user().orElse("未知"));
    }
}
```



**获取所有本地进程**

```Java
public class AllProcessesDemo {
    public static void main(String[] args) {
        // 获取所有本地进程
        Stream<ProcessHandle> processes = ProcessHandle.allProcesses();

        // 打印前10个进程的PID和命令
        processes.limit(10)
                .forEach(process -> {
                     System.out.println("PID：" + process.pid() + "，命令：" + process.info().command().orElse("未知"));
                 });
    }
}
```



**销毁进程**

```Java
// 销毁进程
ProcessHandle process = ProcessHandle.of(pid).orElseThrow();
boolean destroyed = process.destroy(); // 正常销毁
// boolean destroyed = process.destroyForcibly(); // 强制销毁

if (destroyed) {
    System.out.println("进程已销毁");
} else {
    System.out.println("进程销毁失败");
}
```



**监听进程终止事件**

```Java
// 监听进程终止事件
ProcessHandle process = ProcessHandle.of(pid).orElseThrow();
process.onExit().thenRun(() -> {
    System.out.println("进程已终止");
});
```



---

## 其它重要特性



**响应式流**

JDK 9 引入了 Flow API，它是响应式流（Reactive Streams）规范的 Java 实现。响应式流是一种用于处理异步数据流的标准，它提供了非阻塞背压机制，解决了异步数据流处理中的速度不匹配问题。

Flow API 包含四个核心接口：

- `Publisher<T>`：发布者，负责发布数据
- `Subscriber<T>`：订阅者，负责接收和处理数据
- `Subscription`：订阅，用于管理订阅关系和控制数据流
- `Processor<T, R>`：处理器，既是发布者又是订阅者，用于转换数据流

```Java
public class FlowDemo {
    public static void main(String[] args) throws InterruptedException {
        // 创建发布者
        SubmissionPublisher<String> publisher = new SubmissionPublisher<>();

        // 创建订阅者
        Flow.Subscriber<String> subscriber = new Flow.Subscriber<>() {
            private Flow.Subscription subscription;

            @Override
            public void onSubscribe(Flow.Subscription subscription) {
                this.subscription = subscription;
                subscription.request(1); // 请求1个元素
            }

            @Override
            public void onNext(String item) {
                System.out.println("收到：" + item);
                subscription.request(1); // 请求下一个元素
            }

            @Override
            public void onError(Throwable throwable) {
                throwable.printStackTrace();
            }

            @Override
            public void onComplete() {
                System.out.println("数据流结束");
            }
        };

        // 订阅
        publisher.subscribe(subscriber);

        // 发布数据
        publisher.submit("Java");
        publisher.submit("Python");
        publisher.submit("C++");

        // 关闭发布者
        publisher.close();

        // 等待处理完成
        Thread.sleep(1000);
    }
}
```

Flow API 为 Java 的响应式编程奠定了基础，后续的 Spring WebFlux 等框架都是基于 Flow API 构建的



**钻石操作符增强**

JDK 7 引入了钻石操作符（`<>`），用于简化泛型的创建。JDK 9 对钻石操作符进行了增强，允许在匿名内部类中使用钻石操作符

```Java
// 该写法在 JDK 8 中会编译报错，但是 JDK 9 中可以
List<String> list = new ArrayList<>() {
    {
        add("Java");
        add("Python");
    }
};
```



**try-with-resources 增强**

JDK 7 引入了 try-with-resources 语句，用于自动关闭实现了`AutoCloseable`接口的资源。JDK 9 对 try-with-resources 进行了增强，允许在 try 语句中使用已经声明的资源变量

JDK 8 的写法

```Java
// JDK 8中必须在try语句中声明资源变量
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine())!= null) {
        System.out.println(line);
    }
}
```

JDK 9 的写法

```Java
// JDK 9中可以使用已经声明的资源变量
BufferedReader br = new BufferedReader(new FileReader("file.txt"));
try (br) {
    String line;
    while ((line = br.readLine())!= null) {
        System.out.println(line);
    }
}
```



**下划线关键字的限制**

在 JDK 8 及以下版本中，下划线（`_`）可以作为标识符使用。JDK 9 将下划线列为关键字，不能再作为标识符使用

```Java
// JDK 8及以下版本合法
int _ = 10;

// JDK 9及以上版本编译错误
int _ = 10;
```

这一变化是为了将来支持 lambda 表达式的未使用参数语法



**String 底层结构改变**

JDK 9 对 String 的底层实现进行了重大改变，从原来的`char[]`改为`byte[]`加上编码标识。这一改变可以显著减少字符串的内存占用，因为大多数字符串只包含 Latin-1 字符，使用`byte[]`比`char[]`节省一半的内存

这一改变对开发者是透明的，不需要修改任何代码



 **CompletableFuture 增强**

JDK 9 对`CompletableFuture`进行了增强，新增了多个实用方法：

- `defaultExecutor()`：返回默认的执行器
- `copy()`：创建 CompletableFuture 的副本
- `completeAsync()`：异步完成 CompletableFuture
- `orTimeout()`：如果在指定时间内没有完成，则抛出 TimeoutException
- `completeOnTimeout()`：如果在指定时间内没有完成，则使用指定的值完成



---

# JDK 10

## 简介



2018 年 3 月 21 日，Oracle 官方宣布 Java 10 正式发布

JDK 10 是 Java 采用半年发布周期后的第一个正式版本，它在 JDK 9 模块化架构的基础上，进一步优化了开发体验与运行性能。其最具标志性的特性是局部变量类型推断（var 关键字），大幅简化了变量声明的冗余代码，让 Java 代码更加简洁优雅

与此同时，JDK 10 在 JVM 性能、容器环境适配、集合 API 等方面也带来了多项实用改进，例如 G1 并行 Full GC、容器资源自动识别、不可变集合收集器等，进一步提升了 Java 应用的运行效率与云原生适配能力



需要注意的是 Java 9 和 Java 10 都不是 LTS 版本，和过去的 Java 大版本升级不同，这两个只有半年左右的开发期和维护期。而未来的 Java 11，也就是 18.9 LTS，才是 Java 8 之后第一个 LTS 版本

JDK 10 一共定义了 109 个新特性，其中包含 12 个 JEP（对于程序员来讲，真正的新特性其实就 1 个，还有一些新的 API 和 JVM 规范以及 Java 语言规范上的改动

JDK 10 的 12 个 JEP，可参阅官方文档：https://openjdk.org/projects/jdk/10/



---

## 局部变量类型推断



局部变量类型推断是 JDK 10 最核心、最具代表性的特性。它允许开发者在声明局部变量时省略变量类型，由编译器根据右侧的初始化表达式自动推断类型

`var`并不是一个关键字，而是一个保留类型名，它只在局部变量声明的位置具有特殊含义，在其他位置仍然可以作为普通标识符使用。类型推断完全发生在编译期，推断出的类型和显式声明的类型完全一致，不会影响运行时性能，也不会丢失静态类型安全



在传统 Java 代码中，变量声明往往存在严重的类型冗余，尤其是复杂泛型场景，左侧的类型声明完全可以从右侧的初始化表达式推导出来，属于重复书写

**传统写法示例**

```Java
public class TraditionalVarDemo {
    public static void main(String[] args) {
        // 简单类型：左右两侧类型重复
        String str = new String("Hello World");
        Integer num = Integer.valueOf(100);

        // 复杂泛型：类型声明冗长繁琐
        Map<String, List<Map<Integer, String>>> complexMap = new HashMap<>();

        // 循环中：类型声明增加代码长度
        for (Map.Entry<String, List<Map<Integer, String>>> entry : complexMap.entrySet()) {
            System.out.println(entry.getKey());
        }
    }
}
```



**var 关键字方案**

```Java
public class VarDemo {
    public static void main(String[] args) {
        // 简单类型：自动推断
        var str = new String("Hello World");
        var num = Integer.valueOf(100);

        // 复杂泛型：大幅简化
        var complexMap = new HashMap<String, List<Map<Integer, String>>>();

        // 循环中：代码更简洁
        for (var entry : complexMap.entrySet()) {
            System.out.println(entry.getKey());
        }
    }
}
```

使用`var`后，代码更加简洁，开发者可以专注于变量名与业务逻辑，而非冗余的类型声明。对于复杂泛型场景，简化效果尤为明显



使用`var`声明局部变量时，必须满足以下基本条件：

1. 必须是局部变量（方法内部、代码块内部、for 循环内部）
2. 必须在声明时进行初始化
3. 初始化表达式不能为`null`
4. 不能同时声明多个变量



`var`并非在所有地方都能使用，以下场景无法使用`var`：

1. 成员变量（字段）：类的成员变量不能使用`var`
2. 方法参数：方法的形参不能使用`var`
3. 方法返回值类型：方法的返回值类型不能使用`var`
4. 无初始化的变量声明：只声明不赋值无法推断类型
5. 初始化为 null：null 无法推断具体类型
6. 多变量同时声明：`var a = 1, b = 2;` 不允许
7. 数组声明：`var[] arr = new int[10];` 不允许



---

## Optional 类增强



JDK 8 引入 Optional 类，JDK 9 对其进行了功能扩充，JDK 10 进一步优化了 Optional 的 API，新增了一个非常实用的无参`orElseThrow()`方法

JDK 10 为`Optional`类新增了无参的`orElseThrow()`方法。该方法的作用是：如果 Optional 包含非空值，则返回该值；如果 Optional 为空，则直接抛出`NoSuchElementException`异常

在此之前，`orElseThrow()`方法需要传入一个异常提供者`Supplier`，用于自定义异常类型。而新增的无参版本提供了默认的异常抛出行为，简化了最常用的 "空值即抛异常" 场景



**JDK 9 及之前的写法**：

```Java
public class TraditionalOptionalDemo {
    public static void main(String[] args) {
        Optional<String> optional = Optional.ofNullable(getName());

        // 方式1：传入异常Supplier，代码冗余
        String name1 = optional.orElseThrow(() -> new java.util.NoSuchElementException());

        // 方式2：先判断再get，代码繁琐
        if (optional.isPresent()) {
            String name2 = optional.get();
        } else {
            throw new java.util.NoSuchElementException();
        }
    }

    private static String getName() {
        return null;
    }
}
```



**JDK 10 的写法：**

```Java
public class OptionalEnhanceDemo {
    public static void main(String[] args) {
        Optional<String> optional = Optional.ofNullable(getName());

        // 无参orElseThrow()，简洁且语义清晰
        String name = optional.orElseThrow();
    }

    private static String getName() {
        return null;
    }
}
```



虽然`orElseThrow()`无参版本和`get()`方法在行为上几乎完全一致（空值时都抛出`NoSuchElementException`），但它们的设计语义不同：

- `get()`：设计初衷是获取值，空值抛异常属于 "意外"，语义不明确，官方不推荐直接使用
- `orElseThrow()`：语义明确，显式表达了 "空值则抛出异常" 的业务意图，是推荐的写法



---

## 集合与 Stream API 增强



JDK 9 引入了集合工厂方法用于创建不可变集合，JDK 10 进一步完善了不可变集合体系，新增了集合复制方法和 Stream 不可变收集器

JDK 10 对集合框架和 Stream API 的增强主要包含两部分：

1. 集合复制方法：`List.copyOf()`、`Set.copyOf()`、`Map.copyOf()`，用于根据已有集合创建不可变副本
2. 不可变集合收集器：`Collectors.toUnmodifiableList()`、`Collectors.toUnmodifiableSet()`、`Collectors.toUnmodifiableMap()`，用于将 Stream 收集为不可变集合

这些方法返回的集合都是真正的不可变集合，不支持添加、删除、修改操作，与 JDK 9 的集合工厂方法返回的集合类型一致



```Java
public class ImmutableEnhanceDemo {
    public static void main(String[] args) {
        List<String> original = new ArrayList<>();
        original.add("Java");
        original.add("Python");

        // 1. copyOf()创建真正的不可变副本
        List<String> copy = List.copyOf(original);
        original.add("C++");
        System.out.println(copy); // 输出[Java, Python]，不受原集合影响

        // 2. Stream直接收集为不可变集合
        List<String> result = original.stream()
                                     .filter(s -> s.length() > 3)
                                     .collect(Collectors.toUnmodifiableList());
    }
}
```



---

## JVM 性能优化特性



JDK 10 在 JVM 层面带来了多项重要的性能优化，其中最具代表性的是 G1 并行 Full GC 和应用类数据共享（AppCDS）开源，这些优化可以在不修改业务代码的情况下直接提升应用性能



**G1 垃圾收集器并行 Full GC**

G1 垃圾收集器在 JDK 7 中引入，JDK 9 成为默认垃圾收集器。但在 JDK 10 之前，G1 的 Full GC 是单线程执行的，当 G1 退化为 Full GC 时，停顿时间会非常长

在 G1 的设计中，理想情况下是通过并发标记和混合回收避免 Full GC，但在极端情况下（如内存不足、巨型对象分配）仍然会触发 Full GC。单线程 Full GC 在多核服务器上效率极低，长时间停顿会严重影响业务可用性

并行 Full GC 充分利用了多核 CPU 的优势，将 Full GC 的停顿时间缩短数倍，提升了极端场景下的应用稳定性



**应用类数据共享（AppCDS）开源**

类数据共享（CDS）是一项将类的元数据共享到归档文件中的技术，应用启动时可以直接加载归档文件，减少类加载的时间和内存占用

传统 Java 应用启动时需要加载大量类，耗时较长，且多个 Java 进程运行时，相同的类会被重复加载，浪费内存。AppCDS 通过以下方式优化：

1. 缩短启动时间：直接加载预先生成的归档文件，跳过类解析、验证等步骤
2. 降低内存占用：多个 JVM 进程可以共享同一份类元数据，减少内存占用



**线程局部握手**

线程局部握手是 JVM 安全点机制的优化。传统的安全点需要所有 Java 线程都到达安全点才能执行操作，而线程局部握手可以针对单个线程执行操作，不需要暂停所有线程

传统安全点机制在执行一些仅需单个线程参与的操作（如线程 dump、取消偏向锁）时，仍然需要暂停所有线程，造成不必要的停顿。线程局部握手将这类操作的影响范围缩小到单个线程，大幅降低了全局停顿的频率和时间



---

## 容器环境原生支持



随着容器化部署的普及，Java 在 Docker 等容器环境中的资源适配问题日益突出。JDK 10 首次原生支持容器环境，JVM 可以自动识别容器的资源限制，合理分配 CPU 和内存

在 JDK 10 之前，JVM 默认读取宿主机的 CPU 和内存信息，无法感知容器的资源限制。这导致在容器中运行 Java 应用时，经常出现内存溢出、CPU 使用不合理等问题



JDK 9 及之前：

- 内存问题：容器限制了 1G 内存，但 JVM 默认按宿主机内存设置堆大小（通常为物理内存的 1/4），导致堆内存过大，最终触发容器 OOM Kill
- CPU 问题：容器限制了 2 核 CPU，但 JVM 按宿主机的 32 核设置 GC 线程数、编译线程数，造成线程过多，资源竞争严重
- 解决方案繁琐：需要手动指定`-Xmx`、`-XX:ParallelGCThreads`等参数，适配成本高

JDK 10：

JVM 自动检测容器环境，读取 cgroup 的资源限制，自动调整内存、线程数等参数，无需手动配置即可在容器中合理运行



---

## 其它重要特性



**Unicode 8.0 支持**

JDK 10 将 Unicode 支持版本升级到了 8.0，新增了大量的字符、表情符号和脚本支持。这意味着 Java 的字符处理、字符串操作可以正确处理更多的 Unicode 字符，满足国际化业务的需求



**根证书集标准化**

JDK 10 在 OpenJDK 中提供了默认的根证书集，基于 Mozilla 的 CA 证书列表。在此之前，OpenJDK 默认没有根证书，导致 HTTPS 连接经常出现证书验证失败的问题，需要手动配置

标准化的根证书集让 OpenJDK 的 HTTPS 访问开箱即用，提升了开发体验和兼容性



 **javah 工具移除**

JDK 10 正式移除了`javah`工具。`javah`用于生成 JNI 头文件，在 JDK 8 中已经可以通过`javac -h`命令替代。JDK 10 彻底移除了过时的`javah`工具，统一使用`javac -h`生成 JNI 头文件



**额外的 Unicode 语言标签扩展**

JDK 10 增强了`java.util.Locale`类，支持更多的 Unicode 语言标签扩展，包括货币类型、数字系统、日历系统等，可以更精细地控制本地化行为



**基于 Java 的 JIT 编译器 Graal 初步集成**

JDK 10 初步集成了 Graal 编译器，它是一款用 Java 编写的高性能 JIT 编译器。Graal 可以作为 JIT 编译器替换 C2，也可以用于 AOT 编译，为 Java 的性能优化提供了新的方向。该特性在后续版本中得到了持续发展



---

# JDK 11

## 简介



JDK 11 是 Java 9 开启半年发布周期后的第二个长期支持版本（LTS），也是目前企业生产环境中使用率极高的稳定版本，接替 JDK 8 成为主流商用版本。相较于 JDK 10 的轻量化迭代，JDK 11 进行了大量实用功能补齐、语法优化、性能升级与废弃特性清理，兼具实用性、稳定性、高性能三大核心优势



JDK 11 延续了 JDK 10 的简洁化设计思路，进一步简化代码编写、完善 API 体系，同时重磅落地多项生产级特性：如 HTTP Client 正式版、单文件 Java 程序运行、var 隐式 Lambda 参数，并在 JVM、垃圾回收、容器适配、安全性上做了深度优化。此外，JDK 11 大规模清理冗余、废弃过时 API，让 Java 体系更加轻量化、规范化



---

## Lambda 表达式支持 var 隐式参数



JDK 10 的 `var `仅支持局部变量类型推断，JDK 11 拓展 var 能力，允许在 Lambda 表达式的形参中使用 `var` 声明参数类型，由编译器自动推断参数类型

该特性不改变 Lambda 原有语法，仅统一代码风格，让 Lambda 参数声明与普通局部变量声明保持一致，兼顾简洁性与代码规范性

```Java
public class LambdaVarDemo {
    public static void main(String[] args) {
        List.of("张三", "李四", "王五").stream()
                // var推断参数类型，简洁优雅
                .filter((var name) -> name.length() > 2)
                // 支持参数注解，弥补省略类型的短板
                .filter((@NonNull var name) -> name.startsWith("张"))
                .forEach(System.out::println);
    }
}

```



---

## 单 Java 文件直接运行（无需编译）



JDK 11 新增单文件源码直接运行机制：对于单个 `.java` 文件，无需手动执行 `javac` 编译，可直接通过 `java 文件名.java` 命令运行源码

JVM 会自动完成临时编译、加载、运行，运行结束后自动清理 class 文件，极大简化了简单程序、测试脚本、Demo 代码的运行流程



```Java
public class HelloJava11 {
    public static void main(String[] args) {
        System.out.println("JDK11 单文件直接运行成功！");
    }
}
```

JDK11 之前运行步骤：javac 编译 -> java 运行 class 文件 -> 手动删除 class 文件

JDK11 运行步骤：直接执行命令

```
java HelloJava11.java
```



---

## String 全新工具方法



JDK 11 为 `String` 类新增 5 个超高实用率的工具方法，解决日常开发中空字符串判断、空白去除、字符串重复、行切割等高频痛点，彻底告别手动工具类封装



**isBlank()** 

判断是否为空白字符串

区别于`isEmpty()`：`isEmpty()`仅判断长度为 0，`isBlank()`可判断空字符串+全空格/制表符/换行符

```Java
public class StringBlankDemo {
    public static void main(String[] args) {
        System.out.println("".isBlank());      // true
        System.out.println("   ".isBlank());   // true
        System.out.println("\t\n".isBlank());  // true
        System.out.println("java".isBlank());  // false

        // 对比 isEmpty
        System.out.println("   ".isEmpty());    // false
    }
}
```



**strip() / stripLeading() / stripTrailing()** 

传统`trim()`仅去除 ASCII 空格，JDK11 `strip()`支持去除全类型空白字符（中文全角空格、Unicode 空白符），适配国际化场景

```Java
public class StringStripDemo {
    public static void main(String[] args) {
        // 包含全角空格
        String str = "　 Java11 　";
        System.out.println(str.trim());    // 无法去除全角空格
        System.out.println(str.strip());   // 精准去除所有空白
        System.out.println(str.stripLeading()); // 去除开头空白
        System.out.println(str.stripTrailing()); // 去除结尾空白
    }
}
```



**repeat(int count)** 

快速生成重复字符串，无需循环拼接，底层优化性能更高

```Java
public class StringRepeatDemo {
    public static void main(String[] args) {
        System.out.println("Java ".repeat(3)); // Java Java Java 
        System.out.println("*".repeat(10));    // **********
    }
}
```



**lines()** 

将多行字符串按换行符切割为 Stream 流，自动适配 Windows/Linux 换行符，便捷处理文本内容

```Java
public class StringLinesDemo {
    public static void main(String[] args) {
        String text = "Java8\nJava11\r\nJava17";
        text.lines().forEach(System.out::println);
    }
}
```



---

## Optional 类增强



`isPresent()`：判断非空

`isEmpty()`：判断为空（JDK11新增）

```Java
public class OptionalEmptyDemo {
    public static void main(String[] args) {
        Optional<String> emptyOpt = Optional.empty();
        System.out.println(emptyOpt.isEmpty()); // true
        System.out.println(emptyOpt.isPresent()); // false
    }
}

```



---

## 集合与 Stream 小幅优化



延续 JDK 9/10 不可变集合体系，JDK 11 微调 Stream 空安全逻辑，优化空集合遍历性能，无语法改动，底层运行效率小幅提升



---

## 标准化 HTTP Client API



HTTP Client 从 JDK 9 孵化、JDK 10 预览，在 JDK 11 正式转正标准化，成为 JDK 原生内置的 HTTP 请求工具。

从此 Java 无需依赖  HttpClient、OkHttp、RestTemplate 等第三方框架，原生支持 HTTP/1.1、HTTP/2、HTTPS、同步/异步请求、流式响应、超时配置，完全满足日常接口调用需求



---

## JVM 与 GC 重磅新特性



**ZGC 低延迟垃圾收集器**

JDK 11 首次引入 ZGC（Z Garbage Collector），一款面向超大内存、超低延迟的垃圾收集器，是 Java 低延迟场景的革命性优化



核心亮点

1. 极低停顿时间：停顿时间控制在 10 ms 以内，几乎无感 GC
2. 支持 TB 级内存：完美适配超大堆内存服务
3. 几乎所有 GC 阶段并发执行：最大化减少 STW（Stop-The-World）



启用方式

```SH
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -jar app.jar
```



**G1 GC 深度优化**

基于JDK 10 G1并行 Full GC，JDK 11 进一步优化：

1. 优化 G1 混合回收算法，减少无效扫描
2. 降低 GC 内存碎片，提升大对象分配成功率
3. 优化自适应堆大小调整逻辑，适配动态负载



**容器化资源适配升级**

在 JDK 10 容器支持基础上，JDK 11 完善 cgroup v1 适配，精准识别 K8s 资源配额，修复容器内存计算偏差问题，容器环境下 JVM 参数自适应更加精准稳定
