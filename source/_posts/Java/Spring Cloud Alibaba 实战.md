---
title: Spring Cloud Alibaba 实战
date: 2024-09-16 18:10:01
tags: Java
categories: 
  - Java
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Spring%20Cloud%20Alibaba%20%E5%AE%9E%E6%88%98/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Spring%20Cloud%20Alibaba%20%E5%AE%9E%E6%88%98/preview.jpg
---



# Spring Cloud Alibaba 实战

## 微服务介绍

### 微服务常见概念



* 服务调用

  将一个系统拆分成各个微服务后，各个微服务之间协同工作才能对外提供完整的服务，这就涉及到各个 微服务之间的调用问题。目前各个微服务之间一般会采用 Restful 接口或者 RPC 协议的方式进行调用

  * Restful 接口

    Restful 接口一般是基于 HTTP 协议实现的，这种协议使用上比较广泛，几乎所有的编程语言都支持 HTTP 协议

  * RPC 协议

    RPC 是一种远程过程调用，能够做到像调用本地服务一样调用远程服务。RPC 框架在底层屏蔽了数据的传输方式，序列化方式和交互的细节信息，让使用 RPC 框架开发微服务的人员觉得更加简单，实现起来更加容易

* 服务治理

  服务治理，说白了就是如何自动化的管理各个微服务，核心的功能就是服务的注册、发现和剔除

  * 服务注册

    各个微服务实例在启动时，能够将自身提供的服务注册到某个注册中心

  * 服务发现

    当某个微服务将自身提供的服务注册到注册中心时，其他微服务实例能够通过注册中心感知到这个微服务提供的服务，并且能够获取到这个微服务的实例信息，通过这个微服务的实例信息就能够调用这个微服务的方法，来进行相应的读写操作

  * 服务剔除

    如果某个微服务实例出现故障，或者连接一直超时，则注册中心会认为当前微服务实例不可用，就会将 这个微服务实例剔除出注册中心，使其不再被其他微服务感知到和调用到

* 注册中心

  提供微服务注册、发现和剔除功能的服务组件

* 服务网关

  服务网关是所有微服务的入口，客户端在访问各个微服务时，首先需要经过服务网关。接入服务网关后，会将所有 API 的调用统一接入到 API 的网关层，由网关层统一接收参数进行路由转发，将返回的结果数据返回给客户端

  通常情况下，一个服务网关最基本的功能包括：统一接入、限流、熔断、降级、安全防护、协议适配、 容错等等。主要专注的是对系统安全、流量和路由等的管理。这样，业务开发人员就可以专注于开发业务逻辑

* 服务限流

  在高并发大流量场景下，经常会出现某个服务或者接口因为调用的流量过大而导致不可用的情况，由于某个服务或者接口的不可用，可能还会导致整个系统崩溃。此时，就会对系统采取限流的手段来进行防护，当请求达到一定的频率或者速率时，对这些请求采取排队、等待、降级等策略，甚至是拒绝服务

* 服务熔断

  如果某个服务出现故障不可用，或者调用超时，为了不让其他服务受到牵累而导致整个系统不可用，则断开与这个服务的连接，暂停对这个服务的调用

* 服务降级

  服务降级主要是从整个系统的负载情况进行考虑，如果某些服务的负载情况比较高，则为了预防某些功能出现负载过高而导致响应慢的问题，会在提供这些功能的方法内部暂时舍弃对一些非核心功能接口的调用，直接返回一个提前准备好的错误处理信息

  服务降级是有损服务，但是能够保证整个系统的稳定性和可用性

* 服务容错

  服务容错指的是微服务能够容纳一定错误情况的发生。从某种意义上说，服务限流、服务熔断和服务降 级都是服务容错的措施

* 链路追踪

  当系统被拆分成各个微服务后，一次请求往往会涉及到多个服务之间的调用关系。如果系统出现问题， 则会增加定位问题的难度。为了解决这个问题，就需要对一次请求涉及到的多个服务链路的日志进行追 踪和记录，一方面可以记录调用的链路，另一方面还可以监控系统中各个调用环节的性能，这就是链路追踪



------

## Spring Cloud Alibaba

### 主要功能



Spring Cloud Alibaba 是阿里开源的一套微服务解决方案，包含各种微服务组件，能够极大的方便开发人员构建微服务应用，主要功能有：

* 服务限流降级

  默认支持 WebServlet、WebFlux、OpenFeign、RestTemplate、Spring Cloud Gateway、Zuul、Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控

* 服务注册与发现

  适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持

* 分布式配置管理

  支持分布式系统中的外部化配置，配置更改时自动刷新

* 消息驱动能力

  基于 Spring Cloud Stream 为微服务应用构建消息驱动能力

* 分布式事务

  使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题

* 对象存储

  阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据

* 分布式任务调度

  提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。 同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker （schedulerx-client）上执行

* 短信服务

  覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道



除了上述所具有的功能外，针对企业级用户的场景，Spring Cloud Alibaba 配套的企业版微服务治理方 案 微服务引擎 MSE 还提供了企业级微服务治理中心，包括全链路灰度、服务预热、无损上下线和离群实例摘除等更多更强大的治理能力，同时还提供了企业级 Nacos 注册配置中心，企业级云原生网关等多种产品及解决方案



------

### 核心组件



* Sentinel

  把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性

* Nacos

  一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台

* RocketMQ

  一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的 消息发布与订阅服务

* Dubbo

  一款高性能 Java RPC 框架

* Seata

  阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案

* OSS

  阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访 问任意类型的数据

* Alibaba Cloud SchedulerX

  阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、 高可靠、高可用的定时（基于 Cron 表达式）任务调度服务



------

## 项目准备

### 项目流程设计



整个项目主要分为用户微服务、商品微服务和订单微服务，整个过程模拟的是用户下单扣减库存的操 作。这里，为了简化整个流程，将商品的库存信息保存到了商品数据表，同时，使用商品微服务来扣减库存。各服务之间的调用流程如下

![image-20231116172526704](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231116172526704.png)

用户微服务、商品微服务和订单微服务的整体流程为：用户通过客户端调用订单微服务的提交订单的接口后，订单微服务会分别调用用户微服务和商品微服务的接口来查询用户信息和商品信息，并校验商品库存是否充足，如果商品库存充足的话，就会保存订单。并且会调用商品微服务的扣减库存的接口来扣减库存



------

### 技术选型



整个项目采用 SpringCloud Alibaba 技术栈实现，主要的技术选型如下所示

* 持久层框架：MyBatis、MyBatis-Plus
* 微服务框架：SpringCloud Alibaba
* 消息中间件：RocketMQ
* 服务治理与配置中心：Nacos
* 负载均衡组件：Ribbon
* 远程服务调用：Fegin
* 服务限流与容错：Sentinel
* 服务网关：SpringCloud-Gateway
* 链路追踪：Sleuth + ZipKin
* 分布式事务：Seata
* 数据存储：MySQL + ElasticSearch



------

### 服务模块



为了方便开发和维护，同时为了模块的复用性，整体项目在搭建时，会将用户微服务、商品微服务和订 单微服务放在同一个 Maven 父工程下，作为父工程的子模块，同时，将用户微服务、商品微服务和订单微服务都会使用的 JavaBean 单独作为一个 Maven 模块，以及各服务都会使用的工具类单独作为一个 Maven 模块

![image-20231116200547985](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231116200547985.png)

本文只记录比较独特或核心功能的代码，完整代码可查看代码仓库：



------

### 服务规划



对于用户微服务、商品微服务和订单微服务来说，每个服务占用的端口和访问的基础路径是不同的，这 里就将每个服务占用的端口和访问的基础路径整理成下表所示

| 服务名称 |   项目名称   | 端口 | 访问基础路径 |
| :------: | :----------: | :--: | :----------: |
| 用户服务 |  shop-user   | 8060 |    /user     |
| 商品服务 | shop-product | 8070 |   /product   |
| 订单服务 |  shop-order  | 8080 |    /order    |



------

### 核心业务逻辑



* 用户模块

  ```java
  /** 
   * @description 用户接口 查询用户信息
   */
  @Slf4j 
  @RestController 
  public class UserController {
  
  	@Autowired
  	private UserService userService;
  	
  	@GetMapping(value = "/get/{uid}") 
  	public User getUser(@PathVariable("uid") Long uid){ 
  		User user = userService.getUserById(uid); 
  		log.info("获取到的用户信息为：{}", JSONObject.toJSONString(user)); 
  		return user;
  	} 
  	
  }
  ```

* 商品模块

  ```java
  /** 
  * @description 商品api ,查询商品信息和更新库存
  */
  @RestController 
  @Slf4j 
  public class ProductController {
      
  	@Autowired 
  	private ProductService productService;
  
  	@GetMapping(value = "/get/{pid}") 
  	public Product getProduct(@PathVariable("pid") Long pid){ 
  	
  		Product product = productService.getProductById(pid); 
  		log.info("获取到的商品信息为：{}", 
  		JSONObject.toJSONString(product)); 
  		return product;
  	}
  	
  	@GetMapping(value = "/update_count/{pid}/{count}") 
  	public Result<Integer> updateCount(@PathVariable("pid") Long pid,@PathVariable("count") Integer count){ 
  	
  		log.info("更新商品库存传递的参数为: 商品id:{}, 购买数量:{} ", pid, count); 
  		int updateCount = productService.updateProductStockById(count, pid); 
  		Result<Integer> result = new Result<>(HttpCode.SUCCESS, "执行成功",updateCount); 
  		
  		return result;
  	}
  }
  ```

* 订单模块

  ```java
  /*** 
  * @description 下单
  */
  @Service
  @Slf4j
  public class OrderServiceImpl implements OrderService {
  
  	@Autowired
  	private OrderMapper orderMapper;
  	@Autowired
  	private OrderItemMapper orderItemMapper;
  	@Autowired
  	private RestTemplate restTemplate;
  
  	@Override
  	@Transactional(rollbackFor = Exception.class)
  	public void saveOrder(OrderParams orderParams) {
  		// 请求参数校验
  		...
  		User user = restTemplate.getForObject("http://localhost:8060/user/get/" + orderParams.getUserId(), User.class);
  		// 用户信息校验
  		...
  		Product product = restTemplate.getForObject("http://localhost:8070/product/get/" + orderParams.getProductId(), Product.class);
  		// 商品信息及库存校验
  		...
  		
          // 保存订单信息
          ...
  
  		// 生成库存更新请求，扣减库存
  		orderItemMapper.insert(orderItem);Result<Integer> result =
  restTemplate.getForObject("http://localhost:8070/product/update_count/" + orderParams.getProductId() + "/" + orderParams.getCount(), Result.class); 
  		if (result.getCode() != HttpCode.SUCCESS){ 
  			throw new RuntimeException("库存扣减失败");
  		} 
  		log.info("库存扣减成功");
  	} 
  }
  ```




------

## 服务注册与发现

