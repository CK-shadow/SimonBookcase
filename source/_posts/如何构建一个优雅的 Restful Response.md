---
title: 如何构建一个优雅的 Restful Response
date: 2022-09-18 14:37:32
tags: Java
categories: 
  - JAVA
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA%E4%BC%98%E9%9B%85%E7%9A%84%20Restful%20Response/F100009910.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/%E5%A6%82%E4%BD%95%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AA%E4%BC%98%E9%9B%85%E7%9A%84%20Restful%20Response/F100009910.jpg
---



## 如何构建一个优雅的 Restful Response

### JSON Response



现在的代码开发中，大都是使用 JSON 来返回响应数据，要实现这个，只需要添加 @RestController 即可

```java
@RestController
@RequestMapping("/product")
public class ProductInfoController {

    @Autowired
    ProductInfoService productInfoService;

    @GetMapping("/findById")
    public ProductInfoVo findById(Integer id) {
        ...
    }

}
```



------

### 统一状态码

#### 返回格式



在添加了 @RestController 注解之后，我们返回的响应数据是这样的

```java
{
  "productId": 1,
  "productName": "泡脚",
  "productPrice": 100.00,
  "productDescription": "中药泡脚加按摩",
  "productStatus": 0,
}
```



这对前端来说并不友好，因为这个 Response 并不能直观的反应此次请求是成功还是失败，所以通过情况下返回一个状态码，通过状态码来判断此次请求的结果，如下所示，假设我们约定 200 为成功

```java
{
  "code": 200,
  "msg": "请求成功",
  "data": {
    "productId": 1,
    "productName": "泡脚",
    "productPrice": 100.00,
    "productDescription": "中药泡脚加按摩",
    "productStatus": 0,
  }
}
```



------

#### 封装 ResultVO



状态码可以写常量，但是最好的方式，还是使用枚举类



首先，定义一个状态码接口，用于规定状态码标准

```java
public interface StatusCode {
    public int getCode();
    public String getMsg();
}
```



然后，定义状态码枚举类，因为状态码不允许随意更改，因此不提供 Set 方法

```java
@Getter
public enum ResultCode implements StatusCode{
    SUCCESS(200, "请求成功"),
    FAILED(1001, "请求失败"),
    VALIDATE_ERROR(1002, "参数校验失败"),
    RESPONSE_PACK_ERROR(1003, "response返回包装失败");

    private int code;
    private String msg;

    ResultCode(int code, String msg) {
        this.code = code;
        this.msg = msg;
    }
}
```



最后定义 ResultVO 包装类，并提供一些默认的构造方法

```java
@Data
public class ResultVo {
    
    // 状态码
    private int code;
    // 状态信息
    private String msg;
    // 返回对象
    private Object data;

    // 手动设置返回vo
    public ResultVo(int code, String msg, Object data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    // 默认返回成功状态码，数据对象
    public ResultVo(Object data) {
        this.code = ResultCode.SUCCESS.getCode();
        this.msg = ResultCode.SUCCESS.getMsg();
        this.data = data;
    }

    // 返回指定状态码，数据对象
    public ResultVo(StatusCode statusCode, Object data) {
        this.code = statusCode.getCode();
        this.msg = statusCode.getMsg();
        this.data = data;
    }

    // 只返回状态码
    public ResultVo(StatusCode statusCode) {
        this.code = statusCode.getCode();
        this.msg = statusCode.getMsg();
        this.data = null;
    }
}
```



这时候我们返回响应结果就不能 return data 了，而是 return new ResultVO(data)

```java
@PostMapping("/product/{id}")
public ResultVo findById(@PathVariable Long id) {
    ProductInfo productInfo =productInfoService.findById(id);
    return new ResultVo(prductInfo);
```



------

### 异常处理



在代码开发中，我们经常会使用 @Validated 注解来校验参数，减少代码

```java
@Data
public class ProductInfoVo {
    @NotNull(message = "商品名称不允许为空")
    private String productName;

    @Min(value = 0, message = "商品价格不允许为负数")
    private BigDecimal productPrice;

    private Integer productStatus;
}
```

```java
@PostMapping("/findByVo")
public ProductInfo findByVo(@Validated ProductInfoVo vo) {
    ProductInfo productInfo = new ProductInfo();
    BeanUtils.copyProperties(vo, productInfo);
    return new ResultVo(productInfoService.getOne(new QueryWrapper(productInfo)));
}
```



这时候，如果我们传入的 productPrice 是负数的话，会得到如下响应结果

