---
title: 数据同步工具 Canal
date: 2025-02-27 00:01:53
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20Canal/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20Canal/preview.jpg
tags:
  - 大数据
  - 数据集成与同步
  - Canal
categories: 大数据
---



# 简介

## 什么是 Canal



canal，译意为水道 / 管道 / 沟渠，主要用途是基于 MySQL 数据库增量日志解析，提供增量数据订阅和消费

早期阿里巴巴因为杭州和美国双机房部署，存在跨机房同步的业务需求，实现方式主要是基于业务 trigger 获取增量变更。从 2010 年开始，业务逐步尝试数据库日志解析获取增量变更进行同步，由此衍生出了大量的数据库增量订阅和消费业务



基于日志增量订阅和消费的业务包括

- 数据库镜像
- 数据库实时备份
- 索引构建和实时维护(拆分异构索引、倒排索引等)
- 业务 cache 刷新
- 带业务逻辑的增量数据处理

当前的 canal 支持源端 MySQL 版本包括 5.1.x , 5.5.x , 5.6.x , 5.7.x , 8.0.x



------

## MySQL 的 Binlog



MySQL 的 Binlog 可以说是 MySQL 最重要的日志了，它记录了所有的 DDL 和 DML（除了查询语句）。以事件形式记录，还包含语句执行所消耗的时间，MySQL 的 Binlog 是事务安全型的

一般来说开启 Binlog 会有大概 1% 的性能损耗，Binlog 有两个最重要的使用场景：

1. MySQL Replication 在 Master 端开启 Binlog，Master 把它的 Binlog 传递给 Slaves 来达 Master-Slaves 到数据一致的目的
2. 数据恢复

Binlog 包含两类文件：二进制日志索引文件（后缀名为 .index，用于记录所有的二进制文件）和二进制日志文件（后缀名为 .00000*，记录数据库所有的 DDL 和 DML 语句事件）



------

## Binlog 的分类



MySQL Binlog 的格式有三种，STATEMENT、MIXED 和 ROW，在配置文件中可以选择配置 binlog_format=statement|mixed|row，以下是三种格式的区别：

* STATEMENT

  语句级，Binlog 会记录每次执行写操作的语句，相对 ROW 格式节省空间，但可能产生不一致性。例如 "update test set create_date = now()"，如果用 Binlog 日志进行恢复，由于执行的事件不同产生的数据可能就不同

  优点：节省空间

  缺点：可能产生数据不一致

* ROW

  行级，Binlog 会记录每次操作后每行记录的变化

  优点：保持数据的绝对一致性，只记录执行后的结果

  缺点：占用较大空间

* MIXED

  二者的混合，一般情况下还是使用 STATEMENT 的方式进行处理，当执行 SQL 中包含 UUID()、now() 等函数的时候则用 ROW 的方式进行处理

  优点：节省空间，同时兼顾了一定的数据一致性

  缺点：在特殊情况下还是会有数据不一致的现象，另外 STATEMENT 和 MIXED 对 Binlog 的监控都不方便



------

## MySQL 主从复制的过程



1. Master 主库将 DDL 和 DML 写入到 Binlog 中
2. Slave 从库向 Master 发送 dump 协议，将 Master 主库的 Binlog 拷贝到自己的中继日志（Relay Log）
3. Slave 从库读取并重做中继日志的事件，将改变的数据同步到自己的数据库中



------

## Canal 的工作原理



1. Canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送 dump 协议
2. MySQL master 收到 dump 请求，开始推送 Binlog 给 slave (即 canal )
3. Canal 解析 Binlog 对象(原始为 byte 流)



------

## 使用场景



Otter 是阿里用于进行异地数据库之间的同步框架，Canal 是其中一部分

![image-20240705000444461](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20Canal/image-20240705000444461.png)



常见场景：

* 更新缓存
* 抓取业务表的新增数据变化，用于制作实时统计



------

# 下载与安装

## 提前准备



对于自建 MySQL , 需要先开启 Binlog 写入功能，配置 binlog-format 为 ROW 模式，my.cnf 中配置如下

```cnf
[mysqld]
log-bin=mysql-bin # 开启 binlog
binlog-format=ROW # 选择 ROW 模式
server_id=1 # 配置 MySQL replaction 需要定义，不要和 canal 的 slaveId 重复
```



授权 Canal 链接 MySQL 账号具有作为 MySQL slave 的权限, 如果已有账户可直接 grant

```SH
CREATE USER canal IDENTIFIED BY 'canal';  
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'canal'@'%';
-- GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' ;
FLUSH PRIVILEGES;
```



------

## 使用 Docker 安装 Canal



* 拉取镜像

  ```SH
  docker pull canal/canal-server:v1.1.1
  ```

* Canal docker 目录下自带了一个 run.sh 脚本

  ```SH
  # 下载脚本
  wget https://raw.githubusercontent.com/alibaba/canal/master/docker/run.sh 
  
  # 构建一个destination name为test的队列
  sh run.sh -e canal.auto.scan=false \
  		  -e canal.destinations=test \
  		  -e canal.instance.master.address=127.0.0.1:3306  \
  		  -e canal.instance.dbUsername=canal  \
  		  -e canal.instance.dbPassword=canal  \
  		  -e canal.instance.connectionCharset=UTF-8 \
  		  -e canal.instance.tsdb.enable=true \
  		  -e canal.instance.gtidon=false  \
  ```




------

# 数据同步案例

## Canal 数据结构



![image-20240711175934181](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20Canal/image-20240711175934181.png)



