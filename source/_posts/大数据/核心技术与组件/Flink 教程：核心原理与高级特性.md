---
title: Flink 教程：核心原理与高级特性
date: 2025-05-21 12:55:26
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/preview.jpg
tags:
  - 大数据
  - Hadoop
  - Flink
categories: 大数据
---



# Flink 中的时间和窗口

## 窗口的概念



Flink 是一种流式计算引擎，主要是来处理无界数据流的，数据源源不断、无穷无尽。想要更加方便高效地处理无界流，一种方式就是将无限数据切割成有限的“数据块”进行处理，这就是所谓的“窗口”（Window）

![image-20240903000619145](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240903000619145.png)

Flink 中窗口并不是静态准备好的，而是动态创建——当有落在这个窗口区间范围的数据达到时，才创建对应的窗口。另外，这里我们认为到达窗口结束时间时，窗口就触发计算并关闭，事实上“触发计算”和“窗口关闭”两个行为也可以分开



------

## 窗口的分类



**按照驱动类型分类**

![image-20240904001409455](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240904001409455.png)

<br>

**按照窗口分配数据的规则分类**

根据分配数据的规则，窗口的具体实现可以分为 4 类：滚动窗口（Tumbling Window）、滑动窗口（Sliding Window）、会话窗口（Session Window），以及全局窗口（Global Window）

* 滚动窗口

  ![image-20240904001514773](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240904001514773.png)

* 滑动窗口

  ![image-20240904001602327](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240904001602327.png)

* 会话窗口

  ![image-20240904001708388](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240904001708388.png)

* 全局窗口

  ![image-20240904001805101](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240904001805101.png)



------

## 窗口 API 概述



在定义窗口操作之前，首先需要确定，到底是基于按键分区（Keyed）的数据流 KeyedStream 来开窗，还是直接在没有按键分区的 DataStream 上开窗。也就是说，在调用窗口算子之前，是否有 keyBy 操作

经过按键分区 keyBy 操作后，数据流会按照 key 被分为多条逻辑流（logical streams），这就是 KeyedStream。基于 KeyedStream 进行窗口操作时，窗口计算会在多个并行子任务上同时执行。相同 key 的数据会被发送到同一个并行子任务，而窗口操作会基于每个 key 进行单独的处理。所以可以认为，每个 key 上都定义了一组窗口，各自独立地进行统计计算

在代码实现上，我们需要先对 DataStream 调用 .keyBy() 进行按键分区，然后再调用 .window() 定义窗口

```JAVA
stream.keyBy(...).window(...)
```

如果没有进行 keyBy，那么原始的 DataStream 就不会分成多条逻辑流。这时窗口逻辑只能在一个任务（task）上执行，就相当于并行度变成了 1

```JAVA
stream.windowAll(...)
```

对于非按键分区的窗口操作，手动调大窗口算子的并行度也是无效的，windowAll 本身就是一个非并行的操作

<br>

窗口操作主要有两个部分：窗口分配器（Window Assigners）和窗口函数（Window Functions）

```JAVA
stream.keyBy(<key selector>).window(<window assigner>).aggregate(<window function>)
```

其 中 .window() 方法需要传入一个窗口分配器 ，它指明了窗口的类型；而后面的 .aggregate() 方法传入一个窗口函数作为参数，它用来定义窗口具体的处理逻辑。窗口分配器有各种形式，而窗口函数的调用方法也不只 .aggregate() 一种



------

## 窗口分配器



定义窗口分配器（Window Assigners）是构建窗口算子的第一步，它的作用就是定义数据应该被“分配”到哪个窗口。所以可以说，窗口分配器其实就是在指定窗口的类型

窗口分配器最通用的定义方式，就是调用 .window() 方法。这个方法需要传入一个 WindowAssigner 作为参数，返回 WindowedStream。如果是非按键分区窗口，那么直接调用 .windowAll() 方法，同样传入一个 WindowAssigner，返回的是 AllWindowedStream

窗口按照驱动类型可以分成时间窗口和计数窗口，而按照具体的分配规则，又有滚动窗口、滑动窗口、会话窗口、全局窗口四种。除去需要自定义的全局窗口外，其他常用的类型 Flink 中都给出了内置的分配器实现，方便实现各种需求

<br>

**时间窗口**

时间窗口是最常用的窗口类型，又可以细分为滚动、滑动和会话三种

* 滚动处理时间窗口

  ```JAVA
  stream.keyBy(...)
  // of()方法需要传入一个 Time 类型的参数 size，表示滚动窗口的大小
  // of()还有一个重载方法，可以传入两个 Time 类型的参数：size 和 offset。第一个参数当然还是窗口大小，第二个参数则表示窗口起始点的偏移量
  .window(TumblingProcessingTimeWindows.of(Time.seconds(5)))
  .aggregate(...)
  ```

* 滑动处理时间窗口

  ```JAVA
  stream.keyBy(...)
  // of()方法需要传入两个 Time 类型的参数：size 和 slide，前者表示滑动窗口的大小，后者表示滑动窗口的滑动步长
  // 滑动窗口同样可以追加第三个参数，用于指定窗口起始点的偏移量，用法与滚动窗口完全一致
  .window(SlidingProcessingTimeWindows.of(Time.seconds(10)， Time.seconds(5)))
  .aggregate(...)
  ```

* 处理时间会话窗口

  ```JAVA
  stream.keyBy(...)
  // withGap()方法需要传入一个 Time 类型的参数 size，表示会话的超时时间，也就是最小间隔 session gap
  .window(ProcessingTimeSessionWindows.withGap(Time.seconds(10)))
  .aggregate(...)
  ```

<br>

**计数窗口**

计数窗口概念非常简单，本身底层是基于全局窗口（Global Window）实现的。Flink 提供了非常方便的接口：直接调用 .countWindow() 方法。根据分配规则的不同，又可以分为滚动计数窗口和滑动计数窗口两类

* 滚动计数窗口

  ```JAVA
  stream.keyBy(...)
  // 滚动计数窗口只需要传入一个长整型的参数 size，表示窗口的大小
  .countWindow(10)
  ```

* 滑动计数窗口

  ```JAVA
  stream.keyBy(...)
  // .countWindow()调用时传入两个参数：size 和 slide，前者表示窗口大小，后者表示滑动步长
  .countWindow(10，3)
  ```

<br>

**全局窗口**

全局窗口是计数窗口的底层实现，一般在需要自定义窗口时使用。它的定义同样是直接调用 .window()，分配器由 GlobalWindows 类提供

```JAVA
stream.keyBy(...)
.window(GlobalWindows.create());
```

需要注意使用全局窗口，必须自行定义触发器才能实现窗口计算，否则起不到任何作用



------

## 窗口函数



![image-20240908001331149](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240908001331149.png)

窗口函数定义了要对窗口中收集的数据做的计算操作，根据处理的方式可以分为两类：增量聚合函数和全窗口函数

<br>

**增量聚合函数（ReduceFunction / AggregateFunction）**

典型的增量聚合函数有两个：ReduceFunction 和 AggregateFunction

```JAVA
// ReduceFunction
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    env.socketTextStream("hadoop1", 7777)
        .map(new WaterSensorMapFunction())
        .keyBy(r - > r.getId())
        // 设置滚动事件时间窗口
        .window(TumblingProcessingTimeWindows.of(Time.secon ds(10)))
        .reduce(new ReduceFunction < WaterSensor > () {
            @Override
            public WaterSensor reduce(WaterSensor value1, WaterSensor value2) throws Exception {
                System.out.println("调用 reduce 方法，之前的结果: "+value1 + ", 现在来的数据: "+value2);
                return new WaterSensor(value1.getId(), System.currentTimeMillis(), value1.getVc() + value2.getVc());
            }
        })
        .print(); env.execute();
}
```

<br>

ReduceFunction 可以解决大多数归约聚合的问题，但是这个接口有一个限制，就是聚合状态的类型、输出结果的类型都必须和输入数据类型一样

AggregateFunction 可以看作是 ReduceFunction 的通用版本，这里有三种类型：输入类型（IN）、累加器类型（ACC）和输出类型（OUT）。输入类型 IN 就是输入流中元素的数据类型；累加器类型 ACC 则是我们进行聚合的中间状态类型；而输出类型当然就是最终计算结果的类型了

接口中有四个方法：

* createAccumulator()：创建一个累加器，这就是为聚合创建了一个初始状态，每个聚合任务只会调用一次
* add()：将输入的元素添加到累加器中
* getResult()：从累加器中提取聚合的输出结果
* merge()：合并两个累加器，并将合并后的状态作为一个累加器返回

