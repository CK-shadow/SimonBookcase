---
title: 流量控制组件之 Sentinel
date: 2022-12-03 13:31:52
tags: 后端架构
categories: 
  - 后端架构
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%90%8E%E7%AB%AF%E6%9E%B6%E6%9E%84/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6%E7%BB%84%E4%BB%B6%E4%B9%8BSentinel/pexels-ciboulette-574024.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%90%8E%E7%AB%AF%E6%9E%B6%E6%9E%84/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6%E7%BB%84%E4%BB%B6%E4%B9%8BSentinel/pexels-ciboulette-574024.jpg

---

## 流量控制组件之 Sentinel

### 分布式系统遇到的问题



随着服务的不断增多，服务之间的调用链路也变得更加的复杂。这时候，如果服务链路中的某一个节点出现问题，就会造成整条服务链路的不可用，进而影响其它的服务，比如流量激增造成的服务雪崩



------



### 解决方案



* 超时机制

  在不做任何处理的情况下，服务提供者不可用会导致消费者请求线程强制等待，而造成系统资源耗尽。加入超时机制， 一旦超时，就释放资源。由于释放资源速度较快，一定程度上可以抑制资源耗尽的问题

* 限流

  避免流量激增造成服务负载过大

* 隔离

  用户的请求将不再直接访问服务，而是通过线程池中的空闲线程来访问服务，如果线程池已满，则会进行降级处理，用户的请求不会被阻塞，至少可以看到一个执行结果（例如返回友好的提示信息），而不是无休止的等待或者看到系统崩溃

  信号隔离也可以用于限制并发访问，防止阻塞扩散, 与线程隔离最大不同在于执行依赖代码的线程依然是请求线程（该线程需要通过信号申请, 如果客户端是可信的且可以快速返回，可以使用信号隔离替换线程隔离,降低开销。信号量的大小可以动态调整, 线程池大小不可以

* 服务熔断

  远程服务不稳定或网络抖动时暂时关闭，就叫服务熔断

* 服务降级

  所谓降级，就是当某个服务熔断之后，服务将不再被调用，此时客户端可以自己准备一个本地的fallback（回退）回调， 返回一个缺省值



------

### 快速开始

#### 与 Hystrix 对比



|                |                    Sentinel                    |            Hystrix            |
| :------------: | :--------------------------------------------: | :---------------------------: |
|    隔离策略    |                   信号量隔离                   |          线程池隔离           |
|  熔断降级策略  |             基于响应时间或失败比率             |         基于失败比率          |
|  实时指标实现  |                    滑动窗口                    |    滑动窗口（基于RxJava）     |
|    规则配置    |                 支持多种数据源                 |        支持多种数据源         |
|     扩展性     |                   多个扩展点                   |          插件的形式           |
| 基于注解的支持 |                      支持                      |             支持              |
|      限流      |        基于 QPS，支持基于调用关系的限流        |          有限的支持           |
|    流量整形    |             支持慢启动，匀速器模式             |            不支持             |
|  系统负责保护  |                      支持                      |            不支持             |
|     控制台     | 开箱即用，可配置规则，查看秒级监控，机器发现等 |            不完善             |
|  常见框架适配  |     Servlet、Spring Cloud、Dubbo、gRPC 等      | Servlet、Spring Cloud Netflix |



------

#### 安装 Sentinel Dashboard



Sentinel 分为两部分，Dashboard 和 Client，其中 Client 即 Service，而 Dashboard 是需要独立部署的

Sentinel Dashboard 有多种安装方式，可以下载 jar 包使用 java -jar 进行启动，也可以使用 docker 部署，下面以 docker 安装部署为例



1. 搜索 Sentinel Dashboard 镜像

   ```
   docker search sentinel-dashboard
   ```

2. 下载镜像

   ```
   docker pull bladex/sentinel-dashboard 
   ```

3. 运行 Sentinel Dashboard

   ```
   docker run --name sentinel -d -p 8858:8858 bladex/sentinel-dashboard 
   ```

4. 直接浏览器访问，地址：http://localhost:8858

   用户名及密码都是 sentinel



------

#### 接入 Sentinel Client



与大多数框架类似，Sentinel 既支持 JDK 配置，也支持注解配置。但这两种方式都需要侵入代码，在代码中完成规则配置。最简单的方式则是使用 sentinel-starter，然后在 dashboard 页面上完成规则配置



1. 引入 Maven 依赖

   ```xml
   <dependency>
   	<groupId>com.alibaba.cloud</groupId>
   	<artifactId>spring‐cloud‐starter‐alibaba‐sentinel</artifactId>
   </dependency>
   ```

2. 添加配置

   ```yml
   spring:
     cloud:
       sentinel:
         transport:
           # sentinel dashboard 的地址
           dashbord: localhsot:8858	
   ```



接下来即可在 dashboard 完成流控配置了



------

### Dashboard 功能

#### 流控规则



![image-20221203210334586](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%90%8E%E7%AB%AF%E6%9E%B6%E6%9E%84/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6%E7%BB%84%E4%BB%B6%E4%B9%8BSentinel/image-20221203210334586.png)



* 资源名

  限流规则的作用对象

* 针对来源

  调用来源，default 表示所有应用

