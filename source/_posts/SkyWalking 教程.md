---
title: SkyWalking 教程
date: 2023-10-22 16:59:53
tags: 中间件
categories: 
  - 中间件
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/preview.jpg
---



# SkyWalking 教程

## APM 系统

### 简介



APM（Application Performance Management）即应用性能管理系统，即对企业系统即时监控以实现对应用程序性能管理和故障管理的系统化的解决方案。应用性能管理，主要指对企业的关键业务进行检、优化，提供企业应用的可靠性和质量，保证用户得到良好的服务，降低 IT 总拥有成本

APM 系统是可以帮助理解系统行为、用于分析性能问题的工具，以便发生故障的时候，能够快速定位和解决问题



------

### 分布式链路追踪



随着分布式系统和微服务架构的出现，一次用户的请求会经过多个系统，不同系统之间的调用关系十分复杂，任何一个系统出错都有可能整个请求的处理结果。以往的监控系统往往只能知道单个系统的健康状况、一次请求的成功失败，无法快速定位失败的根本原因



除此之外，复杂的分布式系统也面临着以下问题：

* 性能分析：一个服务依赖多个服务，被依赖的服务也依赖了其它服务了。如果某个接口耗时突然变长，那未必是直接调用的下游服务慢了，也可能是下游的下游造成的，如果快速定位耗时变长的根本原因呢
* 链路梳理：需求迭代很快，系统之间调用关系变化频繁，靠人工很难梳理清楚系统链路拓扑

为了解决这些问题，Google 推出了一个分布式链路追踪系统 Dapper，之后各个互联网公司都参照 Dapper 的思想推出了自己的分布式链路追踪系统，而这些系统就是分布式系统下的 APM 系统



------

### OpenTracing



分布式链路追踪最开始由 Google 在 Drapper 的论文中提出，而 OpenTracing 通过提供平台无关、厂商无关的 API，使得开发人员能够方便的添加或更换追踪系统的实现



------

### 主流的开源 APM 产品



* PinPoint

  PinPoint 是由一个韩国团队开发并开源，针对 Java 编写的大规模分布式系统设计，通过 JavaAgent 做字节码植入，实现加入 TraceId 和获取性能数据的目的，对应用代码零侵入

  官方网站：https://github.com/naver/pinpoint

  ![image-20230908204011131](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/image-20230908204011131.png)

* SkyWalking

  SkyWalking 是 Apache 基金会下面的一个开源 APM 项目，为微服务架构和云原生系统架构设计。它通过探针自动收集所需的指标，并进行分布式追踪。通过这些调用链路以及指标，SkyWalking APM 会感知应用间关系和服务间关系，并进行相应的指标统计。SkyWalking 支持链路追踪和监控应用组件，基本覆盖主流框架和容器，比如国产的 Dubbo 和 motan 等，国际化的 Spring Boot 和 Spring Cloud

  官方网站：https://skywalking.apache.org/

* Zipkin

  Zipkin 是由 Twitter 开源，是分布式链路调用监控系统，聚合各业务系统调用延迟数据，达到链路调用监控追踪。Zipkin 基于 Google 的论文 Drapper 实现，主要完成数据的收集、存储、搜索和页面展示

  官方网站：https://zipkin.io/

* CAT

  CAT 是由大众点评开源的项目，基于 Java 开发的实时应用监控平台，包括实时应用监控、业务监控，可以提供十几张报表展示

  官方网站：https://github.com/dianping/cat



------

## SkyWalking

### 概述



根据官方的解释，SkyWalking 是一个可观测性分析平台和性能监控平台，提供分布式链路追踪、服务网格遥测分析、度量聚合和可视化一体化解决方案

下面是 SkyWalking 的几大特点：

* 多语言自动探针，Java、.NET Core 和 Node.js
* 多种监控手段，语言探针和 Service Mesh
* 轻量高效，不需要额外搭建大数据平台
* 模块化架构，UI、存储、集群管理多种机制可选
* 支持告警
* 优秀的可视化效果



