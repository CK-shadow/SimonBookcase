---
title: RabbitMQ 从入门到实战
date: 2024-07-01 03:23:13
tags: MQ
categories: 
  - 系统架构
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/preview.jpg
---



# RabbitMQ 从入门到实战

## MQ 相关概念

### 什么是 MQ



MQ(message queue)，从字面意思上看，本质是个队列，FIFO 先入先出，只不过队列中存放的内容是 message 而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ 是一种非常常 见的上下游「逻辑解耦 + 物理解耦」的消息通信服务。使用了 MQ 之后，消息发送上游只需要依赖 MQ，不用依赖其他服务



------

### 为什么要用 MQ



**流量削峰**

举个例子，如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好



**应用解耦**

以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合  调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于  消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在  这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流  系统恢复后，继续处理订单信息即可，中单用户感受不到物流系统的故障，提升系统的可用性

![image-20240401235824839](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240401235824839.png)



**异步处理**

有些服务间调用是异步的，例如 A 调用 B，B 需要花费很长时间执行，但是 A 需要知道 B 什么时候可以执行完

以前一般有两种方式，A 过一段时间去调用 B 的查询 api 查询。或者 A 提供一个 callback api， B 执行完之后调用 api 通知 A 服务。这两种方式都不是很优雅

使用消息总线，可以很方便解决这个问题， A 调用 B 服务后，只需要监听 B 处理完成的消息，当 B 处理完成后，会发送一条消息给 MQ，MQ 会将此消息转发给 A 服务。这样 A 服务既不用循环调用 B 的查询 api，也不用提供 callback api。同样 B 服务也不用 做这些操作。A 服务还能及时的得到异步处理成功的消息

![image-20240401235911770](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240401235911770.png)



------

### 常用的 MQ



**ActiveMQ**

优点：单机吞吐量万级，时效性 ms 级，可用性高，基于主从架构实现高可用性，消息可靠性较高，有低概率丢失数据

缺点：官方社区现在对 ActiveMQ 5.x 维护越来越少，高吞吐量场景较少使用



**Kafka**

大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开 Kafka，这款为大数据而生的消息中间件，以其百万级 TPS  的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被  LinkedIn，Uber，Twitter，Netflix 等大公司所采纳

优点: 性能卓越，单机写入 TPS 约在百万条/秒，最大的优点，就是吞吐量高。时效性 ms 级，可用性非常高，kafka  是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用。消费者采用 Pull  方式获取消息，消息有序，通过控制能够保证所有消息被消费且仅被消费一次；有优秀的第三方 Kafka Web 管理界面  Kafka-Manager；在日志领域比较成熟，被多家公司和多个开源项目使用；功能较为简单，主要支持简单的 MQ  功能，在大数据领域的实时计算以及日志采集被大规模使用

缺点：Kafka 单机超过 64 个队列/分区，Load 会发生明显的飙高现象，队列越多，load 越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢



**RocketMQ**

RocketMQ 出自阿里巴巴的开源产品，用 Java 语言实现，在设计时参考了 Kafka，并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog 分发等场景

优点：单机吞吐量十万级，可用性非常高，分布式架构，消息可以做到 0 丢失，MQ 功能较为完善，还是分布式的，扩展性好,支持 10 亿级别的消息堆积，不会因为堆积导致性能下降，源码是 java 我们可以自己阅读源码，定制自己公司的 MQ

缺点：支持的客户端语言不多，目前是 java 及 c++，其中 c++ 不成熟；社区活跃度一般,没有在 MQ 核心中去实现 JMS 等接口,有些系统要迁移需要修改大量代码



**RabbitMQ**

2007 年发布，是一个在 AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一

优点：由于 erlang 语言的高并发特性，性能较好；吞吐量到万级，MQ 功能比较完备，健壮、稳定、易用、跨平台、支持多种语言  如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP 等，支持 AJAX  文档齐全；开源提供的管理界面非常棒，用起来很好用,社区活跃度高；更新频率相当高

缺点：商业版需要收费,学习成本较高



------

### MQ 的选择



**Kafka**

Kafka 主要特点是基于 Pull 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，如果有日志采集功能，肯定是首选 kafka 了



**RocketMQ**

天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RoketMQ 在稳定性上可能更值得信赖，这些业务场景在阿里双 11 已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择 RocketMQ



**RabbitMQ**

结合 erlang 语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的数据量没有那么大，中小型公司优先选择功能比较完备的 RabbitMQ



------

## RabbitMQ

### 特性



![image-20240402235829879](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240402235829879.png)



------

### 四大核心概念



* 生产者

  产生数据发送消息的程序是生产者

* 交换机

  交换机是 RabbitMQ 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定

* 队列

  队列是 RabbitMQ 内部使用的一种数据结构，尽管消息流经 RabbitMQ 和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式

* 消费者

  消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者



------

### 名词介绍



![image-20240403232612107](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240403232612107.png)



* Broker

  接收和分发消息的应用，RabbitMQ Server 就是 Message Broker

* Virtua Host

  出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 提供的服务时，可以划分出多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 等

* Connection

  publisher／consumer 和 broker 之间的 TCP 连接

* Channel

  如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection  的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 thread  创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客户端和 message broker 识别  channel，所以 channel 之间是完全隔离的。Channel 作为轻量级的 Connection 极大减少了操作系统建立 TCP  connection 的开销

* Exchange

  message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发消息到 queue  中去。常用的类型有：direct (point-to-point)，topic (publish-subscribe) 和 fanout  (multicast)

* Queue

  消息最终被送到这里等待 consumer 取走

* Binding

  exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保存到 exchange 中的查询表中，用于 message 的分发依据



------

### 安装 RabbitMQ



使用 Docker 安装 RabbitMQ 和控制台

```sh
# 安装启动 rabbitmq 容器
docker run -d --name myRabbitMQ -e RABBITMQ_DEFAULT_USER=用户名 -e RABBITMQ_DEFAULT_PASS=密码 -p 15672:15672 -p 5672:5672 rabbitmq:3.8.14-management
```



------

## 简单案例

### Hello World



![image-20240403233718410](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240403233718410.png)



**引入依赖**

```XML
<dependencies>
    <!--rabbitmq 依赖客户端-->
    <dependency>
        <groupId>com.rabbitmq</groupId>
        <artifactId>amqp-client</artifactId>
        <version>5.8.0</version>
    </dependency>
    <!--操作文件流的一个依赖-->
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.6</version>
    </dependency>
</dependencies>
```



**生产者**

```JAVA
public class Producer {
    private final static String QUEUE_NAME = "hello";

    public static void main(String[] args) throws Exception {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        factory.setUsername("admin");
        factory.setPassword("123456");
        //channel 实现了自动 close 接口 自动关闭 不需要显示关闭
        //创建连接
        Connection connection = factory.newConnection();
        //获取信道
        Channel channel = connection.createChannel();
        /**
         * 生成一个队列
         * 1.队列名称
         * 2.队列里面的消息是否持久化 也就是是否用完就删除
         * 3.该队列是否只供一个消费者进行消费 是否进行共享 true 可以多个消费者消费
         * 4.是否自动删除 最后一个消费者端开连接以后 该队列是否自动删除 true 自动删除
         * 5.其他参数
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        String message = "hello world";
        /**
         * 发送一个消息
         * 1.发送到那个交换机
         * 2.路由的 key 是哪个
         * 3.其他的参数信息
         * 4.发送消息的消息体
         */
        channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        System.out.println("消息发送完毕");

    }
    
}

```