### 硬编码的问题



如果将用户微服务和商品微服务所在的 IP 地址和端口号硬编码到订单微服务中，会存在非常多的问题， 其中，最明显的问题有三个

1. 如果用户微服务和商品微服务的 IP 地址或者端口号发生了变化，则订单微服务将变得不可用，需要对同步修改订单微服务中调用用户微服务和商品微服务的 IP 地址和端口号
2. 如果系统中提供了多个用户微服务和商品微服务，则无法实现微服务的负载均衡功能
3. 如果系统需要支持更高的并发，需要部署更多的用户微服务和商品微服务以及订单微服务，如果将用户微服务和商品微服务的 IP 地址和端口硬编码到订单微服务，则后续的维护会变得异常复杂

所以，在微服务开发的过程中，需要引入服务治理功能，实现微服务之间的动态注册与发现



------

### 服务治理



如果系统采用了微服务的架构模式，随着微服务数量的不断增多，服务之间的调用关系会变得纵横交错，以纯人工手动的方式来管理这些微服务以及微服务之间的调用关系是及其复杂的，也是极度不可取的。所以，需要引入服务治理的功能。服务治理也是在微服务架构模式下的一种最核心和最基本的模块，主要用来实现各个微服务的自动注册与发现

引入服务治理后，微服务项目总体上可以分为三个大的模块：服务提供者、服务消费者和注册中心，三 者的关系如下图所示

![image-20231116224353724](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231116224353724.png)

* 服务提供者会将自身提供的服务注册到注册中心，并向注册中心发送心跳信息来证明自己还存活， 其中，心跳信息中就会包含服务提供者自身提供的服务信息
* 注册中心会存储服务提供者上报的信息，并通过服务提供者发送的心跳来更新服务提供者最后的存活时间，如果超过一段时间没有收到服务提供者上报的心跳信息，则注册中心会认为服务提供者不可用，会将对应的服务提供者从服务列表中剔除
* 服务消费者会向注册中心订阅自身监听的服务，注册中心会保存服务消费者的信息，也会向服务消费者推送服务提供者的信息
* 服务消费者从注册中心获取到服务提供者的信息时，会直接调用服务提供者的接口来实现远程调用



------

### 常见的注册中心



从上面的分析可以看出，微服务实现服务治理的关键就是引入了注册中心，它是微服务架构模式下一个非常重要的组件，主要实现了服务注册与发现，服务配置和服务的健康检测等功能

能够实现注册中心功能的组件有很多，但是常用的组件大概包含：Zookeeper、Eureka、Consul、 Etcd、Nacos 等

* Zookeeper

  Zookeeper 是 Apache Hadoop 的一个子项目，它是 一个分布式服务治理框架，主要用来解决应用开发中遇到的一些数据管理问题，例如：分布式集群管 理、元数据管理、分布式配置管理、状态同步和统一命名管理等。在高并发环境下，也可以通过 Zookeeper 实现分布式锁功能

* Eureka

  Eureka 是 Netflix 开源的 SpringCloud 中支持服务注册与发现的组件，但是 Eureka 2.X 后来闭源了

* Consul

  Consul 是 HashiCorp 公司推出的开源产品，用于实现分布式系统的服务发现、服务隔离、服务配置， 这些功能中的每一个都可以根据需要单独使用，也可以同时使用所有功能

* ETCD

  ETCD 是一个高度一致的分布式键值存储，它提供了一种可靠的方式来存储需要由分布式系统或机器集群访问的数据。它可以优雅地处理网络分区期间的领导者选举，即使在领导者节点中也可以容忍机器故障

* Nacos

  Nacos是阿里巴巴开源的一款更易于构建云原生应用的支持动态服务发 现、配置管理和服务管理的平台，其提供了一组简单易用的特性集，能够快速实现动态服务发现、服务配置、服务元数据及流量管理



------

### 搭建 Nacos 环境



1. 到 https://github.com/alibaba/nacos/releases 链接下载 Nacos 的安装包，这里下载的安装包为：nacos-server-1.4.3.zip

2. 解压 Nacos 安装包，并在命令行进入到 Nacos 的 bin 目录下执行如下命令以单机的方式启动 Nacos

   如果需要以单机的方式启动 Nacos，则需要添加 -m standalone 参数，否则，Nacos 会以集群 的方式启动

   ```
   startup.cmd -m standalone
   ```

3. 启动 Nacos 之后，在浏览器中输入链接 http://localhost:8848/nacos  来访问 Nacos 的管理界 面，默认的用户名和密码都是 Nacos，如下所示

   ![image-20231116233958442](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231116233958442.png)

4. 进入到 Nacos 的服务管理-服务列表菜单下，如下所示

   ![image-20231116234040809](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231116234040809.png)

   可以看到，在 Nacos 的服务管理-服务列表菜单下还没有任何服务，接下来，就对项目的代码进行改造



------

### 实现服务注册



引入 Nacos 注册中心时，我们需要对项目的代码进行一定的改造，以便利用 Nacos 实现服务的注册与发现功能