SkyWalking 整体架构如下：

![image-20230909170625937](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/image-20230909170625937.png)



SkyWalking 提供 Tracing 和 Metrics 数据的聚合和获取

Mertrics 的特点是，它是可累加的，具有原子性，每个都是一个逻辑计量单元，或者一个时间段内的柱状图。例如，队列的当前深度可以被定义为一个计量单元，在写入或读取时被更新统计；输入 HTTP 请求的数量可以被定义为一个计数器，用于简单累加；请求的执行时间可以被定义为一个柱状图，在指定时间片上更新和统计汇总

Tracing 的最大特点是，它在单次请求范围内处理信息。任何的数据、元数据信息都被绑定到系统的单个事务上。例如，一次调用远程服务的 RPC 执行过程；一次实际的 SQL 查询语句；一次 HTTP 请求的业务性 ID

总结，Metric 主要用于进行数据的统计，比如 HTTP 请求数的计算，Tracing 主要包含某一次请求的链路数据，详细内容可以查看 SkyWalking 开发者吴晟翻译的这篇文章：http://blog.oneapm.com/apm-tech/811.html



整体架构主要包含以下三个部分：

* 探针（agent）负责数据的收集，包含了 Metrics 和 Tracing 的数据，agent 会被安装到服务所在的服务器上，以方便数据的获取
* 可观测性分析平台 AOP，接收探针发送的数据，并在内存中使用分析引擎进行数据的整合运算，然后将数据存储到对应的存储介质上，比如 ElasticSeatch、MySQL、H2 等，同时 AOP 还使用查询引擎提供查询接口
* SkyWalking 提供单独的 UI 进行数据的查看，UI 会调用 OAP 提供的接口，获取数据并进行展示



------

### 优势



SkyWalking 相较于其它的分布式链路监控工具，具有以下特点：

* 社区相当活跃，SkyWalking 已经进入 Apache 孵化，目前的 star 数已经超过 22 k，开发者是中国人，学习和交流更加方便
* SkyWalking 几乎支持所有的主流开发语言，相较于其它工具，比如 PinPoint，只支持 Java、PHP 和 Python，具有较大优势
* 探针无侵入性，对比 CAT 具有侵入性的探针，优势较大，不修改原有项目代码就可完成集成
* 探针性能优秀，有网友对 PinPoint 和 SkyWalking 进行过测试，由于 PinPoint 收集的数据过多，所以对性能损耗较大，而 SkyWalking 的探针性能十分出色
* 支持的组件较多，特别是对 RPC 框架的支持，SkyWalking 对 Dubbo、gRPC 等有原生的支持，甚至连小众的 motan 和 sofarpc 都支持



------

## 快速入门

### 部署 OAP 服务



1. 前往官网 Download 页面 https://skywalking.apache.org/downloads/#SkyWalkingAPM，下载 SkyWalking APM，点击 Distribution，选择需要下载的版本和格式。本文下载的 9.3.0 版本的 tar 包

   ![image-20230909181905100](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/image-20230909181905100.png)

2. 下载所需语言的 Agent，同样是 Distribution 下下载

   ![image-20230909182212617](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/image-20230909182212617.png)

   

4. 解压部署

   ```sh
   # 1.创建并进入 skywalking 目录
   mkdir /usr/local/skywalking
   cd /usr/local/skywalking
   
   # 2.将刚刚下载的两个文件上传到当前目录下
   
   # 3.解压 skywalking apm
   tar -zxf apm安装包名
   
   # 4.此时的 skywalking 就已经可以启动了，skywalking apm 的 bin 目前，输入以下指令便会启动 opt 和 ui，默认使用的数据库是 H2，重启会丢失数据
   # 需要注意的是，skywalking 是由 Java 实现的，启动 skywalking 的环境必须已经部署 jdk
   sh start.sh
   # 启动成功后就可通过 ip地址/8080 访问 ui 界面了
   ```



------

### Agent 探针



启动 ui 之后，在上面暂时还看不到任何数据，因为我们还没有启动项目并接入 SkyWalking OAP，这就需要用到刚刚下载的 agent 包了

