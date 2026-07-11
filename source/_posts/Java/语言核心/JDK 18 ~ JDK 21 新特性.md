---
title: JDK 18 ~ JDK 21 新特性
date: 2026-01-26 21:45:49
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%2018%20~%20JDK%2021%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%2018%20~%20JDK%2021%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
tags:
  - Java
  - JDK
categories: Java
---





# JDK 18

## 简介



JDK 18 是 Java 开启半年发布周期后的第九个正式版本，属于非长期支持（Non-LTS）版本，也是 JDK 17 LTS 落地后的首个迭代版本。它没有推出颠覆性的全新语法，更多聚焦于现有特性打磨、开发体验补全、底层基建升级，是一个务实性极强的优化版本。其中最具普惠价值的特性是默认字符集统一为 UTF-8，彻底解决了困扰 Java 开发者数十年的跨平台字符集乱码问题；内置简单 Web 服务器、JavaDoc 代码片段增强等特性，则从工具链层面进一步降低了开发测试成本

与此同时，Switch 模式匹配迎来第二次预览优化，语法细节与穷尽性校验更趋完善；外部函数与内存 API、Vector API 持续迭代孵化，高性能与原生交互能力稳步推进；服役二十余年的终结器（Finalizer）正式标记为待移除，标志着 Java 资源管理机制彻底向现代化方向转型。整体来看，JDK 18 承上启下，既修复了长期存在的历史痛点，又为 JDK 21 LTS 的特性定型做好了充分铺垫



---

## 默认 UTF-8 字符集



默认字符集统一为 UTF-8 是 JDK 18 最具普惠性的特性，彻底解决了 Java 跨平台运行时因默认字符集不一致导致的乱码问题，是影响范围最广的一项改进

在 JDK 18 之前，Java 的默认字符集由操作系统决定：Linux/macOS 下默认 UTF-8，Windows 中文环境下默认 GBK。这导致同一份代码在不同系统上运行时，`String.getBytes()`、`InputStreamReader`等依赖默认字符集的 API 会产生不同结果，频繁出现本地正常、服务器乱码，或者 Windows 开发、Linux 部署乱码的问题

JDK 18 起，所有平台的默认字符集统一为 UTF-8，`Charset.defaultCharset()`默认返回`UTF-8`，所有依赖默认字符集的标准库 API 行为完全一致，实现了真正的跨平台字符集统一



---

## 内置工具增强



JDK 18 新增了多个开箱即用的开发工具，补齐了本地测试、文档编写的工具链短板，无需额外安装第三方软件即可完成常见开发操作

<br>

**简单 Web 服务器（jwebserver）**

JDK 18 内置了一款纯静态文件 HTTP 服务器，通过命令`jwebserver`即可快速启动，将指定目录作为静态资源根目录，提供 HTTP 文件服务。它定位是开发测试工具，用于本地页面调试、静态资源共享、接口 Mock 测试等场景，替代了以往需要安装 Nginx、Python HTTP Server、Node.js 静态服务等第三方工具的需求

<br>

打开命令行，直接执行命令即可启动：

```SH
# 最简启动：默认端口8080，根目录为当前目录，绑定localhost
jwebserver
```

常用参数：

```SH
# 指定端口8000、指定静态资源目录、绑定所有网卡地址
jwebserver -p 8000 -d ./static -b 0.0.0.0
```

<br>

**JavaDoc 代码片段增强**

JDK 18 优化了 JavaDoc 的代码示例能力，新增`@snippet`标签，用于在文档中嵌入标准化的代码片段。相比传统的`<pre>`标签，它支持语法高亮、代码编译校验、外部文件引用等能力，避免文档中的示例代码出现语法错误、与实际代码不同步的问题

<br>

内联代码片段

```Java
/**
 * 字符串工具类
 *
 * 使用示例：
 * {@snippet :
 * String result = StringUtils.toUpperCase("hello");
 * System.out.println(result); // 输出 HELLO
 * }
 *
 * @author admin
 */
public class StringUtils {
    public static String toUpperCase(String s) {
        return s == null ? null : s.toUpperCase();
    }
}
```

还可以直接引用项目中的真实代码文件，保证文档示例与实际代码完全同步：

```Java
/**
 * {@snippet file="StringUtilsExample.java" region="basic-usage"}
 */
```

<br>

**互联网地址解析 SPI**

JDK 18 开放了互联网地址解析的服务提供者接口（SPI），允许开发者自定义域名解析逻辑，替代 JDK 默认依赖操作系统的域名解析方式

在此之前，Java 的域名解析完全依赖系统的`/etc/hosts`和 DNS 配置，无法灵活定制。开放 SPI 后，可以实现自定义 DNS 缓存、内网域名解析、域名劫持防护、动态域名解析等能力



---

## Switch 模式匹配（第二次预览）



继 JDK 17 首次预览后，Switch 模式匹配在 JDK 18 迎来第二次预览迭代。本次迭代重点优化了穷尽性校验逻辑、模式变量作用域规则与语法歧义，修复了首版的边缘场景问题，语法形态更趋稳定，为后续转正打下坚实基础

本次预览的核心优化点

