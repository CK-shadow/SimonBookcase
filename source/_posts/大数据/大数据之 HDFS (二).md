---
title: 大数据之 HDFS (二)
date: 2022-05-22 00:35:53
tags: 
  - 大数据
  - HDFS
categories: 
  - 大数据
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220522003223149.png
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220522003223149.png
---



# 大数据之 HDFS (二)

## 概述

### 产生背景和定义



随着数据量不断增加，单台服务器无法存储所有的数据，那么就需要分配到多台服务器中，但不方便管理和维护，因此需要一种系统来管理多台机器上的文件。文件存储在不同的机器之上，但是对于使用者来说，与操作一台服务器并没有太大差别，只需通过一行命令就可以取到想要的数据。这就是分布式文件系统，HDFS 是分布式文件系统的一种



HDFS 适合一次写入，多次读出的场景，一个文件经过创建、写入和关闭之后就不会再修改



------

### 优缺点



* 优点
  1. 高容错性，数据自动保存多个副本，某一副本丢失后会自动恢复
  2. 适合处理大规模数据，能够处理 PB 级别规模的数据和百万规模以上的文件数量
  3. 可构建在廉价的机器上，通过多副本机制提高可靠性
* 缺点
  1. 不适合低延时访问
  2. 无法高效的对大量小文件进行存储，大量小文件不仅会占用 NameNode 的内存，而且小文件存储的寻址时间会超过读取时间，违背了 HDFS 的设计目标
  3. 不支持文件并发写入和随机修改，只支持数据追加



------

### 组成



![image-20220512233033291](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220512233033291.png)



1. NameNode
   * 即 Master，实际的管理者
   * 管理 HDFS 的名称空间
   * 配置副本策略
   * 管理数据块映射信息
2. DataNode
   * Slave，NameNode 下达命令，DataNode 执行相应的操作
   * 存储实际的数据块
   * 执行数据块的读写操作
3. Client
   * 文件切分，文件上传到 HDFS 时，Client 将文件切分成一个一个的 Block，然后进行上传
   * 与 NameNode 交互，获取文件位置
   * 与 DataNode 交互，读写数据
   * 提供一些命令来管理或访问 HDFS
4. Secondary NameNode
   * 并非 NameNode 的热备，在 NameNode 宕机时，不能马上替换 NameNode 并提供服务
   * 辅助 NameNode，分担其工作量，在紧急情况下，可辅助恢复 NameNode



------

### 文件块



HDFS 中的文件在物理上时分块存储（Block）的，块的大小可以通过配置参数（dfs blocksize）来设置，默认大小在 Hadoop 2.x/3.x 版本中是 128M，1.x 版本中是 64M



文件块既不能设置的太大，也不能设置的太小。设置的太大，文件读取时间会远超过寻址时间，设置的太小，不仅会大量占用 NameNode 内存，甚至会造成寻址时间超过读取时间，违背了 HDFS 设计原则。寻址时间约为传输时间的 1% 为最佳状态，因此块大小最好根据磁盘传输速率来设置



------

## HDFS 常用操作指令



* 创建目录

  hadoop dfs -mkdir /test

* 上传文件或目录

  hadoop dfs -put test /

* 查看目录

  hadoop dfs -ls /

* 创建一个空文件

  hadoop dfs -touchz /test

* 删除一个文件

  hadoop dfs -rm /test

* 删除一个目录

  hadoop dfs -rmr /test

* 重命名

  hadoop dfs -mv /test /test1

* 查看文件

  hadoop dfs -cat /test

* 将指定目录下的所有内容merge成一个文件，下载到本地

  hadoop dfs -getmerge /test test1

* 使用du文件和目录大小

  hadoop dfs -du /

* 将目录拷贝到本地

  hadoop dfs -copyToLocal  /test localdir



------

## HDFS API

### 基础示例



首先引入 HDFS 相关依赖

```xml
<dependency>
  <groupId>org.apache.hadoop</groupId>
  <artifactId>hadoop-client</artifactId>
  <version>3.2.3</version>
</dependency>
```

```java
public class HDFSTest {

    public static void main(String[] args) throws Exception {
        // 获取连接
        FileSystem fileSystem = getFileSystem();
        // 创建文件
        fileSystem.mkdirs(new Path("/simon/hdfs-test"));
        // 关闭连接
        close(fileSystem);
    }

    private static FileSystem getFileSystem() throws Exception {
        // 获取集群连接地址
        URI uri = new URI("HDFS集群地址");
        // 配置文件，设置集群数量为 2
        Configuration configuration = new Configuration();
        configuration.set("dfs.replication", "2");
        // 用户
        String user = "Simon";
        // 返回客户端对象
        return FileSystem.get(uri, configuration, user);
    }

    private static  void close(FileSystem fileSystem) throws Exception {
        fileSystem.close();
    }

}
```



------

### 其它常用操作



* 上传文件

  ```java
  // 上传文件
  fileSystem.copyFromLocalFile(new Path("D:\\test.txt"), new Path("HDFS集群地址/simon/hdfs-test/"));
  ```

