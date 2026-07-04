---
title: Flink 教程：基础入门与 DataStream API
date: 2025-04-09 18:16:05
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/preview.jpg
tags:
  - 大数据
  - Hadoop
  - Flink
categories: 大数据
---



# Flink 概述

## Flink 是什么



Flink 官网地址：[Apache Flink® — Stateful Computations over Data Streams | Apache Flink](https://flink.apache.org/)

<br>

Flink 起源于 Stratosphere 项目，Stratosphere 是在 2010~2014 年由 3 所地处柏林的大学和欧洲的一些其他的大学共同进行的研究项目，2014 年 4 月 Stratosphere 的代码被复制并捐赠给了 Apache 软件基金会，参加这个孵化项目的初始成员是 Stratosphere 系统的核心开发人员，2014 年 12 月，Flink 一 跃成为 Apache 软件基金会的顶级项目

在德语中，Flink 一词表示快速和灵巧，项目采用一只松鼠的彩色图案作为 logo，这不仅是因为松鼠具有快速和灵巧的特点，还因为柏林的松鼠有一种迷人的红棕色，而 Flink 的松鼠 logo 拥有可爱的尾巴，尾巴的颜色与 Apache 软件基金会的 logo 颜色相呼应，也就是说，这是一只 Apache 风格的松鼠

![image-20240717205828832](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240717205828832.png)

Flink 主页在其顶部展示了该项目的理念：“Apache Flink 是为分布式、高性能、随时可用以及准确的流处理应用程序打造的开源流处理框架”

Apache Flink 是一个框架和分布式处理引擎，用于对无界和有界数据流进行有状态计算。Flink 被设计在所有常见的集群环境中运行，以内存执行速度和任意规模来执行计算



------

## 有界流和无界流



无界数据流：

* 有定义流的开始，但没有定义流的结束
* 会无休止的产生数据
* 无界流的数据必须持续处理，即数据被摄取后立刻处理

<br>

有界数据流：

* 有定义流的开始，也有定义流的结束
* 可以在摄取所有数据后再进行计算
* 有界数据流可以被排序，所以不需要有序摄取
* 有界流处理通常被定义为批处理



------

## 有状态流处理



把流处理需要的额外数据保存成一个状态，然后针对这条数据进行处理，并且更新状态。这就是所谓的有状态的流处理

![image-20240718224318302](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240718224318302.png)

* 状态在内存中：优点，速度快；缺点，可靠性差
* 状态在分布式系统中：优点，可靠性高；缺点，速度慢



------

## Flink 特点



Flink 的主要特点如下：

* 高吞吐和低延迟，每秒处理数百万个事件，毫秒级延迟
* 结果的准确性，Flink 提供了事件事件（event time）和处理时间（process time）语义，对于乱序事件流，事件事件语义仍能提供一致且准确的结果
* 精确一次（exactly once）的状态一致性保证
* 可以连接到最常用的外部系统，如 Kafka、Hive、JDBC、HDFS、Redis 等
* 高可用，本身高可用的设置，加上与 K8S、Yarn 和 Mesos 等的紧密集成，再加上从故障中快速恢复和动态扩展任务的能力，Flink 至少能做到以极少的停机时间 7 x 24 全天候运行



------

## Flink vs Spark Streaming



Spark 以批处理为根本

Spark 数据模型：Spark 采用 RDD 模型，Spark Streaming 的 DStream 实际上也就是一组组小批数据 RDD 的集合

Spark 运行时架构：Spark 是批计算，将 DAG 划分为不同的 Stage，一个完成后才可以计算下一个

![image-20240719212520488](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240719212520488.png)

<br>

Flink 以流处理为根本

Flink 数据模型：Flink 基本数据类型是数据流，以及事件（Event）序列

Flink 运行时架构：Flink 是标准的流执行模式，一个事件在一个节点处理完后可以直接发往下一个节点进行处理

![image-20240719231101079](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240719231101079.png)



|              | Flink              | Spark Streaming                        |
| ------------ | ------------------ | -------------------------------------- |
| **计算模型** | 流计算             | 微批处理                               |
| **时间语义** | 事件时间、处理时间 | 处理时间                               |
| **窗口**     | 多、灵活           | 少、不灵活（窗口数必须是批次的整数倍） |
| **状态**     | 有                 | 没有                                   |
| **流式 SQL** | 有                 | 没有                                   |



------

## Flink 分层 API



![image-20240720124201587](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240720124201587.png)



有状态流处理：通过底层 API（处理函数），对最原始数据进行加工处理，底层 API 与 Data Stream API 相集成，可以处理复杂的计算

Data Stream API（流处理）和 Data Set API（批处理）封装了底层函数，提供了通用模块，比如转换、聚合、连接、窗口等操作，Flink 1.12 以后，Data Stream API 已经实现了真正的流批一体，所以 Data Set API 已经过时

Table API 是以表为中心的声明式编程，其中表可能会动态变化。Table API 遵循关系模型：表有二维数据结构，类似于关系型数据库中的表，同时 API 提供可比较的操作，如 select、join、group-by 等，我们可在再表与 Data Stream API 之间无缝切换，以允许程序将 Table API 与 Data Stream 和 Data Set 混合使用

SQL 这一层在语法和表达能力上与 Table API 类似，但是是以 SQL 查询表达式的形式表现程序，SQL 抽象与 Table API 交互紧密，同时 SQL 查询可以直接在 Table API 定义的表上执行



---

# 快速上手

## 创建项目



创建一个 Maven 项目，并引入以下依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-streaming-java</artifactId>
        <version>1.17.0</version>
    </dependency>
</dependencies>
```

<br>

然后准备一个 hello.txt 文件，内容如下：

```
hello flink
hello world
hello java
```



------

## 批处理 WordCount



```java
public class Main {
    public static void main(String[] args) throws Exception {

        // 创建执行环境
        ExecutionEnvironment environment = ExecutionEnvironment.getExecutionEnvironment();

        // 从文件读取数据，按行读取
        DataSource<String> lineDS = environment.readTextFile("Hello.txt 文件路径");

        // 转换数据格式
        // 遍历读取每一行数据，按空格拆分，并将单词转为一个元组，第一个元素是单词，但一个元素为数值 1
        FlatMapOperator<String, Tuple2<String, Long>> wordAndOne = lineDS.flatMap((FlatMapFunction<String, Tuple2<String, Long>>) (line, collector) -> {
            String[] words = line.split(" ");
            for (String word : words) {
                collector.collect(Tuple2.of(word, 1L));
            }
        });

        // 按元组的第一个元素，即 Word 进行分组
        UnsortedGrouping<Tuple2<String, Long>> wordsAndOneUG = wordAndOne.groupBy(0);

        // 将分组内的第二个元素，即数值 1 进行聚合统计
        AggregateOperator<Tuple2<String, Long>> sum = wordsAndOneUG.sum(1);

        // 打印结果
        sum.print();

    }
}
```

<br>

输出内容如下：

```
(flink,1)
(world,1)
(hello,3)
(java,1)
```

<br>

需要注意的是，这种代码的实现方式，是基于 DataSet API 的，也就是对数据的处理转换，是看作数据集来进行操作的。事实上 Flink 本身是流批统一的处理架构，批量的数据集本质上也是流，没有必要用两套不同的 API 来实现。所以从 Flink 1.12 开始，官方推荐的做法是直接使用 Data Stream API，在提交任务时通过将执行模式设为 BATCH 来进行批处理：

```sh
$ bin/flink run -Dexecution.runtime-mode=BATCH BatchWordCount.jar
```

这样，Data Set API 就没什么用了，在实际应用中我们只要维护一套 DataStream API 就可以。这里只是为了方便理解，依然用 Data Set API 做了批处理的实现



------

## 流处理 WordCount



```java
public class 流处理WordCount {
    public static void main(String[] args) throws Exception {

        // 创建执行环境
        StreamExecutionEnvironment environment = StreamExecutionEnvironment.getExecutionEnvironment();

        // 从文件读取数据，按行读取
        DataStreamSource<String> lineStream = environment.readTextFile("Hello.txt 文件路径");

        // 转换、分组、求和，得到统计结果
        SingleOutputStreamOperator<Tuple2<String, Long>> sum = lineStream.flatMap((FlatMapFunction<String, Tuple2<String, Long>>) (line, collector) -> {
            String[] words = line.split(" ");
            for (String word : words) {
                collector.collect(Tuple2.of(word, 1L));
            }
        })
                // 按元组的第一个元素，即 Word 进行分组
                .keyBy(data -> data.f0)
                // 将分组内的第二个元素，即数值 1 进行聚合统计
                .sum(1);

        // 打印结果
        sum.print();

        // 执行
        environment.execute();
    }
}
```



------

## 流处理无界数据



在实际的生产环境中，真正的数据流其实是无界的，有开始却没有结束，这就要求我们需要持续地处理捕获的数据。为了模拟这种场景，可以监听 socket 端口，然后向该端口不断的发送数据



```java
public class 流处理无界数据 {
    public static void main(String[] args) throws Exception {

        // 创建执行环境
        StreamExecutionEnvironment environment = StreamExecutionEnvironment.getExecutionEnvironment();

        // 读取文本流
        DataStreamSource<String> lineStream = environment.socketTextStream("主机名称", 端口号);

        // 转换、分组、求和，得到统计结果
        SingleOutputStreamOperator<Tuple2<String, Long>> sum = lineStream.flatMap((FlatMapFunction<String, Tuple2<String, Long>>) (line, collector) -> {
            String[] words = line.split(" ");
            for (String word : words) {
                collector.collect(Tuple2.of(word, 1L));
            }
        })
                // 按元组的第一个元素，即 Word 进行分组
                .keyBy(data -> data.f0)
                // 将分组内的第二个元素，即数值 1 进行聚合统计
                .sum(1);

        // 打印结果
        sum.print();

        // 执行
        environment.execute();
    }
}
```

<br>

程序启动之后没有任何输出、也不会退出。这是正常的，因为 Flink 的流处理是事件驱动的，当前程序会一直处于监听状态，只有接收到数据才会执行任务、输出统计结果

在主机中，输入 hello flink，输出如下内容

```
(flink,1)
(hello,1)
```

再输入 hello world ，输出如下内容

```
(world,1)
(hello,2)
```



---

# Flink 部署

## 集群角色



![image-20240721001556629](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240721001556629.png)



Flink 提交作业和执行任务，需要几个关键组件：

* 客户端（Client）：代码由客户端获取并进行转化，之后提交给 Job Manager
* Job Manager：Flink 集群的大脑，对作业进行中央调度管理，获取到作业后进一步转化，然后分发任务给众多的 Task Manager
* Task Manager：执行任务的节点



------

## 部署模式



Flink 为各种场景提供了不同的部署模式，主要有以下三种：会话模式（Session Mode）、单作业模式（Per-Job Mode）、应用模式（Application Mode）

<br>

**会话模式**

会话模式最符合常规思维，需要先启动一个集群，保持一个会话，在这个会话中通过客户端提交作业。集群启动时所有的资源都已经确定，所以所有提交的作业会竞争集群的资源

![image-20240721183110225](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240721183110225.png)

会话模式比较适用于单个规模小、执行任务短的大量作业

<br>

**单作业模式**

会话模式因为资源共享会导致很多问题，所以为了更好的隔离资源，可以考虑为每个提交的作业启动一个集群，这就是所谓的单作业模式

![image-20240721183841909](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240721183841909.png)

作业完成后，集群就会关闭，所有的资源也会得到释放

需要注意的是，Flink 本身无法直接这样运行，所以单作业模式一般需要借助一些资源管理框架来启动集群，比如 Yarn、K8S

<br>

**应用模式**

上面两种模式应用都是在客户端上执行，然后由客户端提交给 Job Manager。但这种方式客户端需要占用大量的网络带宽，去下载依赖和把二进制数据发送给 Job Manager，加上很多情况下我们提交作用用的是同一个客户端，就会加重客户端所在节点的资源消耗

解决办法就是不要客户端了，直接把应用提交到 Job Manager 上运行，这也就代表着需要为每一个提交的应用单独启动一个 Job Manager，也就是创建一个集群。这个 Job Manager 只为执行这一个应用而存在，执行结束后 Job Manager 也就关闭了，这就是应用模式

![image-20240722002152146](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240722002152146.png)

应用模式和单作业模式都是提交作业后才创建集群，单作业模式是通过客户端来提交的，客户端解析出来的每一个作业对应一个集群，而应用模式下，是直接由 Job Manager 来执行应用的

在 Flink 1.9 中，官方已经将单作业模式标记为过期模式，可以预见应用模式将成为 Flink 在生产环境使用的主要部署模式



------

## 运行模式



Flink 根据是否依赖外部资源管理平台分为两类运行模式

不依赖外部资源管理平台的运行模式称为独立模式，独立模式是独立运行的，不依赖任何外部的资源管理平台；当然独立也是有代价的：如果资源不足，或者出现故障，没有自动扩展或重分配资源的保证，必须手动处理。所以独立模式一般只用在开发测试或作业非常少的场景下，因为没有资源管理平台，所以独立模式无法部署单作业模式，但是可以部署会话模式和应用模式

也可以通过依赖外部资源管理平台来进行部署，这种运行模式下三种模式都可以部署，外部资源平台主要包括 Yarn、K8S



------

## 历史服务器



Flink 提供了历史服务器，用来在相应的 Flink 集群关闭后查询已完成作业的统计信息。只有当作业处于运行中的状态，才能够查看到相关的 WebUI 统计信息。通过 History Server 我们才能查询这些已完成作业的统计信息，无论是正常退出还是异常退出

此外，它对外提供了 REST API，它接受 HTTP 请求并使用 JSON 数据进行响应。Flink 任务停止后，JobManager 会将已经完成任务的统计信息进行存档，History Server 进程则在任务停止后可以对任务统计信息进行查询。比如：最后一次的 Checkpoint、任务运行时的相关配置



---

# 运行时架构

## 系统架构



以单机模式为例

![image-20240724015108029](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240724015108029.png)



**Job Manager**

Job Manager 是一个 Flink 集群中任务管理和调度的核心，是控制应用执行的主进程。也就是说，每个应用都应该被唯一的 Job Manager 所控制执行。Job Manger 又包含 3 个不同的组件

1. Job Master

   Job Master 是 Job Manager 中最核心的组件，负责处理单独的作业（Job）。所以 Job Master和具体的 Job 是一一对应的，多个 Job 可以同时运行在一个 Flink 集群中, 每个 Job 都有一个自己的 Job Master。需要注意在早期版本的 Flink 中，没有 Job Master 的概念；而  Job Manager 的概念范围较小，实际指的就是现在所说的 Job Master

   在作业提交时，Job Master 会先接收到要执行的应用。Job Master 会把 Job Graph 转换成一个物理层面的数据流图，这个图被叫作“执行图”，它包含了所有可以并发执行的任务。Job Master 会向资源管理器（Resource Manager）发出请求，申请执行任务必要的资源。一旦它获取到了足够的资源，就会将执行图分发到真正运行它们的 Task Manager上

   而在运行过程中 ，Job Master 会负责所有需要中央协调的操作 ，比如说检查点（checkpoints）的协调

2. 资源管理器（Resource Manager）

   Resource Manager 主要负责资源的分配和管理，在 Flink 集群中只有一个。所谓“资源”，主要是指 Task Manager 的任务槽（task slots）。任务槽就是 Flink 集群中的资源调配单元，包含了机器用来执行计算的一组 CPU 和内存资源。每一个任务（Task）都需要分配到一个 slot 上执行

3. 分发器（Dispatcher）

   Dispatcher 主要负责提供一个 REST 接口，用来提交应用，并且负责为每一个新提交的作业启动一个新的 Job Master 组件。Dispatcher 也会启动一个 Web UI，用来方便地展示和监控作业执行的信息。Dispatcher 在架构中并不是必需的，在不同的部署模式下可能会被忽略掉

<br>

**Task Manager**

Task Manager 是 Flink 中的工作进程，数据流的具体计算就是它来做的。Flink 集群中必须至少有一个 TaskManager；每一个 TaskManager 都包含了一定数量的任务槽（task slots）

Slot 是资源调度的最小单位，slot 的数量限制了 Task Manager 能够并行处理的任务数量。启动之后，Task Manager 会向资源管理器注册它的 slots；收到资源管理器的指令后，Task Manager 就会将一个或者多个槽位提供给 Job Master 调用，Job Master 就可以分配任务来执行了

在执行过程中，Task Manager 可以缓冲数据，还可以跟其他运行同一应用的 Task Manager 交换数据



------

## 核心概念之并行度



当要处理的数据量非常大时，我们可以把一个算子操作，复制多份到多个节点，数据来了之后就可以到其中任意一个执行。这样一来，一个算子任务就被拆分成了多个并行的子任务（subtasks），再将它们分发到不同节点，就真正实现了并行计算

在 Flink 执行过程中，每一个算子（operator）可以包含一个或多个子任务（operator subtask），这些子任务在不同的线程、不同的物理机或不同的容器中完全独立地执行



![image-20240728173435008](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240728173435008.png)



一个特定算子的子任务（subtask）的个数被称之为其并行度（parallelism）。这样，包含并行子任务的数据流，就是并行数据流，它需要多个分区（stream partition）来分配并行任务。一般情况下，一个流程序的并行度，可以认为就是其所有算子中最大的并行度。一个程序中，不同的算子可能具有不同的并行度

如上图所示，数据流中有 source、map、window、sink 四个算子，其中 sink 算子的并行度为 1，其他算子的并行度都为 2。所以这段流处理程序的并行度就是 2

<br>

并行度既可以在代码中设置，可以在提交应用时设置，也可以在配置文件中设置，默认并行度就是当前机器的 CPU 核心数

```JAVA
// 设置算子并行度
stream.map(word -> Tuple2.of(word, 1L)).setParallelism(2);
// 设置全局并行度
env.setParallelism(2);
```

```sh
// 提交应用时设置并行度
bin/flink run –p 2 –c com.test.SocketStreamWordCount ./FlinkTutorial-1.0-SNAPSHOT.jar
```

```
# 在 flink-conf.yaml 中配置
parallelism.default: 2
```



------

## 核心概念之算子链



一个数据流在算子之间传输数据的形式可以是一对一（one-to-one）的直通（forwarding）模式，也可以是打乱的重分区（redistributing）模式，具体是哪一种形式，取决于算子的种类

1. 一对一（One-to-one，forwarding）

   这种模式下，数据流维护着分区以及元素的顺序。比如 source 和 map 算子，source 算子读取数据之后，可以直接发送给 map 算子做处理，它们之间不需要重新分区，也不需要调整数据的顺序。这就意味着 map 算子的子任务，看到的元素个数和顺序跟 source 算子的子任务产生的完全一样，保证着一对一的关系。map、filter、flatMap 等算子都是这种 one-to-one 的对应关系。这种关系类似于 Spark 中的窄依赖

2. 重分区（Redistributing）

   在这种模式下，数据流的分区会发生改变。比如 map 和 keyBy/window 算子之间，以及 keyBy/window 算子和 Sink 算子之间，都是这样的关系。每一个算子的子任务，会根据数据传输的策略，把数据发送到不同的下游目标任务。这些传输方式都会引起重分区的过程，这一过程类似于 Spark 中的 shuffle

<br>

在 Flink 中，并行度相同的一对一（one to one）算子操作，可以直接链接在一起形成一个大的任务（task），这样原来的算子就成为了真正任务里的一部分。每个 task 会被一个线程执行。这样的技术被称为算子链（Operator Chain）

Flink 默认会按照算子链的原则进行链接合并，如果我们想要禁止合并或者自行定义，也可以在代码中对算子做一些特定的设置：

```JAVA
// 禁用算子链
.map(word -> Tuple2.of(word, 1L)).disableChaining();
// 从当前算子开始新链
.map(word -> Tuple2.of(word, 1L)).startNewChain();
```



------

## 核心概念之任务槽



Flink 中每一个 TaskManager 都是一个 JVM 进程，它可以启动多个独立的线程，来并行执行多个子任务（subtask）。但是，TaskManager 的计算资源是有限的，并行的任务越多，每个线程的资源就会越少，为了控制并发量，需要在 TaskManager 上对每个任务运行所占用的资源做出明确的划分，这就是所谓的任务槽（taskslots）。每个任务槽（task slot）其实表示了 TaskManager 拥有计算资源的一个固定大小的子集，这些资源就是用来独立执行一个子任务的

<br>

在 Flink 的 flink-conf.yaml 配置文件中 ， 可以设置 TaskManager 的 slot 数量，默认是 1 个 slot

```
taskmanager.numberOfTaskSlots: 8
```

需要注意的是，slot 目前仅仅用来隔离内存，不会涉及 CPU 的隔离。在具体应用时，可以将 slot 数量配置为机器的 CPU 核心数，尽量避免不同任务之间对 CPU 的竞争。这也是开发环境默认并行度设为机器 CPU 数量的原因

<br>

任务槽和并行度都跟程序的并行执行有关，但两者是完全不同的概念。简单来说任务槽是静态的概念 ， 是指 TaskManager 具有的并发执行能力 ，可以通过参 数 taskmanager.numberOfTaskSlots 进行配置；而并行度是动态概念，也就是 TaskManager 运行程序时实际使用的并发能力，可以通过参数 parallelism.default 进行配置



------

## 独立模式会话作业提交流程



![image-20240802231920769](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240802231920769.png)



------

## Yarn 模式会话作业提交流程



![image-20240802231953019](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240802231953019.png)



---

# Data Stream API



DataStream API 是 Flink 的核心层 API。一个 Flink 程序，其实就是对 DataStream 的各种转换。具体来说，代码基本上都由以下几部分构成：

![image-20240803000313141](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240803000313141.png)



------

## 执行环境



在代码中创建执行环境的方式，就是调用这个类的静态方法，具体有以下三种：

```JAVA
// 最简单的方式，会根据当前运行的上下文直接得到正确的结果,是最常用的一种创建执行环境的方式
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

// 这个方法返回一个本地执行环境。可以在调用时传入一个参数，指定默认的并行度；如果不传入，则默认并行度就是本地的 CPU 核心数
StreamExecutionEnvironment localEnv = StreamExecutionEnvironment.createLocalEnvironment();

// 这个方法返回集群执行环境。需要在调用时指定 JobManager 的主机名和端口号，并指定要在集群中运行的 Jar 包
StreamExecutionEnvironment remoteEnv = StreamExecutionEnvironment.createRemoteEnvironment(
	"host", // JobManager 主机名 
	1234, // JobManager 进程端口号
	"path/to/jarFile.jar" // 提交给 JobManager 的 JAR 包
);
```

<br>

从 Flink 1.12 开始，官方推荐的做法是直接使用 DataStream API，在提交任务时通过将执行模式设为 BATCH 来进行批处理。不建议使用 DataSet API

```JAVA
// 流处理环境
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
```

DataStream API 执行模式包括：流执行模式、批执行模式和自动模式

<br>

批执行模式的使用。主要有两种方式：

```SH
# 通过命令行配置
bin/flink run -Dexecution.runtime-mode=BATCH ...
```

```JAVA
// 通过代码配置
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
env.setRuntimeMode(RuntimeExecutionMode.BATCH);
```

<br>

需要注意的是，写完输出（sink）操作并不代表程序已经结束。因为当 main() 方法被调用时，其实只是定义了作业的每个执行操作，然后添加到数据流图中；这时并没有真正处理数据——因为数据可能还没来。Flink 是由事件驱动的，只有等到数据到来，才会触发真正的计算，这也被称为“延迟执行”或“懒执行”。所以我们需要显式地调用执行环境的 execute() 方法，来触发程序执行。execute() 方法将一直等待作业完成，然后返回一个执行结果（JobExecutionResult）

```JAVA
env.execute();
```



------

## 源算子



Flink 可以从各种来源获取数据，然后构建 DataStream 进行转换处理。一般将数据的输入来源称为数据源（data source），而读取数据的算子就是源算子（source operator）。所以，source 就是我们整个处理程序的输入端

![image-20240803003611068](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240803003611068.png)

<br>

**从集合读取数据**

最简单的读取数据的方式，就是在代码中直接创建一个 Java 集合，然后调用执行环境的 fromCollection 方法进行读取。这相当于将数据临时存储到内存中，形成特殊的数据结构后，作为数据源使用，一般用于测试

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	List<Integer> data = Arrays.asList(1, 22, 3);
	DataStreamSource<Integer> ds = env.fromCollection(data);
	stream.print();
	env.execute();
}
```

<br>

**从文件读取数据**

真正的实际应用中，自然不会直接将数据写在代码中。通常情况下，我们会从存储介质中获取数据，一个比较常见的方式就是读取日志文件。这也是批处理中最常见的读取方式

```JAVA
<dependency>
	<groupId>org.apache.flink</groupId>
	<artifactId>flink-connector-files</artifactId>
	<version>${flink.version}</version>
</dependency>
```

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	FileSource<String> fileSource = FileSource
        .forRecordStreamFormat(new TextLineInputFormat(), new Path("input/word.txt")).build();
	env.fromSource(fileSource,WatermarkStrategy.noWatermarks(),"file").print();
	env.execute();
}
```

<br>

**从 Socket 读取数据**

不论从集合还是文件，我们读取的其实都是有界数据。在流处理的场景中，数据往往是无界的

之前用到的读取 socket 文本流，就是流处理场景。但是这种方式由于吞吐量小、稳定性较差，一般也是用于测试

```JAVA
DataStream<String> stream = env.socketTextStream("localhost", 7777);
```

<br>

**从 Kafka 读取数据**

Flink 官方提供了连接工具 flink-connector-kafka ， 直接帮我们实现了一个消费者 FlinkKafkaConsumer，它就是用来读取 Kafka 数据的 SourceFunction

```xml
<dependency>
	<groupId>org.apache.flink</groupId>
	<artifactId>flink-connector-kafka</artifactId>
	<version>${flink.version}</version>