```json
{
  "timestamp": "2020-04-19T03:06:37.268+0000",
  "status": 400,
  "error": "Bad Request",
  "errors": [
    {
      "codes": [
        "Min.productInfoVo.productPrice",
        "Min.productPrice",
        "Min.java.math.BigDecimal",
        "Min"
      ],
      "arguments": [
        {
          "codes": [
            "productInfoVo.productPrice",
            "productPrice"
          ],
          "defaultMessage": "productPrice",
          "code": "productPrice"
        },
        0
      ],
      "defaultMessage": "商品价格不允许为负数",
      "objectName": "productInfoVo",
      "field": "productPrice",
      "rejectedValue": -1,
      "bindingFailure": false,
      "code": "Min"
    }
  ],
  "message": "Validation failed for object\u003d\u0027productInfoVo\u0027. Error count: 1",
  "trace": "org.springframework.validation.BindException: org.springframework.validation.BeanPropertyBindingResult: 1 errors\nField error in object \u0027productInfoVo\u0027 on field \u0027productPrice\u0027: rejected value [-1]; codes [Min.productInfoVo.productPrice,Min.productPrice,Min.java.math.BigDecimal,Min]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [productInfoVo.productPrice,productPrice]; arguments []; default message [productPrice],0]; default message [商品价格不允许为负数]\n\tat org.springframework.web.method.annotation.ModelAttributeMethodProcessor.resolveArgument(ModelAttributeMethodProcessor.java:164)\n\tat org.springframework.web.method.support.HandlerMethodArgumentResolverComposite.resolveArgument(HandlerMethodArgumentResolverComposite.java:121)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.getMethodArgumentValues(InvocableHandlerMethod.java:167)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:134)\n\tat org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:105)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:879)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:793)\n\tat org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)\n\tat org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1040)\n\tat org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:943)\n\tat org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)\n\tat org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:660)\n\tat org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:741)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat com.alibaba.druid.support.http.WebStatFilter.doFilter(WebStatFilter.java:124)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)\n\tat org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)\n\tat org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:541)\n\tat org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139)\n\tat org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)\n\tat org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)\n\tat org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)\n\tat org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:373)\n\tat org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65)\n\tat org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:868)\n\tat org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1594)\n\tat org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)\n\tat org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)\n\tat java.base/java.lang.Thread.run(Thread.java:830)\n",
  "path": "/leilema/product/product-info/findByVo"
}
```



虽然成功拦截了请求并且返回了 商品价格不允许为负数 的信息，但是与我们之前定义的 ResultVO 数据结构并不相同，这时候就需要拦截并处理这个异常信息，返回约定好的数据格式

数据不符合规范的时候，@Validated 注解会抛出 BindException 异常，我们可以用 @RestControllerAdvice 来增强所有的 @RestController，然后使用 @ExceptionHandler 注解拦截 BindException 并对其进行处理

```java
@RestControllerAdvice
public class ControllerExceptionAdvice {

    @ExceptionHandler({BindException.class})
    public ResultVo MethodArgumentNotValidExceptionHandler(BindException e) {
        // 从异常对象中拿到ObjectError对象
        ObjectError objectError = e.getBindingResult().getAllErrors().get(0);
        return new ResultVo(ResultCode.VALIDATE_ERROR, objectError.getDefaultMessage());
    }
}
```



这时候再传入一个负数价格，返回的响应就是这个样子了

```json
{
  "code": 1002,
  "msg": "参数校验失败",
  "data": "商品价格不允许为负数"
}
```



------

### 统一响应
#### AOP 统一响应

这个时候我们在回头看一下 Controller 层的代码，如果我们想返回预期的 Restful 风格的 Response，代码就需要这么写

```java
return new ResultVo(productInfoService.getOne(new QueryWrapper(productInfo)));
```

每次都需要 new ResultVO 自然是不方便的，我们可以加一个 AOP 来对所有的 Controller 自动进行响应包装，也可以创建一个自定义 Controller 注解继承 RestController 注解，不同的是实现这个注解的接口会对其进行响应包装，两者的实现原理是相同的，都是使用 AOP 来减少代码

