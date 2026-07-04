---
title: 数据同步工具 DataX
date: 2025-02-09 13:56:36
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20DataX/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20DataX/preview.jpg
tags:
  - 大数据
  - 数据集成与同步
  - DataX
categories: 大数据
---



# 概述

## 什么是 DataX



DataX 是阿里巴巴开源的一个异构数据源离线同步工具，致力于实现包括关系型数据库 (MySQL、Oracle 等)、HDFS、Hive、ODPS、HBase、FTP 等各种异构数据源之间稳定高效的数据同步功能



------

## DataX 的设计



为了解决异构数据源同步问题，DataX 将复杂的网状的同步链路变成了星型数据链路，DataX 作为中间传输载体负责连接各种数据源。当需要接入一个新的数据源的时候，只需要将此数据源对接到 DataX，便能跟已有的数据源做到无缝数据同步

![image-20240620233732903](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20DataX/image-20240620233732903.png)



------

## 支持的数据源



DataX 目前已经有了比较全面的插件体系，主流的 RDBMS 数据库、NOSQL、大数据计算系统都已经接入

![image-20240620233932658](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20DataX/image-20240620233932658.png)



------

## 框架设计



![image-20240621000342972](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20DataX/image-20240621000342972.png)

Reader：数据采集模块，负责采集数据源的数据，将数据发送给 Framework

Writer：数据写入模块，负责不断向 Framework 取数据，并将数据写入到目的端

Framework：用于连接 reader 和 writer，作为两者的数据传输



------

## 运行原理



![image-20240621000655562](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/%E6%95%B0%E6%8D%AE%E9%9B%86%E6%88%90%E4%B8%8E%E5%90%8C%E6%AD%A5/%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E5%B7%A5%E5%85%B7%20DataX/image-20240621000655562.png)

 Job：单个作业的管理节点，负责数据清理、子任务划分、TaskGroup 监控管理

Task：由 Job 切分而来，是 DataX 作业的最小单元，每个 Task 负责一部分数据的同步工作

Schedule：将 Task 组成 TaskGroup，单个 TaskGroup 的并发数量为 5

TaskGroup：负责启动 Task

<br>

举例来说，用户提交了一个 DataX 作业，并且配置了 20 个并发，目的是将一个 100 张分表的 mysql 数据同步到 odps 里面。 DataX 的调度决策思路是： 

1. DataXJob 根据分库分表切分成了 100 个 Task
2. 根据 20 个并发，DataX 计算共需要分配 4 个 TaskGroup
3. 4 个 TaskGroup 平分切分好的 100 个 Task，每一个 TaskGroup 负责以 5 个并发共计运行 25 个 Task



------

# 快速入门

## 官方地址



下载地址：http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz

源码地址：https://github.com/alibaba/DataX



------

## 安装



1. 下载 DataX 镜像

   ```SH
   docker pull datax/datax-web
   ```

2. 创建并运行 DataX 容器

   ```SH
   docker run -d -p 8080:8080 --name datax-container datax/datax-web
   ```

3. 通过访问 http://localhost:8080/ 来验证 DataX 是否安装成功。如果 DataX-Web 运行正常，应该能看到 Web 管理界面



------

# 使用案例

## 从内存读取数据导入 MySQL



数据的读写格式都是通过 JSON 来配置的，DataX 官网提供了多种数据源的读写案例

这是一份从内存产生到 MySQL 导入的数据