AggregateFunction 的工作原理是：首先调用 createAccumulator() 为任务初始化一个状态（累加器）；而后每来一个数据就调用一次 add() 方法，对数据进行聚合，得到的结果保存在状态中；等到了窗口需要输出时，再调用 getResult() 方法得到计算结果

与 ReduceFunction 相同，AggregateFunction 也是增量式的聚合；而由于输入、中间状态、输出的类型可以不同，使得应用更加灵活方便

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<WaterSensor> sensorDS = env.socketTextStream("hadoop1", 7777)
        .map(new WaterSensorMapFunction());
    KeyedStream<WaterSensor, String> sensorKS = sensorDS.keyBy(sensor -> sensor.getId());
    // 1. 窗口分配器
    WindowedStream<WaterSensor, String, TimeWindow> sensorWS =
        sensorKS.window(TumblingProcessingTimeWindows.of(Time.seconds(10)));
    SingleOutputStreamOperator<String> aggregate = 
        sensorWS.aggregate( new AggregateFunction<WaterSensor, Integer, String>() {
            @Override
            public Integer createAccumulator() {
                System.out.println("创建累加器");
                return 0;
            }
            @Override
            public Integer add(WaterSensor value, Integer accumulator) {
                System.out.println(" 调 用 add 方法,value="+value);
                return accumulator + value.getVc();
            }
            @Override
            public String getResult(Integer accumulator) {
                System.out.println("调用 getResult 方法");
                return accumulator.toString();
            }
            @Override
            public Integer merge(Integer a, Integer b) {
                System.out.println("调用 merge 方法");
                return null;
            }
        });
    aggregate.print();
    env.execute();
}
```

Flink 也为窗口的聚合提供了一系列预定义的简单聚合方法，可以直接基于 WindowedStream 调用。主要包括.sum()/max()/maxBy()/min()/minBy()，与 KeyedStream 的简单聚合非常相似。它们的底层，其实都是通过 AggregateFunction 来实现的

<br>

**全窗口函数（full window functions）**

有些场景下，我们要做的计算必须基于全部的数据才有效，这时做增量聚合就没什么意义了；另外，输出的结果有可能要包含上下文中的一些信息（比如窗口的起始时间），这是增量聚合函数做不到的

所以，我们还需要有更丰富的窗口计算方式。窗口操作中的另一大类就是全窗口函数。与增量聚合函数不同，全窗口函数需要先收集窗口中的数据，并在内部缓存起来，等到窗口要输出结果的时候再取出数据进行计算

在 Flink 中，全窗口函数也有两种：WindowFunction 和 ProcessWindowFunction，不过 WindowFunction 能提供的上下文信息较少，也没有更高级的功能。事实上，它的作用可以被 ProcessWindowFunction 全覆盖，所以之后可能会逐渐弃用

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<WaterSensor> sensorDS = env.socketTextStream("hadoop1", 7777)
        .map(new WaterSensorMapFunction());
    KeyedStream<WaterSensor, String> sensorKS = sensorDS.keyBy(sensor -> sensor.getId());
    // 1. 窗口分配器
    WindowedStream<WaterSensor, String, TimeWindow> sensorWS =
        sensorKS.window(TumblingProcessingTimeWindows.of(Time.seconds(10)));
    SingleOutputStreamOperator<String> process = sensorWS.process(
        new ProcessWindowFunction<WaterSensor, String, String, TimeWindow>() {
            @Override
            public void process(String s, Context context, Iterable<WaterSensor> elements, Collector<String> out) 
                throws Exception {
                long count = elements.spliterator().estimateSize();
                long windowStartTs = context.window().getStart();
                long windowEndTs = context.window().getEnd();
                String windowStart = DateFormatUtils.format(windowStartTs, "yyyy-MM-dd HH:mm:ss.SSS");
                String windowEnd = DateFormatUtils.format(windowEndTs, "yyyy-MM-dd HH:mm:ss.SSS");
                out.collect("key=" + s + "的窗口[" + windowStart + "," + windowEnd + ") 包 含 " + 
                            count + " 条 数 据 ===>" + elements.toString());
            }
        });
    process.print();
    env.execute();
}
```

<br>

**增量函数和全窗口函数的混合使用**

在实际应用中，我们往往希望兼具这两者的优点，把它们结合在一起使用。Flink 的 Window API 就给我们实现了这样的用法

在调用 WindowedStream 的 .reduce() 和 .aggregate() 方法时，只是简单地直接传入了一个 ReduceFunction 或 AggregateFunction 进行增量聚合。除此之外，其实还可以传入第二个参数：一个全窗口函数，可以是 WindowFunction 或者 ProcessWindowFunction

```JAVA
// ReduceFunction 与 WindowFunction 结合
public <R> SingleOutputStreamOperator<R> reduce(ReduceFunction<T> reduceFunction，WindowFunction<T，R，K，W> function)
    
// ReduceFunction 与 ProcessWindowFunction 结合
public <R> SingleOutputStreamOperator<R> reduce(ReduceFunction<T> reduceFunction，ProcessWindowFunction<T，R，
K，W> function)
    
// AggregateFunction 与 WindowFunction 结合
public <ACC，V，R> SingleOutputStreamOperator<R> aggregate(AggregateFunction<T，ACC，V> aggFunction，WindowFunction<V，R，K，W> windowFunction)
    
// AggregateFunction 与 ProcessWindowFunction 结合
public <ACC，V，R> SingleOutputStreamOperator<R> aggregate(AggregateFunction<T，ACC，V> aggFunction, ProcessWindowFunction<V，R，K，W> windowFunction)
```

这样调用的处理机制是：基于第一个参数（增量聚合函数）来处理窗口数据，每来一个数据就做一次聚合；等到窗口需要触发计算时，则调用第二个参数（全窗口函数）的处理逻辑输出结果。需要注意的是，这里的全窗口函数就不再缓存所有数据了，而是直接将增量聚合函数的结果拿来当作了 Iterable 类型的输入



------

## 其他 API



**触发器（Trigger）**

触发器主要是用来控制窗口什么时候触发计算。所谓的“触发计算”，本质上就是执行窗口函数，所以可以认为是计算得到结果并输出的过程

基于 WindowedStream 调用 .trigger() 方法，就可以传入一个自定义的窗口触发器（Trigger）

```JAVA
stream.keyBy(...)
.window(...)
.trigger(new MyTrigger())
```

<br>

**移除器（Evictor）**

移除器主要用来定义移除某些数据的逻辑。基于 WindowedStream 调用 .evictor() 方法，就可以传入一个自定义的移除器（Evictor）。Evictor 是一个接口，不同的窗口类型都有各自预实现的移除器

```JAVA
stream.keyBy(...)
.window(...)
.evictor(new MyEvictor())
```



------

# 时间语义



![image-20240908233646247](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240908233646247.png)



------

# 水位线

## 什么是水位线



在 Flink 中，用来衡量事件时间进展的标记，就被称作“水位线”（Watermark）

具体实现上，水位线可以看作一条特殊的数据记录，它是插入到数据流中的一个标记点，主要内容就是一个时间戳，用来指示当前的事件时间。而它插入流中的位置，就应该是在某个数据到来之后；这样就可以从这个数据中提取时间戳，作为当前水位线的时间戳了

![image-20240910233021462](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233021462.png)

![image-20240910233051214](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233051214.png)

![image-20240910233148545](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233148545.png)

![image-20240910233233246](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233233246.png)

![image-20240910233353675](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233353675.png)



------

## 水位线和窗口的工作原理



![image-20240910233728142](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233728142.png)

![image-20240910233738277](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233738277.png)

![image-20240910233749812](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240910233749812.png)

Flink 中窗口并不是静态准备好的，而是动态创建——当有落在这个窗口区间范围的数据达到时，才创建对应的窗口。另外，这里我们认为到达窗口结束时间时，窗口就触发计算并关闭，事实上“触发计算”和“窗口关闭”两个行为也可以分开



------

## 生成水位线



完美的水位线是“绝对正确”的，也就是一个水位线一旦出现，就表示这个时间之前的数据已经全部到齐、之后再也不会出现了。不过如果要保证绝对正确，就必须等足够长的时间，这会带来更高的延迟

如果我们希望处理得更快、实时性更强，那么可以将水位线延迟设得低一些。这种情况下，可能很多迟到数据会在水位线之后才到达，就会导致窗口遗漏数据，计算结果不准确。

当然，如果我们对准确性完全不考虑、一味地追求处理速度，可以直接使用处理时间语义，这在理论上可以得到最低的延迟

所以 Flink 中的水位线，其实是流处理中对低延迟和结果正确性的一个权衡机制，而且把控制的权力交给了程序员，我们可以在代码中定义水位线的生成策略