**消费者**

```JAVA
public class Consumer {
    private final static String QUEUE_NAME = "hello";

    public static void main(String[] args) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        factory.setUsername("admin");
        factory.setPassword("123456");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        System.out.println("等待接收消息.........");

        //推送的消息如何进行消费的接口回调
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody());
            System.out.println(message);
        };
        //取消消费的一个回调接口 如在消费的时候队列被删除掉了
        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println("消息消费被中断");
        };
        /**
         * 消费者消费消息 - 接受消息
         * 1.消费哪个队列
         * 2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
         * 3.消费者未成功消费的回调
         * 4.消息被取消时的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }

}
```



------

### Work Queues



Work Queues——工作队列 (又称任务队列) 的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。 相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进 程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务

![image-20240404214605490](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240404214605490.png)



**抽取工具类**

```JAVA
public class RabbitMqUtils {
    //得到一个连接的 channel
    public static Channel getChannel() throws Exception {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        factory.setUsername("admin");
        factory.setPassword("123456");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();
        return channel;
    }
}
```



**创建两个工作队列，并且启动**

```JAVA
public class Work01 {

    //队列的名称
    public static final String QUEUE_NAME="hello";


    //接收消息
    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.getChannel();

        //消息的接受
        DeliverCallback deliverCallback = (consumerTag,message) ->{
            System.out.println("接收到的消息:"+new String(message.getBody()));
        };

        //消息接受被取消时，执行下面的内容
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag+"消息被消费者取消消费接口回调逻辑");
        };

        //消息的接受
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}
```



创建好一个工作队列，只需要以多线程方式启动两次 main 函数即可，以 first、second 区别消息队列

要开启多线程功能，首先启动该消息队列，然后如图开启多线程：

![image-20240404215110629](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240404215110629.png)



**创建一个生产者，发送消息进程**

```JAVA
public class Task01 {

    //队列名称
    public static final String QUEUE_NAME="hello";

    //发送大量消息
    public static void main(String[] args) throws IOException, TimeoutException {

        Channel channel = RabbitMQUtils.getChannel();
        //队列的声明
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        //发送消息
        //从控制台当中接受信息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();
            channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
            System.out.println("消息发送完成:"+message);
        }
    }
    
}

```



![image-20240404215154772](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240404215154772.png)



------

### 消息应答



为了保证消息在发送过程中不丢失，引入消息应答机制，消息应答就是：消费者在接收到消息并且处理该消息之后，告诉 rabbitmq 它已经处理了，rabbitmq 可以把该消息删除了



**自动应答**

消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡, 因为这种模式如果消息在接收到之前，消费者那边出现连接或者 channel 关闭，那么消息就丢失 了, 当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使 得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用在消费者可以高效并以 某种速率能够处理这些消息的情况下使用



**手动应答的方法**

* `Channel.basicAck` (肯定确认应答)

  第一个参数是消息的标记，第二个参数表示是否应用于多消息，RabbitMQ 已知道该消息并且成功的处理消息，可以将其丢弃了

  ```JAVA
  basicAck(long deliveryTag, boolean multiple);
  ```

* `Channel.basicReject` (否定确认应答)

  第一个参数表示拒绝 `deliveryTag` 对应的消息，第二个参数表示是否 `requeue`：true 则重新入队列，false 则丢弃或者进入死信队列

  该方法 reject 后，该消费者还是会消费到该条被 reject 的消息

  ```JAVA
  basicReject(long deliveryTag, boolean requeue);
  ```

* `Channel.basicNack` (用于否定确认)：示己拒绝处理该消息，可以将其丢弃了

  第一个参数表示拒绝 `deliveryTag` 对应的消息，第二个参数是表示否应用于多消息，第三个参数表示是否 `requeue`，与 basicReject 区别就是同时支持多个消息，可以 拒绝签收 该消费者先前接收未 ack 的所有消息。拒绝签收后的消息也会被自己消费到

  ```JAVA
  basicNack(long deliveryTag, boolean multiple, boolean requeue);
  ```

* `Channel.basicRecover`

  是否恢复消息到队列，参数是是否 `requeue`，true 则重新入队列，并且尽可能的将之前 `recover` 的消息投递给其他消费者消费，而不是自己再次消费。false 则消息会重新被投递给自己

  ```JAVA
  basicRecover(boolean requeue);
  ```

  

**multiple 的作用**

手动应答的好处是可以批量应答并且减少网络拥堵

true 代表批量应答 channel 上未应答的消息，比如说 channel 上有传送 tag 的消息 5,6,7,8 当前 tag 是 8 那么此时 5-8 的这些还未应答的消息都会被确认收到消息应答

false 同上面相比只会应答 tag=8 的消息 5,6,7 这三个消息依然不会被确认收到消息应答

![image-20240404223411465](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240404223411465.png)



------

### 消息自动重新入队



如果消费者由于某些原因失去连接 (其通道已关闭，连接已关闭或 TCP 连接丢失)，导致消息未发送 ACK 确认，RabbitMQ 将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息

![image-20240404224600247](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240404224600247.png)



------

### RabbitMQ 持久化



**队列持久化**

之前我们创建的队列都是非持久化的，RabbitMQ 如果重启，该队列就会被删除掉，如果要队列实现持久化需要在声明队列的时候把 durable 参数设置为 true，代表开启持久化

```JAVA
public class Task02 {

    //队列名称
    public static final String TASK_QUEUE_NAME = "ACK_QUEUE";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.getChannel();

        //开启持久化
        boolean durable = true;
        //声明队列
        channel.queueDeclare(TASK_QUEUE_NAME,durable,false,false,null);
        //在控制台中输入信息
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入信息：");
        while (scanner.hasNext()){
            String message = scanner.next();
            channel.basicPublish("",TASK_QUEUE_NAME,null,message.getBytes("UTF-8"));
            System.out.println("生产者发出消息:"+ message);
        }

    }
}
```

如果之前声明的队列不是持久化的，需要把原先队列先删除，或者重新创建一个持久化的队列，否则就会报错



使用 Spring Starter AMQP 来连接 RabbitMQ 时，可以通过配置消息队列的属性来开启队列持久化

```JAVA
@Configuration  
public class RabbitMqConfig {  
  
    @Bean  
    public Queue myDurableQueue() {  
    	// 第二个参数设置为true表示队列是持久的  
        return new Queue("myDurableQueue", true); 
    }  
}
```



**消息持久化**

需要在消息生产者发布消息的时候，开启消息的持久化

在 basicPublish 方法的第二个参数添加这个属性： `MessageProperties.PERSISTENT_TEXT_PLAIN`

将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉 RabbitMQ 将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候  但是还没有存储完，消息还在缓存的一个间隔点。此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经绰绰有余了

```JAVA
public class Task02 {

    //队列名称
    public static final String TASK_QUEUE_NAME = "ACK_QUEUE";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.getChannel();

        //队列持久化
        boolean durable = true;
        //声明队列
        channel.queueDeclare(TASK_QUEUE_NAME,durable,false,false,null);
        //在控制台中输入信息
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入信息：");
        while (scanner.hasNext()){
            String message = scanner.next();
        	//设置生产者发送消息为持久化消息(要求保存到磁盘上)
                        channel.basicPublish("",TASK_QUEUE_NAME,MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes("UTF-8"));

            System.out.println("生产者发出消息:"+ message);
        }

    }
}
```