* 下载文件

  ```java
  // 下载文件
  fileSystem.copyToLocalFile(new Path("HDFS集群地址/simon/hdfs-test/"), new Path("D:\\test.txt"));
  ```

* 删除文件

  ```java
  // 删除文件
  // 第二个参数表示是否递归删除
  fileSystem.delete(new Path("HDFS集群地址/simon/hdfs-test/"), true);
  ```

* 移动或重命名文件

  ```java
  // 移动或重命名文件
  fileSystem.rename(new Path("HDFS集群地址/simon/hdfs-test/test.txt"), new Path("HDFS集群地址/simon/hdfs-test/test1.txt"));
  ```

* 获取文件信息

  ```java
  // 获取文件夹下所有文件信息，返回一个迭代器
  RemoteIterator<LocatedFileStatus> iterator = fileSystem.listFiles(new Path("HDFS集群地址/simon/hdfs-test/"), true);
  // 遍历迭代器
  while (iterator.hasNext()) {
  	LocatedFileStatus status = iterator.next();
  	System.out.println(status.getPath());
  }
  ```




------

## HDFS 读写数据

### 写数据流程



![image-20220515181419772](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220515181419772.png)

1. client 发起文件上传请求，通过 RPC 与 NameNode 建立通讯，NameNode 检查目标文件是否已存在，父目录是否存在，返回是否可以上传
2. client 请求第一个 block 该传输到哪些 DataNode 服务器上
3. NameNode 根据配置文件中指定的备份数量及副本放置策略进行文件分配，返回可用的 DataNode 的地址，如：A，B，C
4. client 请求3台 DataNode 中的一台A上传数据（本质上是一个 RPC 调用，建立 pipeline），A收到请求会继续调用B，然后B调用C，将整个 pipeline 建立完成，后逐级返回 client
5. client 开始往A上传第一个 block（先从磁盘读取数据放到一个本地内存缓存），以 packet 为单位（默认64K），A收到一个 packet 就会传给B，B传给C；A每传一个 packet 会放入一个应答队列等待应答
6. 数据被分割成一个个 packet 数据包在 pipeline 上依次传输，在 pipeline 反方向上，逐个发送 ack（ack 应答机制），最终由pipeline中第一个 DataNode 节点A将 pipeline ack 发送给client
7. 当一个 block 传输完成之后，client 再次请求 NameNode 上传第二个 block 到服务器



------

### 副本存储节点选择



HDFS中副本存储节点的选择是根据机架感知来选择的

副本数量默认为 3。可以通过配置进行修改，但设置副本数应满足不大于 DataNode 的数量。比如目前只有 3 台设备，最多也就 3 个副本，只有节点数的增加到10 台时，副本数最多才能达到 10



副本节点选择的原则（以副本数为3举例）：

第一个副本：在 Client 所在的节点上，如果客户端在集群外，则随机挑选一个节点

第二个副本：在另一个机架上随机挑选一个节点

第三个副本：在第二个副本所在的机架内再随机挑选一个节点



------

### 读数据流程



![image-20220515213810060](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220515213810060.png)



1. Client 向 NameNode 发起 RPC 请求，来确定请求文件 block 所在的位置
2. NameNode 会视情况返回文件的部分或者全部 block 列表，对于每个 block，NameNode 都会返回含有该 block 副本的 DataNode 地址
3. 这些返回的 DataNode 地址，会按照集群拓扑结构得出 DataNode 与客户端的距离，然后进行排序，排序两个规则：网络拓扑结构中距离Client近的排靠前；心跳机制中超时汇报的 DataNode 状态为 STALE，这样的排靠后
4. Client 选取排序靠前的 DataNode 来读取 block，如果客户端本身就是 DataNode，那么将从本地直接获取数据；底层上本质是建立 Socket Stream（FSDataInputStream），重复的调用父类 DataInputStream 的 read 方法，直到这个块上的数据读取完毕
5. 当读完列表的 block 后，若文件读取还没有结束，客户端会继续向 NameNode 获取下一批的 block 列表
6. 读取完一个 block 都会进行 checksum 验证，如果读取 DataNode 时出现错误，客户端会通知 NameNode，然后再从下一个拥有该 block 副本的 DataNode 继续读
7. read 方法是并行的读取 block 信息，不是一块一块的读取；NameNode 只是返回 Client 请求包含块的 DataNode 地址，并不是返回请求块的数据
8. 最终读取来所有的 block 会合并成一个完整的最终文件



------

## NameNode 和 Secondary NameNode

### 工作流程详解



![image-20220515222532473](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220515222532473.png)





* **第一阶段：namenode启动**
  1. 第一次启动 NameNode 格式化后，创建 fsimage 和 edits 文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存
  2. 客户端对元数据进行增删改的请求
  3. NameNode 记录操作日志，更新滚动日志
  4. NameNode 在内存中对数据进行增删改查