<br>

在 Flink 的 DataStream API 中 ， 有 一 个 单 独 用 于 生 成 水 位 线 的 方法：.assignTimestampsAndWatermarks()，它主要用来为流中的数据分配时间戳，并生成水位线来指示事件时间。具体使用如下：

```JAVA
DataStream<Event> stream = env.addSource(new ClickSource());
DataStream<Event> withTimestampsAndWatermarks = stream.assignTimestampsAndWatermarks(<watermark strategy>);
```

WatermarkStrategy 作为参数，这就是所谓的“水位线生成策略 ”。WatermarkStrategy 是一个接口，该接口中包含了一个“时间戳分配器” TimestampAssigner 和一个“水位线生成器” WatermarkGenerator

```JAVA
public interface WatermarkStrategy<T> extends TimestampAssignerSupplier<T>, WatermarkGeneratorSupplier<T>{
    // 负责从流中数据元素的某个字段中提取时间戳，并分配给元素。时间戳的分配是生成水位线的基础。
    @Override
    TimestampAssigner<T> createTimestampAssigner(TimestampAssignerSupplier.Context context);
    // 主要负责按照既定的方式，基于时间戳生成水位线
    @Override
    WatermarkGenerator<T> createWatermarkGenerator(WatermarkGeneratorSupplier.Context context);
}
```



------

## 内置水位线



**有序流中内置水位线设置**

对于有序流，主要特点就是时间戳单调增长，所以永远不会出现迟到数据的问题。这是周期性生成水位线的最简单的场景，直接调用  WatermarkStrategy.forMonotonousTimestamps() 方法就可以实现

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<WaterSensor> sensorDS = env.socketTextStream("hadoop102", 7777)
        .map(new WaterSensorMapFunction());
    
    // TODO 1.定义 Watermark 策略
    WatermarkStrategy<WaterSensor> watermarkStrategy = WatermarkStrategy
        // 1.1 指定 watermark 生成：升序的 watermark，没有等待时间
        .<WaterSensor>forMonotonousTimestamps()
        // 1.2 指定 时间戳分配器，从数据中提取
        .withTimestampAssigner(new SerializableTimestampAssigner<WaterSensor>() {
            @Override
            public long extractTimestamp(WaterSensor element, long recordTimestamp) {
                // 返回的时间戳，要 毫秒
                System.out.println(" 数 据 =" + element + ",recordTs=" + recordTimestamp);
                return element.getTs() * 1000L;
            }
        });
    
    // TODO 2. 指定 watermark 策略
    SingleOutputStreamOperator<WaterSensor> sensorDSwithWatermark = sensorDS.assignTimestampsAndWatermarks(watermarkStrategy);
    sensorDSwithWatermark.keyBy(sensor -> sensor.getId())
        // TODO 3.使用 事件时间语义 的窗口
        .window(TumblingEventTimeWindows.of(Time.seconds(10)))
        .process(
        new ProcessWindowFunction<WaterSensor, String, String, TimeWindow>() {
            @Override
            public void process(String s, Context context, Iterable<WaterSensor> elements, Collector<String> out) 
                throws Exception {
                long startTs = context.window().getStart();
                long endTs = context.window().getEnd();
                String windowStart = DateFormatUtils.format(startTs, "yyyy-MM-dd HH:mm:ss.SSS");
                String windowEnd = DateFormatUtils.format(endTs, "yyyy-MM-dd HH:mm:ss.SSS");
                long count = elements.spliterator().estimateSize();
                out.collect("key=" + s + " 的 窗 口 [" + windowStart + "," + windowEnd + ") 包 含 " + 
                            count + " 条 数 据 ===>" + elements.toString());
            }
        }
    )
        .print();
    env.execute();
}
```

<br>

**乱序流中内置水位线设置**

由于乱序流中需要等待迟到数据到齐，所以必须设置一个固定量的延迟时间。这时生成水位线的时间戳，就是当前数据流中最大的时间戳减去延迟的结果，相当于把表调慢，当前时钟会滞后于数据的最大时间戳。调用 WatermarkStrategy. forBoundedOutOfOrderness() 方法就可以实现。这个方法需要传入一个 maxOutOfOrderness 参数，表示“最大乱序程度”，它表示数据流中乱序数据时间戳的最大差值；如果我们能确定乱序程度，那么设置对应时间长度的延迟，就可以等到所有的乱序数据了

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<WaterSensor> sensorDS = env.socketTextStream("hadoop102", 7777)
        .map(new WaterSensorMapFunction());
    
    // TODO 1.定义 Watermark 策略
    WatermarkStrategy<WaterSensor> watermarkStrategy = WatermarkStrategy
        // 1.1 指定 watermark 生成：乱序的，等待 3s
        .<WaterSensor>forBoundedOutOfOrderness(Duration.ofSeconds(3))
        // 1.2 指定 时间戳分配器，从数据中提取
        .withTimestampAssigner((element, recordTimestamp) -> {
            // 返回的时间戳，要 毫秒
            System.out.println("数据=" + element + ",recordTs=" + recordTimestamp);
            return element.getTs() * 1000L;
        });

    // TODO 2. 指定 watermark 策略
	SingleOutputStreamOperator<WaterSensor> sensorDSwithWatermark = sensorDS.assignTimestampsAndWatermarks(watermarkStrategy);
    sensorDSwithWatermark.keyBy(sensor -> sensor.getId())
        // TODO 3.使用 事件时间语义 的窗口
        .window(TumblingEventTimeWindows.of(Time.seconds(10)))
        .process(new ProcessWindowFunction<WaterSensor, String, String, TimeWindow>() {
            @Override
            public void process(String s, Context context, Iterable<WaterSensor> elements, Collector<String> out) 
                throws Exception {
                long startTs = context.window().getStart();
                long endTs = context.window().getEnd();
                String windowStart = DateFormatUtils.format(startTs, "yyyy-MM-dd HH:mm:ss.SSS");
                String windowEnd = DateFormatUtils.format(endTs, "yyyy-MM-dd HH:mm:ss.SSS");
                long count = elements.spliterator().estimateSize();
                out.collect("key=" + s + " 的 窗 口 [" + windowStart + "," + windowEnd + ") 包 含 " + 
                            count + " 条 数 据 ===>" + elements.toString());
            }
        }
                )
        .print();
    env.execute();
}
```



------

## 自定义水位线生成器



**周期性水位线生成器（Periodic Generator）**

周期性生成器一般是通过 onEvent() 观察判断输入的事件，而在 onPeriodicEmit() 里发出水位线

下面是一段自定义周期性生成水位线的代码：

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.addSource(new ClickSource()).assignTimestampsAndWatermarks(new CustomWatermarkStrategy()).print();
    env.execute();
}

public static class CustomWatermarkStrategy implements WatermarkStrategy<Event> {
    @Override
    public TimestampAssigner<Event> createTimestampAssigner(TimestampAssignerSupplier.Context context) {
        return new SerializableTimestampAssigner<Event>() {
            @Override
            public long extractTimestamp(Event element ， long recordTimestamp) {
                return element.timestamp; // 告诉程序数据源里的时间戳是哪一个字段
            }
        };
    }

    @Override
    public WatermarkGenerator<Event> createWatermarkGenerator(WatermarkGeneratorSupplier.Context context) {
        return new CustomBoundedOutOfOrdernessGenerator();
    }
}

public static class CustomBoundedOutOfOrdernessGenerator implements WatermarkGenerator<Event> {
    private Long delayTime = 5000L; // 延迟时间
    private Long maxTs = -Long.MAX_VALUE + delayTime + 1L; // 观察到的最大时间戳

    @Override
    public void onEvent(Event event ， long eventTimestamp ， WatermarkOutput output) {// 每来一条数据就调用一次
        maxTs = Math.max(event.timestamp，maxTs); // 更新最大时间戳
    }
    
