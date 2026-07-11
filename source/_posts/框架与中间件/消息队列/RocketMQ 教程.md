---
title: RocketMQ 教程
date: 2025-12-07 19:45:45
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/preview.jpg
tags:
  - 框架和中间件
  - 消息队列
  - RocketMQ
categories: 框架和中间件
---



# 简介

## MQ 简介



MQ（Message Queue），消息队列，是在互联网中使用非常广泛的一种服务中间件。消息队列是一种异步通信机制，允许应用程序、系统或组件之间通过发送和接收消息来进行通信。它就像是一个中间的 “仓库”，发送方将消息发送到这个 “仓库”，而接收方可以根据自己的节奏从 “仓库” 中获取消息进行处理，发送方和接收方不需要同时运行，也不需要知道对方的具体位置和实现细节

<br>

MQ 的作用主要有以下三个方面：

* 异步：以用户注册为例，当用户点击注册按钮后，系统除了要完成数据库中用户信息的插入，还可能需要发送注册成功邮件、推送新手引导消息等操作。如果这些操作都同步进行，用户可能需要等待较长时间才能看到注册成功的提示。使用消息队列后，系统可以在插入用户信息后，将发送邮件和推送消息的任务封装成消息发送到消息队列，然后立即返回给用户注册成功的提示，后续由专门的消费者从消息队列中获取消息来异步处理发送邮件和推送消息的任务，提高了用户体验
* 解耦：在一个大型系统中，往往有多个不同的模块或服务，如订单系统、库存系统、物流系统等。这些系统之间如果直接相互调用，会导致它们之间的耦合度非常高。使用消息队列后，订单系统只需将订单消息发送到消息队列，而无需关心库存系统和物流系统何时以及如何处理订单，库存系统和物流系统从消息队列获取订单消息进行处理，这样各个系统可以独立地进行开发、测试和升级，降低了系统间的耦合度
* 削峰：在电商促销活动等高峰时段，大量用户同时下单。如果没有消息队列，订单系统可能会因为瞬间的高并发请求而崩溃。消息队列可以作为缓冲，将大量的订单请求先存储在队列中，然后订单系统按照自己的处理能力从队列中逐步获取订单进行处理，避免系统因流量过大而瘫痪，保护了后端系统的稳定性



------

## RocketMQ 简介



RocketMQ 是阿里巴巴开源的一个消息中间件，在阿里内部历经了双十一等很多高并发场景的考验，能够处理亿万级别的消息。2016 年开源后捐赠给 Apache，现在是 Apache 的一个顶级项目



![image-20250222113117147](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250222113117147.png)

