---
title: 远程调用之 OpenFeign
date: 2023-02-19 18:27:51
tags: JAVA
categories: 
  - JAVA
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/%E8%BF%9C%E7%A8%8B%E8%B0%83%E7%94%A8%E4%B9%8B%20OpenFeign/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/%E8%BF%9C%E7%A8%8B%E8%B0%83%E7%94%A8%E4%B9%8B%20OpenFeign/preview.jpg

---



## 远程调用之 OpenFeign

### 简介

#### 什么是 OpenFeign



OpenFeign 是 Spring Cloud 的二级子项目，不同于 Feign，Feign 是 Netflix 公司下的，而 OpenFeign 是 Spring Cloud 下的

OpenFeign 是一种声明式、模板化的 HTTP 客户端。声明式调用是指像调用本地方法一样调用远程方法，无需感知操作远程 HTTP 请求



------

### 使用案例

#### 快速开始



1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   ```

2. 在启动类上添加注解

   ```java
   @EnableFeignClients
   @SpringBootApplication
   public class SpringCloudDemoApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(SpringCloudDemoApplication.class, args);
       }
   
   }
   ```

3. 编写调用接口代码

   ```java
   /**
    * FeignClient 注解中的参数有 value 和 url
    * 如果有用到注册中心，就可以使用 value 参数，内容就写注册中心中的 application name
    * 如果直接调用远程 url，就使用 url 参数
    */
   @FeignClient(url = "localhost:8082")
   public interface UserService {
   
       @GetMapping("/user/{id}")
       UserVO queryUserById(@PathVariable("id") String id);
   
   }
   ```

4. 最后，就可以像调用本地接口一样调用 Feign 接口了

   ```java
   @RestController
   public class UserController {
       
       @Resource
       private UserService userService;
       
       @GetMapping("/user/{id}")
       public UserVO queryUserById(@PathVariable String id) {
           return  userService.queryUserById(id);
       }
       
   }
   ```



------

### 自定义配置

#### 日志



可以在配置文件中设置 feign 输出的日志级别

```yml
feign:
  client:
    config:
      # 微服务的名称
      demo-service:
        loggerLevel: FULL
```

日志一共有 4 个级别：

* NONE【性能最佳，适用于生产】：不记录任何日志（默认值）
* BASIC【适用于生产环境追踪问题】：仅记录请求方法、URL、响应状态代码以及执行时间
* HEADERS：记录BASIC级别的基础上，记录请求和响应的header
* FULL【比较适用于开发及测试环境定位问题】：记录请求和响应的header、body 和元数据



------

#### 契约配置



Spring Cloud 在 Feign 的基础上做了扩展，使用 Spring MVC 的注解来完成 Feign 的功能。原生的 Feign 是不支持 Spring MVC 注解的，如果你想在 Spring Cloud 中使用原生的注解方式来定义客户端也是可以的，通过配置契约来改变这个配置，Spring Cloud 中默认的 是 SpringMvcContract

```yml
feign:
  client:
    config:
      # 微服务的名称
      demo-service:
        contract: feign.Contract.Default
```

这样就可以使用 Feign 的原生注解了，比如 @RequestLine 等



------

#### 自定义拦截器



首先，编写拦截器代码

```java
public class MyRequestInterceptor implements RequestInterceptor {
    @Override
    public void apply(RequestTemplate template) {
        // 业务逻辑
    }
}
```



然后，在配置文件中配置自定义拦截器

```yml
feign:
  client:
    config:
      # 微服务的名称
      demo-service:
        requestInterceptors[0]: com.spring.cloud.demo.config.MyRequestInterceptor
```



------

#### 配置超时时间



```yml
feign:
  client:
    config:
      # 微服务的名称
      demo-service:
        # 连接超时时间，默认2s
        connectTimeout: 2000
        # 请求超时时间，默认5s
        readTimeout: 5000
```



------

#### 客户端组件配置



Feign 中默认使用 JDK 原生的 URLConnection 发送 HTTP 请求，我们可以集成别的组件来 替换掉 URLConnection，比如 Apache HttpClient，OkHttp



第一步，引入依赖

```xml
<dependency>
  <groupId>io.github.openfeign</groupId>
  <artifactId>feign-httpclient</artifactId>
</dependency>
```

然后，添加配置即可

```yml
feign:
  httpclient:
    enabled: true
```



------

#### GZIP 压缩配置



开启压缩可以有效节约网络资源，提升接口性能，我们可以配置 GZIP 来压缩数据

```yml
feign:
  compression:
    request:
      # 是否压缩请求内容
      enabled: true
      # 需要进行压缩的数据格式
      mime-types: text/xml,application/xml,application/json
      # 最小压缩值
      min-request-size: 2048
    response:
      # 是否压缩响应
      enabled: true
```