```JSON
{
    "job": {
        "setting": {
            "speed": {
                "channel": 1
            }
        },
        "content": [
            {
                 "reader": {
                    "name": "streamreader",
                    "parameter": {
                        "column" : [
                            {
                                "value": "DataX",
                                "type": "string"
                            },
                            {
                                "value": 19880808,
                                "type": "long"
                            },
                            {
                                "value": "1988-08-08 08:08:08",
                                "type": "date"
                            },
                            {
                                "value": true,
                                "type": "bool"
                            },
                            {
                                "value": "test",
                                "type": "bytes"
                            }
                        ],
                        "sliceRecordCount": 1000
                    }
                },
                "writer": {
                    "name": "mysqlwriter",
                    "parameter": {
                        "writeMode": "insert",
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "name"
                        ],
                        "session": [
                        	"set session sql_mode='ANSI'"
                        ],
                        "preSql": [
                            "delete from test"
                        ],
                        "connection": [
                            {
                                "jdbcUrl": "jdbc:mysql://127.0.0.1:3306/datax?useUnicode=true&characterEncoding=gbk",
                                "table": [
                                    "test"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

<br>

JSON 文件编写好之后，通过以下脚本来执行任务

```SH
opt/module/datax/bin/datax.py ./mysql2stream.json 
```



------

## 从 txt 文件读取数据导入 HDFS



```JSON
{
    "setting": {},
    "job": {
        "setting": {
            "speed": {
                "channel": 2
            }
        },
        "content": [
            {
                "reader": {
                    "name": "txtfilereader",
                    "parameter": {
                        "path": ["/Users/shf/workplace/txtWorkplace/job/dataorcfull.txt"],
                        "encoding": "UTF-8",
                        "column": [
                            {
                                "index": 0,
                                "type": "long"
                            },
                            {
                                "index": 1,
                                "type": "long"
                            },
                            {
                                "index": 2,
                                "type": "long"
                            },
                            {
                                "index": 3,
                                "type": "long"
                            },
                            {
                                "index": 4,
                                "type": "DOUBLE"
                            },
                            {
                                "index": 5,
                                "type": "DOUBLE"
                            },
                            {
                                "index": 6,
                                "type": "STRING"
                            },
                            {
                                "index": 7,
                                "type": "STRING"
                            },
                            {
                                "index": 8,
                                "type": "STRING"
                            },
                            {
                                "index": 9,
                                "type": "BOOLEAN"
                            },
                            {
                                "index": 10,
                                "type": "date"
                            },
                            {
                                "index": 11,
                                "type": "date"
                            }
                        ],
                        "fieldDelimiter": "\t"
                    }
                },
                "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://xxx:port",
                        "fileType": "orc",
                        "path": "/user/hive/warehouse/writerorc.db/orcfull",
                        "fileName": "xxxx",
                        "column": [
                            {
                                "name": "col1",
                                "type": "TINYINT"
                            },
                            {
                                "name": "col2",
                                "type": "SMALLINT"
                            },
                            {
                                "name": "col3",
                                "type": "INT"
                            },
                            {
                                "name": "col4",
                                "type": "BIGINT"
                            },
                            {
                                "name": "col5",
                                "type": "FLOAT"
                            },
                            {
                                "name": "col6",
                                "type": "DOUBLE"
                            },
                            {
                                "name": "col7",
                                "type": "STRING"
                            },
                            {
                                "name": "col8",
                                "type": "VARCHAR"
                            },
                            {
                                "name": "col9",
                                "type": "CHAR"
                            },
                            {
                                "name": "col10",
                                "type": "BOOLEAN"
                            },
                            {
                                "name": "col11",
                                "type": "date"
                            },
                            {
                                "name": "col12",
                                "type": "TIMESTAMP"
                            }
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                        "compress":"NONE"
                    }
                }
            }
        ]
    }
}
```



------

## 把数据导入到 ES



```JSON
{
  "job": {
    "setting": {
        "speed": {
            "channel": 1
        }
    },
    "content": [
      {
        "reader": {
          ...
        },
        "writer": {
          "name": "elasticsearchwriter",
          "parameter": {
            "endpoint": "http://xxx:9999",
            "accessId": "xxxx",
            "accessKey": "xxxx",
            "index": "test-1",
            "type": "default",
            "cleanup": true,
            "settings": {"index" :{"number_of_shards": 1, "number_of_replicas": 0}},
            "discovery": false,
            "batchSize": 1000,
            "splitter": ",",
            "column": [
              {"name": "pk", "type": "id"},
              { "name": "col_ip","type": "ip" },
              { "name": "col_double","type": "double" },
              { "name": "col_long","type": "long" },
              { "name": "col_integer","type": "integer" },
              { "name": "col_keyword", "type": "keyword" },
              { "name": "col_text", "type": "text", "analyzer": "ik_max_word"},
              { "name": "col_geo_point", "type": "geo_point" },
              { "name": "col_date", "type": "date", "format": "yyyy-MM-dd HH:mm:ss"},
              { "name": "col_nested1", "type": "nested" },
              { "name": "col_nested2", "type": "nested" },
              { "name": "col_object1", "type": "object" },
              { "name": "col_object2", "type": "object" },
              { "name": "col_integer_array", "type":"integer", "array":true},
              { "name": "col_geo_shape", "type":"geo_shape", "tree": "quadtree", "precision": "10m"}
            ]
          }
        }
      }
    ]
  }
}
```



------

# 使用优化

## 关键参数



* job.setting.speed.channel : channel 并发数
* job.setting.speed.record : 2 全局配置 channel 的 record 限速
* job.setting.speed.byte：全局配置 channel 的 byte 限速
* core.transport.channel.speed.record：单个 channel 的 record 限速
* core.transport.channel.speed.byte：单个 channel 的 byte 限速





------

## 设置 Channel 速度



在 DataX 内部对每个 Channel 会有严格的速度控制，分两种，一种是控制每秒同步的记 录数，另外一种是每秒同步的字节数，默认的速度限制是 1MB/s，可以根据具体硬件情况设 置这个 byte 速度或者 record 速度，一般设置 byte 速度，比如：我们可以把单个 Channel 的 速度上限配置为 5MB



------

## 设置 JVM 内存



当提升 DataX Job 内 Channel 并发数时，内存的占用会显著增加，因为 DataX 作为数据 交换通道，在内存中会缓存较多的数据。例如 Channel 中会有一个 Buffer，作为临时的数据 交换的缓冲区，而在部分 Reader 和 Writer 的中，也会存在一些 Buffer，为了防止 OOM 等错 误，调大 JVM 的堆内存