官网地址：[RocketMQ · 官方网站 | RocketMQ](https://rocketmq.apache.org/)

<br>

早期阿里使用 ActiveMQ，但是，当消息开始逐渐增多时，ActiveMQ 的 IO 性能很快就达到了瓶颈。于是，阿里开始关注 Kafka。但是，Kafka 是针对日志收集场景设计的，它的高级功能并不是很贴合阿里的业务场景。尤其是当它的 Topic 过多时，由于 Partition 文件也会过多，这就会加大文件索引的耗时，严重影响 IO 性能。于是，阿里才决定自研中间件，最早叫做 MeteQ，后来改名为 RocketMQ。最早所希望解决的的最大问题就是多 Topic 下的 IO 性能压力，但是在阿里内部的不断改进下，RocketMQ 开始展现出一些不一样的优势



------

## RocketMQ 特点



当今互联网 MQ 产品众多，其中，影响力和适用范围最大的当属 Apache Kafka、RabbitMQ、Apache RocketMQ 和 Apache Plusar。这几大产品虽然都是典型的 MQ 产品，但是由于设计和实践上的一些差异，造成它们适合于不同的细分场景

<br>

|                 | 优点                                                         | 缺点                                 | 适合场景                     |
| --------------- | ------------------------------------------------------------ | ------------------------------------ | ---------------------------- |
| Apache Kafka    | 吞吐量非常大，性能非常好，集群高可用                         | 会有丢数据的可能，功能比较单一       | 日志分析，大数据采集         |
| RabbitMQ        | 消息可靠性高，功能全面                                       | erlang 语言不好定制，吞吐量比较低    | 企业内部小规模服务调用       |
| Apache RocketMQ | 高吞吐、高性能、高可用，功能全面，客户端协议丰富，使用 Java 语言开发，方便定制 | 服务加载比较慢                       | 几乎全场景，特别适合金融场景 |
| Apache Plusar   | 基于 Bookeeper 构建，消息可靠性非常高                        | 周边生态还有差距，目前使用的公司较少 | 企业内部大规模服务调用       |

<br>

其中 RocketMQ，孵化自阿里巴巴，历经阿里多年双十一考验，RocketMQ 可以说是从全世界最严苛的高并发场景中摸爬滚打出来的过硬产品，也是少数几个在金融场景比较适用的 MQ 产品。横向对比来看，RocketMQ 的消息吞吐量和 Kafka 相比还是有差距，但是比 RabbitMQ 高很多。在阿里内部，RocketMQ 集群每天处理的请求超五万亿次，支持和核心应用超 3000 个。而 RocketMQ 最大的优势就是它就是为金融互联网而生的，它的消息可靠性相比 Kafka 也有了很大提升，消息吞吐量相比 RabbitMQ 也有了很大提升。另外，RocketMQ 的高级功能也越来越全面，广播消费、延迟队列、死信队列等高级功能一应俱全，甚至某些功能如事务消息，已经呈现出领先潮流的趋势



------

## 常用名词



**Nameserver**

功能：NameServer 是 RocketMQ 的命名服务，充当整个消息系统的 “目录服务”。它负责存储和管理 Broker 的元数据信息，包括 Broker 的地址、主题路由信息等

特点：NameServer 是无状态的，多个 NameServer 节点之间相互独立，不会进行信息交换。Broker 会定期向 NameServer 发送心跳信息，以保持其元数据的最新状态。客户端（生产者和消费者）在启动时会从 NameServer 获取主题的路由信息，从而知道消息应该发送到哪个 Broker 或从哪个 Broker 消费消息

<br>

**Broker**

功能：Broker 是 RocketMQ 的核心组件，负责消息的存储、转发和查询。它接收生产者发送的消息，并将其存储在本地磁盘上；同时，为消费者提供消息的拉取服务

<br>

**Topic**

定义：Topic 是 RocketMQ 中消息的逻辑分类，用于区分不同类型的消息。生产者将消息发送到特定的 Topic，消费者则从感兴趣的 Topic 中消费消息

作用：Topic 可以看作是一个消息的集合，不同的业务系统可以使用不同的 Topic 来隔离消息，方便消息的管理和维护。例如，电商系统可以使用 “order_topic” 来处理订单相关的消息，使用 “payment_topic” 来处理支付相关的消息

<br>

**Queue**

定义：Queue 是 Topic 的物理划分，一个 Topic 可以包含多个 Queue。Queue 是消息存储和消费的基本单位，生产者将消息发送到 Topic 下的某个 Queue，消费者从 Queue 中拉取消息

作用：Queue 具有顺序性，在同一个 Queue 中的消息是按照发送顺序存储和消费的。通过增加 Queue 的数量，可以提高消息的并发处理能力

<br>

**Producer**

定义：Producer 是消息的发送者，负责将业务系统产生的消息发送到 RocketMQ 的 Broker 中

<br>

**Consumer**

定义：Consumer 是消息的接收者，负责从 RocketMQ 的 Broker 中拉取消息并进行处理

<br>

**Consumer Group**

定义：Consumer Group 是一组消费者的集合，同一个消费组内的消费者共同消费一个 Topic 下的消息

作用：消费组可以实现消息的负载均衡和容错。当一个消费组中有多个消费者实例时，RocketMQ 会自动将 Topic 下的 Queue 分配给不同的消费者实例，从而实现消息的并行消费。如果某个消费者实例出现故障，其他消费者实例可以继续消费消息，保证消息不会丢失

<br>

**Tag**

定义：Tag 是对 Topic 下消息的进一步细分，用于在同一个 Topic 中区分不同类型的消息。生产者在发送消息时可以为消息指定 Tag，消费者在订阅消息时可以根据 Tag 进行过滤，只消费感兴趣的消息

作用：Tag 可以提高消息的处理效率，减少不必要的消息消费。例如，在 “order_topic” 中，可以使用 “new_order”、“cancel_order” 等 Tag 来区分不同类型的订单消息



------

## 整体架构设计



![image-20250223181403559](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250223181403559.png)



------

## 模型关系



![image-20250223181425577](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250223181425577.png)



------

# 安装

## 环境准备



RocketMQ 基于 Java 开发，需要安装 Java 运行环境，建议使用 Java 8 及以上版本。可以通过以下命令检查是否已安装 Java：

```SH
java -version
```

如果未安装，可以使用以下命令安装 OpenJDK 8：

```SH
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

对于 CentOS 系统，可以使用以下命令安装：

```SH
sudo yum install java-1.8.0-openjdk-devel
```



------

## 下载安装



**下载**

访问 [RocketMQ 官方发布页面](https://github.com/apache/rocketmq/releases)，选择合适的版本，以 5.3.0 版本为例，使用以下命令下载并解压：

```SH
wget https://dlcdn.apache.org/rocketmq/5.3.0/rocketmq-all-5.3.0-bin-release.zip
unzip rocketmq-all-5.3.0-bin-release.zip
mv rocketmq-all-5.3.0-bin-release rocketmq
```

<br>

**配置环境变量**

编辑  ~/.bashrc 文件，添加以下内容：

```SH
export ROCKETMQ_HOME=/path/to/rocketmq
export PATH=$PATH:$ROCKETMQ_HOME/bin
```

将 /path/to/rocketmq 替换为实际的 RocketMQ 安装路径。然后使配置生效：

```SH
source ~/.bashrc
```

<br>

**启动 NameServer**

NameServer 是 RocketMQ 的命名服务，负责存储和管理 Broker 的元数据信息。使用以下命令启动 NameServer：

```SH
nohup sh mqnamesrv &
```

启动后，可以通过查看日志文件来确认是否启动成功：

```SH
tail -f ~/logs/rocketmqlogs/namesrv.log
```

如果看到类似 The Name Server boot success 的日志信息，则表示启动成功

<br>

**启动 Broker**

Broker 是 RocketMQ 的消息存储和转发服务，使用以下命令启动 Broker：

```SH
nohup sh mqbroker -n localhost:9876 &
```

其中 localhost:9876 是 NameServer 的地址。同样，可以通过查看日志文件来确认是否启动成功：

```SH
tail -f ~/logs/rocketmqlogs/broker.log
```

如果看到类似 The broker[%s, %s] boot success 的日志信息，则表示启动成功

<br>

**验证安装**

可以使用 RocketMQ 提供的命令行工具来验证安装是否成功。首先发送一条消息：

```SH
sh tools.sh org.apache.rocketmq.example.quickstart.Producer
```

然后消费这条消息：

```SH
sh tools.sh org.apache.rocketmq.example.quickstart.Consumer
```

如果能正常发送和消费消息，则说明 RocketMQ 安装成功

<br>

**关闭服务**

如果需要关闭 RocketMQ 服务，可以使用以下命令：

```SH
sh mqshutdown broker
sh mqshutdown namesrv
```



------

## 搭建 Dashboard



**下载**

可以从 [RocketMQ Dashboard 的 GitHub 仓库](https://github.com/apache/rocketmq-dashboard) 下载其源码或者预编译的二进制包

```SH
wget https://github.com/apache/rocketmq-dashboard/releases/download/v2.0.0/rocketmq-dashboard-2.0.0.jar
```

<br>

**配置**

在运行 Dashboard 之前，需要对其进行一些配置，主要是指定 NameServer 的地址

可以通过以下两种方式进行配置：

1. 在启动 Dashboard 时，通过 -D 参数指定配置信息，例如：

   ```SH
   java -Dserver.port=8080 -Drocketmq.config.namesrvAddr=localhost:9876 -jar rocketmq-dashboard-2.0.0.jar
   ```

2. 使用配置文件，创建一个 application.properties 文件，内容如下：

   ```properties
   server.port=8080
   rocketmq.config.namesrvAddr=localhost:9876
   ```

   其中 server.port 是 Dashboard 启动的端口，rocketmq.config.namesrvAddr 是 NameServer 的地址

<br>

**启动**

在配置好后，使用以下命令启动 RocketMQ Dashboard：

```SH
nohup java -jar rocketmq-dashboard-2.0.0.jar &
```

若使用了配置文件，启动命令如下：

```SH
nohup java -Dspring.config.location=./application.properties -jar rocketmq-dashboard-2.0.0.jar &
```

启动后，可以通过查看日志文件来确认是否启动成功：

```SH
tail -f nohup.out
```

如果看到类似 Started RocketMQDashboardApplication 的日志信息，则表示启动成功

<br>

**访问**

打开浏览器，访问 http://<服务器 IP 地址>:8080（这里的端口号要和配置中的 server.port 一致），即可进入 RocketMQ Dashboard 的可视化界面。在该界面中，可以进行以下操作：

* 主题管理：查看、创建、删除主题，以及对主题的配置进行修改
* 消费者管理：查看消费者组的状态、消费进度等信息
* Broker 管理：监控 Broker 的运行状态、磁盘使用情况等
* 消息查询：根据主题、消息 ID 等条件查询消息



------

## 升级为分布式集群



**规划**

RocketMQ 集群通常采用多 NameServer 和多 Broker 节点的架构。NameServer 负责存储和管理 Broker 的元数据信息，Broker 负责消息的存储和转发。建议至少部署 2 个 NameServer 节点和 2 组 Broker 节点（每组包含 Master 和 Slave 节点）

<br>

**准备新节点**

按照之前单机版部署的步骤，在新的服务器上安装 Java 环境，并下载解压 RocketMQ

配置环境变量，确保新节点可以正常使用 RocketMQ 的命令

<br>

**配置 Nameserver 集群**

在每个 NameServer 节点上，无需进行额外的配置修改，直接启动 NameServer 服务：

```SH
nohup sh mqnamesrv &
```

不同节点的 NameServer 会自动发现彼此，形成集群

<br>

**配置 Broker 集群**

1. 复制 Broker 配置文件

   在 $ROCKETMQ_HOME/conf 目录下，有多种 Broker 配置模板，选择合适的模板进行复制，例如 2m-2s-sync（表示 2 个 Master 和 2 个 Slave，采用同步复制模式）：

   ```SH
   cp $ROCKETMQ_HOME/conf/2m-2s-sync/broker-a.properties /path/to/custom-config/broker-a.properties
   cp $ROCKETMQ_HOME/conf/2m-2s-sync/broker-a-s.properties /path/to/custom-config/broker-a-s.properties
   cp $ROCKETMQ_HOME/conf/2m-2s-sync/broker-b.properties /path/to/custom-config/broker-b.properties
   cp $ROCKETMQ_HOME/conf/2m-2s-sync/broker-b-s.properties /path/to/custom-config/broker-b-s.properties
   ```

2. 修改配置文件

   主要修改以下几个关键配置项：

   * brokerClusterName：指定 Broker 集群的名称，所有 Broker 节点的该配置应保持一致
   * brokerName：不同的 Broker 组应使用不同的名称，如 broker-a 和 broker-b
   * brokerId：Master 节点的 brokerId 为 0，Slave 节点的 brokerId 为大于 0 的整数
   * namesrvAddr：指定 NameServer 集群的地址，多个地址用分号分隔，例如 192.168.1.100:9876;192.168.1.101:987
   * brokerIP1：指定 Broker 节点的 IP 地址

   以下是 broker-a.properties 的示例配置：

   ```properties 
   brokerClusterName = DefaultCluster
   brokerName = broker-a
   brokerId = 0
   namesrvAddr = 192.168.1.100:9876;192.168.1.101:9876
   brokerIP1 = 192.168.1.102
   deleteWhen = 04
   fileReservedTime = 48
   brokerRole = SYNC_MASTER
   flushDiskType = SYNC_FLUSH
   ```

3. 启动 Broker 节点

   在每个 Broker 节点上，使用修改后的配置文件启动 Broker 服务：

   ```SH
   nohup sh mqbroker -c /path/to/custom-config/broker-a.properties &
   ```

   依次启动所有的 Master 和 Slave 节点

<br>

**验证集群部署**

打开 RocketMQ Dashboard，在界面上查看集群的节点信息、主题分布等，确认集群是否正常工作



------

## 升级为 Dledger 架构



**环境准备**

确保所有参与 Dledger 集群的服务器都已经安装了 Java 8 及以上版本，并且已经下载并解压了支持 Dledger 的 RocketMQ 版本（通常 RocketMQ 4.5.0 及以上版本支持 Dledger）。同时，要保证各节点之间网络畅通，防火墙允许相关端口的通信

<br>

**配置 Dledger 相关参数**

1. 复制配置文件

   在 $ROCKETMQ_HOME/conf 目录下，有 Dledger 相关的配置模板，比如 dledger 目录下的配置文件，复制到自定义配置目录：

   ```SH
   cp -r $ROCKETMQ_HOME/conf/dledger /path/to/custom-config/dledger
   ```

2. 修改 Broker 配置文件

   以 broker-n0.conf 为例（这里 n0 表示一个节点标识），主要修改以下关键配置项：

   ```conf
   # 集群名称，所有 Broker 节点保持一致
   brokerClusterName = DledgerCluster
   # Broker 组名称，同一组内的 Master 和 Slave 使用相同名称
   brokerName = broker-dledger
   # 该 Broker 节点在组内的唯一标识，Dledger 模式下可以从 0 开始递增
   brokerId = 0
   # NameServer 地址，多个地址用分号分隔
   namesrvAddr = 192.168.1.100:9876;192.168.1.101:9876
   # 开启 Dledger 功能
   enableDLegerCommitLog = true
   # Dledger 组名称，同一组内的节点保持一致
   dLegerGroup = broker-dledger
   # Dledger 节点列表，格式为 节点 ID@节点 IP:端口
   dLegerPeers = n0@192.168.1.102:40911;n1@192.168.1.103:40911;n2@192.168.1.104:40911
   # 当前节点的 ID，要和 dLegerPeers 中的节点 ID 对应
   dLegerSelfId = n0
   ```

3. 为每个节点准备配置文件

   按照上述方式，为集群中的每个节点创建对应的配置文件，只需修改 brokerId 和 dLegerSelfId 为该节点对应的标识

<br>

**启动 Dledger 集群**

1. 启动 Nameserver

   确保 NameServer 集群已经正常运行，如果之前没有启动或者有新的 NameServer 节点，使用以下命令启动：

   ```SH
   nohup sh mqnamesrv &
   ```

2. 启动 Broker

   依次在每个节点上使用修改后的配置文件启动 Broker：

   ```SH
   nohup sh mqbroker -c /path/to/custom-config/dledger/broker-n0.conf &
   ```

   broker-n0.conf 要替换为当前节点对应的配置文件

<br>

**验证集群部署**

打开 RocketMQ Dashboard，查看集群的节点信息、主题分布等，确认 Dledger 架构下的 Broker 节点正常工作，并且可以正常创建和消费消息



------

## 两种架构的区别



传统的 RocketMQ 分布式架构主要基于 Master - Slave 模式。其设计目标是通过多节点部署来提高系统的吞吐量、可扩展性和可用性。Master 节点负责处理读写请求，Slave 节点作为备份，在 Master 出现故障时可以接管部分工作，以保证系统的持续运行

Dledger 架构是为了解决传统 Master - Slave 模式在数据一致性和故障切换方面的不足而设计的。它借鉴了 Raft 协议的思想，采用分布式一致性算法来保证数据在多个节点之间的强一致性，并且能够自动进行 Leader 选举和故障转移，提高了系统的可靠性和稳定性

<br>

在传统的 Master - Slave 模式中，数据一致性主要依赖于同步或异步复制机制。异步复制模式下，Slave 节点的数据复制可能会有一定的延迟，在 Master 节点发生故障时，可能会导致部分数据丢失；同步复制模式虽然可以保证数据的一致性，但会增加系统的延迟，影响性能

Dledger 架构采用 Raft 协议实现数据的强一致性。在数据写入时，只有当数据被多数节点（超过半数）成功写入后，才会返回写入成功的响应，从而保证了即使部分节点出现故障，数据也不会丢失，并且所有节点的数据始终保持一致

<br>

在传统的分布式架构中，当 Master 节点出现故障时，需要手动或通过外部工具进行故障切换，将 Slave 节点提升为 Master 节点。这个过程可能会比较复杂，并且在切换过程中会有一定的服务中断时间

Dledger 架构具有自动故障转移的能力。当 Leader 节点（类似于传统架构中的 Master 节点）出现故障时，Dledger 集群会自动进行 Leader 选举，从剩余的 Follower 节点中选出新的 Leader 节点，整个过程无需人工干预，并且服务中断时间极短

<br>

分布式架构适用于对数据一致性要求不是特别高，更注重系统吞吐量和简单性的场景。例如，一些日志收集、实时数据分析等场景，允许一定程度的数据丢失

Dledger 架构适用于对数据一致性和可靠性要求极高的场景，如金融交易、订单处理等。在这些场景中，任何数据的丢失或不一致都可能导致严重的后果



------

# 功能特性

## 客户端基本流程



确保已经安装并启动了 RocketMQ 服务，同时在项目中添加 RocketMQ 客户端依赖。如果使用的是 Maven 项目，可以在 pom.xml 中添加以下依赖：

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>5.3.0</version>
</dependency>
```

<br>

**生产者代码示例**

```JAVA
public class ProducerExample {
    public static void main(String[] args) throws MQClientException, InterruptedException {
        // 创建一个生产者实例，并指定生产者组名
        DefaultMQProducer producer = new DefaultMQProducer("ProducerGroup");
        // 指定 NameServer 的地址
        producer.setNamesrvAddr("localhost:9876");
        // 启动生产者实例
        producer.start();

        try {
            for (int i = 0; i < 10; i++) {
                // 创建消息实例，指定主题、标签和消息体
                Message msg = new Message("TopicTest",
                        "TagA",
                        ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
                // 发送消息并获取发送结果
                SendResult sendResult = producer.send(msg);
                System.out.printf("%s%n", sendResult);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        // 关闭生产者实例
        producer.shutdown();
    }
}
```

<br>

**消费者代码示例**

```java
public class ConsumerExample {
    public static void main(String[] args) throws InterruptedException, MQClientException {
        // 创建一个消费者实例，并指定消费者组名
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroup");
        // 指定 NameServer 的地址
        consumer.setNamesrvAddr("localhost:9876");
        // 订阅主题和标签，* 表示订阅该主题下的所有消息
        consumer.subscribe("TopicTest", "*");
        // 注册消息监听器，用于处理接收到的消息
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
                                                            ConsumeConcurrentlyContext context) {
                for (MessageExt msg : msgs) {
                    System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), new String(msg.getBody()));
                }
                // 返回消费成功状态
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动消费者实例
        consumer.start();
        System.out.printf("Consumer Started.%n");
    }
}
```



------

## 消息确认机制



**生产者消息发送确认**

```JAVA
public class SyncProducer {
    public static void main(String[] args) throws MQClientException, InterruptedException {
        DefaultMQProducer producer = new DefaultMQProducer("ProducerGroup");
        producer.setNamesrvAddr("localhost:9876");
        producer.start();

        // 在同步发送模式下，生产者调用 send 方法发送消息后，会阻塞等待 Broker 返回发送结果
        // 这种方式能够明确知道消息是否成功发送到 Broker
        try {
            Message msg = new Message("TopicTest",
                    "TagA",
                    "Hello RocketMQ".getBytes(RemotingHelper.DEFAULT_CHARSET));
            SendResult sendResult = producer.send(msg);
            System.out.printf("Send Result: %s%n", sendResult);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        // 异步发送时，生产者调用 send 方法后不会阻塞，而是立即返回
        // 同时，需要为其指定一个回调函数，当 Broker 返回发送结果时，会触发该回调函数
        Message msg = new Message("TopicTest",
                "TagA",
                "Hello RocketMQ".getBytes(RemotingHelper.DEFAULT_CHARSET));
        producer.send(msg, new SendCallback() {
            @Override
            public void onSuccess(SendResult sendResult) {
                System.out.printf("Send Success: %s%n", sendResult);
            }

            @Override
            public void onException(Throwable e) {
                System.out.printf("Send Failed: %s%n", e);
            }
        });
        
        // 单向发送不等待 Broker 的响应，也没有确认机制
        // 这种方式发送速度最快，但无法保证消息是否成功到达 Broker
        try {
            Message msg = new Message("TopicTest",
                    "TagA",
                    "Hello RocketMQ".getBytes(RemotingHelper.DEFAULT_CHARSET));
            producer.sendOneway(msg);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        producer.shutdown();
    }
}
```

<br>

**消费者消息消费确认**

在推模式（DefaultMQPushConsumer）中，消费者注册消息监听器来处理消息。监听器处理完消息后，需要返回一个消费状态

ConsumeConcurrentlyStatus.CONSUME_SUCCESS 表示消息消费成功，Broker 会将这些消息标记为已消费

ConsumeConcurrentlyStatus.RECONSUME_LATER 表示消费失败，需要稍后重新消费，Broker 会在一段时间后重新将消息推送给消费者

```JAVA
public class PushConsumer {
    public static void main(String[] args) throws InterruptedException, MQClientException {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ConsumerGroup");
        consumer.setNamesrvAddr("localhost:9876");
        consumer.subscribe("TopicTest", "*");
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
                                                            ConsumeConcurrentlyContext context) {
                for (MessageExt msg : msgs) {
                    System.out.printf("Receive Message: %s %n", new String(msg.getBody()));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
        System.out.printf("Consumer Started.%n");
    }
}
```

<br>

在拉模式（DefaultMQPullConsumer）中，消费者主动从 Broker 拉取消息，处理完消息后，需要手动更新消费进度，以此来确认消息已经消费

通过维护 OFFSE_TABLE 来记录每个消息队列的消费偏移量，确保下次拉取消息时从正确的位置开始

```JAVA
public class PullConsumer {
    private static final Map<MessageQueue, Long> OFFSE_TABLE = new HashMap<>();

    public static void main(String[] args) throws MQClientException {
        DefaultMQPullConsumer consumer = new DefaultMQPullConsumer("ConsumerGroup");
        consumer.setNamesrvAddr("localhost:9876");
        consumer.start();

        Set<MessageQueue> mqs = consumer.fetchSubscribeMessageQueues("TopicTest");
        for (MessageQueue mq : mqs) {
            SINGLE_MQ:
            while (true) {
                try {
                    PullResult pullResult =
                            consumer.pullBlockIfNotFound(mq, null, getMessageQueueOffset(mq), 32);
                    System.out.printf("%s%n", pullResult);
                    putMessageQueueOffset(mq, pullResult.getNextBeginOffset());
                    switch (pullResult.getPullStatus()) {
                        case FOUND:
                            for (MessageExt msg : pullResult.getMsgFoundList()) {
                                System.out.printf("%s%n", new String(msg.getBody()));
                            }
                            break;
                        case NO_MATCHED_MSG:
                            break;
                        case NO_NEW_MSG:
                            break SINGLE_MQ;
                        case OFFSET_ILLEGAL:
                            break;
                        default:
                            break;
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
        consumer.shutdown();
    }

    private static long getMessageQueueOffset(MessageQueue mq) {
        Long offset = OFFSE_TABLE.get(mq);
        if (offset != null)
            return offset;
        return 0;
    }

    private static void putMessageQueueOffset(MessageQueue mq, long offset) {
        OFFSE_TABLE.put(mq, offset);
    }
}
```



------

## 集群模式和广播模式



在集群模式下，一个消费组（Consumer Group）中的多个消费者实例共同消费主题（Topic）下的消息，每个消息只会被消费组内的一个消费者实例消费。这种模式可以实现消息的负载均衡和高可用性，适用于需要对消息进行分布式处理的场景

集群模式下，消费进度由 Broker 来维护，这也是 RocketMQ 的默认消息消费模式

```JAVA
public class ClusterConsumer {
    public static void main(String[] args) throws Exception {
        // 创建消费者实例
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ClusterConsumerGroup");
        // 设置 NameServer 地址
        consumer.setNamesrvAddr("localhost:9876");
        // 订阅主题
        consumer.subscribe("TopicTest", "*");
        // 注册消息监听器
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
                for (MessageExt msg : msgs) {
                    System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), new String(msg.getBody()));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动消费者
        consumer.start();
        System.out.printf("Consumer Started.%n");
    }
}
```

<br>

广播模式下，消费组内的每个消费者实例都会收到主题下的所有消息。即消息会被广播给消费组内的每一个消费者，每个消费者都会独立消费一份完整的消息

在广播模式下，每个消费者实例维护自己的消费进度。即使某个消费者处理消息较慢或出现故障，也不会影响其他消费者的正常消费

```JAVA
public class BroadcastConsumer {
    public static void main(String[] args) throws Exception {
        // 创建消费者实例
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("BroadcastConsumerGroup");
        // 设置 NameServer 地址
        consumer.setNamesrvAddr("localhost:9876");
        // 设置消费模式为广播模式
        consumer.setMessageModel(MessageModel.BROADCASTING);
        // 从消息队列头部开始消费
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
        // 订阅主题
        consumer.subscribe("TopicTest", "*");
        // 注册消息监听器
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
                for (MessageExt msg : msgs) {
                    System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), new String(msg.getBody()));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动消费者
        consumer.start();
        System.out.printf("Broadcast Consumer Started.%n");
    }
}
```



------

## 定时/延时消息



定时消息指消息在指定的某个具体时间点才会被投递到消费者进行消费。生产者可以指定消息发送的具体时间，RocketMQ 会在到达该时间时将消息发送给消费者

延时消息是指消息发送后，不会立即被投递，而是在经过一段指定的延迟时间后才被投递到消费者。生产者只需要设置消息的延迟级别或延迟时间，RocketMQ 会根据设置来延迟消息的投递

相比于 RabbitMQ 和 Kafka，RabbitMQ 只能通过使用死信队列变相实现延迟消息，或者加装一个插件来实现。Kafka 则不太好实现延迟消息

<br>

RocketMQ 5.x 版本提供了两种实现延时消息的机制，一种是固定的延时级别，一种是指定消息发送时间

```JAVA
Message message = new Message("TimedMessageTopic", "TagA", "Hello RocketMQ!".getBytes());
// 设置定时时间，这里设置为当前时间 + 5秒
long currentTime = System.currentTimeMillis();
Date sendTime = new Date(currentTime + 5000);
message.setStartDeliverTime(sendTime.getTime());
```

```JAVA
Message message = new Message("DelayMessageTopic", "TagA", "Hello RocketMQ!".getBytes());
// 设置延时级别，这里设置为3级，对应延迟时间为10秒
message.setDelayTimeLevel(3);
```

RocketMQ 默认提供了 18 个延时级别，对应的时间分别为 1s、5s、10s、30s、1m、2m、3m、4m、5m、6m、7m、8m、9m、10m、20m、30m、1h、2h

在 RocketMQ 4.x 版本及以前，只能通过设置延时级别来设置消息的延迟时间，如果想要设置默认配置中没有的时间，比如 15s 后发送，则需要修改配置文件，修改延时级别对应的时间

<br>

两种消息的实现原理也不同，定时消息是将消息直接写入CommitLog（RocketMQ 的主存储日志），Broker 通过调度线程周期性扫描CommitLog，检查消息的startDeliverTime是否到达。时间到达后，将消息投递到消费者队列（如ConsumeQueue）

延时消息是将消息先写入CommitLog，但被标记为延迟类型，Broker 根据delayTimeLevel计算出实际投递时间，并将消息路由到延迟队列（如SCHEDULE_TOPIC_XXXX）。延迟队列通过时间轮算法或优先队列调度，到期后将消息重新投递到目标消费者队列

<br>

延时消息仅支持在 MessageType 为 Delay 的主题内使用，即定时消息只能发送至类型为定时消息的主题中，发送的消息的类型必须和主题的类型一致

RocketMQ 定时消息的状态支持持久化存储，系统由于故障重启后，仍支持按照原来设置的定时时间触发消息投递。若存储系统异常重启，可能会导致定时消息投递出现一定延迟



------

## 批量消息



RocketMQ 允许将多条消息组合成一个批次，作为一个整体进行发送，而不是逐条发送消息。这么做可以减少网络开销，提高发送效率，还可以保证顺序性

<br>

```JAVA
// 创建消息列表
List<Message> messages = new ArrayList<>();
// 创建并添加消息到列表
messages.add(new Message("BatchTopic", "TagA", "OrderID001", "Hello RocketMQ 1".getBytes()));
messages.add(new Message("BatchTopic", "TagA", "OrderID002", "Hello RocketMQ 2".getBytes()));
messages.add(new Message("BatchTopic", "TagA", "OrderID003", "Hello RocketMQ 3".getBytes()));

try {
	// 发送批量消息
	SendResult sendResult = producer.send(messages);
	System.out.println(sendResult);
} catch (Exception e) {
	e.printStackTrace();
}
```

<br>

需要注意的是：

* 批量消息的总大小不能超过 Broker 配置的最大消息大小限制，默认情况下是 4MB。如果批量消息超过了这个限制，发送将会失败
* 批量发送的消息必须属于同一个主题，因为不同主题的消息在 Broker 中的存储和路由方式可能不同，无法放在一个批次中发送
* 为了保证消息的顺序性和正确处理，批量消息通常需要发送到同一个消息队列中
* 批量发送消息时，可能会出现部分消息发送成功，部分消息发送失败的情况。需要在代码中进行适当的异常处理，例如记录发送失败的消息，以便后续进行重试或其他处理



------

## 顺序消息



顺序消息是 Apache RocketMQ 提供的一种高级消息类型，支持消费者按照发送消息的先后顺序获取消息，从而实现业务场景中的顺序处理。 相比其他类型消息，顺序消息在发送、存储和投递的处理过程中，更多强调多条消息间的先后顺序关系

RocketMQ 顺序消息的顺序关系通过消息组（MessageGroup）判定和识别，发送顺序消息时需要为每条消息设置归属的消息组，相同消息组的多条消息之间遵循先进先出的顺序关系，不同消息组、无消息组的消息之间不涉及顺序性

基于消息组的顺序判定逻辑，支持按照业务逻辑做细粒度拆分，可以在满足业务局部顺序的前提下提高系统的并行度和吞吐能力

<br>

**生产者**

```JAVA
// 选择消息队列（示例：按订单 ID 哈希分配到同一队列）
MessageQueueSelector selector = (mqs, msg, arg) -> {
    Long orderId = (Long) arg;
    int index = orderId.intValue() % mqs.size();
    return mqs.get(index);
};
producer.send(msg, selector, orderId); // orderId 为业务键
```

**消费者**

```JAVA
consumer.setMessageModel(MessageModel.CLUSTERING);
consumer.registerMessageListener(new MessageListenerOrderly() {
    @Override
    public ConsumeOrderlyStatus consumeMessage(List<MessageExt> msgs, ConsumeOrderlyContext context) {
        // 单线程处理消息
        for (MessageExt msg : msgs) {
            // 业务逻辑
        }
        return ConsumeOrderlyStatus.SUCCESS;
    }
});
```

<br>

需要注意的是：

* 理解全局有序和局部有序，大部分业务场景下，需要的是局部有序，如果要保持全局有序，就只保留一个 MessageQueue，这样会非常影响性能
* 生产者尽可能将有序消息打散到不同的 MessageQueue 上，避免过于集中导致数据热点竞争
* 消费者端只进行有限次数的重试，如果一条消息处理失败，RocketMQ 会将后续消息阻塞住，让消费者进行重试。如果消费者一直重试失败，超出最大重试次数，RocketMQ 就会跳过这一条消息，处理后面的消息，就会造成消息乱序
* 如果在处理消息的过程中出现逻辑错误，不建议抛出异常，可以返回 ConsumeOrderlyStatus.SUSPEND_CURRENT_QUEUE_A_MOMENT 作为替代



------

## 事务消息



以电商交易场景为例，用户支付订单这一核心操作的同时会涉及到下游物流发货、积分变更、购物车状态清空等多个子系统的变更

**XA 协议事务**

为了保证上述四个分支的执行结果一致性，典型方案是基于 XA 协议的分布式事务系统来实现。将四个调用分支封装成包含四个独立事务分支的大事务。基于 XA 分布式事务的方案可以满足业务处理结果的正确性，但最大的缺点是多分支环境下资源锁定范围大，并发度低，随着下游分支的增加，系统性能会越来越差

<br>

**基于普通消息**

将上述基于 XA 事务的方案进行简化，将订单系统变更作为本地事务，剩下的系统变更作为普通消息的下游来执行，事务分支简化成普通消息+订单表事务，充分利用消息异步化的能力缩短链路，提高并发度

![image-20250315224423110](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250315224423110.png)

该方案中消息下游分支和订单系统变更的主分支很容易出现不一致的现象，例如：

* 消息发送成功，订单没有执行成功，需要回滚整个事务
* 订单执行成功，消息没有发送成功，需要额外补偿才能发现不一致
* 消息发送超时未知，此时无法判断需要回滚订单还是提交订单变更

<br>

**基于事务消息**

上述普通消息方案中，普通消息和订单事务无法保证一致的原因，本质上是由于普通消息无法像单机数据库事务一样，具备提交、回滚和统一协调的能力

而基于 Apache RocketMQ 实现的分布式事务消息功能，在普通消息基础上，支持二阶段的提交能力。将二阶段提交和本地事务绑定，实现全局提交结果的一致性

![image-20250315225002963](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250315225002963.png)



**事务消息流程**

![image-20250315225702843](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250315225702843.png)

1. 生产者将消息发送至 RocketMQ 服务端
2. RocketMQ 服务端将消息持久化成功之后，向生产者返回 Ack 确认消息已经发送成功，此时消息被标记为"暂不能投递"，这种状态下的消息即为半事务消息
3. 生产者开始执行本地事务逻辑
4. 生产者根据本地事务执行结果向服务端提交二次确认结果（Commit 或是 Rollback），根据 Commit 或 Rollback 决定是否将消息投递给消费者
5. 在断网或者是生产者应用重启的特殊情况下，若服务端未收到发送者提交的二次确认结果，或服务端收到的二次确认结果为 Unknown 未知状态，经过固定时间后，服务端将对消息生产者即生产者集群中任一生产者实例发起消息回查
6. 生产者收到消息回查后，需要检查对应消息的本地事务执行的最终结果
7. 生产者根据检查到的本地事务的最终状态再次提交二次确认，服务端仍按照步骤4对半事务消息进行处理

<br>

**事务监听器**

```JAVA
public class TransactionListenerImpl implements TransactionListener {
    // 用于记录事务状态，模拟本地事务执行结果
    private AtomicInteger transactionIndex = new AtomicInteger(0);
    private ConcurrentHashMap<String, Integer> localTrans = new ConcurrentHashMap<>();

    /**
     * 执行本地事务
     * @param msg 待发送的消息
     * @param arg 额外参数
     * @return 本地事务执行状态
     */
    @Override
    public LocalTransactionState executeLocalTransaction(Message msg, Object arg) {
        int value = transactionIndex.getAndIncrement();
        int status = value % 3;
        localTrans.put(msg.getTransactionId(), status);
        switch (status) {
            case 0:
                // 模拟本地事务提交
                System.out.printf("%s: 本地事务提交，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
                return LocalTransactionState.COMMIT_MESSAGE;
            case 1:
                // 模拟本地事务回滚
                System.out.printf("%s: 本地事务回滚，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
                return LocalTransactionState.ROLLBACK_MESSAGE;
            default:
                // 模拟本地事务未知状态
                System.out.printf("%s: 本地事务状态未知，等待回查，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
                return LocalTransactionState.UNKNOW;
        }
    }

    /**
     * 事务状态回查方法
     * @param msg 待检查的消息
     * @return 事务最终状态
     */
    @Override
    public LocalTransactionState checkLocalTransaction(MessageExt msg) {
        Integer status = localTrans.get(msg.getTransactionId());
        if (null != status) {
            switch (status) {
                case 0:
                    System.out.printf("%s: 回查结果 - 本地事务已提交，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
                    return LocalTransactionState.COMMIT_MESSAGE;
                case 1:
                    System.out.printf("%s: 回查结果 - 本地事务已回滚，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
                    return LocalTransactionState.ROLLBACK_MESSAGE;
                default:
                    System.out.printf("%s: 回查结果 - 本地事务仍未知，继续等待，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
                    return LocalTransactionState.UNKNOW;
            }
        }
        System.out.printf("%s: 回查结果 - 未找到事务记录，消息 ID: %s %n", Thread.currentThread().getName(), msg.getTransactionId());
        return LocalTransactionState.COMMIT_MESSAGE;
    }
}
```

<br>

**生产者**

```JAVA
public class TransactionProducer {
    public static void main(String[] args) throws MQClientException, InterruptedException {
        // 创建事务消息生产者
        TransactionMQProducer producer = new TransactionMQProducer("TransactionProducerGroup");
        // 设置 NameServer 地址
        producer.setNamesrvAddr("localhost:9876");

        // 创建线程池用于异步执行本地事务
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        producer.setExecutorService(executorService);

        // 设置事务监听器
        TransactionListener transactionListener = new TransactionListenerImpl();
        producer.setTransactionListener(transactionListener);

        // 启动生产者
        producer.start();

        String[] tags = new String[]{"TagA", "TagB", "TagC"};
        for (int i = 0; i < 3; i++) {
            try {
                Message msg = new Message("TransactionTopic", tags[i % tags.length], "KEY" + i,
                        ("Hello RocketMQ " + i).getBytes(StandardCharsets.UTF_8));
                // 发送事务消息
                SendResult sendResult = producer.sendMessageInTransaction(msg, null);
                System.out.printf("%s: 消息发送结果 - %s %n", Thread.currentThread().getName(), sendResult);
                Thread.sleep(10);
            } catch (MQClientException | InterruptedException e) {
                e.printStackTrace();
            }
        }

        // 等待一段时间，确保事务回查完成
        Thread.sleep(10000);
        // 关闭生产者
        producer.shutdown();
    }
}
```

<br>

**消费者**

```JAVA
public class TransactionConsumer {
    public static void main(String[] args) throws InterruptedException, MQClientException {
        // 创建消费者实例
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("TransactionConsumerGroup");
        // 设置 NameServer 地址
        consumer.setNamesrvAddr("localhost:9876");
        // 订阅主题
        consumer.subscribe("TransactionTopic", "*");

        // 注册消息监听器
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
                for (MessageExt msg : msgs) {
                    System.out.printf("%s: 收到消息 - 主题: %s, 消息内容: %s %n", Thread.currentThread().getName(), msg.getTopic(), new String(msg.getBody()));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });

        // 启动消费者
        consumer.start();
        System.out.printf("消费者已启动 %n");
    }
}
```



------

## 消息发送重试



RocketMQ 客户端连接服务端发起消息发送请求时，可能会因为网络故障、服务异常等原因导致调用失败。为保证消息的可靠性，RocketMQ 在客户端 SDK 中内置请求重试逻辑，尝试通过重试发送达到最终调用成功的效果

同步发送和异步发送模式均支持消息发送重试

<br>

触发消息发送重试机制的条件如下：

* 客户端消息发送请求调用失败或请求超时
* 网络异常造成连接失败或请求超时
* 服务端节点处于重启或下线等状态造成连接失败
* 服务端运行慢造成请求超时
* 服务端返回失败错误码

<br>

生产者在初始化时设置消息发送最大重试次数，当出现上述触发条件的场景时，生产者客户端会按照设置的重试次数一直重试发送消息，直到消息发送成功或达到最大重试次数重试结束，并在最后一次重试失败后返回调用错误响应

* 同步发送：调用线程会一直阻塞，直到某次重试成功或最终重试失败，抛出错误码和异常
* 异步发送：调用线程不会阻塞，但调用结果会通过异常事件或者成功事件返回

<br>

除服务端返回系统流控错误场景，其他触发条件触发重试后，均会立即进行重试，无等待间隔

若由于服务端返回流控错误触发重试，系统会按照指数退避策略进行延迟重试。指数退避算法通过以下参数控制重试行为：

* INITIAL_BACKOFF： 第一次失败重试前后需等待多久，默认值：1 秒
* MULTIPLIER ：指数退避因子，即退避倍率，默认值：1.6
* JITTER ：随机抖动因子，默认值：0.2
* MAX_BACKOFF ：等待间隔时间上限，默认值：120 秒
* MIN_CONNECT_TIMEOUT ：最短重试间隔，默认值：20秒

<br>

建议算法如下：

```
ConnectWithBackoff()
  current_backoff = INITIAL_BACKOFF
  current_deadline = now() + INITIAL_BACKOFF
  while (TryConnect(Max(current_deadline, now() + MIN_CONNECT_TIMEOUT))!= SUCCESS)
    SleepUntil(current_deadline)
    current_backoff = Min(current_backoff * MULTIPLIER, MAX_BACKOFF)
    current_deadline = now() + current_backoff + UniformRandom(-JITTER * current_backoff, JITTER * current_backoff)
```



------

## 消息流控机制



消息流控指的是系统容量或水位过高，RocketMQ 服务端会通过快速失败返回流控错误来避免底层资源承受过高压力

<br>

RocketMQ 的消息流控触发条件如下：

* 存储压力大：消费者分组的初始消费位点为当前队列的最大消费位点。若某些场景例如业务上新等需要回溯到指定时刻前开始消费，此时队列的存储压力会瞬间飙升，触发消息流控
* 服务端请求任务排队溢出：若消费者消费能力不足，导致队列中有大量堆积消息，当堆积消息超过一定数量后会触发消息流控，减少下游消费系统压力

<br>

当系统触发消息发送流控时，客户端会收到系统限流错误和异常，错误码信息如下：

* reply-code：530
* reply-text：TOO_MANY_REQUESTS

客户端收到系统流控错误码后，会根据指数退避策略进行消息发送重试

<br>

触发限流的根本原因是系统容量或水位过高，可以利用可观测性功能监控系统水位容量等，保证底层资源充足，避免触发流控机制

如果因为突发原因触发消息流控，且客户端内置的重试流程执行失败，则建议业务方将请求调用临时替换到其他系统进行应急处理



------

## 消息过滤



过滤的含义指的是将符合条件的消息投递给消费者，而不是将匹配到的消息过滤掉。RocketMQ 的消息过滤功能通过生产者和消费者对消息的属性、标签进行定义，并在 Apache RocketMQ 服务端根据过滤条件进行筛选匹配，将符合条件的消息投递给消费者进行消费

![image-20250319215831113](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250319215831113.png)

消息过滤主要通过以下几个关键流程实现：

* 生产者：生产者在初始化消息时预先为消息设置一些属性和标签，用于后续消费时指定过滤目标
* 消费者：消费者在初始化及后续消费流程中通过调用订阅关系注册接口，向服务端上报需要订阅指定主题的哪些消息，即过滤条件
* 服务端：消费者获取消息时会触发服务端的动态过滤计算，RocketMQ 服务端根据消费者上报的过滤条件的表达式进行匹配，并将符合条件的消息投递给消费者

<br>

RocketMQ 支持 Tag 标签过滤和 SQL 属性过滤，这两种过滤方式对比如下：

| **对比项** | Tag 标签过滤                   | SQL 属性过滤                                                 |
| ---------- | ------------------------------ | ------------------------------------------------------------ |
| 过滤目标   | 消息的 Tag 标签                | 消息的属性，包括用户自定义属性以及系统属性（Tag 是一种系统属性） |
| 过滤能力   | 精准匹配                       | SQL 语法匹配                                                 |
| 适用场景   | 简单过滤场景、计算逻辑简单轻量 | 复杂过滤场景、计算逻辑较复杂                                 |

<br>

**标签过滤示例**

* 发送消息，设置 Tag 标签

  ```JAVA
  Message message = messageBuilder.setTopic("topic")
  //设置消息索引键，可根据关键字精确查找某条消息。
  .setKeys("messageKey")
  //设置消息Tag，用于消费端根据指定Tag过滤消息。
  //该示例表示消息的Tag设置为"TagA"。
  .setTag("TagA")
  //消息体。
  .setBody("messageBody".getBytes())
  .build();
  ```

* 订阅消息，匹配单个 Tag 标签

  ```JAVA
  String topic = "Your Topic";
  //只订阅消息标签为"TagA"的消息。
  FilterExpression filterExpression = new FilterExpression("TagA", FilterExpressionType.TAG);
  pushConsumer.subscribe(topic, filterExpression);
  ```

* 订阅消息，匹配多个 Tag 标签

  ```JAVA
  String topic = "Your Topic";
  //只订阅消息标签为"TagA"、"TagB"或"TagC"的消息。
  FilterExpression filterExpression = new FilterExpression("TagA||TagB||TagC", FilterExpressionType.TAG);
  pushConsumer.subscribe(topic, filterExpression);
  ```

* 订阅消息，匹配 Topic 中的所有消息，不进行过滤

  ```JAVA
  String topic = "Your Topic";
  //使用Tag标签过滤消息，订阅所有消息。
  FilterExpression filterExpression = new FilterExpression("*", FilterExpressionType.TAG);
  pushConsumer.subscribe(topic, filterExpression);
  ```

<br>

**SQL 属性过滤**

* 发送消息，同时设置消息 Tag 标签和自定义属性

  ```JAVA
  Message message = messageBuilder.setTopic("topic")
  //设置消息索引键，可根据关键字精确查找某条消息。
  .setKeys("messageKey")
  //设置消息Tag，用于消费端根据指定Tag过滤消息。
  //该示例表示消息的Tag设置为"messageTag"。
  .setTag("messageTag")
  //消息也可以设置自定义的分类属性，例如环境标签、地域、逻辑分支。
  //该示例表示为消息自定义一个属性，该属性为地域，属性值为杭州。
  .addProperty("Region", "Hangzhou")
  //消息体。
  .setBody("messageBody".getBytes())
  .build();
  ```

* 订阅消息，根据单个自定义属性匹配消息

  ```JAVA
  String topic = "topic";
  //只订阅地域属性为杭州的消息。
  FilterExpression filterExpression = new FilterExpression("Region IS NOT NULL AND Region='Hangzhou'", FilterExpressionType.SQL92);
  simpleConsumer.subscribe(topic, filterExpression);
  ```

* 订阅消息，同时根据多个自定义属性匹配消息

  ```JAVA
  String topic = "topic";
  //只订阅地域属性为杭州且价格属性大于30的消息。
  FilterExpression filterExpression = new FilterExpression("Region IS NOT NULL AND price IS NOT NULL AND Region = 'Hangzhou' AND price > 30", FilterExpressionType.SQL92);
  simpleConsumer.subscribe(topic, filterExpression);
  ```

* 订阅消息，匹配 Topic 中的所有消息，不进行过滤

  ```JAVA
  String topic = "topic";
  //订阅所有消息。
  FilterExpression filterExpression = new FilterExpression("True", FilterExpressionType.SQL92);
  simpleConsumer.subscribe(topic, filterExpression);
  ```

<br>

SQL 语法规范如下：

![image-20250319220508858](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250319220508858.png)



------

## 消息负载均衡



RocketMQ 领域模型中，同一条消息支持被多个消费者分组订阅，同时，对于每个消费者分组可以初始化多个消费者。您可以根据消费者分组和消费者的不同组合，实现以下两种不同的消费效果：

![image-20250324224644463](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250324224644463.png)

* 消费组间广播消费 ：如上图所示，每个消费者分组只初始化唯一一个消费者，每个消费者可消费到消费者分组内所有的消息，各消费者分组都订阅相同的消息，以此实现单客户端级别的广播一对多推送效果

  该方式一般可用于网关推送、配置推送等场景

* 消费组内共享消费 ：如上图所示，每个消费者分组下初始化了多个消费者，这些消费者共同分担消费者分组内的所有消息，实现消费者分组内流量的水平拆分和均衡负载

  该方式一般可用于微服务解耦场景

<br>

消费组间广播消费场景下，每个消费者分组内只有一个消费者，因此不涉及消费者的负载均衡

消费组内共享消费场景下，消费者分组内多个消费者共同分担消息，消息按照哪种逻辑分配给哪个消费者，就是由消费者负载均衡策略所决定的

根据消费者类型的不同，消费者负载均衡策略分为以下两种模式：

* 消息粒度负载均衡：PushConsumer 和 SimpleConsumer 默认负载策略
* 队列粒度负载均衡：PullConsumer 默认负载策略

<br>

![image-20250324230024215](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250324230024215.png)

消息粒度负载均衡策略中，同一消费者分组内的多个消费者将按照消息粒度平均分摊主题中的所有消息，即同一个队列中的消息，可被平均分配给多个消费者共同消费

消息粒度的负载均衡机制，是基于内部的单条消息确认语义实现的。消费者获取某条消息后，服务端会将该消息加锁，保证这条消息对其他消费者不可见，直到该消息消费成功或消费超时。因此，即使多个消费者同时消费同一队列的消息，服务端也可保证消息不会被多个消费者重复消费

在顺序消息中，消息的顺序性指的是同一消息组内的多个消息之间的先后顺序。因此，顺序消息场景下，消息粒度负载均衡策略还需要保证同一消息组内的消息，按照服务端存储的先后顺序进行消费。不同消费者处理同一个消息组内的消息时，会严格按照先后顺序锁定消息状态，确保同一消息组的消息串行消费

<br>

![image-20250324230114071](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250324230114071.png)

队列粒度负载均衡策略中，同一消费者分组内的多个消费者将按照队列粒度消费消息，即每个队列仅被一个消费者消费

队列粒度的负载均衡，基于队列数量、消费者数量等运行数据进行统一的算法分配，将每个队列绑定到特定的消费者，然后每个消费者按照取消息>提交消费位点>持久化消费位点的消费语义处理消息，取消息过程不提交消费状态，因此，为了避免消息被多个消费者重复消费，每个队列仅支持被一个消费者消费



------

## 消费进度管理



RocketMQ 主题和队列的定义，消息是按到达服务端的先后顺序存储在指定主题的多个队列中，每条消息在队列中都有一个唯一的 Long 类型坐标，这个坐标被定义为消息位点

任意一个消息队列在逻辑上都是无限存储，即消息位点会从 0 到 Long.MAX 无限增加。通过主题、队列和位点就可以定位任意一条消息的位置，具体关系如下图所示：

![image-20250324235243309](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250324235243309.png)

RocketMQ 定义队列中最早一条消息的位点为最小消息位点（MinOffset）；最新一条消息的位点为最大消息位点（MaxOffset）。虽然消息队列逻辑上是无限存储，但由于服务端物理节点的存储空间有限，RocketMQ 会滚动删除队列中存储最早的消息。因此，消息的最小消费位点和最大消费位点会一直递增变化

<br>

RocketMQ 领域模型为发布订阅模式，每个主题的队列都可以被多个消费者分组订阅。若某条消息被某个消费者消费后直接被删除，则其他订阅了该主题的消费者将无法消费该消息

RocketMQ 领域模型为发布订阅模式，每个主题的队列都可以被多个消费者分组订阅。若某条消息被某个消费者消费后直接被删除，则其他订阅了该主题的消费者将无法消费该消息

当消费者客户端离线，又再次重新上线时，会严格按照服务端保存的消费进度继续处理消息。如果服务端保存的历史位点信息已过期被删除，此时消费位点向前移动至服务端存储的最小位点



![image-20250324235353225](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250324235353225.png)

* ConsumerOffset ≤ MaxOffset：
  * 当消费速度和生产速度一致，且全部消息都处理完成时，最大消息位点和消费位点相同，即ConsumerOffset = MaxOffset
  * 当消费速度较慢小于生产速度时，队列中会有部分消息未消费，此时消费位点小于最大消息位点，即ConsumerOffset < MaxOffset，两者之差就是该队列中堆积的消息量
* ConsumerOffset ≥ MinOffset：正常情况下有效的消费位点 ConsumerOffset 必然大于等于最小消息位点 MinOffset。消费位点小于最小消息位点时是无效的，相当于消费者要消费的消息已经从队列中删除了，是无法消费到的，此时服务端会将消费位点强制纠正到合法的消息位点

<br>

若消费者分组的初始消费位点或当前消费位点不符合您的业务预期，可以通过重置消费位点调整您的消费进度



------

## 消费重试



RocketMQ 的消费重试主要解决的是业务处理逻辑失败导致的消费完整性问题，是一种为业务兜底的策略，不应该被用做业务流程控制。建议以下消费失败场景使用重试机制：

推荐使用消息重试场景如下：

* 业务处理失败，且失败原因跟当前的消息内容相关，比如该消息对应的事务状态还未获取到，预期一段时间后可执行成功
* 消费失败的原因不会导致连续性，即当前消息消费失败是一个小概率事件，不是常态化的失败，后面的消息大概率会消费成功。此时可以对当前消息进行重试，避免进程阻塞

典型错误使用场景如下：

- 消费处理逻辑中使用消费失败来做条件判断的结果分流，是不合理的，因为处理逻辑已经预见了一定会大量出现该判断分支
- 消费处理中使用消费失败来做处理速率限流，是不合理的。限流的目的是将超出流量的消息暂时堆积在队列中达到削峰的作用，而不是让消息进入重试链路

<br>

消费重试指的是，消费者在消费某条消息失败后，RocketMQ 服务端会根据重试策略重新消费该消息，超过一定次数后若还未消费成功，则该消息将不再继续重试，直接被发送到死信队列中

消费重试的触发条件：

* 消费失败，包括消费者返回消息失败状态标识或抛出非预期异常
* 消息处理超时，包括在 PushConsumer 中排队超时

消息重试策略主要行为：

* 重试过程状态机：控制消息在重试流程中的状态和变化逻辑
* 重试间隔：上一次消费失败或超时后，下次重新尝试消费的间隔时间
* 最大重试次数：消息可被重试消费的最大次数

<br>

无序消息（非顺序消息）：重试间隔为阶梯时间，具体时间如下：

![image-20250327151803422](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250327151803422.png)

顺序消息：重试间隔为固定时间，可通过参数设置



------

## 消息存储与清理机制



RocketMQ 使用存储时长作为消息存储的依据，即每个节点对外承诺消息的存储时长。在存储时长范围内的消息都会被保留，无论消息是否被消费；超过时长限制的消息则会被清理掉

消息存储机制主要定义以下关键问题：

- 消息存储管理粒度：RocketMQ 按存储节点管理消息的存储时长，并不是按照主题或队列粒度来管理
- 消息存储判断依据：消息存储按照存储时间作为判断依据，相对于消息数量、消息大小等条件，使用存储时间作为判断依据，更利于业务方对消息数据的价值进行评估
- 消息存储和是否消费状态无关：RocketMQ 的消息存储是按照消息的生产时间计算，和消息是否被消费无关。按照统一的计算策略可以有效地简化存储机制

<br>

RocketMQ 按照服务端节点粒度管理存储时长而非队列或主题，原因如下：

* 消息存储优势权衡：RocketMQ 基于统一的物理日志队列和轻量化逻辑队列的二级组织方式，管理物理数据。这种机制可以带来顺序读写、高吞吐、高性能等优势，但缺点是不支持按主题和队列单独管理
* 安全生产和容量保障风险要求：即使 RocketMQ 按照主题或者队列独立生成存储文件，但存储层本质还是共享存储介质。单独根据主题或队列控制存储时长，这种方式看似更灵活，但实际上整个集群仍然存在容量风险，可能会导致存储时长 SLA 被打破。从安全生产角度考虑，最合理的方式是将不同存储时长的消息通过不同集群进行分离治理

<br>

在 RocketMQ中，消息保存时长并不能完整控制消息的实际保存时间，因为消息存储仍然使用本地磁盘，本地磁盘空间不足时，为保证服务稳定性消息仍然会被强制清理，导致消息的实际保存时长小于设置的保存时长



------

# SpringBoot 整合

## 依赖和配置



在 pom.xml 文件里添加 RocketMQ 和 Spring Boot 的相关依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.rocketmq</groupId>
        <artifactId>rocketmq-spring-boot-starter</artifactId>
        <version>2.2.2</version>
    </dependency>
</dependencies>
```

在 application.properties 或者 application.yml 中配置 RocketMQ 的相关信息：

```properties
rocketmq.name-server=127.0.0.1:9876
rocketmq.producer.group=my-group
```



------

## 普通消息



发送普通消息

```java
@Autowired
private RocketMQTemplate rocketMQTemplate;

public void sendMessage(String topic, String message) {
    rocketMQTemplate.convertAndSend(topic, message);
}
```

<br>

接收普通消息

```JAVA
@Service
@RocketMQMessageListener(topic = "test-topic", consumerGroup = "my-group")
public class MessageListener implements RocketMQListener<String> {

    @Override
    public void onMessage(String message) {
        System.out.println("Received message: " + message);
    }
}    
```



------

## 定时/延时消息



发送延时级别为 5 的消息

```JAVA
// 使用同步发送方式
rocketMQTemplate.syncSend("topic:tag", MessageBuilder.withPayload("延时级别5的消息").build(), 5000, 5);

// 或通过消息头设置
Message<String> message = MessageBuilder.withPayload("延时级别5的消息")
        .setHeader(RocketMQHeaders.DELAY, 5)
        .build();
rocketMQTemplate.send("topic:tag", message);
```

<br>

发送 7 秒后收到的消息

RocketMQ 4.x 版本不支持自定义秒级延迟，但 RocketMQ 5.0+ 支持定时消息功能

```JAVA
long delayTime = System.currentTimeMillis() + 7000; // 当前时间 +7秒
Message<String> msg = MessageBuilder.withPayload("7秒后投递的消息")
        .setHeader(RocketMQHeaders.DELIVERY_TIMESTAMP, delayTime)
        .build();
rocketMQTemplate.send("topic:tag", msg);
```

<br>

发送今晚八点收到的消息

```JAVA
// 计算今晚八点的时间戳
LocalDateTime eightPM = LocalDateTime.now()
        .withHour(20)
        .withMinute(0)
        .withSecond(0)
        .withNano(0);
if (eightPM.isBefore(LocalDateTime.now())) {
    eightPM = eightPM.plusDays(1); // 若已过八点，改为明天
}
long timestamp = eightPM.atZone(ZoneId.systemDefault()).toInstant().toEpochMilli();

// 发送消息
Message<String> message = MessageBuilder.withPayload("今晚八点投递的消息")
        .setHeader(RocketMQHeaders.DELIVERY_TIMESTAMP, timestamp)
        .build();
rocketMQTemplate.send("topic:tag", message);
```

<br>

接收消息

```JAVA
@Service
@RocketMQMessageListener(topic = "delay-topic", consumerGroup = "my-consumer-group")
public class RocketMQConsumer implements RocketMQListener<String> {

    @Override
    public void onMessage(String message) {
        System.out.println("收到消息: " + message);
    }
}
```



------

## 批量消息



同步批量发送

```JAVA
@Autowired
private RocketMQTemplate rocketMQTemplate;

public void sendBatchMessages() {
    List<Message<String>> messages = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        String payload = "Batch Message " + i;
        Message<String> message = MessageBuilder.withPayload(payload).build();
        messages.add(message);
    }
    
    // 同步批量发送（阻塞直到收到Broker响应）
    SendResult sendResult = rocketMQTemplate.syncSend("batch-topic", messages);
    System.out.println("批量发送结果：" + sendResult);
}
```

<br>

异步批量发送

```JAVA
public void sendAsyncBatchMessages() {
    List<Message<String>> messages = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        messages.add(MessageBuilder.withPayload("Async Batch " + i).build());
    }

    // 异步批量发送（非阻塞）
    rocketMQTemplate.asyncSend("batch-topic", messages, new SendCallback() {
        @Override
        public void onSuccess(SendResult sendResult) {
            System.out.println("异步批量发送成功：" + sendResult);
        }

        @Override
        public void onException(Throwable throwable) {
            System.err.println("异步批量发送失败：" + throwable.getMessage());
        }
    });
}
```



------

## 顺序消息



发送消息

```JAVA
@Service
public class OrderProducer {