1. 密封类穷尽性校验增强：当 Switch 的匹配目标是密封类的父类型、泛型类型时，编译器也能准确识别所有允许的子类，正确执行穷尽性校验，避免漏判
2. 模式变量作用域优化：进一步明确模式变量的作用域边界，修复了嵌套场景下的作用域歧义问题，规则更符合直觉
3. null 处理逻辑优化：统一了 null 分支的匹配优先级与行为，`case null`的语义更清晰
4. 守卫条件语法完善：`when`守卫子句的类型推断更精准，支持更复杂的条件表达式

核心语法与 JDK 17 首版保持一致，日常使用的写法基本不变，主要是底层规则与边缘场景的优化



---

## 底层基建与孵化特性



**核心反射底层重写**

JDK 18 使用方法句柄（Method Handles）重新实现了`java.lang.reflect`包的核心反射逻辑，替代了沿用多年的原生代码（JNI）实现。反射调用的核心路径不再依赖本地 C 代码，全部转为 Java 层面的方法句柄实现

所有使用反射的场景都会受益，尤其是依赖大量反射的框架（Spring、MyBatis 等）、动态代理、ORM 映射等场景，性能提升更明显

<br>

**外部函数与内存 API（第二次孵化）**

外部函数与内存 API（Foreign Function & Memory API）是 Project Panama 的核心成果，目标是替代传统 JNI，提供纯 Java 的、类型安全的原生代码调用与堆外内存管理能力。JDK 17 首次孵化，JDK 18 进入第二次孵化，优化了 API 设计，提升了性能与易用性

主要面向高性能、底层交互场景，如调用操作系统原生 API、集成 C/C++ 高性能库、大数据与机器学习原生组件对接等。普通业务开发者了解即可，底层框架与高性能组件开发会直接受益

<br>**Vector API（第三次孵化）**

Vector API 利用 CPU 的 SIMD（单指令多数据流）指令实现数据并行计算，大幅提升数值计算性能。JDK 18 进入第三次孵化，进一步完善了 API 设计，支持更多 CPU 架构，优化了标量回退的性能表现

在数值计算、图像处理、密码学、大数据算法等场景下，可通过向量指令一次处理多个数据，性能提升数倍甚至一个数量级，补齐了 Java 在高性能数值计算领域的短板



---

## GC 与 JVM 性能优化



JDK 18 没有推出全新的垃圾收集器，而是对现有主流收集器进行了细节优化，进一步提升稳定性、降低停顿、减少内存占用，整体性能持续稳步提升



---

## 终结器正式标记为待移除



终结器（Finalizer）是 Java 最古老的资源回收机制之一，从 JDK 1.0 就已存在。JDK 18 正式将其标记为待移除（Deprecated for Removal），标志着这一古老机制即将退出历史舞台，也是 Java 资源管理现代化的重要里程碑

`Object`类中的`finalize()`方法是终结器的核心：垃圾回收器在回收对象内存之前，会调用该对象的`finalize()`方法，开发者可以在其中执行资源释放逻辑

但该机制天生存在严重缺陷：

- 不可靠：终结器的执行时间、执行线程完全不确定，甚至可能永远不会执行
- 性能差：会拖慢垃圾回收的速度，增加 GC 停顿时间
- 内存泄漏：终结器执行异常会导致对象无法被回收，引发内存泄漏
- 安全隐患：可能被恶意利用，绕过安全检查
- 线程不确定：在 Finalizer 线程中执行，无法控制线程优先级与资源

JDK 18 中`finalize()`方法以及相关的`Finalizer`机制被正式标记为`@Deprecated(forRemoval = true)`，未来的 JDK 版本会彻底移除相关代码



---

# JDK 19

## 简介



JDK 19 是 Java 开启半年发布周期后的第十个正式版本，属于非长期支持（Non-LTS）版本，也是 Java 并发编程发展史上具有里程碑意义的版本。由 Project Loom 孵化多年的虚拟线程（Virtual Threads）首次进入预览阶段，彻底打破了传统操作系统线程的并发瓶颈，让 Java 以极低的成本支持百万级并发，同时保留同步编程的简洁性，从根本上解决了高并发场景下 “异步编程复杂度高、同步编程并发量不足” 的长期痛点

除虚拟线程这一重磅特性外，JDK 19 在语法与并发模型上持续推进：记录模式（Record Patterns）首次预览，与 Record、密封类配合实现更强大的模式匹配能力；Switch 模式匹配迎来第三次预览，整合记录模式能力，语法形态趋于稳定；结构化并发（Structured Concurrency）进入孵化器，重新定义了多任务并行的编程范式，让并发代码更可靠、更易调试。与此同时，外部函数与内存 API、Vector API 持续迭代孵化，GC 与平台能力稳步优化，整体向着 JDK 21 LTS 的最终形态快速收敛



---

## 虚拟线程（首次预览）



虚拟线程是 JDK 19 最具标志性的重磅特性，也是 Project Loom 多年研发的核心成果。它是 JVM 实现的轻量级线程，无需映射到操作系统内核线程，内存占用小、创建成本低，可轻松支持百万级并发，同时完全兼容现有的 Thread API，让开发者以同步编程的简单写法，获得远超传统线程的并发能力

