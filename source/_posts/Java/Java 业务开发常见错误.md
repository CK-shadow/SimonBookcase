---
title: Java 业务开发常见错误
date: 2022-10-22 19:23:32
tags: Java
categories: 
  - Java
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%20%E4%B8%9A%E5%8A%A1%E5%BC%80%E5%8F%91%E5%B8%B8%E8%A7%81%E9%94%99%E8%AF%AF/pexels-kyle-karbowski-9700586.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%20%E4%B8%9A%E5%8A%A1%E5%BC%80%E5%8F%91%E5%B8%B8%E8%A7%81%E9%94%99%E8%AF%AF/pexels-kyle-karbowski-9700586.jpg
---

## Java 业务开发常见错误

### 线程安全

#### 线程重用导致信息错乱



有时我们会使用 ThreadLocal 来缓存一些信息。但是需要注意，程序运行在 Tomcat 中，执行程序的线程是 Tomcat 的工作线程，而 Tomcat 的工作线程是基于线程池的

所以，线程池会重用固定的几个线程，一旦线程重用，那么很可能首次从 ThreadLocal 获取的值是之前请求遗留的值。这时，ThreadLocal 中的数据就是上一个请求缓存的数据

因此，使用类似 ThreadLocal 工具来存放一些数据时，需要特别注意在代码运行完后，显式地去清空设置的数据



```java
@GetMapping("right")
public Map right(@RequestParam("userId") Integer userId) {
    String before  = Thread.currentThread().getName() + ":" + currentUser.get();
    currentUser.set(userId);
    try {
        String after = Thread.currentThread().getName() + ":" + currentUser.get();
        Map result = new HashMap();
        result.put("before", before);
        result.put("after", after);
        return result;
    } finally {
        //在finally代码块中删除ThreadLocal中的数据，确保数据不串
        currentUser.remove();
    }
}
```



------

#### 线程安全的并发工具并不能解决所有的线程安全问题



例如 ConcurrentHashMap 只能保证提供的原子性读写操作是线程安全的，如果我们需要对其进行复合操作，例如先读后写，它并不能保证这一整个操作的原子性，就需要我们自己来维护了，比如对其加锁

当然，ConcurrentHashMap 提供了一些原子性的简单复合逻辑方法，用好这些方法就可以发挥其威力。这就引申出代码中常见的另一个问题：在使用一些类库提供的高级工具类时，开发人员可能还是按照旧的方式去使用这些新类，因为没有使用其特性，所以无法发挥其威力



------

#### 充分利用并发工具特性



让我们来使用 CurrentHashMap 来统计 key 的出现次数，Key 的范围是 10，使用最多 10 个并发，循环操作 1000 万次

我们吸取之前的教训，直接通过锁的方式锁住 Map，然后做判断、读取现在的累计值、加 1、保存累加后值的逻辑



```java
//循环次数
private static int LOOP_COUNT = 10000000;
//线程数量
private static int THREAD_COUNT = 10;
//元素数量
private static int ITEM_COUNT = 10;
private Map<String, Long> normaluse() throws InterruptedException {
    ConcurrentHashMap<String, Long> freqs = new ConcurrentHashMap<>(ITEM_COUNT);
    ForkJoinPool forkJoinPool = new ForkJoinPool(THREAD_COUNT);
    forkJoinPool.execute(() -> IntStream.rangeClosed(1, LOOP_COUNT).parallel().forEach(i -> {
        //获得一个随机的Key
        String key = "item" + ThreadLocalRandom.current().nextInt(ITEM_COUNT);
                synchronized (freqs) {      
                    if (freqs.containsKey(key)) {
                        //Key存在则+1
                        freqs.put(key, freqs.get(key) + 1);
                    } else {
                        //Key不存在则初始化为1
                        freqs.put(key, 1L);
                    }
                }
            }
    ));
    forkJoinPool.shutdown();
    forkJoinPool.awaitTermination(1, TimeUnit.HOURS);
    return freqs;
}
```



我们可以使用 CurrentHashMap 的特性来对这段代码进行改进

使用 ConcurrentHashMap 的原子性方法 computeIfAbsent 来做复合逻辑操作，判断 Key 是否存在 Value，如果不存在则把 Lambda 表达式运行后的结果放入 Map 作为 Value，也就是新创建一个 LongAdder 对象，最后返回 Value

由于 computeIfAbsent 方法返回的 Value 是 LongAdder，是一个线程安全的累加器，因此可以直接调用其 increment 方法进行累加

这样在确保线程安全的情况下达到极致性能，把之前 7 行代码替换为了 1 行



```java
private Map<String, Long> gooduse() throws InterruptedException {
    ConcurrentHashMap<String, LongAdder> freqs = new ConcurrentHashMap<>(ITEM_COUNT);
    ForkJoinPool forkJoinPool = new ForkJoinPool(THREAD_COUNT);
    forkJoinPool.execute(() -> IntStream.rangeClosed(1, LOOP_COUNT).parallel().forEach(i -> {
        String key = "item" + ThreadLocalRandom.current().nextInt(ITEM_COUNT);
                //利用computeIfAbsent()方法来实例化LongAdder，然后利用LongAdder来进行线程安全计数
                freqs.computeIfAbsent(key, k -> new LongAdder()).increment();
            }
    ));
    forkJoinPool.shutdown();
    forkJoinPool.awaitTermination(1, TimeUnit.HOURS);
    //因为我们的Value是LongAdder而不是Long，所以需要做一次转换才能返回
    return freqs.entrySet().stream()
            .collect(Collectors.toMap(
                    e -> e.getKey(),
                    e -> e.getValue().longValue())
            );
}
```



通过处理时间对比可以看到，优化后的代码，相比使用锁来操作 ConcurrentHashMap 的方式，性能提升了 10 倍。原因就在源码最核心的部分，也就是 Java 自带的 Unsafe 实现的 CAS。它在虚拟机层面确保了写入数据的原子性，比加锁的效率高得多

像 ConcurrentHashMap 这样的高级并发工具的确提供了一些高级 API，只有充分了解其特性才能最大化其威力，而不能因为其足够高级、酷炫盲目使用



------

#### 不要盲目使用工具



CopyOnWrite 是一个时髦的技术，不管是 Linux 还是 Redis 都会用到。在 Java 中，CopyOnWriteArrayList 虽然是一个线程安全的 ArrayList，但因为其实现方式是，每次修改数据时都会复制一份数据出来，所以有明显的适用场景，即读多写少或者说希望无锁读的场景。如果读写比例均衡或者有大量写操作的话，使用 CopyOnWriteArrayList 的性能会非常糟糕



下面的代码便是对 CopyOnWriteArrayList 和普通加锁方式的 ArrayList 在并发读和并发写场景下的性能测试

```java
//测试并发写的性能
@GetMapping("write")
public Map testWrite() {
    List<Integer> copyOnWriteArrayList = new CopyOnWriteArrayList<>();
    List<Integer> synchronizedList = Collections.synchronizedList(new ArrayList<>());
    StopWatch stopWatch = new StopWatch();
    int loopCount = 100000;
    stopWatch.start("Write:copyOnWriteArrayList");
    //循环100000次并发往CopyOnWriteArrayList写入随机元素
    IntStream.rangeClosed(1, loopCount).parallel().forEach(__ -> copyOnWriteArrayList.add(ThreadLocalRandom.current().nextInt(loopCount)));
    stopWatch.stop();
    stopWatch.start("Write:synchronizedList");
    //循环100000次并发往加锁的ArrayList写入随机元素
    IntStream.rangeClosed(1, loopCount).parallel().forEach(__ -> synchronizedList.add(ThreadLocalRandom.current().nextInt(loopCount)));
    stopWatch.stop();
    log.info(stopWatch.prettyPrint());
    Map result = new HashMap();
    result.put("copyOnWriteArrayList", copyOnWriteArrayList.size());
    result.put("synchronizedList", synchronizedList.size());
    return result;
}

//帮助方法用来填充List
private void addAll(List<Integer> list) {
    list.addAll(IntStream.rangeClosed(1, 1000000).boxed().collect(Collectors.toList()));
}

//测试并发读的性能
@GetMapping("read")
public Map testRead() {
    //创建两个测试对象
    List<Integer> copyOnWriteArrayList = new CopyOnWriteArrayList<>();
    List<Integer> synchronizedList = Collections.synchronizedList(new ArrayList<>());
    //填充数据   
    addAll(copyOnWriteArrayList);
    addAll(synchronizedList);
    StopWatch stopWatch = new StopWatch();
    int loopCount = 1000000;
    int count = copyOnWriteArrayList.size();
    stopWatch.start("Read:copyOnWriteArrayList");
    //循环1000000次并发从CopyOnWriteArrayList随机查询元素
    IntStream.rangeClosed(1, loopCount).parallel().forEach(__ -> copyOnWriteArrayList.get(ThreadLocalRandom.current().nextInt(count)));
    stopWatch.stop();
    stopWatch.start("Read:synchronizedList");
    //循环1000000次并发从加锁的ArrayList随机查询元素
    IntStream.range(0, loopCount).parallel().forEach(__ -> synchronizedList.get(ThreadLocalRandom.current().nextInt(count)));
    stopWatch.stop();
    log.info(stopWatch.prettyPrint());
    Map result = new HashMap();
    result.put("copyOnWriteArrayList", copyOnWriteArrayList.size());
    result.put("synchronizedList", synchronizedList.size());
    return result;
}
```



测试结果发现，大量写的场景（10 万次 add 操作），CopyOnWriteArray 几乎比同步的 ArrayList 慢一百倍。而在大量读的场景下（100 万次 get 操作），CopyOnWriteArray 又比同步的 ArrayList 快五倍以上。因为 CopyOnWriteList 每次 add 时，都会用 Arrays.copyOf 创建一个新数组，频繁 add 时内存的申请释放消耗会很大



没有了解清楚工具的适用场景，在不合适的场景下使用了错误的工具会导致性能更差。比如，没有理解 CopyOnWriteArrayList 的适用场景，把它用在了读写均衡或者大量写操作的场景下，导致性能问题。对于这种场景，你可以考虑是用普通的 List



