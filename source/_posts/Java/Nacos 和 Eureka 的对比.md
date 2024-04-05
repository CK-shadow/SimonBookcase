---
title: Nacos 和 Eureka 的对比
date: 2023-02-19 15:49:38
tags: 
  - Java
  - 微服务
categories: 
  - Java
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/preview.jpg

---

## Nacos 和 Eureka 的对比

### 功能对比



|                    | Spring Cloud Nacos                                           | Spring Cloud Eureka                                          |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| CAP 模型           | 支持 CP 和 AP 模型                                           | AP 模型                                                      |
| 客户端更新服务信息 | 使用注册 + DNS-F + 健康检查模式，DNS-F 客户端使用监听模式，pull/push 拉取更新信息 | 客户端定时轮询服务端获取其它服务 IP 信息，相比之下服务端压力较大、延迟较大 |
| 伸缩性             | 使用 Raft 选举算法，性能、可用性、容错性较好，新加入节点无需与所有节点广播同步信息 | 广播同步信息，集群机器数量较多后压力较大                     |
| 健康检查方式       | 支持客户端/服务端关闭健康检查，检查方式有 TCP、HTTP、SQL，支持自己构建健康检查器 | 客户端向服务端发送 HTTP 心跳                                 |
| 负载均衡           | 支持                                                         | 支持                                                         |
| 手动上下线服务     | 控制台页面或 API                                             | API                                                          |
| 跨中心同步         | 支持                                                         | 不支持                                                       |
| k8s 集成           | 支持                                                         | 不支持                                                       |
| 分组               | 可根据业务或环境进行分组管理                                 | 不支持                                                       |
| 权重               | 支持                                                         | 不支持                                                       |



对比结果：

1. 相比于 Nacos，Eureka 集成更为简单，只需代码配置即可，而 Nacos 需要单独安装客户端
2. Nacos 具备服务优雅上下线和流量管理（API+后台管理页面），而 Eureka 的后台页面仅供展示，需要使用 api 操作上下线且不具备流量管理功能
3. Nacos 整合了注册中心、配置中心功能，把原来两套集群整合成一套，简化了部署维护
4. Eureka 2.x 开源工作已停止，而 Nacos 在以后的版本会支持 SpringCloud + Kubernetes 的组合
5. 从伸缩性和扩展性来看 Nacos 支持跨注册中心同步，而 Eureka 不支持，且在伸缩扩容方面，Nacos 比 Eureka 更优
6. Nacos 具有分组隔离功能，一套 Nacos 集群可以支撑多项目、多环境



------

### 快速开始 - Nacos

#### 使用 docker 安装 Nacos



1. 搜索 nacos 镜像

   ```
   docker search nacos-server
   ```

2. 下载镜像

   ```
   docker pull nacos/nacos-server
   ```

3. 启动镜像

   这时最简单的启动方式，仅供调试使用，线上建议使用 docker-compose 配置数据库信息启动

   ```
   docker run --name nacos-quick -e MODE=standalone -p 8849:8848 -d nacos/nacos-server
   ```

4. 访问 localhost:8849/nacos

   账号及密码都是 nacos



------

#### Spring Boot 集成 Nacos 



1. 引入依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

2. 在项目配置文件中添加 nacos 地址

   ```yml
   spring:
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848
   ```

3. 启动项目，即可在控制台看到项目信息

   ![image-20221204220455623](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204220455623.png)



------

#### 其他配置