    @Override
    public void onPeriodicEmit(WatermarkOutput output) {
        // 发射水位线，默认 200ms 调用一次
        output.emitWatermark(new Watermark(maxTs - delayTime - 1L));
    }
}
```

如果想修改默认周期时间，可以通过下面方法修改。例如：修改为 400ms

```JAVA
env.getConfig().setAutoWatermarkInterval(400L);
```

<br>

**断点式水位线生成器（Punctuated Generator）**

断点式生成器会不停地检测 onEvent()中的事件，当发现带有水位线信息的事件时，就立即发出水位线。把发射水位线的逻辑写在 onEvent 方法当中即可

<br>

**在数据源中发送水位线**

也可以在自定义的数据源中抽取事件时间，然后发送水位线。这里要注意的是，在自定义数据源中发送了水位线以后，就不能再在程序中使用

 assignTimestampsAndWatermarks 方法来生成水位线了。在自定义数据源中生成水位线和在程序中使用 assignTimestampsAndWatermarks 方法生成水位线二者只能取其一。示例程序如下：

```JAVA
env.fromSource( kafkaSource, WatermarkStrategy.forBoundedOutOfOrderness(Duration.ofSeconds(3)), "kafkasource")
```



------

## 水位线的传递



![image-20240915001048999](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240915001048999.png)

在流处理中，上游任务处理完水位线、时钟改变之后，要把当前的水位线再次发出，广播给所有的下游子任务。而当一个任务接收到多个上游并行任务传递来的水位线时，应该以最小的那个作为当前任务的事件时钟

水位线在上下游任务之间的传递，非常巧妙地避免了分布式系统中没有统一时钟的问题，每个任务都以“处理完之前所有数据”为标准来确定自己的时钟

在多个上游并行任务中，如果有其中一个没有数据，由于当前 Task 是以最小的那个作为当前任务的事件时钟，就会导致当前 Task 的水位线无法推进，就可能导致窗口无法触发。这时候可以设置空闲等待



------

## 迟到数据的处理



**推迟水位线推进**

在水位线产生时，设置一个乱序容忍度，推迟系统时间的推进，保证窗口计算被延迟执行，为乱序的数据争取更多的时间进入窗口

```JAVA
WatermarkStrategy.forBoundedOutOfOrderness(Duration.ofSeconds(10));
```

<br>

**设置窗口延迟关闭**

Flink 的窗口，也允许迟到数据。当触发了窗口计算后，会先计算当前的结果，但是此时并不会关闭窗口。以后每来一条迟到数据，就触发一次这条数据所在窗口计算(增量计算)。直到wartermark 超过了窗口结束时间+推迟时间，此时窗口会真正关闭。但是，允许迟到只能运用在 event time 上

```JAVA
.window(TumblingEventTimeWindows.of(Time.seconds(5)))
.allowedLateness(Time.seconds(3))
```

<br>

**使用侧流接收迟到的数据**

```JAVA
.windowAll(TumblingEventTimeWindows.of(Time.seconds(5)))
.allowedLateness(Time.seconds(3))
.sideOutputLateData(lateWS)
```



------

## **基于时间的合流 -- 双流联结（Join）**



可以发现，根据某个 key 合并两条流，与关系型数据库中表的 join 操作非常相近。事实上，Flink 中两条流的 connect 操作，就可以通过 keyBy 指定键进行分组后合并，实现了类似于 SQL 中的 join 操作；另外 connect 支持处理函数，可以使用自定义实现各种需求，其实已经能够处理双流 join 的大多数场景

不过处理函数是底层接口，所以尽管 connect 能做的事情多，但在一些具体应用场景下还是显得太过抽象了。比如，如果我们希望统计固定时间内两条流数据的匹配情况，那就需要自定义来实现——其实这完全可以用窗口（window）来表示。为了更方便地实现基于时间的合流操作，Flink 的 DataStrema API 提供了内置的 join 算子

<br>

**窗口联结（Window Join）**

Flink 为基于一段时间的双流合并专门提供了一个窗口联结算子，可以定义时间窗口，并将两条流中共享一个公共键（key）的数据放在窗口中进行配对处理

窗口联结在代码中的实现，首先需要调用 DataStream 的 .join() 方法来合并两条流，得到一个 JoinedStreams；接着通过 .where() 和 .equalTo() 方法指定两条流中联结的 key；然后通过.window() 开窗口，并调用 .apply() 传入联结窗口函数进行处理计算。通用调用形式如下：

```JAVA
stream1.join(stream2)
    // 的参数是键选择器（KeySelector），用来指定第一条流中的 key
    .where(<KeySelector>)
    // 传入的 KeySelector 则指定了第二条流中的 key
    .equalTo(<KeySelector>)
    // 传入的就是窗口分配器，三种时间窗口都可以用在这里：滚动窗口、滑动窗口和会话窗口
    .window(<WindowAssigner>)
    // 传入的 JoinFunction 也是一个函数类接口，使用时需要实现内部的.join()方法
    .apply(<JoinFunction>)
```

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<Tuple2<String, Integer>> ds1 = env.fromElements(
        Tuple2.of("a", 1),
        Tuple2.of("a", 2),
        Tuple2.of("b", 3),
        Tuple2.of("c", 4))
        .assignTimestampsAndWatermarks(WatermarkStrategy.<Tuple2<String, Integer>>forMonotonousTimestamps()
                                       .withTimestampAssigner((value, ts) -> value.f1 * 1000L));
    SingleOutputStreamOperator<Tuple3<String, Integer,Integer>> ds2 = env.fromElements(
        Tuple3.of("a", 1,1),
        Tuple3.of("a", 11,1),Tuple3.of("b", 2,1),
        Tuple3.of("b", 12,1),
        Tuple3.of("c", 14,1),
        Tuple3.of("d", 15,1))
        .assignTimestampsAndWatermarks(WatermarkStrategy.<Tuple3<String, Integer,Integer>>forMonotonousTimestamps()
                                       .withTimestampAssigner((value, ts) -> value.f1 * 1000L));
    
    // 1. 落在同一个时间窗口范围内才能匹配
    // 2. 根据 keyby 的 key，来进行匹配关联
    // 3. 只能拿到匹配上的数据，类似有固定时间范围的 inner join
    DataStream<String> join = ds1.join(ds2)
        .where(r1 -> r1.f0) // ds1 的 keyby
        .equalTo(r2 -> r2.f0) // ds2 的 keyby
        .window(TumblingEventTimeWindows.of(Time.seconds(10)))
        .apply(new JoinFunction<Tuple2<String, Integer>,  Tuple3<String, Integer, Integer>, String>() {
            /**
			* 关联上的数据，调用 join 方法
			* @param first ds1 的数据
			* @param second ds2 的数据
			* @return
			* @throws Exception
			*/
            @Override
            public String join(Tuple2<String, Integer> first, Tuple3<String, Integer, Integer> second) throws Exception {
                return first + "<----->" + second;
            }});
    join.print();
    env.execute();
}
```

<br>



**间隔联结（Interval Join）**

在有些场景下，要处理的时间间隔可能并不是固定的。这时显然不应该用滚动窗口或滑动窗口来处理——因为匹配的两个数据有可能刚好“卡在”窗口边缘两侧，于是窗口内就都没有匹配了；会话窗口虽然时间不固定，但也明显不适合这个场景。基于时间的窗口联结已经无能为力了

为了应对这样的需求，Flink 提供了一种叫作“间隔联结”（interval join）的合流操作。顾名思义，间隔联结的思路就是针对一条流的每个数据，开辟出其时间戳前后的一段时间间隔，看这期间是否有来自另一条流的数据匹配

间隔联结具体的定义方式是，我们给定两个时间点，分别叫作间隔的“上界”（upperBound）和“下界”（lowerBound）；于是对于一条流（不妨叫作 A）中的任意一个数据元素 a，就可以开辟一段时间间隔：[a.timestamp + lowerBound, a.timestamp + upperBound], 即以 a 的时间戳为中心，下至下界点、上至上界点的一个闭区间：我们就把这段时间作为可以匹配另一条流数据的“窗口”范围。所以对于另一条流（不妨叫 B）中的数据元素 b，如果它的时间戳落在了这个区间范围内，a 和 b 就可以成功配对，进而进行计算输出结果。所以匹配的条件为：

```
a.timestamp + lowerBound <= b.timestamp <= a.timestamp + upperBound
```

需要注意，做间隔联结的两条流 A 和 B，也必须基于相同的 key；下界 lowerBound 应该小于等于上界 upperBound，两者都可正可负；间隔联结目前只支持事件时间语义

![image-20240918002439439](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240918002439439.png)

下方的流 A 去间隔联结上方的流 B，所以基于 A 的每个数据元素，都可以开辟一个间隔区间。我们这里设置下界为-2 毫秒，上界为 1 毫秒。于是对于时间戳为 2 的 A 中元素，它的可匹配区间就是[0, 3],流 B 中有时间戳为 0、1 的两个元素落在这个范围内，所以就可以得到匹配数据对（2, 0）和（2, 1）。同样地，A 中时间戳为 3 的元素，可匹配区间为[1, 4]，B 中只有时间戳为 1 的一个数据可以匹配，于是得到匹配数据对（3, 1）