------

### 并发锁

#### 清楚加锁和需要加锁的对象



静态字段属于类，类级别的锁才能保护；而非静态字段属于类实例，实例级别的锁就可以保护

比如以下这段代码



```java
class Data {
    @Getter
    private static int counter = 0;
    
    public static int reset() {
        counter = 0;
        return counter;
    }

    public synchronized void wrong() {
        counter++;
    }
}

// 对其进行测试

@GetMapping("wrong")
public int wrong(@RequestParam(value = "count", defaultValue = "1000000") int count) {
    Data.reset();
    //多线程循环一定次数调用Data类不同实例的wrong方法
    IntStream.rangeClosed(1, count).parallel().forEach(i -> new Data().wrong());
    return Data.getCounter();
}
```



默认运行 100 万次，所以执行后应该输出 100 万，但是实际输出结果却并非如此。在非静态的 wrong 方法上加锁，只能确保多个线程无法执行同一个实例的 wrong 方法，却不能保证不会执行不同实例的 wrong 方法。而静态的 counter 在多个实例中共享，所以必然会出现线程安全问题

所以，我们需要变更一下加锁方式



```java
class Data {
    @Getter
    private static int counter = 0;
    private static Object locker = new Object();

    public void right() {
        synchronized (locker) {
            counter++;
        }
    }
}
```



也可以把 wrong 方法定义为静态，这个时候锁是类级别的。可以是可以，但我们不可能为了解决线程安全问题改变代码结构，把实例方法改为静态方法



------

#### 加锁要考虑场景和密度



如果精细化考虑了锁应用范围后，性能还无法满足需求的话，我们就要考虑另一个维度的粒度问题了，即：区分读写场景以及资源的访问冲突，考虑使用悲观方式的锁还是乐观方式的锁

对于读写比例差异明显的场景，考虑使用 ReentrantReadWriteLock 细化区分读写锁，来提高性能

如果你的 JDK 版本高于 1.8、共享资源的冲突概率也没那么大的话，考虑使用 StampedLock 的乐观读的特性，进一步提高性能

JDK 里 ReentrantLock 和 ReentrantReadWriteLock 都提供了公平锁的版本，在没有明确需求的情况下不要轻易开启公平锁特性，在任务很轻的情况下开启公平锁可能会让性能下降上百倍



------

#### 小心死锁



业务逻辑中有多把锁时要考虑死锁问题，通常的规避方案是，避免无限等待和循环等待

此外，如果业务逻辑中锁的实现比较复杂的话，要仔细看看加锁和释放是否配对，是否有遗漏释放或重复释放的可能性；并且对于分布式锁要考虑锁自动超时释放了，而业务逻辑却还在进行的情况下，如果别的线线程或进程拿到了相同的锁，可能会导致重复执行



------

### 线程池

#### 线程池的声明需要手动进行



Java 中的 Executors  类定义了一些快捷的工具方法，来帮助我们快速创建线程池。《阿里巴巴 Java 开发手册》中提到，禁止使用这些方法来创建线程池，而应该手动 new  ThreadPoolExecutor 来创建线程池。这一条规则的背后，是大量血淋淋的生产事故，最典型的就是 newFixedThreadPool 和 newCachedThreadPool，可能因为资源耗尽导致 OOM 问题

翻看 newFixedThreadPool  方法的源码不难发现，线程池的工作队列直接 new 了一个 LinkedBlockingQueue，而默认构造方法的  LinkedBlockingQueue 是一个 Integer.MAX_VALUE 长度的队列，可以认为是无界的。虽然使用 newFixedThreadPool 可以把工作线程控制在固定的数量上，但任务队列是无界的。如果任务较多并且执行较慢的话，队列可能会快速积压，撑爆内存导致 OOM

翻看 newCachedThreadPool  的源码可以看到，这种线程池的最大线程数是 Integer.MAX_VALUE，可以认为是没有上限的，而其工作队列  SynchronousQueue  是一个没有存储空间的阻塞队列。这意味着，只要有请求到来，就必须找到一条工作线程来处理，如果当前没有空闲的线程就再创建一条新的

而这两种方式，在线程池处理线程任务较多且线程阻塞时间较长的时候，都是有可能导致 OOM 的



因此，不建议使用 Executors 提供的两种快捷的线程池，原因如下：

* 我们需要根据自己的场景、并发情况来评估线程池的几个核心参数，包括核心线程数、最大线程数、线程回收策略、工作队列的类型，以及拒绝策略，确保线程池的工作行为符合需求，一般都需要设置有界的工作队列和可控的线程数
* 任何时候，都应该为自定义线程池指定有意义的名称，以方便排查问题。当出现线程数量暴增、线程死锁、线程占用大量 CPU、线程执行出现异常等问题时，我们往往会抓取线程栈。此时，有意义的线程名称，就可以方便我们定位问题
* 除了建议手动声明线程池以外，还建议用一些监控手段来观察线程池的状态



------

#### 线程池线程管理策略详解



可以总结出线程池默认的工作行为：

1. 不会初始化  corePoolSize  个线程，有任务来了才创建工作线程
2. 当核心线程满了之后不会立即扩容线程池，而是把任务堆积到工作队列中
3. 当工作队列满了后扩容线程池，一直到线程个数达到 maximumPoolSize 为止
4. 如果队列已满且达到了最大线程后还有任务进来，按照拒绝策略处理
5. 当线程数大于核心线程数时，线程等待  keepAliveTime 后还是没有任务需要处理的话，收缩线程到核心线程数



了解这个策略，有助于我们根据实际的容量规划需求，为线程池设置合适的初始化参数。当然，我们也可以通过一些手段来改变这些默认工作行为，比如：

1. 声明线程池后立即调用 prestartAllCoreThreads 方法，来启动所有核心线程
2. 传入 true 给 allowCoreThreadTimeOut  方法，来让线程池在空闲的时候同样回收核心线程



------

#### 务必确认清楚线程池本身是不是复用的



既然使用了线程池就需要确保线程池是在复用的，每次 new 一个线程池出来可能比不用线程池还糟糕。如果你没有直接声明线程池而是使用其他同学提供的类库来获得一个线程池，请务必查看源码，以确认线程池的实例化方式和配置是符合预期的



------

#### 需要仔细斟酌线程池的混用策略



要根据任务的“轻重缓急”来指定线程池的核心参数，包括线程数、回收策略和任务队列：

* 对于执行比较慢、数量不大的 IO 任务，或许要考虑更多的线程数，而不需要太大的队列
* 而对于吞吐量较大的计算型任务，线程数量不宜过多，可以是 CPU 核数或核数 * 2（理由是，线程一定调度到某个 CPU 进行执行，如果任务本身是 CPU 绑定的任务，那么过多的线程只会增加线程切换的开销，并不能提升吞吐量），但可能需要较长的队列来做缓冲



Java 8 的 parallel stream 功能，可以让我们很方便地并行处理集合中的元素，其背后是共享同一个 ForkJoinPool，默认并行度是 CPU 核数 -1。对于 CPU  绑定的任务来说，使用这样的配置比较合适，但如果集合操作涉及同步 IO 操作的话（比如数据库操作、外部服务调用等），建议自定义一个  ForkJoinPool（或普通线程池）



------

### 连接池

#### 注意鉴别客户端 SDK 是否基于连接池



在使用三方客户端进行网络通信时，我们首先要确定客户端 SDK 是否是基于连接池技术实现的。我们知道，TCP 是面向连接的基于字节流的协议：

* 面向连接，意味着连接需要先创建再使用，创建连接的三次握手有一定开销
* 基于字节流，意味着字节是发送数据的最小单元，TCP 协议本身无法区分哪几个字节是完整的消息体，也无法感知是否有多个客户端在使用同一个 TCP 连接，TCP 只是一个读写数据的管道

如果客户端 SDK 没有使用连接池，而直接是 TCP 连接，那么就需要考虑每次建立 TCP 连接的开销，并且因为 TCP 基于字节流，在多线程的情况下对同一连接进行复用，可能会产生线程安全问题



我们先看一下涉及 TCP 连接的客户端 SDK，对外提供 API 的三种方式。在面对各种三方客户端的时候，只有先识别出其属于哪一种，才能理清楚使用方式

* 连接池和连接分离的 API：有一个  XXXPool 类负责连接池实现，先从其获得连接  XXXConnection，然后用获得的连接进行服务端请求，完成后使用者需要归还连接。通常，XXXPool  是线程安全的，可以并发获取和归还连接，而 XXXConnection 是非线程安全的
* 内部带有连接池的 API：对外提供一个 XXXClient 类，通过这个类可以直接进行服务端请求；这个类内部维护了连接池，SDK 使用者无需考虑连接的获取和归还问题。一般而言，XXXClient 是线程安全的
* 非连接池的 API：一般命名为 XXXConnection，以区分其是基于连接池还是单连接的，而不建议命名为 XXXClient 或直接是 XXX。直接连接方式的 API 基于单一连接，每次使用都需要创建和断开连接，性能一般，且通常不是线程安全的

虽然上面提到了 SDK 一般的命名习惯，但不排除有一些客户端特立独行，因此在使用三方 SDK 时，一定要先查看官方文档了解其最佳实践



------

#### 连接池的配置不是一成不变的



最大连接数不是设置得越大越好。如果设置得太大，不仅仅是客户端需要耗费过多的资源维护连接，更重要的是由于服务端对应的是多个客户端，每一个客户端都保持大量的连接，会给服务端带来更大的压力

当然，连接池最大连接数设置得太小，很可能会因为获取连接的等待时间太长，导致吞吐量低下，甚至超时无法获取连接



在真实情况下，只要数据库可以承受，你可以选择在遇到连接超限的时候先设置一个足够大的连接数，然后观察最终应用的并发，再按照实际并发数留出一半的余量来设置最终的最大连接

更合适的做法是，对类似数据库连接池的重要资源进行持续检测，并设置一半的使用量作为报警阈值，出现预警后及时扩容



------

### HTTP 调用

#### 连接超时和读取超时参数



连接超时参数 ConnectTimeout，让用户配置建连阶段的最长等待时间