1. 在用户微服务的 pom.xml 文件中添加 nacos 的服务注册与发现依赖

   ```xml
   <dependency> 
   	<groupId>com.alibaba.cloud</groupId> 
   	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

2. 在用户微服务的 resources 目录下的 application.yml 文件中添加 Nacos 注册中心的服务地址配置

   ```yaml
   spring: 
     cloud: 
       nacos: 
         discovery: 
           server-addr: 127.0.0.1:8848
   ```

3. 在用户微服务的启动类上标注 @EnableDiscoveryClient 注解

   ```java
   /** 
    * @description 服务启动类 
    */
   @SpringBootApplication 
   @EnableDiscoveryClient 
   public class UserStarter {
   
   	public static void main(String[] args){ 
   		SpringApplication.run(UserStarter.class, args);
   	} 
   	
   }
   ```

4. 启动用户微服务，并刷新 Nacos 页面，可以看到，用户微服务已经成功注册到 Nacos 中

   ![image-20231118000200328](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231118000200328.png)

5. 使用同样的方式改造商品和订单微服务



------

### 实现服务发现和远程调用



1. 注入 DiscoveryClient 类，并添加一个方法，根据 Service Name 获取地址信息

   ```java
   public class OrderService implements OrderService {
   
   	@Autowired 
   	private DiscoveryClient discoveryClient;
   	
   	private String getServiceUrl(String serviceName){ 
   		ServiceInstance serviceInstance = discoveryClient.getInstances(serviceName).get(0); 
   		return serviceInstance.getHost() + ":" + serviceInstance.getPort();
   	}
   
   }
   ```

2. 有了服务发现之后，调用其它服务的接口就不再硬编码地址了，而是通过注册中心获取

   ```
   // 示例代码
   String userUrl = getServiceUrl("server-user");
   User user = restTemplate.getForObject("http://" + userUrl + "/user/get/" + orderParams.getUserId(), User.class);
   ```

3. 对所有需要调用其它服务接口的地方都进行修改



------

## 服务调用的负载均衡

### 简单的负载均衡



在上面获取服务的地址信息时，总是取第一个 Service 的，我们可以改成随机取一个来实现多 Service 的均衡调用

```java
private String getServiceUrl(String serviceName){ 

	List<ServiceInstance> instances = discoveryClient.getInstances(serviceName); 
	int index = new Random().nextInt(instances.size()); 
	ServiceInstance serviceInstance = instances.get(index); 
	String url = serviceInstance.getHost() + ":" + serviceInstance.getPort(); 
	log.info("负载均衡后的服务地址为:{}", url); 
	
	return url;
}
```



------

### 使用 Ribbon 实现负载均衡



Ribbon 是 SpringCloud 提供的一个能够实现负载均衡的组件，使用 Ribbon 能够轻松实现微服务之间调用的负载均衡



1. 首先在 RestTemplate 上添加 LoadBalanced 注解

   ```java
   @Bean 
   @LoadBalanced 
   public RestTemplate restTemplate(){ 
   	return new RestTemplate();
   }
   ```

2. 使用了 Ribbon，调用其它微服务时就不用再使用端口和地址了，使用 Service Name 即可

   ```java
   String userServer = "user-server";
   User user = restTemplate.getForObject("http://" + userServer + "/user/get/" + orderParams.getUserId(), User.class);
   ```



------

### 使用 Feign 实现负载均衡



Fegin 是 SpringCloud 提供的一个 HTTP 客户端，但只是一个声明式的伪客户端，它能够使远程调用和本地调用一样简单。阿里巴巴开源的 Nacos 能够兼容 Ribbon，而 Fegin 又集成了 Ribbon，所以，使用 Fegin 也能够实现负载均衡



1. 引入依赖

   ```
   <dependency> 
   	<groupId>org.springframework.cloud</groupId> 
   	<artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   ```

2. 在启动类上添加 @EnableFeignClients 注解

   ```java
   @SpringBootApplication 
   @EnableDiscoveryClient 
   @EnableFeignClients public class OrderStarter { 
   
   	public static void main(String[] args){ 
   		SpringApplication.run(OrderStarter.class, args);
   	} 
   	
   }
   ```

3. 在 order 服务下创建一个 UserService 接口

   ```java
   @FeignClient("server-user") 
   public interface UserService { 
   
   	@GetMapping(value = "/user/get/{uid}") 
   	User getUser(@PathVariable("uid") Long uid);
   	
   }
   ```

4. 之后直接使用 UserService 调用接口就行了

   ```java
   User user = userService.getUser(orderParams.getUserId());
   ```

5. 以同样的方式修改其它调用接口的代码



------

## Sentinel 实现服务限流与容错

### 常见的服务容错方案





服务容错在一定程度上就是尽最大努力来兼容错误情况的发生，因为在分布式和微服务环境中，不可避免的会出现一些异常情况，我们在设计分布式和微服务系统时，就要考虑到这些异常情况的发生，使得系统具备服务容错能力

常见的服务错误方案包含：服务限流、服务隔离、服务超时、服务熔断和服务降级等



* 服务限流

  服务限流就是限制进入系统的流量，以防止进入系统的流量过大而压垮系统。其主要的作用就是保护服 务节点或者集群后面的数据节点，防止瞬时流量过大使服务和数据崩溃（如前端缓存大量实效），造成 不可用；还可用于平滑请求

  限流算法有两种，一种就是简单的请求总量计数，一种就是时间窗口限流（一般为 1 s），如令牌桶算法和漏牌桶算法就是时间窗口的限流算法

* 服务隔离

  服务隔离有点类似于系统的垂直拆分，就按照一定的规则将系统划分成多个服务模块，并且每个服务模块之间是互相独立的，不会存在强依赖的关系。如果某个拆分后的服务发生故障后，能够将故障产生的影响限制在某个具体的服务内，不会向其他服务扩散，自然也就不会对整体服务产生致命的影响

  互联网行业常用的服务隔离方式有：线程池隔离和信号量隔离

* 服务超时

  整个系统采用分布式和微服务架构后，系统被拆分成一个个小服务，就会存在服务与服务之间互相调用的现象，从而形成一个个调用链。形成调用链关系的两个服务中，主动调用其他服务接口的服务处于调用链的上游，提供接口供其他服务调用的服务处于调用链的下游

  服务超时就是在上游服务调用下游服务时，设置一个最大响应时间，如果超过这个最大响应时间下游服 务还未返回结果，则断开上游服务与下游服务之间的请求连接，释放资源

* 服务熔断

  在分布式与微服务系统中，如果下游服务因为访问压力过大导致响应很慢或者一直调用失败时，上游服务为了保证系统的整体可用性，会暂时断开与下游服务的调用连接。这种方式就是熔断

  服务熔断一般情况下会有三种状态：关闭、开启和半熔断

  * 关闭状态：服务一切正常，没有故障时，上游服务调用下游服务时，不会有任何限制
  * 开启状态：上游服务不再调用下游服务的接口，会直接返回上游服务中预定的方法
  * 半熔断状态：处于开启状态时，上游服务会根据一定的规则，尝试恢复对下游服务的调用。此时， 上游服务会以有限的流量来调用下游服务，同时，会监控调用的成功率。如果成功率达到预期，则进入关闭状态。如果未达到预期，会重新进入开启状态

* 服务降级

  服务降级，说白了就是一种服务托底方案，如果服务无法完成正常的调用流程，就使用默认的托底方案来返回数据



------

### 关于 Sentinel



随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性



Sentinel 的特征有：

* 丰富的应用场景：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等
* 完备的实时监控：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况
* 广泛的开源生态：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Apache Dubbo、gRPC、Quarkus 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。同时 Sentinel 提供 Java/Go/C++ 等多语言的原生实现
* 完善的 SPI 扩展机制：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等



![image-20231118211209727](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231118211209727.png)



------

### 安装 Sentinel 控制台



Sentinel 提供一个轻量级的控制台, 它提供机器发现、单机资源实时监控以及规则管理等功能



1. 到链接 https://github.com/alibaba/Sentinel/releases 下载 Sentinel 控制台，如下所示，下载的 Sentinel 控制台是1.8.4 版本

   ![image-20231118211430227](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231118211430227.png)

2. Sentinel 控制台下载完成后，在本地启动 Sentinel 控制台

   ```sh
   java -Dserver.port=8888 -Dcsp.sentinel.dashboard.server=localhost:8888 Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.4.jar
   ```

   如果想在 CentOS 服务器上以后台进程方式启动 Sentinel 控制台，可以使用如下命令

   ```sh
   nohup java -Dserver.port=8888 -Dcsp.sentinel.dashboard.server=localhost:8888 Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.4.jar >> /dev/null &
   ```

3. 在浏览器中输入 http://localhost:8888 访问 Sentinel 控制台

   输入默认的用户名 sentinel 和密码 sentinel，登录 Sentinel 控制台

   ![image-20231118211731869](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231118211731869.png)



------

### 项目集成 Sentinel



1. 在订单微服务的 shop-order 的 pom.xml 文件中添加 Sentinel 的相关依赖

   ```xml
   <dependency> 
   	<groupId>com.alibaba.cloud</groupId> 
   	<artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   </dependency>
   ```

2. 在订单微服务的 shop-order 的 application.yml 文中加入Sentinel 相关的配置

   ```yaml
   spring: 
   	cloud: 
   		sentinel: 
   			transport: 
   				port: 9999
   #指定和Sentinel控制台交互的端口，任意指定一个未使用的端口即可 dashboard: 127.0.0.1:8888 #Sentinel控制台服务地址
   ```

3. 新增一个测试接口

   ```java
   @GetMapping(value = "/test_sentinel") 
   public String testSentinel(){ 
   
   	log.info("测试Sentinel"); 
   	return "sentinel";
   	
   }
   ```

4. 启动订单微服务，在浏览器中输入 http://localhost:8080/order/test_sentinel 访问在订单微服务中新增的接口

5. 刷新 Sentinel 页面，会发现已经显示了订单微服务的菜单

   ![image-20231118212554681](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231118212554681.png)

   需要注意的是，直接启动订单微服务和 Sentinel，会发现 Sentinel 中没有订单微服务的数据，因为 Sentinel 是懒加载机制，所以需要访问一下接口，再去访问 Sentinel 就有数据了



------

### 集成 Sentinel 限流



使用 Sentinel 为 http://localhost:8080/order/test_sentinel 接口限流，步骤如下



1. 在 Sentinel 控制台找到 server-order 下的簇点链路菜单

2. 在簇点链路列表中找到 /test_sentinel，在右侧的操作中选择流控

3. 点击流控按钮会显示新增流控规则的弹出框，单机阈值后直接填写 1，配置好之后点击新增按钮。配置表示 http://localhost:8080/order/test_sentinel 接口的  QPS 为 1，每秒访问 1 次。如果每秒访问的次数超过 1 次，则会被 Sentinel 限流

   ![image-20231118231524333](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231118231524333.png)

4. 在浏览器上不断刷新 http://localhost:8080/order/test_sentinel 接口，当每秒中访问的次数超过 1次时，会被 Sentinel 限流



------

### Feign 整合 Sentinel 限流实现容错



1. 引入依赖

   ```xml
   <dependency> 
   	<groupId>com.alibaba.cloud</groupId> 
   	<artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   </dependency>
   ```

2. 开启开关

   ```yaml
   feign: 
     sentinel: 
       enabled: true
   ```

3. 在 Feign 接口上添加 Fallback 参数

   ```java
   @FeignClient(value = "server-product", fallback = ProductServiceFallBack.class) 
   public interface ProductService {
   
   	/** 
   	 * 获取商品信息 
   	 */
   	 @GetMapping(value = "/product/get/{pid}") 
   	 Product getProduct(@PathVariable("pid") Long pid);
   
   }
   ```

4. 创建 Fallback 类实现服务降级方法

   ```java
   @Component public class ProductServiceFallBack implements ProductService { 
   
   	@Override public Product getProduct(Long pid) { 
   		Product product = new Product();
   		product.setId(-1L); 
   		return product;
   		
   	}
   }
   ```



或者使用 FallbackFactory，可以获得抛出的异常信息

```java
@FeignClient(value = "server-user", fallbackFactory = UserServiceFallBackFactory.class) 
public interface UserService { 

	@GetMapping(value = "/user/get/{uid}") 
	User getUser(@PathVariable("uid") Long uid);
	
}
```

```java
@Component public class UserServiceFallBackFactory implements FallbackFactory<UserService> {

	@Override public UserService create(Throwable cause) { 
		return new UserService() { 
			@Override public User getUser(Long uid) {
				User user = new User(); 
				user.setId(-1L); 
				
				return user;
			} 
		}; 
	} 
}
```

需要注意的是：使用 Sentinel 实现服务容错时，fallback 和 fallbackFactory 只能使用其中一种 方式实现服务容错，二者不能同时使用



------

## Sentinel 核心技术与配置规则

### Sentinel 核心功能



随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。所以，Sentinel 的核心功能包括：流量控制、熔断降级、系统负载保护

* 流量控制

  在高并发、大流量场景下，进入系统的流量如果不加控制的话，系统就很有可能会被流量压垮。所以， 在流量正式进入系统之前，需要对流量进行控制，以便使流量均匀、可控的方式进入系统

  Sentinel 作为一个非常出色的容错组件，能够将不可控的流量经过处理转化成均匀、可控的流量

* 熔断降级

  如果检测到系统中的某个调用链路中某个节点出现故障，比如请求超时、服务宕机或者异常比超出一定阈值时，就会对出现故障的节点的调用频率进行限制，甚至不调用出现故障的节点，让请求能够快速失败并返回，以最大程度避免影响到其他节点的服务而导致系统的级联故障

  Sentinel 主要通过限制并发线程数和响应时间对资源的访问进行降级

  1. 限制并发线程数

     Sentinel 可以通过限制服务节点的并发线程数量，来减少对其他服务节点的影响。例如，当某个服务节点出现故障，例如响应时间变长，或者直接宕机。此时，对服务的直接影响就是会造成请求线程数的不断堆积。如果这些堆积的线程数达到一定的数量后，对当前服务节点的后续请求就会被拒绝，等到堆积的线程完成任务后再开始继续接收新的请求

  2. 通过响应时间

     Sentinel 除了可以通过限制并发线程数进行降级外，也能够通过响应时间进行降级。如果依赖的服务出现响应时间过长的情况，则所有对该服务的请求都会被拒绝，直到过了指定的时间窗口之后才能再次访问该服务

* 系统负载保护

  Sentinel 提供了系统维度的自适应保护能力。当系统的压力和负载比较高的时候，如果还持续让大量的请求进入系统，此时就有可能将系统压垮，进而导致系统宕机。Sentinel 会在集群环境下，将本应服务器 A 承载的流量转发到其他服务器上，比如转发到服务器 B 上。如果此时服务器 B 也处于高负载的状态， 则 Sentinel 会提供相应的保护机制，让系统的入口流量和系统的整体负载达到平衡，让系统整体可用， 并且能够最大限度的处理请求



------

### 流控规则



Sentinel 能够对流量进行控制，主要是监控应用的 QPS 流量或者并发线程数等指标，如果达到指定的阈值时，就会被流量进行控制，以避免服务被瞬时的高并发流量击垮，保证服务的高可靠性



1. 点击簇点链路菜单，可以看到之前访问过的接口

2. 点击右侧的流控按钮，会弹出新增流控规则的提示框

   ![image-20231119121035669](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119121035669.png)

   配置项的说明如下：

   * 资源名：资源的唯一名称，默认就是请求的接口路径，可以自行修改，但是要保证唯一
   * 针对来源：具体针对某个微服务进行限流，默认值为 default，表示不区分来源，全部限流
   * 阈值类型：QPS 表示通过 QPS 进行限流，并发线程数表示通过并发线程数限流
   * 单机阈值：与阈值类型组合使用。如果阈值类型选择的是 QPS，表示当调用接口的 QPS 达到阈值时，进行限流操作。如果阈值类型选择的是并发线程数，则表示当调用接口的并发线程数达到阈值时，进行限流操作
   * 是否集群：选中则表示集群环境，不选中则表示非集群环境

3. 上文中展示的限流规则便是简单限流，此处便不再展示

4. 点击高级选项配置

   ![image-20231119121335455](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119121335455.png)

   可以看到，Sentinel 主要提供了三种流控模式，分别为直接、关联和链路

   * 直接：默认的流控模式，当接口达到限流条件时，直接开启限流功能
   * 关联：当关联的资源达到限流条件时，开启限流功能
   * 链路：当从某个接口请求过来的资源达到限流条件时，开启限流功能



简单限流使用的便是直接流控模式，因此只展示关联流控和链路流控

首先展示关联流控

在订单微服务的中新增 /test_sentinel2 接口

```java
@GetMapping(value = "/test_sentinel2") 
public String testSentinel2(){ 

	log.info("测试Sentinel2"); 
	return "sentinel2";
	
}
```

在浏览器上访问下 http://localhost:8080/order/test_sentinel2 接口，以便使 Sentinel 检测到 http://localhost:8080/order/test_sentinel2 接口

如果想使用 Sentinel 对某个接口进行限流和降级等操作，一定要先访问下接口，使 Sentinel 检测出相应的接口

在流控模式中选择关联，在关联资源中输入 /test_sentinel2

![image-20231119124111019](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119124111019.png)

这时候，便是 /test_sentinel 和 /test_sentinel2 两个接口加起来允许的 QPS 为 3 了



然后是链路流控，首先在订单微服务创建一个测试接口

```JAVA
@Slf4j 
@RestController 
public class SentinelController {