Agent 探针可以让我们不修改代码的情况下，对 Java 应用上使用到的组件进行动态监控，获取运行数据发送到 OAP 上进行统计和存储。Agent 探针在 Java 中时使用 Java Agent 技术实现的，不需要更改任何代码，Java Agent 会通过虚拟机接口在运行期修改代码



这时候就需要用到刚才下载的 Java Agent jar 包了，我们需要准备好一个可以直接启动的 jar 包项目，然后在启动的时候添加一个参数即可

```
java -jar -javaagent:刚刚下载的agent包的全路径 可以启动的jar包项目
```

启动完成后，调用 jar 包项目的接口，即可在 SkyWalking UI 上看到数据了





------

## 实战进阶

### ES 持久化



SkyWalking 默认使用的数据库是 H2，这是一个内存型数据库，重启会造成数据的丢失。因此需要使用持久化数据库存储，SkyWalking 支持多种类型的数据存储方式，包括 ES、MySQL，TiDB、postgresql 以及 SkyWalking 团队自己开发的 banyandb，一般使用 ES 较多

修改数据存储方式，需要修改 config 目录下的 application.yaml 文件

```yaml
storage:
  selector: ${SW_STORAGE:h2}
  elasticsearch:
    namespace: ${SW_NAMESPACE:""}
    clusterNodes: ${SW_STORAGE_ES_CLUSTER_NODES:localhost:9200}
    protocol: ${SW_STORAGE_ES_HTTP_PROTOCOL:"http"}
    connectTimeout: ${SW_STORAGE_ES_CONNECT_TIMEOUT:3000}
    socketTimeout: ${SW_STORAGE_ES_SOCKET_TIMEOUT:30000}
    responseTimeout: ${SW_STORAGE_ES_RESPONSE_TIMEOUT:15000}
    numHttpClientThread: ${SW_STORAGE_ES_NUM_HTTP_CLIENT_THREAD:0}
    user: ${SW_ES_USER:""}
    password: ${SW_ES_PASSWORD:""}
```

找到如上配置位置，首先将 selector 改为 elasticsearch，如果然后修改连接地址，如果有用户名及密码的话还需要修改用户名和密码

修改完成后，重启 SkyWalking 即可，访问接口产生数据后，再重启数据库，就会发现数据库依然存在了



------

### gPRC 导入日志



除了查看请求的响应时间及拓扑图，SkyWalking 也可以存储服务器日志，通过 SkyWalking UI 查看请求日志，具体操作如下：



1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.apache.skywalking</groupId>
       <artifactId>apm-toolkit-logback-1.x</artifactId>
       <version>9.0.0</version>
   </dependency>
   ```

2. 创建 logback.xml 文件，添加日志配置

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration scan="true" scanPeriod="60 seconds" debug="false">
       <!-- 日志存放路径 -->
       <property name="log.path" value="logs" />
    
       <!-- 日志输出格式 -->
       <property name="log.pattern"
                 value="%d{HH:mm:ss.SSS} [%thread] %-5level %logger{20} %tid - [%method,%line] - %msg%n"/>
    
       <!-- 控制台输出 -->
       <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
           <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
               <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogBackLayout">
                   <pattern>${log.pattern}</pattern>
               </layout>
           </encoder>
       </appender>
   
    
       <!-- 日志传输到skywalking中的appender，通过grpc传输 -->
       <appender name="gpc-log" class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.log.GRPCLogClientAppender">
           <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
               <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
                   <Pattern>${log.pattern}</Pattern>
               </layout>
           </encoder>
       </appender>
    
       <!--系统操作日志-->
       <root level="info">
           <appender-ref ref="console"/>
           <appender-ref ref="gpc-log" />
       </root>
    
   </configuration>
   ```

3. 配置完成后，重启项目，发起请求，就可以再 SkyWalking UI 上看到添加了 traceId 的日志了

   ![image-20230915001802927](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/image-20230915001802927.png)



------