读取超时参数 ReadTimeout，用来控制从 Socket 上读取数据的最长等待时间



一般来说，TCP 三次握手建立连接需要的时间非常短，通常在毫秒级最多到秒级，不可能需要十几秒甚至几十秒。如果很久都无法建连，很可能是网络或防火墙配置的问题。这种情况下，如果几秒连接不上，那么可能永远也连接不上。因此，设置特别长的连接超时意义不大，将其配置得短一些（比如 1~5 秒）即可。如果是纯内网调用的话，这个参数可以设置得更短，在下游服务离线无法连接的时候，可以快速失败

读取连接超时，可能是请求中断，请求无法到服务端；也可能是服务端接收到了请求，还未处理或者正在处理便超时了；也有可能是服务端已经处理完毕，返回响应结果时超时。因此，读取连接超时不完全等同于请求失败



------

#### Feign 和 Ribbon 的超时配置



1. 默认情况下 Feign 的读取超时是 1 秒，如此短的读取超时时间是不合理的

2. 如果要配置 Feign 的读取超时，就必须同时配置连接超时，才能生效

   ```properties
   feign.client.config.default.readTimeout=3000
   feign.client.config.default.connectTimeout=3000
   ```

3. 但是单独的超时可以覆盖全局超时

   ```properties
   feign.client.config.clientsdk.readTimeout=2000
   ```

4. 除了可以配置 Feign，也可以配置 Ribbon 组件的参数来修改两个超时时间，但是参数首字母要大写

   ```properties
   ribbon.ReadTimeout=4000
   ribbon.ConnectTimeout=4000
   ```

5. 同时配置 Feign 和 Ribbon 的超时，以 Feign 为准



------

#### Ribbon 的自动重试



翻看 Ribbon 的源码可以发现，MaxAutoRetriesNextServer 参数默认为 1，也就是 Get 请求在某个服务端节点出现问题（比如读取超时）时，Ribbon 会自动重试一次

如果我们不想起进行充实，可以将将 MaxAutoRetriesNextServer 参数配置为 0，禁用服务调用失败后在下一个服务端节点的自动重试。在配置文件中添加一行即可：

```properties
ribbon.MaxAutoRetriesNextServer=0
```



------

#### HTTPClient 的并发限制



查看 PoolingHttpClientConnectionManager 源码，可以注意到有两个重要参数：

* defaultMaxPerRoute=2，也就是同一个主机 / 域名的最大并发请求数为 2
* maxTotal=20，也就是所有主机整体最大并发为 20，这也是 HttpClient 整体的并发度



在使用 HTTPClient 访问下游接口时，如果 TPS 上不去，也有可能是这两个配置的问题



------

### Spring 声明式事务

#### 小心 Spring 的事务可能没有生效



除非特殊配置（比如使用 AspectJ 静态织入实现 AOP），否则只有定义在 public 方法上的 @Transactional 才能生效。原因是，Spring 默认通过动态代理的方式实现 AOP，对目标方法进行增强，private 方法无法代理到，Spring 自然也无法动态增强事务处理逻辑

必须通过代理过的类从外部调用目标方法才能生效



------

#### 事务即便生效也不一定能回滚



只有异常传播出了标记了 @Transactional 注解的方法，事务才能回滚

默认情况下，出现 RuntimeException（非受检异常）或 Error 的时候，Spring 才会回滚事务



------

#### 请确认事务传播配置是否符合自己的业务逻辑



如果方法涉及多次数据库操作，并希望将它们作为独立的事务进行提交或回滚，那么我们需要考虑进一步细化配置事务传播方式，也就是 @Transactional 注解的 Propagation 属性



------

### 数据库索引

#### InnoDB 是如何存储数据的



虽然数据保存在磁盘中，但其处理是在内存中进行的。为了减少磁盘随机读取次数，InnoDB 采用页而不是行的粒度来保存数据，即数据被分成若干页，以页为单位保存在磁盘中。InnoDB 的页大小，一般是 16KB

各个数据页组成一个双向链表，每个数据页中的记录按照主键顺序组成单向链表；每一个数据页中有一个页目录，方便按照主键查询记录。数据页的结构如下：

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%20%E4%B8%9A%E5%8A%A1%E5%BC%80%E5%8F%91%E5%B8%B8%E8%A7%81%E9%94%99%E8%AF%AF/1302b4a8d877609486c9a9eed2d8d8d1.webp)

页目录通过槽把记录分成不同的小组，每个小组有若干条记录。如图所示，记录中最前面的小方块中的数字，代表的是当前分组的记录条数，最小和最大的槽指向 2 个特殊的伪记录。有了槽之后，我们按照主键搜索页中记录时，就可以采用二分法快速搜索，无需从最小记录开始遍历整个页中的记录链表



------

#### 考虑额外创建二级索引的代价



首先是维护代价。创建 N 个二级索引，就需要再创建 N 棵 B+ 树，新增数据时不仅要修改聚簇索引，还需要修改这 N 个二级索引

其次是空间代价。虽然二级索引不保存原始数据，但要保存索引列的数据，所以会占用更多的空间

最后是回表的代价。二级索引不保存原始数据，通过索引找到主键后需要再查询聚簇索引，才能得到我们要的数据



关于索引开销的最佳实践：

第一，无需一开始就建立索引，可以等到业务场景明确后，或者是数据量超过 1 万、查询变慢后，再针对需要查询、排序或分组的字段创建索引

第二，尽量索引轻量级的字段，比如能索引 int 字段就不要索引 varchar 字段。索引字段也可以是部分前缀，在创建的时候指定字段索引长度。针对长文本的搜索，可以考虑使用 Elasticsearch 等专门用于文本搜索的索引数据库

第三，尽量不要在 SQL 语句中 SELECT *，而是 SELECT 必要的字段，甚至可以考虑使用联合索引来包含我们要搜索的字段，既能实现索引加速，又可以避免回表的开销



------

### 数值计算

#### 危险的 Double



众所周知，使用浮点数进行计算的时候可能会丢失精度。这是因为对于计算机而言，0.1 无法精确表达，这是浮点数计算造成精度损失的根源

```java
System.out.println(0.1+0.2); // 输出结果：0.30000000000000004
```

因此我们经常会使用 BigDecimal 来进行数值计算，但 BigDecimal 的结果还是不精确，只不过提高了精度而已

```java
System.out.println(new BigDecimal(0.1).add(new BigDecimal(0.2))); 
// 输出结果：0.3000000000000000166533453693773481063544750213623046875
```

所以，使用 BigDecimal 表示和计算浮点数，且务必使用字符串的构造方法来初始化 BigDecimal

```java
System.out.println(new BigDecimal("0.1").add(new BigDecimal("0.2"))); // 输出结果：0.3
```



------

#### 考虑浮点数舍入和格式化的方式



对 3.35D 和 3.35F 进行四舍五入计算，得到的结果并不相同

```java
System.out.println(String.format("%.1f", 3.35D)); // 3.4
System.out.println(String.format("%.1f", 3.35F)); // 3.3
```

这就是由精度问题和舍入方式共同导致的，double 和 float 的 3.35 其实相当于 3.350xxx 和 3.349xxx

所以浮点数的字符串格式化也要通过 BigDecimal 进行

```java
BigDecimal num1 = new BigDecimal("3.35");
BigDecimal num2 = num1.setScale(1, BigDecimal.ROUND_DOWN);
System.out.println(num2);
BigDecimal num3 = num1.setScale(1, BigDecimal.ROUND_HALF_UP);
System.out.println(num3);
```



------

#### 判等问题



在使用 equals 方法比较 1.0 和 1 这两个 BigDecimal 判等，结果是 false

```java
System.out.println(new BigDecimal("1.0").equals(new BigDecimal("1")))
```

BigDecimal 的 equals 方法的注释中说明了原因，equals 比较的是 BigDecimal 的 value 和 scale，即小数点右边的位数，1.0 的 scale 是 1，1 的 scale 是 0，所以结果一定是 false

如果我们希望只比较 BigDecimal 的 value，可以使用 compareTo 方法

```java
System.out.println(new BigDecimal("1.0").compareTo(new BigDecimal("1"))==0);
```



这是，如果我们使用 BigDecimal 作为 HashMap 或 HashSet 的 key 的时候就会产生问题，因为对他们而言 1 和 1.0 是两个不同的 key，解决这个问题的办法有两个：

1. 使用 TreeSet 替换 HashSet。TreeSet 不使用 hashCode 方法，也不使用 equals 比较元素，而是使用 compareTo 方法，所以不会有问题
2. 把 BigDecimal 存入 HashSet 或 HashMap 前，先使用 stripTrailingZeros 方法去掉尾部的零，比较的时候也去掉尾部的 0，确保 value 相同的 BigDecimal，scale 也是一致的



------

### 集合类

#### 使用 Arrays.asList 把数据转换为 List 的三个坑



不能直接使用 Arrays.asList 来转换基本类型数组

如下，通过 asList 方法将 int 数组转为 ArrayList，得到的并不是包含三个数字的 List 集合，而是包含一个 int 数组的 List 集合，这个 int 数字中有三个数字

```java
int[] arr = {1, 2, 3};
List list = Arrays.asList(arr);
```

修复方式有两种，如果使用 Java8 以上版本可以使用 Arrays.stream 方法来转换，否则可以把 int 数组声明为包装类型 Integer 数组

```java
int[] arr1 = {1, 2, 3};
List list1 = Arrays.stream(arr1).boxed().collect(Collectors.toList());


Integer[] arr2 = {1, 2, 3};
List list2 = Arrays.asList(arr2);
```





Arrays.asList 返回的 List 不支持增删操作

```java
String[] arr = {"1", "2", "3"};
List list = Arrays.asList(arr);
list.add("4"); // 抛出 UnsupportedOperationException
```

Arrays.asList 返回的 List 并不是我们期望的 java.util.ArrayList，而是 Arrays 的内部类 ArrayList。ArrayList 内部类继承自 AbstractList 类，并没有覆写父类的 add 方法，而父类中 add 方法的实现，就是抛出 UnsupportedOperationException