</dependency>
```

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	KafkaSource<String> kafkaSource = KafkaSource.<String>builder()
        .setBootstrapServers("server01:9092")
        .setTopics("topic_1")
        .setGroupId("test")
        .setStartingOffsets(OffsetsInitializer.latest())
        .setValueOnlyDeserializer(new SimpleStringSchema())
        .build();
	DataStreamSource<String> stream = env.fromSource(kafkaSource, WatermarkStrategy.noWatermarks(), "kafka-source");
	stream.print("Kafka");
	env.execute();
}
```



------

## 转换算子之基本转换算子



数据源读入数据之后，我们就可以使用各种转换算子，将一个或多个 DataStream 转换为新的 DataStream

![image-20240804191334079](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240804191334079.png)

<br>

**映射（map）**

map 主要用于将数据流中的数据进行转换，形成新的数据流。简单来说，就是一个“一一映射”，消费一个元素就产出一个元素

只需要基于 DataStream 调用 map()方法就可以进行转换处理。方法需要传入的参数是接口 MapFunction 的实现；返回值类型还是 DataStream，不过泛型（流中的元素类型）可能改变

下面代码用两种方式实现了提取 WaterSensor 中的 id 字段的功能

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	DataStreamSource<WaterSensor> stream = env.fromElements(
        new WaterSensor("sensor_1", 1, 1),
		new WaterSensor("sensor_2", 2, 2)
	);
    
	// 方式一：传入匿名类，实现 MapFunction
	stream.map(new MapFunction<WaterSensor, String>() {
		@Override
		public String map(WaterSensor e) throws Exception {
			return e.id;
		}
	}).print();
    
	// 方式二：传入 MapFunction 的实现类
	// stream.map(new UserMap()).print();
	env.execute();
}

