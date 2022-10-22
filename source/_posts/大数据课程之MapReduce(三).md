---
title: 大数据课程之 MapReduce(三)
date: 2022-09-17 19:16:08
tags: 大数据
categories: 
  - 大数据
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BMapReduce%28%E4%B8%89%29/F100031816.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BMapReduce%28%E4%B8%89%29/F100031816.jpg

---

# 大数据课程之 MapReduce(三)

## 概述

### 定义



MapReduce 是一个分布式运算的编程框架，是用户开发“基于 Hadoop 的数据分析应用”的核心框架



MapReduce 的核心功能是将用户编写的业务逻辑代码和自带默认组件整合成一个完整的分布式运算程序，并发运行在一个 Hadoop 集群上



------

### 优缺点



优点

1. 易于编程，用户只关心业务逻辑
2. 良好的扩展性，可动态增加服务器
3. 高容错性，任何一台机器挂掉，可以将任务转移到其它节点
4. 适合海量数据计算

缺点

1. 不擅长实时计算
2. 不擅长流式计算
3. 不擅长 DAG 有向无环图计算



------

### 核心思想



![image-20220516232151563](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BMapReduce%28%E4%B8%89%29/image-20220516232151563.png)



分布式的运算程序往往需要分成至少 2 个阶段，第一个阶段的  MapTask 并发实例，完全并行运行，互不相干，第二个阶段的 ReduceTask 并发实例互不相干，但是他们的数据依赖于上一个阶段 的所有 MapTask 并发实例的输出

MapReduce 编程模型只能包含一个 Map 阶段和一个 Reduce 阶段，如果用户的业 务逻辑非常复杂，那就只能多个 MapReduce 程序，串行运行



------

### MapReduce 进程



一个完整的 MapReduce 程序在分布式运行时有三类实例进程：

1. MrAppMaster：负责整个程序的过程调度及状态协调
2. MapTask：负责 Map 阶段的整个数据处理流程
3. ReduceTask：负责 Reduce 阶段的整个数据处理流程



------

### 常用序列化类型



![preload](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BMapReduce%28%E4%B8%89%29/zo16gfucat.png)

除了 Text  类型的其他类型都是在Java类型的后面加了 Writable



------

### MapReduce 编程规范



用户编写的程序分成三个部分：Mapper、Reducer 和 Driver