对原始数组的修改会影响到我们获得的那个 List

看一下 ArrayList 的实现，可以发现 ArrayList 其实是直接使用了原始的数组。所以，我们要特别小心，把通过 Arrays.asList 获得的 List 交给其他方法处理，很容易因为共享了数组，相互修改产生 Bug

修复方式比较简单，重新 new 一个 ArrayList 初始化 Arrays.asList 返回的 List 即可



------

#### 谨慎使用 List.subList 进行切片



业务开发时常常要对 List 做切片处理，即取出其中部分元素构成一个新的 List，我们通常会想到使用 List.subList 方法。但，和 Arrays.asList 的问题类似，List.subList 返回的子 List 不是一个普通的 ArrayList。这个子 List 可以认为是原始 List 的视图，会和原始 List 相互影响

既然 SubList 相当于原始 List 的视图，那么避免相互影响的修复方式有两种：

1. 不直接使用 subList 方法返回的 SubList，而是重新使用 new ArrayList，在构造方法传入 SubList，来构建一个独立的 ArrayList
2. 对于 Java 8 使用 Stream 的 skip 和 limit API 来跳过流中的元素，以及限制流中元素的个数，同样可以达到 SubList 切片的目的

```java
//方式一：
List<Integer> subList = new ArrayList<>(list.subList(1, 4));

//方式二：
List<Integer> subList = list.stream().skip(1).limit(3).collect(Collectors.toList());
```



------

### 序列化

#### 序列化和反序列化需要确保算法一致



在业务开发中，我们经常会使用 RedisTemplate 来操作 Redis，数据（包含 Key 和 Value）要保存到 Redis，需要经过序列化算法来序列化成字符串。在 value 也是字符串的时候，我们就希望将其直接存进 Redis 而不希望再进行序列话的操作，这时候就可以用 StringRedisTemplate



那么 RedisTemplate 和 StringRedisTemplate 有什么区别呢

```java
redisTemplate.opsForValue().set("redisTemplate", new User("Simon"));
stringRedisTemplate.opsForValue().set("stringRedisTemplate", objectMapper.writeValueAsString(new User("Simon")));

System.out.println(redisTemplate.opsForValue().get("stringRedisTemplate")); // null
System.out.println(stringRedisTemplate.opsForValue().get("redisTemplate")); // null
```

如图所示，我们分别用 RedisTemplate 和 StringRedisTemplate 存一个相同的值，并交换 template 读取出来，会发现两个 template 都读取不到对方存储的内容

因为 RedisTemplate 存进去的 key 和 value 都是 JDK 序列化之后的内容，其中 key 这种形式的，\xac\xed\x00\x05t\x00\rredisTemplate。而 StringRedisTemplate 存进去的都是 String，其中 value 则是 JSON 字符串

既然两者存储的数据无法通用，那么就只能让他们自己读取自己存的数据：

* 使用 RedisTemplate 读出的数据，由于是 Object 类型的，使用时可以先强制转换为 User 类型
* 使用 StringRedisTemplate 读取出的字符串，需要手动将 JSON 反序列化为 User 类型

```java
//使用RedisTemplate获取Value，无需反序列化就可以拿到实际对象，虽然方便，但是Redis中保存的Key和Value不易读
User userFromRedisTemplate = (User) redisTemplate.opsForValue().get("redisTemplate");

//使用StringRedisTemplate，虽然Key正常，但是Value存取需要手动序列化成字符串
User userFromStringRedisTemplate = objectMapper.readValue(stringRedisTemplate.opsForValue().get("stringRedisTemplate"), User.class);
```



使用 RedisTemplate 获取 Value 虽然方便，但是 Key 和 Value 不易读；而使用 StringRedisTemplate 虽然 Key 是普通字符串，但是 Value 存取需要手动序列化成字符串，有没有两全其美的方式呢。

当然有，自己定义 RedisTemplate 的 Key 和 Value 的序列化方式即可：Key 的序列化使用 RedisSerializer.string()（也就是 StringRedisSerializer 方式）实现字符串序列化，而 Value 的序列化使用 Jackson2JsonRedisSerializer

```java
@Bean
public <T> RedisTemplate<String, T> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate<String, T> redisTemplate = new RedisTemplate<>();
    redisTemplate.setConnectionFactory(redisConnectionFactory);
    Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
    redisTemplate.setKeySerializer(RedisSerializer.string());
    redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
    redisTemplate.setHashKeySerializer(RedisSerializer.string());
    redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
    redisTemplate.afterPropertiesSet();
    return redisTemplate;
}
```



这个时候我们再尝试一下使用 RedisTemplate 存取数据

```java
User user = new User("zhuye", 36);
userRedisTemplate.opsForValue().set(user.getName(), user);
Object userFromRedis = userRedisTemplate.opsForValue().get(user.getName());
log.info("userRedisTemplate get {} {}", userFromRedis, userFromRedis.getClass());
log.info("stringRedisTemplate get {}", stringRedisTemplate.opsForValue().get(user.getName()));
```

通过日志可以看到，二者都成功取出了数据，Redis 里也可以查到 Key 是纯字符串，Value 是 JSON 序列化后的 User 对象。但值得注意的是，第一行的日志输出显示，userRedisTemplate 获取到的 Value，是 LinkedHashMap 类型的，完全不是泛型的 RedisTemplate 设置的 User 类型

修复方式是，修改自定义 RestTemplate 的代码，把 new 出来的 Jackson2JsonRedisSerializer 设置一个自定义的 ObjectMapper，启用 activateDefaultTyping 方法把类型信息作为属性写入序列化后的数据中。或者，直接使用 RedisSerializer.json() 快捷方法，它内部使用的 GenericJackson2JsonRedisSerializer 直接设置了把类型作为属性保存到 Value 中

```java
// 方式一
...
Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
ObjectMapper objectMapper = new ObjectMapper();
//把类型信息作为属性写入Value
objectMapper.activateDefaultTyping(objectMapper.getPolymorphicTypeValidator(), ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
...

// 方式二
redisTemplate.setKeySerializer(RedisSerializer.string());
redisTemplate.setValueSerializer(RedisSerializer.json());
redisTemplate.setHashKeySerializer(RedisSerializer.string());
redisTemplate.setHashValueSerializer(RedisSerializer.json());
```



------

### 消除代码重复

#### 工厂模式和模板方法模式消除 if ... else 和重复代码



假设要开发一个购物车下单的功能，针对不同用户进行不同处理：

* 普通用户需要收取运费，运费是商品价格的 10%，无商品折扣
* VIP 用户同样需要收取商品价格 10% 的快递费，但购买两件以上相同商品时，第三件开始享受一定折扣
* 内部用户可以免运费，无商品折扣

我们的目标是实现三种类型的购物车业务逻辑，把入参 Map 对象（Key 是商品 ID，Value 是商品数量），转换为出参购物车类型 Cart

以下是三种购物车的简易代码

```java
//普通用户
//购物车
@Data
public class Cart {
    //商品清单
    private List<Item> items = new ArrayList<>();
    //总优惠
    private BigDecimal totalDiscount;
    //商品总价
    private BigDecimal totalItemPrice;
    //总运费
    private BigDecimal totalDeliveryPrice;
    //应付总价
    private BigDecimal payPrice;
}
//购物车中的商品
@Data
public class Item {
    //商品ID
    private long id;
    //商品数量
    private int quantity;
    //商品单价
    private BigDecimal price;
    //商品优惠
    private BigDecimal couponPrice;
    //商品运费
    private BigDecimal deliveryPrice;
}
//普通用户购物车处理
public class NormalUserCart {
    public Cart process(long userId, Map<Long, Integer> items) {
        Cart cart = new Cart();

        //把Map的购物车转换为Item列表
        List<Item> itemList = new ArrayList<>();
        items.entrySet().stream().forEach(entry -> {
            Item item = new Item();
            item.setId(entry.getKey());
            item.setPrice(Db.getItemPrice(entry.getKey()));
            item.setQuantity(entry.getValue());
            itemList.add(item);
        });
        cart.setItems(itemList);

        //处理运费和商品优惠
        itemList.stream().forEach(item -> {
            //运费为商品总价的10%
            item.setDeliveryPrice(item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity())).multiply(new BigDecimal("0.1")));
            //无优惠
            item.setCouponPrice(BigDecimal.ZERO);
        });

        //计算商品总价
        cart.setTotalItemPrice(cart.getItems().stream().map(item -> item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity()))).reduce(BigDecimal.ZERO, BigDecimal::add));
        //计算运费总价
        cart.setTotalDeliveryPrice(cart.getItems().stream().map(Item::getDeliveryPrice).reduce(BigDecimal.ZERO, BigDecimal::add));
        //计算总优惠
        cart.setTotalDiscount(cart.getItems().stream().map(Item::getCouponPrice).reduce(BigDecimal.ZERO, BigDecimal::add));
        //应付总价=商品总价+运费总价-总优惠
        cart.setPayPrice(cart.getTotalItemPrice().add(cart.getTotalDeliveryPrice()).subtract(cart.getTotalDiscount()));
        return cart;
    }
}
```

```java
//VIP用户
public class VipUserCart {


    public Cart process(long userId, Map<Long, Integer> items) {
        ...


        itemList.stream().forEach(item -> {
            //运费为商品总价的10%
            item.setDeliveryPrice(item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity())).multiply(new BigDecimal("0.1")));
            //购买两件以上相同商品，第三件开始享受一定折扣
            if (item.getQuantity() > 2) {
                item.setCouponPrice(item.getPrice()
                        .multiply(BigDecimal.valueOf(100 - Db.getUserCouponPercent(userId)).divide(new BigDecimal("100")))
                       .multiply(BigDecimal.valueOf(item.getQuantity() - 2)));
            } else {
                item.setCouponPrice(BigDecimal.ZERO);
            }
        });


        ...
        return cart;
    }
}
```

```java
//内部用户
public class InternalUserCart {


    public Cart process(long userId, Map<Long, Integer> items) {
        ...

        itemList.stream().forEach(item -> {
            //免运费
            item.setDeliveryPrice(BigDecimal.ZERO);
            //无优惠
            item.setCouponPrice(BigDecimal.ZERO);
        });

        ...
        return cart;
    }
}
```