### 告警通知



告警通知中需要了解的指标有：

* metrics-name

  指标名称，也就是 OAL 脚本中的指标名。可以配置的告警指标有：服务、实例、端点、服务关系、实例关系、端点关系，只支持 long、int、double 类型

* op

  操作符

* threshold

  阈值

* period

  告警规则多久被检查一次，是一个时间窗口

* count

  在一个时间窗口内，满足 op 超过阈值的次数达到 count 值，就会触发告警

* slience-period

  在时间 N 中触发告警后，在 N + slience-period 这段时间内不告警

* message

  告警时通知的消息





在 apm 的 config 目录下，可以看到一个 alarm-settings.yml 文件，里面配置了一些简单的配置规则，具体内容如下：

```yaml
# Sample alarm rules.
rules:
  # Rule unique name, must be ended with `_rule`.
  service_resp_time_rule:
    metrics-name: service_resp_time
    op: ">"
    threshold: 1000
    period: 10
    count: 3
    silence-period: 5
    message: Response time of service {name} is more than 1000ms in 3 minutes of last 10 minutes.
  service_sla_rule:
    # Metrics value need to be long, double or int
    metrics-name: service_sla
    op: "<"
    threshold: 8000
    # The length of time to evaluate the metrics
    period: 10
    # How many times after the metrics match the condition, will trigger alarm
    count: 2
    # How many times of checks, the alarm keeps silence after alarm triggered, default as same as period.
    silence-period: 3
    message: Successful rate of service {name} is lower than 80% in 2 minutes of last 10 minutes
  service_resp_time_percentile_rule:
    # Metrics value need to be long, double or int
    metrics-name: service_percentile
    op: ">"
    threshold: 1000,1000,1000,1000,1000
    period: 10
    count: 3
    silence-period: 5
    message: Percentile response time of service {name} alarm in 3 minutes of last 10 minutes, due to more than one condition of p50 > 1000, p75 > 1000, p90 > 1000, p95 > 1000, p99 > 1000
  service_instance_resp_time_rule:
    metrics-name: service_instance_resp_time
    op: ">"
    threshold: 1000
    period: 10
    count: 2
    silence-period: 5
    message: Response time of service instance {name} is more than 1000ms in 2 minutes of last 10 minutes
  database_access_resp_time_rule:
    metrics-name: database_access_resp_time
    threshold: 1000
    op: ">"
    period: 10
    count: 2
    message: Response time of database access {name} is more than 1000ms in 2 minutes of last 10 minutes
  endpoint_relation_resp_time_rule:
    metrics-name: endpoint_relation_resp_time
    threshold: 1000
    op: ">"
    period: 10
    count: 2
    message: Response time of endpoint relation {name} is more than 1000ms in 2 minutes of last 10 minutes
```

在文件的最下方还有一个 webhooks 参数，配置该参数，当触发告警时就会请求该路径

```yml
webhooks:
#  - http://127.0.0.1/notify/
#  - http://127.0.0.1/go-wechat/
```





查看 SkyWalking 官方文档，可以看到，除了支持自定义接口，SkyWalking 的告警通知还支持微信、钉钉、飞书等多种通知方式

![image-20231017234919578](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/SkyWalking%20%E6%95%99%E7%A8%8B/image-20231017234919578.png)



以钉钉为例，如果我们想把告警信息通知到钉钉机器人，添加以下配置即可：

```yml
dingtalkHooks:
  textTemplate: |-
    {
      "msgtype": "text",
      "text": {
        # 消息内容
        "content": "Apache SkyWalking Alarm: \n %s."
      }
    }    
  webhooks:
    # 钉钉机器人路径
    - url: https://oapi.dingtalk.com/robot/send?access_token=dummy_token
      # 签名
      secret: dummysecret
```



------

### 自定义链路追踪



1. 引入依赖

   ```xml
   <!-- https://mvnrepository.com/artifact/org.apache.skywalking/apm-toolkit-trace -->
   <dependency>
       <groupId>org.apache.skywalking</groupId>
       <artifactId>apm-toolkit-trace</artifactId>
   </dependency>
   ```

