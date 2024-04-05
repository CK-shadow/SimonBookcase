---
title: 远程调用之 WebClient
date: 2022-09-18 14:47:33
tags: 
  - Java
categories: 
  - Java
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/%E8%BF%9C%E7%A8%8B%E8%B0%83%E7%94%A8%E4%B9%8B%20WebClient/pexels-miguel-%C3%A1-padri%C3%B1%C3%A1n-1591060.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/%E8%BF%9C%E7%A8%8B%E8%B0%83%E7%94%A8%E4%B9%8B%20WebClient/pexels-miguel-%C3%A1-padri%C3%B1%C3%A1n-1591060.jpg
---

## 远程调用之 WebClient

### 什么是 WebClient

#### 简介



在 Spring 5 之前，如果我们想要调用其他系统提供的 HTTP 服务，我们通常可以使用 Spring 提供的 RestTemplate 来访问

RestTemplate 用法很简单，但是它的不足之处在于它的请求是同步阻塞模式，因此存在一定性能瓶颈。当然，如果想要使用异步方式请求，也可以使用 AsyncRestTemplate

从 Spring 5 开始，Spring 中全面引入了 Reactive 响应式编程，WebClient 就属于 Spring WebFlux 的一部分。WebClient 的请求模式属于异步非阻塞、反应式的，能够以少量固定的线程处理高并发的 HTTP 请求。它对同步和异步以及流方案都有很好的支持

因此，从 Spring 5 开始，HTTP 服务之间的通信我们就可以考虑使用 WebClient 来取代之前的 RestTemplate



------

#### WebClient 与 RestTemplate 比较



WebClient 是一个功能完善的 Http 请求客户端，与 RestTemplate 相比，WebClient 支持以下内容：

* 非阻塞 I/O
* 反应流背压（消费者消费负载过高时主动反馈生产者放慢生产速度的一种机制）
* 具有高并发性，硬件资源消耗更少
* 流畅的API设计
* 同步和异步交互
* 流式传输支持



------

### 响应流式编程

#### 什么是响应流式编程



在服务 A（服务消费者）调用服务 B（服务提供者）场景中，当服务 A 向服务 B 发送 HTTP 请求时，线程 A 只有在发起请求和响应结果的一小部分时间内在有效使用 CPU，而更多的时间则只是在 阻塞式 地等待来自服务 B 中线程的处理结果。显然，整个过程的 CPU 利用效率是很低的，很多时间线程被浪费在了 I/O 阻塞上，无法执行其他的处理过程

如果将获取数据这件事情，通过发布订阅来实现，是不是就可以处理阻塞问题。在一个服务的内部，从 Web 服务层到数据访问层，再到数据库的整个调用链路，同样可以采用发布-订阅模式进行重构。这时候，我们希望当数据库中的数据一有变化（事件）就通知上游组件（通知机制），而不是上游组件通过主动拉取数据的方式来获取数据（阻塞）。这样，我们就可以不采用传统的同步调用方式来处理数据，而是由处于数据库上游的各层组件自动来执行事件。这就是响应式编程的核心特点

相较传统开发所普遍采用的“拉”模式，在响应式编程下，基于事件的触发和订阅机制，这就形成了一种类似“推”的工作方式。这种工作方式的优势就在于，生成事件和消费事件的过程是异步执行的，所以线程的生命周期都很短，也就意味着资源之间的竞争关系较少，服务器的响应能力也就越高



------

#### 背压



背压指订阅者能和发布者交互，可以调节发布者发布数据的速率，解决把订阅者压垮的问题

在 JDK 9 中，关键在于发布者 Publisher 的实现类 SubmissionPublisher 的 submit() 方法是 block 方法。订阅者会有一个缓冲池，默认为 Flow.defaultBufferSize() = 256。当订阅者的缓冲池满了之后，发布者调用 submit() 方法发布数据就会被阻塞，发布者就会停（慢）下来；订阅者消费了数据之后（调用Subscription.request方法），缓冲池有位置了，submit方法就会继续执行下去，就是通过这样的机制，实现了调节发布者发布数据的速率，消费得快，生成就快，消费得慢，发布者就会被阻塞，当然就会慢下来了



------

#### Mono 和 Flow



既然是响应流，那么订阅者订阅的自然不是某一个对象，而是一个"流"，即 Mono 和 Flow 两个对象