下面是第一种方法
```java
// 自动扫面 com.simon.rest 下面所有的类
@RestControllerAdvice(basePackages = {"com.simon.rest"})
public class ControllerResponseAdvice implements ResponseBodyAdvice<Object> {
    @Override
    public boolean supports(MethodParameter methodParameter, Class<? extends HttpMessageConverter<?>> aClass) {
        // response 是 ResultVO 类型则不进行包装 
        return !methodParameter.getParameterType().isAssignableFrom(ResultVo.class);
    }

    @Override
    public Object beforeBodyWrite(Object data, MethodParameter returnType, MediaType mediaType, Class<? extends HttpMessageConverter<?>> aClass, ServerHttpRequest request, ServerHttpResponse response) {
        // String类型不能直接包装
        if (returnType.getGenericParameterType().equals(String.class)) {
            ObjectMapper objectMapper = new ObjectMapper();
            try {
                // 将数据包装在ResultVo里后转换为json串进行返回
                return objectMapper.writeValueAsString(new ResultVo(data));
            } catch (JsonProcessingException e) {
                throw new APIException(ResultCode.RESPONSE_PACK_ERROR, e.getMessage());
            }
        }
        // 包装成ResultVo返回
        return new ResultVo(data);
    }
}
```

------

#### NOT 统一响应

有的时候，接口会需要我们返回一个字符串，比如一些回调通知的响应接口，或者健康检查接口，只需要返回一个 "success" 就行了，但是在统一响应的 AOP 包装之下，如果我们返回一个字符串，相应结果就是这样的

```json
{
  "code": 1000,
  "msg": "请求成功",
  "data": "success"
}
```




这种响应结果肯定是不行的，我们可以添加一个不包装响应结果的注解来适用于这种情况
```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface NotControllerResponseAdvice {
}
```

然后在增强过滤方法上过滤包含这个注解的方法：
```java
// 自动扫面 com.simon.rest 下面所有的类
@RestControllerAdvice(basePackages = {"com.simon.rest"})
public class ControllerResponseAdvice implements ResponseBodyAdvice<Object> {
    @Override
    public boolean supports(MethodParameter methodParameter, Class<? extends HttpMessageConverter<?>> aClass) {
        // response 是 ResultVO 类型或者注释了 NotControllerResponseAdvice 注解则不进行包装 
        return !(methodParameter.getParameterType().isAssignableFrom(ResultVo.class) || methodParameter.hasMethodAnnotation(NotControllerResponseAdvice.class));
    }

    @Override
    public Object beforeBodyWrite(Object data, MethodParameter returnType, MediaType mediaType, Class<? extends HttpMessageConverter<?>> aClass, ServerHttpRequest request, ServerHttpResponse response) {
        // String类型不能直接包装
        if (returnType.getGenericParameterType().equals(String.class)) {
            ObjectMapper objectMapper = new ObjectMapper();
            try {
                // 将数据包装在ResultVo里后转换为json串进行返回
                return objectMapper.writeValueAsString(new ResultVo(data));
            } catch (JsonProcessingException e) {
                throw new APIException(ResultCode.RESPONSE_PACK_ERROR, e.getMessage());
            }
        }
        // 包装成ResultVo返回
        return new ResultVo(data);
    }
}
```

最后就在不需要包装的方法上加上注解：
```java
@RestController
public class HealthController {
    @GetMapping("/health")
    @NotControllerResponseAdvice
    public String health() {
        return "success";
    }
}
```


------
### HTTP Code 和 Response Code

在返回的 JSON Response 中，我们会有自己的自定义 code，比如 1000 代表成功，1001 代表某种异常，1002 代表另一种异常等等。然后在返回的 HTTP Response 中，也会有自己的 HTTP Code，比如 200 代表成功，404 NOT FOUND，500 Bad Request 等。那么这两个 Code 的关系怎么处理呢，在开发过程中，一般会采用以下三种处理方式


------
#### HTTP Code 与 Response Code 相同

我们可以让 HTTP Code 与 Response Code 相同，比如某种异常的 Response Code 为 1001，那么就将响应结果的 HTTP Code 也改为 1001，这样做的好处在于某些情况下，我们可能并不关心 Response body 中的内容，只需要知道处理结果就行了，这样我们在拿到 HTTP Code 的时候就可以做对应的处理，而不需要进一步获取 Response body 中的内容



比如在登录账号的时候，以下响应结果代表用户账户名或者密码错误
```json
HTTP Code 1001
{
  "code": 1001,
  "msg": "账户名/密码错误",
  "data": "success"
}
```
这样前端代码在获取到 HTTP Code 的时候就可以弹出提示信息了，而不需要进一步获取 Response body 中的内容，只有在同一个 HTTP Code 对应着多条错误信息的时候，我们才需要获取 body 中的内容来进一步详细展示



