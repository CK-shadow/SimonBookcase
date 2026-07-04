---
title: MongoDB实战：基础入门
date: 2021-10-24 13:49:22
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9A%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8/preview.jpg
tags:
  - 数据库与存储
  - MongoDB
categories: 数据库与存储
---



# 简介

## 什么是 MongoDB



MongoDB 是一个文档数据库（以 JSON 为数据模型），由 C++ 编写，旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。文档指的是 “JSON Document”，并非一般我们理解的 Word、PDF 等文档

MongoDB 是一个介于关系型数据库和非关系型数据库之间的产品，是非关系型数据库中功能最丰富、最像关系型数据库的。它支持的数据结构非常松散，数据格式是 BSON，一种类似于 JSON 的二进制存储格式，简称 Binary JSON，和 JSON 一样支持内嵌的文档对象和数组对象，因此可以存储比较复杂的数据结构

MongoDB 最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系型数据库单表查询的绝大部分功能，而且还支持对数据建立索引。原则上 MySQL 和 Oracle 能做的事，MongoDB 都能做，包括 ACID 事务

MongoDB 在数据库最排名中排第 5，仅次于 Oracle、MySQL 等 RDBMS，在 NoSQL 数据库中排在首位，自诞生依赖，其项目应用广度、社区活跃度指数持续上升



MongoDB 概念与 RDBMS 概念非常类似

|       SQL 概念       |     MongoDB 概念     |
| :------------------: | :------------------: |
|  数据库（database）  |  数据库（database）  |
|     表（table）      |  集合（collection）  |
|      行（row）       |   文档（document）   |
|     列（column）     |    字段（field）     |
|    索引（index）     |    索引（index）     |
| 主键（primary key）  |     主键（_id）      |
|     视图（view）     |     视图（view）     |
| 表连接（table join） | 聚合操作（￥lookup） |



尽管这些概念与 SQL 标准定义类似，但 MongoDB 与传统 RDBMS 仍存在不少差异：

* 半结构化，在一个集合中，文档所拥有的字段并不需要是相同的，而且不需要对所用字段进行声明。因此，MongoDB 具有很明显的半结构化的特点。除了松散的表结构，文档还可以支持多级的嵌套、数组等灵活的数据类型，非常契合面向对象的编程模型
* 弱关系，MongoDB 没有外键的约束，也没有非常强大的表连接能力，类似的功能需要聚合管道技术来补充



------

## 技术优势



MongoDB 基于灵活的 JSON 文档模型，非常适合敏捷式的快速开发。与此同时，其越生俱来的高可用、高水平扩展能力使得它在处理海量、高并发的数据应用时颇有优势

* JSON 结构对象与对象模型接近，开发代码量低
* JSON 的动态模型意味着更容易响应新的业务需求
* 复制集提供 99.99% 的高可用
* 分片架构支持海量数据和无限扩容



------

## 应用场景



从目前阿里云 MongoDB 云数据库上的用户看，MongoDB 的应用已经渗透到了各个领域

* 游戏场景，使用 MongoDB 存储游戏用户信息，用户的装备、积分等直接以内嵌文档的形式储存，方便查询、更新
* 物流场景，使用 MongoDB 存储订单信息，订单状态在运送过程中会不断更新，以 MongoDB 内嵌数组的形式来储存，一次查询就能将订单所有的变更读取出来
* 社交场景，使用 MongoDB 存储用户信息，以及用户发表的朋友圈信息，通过地理位置搜索实现附近的人、地点等功能
* 物联网场景，使用 MongoDB 存储所有接入的只能设备信息，以及设备汇报的日志信息，并对这些信息进行多维度的分析
* 视频直播，使用 MongoDB 存储用户信息、礼物信息等
* 大数据应用，使用云数据库 MongoDB 作为大数据的云存储系统，即时进行数据提取分析



---

# Mongo Shell

## 简介



Mongo Shell 是 MongoDB 的交互式 JavaScript Shell 界面，它为系统管理员提供了强大的界面，并为开发人员提供了直接操作数据库的方法



