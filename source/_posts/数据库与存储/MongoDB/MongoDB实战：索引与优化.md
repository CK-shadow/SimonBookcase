---
title: MongoDB实战：索引与优化
date: 2021-11-29 07:59:16
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9A%E7%B4%A2%E5%BC%95%E4%B8%8E%E4%BC%98%E5%8C%96/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9A%E7%B4%A2%E5%BC%95%E4%B8%8E%E4%BC%98%E5%8C%96/preview.jpg
tags:
  - 数据库与存储
  - MongoDB
categories: 数据库与存储
---



# 简介



与 MySQL 类似，MongoDB 也可以设置索引，并且 MongoDB 索引的数据结构也是 B+ 树



------

# 索引的分类



* 按照索引包含的字段的数量，可分为单键索引和组合索引（也称为复合索引）
* 按照字段的类型，可分住主键索引和非主键索引
* 按照索引节点与物理数据的对应方式，可分为聚簇索引和非聚簇索引
* 按照索引特性的不同，又可以分为唯一索引、稀疏索引、文本索引、地理空间索引等



与大多数数据库一样，MongoDB 支持各种丰富的索引类型，包括单键索引、复合索引、唯一索引等一些常用结构。由于采用了灵活可变的文档类型，因此同样支持嵌套字段、数组的索引。通过建立合适的索引，可以极大提升数据的检索速度，在一些特殊的应用场景，MongoDB 还支持地理空间索引、文本检索索引、TTL 索引等



------

# 索引操作



创建索引

```
db.collection.createIndex(keys, options)
```



语法中 Key 值为你要创建的索引字段，1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1 即可

options 为可选参数，可选参数列表如下

| Parameter          | Type          | Description                                                  |
| ------------------ | ------------- | ------------------------------------------------------------ |
| background         | Boolean       | 建索引过程会阻塞其它数据库操作，background 可指定以后台方式创建索引，即增加 "background" 可选参数。  "background" 默认值为 false |
| unique             | Boolean       | 建立的索引是否唯一。指定为 true 创建唯一索引。默认值为 false |
| name               | string        | 索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称 |
| dropDups           | Boolean       | 3.0+ 版本已废弃。在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 false |
| sparse             | Boolean       | 对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为 true 的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 false |
| expireAfterSeconds | integer       | 指定一个以秒为单位的数值，完成 TTL 设定，设定集合的生存时间  |
| v                  | index version | 索引的版本号。默认的索引版本取决于 mongod 创建索引时运行的版本 |
| weights            | document      | 索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重 |
| default_language   | string        | 对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语 |
| language_override  | string        | 对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language |



```
// 创建索引后台执行
db.values.createIndex({open: 1, close: 1}, {backgroud: true})
// 创建唯一索引
db.values.createIndex({title: 1}, {unique: true})
```

```
// 查看索引信息
db.books.getIndexes()
// 查看索引键
db.books.getIndexKeys()
// 查看索引大小
// 可传入值，传入 0 或 false 以外的任何数据，都会显示每个索引大小以及总大小，传入 0 或 false 时则只会显示总大小，默认为 false
db.books.totalIndexSize(value)
```

```
// 删除指定索引
db.books.dropIndex("索引名称")
// 删除集合中的所有索引
db.books.dropIndexes()
```



------

# 单键索引



在某一个特定的字段上建立索引，MongoDB 在 id 字段上建立了唯一单键索引，所以经常会使用 id 来进行查询；在索引字段上进行精确匹配、排序和范围查找时都会使用此索引



------

# 地理空间索引



在移动互联网时代，基于地理位置的检索（LBS）功能几乎是所有应用系统的标配。MongoDB 为地理空间检索提供了非常方便的功能。地理空间索引就是专门用于实现位置检索的一种特殊索引



假设商家的数据模型如下

```
db.restaurant.insert({
	restaurantId: 0,
	restaurantName: "兰州拉面",
	localtion: {
		type: "Point",
		coordinates: [-73.97, 40.77]
	}
})
```

创建一个 2dsphere 索引

```
db.restaurant.createIndex({localtion: "2dsphere"})
```

查询附近 10000 米内商家信息

```
db.restaurant.find({
	localtion: {
		$near: {
			$geometry: {
				type: "Point",
				coordinates: [-73.97, 40.77]
			},
			$maxDistance: 10000
		}
	}
})
```