public static class UserMap implements MapFunction<WaterSensor, String> {
	@Override
	public String map(WaterSensor e) throws Exception {
		return e.id;
	}
}
```

<br>

**过滤（filter）**

filter 转换操作，顾名思义是对数据流执行一个过滤，通过一个布尔条件表达式设置过滤条件，对于每一个流内元素进行判断，若为 true 则元素正常输出，若为 false 则元素被过滤掉

进行 filter 转换之后的新数据流的数据类型与原数据流是相同的。filter 转换需要传入的参数需要实现 FilterFunction 接口，而 FilterFunction 内要实现 filter()方法，就相当于一个返回布尔类型的条件表达式

下面的代码会将数据流中传感器 id 为 sensor_1 的数据过滤出来

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	DataStreamSource<WaterSensor> stream = env.fromElements(
		new WaterSensor("sensor_1", 1, 1),
		new WaterSensor("sensor_1", 2, 2),
		new WaterSensor("sensor_2", 2, 2),
		new WaterSensor("sensor_3", 3, 3)
	);
    
	// 方式一：传入匿名类实现 FilterFunction
	stream.filter(new FilterFunction<WaterSensor>() {
		@Override
		public boolean filter(WaterSensor e) throws Exception {
			return e.id.equals("sensor_1");
		}
	}).print();

    // 方式二：传入 FilterFunction 实现类
	// stream.filter(new UserFilter()).print();
	env.execute();
}

public static class UserFilter implements FilterFunction<WaterSensor> {
	@Override
	public boolean filter(WaterSensor e) throws Exception {
		return e.id.equals("sensor_1");
	}
}
```