使用 Spring Starter AMQP 来连接 RabbitMQ 时，可以通过以下方式实现

```JAVA
@Service  
public class MyMessageService {  

    private final RabbitTemplate rabbitTemplate;  

    @Autowired  
    public MyMessageService(RabbitTemplate rabbitTemplate) {  
        this.rabbitTemplate = rabbitTemplate;  
    }  

    public void sendMessage(String message) {  
        Message msg = MessageBuilder.withPayload(message)  
                .setDeliveryMode(MessageDeliveryMode.PERSISTENT) // 持久化消息  
                .build();  
        rabbitTemplate.convertAndSend("myDurableExchange", "myRoutingKey", msg);  
    }  
}
```



------

### 不公平分发



在最开始的例子 RabbitMQ 分发消息采用的轮训分发，但是在某种场景下这种策略并不是很好，比方说有两个消费者在处理任务，其中有个消费者 1 处理任务的速度非常快，而另外一个消费者 2 处理速度却很慢，这个时候我们还是采用轮训分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是 RabbitMQ 并不知道这种情况它依然很公平的进行分发

为了避免这种情况，在消费者中消费之前，我们可以设置参数 `channel.basicQos(1);`

```JAVA
//不公平分发
int prefetchCount = 1;
channel.basicQos(prefetchCount);
//采用手动应答
boolean autoAck = false;
channel.basicConsume(TASK_QUEUE_NAME, autoAck, deliverCallback, cancelCallback);
```



Spring Starter AMQP 则可以直接通过 yaml 配置文件实现

```YAML
spring:  
  rabbitmq:  
    listener:  
      simple:  
        prefetch: 1 # 设置预取计数为1，确保消息按顺序分发  
```



------

### 预取值分发



默认消息的发送是异步发送的，所以在任何时候，channel 上不止只有一个消息来自消费者的手动确认，所以本质上是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开发人员能限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。这个时候就可以通过使用 basic.qos 方法设置「预取计数」值来完成的

该值定义通道上允许的未确认消息的最大数量。一旦数量达到配置的数量， RabbitMQ 将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认

通常，增加预取将提高向消费者传递消息的速度。虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的 RAM 消耗 (随机存取存储器) 应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同 100 到 300 范围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险

预取值为 1 是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境 中。对于大多数应用来说，稍微高一点的值将是最佳的



------

## 发布确认

### 发布确认逻辑



生产者将信道设置成 confirm 模式，一旦信道进入 confirm 模式，所有在该信道上面发布的消息都将会被指派一个唯一的 ID(从 1 开始)，一旦消息被投递到所有匹配的队列之后，broker 就会发送一个确认给生产者 (包含消息的唯一 ID)，这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker 回传给生产者的确认消息中 delivery-tag 域包含了确认消息的序列号，此外 broker 也可以设置 basic.ack 的 multiple 域，表示到这个序列号之前的所有消息都已经得到了处理

confirm 模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果 RabbitMQ 因为自身内部错误导致消息丢失，就会发送一条 nack 消息， 生产者应用程序同样可以在回调方法中处理该 nack 消息



------

### 单个消息发布确认



这是一种简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布，`waitForConfirmsOrDie(long)` 这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常

这种确认方式有一个最大的缺点就是：发布速度特别的慢，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了

```JAVA
/**
 * 单个发送
 */
public static void publishMessageIndividually() throws Exception {
    Channel channel = RabbitMqUtils.getChannel();
    //队列声明
    String queueName = UUID.randomUUID().toString();
    channel.queueDeclare(queueName, true, false, false, null);
    //开启发布确认
    channel.confirmSelect();

    long begin = System.currentTimeMillis();

    for (int i = 0; i < MESSAGE_COUNT; i++) {
        String message = i + "";
        channel.basicPublish("", queueName, null, message.getBytes());
        //服务端返回 false 或超时时间内未返回，生产者可以消息重发
        boolean flag = channel.waitForConfirms();
        if (flag) {
            System.out.println("消息发送成功");
        }
    }

    long end = System.currentTimeMillis();
    System.out.println("发布" + MESSAGE_COUNT + "个单独确认消息,耗时" + (end - begin) + "ms");

}
```



如果使用的是 Spring Starter AMQP，则需要修改 RabbitTemplate

配置  `RabbitTemplate`  并设置  `ConfirmCallback`

```JAVA
@Configuration  
public class RabbitConfig {  
  
    @Bean  
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {  
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);  
        rabbitTemplate.setConfirmCallback((correlationData, ack, cause) -> {  
            if (ack) {  
                System.out.println("消息发送成功: " + correlationData.getId());  
            } else {  
                System.out.println("消息发送失败: " + cause);  
            }  
        });  
        return rabbitTemplate;  
    }  
}
```

当你使用  `RabbitTemplate`  发送消息时，需要为每个消息创建一个 `CorrelationData` 实例，这样可以在确认回调中跟踪消息

```java
@Service  
public class MessageSenderService {  
  
    @Autowired  
    private RabbitTemplate rabbitTemplate;  
  
    public void sendMessageWithConfirm(String exchange, String routingKey, String messageBody) {  
        CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());  
        Message message = MessageBuilder.withBody(messageBody.getBytes())  
                .setCorrelationId(correlationData.getId()) // 设置消息的 correlationId  
                .build();  
          
        rabbitTemplate.convertAndSend(exchange, routingKey, message, correlationData);  
    }  
}
```

`spring.rabbitmq.publisher-confirms=true` 在 Spring Boot 2.x 中是默认启用的，所以不需要显式设置它



------

### 批量消息发布确认



上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出 问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布

```JAVA
/**
 * 批量
 */
public static void publishMessageBatch() throws Exception {
    Channel channel = RabbitMqUtils.getChannel();
    //队列声明
    String queueName = UUID.randomUUID().toString();
    channel.queueDeclare(queueName, true, false, false, null);
    //开启发布确认
    channel.confirmSelect();
    //批量确认消息大小
    int batchSize = 100;
    //未确认消息个数
    int outstandingMessageCount = 0;
    long begin = System.currentTimeMillis();

    for (int i = 0; i < MESSAGE_COUNT; i++) {
        String message = i + "";
        channel.basicPublish("", queueName, null, message.getBytes());
        outstandingMessageCount++;
        if (outstandingMessageCount == batchSize) {
            channel.waitForConfirms();
            outstandingMessageCount = 0;
        }
    }
    //为了确保还有剩余没有确认消息 再次确认
    if (outstandingMessageCount > 0) {
        channel.waitForConfirms();
    }
    long end = System.currentTimeMillis();
    System.out.println("发布" + MESSAGE_COUNT + "个批量确认消息,耗时" + (end - begin) + "ms");
}
```



------

### 异步发布确认



异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说， 他是利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功

在 Spring AMQP 中实现异步消息发布确认，可以使用 `RabbitTemplate` 的 `convertAndSend` 方法，并为其提供一个 `ConfirmCallback`。这个回调将在消息被成功发送到 RabbitMQ 或发送失败时被异步调用。由于回调是异步的，它不会阻塞消息发送操作，从而实现了异步发布确认



------

## 交换机

### 简介



RabbitMQ 消息传递模型的核心思想是: 生产者生产的消息从不会直接发送到队列。实际上，通常生产者甚至都不知道这些消息传递传递到了哪些队列中