* 线程数

  为应对太多线程占用的情况，业内有使用隔离的方案，比如通过不同业务逻辑使用不同线程池来隔离业务自身之间的资源争抢（线程池隔离）。这种隔离方案虽然隔离性比较好，但是代价就是线程数目太多，线程上下文切换的 overhead 比较大，特别是 对低延时的调用有比较大的影响。Sentinel 并发控制不负责创建和管理线程池，而是简单统计当前请求上下文的线程数目 （正在执行的调用数目），如果超出阈值，新的请求会被立即拒绝，效果类似于信号量隔离。并发数控制通常在调用端进行配置

* 直接流控模式

  资源调用达到设置的阈值后直接被流控抛出异常

* 关联流控模式

  当两个具有资源争抢或者依赖关系的资源关联起来，避免其中一个抢到了大量的资源而致使另一个操作受限

* 链路流控规则

  根据调用链路入口限流

* 流控效果之快速失败

  默认的流量控制方式，当 QPS 超过任意规则的阈值后，新的请求就会被立即拒绝，拒绝方式为抛出 FlowException

* 流控效果之 Warm Up

  让通过的流量缓慢增加，在一定时间内逐渐 增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮

* 流控效果之排队等待

  严格控制请求通过的间隔时间，也即是让请 求以均匀的速度通过，对应的是漏桶算法



------

#### 降级规则



除了流量控制以外，对调用链路中不稳定的资源进行熔断降级也是保障高可用的重要措施之一。我们需要对不稳定的弱依赖服务调用进行熔断降级，暂时切断不稳定调用，避免局部不稳定因素导致整体的雪崩。熔断降级作为保护自身 的手段，通常在客户端（调用端）进行配置



* 慢调用比例

  选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间）， 请求的响应时间大于该值则统计为慢调用。当单位统计时长（statIntervalMs）内请求数目大于设置的最小请求数目，并 且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态 （HALF ­OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断

* 异常比例

  单位统计时长（statIntervalMs）内请求数目大于设置的最小请求数目，并且异常的比例 大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF­OPEN 状 态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 [0.0, 1.0]， 代表 0% ­ 100%

* 异常数

  当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF­OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。 注意：异常降级仅针对业务异常，对 Sentinel 限流降级本身的异常（BlockException）不生效



------

#### 热点参数限流



热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效

使用热点参数限流需要代码及控制台共同共用，代码中需要时间 @SentinelResource 注解来标明热点资源



------

#### 系统规则



Sentinel 系统自适应限流从整体维度对应用入口流量进行控制，结合应用的 Load、CPU 使用率、总体平均 RT、入口 QPS 和并发线程数等几个维度的监控指标，通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让 系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性



* Load

  仅对 Linux/Unix­like 机器生效，系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 maxQps * minRt 估算得出。设定参考值一般是 CPU cores * 2.5

* RT

  当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒

* 线程数

  当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护

* 入口 QPS

  当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护

* CPU 使用率

  当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0­1.0），比较灵敏



------

#### 授权控制规则



很多时候，我们需要根据调用来源来判断该次请求是否允许放行，这时候可以使用 Sentinel 的来源访问控制（黑白名单控制）的功能。来源访问控制根据资源的请求来源（origin）限制资源是否通过，若配置白名单则只有请求来源位于白名单内时才可通过；若配置黑名单则请求来源位于黑名单时不通过，其余的请求通过




------

#### 自定义响应内容



在流量超限时，Sentinel 会返回以下内容

```
HTTP Status ：429
Response Body：Blocked by Sentinel（flow limiting）
```



我们也可用通过实现 BlockExceptionHandler 来自定义自己的返回内容

```java
@Component
public class MyBlockExceptionHandler implements BlockExceptionHandler {

    @Override
    public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, BlockException e) throws Exception {
        JSONObject response = new JSONObject();
        String message = "";
        if (e instanceof FlowException) {
            message = "接口限流了";
        } else if (e instanceof DegradeException) {
            message = "服务降级了";
        } else if (e instanceof ParamFlowException) {
            message = "热点参数限流了";
        } else if (e instanceof SystemBlockException) {
            message = "出发系统保护规则了";
        } else if (e instanceof AuthorityException) {
            message = "授权规则不通过";
        }
        response.put("message", message);

        httpServletResponse.setStatus(500);
        httpServletResponse.setCharacterEncoding("UTF-8");
        httpServletResponse.setContentType(MediaType.APPLICATION_JSON_VALUE);
        new ObjectMapper().writeValue(httpServletResponse.getWriter(), response);
    }
}
```

这时候再试一下，返回的内容

```
HTTP Status ：500
Response Body：{"message":"接口限流了"}
```



------

### 持久化模式



Sentinel规则的推送有下面三种模式：原始模式、Pull 模式和 Push 模式

如果不做任何修改，Dashboard 的推送规则方式是通过 API 将规则推送至客户端并直接更新到内存中。这种做法的好处是简单，无依赖；坏处是应用重启规则就会消失，仅用于简单测试，不能用于生产环境

pull 模式的数据源（如本地文件、RDBMS 等）一般是可写入的。使用时需要在客户端注册数据源：将对应的读数据源注册至对应的 RuleManager，将写数据源注册至 transport 的 WritableDataSourceRegistry 中

生产环境下一般更常用的是 push 模式的数据源。对于 push 模式的数据源,如远程配置中心 （ZooKeeper, Nacos, Apollo等等），推送的操作不应由 Sentinel 客户端进行，而应该经控制台统一进行管理，直接进行推送，数据源仅负责获取配置中心推送的配置并更新到本地
