---
title: ElasticSearch 实战
date: 2023-10-28 11:37:30
tags: 数据库
categories: 
  - 数据库
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/preview.jpg
---

# ElasticSearch 实战

## 概述

### 简介



The Elastic Stack, 包括 Elasticsearch、 Kibana、 Beats 和 Logstash（也称为 ELK Stack）。能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化

Elaticsearch，简称为 ES， ES 是一个开源的高扩展的分布式全文搜索引擎， 是整个 ElasticStack 技术栈的核心

它可以近乎实时的存储、检索数据；本身扩展性很好，可以扩展到上百台服务器，处理 PB 级别的数据



------

### 安装



官方网站：https://www.elastic.co/cn/

官方文档：https://www.elastic.co/guide/index.html

安装教程：https://blog.csdn.net/qq_46533159/article/details/127916528



------

### Restful 和 JSON



REST 指的是一组架构约束条件和原则。满足这些约束条件和原则的应用程序或设计就是 RESTful。 Web 应用程序最重要的 REST 原则是，客户端和服务器之间的交互在请求之间是无状态的。从客户端到服务器的每个请求都必须包含理解请求所必需的信息。如果服务器在请求之间的任何时间点重启，客户端不会得到通知。此外，无状态请求可以由任何可用服务器回答，这十分适合云计算之类的环境。客户端可以缓存数据以改进性能

在服务器端，应用程序状态和功能可以分为各种资源。资源是一个有趣的概念实体，它向客户端公开。资源的例子有：应用程序对象、数据库记录、算法等等。每个资源都使用 URI(Universal Resource Identifier) 得到一个唯一的地址。所有资源都共享统一的接口，以便在客户端和服务器之间传输状态。使用的是标准的 HTTP 方法，比如 GET、 PUT、 POST 和 DELETE

在 REST 样式的 Web 服务中，每个资源都有一个地址。资源本身都是方法调用的目
标，方法列表对所有资源都是一样的。这些方法都是标准方法，包括 HTTP GET、 POST、PUT、 DELETE，还可能包括 HEAD 和 OPTIONS。简单的理解就是，如果想要访问互联网上的资源，就必须向资源所在的服务器发出请求，请求体中必须包含资源的网络路径， 以及对资源进行的操作 (增删改查)

REST 样式的 Web 服务若有返回结果，大多数以 JSON 字符串形式返回



------

### 倒排索引



搜索的核心目标实际上是保证搜索的效果和性能，为了高效的实现全文检索，我们可以通过倒排索引来解决

倒排索引是区别于正排索引的概念：

* 正排索引：是以文档对象的唯一 ID 作为索引，以文档内容作为记录
* 倒排索引：Inverted index，指的是将文档内容中的单词作为索引，将包含该词的文档 ID 作为记录

![image-20230421235751884](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230421235751884.png)





------

## 入门

### 简介



Elasticsearch 是面向文档型数据库，一条数据在这里就是一个文档。 为了方便大家理解，我们将 Elasticsearch 里存储文档数据和关系型数据库 MySQL 存储数据的概念进行一个类比

![image-20230422125022127](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230422125022127.png)

ES 里的 Index 可以看做一个库，而 Types 相当于表， Documents 则相当于表的行。这里 Types 的概念已经被逐渐弱化， Elasticsearch 6.X 中，一个 index 下已经只能包含一个 type， Elasticsearch 7.X 中, Type 的概念已经被删除了



------

### 创建索引



对比关系型数据库，创建索引就等同于创建数据库

在 Postman 中，向 ES 服务器发 PUT 请求 ： http://127.0.0.1:9200/shopping

请求后，服务器返回响应，表示创建成功：

```json
{
    "acknowledged": true,//响应结果
    "shards_acknowledged": true,//分片结果
    "index": "shopping"//索引名称
}
```

如果重复发 PUT 请求 ： http://127.0.0.1:9200/shopping 添加索引，会返回错误信息 :

```json
{
    "error": {
        "root_cause": [
            {
                "type": "resource_already_exists_exception",
                "reason": "index [shopping/J0WlEhh4R7aDrfIc3AkwWQ] already exists",
                "index_uuid": "J0WlEhh4R7aDrfIc3AkwWQ",
                "index": "shopping"
            }
        ],
        "type": "resource_already_exists_exception",
        "reason": "index [shopping/J0WlEhh4R7aDrfIc3AkwWQ] already exists",
        "index_uuid": "J0WlEhh4R7aDrfIc3AkwWQ",
        "index": "shopping"
    },
    "status": 400
}
```



------

### 查询和删除索引



查询所有的索引需要在 Postman 中，向 ES 服务器发 GET 请求 ： http://127.0.0.1:9200/_cat/indices?v

会返回如下信息：

```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   shopping J0WlEhh4R7aDrfIc3AkwWQ   1   1          0            0       208b           208b
```



如果只想查看单个索引的话，在 Postman 中，向 ES 服务器发 GET 请求 ： http://127.0.0.1:9200/shopping

返回及如果如下：

```json
{
    "shopping": {//索引名
        "aliases": {},//别名
        "mappings": {},//映射
        "settings": {//设置
            "index": {//设置 - 索引
                "creation_date": "1617861426847",//设置 - 索引 - 创建时间
                "number_of_shards": "1",//设置 - 索引 - 主分片数量
                "number_of_replicas": "1",//设置 - 索引 - 主分片数量
                "uuid": "J0WlEhh4R7aDrfIc3AkwWQ",//设置 - 索引 - 主分片数量
                "version": {//设置 - 索引 - 主分片数量
                    "created": "7080099"
                },
                "provided_name": "shopping"//设置 - 索引 - 主分片数量
            }
        }
    }
}
```



如果想删除索引，则需要发出 DELETE 请求，http://127.0.0.1:9200/shopping

```json
{
    "acknowledged": true
}
```



------

### 创建文档



假设索引已经创建好了，接下来我们来创建文档，并添加数据。这里的文档可以类比为关系型数据库中的表数据，添加的数据格式为 JSON 格式

在 Postman 中，向 ES 服务器发 POST 请求 ： http://127.0.0.1:9200/shopping/_doc，请求体 JSON 内容为：

```json
{
    "title":"小米手机",
    "category":"小米",
    "images":"http://www.gulixueyuan.com/xm.jpg",
    "price":3999.00
}
```

注意，此处发送请求的方式必须为 POST，不能是 PUT，否则会发生错误

返回结果：

```json
{
    "_index": "shopping",//索引
    "_type": "_doc",//类型-文档
    "_id": "ANQqsHgBaKNfVnMbhZYU",//唯一标识，可以类比为 MySQL 中的主键，随机生成
    "_version": 1,//版本
    "result": "created",//结果，这里的 create 表示创建成功
    "_shards": {//
        "total": 2,//分片 - 总数
        "successful": 1,//分片 - 总数
        "failed": 0//分片 - 总数
    },
    "_seq_no": 0,
    "_primary_term": 1
}
```

上面的数据创建后，由于没有指定数据唯一性标识（ID），默认情况下， ES 服务器会随机生成一个。

如果想要自定义唯一性标识，需要在创建时指定： http://127.0.0.1:9200/shopping/_doc/1，请求体JSON内容为：

