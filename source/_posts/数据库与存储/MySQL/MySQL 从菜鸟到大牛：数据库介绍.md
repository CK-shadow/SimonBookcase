---
title: MySQL 从菜鸟到大牛：数据库介绍
date: 2022-03-29 16:49:13
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/preview.jpg
tags:
  - 数据库与存储
  - MySQL
categories: 数据库与存储
---



# 为什么要使用数据库



持久化：把数据保存到可掉电式存储设备中以供之后使用。大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到硬盘中加以“固化”。而持久化的实现过程大多数通过各种关系型数据库来实现

持久化的主要作用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML 数据文件中



![image-20230201230450046](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230201230450046.png)



---

# 数据库与数据库管理系统

## 数据库的相关概念



**DB：数据库（Database）**

即存储数据的仓库，其本质是一个文件系统，它保存了一系列有组织的数据



**DBMS：数据库管理系统（Database Management System）**

是一种操作和管理数据的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库表中的数据



**SQL：结构化查询语言（Structured Query Language）**

专门用来与数据库通信的语言



------

## 常见的数据库管理系统排名



目前互联网上常见的数据库管理系统有 Oracle、MySQL、MS SQL Server、DB2、Sybase 等等，以下是 2023 年最新的数据库受欢迎程度排名（查看最新数据库排名：https://db-engines.com/en/ranking）

![image-20230201232010887](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230201232010887.png)



对应的走势图：https://db-engines.com/en/ranking_trend

![image-20230201232259321](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230201232259321.png)



------

## 常见的数据库介绍



**Oracle**

1979 年，Oracle 2 诞生，它是第一个商用的 RDBMS（关系型数据库管理系统）。随着 Oracle 软件的名气越来越大，公司也改名叫 Oracle 公司

2007 年，总计 85 亿美金收购 BEA System

2009 年，总计 79 亿美金收购 SUN。此前的 2008 年，SUN 以 10 亿美金收购 MySQL。意味着 Oracle 同时拥有了 MySQL 的管理权。至此，Oracle 在数据库领域成为绝对的领导者

2013 年，Oracle 超越 IBM，成为继 Microsoft 后全球第二大软件公司

如今 Oracle 的年收入达到了 400 亿美金，足以证明商用数据库软件的价值



**SQL Server**

SQL Server 是微软开发的大型商用数据库，诞生于 1989 年。C#、.net 等语言常使用，与 WinNT 完全集成，也可以很好的与 Microsoft BackOffice 产品集成



**DB2**

IBM 公司的数据库产品，收费的，常应用于银行系统中



**PostgreSQL**

PostgreSQL 的稳定性极强，最符合 SQL 标准，开放源码，具备商业级 DBMS 质量。PG 对数据量大的文本和 SQL 处理较快



**SyBase**

已经淡出历史舞台，提供了一个非常专业的数据建模工具 PowerDesigner



**SQLite**

嵌入式小型数据库，应用在手机端。零配置，SQLite3 不用安装，不用配置，不用启动、关闭或配置数据库实例。当系统崩溃后不用做任何恢复操作，下次使用数据库时自动恢复



**informix**

IBM 公司出品，取自 information 和 Unix 的结合，它是第一个被移植到 Linux 上的商业数据库产品。仅运行于 unix/linux 平台，命令行操作。性能较高，支持集群，适用于安全性极高的系统，尤其是银行、证券系统的应用



---

# MySQL 介绍

## 概述



![image-20230202221633409](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230202221633409.png)

* MySQL 是一个开放源代码的关系型数据库管理系统，由瑞典 MySQL AB 公司 1995 年开发，迅速成为开源数据库的 No.1
* 2008 年被 SUN 10 亿美金收购，2009 年 SUN 被 Oracle 收购。MySQL 的创造者担心 MySQL 有闭源的风险，由此创建了 MySQL 的分支项目 MariaDB
* MySQL 6.x 版本之后分为社区版和商业版
* MySQL 是一种关联数据库管理系统，将数据保存在不同的表中，而不是将所有的数据放在一个大仓库中，这样就增加了速度并提高了灵活性
* MySQL 是开源的，因此不需要支付额外的费用
* MySQL 是可以定制的，采用了 GPL 协议，可以修改源代码来设计自己的 MySQL 系统
* MySQL 支持大型的数据库，可以处理拥有上千万条记录的大型数据库
* MySQL 支持大型数据库，支持 5000 万条记录的数据仓库，32 位系统表文件最大可支持 4 GB，64 位系统表文件最大可支持 8 TB
* MySQL 使用标准的 SQL 数据语言形式
* MySQL 可以运行于多个系统之上，并支持多种语言，包括但不限于 C、C++、Python、Java、Perl