------

## 常用命令



|              命令               |             说明             |
| :-----------------------------: | :--------------------------: |
|   show dbs \| show databases    |        显示数据库列表        |
|          use 数据库名           | 切换数据库，如果不存在则创建 |
|        db.dropDatabase()        |          删除数据库          |
| show collections \| show tables |   显示当前的数据库集合列表   |
|        db.集合名.stats()        |         查看集合详情         |
|        db.集合名.drop()         |           删除集合           |
|           show users            |   显示当前数据库的用户列表   |
|           show roles            |   显示当前数据库的角色列表   |
|          show profile           |      显示最近发生的操作      |
|         load("XXX.js")          |     执行一个 JS 脚本文件     |
|         exit \| quit()          |        退出当前 shell        |
|          db.version()           |        查看数据库版本        |



---

## 创建管理员账号



```shell
# 设置管理员用户名密码需要切换到 admin 库
use admin
# 创建管理员
db.createUser({user:"admin",pwd:"admin",roles:["root"]})
# 查看所有用户信息
show users
# 删除用户
db.dropUser("admin")
```



mongodb内置角色：

    1. 数据库用户角色：read、readWrite
    2. 数据库管理角色：dbAdmin、dbOwner、userAdmin
    3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager
    4. 备份恢复角色：backup、restore
    5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
    6. 超级用户角色：root  
    7. 内部角色：__system


------

## 创建应用数据库用户



```sh
use testdb
db.createUser({user:"admin",pwd:"admin",roles:["dbOwner"]})
```



默认情况下，MongoDB 不会启用鉴权，以鉴权模式启动 MongoDB

```sh
mongod -f /mongodb/conf/mongo.conf --auth
```

启用鉴权模式后，连接 MongoDB 的相关操作都需要提供身份认证

```sh
mongo localhost:27017 -u admin -p admin -authenticationDatabase=admin
```



---

# 集合操作

## 创建集合



```
db.createCollection(name, options)
```

name 是要创建的集合的名称，options 是可选参数, 指定有关内存大小及索引的选项，可以是如下参数：

|    字段     | 类型 |                             描述                             |
| :---------: | :--: | :----------------------------------------------------------: |
|   capped    | 布尔 | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为 true 时，必须指定 size 参数 |
| autoIndexId | 布尔 | 3.2 之后不再支持该参数。（可选）如为 true，自动在 _id 字段创建索引。默认为 false |
|    size     | 数值 | （可选）为固定集合指定一个最大值，即字节数。如果 capped 为 true，也需要指定该字段 |
|     max     | 数值 |           （可选）指定固定集合中包含文档的最大数量           |

‘

示例

```
db.createCollection("mycol", { capped : true, autoIndexId : true, size : 6142800, max : 10000 } )
```

在 MongoDB 中，你不需要创建集合。当你插入一些文档时，MongoDB 会自动创建集合

```
// 如果 mycol2 集合不存在，则会自动创建
db.mycol2.insert({"name" : "simon"})
```



------

## 删除集合



```
db.collection.drop()
```



---

# 文档操作

## 新增文档



```
db.collection.insert()
```



```js
// document  要插入集合的文件
// writeConcern 可选参数，设置副本集同步数量，等待时间等内容
// ordered 是否按顺序插入，默认为 true
db.collection.insert(
   <document or array of documents>,
   {
     writeConcern: <document>,
     ordered: <boolean>
   }
)
```



例：

```js
db.products.insertOne( { _id: 10, item: "box", qty: 20 } );
```

如果没有输入 _id 参数，MongoDB 会自动为数据生成一个 id，如果输入的 id 已经存在，则会抛出异常

参数内容也可以是个数组，即批量新增文档



3.2 版本之后新增了 db.collection.insertOne() 和 db.collection.insertMany() 方法

```js
db.collection.insertOne(
   <document>,
   {
      writeConcern: <document>
   }
)

db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)
```



------

## 更新文档