可以看到，间隔联结同样是一种内连接（inner join）。与窗口联结不同的是，interval join 做匹配的时间段是基于流中数据的，所以并不确定；而且流 B 中的数据可以不只在一个区间内被匹配

通用调用形式如下:

```JAVA
stream1
    .keyBy(<KeySelector>)
    .intervalJoin(stream2.keyBy(<KeySelector>))
    // 指定间隔的上下界
    .between(Time.milliseconds(-2), Time.milliseconds(1))
    .process (new ProcessJoinFunction<Integer, Integer, String(){
        @Override
        // left 指的就是第一条流中的数据，right 则是第二条流中与它匹配的数据
        // 每当检测到一组匹配，就会调用这里的.processElement()方法，经处理转换之后输出结果
        public void processElement(Integer left, Integer right, Context ctx, Collector<String> out) {
            out.collect(left + "," + right);
        }
    });
```



------

# 处理函数



之前所介绍的流处理 API，无论是基本的转换、聚合，还是更为复杂的窗口操作，都是基于 DataStream 进行转换的，所以可以统称为 DataStream API

在 Flink 更底层，我们可以不定义任何具体的算子（比如 map，filter，或者 window），而只是提炼出一个统一的“处理”（process）操作——它是所有转换算子的一个概括性的表达，可以自定义处理逻辑，所以这一层接口就被叫作“处理函数”（process function）

![image-20240919222628653](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240919222628653.png)



------

## 处理函数的功能与使用



之前学习的转换算子，一般只是针对某种具体操作来定义的，能够拿到的信息比较有限。如果我们想要访问事件的时间戳，或者当前的水位线信息，都是完全做不到的。跟时间相关的操作，目前只会用窗口来处理。而在很多应用需求中，要求对时间有更精细的控制，需要能够获取水位线，甚至要“把控时间”、定义什么时候做什么事，这就不是基本的时间窗口能够实现的了

这时就需要使用底层的处理函数。处理函数提供了一个“定时服务”（TimerService），可以通过它访问流中的事件（event）、时间戳（timestamp）、水位线（watermark），甚至可以注册“定时事件”。而且处理函数继承了 AbstractRichFunction 抽象类，所以拥有富函数类的所有特性，同样可以访问状态（state）和其他运行时信息。此外，处理函数还可以直接将数据输出到侧输出流（side output）中。所以，处理函数是最为灵活的处理方法，可以实现各种自定义的业务逻辑

处理函数的使用与基本的转换操作类似，只需要直接基于 DataStream 调用 .process() 方法就可以了。方法需要传入一个 ProcessFunction 作为参数，用来定义处理逻辑

```JAVA
stream.process(new MyProcessFunction())
```

这里 ProcessFunction 不是接口，而是一个抽象类，继承了 AbstractRichFunction；MyProcessFunction 是它的一个具体实现。所以所有的处理函数，都是富函数（RichFunction），富函数可以调用的东西这里同样都可以调用



------

## ProcessFunction 解析



```JAVA
// I 表示 Input，也就是输入的数据类型；O 表示 Output，也就是处理完成之后输出的数据类型
public abstract class ProcessFunction<I, O> extends AbstractRichFunction {
    ...
       
    // 用于“处理元素”，定义了处理的核心逻辑。这个方法对于流中的每个元素都会调用一次
    // 参数包括三个：输入数据值 value，上下文 ctx，以及“收集器”（Collector）out
    // 方法没有返回值，处理之后的输出数据是通过收集器 out 来定义的
    // value:当前流中的输入元素，也就是正在处理的数据，类型与流中数据类型一致
    // ctx：表示当前运行的上下文，可以获取到当前的时间戳，并提供了用于查询时间和注册定时器的定时服务
    // out：用于返回输出数据。直接调用 out.collect()方法就可以向下游发出一个数据。这个方法可以多次调用，也可以不调用
    public abstract void processElement(I value, Context ctx, Collector<O> out) throws Exception;
    
    // 只有在注册好的定时器触发的时候才会调用，而定时器是通过“定时服务” TimerService 来注册的
    // 有.onTimer()方法做定时触发，用 ProcessFunction 也可以自定义数据按照时间分组 、 定时触发计算输出结果；其实就实现了窗口的功能
    public void onTimer(long timestamp, OnTimerContext ctx, Collector<O> out) throws Exception {}
    
    ...
}
```



------

## 处理函数的分类



DataStream 在调用一些转换方法之后，有可能生成新的流类型；例如调用 .keyBy() 之后得到 KeyedStream，进而再调用 .window() 之后得到 WindowedStream。对于不同类型的流，其实都可以直接调用 .process() 方法进行自定义处理，这时传入的参数就都叫作处理函数。当然，它们尽管本质相同，都是可以访问状态和时间信息的底层 API，可彼此之间也会有所差异

<br>

Flink 提供了 8 个不同的处理函数：

1. ProcessFunction

   最基本的处理函数，基于 DataStream 直接调用 .process() 时作为参数传入

2. KeyedProcessFunction

   对流按键分区后的处理函数，基于 KeyedStream 调用 .process() 时作为参数传入

3. ProcessWindowFunction

   开窗之后的处理函数，也是全窗口函数的代表。基于 WindowedStream 调用 .process() 时作为参数传入

4. ProcessAllWindowFunction

   同样是开窗之后的处理函数，基于 AllWindowedStream 调用 .process() 时作为参数传入

5. CoProcessFunction

   合并（connect）两条流之后的处理函数，基于 ConnectedStreams 调用 .process() 时作为参数传入

6. ProcessJoinFunction

   间隔连接（interval join）两条流之后的处理函数，基于 IntervalJoined 调用 .process() 时作为参数传入

7. BroadcastProcessFunction

   广播连接流处理函数，基于 BroadcastConnectedStream 调用 .process() 时作为参数传入

8. KeyedBroadcastProcessFunction

   按键分区的广播连接流处理函数，同样是基于 BroadcastConnectedStream 调用 .process() 时作为参数传入



------

## 定时器（Timer）和定时服务（TimerService）



在 .onTimer() 方法中可以实现定时处理的逻辑，而它能触发的前提，就是之前曾经注册过定时器、并且现在已经到了触发时间。注册定时器的功能，是通过上下文中提供的“定时服务”来实现的

定时服务与当前运行的环境有关。ProcessFunction 的上下文（Context）中提供了 .timerService() 方法，可以直接返回一个 TimerService 对象。TimerService 是 Flink 关于时间和定时器的基础服务接口，包含以下六个方法：

```java
// 获取当前的处理时间
long currentProcessingTime();

// 获取当前的水位线（事件时间）
long currentWatermark();

// 注册处理时间定时器，当处理时间超过 time 时触发
void registerProcessingTimeTimer(long time);

// 注册事件时间定时器，当水位线超过 time 时触发
void registerEventTimeTimer(long time);

// 删除触发时间为 time 的处理时间定时器
void deleteProcessingTimeTimer(long time);

// 删除触发时间为 time 的处理时间定时器
void deleteEventTimeTimer(long time);
```

六个方法可以分成两大类：基于处理时间和基于事件时间。而对应的操作主要有三个：获取当前时间，注册定时器，以及删除定时器。需要注意，尽管处理函数中都可以直接访问 TimerService，不过只有基于 KeyedStream 的处理函数，才能去调用注册和删除定时器的方法；未作按键分区的 DataStream 不支持定时器操作，只能获取当前时间

TimerService 会以键（key）和时间戳为标准，对定时器进行去重；也就是说对于每个 key 和时间戳，最多只有一个定时器，如果注册了多次，onTimer() 方法也将只被调用一次



------

# 状态管理

## 概述



![image-20240921230519919](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240921230519919.png)



------

## 状态的分类



**托管状态（Managed State）和原始状态（Raw State）**

Flink 的状态有两种：托管状态（Managed State）和原始状态（Raw State）。托管状态就是由 Flink 统一管理的，状态的存储访问、故障恢复和重组等一系列问题都由 Flink 实现，只要调接口就可以；而原始状态则是自定义的，相当于就是开辟了一块内存，需要自己管理，实现状态的序列化和故障恢复

通常采用 Flink 托管状态来实现需求

<br>

**算子状态（Operator State）和按键分区状态（Keyed State）**

在 Flink 中，一个算子任务会按照并行度分为多个并行子任务执行，而不同的子任务会占据不同的任务槽（task slot）。由于不同的 slot 在计算资源上是物理隔离的，所以 Flink 能管理的状态在并行任务间是无法共享的，每个状态只能针对当前子任务的实例有效

而很多有状态的操作（比如聚合、窗口）都是要先做 keyBy 进行按键分区的。按键分区之后，任务所进行的所有计算都应该只针对当前 key 有效，所以状态也应该按照 key 彼此隔离。在这种情况下，状态的访问方式又会有所不同