	@Autowired 
	private SentinelService sentinelService;
	
	@GetMapping(value = "/request_sentinel1") 
	public String requestSentinel1(){ 
	
		log.info("测试Sentinel1"); 
		sentinelService.sendMessage(); 
		return "sentinel1";
	} 
	
	@GetMapping(value = "/request_sentinel2") 
	public String requestSentinel2(){ 
		log.info("测试Sentinel2"); 
		sentinelService.sendMessage(); 
		return "sentinel2";
	} 	
}
```

```JAVA
@Service 
public class SentinelServiceImpl implements SentinelService { 

	@Override 
	@SentinelResource("sendMessage") 
	public void sendMessage() { 
		System.out.println("测试Sentinel的链路流控模式");
	} 	
}
```

其次，需要升级 SpringCloud Alibaba 的依赖版本到 2.2.7.RELEASE，升级 SpringCloud 版本到 Hoxton.SR12，还要升级 Nacos，将 Nacos 注册中心由 1.4.3 版本升级为 2.1.0 版本

在订单微服务的 application.yml 文件中新增链路配置

```yaml
spring: 
  cloud: 
    sentinel: 
      web-context-unify: false
```

在浏览器中分别访问 http://localhost:8080/order/request_sentinel1 和 http://localhost:8080/order/request_sentinel2，查看 Sentinel中 的簇点链路

点击 sendMessage 后面的流控按钮

![image-20231119122440626](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119122440626.png)

在弹出的新增流控规则编辑框中阈值类型选择 QPS，单机阈值输入 3，在打开的高级选项中的流控 模式选择链路，入口资源输入 /request_sentinel1

![image-20231119122506522](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119122506522.png)

点击新增按钮保存配置，如果是访问 /request_sentinel1 接口，如果调用的频率每秒钟超过 3 次，就会触发 Sentinel 的限流操作，而通过 /request_sentinel2 接口调用则不受限制



在流控效果的地方还有三个选项：

* 快速失败：会直接失败，抛出异常，期间不会做任何其他的处理操作
* Warm Up：从开始阈值到最大 QPS 阈值会有一个缓冲，可以设置一个预热时长，这个选项比较适合突发瞬时高并发流量的场景，能够将突发的高并发流量转换为均匀、缓慢增长的场景
* 排队等待：能够使请求均匀的通过，单机的阈值为每秒通过的请求数量，其余的请求会排队等待。 另外，还会设置一个超时时间，当请求超过超时时间未处理时，会被丢弃



------

### 熔断规则



Sentinel 主要提供了三个熔断策略，分别为：慢调用比例、异常比例和异常数

顾名思义，便是基于这几个参数触发熔断，很好理解，便不做展示了



------

### 热点规则



Sentinel 的热点规则可以根据具体的参数来控制流量规则，适用于根据不同参数进行流量控制的场景

1. 在订单微服务中新增 requestSentinel3 方法，其中会带有一个 String 类型的参数 header 和一个 String 类型的参数 body

   ```java
   @GetMapping(value = "/request_sentinel3") 
   @SentinelResource("request_sentinel3") 
   public String requestSentinel3(String header, String body){ 
   
   	log.info("测试Sentinel3"); 
   	return "sentinel3";
   }
   ```

2. 在浏览器中访问 http://localhost:8080/order/request_sentinel3 接口，在 Sentinel 的簇点 链路中会显示 /request_sentinel3 接口

   点击热点按钮，在弹出的热点规则配置框中的参数索引中输入 0，单机阈值输入 1，统计窗口时长输入 1

   ![image-20231119225648215](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119225648215.png)

   表示对 requestSentinel3 方法的第一个参数 header 进行限流，如果每秒钟访问的次数超过 1 次，则触发限流

3. 保存配置后，在浏览器中不断访问 http://localhost:8080/order/request_sentinel3? header=header，当每秒访问的频率超过 1 次时，会触发 Sentinel 的限流操作

   不断访问 http://localhost:8080/order/request_sentinel3?body=body，则不会触发限流操作



还可以针对某个特定的参数而不是顺序进行限流

在弹出的热点规则配置框中打开高级选项，在参数类型中选择 java.lang.String，因为在参数索引 中输入 0，表示的是对 header 参数限流，而 header 参数是 String 类型的。在参数值里输入 header，也就是为参数名为 header 的参数赋值为字符串 header。限流阈值为 1

![image-20231119233939379](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231119233939379.png)

点击保存按钮，在浏览器不断刷新 http://localhost:8080/order/request_sentinel3? header=header，会触发 Sentinel 的限流操作



------

### 授权规则



在某些场景下，需要根据调用接口的来源判断是否允许执行本次请求。此时就可以使用 Sentinel 提供的授权规则来实现，Sentinel 的授权规则能够根据请求的来源判断是否允许本次请求通过

在 Sentinel 的授权规则中，提供了白名单与黑名单两种授权类型



1. 在订单微服务中创建 MyRequestOriginParser 类，实现 RequestOriginParser 接口，用 来处理请求的来源

   ```java
   @Component 
   public class MyRequestOriginParser implements RequestOriginParser { 
   
   	@Override 
   	public String parseOrigin(HttpServletRequest httpServletRequest) { 
   	return httpServletRequest.getParameter("serverName");
   	
   	} 
   }
   ```

2. 在浏览器中访问 http://localhost:8080/order/request_sentinel4，在 Sentinel 的簇点 链路里找到 /request_sentinel4

3. 点击授权按钮，进入授权规则配置框，按照如下方式进行配置

   ![image-20231120213844616](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231120213844616.png)

   其中，流控应用填写的是 test，授权类型为黑名单

   如果我们访问 http://localhost:8080/order/request_sentinel4?serverName=test 的话， 是处于黑名单的状态，无法访问



------

### 系统规则



系统保护规则是应用整体维度的，而不是资源维度的，并且仅对入口流量 (进入应用的流量) 生效

* Load（仅对 Linux/Unix-like 机器生效）：当系统 load1 超过阈值，且系统当前的并发线程数超过系统容量时才会触发系统保护。系统容量由系统的 maxQps * minRt 计算得出。设定参考值一般是 CPU cores * 2.5
* RT：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒
* 线程数：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护
* 入口 QPS：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护
* CPU使用率：当单台机器上所有入口流量的 CPU使用率达到阈值即触发系统保护



1. 在订单微服务创建 MyUrlBlockHandler 类，实现 BlockExceptionHandler 接口， 用来捕获系统级 Sentinel 异常

   ```java
   @Component 
   public class MyUrlBlockHandler implements BlockExceptionHandler { 
   
   	@Override 
   	public void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception { 
   		String msg = null; 
   		if (e instanceof FlowException) { 
   			msg = "限流了";
   		} else if (e instanceof DegradeException) { 
   			msg = "降级了";
   		} else if (e instanceof ParamFlowException) { 
   			msg = "热点参数限流";
   		} else if (e instanceof SystemBlockException) { 
   			msg = "系统规则（负载/...不满足要求）";
   		} else if (e instanceof AuthorityException) { 
   			msg = "授权规则不通过";
   		} 
   		// http状态码 
   		response.setStatus(500); 
   		response.setCharacterEncoding("utf-8"); 
   		response.setHeader("Content-Type", "application/json;charset=utf-8");
   		response.setContentType("application/json;charset=utf-8"); 
   		JSONObject jsonObject = new JSONObject(); 
   		jsonObject.put("code", 500); 
   		jsonObject.put("codeMsg", msg); 
   		response.getWriter().write(jsonObject.toJSONString());
   } }
   ```

2. 在订单微服务的 SentinelController 类中新增 requestSentinel5() 方法

   ```java
   @GetMapping(value = "/request_sentinel5") 
   @SentinelResource("request_sentinel5") 
   public String requestSentinel5(){ 
   	log.info("测试Sentinel5"); 
   	return "sentinel5";
   }
   ```

3. 在浏览器中访问 http://localhost:8080/order/request_sentinel5，在 Sentinel 的簇点 链路里找到 /request_sentinel5

4. 点击流控按钮，进入流控规则配置框，按照如下方式进行配置

   ![image-20231120224552539](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231120224552539.png)

5. 在浏览器中不断刷新 http://localhost:8080/order/request_sentinel5，会显示如下信息

   ```
   {
   	"code":500,
   	"codeMsg":"限流了"
   }
   ```

   说明触发了系统规则，捕获到了 Sentinel 全局异常



------

### SentinelResource 注解



1. 在订单微服务新建 MyBlockHandlerClass 类，用于定义被 Sentinel 限流时的方法

   ```java
   @Slf4j 
   public class MyBlockHandlerClass {
   	public static String blockHandler(BlockException e){ 
   		log.error("限流了:{}", e); 
   		return "限流了";
   	} 
   }
   ```

2. 在订单微服务新建 MyFallbackClass 类，用于定义抛出异常时调用的方法

   ```java
   @Slf4j 
   public class MyFallbackClass {
   	public static String fallback(Throwable e){ 
   		log.error("异常了:{}", e); 
   		return "异常了";
   	} 
   }
   ```

3. sendMessage2() 方法上 的注解

   ```java
   @Override 
   @SentinelResource( 
   	value = "sendMessage2", 
   	blockHandlerClass = MyBlockHandlerClass.class, 
   	blockHandler = "blockHandler", 
   	fallbackClass = MyFallbackClass.class, 
   	fallback = "fallback")
   public String sendMessage2() { 
   	count ++; 
   	System.out.println(count); 
   	//25%的异常率 
   	if (count % 4 == 0){ 
   		throw new RuntimeException("25%的异常率");
   	} 
   	return "sendMessage2";
   }
   ```

4. 浏览器中访问 http://localhost:8080/order/request_sentinel6，在 Sentinel 的簇点链路里找到 /request_sentinel6

5. 点击流控按钮进入流控规则页面，按照下图方式进行配置

   ![image-20231120230914790](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231120230914790.png)

6. 点击新增按钮后在浏览器中刷新 http://localhost:8080/order/request_sentinel6，当刷新的频率超过每秒2次时就会返回“限流了”，当刷新的次数是4的倍数时，就会返回异常了



------

## 服务网关

### 主流的 API 网关



当系统采用分布式、微服务的架构模式后，API 网关就成了整个系统不可分割的一部分。业界通过不断的探索与创新，实现了多种 API 网关的解决方案。目前，比较主流的 API 网关有：Nginx+Lua、Kong 网关、 Zuul 网关、Apache Shenyu 网关、SpringCloud Gateway 网关

* Nginx + Lua

  Nginx 的一些插件本身就实现了限流、缓存、黑白名单和灰度发布，再加上 Nginx 的反向代理和负载均衡，能够实现对服务接口的负载均衡和高可用。而 Lua 语言可以实现一些简单的业务逻辑，Nginx 又支持 Lua 语言。所以，可以基于 Nginx + Lua 脚本实现网关

* Kong 网关

  Kong 网关基于 Nginx 与 Lua 脚本开发，性能高，比较稳定，提供多个限流、鉴权等插件，这些插件支持 热插拔，开箱即用。Kong 网关提供了管理页面，但是，目前基于 Kong 网关二次开发比较困难

* Zuul 网关

  Zuul 网关是 Netflix 开源的网关，功能比较丰富，主要基于 Java 语言开发，便于在 Zuul 网关的基础上进行二次开发。但是 Zuul 网关无法实现动态配置规则，依赖的组件相对来说也比较多，在性能上不如 Nginx

* Apache Shenyu 网关

  Dromara 社区开发的网关框架，ShenYu 的前名是 soul，最近正式加入了 Apache 的孵化器，因此改名为 ShenYu。其是一个异步的，高性能的，跨语言的，响应式的 API 网关，并在此基础上提供了非常丰富的扩展功能

* Spring Cloud Gateway 网关

  Spring 为了替换 Zuul 而开发的网关，SpringCloud Alibaba 技术栈中，并没有单独实现网关的组件。在后续的案例实现中，我们会使用 Spring Cloud Gateway 实现网关功能



------

### Spring Cloud Gateway 概述



Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，基于 Spring 5.0 + Spring Boot 2.0 和 Project Reactor 等技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。Spring Cloud Geteway 作为 Spring Cloud 生态系统中的网关，目标是替代 Zuul，在 Spring Cloud2.0 以上版本 中，没有对新版本的 Zuul 2.0 以上最新高性能版本进行集成，仍然还是使用的 Zuul 1.x 非 Reactor 模式的 老版本。而为了提升网关性能，Spring Cloud Gateway 是基于 WebFlux 框架实现的，而 WebFlux 框架底 层则使用了高性能的 Reactor 模式通信框架 Netty

Spring Cloud Gateway 的目标提供统一的路由方式且基于 Filter 链的方式提供了网关基本的功能，例如： 安全，监控/指标和限流



客户端请求到 Gateway 网关，会先经过 Gateway Handler Mapping 进行请求和路由匹配。匹配成功后再发送到 Gateway Web Handler 处理，然后会经过特定的过滤器链，经过所有前置过滤后，会发送代理请求。请求结果返回后，最后会执行所有的后置过滤器

![image-20231121231653130](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231121231653130.png)

由上图可以看出，Spring Cloud Gateway 的主要流程为：客户端请求会先打到 Gateway，具体的讲应该 是DispacherHandler（因为 Gateway 引入了 WebFlux，作用可以类比 MVC 的 DispacherServlet）， Gateway 根据用户的请求找到相应的 HandlerMapping，请求和具体的 handler 之间有一个映射关系，网关会对请求进行路由，handler会匹配到 RoutePredicateHandlerMapping，匹配请求对应的 Route，然后到达 Web 处理器，WebHandler 代理了一系列网关过滤器和全局过滤器的实例，这些过滤器可以对请求和响应进行修改，最后由代理服务完成用户请求，并将结果返回



------

### 项目集成 Spring Cloud Gateway



1. 新建一个项目模块 shop-gateway，引入以下依赖

   ```xml
   <dependency> 
   	<groupId>org.springframework.cloud</groupId> 
   	<artifactId>spring-cloud-starter-gateway</artifactId>
   </dependency>
   ```

2. 在服务网关 shop-gateway 模块的 resources 目录下新建 application.yml 文件，并在文件中添加如下配置信息

   ```yaml
   server: 
     port: 10001
   spring: 
     application: 
       name: server-gateway
     cloud: 
       gateway: 
         # 此节点下的配置是为了解决 SpringCloud Gateway 跨域的问题
         globalcors:
           cors-configurations: 
           '[/**]': 
             allowedOrigins: "*" 
             allowedMethods: "*" 
             allowCredentials: true 
             allowedHeaders: "*"
         # 表示一个路由数组，可以在此节点下配置多个路由信息
         routes: 
           # 当前路由的唯一标识
           - id: user-gateway 
             uri: http://localhost:8060 
             # 路由的优先级，数字越小表示优先级越高
             order: 1 
             # 网关断言，也就是路由转发的条件，也是一个数组，可以配置多个路由转发条件
             predicates: 
               # 当客户端请求的路径满足 Path 的规则时，进行路由转发操作
               - Path=/server-user/**
             # 网关过滤器，在过滤器中可以修改请求的参数和 header 信息，以及响应的结果和 header 信息，网关过滤器也是一个数组，可以配置多个过滤规则
             filters:
               # 网关在进行路由转发之前，会去掉 1 层访问路径
               - StripPrefix=1
           - id: product-gateway 
             uri: http://localhost:8070 
             order: 1 
             predicates: 
               - Path=/server-product/**
             filters: 
               - StripPrefix=1
           - id: order-gateway 
             uri: http://localhost:8080 
             order: 1 
             predicates: 
               - Path=/server-order/**
             filters: 
               - StripPrefix=1
   ```

3. 在服务网关 shop-gateway 模块的新建 GatewayStarter 类，表示服务网关的启动类

   ```java
   @SpringBootApplication 
   public class GatewayStarter { 
   	public static void main(String[] args){ 
   		SpringApplication.run(GatewayStarter.class, args);
   	} 
   }
   ```

4. 分别启动用户微服务、商品微服务、订单微服务和服务网关

5. 过服务网关访问用户微服务，在浏览器中输入 http://localhost:10001/serveruser/user/get/1001，返回如下数据

   ```json
   {
   	"id": 1001, 
   	"proName": "华为", 
   	"proPrice": 2399, 
   	"proStock": 100
   }
   ```

   可以看到，通过服务网关能够正确访问到商品微服务



------

### 网关整合 Nacos



在初步整合 Spring Cloud Gateway 中，我们在服务网关模块的 application.yml 文件中硬编码配置了服务转发的地址，下面将网关整合 Nacos 实现从 Nacos 注册中心获取转发的服务地址



1. 在服务网关 shop-gateway 模块的 pom.xml 文件中继续添加如下依赖

   ```xml
   <dependency> 
   	<groupId>com.alibaba.cloud</groupId> 
   	<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

2. 在服务网关 shop-gateway 模块的启动类 GatewayStarter 上添加 @EnableDiscoveryClient 注解

   ```java
   @SpringBootApplication 
   @EnableDiscoveryClient 
   public class GatewayStarter { 
   	public static void main(String[] args){ 
   		SpringApplication.run(GatewayStarter.class, args);
   	} 
   }
   ```

3. 在 application 中添加 Nacos 配置

   ```yaml
   spring: 
     cloud: 
       nacos: 
         discovery: 
           server-addr: 127.0.0.1:8848
           locator: 
             enabled: true
   ```

   另外，将硬编码的服务转发地址修改成从 Nacos 中按照名称获取微服务地址，并按照负载均衡策略分发，例如

   ```
   uri: lb://server-user
   ```



------

### 网关整合 Nacos 最简配置



SpringCloud Gateway 整合 Nacos 后，可以不用手动指定其他微服务的名称来从 Nacos 中获取微服务的 地址。接下来，我们就来实现 Spring Cloud Gateway 网关整合 Nacos 的最简配置



修改 application.yaml 如下

```yaml
server: 
  port: 10001
spring: 
  application: 
    name: server-gateway
  cloud: 
    nacos: 
      discovery: 
        server-addr: 127.0.0.1:8848
    gateway: 
      globalcors:
        cors-configurations: 
          '[/**]': 
            allowedOrigins: "*" 
            allowedMethods: "*" 
            allowCredentials: true 
            allowedHeaders: "*"
      discovery: 
        locator: 
          enabled: true
```

可以看到，在 application.yml 文件中，去掉了 spring.cloud.gateway.routes 节点及其下面的所有配置

在浏览器中输入 http://localhost:10001/serveruser/user/get/1001 发现仍能访问到数据



------

### 整合 Sentinel 限流



Sentinel 从 1.6.0 版本开始，提供了 SpringCloud Gateway 的适配模块，并且可以提供两种资源维度的限流，一种是 route 维度；另一种是自定义 API 分组维度

* route 维度：对 application.yml 文件中配置的 spring.cloud.gateway.routes.id 限流，并且资源名为 spring.cloud.gateway.routes.id 对应的值
* 自定义 API 分组维度：利用 Sentinel 提供的 API 接口来自定义 API 分组，并且对这些 API 分组进行限流



首先来看如何实现 route 维度限流

1. 在服务网关 shop-gateway 模块的 pom.xml 文件中添加如下依赖

   ```xml
   <dependencies> 
   	<dependency> 
   		<groupId>com.alibaba.cloud</groupId> 
   		<artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   	</dependency>
   	<dependency> 
   		<groupId>com.alibaba.cloud</groupId> 
   		<artifactId>spring-cloud-alibaba-sentinel-gateway</artifactId>
   	</dependency>
   	<dependency> 
   		<groupId>org.springframework.cloud</groupId> 
   		<artifactId>spring-cloud-starter-gateway</artifactId>
   	</dependency>
   	<dependency> 
   		<groupId>com.alibaba.cloud</groupId> 
   		<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   	</dependency>
   	<dependency> 
   		<groupId>com.alibaba.csp</groupId> 
   		<artifactId>sentinel-spring-cloud-gateway-adapter</artifactId>
   	</dependency> 
   </dependencies>
   ```

2. 在服务网关模块中新建 GatewayConfig 类。基于 Sentinel 的 Gateway 限流是通过其提供的 Filter 来完成的，使用时只需注入对应的 SentinelGatewayFilter 实例以及 SentinelGatewayBlockExceptionHandler 实例即可

   ```java
   @Configuration
   public class GatewayConfig {
   
       private final List<ViewResolver> viewResolvers;
   
       private final ServerCodecConfigurer serverCodecConfigurer;
   
       @Value("${spring.cloud.gateway.discovery.locator.route-id-prefix}")
       private String routeIdPrefix;
   
       public GatewayConfig(ObjectProvider<List<ViewResolver>> viewResolversProvider,
                                   ServerCodecConfigurer serverCodecConfigurer) {
           this.viewResolvers = viewResolversProvider.getIfAvailable(Collections::emptyList);
           this.serverCodecConfigurer = serverCodecConfigurer;
       }
   
       /**
        * 初始化一个限流的过滤器
        */
       @Bean
       @Order(Ordered.HIGHEST_PRECEDENCE)
       public GlobalFilter sentinelGatewayFilter() {
           return new SentinelGatewayFilter();
       }
   
       @PostConstruct
       public void init() {
          this.initGatewayRules();
          this.initBlockHandlers();
       }
   
       /**
        * 配置初始化的限流参数，基于Route模式
        */
       private void initGatewayRules() {
           Set<GatewayFlowRule> rules = new HashSet<>();
   
           /**
            * Sentinel整合SpringCloud Gateway使用的API类型为Route ID类型时，
            * 由于Sentinel为SpringCloud Gateway网关生成的API名称规则如下：
            * 生成的规则为：${spring.cloud.gateway.discovery.locator.route-id-prefix}后面直接加上目标微服务的名称，如下所示。
            * ${spring.cloud.gateway.discovery.locator.route-id-prefix}目标微服务的名称
            * 其中，${spring.cloud.gateway.discovery.locator.route-id-prefix}是在yml文件中配置的访问前缀
            *
            * 为了让通过服务网关访问目标微服务链接后，请求链路中生成的API名称与流控规则中生成的API名称一致，以达到启动项目即可实现访问链接的限流效果，
            * 而无需登录Setinel管理界面手动配置限流规则，可以将
            * 生成GatewayFlowRule对象的resource参数设置为${spring.cloud.gateway.discovery.locator.route-id-prefix}目标微服务的名称
            *
            * 当然，如果不按照上述配置，也可以在项目启动后，通过服务网关访问目标微服务链接后，在Sentinel管理界面的请求链路中找到对应的API名称所代表的请求链路，
            * 然后手动配置限流规则。
            **/
   //        //用户微服务网关
   //        rules.add(this.getGatewayFlowRule("user-gateway"));
   //        //商品微服务网关
   //        rules.add(this.getGatewayFlowRule("product-gateway"));
   //        //订单微服务网关
   //        rules.add(this.getGatewayFlowRule("order-gateway"));
           //用户微服务网关
           rules.add(this.getGatewayFlowRule(getResource("server-user")));
           //商品微服务网关
           rules.add(this.getGatewayFlowRule(getResource("server-product")));
           //订单微服务网关
           rules.add(this.getGatewayFlowRule(getResource("server-order")));
           //加载规则
           GatewayRuleManager.loadRules(rules);
       }
   
       private String getResource(String targetServiceName){
           if (routeIdPrefix == null){
               routeIdPrefix = "";
           }
           return routeIdPrefix.concat(targetServiceName);
       }
   
       private GatewayFlowRule getGatewayFlowRule(String resource){
           //传入资源名称生成GatewayFlowRule
           GatewayFlowRule gatewayFlowRule = new GatewayFlowRule(resource);
           //限流阈值
           gatewayFlowRule.setCount(1);
           //统计的时间窗口，单位为
           gatewayFlowRule.setIntervalSec(1);
           return gatewayFlowRule;
       }
   
       /**
        * 配置限流的异常处理器
        */
       @Bean
       @Order(Ordered.HIGHEST_PRECEDENCE)
       public SentinelGatewayBlockExceptionHandler sentinelGatewayBlockExceptionHandler() {
           return new SentinelGatewayBlockExceptionHandler(viewResolvers, serverCodecConfigurer);
       }
   
       /**
        * 自定义限流异常页面
        */
       private void initBlockHandlers() {
           BlockRequestHandler blockRequestHandler = new BlockRequestHandler() {
               @Override
               public Mono<ServerResponse> handleRequest(ServerWebExchange serverWebExchange, Throwable throwable) {
                   Map map = new HashMap<>();
                   map.put("code", 1001);
                   map.put("codeMsg", "接口被限流了");
                   return ServerResponse.status(HttpStatus.OK).
                           contentType(MediaType.APPLICATION_JSON_UTF8).
                           body(BodyInserters.fromObject(map));
               }
           };
           GatewayCallbackManager.setBlockHandler(blockRequestHandler);
       }
   }
   
   ```

3. 然后修改 application.yaml

   ```yml
   server:
     port: 10001
   spring:
     application:
       name: server-gateway
     cloud:
       nacos:
         discovery:
           server-addr: 127.0.0.1:8848
       sentinel:
         transport:
           port: 7777
           dashboard: 127.0.0.1:8888
         web-context-unify: false
         eager: true
   
       gateway:
         globalcors:
           cors-configurations:
             '[/**]':
               allowedOrigins: "*"
               allowedMethods: "*"
               allowCredentials: true
               allowedHeaders: "*"
         discovery:
           locator:
             enabled: true
             route-id-prefix: gateway-
   ```

   spring.cloud.sentinel.eager 表示程序启动时，流控规则是否立即注册到 Sentinel，配置为 true 表示立即注册到 Sentinel

   spring.cloud.gateway.discovery.locator.route-id-prefix：生成流控规则 API 名称的前缀

4. 在启动类中添加启动参数

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class GatewayStarter {
       public static void main(String[] args){
           System.setProperty("csp.sentinel.app.type", "1");
           SpringApplication.run(GatewayStarter.class, args);
       }
   }
   ```