对比一下代码量可以发现，三种购物车 70% 的代码是重复的。原因很简单，虽然不同类型用户计算运费和优惠的方式不同，但整个购物车的初始化、统计总价、总运费、总优惠和支付价格的逻辑都是一样的。而且当相同逻辑出现错误的时候，比如说计算总价的逻辑出现了错误，就需要我们同时修改三个地方的代码

有了三个购物车后，我们就需要根据不同的用户类型使用不同的购物车了。如下代码所示，使用三个 if 实现不同类型用户调用不同购物车的 process 方法

```java
@GetMapping("wrong")
public Cart wrong(@RequestParam("userId") int userId) {
    //根据用户ID获得用户类型
    String userCategory = Db.getUserCategory(userId);
    //普通用户处理逻辑
    if (userCategory.equals("Normal")) {
        NormalUserCart normalUserCart = new NormalUserCart();
        return normalUserCart.process(userId, items);
    }
    //VIP用户处理逻辑
    if (userCategory.equals("Vip")) {
        VipUserCart vipUserCart = new VipUserCart();
        return vipUserCart.process(userId, items);
    }
    //内部用户处理逻辑
    if (userCategory.equals("Internal")) {
        InternalUserCart internalUserCart = new InternalUserCart();
        return internalUserCart.process(userId, items);
    }

    return null;
}
```



相同的代码应该只在一处出现，我们可以把重复的逻辑定义在抽象类中，三个购物车只要分别实现不同的那份逻辑，这个模式就是模板方法模式。我们在父类中实现了购物车处理的流程模板，然后把需要特殊处理的地方留空白也就是留抽象方法定义，让子类去实现其中的逻辑。由于父类的逻辑不完整无法单独工作，因此需要定义为抽象类

```java
public abstract class AbstractCart {
    //处理购物车的大量重复逻辑在父类实现
    public Cart process(long userId, Map<Long, Integer> items) {

        Cart cart = new Cart();

        List<Item> itemList = new ArrayList<>();
        items.entrySet().stream().forEach(entry -> {
            Item item = new Item();
            item.setId(entry.getKey());
            item.setPrice(Db.getItemPrice(entry.getKey()));
            item.setQuantity(entry.getValue());
            itemList.add(item);
        });
        cart.setItems(itemList);
        //让子类处理每一个商品的优惠
        itemList.stream().forEach(item -> {
            processCouponPrice(userId, item);
            processDeliveryPrice(userId, item);
        });
        //计算商品总价
        cart.setTotalItemPrice(cart.getItems().stream().map(item -> item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity()))).reduce(BigDecimal.ZERO, BigDecimal::add));
        //计算总运费
cart.setTotalDeliveryPrice(cart.getItems().stream().map(Item::getDeliveryPrice).reduce(BigDecimal.ZERO, BigDecimal::add));
        //计算总折扣
cart.setTotalDiscount(cart.getItems().stream().map(Item::getCouponPrice).reduce(BigDecimal.ZERO, BigDecimal::add));
        //计算应付价格
cart.setPayPrice(cart.getTotalItemPrice().add(cart.getTotalDeliveryPrice()).subtract(cart.getTotalDiscount()));
        return cart;
    }

    //处理商品优惠的逻辑留给子类实现
    protected abstract void processCouponPrice(long userId, Item item);
    //处理配送费的逻辑留给子类实现
    protected abstract void processDeliveryPrice(long userId, Item item);
}
```

接下来只需完成不同子类的逻辑就可以了

```java
@Service(value = "NormalUserCart")
public class NormalUserCart extends AbstractCart {

    @Override
    protected void processCouponPrice(long userId, Item item) {
        item.setCouponPrice(BigDecimal.ZERO);
    }

    @Override
    protected void processDeliveryPrice(long userId, Item item) {
        item.setDeliveryPrice(item.getPrice()
                .multiply(BigDecimal.valueOf(item.getQuantity()))
                .multiply(new BigDecimal("0.1")));
    }
}
```

```java
@Service(value = "VipUserCart")
public class VipUserCart extends NormalUserCart {

    @Override
    protected void processCouponPrice(long userId, Item item) {
        if (item.getQuantity() > 2) {
            item.setCouponPrice(item.getPrice()
                    .multiply(BigDecimal.valueOf(100 - Db.getUserCouponPercent(userId)).divide(new BigDecimal("100")))
                    .multiply(BigDecimal.valueOf(item.getQuantity() - 2)));
        } else {
            item.setCouponPrice(BigDecimal.ZERO);
        }
    }
}
```

```java
@Service(value = "InternalUserCart")
public class InternalUserCart extends AbstractCart {
    @Override
    protected void processCouponPrice(long userId, Item item) {
        item.setCouponPrice(BigDecimal.ZERO);
    }

    @Override
    protected void processDeliveryPrice(long userId, Item item) {
        item.setDeliveryPrice(BigDecimal.ZERO);
    }
}
```





或许你已经注意到了，定义三个购物车子类时，我们在 @Service 注解中对 Bean 进行了命名。既然三个购物车都叫 XXXUserCart，那我们就可以把用户类型字符串拼接 UserCart 构成购物车 Bean 的名称，然后利用 Spring 的 IoC 容器，通过 Bean 的名称直接获取到 AbstractCart，调用其 process 方法即可实现通用

```java
@GetMapping("right")
public Cart right(@RequestParam("userId") int userId) {
    String userCategory = Db.getUserCategory(userId);
    AbstractCart cart = (AbstractCart) applicationContext.getBean(userCategory + "UserCart");
    return cart.process(userId, items);
}
```

试想， 之后如果有了新的用户类型、新的用户逻辑，是不是完全不用对代码做任何修改，只要新增一个 XXXUserCart 类继承 AbstractCart，实现特殊的优惠和运费处理逻辑就可以了

这样一来，我们就利用工厂模式 + 模板方法模式，不仅消除了重复代码，还避免了修改既有代码的风险。这就是设计模式中的开闭原则：对修改关闭，对扩展开放



------

#### 利用注解 + 反射消除重复代码



假设银行提供了一些 API 接口，对参数的序列化有点特殊，不使用 JSON，而是需要我们把参数依次拼在一起构成一个大字符串

按照银行提供的 API 文档的顺序，把所有参数构成定长的数据，然后拼接在一起作为整个字符串，因为每一种参数都有固定长度，未达到长度时需要做填充处理：

* 字符串类型的参数不满长度部分需要以下划线右填充，也就是字符串内容靠左
* 数字类型的参数不满长度部分以 0 左填充，也就是实际数字靠右
* 货币类型的表示需要把金额向下舍入 2 位到分，以分为单位，作为数字类型同样进行左填充

比如，创建用户方法和支付方法的定义是这样的：

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%20%E4%B8%9A%E5%8A%A1%E5%BC%80%E5%8F%91%E5%B8%B8%E8%A7%81%E9%94%99%E8%AF%AF/5429e0313c1254c56abf6bc6ff4fc8a6.webp)

代码很容易实现，直接根据接口定义实现填充操作、加签名、请求调用操作即可：

```java
public class BankService {

    //创建用户方法
    public static String createUser(String name, String identity, String mobile, int age) throws IOException {
        StringBuilder stringBuilder = new StringBuilder();
        //字符串靠左，多余的地方填充_
        stringBuilder.append(String.format("%-10s", name).replace(' ', '_'));
        //字符串靠左，多余的地方填充_
        stringBuilder.append(String.format("%-18s", identity).replace(' ', '_'));
        //数字靠右，多余的地方用0填充
        stringBuilder.append(String.format("%05d", age));
        //字符串靠左，多余的地方用_填充
        stringBuilder.append(String.format("%-11s", mobile).replace(' ', '_'));
        //最后加上MD5作为签名
        stringBuilder.append(DigestUtils.md2Hex(stringBuilder.toString()));
        return Request.Post("http://localhost:45678/reflection/bank/createUser")
                .bodyString(stringBuilder.toString(), ContentType.APPLICATION_JSON)
                .execute().returnContent().asString();
    }
}
```



可以看到，这段代码的重复粒度更细：三种标准数据类型的处理逻辑有重复，稍有不慎就会出现 Bug

* 处理流程中字符串拼接、加签和发请求的逻辑，在所有方法重复
* 实际方法的入参的参数类型和顺序，不一定和接口要求一致，容易出错
* 代码层面针对每一个参数硬编码，无法清晰地进行核对，如果参数达到几十个、上百个，出错的概率极大



使用注解和反射这两个武器，就可以针对银行请求的所有逻辑均使用一套代码实现，不会出现任何重复

要实现接口逻辑和逻辑实现的剥离，首先需要以 POJO 类（只有属性没有任何业务逻辑的数据类）的方式定义所有的接口参数。比如，下面这个创建用户 API 的参数：

```java
@Data
public class CreateUserAPI {
    private String name;
    private String identity;
    private String mobile;
    private int age;
}
```

有了接口参数定义，我们就能通过自定义注解为接口和所有参数增加一些元数据。如下所示，我们定义一个接口 API 的注解 BankAPI，包含接口 URL 地址和接口说明：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Inherited
public @interface BankAPI {
    String desc() default "";
    String url() default "";
}
```

然后，我们再定义一个自定义注解 @BankAPIField，用于描述接口的每一个字段规范，包含参数的次序、类型和长度三个属性：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@Documented
@Inherited
public @interface BankAPIField {
    int order() default -1;
    int length() default -1;
    String type() default "";
}
```





如下所示，我们定义了 CreateUserAPI 类描述创建用户接口的信息，通过为接口增加 @BankAPI 注解，来补充接口的 URL 和描述等元数据；通过为每一个字段增加 @BankAPIField 注解，来补充参数的顺序、类型和长度等元数据：

