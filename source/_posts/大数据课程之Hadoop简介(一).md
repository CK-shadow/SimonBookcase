---
title: 大数据课程之 Hadoop 简介(一)
date: 2022-05-22 00:26:38
tags: 大数据
categories: 
  - 大数据
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHadoop%E7%AE%80%E4%BB%8B%28%E4%B8%80%29/image-20220522001934300.png
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHadoop%E7%AE%80%E4%BB%8B%28%E4%B8%80%29/image-20220522001934300.png
---

### 大数据课程之 Hadoop 简介(一)

#### 大数据的前世今生



Google 在 2004 年前后发表的三篇论文，也就是我们经常听到的“三驾马车”，分别是分布式文件系统 GFS、大数据分布式计算框架 MapReduce 和 NoSQL 数据库系统 BigTable

当时的天才程序员，也是 Lucene 开源项目的创始人 Doug Cutting 正在开发开源搜索引擎 Nutch，阅读了 Google 的论文后，他非常兴奋，紧接着就根据论文原理初步实现了类似 GFS 和 MapReduce 的功能。Doug Cutting 将这些大数据相关的功能从 Nutch 中分离了出来，然后启动了一个独立的项目专门开发维护大数据技术，这就是后来赫赫有名的 Hadoop，主要包括 Hadoop 分布式文件系统 HDFS 和大数据计算引擎 MapReduce

Hadoop 发布之后，Yahoo 很快就用了起来。这个时候，Yahoo 的一些人觉得用  MapReduce 进行大数据编程太麻烦了，于是便开发了 Pig。Pig 是一种脚本语言，使用类 SQL 的语法，开发者可以用 Pig  脚本描述要对大数据集上进行的操作，Pig 经过编译后会生成 MapReduce 程序，然后在 Hadoop 上运行

编写 Pig 脚本虽然比直接 MapReduce 编程容易，但是依然需要学习新的脚本语法。于是 Facebook 又发布了 Hive。Hive 支持使用 SQL  语法来进行大数据计算，比如说你可以写个 Select 语句进行数据查询，然后 Hive 会把 SQL 语句转化成 MapReduce 的计算程序

随后，众多 Hadoop 周边产品开始出现，大数据生态体系逐渐形成，其中包括：专门将关系数据库中的数据导入导出到 Hadoop 平台的 Sqoop；针对大规模日志进行分布式收集、聚合和传输的 Flume；MapReduce 工作流调度引擎 Oozie 等

在 Hadoop 早期，MapReduce  既是一个执行引擎，又是一个资源调度框架，服务器集群的资源调度管理由 MapReduce 自己完成。但是这样不利于资源复用，也使得  MapReduce 非常臃肿。于是一个新项目启动了，将 MapReduce 执行引擎和资源调度分离开来，这就是 Yarn

同样是在 2012 年，UC 伯克利 AMP  实验室开发的 Spark 开始崭露头角。当时 AMP 实验室的马铁博士发现使用 MapReduce 进行机器学习计算的时候性能非常差，因为机器学习算法通常需要进行很多次的迭代计算，而 MapReduce 每执行一次 Map 和 Reduce 计算都需要重新启动一次作业，带来大量的无谓消耗。还有一点就是 MapReduce 主要使用磁盘作为存储介质，而 2012  年的时候，内存已经突破容量和成本限制，成为数据运行过程中主要的存储介质。Spark 一经推出，立即受到业界的追捧，并逐步替代 MapReduce 在企业应用中的地位

一般说来，像 MapReduce、Spark 这类计算框架处理的业务场景都被称作批处理计算，因为它们通常针对以“天”为单位产生的数据进行一次计算，然后得到需要的结果，这中间计算需要花费的时间大概是几十分钟甚至更长的时间。相应地，有 Storm、Flink、Spark Streaming 等流计算框架来满足此类大数据应用的场景。 流式计算要处理的数据是实时在线产生的数据，所以这类计算也被称为大数据实时计算。而像 Flink 这样的计算引擎，可以同时支持流式计算和批处理计算

除了大数据批处理和流处理，NoSQL  系统处理的主要也是大规模海量数据的存储与访问，所以也被归为大数据技术。 NoSQL 曾经在 2011 年左右非常火爆，涌现出  HBase、Cassandra 等许多优秀的产品，其中 HBase 是从 Hadoop 中分离出来的、基于 HDFS 的 NoSQL 系统



时至今日，随着大数据系统的不断完善，Hadoop 已不再单指最初的 HDFS 和 MapReduce，狭义的Hadoop指的是是一个适合大数据分布式存储和分布式计算的平台，包括HDFS、MapReduce和YARN；而广义的 Hadoop，指的是 Hadoop 生态圈。目前生态圈结构大致如图所示：

![image-20220510235751987](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHadoop%E7%AE%80%E4%BB%8B%28%E4%B8%80%29/image-20220510235751987.png)

![image-20220511235539697](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHadoop%E7%AE%80%E4%BB%8B%28%E4%B8%80%29/image-20220511235539697.png)



