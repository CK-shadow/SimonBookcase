---
title: 大数据课程之 Yarn(四)
date: 2022-09-17 20:31:32
tags: 大数据
categories: 
  - 大数据
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BYarn%28%E5%9B%9B%29/pexels-lukas-669612.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BYarn%28%E5%9B%9B%29/pexels-lukas-669612.jpg
---
# 大数据课程之 Yarn(四)

## 简介



Yarn 是一个资源调度平台，负责为运算程序提供服务器运算资源，相当于一个分布式的操作系统平台，而 MapReduce 等运算程序则相当于运算在操作系统上的程序



------

## Yarn 资源调度器

### Yarn 基础架构



![image-20220831002952912](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BYarn%28%E5%9B%9B%29/image-20220831002952912.png)



Yarn 主要是由 ResourceManager、NodeManager、ApplicationMaster 和 Container 等组件组成



ResourceManager

一个集群只有一个 RM，它是 YARN 的总指挥，负责协调集群上的计算资源，它有以下两个组件：

- ApplicationsManager：这个不是 ApplicationMaster，注意区分。它会接受 Job 的提交请求，调度 Container 用于启动 ApplicationMaster，以及负责 ApplicationMaster 的失败重启
- Scheduler：调度器纯粹为应用程序分配资源，它不会监控应用的状态，这里的调度就是基于 Container 这个抽象的资源容器，包含内存、CPU、磁盘、网络等

NodeManager

一个集群中有多个 NodeManager，它负责启动 Container，监控 Container 的资源使用情况（cpu、内存、磁盘、网络），并将这些信息汇报给 RM。一个 NodeManager 上可以有多个 Container

Container 是一组硬件资源的抽象，包含 CPU、内存、磁盘、网络等，所有的 Job 都是在 Container 中运行；
ApplicationMaster与 RM 协商资源，并与 NodeManager 一起监控任务，ApplicationMaster 和 Job 一样，都是运行在 Container 中



------

### Yarn 工作机制



![image-20220831234136222](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BYarn%28%E5%9B%9B%29/image-20220831234136222.png)



1. MR 程序提交到客户端所在节点，通过 main 方法执行了 `waitForCompletion` 方法后创建 YarnRunner，YarnRunne r向 RM 申请一个 application 资源
2. RM 返回给 application 资源的提交路径以及 application_id
3. YarnRunner 提交 job 运行所需资源，包括该 job 所需切片的信息、job 在 Hadoop 集群中的参数配置信息和使用的jar包；这些资源文件需在job.submit()方法提交成功后才会在 .staging 文件中生成
4. 资源提交完成后 YarnRunner 向 RM 申请运行 mrAppMaster
5. RM 会在内部将用户的请求初始化一个 Task，然后放入的任务队列里面等待执行
6. 等到 NodeManager 空闲后领取到 Task 任务便创建 container 容器
7. container 容器在里面启动 mrAppMaster
8. container 容器读取 job 资源，获取到了 job 切片信息，向 RM 申请 MapTask 容器用来执行 Map 任务
9. 其他空闲 NodeManager 空闲后领取任务创建对应切片个数的 container 容器
10. 之后 mrAppMaster 发送程序脚本启动对应的 Map 任务，yarnChild 即为 Map 任务进程
11. 当 Map 任务运行完成落磁盘之后，mrAppMaster 会再次向RM申请执行 ReduceTask 任务的资源
12. reduce 向 map 获取分区的数据
13. 当 reduce 任务也运行完成之后，mrAppMaster 通知 RM 并注销自己，同时相关的 MapReduce 的资源也释放掉



------

## Yarn 调度器与调度算法



目前，Hadoop 作业调度器主要有三种，FIFO、容量调度和公平调度，Hadoop 3.1.3 默认的资源调度器是容量调度



------

### FIFO 调度器



First In First Out，单队列，根据提交作业的先后顺序，先到先执行

FIFO  Scheduler是最简单也是最容易理解的调度器，也不需要任何配置，但它并不适用于共享集群。大的应用可能会占用所有集群资源，这就导致其它应用被阻塞。在共享集群中，更适合采用Capacity Scheduler或Fair Scheduler，这两个调度器都允许大任务和小任务在提交的同时获得一定的系统资源



------

### 容量调度器



![image-20220901001710700](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BYarn%28%E5%9B%9B%29/image-20220901001710700.png)



容量调度器每个队列内部先进先出, 同一时间队列中只有一个任务在执行, 队列的并行度为队列的个数。

Capacity Scheduler 是一个 hadoop 支持的可插拔的资源调度器，它允许多租户安全的共享集群资源，它们的 applications 在容量限制之下，可以及时的分配资源。使用操作友好的方式运行 hadoop 应用，同时最大化吞吐能力和集群利用率。