<br>

**扁平映射（flatMap）**

flatMap 操作又称为扁平映射，主要是将数据流中的整体（一般是集合类型）拆分成一个一个的个体使用。消费一个元素，可以产生 0 到多个元素。flatMap 可以认为是“扁平化”（flatten）和“映射”（map）两步操作的结合，也就是先按照某种规则对数据进行打散拆分，再对拆分后的元素做转换处理

同 map 一样，flatMap 也可以使用 Lambda 表达式或者 FlatMapFunction 接口实现类的方式来进行传参，返回值类型取决于所传参数的具体逻辑，可以与原数据流相同，也可以不同

如果输入的数据是 sensor_1，只打印 vc；如果输入的数据是 sensor_2，既打印 ts 又打印 vc

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	DataStreamSource<WaterSensor> stream = env.fromElements(
		new WaterSensor("sensor_1", 1, 1),
		new WaterSensor("sensor_1", 2, 2),
		new WaterSensor("sensor_2", 2, 2),
		new WaterSensor("sensor_3", 3, 3)
	);
    
	stream.flatMap(new MyFlatMap()).print();
	env.execute();
}

public static class MyFlatMap implements FlatMapFunction<WaterSensor, String> {
	@Override
	public void flatMap(WaterSensor value, Collector<String> out) throws Exception {
		if (value.id.equals("sensor_1")) {
			out.collect(String.valueOf(value.vc));
		} else if (value.id.equals("sensor_2")) {
			out.collect(String.valueOf(value.ts));
			out.collect(String.valueOf(value.vc));
		}
	}
}
```



------

## 转换算子之聚合算子



**按键分区（keyBy）**

对于 Flink 而言，DataStream 是没有直接进行聚合的 API 的。因为我们对海量数据做聚合肯定要进行分区并行处理，这样才能提高效率。所以在 Flink 中，要做聚合，需要先进行分区；这个操作就是通过 keyBy 来完成的

keyBy 是聚合前必须要用到的一个算子。keyBy 通过指定键（key），可以将一条流从逻辑上划分成不同的分区（partitions）。这里所说的分区，其实就是并行处理的子任务

基于不同的 key，流中的数据将被分配到不同的分区中去；这样一来，所有具有相同的 key 的数据，都将被发往同一个分区

![image-20240807235334422](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240807235334422.png)

在内部，是通过计算 key 的哈希值（hash code），对分区数进行取模运算来实现的。所以这里 key 如果是 POJO 的话，必须要重写 hashCode() 方法

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	DataStreamSource<WaterSensor> stream = env.fromElements(
		new WaterSensor("sensor_1", 1, 1),
		new WaterSensor("sensor_1", 2, 2),
		new WaterSensor("sensor_2", 2, 2),
		new WaterSensor("sensor_3", 3, 3)
	);
    
	// 方式一：使用 Lambda 表达式
	KeyedStream<WaterSensor, String> keyedStream = stream.keyBy(e -> e.id);
    
	// 方式二：使用匿名类实现 KeySelector
	KeyedStream<WaterSensor, String> keyedStream1 =
	stream.keyBy(new KeySelector<WaterSensor, String>() {
		@Override
		public String getKey(WaterSensor e) throws Exception {
			return e.id;
		}
	});
	env.execute();
}
```