------

## 关于 MySQL 8.0



MySQL 从 5.7 版本直接跳跃发布了 8.0 版本，可见这是一个令人兴奋的里程碑版本。MySQL 8 在功能上做了显著的优化与增强，开发者对 MySQL 的源代码进行了重构，最突出的一个点是对 MySQL Optimizer 优化器进行了改进。不仅在速度上得到了改善，还为用户带来了更好的性能与更棒的体验

这里可以看到 MySQL 最近的一些版本：https://downloads.mysql.com/archives/community/



------

## Oracle vs MySQL



Oracle 更适合大型跨国企业的使用，因为他们对费用不敏感，但是对性能要求和安全性有更高的要求

MySQL 由于其体积小、速度快，总体成本低，可以处理上千万条数据的大型数据库，尤其是开放源代码这一特点，使得很多互联网公司、中小型网站选择了 MySQL 作为数据库



---

# RDBMS 和 非 RDBMS

## 关系型数据库（RDBMS）



这种类型的数据库是最古老的数据库类型，关系型数据库模型是把复杂的数据结构归结为简单的二元关系（即二维表格形式）

![image-20230202234951931](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230202234951931.png)



关系型数据库以行（row）和列（column）的形式存储数据，以便于用户理解。这一系列的行和列被称为表（table），一组表组成了一个库（database）

表与表之间的数据记录有关系（relationship），现实世界中的各种实体以及实体之间的各种联系均用关系模型来表示。关系型数据库，就是建立在关系模型基础上的数据库

SQL 就是关系型数据库的查询语言



关系型数据库的优势主要在于可以时间复杂的查询和对事务的支持



------

## 非关系型数据库（非 RDBMS）



相比于 SQL，NoSQL 泛指非关系型数据库，包括了键值型数据库、文档型数据库、搜索引擎和列存储等，此外还包括图形数据库



以下是常用的 NoSQL 数据库：

* 键值型数据库：Redis，查询速度快，缺点是无法实现条件过滤
* 文档型数据库：最流行的文档型数据库是 MongoDB，其次还有 CouchDB
* 搜索引擎：Solr、Elasticsearch、Splunk
* 列式数据库：适用于分布式文件系统，功能相对有限，典型产品有 HBase
* 图形数据库：适用于数据关系复杂的场景，典型产品有 Neo4J、InfoGrid 等



---

# 关系型数据库设计规则

## 表、记录、字段



（E-R）模型中有三个概念是：实体集、属性和联系集

一个实体集对应数据库中的一张表，一个实体则对应数据库表中的一行数据，也称为一条记录。一个属性则对应数据库表中的一列，也称为一个字段

![image-20230203221950189](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230203221950189.png)



------

## 表的关联关系



* 一对一关联

* 一对多关联

* 多对多关联

  要表示多对多关系，必须创建第三个表，该表通常称为联接表。它将多对多关系划分为两个一对多关系，将这两个表的主键都插入到第三个表中

* 自我关联



---

# SQL 语言规则与规范

## SQL 分类





SQL 语言在功能上主要分为以下三大类：

* DDL（数据定义语言）

  这些语句定义了不同数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和表的数据结构，主要语句包括 CREATE、DROP、ALTER 等

* DML（数据操作语言）

  用于添加、删除、更新和查询数据库记录，并检查数据完整性。主要包括 INSERT、DELETE、UPDATE 和 SELECT，其中 SELECT 是 SQL 语言的基础，最为重要