* Mapper 阶段

  1. 用户自定义的 Mapper 要继承自己的父类 
  2. Mapper 的输入数据是 KV 对（K：偏移量，V：对应的内容）的形式(KV 的类型可自定义) 
  3. Mapper 中的业务逻辑写在map() 方法中
  4. Mapper 的输出数据是 KV 对的形式(KV 的类型可自定义)
  5. map() 方法( MapTask 进程）对每一个<K,V>调用一次

* Reducer 阶段

  1. 用户自定义的 Reducer 要继承自己的父类
  2. Reducer 的输入数据类型对应 Mapper 的输出数据类型，也是 KV
  3. Reducer 的业务逻辑写在 reduce() 方法中
  4. ReduceTask 进程对每一组相同 k 的 <k,v> 组调用一次 reduce() 方法

* Driver 阶段

  相当于 YARN 集群的客户端，用于提交我们整个程序到 YARN 集群，提交的是封装了 MapReduce 程序相关运行参数的 job 对象



------

### WordCount 案例详解



根据 MapReduce 官方案例，对文本文件中按单词首字母分组，计算出每个分组的数量，比如以下文本文件

Hello World

This

is

Map Reduce

按照 MapReduce 编码规范，我们需要写 3 个类分别实现 Mapper、Reducer 和 Driver 的方法



Mapper 实现

```java
/**
 * Mapper 类有 4 个泛型参数，分别是 map 阶段 2 个输入内容和 2 个输出内容
 * 输入内容
 * LongWritable : 每行文本的偏移量
 * Text : 每行文本内容
 * 输出内容
 * Text : 统计出来的单词结果
 * IntWriteable : 该单词出现的次数
 */
public class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {

    private static final IntWritable one = new IntWritable(1);
    private static final Text text = new Text();

    /**
     * 对文本结果进行 Map 统计，只需要入参，将统计的中间结果写入 context，供 Reduce 流程使用
     * 对于 Map 来说，每次读取到一行文本内容，就会调用一次 map 方法
     */
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        // 将 Text 转为 String，便于更多的 api 操作
        String[] words = value.toString().split(" ");
        for (String word : words) {
            text.set(word);
            // 将统计的结果记入 context，key 为单词内容，value 为出现的次数，即 1
            context.write(text, one);
        }
    }

}
```



Reducer 实现

```java
/**
 * 与 Mapper 相同，Reducer 也有 4 个泛型参数，分别书输入的 key value 和输出的 key value
 * 而输入的 key value 则是 map 阶段的输出结果
 */
public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {

    private IntWritable result = new IntWritable();

    /**
     * reduce 阶段会一次性读到所有 key 相同的输入结果，即多个键值对，读取到一次结果则调用一个 reduce 方法
     * 这些键值对的 key 相同，因此 reduce 方法中只有一个 key，而 value 是一个可迭代的结果
     */
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        // 统计当前 key 出现的次数
        int sum = 0;
        for (IntWritable value : values) {
            sum += value.get();
        }
        result.set(sum);
        // 将统计结果写入 context
        context.write(key, result);

    }

}
```



Driver 实现

```java
public class WordCountDriver {

    public static void main(String[] args) throws Exception {
        // 创建一个 job
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);
        // 设置 jar 包路径
        job.setJarByClass(WordCountDriver.class);
        // 设置 map 类和 reduce 类
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);
        // 设置输出的 key 和 value 的类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        // 设置读取文件的路径和输出结果的文件路径
        FileInputFormat.addInputPath(job, new Path("读取文件的路径"));
        FileOutputFormat.setOutputPath(job, new Path("输出文件的路径"));
        // 执行 job, 传入值表示是否监控并打印 job
        boolean result = job.waitForCompletion(true);
        // 退出系统
        System.exit(result ? 0 : 1);
    }

}
```



------

## 序列化

### 概述



序列化就是把内存中的对象，转换成字节序列（或其他数据传输协议）以便于存储到磁盘（持久化）和网络传输

反序列化就是将受到字节序列（或其他数据传输协议）或者是磁盘的持久化数据，转换成内存中的对象



------

### 自定义 bean 对象实现 Writeable 接口



在企业开发中往往常用的基本序列化类型不能满足所有需求，比如在 Hadoop 框架内部传递一个bean对象，那么该对象就需要实现序列化接口

具体实现 bean 对象序列化步骤如下 7 步

1. 必须实现 Writable 接口
2. 反序列化时，需要反射调用空参构造函数，所以必须有空参构造
3. 重写序列化方法
4. 重写反序列化方法
5. 注意反序列化的顺序和序列化的顺序完全一致
6. 要想把结果显示在文件中，需要重写 toString()，可用”\t”分开，方便后续用
7. 如果需要将自定义的 bean 放在 key 中传输，则还需要实现 Comparable 接口，因为 MapReduce 框中的 Shuffle 过程要求对 key 必须能排序



------

## 框架原理

### 切片与 MapTask 并行度决定机制



MapTask的并行度决定Map阶段的任务处理并发度，进而影响到整个 Job 的处理速度



数据块：Block 是 HDFS 物理上把数据分成一块一块

数据切片：数据切片只是在逻辑上对输入进行分片，并不会在磁盘上将其切分成片进行存储

如果切片大小与数据块大小不一致的话，就可能造成一个切片需要去不同的 Block 中读取数据或者一个 Block 被划分成多个数据切片，因此最合理的方式就是切片大小等于 Block 大小

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210602185331161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU5NzIwOA==,size_16,color_FFFFFF,t_70#pic_center)





------

### FileInputFormat 切片机制



切片机制：

1. 简单的按照文件的内容长度进行切片
2. 切片大小，默认等于 Block 大小
3. 切片时不考虑数据集整体，而是逐个针对每一个文件进行单独切片



源码中计算切片大小的相关参数

```java
// mapreduce.input.fileinputformat.split.minsize = 1 默认值为 1
// mapreduce.input.fileinputformat.split.maxsize = Long.MAXValue 默认值为 Long.MAXValue
Math.max(minSize, Math.min(maxSize, blockSize));
```

获取切片信息的相关 API

```java
// 获取切片信息的文件名称
String name = inputSplit.getPath().getName();
// 根据文件类型获取切片信息
FileSplit inputSplit = (FileSplit)context.getInputSplit();
```



------

### MapReduce 工作流程



![image-20220523000115325](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BMapReduce%28%E4%B8%89%29/image-20220523000115325.png)



1. 分片、格式化数据源

   输入 Map 阶段的数据源，必须经过分片和格式化操作

   分片操作：指的是将源文件划分为大小相等的小数据块( Hadoop 2.x 中默认 128MB )，也就是分片，Hadoop 会为每一个分片构建一个 Map 任务，并由该任务运行自定义的 map() 函数，从而处理分片里的每一条记录

   格式化操作：将划分好的分片格式化为键值对<key,value>形式的数据，其中， key 代表偏移量， value 代表每一行内容

2. 执行 MapTask

   每个 Map 任务都有一个内存缓冲区(缓冲区大小 100MB )，输入的分片数据经过 Map 任务处理后的中间结果会写入内存缓冲区中

   如果写人的数据达到内存缓冲的阈值( 80MB )，会启动一个线程将内存中的溢出数据写入磁盘，同时不影响 Map 中间结果继续写入缓冲区

   在溢写过程中， MapReduce 框架会对 key 进行排序，如果中间结果比较大，会形成多个溢写文件，最后的缓冲区数据也会全部溢写入磁盘形成一个溢写文件，如果是多个溢写文件，则最后合并所有的溢写文件为一个文件

3. 执行 Shuffle 过程

   MapReduce 工作过程中， Map 阶段处理的数据如何传递给 Reduce 阶段，这是 MapReduce 框架中关键的一个过程，这个过程叫作 Shuffle

   Shuffle 会将 MapTask 输出的处理结果数据分发给 ReduceTask ，并在分发的过程中，对数据按 key 进行分区和排序

4. 执行 ReduceTask

   输入 ReduceTask 的数据流是<key, {value list}>形式，用户可以自定义 reduce()方法进行逻辑处理，最终以<key, value>的形式输出

5. 写入文件

   MapReduce 框架会自动把 ReduceTask 生成的<key, value>传入 OutputFormat 的 write 方法，实现文件的写入操作



------

## Shuffle 机制

### 简介



 Mapreduce 确保每个 reducer 的输入都是按键排序的。系统执行排序的过程(即将 map 输出作为输入传给 reducer)称为 shuffle



------

### 详细流程



1. mapTask 收集我们 map() 方法输出的 kv 对，放在内存缓冲区 kvbuffer（环形缓冲区：内存中的一种首尾相连的数据结构，kvbuffer 包含数据区和索引区）中，在存数据的时候，会调用 partitioner 进行分区编号的计算，并存入元数据中
2. 当内存缓冲区的数据达到 100M*0.8 时，就会开始溢写到本地磁盘文件 file.out，可能会溢出多次，则会有多个文件，相应的缓冲区中的索引区数据溢出为磁盘索引文件 file.out.index
3. 在溢写前，会先根据分区编号排序，相同的分区的数据，排在一起，再根据 map 的 key 排序（快排）
4. 多个溢写文件会被合并成大的溢出文件（归并排序）
5. 在数据量大的时.候，可以对 maptask 结果启用压缩，将 mapreduce.map.ouput.compress 设为 true，并使用 mapreduce.map.output.compress.codec 设置使用的压缩算法，可以提高数据传输到 reduce 端的效率
6. reduceTask 根据自己的分区号，去各个 mapTask 机器上取相应的结果分区数据
7. reduceTask 会取到同一个分区的来自不同 mapTask 的结果文件，reduceTask 会将这些文件再进行合并（归并排序）
8. 合并成大文件后，shuffle 的过程也就结束了，后面进入 reduceTask 的逻辑运算过程（从文件中取出一个一个的键值对 group，调用用户自定义的 reduce(）方法）



------

### 默认分区



```java
public class HashPartitioner<K, V> extends Partitioner<K, V> {
	public int getPartition(K key, V value, int numReduceTask) {
		return (key.hashCode() & Integer.MAX_VALUE) % numReduceTask;
	}
} 
```

默认分区是根据 key 的 hashCode对reduceTasks 个数取模得到的。用户没法控制哪个 key 存储到哪个分区



------

### 自定义分区



如果想要根据自己的需求生成不同的分区文件，那么就需要自定义分区方法而不是使用默认的分区方法。比如想要将不同开头的手机号放进不同的分区文件中，通过如下步骤实现即可

1. 自定义类继承 Partitioner，重写 getPartition() 方法

   ```java
   public class ProvincePartitioner extends Partitioner<Text, FlowBean> {
       public int getPartition(K key, FlowBean value, int numReduceTask) {
           // 获取电话号码前三位
           String preNum = key.toString().substring(0, 3);
           int partition = 4;
           // 根据号码的不同放入不同的分区文件
           switch (preNum) {
               case "136":
                   partition = 0;
                   break;
               case "137":
                   partition = 1;
                   break;
               case "138":
                   partition = 2;
                   break;
               case "139":
                   partition = 3;
                   break;
               default:
                   break;
           }
           return partition;
       }
   }
   ```

2. 在 job 驱动中，设置自定义 partitioner

   ```java
   job.setPartitionrtClass(ProvincePartitioner.class);
   ```

3. 自定义 partition 后，要根据自定义 partitioner 的逻辑设置相应数量的 reduce task

   ```java
   job.setNumReduceTask(5);
   ```



需要注意的是：

1. 如果 reduceTask 的数量 > getPartition 的结果数，则会多产生几个空的输出文件 part-r-000xx
2. 如果1 < reduceTask 的数量 < getPartition 的结果数，则有一部分分区数据无处安放，会产生异常
3. 如果 reduceTask 的数量 = 1，则不管 mapTask 端输出多少个分区文件，最终结果都交给这一个 reduceTask，最终也就只会产生一个结果文件 part-r-00000



------

## 排序

### 概述



排序是 MapReduce 中非常重要的操作之一，MapTask 和 ReduceTask 都会对数据按照 key 进行排序。该操作是默认行为。任何 MapReduce 程序中数据均会被排序，而不看逻辑是否需要



在 MapReduce 的 shuffle 阶段共有三次排序，分别是：

1. Map 的溢写阶段：根据分区及 key 进行快速排序
2. Map 合并溢写文件阶段：将同一个分区的多个溢写文件经过多轮归并排序最终合并为一个文件
3. Reduce 输入阶段：将同一个分区，不同 map task 的数据文件进行归并排序



------

### 分类



* 部分排序

  MapReduce 根据输入记录的键对数据集排序，保证输出的每个文件内部有序

* 全排序

  最终输出结果只要一个文件，且文件内部有序。实现方式是只设置一个 ReduceTask。但该方法在处理大型文件时效率极低，因为一台机器处理所有文件，完全丧失了 MapReduce 所提供的并行架构

* 辅助排序(GroupingComparator分组)

  在 Reduce 端对 key 进行分组。应用于：在接收的 key 为 bean 对象时，想让一个或几个字段相同(全部字段比较不相同)的 key 进入到同一个 reduce 方法时，可以采用分组排序

* 二次排序

  在自定义排序过程中，如果 compareTo 的判断条件为两个即为二次排序



------

### 实现方式



 bean 对象实现 WritableComparable 接口重写 compareTo 方法，就可以实现自定义排序

```java
@Override
public int compareTo(FlowBean o) {
    // 倒序排序
	return this.sumFlow > o.sumFlow ? -1 : 1;
}
```



------

## Combiner

### 概述



Combiner 是一个本地化的 reduce 操作，它是 map 运算的后续操作，主要是在 map 计算出中间文件前做一个简单的合并重复 key 值的操作



主要作用有：

1. 网络带宽严重被占降低程序效率，提前在 map 上执行分组，减少传输给 reduce 的数据量
2. 单一节点承载过重降低程序性能，全在 reduce 上运行，导致负载过重



每一个 map 都可能会产生大量的本地输出，Combiner 的作用就是对 map 端的输出先做一次合并，以减少在 map 和 reduce 节点之间的数据传输量，以提高网络 IO 性能



------

### 注意事项



有很多人认为这个 combiner 和 map 输出的数据合并是一个过程，其实不然，map 输出的数据合并只会产生在有数据溢写的时候，即进行合并操作

与 mapper 和 reducer 不同的是，combiner 没有默认的实现，需要显式的设置在 conf 中才有作用。

并不是所有的 job 都适用 combiner，只有操作满足结合律的才可设置 combiner。combine 操作类似于：opt(opt(1, 2, 3), opt(4, 5, 6))。如果opt为求和、求最大值的话，可以使用，但是如果是求中值的话，不适用



------

### 自定义 combiner



Combiner 和 Reducer 一样，编写一个类，然后继承 Reducer，reduce 方法中写具体的 Combiner 逻辑，然后在 job 中设置 Combiner 组

执行后看到 map 的输出和  combine 的输入统计是一致的，而 combine 的输出与 reduce 的输入统计是一样的。由此可以看出规约操作成功，而且执行在 map 的最后，reduce 之前。

其实 Combiner 也可以看成就是 Reduce，俩者的代码完全一样



1. Combiner 代码，将相同 key 的 value 合并

   ```java
   public class WordCountCombiner extends Reducer<Text, IntWritable, Text, IntWritable> {
   
       private IntWritable result = new IntWritable();
   
       @Override
       protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
           // 统计当前 key 出现的次数
           int sum = 0;
           for (IntWritable value : values) {
               sum += value.get();
           }
           result.set(sum);
           // 将统计结果写入 context
           context.write(key, result);
   
       }
   }
   ```

2. 在 job 中设置 combiner

   ```java
   job.setCombinerClass(WordCountCombiner.class);
   ```




------

## Join

### Map Join 概述



如果在 Reduce 端处理过多的表，容易出现数据倾斜，通常我们会在 Map 端缓存起来，提前把处理业务逻辑，减少 Reduce 端数据的压力，减少数据倾斜。

Map Join 适用于一张表十分小、一张表很大的场景。

Mapper的setup（初始化）的时候缓存到集合中

map join 指的是在 MapReduce 的 map 阶段先加载一个文件缓存到内存当中，这个文件可能是从磁盘读取的或网络请求的都可以

map(key,value,context) 方法中读取的数据 key 和 value，这两个数据和先前缓存到内存中的数据一起做处理后再 context.write() 到 reduce 阶段

map join 相当于在 map 阶段写数据到 reduce 阶段前对数据做了处理



------

### Map Join 示例



比如有两个 txt 文件

```
// 1.txt
01 mac
02 huawei
03 xiaomi

// 2.txt
201801 01 1
201802 02 2
201803 03 3
201804 01 4
```

1.txt 第一个列代表订单id ，第二列代表商品名称

2.txt 第一列代表时间戳，第二列代表订单id，第三列代表数量

如果希望在 map 阶段输出

时间戳 订单id 订单数量 商品名称



Mapper

```java
/**
 * 将 1.txt 加载到内存文件中，然后数据在 map 端输出数据前转换
 */
public class CacheMapper extends Mapper<LongWritable, Text, Text, NullWritable> {

    Map<String, String> map = new HashMap<String, String>();

    /**
     * 将 1.txt 文件内容读取出来，放在 map 集合中
     */
    @Override
    protected void setup(Context context) throws IOException, InterruptedException {
        // 读取文件
        BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("/1.txt"), "UTF-8"));
        String line;
        // 拆分数据，放入 map 集合
        while (StringUtils.isNotBlank(line = reader.readLine())) {
            String[] args = line.split(" ");
            map.put(args[0], args[1]);
        }
    }

    /**
     * map 阶段，拆分数据，并将商品名称插入进去
     */
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        // 读取数据
        String line = value.toString();
        // 拆分数据
        String[] args = line.split(" ");
        // 根据商品 id 查询商品名称
        String id = args[0];
        String name = map.get(id);
        // 将商品名称加入进去
        line = line + " " + name;
        context.write(new Text(line), NullWritable.get());
    }
}
```



自定义 driver 驱动类

```java
public class CacheDriver {

    public static void main(String[] args) throws Exception {
        // 创建一个 job
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);
        // 设置 jar 包路径
        job.setJarByClass(CacheDriver.class);
        // 设置 map 类和 reduce 类
        job.setMapperClass(CacheMapper.class);
        // 设置输出的 key 和 value 的类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        // 设置读取文件的路径和输出结果的文件路径
        FileInputFormat.addInputPath(job, new Path("读取文件的路径"));
        FileOutputFormat.setOutputPath(job, new Path("输出文件的路径"));
        // 加载缓存商品数据
        job.addCacheFile(new URI("缓存文件路径"));
        // 设置 reduce task 数量，因为没有设置 reduce task
        job.setNumReduceTask(0);
        // 执行 job, 传入值表示是否监控并打印 job
        boolean result = job.waitForCompletion(true);
        // 退出系统
        System.exit(result ? 0 : 1);
    }

}
```



------

## ETL
### 什么是 ETL

ETL分别是“Extract”、“ Transform” 、“Load”三个单词的首字母缩写也就是“抽取”、“转换”、“装载”，但我们日常往往简称其为数据清洗


ETL包含了三方面：

* 抽取：将数据从各种原始的业务系统中读取出来，这是所有工作的前提
* 转换：按照预先设计好的规则将抽取得数据进行转换，使本来异构的数据格式能统一起来。
* 装载：将转换完的数据按计划增量或全部导入到数据仓库中

清理的过程往往只需要 Map 阶段而不需要 Reduce 阶段


-----
### ETL 示例

比如有个日志文件，我们想要过滤掉其中日志长度小于 11 的内容，将其余的内容输出到文件中


Mapper 实现

```java
/**
 * 清洗掉长度小于 11 的日志
 */
public class LogMapper extends Mapper<LongWritable, Text, Text, IntWritable> {

    private static final IntWritable one = new IntWritable(1);
    private static final Text text = new Text();

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        String line = value.toString();
        // ETL 清洗
        if (line.length() < 11 ) {
            return;
        } 
        context.write(value, NullWritable.get());
    }

}
```


Driver 实现

```java
public class LogDrivern {

    public static void main(String[] args) throws Exception {
        // 创建一个 job
        Configuration configuration = new Configuration();
        Job job = Job.getInstance(configuration);
        // 设置 jar 包路径
        job.setJarByClass(LogDriver.class);
        // 设置 map 类和 reduce 类
        job.setMapperClass(WordCountMapper.class);
        // 设置输出的 key 和 value 的类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(NullWritable.class);
        // 设置读取文件的路径和输出结果的文件路径
        FileInputFormat.addInputPath(job, new Path("读取文件的路径"));
        FileOutputFormat.setOutputPath(job, new Path("输出文件的路径"));
        // 执行 job, 传入值表示是否监控并打印 job
        boolean result = job.waitForCompletion(true);
        // 退出系统
        System.exit(result ? 0 : 1);
    }

}
```


-----
## MapReduce 数据压缩
### 概述



压缩的优缺点

优点：减少磁盘 IO 和存储空间

缺点：增加 CPU 开销



压缩的原则

1. 运算密集型 JOB 少用压缩
2. IO 密集型的 JOB 多用压缩



------

### MapReduce 支持的压缩编码



| 压缩格式 | MapReduce 自带 |  算法   | 文件扩展名 | 是否可切片 | 换成压缩格式后，原来的代码是否需要修改 |
| :------: | :------------: | :-----: | :--------: | :--------: | :------------------------------------: |
| DEFLATE  |       是       | DEFLATE |  .deflate  |     否     |                 不需要                 |
|   Gzip   |       是       | DEFLATE |    .gz     |     否     |                 不需要                 |
|  bzip2   |       是       |  bzip2  |    .bz2    |     是     |                 不需要                 |
|   LZ0    |       否       |   LZ0   |    .lzo    |     是     |     需要建索引，还需要指定输入格式     |
|  Snappy  |       是       | Snappy  |  .snappy   |     否     |                 不需要                 |



------

### 压缩性能比较



| 压缩算法 | 原始文件大小 | 压缩文件大小 | 压缩速度 | 解压速度 |
| :------: | :----------: | :----------: | :------: | :------: |
|   Gzip   |    8.3GB     |    1.8GB     | 17.5MB/s |  58MB/s  |
|  bzip2   |    8.3GB     |    1.1GB     | 2.4MB/s  | 9.5MB/s  |
|   LZ0    |    8.3GB     |    2.9GB     | 49.3MB/s | 74.6MB/s |



------

### 数据压缩位置的选择



数据压缩可以在 MapReduce 的任意阶段进行

![image-20220830000501189](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BMapReduce%28%E4%B8%89%29/image-20220830000501189.png)