Capacity Scheduler 提供的核心理念就是 Queues（队列），这些 Queues 通常由管理员设定，它支持多个队列，每个队列可配置一定的资源量，每个队列采用FIFO 调度策略。为了在共享资源上，提供更多的控制和预见性，Capacity Scheduler 支持多级 queue，以确保在其他 queues 允许使用空闲资源之前，资源可以在一个组织的 sub-queues 之间共享。

为了防止同一个用户（一个用户可以绑定多个队列）的作业独占队列中的资源，该调度器会对同一用户提交的作业所占资源进行限定：

* 首先，计算每个队列中正在运行的任务数与其应该分得的计算资源之间的比值，选择一个该比值最小的队列（即最闲的）
* 其次，按照作业优先级和提交时间的顺序，同时考虑用户资源量限制和内存限制对队列内任务排序

如上图，三个队列同时按照任务的先后顺序依次执行，比如：job11，job21和job31 分别排在队列最前面，先运行，也是并行运行



------

### 公平调度器



![image-20220902003021551](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BYarn%28%E5%9B%9B%29/image-20220902003021551.png)



公平调度可以在多个队列间工作。如上图所示，假设有两个用户 A 和 B，分别拥有一个队列：

* 当 A 启动一个 job 而 B 没有任务时，A 会获得全部集群资源
* 当 B 启动一个 job 后，A 的 job 会继续运行，不过一会儿之后两个任务会各自获得一半的集群资源
* 如果此时 B 再启动第二个 job 并且其它 job 还在运行，则它将会和 B 的第一个 job 共享 B 这个队列的资源，也就是 B 的两个 job 会各自使用四分之一的集群资源，而 A 的 job 仍然用于集群一半的资源，结果就是资源最终在两个用户之间平等的共享

FairScheduler 将应用组织到队列中，并在这些队列之间公平地共享资源。默认情况下，所有用户共享一个名为default的队列。如果应用明确在容器资源请求中指定了队列，则该请求将提交到指定的队列。可以通过配置，根据请求中包含的用户名或组分配队列。在每个队列中，使用调度策略在运行的应用程序之间共享资源。默认设置是基于内存的公平共享，但是也可以配置具有优势资源公平性的 FIFO 和多资源



------

## Yarn 操作

### Yarn 常用命令



```sh
-- 列出所有的 application
yarn application -list

-- 根据 application 状态过滤
yarn application -list -appStates <具体状态>

-- Kill 掉 application
yarn application -kill <applicationId>

-- 查询 application 日志
yarn logs -applicationId <applicationId>

-- 查询 container 日志
yarn logs -applicationId <applicationId> -container <containerId>

-- 查看尝试运行的任务
yarn applicationattempt -list <applicationId>

-- 打印 applicationattempt 状态
yarn applicationattempt -status <applicationattemptId>

-- 列出所有的 container
yarn container -list <applicationattemptId>

-- 打印 container 状态
yarn container -status <containerId>

-- 列出所有节点
yarn node -list -all

-- 加载队列配置
yarn rmadmin -refreshQueues

-- 打印队列信息
yarn queue -status <queueName>
```



------

### 核心配置参数



|                             作用                             |                            参数                             |
| :----------------------------------------------------------: | :---------------------------------------------------------: |
|                 配置调度器，默认是容量调度器                 |            yarn.resourcemanager.scheduler.class             |
|      ResourceManager处理client请求的线程数量，默认是50       |     yarn.resourcemanager.scheduler.client.thread-count      |
| 是否让yarn自己检测硬件进行配置，默认是false，类似安装软件时的自动安装 |   yarn.nodemanager.resource.detect-hardware-capabilities    |
|             是否将虚拟核数当作CPU核数，默认false             | yarn.nodemanager.resource.count-logical-processors-as-cores |
|               虚拟核数和物理核数乘数，默认1.0                |     yarn.nodemanager.resource.pcores-vcores-multiplier      |
|                 NodeManager使用内存，默认8G                  |             yarn.nodemanager.resource.memory-mb             |
|                NodeManager为系统保留多少内存                 |     yarn.nodemanager.resource.system-reserved-memory-mb     |
|               NodeManager使用CPU核数，默认8个                |            yarn.nodemanager.resource.cpu-vcores             |
|         是否开启物理内存检测限制container，默认打开          |            yarn.nodemanager-pvmem-check-enabled             |
|         是否开启虚拟内存检测限制container，默认打开          |             yarn.nodemanager.vmem-check-enabled             |
|               虚拟内存和物理内存比例，默认2:1                |              yarn.nodemanager.vmem-pmem-ratio               |
|                     容器最小内存，默认1G                     |            yarn.scheduler.minimum-allocation-mb             |
|                     容器最大内存，默认8G                     |            yarn.scheduler.maximum-allocation-mb             |
|                   容量最小CPU核数，默认1个                   |          yarn.scheduler.minimum-allocation-vcores           |
|                   容器最大CPU核数，默认4个                   |          yarn.scheduler.maximum-allocation-vcores           |