```json
{
    "title":"小米手机",
    "category":"小米",
    "images":"http://www.gulixueyuan.com/xm.jpg",
    "price":3999.00
}
```

返回结果如下：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",//<------------------自定义唯一性标识
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 1,
    "_primary_term": 1
}
```

需要注意的是，如果增加数据时明确数据主键，那么请求方式也可以为 PUT



------

### 查询文档



查看文档时，需要指明文档的唯一性标识，类似于 MySQL 中数据的主键查询

在 Postman 中，向 ES 服务器发 GET 请求 ： http://127.0.0.1:9200/shopping/_doc/1 

返回结果如下：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version": 1,
    "_seq_no": 1,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "title": "小米手机",
        "category": "小米",
        "images": "http://www.gulixueyuan.com/xm.jpg",
        "price": 3999
    }
}
```

查找不存在的内容，向 ES 服务器发 GET 请求 ： http://127.0.0.1:9200/shopping/_doc/1001

返回结果如下：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1001",
    "found": false
}
```

查看索引下所有数据，向 ES 服务器发 GET 请求 ： http://127.0.0.1:9200/shopping/_search

返回结果如下：

```json
{
    "took": 133,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 2,
            "relation": "eq"
        },
        "max_score": 1,
        "hits": [
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "ANQqsHgBaKNfVnMbhZYU",
                "_score": 1,
                "_source": {
                    "title": "小米手机",
                    "category": "小米",
                    "images": "http://www.gulixueyuan.com/xm.jpg",
                    "price": 3999
                }
            },
            {
                "_index": "shopping",
                "_type": "_doc",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "title": "小米手机",
                    "category": "小米",
                    "images": "http://www.gulixueyuan.com/xm.jpg",
                    "price": 3999
                }
            }
        ]
    }
}
```



------

### 修改和删除文档



和新增文档一样，输入相同的 URL 地址请求，如果请求体变化，会将原有的数据内容覆盖

在 Postman 中，向 ES 服务器发 POST 请求 ： http://127.0.0.1:9200/shopping/_doc/1

请求体 JSON 内容为:

```json
{
    "title":"华为手机",
    "category":"华为",
    "images":"http://www.gulixueyuan.com/hw.jpg",
    "price":1999.00
}
```

修改成功后，服务器响应结果：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version": 2,
    "result": "updated",//<-----------updated 表示数据被更新
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 2,
    "_primary_term": 1
}
```



修改数据时，也可以只修改某一给条数据的局部信息

在 Postman 中，向 ES 服务器发 POST 请求 ： http://127.0.0.1:9200/shopping/_update/1

请求体JSON内容为:

```json
{
	"doc": {
		"title":"小米手机",
		"category":"小米"
	}
}
```

返回结果如下：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version": 3,
    "result": "updated",//<-----------updated 表示数据被更新
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 3,
    "_primary_term": 1
}
```



删除一个文档不会立即从磁盘上移除，它只是被标记成已删除（逻辑删除）

在 Postman 中，向 ES 服务器发 DELETE 请求 ： http://127.0.0.1:9200/shopping/_doc/1

返回结果：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version": 4,
    "result": "deleted",//<---删除成功
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 4,
    "_primary_term": 1
}
```



------

### 条件查询、分页和排序



查找 category 为小米的文档，在 Postman 中，向 ES 服务器发 GET 请求 ： http://127.0.0.1:9200/shopping/_search，附带 JSON 体如下：

```json
{
	"query":{
		"match":{
			"category":"小米"
		}
	}
}
```



查找所有文档内容，也可以这样，在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match_all":{}
	}
}
```



如果你想查询指定字段，在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match_all":{}
	},
	"_source":["title"]
}
```



如果想要分页查询的话，在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match_all":{}
	},
	"from":0,
	"size":2
}
```



如果你想通过排序查出价格最高的手机，在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match_all":{}
	},
	"sort":{
		"price":{
			"order":"desc"
		}
	}
}
```



------

### 条件查询和范围查询



假设想找出小米牌子，价格为3999元的。（must相当于数据库的&&）

在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"bool":{
			"must":[{
				"match":{
					"category":"小米"
				}
			},{
				"match":{
					"price":3999.00
				}
			}]
		}
	}
}
```



假设想找出小米和华为的牌子。（should相当于数据库的||）

在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"bool":{
			"should":[{
				"match":{
					"category":"小米"
				}
			},{
				"match":{
					"category":"华为"
				}
			}]
		}
}
```



假设想找出小米和华为的牌子，价格大于2000元的手机

在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"bool":{
			"should":[{
				"match":{
					"category":"小米"
				}
			},{
				"match":{
					"category":"华为"
				}
			}],
            "filter":{
            	"range":{
                	"price":{
                    	"gt":2000
                	}
	            }
    	    }
		}
	}
}
```



------

### 全文检索和聚合查询



全文检索像搜索引擎那样，如品牌输入“小华”，返回结果带回品牌有“小米”和华为的。

在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match":{
			"category" : "小华"
		}
	}
}
```



如果需要完全匹配，在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match_phrase":{
			"category" : "为"
		}
	}
}
```



如果需要高亮显示，在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"query":{
		"match_phrase":{
			"category" : "为"
		}
	},
    "highlight":{
        "fields":{
            "category":{}//<----高亮这字段
        }
    }
}
```



聚合允许使用者对 es 文档进行统计分析，类似与关系型数据库中的 group by，当然还有很多其他的聚合，例如取最大值max、平均值avg等等。

接下来按price字段进行分组：

在 Postman 中，向 ES 服务器发 GET请求 ： http://127.0.0.1:9200/shopping/_search，附带JSON体如下：

```json
{
	"aggs":{//聚合操作
		"price_group":{//名称，随意起名
			"terms":{//分组
				"field":"price"//分组字段
			}
		}
	}
}
```



------

## 进阶

### 系统架构



![image-20230522234021493](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230522234021493.png)



一个运行中的 Elasticsearch 实例称为一个节点，而集群是由一个或者多个拥有相同 cluster.name 配置的节点组成， 它们共同承担数据和负载的压力。当有节点加入集群中或者从集群中移除节点时，集群将会重新平均分布所有的数据

当一个节点被选举成为主节点时， 它将负责管理集群范围内的所有变更，例如增加、删除索引，或者增加、删除节点等。 而主节点并不需要涉及到文档级别的变更和搜索等操作，所以当集群只拥有一个主节点的情况下，即使流量的增加它也不会成为瓶颈。 任何节点都可以成为主节点。如果集群只有一个节点，那么它同时也成为了主节点

作为用户，我们可以将请求发送到集群中的任何节点 ，包括主节点。 每个节点都知道任意文档所处的位置，并且能够将我们的请求直接转发到存储我们所需文档的节点。 无论我们将请求发送到哪个节点，它都能负责从各个包含我们所需文档的节点收集回数据，并将最终结果返回給客户端。 Elasticsearch 对这一切的管理都是透明的



------

### 故障转移



当集群中只有一个节点在运行时，意味着会有一个单点故障问题——没有冗余。 幸运的是，我们只需再启动一个节点即可防止数据丢失。当你在同一台机器上启动了第二个节点时，只要它和第一个节点有同样的 cluster.name 配置，它就会自动发现集群并加入到其中。但是在不同机器上启动节点的时候，为了加入到同一集群，你需要配置一个可连接到的单播主机列表。之所以配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上运行的节点才会自动组成集群