|        配置项        |                      key                       |           默认值           |                             说明                             |
| :------------------: | :--------------------------------------------: | :------------------------: | :----------------------------------------------------------: |
|      nacos 地址      |    spring.cloud.nacos.discovery.server-addr    |             无             |              Nacos Server 启动监听的 IP 和端口               |
|        服务名        |      spring.cloud.nacos.discovery.service      | ${spring.application.name} |                       给当前服务的命名                       |
|       服务分组       |       spring.cloud.nacos.discovery.group       |       DEFAULT_GROUP        |                      设置服务所在的分组                      |
|         权重         |      spring.cloud.nacos.discovery.weight       |             1              |             取值范围 1 - 100，取值越大，权重越大             |
|        网卡名        | spring.cloud.nacos.discovery.network-interface |             无             | 当 IP 未配置时，注册的 IP 为此网卡对应的 IP 地址，如果此项也未配置，则默认取第一块网卡的地址 |
|    注册的 IP 地址    |        spring.cloud.nacos.discovery.ip         |             无             |                          优先级最高                          |
|      注册的端口      |       spring.cloud.nacos.discovery.port        |             -1             |                默认情况下不用配置，会自动探测                |
|       命名空间       |     spring.cloud.nacos.discovery.namespace     |             无             |   常用场景之一是不同环境的注册隔离，例如开发环境和测试环境   |
|      AccessKey       |    spring.cloud.nacos.discovery.access-key     |             无             |                要上阿里云时，阿里云上的账户名                |
|      SecretKey       |    spring.cloud.nacos.discovery.secret-key     |             无             |               要上阿里云是，阿里云上的账户密码               |
|       Metadata       |     spring.cloud.nacos.discovery.metadata      |             无             | 使用 Map 格式配置，用户可根据自己的需要自定义一些与服务相关的元数据信息 |
|       日志文件       |     spring.cloud.nacos.discovery.log-name      |             无             |                                                              |
|         集群         |   spring.cloud.nacos.discovery.cluster-name    |          DEFAULT           |                    配置成 Nacos 集群名称                     |
|        接入点        |     spring.cloud.nacos.discovery.endpoint      |           UTF-8            |   地域的某个服务的入口域名，通过此域名可动态拿到服务端地址   |
|   是否集成 Ribbon    |              ribbon.nacos.enabled              |            true            |                                                              |
| 是否开启 Nacos Watch |   spring.cloud.nacos.discovery.watch.enabled   |            true            |                                                              |



------

#### 控制台功能



控制台提供了服务管理以及注册管理的功能，如果使用 Nacos 作为注册中心，就只需要关注服务管理相关的功能了



* 服务列表管理

  服务列表帮助用户以统一的视图管理其所有的微服务以及服务健康状态。整体界面布局是左上角有服务的搜索框和搜索按钮，页面中央是服务列表的展示。服务列表主要展示服务名、集群数目、实例数目、健康实例数目和详情按钮五个栏目

  ![image-20221204231520757](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204231520757.png)

  在服务列表页面点击详情，可以看到服务的详情。可以查看服务、集群和实例的基本信息

* 服务流量权重支持以及流量保护

  Nacos 为用户提供了流量权重控制的能力，同时开放了服务流量的阈值保护，以帮助用户更好的保护服务服务提供者集群不被意外打垮。如下图所以，可以点击实例的编辑按钮，修改实例的权重。如果想增加实例的流量，可以将权重调大，如果不想实例接收流量，则可以将权重设为 0

  ![image-20221204231624004](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204231624004.png)

* 服务元数据管理

  Nacos提供多个维度的服务元数据的暴露，帮助用户存储自定义的信息。这些信息都是以K-V的数据结构存储，在控制台上，会以k1=v1,k2=v2这样的格式展示。类似的，编辑元数据可以通过相同的格式进行。例如服务的元数据编辑，首先点击服务详情页右上角的“编辑服务”按钮，然后在元数据输入框输入：version=1.0,env=prod

  ![image-20221204231702498](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204231702498.png)

  点击确认，就可以在服务详情页面，看到服务的元数据已经更新了

* 服务优雅上下线

  Nacos还提供服务实例的上下线操作，在服务详情页面，可以点击实例的“上线”或者“下线”按钮，被下线的实例，将不会包含在健康的实例列表里

  ![image-20221204231823007](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204231823007.png)



------

### 快速开始 - Eureka

#### 启动 Eureka Server



1. 创建项目，添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

2. 添加配置文件

   ```yml
   server:
     # 服务注册中心的端口号
     port: 8888
   eureka:
     instance:
       # 注册中心的地址
       hostname: localhost
     client:
       # 是否向注册中心注册自己
       registerWithEureka: false
       # 是否检索服务
       fetchRegistry: false
       serviceUrl:
         # 指定服务注册中心的地址
         defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
   ```

3. 在启动类上添加注册 @EnableEurekaServer

4. 启动项目，访问 127.0.0.1:8888，即可看到 Eureke 可视化界面

   ![image-20221204232902300](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204232902300.png)





------

#### 启动 Eureka Client

1. 创建项目，添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   ```

2. 添加配置文件

   ```yml
   spring:
     application:
       name: service-provider
   server:
     # 服务端口号
     port: 8889
   eureka:
     client:
       serviceUrl:
         # 服务注册中心的地址
         defaultZone: http://localhost:8888/eureka/
   ```

3. 在启动类上添加注册 @EnableEurekaClient

4. 启动项目，即可在页面上看到项目信息

   ![image-20221204233244158](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Nacos%20%E5%92%8C%20Eureka%20%E5%AF%B9%E6%AF%94/image-20221204233244158.png)