两者不同的是 Mono 表示的是包含 0 或者 1 个元素的异步序列，而 Flow 表示的是包含 0 到 N 个元素的异步序列



------

### WebClient 使用示例

#### 引入依赖



```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```



------

#### 构建 WebClient



构建 WebClient 的方式有多种：

```java
WebClient webClient = WebClient.create();
// 在 create 的时候还可以为其指定 baseUrl
WebClient webClient = WebClient.create("localhost:8080");
```



还可以使用构造者模式来为其指定各种参数

```java
WebClient webClient = WebClient.builder()
				.baseUrl("localhost:8080")
				.defaultHeader("A", "A")
				.defaultCookie("B", "B")
				.build();
```



------

#### Get 请求



```java
Mono<String> response = WebClient.create()
				.method(HttpMethod.GET)
				.uri("localhost:8080/hello")
        .retrieve()
        .bodyToMono(String.class);
System.out.println("response:" + response.block());
```

也可以这么写

```java
Mono<String> response = WebClient.create()
				.get()
				.uri("localhost:8080/hello")
				.retrieve()
				.bodyToMono(String.class);
System.out.println("response:" + response.block());
```



------

#### Get 请求携带参数



```java
// 添加请求参数
MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
params.add("A", "A");
params.add("B", "B");
Map<String, Object> pathVariable = new HashMap<>();
pathVariable.put("id", "123");
// 构建 uri
String uri = UriComponentsBuilder.fromUriString("localhost:8080/{id}")
				.queryParams(params)
				.uriVariables(pathVariable)
				.toUriString();
Mono<String> mono = WebClient.create()
				.get()
				.uri(uri)
				.retrieve()
				.bodyToMono(String.class);
System.out.println("Response:" + mono.block());
```



------

#### Post 请求提交 Form 表单



BodyInserters 类提供的各种工厂方法来构造 BodyInserter 对象并将其传递给 body 方法。BodyInserters 类包含从 Object，Publisher，Resource，FormData，MultipartData 等创建 BodyInserter 的方法

```java
MultiValueMap<String, String> formData = new LinkedMultiValueMap<>();
formData.add("A", "A");
formData.add("B", "B");
Mono<String> response = WebClient.create()
				.post()
				.uri("localhost:8080/form")
				.contentType(MediaType.APPLICATION_FORM_URLENCODED)
				.body(BodyInserters.fromFormData(formData))
				.retrieve()
				.bodyToMono(String.class);
System.out.println("response:" + response.block());
```



------

#### Post 请求提交 JSON



```java
User user = new User();
Mono<String> response = WebClient.create()
				.post()
				.uri("localhost:8080/json")
				.contentType(MediaType.APPLICATION_JSON)
				.body(Mono.just(user), User.class)
				.retrieve()
				.bodyToMono(String.class);
System.out.println("response:" + response.block());
```

或者

```java
User user = new User();
Mono<String> response = WebClient.create()
				.post()
				.uri("localhost:8080/json")
				.contentType(MediaType.APPLICATION_JSON)
				.bodyValue(user)
				.retrieve()
				.bodyToMono(String.class);
System.out.println("response:" + response.block());
```



------

#### 失败处理



```java
Mono<String> response = WebClient.create()
				.get()
				.uri("localhost:8080/unknown")
				.retrieve()
				.onStatus(e -> e.is4xxClientError(), resp -> {
						log.info("error:{}, message;{}", resp.statusCode().value(), resp.statusCode().getReasonPhrase());
						return Mono.error(new RuntimeException("Request Fail" + resp.statusCode().value()));
				})
				.bodyToMono(String.class)
				.doOnError(WebClientResponseException.class, error -> {
						log.info("error:{}, message:{}", error.getStatusCode(), error.getResponseBodyAsString());
						throw  new RuntimeException("Request Fail" + error.getResponseBodyAsString());
				})
				.onErrorReturn("fallback");
System.out.println("response:" + response.block());
```



* 可以使用 onStatus 根据status code进行异常适配
* 可以使用 doOnError 异常适配
* 可以使用 onErrorReturn 返回默认值



------

#### 动态 URL



```java
Mono<String> response = WebClient.create()
				.get()
				.uri("localhost:8080/user/{id}", 1)
				.retrieve()
				.bodyToMono(String.class);
System.out.println("response:" + response.block());
```



------

#### retrieve 和 exchange