    @Autowired
    private RocketMQTemplate rocketMQTemplate;

    public void sendOrderMessage(String orderId, String messageBody) {
        // 发送顺序消息
        rocketMQTemplate.syncSendOrderly(
            "order-topic", 
            MessageBuilder.withPayload(messageBody).build(),
            orderId,  // 业务ID（用于哈希选择队列）
            2000      // 发送超时时间（毫秒）
        );
    }
}
```

<br>

还可以自定义路由规则

```JAVA
rocketMQTemplate.setMessageQueueSelector(new MessageQueueSelector() {
    @Override
    public MessageQueue select(List<MessageQueue> queues, org.apache.rocketmq.common.message.Message msg, Object arg) {
        String orderId = (String) arg;
        int index = Math.abs(orderId.hashCode()) % queues.size();
        return queues.get(index);
    }
});
```

<br>

接收消息

```JAVA
@Service
@RocketMQMessageListener(
    topic = "order-topic",
    consumerGroup = "order-consumer-group",
    consumeMode = ConsumeMode.ORDERLY  // 关键配置：顺序消费模式
)
public class OrderConsumer implements RocketMQListener<String> {

    @Override
    public void onMessage(String message) {
        // 处理消息（确保业务逻辑幂等）
        System.out.println("顺序消费消息: " + message);
    }
}
```



------

## 事务消息



事务消息生产者

```JAVA
@Service
public class TransactionProducerService {