------

#### Hadoop 生态圈简介



* HDFS

  分布式文件存储系统

  HDFS 是 Hadoop 体系中数据存储管理的基础。HDFS简化了文件的一致性模型，通过流式数据访问，提供高吞吐量应用程序数据访问功能，适合带有大型数据集的应用程序

* MapReduce

  MapReduce是一种基于磁盘的分布式并行批处理计算模型，用以进行大数据量的计算。它屏蔽了分布式计算框架细节，将计算抽象成map和reduce两部分

  其中Map对数据集上的独立元素进行指定的操作，生成键-值对形式中间结果。Reduce则对中间结果中相同“键”的所有“值”进行规约，以得到最终结果

* HBase

  分布式列存储数据库

  HBase 是一个建立在 HDFS 之上，面向列的针对结构化数据的可伸缩、高可靠、高性能、分布式和面向列的动态模式数据库

  HBase 采用了 BigTable 的数据模型：增强的稀疏排序映射表（Key/Value），其中，键由行关键字、列关键字和时间戳构成

* Zookeeper

  分布式协作服务

  解决分布式环境下的数据管理问题：统一命名，状态同步，集群管理，配置同步等

  Hadoop 的许多组件依赖于 Zookeeper，它运行在计算机集群上面，用于管理 Hadoop 操作

*  Hive / Impala

  Hive 定义了一种类似 SQL 的查询语言(HQL),将 SQL 转化为 MapReduce 任务在 Hadoop 上执行。通常用于离线分析

  Impala 是用于处理存储在Hadoop集群中的大量数据的MPP（大规模并行处理）SQL查询引擎。 与 Hive 不同，Impala 不基于 MapReduce 算法。 它实现了一个基于守护进程的分布式架构，它负责在同一台机器上运行的查询执行的所有方面。因此执行效率高于 Hive

* Sqoop

  Sqoop 利用数据库技术描述数据架构，用于在关系数据库、数据仓库和 Hadoop 之间转移数据

* Flume

  Flume 是一个可扩展、适合复杂环境的海量日志收集系统，也可以用于收集其他类型数据。它将数据从产生、传输、处理并最终写入目标的路径的过程抽象为数据流，在具体的数据流中，数据源支持在 Flume 中定制数据发送方，从而支持收集各种不同协议数据

  同时，Flume 数据流提供对日志数据进行简单处理的能力，如过滤、格式转换等。此外，Flume 还具有能够将日志写往各种数据目标（可定制）的能力

* Oozie

  Oozie 是一个可扩展的工作体系，集成于 Hadoop 的堆栈，用于协调多个 MapReduce 作业的执行。它能够管理一个复杂的系统，基于外部事件来执行，外部事件包括数据的定时和数据的出现

* Yarn

  YARN 是下一代MapReduce，即MRv2，是在第一代 MapReduce 基础上演变而来的，主要是为了解决原始 Hadoop 扩展性较差，不支持多计算框架而提出的

* Mesos

  当前有一些公司使用 Mesos 管理集群资源，比如 Twitter。与 yarn 类似，Mesos 是一个资源统一管理和调度的平台，同样支持比如 MR、steaming 等多种运算框架

* Spark

  Spark 提供了一个更快、更通用的数据处理平台。和 Hadoop 相比，Spark 可以让你的程序在内存中运行时速度提升 100 倍，或者在磁盘上运行时速度提升10 倍。不同于 MapReduce 的是—— Job 中间输出结果可以保存在内存中，从而不再需要读写 HDFS

* Flink 

  Flink 是一个基于内存的分布式并行处理框架，类似于 Spark，但在部分设计思想有较大出入。对 Flink 而言，其所要处理的主要场景就是流数据，批数据只是流数据的一个极限特例而已

* Kafka

  Kafka 是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。实现了主题、分区及其队列模式以及生产者、消费者架构模式

* Ambari 

  是创建、管理、监视 Hadoop 的集群，是为了让 Hadoop 以及相关的大数据软件更容易使用的一个web工具



------

#### Hadoop 优点



* 高可用

  Hadoop 底层维护多个数据副本，即便某个计算单元或存储出现故障，也不会导致数据丢失

* 高扩展

  可以在任务执行过程中动态的添加服务器数量而不用停止任务

* 高效性

  在 MapReduce 的思想下，Hadoop 是并行工作的

* 高容错

  能够自动将运行失败的任务重新分配



------

#### HDFS 基本概念



HDFS 是一个分布式文件存储系统，主要有以下几个基本概念：

* NameNode

  存储文件的元数据，如文件名、文件目录结构、文件属性（生成时间、副本数、文件权限），以及每个文件的块列表和块所在的 DataNode 等

* DataNode

  在本地文件系统存储文件块数据，以及块数据的校验和

* Secondary NameNode

  每隔一段时间对 NameNode 进行数据备份





------

#### MapReduce 简介



MapReduce 将计算过程分为两个阶段，map 和 reduce

map 阶段并行处理输入数据，reduce 阶段对 map 结果进行汇总