Java 中的传统线程（平台线程）是与操作系统内核线程一一对应的，内核线程的调度由操作系统负责，每个线程默认占用约 1MB 的栈内存，上下文切换开销大，一台普通服务器最多只能支撑几千到上万条并发线程

虚拟线程是由 JVM 管理的轻量级线程，它是用户态的线程实现，多条虚拟线程可以共享同一条平台线程（载体线程）。当虚拟线程遇到阻塞操作（如 IO 等待、Sleep）时，JVM 会自动将其从载体线程上卸载，唤醒时再重新挂载，全程不占用平台线程。虚拟线程的内存占用仅为 KB 级，创建与调度开销极低，单台服务器可轻松运行百万级虚拟线程

该特性在 JDK 19 为首次预览版，需添加`--enable-preview`参数启用，经过 JDK 20 二次预览后，在 JDK 21 LTS 正式转正

<br>

传统 Java 并发模型存在不可调和的痛点：高并发场景下，平台线程资源有限，同步编程模型并发量上不去；为了提升吞吐量，开发者不得不转向 RxJava、CompletableFuture 等异步编程模型，但代码复杂度陡增、调试困难、异常堆栈难以追踪，开发与维护成本极高

```Java
public class PlatformThreadDemo {
    public static void main(String[] args) {
        // 传统线程池：线程数有限，通常是CPU核数的几倍
        ExecutorService executor = Executors.newFixedThreadPool(200);
        
        for (int i = 0; i < 10000; i++) {
            executor.submit(() -> {
                // IO密集型任务：大部分时间阻塞等待
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName());
            });
        }
        
        executor.shutdown();
    }
}
```

<br>

虚拟线程解决方案：

```Java
public class VirtualThreadDemo {
    public static void main(String[] args) {
        // 每个任务分配一个虚拟线程，无需池化，轻松支持十万百万级
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 0; i < 100000; i++) {
                executor.submit(() -> {
                    Thread.sleep(1000);
                    System.out.println(Thread.currentThread().getName());
                });
            }
        }
    }
}
```

<br>

**基本语法与用法**

直接创建虚拟线程

```Java
// 方式1：Thread.startVirtualThread() 快速启动
Thread.startVirtualThread(() -> {
    System.out.println("虚拟线程运行中");
});

// 方式2：Thread.Builder 构建，支持自定义名称、优先级等
Thread vThread = Thread.ofVirtual()
        .name("my-virtual-thread-", 0)
        .start(() -> {
            System.out.println(Thread.currentThread().getName());
        });
```

虚拟线程执行器（推荐）

通过`Executors.newVirtualThreadPerTaskExecutor()`创建执行器，每个提交的任务都会分配一个独立的虚拟线程，自动管理生命周期

```Java
// try-with-resources 自动关闭，等待所有任务完成
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    // 提交10万个任务，每个任务对应一个虚拟线程
    for (int i = 0; i < 100000; i++) {
        int taskId = i;
        executor.submit(() -> {
            // 模拟IO阻塞操作
            Thread.sleep(100);
            return taskId * 2;
        });
    }
}
```



---

## 记录模式（首次预览）



记录模式（Record Patterns）是 JDK 19 引入的全新语法特性，它扩展了模式匹配的能力，支持对 Record 对象进行解构，直接在模式匹配中提取 Record 的字段值，无需再手动调用访问器方法，进一步简化了数据处理的样板代码。该特性与 Record、密封类、Switch 模式匹配深度协同，是现代 Java 类型体系的重要组成部分

传统的 instanceof 模式匹配只能判断类型并绑定变量，要获取 Record 的字段还需要调用访问器方法；记录模式则可以在匹配类型的同时，直接解构 Record 对象，将字段提取为变量，一步完成类型判断与字段提取

该特性在 JDK 19 为首次预览版，需启用预览参数，经过 JDK 20 二次优化后，在 JDK 21 LTS 正式转正

<br>

处理 Record 数据对象时，传统写法需要先匹配类型，再逐个调用访问器获取字段，代码层级多，样板代码冗余

```Java
public int calculateSum(Object obj) {
    if (obj instanceof Point p) {
        // 需要手动调用访问器获取字段
        int x = p.x();
        int y = p.y();
        return x + y;
    }
    return 0;
}
```

记录模式解决方案：

```Java
public int calculateSum(Object obj) {
    // 直接解构，一步提取x和y
    if (obj instanceof Point(int x, int y)) {
        return x + y;
    }
    return 0;
}
```

代码更紧凑，语义更直接，省去了中间变量的声明与赋值

<br>

**基本语法与用法**

instanceof 记录模式

```Java
// 定义Record
public record Point(int x, int y) {}

// 解构使用
public class RecordPatternDemo {
    public static void main(String[] args) {
        Object obj = new Point(3, 4);
        
        // 类型匹配 + 字段解构
        if (obj instanceof Point(int x, int y)) {
            System.out.println("x: " + x + ", y: " + y);
            System.out.println("和: " + (x + y));
        }
    }
}
```

支持嵌套 Record 的多层解构，直接提取深层字段，无需逐层判断