* **第二阶段：Secondary NameNode工作**
  1. Secondary NameNode 询问 NameNode 是否需要 checkpoint。直接带回 NameNode 是否检查结果
  2. Secondary NameNode 请求执行 checkpoint
  3. NameNode 滚动正在写的 edits 日志
  4. .Secondary NameNode 加载编辑日志和镜像文件到内存，并合并
  5. 生成新的镜像文件 fsimage.chkpoint
  6. 拷贝 fsimage.chkpoint 到 NameNode
  7. NameNode 将 fsimage.chkpoint 重新命名成 fsimage



------

### CheckPoint 触发条件



通常情况下，SecondaryNameNode每隔一小时执行一次检查点操作

```xml
<!-- hdfs-default.xml -->
<!-- CheckPoint 间隔时间，单位为秒 -->
<property>
	<name>dfs.namenode.checkpoint.period</name>
    <value>3600</value>
</property>
```

一分钟检查 NameNode 的事务数量。若检查事务数达到这个值 1,000,000，也触发一次 Secondary NameNode 的 CheckPoint 操作

```xml
<!-- hdfs-site.xml -->
<!-- 事务数量配置 -->
<property>
	<name>dfs.namenode.checkpoint.txns</name>
    <value>1000000</value>
</property>
<!-- 间隔多久检查一次事务数量 -->
<property>
	<name>dfs.namenode.checkpoint.check.period</name>
    <value>60</value>
</property>
<!-- 在 NameNode 上保存的 fsimage 文件数量，超出的会被删除，默认为 2 -->
<property>
	<name>dfs.namenode.checkpoint.retained</name>
    <value>2</value>
</property>
```





------

### fsimage 文件和 edits 文件



NameNode 被格式化之后，将在 /opt/module/hadoop/data/tmp/dfs/name/current 目录中产生如下文件

```txt
edits_0000000000000000000
fsimage_0000000000000000000.md5
seen_txid
VERSION
```

* Fsimage文件

  HDFS 文件系统元数据的一个永久性的检查点，其中包含 HDFS 文件系统的所有目录和文件 inode 的序列化信息（id、类型、目录、所属用户、用户权限、时间戳……）

* Edits文件

  存放 HDFS 文件系统的所有更新操作的路径，文件系统客户端执行的所有写操作首先会被记录到 edits 文件中

* seen_txid

  保存的是一个数字，就是最后一个edits_的数字



每次 NameNode 启动的时候都会将 fsimage 文件读入内存，并从 00001 开始到 seen_txid 中记录的数字依次执行每个 edits 里面的更新操作，保证内存中的元数据信息是最新的、同步的，可以看成 NameNode 启动的时候就将 fsimage 和 edits 文件进行了合并



fsimage 文件和 edits 文件无法直接查看，需要使用 HDFS 专用操作指令查看

```sh
// 查看 fsiamge 文件
hdfs oiv -p 文件类型 -i 镜像文件 -o 转换后文件输出路径
// 查看 edits 文件
hdfs oev -p 文件类型 -i 编辑日志 -o 转换后文件输出路径
```



------

## DataNode

### 工作流程详解



![image-20220515235103604](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E5%A4%A7%E6%95%B0%E6%8D%AE%E8%AF%BE%E7%A8%8B%E4%B9%8BHDFS%28%E4%BA%8C%29/image-20220515235103604.png)

1. 一个数据块在 DataNode 上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳
2. DataNode 启动后向 NameNode 注册，通过后，周期性（6小时）的向 NameNode 上报所有的块信息
3. 心跳是每 3 秒一次，心跳返回结果带有 NameNode 给该 DataNode 的命令如复制块数据到另一台机器，或删除某个数据块。如果超过 10 分钟没有收到某个DataNode 的心跳，则认为该节点不可用
4. 集群运行中可以安全加入和退出一些机器



------

### 数据完整性



1. 当 DataNode 读取 Block 的时候，它会计算 CheckSum
2. 如果计算后的 CheckSum，与 Block 创建时值不一样，说明 Block 已经损坏
3. Client 读取其他 DataNode 上的 Block
4. 常见的校验算法 crc（32），md5（128），sha1（160），HDFS 校验位使用的是 crc 算法
5. DataNode 在其文件创建后周期验证 CheckSum



------

### 掉线时限参数设置



DataNode 进程死亡或者网络故障造成 DataNode 无法与 NameNode 通信，NameNode 不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。HDFS默认的超时时长为 10 分钟 +30 秒。如果定义超时时间为 timeout，则超时时长的计算公式为：

```
timeout = 2 * dfs.namenode.heartbeat.recheck-interval + 10 * dfs.heartbeat.interval
```

而默认的 dfs.namenode.heartbeat.recheck-interval 大小为 5 分钟，dfs.heartbeat.interval 默认为3秒

需要注意的是hdfs-site.xml 配置文件中的 heartbeat.recheck.interval 的单位为毫秒，dfs.heartbeat.interval 的单位为秒

```xml
<property>
	<name>dfs.namenode.heartbeat.recheck-interval</name>
    <value>300000</value>
</property>
<property>
	<name>dfs.heartbeat.interval</name>
    <value>3</value>
</property>
```