相反，生产者只能将消息发送到交换机 (exchange)，交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把他们到许多队列中还是说应该丢弃它们。这就的由交换机的类型来决定

![image-20240406233224188](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240406233224188.png)



------

### 交换机的类型



* Direct

  处理路由键。需要将一个队列绑定到交换机上，要求该消息与一个特定的路由键完全匹配。这是一个完整的匹配。如果一个队列绑定到该交换机上要求路由键  abc ，则只有被标记为 abc 的消息才被转发，不会转发 abc.def，也不会转发 dog.ghi，只会转发 abc

* Topic

  将路由键和某模式进行匹配。此时队列需要绑定要一个模式上。符号“#”匹配一个或多个词，符号 * 匹配不多不少一个词。因此 abc.# 能够匹配到 abc.def.ghi，但是 abc.* 只会匹配到 abc.def

* Headers

  不处理路由键。而是根据发送的消息内容中的headers属性进行匹配。在绑定 Queue 与 Exchange  时指定一组键值对；当消息发送到RabbitMQ 时会取到该消息的 headers 与 Exchange  绑定时指定的键值对进行匹配；如果完全匹配则消息会路由到该队列，否则不会路由到该队列。headers 属性是一个键值对，可以是  Hashtable，键值对的值可以是任何类型。而 fanout，direct，topic 的路由键都需要要字符串形式的

  匹配规则 x-match 有下列两种类型：

  * x-match = all ：表示所有的键值对都匹配才能接受到消息
  * x-match = any ：表示只要有键值对匹配就能接受到消息

* Fanout

  不处理路由键。你只需要简单的将队列绑定到交换机上。一个发送到交换机的消息都会被转发到与该交换机绑定的所有队列上。很像子网广播，每台子网内的主机都获得了一份复制的消息。Fanout 交换机转发消息是最快的



------

### 临时队列



之前的章节我们使用的是具有特定名称的队列 (还记得 hello 和 ack_queue 吗？)。队列的名称我们来说至关重要，我们需要指定我们的消费者去消费哪个队列的消息

每当我们连接到 Rabbit 时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。其次一旦我们断开了消费者的连接，队列将被自动删除

创建临时队列的方式如下:

```JAVA
String queueName = channel.queueDeclare().getQueue();
```

![image-20240407225658523](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240407225658523.png)



------

### Binding



什么是 bingding 呢，binding 其实是 exchange 和 queue 之间的桥梁，它告诉我们 exchange 和那个队列进行了绑定关系。比如说下面这张图告诉我们的就是 X 与 Q1 和 Q2 进行了绑定

![image-20240407225739856](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240407225739856.png)

![image-20240407225743978](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240407225743978.png)



------

### Fanout Exchange



Fanout 这种类型非常简单。正如从名称中猜到的那样，它是将接收到的所有消息广播到它知道的 所有队列中。系统中默认有些 exchange 类型

![image-20240407225900603](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240407225900603.png)

![image-20240407225905625](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240407225905625.png)



为了说明这种模式，将构建一个简单的日志系统。它将由两个程序组成:第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘

ReceiveLogs01 将接收到的消息打印在控制台

```JAVA
/**
 * @author zhiyuan
 */
public class ReceiveLogs01 {
    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        /**
         * 生成一个临时的队列 队列的名称是随机的
         * 当消费者断开和该队列的连接时 队列自动删除
         */
        String queueName = channel.queueDeclare().getQueue();
        //把该临时队列绑定我们的 exchange 其中 routingkey(也称之为 binding key)为空字符串
        channel.queueBind(queueName, EXCHANGE_NAME, "");
        System.out.println("等待接收消息,把接收到的消息打印在屏幕........... ");

        //发送回调
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("控制台打印接收到的消息" + message);
        };
        channel.basicConsume(queueName, true, deliverCallback, consumerTag -> {});

    }
}
```



ReceiveLogs02 把消息写出到文件

```JAVA
public class ReceiveLogs02 {
    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws Exception {

        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        /**
         * 生成一个临时的队列 队列的名称是随机的
         * 当消费者断开和该队列的连接时 队列自动删除
         */
        String queueName = channel.queueDeclare().getQueue();
        //把该临时队列绑定我们的 exchange 其中 routingkey(也称之为 binding key)为空字符串
        channel.queueBind(queueName, EXCHANGE_NAME, "");
        System.out.println("等待接收消息,把接收到的消息写到文件........... ");

        //发送回调
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            File file = new File("D:\\test\\rabbitmq_info.txt");
            FileUtils.writeStringToFile(file,message,"UTF-8");
            System.out.println("数据写入文件成功");
        };
        channel.basicConsume(queueName, true, deliverCallback, consumerTag -> {});

    }
}
```



EmitLog 发送消息给两个消费者接收：

```JAVA
public class EmitLog {
    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();

        /**
         * 声明一个 exchange
         * 1.exchange 的名称
         * 2.exchange 的类型
         */
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入信息");
        while (sc.hasNext()) {
            String message = sc.nextLine();
            channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes("UTF-8"));
            System.out.println("生产者发出消息" + message);
        }
    }
    
}
```



------

### Direct Exchange



上一节中的我们的日志系统将所有消息广播给所有消费者，对此我们想做一些改变，例如我们希望将日志消息写入磁盘的程序仅接收严重错误 (errros)，而不存储哪些警告(warning) 或信息 (info) 日志 消息避免浪费磁盘空间。Fanout 这种交换类型并不能给我们带来很大的灵活性 - 它只能进行无意识的广播，在这里我们将使用 direct 这种类型来进行替换，这种类型的工作方式是，消息只去到它绑定的 routingKey 队列中去

![image-20240407233706482](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240407233706482.png)



当生产者生产消息到 `direct_logs` 交换机里，该交换机会检测消息的 routingKey 条件，然后分配到满足条件的队列里，最后由消费者从队列消费消息



生产者

```java
public class DirectLogs {

    //交换机名称
    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.getChannel();


        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String message = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"info",null,message.getBytes("UTF-8"));
            System.out.println("生产者发出消息:"+message);
        }
    }
}
```



消费者1

```JAVA
public class ReceiveLogsDirect01 {

    public static final String EXCHANGE_NAME="direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.getChannel();
        //声明一个direct交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
        //声明一个队列
        channel.queueDeclare("console",false,false,false,null);
        channel.queueBind("console",EXCHANGE_NAME,"info");
        channel.queueBind("console",EXCHANGE_NAME,"warning");
        //接收消息
        DeliverCallback deliverCallback = (consumerTag,message) -> {
          System.out.println("ReceiveLogsDirect01控制台打印接收到的消息:"+new String(message.getBody(),"UTF-8"));
        };
        //消费者取消消息时回调接口
        channel.basicConsume("console",true,deliverCallback,consumerTag -> {});

    }
}
```



消费者2

```JAVA
public class ReceiveLogsDirect02 {

    public static final String EXCHANGE_NAME="direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.getChannel();
        //声明一个direct交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
        //声明一个队列
        channel.queueDeclare("disk",false,false,false,null);
        channel.queueBind("disk",EXCHANGE_NAME,"error");

        //接收消息
        DeliverCallback deliverCallback = (consumerTag,message) -> {
          System.out.println("ReceiveLogsDirect02控制台打印接收到的消息:"+new String(message.getBody(),"UTF-8"));
        };
        //消费者取消消息时回调接口
        channel.basicConsume("disk",true,deliverCallback,consumerTag -> {});

    }
}
```