```Java
// 嵌套Record
public record Circle(Point center, double radius) {}

// 嵌套解构
public double getCenterX(Object obj) {
    if (obj instanceof Circle(Point(int x, int y), double r)) {
        // 直接提取深层的x字段
        return x;
    }
    return 0;
}
```

记录模式与 Switch 模式结合，是最常用的场景，简洁处理多类型数据分支

```Java
public double calculateArea(Shape shape) {
    return switch (shape) {
        case Circle(Point center, double r) -> Math.PI * r * r;
        case Rectangle(double w, double h) -> w * h;
        case Triangle(double base, double h) -> base * h / 2;
    };
}
```



---

## Switch 模式匹配（第三次预览）



继 JDK 17 首次预览、JDK 18 二次预览后，Switch 模式匹配在 JDK 19 迎来第三次预览迭代。本次迭代核心是整合记录模式能力，优化穷尽性校验规则与语法细节，整体语法形态趋于稳定，为 JDK 21 LTS 正式转正做好最终准备

本次预览的核心优化点

1. 支持记录模式：新增对 Record 解构的支持，Switch 分支可直接匹配并解构 Record 对象，模式匹配能力大幅增强。
2. 穷尽性校验更精准：密封类、泛型场景下的穷尽性判断更严谨，边缘场景下的漏判、误判问题得到修复。
3. 语法规则统一：统一了各种模式的作用域规则、变量绑定逻辑，语法一致性更好。
4. null 处理逻辑优化：进一步明确 null 分支的匹配优先级与行为，语义更清晰

<br>

**记录模式（本次新增）**

```Java
public double getArea(Shape shape) {
    return switch (shape) {
        case Circle(_, double r) -> Math.PI * r * r; // _表示忽略该字段
        case Rectangle(double w, double h) -> w * h;
        case Triangle(double b, double h) -> b * h / 2;
    };
}
```

本次新增下划线`_`模式，用于表示忽略对应位置的字段，语义更清晰



---

## 结构化并发（孵化器）



结构化并发（Structured Concurrency）是 JDK 19 引入的孵化器特性，是 Project Loom 的另一项重要成果。它提出了全新的并发编程思想：将多个子任务视为一个统一的工作单元，统一管理生命周期，解决了传统线程池并发的错误传播难、资源泄漏、调试困难等问题，让并发代码的结构、错误处理、可观测性都得到显著提升

传统的线程池并发模型中，提交的子任务是独立的：主线程失败了子线程可能还在运行，子任务失败了主线程无法及时感知，子任务的生命周期没有和父任务绑定，属于 “非结构化” 的并发

结构化并发要求：任务的生命周期与代码的结构块绑定，就像结构化编程中代码块的进入与退出一样。进入作用域时提交子任务，退出作用域时等待所有子任务完成，统一处理成功与失败，保证所有子任务在作用域结束时都被终止，不会出现野线程

该特性在 JDK 19 为孵化器阶段，需添加`--add-modules jdk.incubator.concurrent`参数启用，后续版本持续迭代，逐步走向成熟

<br>

传统 ExecutorService 拆分并行任务时，存在诸多痛点：

- 一个子任务失败，其他子任务仍在运行，浪费资源
- 主线程异常退出，子任务不会停止，造成资源泄漏
- 异常堆栈不清晰，难以追踪父子任务的关系
- 调试困难，无法直观看到任务的层级结构

```Java
// 并行调用两个接口，组装结果
public UserInfo getUserInfo(Long userId) throws ExecutionException, InterruptedException {
    try (ExecutorService executor = Executors.newFixedThreadPool(2)) {
        Future<BasicInfo> basicFuture = executor.submit(() -> getBasicInfo(userId));
        Future<DetailInfo> detailFuture = executor.submit(() -> getDetailInfo(userId));
        
        // 一个任务失败，另一个还会继续运行，浪费资源
        return new UserInfo(basicFuture.get(), detailFuture.get());
    }
}
```

结构化并发解决方案：

```Java
public UserInfo getUserInfo(Long userId) throws Exception {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        // 提交两个子任务
        var basicTask = scope.fork(() -> getBasicInfo(userId));
        var detailTask = scope.fork(() -> getDetailInfo(userId));
        
        // 等待所有任务完成，任意一个失败则全部取消
        scope.join();
        scope.throwIfFailed();
        
        // 全部成功则获取结果
        return new UserInfo(basicTask.get(), detailTask.get());
    }
}
```

<br>

**基本语法与用法**

ShutdownOnFailure 策略（失败即关闭）

只要有一个子任务失败，就取消所有其他子任务，适合所有子任务都必须成功的场景

```Java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    // 提交多个子任务
    var task1 = scope.fork(() -> task1());
    var task2 = scope.fork(() -> task2());
    
    scope.join();           // 等待所有任务结束
    scope.throwIfFailed();  // 有失败则抛出异常
    
    // 全部成功，获取结果
    return new Result(task1.get(), task2.get());
}
```

ShutdownOnSuccess 策略（成功即关闭）

只要有一个子任务成功，就取消其他所有子任务，适合只要一个结果即可的场景