如果启动了第二个节点，集群将会拥有两个节点 : 所有主分片和副本分片都已被分配 



![image-20230522234452400](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230522234452400.png)

通过 elasticsearch-head 插件查看集群情况

![image-20230522234512373](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230522234510784.png)



集群健康值:green( 3 of 6 )：表示所有 6 个分片（包括 3 个主分片和 3 个副本分片）都在正常运行

第二个节点加入到集群后， 3 个副本分片将会分配到这个节点上——每 个主分片对应一个副本分片。这意味着当集群内任何一个节点出现问题时，我们的数据都完好无损。所 有新近被索引的文档都将会保存在主分片上，然后被并行的复制到对应的副本分片上。这就保证了我们 既可以从主分片又可以从副本分片上获得文档



------

### 水平扩容



怎样为我们的正在增长中的应用程序按需扩容呢？当启动了第三个节点，我们的集群将会拥有三个节点的集群 : 为了分散负载而对分片进行重新分配

![image-20230524000640460](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230524000640460.png)

Node 1 和 Node 2 上各有一个分片被迁移到了新的 Node 3 节点，现在每个节点上都拥有 2 个分片， 而不是之前的 3 个。 这表示每个节点的硬件资源（CPU, RAM, I/O）将被更少的分片所共享，每个分片的性能将会得到提升

分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力。 我们这个拥有 6 个分片（3 个主分片和 3 个副本分片）的索引可以最大扩容到 6 个节点，每个节点上存在一个分片，并且每个 分片拥有所在节点的全部资源



但是如果我们想要扩容超过 6 个节点怎么办呢？

主分片的数目在索引创建时就已经确定了下来。实际上，这个数目定义了这个索引能够存储的最大数据量。（实际大小取决于你的数据、硬件和使用场景） 但是，读操作搜索和返回数据可以同时被主分片或副本分片所处理，所以当你拥有越多的副本分片时，也将拥有越高的吞吐量

在运行中的集群上是可以动态调整副本分片数目的，我们可以按需伸缩集群。让我们把副本数从默认的 1 增加到 2

```
#PUT http://127.0.0.1:1001/users/_settings

{
    "number_of_replicas" : 2
}
```

users 索引现在拥有 9 个分片： 3 个主分片和 6 个副本分片。 这意味着我们可以将集群扩容到 9 个节点，每个节点上一个分片。相比原来 3 个节点时，集群搜索性能可以提升 3 倍

![image-20230524000843764](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230524000843764.png)



当然，如果只是在相同节点数目的集群上增加更多的副本分片并不能提高性能，因为每个分片从节点上获得的资源会变少。 你需要增加更多的硬件资源来提升吞吐量。但是更多的副本分片数提高了数据冗余量：按照上面的节点配置，我们可以在失去 2 个节点的情况下不丢失任何数据



------

### 应对故障



我们关闭第一个节点，这时集群的状态为:关闭了一个节点后的集群

![image-20230524001114509](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230524001114509.png)

我们关闭的节点是一个主节点。而集群必须拥有一个主节点来保证正常工作，所以发生的第一件事情就是选举一个新的主节点： Node 2 。在我们关闭 Node 1 的同时也失去了主分片 1 和 2 ，并且在缺失主分片的时候索引也不能正常工作。 如果此时来检查集群的状况，我们看到的状态将会为 red ：不是所有主分片都在正常工作

幸运的是，在其它节点上存在着这两个主分片的完整副本， 所以新的主节点立即将这些分片在 Node 2 和 Node 3 上对应的副本分片提升为主分片， 此时集群的状态将会为 yellow。这个提升主分片的过程是瞬间发生的，如同按下一个开关一般



为什么我们集群状态是 yellow 而不是 green 呢？

虽然我们拥有所有的三个主分片，但是同时设置了每个主分片需要对应 2 份副本分片，而此时只存在一份副本分片。 所以集群不能为 green 的状态，不过我们不必过于担心：如果我们同样关闭了 Node 2 ，我们的程序 依然可以保持在不丢任何数据的情况下运行，因为Node 3 为每一个分片都保留着一份副本

如果想回复原来的样子，要确保 Node-1 的配置文件有如下配置：

```
discovery.seed_hosts: ["localhost:9302", "localhost:9303"]
```

集群可以将缺失的副本分片再次进行分配，那么集群的状态也将恢复成之前的状态。 如果 Node 1 依然拥有着之前的分片，它将尝试去重用它们，同时仅从主分片复制发生了修改的数据文件。和之前的集群相比，只是 Master 节点切换了



------

### 路由计算与分片控制



当索引一个文档的时候，文档会被存储到一个主分片中。 Elasticsearch 如何知道一个文档应该存放到哪个分片中呢？当我们创建文档时，它如何决定这个文档应当被存储在分片 1 还是分片 2 中呢？首先这肯定不会是随机的，否则将来要获取文档的时候我们就不知道从何处寻找了。实际上，这个过程是根据下面这个公式决定的：

```
shard = hash(routing) % number_of_primary_shards
```

routing 是一个可变值，默认是文档的 _id ，也可以设置成一个自定义的值。 routing 通过 hash 函数生成一个数字，然后这个数字再除以 number_of_primary_shards （主分片的数量）后得到余数 。这个分布在 0 到 number_of_primary_shards-1 之间的余数，就是我们所寻求的文档所在分片的位置

![image-20230604220347480](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230604220347480.png)

这就解释了为什么我们要在创建索引的时候就确定好主分片的数量并且永远不会改变这个数量: 因为如果数量变化了，那么所有之前路由的值都会无效，文档也再也找不到了