基于这样的想法，可以将托管状态分为两类：算子状态和按键分区状态

![image-20240921231813592](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240921231813592.png)

![image-20240921231833868](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20240921231833868.png)



------

## 按键分区状态



按键分区状态（Keyed State）顾名思义，是任务按照键（key）来访问和维护的状态。它的特点非常鲜明，就是以 key 为作用范围进行隔离

需要注意，使用 Keyed State 必须基于 KeyedStream。没有进行 keyBy 分区的 DataStream，即使转换算子实现了对应的富函数类，也不能通过运行时上下文访问 Keyed State

<br>

**值状态（Value State）**

顾名思义，状态中只保存一个“值”（value）。ValueState 本身是一个接口，源码中定义如下：

```JAVA
public interface ValueState<T> extends State {
    
    // 获取当前状态的值
    T value() throws IOException;
    
    // 对状态进行更新，传入的参数 value 就是要覆写的状态值
    void update(T value) throws IOException;
    
}
```

在具体使用时，为了让运行时上下文清楚到底是哪个状态，我们还需要创建一个“状态描述器”（StateDescriptor）来提供状态的基本信息。例如源码中，ValueState 的状态描述器构造方法如下：

```JAVA
public ValueStateDescriptor(String name, Class<T> typeClass) {
    
	super(name, typeClass, null);
    
}
```

<br>

案例需求：检测每种传感器的水位值，如果连续的两个水位值超过 10，就输出报警

```JAVA
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    env.setParallelism(1);
    SingleOutputStreamOperator<WaterSensor> sensorDS = env.socketTextStream("hadoop1", 7777)
        .map(new WaterSensorMapFunction())
        .assignTimestampsAndWatermarks(WatermarkStrategy.<WaterSensor>forBoundedOutOfOrderness(Duration.ofSeconds(3))
                                       .withTimestampAssigner((element, ts) -> element.getTs() * 1000L));
    
    sensorDS.keyBy(r -> r.getId()).process(
        new KeyedProcessFunction<String, WaterSensor, String>() {
            // TODO 1.定义状态
            ValueState<Integer> lastVcState;
            
            @Override
            public void open(Configuration parameters) throws Exception {
                super.open(parameters);
                // TODO 2.在 open 方法中，初始化状态
                // 状态描述器两个参数：第一个参数，起个名字，不重复；第二个参数，存储的类型
                lastVcState = getRuntimeContext()
                    .getState(new ValueStateDescriptor<Integer>("lastVcState", Types.INT));
            }
            
            @Override
            public void processElement(WaterSensor value, Context ctx, Collector<String> out) throws Exception {
                // lastVcState.value(); // 取出本组值状态的数据
                // lastVcState.update(); // 更新本组值状态 的数据
                // lastVcState.clear(); // 清除本组值状态的数据
                // 1. 取出上一条数据的水位值(Integer 默认值是 null，判断)
                int lastVc = lastVcState.value() == null ? 0 : lastVcState.value();
                // 2. 求差值的绝对值，判断是否超过 10
                Integer vc = value.getVc();
                if (Math.abs(vc - lastVc) > 10) {
                    out.collect("传感器=" + value.getId() + "==>当前水位值=" + vc 
                                + ",与上一条水位值=" + lastVc + ", 相差超过 10！！！！");
                }
                
                // 3. 更新状态里的水位值
                lastVcState.update(vc);
            }
        })
        .print();
    env.execute();
}
```

<br>

**列表状态（List State）**

将需要保存的数据，以列表（List）的形式组织起来。在 ListState 接口中同样有一个类型参数 T，表示列表中数据的类型。ListState 也提供了一系列的方法来操作状态，使用方式与一般的 List 非常相似

* Iterable get()：获取当前的列表状态，返回的是一个可迭代类型 Iterable
* update(List values)：传入一个列表 values，直接对状态进行覆盖
* add(T value)：在状态列表中添加一个元素 value
* addAll(List values)：向列表中添加多个元素，以列表 values 形式传入

<br>

**Map 状态（Map State）**

把一些键值对（key-value）作为状态整体保存起来，可以认为就是一组 key-value 映射的列表。对应的 MapState<UK, UV>接口中，就会有 UK、UV 两个泛型，分别表示保存的 key和 value 的类型。同样，MapState 提供了操作映射状态的方法，与 Map 的使用非常类似

* UV get(UK key)：传入一个 key 作为参数，查询对应的 value 值
* put(UK key, UV value)：传入一个键值对，更新 key 对应的 value 值
* putAll(Map<UK, UV> map)：将传入的映射 map 中所有的键值对，全部添加到映射状态中
* remove(UK key)：将指定 key 对应的键值对删除
* boolean contains(UK key)：判断是否存在指定的 key，返回一个 boolean 值
* Iterable<Map.Entry<UK, UV>> entries()：获取映射状态中所有的键值对
* Iterable keys()：获取映射状态中所有的键（key），返回一个可迭代 Iterable 类型
* Iterable values()：获取映射状态中所有的值（value），返回一个可迭代 Iterable 类型
* boolean isEmpty()：判断映射是否为空，返回一个 boolean 值

<br>

**归约状态（Reduce Stagte）**

类似于值状态（Value），不过需要对添加进来的所有数据进行归约，将归约聚合之后的值作为状态保存下来。ReducingState<T>这个接口调用的方法类似于 ListState，只不过它保存的只是一个聚合值，所以调用.add()方法时，不是在状态列表里添加元素，而是直接把新数据和之前的状态进行归约，并用得到的结果更新状态

<br>

**聚合状态（Aggregating State）**

与归约状态非常类似，聚合状态也是一个值，用来保存添加进来的所有数据的聚合结果。与 ReducingState 不同的是，它的聚合逻辑是由在描述器中传入一个更加一般化的聚合函数（AggregateFunction）来定义的；这也就是之前我们讲过的 AggregateFunction，里面通过一个累加器（Accumulator）来表示状态，所以聚合的状态类型可以跟添加进来的数据类型完全不同，使用更加灵活

<br>

**状态生存时间**

在实际应用中，很多状态会随着时间的推移逐渐增长，如果不加以限制，最终就会导致存储空间的耗尽。一个优化的思路是直接在代码中调用 .clear() 方法去清除状态，但是有时候逻辑要求不能直接清除。这时就需要配置一个状态的“生存时间”（time-to-live，TTL），当状态在内存中存在的时间超出这个值时，就将它清除

具体实现上，如果用一个进程不停地扫描所有状态看是否过期，显然会占用大量资源做无用功。状态的失效其实不需要立即删除，所以我们可以给状态附加一个属性，也就是状态的“失效时间”。状态创建的时候，设置 失效时间 = 当前时间 + TTL；之后如果有对状态的访问和修改，我们可以再对失效时间进行更新；当设置的清除条件被触发时（比如，状态被访问的时候，或者每隔一段时间扫描一次失效状态），就可以判断状态是否失效、从而进行清除了



------

## 状态后端



在 Flink 中，状态的存储、访问以及维护，都是由一个可插拔的组件决定的，这个组件就叫作状态后端（state backend）。状态后端主要负责管理本地状态的存储方式和位置



------

## 状态后端的分类



状态后端是一个“开箱即用”的组件，可以在不改变应用程序逻辑的情况下独立配置。Flink 中提供了两类不同的状态后端，一种是“哈希表状态后端”（HashMapStateBackend），另一种是“内嵌 RocksDB 状态后端”（EmbeddedRocksDBStateBackend）。如果没有特别配置，系统默认的状态后端是 HashMapStateBackend

<br>

哈希表状态后端（HashMapStateBackend）

HashMapStateBackend 是把状态存放在内存里。具体实现上，哈希表状态后端在内部会直接把状态当作对象（objects），保存在 Taskmanager 的 JVM 堆上。普通的状态，以及窗口中收集的数据和触发器，都会以键值对的形式存储起来，所以底层是一个哈希表（HashMap），这种状态后端也因此得名

<br>

内嵌 RocksDB 状态后端（EmbeddedRocksDBStateBackend）

RocksDB 是一种内嵌的 key-value 存储介质，可以把数据持久化到本地硬盘。配置 EmbeddedRocksDBStateBackend 后 ，会将处理的数据全部放入 RocksDB 数据库中 ，RocksDB 默认存储在 TaskManager 的本地数据目录里

RocksDB 的状态数据被存储为序列化的字节数组，读写操作需要序列化/反序列化，因此状态的访问性能要差一些。另外，因为做了序列化，key 的比较也会按照字节进行，而不是直接调用 .hashCode() 和 .equals() 方法