------

### Topic Exchange



尽管使用 direct 交换机改进了我们的系统，但是它仍然存在局限性——比方说我们想接收的日志类型有 info.base 和 info.advantage，某个队列只想 info.base 的消息，那这个时候 direct 就办不到了。这个时候就只能使用 topic 类型

发送到类型是 topic 交换机的消息的 routing_key 不能随意写，必须满足一定的要求，它必须是一个单词列表，以点号分隔开。这些单词可以是任意单词

*(星号) 可以代替一个单词，#(井号) 可以替代零个或多个单词

![image-20240408230116579](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240408230116579.png)

* Q1 --> 绑定的是
  * 中间带 orange 带 3 个单词的字符串 `(*.orange.*)`
* Q2 --> 绑定的是
  * 最后一个单词是 rabbit 的 3 个单词 `(*.*.rabbit)`
  * 第一个单词是 lazy 的多个单词 `(lazy.#)`



上图是一个队列绑定关系图

| 例子                     | 说明                                       |
| ------------------------ | ------------------------------------------ |
| quick.orange.rabbit      | 被队列 Q1Q2 接收到                         |
| lazy.orange.elephant     | 被队列 Q1Q2 接收到                         |
| quick.orange.fox         | 被队列 Q1 接收到                           |
| lazy.brown.fox           | 被队列 Q2 接收到                           |
| lazy.pink.rabbit         | 虽然满足两个绑定但只被队列 Q2 接收一次     |
| quick.brown.fox          | 不匹配任何绑定不会被任何队列接收到会被丢弃 |
| quick.orange.male.rabbit | 是四个单词不匹配任何绑定会被丢弃           |
| lazy.orange.male.rabbit  | 是四个单词但匹配 Q2                        |



当一个队列绑定键是 #，那么这个队列将接收所有数据，就有点像 fanout 了

如果队列绑定键当中没有#和*出现，那么该队列绑定类型就是 direct 了



```JAVA
public class EmitLogTopic {
    private static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

        /**
         * Q1-->绑定的是
         *      中间带 orange 带 3 个单词的字符串(*.orange.*)
         * Q2-->绑定的是
         *      最后一个单词是 rabbit 的 3 个单词(*.*.rabbit)
         *      第一个单词是 lazy 的多个单词(lazy.#)
         *
         */
        Map<String, String> bindingKeyMap = new HashMap<>();
        bindingKeyMap.put("quick.orange.rabbit", "被队列 Q1Q2 接收到");
        bindingKeyMap.put("lazy.orange.elephant", "被队列 Q1Q2 接收到");
        bindingKeyMap.put("quick.orange.fox", "被队列 Q1 接收到");
        bindingKeyMap.put("lazy.brown.fox", "被队列 Q2 接收到");
        bindingKeyMap.put("lazy.pink.rabbit", "虽然满足两个绑定但只被队列 Q2 接收一次");
        bindingKeyMap.put("quick.brown.fox", "不匹配任何绑定不会被任何队列接收到会被丢弃");
        bindingKeyMap.put("quick.orange.male.rabbit", "是四个单词不匹配任何绑定会被丢弃");
        bindingKeyMap.put("lazy.orange.male.rabbit", "是四个单词但匹配 Q2");
        for (Map.Entry<String, String> bindingKeyEntry : bindingKeyMap.entrySet()) {
            String bindingKey = bindingKeyEntry.getKey();
            String message = bindingKeyEntry.getValue();

            channel.basicPublish(EXCHANGE_NAME, bindingKey, null, message.getBytes("UTF-8"));
            System.out.println("生产者发出消息：" + message);
        }
    }
}
```

```JAVA
public class ReceiveLogsTopic01 {
    private static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
        //声明 Q1 队列与绑定关系
        String queueName = "Q1";
        //声明
        channel.queueDeclare(queueName, false, false, false, null);
        //绑定
        channel.queueBind(queueName, EXCHANGE_NAME, "*.orange.*");
        System.out.println("等待接收消息........... ");

        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println(" 接收队列:" + queueName + " 绑定键:" + delivery.getEnvelope().getRoutingKey() + ",消息:" + message);
        };
        channel.basicConsume(queueName, true, deliverCallback, consumerTag -> {
        });
    }
}
```

```JAVA
public class ReceiveLogsTopic02 {
    private static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
        //声明 Q2 队列与绑定关系
        String queueName = "Q2";
        //声明
        channel.queueDeclare(queueName, false, false, false, null);
        //绑定
        channel.queueBind(queueName, EXCHANGE_NAME, "*.*.rabbit");
        channel.queueBind(queueName, EXCHANGE_NAME, "lazy.#");

        System.out.println("等待接收消息........... ");

        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println(" 接收队列:" + queueName + " 绑定键:" + delivery.getEnvelope().getRoutingKey() + ",消息:" + message);
        };
        channel.basicConsume(queueName, true, deliverCallback, consumerTag -> {
        });
    }
}
```



------

## 死信队列

### 死信的概念



死信，顾名思义就是无法被消费的消息，字面意思可以这样理解，一般来说，producer 将消息投递到 broker 或者直接到 queue 里了，consumer 从 queue 取出消息进行消费，但某些时候由于特定的原因导致 queue 中的某些消息无法被消费，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列

应用场景：为了保证订单业务的消息数据不丢失，需要使用到 RabbitMQ 的死信队列机制，当消息消费发生异常时，将消息投入死信队列中。还有比如说：用户在商城下单成功并点击去支付后在指定时间未支付时自动失效



------

### 死信的来源



* 消息 TTL 过期

  TTL是 Time To Live 的缩写, 也就是生存时间

* 队列达到最大长度

  队列满了，无法再添加数据到 MQ 中

* 消息被拒绝

  - (basic.reject 或 basic.nack) 并且 requeue = false



------

### 死信之 TTL



![image-20240408234252084](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240408234252084.png)



如果使用的是 Spring Starter AMQP

```JAVA
@Configuration  
public class RabbitMqConfig {  
  
    @Bean  
    public Queue myQueue() {  
        Map<String, Object> args = new HashMap<>();  
        // 设置消息的TTL为10秒  
        args.put("x-message-ttl", 10000);  
        // 设置死信交换机（DLX）的名称  
        args.put("x-dead-letter-exchange", "myDeadLetterExchange");  
        // 设置死信路由键（可选）  
        args.put("x-dead-letter-routing-key", "myDeadLetterRoutingKey");  
        return new Queue("myQueue", true, false, false, args);  
    }  
  
    @Bean  
    public DirectExchange myDeadLetterExchange() {  
        return new DirectExchange("myDeadLetterExchange");  
    }  
  
    @Bean  
    public Binding bindingDeadLetter() {  
        return BindingBuilder.bind(myDeadLetterQueue()).to(myDeadLetterExchange()).with("myDeadLetterRoutingKey");  
    }  
  
    @Bean  
    public Queue myDeadLetterQueue() {  
        return new Queue("myDeadLetterQueue");  
    }  
}
```

在上面的代码中，`myQueue` 是主队列，设置了消息的 TTL 为 10 秒，并配置了死信交换机和路由键。当消息在队列中过期或由于其他原因被拒绝时，它们将被发送到 `myDeadLetterExchange`，并使用 `myDeadLetterRoutingKey` 路由键



------

### 死信之最大长度