```Java
try (var scope = new StructuredTaskScope.ShutdownOnSuccess<String>()) {
    scope.fork(() -> queryFromDB(id));
    scope.fork(() -> queryFromCache(id));
    scope.fork(() -> queryFromRemote(id));
    
    scope.join();
    // 返回第一个成功的结果
    return scope.result();
}
```



---

##  底层孵化与平台特性



**外部函数与内存 API（第三次孵化）**

外部函数与内存 API（Foreign Function & Memory API）进入第三次孵化，进一步优化了 API 设计，提升了性能与易用性，降低了 JNI 的替代门槛。它允许纯 Java 代码安全地调用原生库、管理堆外内存，无需编写 JNI 代码，类型安全且性能更优

该特性主要面向底层框架开发、高性能计算、原生库集成场景，普通业务开发者了解即可

<br>

**Vector API（第四次孵化）**

Vector API 进入第四次孵化，完善了更多向量操作，优化了不同 CPU 架构的适配，提升了标量回退的性能。它利用 CPU 的 SIMD 指令实现数据并行计算，在数值计算、图像处理、密码学等场景可带来数倍性能提升

<br>

**Linux/RISC-V 端口正式支持**

JDK 19 正式提供 Linux/RISC-V 架构的完整支持，将 Java 生态拓展到 RISC-V 指令集架构，适配新一代硬件发展趋势，在嵌入式、服务器、物联网等场景有重要意义



---

# JDK 20

## 简介



JDK 20 是 Java 开启半年发布周期后的第十一个正式版本，属于非长期支持（Non-LTS）版本，也是 JDK 21 LTS 落地前的最后一个打磨版本，承担了「特性定型、生态补全、稳定性验证」的核心作用。经过前序版本的多轮迭代，虚拟线程、记录模式、Switch 模式匹配三大核心特性全部进入最终预览阶段，语法与 API 形态完全稳定，与 JDK 21 正式版高度一致；并发生态进一步完善，新增作用域值（Scoped Values）孵化器特性，为虚拟线程提供了轻量安全的上下文传递方案，与结构化并发共同构成了下一代并发编程的完整体系

整体来看，JDK 20 没有颠覆性的全新特性，更偏向于现有创新的打磨与补全：语法特性收敛定型，并发生态闭环成型，底层能力持续优化。它既是 JDK 19 创新特性的稳定版，也是 JDK 21 LTS 的预演版，是从 JDK 17 向新一代 LTS 过渡的最佳中间验证节点



---

## 虚拟线程（第二次预览）



继 JDK 19 首次预览后，虚拟线程在 JDK 20 迎来第二次预览迭代。本次迭代以性能优化、稳定性提升、生态完善为核心，修复了首版的边缘问题，优化了调度效率与阻塞适配，补全了监控调试能力，语法与 API 完全稳定，是正式转正前的最终形态

<br>

本次预览的核心优化点

1. 调度性能全面升级

   优化了底层 ForkJoinPool 调度器的任务分发逻辑，高并发场景下的调度吞吐量提升显著；优化了虚拟线程的挂载 / 卸载机制，减少上下文切换开销，IO 密集型场景的整体吞吐量进一步提升。

2. 阻塞操作适配全覆盖

   补全了更多阻塞场景的虚拟线程适配：更多类型的锁、文件 IO、JNI 调用的阻塞场景优化，进一步减少了阻塞操作占用载体线程的时间，最大程度发挥虚拟线程的并发优势。

3. 监控与调试能力完善

   JFR 新增多项虚拟线程专属事件，可追踪虚拟线程的创建、挂载、卸载、阻塞等全生命周期状态；线程转储工具支持结构化展示虚拟线程，调试与问题排查更方便。

4. API 对齐与兼容性优化

   进一步对齐 Thread 类的 API，缩小虚拟线程与平台线程的行为差异；优化了与 ThreadLocal 的兼容性，同时为作用域值的落地做好了底层支撑

<br>

虚拟线程执行器（推荐生产用法）

每个任务分配一个独立虚拟线程，无需池化，配合 try-with-resources 自动管理生命周期

```Java
public class VirtualThreadExecutorDemo {
    public static void main(String[] args) {
        // 每个任务对应一个虚拟线程，轻松支撑十万级并发
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 0; i < 10000; i++) {
                int taskId = i;
                executor.submit(() -> {
                    Thread.sleep(100); // IO阻塞自动卸载载体线程
                    return taskId * 2;
                });
            }
        }
    }
}
```



---

## 记录模式（第二次预览）



记录模式在 JDK 19 首次预览后，JDK 20 迎来第二次预览迭代。本次迭代重点优化类型推断、嵌套解构灵活性与穷尽性校验逻辑，修复语法歧义，语法形态完全定型，为 JDK 21 正式转正做好了最终准备

<br>

**instanceof 解构**

类型判断与字段提取一步完成，省去手动调用访问器的样板代码

```Java
public record Point(int x, int y) {}

public int calculateSum(Object obj) {
    if (obj instanceof Point(int x, int y)) {
        return x + y;
    }
    return 0;
}
```

<br>

**嵌套解构**

支持多层嵌套 Record 的直接解构，无需逐层判断与取值

```Java
public record Circle(Point center, double radius) {}

public double getCenterX(Object obj) {
    // 直接提取深层的x字段，下划线忽略不需要的y字段
    if (obj instanceof Circle(Point(int x, _), double r)) {
        return x;
    }
    return 0;
}
```