2. 之后便可以在代码中绑定或获取 traceId

   ```
   // 向上下文对象绑定 key/value
   TraceContent.putCorrelation("key", "value");
   // 获取 traceId
   String traceId = TraceContent.traceId();
   ```

3. 可以使用 @Trace 注解追踪指定方法，可以使用 @Tags 和 @Tag 注解实现多个数据的绑定



------

## Java Agent 

### 简介



在 JVM 进程运行期间获取应用运行的 JVM 信息、变量信息等等。哪怕需要正在生产环境运行期间的 JVM 中的数据，也可以通过 JDK 自带的 Jmap、Jstack 等工具获取

JVM 的设计人员很聪明，在 JVM 设计之初，他们就考虑了虚拟机状态监控、DEBUG、线程和内存分析等功能



在 JDK1.5 之前，JVM 规范定义了 JVMPI（Java Virtual Machine Profiler Interface）语义，JVMPI 提供了一批 JVM 分析接口。 JVMPI 可以监控就 JVM 发生的各种事件，比如，JVM 创建、关闭、Java 类被加载、创建对象或 GC 回收等 37 种事件。除了 JVMPI ，JVM 规范还定义了 JVMDI（Java Virtual Machine Debug Interface）语义，JVMDI 提供了一批 JVM 调试接口

JDK 1.5 及之后的版本将 JVMPI 和 JVMDI 合二为一，形成了一套 JVM 语义——JVMTI（JVMTM Tool Interface），包括 JVM 分析接口和 JVM 调试接口
JVMTI 是开发和监控工具使用的编程接口，它提供了一种方法，用于检查状态和控制在 Java 虚拟机（VM）中运行的应用程序的执行。JVMTI 旨在为需要访问 VM 状态的所有工具提供 VM 接口，包括但不限于：评测、调试、监视、线程分析和覆盖率分析工具



简单来说，JVMTI 是一套 JVM 制定的规范，在不同的 JVM 中实现方式也不一定都一样。JVMTI 提供的是 Native API 调用方式（即大家更熟知的 JNI 调用方式）

JVMTI 接口用 C/C++ 语言来暴露 Native API，并最终以动态链路库的形式被 JVM 加载并运行

在 Java Agent 问世之前，苦逼的开发人员只能通过 JVMTI 的 Native API 调用方式完成代码的动态侵入，非常的繁琐、不友好，而且门槛有点高

因此，在 JDK1.5 版本以后，JVM 提供了 Java Agent 技术来解决上述痛点



与普通 Java 程序通过 main 方法启动不同，Java Agent 并不是一个可以单独启动的程序，它必须依附在一个 Java 应用程序（JVM）上，与主程序运行在同一个 JVM 进程中，通过 Instrumentation 接口与 JVM 进行交互

Java Agent 提供了一种在加载字节码时对字节码进行修改的能力，有两种执行方式：

1. 在应用运行之前，通过 premain() 方法来实现「在应用启动时侵入并代理应用」，这种方式是利用 Instrumentation 接口实现的
2. 在应用运行之后，通过 Attach API 和 agentmain() 方法来实现「在应用启动后的某一个运行阶段中侵入并代理应用」，这种方式是利用 Attach 接口实现的



------

### 应用场景



* 调试代码

  通过 IDE（Intellij Idea 和 Eclipse）调试代码示程序员必备技能之一，通过 IDEA 调试代码就是基于 Java Agent 技术实现的

* 热部署

  常见的热部署工具有 Intellij Idea 的 HotSwap 和 Jrebel

* 在线诊断

  Arthas 就是一款非常优秀的 Java 在线诊断工具

* 性能分析

  TProfiler 采用 Java Agent 技术，在 JVM 启动时把时间采集程序注入到字节码中，整个过程无需修改应用源码，非常的方便

* APM

  很多优秀应用程序性能监测产品如 Pinpoint、Skywalking、Zipkin 等，都是基于 Java Agent 实现的