```
// 更新单个文档
db.collection.updateOne(
   <filter>,
   <update>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ],
     hint:  <document|string>        // Available starting in MongoDB 4.2.1
   }
)

// 更新多个文档
db.collection.updateMany(
   <filter>,
   <update>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ],
     hint:  <document|string>        // Available starting in MongoDB 4.2.1
   }
)

// 替换单个文档
db.collection.replaceOne(
   <filter>,
   <replacement>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     hint: <document|string>                   // Available starting in 4.2.1
   }
)
```



* filter：更新的条件
* update 或 replacement：更新或替换的内容
* upset：如果不存在要更新的内容是否新增，默认为 false
* writeConcern：可选，抛出异常的级别
* collation：排序规则
* hint：可以用于指定 filter 索引



比如，将用户名为 Simon 的用户 age 改为 18

```
db.restaurant.updateOne(
  { "name" : "Simon" },
  { $set: { "age" : 18 } }
);
```



$set 是要进行操作的操作符，常用的操作符如下

| 操作符    | 格式                                  | 描述                                       |
| --------- | ------------------------------------- | ------------------------------------------ |
| $set      | { $set : { filed : value }}           | 指定一个键并更新，如果不存在则新建         |
| $unset    | { $unset : { filed : 1}}              | 删除一个键                                 |
| $inc      | { $inc : { filed : value }}           | 对数值进行增减                             |
| $rename   | { $rename : { old_name: new_name}}    | 修改字段名称                               |
| $push     | { $push : { filed : value }}          | 将数值追加到数组中，如果数组不存在则初始化 |
| $pushAll  | { $pushAll : { filed : value_array }} | 将多个值追加到数组中                       |
| $pull     | { $set : { filed : value }}           | 从数组中删除指定元素                       |
| $addToSet | { $addToSet : { filed : value }}      | 添加元素到数组中，具有排重功能             |
| $pop      | { $pop : { filed : 1}}                | 删除第一个或最后一个元素                   |



------

## 查询文档



```
db.collection.find(query, projection)
```

* query 可选，使用查询操作符指定查询条件
* projection 可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）



返回文档中所有 status 为 A 的文档，返回所有字段

```
db.inventory.find( { status: "A" } )
```

返回文档中所有 status 为 A 的文档，返回 item、status、id 3 个字段

```
db.inventory.find( { status: "A" }, { item: 1, status: 1 } )
```

id 字段是默认返回的，如果不想要 id 字段

```
db.inventory.find( { status: "A" }, { item: 1, status: 1, _id: 0 } )
```

返回文档中所有 status 为 A 的文档，返回除了 status 之外的所有字段

```
db.inventory.find( { status: "A" }, { status: 0 } )
```

返回嵌套文档中的指定字段

```
db.inventory.find(
   { status: "A" },
   { item: 1, status: 1, "size.uom": 1 }
)
```

如果想检索数组中包含red、blank两个元素并且不在乎元素顺序或者数组中是否有其它元素。可以使用

```
db.inventory.find( { tags: { $all: ["red", "blank"] } } )
```

返回 inventory 集合中数组字段 tags 中有一个元素的值 red 的所有文档

```
db.inventory.find( { tags: "red" } )
```

返回 inventory 集合中数组字段 dim_cm 中最少有一个元素的值大于 25的所有文档

```
db.inventory.find( { dim_cm: { $gt: 25 } } )
```

返回 inventory 集合中数组字段 dim_cm 中单个元素同时满足大于 15 并且小于 20，或者一个元素满足大于 15，另外一个元素小于 20 的所有文档

```
db.inventory.find( { dim_cm: { $gt: 15, $lt: 20 } } )
```

返回的是 item 字段值为 null 的文档或者不包含 item 字段的文档

```
db.inventory.find( { item: null } )
```



------

## 删除文档



```
// 删除单个文档
db.collection.deleteOne(
   <filter>,
   {
      writeConcern: <document>,
      collation: <document>,
      hint: <document|string>        // Available starting in MongoDB 4.4
   }
)

// 删除多个文档
db.collection.deleteMany(
   <filter>,
   {
      writeConcern: <document>,
      collation: <document>
   }
)
```