EmbeddedRocksDBStateBackend 始终执行的是异步快照，所以不会因为保存检查点而阻塞数据的处理；而且它还提供了增量式保存检查点的机制，这在很多情况下可以大大提升保存效率



------

## 如何选择正确的状态后端



HashMap 和 RocksDB 两种状态后端最大的区别，就在于本地状态存放在哪里

HashMapStateBackend 是内存计算，读写速度非常快；但是，状态的大小会受到集群可用内存的限制，如果应用的状态随着时间不停地增长，就会耗尽内存资源

而 RocksDB 是硬盘存储，所以可以根据可用的磁盘空间进行扩展，所以它非常适合于超级海量状态的存储。不过由于每个状态的读写都需要做序列化/反序列化，而且可能需要直接从磁盘读取数据，这就会导致性能的降低，平均读写性能要比 HashMapStateBackend 慢一个数量级



------

## 状态后端的配置



在不做配置的时候，应用程序使用的默认状态后端是由集群配置文件 flink-conf.yaml 中指定的，配置的键名称为 state.backend。这个默认配置对集群上运行的所有作业都有效，可以通过更改配置值来改变默认的状态后端。另外，还可以在代码中为当前作业单独配置状态后端，这个配置会覆盖掉集群配置文件的默认值

<br>

1. 全局配置

   在 flink-conf.yaml 中，可以使用 state.backend 来配置默认状态后端

   配置项的可能值为 hashmap，这样配置的就是 HashMapStateBackend；如果配置项的值是 rocksdb，这样配置的就是 EmbeddedRocksDBStateBackend

   ```yaml
   # 默认状态后端
   state.backend: hashmap
   # 存放检查点的文件路径
   state.checkpoints.dir: hdfs://hadoop102:8020/flink/checkpoints
   ```

2. 为每个作业（Per-job/Application）单独配置状态后端

   ```JAVA
   StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
   env.setStateBackend(new HashMapStateBackend());
   // 或
   env.setStateBackend(new EmbeddedRocksDBStateBackend());
   ```



------

# 容错机制

## 检查点 CheckPoint



![image-20241027224209854](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20241027224209854.png)



------

## 检查点的保存



“随时存档”确实恢复起来方便，可是需要我们不停地做存档操作。如果每处理一条数据就进行检查点的保存，当大量数据同时到来时，就会耗费很多资源来频繁做检查点，数据处理的速度就会受到影响。所以在 Flink 中，检查点的保存是周期性触发的，间隔时间可以进行设置

<br>

应该在所有任务（算子）都恰好处理完一个相同的输入数据的时候，将它们的状态保存下来，这样做可以实现一个数据被所有任务（算子）完整地处理完，状态得到了保存

如果出现故障，我们恢复到之前保存的状态，故障时正在处理的所有数据都需要重新处理；我们只需要让源（source）任务向数据源重新提交偏移量、请求重放数据就可以了。当然这需要源任务可以把偏移量作为算子状态保存下来，而且外部数据源能够重置偏移量；kafka 就是满足这些要求的一个最好的例子



------

## 从检查点恢复状态



![image-20241102230832404](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20241102230832404.png)



------

## 检查点分界线



借鉴水位线的设计，在数据流中插入一个特殊的数据结构，专门用来表示触发检查点保存的时间点。收到保存检查点的指令后，Source 任务可以在当前数据流中插入这个结构；之后的所有任务只要遇到它就开始对状态做持久化快照保存。由于数据流是保持顺序依次处理的，因此遇到这个标识就代表之前的数据都处理完了，可以保存一个检查点；而在它之后的数据，引起的状态改变就不会体现在这个检查点中，而需要保存到下一个检查点

这种特殊的数据形式，把一条流上的数据按照不同的检查点分隔开，所以就叫做检查点的分界线（Checkpoint Barrier）

![image-20241102231135368](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20241102231135368.png)



------

## 检查点的启用



检查点的作用是为了故障恢复，我们不能因为保存检查点占据了大量时间、导致数据处理性能明显降低。为了兼顾容错性和处理性能，可以在代码中对检查点进行各种配置

```JAVA
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
// 每隔 1 秒启动一次检查点保存
env.enableCheckpointing(1000);
```

检查点的间隔时间是对处理性能和故障恢复速度的一个权衡。如果希望对性能的影响更小，可以调大间隔时间；而如果希望故障重启后迅速赶上实时的数据处理，就需要将间隔时间设小一些



------

## 检查点的存储



检查点具体的持久化存储位置，取决于检查点存储的设置。默认情况下，检查点存储在  JobManager 的堆内存中。而对于大状态的持久化保存，Flink 也提供了在其他存储位置进行保存的接口

具体可以通过调用检查点配置的 .setCheckpointStorage() 来配置，需要传入一 个 CheckpointStorage 的实现类。Flink 主要提供了两种 CheckpointStorage：作业管理器的堆内存和文件系统

```JAVA
// 配置存储检查点到 JobManager 堆内存
env.getCheckpointConfig().setCheckpointStorage(newJobManagerCheckpointStorage());

// 配置存储检查点到文件系统
env.getCheckpointConfig().setCheckpointStorage(new FileSystemCheckpointStorage("hdfs://namenode:40010/flink/checkpoints"));
```

对于实际生产应用，一般会将 CheckpointStorage 配置为高可用的分布式文件系统（HDFS，S3 等）



------

## 保存点



除了检查点外，Flink 还提供了另一个非常独特的镜像保存功能——保存点（savepoint），从名称就可以看出，这也是一个存盘的备份，它的原理和算法与检查点完全相同，只是多了一些额外的元数据

<br>

保存点与检查点最大的区别，就是触发的时机。检查点是由 Flink 自动管理的，定期创建，发生故障之后自动读取进行恢复，这是一个自动存盘的功能；而保存点不会自动创建，必须由用户明确地手动触发保存操作，所以就是手动存盘

保存点可以当作一个强大的运维工具来使用。我们可以在需要的时候创建一个保存点，然后停止应用，做一些处理调整之后再从保存点重启。它适用的具体场景有：

* 版本管理和归档存储
* 更新 Flink 版本
* 更新应用程序
* 调整并行度
* 暂停应用程序



------

## 使用保存点



保存点的使用非常简单，我们可以使用命令行工具来创建保存点，也可以从保存点恢复作业



1. 创建保存点

   要在命令行中为运行的作业创建一个保存点镜像，只需要执行：

   ```SH
   bin/flink savepoint :jobId [:targetDirectory]
   ```

   jobId 需要填充要做镜像保存的作业 ID，目标路径 targetDirectory 可选，表示保存点存储的路径

   对于保存点的默认路径，可以通过配置文件 flink-conf.yaml 中的 state.savepoints.dir 项来设定：

   ```yml
   state.savepoints.dir: hdfs:///flink/savepoints
   ```

   对于单独的作业，我们也可以在程序代码中通过执行环境来设置：

   ```JAVA
   env.setDefaultSavepointDir("hdfs:///flink/savepoints");
   ```

   由于创建保存点一般都是希望更改环境之后重启，所以创建之后往往紧接着就是停掉作业的操作。除了对运行的作业创建保存点，也可以在停掉一个作业时直接创建保存点：

   ```SH
   bin/flink stop --savepointPath [:targetDirectory] :jobId
   ```

2. 从保存点重启应用

   已经知道，提交启动一个 Flink 作业，使用的命令是 flink run；现在要从保存点重启一个应用，其实本质是一样的：

   ```SH
   bin/flink run -s :savepointPath [:runArgs]
   ```

   只要增加一个-s 参数，指定保存点的路径就可以了，其它启动时的参数还是完全一样的



------

## 一致性的概念和级别



一致性其实就是结果的正确性，一般从数据丢失、数据重复来评估

流式计算本身就是一个一个来的，所以正常处理的过程中结果肯定是正确的；但在发生故障、需要恢复状态进行回滚时就需要更多的保障机制了。通过检查点的保存来保证状态恢复后结果的正确，所以主要讨论的就是状态的一致性

一般说来，状态一致性有三种级别：

* 最多一次（At-Most-Once）
* 至少一次（At-Least-Once）
* 精确一次（Exactly-Once）



------

## 端到端的状态一致性



已经知道检查点可以保证 Flink 内部状态的一致性，而且可以做到精确一次。那是不是说，只要开启了检查点，发生故障进行恢复，结果就不会有任何问题呢