可以通过其他方式间接地达到这个目的，比如设置队列的 `x-max-length` 或 `x-max-length-bytes` 参数，这两个参数分别用于限制队列中消息的最大数量和最大字节数

需要注意的是，`x-max-length `和 `x-max-length-bytes` 是 RabbitMQ 的插件参数，并不是 AMQP 标准的一部分。因此，需要确保 RabbitMQ 服务器已经安装了相关的插件（如 `rabbitmq_limited_queue` 插件），并且客户端库支持这些参数

```Java
@Configuration  
public class RabbitMqConfig {  
  
    @Bean  
    public Queue myQueue() {  
        // 使用QueueBuilder来构建队列，并设置最大消息数量  
        return QueueBuilder.durable("myQueue")  
                .withArgument("x-max-length", 1000) // 设置队列最大消息数量为1000  
                .build();  
    }  
  
    // 其他RabbitMQ配置...  
}
```



------

### 死信之消息被拒



在 RabbitMQ 中，当消费者拒绝一条消息并且设置了 requeue 参数为 false 时，默认情况下，这条消息会被 RabbitMQ 丢弃。requeue 参数决定了当消息被拒绝时是否应该重新入队。如果 requeue 为 true，则消息会被重新放回队列中等待下一次消费；如果为 false，则消息会被丢弃

因此，为了避免重要消息的丢失，建议在 RabbitMQ 中配置死信队列。死信队列是一种特殊的队列，用于处理那些无法被正常消费或处理失败的消息。当消息被拒绝或过期时，RabbitMQ 可以将这些消息发送到死信队列，以便进行后续的处理或记录



首先，定义一个死信交换机和一个死信队列。死信交换机将接收那些被拒绝或过期的消息

```JAVA
@Configuration  
public class RabbitMqConfig {  
  
    @Bean  
    public DirectExchange deadLetterExchange() {  
        return new DirectExchange("deadLetterExchange");  
    }  
  
    @Bean  
    public Queue deadLetterQueue() {  
        return new Queue("deadLetterQueue");  
    }  
  
    @Bean  
    public Binding bindingDeadLetter() {  
        return BindingBuilder.bind(deadLetterQueue()).to(deadLetterExchange()).with("deadLetterRoutingKey");  
    }  
}
```

然后，配置主队列，以便在消息被拒绝时将它们发送到死信交换机。这可以通过设置队列的 `x-dead-letter-exchange` 参数来实现

```JAVA
@Bean  
public Queue mainQueue() {  
    Map<String, Object> args = new HashMap<>();  
    args.put("x-dead-letter-exchange", "deadLetterExchange"); // 设置死信交换机名称  
    args.put("x-dead-letter-routing-key", "deadLetterRoutingKey"); // 设置发送到死信交换机的路由键  
    return new Queue("mainQueue", true, false, false, args);  
}
```

发送消息到主队列与平常一样

```JAVA
@Autowired  
private RabbitTemplate rabbitTemplate;  
  
public void sendMessageToMainQueue(String message) {  
    rabbitTemplate.convertAndSend("mainQueue", message);  
}
```

当消息在消费者中被拒绝并且设置了 `requeue` 参数为 `false` 时，RabbitMQ 会将其发送到配置的死信交换机

```JAVA
@RabbitListener(queues = "mainQueue")  
public void receiveMessage(Message message, Channel channel) throws IOException {  
    try {  
        // 处理消息逻辑...  
    } catch (Exception e) {  
        // 如果处理失败，则拒绝消息并不重新入队  
        channel.basicReject(message.getMessageProperties().getDeliveryTag(), false);  
    }  
}
```

最后，需要一个消费者来监听死信队列并处理其中的消息

```JAVA
@RabbitListener(queues = "deadLetterQueue")  
public void receiveDeadLetterMessage(String message) {  
    // 处理死信队列中的消息逻辑...  
}
```



------

## 延迟队列

### 延迟队列的概念



延时队列,队列内部是有序的，最重要的特性就体现在它的延时属性上，延时队列中的元素是希望 在指定时间到了以后或之前取出和处理，简单来说，延时队列就是用来存放需要在指定时间被处理的元素的队列



------

### 使用场景



1. 订单在十分钟之内未支付则自动取消
2. 新创建的店铺，如果在十天内都没有上传过商品，则自动发送消息提醒
3. 用户注册成功后，如果三天内没有登陆则进行短信提醒
4. 用户发起退款，如果三天内没有得到处理则通知相关运营人员
5. 预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议



这些场景都有一个特点，需要在某个事件发生之后或者之前的指定时间点完成某一项任务，如： 发生订单生成事件，在十分钟之后检查该订单支付状态，然后将未支付的订单进行关闭；那我们一直轮询数据，每秒查一次，取出需要被处理的数据，然后处理不就完事了吗

如果数据量比较少，确实可以这样做，比如：对于「如果账单一周内未支付则进行自动结算」这样的需求，  如果对于时间不是严格限制，而是宽松意义上的一周，那么每天晚上跑个定时任务检查一下所有未支付的账单，确实也是一个可行的方案。但对于数据量比较大，并且时效性较强的场景，如：「订单十分钟内未支付则关闭」，短期内未支付的订单数据可能会有很多，活动期间甚至会达到百万甚至千万级别，对这么庞大的数据量仍旧使用轮询的方式显然是不可取的，很可能在一秒内无法完成所有订单的检查，同时会给数据库带来很大压力，无法满足业务要求而且性能低下



------

### TTL 的两种设置



TTL 是什么呢？TTL 是 RabbitMQ 中一个消息或者队列的属性，表明一条消息或者该队列中的所有消息的最大存活时间，单位是毫秒

换句话说，如果一条消息设置了 TTL 属性或者进入了设置 TTL 属性的队列，那么这条消息如果在 TTL 设置的时间内没有被消费，则会成为「死信」。如果同时配置了队列的 TTL 和消息的 TTL，那么较小的那个值将会被使用，有两种方式设置 TTL



* 设置队列的 TTL

  在创建队列的时候设置队列的 x-message-ttl 属性

  ```JAVA
  Map<String, Object> params = new HashMap<>();
  params.put("x-message-ttl",5000);
  return QueueBuilder.durable("QA").withArguments(args).build(); // QA 队列的最大存活时间位 5000 毫秒
  ```

* 设置消息的 TTL

  针对每条消息设置 TTL

  ```JAVA
  rabbitTemplate.converAndSend("X","XC",message,correlationData -> {
      correlationData.getMessageProperties().setExpiration("5000");
  });
  ```



如果设置了队列的 TTL  属性，那么一旦消息过期，就会被队列丢弃(如果配置了死信队列被丢到死信队列中)，而第二种方式，消息即使过期，也不一定会被马上丢弃，因为消息是否过期是在即将投递到消费者之前判定的，如果当前队列有严重的消息积压情况，则已过期的消息也许还能存活较长时间

另外，还需要注意的一点是，如果不设置 TTL，表示消息永远不会过期，如果将 TTL 设置为 0，则表示除非此时可以直接投递该消息到消费者，否则该消息将会被丢弃



------

### 实现延迟队列



1. 首先，需要在 RabbitMQ 服务器上安装 `rabbitmq_delayed_message_exchange` 插件。可以通过 RabbitMQ 的管理界面或命令行来安装