<br>

**配合 Switch 模式匹配**

与 Switch 模式结合是最主流的使用方式，简洁处理多类型数据分支

```Java
sealed interface Shape permits Circle, Rectangle, Triangle {}
record Circle(double radius) implements Shape {}
record Rectangle(double width, double height) implements Shape {}
record Triangle(double base, double height) implements Shape {}

public double calculateArea(Shape shape) {
    return switch (shape) {
        case Circle(double r) -> Math.PI * r * r;
        case Rectangle(double w, double h) -> w * h;
        case Triangle(double b, double h) -> b * h / 2;
    };
}
```



---

## Switch 模式匹配（第四次预览）



Switch 模式匹配在 JDK 20 迎来第四次预览，也是正式转正前的最后一次预览。本次迭代全面收敛语法规则，定型穷尽性校验逻辑，与记录模式深度协同，语法形态与 JDK 21 正式版完全一致

<br>

**类型模式 + null 分支**

替代多层 if-else instanceof 写法，结构扁平清晰，原生支持 null 处理

```Java
public String formatValue(Object obj) {
    return switch (obj) {
        case null -> "空值";
        case String s -> "字符串：" + s;
        case Integer i -> "整数：" + i;
        case Double d -> "浮点数：" + d;
        default -> "未知类型";
    };
}
```

<br>

**守卫条件（when 子句）**

在类型匹配基础上增加额外过滤条件，实现更精细的分支划分

```Java
public String checkNumber(Number num) {
    return switch (num) {
        case Integer i when i > 100 -> "大整数";
        case Integer i when i < 0 -> "负整数";
        case Integer i -> "普通整数";
        case Double d when d > 100.0 -> "大浮点数";
        default -> "其他数值";
    };
}
```

<br>

**配合记录模式解构**

直接在 Switch 分支中解构 Record 对象，一步完成类型判断与字段提取

```Java
public String getOrderDesc(Order order) {
    return switch (order) {
        case NormalOrder(String id, double amount) when amount > 1000 -> "大额订单：" + id;
        case NormalOrder(String id, double amount) -> "普通订单：" + id;
        case VipOrder(String id, int level) -> "VIP订单，等级：" + level;
    };
}
```



---

## 作用域值（首次孵化器）



作用域值（Scoped Values）是 JDK 20 引入的全新孵化器特性，由 Project Loom 团队推出，专门为虚拟线程场景设计，旨在提供一种轻量、安全、结构化的线程上下文传递方案，替代传统 ThreadLocal 在高并发虚拟线程场景下的短板

作用域值是一种按代码作用域绑定的不可变数据，用于在同一个线程的方法调用链、以及结构化的子任务之间传递上下文数据。它具有以下核心特点：

* 不可变性：绑定后仅可读，不可修改，天然线程安全
* 结构化作用域：与代码块绑定，进入作用域时绑定，退出作用域时自动销毁，无内存泄漏
* 自动继承：结构化并发的子任务、子虚拟线程会自动继承父作用域的值
* 轻量高效：虚拟线程场景下无需每个线程保存副本，内存开销远低于 ThreadLocal

该特性处于孵化器阶段，需添加参数`--add-modules jdk.incubator.concurrent`启用，后续版本会持续优化，逐步走向正式

<br>

传统 ThreadLocal 在虚拟线程时代存在诸多难以解决的问题：

1. 可变不安全：ThreadLocal 的值可被任意修改，容易出现意料之外的线程安全问题
2. 内存开销大：每个线程都保存一份独立副本，百万级虚拟线程会占用大量内存
3. 生命周期不可控：线程池复用场景下容易出现内存泄漏，清理不及时会导致数据污染
4. 子线程继承笨重：InheritableThreadLocal 的子线程继承是浅拷贝，开销大，且无法感知结构化生命周期

作用域值针对性地解决了这些问题：不可变保证安全，作用域绑定保证自动清理，共享不可变数据保证低内存开销，完美适配虚拟线程的高并发场景

<br>

**定义与基础使用**

```Java
public class ScopedValueDemo {
    // 定义一个作用域值，通常声明为静态final
    private static final ScopedValue<String> CURRENT_USER = ScopedValue.newInstance();
    private static final ScopedValue<String> REQUEST_ID = ScopedValue.newInstance();

    public static void main(String[] args) {
        // 绑定值并执行作用域内的代码
        ScopedValue.where(CURRENT_USER, "张三")
                .where(REQUEST_ID, "REQ_001")
                .run(() -> {
                    // 作用域内可直接读取
                    System.out.println("当前用户：" + CURRENT_USER.get());
                    System.out.println("请求ID：" + REQUEST_ID.get());
                    
                    // 调用其他方法，无需传参即可获取上下文
                    processRequest();
                });
        
        // 作用域外无法读取，调用get会抛出NoSuchElementException
    }

    private static void processRequest() {
        // 方法调用链中无需层层传参，直接读取作用域值
        System.out.println("处理请求，用户：" + CURRENT_USER.get());
    }
}
```

<br>

**有返回值的调用**