没那么简单。在实际应用中，一般要保证从用户的角度看来，最终消费的数据是正确的。而用户或者外部应用不会直接从 Flink 内部的状态读取数据，往往需要我们将处理结果写入外部存储中。这就要求我们不仅要考虑 Flink 内部数据的处理转换，还涉及到从外部数据源读取，以及写入外部持久化系统，整个应用处理流程从头到尾都应该是正确的

所以完整的流处理应用，应该包括了数据源、流处理器和外部存储系统三个部分。这个完整应用的一致性，就叫做“端到端（end-to-end）的状态一致性”，它取决于三个组件中最弱的那一环。一般来说，能否达到 at-least-once 一致性级别，主要看数据源能够重放数据；而能否达到 exactly-once 级别，流处理器内部、数据源、外部存储都要有相应的保证机制



实际应用中，最难做到、也最希望做到的一致性语义，无疑就是端到端（end-to-end）的“精确一次”。我们知道，对于 Flink 内部来说，检查点机制可以保证故障恢复后数据不丢（在能够重放的前提下），并且只处理一次，所以已经可以做到 exactly-once 的一致性语义了。所以端到端一致性的关键点，就在于输入的数据源端和输出的外部存储端

![image-20241109112508636](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20241109112508636.png)



------

# Flink SQL



![image-20241109123158724](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20241109123158724.png)

Table API 和 SQL 是最上层的 API，在 Flink 中这两种 API 被集成在一起，SQL 执行的对象也是 Flink 中的表（Table），所以我们一般会认为它们是一体的。Flink 是批流统一的处理框架，无论是批处理（DataSet API）还是流处理（DataStream API），在上层应用中都可以直接使用 Table API 或者 SQL 来实现；这两种 API 对于一张表执行相同的查询操作，得到的结果是完全一样的

需要说明的是，Table API 和 SQL 最初并不完善，在 Flink 1.9 版本合并阿里巴巴内部版本 Blink 之后发生了非常大的改变，此后也一直处在快速开发和完善的过程中，直到 Flink1.12 版本才基本上做到了功能上的完善。而即使是在目前最新的 1.17 版本中，Table API 和SQL 也依然不算稳定，接口用法还在不停调整和更新。具体的 API 调用可以随时关注官网的更新变化

SQL API 是基于 SQL 标准的 Apache Calcite 框架实现的，可通过纯 SQL 来开发和运行一个 Flink 任务



------

## 关系型表/SQL 与流处理的对比



|                               | 关系型表/SQL               | 流处理                                       |
| ----------------------------- | -------------------------- | -------------------------------------------- |
| **处理的数据对象**            | 字段元组的有界集合         | 字段元组的无限序列                           |
| **查询（Query）对数据的访问** | 可以访问到完整的数据输入   | 无法访问到所有数据，必须“持续”等待流式输入   |
| **查询终止条件**              | 生成固定大小的结果集后终止 | 永不停止，根据持续收到的数据不断更新查询结果 |



------

## 动态表和持续查询



流处理面对的数据是连续不断的，这导致了流处理中的“表”跟熟悉的关系型数据库中的表完全不同；而基于表执行的查询操作，也就有了新的含义

<br>

动态表

当流中有新数据到来，初始的表中会插入一行；而基于这个表定义的 SQL 查询，就应该在之前的基础上更新结果。这样得到的表就会不断地动态变化，被称为“动态表”（Dynamic Tables）

动态表是 Flink 在 Table API 和 SQL 中的核心概念，它为流数据处理提供了表和 SQL 支持。熟悉的表一般用来做批处理，面向的是固定的数据集，可以认为是“静态表”；而动态表则完全不同，它里面的数据会随时间变化

<br>

持续查询

动态表可以像静态的批处理表一样进行查询操作。由于数据在不断变化，因此基于它定义的 SQL 查询也不可能执行一次就得到最终结果。这样一来，我们对动态表的查询也就永远不会停止，一直在随着新数据的到来而继续执行。这样的查询就被称作“持续查询”（Continuous Query）。对动态表定义的查询操作，都是持续查询；而持续查询的结果也会是一个动态表

由于每次数据到来都会触发查询操作，因此可以认为一次查询面对的数据集，就是当前输入动态表中收到的所有数据。这相当于是对输入动态表做了一个“快照”（snapshot），当作有限数据集进行批处理；流式数据的到来会触发连续不断的快照查询，像动画一样连贯起来，就构成了“持续查询”



![image-20241109235745524](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E7%BB%84%E4%BB%B6/Flink%20%E6%95%99%E7%A8%8B%EF%BC%9A%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86%E4%B8%8E%E9%AB%98%E7%BA%A7%E7%89%B9%E6%80%A7/image-20241109235745524.png)

持续查询的步骤如下：

1. 流（stream）被转换为动态表（dynamic table）
2. 对动态表进行持续查询（continuous query），生成新的动态表
3. 生成的动态表被转换成流

这样，只要 API 将流和动态表的转换封装起来，就可以直接在数据流上执行 SQL 查询，用处理表的方式来做流处理了



------

## 将流转换成动态表



如果把流看作一张表，那么流中每个数据的到来，都应该看作是对表的一次插入（Insert）操作，会在表的末尾添加一行数据。因为流是连续不断的，而且之前的输出结果无法改变、只能在后面追加；所以我们其实是通过一个只有插入操作（insert-only）的更新日志（changelog）流，来构建一个表

例如，当用户点击事件到来时，就对应着动态表中的一次插入（Insert）操作，每条数据就是表中的一行；随着插入更多的点击事件，得到的动态表将不断增长



------

## 将动态表转换成流



与关系型数据库中的表一样，动态表也可以通过插入（Insert）、更新（Update）和删除（Delete）操作，进行持续的更改。将动态表转换为流或将其写入外部系统时，就需要对这些更改操作进行编码，通过发送编码消息的方式告诉外部系统要执行的操作。在 Flink 中，Table API 和 SQL 支持三种编码方式：

* 仅追加（Append-only）流

  仅通过插入（Insert）更改来修改的动态表，可以直接转换为“仅追加”流。这个流中发出的数据，其实就是动态表中新增的每一行

* 撤回（Retract）流

  撤回流是包含两类消息的流，添加（add）消息和撤回（retract）消息

  具体的编码规则是：INSERT 插入操作编码为 add 消息；DELETE 删除操作编码为 retract 消息；而 UPDATE 更新操作则编码为被更改行的 retract 消息，和更新后行（新行）的 add 消息。这样，我们可以通过编码后的消息指明所有的增删改操作，一个动态表就可以转换为撤回流了

* 更新插入（Upsert）流

  更新插入流中只包含两种类型的消息：更新插入（upsert）消息和删除（delete）消息

  所谓的“upsert”其实是“update”和“insert”的合成词，所以对于更新插入流来说，INSERT 插入操作和 UPDATE 更新操作，统一被编码为 upsert 消息；而 DELETE 删除操作则被编码为 delete 消息

  需要注意的是，在代码里将动态表转换为 DataStream 时，只支持仅追加（append-only）和撤回（retract）流，我们调用 toChangelogStream()得到的其实就是撤回流。而连接到外部系统时，则可以支持不同的编码方法，这取决于外部系统本身的特性



------

## 时间属性



基于时间的操作（比如时间窗口），需要定义相关的时间语义和时间数据来源的信息。在 Table API 和 SQL 中，会给表单独提供一个逻辑上的时间字段，专门用来在表处理程序中指示时间

所以所谓的时间属性（time attributes），其实就是每个表模式结构（schema）的一部分。它可以在创建表的 DDL 里直接定义为一个字段，也可以在 DataStream 转换成表时定义。一旦定义了时间属性，它就可以作为一个普通字段引用，并且可以在基于时间的操作中使用

时间属性的数据类型必须为 TIMESTAMP，它的行为类似于常规时间戳，可以直接访问并且进行计算

按照时间语义的不同，可以把时间属性的定义分成事件时间（event time）和处理时间（processing time）两种情况

<br>

**事件时间**

事件时间属性可以在创建表 DDL 中定义，增加一个字段，通过 WATERMARK 语句来定义事件时间属性。具体定义方式如下：

```SQL
CREATE TABLE EventTable(
	user STRING,
	url STRING,
	ts TIMESTAMP(3),
	WATERMARK FOR ts AS ts - INTERVAL '5' SECOND
) WITH (
	...
);
```

这里把 ts 字段定义为事件时间属性，而且基于 ts 设置了 5 秒的水位线延迟

时间戳类型必须是 TIMESTAMP 或者 TIMESTAMP_LTZ 类型。但是时间戳一般都是秒或者是毫秒（BIGINT 类型），这种情况可以通过如下方式转换：

```
ts BIGINT,
time_ltz AS TO_TIMESTAMP_LTZ(ts, 3),
```