所有的文档 API ( get . index . delete 、 bulk , update 以及 mget ）都接受一个叫做 routing 的路由参数，通过这个参数我们可以自定义文档到分片的映射。一个自定义的路由参数可以用来确保所有相关的文档—一例如所有属于同一个用户的文档——都被存储到同一个分片中





我们可以发送请求到集群中的任一节点。每个节点都有能力处理任意请求。每个节点都知道集群中任一文档位置，所以可以直接将请求转发到需要的节点上。在下面的例子中，如果将所有的请求发送到 Node 1001，我们将其称为协调节点 coordinating node

![image-20230604220503444](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230604220503444.png)

当发送请求的时候， 为了扩展负载，更好的做法是轮询集群中所有的节点



------

### 数据写流程



新建、更新和删除索引请求都是写操作， 必须在主分片上面完成之后才能被复制到相关的副本分片

![image-20230604220934091](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230604220934091.png)

在客户端收到成功响应时，文档变更已经在主分片和所有副本分片执行完成，变更是安全的。有一些可选的请求参数允许您影响这个过程，可能以数据安全为代价提升性能。这些选项很少使用，因为 Elasticsearch 已经很快，但是为了完整起见， 请参考以下内容：

* consistency

  即一致性。在默认设置下，即使仅仅是在试图执行一个写操作之前，主分片都会要求必须要有规定数量 quorum（或者换种说法，也即必须要有大多数）的分片副本处于活跃可用状态，才会去执行写操作（其中分片副本 可以是主分片或者副本分片）。这是为了避免在发生网络分区故障（network partition）的时候进行写操作，进而导致数据不一致。 规定数量即： int((primary + number_of_replicas) / 2 ) + 1

  consistency 参数的值可以设为：

  * one ：只要主分片状态 ok 就允许执行写操作
  * all：必须要主分片和所有副本分片的状态没问题才允许执行写操作
  * quorum：默认值为 quorum , 即大多数的分片副本状态没问题就允许执行写操作

  注意，规定数量的计算公式中 number_of_replicas 指的是在索引设置中的设定副本分片数，而不是指当前处理活动状态的副本分片数。如果你的索引设置中指定了当前索引拥有 3 个副本分片，那规定数量的计算结果即：int((1 primary + 3 replicas) / 2) + 1 = 3，如果此时你只启动两个节点，那么处于活跃状态的分片副本数量就达不到规定数量，也因此您将无法索引和删除任何文档

* timeout

  如果没有足够的副本分片会发生什么？Elasticsearch 会等待，希望更多的分片出现。默认情况下，它最多等待 1 分钟。 如果你需要，你可以使用 timeout 参数使它更早终止：100 是 100 毫秒，30s 是 30 秒

新索引默认有 1 个副本分片，这意味着为满足规定数量应该需要两个活动的分片副本。 但是，这些默认的设置会阻止我们在单一节点上做任何事情。为了避免这个问题，要求只有当 number_of_replicas 大于 1 的时候，规定数量才会执行



------

### 数据读流程



![image-20230604234639084](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230604234639084.png)

在处理读取请求时，协调结点在每次请求的时候都会通过轮询所有的副本分片来达到负载均衡。在文档被检索时，已经被索引的文档可能已经存在于主分片上但是还没有复制到副本分片。 在这种情况下，副本分片可能会报告文档不存在，但是主分片可能成功返回文档。 一旦索引请求成功返回给用户，文档在主分片和副本分片都是可用的



------

### 索引更新流程和批量操作流程



部分更新一个文档结合了先前说明的读取和写入流程：

![image-20230604235113134](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230604235113134.png)

部分更新一个文档的步骤如下：

1. 客户端向 Node 1 发送更新请求
2. 它将请求转发到主分片所在的 Node 3
3. Node 3 从主分片检索文档，修改 _source 字段中的 JSON，并且尝试重新索引主分片的文档。如果文档已经被另一个进程修改, 它会重试步骤 3 , 超过 retry_on_conflict 次后放弃
4. 如果 Node 3 成功地更新文档，它将新版本的文档并行转发到 Node 1 和 Node 2 上的副本分片，重新建立索引。一旦所有副本分片都返回成功，Node 3 向协调节点也返回成功，协调节点向客户端返回成功

当主分片把更改转发到副本分片时， 它不会转发更新请求。 相反，它转发完整文档的新版本。请记住，这些更改将会异步转发到副本分片，并且不能保证它们以发送它们相同的顺序到达。 如果 Elasticsearch 仅转发更改请求，则可能以错误的顺序应用更改，导致得到损坏的文档



mget 和 bulk API 的模式类似于单文档模式。区别在于协调节点知道每个文档存在于哪个分片中。它将整个多文档请求分解成每个分片的多文档请求，并且将这些请求并行转发到每个参与节点

协调节点一旦收到来自每个节点的应答，就将每个节点的响应收集整理成单个响应，返回给客户端

![image-20230605232544043](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230605232544043.png)

用单个 mget 请求取回多个文档所需的步骤顺序:

1. 客户端向 Node 1 发送 mget 请求
2. Node 1 为每个分片构建多文档获取请求，然后并行转发这些请求到托管在每个所需的主分片或者副本分片的节点上。一旦收到所有答复，Node 1 构建响应并将其返回给客户端



bulk API， 允许在单个批量请求中执行多个创建、索引、删除和更新请求

![image-20230605232652799](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230605232652799.png)

bulk API 按如下步骤顺序执行：

1. 客户端向 Node 1 发送 bulk 请求
2. Node 1 为每个节点创建一个批量请求，并将这些请求并行转发到每个包含主分片的节点主机
3. 主分片一个接一个按顺序执行每个操作。当每个操作成功时, 主分片并行转发新文档（或删除）到副本分片，然后执行下一个操作。一旦所有的副本分片报告所有操作成功，该节点将向协调节点报告成功，协调节点将这些响应收集整理并返回给客户端



------

### 近实时搜索



随着按段（per-segment）搜索的发展，一个新的文档从索引到可被搜索的延迟显著降低了。新文档在几分钟之内即可被检索，但这样还是不够快。磁盘在这里成为了瓶颈。提交（Commiting）一个新的段到磁盘需要一个 fsync 来确保段被物理性地写入磁盘，这样在断电的时候就不会丢失数据。但是 fsync 操作代价很大；如果每次索引一个文档都去执行一次的话会造成很大的性能问题

我们需要的是一个更轻量的方式来使一个文档可被搜索，这意味着 fsync 要从整个过程中被移除。在 Elasticsearch 和磁盘之间是文件系统缓存。像之前描述的一样，在内存索引缓冲区中的文档会被写入到一个新的段中。但是这里新段会被先写入到文件系统缓存—这一步代价会比较低，稍后再被刷新到磁盘—这一步代价比较高。不过只要文件已经在缓存中，就可以像其它文件一样被打开和读取了

![image-20230610101903199](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230610101903199.png)



Lucene 允许新段被写入和打开，使其包含的文档在未进行一次完整提交时便对搜索可见。这种方式比进行一次提交代价要小得多，并且在不影响性能的前提下可以被频繁地执行

![image-20230610101928061](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230610101928061.png)

在 Elasticsearch 中，写入和打开一个新段的轻量的过程叫做 refresh。默认情况下每个分片会每秒自动刷新一次。这就是为什么我们说 Elasticsearch 是近实时搜索：文档的变化并不是立即对搜索可见，但会在一秒之内变为可见

这些行为可能会对新用户造成困惑：他们索引了一个文档然后尝试搜索它，但却没有搜到。这个问题的解决办法是用 refresh API 执行一次手动刷新：/usersl_refresh

尽管刷新是比提交轻量很多的操作，它还是会有性能开销。当写测试的时候，手动刷新很有用，但是不要在生产环境下每次索引一个文档都去手动刷新。相反，你的应用需要意识到 Elasticsearch 的近实时的性质，并接受它的不足



并不是所有的情况都需要每秒刷新。可能你正在使用 Elasticsearch 索引大量的日志文件，你可能想优化索引速度而不是近实时搜索，可以通过设置 refresh_interval ，降低每个索引的刷新频率

```json
{
    "settings": {
    	"refresh_interval": "30s"
    }
}
```

refresh_interval 可以在既存索引上进行动态更新。在生产环境中，当你正在建立一个大的新索引时，可以先关闭自动刷新，待开始使用该索引时，再把它们调回来

```json
# 关闭自动刷新
PUT /users/_settings
{ "refresh_interval": -1 }

# 每一秒刷新
PUT /users/_settings
{ "refresh_interval": "1s" }
```



------

### 持久化变更



如果没有用 fsync 把数据从文件系统缓存刷（flush）到硬盘，我们不能保证数据在断电甚至是程序正常退出之后依然存在。为了保证 Elasticsearch 的可靠性，需要确保数据变化被持久化到磁盘。在动态更新索引，我们说一次完整的提交会将段刷到磁盘，并写入一个包含所有段列表的提交点。Elasticsearch 在启动或重新打开一个索引的过程中使用这个提交点来判断哪些段隶属于当前分片

即使通过每秒刷新 (refresh）实现了近实时搜索，我们仍然需要经常进行完整提交来确保能从失败中恢复。但在两次提交之间发生变化的文档怎么办? 我们也不希望丢失掉这些数据。Elasticsearch 增加了一个 translog ，或者叫事务日志，在每一次对 Elasticsearch 进行操作时均进行了日志记录



整个流程如下：

1. 一个文档被索引之后，就会被添加到内存缓冲区，并且追加到了 translog

   ![image-20230618215933275](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230618215933275.png)

2. 分片每秒被刷新（refresh）一次，这些在内存缓冲区的文档被写入到一个新的段中，且没有进行 fsync 操作，这个段被打开，使其可被搜索。内存缓冲区被清空

   ![image-20230618220049539](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230618220049539.png)

3. 这个进程继续工作，更多的文档被添加到内存缓冲区和追加到事务日志

   ![image-20230618220111343](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230618220111343.png)

4. 每隔一段时间—例如 translog 变得越来越大，索引被刷新（flush）；一个新的 translog 被创建，并且一个全量提交被执行。所有在内存缓冲区的文档都被写入一个新的段。缓冲区被清空。一个提交点被写入硬盘。文件系统缓存通过 fsync 被刷新（flush） 。老的 translog 被删除





translog 提供所有还没有被刷到磁盘的操作的一个持久化纪录。当 Elasticsearch 启动的时候，它会从磁盘中使用最后一个提交点去恢复己知的段，并且会重放 translog 中所有在最后一次提交后发生的变更操作

translog 也被用来提供实时 CRUD。当你试着通过 ID 查询、更新、删除一个文档，它会在尝试从相应的段中检索之前，首先检查 translog 任何最近的变更。这意味着它总是能够实时地获取到文档的最新版本

![image-20230618220235387](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230618220235387.png)



执行一个提交并且截断 translog 的行为在 Elasticsearch 被称作一次 flush。分片每 30 分钟被自动刷新（flush)，或者在 translog 太大的时候也会刷新

你很少需要自己手动执行 flush 操作，通常情况下，自动刷新就足够了。这就是说，在重启节点或关闭索引之前执行 flush 有益于你的索引。当 Elasticsearch 尝试恢复或重新打开一个索引，它需要重放 translog 中所有的操作，所以如果日志越短，恢复越快

translog 的目的是保证操作不会丢失，在文件被 fsync 到磁盘前，被写入的文件在重启之后就会丢失。默认 translog 是每 5 秒被 fsync 刷新到硬盘，或者在每次写请求完成之后执行（e.g. index, delete, update, bulk）。这个过程在主分片和复制分片都会发生。最终，基本上，这意味着在整个请求被 fsync 到主分片和复制分片的 translog 之前，你的客户端不会得到一个 200 OK 响应

在每次请求后都执行一个 fsync 会带来一些性能损失，尽管实践表明这种损失相对较小（特别是 bulk 导入，它在一次请求中平摊了大量文档的开销）

但是对于一些大容量的偶尔丢失几秒数据问题也并不严重的集群，使用异步的 fsync 还是比较有益的。比如，写入的数据被缓存到内存中，再每 5 秒执行一次 fsync 。如果你决定使用异步 translog 的话，你需要保证在发生 crash 时，丢失掉 sync_interval 时间段的数据也无所谓。请在决定前知晓这个特性。如果你不确定这个行为的后果，最好是使用默认的参数 {“index.translog.durability”: “request”} 来避免数据丢失



------

### 段合并



由于自动刷新流程每秒会创建一个新的段，这样会导致短时间内的段数量暴增。而段数目太多会带来较大的麻烦。每一个段都会消耗文件句柄、内存和 cpu 运行周期。更重要的是，每个搜索请求都必须轮流检查每个段；所以段越多，搜索也就越慢

Elasticsearch 通过在后台进行段合并来解决这个问题。小的段被合并到大的段，然后这些大的段再被合并到更大的段

段合并的时候会将那些旧的已删除文档从文件系统中清除。被删除的文档（或被更新文档的旧版本）不会被拷贝到新的大段中

启动段合并不需要你做任何事。进行索引和搜索时会自动进行



------

### 文档分析



分析包含下面的过程：

* 将一块文本分成适合于倒排索引的独立的词条
* 将这些词条统一化为标准格式以提高它们的“可搜索性”，或者 recall



分析器执行上面的工作。分析器实际上是将三个功能封装到了一个包里：

* 字符过滤器：首先，字符串按顺序通过每个 字符过滤器 。他们的任务是在分词前整理字符串。一个字符过滤器可以用来去掉 HTML，或者将 & 转化成 and
* 分词器：其次，字符串被分词器分为单个的词条。一个简单的分词器遇到空格和标点的时候，可能会将文本拆分成词条
* Token 过滤器：最后，词条按顺序通过每个 token 过滤器 。这个过程可能会改变词条（例如，小写化 Quick ），删除词条（例如， 像 a， and， the 等无用词），或者增加词条（例如，像 jump 和 leap 这种同义词）



ES 的默认分词器无法识别中文中测试、 单词这样的词汇，而是简单的将每个字拆完分为一个词

这样的结果显然不符合我们的使用要求，所以我们需要下载 ES 对应版本的中文分词器

[IK 中文分词器下载网址](https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.8.0)

将解压后的后的文件夹放入 ES 根目录下的 plugins 目录下，重启 ES 即可使用



ES 中也可以进行扩展词汇。比如我们添加了一个索引"弗雷尔卓德"，默认情况下，分词器会将其分为 5 个字。我们需要让分词器意识到，这是一个完整的词

1. 首先进入 ES 根目录中的 plugins 文件夹下的 ik 文件夹，进入 config 目录，创建 custom.dic文件，写入“弗雷尔卓德”
2. 同时打开 IKAnalyzer.cfg.xml 文件，将新建的 custom.dic 配置其中
3. 重启 ES 服务器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict">custom.dic</entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">words_location</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```



------

### 文档控制



当我们使用 index API 更新文档，可以一次性读取原始文档，做我们的修改，然后重新索引整个文档。最近的索引请求将获胜：无论最后哪一个文档被索引，都将被唯一存储在 Elasticsearch 中。如果其他人同时更改这个文档，他们的更改将丢失

很多时候这是没有问题的。也许我们的主数据存储是一个关系型数据库，我们只是将数据复制到 Elasticsearch 中并使其可被搜索。也许两个人同时更改相同的文档的几率很小。或者对于我们的业务来说偶尔丢失更改并不是很严重的问题

但有时丢失了一个变更就是非常严重的。试想我们使用 Elasticsearch 存储我们网上商城商品库存的数量，每次我们卖一个商品的时候，我们在 Elasticsearch 中将库存数量减少。有一天，管理层决定做一次促销。突然地，我们一秒要卖好几个商品。假设有两个 web 程序并行运行，每一个都同时处理所有商品的销售



web_1 对 stock_count 所做的更改已经丢失，因为 web_2 不知道它的 stock_count 的拷贝已经过期。结果我们会认为有超过商品的实际数量的库存，因为卖给顾客的库存商品并不存在，我们将让他们非常失望

变更越频繁，读数据和更新数据的间隙越长，也就越可能丢失变更。在数据库领域中，有两种方法通常被用来确保并发更新时变更不会丢失：

* 悲观并发控制：这种方法被关系型数据库广泛使用，它假定有变更冲突可能发生，因此阻塞访问资源以防止冲突。一个典型的例子是读取一行数据之前先将其锁住，确保只有放置锁的线程能够对这行数据进行修改悲观并发控制：这种方法被关系型数据库广泛使用，它假定有变更冲突可能发生，因此阻塞访问资源以防止冲突。一个典型的例子是读取一行数据之前先将其锁住，确保只有放置锁的线程能够对这行数据进行修改
* 乐观并发控制：Elasticsearch 中使用的这种方法假定冲突是不可能发生的，并且不会阻塞正在尝试的操作。然而，如果源数据在读写当中被修改，更新将会失败。应用程序接下来将决定该如何解决冲突。例如，可以重试更新、使用新的数据、或者将相关情况报告给用户





内部系统版本控制

Elasticsearch 是分布式的。当文档创建、更新或删除时，新版本的文档必须复制到集群中的其他节点。Elasticsearch 也是异步和并发的，这意味着这些复制请求被并行发送，并且到达目的地时也许顺序是乱的。Elasticsearch 需要一种方法确保文档的旧版本不会覆盖新的版本

当我们之前讨论 index , GET 和 DELETE 请求时，我们指出每个文档都有一个 _version（版本号），当文档被修改时版本号递增。Elasticsearch 使用这个 version 号来确保变更以正确顺序得到执行。如果旧版本的文档在新版本之后到达，它可以被简单的忽略

我们可以利用 version 号来确保应用中相互冲突的变更不会导致数据丢失。我们通过指定想要修改文档的 version 号来达到这个目的。如果该版本不是当前版本号，我们的请求将会失败

老的版本 es 使用 version，但是新版本不支持了，会报下面的错误，提示我们用 if_seq _no 和 if _primary_term

```json
{
    "error": {
        "root_cause": [
            {
                "type": "action_request_validation_exception",
                "reason": "Validation Failed: 1: internal versioning can not be used for optimistic concurrency control. Please use `if_seq_no` and `if_primary_term` instead;"
            }
        ],
        "type": "action_request_validation_exception",
        "reason": "Validation Failed: 1: internal versioning can not be used for optimistic concurrency control. Please use `if_seq_no` and `if_primary_term` instead;"
    },
    "status": 400
}
```

```json
#POST http://127.0.0.1:9200/shopping/_update/1001?if_seq_no=11&if_primary_term=15
{
    "doc":{
        "title":"华为手机2"
    }
}
```





外部系统版本控制

一个常见的设置是使用其它数据库作为主要的数据存储，使用 Elasticsearch 做数据检索，这意味着主数据库的所有更改发生时都需要被复制到 Elasticsearch，如果多个进程负责这一数据同步，你可能遇到类似于之前描述的并发问题

如果你的主数据库已经有了版本号，或一个能作为版本号的字段值比如 timestamp，那么你就可以在 Elasticsearch 中通过增加 version_type=extermal 到查询字符串的方式重用这些相同的版本号，版本号必须是大于零的整数，且小于 9.2E+18，一个 Java 中 long 类型的正值

外部版本号的处理方式和我们之前讨论的内部版本号的处理方式有些不同，Elasticsearch 不是检查当前 _version 和请求中指定的版本号是否相同，而是检查当前 _version 是否小于指定的版本号。如果请求成功，外部的版本号作为文档的新 _version 进行存储



------

### 文档展示 Kibana



Kibana 是一个免费且开放的用户界面，能够让你对 Elasticsearch 数据进行可视化，并让你在 Elastic Stack 中进行导航。你可以进行各种操作，从跟踪查询负载，到理解请求如何流经你的整个应用，都能轻松完成

[Kibana下载地址](https://www.elastic.co/cn/downloads/kibana)

1. 解压缩下载的 zip 文件

2. 修改 config/kibana.yml 文件

   ```yaml
   # 默认端口
   server.port: 5601
   # ES 服务器的地址
   elasticsearch.hosts: ["http://localhost:9200"]
   # 索引名
   kibana.index: ".kibana"
   # 支持中文
   i18n.locale: "zh-CN"
   ```

3. Windows 环境下执行 bin/kibana.bat 文件Windows 环境下执行 bin/kibana.bat 文件

4. 通过浏览器访问：http://localhost:5601

   ![image-20230723234938833](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230723234938833.png)



------

## 集成

### 与 SpringBoot 集成



1. 添加依赖

   ```xml
   <dependency>
   	<groupId>org.springframework.boot</groupId>
   	<artifactId>spring-boot-starter-data-elasticsearch</artifactId>
   </dependency>
   ```

2. 添加配置

   ```yaml
   # es 服务地址
   elasticsearch.host=127.0.0.1
   # es 服务端口
   elasticsearch.port=9200
   # 配置日志级别,开启 debug 日志
   logging.level.com.atguigu.es=debug
   ```

3. 创建实体类

   ```java
   @Data
   @Document(indexName = "shopping", shards = 3, replicas = 1)
   public class Product {
       //必须有 id,这里的 id 是全局唯一的标识，等同于 es 中的"_id"
       @Id
       private Long id;//商品唯一标识
   
       /**
        * type : 字段数据类型
        * analyzer : 分词器类型
        * index : 是否索引(默认:true)
        * Keyword : 短语,不进行分词
        */
       @Field(type = FieldType.Text, analyzer = "ik_max_word")
       private String title;//商品名称
   
       @Field(type = FieldType.Keyword)
       private String category;//分类名称
   
       @Field(type = FieldType.Double)
       private Double price;//商品价格
   
       @Field(type = FieldType.Keyword, index = false)
       private String images;//图片地址
   }
   ```

4. 创建 DAO

   ```java
   @Repository
   public interface ProductDao extends ElasticsearchRepository<Product, Long>{
   
   }
   ```

5. 代码测试

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest
   public class SpringDataESIndexTest {
       //注入 ElasticsearchRestTemplate
       @Autowired
       private ElasticsearchRestTemplate elasticsearchRestTemplate;
       //创建索引并增加映射配置
       @Test
       public void createIndex(){
           //创建索引，系统初始化会自动创建索引
           System.out.println("创建索引");
       }
   
       @Test
       public void deleteIndex(){
           //创建索引，系统初始化会自动创建索引
           boolean flg = elasticsearchRestTemplate.deleteIndex(Product.class);
           System.out.println("删除索引 = " + flg);
       }
       
       @Test
       public void termQuery(){
           TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("title", "小米");
                   Iterable<Product> products = productDao.search(termQueryBuilder);
           for (Product product : products) {
               System.out.println(product);
           }
       }
   }
   ```




------

## 优化

### 硬件选择



Elasticsearch 的基础是 Lucene，所有的索引和文档数据是存储在本地的磁盘中，具体的路径可在 ES 的配置文件…/config/elasticsearch.yml 中配置，如下：

```yml
# Path to directory where to store the data (separate multiple locations by comma):
path.data: /path/to/data

# Path to log files:
path.logs: /path/to/logs
```



磁盘在现代服务器上通常都是瓶颈。Elasticsearch 重度使用磁盘，你的磁盘能处理的吞吐量越大，你的节点就越稳定。这里有一些优化磁盘 I/O 的技巧：

* 使用 SSD，就像其他地方提过的，他们比机械磁盘优秀多了
* 使用 RAID0。条带化 RAID 会提高磁盘 IO，代价显然就是当一块硬盘故障时整个就故障了。不要使用镜像或者奇偶校验 RAID，因为副本已经提供了这个功能
* 另外，使用多块硬盘，并允许 Elasticsearch 通过多个 path data 目录配置把数据条带化分配到它们上面
* 不要使用远程挂载的存储，比如 NFS 或者 SMB/CIFS。这个引入的延迟对性能来说完全是背道而驰的



------

### 分片策略



**合理设置分片数**

分片和副本的设计为 ES 提供了支持分布式和故障转移的特性，但并不意味着分片和副本是可以无限分配的。而且索引的分片完成分配后由于索引的路由机制，我们是不能重新修改分片数的

一个分片并不是没有代价的。需要了解：

* 一个分片的底层即为一个 Lucene 索引，会消耗一定文件句柄、内存、以及 CPU 运转
* 每一个搜索请求都需要命中索引中的每一个分片，如果每一个分片都处于不同的节点还好， 但如果多个分片都需要在同一个节点上竞争使用相同的资源就有些糟糕了
* 用于计算相关度的词项统计信息是基于分片的。如果有许多分片，每一个都只有很少的数据会导致很低的相关度



一个业务索引具体需要分配多少分片可能需要架构师和技术人员对业务的增长有个预先的判断，横向扩展应当分阶段进行。为下一阶段准备好足够的资源。 只有当你进入到下一个阶段，你才有时间思考需要作出哪些改变来达到这个阶段。一般来说，我们遵循一些原则：

* 控制每个分片占用的硬盘容量不超过 ES 的最大 JVM 的堆空间设置（一般设置不超过 32G，参考下文的 JVM 设置原则），因此，如果索引的总容量在 500G 左右，那分片大小在 16 个左右即可；当然，最好同时考虑原则 2
* 考虑一下 node 数量，一般一个节点有时候就是一台物理机，如果分片数过多，大大超过了节点数，很可能会导致一个节点上存在多个分片，一旦该节点故障，即使保持了 1 个以上的副本，同样有可能会导致数据丢失，集群无法恢复。所以， 一般都设置分片数不超过节点数的 3 倍
* 主分片，副本和节点最大数之间数量，我们分配的时候可以参考以下关系：节点数 <= 主分片数 *（副本数 +1）



**推迟分片分配**

对于节点瞬时中断的问题，默认情况，集群会等待一分钟来查看节点是否会重新加入，如果这个节点在此期间重新加入，重新加入的节点会保持其现有的分片数据，不会触发新的分片分配。这样就可以减少 ES 在自动再平衡可用分片时所带来的极大开销

通过修改参数 delayed_timeout ，可以延长再均衡的时间，可以全局设置也可以在索引级别进行修改

```json
#PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```





------

### 路由选择



当我们查询文档的时候， Elasticsearch 如何知道一个文档应该存放到哪个分片中呢？它其实是通过下面这个公式来计算出来：

```
shard = hash(routing) % number_of_primary_shards
```

routing 默认值是文档的 id，也可以采用自定义值，比如用户 id



**不带 routing 查询**

在查询的时候因为不知道要查询的数据具体在哪个分片上，所以整个过程分为 2 个步骤：

* 分发：请求到达协调节点后，协调节点将查询请求分发到每个分片上
* 聚合：协调节点搜集到每个分片上查询结果，在将查询的结果进行排序，之后给用户返回结果



**带 routing 查询**

查询的时候，可以直接根据 routing 信息定位到某个分配查询，不需要查询所有的分配，经过协调节点排序。向上面自定义的用户查询，如果 routing 设置为 userid 的话，就可以直接查询出数据来，效率提升很多



------

### 优化写入速度



ES 的默认配置，是综合了数据可靠性、写入速度、搜索实时性等因素。实际使用时，我们需要根据公司要求，进行偏向性的优化

针对于搜索性能要求不高，但是对写入要求较高的场景，我们需要尽可能的选择恰当写优化策略。综合来说，可以考虑以下几个方面来提升写索引的性能：

* 加大 Translog Flush，目的是降低 Iops、Writeblock
* 增加 Index Refesh 间隔，目的是减少 Segment Merge 的次数
* 调整 Bulk 线程池和队列
* 优化节点间的任务分布
* 优化 Lucene 层的索引建立，目的是降低 CPU 及 IO



------

## 面试题

### ES 的 master 选举流程



* Elasticsearch 的选主是 ZenDiscovery 模块负责的，主要包含 Ping（节点之间通过这个 RPC 来发现彼此）和 Unicast（单播模块包含 - 一个主机列表以控制哪些节点需要 ping 通）这两部分
* 对所有可以成为 master 的节点（node master: true）根据 nodeId 字典排序，每次选举每个节点都把自己所知道节点排一次序，然后选出第一个（第 0 位）节点，暂且认为它是 master 节点
* 如果对某个节点的投票数达到一定的值（可以成为 master 节点数 n/2+1）并且该节点自己也选举自己，那这个节点就是 master。否则重新选举一直到满足上述条件
* master 节点的职责主要包括集群、节点和索引的管理，不负责文档级别的管理；data 节点可以关闭 http 功能



------

### ES 的集群脑裂问题



“脑裂”问题可能的成因：

* 网络问题：集群间的网络延迟导致一些节点访问不到 master, 认为 master 挂掉了从而选举出新的 master, 并对 master 上的分片和副本标红，分配新的主分片
* 节点负载：主节点的角色既为 master 又为 data, 访问量较大时可能会导致 ES 停止响应造成大面积延迟，此时其他节点得不到主节点的响应认为主节点挂掉了，会重新选取主节点
* 内存回收：data 节点上的 ES 进程占用的内存较大，引发 JVM 的大规模内存回收，造成 ES 进程失去响应



脑裂问题解决方案：

* 减少误判：discovery.zen ping_ timeout 节点状态的响应时间，默认为 3s，可以适当调大，如果 master 在该响应时间的范围内没有做出响应应答，判断该节点已经挂掉了。调大参数（如 6s，discovery.zen.ping_timeout:6），可适当减少误判
* 选举触发：discovery.zen.minimum. _master_ nodes:1，该参数是用于控制选举行为发生的最小集群主节点数量。当备选主节点的个数大于等于该参数的值，且备选主节点中有该参数个节点认为主节点挂了，进行选举。官方建议为 (n / 2) +1, n 为主节点个数（即有资格成为主节点的节点个数）
* 角色分离：即 master 节点与 data 节点分离，限制角色



------

### ES 索引文档的流程



![image-20230730160028311](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230730160028311.png)

* 协调节点默认使用文档 ID 参与计算（也支持通过 routing），以便为路由提供合适的分片：shard = hash(document_id) % (num_of_primary_shards)
* 当分片所在的节点接收到来自协调节点的请求后，会将请求写入到 Memory Buffer，然后定时（默认是每隔 1 秒）写入到 Filesystem Cache，这个从 Memory Buffer 到 Filesystem Cache 的过程就叫做 refresh
* 当然在某些情况下，存在 Momery Buffer 和 Filesystem Cache 的数据可能会丢失， ES 是通过 translog 的机制来保证数据的可靠性的。其实现机制是接收到请求后，同时也会写入到 translog 中，当 Filesystemcache 中的数据写入到磁盘中时，才会清除掉，这个过程叫做 flush
* 在 flush 过程中，内存中的缓冲将被清除，内容被写入一个新段，段的 fsync 将创建一个新的提交点，并将内容刷新到磁盘，旧的 translog 将被删除并开始一个新的 translog
* flush 触发的时机是定时触发（默认 30 分钟）或者 translog 变得太大（默认为 512M）时



------

### ES 更新和删除文档的流程



* 删除和更新也都是写操作，但是 Elasticsearch 中的文档是不可变的，因此不能被删除或者改动以展示其变更
* 磁盘上的每个段都有一个相应的.del 文件。当删除请求发送后，文档并没有真的被删除，而是在.del 文件中被标记为删除。该文档依然能匹配查询，但是会在结果中被过滤掉。当段合并时，在.del 文件中被标记为删除的文档将不会被写入新段
* 在新的文档被创建时， Elasticsearch 会为该文档指定一个版本号，当执行更新时，旧版本的文档在.del 文件中被标记为删除，新版本的文档被索引到一个新段。旧版本的文档依然能匹配查询，但是会在结果中被过滤掉



------

### ES 搜索的流程



![image-20230730160623558](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/ElasticSearch%20%E5%AE%9E%E6%88%98/image-20230730160623558.png)

* 搜索被执行成一个两阶段过程，我们称之为 Query Then Fetch
* 在初始查询阶段时，查询会广播到索引中每一个分片拷贝（主分片或者副本分片）。 每个分片在本地执行搜索并构建一个匹配文档的大小为 from + size 的优先队列。 PS：在搜索的时候是会查询Filesystem Cache 的，但是有部分数据还在 Memory Buffer，所以搜索是近实时的
* 每个分片返回各自优先队列中所有文档的 ID 和排序值给协调节点，它合并这些值到自己的优先队列中来产生一个全局排序后的结果列表
* 接下来就是取回阶段， 协调节点辨别出哪些文档需要被取回并向相关的分片提交多个 GET 请求。每个分片加载并丰富文档，如果有需要的话，接着返回文档给协调节点。一旦所有的文档都被取回了，协调节点返回结果给客户端
* Query Then Fetch 的搜索类型在文档相关性打分的时候参考的是本分片的数据，这样在文档数量较少的时候可能不够准确， DFS Query Then Fetch 增加了一个预查询的处理，询问 Term 和 Document frequency，这个评分更准确，但是性能会变差



------

### ES 在部署时，对 Linux 的设置有哪些优化方法



* 64 GB 内存的机器是非常理想的， 但是 32 GB 和 16 GB 机器也是很常见的。少于 8 GB 会适得其反
* 如果你要在更快的 CPU 和更多的核心之间选择，选择更多的核心更好。多个内核提供的额外并发远胜过稍微快一点点的时钟频率
* 如果你负担得起 SSD，它将远远超出任何旋转介质。 基于 SSD 的节点，查询和索引性能都有提升。如果你负担得起， SSD 是一个好的选择
* 即使数据中心们近在咫尺，也要避免集群跨越多个数据中心。绝对要避免集群跨越大的地理距离
* 请确保运行你应用程序的 JVM 和服务器的 JVM 是完全一样的。 在 Elasticsearch 的几个地方，使用 Java 的本地序列化
* 通过设置 gateway.recover_after_nodes、 gateway.expected_nodes、 gateway.recover_after_time 可以在集群重启的时候避免过多的分片交换，这可能会让数据恢复从数个小时缩短为几秒钟
* Elasticsearch 默认被配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上运行的节点才会自动组成集群。最好使用单播代替组播
* 不要随意修改垃圾回收器（CMS）和各个线程池的大小
* 把你的内存的（少于）一半给 Lucene（但不要超过 32 GB！），通过 ES_HEAP_SIZE 环境变量设置
* 内存交换到磁盘对服务器性能来说是致命的。如果内存交换到磁盘上，一个 100 微秒的操作可能变成 10 毫秒。 再想想那么多 10 微秒的操作时延累加起来。 不难看出 swapping 对于性能是多么可怕
* Lucene 使用了大量的文件。同时， Elasticsearch 在节点和 HTTP 客户端之间进行通信也使用了大量的套接字。 所有这一切都需要足够的文件描述符。你应该增加你的文件描述符，设置一个很大的值，如 64,000



------

### GC 方面，在使用 ES 时要注意什么



倒排词典的索引需要常驻内存，无法 GC，需要监控 data node 上 segment memory 增长趋势

各类缓存， field cache, filter cache, indexing cache, bulk queue 等等，要设置合理的大小，并且要应该根据最坏的情况来看 heap 是否够用，也就是各类缓存全部占满的时候，还有 heap 空间可以分配给其他任务吗？避免采用 clear cache 等“自欺欺人”的方式来释放内存

避免返回大量结果集的搜索与聚合。确实需要大量拉取数据的场景，可以采用 scan & scroll api 来实现

cluster stats 驻留内存并无法水平扩展，超大规模集群可以考虑分拆成多个集群通过 tribe node 连接

想知道 heap 够不够，必须结合实际应用场景，并对集群的 heap 使用情况做持续的监控



------

### ES 对于大数据量（上亿量级）的聚合如何实现



Elasticsearch 提供的首个近似聚合是 cardinality 度量。它提供一个字段的基数，即该字段的 distinct 或者 unique 值的数目。它是基于 HLL 算法的。 HLL 会先对我们的输入作哈希运算，然后根据哈希运算的结果中的 bits 做概率估算从而得到基数。其特点是：可配置的精度，用来控制内存的使用（更精确 ＝ 更多内存）；小的数据集精度是非常高的；我们可以通过配置参数，来设置去重需要的固定内存使用量。无论数千还是数十亿的唯一值，内存使用量只与你配置的精确度相关



------

### 在并发情况下， ES 如果保证读写一致



* 可以通过版本号使用乐观并发控制，以确保新版本不会被旧版本覆盖，由应用层来处理具体的冲突
* 另外对于写操作，一致性级别支持 quorum/one/all，默认为 quorum，即只有当大多数分片可用时才允许写操作。但即使大多数可用，也可能存在因为网络等原因导致写入副本失败，这样该副本被认为故障，分片将会在一个不同的节点上重建
* 对于读操作，可以设置 replication 为 sync(默认)，这使得操作在主分片和副本分片都完成后才会返回；如果设置 replication 为 async 时，也可以通过设置搜索请求参数 _preference 为 primary 来查询主分片，确保文档是最新版本