```java
@BankAPI(url = "/bank/createUser", desc = "创建用户接口")
@Data
public class CreateUserAPI extends AbstractAPI {
    @BankAPIField(order = 1, type = "S", length = 10)
    private String name;
    @BankAPIField(order = 2, type = "S", length = 18)
    private String identity;
    @BankAPIField(order = 4, type = "S", length = 11) //注意这里的order需要按照API表格中的顺序
    private String mobile;
    @BankAPIField(order = 3, type = "N", length = 5)
    private int age;
}
```

以上，我们通过注解实现了对 API 参数的描述。接下来，我们再看看反射如何配合注解实现动态的接口参数组装：

```java
private static String remoteCall(AbstractAPI api) throws IOException {
    //从BankAPI注解获取请求地址
    BankAPI bankAPI = api.getClass().getAnnotation(BankAPI.class);
    bankAPI.url();
    StringBuilder stringBuilder = new StringBuilder();
    Arrays.stream(api.getClass().getDeclaredFields()) //获得所有字段
            .filter(field -> field.isAnnotationPresent(BankAPIField.class)) //查找标记了注解的字段
            .sorted(Comparator.comparingInt(a -> a.getAnnotation(BankAPIField.class).order())) //根据注解中的order对字段排序
            .peek(field -> field.setAccessible(true)) //设置可以访问私有字段
            .forEach(field -> {
                //获得注解
                BankAPIField bankAPIField = field.getAnnotation(BankAPIField.class);
                Object value = "";
                try {
                    //反射获取字段值
                    value = field.get(api);
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
                //根据字段类型以正确的填充方式格式化字符串
                switch (bankAPIField.type()) {
                    case "S": {
                        stringBuilder.append(String.format("%-" + bankAPIField.length() + "s", value.toString()).replace(' ', '_'));
                        break;
                    }
                    case "N": {
                        stringBuilder.append(String.format("%" + bankAPIField.length() + "s", value.toString()).replace(' ', '0'));
                        break;
                    }
                    case "M": {
                        if (!(value instanceof BigDecimal))
                            throw new RuntimeException(String.format("{} 的 {} 必须是BigDecimal", api, field));
                        stringBuilder.append(String.format("%0" + bankAPIField.length() + "d", ((BigDecimal) value).setScale(2, RoundingMode.DOWN).multiply(new BigDecimal("100")).longValue()));
                        break;
                    }
                    default:
                        break;
                }
            });
    //签名逻辑
   stringBuilder.append(DigestUtils.md2Hex(stringBuilder.toString()));
    String param = stringBuilder.toString();
    long begin = System.currentTimeMillis();
    //发请求
    String result = Request.Post("http://localhost:45678/reflection" + bankAPI.url())
            .bodyString(param, ContentType.APPLICATION_JSON)
            .execute().returnContent().asString();
    log.info("调用银行API {} url:{} 参数:{} 耗时:{}ms", bankAPI.desc(), bankAPI.url(), param, System.currentTimeMillis() - begin);
    return result;
}
```



其实，许多涉及类结构性的通用处理，都可以按照这个模式来减少重复代码。反射给予了我们在不知晓类结构的时候，按照固定的逻辑处理类的成员；而注解给了我们为这些成员补充元数据的能力，使得我们利用反射实现通用逻辑的时候，可以从外部获得更多我们关心的数据



------

### 接口设计

#### 接口的响应要明确表示接口的处理结果



针对响应体的设计混乱、响应结果的不明确问题，服务端需要明确响应体每一个字段的意义，以一致的方式进行处理，并确保不透传下游服务的错误



------

#### 要考虑接口变迁的版本控制策略



接口不可能一成不变，需要根据业务需求不断增加内部逻辑。如果做大的功能调整或重构，涉及参数定义的变化或是参数废弃，导致接口无法向前兼容，这时接口就需要有版本的概念。在考虑接口版本策略设计时，我们需要注意的是，最好一开始就明确版本策略，并考虑在整个服务端统一版本策略

既然接口总是要变迁的，那么最好一开始就确定版本策略。比如，确定是通过 URL Path 实现，是通过 QueryString 实现，还是通过 HTTP 头实现。这三种实现方式的代码如下：

```java
//通过URL Path实现版本控制
@GetMapping("/v1/api/user")
public int right1(){
    return 1;
}
//通过QueryString中的version参数实现版本控制
@GetMapping(value = "/api/user", params = "version=2")
public int right2(@RequestParam("version") int version) {
    return 2;
}
//通过请求头中的X-API-VERSION参数实现版本控制
@GetMapping(value = "/api/user", headers = "X-API-VERSION=3")
public int right3(@RequestHeader("X-API-VERSION") int version) {
    return 3;
}
```

这三种方式中，URL Path 的方式最直观也最不容易出错；QueryString 不易携带，不太推荐作为公开 API 的版本策略；HTTP 头的方式比较没有侵入性，如果仅仅是部分接口需要进行版本控制，可以考虑这种方式



------

### 缓存设计

#### 不要把 Redis 当作数据库



通常，我们会使用 Redis 等分布式缓存数据库来缓存数据，但是千万别把 Redis 当做数据库来使用。我就见过许多案例，因为 Redis 中数据消失导致业务逻辑错误，并且因为没有保留原始数据，业务都无法恢复

Redis 的确具有数据持久化功能，可以实现服务重启后数据不丢失。这一点，很容易让我们误认为 Redis 可以作为高性能的 KV 数据库

其实，从本质上来看，Redis（免费版）是一个内存数据库，所有数据保存在内存中，并且直接从内存读写数据响应操作，只不过具有数据持久化能力。所以，Redis 的特点是，处理请求很快，但无法保存超过内存大小的数据

然后，从 Key 范围角度来说，allkeys 可以确保即使 Key 没有 TTL 也能回收，如果使用的时候客户端总是“忘记”设置缓存的过期时间，那么可以考虑使用这个系列的算法。而 volatile 会更稳妥一些，万一客户端把 Redis 当做了长效缓存使用，只是启动时候初始化一次缓存，那么一旦删除了此类没有 TTL 的数据，可能就会导致客户端出错

所以，不管是使用者还是管理者都要考虑 Redis 的使用方式，使用者需要考虑应该以缓存的姿势来使用 Redis，管理者应该为 Redis 设置内存限制和合适的驱逐策略，避免出现 OOM



------

#### 注意缓存数据同步策略



在实际情况下，修改了原始数据后，考虑到缓存数据更新的及时性，我们可能会采用主动更新缓存的策略。这些策略可能是：

* 先更新缓存，再更新数据库
* 先更新数据库，再更新缓存
* 先删除缓存，再更新数据库，访问的时候按需加载数据到缓存
* 先更新数据库，再删除缓存，访问的时候按需加载数据到缓存



“先更新缓存再更新数据库”策略不可行。数据库设计复杂，压力集中，数据库因为超时等原因更新操作失败的可能性较大，此外还会涉及事务，很可能因为数据库更新失败，导致缓存和数据库的数据不一致

“先更新数据库再更新缓存”策略不可行。一是，如果线程 A 和 B 先后完成数据库更新，但更新缓存时却是 B 和 A 的顺序，那很可能会把旧数据更新到缓存中引起数据不一致；二是，我们不确定缓存中的数据是否会被访问，不一定要把所有数据都更新到缓存中去

“先删除缓存再更新数据库，访问的时候按需加载数据到缓存”策略也不可行。在并发的情况下，很可能删除缓存后还没来得及更新数据库，就有另一个线程先读取了旧值到缓存中，如果并发量很大的话这个概率也会很大

“先更新数据库再删除缓存，访问的时候按需加载数据到缓存”策略是最好的。虽然在极端情况下，这种策略也可能出现数据不一致的问题，但概率非常低，基本可以忽略。举一个“极端情况”的例子，比如更新数据的时间节点恰好是缓存失效的瞬间，这时 A 先读取到了旧值，随后在 B 操作数据库完成更新并且删除了缓存之后，A 再把旧值加入缓存

需要注意的是，更新数据库后删除缓存的操作可能失败，如果失败则考虑把任务加入延迟队列进行延迟重试，确保数据可以删除，缓存可以及时更新。因为删除操作是幂等的，所以即使重复删问题也不是太大，这又是删除比更新好的一个原因

因此，针对缓存更新更推荐的方式是，缓存中的数据不由数据更新操作主动触发，统一在需要使用的时候按需加载，数据更新后及时删除缓存中的数据即可



------

### 异步处理

#### 消息队列



异步处理是互联网应用不可或缺的一种架构模式，大多数业务项目都是由同步处理、异步处理和定时任务处理三种模式相辅相成实现的。区别于同步处理，异步处理无需同步等待流程处理完毕，因此适用场景主要包括：

* 服务于主流程的分支流程。比如，在注册流程中，把数据写入数据库的操作是主流程，但注册后给用户发优惠券或欢迎短信的操作是分支流程，时效性不那么强，可以进行异步处理
* 用户不需要实时看到结果的流程。比如，下单后的配货、送货流程完全可以进行异步处理，每个阶段处理完成后，再给用户发推送或短信让用户知晓即可

同时，异步处理因为可以有 MQ 中间件的介入用于任务的缓冲的分发，所以相比于同步处理，在应对流量洪峰、实现模块解耦和消息广播方面有功能优势



在使用异步处理这种架构模式的时候，我们一般都会使用 MQ 中间件配合实现异步流程，需要重点考虑四个方面的问题：

1. 要考虑异步流程丢消息或处理中断的情况，异步流程需要有备线进行补偿。比如全量补偿方式，即便异步流程彻底失效，通过补偿也能让业务继续进行
2. 异步处理的时候需要考虑消息重复的可能性，处理逻辑需要实现幂等，防止重复处理
3. 微服务场景下不同服务多个实例监听消息的情况，一般不同服务需要同时收到相同的消息，而相同服务的多个实例只需要轮询接收消息。我们需要确认 MQ 的消息路由配置是否满足需求，以避免消息重复或漏发问题
4. 要注意始终无法处理的死信消息，可能会引发堵塞 MQ 的问题。一般在遇到消息处理失败的时候，我们可以设置一定的重试策略。如果重试还是不行，那可以把这个消息扔到专有的死信队列特别处理，不要让死信影响到正常消息的处理



------

### NoSQL 数据库

#### Redis VS MySQL