---

# 聚合操作

## 简介



聚合操作处理数据并返回结果（比如平均值、求和等）。聚合操作组值来自多个文档，可以对分组数组执行各种操作以返回单个结果。聚合操作包含三类：单一作用聚合、聚合管道、MapReduce

* 单一作用聚合：提供了对常见聚合过程的简单访问，操作从单个集合聚合文档
* 聚合管道：数据聚合的框架，模型基于数据处理流水线的概念，文档进入多级管道，将文档转换为聚合结果
* MapReduce：MapReduce 操作具有两个阶段，一阶段处理文档输出一个或多个结果，二阶段组合 Map 输出聚合结果



------

## 单一聚合操作



MongoDB 提供了 count()、distinctCount() 这类单一作用的聚合函数，所有的操作都聚合来自单个集合的文档，虽然这些操作提供了对公共聚合过程的简单访问，但它缺乏聚合管道和 MapReduce 的灵活性和功能



MongoDB 中聚合的方法使用 aggregate()

```
db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)
```

比如计算每个年龄的人数

```
db.collection.aggregate([{$group : {_id : "$age", num_tutorial : {$sum : 1}}}])
```



一些常用的聚合表达式

|  表达式   |                             描述                             |
| :-------: | :----------------------------------------------------------: |
|   $sum    |                           计算总和                           |
|   $avg    |                          计算平均值                          |
|   $min    |               获取集合中所有文档对应值得最小值               |
|   $max    |               获取集合中所有文档对应值得最大值               |
|   $push   |          将值加入一个数组中，不会判断是否有重复的值          |
| $addToSet | 将值加入一个数组中，会判断是否有重复的值，若相同的值在数组中已经存在了，则不加入 |
|  $first   |             根据资源文档的排序获取第一个文档数据             |
|   $last   |            根据资源文档的排序获取最后一个文档数据            |



------

## 聚合管道



管道在 Unix 和 Linux 中一般用于将当前命令的输出结果作为下一个命令的参数

MongoDB 的聚合管道将 MongoDB 文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的



聚合管道操作语法

```
pipeline = [$stage1, $stage2, ... $stageN]
db.collection.aggregate(pipeline, {options})
```

* pipeline：数据聚合阶段，除 $out、$merge 和 $geonear 外，每个阶段可以在管道中出现多次
* options：其它参数，包括查询计划，是否使用临时文件、游标、最大操作时间、读写策略、强制索引等等



常用聚合阶段

|      阶段      |                             描述                             |    SQL 等价     |
| :------------: | :----------------------------------------------------------: | :-------------: |
|     $match     |              用于过滤数据，只输出符合条件的文档              |      WHERE      |
|    $project    | 修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档 |       AS        |
|    $lookup     |                           左外连接                           | LEFT OUTER JOIN |
|     $sort      |                             排序                             |    ORDER BY     |
|     $group     |                             分组                             |    GROUP BY     |
|  $skip/$limit  |                           分页参数                           |                 |
|    $unwind     | 将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值 |                 |
|  $graphLookup  |                            图搜索                            |                 |
| $facet/$bucket |                           分面搜索                           |                 |



* $project

  投影操作，将原始字段投影成指定名称，比如将集合中的 name 投影成 username

  ```
  db.users.aggregate([{$project:{username:"$name"}}]);
  ```

  $project 可以灵活控制输出文档的格式，可以剔除不需要的字段

  ```
  db.users.aggregate([{$project:{_id:0, name:1}}])
  ```

* $match

  $match 用于对文档进行筛选，之后可以在得到的子文档上进行聚合，$match 可以使用除了地理空间之外的所有的常规查询操作符。在实际开发中尽量将 $match 放在管道前面的位置，这样做有两个好处，一是可以快速将不需要的文档过滤掉，减少管道的工作量，二是如果在投射和分组之前执行 $match，查询可以使用索引

  ```
  db.users.aggregate([{$match:{name:"Simon"}}])
  ```