<br>

需要注意的是，keyBy 得到的结果将不再是 DataStream，而是会将 DataStream 转换为 KeyedStream。KeyedStream 可以认为是“分区流”或者“键控流”，它是对 DataStream 按照 key 的一个逻辑分区，所以泛型有两个类型：除去当前流中的元素类型外，还需要指定 key的类型

KeyedStream 也继承自 DataStream，所以基于它的操作也都归属于 DataStream API。但它跟之前的转换操作得到的 SingleOutputStreamOperator 不同，只是一个流的分区操作，并不是一个转换算子。KeyedStream 是一个非常重要的数据结构，只有基于它才可以做后续的聚合操作（比如 sum，reduce）

<br>

**简单聚合（sum/min/max/minBy/maxBy）**

sum()：在输入流上，对指定的字段做叠加求和的操作

min()：在输入流上，对指定的字段求最小值

max()：在输入流上，对指定的字段求最大值

minBy()：与 min() 类似，在输入流上针对指定字段求最小值。不同的是，min()只计算指定字段的最小值，其他字段会保留最初第一个数据的值；而 minBy()则返回包含字段最小值的整条数据

maxBy()：与 max() 类似 ，在 输入流上针对指定字段求最大值,两者区别与min()/minBy()完全一致

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	DataStreamSource<WaterSensor> stream = env.fromElements(
		new WaterSensor("sensor_1", 1, 1),
		new WaterSensor("sensor_1", 2, 2),
		new WaterSensor("sensor_2", 2, 2),
		new WaterSensor("sensor_3", 3, 3)
	);
	stream.keyBy(e -> e.id).max("vc"); // 指定字段名称
	env.execute();
}
```

一个聚合算子，会为每一个 key 保存一个聚合的值，在 Flink 中我们把它叫作“状态”（state）。所以每当有一个新的数据输入，算子就会更新保存的聚合结果，并发送一个带有更新后聚合值的事件到下游算子。对于无界流来说，这些状态是永远不会被清除的，所以我们使用聚合算子，应该只用在含有有限个 key 的数据流上

<br>

**归约聚合（reduce）**

reduce 可以对已有的数据进行归约处理，把每一个新输入的数据和当前已经归约出来的值，再做一个聚合计算。reduce 操作也会将 KeyedStream 转换为 DataStream。它不会改变流的元素数据类型，所以输出类型和输入类型是一样的

调用 KeyedStream 的 reduce 方法时，需要传入一个参数，实现 ReduceFunction 接口。接口在源码中的定义如下：

```JAVA
public interface ReduceFunction<T> extends Function, Serializable {
	T reduce(T value1, T value2) throws Exception;
}
```

ReduceFunction 接口里需要实现 reduce()方法，这个方法接收两个输入事件，经过转换处理之后输出一个相同类型的事件。在流处理的底层实现过程中，实际上是将中间“合并的结果”作为任务的一个状态保存起来的；之后每来一个新的数据，就和之前的聚合状态进一步做归约

```java
// 使用 reduce 实现 max 和 maxBy 的功能
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	env.socketTextStream("hadoop102", 7777)
        .map(new WaterSensorMapFunction())
        .keyBy(WaterSensor::getId)
        .reduce(new ReduceFunction<WaterSensor>() {
            @Override
            public WaterSensor reduce(WaterSensor value1, WaterSensor value2) throws Exception {
                System.out.println("Demo7_Reduce.reduce");
                int maxVc = Math.max(value1.getVc(), value2.getVc());
                //实现 max(vc)的效果 取最大值，其他字段以当前组的第一个为主
                //value1.setVc(maxVc);
                //实现 maxBy(vc)的效果 取当前最大值的所有字段
                if (value1.getVc() > value2.getVc()){
                    value1.setVc(maxVc);
                    return value1;
                }else {
                    value2.setVc(maxVc);
                    return value2;
                }
            }
        })
        .print();