5. 这时候启动项目，打开网关控制台，可以在 server-gateway 菜单下的流控规则子菜单中可以看到网关的流控规则已经注册到 Sentinel

   ![image-20231123231700622](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231123231700622.png)





接着来看自定义 API 分组维度限流

1. 在服务网关模块中新增 initCustomizedApis 方法，初始化 API 管理的信息

   ```java
   private void initCustomizedApis() {
       Set<ApiDefinition> definitions = new HashSet<>();
       ApiDefinition api1 = new ApiDefinition("user_api1")
               .setPredicateItems(new HashSet<ApiPredicateItem>() {{
                   // 以/server-user/user/api1 开头的请求
                   add(new ApiPathPredicateItem().setPattern("/server-user/user/api1/**").
                           setMatchStrategy(SentinelGatewayConstants.URL_MATCH_STRATEGY_PREFIX));
               }});
       ApiDefinition api2 = new ApiDefinition("user_api2")
               .setPredicateItems(new HashSet<ApiPredicateItem>() {{
                   // 以/server-user/user/api2/demo1 完成的url路径匹配
                   add(new ApiPathPredicateItem().setPattern("/server-user/user/api2/demo1"));
               }});
       definitions.add(api1);
       definitions.add(api2);
       GatewayApiDefinitionManager.loadApiDefinitions(definitions);
   }
   ```

   user_api1分组：匹配以 /product-serv/product/api1 开头的所有请求

   user_api2分组：精确匹配/server-user/user/api2/demo1