2. 使用 `@Configuration` 类来定义延迟交换机和队列。需要指定交换机类型为 `x-delayed-message` ，并设置队列的相关属性

   ```JAVA
   @Configuration  
   public class RabbitMqConfig {  
     
       @Bean  
       public DirectExchange delayedExchange() {  
           Map<String, Object> args = new HashMap<>();  
           args.put("x-delayed-type", "direct");  
           return new DirectExchange("delayed_exchange", true, false, args);  
       }  
     
       @Bean  
       public Queue delayedQueue() {  
           return new Queue("delayed_queue", true);  
       }  
     
       @Bean  
       public Binding binding(Queue delayedQueue, DirectExchange delayedExchange) {  
           return BindingBuilder.bind(delayedQueue).to(delayedExchange).with("delayed_key");  
       }  
   }
   ```

3. 在发送消息时，需要设置消息的延迟属性。这可以通过在消息的属性中添加 `x-delay` 字段来实现

   ```JAVA
   @Autowired  
   private RabbitTemplate rabbitTemplate;  
     
   public void sendDelayedMessage(String message, int delay) {  
       MessageProperties messageProperties = new MessageProperties();  
       messageProperties.setHeader("x-delay", delay);  
       Message msg = new Message(message.getBytes(), messageProperties);  
       rabbitTemplate.convertAndSend("delayed_exchange", "delayed_key", msg);  
   }
   ```

4. 定义消息监听器来接收队列中的消息

   ```JAVA
   @RabbitListener(queues = "delayed_queue")  
   public void receiveMessage(String message) {  
       System.out.println("Received message: " + message);  
   }
   ```



------

## 发布确认高级

### 发布确认 SpringBoot 版本



![image-20240414000837957](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240414000837957.png)



在配置文件当中需要添加

```properties
spring.rabbitmq.publisher-confirm-type=correlated
```

* `NONE` 值是禁用发布确认模式，是默认值
* `CORRELATED` 值是发布消息成功到交换器后会触发回调方法
* `SIMPLE` 值经测试有两种效果，其一效果和 CORRELATED 值一样会触发回调方法，其二在发布消息成功后使用  rabbitTemplate 调用 waitForConfirms 或 waitForConfirmsOrDie 方法等待 broker  节点返回发送结果，根据返回结果来判定下一步的逻辑，要注意的点是 waitForConfirmsOrDie 方法如果返回 false 则会关闭  channel，则接下来无法发送消息到 broker



**添加配置类**

```JAVA
@Configuration
public class ConfirmConfig {
    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    //声明业务 Exchange
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        return new DirectExchange(CONFIRM_EXCHANGE_NAME);
    }

    // 声明确认队列
    @Bean("confirmQueue")
    public Queue confirmQueue() {
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    // 声明确认队列绑定关系
    @Bean
    public Binding queueBinding(@Qualifier("confirmQueue") Queue queue,
                                @Qualifier("confirmExchange") DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("key1");
    }
}
```



**消息生产者的回调接口**

```JAVA
@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback {
    /**
     * 交换机不管是否收到消息的一个回调方法
     *
     * @param correlationData 消息相关数据
     * @param ack             交换机是否收到消息
     * @param cause           为收到消息的原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到 id 为:{}的消息", id);
        } else {
            log.info("交换机还未收到 id 为:{}消息，原因:{}", id, cause);
        }
    }

}
```



**消息生产者**

```JAVA
@RestController
@RequestMapping("/confirm")
@Slf4j
public class ProducerController {
    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Autowired
    private MyCallBack myCallBack;

    //依赖注入 rabbitTemplate 之后再设置它的回调对象
    @PostConstruct
    public void init() {
        rabbitTemplate.setConfirmCallback(myCallBack);
    }
    
    /**
     * 消息回调和退回
     *
     * @param message
     */
    @GetMapping("sendMessage/{message}")
    public void sendMessage(@PathVariable String message) {

        //指定消息 id 为 1
        CorrelationData correlationData1 = new CorrelationData("1");
        String routingKey = "key1";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME, routingKey, message + routingKey, correlationData1);
        log.info(routingKey + "发送消息内容:{}", message + routingKey);

        CorrelationData correlationData2 = new CorrelationData("2");
        routingKey = "key2";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME, routingKey, message + routingKey, correlationData2);
        log.info(routingKey + "发送消息内容:{}", message + routingKey);

    }

}
```



**消息消费者**

```JAVA
@Component
@Slf4j
public class ConfirmConsumer {
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    @RabbitListener(queues = CONFIRM_QUEUE_NAME)
    public void receiveMsg(Message message) {
        String msg = new String(message.getBody());
        log.info("接受到队列 confirm.queue 消息:{}", msg);
    }

}
```



结果分析：

![image-20240414001112349](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240414001112349.png)

可以看到，发送了两条消息，第一条消息的 RoutingKey 为 "key1"，第二条消息的 RoutingKey 为  "key2"，两条消息都成功被交换机接收，也收到了交换机的确认回调，但消费者只收到了一条消息，因为第二条消息的 RoutingKey 与队列的  BindingKey 不一致，也没有其它队列能接收这个消息，所有第二条消息被直接丢弃了

丢弃的消息交换机是不知道的，需要解决告诉生产者消息传送失败



------

### 回退消息



```JAVA
rabbitTemplate.setReturnsCallback(myCallBack);
```

在仅开启了生产者确认机制的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息，如果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道消息被丢弃这个事件的

通过设置 mandatory 参数可以在当消息传递过程中不可达目的地时将消息返回给生产者



**修改配置**

```yaml
#消息退回
spring.rabbitmq.publisher-returns=true
```



**修改回调接口**

```JAVA
@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnsCallback {

    /**
     * 交换机不管是否收到消息的一个回调方法
     *
     * @param correlationData 消息相关数据
     * @param ack             交换机是否收到消息
     * @param cause           为收到消息的原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到 id 为:{}的消息", id);
        } else {
            log.info("交换机还未收到 id 为:{}消息，原因:{}", id, cause);
        }
    }

    //当消息无法路由的时候的回调方法
    @Override
    public void returnedMessage(ReturnedMessage returned) {

        log.error("消息：{}，被交换机 {} 退回，原因：{}，路由key：{},code:{}",
                new String(returned.getMessage().getBody()), returned.getExchange(),
                returned.getReplyText(), returned.getRoutingKey(),
                returned.getReplyCode());

    }
}
```



**修改生产者**

```JAVA
//依赖注入 rabbitTemplate 之后再设置它的回调对象
@PostConstruct
public void init() {
    //消息回调
    rabbitTemplate.setConfirmCallback(myCallBack);
    /**
     * true：交换机无法将消息进行路由时，会将该消息返回给生产者
     * false：如果发现消息无法进行路由，则直接丢弃
     */
    rabbitTemplate.setMandatory(true);
    //设置回退消息交给谁处理
    rabbitTemplate.setReturnsCallback(myCallBack);
  	//RabbitMQ版本低的是 rabbitTemplate.setReturnCallback(myCallBack);

}
```



结果分析：

![image-20240416214908827](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240416214908827.png)



------

### 备份交换机



有了 mandatory  参数和回退消息，我们获得了对无法投递消息的感知能力，在生产者的消息无法被投递时发现并处理。但有时候，我们并不知道该如何处理这些无法路由的消息，最多打个日志，然后触发报警，再来手动处理。而通过日志来处理这些无法路由的消息是很不优雅的做法，特别是当生产者所在的服务有多台机器的时候，手动复制日志会更加麻烦而且容易出错。而且设置 mandatory 参数会增加生产者的复杂性，需要添加处理这些被退回的消息的逻辑。如果既不想丢失消息，又不想增加生产者的复杂性，该怎么做呢