env.execute();
```



------

## 转换算子之用户自定义函数



用户自定义函数（user-defined function，UDF），即用户可以根据自身需求，重新实现算子的逻辑。用户自定义函数分为：函数类、匿名函数、富函数类

<br>

**函数类**

Flink 暴露了所有 UDF 函数的接口，具体实现方式为接口或者抽象类，例如 MapFunction、FilterFunction、ReduceFunction 等。所以用户可以自定义一个函数类，实现对应的接口

使用 FilterFunction 接口来从用户的点击数据中筛选包含 sensor_1 的内容

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	DataStreamSource<WaterSensor> stream = env.fromElements(
        new WaterSensor("sensor_1", 1, 1),
        new WaterSensor("sensor_1", 2, 2),
        new WaterSensor("sensor_2", 2, 2),
        new WaterSensor("sensor_3", 3, 3)
    );
	//map 函数使用 Lambda 表达式，不需要进行类型声明
    SingleOutputStreamOperator<String> filter = stream.filter(sensor -> "sensor_1".equals(sensor.id));
    filter.print();
    env.execute();
}
```

<br>

**富函数类**

富函数类也是 DataStream API 提供的一个函数类的接口，所有的 Flink 函数类都有其 Rich 版 本 。 富函数类一般是以抽象类的形式出现的。例如：RichMapFunction 、RichFilterFunction、RichReduceFunction 等。与常规函数类的不同主要在于，富函数类可以获取运行环境的上下文，并拥有一些生命周期方法，所以可以实现更复杂的功能

Rich Function 有生命周期的概念。典型的生命周期方法有：

* open() 方法，是 Rich Function 的初始化方法，也就是会开启一个算子的生命周期。当一个算子的实际工作方法例如 map() 或者 filter() 方法被调用之前，open() 会首先被调用
* close() 方法，是生命周期中的最后一个调用的方法，类似于结束方法。一般用来做一些清理工作

需要注意的是，这里的生命周期方法，对于一个并行子任务来说只会调用一次；而对应的，实际工作方法，例如 RichMapFunction 中的 map()，在每条数据到来后都会触发一次调用

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	env.setParallelism(2);
	env.fromElements(1,2,3,4)
        .map(new RichMapFunction<Integer, Integer>() {
            @Override
            public void open(Configuration parameters) throws Exception {
                super.open(parameters);
                System.out.println(" 索 引 是 ： " + getRuntimeContext().getIndexOfThisSubtask() + " 的任务的生命周期开始");
            }
            
            @Override
            public Integer map(Integer integer) throws Exception {
                return integer + 1;
            }
            
            @Override
            public void close() throws Exception {
                super.close();
                System.out.println(" 索 引 是 ： " + getRuntimeContext().getIndexOfThisSubtask() + " 的任务的生命周期结束");
			}
        })
        .print();
    env.execute();
}
```



------

## 转换算子之物理分区算子



常见的物理分区策略有：随机分配（Random）、轮询分配（Round-Robin）、重缩放（Rescale）和广播（Broadcast）

<br>

**随机分区（shuffle）**

最简单的重分区方式就是直接“洗牌”。通过调用 DataStream 的.shuffle()方法，将数据随机地分配到下游算子的并行任务中去

随机分区服从均匀分布（uniform distribution），所以可以把流中的数据随机打乱，均匀地传递到下游任务分区。因为是完全随机的，所以对于同样的输入数据, 每次执行得到的结果也不会相同

![image-20240901182515124](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240901182515124.png)

```JAVA
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	env.setParallelism(2);
	DataStreamSource<Integer> stream = env.socketTextStream("hadoop", 7777);;
	stream.shuffle().print()
	env.execute();
}
```

<br>

**轮询分区（Round-Robin）**

轮询，简单来说就是“发牌”，按照先后顺序将数据做依次分发。通过调用 DataStream的.rebalance()方法，就可以实现轮询重分区。rebalance 使用的是 Round-Robin 负载均衡算法，可以将输入流数据平均分配到下游的并行任务中去

![image-20240901182711149](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240901182711149.png)

```JAVA
stream.rebalance()
```

<br>

**重缩放分区（rescale）**

重缩放分区和轮询分区非常相似。当调用 rescale() 方法时，其实底层也是使用 Round- Robin 算法进行轮询，但是只会将数据轮询发送到下游并行任务的一部分中。rescale 的做法是分成小团体，发牌人只给自己团体内的所有人轮流发牌

![image-20240901182848757](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240901182848757.png)

```JAVA
stream.rescale()
```

<br>

**广播（broadcast）**

这种方式其实不应该叫做“重分区”，因为经过广播之后，数据会在不同的分区都保留一份，可能进行重复处理。可以通过调用 DataStream 的 broadcast() 方法，将输入数据复制并发送到下游算子的所有并行任务中去

```JAVA
stream.broadcast()
```

<br>

**全局分区（global）**

全局分区也是一种特殊的分区方式。这种做法非常极端，通过调用 .global() 方法，会将所有的输入流数据都发送到下游算子的第一个并行子任务中去。这就相当于强行让下游任务并行度变成了 1，所以使用这个操作需要非常谨慎，可能对程序造成很大的压力

```JAVA
stream.global()
```

<br>

**自定义分区（Custom）**

当 Flink 提供的所有分区策略都不能满足用户的需求时，可以通过使用 partitionCustom() 方法来自定义分区策略

```JAVA
// 自定义分区
public class MyPartitioner implements Partitioner<String> {
	@Override
	public int partition(String key, int numPartitions) {
        return Integer.parseInt(key) % numPartitions;
    }
}