使用`call`方法替代`run`，支持作用域代码返回结果

```Java
Integer result = ScopedValue.where(CURRENT_USER, "李四")
        .call(() -> {
            // 业务逻辑
            return 100;
        });
```

<br>

**虚拟线程自动继承**

子虚拟线程会自动继承父线程的作用域值，无需手动传递

```Java
ScopedValue.where(CURRENT_USER, "张三").run(() -> {
    // 启动虚拟子线程，自动继承作用域值
    Thread.startVirtualThread(() -> {
        System.out.println("子线程获取用户：" + CURRENT_USER.get()); // 正常读取
    });
});
```



---

## 结构化并发（第二次孵化器）



结构化并发在 JDK 19 首次孵化后，JDK 20 迎来第二次迭代。本次优化聚焦 API 简化、异常处理增强与作用域值集成，任务生命周期管理更严谨，错误传播更清晰，与整个并发生态的协同更顺畅，向生产可用又迈进了一步

1. 深度集成作用域值

   结构化任务作用域内的子任务，会自动继承父线程的所有作用域值，上下文传递无需额外处理，天然支持链路追踪、用户身份等信息的透传

2. 异常处理与堆栈优化

   子任务失败的异常堆栈更完整，保留了父子任务的调用关系，问题排查更直观；失败原因的封装更合理，多任务失败时可完整收集所有异常信息

3. 任务取消逻辑优化

   触发关闭策略后，子任务的取消更及时，资源释放更彻底，减少无效的资源占用

4. API 语义优化

   调整了部分方法命名与参数设计，语义更清晰，使用更符合直觉，降低学习成本



---

# JDK 21

## 简介



JDK 21 是 Java 开启半年发布周期后的第十二个正式版本，也是继 JDK 8、JDK 11、JDK 17 之后的第四个长期支持版本（LTS），Oracle 官方提供长达 8 年的标准支持与扩展维护，是未来 5-8 年企业生产环境的绝对主流选型。它集 JDK 17 以来所有创新之大成，虚拟线程、分代 ZGC、Switch 模式匹配、记录模式、序列集合五大核心特性正式转正，标志着 Java 在并发编程、语法现代化、性能优化三个维度完成了里程碑式的跨越

作为新一代 LTS 基准版本，JDK 21 彻底重构了 Java 的高并发编程模型：虚拟线程让百万级并发成为常态，同步编程重新成为高并发首选；分代 ZGC 在保持亚毫秒停顿的同时大幅提升吞吐量，低延迟与高吞吐不再不可兼得；序列集合补齐了集合框架的多年短板，Switch 模式匹配与记录模式则让现代语法体系全面落地。无论是新项目技术选型，还是存量系统升级迁移，JDK 21 都是当前最稳妥、收益最高的版本选择



---

## 虚拟线程（正式转正）



经过 JDK 19、JDK 20 两轮预览打磨，虚拟线程（Virtual Threads）在 JDK 21 中正式成为 Java 语言标准特性，无需任何预览参数即可直接用于生产环境。这是 Java 近十年来最具颠覆性的并发特性，彻底打破了操作系统线程的并发瓶颈



---

##  Switch 模式匹配（正式转正）



经过 JDK 17 到 JDK 20 共四轮预览迭代，Switch 模式匹配在 JDK 21 中正式成为 Java 语言标准特性，语法完全稳定，可直接用于生产环境。它彻底替代了传统「if-else + instanceof + 强转」的冗余写法，让多类型分支逻辑简洁优雅



---

## 记录模式（正式转正）



经过 JDK 19、JDK 20 两轮预览，记录模式（Record Patterns）在 JDK 21 正式转正。它扩展了模式匹配能力，支持对 Record 对象进行解构，直接提取字段值，与 Switch 模式匹配、密封类深度协同，是现代 Java 代数数据类型体系的核心组成部分

记录模式允许在模式匹配中直接解构 Record 对象，将 Record 的组件提取为变量，一步完成类型匹配与字段提取，无需手动调用访问器方法。它既可以配合 instanceof 使用，也可以在 Switch 模式匹配中使用



---

## 序列集合（正式特性）



序列集合（Sequenced Collections）是 JDK 21 对 Java 集合框架的重要补强，新增了 `SequencedCollection`、`SequencedSet`、`SequencedMap` 三个接口，统一了所有有序集合的操作 API，解决了 List、Deque、SortedSet、LinkedHashMap 等有序集合操作方法不统一、使用别扭的长期痛点

<br>

在 JDK 21 之前，Java 中很多集合都是有序的，但它们获取首尾元素、反转遍历、添加首尾元素的方法却各不相同，没有统一的接口抽象，使用起来非常别扭

```Java
// List 获取首尾元素
String first = list.get(0);
String last = list.get(list.size() - 1);

// Deque 获取首尾元素
String first = deque.getFirst();
String last = deque.getLast();

// SortedSet 获取首尾元素
String first = sortedSet.first();
String last = sortedSet.last();

// LinkedHashMap 获取首尾键值
var first = map.entrySet().iterator().next();
var last = // 更麻烦，需要遍历到最后一个
```

<br>

序列集合新增统一的父接口，所有有序集合都实现该接口，提供统一的操作方法