* $near 查询操作符，用于实现附近商家的检索，返回的结果会按照距离排序
* $geometry 操作符用于指定一个 GeoJSON 格式的地理空间对象，type = Point 表示地理坐标点，coordinates 则是当前经纬度，$maxdistance 限定了范围，单位是米



------

# 全文索引



MongoDB 支持全文检索功能，可通过建立文本索引来实现简易的分词检索

$text 操作符可以在有 text index 的集合上执行文本检索。$text 将会使用空格和标点符号作为分隔符对检索字符串进行分词，并且对检索字符串中所有的分词结果进行一个逻辑上的 OR 操作



数据内容如下

```
db.stores.insert([
	{_id: 1, name: "Java", description: "Java Code"},
	{_id: 2, name: "MongoDB", description: "database MongoDB"}
	{_id: 3, name: "MySQL", description: "database MySQL"}
])
```

创建 name 和 description 的全文索引

```
db.stores.createIndex({name: "text", description: "text"})
```

查询包含有 database 的内容

```
db.stores.find({$text: {$search: "database"}})
```

MongoDB 暂未提供中文分词功能，这使得该功能的使用场景十分受限



------

# Hash 索引



不同于传统的 B-Tree 索引，哈希索引使用 hash 函数来创建索引。在索引字段上进行精确匹配，但不支持范围查询，不支持多键 hash。Hash 索引上的入口时均匀分布的，这在分片集合中非常有用

```
db.users.createIndex({username: "hashed"})
```



------

# 通配符索引



MongoDB 的文档模式是动态变化的，而通配符索引可以建立在一些不可预知的字段上，以实现查询的加速。MongoDB 4.2 引入了通配符索引来支持对未知或任意字段的查询



数据准备，不同的商品属性不同

```
db.products.insert([
	{
		"product_name": "Spy Coat",
		"product_attributes": {
			"material": ["Tweed", "Wool", "leather"],
			"size": {
				"length": 72,
				"units": "inches"
			}
		}
	},
	{
		"product_name": "Spy Pen",
		"product_attributes": {
			"colors": ["blue", "black"],
			"secrect_feather": {
				"name": "laser",
				"power": "1000",
				"units": "watts"
			} 
		}
	},
	{
		"product_name": "Spy Book"
	}
])
```

创建通配符索引

```
db.products.createIndex({"product_attributes.$**": 1})
```

* 注意，通配符索引不兼容索引类型或属性
* 通配符索引是稀疏的，不索引空字段。因此，通配符索引不能支持查询字段不存在的文档
* 通配符索引为文档或数组的内容生成条目，而不失文档/数组本身。因此通配符索引不能支持精确的文档/数组匹配。通配符索引可以支持查询字段等于空文档 {} 的情况



------

# 唯一索引



在现实场景中，唯一性是很常见的一种索引约束需求，重复的数据记录会带来许多处理上的麻烦，比如订单的编号、用户名等等。通过建立唯一性索引，可以保证集合中文档的指定字段拥有唯一值

```
// 创建唯一索引
db.values.createIndex({title: 1}, {unique: true})
// 复合索引支持唯一性约束
db.values.createIndex({title: 1, type: 1}, {unique: true})
// 多键索引支持唯一性约束
db.values.createIndex({ratings: 1}, {unique: true})
```

* 唯一索引对于文档中缺失的字段，会用 null 代替，因此不存在允许多个文档缺失索引字段的情况
* 对于分片的集合，唯一性约束必须匹配分片规则。换句话说，为了保证全局的唯一性，分片键必须作为唯一性索引的前缀字段



------

# 部分索引



部分索引仅对满足指定过滤器表达式的文档进行索引。通过在一个集合中为文档的一个子集建立索引，部分索引具有更低的存储需求和更低的创建及维护成本，3.2 版本新增功能

部分索引提供了稀疏索引功能的超集，优先级应高于稀疏索引

```
db.restaurants.createIndex(
	{cuisine: 1, name: 1},
	{partialFilterExpression: {rating: {$gt: 5}}}
)
```

```
// 符合条件，可以触发索引
db.restaurants.find({cuisine: "Italian", rating: {$gt: 8}})
// 不符合条件，不触发索引
db.restaurants.find({cuisine: "Italian"})
```



------

# 稀疏索引



索引的稀疏属性确保索引只包含具有索引字段的文档的条目，索引将跳过没有索引字段的文档