// 使用自定义分区
public static void main(String[] args) throws Exception {
	StreamExecutionEnvironment env = StreamExecutionEnvironment.createLocalEnvironmentWithWebUI(new Configuration());
    env.setParallelism(2);
    DataStreamSource<String> socketDS = env.socketTextStream("hadoop", 7777);
	DataStream<String> myDS = socketDS.partitionCustom(new MyPartitioner(), value -> value);
    myDS.print();
    env.execute();
}
```



------

## 转换算子之分流



所谓“分流”，就是将一条数据流拆分成完全独立的两条、甚至多条流。也就是基于一个 DataStream，定义一些筛选条件，将符合条件的数据拣选出来放到对应的流里

![image-20240901224400618](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240901224400618.png)



其实根据条件筛选数据的需求，本身非常容易实现：只要针对同一条流多次独立调用 .filter() 方法进行筛选，就可以得到拆分之后的流了

这种实现非常简单，但代码显得有些冗余——处理逻辑对拆分出的三条流其实是一样的，却重复写了三次。而且这段代码背后的含义，是将原始数据流 stream 复制三份，然后对每一份分别做筛选；这明显是不够高效的。能不能不用复制流，直接用一个算子就把它们都拆分开呢

简单来说，只需要调用上下文 ctx 的.output() 方法，就可以输出任意类型的数据了。而侧输出流的标记和提取，都离不开一个“输出标签”（OutputTag），指定了侧输出流的 id 和类型

<br>

例如：将 WaterSensor 按照 Id 类型进行分流

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    SingleOutputStreamOperator<WaterSensor> ds = env.socketTextStream("hadoop", 7777).map(new WaterSensorMapFunction());
	OutputTag<WaterSensor> s1 = new OutputTag<>("s1", Types.POJO(WaterSensor.class)){};
	OutputTag<WaterSensor> s2 = new OutputTag<>("s2", Types.POJO(WaterSensor.class)){};
    
    //返回的都是主流
    SingleOutputStreamOperator<WaterSensor> ds1 = ds.process(new ProcessFunction<WaterSensor, WaterSensor>() {
        @Override
        public void processElement(WaterSensor value, Context ctx, Collector<WaterSensor> out) throws Exception {
            if ("s1".equals(value.getId())) {
                ctx.output(s1, value);
            } else if ("s2".equals(value.getId())) {
                ctx.output(s2, value);
            } else {
                //主流
                out.collect(value);
            }}
    });
    ds1.print("主流，非 s1,s2 的传感器");
    SideOutputDataStream<WaterSensor> s1DS = ds1.getSideOutput(s1);
    SideOutputDataStream<WaterSensor> s2DS = ds1.getSideOutput(s2);
    s1DS.printToErr("s1");
    s2DS.printToErr("s2");
    env.execute();
}
```



------

## 转换算子之合流



**联合（Union）**

最简单的合流操作，就是直接将多条流合在一起，叫作流的“联合”（union）。联合操作要求必须流中的数据类型必须相同，合并之后的新流会包括所有流中的元素，数据类型不变

![image-20240901235909843](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240901235909843.png)

```JAVA
stream1.union(stream2, stream3, ...)
```



<br>

**连接（Connect）**

流的联合虽然简单，不过受限于数据类型不能改变，灵活性大打折扣，所以实际应用较少出现。除了联合（union），Flink 还提供了另外一种方便的合流操作——连接（connect）

![image-20240902000043740](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240902000043740.png)