* $count

  计数并返回与结果匹配的数量

  ```
  db.users.aggregate([{$match:{name:"Simon"}, {$count:"name_count"}])
  ```

* $group

  按指定的表达式对文档进行分组，并将每个不同分组的文档输出到下一阶段。输出文档包含一个 id 字段，该字段按键包含不同的组。输出文档还可以包含计算字段，该字段保存由 $group 的 _id 字段分组的一些 accumulator 表达式的值。$group 不会输出具体的文档而是统计信息

  ```
  {$group:{_id:<expression>, <field>:{<accumulator1>:<expression1>}, ...}}
  ```

  * id 字段是必填的，可以指定 id 值为 null 来为整个文档计算累计值
  * 剩余的计算字段是可选的，并使用 accumulator 运算符进行计算
  * id 和 accumulator 表示式可以接收任何有效的表达式

* $limit

  限制管道传到下一阶段的文档数

  ```
  db.users.aggregate([{$limit:5}])
  ```

  $sort 无论是在 $limit 之前还是之后出现，$sort 都只会在过程中维持前 N 个结果，其中 N 是指定的限制，MongoDB 只需将前 N 项内容存储在内存中

* $skip

  跳过指定数量的文档，并将其余文档传递到下一阶段

  ```
  db.users.aggregate([{$skip:5}])
  ```

* $sort

  对所有输入文档进行排序，并按照顺序将他们返回到管道

  ```
  db.users.aggregate([{$sort:{age:-1, name:1}}])
  ```

  其中 1 代表升序，-1 代表降序



------

## MapReduce



在用 MongoDB 查询时，若返回的数据量很大，或者做一些比较复杂的统计和聚合操作做花费的时间很长时，可以使用 MongoDB 中的  mapReduce 进行实现。mapReduce  是个灵活且强大的数据聚合工具，它的好处是可以把一个聚合任务分解为多个小的任务，分配到多个服务器上并行处理

```
db.collection_name.mapReduce(
    function() {emit(key, value);},                  // map 函数
    function(key, values) {return reduceFunction},   // reduce 函数
    {
        out: collection,
        query: document,
        sort: document,
        limit: number
    }
)
```

* map 函数：一个 javascript 函数，它用一个键映射一个值并发出一个键值对
* reduce 函数：一个 javascript 函数，用于减少或分组具有相同键的所有文档
* out：指定 map-reduce 查询结果的位置
* query：指定用于选择文档的可选选择条件
* sort：指定可选的排序条件
* limit：指定要返回的最大文档数（可选）



---

# 视图

## 简介



MongoDB 视图是一个可查询的对象，它的内容由其它集合或视图上的聚合管道定义。MongoDB 不会将视图内容持久化到磁盘，当客户查询视图时，视图的内容按需计算。MongoDB 可以要求客户端具有查询视图的权限，MongoDB 不会对视图进行写操作



作用：

* 数据抽象
* 保护敏感数据的一种方法
* 只读
* 结合基于角色的授权，可按角色访问信息



------

## 创建视图



单个集合创建视图，比如查看当天价格最高的 10 笔订单

```
db.createView(
	"orderInfo",  // 视图名称
	"order",  // 数据源
	[
		// 筛选符合条件的订单，大于当天，这里要注意时区
		{$match: {"orderTime": {$gte: ISODate("2022-01-26T00:00:000Z")}}},
		// 金额倒序
		{$sort: {"price": -1}},
		// 限制 10 个文档
		{$limit: 10},
		// 选择需要输出的字段内容
		{$project: {_id: 0, orderNo: 1, price: 1, orderTime: 1}}
	]
)
```



多个集合创建视图，多个集合创建视图与单个基本相同，致使多了 $lookup 关联文档

```
db.createView(
	"orderDetail",  // 视图名称
	"order",  // 数据源
	[
		{$lookup : {"from": "shipping", "localField": "orderNo", "foreignField": "orderNo", "as": "shipping"}},
		// 选择需要输出的字段内容
		{$project: {orderNo: 1, price: 1, shipping.address: 1}}
	]
)
```