    @Autowired
    private RocketMQTemplate rocketMQTemplate;

    public void sendTransactionMessage(String messageBody) {
        // 创建事务监听器
        TransactionListener transactionListener = new TransactionListener() {
            @Override
            public LocalTransactionState executeLocalTransaction(Message msg, Object arg) {
                try {
                    // 执行本地事务，例如数据库操作
                    performLocalTransaction((String) arg);
                    return LocalTransactionState.COMMIT_MESSAGE;
                } catch (Exception e) {
                    // 回滚事务
                    return LocalTransactionState.ROLLBACK_MESSAGE;
                }
            }

            @Override
            public void commitMessage(Message msg, Object arg) {
                // 提交事务后的处理
                System.out.println("事务提交成功");
            }

            @Override
            public void rollbackMessage(Message msg, Object arg) {
                // 回滚事务后的处理
                System.out.println("事务回滚");
            }
        };

        // 发送事务消息
        rocketMQTemplate.send("transaction-topic", messageBody, transactionListener, 3000);
    }

    private void performLocalTransaction(String arg) {
        // 模拟本地事务，例如数据库操作
        System.out.println("执行本地事务： " + arg);
        // 假设执行成功
        // 如果失败，可以抛出异常
    }
}
```

<br>

消费者

```JAVA
@Service
@RocketMQMessageListener(topic = "transaction-topic", consumerGroup = "transaction-consumer-group")
public class TransactionConsumer implements RocketMQListener<String> {