需要分为两步：首先基于一条 DataStream 调用 .connect() 方法，传入另外一条 DataStream 作为参数，将两条流连接起来，得到一个 ConnectedStreams；然后再调用同处理方法得到 DataStream。这里可以的调用的同处理方法有 .map()/.flatMap()，以及 .process() 方法

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<Integer> source1 = env.socketTextStream("hadoop102", 7777)
        .map(i -> Integer.parseInt(i));
    DataStreamSource<String> source2 = env.socketTextStream("hadoop102", 8888);

    /**
    * TODO 使用 connect 合流
    * 1、一次只能连接 2 条流
    * 2、流的数据类型可以不一样
    * 3、 连接后可以调用 map、flatmap、process 来处理，但是各处理各的
    */
	ConnectedStreams<Integer, String> connect = source1.connect(source2);
    SingleOutputStreamOperator<String> result = connect.map(new CoMapFunction<Integer, String, String>() {
        @Override
        public String map1(Integer value) throws Exception {
            return "来源于数字流:" + value.toString();
        }
        @Override
        public String map2(String value) throws Exception {
            return "来源于字母流:" + value;
        }
    });
    result.print();
    env.execute(); 
}
```



------

## 输出算子



Flink 作为数据处理框架，最终还是要把计算处理的结果写入外部存储，为外部应用提供支持

![image-20240902000550240](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240902000550240.png)

Flink 官方为我们提供了一部分的框架的 Sink 连接器。如下图所示，列出了 Flink 官方目前支持的第三方系统连接器

![image-20240902000618568](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240902000618568.png)

除 Flink 官方之外，Apache Bahir 框架，也实现了一些其他第三方系统与 Flink 的连接器

![image-20240902000639238](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E4%B8%8E%20DataStream%20API/image-20240902000639238.png)

除此以外，就需要用户自定义实现 sink 连接器了

<br>

**输出到文件**

Flink 专门提供了一个流式文件系统的连接器：FileSink，为批处理和流处理提供了一个统一的 Sink，它可以将分区文件写入 Flink 支持的文件系统。FileSink 支持行编码（Row-encoded）和批量编码（Bulk-encoded）格式。这两种不同的方式都有各自的构建器（builder），可以直接调用 FileSink 的静态方法：

* 行编码： FileSink.forRowFormat（basePath，rowEncoder）
* 批量编码： FileSink.forBulkFormat（basePath，bulkWriterFactory）

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
	// 每个目录中，都有并行度个数的文件在写入
	env.setParallelism(2);
    // 必须开启 checkpoint，否则一直都是 .inprogress
    env.enableCheckpointing(2000, CheckpointingMode.EXACTLY_ONCE);
	DataGeneratorSource<String> dataGeneratorSource = new DataGeneratorSource<>(
        new GeneratorFunction<Long, String>() {
            @Override
            public String map(Long value) throws Exception {
                return "Number:" + value;
            }
        }, 
        Long.MAX_VALUE,
        RateLimiterStrategy.perSecond(1000),
        Types.STRING);
    DataStreamSource<String> dataGen = env.fromSource(dataGeneratorSource, WatermarkStrategy.noWatermarks(), "data-generator");

    // 输出到文件系统
    FileSink<String> fieSink = FileSink
        // 输出行式存储的文件，指定路径、指定编码
        .<String>forRowFormat(new Path("f:/tmp"), new SimpleStringEncoder<>("UTF-8"))
        // 输出文件的一些配置： 文件名的前缀、后缀
        .withOutputFileConfig( OutputFileConfig
                              .builder()
                              .withPartPrefix("atguigu-")
                              .withPartSuffix(".log")
                              .build())
        // 按照目录分桶：如下，就是每个小时一个目录
        .withBucketAssigner(new DateTimeBucketAssigner<>("yyyy-MM-dd HH", ZoneId.systemDefault()))
        // 文件滚动策略: 1 分钟 或 1m
        .withRollingPolicy( DefaultRollingPolicy.builder()
                           .withRolloverInterval(Duration.ofMinutes(1))
                           .withMaxPartSize(new MemorySize(1024*1024))
                           .build())
        .build();

    dataGen.sinkTo(fieSink);
    env.execute();
}
```

<br>

**输出到 Kafka**

输出无 key 的 record，如果要输出有 key 的 record，则需要自定义序列化器

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    // 如果是精准一次，必须开启 checkpoint（后续介绍）
    env.enableCheckpointing(2000, CheckpointingMode.EXACTLY_ONCE);
    SingleOutputStreamOperator<String> sensorDS = env.socketTextStream("hadoop102", 7777);
	/**
	* Kafka Sink:
	* TODO 注意：如果要使用精准一次 写入 Kafka，需要满足以下条件，缺一不可
	* 1、开启 checkpoint（后续介绍）
	* 2、设置事务前缀
	* 3、设置事务超时时间： checkpoint 间隔 < 事务超时时间 < max 的 15 分钟
	*/
	KafkaSink<String> kafkaSink = KafkaSink.<String>builder()
        // 指定 kafka 的地址和端口
        .setBootstrapServers("hadoop102:9092,hadoop103:9092, hadoop104:9092")
        // 指定序列化器：指定 Topic 名称、具体的序列化
        .setRecordSerializer( KafkaRecordSerializationSchema.<String>builder()
                             .setTopic("ws")
                             .setValueSerializationSchema(new SimpleStringSchema())
                             .build())
        // 写到 kafka 的一致性级别： 精准一次、至少一次
        .setDeliveryGuarantee(DeliveryGuarantee.EXACTLY_ONCE)
        // 如果是精准一次，必须设置 事务的前缀
        .setTransactionalIdPrefix("atguigu-")
        // 如果是精准一次，必须设置 事务超时时间: 大于 checkpoint间隔，小于 max 15 分钟
        .setProperty(ProducerConfig.TRANSACTION_TIMEOUT_CONFIG, 10*60*1000+"")
        .build();
    sensorDS.sinkTo(kafkaSink);
    env.execute();
}
```

<br>

**输出到 MySQL**

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<WaterSensor> sensorDS = env.socketTextStream("hadoop102", 7777)
        .map(new WaterSensorMapFunction());
	/**
	* TODO 写入 mysql
	* 1、只能用老的 sink 写法： addsink
	* 2、JDBCSink 的 4 个参数:
	* 第一个参数： 执行的 sql，一般就是 insert into
	* 第二个参数： 预编译 sql， 对占位符填充值
	* 第三个参数： 执行选项 ---》 攒批、重试
	* 第四个参数： 连接选项 ---》 url、用户名、密码
	*/
    SinkFunction<WaterSensor> jdbcSink = JdbcSink
        .sink("insert into ws values(?,?,?)", new JdbcStatementBuilder<WaterSensor>() {
            @Override
            public void accept(PreparedStatement preparedStatement, WaterSensor waterSensor) throws SQLException {
                //每收到一条 WaterSensor，如何去填充占位符
                preparedStatement.setString(1, waterSensor.getId());
                preparedStatement.setLong(2, waterSensor.getTs());
                preparedStatement.setInt(3, waterSensor.getVc());
            }
        },
              JdbcExecutionOptions
              .builder()
              .withMaxRetries(3) // 重试次数
              .withBatchSize(100) // 批次的大小：条数
              .withBatchIntervalMs(3000) // 批次的时间
              .build(),
              new JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
              .withUrl("jdbc:mysql://hadoop102:3306/test? serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-
8")
             .withUsername("root")
             .withPassword("000000")
             .withConnectionCheckTimeoutSeconds(60) //重试的超时时间
             .build());
              sensorDS.addSink(jdbcSink);
              env.execute();
}
```

<br>

**自定义 Sink 输出**

如果想将数据存储到自己的存储设备中，而 Flink 并没有提供可以直接使用的连接器，就只能自定义 Sink 进行输出了。与 Source 类似，Flink 提供了通用的SinkFunction 接口和对应的 RichSinkDunction 抽象类，只要实现它，通过简单地调用 DataStream 的.addSink() 方法就可以自定义写入任何外部存储

在实现 SinkFunction 的时候，需要重写的一个关键方法 invoke()，在这个方法中就可以实现将流里的数据发送出去的逻辑

这种方式比较通用，对于任何外部存储系统都有效；不过自定义 Sink 想要实现状态一致性并不容易，所以一般只在没有其它选择时使用。实际项目中用到的外部连接器 Flink 官方基本都已实现，而且在不断地扩充，因此自定义的场景并不常见