retrieve 方法是直接获取响应 body，但是，如果需要响应的头信息、Cookie 等，可以使用 exchange 方法，该方法可以访问整个ClientResponse

由于响应的得到是异步的，所以都可以调用 block 方法来阻塞当前程序，等待获得响应的结果



```java
Mono<ClientResponse> mono = WebClient.create()
				.get()
				.uri("localhost:8080/hello")
				.exchange();
ClientResponse clientResponse = mono.block();
System.out.println("HTTP Status:" + clientResponse.statusCode());
System.out.println("Response Body:" + clientResponse.bodyToMono(String.class).block());
```



------

#### 下载文件



```java
Mono<ClientResponse> clientResponseMono = WebClient.create().get()
				.uri("localhost:8080/download")
				.accept(MediaType.APPLICATION_OCTET_STREAM)
				.exchange();
ClientResponse response = clientResponseMono.block();
String disposition = response.headers().asHttpHeaders().getFirst(HttpHeaders.CONTENT_DISPOSITION);
String filename = disposition.substring(disposition.indexOf("=") + 1);
Resource resource = response.bodyToMono(Resource.class).block();
File output = new File(filename);
FileUtils.copyInputStreamToFile(resource.getInputStream(), output);
System.out.println(output.getAbsolutePath());
```





下载图片

```java
Resource resource = WebClient.create().get()
					.uri("localhost:8080/download")
					.accept(MediaType.IMAGE_PNG)
					.retrieve().bodyToMono(Resource. class).block();
BufferedImage bufferedImage = ImageIO.read(resource.getInputStream());
ImageIO.write(bufferedImage, "PNG", new File("filePath"));
```



------

#### 上传文件



```java
// 构建文件数据
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.IMAGE_PNG);
HttpEntity<ClassPathResource> entity = new HttpEntity<>(new ClassPathResource("filepath"), headers);
MultiValueMap<String, Object> data = new LinkedMultiValueMap<>();
data.add("filename", entity);

Mono<String> response = WebClient.create()
				.post()
				.uri("localhost:8080/upload")
				.contentType(MediaType.IMAGE_PNG)
				.body(BodyInserters.fromMultipartData(data))
				.retrieve()
				.bodyToMono(String.class);
System.out.println("response:" + response.block());
```



------

#### 组合返回结果



有时，我们可能需要调用多个接口，来获取它们组合的结果。更高效地方式是避免单独阻塞每个响应，而是等待组合结果



```java
WebClient webClient = WebClient.create("localhost:8080");
Mono<String> mono1 = webClient.get()
				.uri("/user/{id}", 1)
				.retrieve()
				.bodyToMono(String.class);
Mono<String> mono2 = webClient.get()
				.uri("/user/{2}", 2)
				.retrieve()
				.bodyToMono(String.class);
Map<String, String> data = Mono.zip(mono1, mono2, (resp1, resp2) -> {
				Map<String, String> map = new HashMap<>();
				map.put("resp1", resp1);
				map.put("resp2", resp2);
				return map;
}).block();
System.out.println("Response:" + data);
```



------

#### Fileter 过滤器



可以通过设置 filter 拦截器，统一修改拦截请求，比如认证的场景，如下示例，filter 注册单个拦截器，filters 可以注册多个拦截器，basicAuthentication 是系统内置的用于 basicAuth 的拦截器，limitResponseSize 是系统内置用于限制响值 byte 大小的拦截器



```java
Mono<String> response = WebClient.builder()
				.filter((request, next) -> {
						ClientRequest filtered = ClientRequest.from(request)
								.header("A", "A")
								.build();
						return next.exchange(request);
				})
				.filters(filters -> {
						filters.add(ExchangeFilterFunctions.basicAuthentication("username", "password"));
						filters.add(ExchangeFilterFunctions.limitResponseSize(800));
				})
				.build().get()
				.uri("localhost:8080/hello")
				.retrieve()
				.bodyToMono(String.class);
System.out.println("Response:" + response.block());	
```





------

#### WebSocket 支持



WebClient 不支持 websocket 请求，请求 websocket 接口时需要使用 WebSocketClient



```java
WebSocketClient webSocketClient = new ReactorNettyWebSocketClient();
URI uri = new URI("ws://localhost:8080/socket");
webSocketClient.execute(uri, session -> session.receive()
				.doOnNext(System.out::println)
				.then());
```