Canal 每次从日志中拉取的数据为一个 Message，一个 Message 里面包含多个 Entry

Entry 包含表名、Entry 类型和序列化之前的数据，Entry 类型有事务、行数据、心跳等，一般做数据分析只需要行数据即可

反序列化后的数据包括数据的操作类型，执行 SQL 之前和之后的数据



------

## 将 Canal 数据同步到 Java Project



首先，找到 canal.properties 文件，修改数据同步方式，默认的就是 tcp

```properties
# tcp, kafka, RocketMQ 这里选择 tcp 模式
canal.serverMode = kafka
```



然后，创建一个 Maven Project，并引入以下依赖

```xml
<dependency>
    <groupId>com.alibaba.otter</groupId>
    <artifactId>canal.client</artifactId>
</dependency>
```



接收数据的代码如下

```java
public static void main(String args[]) {
    // 创建链接
    CanalConnector connector = CanalConnectors.newSingleConnector(new InetSocketAddress(AddressUtils.getHostIp(),
                                                                                        11111), "example", "", "");
    int batchSize = 1000;
    int emptyCount = 0;
    try {
        connector.connect();
        // 订阅，前面是 库名，后面是表名
        connector.subscribe(".*\\..*");
        connector.rollback();
        int totalEmptyCount = 120;
        while (emptyCount < totalEmptyCount) {
            // 获取指定数量的数据，数据量不够则返回全部数据
            Message message = connector.getWithoutAck(batchSize); 
            long batchId = message.getId();
            int size = message.getEntries().size();
            if (batchId == -1 || size == 0) {
                emptyCount++;
                System.out.println("empty count : " + emptyCount);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                }
            } else {
                emptyCount = 0;
                // System.out.printf("message[batchId=%s,size=%s] \n", batchId, size);
                printEntry(message.getEntries());
            }

            connector.ack(batchId); // 提交确认
            // connector.rollback(batchId); // 处理失败, 回滚数据
        }

        System.out.println("empty too many times, exit");
    } finally {
        connector.disconnect();
    }
}

private static void printEntry(List<Entry> entrys) {
    for (Entry entry : entrys) {
        // 不处理事务数据
        if (entry.getEntryType() == EntryType.TRANSACTIONBEGIN || entry.getEntryType() == EntryType.TRANSACTIONEND) {
            continue;
        }
		// 反序列化
        RowChange rowChage = null;
        try {
            rowChage = RowChange.parseFrom(entry.getStoreValue());
        } catch (Exception e) {
            throw new RuntimeException("ERROR ## parser of eromanga-event has an error , data:" + entry.toString(),
                                       e);
        }

        EventType eventType = rowChage.getEventType();
        System.out.println(String.format("================&gt; binlog[%s:%s] , name[%s,%s] , eventType : %s",
                                         entry.getHeader().getLogfileName(), entry.getHeader().getLogfileOffset(),
                                         entry.getHeader().getSchemaName(), entry.getHeader().getTableName(),
                                         eventType));

        // 打印同步过来的数据内容
        for (RowData rowData : rowChage.getRowDatasList()) {
            if (eventType == EventType.DELETE) {
                printColumn(rowData.getBeforeColumnsList());
            } else if (eventType == EventType.INSERT) {
                printColumn(rowData.getAfterColumnsList());
            } else {
                System.out.println("-------&gt; before");
                printColumn(rowData.getBeforeColumnsList());
                System.out.println("-------&gt; after");
                printColumn(rowData.getAfterColumnsList());
            }
        }
    }
}

private static void printColumn(List<Column> columns) {
    for (Column column : columns) {
        System.out.println(column.getName() + " : " + column.getValue() + "    update=" + column.getUpdated());
    }
}
```



启动 Canal Client 后，可以从控制台从看到类似消息：

```
empty count : 1
empty count : 2
empty count : 3
empty count : 4
```

此时代表当前数据库无变更数据



触发数据库变更，可以从控制台中看到：

```
empty count : 1
empty count : 2
empty count : 3
empty count : 4
================> binlog[mysql-bin.001946:313661577] , name[test,xdual] , eventType : INSERT
ID : 4    update=true
```



------

## 将 Canal 数据同步到 Kafka



修改 canal.properties 配置文件

```properties 
# tcp, kafka, RocketMQ 这里选择kafka模式
canal.serverMode = kafka
# 解析器的线程数，打开此配置，不打开则会出现阻塞或者不进行解析的情况
canal.instance.parser.parallelThreadSize = 16
# 配置MQ的服务地址，这里配置的是kafka对应的地址和端口
canal.mq.servers = 127.0.0.1:9092
# 配置instance，在conf目录下要有example同名的目录，可以配置多个
canal.destinations = example
```



然后配置 instance，找到 /conf/example/instance.properties 配置文件：

```properties 
## mysql serverId , v1.0.26+ will autoGen(自动生成，不需配置)
# canal.instance.mysql.slaveId=0

# position info
canal.instance.master.address=127.0.0.1:3306
# 在Mysql执行 SHOW MASTER STATUS;查看当前数据库的binlog
canal.instance.master.journal.name=mysql-bin.000006
canal.instance.master.position=4596
# 账号密码
canal.instance.dbUsername=canal
canal.instance.dbPassword=Canal@****
canal.instance.connectionCharset = UTF-8
#MQ队列名称
canal.mq.topic=canaltopic
#单队列模式的分区下标
canal.mq.partition=0
```



配置完成后，重启 Canal，当数据发生变化时就会推送到 Kafka 了