1. `SequencedCollection`：有序集合父接口，List、Deque、SortedSet 等都实现了它
2. `SequencedSet`：有序 Set 接口，继承 SequencedCollection
3. `SequencedMap`：有序 Map 接口，LinkedHashMap、TreeMap 等都实现了它

| 方法            | 作用                   |
| --------------- | ---------------------- |
| `addFirst(e)`   | 在集合开头添加元素     |
| `addLast(e)`    | 在集合末尾添加元素     |
| `getFirst()`    | 获取第一个元素         |
| `getLast()`     | 获取最后一个元素       |
| `removeFirst()` | 移除并返回第一个元素   |
| `removeLast()`  | 移除并返回最后一个元素 |
| `reversed()`    | 返回集合的逆序视图     |

SequencedMap 还额外提供：

- `firstEntry()`、`lastEntry()`：获取首尾条目
- `pollFirstEntry()`、`pollLastEntry()`：移除并返回首尾条目
- `reversed()`：返回逆序 Map 视图



---

## 分代 ZGC（正式转正）



分代 ZGC（Generational ZGC）是 JDK 21 在 JVM 领域最重磅的升级，也是 ZGC 发展史上的重要里程碑。它在保持亚毫秒级停顿的核心优势基础上，引入分代回收机制，大幅提升了吞吐量、降低了内存占用，让 ZGC 从 “极致低延迟” 走向 “低延迟与高吞吐兼得”，正式成为生产环境的主流 GC 选型

JDK 15 转正的初代 ZGC 虽然停顿极低，但也存在明显短板：

- 内存占用高：所有对象同代回收，需要更大的堆内存空间
- 吞吐量偏低：并发回收开销较大，吞吐量弱于 G1
- 长时间运行易退化：生命周期短的对象占绝大多数，但每次都要和老对象一起回收，效率不高

根据弱分代假说：绝大多数对象都是朝生夕死的。分代 ZGC 将堆分为年轻代与老年代，年轻代对象回收频率高、存活时间短，老年代对象回收频率低。针对不同分代采用不同的回收策略，大幅提升回收效率

<br>

ZGC 早已正式转正，分代 ZGC 在 JDK 21 默认开启，只需一行参数启用 ZGC 即可享受分代收益：

```SH
java -XX:+UseZGC -jar app.jar
```

如需关闭分代（不推荐），可添加参数：

```SH
java -XX:+UseZGC -XX:-ZGenerational -jar app.jar
```



---

## 并发配套孵化特性



JDK 21 在并发体系上持续完善，结构化并发与作用域值两大配套特性进入第二轮孵化器迭代，API 更成熟、设计更合理，与虚拟线程共同构成下一代并发编程的完整生态

<br>

**结构化并发（第二次孵化器）**

结构化并发是一种全新的并发编程范式，将多个子任务视为统一的工作单元，生命周期与代码作用域绑定，解决了传统线程池并发的资源泄漏、错误传播难、调试困难等问题

```Java
public OrderInfo getOrderInfo(Long orderId) throws Exception {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        var baseTask = scope.fork(() -> getOrderBase(orderId));
        var itemTask = scope.fork(() -> getOrderItems(orderId));
        
        scope.join();
        scope.throwIfFailed();
        
        return new OrderInfo(baseTask.get(), itemTask.get());
    }
}
```

<br>

**作用域值（第二次孵化器）**

作用域值（Scoped Values）是专为虚拟线程设计的轻量上下文传递方案，替代传统 ThreadLocal 在高并发场景下的短板，提供不可变、结构化、自动继承的上下文传递能力

```Java
private static final ScopedValue<String> CURRENT_USER = ScopedValue.newInstance();

public void handleRequest() {
    ScopedValue.where(CURRENT_USER, "张三").run(() -> {
        // 作用域内任意方法都可直接读取
        processBusiness();
    });
}
```



---

## 其他预览与孵化特性



**字符串模板（首次预览）**

字符串模板（String Templates）是 JDK 21 推出的全新语法预览特性，旨在提供更安全、更灵活的字符串拼接方式，替代传统的 `+` 拼接、`String.format`、`StringBuilder` 等方案

```Java
String name = "张三";
int age = 25;

// STR 模板处理器，直接嵌入表达式
String info = STR."姓名：\{name}，年龄：\{age}";
// 输出：姓名：张三，年龄：25

// 支持复杂表达式
String desc = STR."明年年龄：\{age + 1}";
```

<br>

**外部函数与内存 API（第三次预览）**

外部函数与内存 API（Foreign Function & Memory API）进入第三次预览，API 设计更简洁成熟，性能持续优化。它允许纯 Java 代码安全地调用原生 C 库、管理堆外内存，目标是全面替代传统 JNI，开发更简单、类型更安全、性能更高

主要面向底层框架开发、高性能计算、原生库集成场景，普通业务开发者了解即可

<br>

**Vector API（第六次孵化器）**

Vector API 进入第六次孵化，持续优化指令适配与性能表现。它利用 CPU 的 SIMD 指令实现数据并行计算，在数值计算、图像处理、密码学、大数据算法等场景可带来数倍性能提升，补齐 Java 在高性能数值计算领域的短板