* DCL（数据控制语言）

  用于定义数据库、表、字段和用户的访问权限和安全级别。主要语句包括 GRANT、REVOKE、COMMIT、ROLLBACK、SAVEPOINT 等



------

## SQL 大小写规范



* MySQL 在 Windows 环境下是不区分大小写的
* MySQL 在 Linux 环境下是区分大小写的
  * 数据库名、表名、表的别名、变量名是严格区分大小写的
  * 关键字、函数名、字段名、字段的别名是不区分大小写的
* 推荐采用统一的书写规范
  * 数据库名、表名、表的别名、字段名、字段的别名都小写
  * SQL 关键字、函数名、绑定变量等都大写



------

## 注释



```
单行注释：#注释文字（MySQL 特有的方式）
单行注释：-- 注释文字（-- 后面必须包含一个空格）
多行注释：/* 注释文字 */
```



---

# 基本的 SELECT 语句

## SELECT ...



```SQL
SELECT 1;
SELECT 1 + 3;
# 查询数据库版本
SELECT version();
```



------

## SELECT ... FROM



* 语法

  ```sql
  SELECT 列名
  FROM 表名
  ```

* 查询全部的列

  ```sql
  SELECT *
  FROM departments
  ```

* 伪表

  ```
  SELECT 1 + 3
  等同于
  SELECT 1 + 3 FROM DUAL; # dual 伪表
  ```

* 查询指定的列

  ```sql
  SELECT department_id, department_localcation
  FROM departments
  ```



在生产环境下，不推荐使用 SELECT * 进行查询，查询需要的列即可



------

## 列的别名



可以对查询出来的列重命名，以下方式均可完成

```sql
SELECT department_id id FROM departments;
SELECT department_id "id" FROM departments;
SELECT department_id AS "id" FROM departments;
```

在 MySQL 中，AS 关键字和双引号都是非必须的，但在别名中有空格时，则必须用双引号括起来

在实际开发中，推荐使用 AS 关键字和双引号



------

## 去重



```sql
SELECT DISTINCT department_id id FROM departments;
```



------

## 空值参与运算



当 commission_pct 为 null 时作为 0 计算

```sql
SELECT 12 * salary * (1 + commission_pct) AS "annual_sal" FROM employees
```



------

## 着重号



```sql
# 错误的
SELECT * FROM ORDER; 
# 正确的
SELECT * FROM `ORDER`; 
```



------

## 显示表结构



```
DESCRIBE employees;
或者
DESC employees;
```



------

## 过滤条件



* 语法

  ```sql
  SELECT 字段名
  FROM 表名
  WHERE 过滤条件
  ```

* 查询所有年龄为 18 的员工

  ```sql
  SELECT * 
  FROM employees
  WHERE age = 18;
  ```



---

# 运算符

## 算数运算符



算数运算符主要用于数学运算，其可以连接运算符前后两个数值或表达式，对数值或表达式进行加（+）、减（-）、乘（*）、除（/）和取模（%）运算

![image-20230204233434335](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230204233434335.png)



------

## 比较运算符



比较运算符用来对表达式左边的操作数和右边的操作数进行比较，比较的结果为真则返回 1，比较的结果为假则返回 0，其它结果返回 NULL

比较运算符常用来作为 SELECT 查询语句的条件来使用，返回符合条件的记录结果

![image-20230204234107692](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230204234107692.png)



------

## 逻辑运算符



逻辑运算符主要用来判断表达式的真假，在 MySQL 中，逻辑运算符返回的结果为 1、0 或者 NULL

MySQL 中支持 4 种逻辑运算符，如下：

![image-20230204234316885](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230204234316885.png)



------

## 位运算符



位运算符是在二进制数上进行计算的运算符，位运算符会先将操作数变成二进制数，然后进行位运算，最后将计算结果冲二进制变成十进制数

MySQL 支持的位运算符如下：

![image-20230204234819715](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230204234819715.png)



------

## 运算符的优先级



![image-20230204234517274](https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%8E%E5%AD%98%E5%82%A8/MySQL/MySQL%20%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%89%9B%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BB%8B%E7%BB%8D/image-20230204234517274.png)



数字编号越大，优先级越高，优先级高的运算符先进行计算