特性：只对存在字段的文档进行（包含字段值为 null 的文档）

```
db.addresses.createIndex({"xmpp_id: 1"}, {sparse: true})
```

如果稀疏索引会导致查询和排序操作的结果集不完成，MongoDB 将不会使用索引，除非 hint() 明确指定索引



------

# TTL 索引



在一般的应用系统中，并非所有数据都需要永久存储。例如一些系统事件、用户消息等，这些数据随着时间的推移，其重要程度逐渐降低。更重要的是，存储这些大量的历史数据需要花费较高的成本，因此项目中通常会对过期且不再使用的数据进行老化处理

通常的做法如下：

方案一：为每个数据记录一个时间戳，应用侧开启一个定时器，按时间戳定期删除过期的数据

方案二：数据按日期进行分表，同一天的数据归档到同一张表，同样使用定时器删除过期的表

对于数据老化，MongoDB 提供了一种更便捷的做法：TTL 索引。TTL 索引需要声明一个日期类型的字段，TTL 索引是特殊的单字段索引，MongoDB 可以使用它在一定时间或特定时间时钟后自动从集合中删除文档

```
// 创建 TTL 索引，TTL 值为 3600 秒
db.eventLog.createIndex({"lastMofiedTime": 1}, {expireAfterSeconds: 3600})
```

对集合创建 TTL 索引之后，MongoDB 会周期性的运行后台线程对该集合进行检查以及数据清理工作。除了数据老化功能，TTL 索引具有普通索引的功能，同样用于加速数据的查询

TTL 索引不保证过期数据在过期后会立即被删除，文档过期和 MongoDB 从集合中删除文档的时间可能存在延迟，删除过期文档的后台程序每 60 秒运行一次。因此，文档到期和后台任务运行的时间段内，文档仍会存在



TTL 索引的确可以减少开发的工作量，而且通过数据库自动清理的方式更加高效可靠，但是使用 TTL 索引需要注意以下事项：

* TTL 索引只支持单个字段，并且必须是非 _id 字段
* TTL 索引不能用于固定集合
* TTL 索引不能保证及时的数据老化，MongoDB 会通过后台的 TTL Monitor 定时器来清理老化数据，默认的间隔时间是 1 分钟。当然在数据库负载过高的情况下，TTL 的行为则会收到进一步的影响
* TTL 索引对数据库的清理仅使用了remove 命令，这种方式并不是很高效。因此 TTL Monitor 在运行期间对系统 CPU、磁盘都会造成一定的压力。相比之下，按日期分表的方式更加高效



------

# 隐藏索引



隐藏索引对查询规划器不可见，不能用于支持查询。通过对规划器隐藏索引，用户可以在不实际删除索引的情况下评估删除索引的潜在影响。如果影响是负面的，用户可以取消隐藏索引，而不必重新创建索引，4.4 版本新功能

```
// 创建隐藏索引
db.restaurant.createIndex({borough: 1}, {hidden: true})
// 隐藏现有索引
db.restaurant.hiddenIndex({borough: 1})
db.restaurant.hiddenIndex("索引名称")
// 取消隐藏索引
db.restaurant.unhiddenIndex({borough: 1})
db.restaurant.unhiddenIndex("索引名称")
```



------

# 执行计划



MongoDB 提供了 explain 命令，帮助我们评估指定查询模型的执行计划，然后根据实际情况进行调整，提高查询效率

```
db.collection.find().explain(<verbose>)
```

verbose 为可选参数，表示执行计划的输出模式，共有 3 个值，默认为 queryPlanner

* queryPlanner

  执行计划的详细信息，包括查询计划、集合信息、查询条件、最佳执行计划、查询方式和 MongoDB 服务信息等

* executionStats

  最佳执行计划的执行情况和被拒绝的计划等信息

* allPlansExecution

  选择并执行最佳执行计划，并返回最佳执行计划和其它执行计划的执行情况



queryPlanner 响应参数

![image-20230101223029240](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9A%E7%B4%A2%E5%BC%95%E4%B8%8E%E4%BC%98%E5%8C%96/image-20230101223029240.png)

executionStats 响应参数

![image-20230101223052859](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MongoDB/MongoDB%E5%AE%9E%E6%88%98%EF%BC%9A%E7%B4%A2%E5%BC%95%E4%B8%8E%E4%BC%98%E5%8C%96/image-20230101223052859.png)