2. 修改 init 方法

   ```java
   @PostConstruct 
   public void init() { 
   	this.initBlockHandlers(); 
   	this.initCustomizedApis();
   }
   ```

3. 启动项目，会发现在 API 管理里面会发现我们定义的 API 分组已经自动注册到 Sentinel 中

   ![image-20231123235937727](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231123235937727.png)

4. 在 Sentinel 管理界面的流控规则中，新增网关流控规则

   ![image-20231124000002460](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231124000002460.png)

5. 点击新增网关流控规则后，会弹出新增网关流控规则配置框，按照如下方式为 user_api1 分组配置限流规则

   ![image-20231124000034500](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231124000034500.png)



------

### Spring Cloud Gateway 内置断言



断言的英文是 Predicate，也可以翻译成谓词。主要的作用就是进行条件判断，可以在网关中实现多种条件判断，只有所有的判断结果都通过时，也就是所有的条件判断都返回 true，才会真正的执行路由功能

SpringCloud Gateway 包括许多内置的断言工厂，所有这些断言都与 HTTP 请求的不同属性匹配



* 基于日期时间类型的断言

  * AfterRoutePredicateFactory： 接收一个日期时间参数，判断当前请求的日期时间是否晚于指定的日期时间
  * BeforeRoutePredicateFactory： 接收一个日期时间参数，判断当前请求的日期时间是否早于指定的日期时间
  * BetweenRoutePredicateFactory： 接收两个日期时间参数，判断当前请求的日期时间是否在指定 的时间时间段内

  ```
  例如： - After=2022-05-10T23:59:59.256+08:00[Asia/Shanghai]
  ```

* 基于远程地址的断言

  RemoteAddrRoutePredicateFactory：接收一个 IP地址段，判断发出请求的客户端的 IP 地址是否在指定的 IP 地址段内

  ```
  例如：- RemoteAddr=192.168.0.1/24
  ```

* 基于 Cookie 的断言

  CookieRoutePredicateFactory：接收两个参数， Cookie 的名称和一个正则表达式。 判断请求的 Cookie 是否具有给定名称且值与正则表达式匹配

  ```
  例如：- Cookie=name, binghe.
  ```

* 基于 Header 的断言

  HeaderRoutePredicateFactory：接收两个参数，请求 Header 的名称和正则表达式。 判断请求 Header 中是否具有给定的名称且值与正则表达式匹配

  ```
  例如：- Header=X-Request-Id, \d+
  ```

* 基于 Host 的断言

  HostRoutePredicateFactory：接收一个参数，这个参数通常是主机名或者域名的模式，例如 **.binghe.com 这种格式。判断发出请求的主机是否满足匹配规则

  ```
  例如：- Host=**.binghe.com
  ```

* 基于 Method 请求方法的断言

  MethodRoutePredicateFactory：接收一个参数，判断请求的类型是否跟指定的类型匹配，通常指的是请求方式。例如 POST、GET、PUT 等请求方式

  ```
  例如：- Method=GET
  ```

* 基于 Path 请求路径的断言

  PathRoutePredicateFactory：接收一个参数，判断请求的链接地址是否满足路径规则，通常指的是请求的 URI 部分

  ```
  例如：- Path=/binghe/{segment}
  ```

* 基于 Query 请求参数的断言

  QueryRoutePredicateFactory ：接收两个参数，请求参数和正则表达式， 判断请求的参数是否具有给定的名称并且参数值是否与正则表达式匹配

  ```
  例如：- Query=name, binghe.
  ```

* 基于路由权重的断言

  WeightRoutePredicateFactory：接收一个[组名,权重]格式的数组，然后对于同一个组内的路由按照权重转发



```yml
- id: weight1 
  uri: http://localhost:8080 
  predicates: 
    - Path=/api/** 
    - Weight=group1,2
  filters: 
    - StripPrefix=1
- id: weight2 
  uri: http://localhost:8081 
  predicates: 
    - Path=/api/** 
    - Weight=group1,8
  filters: 
    - StripPrefix=1
```



------

### 自定义断言