前面在设置死信队列的文章中，我们提到，可以为队列设置死信交换机来存储那些处理失败的消息，可是这些不可路由消息根本没有机会进入到队列，因此无法使用死信队列来保存消息。 在 RabbitMQ 中，有一种备份交换机的机制存在，可以很好的应对这个问题

什么是备份交换机呢？备份交换机可以理解为 RabbitMQ  中交换机的“备胎”，当我们为某一个交换机声明一个对应的备份交换机时，就是为它创建一个备胎，当交换机接收到一条不可路由消息时，将会把这条消息转发到备份交换机中，由备份交换机来进行转发和处理，通常备份交换机的类型为 Fanout ，这样就能把所有消息都投递到与其绑定的队列中，然后我们在备份交换机下绑定一个队列，这样所有那些原交换机无法被路由的消息，就会都进 入这个队列了。当然，我们还可以建立一个报警队列，用独立的消费者来进行监测和报警



![image-20240417000318286](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240417000318286.png)



**修改配置类**

```JAVA
@Configuration
public class ConfirmConfig {
    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";
    //关于备份的
    public static final String BACKUP_EXCHANGE_NAME = "backup.exchange";
    public static final String BACKUP_QUEUE_NAME = "backup.queue";
    public static final String WARNING_QUEUE_NAME = "warning.queue";


    /*
    //声明业务 Exchange
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        return new DirectExchange(CONFIRM_EXCHANGE_NAME);
    }
    */

    // 声明确认队列
    @Bean("confirmQueue")
    public Queue confirmQueue() {
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    // 声明确认队列绑定关系
    @Bean
    public Binding queueBinding(@Qualifier("confirmQueue") Queue queue,
                                @Qualifier("confirmExchange") DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("key1");
    }

    //************************以下是关于备份的******************************

    //声明备份 Exchange
    @Bean("backupExchange")
    public FanoutExchange backupExchange() {
        return new FanoutExchange(BACKUP_EXCHANGE_NAME);
    }

    //声明确认 Exchange 交换机的备份交换机
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        ExchangeBuilder exchangeBuilder = ExchangeBuilder.directExchange(CONFIRM_EXCHANGE_NAME)
                .durable(true)
                //设置该交换机的备份交换机
                .withArgument("alternate-exchange", BACKUP_EXCHANGE_NAME);
        return exchangeBuilder.build();
    }


    // 声明警告队列
    @Bean("warningQueue")
    public Queue warningQueue() {
        return QueueBuilder.durable(WARNING_QUEUE_NAME).build();
    }

    // 声明报警队列绑定关系
    @Bean
    public Binding warningBinding(@Qualifier("warningQueue") Queue queue,
                                  @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(queue).to(backupExchange);
    }

    // 声明备份队列
    @Bean("backQueue")
    public Queue backQueue() {
        return QueueBuilder.durable(BACKUP_QUEUE_NAME).build();
    }

    // 声明备份队列绑定关系
    @Bean
    public Binding backupBinding(@Qualifier("backQueue") Queue queue,
                                 @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(queue).to(backupExchange);
    }
}
```



**报警消费者**

```JAVA
@Component
@Slf4j
public class WarningConsumer {
    public static final String WARNING_QUEUE_NAME = "warning.queue";

    @RabbitListener(queues = WARNING_QUEUE_NAME)
    public void receiveWarningMsg(Message message) {
        String msg = new String(message.getBody());
        log.error("报警发现不可路由消息：{}", msg);
    }
}
```



![image-20240417000441283](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240417000441283.png)

mandatory 参数与备份交换机可以一起使用的时候，如果两者同时开启，消息究竟何去何从？谁优先级高，经过上面结果显示答案是备份交换机优先级高



------

## 其它

### 幂等性



在海量订单生成的业务高峰期，生产端有可能就会重复发生了消息，这时候消费端就要实现幂等性， 这就意味着我们的消息永远不会被消费多次，即使我们收到了一样的消息

业界主流的幂等性有两种操作:

1. 唯一 ID+指纹码机制,利用数据库主键去重
2. 利用 redis 的原子性去实现



* 唯一 ID + 指纹码机制

  指纹码：我们的一些规则或者时间戳加别的服务给到的唯一信息码,它并不一定是我们系统生成的，基本都是由我们的业务规则拼接而来，但是一定要保证唯一性，然后就利用查询语句进行判断这个 id  是否存在数据库中，优势就是实现简单就一个拼接，然后查询判断是否重复；劣势就是在高并发时，如果是单个数据库就会有写入性能瓶颈当然也可以采用分库分表提升性能，但也不是最推荐的方式

*  Redis 原子性

  利用 redis 执行 setnx 命令，天然具有幂等性。从而实现不重复消费



------

### 优先级队列



* 在控制台设置队列优先级

  ![image-20240418000906891](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240418000906891.png)

* 队列代码中添加优先级

  ```JAVA
  Map<String, Object> params = new HashMap();
  params.put("x-max-priority", 10);
  channel.queueDeclare("hello", true, false, false, params);
  ```

* 消息代码中添加优先级

  ```JAVA
  AMQP.BasicProperties properties = new AMQP.BasicProperties().builder().priority(10).build();
  ```



------

### 惰性队列



RabbitMQ 从 3.6.0  版本开始引入了惰性队列的概念。惰性队列会尽可能的将消息存入磁盘中，而在消费者消费到相应的消息时才会被加载到内存中，它的一个重要的设计目标是能够支持更长的队列，即支持更多的消息存储。当消费者由于各种各样的原因(比如消费者下线、宕机亦或者是由于维护而关闭等)而致使长时间内不能消费消息造成堆积时，惰性队列就很有必要了

默认情况下，当生产者将消息发送到 RabbitMQ 的时候，队列中的消息会尽可能的存储在内存之中，  这样可以更加快速的将消息发送给消费者。即使是持久化的消息，在被写入磁盘的同时也会在内存中驻留一份备份。当RabbitMQ  需要释放内存的时候，会将内存中的消息换页至磁盘中，这个操作会耗费较长的时间，也会阻塞队列的操作，进而无法接收新的消息。虽然 RabbitMQ  的开发者们一直在升级相关的算法， 但是效果始终不太理想，尤其是在消息量特别大的时候

队列具备两种模式：default 和 lazy。默认的为default 模式，在3.6.0 之前的版本无需做任何变更。lazy  模式即为惰性队列的模式，可以通过调用 channel.queueDeclare 方法的时候在参数中设置，也可以通过 Policy  的方式设置，如果一个队列同时使用这两种方式设置的话，那么 Policy 的方式具备更高的优先级。  如果要通过声明的方式改变已有队列的模式的话，那么只能先删除队列，然后再重新声明一个新的



在队列声明的时候可以通过“x-queue-mode”参数来设置队列的模式，取值为“default”和“lazy”。下面示例中演示了一个惰性队列的声明细节：

```JAVA
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-queue-mode", "lazy");
channel.queueDeclare("myqueue", false, false, false, args);
```



内存开销对比

![image-20240418001313842](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/RabbitMQ%20%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E7%B2%BE%E9%80%9A/image-20240418001313842.png)

在发送 1 百万条消息，每条消息大概占 1KB 的情况下，普通队列占用内存是 1.2GB，而惰性队列仅仅占用 1.5MB