Redis 是一款设计简洁的缓存数据库，数据都保存在内存中，所以读写单一 Key 的性能非常高。但 Redis 薄弱的地方是，不擅长做 Key 的搜索。对 MySQL，我们可以使用 LIKE 操作前匹配走 B+ 树索引实现快速搜索；但对 Redis，我们使用 Keys 命令对 Key 的搜索，其实相当于在 MySQL 里做全表扫描



Redis 慢的原因有两个：

1. Redis 的 Keys 命令是 O(n) 时间复杂度。如果数据库中 Key 的数量很多，就会非常慢
2. Redis 是单线程的，对于慢的命令如果有并发，串行执行就会非常耗时



一般而言，我们使用 Redis 都是针对某一个 Key 来使用，而不能在业务代码中使用 Keys 命令从 Redis 中“搜索数据”，因为这不是 Redis 的擅长。对于 Key 的搜索，我们可以先通过关系型数据库进行，然后再从 Redis 存取数据（如果实在需要搜索 Key 可以使用 SCAN 命令）

此外值得一提的是，Redis 提供了丰富的数据结构（Set、SortedSet、Hash、List），并围绕这些数据结构提供了丰富的 API。如果我们好好利用这个特点的话，可以直接在 Redis 中完成一部分服务端计算，避免“读取缓存 -> 计算数据 -> 保存缓存”三部曲中的读取和保存缓存的开销，进一步提高性能



------

#### InfluxDB VS MySQL



InfluxDB 是一款优秀的时序数据库。时序数据库的优势，在于处理指标数据的聚合，并且读写效率非常高

在按照时间区间聚合使用 InfluxDB 时，我们看到了 InfluxDB 的性能优势。但，我们肯定不能把 InfluxDB 当作普通数据库，原因是：

1. InfluxDB 不支持数据更新操作，毕竟时间数据只能随着时间产生新数据，肯定无法对过去的数据做修改
2. 从数据结构上说，时间序列数据数据没有单一的主键标识，必须包含时间戳，数据只能和时间戳进行关联，不适合普通业务数据



此外需要注意，即便只是使用 InfluxDB 保存和时间相关的指标数据，我们也要注意不能滥用 tag

InfluxDB 提供的 tag 功能，可以为每一个指标设置多个标签，并且 tag 有索引，可以对 tag 进行条件搜索或分组。但是，tag 只能保存有限的、可枚举的标签，不能保存 URL 等信息，否则可能会出现 high series cardinality 问题，导致占用大量内存，甚至是 OOM。对于 InfluxDB，我们无法把 URL 这种原始数据保存到数据库中，只能把数据进行归类，形成有限的 tag 进行保存

总结一下，对于 MySQL 而言，针对大量的数据使用全表扫描的方式来聚合统计指标数据，性能非常差，一般只能作为临时方案来使用。此时，引入 InfluxDB 之类的时间序列数据库，就很有必要了。时间序列数据库可以作为特定场景（比如监控、统计）的主存储，也可以和关系型数据库搭配使用，作为一个辅助数据源，保存业务系统的指标数据



------

#### Elasticsearch VS MySQL



Elasticsearch（以下简称 ES），是目前非常流行的分布式搜索和分析数据库，独特的倒排索引结构尤其适合进行全文搜索。简单来讲，倒排索引可以认为是一个 Map，其 Key 是分词之后的关键字，Value 是文档 ID/ 片段 ID 的列表。我们只要输入需要搜索的单词，就可以直接在这个 Map 中得到所有包含这个单词的文档 ID/ 片段 ID 列表，然后再根据其中的文档 ID/ 片段 ID 查询出实际的文档内容



但 ES 这种以索引为核心的数据库，也不是万能的，频繁更新就是一个大问题

MySQL 可以做到仅更新某行数据的某个字段，但 ES 里每次数据字段更新都相当于整个文档索引重建。即便 ES 提供了文档部分更新的功能，但本质上只是节省了提交文档的网络流量，以及减少了更新冲突，其内部实现还是文档删除后重新构建索引。因此，如果要在 ES 中保存一个类似计数器的值，要实现不断更新，其执行效率会非常低

ES 是一个分布式的全文搜索数据库，所以与 MySQL 相比的优势在于文本搜索，而且因为其分布式的特性，可以使用一个大 ES 集群处理大规模数据的内容搜索。但，由于 ES 的索引是文档维度的，所以不适用于频繁更新的 OLTP 业务。一般而言，我们会把 ES 和 MySQL 结合使用，MySQL 直接承担业务系统的增删改操作，而 ES 作为辅助数据库，直接扁平化保存一份业务数据，用于复杂查询、全文搜索和统计



------

### 数据源头

#### 数据源头安全



1. 客户端的计算不可信。虽然目前很多项目的前端都是富前端，会做大量的逻辑计算，无需访问服务端接口就可以顺畅完成各种功能，但来自客户端的计算结果不能直接信任。最终在进行业务操作时，客户端只能扮演信息收集的角色，虽然可以将诸如价格等信息传给服务端，但只能用于校对比较，最终要以服务端的计算结果为准
2. 所有来自客户端的参数都需要校验判断合法性。即使我们知道用户是在一个下拉列表选择数据，即使我们知道用户通过网页正常操作不可能提交不合法的值，服务端也应该进行参数校验，防止非法用户绕过浏览器 UI 页面通过工具直接向服务端提交参数
3. 除了请求 Body 中的信息，请求头里的任何信息同样不能信任。我们要知道，来自请求头的 IP、Referer 和 Cookie 都有被篡改的可能性，相关数据只能用来参考和记录，不能用作重要业务逻辑
4. 如果接口面向外部用户，那么一定不能出现用户标识这样的参数，当前用户的标识一定来自服务端，只有经过身份认证后的用户才会在服务端留下标识。如果你的接口现在面向内部其他服务，那么也要千万小心这样的接口只能内部使用，还可能需要进一步考虑服务端调用方的授权问题



------

### 安全兜底

#### 开放平台资源的使用需要考虑防刷



代码本身涉及有偿使用的三方服务。如果因为代码本身缺少授权、用量控制而被利用导致大量调用，势必会消耗大量的钱，给公司造成损失。有些三方服务可能采用后付款方式的结算，出现问题后如果没及时发现，下个月结算时就会收到一笔数额巨大的账单

对于短信验证码这种开放接口，程序逻辑内需要有防刷逻辑。好的防刷逻辑是，对正常使用的用户毫无影响，只有疑似异常使用的用户才会感受到。对于短信验证码，有如下 4 种可行的方式来防刷：

1. 只有固定的请求头才能发送验证码

   对于那些使用爬虫来抓取短信接口地址的程序来说，往往只能抓取到 URL，而难以分析出请求发送短信还需要的额外请求头，可以看作第一道基本防御

2. 只有先到过注册页面才能发送验证码

   我们可以在页面或界面打开时请求固定的前置接口，为这个设备开启允许发送验证码的窗口，之后的请求发送验证码才是有效请求。这种方式可以防御直接绕开固定流程，通过接口直接调用的发送验证码请求，并不会干扰普通用户

3. 控制相同手机号的发送次数和发送频次

4. 增加前置图形验证



------

#### 虚拟资产并不能凭空产生无限使用



代码涉及虚拟资产的发放，比如积分、优惠券等。虽然说虚拟资产不直接对应货币，但一般可以在平台兑换具有真实价值的资产。比如，优惠券可以在下单时使用，积分可以兑换积分商城的商品。所以从某种意义上说，虚拟资产就是具有一定价值的钱，但因为不直接涉及钱和外部资金通道，所以容易产生随意性发放而导致漏洞

更合适的做法是，把优惠券看作一种资源，其生产不是凭空的，而是需要事先申请



------

#### 钱的进出一定要和订单挂钩并且实现幂等



代码涉及真实钱的进出。比如，对用户扣款，如果出现非正常的多次重复扣款，小则用户投诉、用户流失，大则被相关管理机构要求停业整改，影响业务。又比如，给用户发放返现的付款功能，如果出现漏洞造成重复付款，涉及 B 端的可能还好，但涉及 C 端用户的重复付款可能永远无法追回

涉及钱的进出，需要做好以下两点：

1. 任何资金操作都需要在平台侧生成业务属性的订单，可以是优惠券发放订单，可以是返现订单，也可以是借款订单，一定是先有订单再去做资金操作。同时，订单的产生需要有业务属性。业务属性是指，订单不是凭空产生的，否则就没有控制的意义。比如，返现发放订单必须关联到原先的商品订单产生；再比如，借款订单必须关联到同一个借款合同产生
2. 一定要做好防重，也就是实现幂等处理，并且幂等处理必须是全链路的



------

### 注入类问题

#### SQL 注入



客户端提供给服务端的查询值，是一个数据，会成为 SQL 查询的一部分。黑客通过修改这个值注入一些 SQL，来达到在服务端运行 SQL 的目的，相当于把查询条件的数据变为了查询代码。这种攻击方式，叫做 SQL 注入



虽然我们已经开始关注 SQL 注入的问题，但还是有一些认知上的误区，主要表现在以下三个方面：

1. 认为 SQL 注入问题只可能发生于 Http Get 请求，也就是通过 URL 传入的参数才可能产生注入点。这是很危险的想法。从注入的难易度上来说，修改 URL 上的 QueryString 和修改 Post 请求体中的数据，没有任何区别，因为黑客是通过工具来注入的，而不是通过修改浏览器上的 URL 来注入的。甚至 Cookie 都可以用来 SQL 注入，任何提供数据的地方都可能成为注入点
2. 认为不返回数据的接口，不可能存在注入问题
3. 认为 SQL 注入的影响范围，只是通过短路实现突破登录，只需要登录操作加强防范即可

真正的解决方式，还是使用参数化查询，让任何外部输入值只可能作为数据来处理



------

#### 代码注入



对于规则引擎，我们可能会用动态语言做一些计算，和 SQL 注入一样外部传入的数据只能当做数据使用，如果被黑客利用传入了代码，那么代码可能就会被动态执行。这种攻击方式，叫做代码注入

解决这个问题有两种方式：