这么做到好处在于减少了前端不需要具体响应信息时的代码，实现起来也较为简单，没有繁杂的对应关系
不方便的地方在于我们在使用自定义的 HTTP Code 的时候，使用 Spring Cloud Feign 调用远程接口的时候，它会将非 200 状态码的响应包装成自定义异常，就没法获取到 Response body 中的错误信息了


------
#### HTTP Code 固定不变

这种方式就是不关心 Response Code 的结果，只要服务器端能否响应结果，HTTP Code 就是 200
```json
HTTP Code 200
{
  "code": 1001,
  "msg": "账户名/密码错误",
  "data": "success"
}
```
这种方式的好处在于不在关心 HTTP Code 与 Response Code 的对应关系了，远程调用也更方便一些。但是前端获取响应结果的时候就需要判断两次，即先判断 HTTP Code 再判断 Response Code



------
#### Restful HTTP Code
其实，Restful 规范中已经定义了 HTPP Code 的含义，比如 200 表示成功，201 表示创建成功，204 表示删除成功等，以下是更多的状态码的定义
[Restful HTTP Code](https://wenku.baidu.com/view/4cae3aa08462caaedd3383c4bb4cf7ec4afeb61c.html)
相比于上面两种方式，这个肯定是最规范的，但也是最麻烦的，我们需要在定义错误状态码的时候去找到规范中定义的 HTTP Code，以及远程调用时的麻烦



-----
### HATEOAS
#### HATEOAS是什么


Richardson 提出了 REST 一种 成熟度模型，我们称之为 Richardson Maturity Model，这种模式将REST按照成熟度划分为 4 个等级
* Level0：使用 HTTP 作为 WEB 服务的传输方式，以 REST 样式公开 SOAP Web 服务
* Level1：使用适当的 URI（使用名词）公开资源，这种方式提出了资源的概念
* Level2：资源使用正确的 URI + HTTP 方法，比如更新用户就用 put 方式，查询用 get 方式
* Level3：使用 HATEOAS (作为应用程序状态引擎的超媒体)，在资源的表达中包含了链接信息，客户端可以在链接信息中发现可以执行的操作



HATEOAS 代表“超媒体是应用程序状态的引擎”，使用 HATEOAS 约束是 REST 风格中成熟度最高的，也是官方推荐的一种方式，没使用 HATEOAS 的项目，服务端和客户端是耦合的，客户端只能通过相关文档来知道服务端做了什么修改，使用 HATEOAS 约束的 REST 服务，服务端修改接口信息后，客户端可以通过服务器提供的资源的表达来智能地发现可以执行的操作，客户端不需要做啥修改，因为资源信息是会动态改变的

在 Spring 的官网，已经有提供这个项目的相关文档，链接：[https://spring.io/projects/spring-hateoas](https://spring.io/projects/spring-hateoas)

------



#### 示例

RESTful API 最好做到 Hypermedia，即返回结果中提供链接，连向其他 API 方法，使得用户不查文档，也知道下一步应该做什么
比如，当用户向 http://api.example.com 的根目录发出请求，会得到这样一个文档
``` json
{
    "link":{
        "rel":"collection https://www.example.com/zoos",
        "href":"https://api.example.com/zoos",
        "title":"List of zoos",
        "type":"application/vnd.yourformat+json"
    }
}
```


上面代码表示，文档中有一个 link 属性，用户读取这个属性就知道下一步该调用什么 API 了。rel 表示这个 API 与当前网址的关系（collection关系，并给出该collection的网址），href 表示 API 的路径，title 表示 API 的标题，type 表示返回类型
Hypermedia API 的设计被称为 HATEOAS。Github 的 API 就是这种设计，访问 api.github.com 会得到一个所有可 用API 的网址列表
``` json
{
  "current_user_url": "https://api.github.com/user",
  "authorizations_url": "https://api.github.com/authorizations",
  // ...
}
```
从上面可以看到，如果想获取当前用户的信息，应该去访问 api.github.com/user，然后就得到了下面结果
``` json
{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3"
}
```
上面代码表示，服务器给出了提示信息，以及文档的网址
但是这种规范实现起来较为麻烦，公司内部开发的话接口文档也远比其有效，是否需要实现大家见仁见智



------

#### 常用状态码



虽然 HTTP Status 的状态码有很多，但是在实际业务开发中，能够被我们使用到的都是很少的，以下是常用的一些状态码：

* 200 常用于查询成功的返回结果
* 201 新建成功
* 204 删除或修改成功，如果没有返回结果的话都可以使用，如果有返回结果的话也可使用 200
* 400 请求参数有误
* 401 token 有误
* 500 服务端错误