    @Override
    public void onMessage(String message) {
        System.out.println("收到事务消息: " + message);
        // 处理消息
    }
}
```



------

# 最佳实践

## 发送消息注意事项



**Tag 的使用**

一个应用尽可能用一个 Topic，而消息子类型则可以用 tags 来标识。tags 可以由应用自由设置，只有生产者在发送消息设置了 tags，消费方在订阅消息时才可以利用 tags 通过 broker 做消息过滤，5.x SDK 可以调用 messageBuilder.setTag("messageTag")，历史版本可以调用 message.setTags("messageTag")

<br>

**Keys 的使用**

每个消息在业务层面一般建议映射到业务的唯一标识并设置到 keys 字段，方便将来定位消息丢失问题。服务器会为每个消息创建索引（哈希索引），应用可以通过 topic、key 来查询这条消息内容，以及消息被谁消费。由于是哈希索引，请务必保证 key 尽可能唯一，这样可以避免潜在的哈希冲突。常见的设置策略使用订单 Id、用户 Id、请求 Id 等比较离散的唯一标识来处理

<br>

**日志的打印**

消息发送成功或者失败要打印消息日志，用于业务排查问题。Send 消息方法只要不抛异常，就代表发送成功



------

## 消息发送失败处理方式



Producer 的 send 方法本身支持内部重试

以上策略也是在一定程度上保证了消息可以发送成功。如果业务要求消息发送不能丢，仍然需要对可能出现的异常做兜底，比如调用 send 同步方法发送失败时，则尝试将消息存储到 db，然后由后台线程定时重试，确保消息一定到达 Broker

上述 DB 重试方式没有集成到 MQ 客户端内部做，而是要求应用自己去完成，主要基于以下几点考虑：首先，MQ 的客户端设计为无状态模式，方便任意的水平扩展，且对机器资源的消耗仅仅是 cpu、内存、网络。其次，如果 MQ 客户端内部集成一个 KV 存储模块，那么数据只有同步落盘才能较可靠，而同步落盘本身性能开销较大，所以通常会采用异步落盘，又由于应用关闭过程不受 MQ 运维人员控制，可能经常会发生 kill -9 这样暴力方式关闭，造成数据没有及时落盘而丢失。第三，Producer 所在机器的可靠性较低，一般为虚拟机，不适合存储重要数据。综上，建议重试过程交由应用来控制



------

## 消费过程幂等



RocketMQ 无法避免消息重复（Exactly-Once），所以如果业务对消费重复非常敏感，务必要在业务层面进行去重处理。可以借助关系数据库进行去重。首先需要确定消息的唯一键，可以是 msgId，也可以是消息内容中的唯一标识字段，例如订单 Id 等。在消费之前判断唯一键是否在关系数据库中存在。如果不存在则插入，并消费，否则跳过。（实际过程要考虑原子性问题，判断是否存在可以尝试插入，如果报主键冲突，则插入失败，直接跳过）

msgId 一定是全局唯一标识符，但是实际使用中，可能会存在相同的消息有两个不同 msgId 的情况（消费者主动重发、因客户端重投机制导致的重复等），这种情况就需要使业务字段进行重复消费



------

## 消费速度慢的处理方式



**提升消费并行度**

绝大部分消息消费行为都属于 IO 密集型，即可能是操作数据库，或者调用 RPC，这类消费行为的消费速度在于后端数据库或者外系统的吞吐量，通过增加消费并行度，可以提高总的消费吞吐量，但是并行度增加到一定程度，反而会下降。所以，应用必须要设置合理的并行度。 如下有几种修改消费并行度的方法：

* 同一个 ConsumerGroup 下，通过增加 Consumer 实例数量来提高并行度。可以通过加机器，或者在已有机器启动多个进程的方式
* 提高单个 Consumer 的消费并行线程，5.x PushConsumer SDK 可以通过PushConsumerBuilder.setConsumptionThreadCount() 设置线程数，SimpleConsumer可以由业务线程自由增加并发，底层线程安全；历史版本SDK PushConsumer可以通过修改参数 consumeThreadMin、consumeThreadMax实现

<br>

**批量方式消费**

某些业务流程如果支持批量方式消费，则可以很大程度上提高消费吞吐量，例如订单扣款类应用，一次处理一个订单耗时 1 s，一次处理 10 个订单可能也只耗时 2 s，这样即可大幅度提高消费的吞吐量。建议使用 5.x SDK 的 SimpleConsumer，每次接口调用设置批次大小，一次性拉取消费多条消息

<br>

**重置位点跳过非重要消息**

发生消息堆积时，如果消费速度一直追不上发送速度，如果业务对数据要求不高的话，可以选择丢弃不重要的消息。建议使用重置位点功能直接调整消费位点到指定时刻或者指定位置



------

## 消费打印日志



如果消息量较少，建议在消费入口方法打印消息，消费耗时等，方便后续排查问题

```JAVA
new MessageListener() {
    @Override
    public ConsumeResult consume(MessageView messageView) {
        LOGGER.info("Consume message={}", messageView);
        //Do your consume process
        return ConsumeResult.SUCCESS;
        }
}
```

如果能打印每条消息消费耗时，那么在排查消费慢等线上问题时，会更方便。但如果线上环境 TPS 很高，不建议开启，避免日志太多影响性能



------

# 系统设计

## 消息持久化设计



消息持久化也就是将内存中的消息写⼊到本地磁盘的过程。⽽磁盘 IO 操作通常是⼀个很耗性能，很慢的操作，所以，对消息持久化机制的设计，是⼀个 MQ 产品提升性能的关键，甚⾄可以说是最为重要的核⼼也不为过。RocketMQ 消息直接采⽤磁盘⽂件保存消息，默认路径在 ${user_home}/store ⽬录。这些存储⽬录可以在 broker.conf 中⾃⾏指定



![image-20250525122311609](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250525122311609.png)

存储⽂件主要分为三个部分：

* CommitLog：存储消息的元数据。所有消息都会顺序存⼊到 CommitLog ⽂件当中。CommitLog 由多个⽂件组成，每个⽂件固定⼤⼩ 1G。以第⼀条消息的偏移量为⽂件名
* ConsumerQueue：存储消息在 CommitLog 的索引。⼀个 MessageQueue ⼀个⽂件，记录当前 MessageQueue 被哪些消费者组消费到了哪⼀条 CommitLog
* IndexFile：为了消息查询提供了⼀种通过 key 或时间区间来查询消息的⽅法，这种通过 IndexFile 来查找消息的⽅法不影响发送与消费消息的主流程

另外，还有⼏个辅助的存储⽂件，主要记录⼀些描述消息的元数据：

* checkpoint：数据存盘检查点。⾥⾯主要记录 commitlog ⽂件、ConsumeQueue ⽂件以及 IndexFile ⽂件最后⼀次刷盘的时间戳

* config/*.json：这些⽂件是将 RocketMQ 的⼀些关键配置信息进⾏存盘保存。例如 Topic 配置、消费者组配置、消费者组消息偏移量 Offset 等等⼀些信息

* abort：这个⽂件是 RocketMQ ⽤来判断程序是否正常关闭的⼀个标识⽂件。正常情况下，会在启动时创建，⽽关闭服务时删除。但是如果遇到⼀些服务器宕机，或者 kill -9 这样⼀些⾮正常关闭服务的情况，这个 abort ⽂件就不会删除，因此 RocketMQ 就可以判断上⼀次服务是⾮正常关闭的，后续就会做⼀些数据

  恢复的操作

<br>

整体的消息存储结构，官⽅做了个图进⾏描述：

![image-20250525123134190](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%A1%86%E6%9E%B6%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/RocketMQ%20%E6%95%99%E7%A8%8B/image-20250525123134190.png)

简单来说，Producer 发过来的所有消息，不管是属于那个 Topic，Broker 都统⼀存在 CommitLog ⽂件当中，然后分别构建 ConsumeQueue ⽂件和 IndexFile 两个索引⽂件，⽤来辅助消费者进⾏消息检索。这种设计最直接的好处是可以较少查找⽬标⽂件的时间，让消息以最快的速度落盘。对⽐ Kafka 存⽂件时，需要寻找消息所属的 Partition ⽂件，再完成写⼊。当 Topic ⽐较多时，这样的 Partition 寻址就会浪费⾮常多的时间。所以 Kafka 不太适合多 Topic 的场景。⽽ RocketMQ 的这种快速落盘的⽅式，在多 Topic 的场景下，优势就⽐