1. 在网关服务下新建 NameRoutePredicateConfig 类，主要定义一个 Spring 类型的 name 成员变量，用来接收配置文件中的参数“

   ```java
   @Data 
   public class NameRoutePredicateConfig implements Serializable { 
   
   	private static final long serialVersionUID = -3289515863427972825L; 
   	
   	private String name;
   	
   }
   ```

2. 实现自定义断言时，需要新建类继承 AbstractRoutePredicateFactory 类，新建 NameRoutePredicateFactory 类，继承 AbstractRoutePredicateFactory  类，并覆写相关的方法

   ```java
   @Component 
   public class NameRoutePredicateFactory extends AbstractRoutePredicateFactory<NameRoutePredicateConfig> {
   
   	public NameRoutePredicateFactory() { 
   		super(NameRoutePredicateConfig.class);
   	}
   
   	@Override 
   	public Predicate<ServerWebExchange> apply(NameRoutePredicateConfig config) { 
   		return (serverWebExchange)->{ 
   			String name = serverWebExchange.getRequest().getQueryParams().getFirst("name"); 
   			if (StringUtils.isEmpty(name)){ 
   				name = "";
   			} 
   			return name.equals(config.getName());
   		}; 
   	}
   
   	@Override 
   	public List<String> shortcutFieldOrder() { 
   		return Arrays.asList("name");
   	} 
   }
   ```

3. 在服务网关的 application.yml 文件中的 spring.cloud.gateway.routes 节点下的 - id: usergateway 下面进行如下配置

   ```yaml
   spring: 
     cloud: 
       gateway: 
         routes: 
           - id: user-gateway 
             uri: http://localhost:8060 
             order: 1 
             predicates: 
               - Path=/server-user/** 
               - Name=Simon
             filters: 
               - StripPrefix=1
   ```

   这样通过服务网关访问用户微服务时，只有在访问的链接后面添加 ?name=Simon 参数时才能正确访问用户微服务



------

### 网关过滤器



过滤器可以在请求过程中，修改请求的参数和响应的结果等信息。在生命周期的角度总体上可以分为前 置过滤器（Pre）和后置过滤器（Post）。在实现的过滤范围角度可以分为局部过滤器（GatewayFilter）和 全局过滤器（GlobalFilter）。局部过滤器作用的范围是某一个路由，全局过滤器作用的范围是全部路由

* Pre 前置过滤器：在请求被网关路由之前调用，可以利用这种过滤器实现认证、鉴权、路由等功能，也可以记录访问时间等信息
* Post 后置过滤器：在请求被网关路由到微服务之后执行。可以利用这种过滤器修改 HTTP 的响应 Header 信息，修改返回的结果数据（例如对于一些敏感的数据，可以在此过滤器中统一处理后返回），收集一些统计信息等
* 局部过滤器（GatewayFilter）：也可以称为网关过滤器，这种过滤器主要是作用于单一路由或者某个路由分组
* 全局过滤器（GlobalFilter）：这种过滤器主要作用于所有的路由



------

## 消息队列

### 常见的 MQ



| 消息中间件(MQ) | 优点                                                         | 缺点                                                         | 使用场景                                    |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------- |
| RabbitMQ       | 功能全面、消息的可靠性比较高                                 | 吞吐量低，消息大量积累会 影响性能，使用的开发语言是 erlang，不好定制功能 | 规模不大的场景                              |
| Kafka          | 吞吐量最高，性能最好，集群模式下高可用                       | 功能上比较单一，会丢失部分数据                               | 日志分析，大数 据场景                       |
| RocketMQ       | 吞吐量高，性能高，可用性高，功能全面。使用 Java 语言开发，容易定制功能 | 开源版不如阿里云上版，文档比较简单                           | 几乎支持所有场景，包含大数据 场景和业务场景 |



------

### 项目整合 RocketMQ



1. 引入依赖

   ```xml
   <dependency> 
   	<groupId>org.apache.rocketmq</groupId> 
   	<artifactId>rocketmq-spring-boot-starter</artifactId> 
   	<version>2.0.3</version>
   </dependency>
   <dependency> 
   	<groupId>org.apache.rocketmq</groupId> 
   	<artifactId>rocketmq-client</artifactId> 
   	<version>4.5.2</version>
   </dependency>
   ```

2. 添加配置

   ```yml
   rocketmq: 
     name-server: 127.0.0.1:9876 
     producer: 
       group: order-group
   ```

3. 发送消息

   ```java
   @Autowired 
   private RocketMQTemplate rocketMQTemplate;
   
   public void saveOrder(OrderParams orderParams) { 
   	rocketMQTemplate.convertAndSend("order-topic", order);
   }
   ```

4. 消费消息

   ```java
   @Slf4j 
   @Component 
   @RocketMQMessageListener(consumerGroup = "user-group", topic = "order-topic") 
   public class RocketConsumeListener implements RocketMQListener<Order> { 
   
   	@Override 
   	public void onMessage(Order order) { 
   		log.info("用户微服务收到了订单信息：{}", JSONObject.toJSONString(order));
   	} 
   }
   ```



------

### RocketMQ 基本概念



* 消息模型

  RocketMQ 主要由 Producer、Broker、Consumer 三部分组成，其中 Producer 负责生产消息， Consumer 负责消费消息，Broker 负责存储消息。Broker 在实际部署过程中对应一台服务器，每个 Broker 可以存储多个 Topic 的消息，每个 Topic 的消息也可以分片存储于不同的 Broker。Message Queue 用于存储消息的物理地址，每个 Topic 中的消息地址存储于多个 Message Queue 中。 ConsumerGroup 由多个 Consumer 实例构成

* 消息生产者

  负责生产消息，一般由业务系统负责生产消息。一个消息生产者会把业务应用系统里产生的消息发送到 broker 服务器。RocketMQ 提供多种发送方式，同步发送、异步发送、顺序发送、单向发送。同步和异步方式均需要 Broker 返回确认信息，单向发送不需要

* 消息消费者

  负责消费消息，一般是后台系统负责异步消费。一个消息消费者会从 Broker 服务器拉取消息、并将其提供给应用程序。从用户应用的角度而言提供了两种消费形式：拉取式消费、推动式消费

* 主题

  表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是 RocketMQ 进行消息订阅的基本单位

* 代理服务器

  消息中转角色，负责存储消息、转发消息。代理服务器在 RocketMQ 系统中负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备。代理服务器也存储消息相关的元数据，包括消费者组、 消费进度偏移和主题和队列消息等

* Namesrv 

  名称服务充当路由消息的提供者。生产者或消费者能够通过名字服务查找各主题相应的 Broker IP 列表。 多个 Namesrv 实例组成集群，但相互独立，没有信息交换

* 拉取式消费

  Consumer 消费的一种类型，应用通常主动调用 Consumer 的拉消息方法从 Broker 服务器拉消息、主动权由应用控制。一旦获取了批量消息，应用就会启动消费过程

* 推动式消费

  Consumer 消费的一种类型，该模式下 Broker 收到数据后会主动推送给消费端，该消费模式一般实时性较高

* 生产者组

  同一类 Producer 的集合，这类 Producer 发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，则 Broker 服务器会联系同一生产者组的其他生产者实例以提交或回溯消费

* 消费者组

  同一类 Consumer 的集合，这类 Consumer 通常消费同一类消息且消费逻辑一致。消费者组使得在消息消费方面，实现负载均衡和容错的目标变得非常容易。要注意的是，消费者组的消费者实例必须订阅完 全相同的 Topic。RocketMQ 支持两种消息模式：集群消费（Clustering）和广播消费 （Broadcasting）

* 集群消费

  集群消费模式下,相同 Consumer Group 的每个 Consumer 实例平均分摊消息

* 广播消费

  广播消费模式下，相同 Consumer Group 的每个 Consumer 实例都接收全量的消息

* 普通顺序消费

  普通顺序消费模式下，消费者通过同一个消息队列（ Topic 分区，称作 Message Queue） 收到的消息是有顺序的，不同消息队列收到的消息则可能是无顺序的

* 严格顺序消费

  严格顺序消息模式下，消费者收到的所有消息均是有顺序的

* 消息

  消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。 RocketMQ 中每个消息拥有唯一的 Message ID，且可以携带具有业务标识的 Key。系统提供了通过 Message ID 和 Key 查询消息的功能

* 标签

  为消息设置的标志，用于同一主题下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一主题下设置不同标签。标签能够有效地保持代码的清晰度和连贯性，并优化 RocketMQ 提供的查询系统。消费者可以根据 Tag 实现对不同子主题的不同消费逻辑，实现更好的扩展性



------

### RocketMQ 特性



* 订阅与发布

  消息的发布是指某个生产者向某个 topic 发送消息；消息的订阅是指某个消费者关注了某个 topic 中带有某些 tag 的消息，进而从该 topic 消费数据

* 消息顺序

  消息有序指的是一类消息消费时，能按照发送的顺序来消费。例如：一个订单产生了三条消息分别是订单创建、订单付款、订单完成。消费时要按照这个顺序消费才能有意义，但是同时订单之间是可以并行消费的。RocketMQ 可以严格的保证消息有序

  顺序消息分为全局顺序消息与分区顺序消息，全局顺序是指某个 Topic 下的所有消息都要保证顺序；部分顺序消息只要保证每一组消息被顺序消费即可

  * 全局顺序对于指定的一个 Topic，所有消息按照严格的先入先出（FIFO）的顺序进行发布和消费。 适用场景：性能要求不高，所有的消息严格按照 FIFO 原则进行消息发布和消费的场景
  * 分区顺序对于指定的一个 Topic，所有消息根据 sharding key 进行区块分区。 同一个分区内的消息按照严格的 FIFO 顺序进行发布和消费。 Sharding key 是顺序消息中用来区分不同分区的关键字段，和普通消息的 Key 是完全不同的概念。 适用场景：性能要求高，以 sharding key 作为分区字段，在同一个区块中严格的按照 FIFO 原则进行消息发布和消费的场景

* 消息过滤

  RocketMQ 的消费者可以根据 Tag 进行消息过滤，也支持自定义属性过滤。消息过滤目前是在 Broker 端实现的，优点是减少了对于 Consumer 无用消息的网络传输，缺点是增加了 Broker 的负担、而且实现相对复杂

* 消息可靠性

  RocketMQ 支持消息的高可靠，影响消息可靠性的几种情况：

  1. Broker 非正常关闭
  2. Broker 异常 Crash
  3. OS Crash
  4. 机器掉电，但是能立即恢复供电情况
  5. 机器无法开机（可能是 cpu、主板、内存等关键设备损坏）
  6. 磁盘设备损坏

  1、2、3、4 四种情况都属于硬件资源可立即恢复情况，RocketMQ 在这四种情况下能保证消息不丢， 或者丢失少量数据（依赖刷盘方式是同步还是异步）

  5、6 属于单点故障，且无法恢复，一旦发生，在此单点上的消息全部丢失。RocketMQ 在这两种情况 下，通过异步复制，可保证 99% 的消息不丢，但是仍然会有极少量的消息可能丢失。通过同步双写技术可以完全避免单点，同步双写势必会影响性能，适合对消息可靠性要求极高的场合