1. 第一种方式和解决 SQL 注入一样，需要把外部传入的条件数据仅仅当做数据来对待。我们可以通过 SimpleBindings 来绑定参数初始化 name 变量，而不是直接拼接代码
2. 第二种解决方法是，使用 SecurityManager 配合 AccessControlContext，来构建一个脚本运行的沙箱环境。脚本能执行的所有操作权限，是通过 setPermissions 方法精细化设置的



------

#### XSS 攻击



对于用户注册、留言评论等功能，服务端会从客户端收集一些信息，本来用户名、邮箱这类信息是纯文本信息，但是黑客把信息替换为了 JavaScript 代码。那么，这些信息在页面呈现时，可能就相当于执行了 JavaScript 代码。甚至是，服务端可能把这样的代码，当作普通信息保存到了数据库。黑客通过构建 JavaScript 代码来实现修改页面呈现、盗取信息，甚至蠕虫攻击的方式，叫做 XSS（跨站脚本）攻击

XSS 问题的根源在于，原本是让用户传入或输入正常数据的地方，被黑客替换为了 JavaScript 脚本，页面没有经过转义直接显示了这个数据，然后脚本就被执行了。更严重的是，脚本没有经过转义就保存到了数据库中，随后页面加载数据的时候，数据中混入的脚本又当做代码执行了。黑客可以利用这个漏洞来盗取敏感数据，诱骗用户访问钓鱼网站等



解决方式主要有三种：

1. 对请求内容进行转义

   定义一个 servlet Filter，通过 HttpServletRequestWrapper 实现 servlet 层面的统一参数替换，来对 Get 请求进行内容转义。再自定义一个 Jackson 反列化器，来实现反序列化时的字符串的 HTML 转义，这样就实现了 Post 请求的内容转义

2. 修改之前的 SimpleModule 加入自定义序列化器，并且实现序列化时处理字符串转义，实现从数据库中读取内容时进行转义

3. 如果需要在 Cookie 中写入敏感信息的话，我们可以开启 HttpOnly 属性。这样 JavaScript 代码就无法读取 Cookie 了，即便页面被 XSS 注入了攻击代码，也无法获得我们的 Cookie



------

### 数据安全

#### 如何保存密码



众所周知，从安全角度来说，密码是不可以明文保存在数据库的，因此很多时候会对密码进行 MD5 摘要之后再进行保存

但 MD5 摘要保存并不意味着绝对的安全，黑客可以通过建立字典表或彩虹表，通过暴力破解的方式来反推出原始密码

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%20%E4%B8%9A%E5%8A%A1%E5%BC%80%E5%8F%91%E5%B8%B8%E8%A7%81%E9%94%99%E8%AF%AF/e1b3638dea64636494c3dcb0bb9b8ade.webp)



这时候我们可以通过多次摘要来提升密码的安全性，但这并不能解决根本问题，比如下图，即使加密了两次，仍旧被破解了出来

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%20%E4%B8%9A%E5%8A%A1%E5%BC%80%E5%8F%91%E5%B8%B8%E8%A7%81%E9%94%99%E8%AF%AF/ce87f65a3289e50d4e29754073b7eab1.webp)



为提升数据库中的密码的安全性，我们可以对原始密码加盐，然后再摘要保存，比如将原始密码 admin123 + salt，然后再进行保存。但是如果盐值太简单的话，仍旧可以被破解出来

黑客可以设置一个简单密码 123，通过暴力破解获知了数据库中的原始密码是 123salt，这便得到了盐值。如果盐值是全局统一的话，那么破解其它用户的密码也有了可能

因此，设置的盐值我们需要做到以下几点：

1. 盐值不能太简单，也不能太短，尽量长一点
2. 每个人的盐值都不同
3. 不要使用用户数据作为盐值，这样一旦破解一个用户的密码便获取了其它用户密码的规律



更好的做法是，不要使用像 MD5 这样快速的摘要算法，而是使用慢一点的算法。比如 Spring Security 已经废弃了 MessageDigestPasswordEncoder，推荐使用 BCryptPasswordEncoder，也就是BCrypt来进行密码哈希。BCrypt 是为保存密码设计的算法，相比 MD5 要慢很多

```java
@GetMapping("better")
public UserData better(@RequestParam(value = "name", defaultValue = "zhuye") String name, @RequestParam(value = "password", defaultValue = "Abcd1234") String password) {
    UserData userData = new UserData();
    userData.setId(1L);
    userData.setName(name);
    //保存哈希后的密码
    userData.setPassword(passwordEncoder.encode(password));
    userRepository.save(userData);
    //判断密码是否匹配
    log.info("match ? {}", passwordEncoder.matches(password, userData.getPassword()));
    return userData;
    // 生成的密码
    // "password": "$2a$10$wPWdQwfQO2lMxqSIb6iCROXv7lKnQq5XdMO96iCYCj7boK9pk6QPC"
    //格式为：$<ver>$<cost>$<salt><digest>
}
```



我们可以发现三点规律：

1. 我们调用 encode、matches 方法进行哈希、做密码比对的时候，不需要传入盐。BCrypt 把盐作为了算法的一部分，强制我们遵循安全保存密码的最佳实践
2. 生成的盐和哈希后的密码拼在了一起：$是字段分隔符，其中第一个$后的 2a 代表算法版本，第二个$后的 10 是代价因子（默认是 10，代表 2 的 10 次方次哈希），第三个$后的 22 个字符是盐，再后面是摘要。所以说，我们不需要使用单独的数据库字段来保存盐
3. 代价因子的值越大，BCrypt 哈希的耗时越久。因此，对于代价因子的值，更建议的实践是，根据用户的忍耐程度和硬件，设置一个尽可能大的值。制作 8 位密码长度的 MD5 彩虹表需要 5 个月，在代价因子设为 10 的时候，可能就需要几十年了



除了做好密码哈希保存的工作外，我们还要建设一套完善的安全防御机制，在感知到暴力破解危害的时候，开启短信验证、图形验证码、账号暂时锁定等防御机制来抵御暴力破解



------

#### 如何保存要素信息



我们把姓名和身份证号，称为二要素。因为我们有时候会需要用到这些数据，所以上文提到的单向散列算法就不适用了，因为数据无法解密。这时候我们便需要用到真正的加密算法了，包括对称加密和非对称加密

对称加密算法，是使用相同的密钥进行加密和解密。使用对称加密算法来加密双方的通信的话，双方需要先约定一个密钥，加密方才能加密，接收方才能解密。如果密钥在发送的时候被窃取，那么加密就是白忙一场。因此，这种加密方式的特点是，加密速度比较快，但是密钥传输分发有泄露风险

非对称加密算法，或者叫公钥密码算法。公钥密码是由一对密钥对构成的，使用公钥或者说加密密钥来加密，使用私钥或者说解密密钥来解密，公钥可以任意公开，私钥不能公开。使用非对称加密的话，通信双方可以仅分享公钥用于加密，加密后的数据没有私钥无法解密。因此，这种加密方式的特点是，加密速度比较慢，但是解决了密钥的配送分发安全问题

但是，对于保存敏感信息的场景来说，加密和解密都是我们的服务端程序，不太需要考虑密钥的分发安全性，也就是说使用非对称加密算法没有太大的意义



对称加密常用的加密算法，有 DES、3DES 和 AES。其中 DES 较老，安全性不够。3DES 解决了 DES 不够安全的问题，但是比 AES 慢，也不太推荐。AES 是当前公认的比较安全，兼顾性能的对称加密算法



------

### 定位应用问题

#### 在不同环境排查问题，有不同的方式



如果是在自己的开发环境排查问题，那你几乎可以使用任何自己熟悉的工具来排查，甚至可以进行单步调试。只要问题能重现，排查就不会太困难，最多就是把程序调试到 JDK 或三方类库内部进行分析

如果是在测试环境排查问题，相比开发环境少的是调试，不过你可以使用 JDK 自带的 jvisualvm 或阿里的Arthas，附加到远程的 JVM 进程排查问题。另外，测试环境允许造数据、造压力模拟我们需要的场景，因此遇到偶发问题时，我们可以尝试去造一些场景让问题更容易出现，方便测试

如果是在生产环境排查问题，往往比较难：一方面，生产环境权限管控严格，一般不允许调试工具从远程附加进程；另一方面，生产环境出现问题要求以恢复为先，难以留出充足的时间去慢慢排查问题。但，因为生产环境的流量真实、访问量大、网络权限管控严格、环境复杂，因此更容易出问题，也是出问题最多的环境



------

#### 生产问题的排查很大程度依赖监控



首先是日志

* 确保错误、异常信息可以被完整地记录到文件日志中
* 确保生产上程序的日志级别是 INFO 以上。记录日志要使用合理的日志优先级，DEBUG 用于开发调试、INFO 用于重要流程信息、WARN 用于需要关注的问题、ERROR 用于阻断流程的错误

其次，开发和运维团队做好充足的监控，而且是多个层次的监控

* 主机层面，对 CPU、内存、磁盘、网络等资源做监控。如果应用部署在虚拟机或 Kubernetes 集群中，那么除了对物理机做基础资源监控外，还要对虚拟机或 Pod 做同样的监控。监控层数取决于应用的部署方案，有一层 OS 就要做一层监控
* 网络层面，需要监控专线带宽、交换机基本情况、网络延迟
* 所有的中间件和存储都要做好监控，不仅仅是监控进程对 CPU、内存、磁盘 IO、网络使用的基本指标，更重要的是监控组件内部的一些重要指标。比如，著名的监控工具 Prometheus，就提供了大量的exporter来对接各种中间件和存储系统
* 应用层面，需要监控 JVM 进程的类加载、内存、GC、线程等常见指标（比如使用Micrometer来做应用监控），此外还要确保能够收集、保存应用日志、GC 日志

最后，做好应用快照。这里的“快照”是指，应用进程在某一时刻的快照。通常情况下，我们会为生产环境的 Java 应用设置 -XX:+HeapDumpOnOutOfMemoryError 和 -XX:HeapDumpPath=…这 2 个 JVM 参数，用于在出现 OOM 时保留堆快照