* 至少一次

  至少一次(At least Once)指每个消息必须投递一次。Consumer 先 Pull 消息到本地，消费完成后，才向服务器返回 ack，如果没有消费一定不会 ack 消息，所以 RocketMQ 可以很好的支持此特性

* 回溯消息

  回溯消费是指 Consumer 已经消费成功的消息，由于业务上需求需要重新消费，要支持此功能，Broker 在向 Consumer 投递成功消息后，消息仍然需要保留。并且重新消费一般是按照时间维度，例如由于 Consumer 系统故障，恢复后需要重新消费 1 小时前的数据，那么 Broker 要提供一种机制，可以按照时间 维度来回退消费进度。RocketMQ 支持按照时间回溯消费，时间维度精确到毫秒

* 事务消息

  RocketMQ 事务消息（Transactional Message）是指应用本地事务和发送消息操作可以被定义到全局事务中，要么同时成功，要么同时失败。RocketMQ 的事务消息提供类似 X/Open XA 的分布事务功能，通过事务消息能达到分布式事务的最终一致

* 定时消息

  定时消息（延迟队列）是指消息发送到 broker 后，不会立即被消费，等待特定时间投递给真正的 topic。 broker 有配置项 messageDelayLevel，默认值为“1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h”，18 个level。可以配置自定义 messageDelayLevel。注意，messageDelayLevel 是broker的属性，不属于某个 topic。发消息时，设置 delayLevel 等级即可： msg.setDelayLevel(level)。level有以下三种情况：

  * level == 0，消息为非延迟消息
  * 1<=level<=maxLevel，消息延迟特定时间，例如 level==1，延迟 1s
  * level > maxLevel，则 level== maxLevel，例如 level==20，延迟2h

  定时消息会暂存在名为 SCHEDULE_TOPIC_XXXX 的 topic 中，并根据 delayTimeLevel 存入特定的 queue， queueId = delayTimeLevel – 1，即一个 queue 只存相同延迟的消息，保证具有相同发送延迟的消息能够顺序消费。broker 会调度地消费 SCHEDULE_TOPIC_XXXX，将消息写入真实的 topic

  需要注意的是，定时消息会在第一次写入和调度写入真实 topic 时都会计数，因此发送数量、tps 都会变高

* 消息重试

  Consumer 消费消息失败后，要提供一种重试机制，令消息再消费一次。Consumer 消费消息失败通常可以认为有以下几种情况：

  * 由于消息本身的原因，例如反序列化失败，消息数据本身无法处理等。这种错误通常需要跳过这条消息，再消费其它消息，而这条失败的消息即使立刻重试消费，99% 也不成功，所以最好提供一种定时重试机制，即过 10 秒后再重试
  * 由于依赖的下游应用服务不可用，例如 db 连接不可用，外系统网络不可达等。遇到这种错误，即使跳过当前失败的消息，消费其他消息同样也会报错。这种情况建议应用 sleep 30s，再消费下一条消息，这样可以减轻 Broker 重试消息的压力

  RocketMQ 会为每个消费组都设置一个 Topic 名称为 “%RETRY%+consumerGroup” 的重试队列（这里需要注意的是，这个 Topic 的重试队列是针对消费组，而不是针对每个 Topic 设置的），用于暂时保存因为各种异常而导致 Consumer 端无法消费的消息

  考虑到异常恢复起来需要一些时间，会为重试队列设置多个重试级别，每个重试级别都有与之对应的重新投递延时，重试次数越多投递延时就越大。RocketMQ 对于重试消息的处理是先保存至 Topic 名称为 “SCHEDULE_TOPIC_XXXX” 的延迟队列中，后台定时任务按照对应的时间进行 Delay 后重新保存至 “%RETRY%+consumerGroup” 的重试队列中

* 消息重投

  生产者在发送消息时，同步消息失败会重投，异步消息有重试，oneway 没有任何保证。消息重投保证消息尽可能发送成功、不丢失，但可能会造成消息重复，消息重复在 RocketMQ 中是无法避免的问题。消息重复在一般情况下不会发生，当出现消息量大、网络抖动，消息重复就会是大概率事件。另外，生产 者主动重发、consumer 负载变化也会导致重复消息。如下方法可以设置消息重试策略：

  * retryTimesWhenSendFailed：同步发送失败重投次数，默认为 2，因此生产者会最多尝试发送 retryTimesWhenSendFailed + 1 次。不会选择上次失败的 broker，尝试向其他 broker 发送，最大程度保证消息不丢。超过重投次数，抛出异常，由客户端保证消息不丢。当出现 RemotingException、MQClientException 和部分 MQBrokerException 时会重投
  * retryTimesWhenSendAsyncFailed：异步发送失败重试次数，异步重试不会选择其他 broker，仅在同一个 broker上做重试，不保证消息不丢
  * retryAnotherBrokerWhenNotStoreOK：消息刷盘（主或备）超时或 slave 不可用（返回状态非 SEND_OK），是否尝试发送到其他 broker，默认 false。十分重要消息可以开启

* 流量控制

  生产者流控，因为 broker 处理能力达到瓶颈；消费者流控，因为消费能力达到瓶颈

* 死信队列

  死信队列用于处理无法被正常消费的消息。当一条消息初次消费失败，消息队列会自动进行消息重试； 达到最大重试次数后，若消费依然失败，则表明消费者在正常情况下无法正确地消费该消息，此时，消息队列 不会立刻将消息丢弃，而是将其发送到该消费者对应的特殊队列中

  RocketMQ 将这种正常情况下无法被消费的消息称为死信消息（Dead-Letter Message），将存储死信 消息的特殊队列称为死信队列（Dead-Letter Queue）。在 RocketMQ 中，可以通过使用 console 控制台对死信队列中的消息进行重发来使得消费者实例再次进行消费



------

### RocketMQ 最佳实践



* 生产者

  * tag 的使用

    一个应用尽可能用一个 Topic，而消息子类型则可以用 tags 来标识。tags 可以由应用自由设置，只有生产者在发送消息设置了 tags，消费方在订阅消息时才可以利用 tags 通过 broker 做消息过滤： message.setTags("TagA")

  * keys 的使用

    每个消息在业务层面的唯一标识码要设置到 keys 字段，方便将来定位消息丢失问题。服务器会为每个消息创建索引（哈希索引），应用可以通过 topic、key 来查询这条消息内容，以及消息被谁消费。由于是哈希索引，请务必保证 key 尽可能唯一，这样可以避免潜在的哈希冲突

  * 日志的打印

    消息发送成功或者失败要打印消息日志，务必要打印 SendResult 和 key 字段。send 消息方法只要不抛异常，就代表发送成功。发送成功会有多个状态，在 sendResult 里定义

  * 消息发送失败的处理方式

    Producer 的 send 方法本身支持内部重试，重试逻辑如下：

    * 至多重试 2 次
    * 如果同步模式发送失败，则轮转到下一个 Broker，如果异步模式发送失败，则只会在当前 Broker 进行重试。这个方法的总耗时时间不超过 sendMsgTimeout 设置的值，默认 10s
    * 如果本身向 broker 发送消息产生超时异常，就不会再重试

    以上策略也是在一定程度上保证了消息可以发送成功。如果业务对消息可靠性要求比较高，建议应用增加相应的重试逻辑：比如调用 send 同步方法发送失败时，则尝试将消息存储到 db，然后由后台线程定时重试，确保消息一定到达 Broker

  * 选择 oneway 形式发送

    对可靠性要求不高，可以采用 oneway 形式调用，oneway 形式只发送请求不等待应答，而发送请求在客户端实现层面仅仅是一个操作系统系统调用的开销，即将数据写入客户端的 socket 缓冲区，此过程耗时通常在微秒级

* 消费者

  * 消费幂等

    RocketMQ 无法避免消息重复（Exactly-Once），所以如果业务对消费重复非常敏感，务必要在业务层面进行去重处理

  * 消费打印日志

    如果消息量较少，建议在消费入口方法打印消息，消费耗时等，方便后续排查问题



------

## 配置中心

### 常见的配置中心



| 配置中心           | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| Consul             | 谷歌基于 Go 语言开发出的一款支持服务动态发现的配置管理中心服务。在 Consul 中，内置了服务注册与发现功能，实现了分布式一致性协议，支持 Key-Value 存储，支持多数据中心 |
| Apollo             | 协程开源的一款支持分布式的配置中心。支持修改后的配置动态实时生效，对项目的配置进行版本化管理，对配置的修改支持审计，支持项目的灰度发布 |
| Disconf            | 百度开源的一款支持分布式的配置中心，主要是利用 Zookeeper 实现配置信息变 更后，实时通知各个微服务，使变更后的配置信息生效 |
| SpringCloud Config | SpringCloud 技术栈中自带的配置中心组件，支持使用 Git 仓库存储配置信息，不 支持配置信息变更后实时生效 |
| Nacos              | SpringCloud Alibaba 技术栈中的一个在微服务环境下，支持分布式服务注册与发现，支持服务元数据及流量管理，支持分布式配置中心的组件。使用 Nacos 可以轻松实现服务的注册与发现，以及分布式配置中心功能 |



------

### 项目集成 Nacos 配置中心



1. 引入依赖

   ```
   <dependency> 
   	<groupId>com.alibaba.cloud</groupId> 
   	<artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
   </dependency>
   ```

2. 创建配置文件

   将微服务的配置放在 Nacos 中时，就暂时不用微服务中的 application.yml 配置文件了，而是在项目中新建一个 bootstrap.yml 文件。因为配置文件的优先级从高到低依次为：bootstrap.properties -> bootstrap.yml -> application.properties -> application.yml

   ```yml
   spring: 
     application: 
       name: server-user
     cloud: 
       nacos: 
         config: 
           server-addr: 127.0.0.1:8848 
           file-extension: yaml 
           group: user_group
     profiles: 
       active: dev
   ```

3. 启动 Nacos，在浏览器中输入 http://localhost:8848/nacos 并登录 Nacos，选择 Nacos 菜单中 的配置管理-配置列表

   点击配置列表界面中的 +，点开后的界面如下所示

   ![image-20231126161437173](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20231126161437173.png)

   Data ID 就是配置集 ID，通常情况下就是某个配置文件的 ID，这个 ID 的命名遵循一定的规则

   ```
   ${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config .file-extension}
   ```

   对应到用户微服务的 Data ID 就是：server-user-dev.yaml

4. 添加配置内容并保存



------

### 获取配置内容



通过代码的方式

```
@Autowired 
private ConfigurableApplicationContext context;

String authorName = context.getEnvironment().getProperty("author.name");
```



通过注解的方式

```
@Value("${author.name}") 
private String nacosAuthorName;
```

