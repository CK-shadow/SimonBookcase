---
title: MySQL 从菜鸟到大牛之初级篇
date: 2023-03-27 21:39:45
tags: 数据库
categories: 
  - 数据库
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/preview.jpg
---

# MySQL 从菜鸟到大牛之初级篇

## 为什么要使用数据库



持久化：把数据保存到可掉电式存储设备中以供之后使用。大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到硬盘中加以“固化”。而持久化的实现过程大多数通过各种关系型数据库来实现

持久化的主要作用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML 数据文件中



![image-20230201230450046](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230201230450046.png)

------

## 数据库与数据库管理系统

### 数据库的相关概念



| DB：数据库（Database）                                       |
| ------------------------------------------------------------ |
| 即存储数据的仓库，其本质是一个文件系统，它保存了一系列有组织的数据 |
| DBMS：数据库管理系统（Database Management System）           |
| 是一种操作和管理数据的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库表中的数据 |
| SQL：结构化查询语言（Structured Query Language）             |
| 专门用来与数据库通信的语言                                   |



------

### 常见的数据库管理系统排名



目前互联网上常见的数据库管理系统有 Oracle、MySQL、MS SQL Server、DB2、Sybase 等等，以下是 2023 年最新的数据库受欢迎程度排名（查看最新数据库排名：https://db-engines.com/en/ranking）

![image-20230201232010887](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230201232010887.png)



对应的走势图：https://db-engines.com/en/ranking_trend

![image-20230201232259321](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230201232259321.png)



------

### 常见的数据库介绍



Oracle

1979 年，Oracle 2 诞生，它是第一个商用的 RDBMS（关系型数据库管理系统）。随着 Oracle 软件的名气越来越大，公司也改名叫 Oracle 公司

2007 年，总计 85 亿美金收购 BEA System

2009 年，总计 79 亿美金收购 SUN。此前的 2008 年，SUN 以 10 亿美金收购 MySQL。意味着 Oracle 同时拥有了 MySQL 的管理权。至此，Oracle 在数据库领域成为绝对的领导者

2013 年，Oracle 超越 IBM，成为继 Microsoft 后全球第二大软件公司

如今 Oracle 的年收入达到了 400 亿美金，足以证明商用数据库软件的价值



SQL Server

SQL Server 是微软开发的大型商用数据库，诞生于 1989 年。C#、.net 等语言常使用，与 WinNT 完全集成，也可以很好的与 Microsoft BackOffice 产品集成



DB2

IBM 公司的数据库产品，收费的，常应用于银行系统中



PostgreSQL

PostgreSQL 的稳定性极强，最符合 SQL 标准，开放源码，具备商业级 DBMS 质量。PG 对数据量大的文本和 SQL 处理较快



SyBase

已经淡出历史舞台，提供了一个非常专业的数据建模工具 PowerDesigner



SQLite

嵌入式小型数据库，应用在手机端。零配置，SQLite3 不用安装，不用配置，不用启动、关闭或配置数据库实例。当系统崩溃后不用做任何恢复操作，下次使用数据库时自动恢复



informix

IBM 公司出品，取自 information 和 Unix 的结合，它是第一个被移植到 Linux 上的商业数据库产品。仅运行于 unix/linux 平台，命令行操作。性能较高，支持集群，适用于安全性极高的系统，尤其是银行、证券系统的应用



------

## MySQL 介绍

### 概述



![image-20230202221633409](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230202221633409.png)

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

### 关于 MySQL 8.0



MySQL 从 5.7 版本直接跳跃发布了 8.0 版本，可见这是一个令人兴奋的里程碑版本。MySQL 8 在功能上做了显著的优化与增强，开发者对 MySQL 的源代码进行了重构，最突出的一个点是对 MySQL Optimizer 优化器进行了改进。不仅在速度上得到了改善，还为用户带来了更好的性能与更棒的体验

这里可以看到 MySQL 最近的一些版本：https://downloads.mysql.com/archives/community/



------

### Oracle vs MySQL



Oracle 更适合大型跨国企业的使用，因为他们对费用不敏感，但是对性能要求和安全性有更高的要求

MySQL 由于其体积小、速度快，总体成本低，可以处理上千万条数据的大型数据库，尤其是开放源代码这一特点，使得很多互联网公司、中小型网站选择了 MySQL 作为数据库



------

## RDBMS 和 非 RDBMS

### 关系型数据库（RDBMS）



这种类型的数据库是最古老的数据库类型，关系型数据库模型是把复杂的数据结构归结为简单的二元关系（即二维表格形式）

![image-20230202234951931](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230202234951931.png)



关系型数据库以行（row）和列（column）的形式存储数据，以便于用户理解。这一系列的行和列被称为表（table），一组表组成了一个库（database）

表与表之间的数据记录有关系（relationship），现实世界中的各种实体以及实体之间的各种联系均用关系模型来表示。关系型数据库，就是建立在关系模型基础上的数据库

SQL 就是关系型数据库的查询语言



关系型数据库的优势主要在于可以时间复杂的查询和对事务的支持



------

### 非关系型数据库（非 RDBMS）



相比于 SQL，NoSQL 泛指非关系型数据库，包括了键值型数据库、文档型数据库、搜索引擎和列存储等，此外还包括图形数据库



以下是常用的 NoSQL 数据库：

* 键值型数据库：Redis，查询速度快，缺点是无法实现条件过滤
* 文档型数据库：最流行的文档型数据库是 MongoDB，其次还有 CouchDB
* 搜索引擎：Solr、Elasticsearch、Splunk
* 列式数据库：适用于分布式文件系统，功能相对有限，典型产品有 HBase
* 图形数据库：适用于数据关系复杂的场景，典型产品有 Neo4J、InfoGrid 等



------

## 关系型数据库设计规则

### 表、记录、字段



（E-R）模型中有三个概念是：实体集、属性和联系集

一个实体集对应数据库中的一张表，一个实体则对应数据库表中的一行数据，也称为一条记录。一个属性则对应数据库表中的一列，也称为一个字段

![image-20230203221950189](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230203221950189.png)



------

### 表的关联关系



* 一对一关联

* 一对多关联

* 多对多关联

  要表示多对多关系，必须创建第三个表，该表通常称为联接表。它将多对多关系划分为两个一对多关系，将这两个表的主键都插入到第三个表中

* 自我关联



------

## SQL 语言规则与规范

### SQL 分类





SQL 语言在功能上主要分为以下三大类：

* DDL（数据定义语言）

  这些语句定义了不同数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和表的数据结构，主要语句包括 CREATE、DROP、ALTER 等

* DML（数据操作语言）

  用于添加、删除、更新和查询数据库记录，并检查数据完整性。主要包括 INSERT、DELETE、UPDATE 和 SELECT，其中 SELECT 是 SQL 语言的基础，最为重要

* DCL（数据控制语言）

  用于定义数据库、表、字段和用户的访问权限和安全级别。主要语句包括 GRANT、REVOKE、COMMIT、ROLLBACK、SAVEPOINT 等



------

### SQL 大小写规范



* MySQL 在 Windows 环境下是不区分大小写的
* MySQL 在 Linux 环境下是区分大小写的
  * 数据库名、表名、表的别名、变量名是严格区分大小写的
  * 关键字、函数名、字段名、字段的别名是不区分大小写的
* 推荐采用统一的书写规范
  * 数据库名、表名、表的别名、字段名、字段的别名都小写
  * SQL 关键字、函数名、绑定变量等都大写



------

### 注释



```
单行注释：#注释文字（MySQL 特有的方式）
单行注释：-- 注释文字（-- 后面必须包含一个空格）
多行注释：/* 注释文字 */
```



------

## 基本的 SELECT 语句

### SELECT ...



```SQL
SELECT 1;
SELECT 1 + 3;
# 查询数据库版本
SELECT version();
```



------

### SELECT ... FROM



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

### 列的别名



可以对查询出来的列重命名，以下方式均可完成

```sql
SELECT department_id id FROM departments;
SELECT department_id "id" FROM departments;
SELECT department_id AS "id" FROM departments;
```

在 MySQL 中，AS 关键字和双引号都是非必须的，但在别名中有空格时，则必须用双引号括起来

在实际开发中，推荐使用 AS 关键字和双引号



------

### 去重



```sql
SELECT DISTINCT department_id id FROM departments;
```



------

### 空值参与运算



当 commission_pct 为 null 时作为 0 计算

```sql
SELECT 12 * salary * (1 + commission_pct) AS "annual_sal" FROM employees
```



------

### 着重号



```sql
# 错误的
SELECT * FROM ORDER; 
# 正确的
SELECT * FROM `ORDER`; 
```



------

### 显示表结构



```
DESCRIBE employees;
或者
DESC employees;
```



------

### 过滤条件



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



------

## 运算符

### 算数运算符



算数运算符主要用于数学运算，其可以连接运算符前后两个数值或表达式，对数值或表达式进行加（+）、减（-）、乘（*）、除（/）和取模（%）运算

![image-20230204233434335](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230204233434335.png)



------

### 比较运算符



比较运算符用来对表达式左边的操作数和右边的操作数进行比较，比较的结果为真则返回 1，比较的结果为假则返回 0，其它结果返回 NULL

比较运算符常用来作为 SELECT 查询语句的条件来使用，返回符合条件的记录结果

![image-20230204234107692](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230204234107692.png)



------

### 逻辑运算符



逻辑运算符主要用来判断表达式的真假，在 MySQL 中，逻辑运算符返回的结果为 1、0 或者 NULL

MySQL 中支持 4 种逻辑运算符，如下：

![image-20230204234316885](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230204234316885.png)



------

### 位运算符



位运算符是在二进制数上进行计算的运算符，位运算符会先将操作数变成二进制数，然后进行位运算，最后将计算结果冲二进制变成十进制数

MySQL 支持的位运算符如下：

![image-20230204234819715](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230204234819715.png)



------

### 运算符的优先级



![image-20230204234517274](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230204234517274.png)



数字编号越大，优先级越高，优先级高的运算符先进行计算



------

## 排序与分页

### 排序数据



* 语法

  使用 ORDER BY 进行排序，ASC 为升序，DESC 为降序，ORDER BY 子句在 SELECT 子句的结尾

* 单列排序

  ```sql
  # 默认按年龄升序
  SELECT * 
  FROM employees
  ORDER BY age;
  
  # 倒序为
  SELECT * 
  FROM employees
  ORDER BY age DESC;
  ```

* ORDER BY 可以使用列的别名

  ```sql
  SELECT id, salary * 12 AS annual_sal
  FROM employees
  ORDER BY annual_sal;
  ```

* 多列排序

  ```sql
  SELECT * 
  FROM employees
  ORDER BY age DESC, id ASC;
  ```




------

### 分页



* 语法

  ```
  SELECT 字段名
  FROM 表名
  LIMIT [位置偏移量], 条目数
  ```

* 查询前 10 条数据

  ```
  SELECT *
  FROM employees
  LIMIT 0, 10;
  或者
  SELECT *
  FROM employees
  LIMIT 10;
  ```

* 查询前 11 到 20 条数据

  ```
  SELECT *
  FROM employees
  LIMIT 10, 20;
  ```

* MySQL 8.0 新特性

  MySQL 8.0 中可以使用 "LIMIT 3 OFFSET 4"，意思是获取从第 5 条记录开始后的后 3 条记录，和 LIMIT 4, 3 的效果相同

* 分页的好处

  约束返回结果的数量可以减少数据表的网络传输量，也可以提升查询效率。如果我们知道结果只有一条，就可以使用 LIMIT 1，这样 SELECT 在查询到一条数据之后就会返回，而不用全表扫描

* 与 Oracle 的不同

  在 MySQL 中，分页使用的是 LIMIT 关键字，而在 Oracle 中，要使用 ROWNUM 来统计行数

  ```sql
  -- 查询前 5 条记录
  SELECT *
  FROM employees
  WHERE ROWNUM < 5
  ORDER BY salary DESC;
  ```

  这条语句是取出前 5 条数据，然后按照 salary 倒序排列，如果想取出工资最高的前 5 个，则需要这么写

  ```sql
  SELECT *
  FROM  (
  	SELECT *
  	FROM employees
  	ORDER BY salary DESC)
  WHERE rownum < 5;
  ```

  



------

## 多表查询

### 笛卡尔积



笛卡尔积是一个数学运算，假设有两个集合 X 和 Y，那么 X 和 Y 的笛卡尔积就是 X 和 Y 所有可能的组合。也就是第一个对量来自 X，第二个对象来自 Y 的所有可能，组合的个数即两个元素中个数的乘积

![image-20230205165954618](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230205165954618.png)

SQL 92 中，笛卡尔积也称为交叉连接，英文是 CROSS JOIN。在 SQL 99 中也是 CROSS JOIN 表示交叉连接。它的左右就是可以把任意表进行连接，即使这两张表不想关，在 MySQL 中如下情况会出现笛卡尔积

```sql
SELECT * FROM employees, departments;
```



------

### 内连接与外连接



* 内连接

  合并具有同一列的两个以上的表的行，其中结果集不包含一个表和另一个表不匹配的行

  ```sql
  SELECT employee_id, department_name
  FROM employee e INNER JOIN departments d
  ON e.department_id = d.department_id;
  ```

* 左外连接

  两个表在连接过程中除了返回满足连接条件的行还返回左表中不满足连接条件的行，这种连接称为左外连接

  ```sql
  SELECT employee_id, department_name
  FROM employee e LEFT JOIN departments d
  ON e.department_id = d.department_id;
  ```

* 右外连接

  两个表在连接过程中除了返回满足连接条件的行还返回右表中不满足连接条件的行，这种连接称为右外连接

  ```sql
  SELECT employee_id, department_name
  FROM employee e RIGHT JOIN departments d
  ON e.department_id = d.department_id;
  ```



------

### 7 种 JOIN 的实现



以下是 SQL 99 规定的 7 种 JOIN 的查询 SQL，但是 MySQL 不支持 FULL OUTER JOIN，因此 MySQL 只有 5 种实现方式

![image-20230205220712828](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230205220712828.png)

 

------

### SQL99 新特性



* 自然连接

  自然连接是 SQL99 在 SQL92 的基础上新增的语法，用关键字  NATURAL JOIN 表示。自然连接类似于 SQL92 中的等值连接，它会自动查询两张表中所有相同的字段，然后进行等值连接

  比如，SQL92语法中等值连接的写法：

  ```sql
  SELECT emp.*, dept.*
  FROM employees emp JOIN departments dept
  ON emp.department_id = dept.department_id
  AND emp.manager_id = dept.manager_id;
  ```

  在 SQL99 中可以使用自然连接  NATURAL JOIN 代替

  ```sql
  SELECT emp.*, dept.*
  FROM employees emp NATURAL JOIN departments dept;
  ```

  虽然使用自然连接 NATURAL JOIN 代替更简洁，但其远不如 SQL92 中的等值连接那样灵活。因为自然连接 NATURAL JOIN 是一定会自动查询两张表中所有相同的字段，但很多时候我们仅需要一个相同字段作为连接条件即可，并不需要所有相同字段作为连接条件，因此自然连接 NATURAL JOIN 的应用范围是十分有限的

* ### USING 连接

  USING 连接是指定表中同名字段进行等值连接。但是只能配合  JOIN 一起使用

  比如，在 SQL92 语法中的等值连接如下：

  ```SQL
  SELECT emp.*, dept.*
  FROM employees emp JOIN departments dept
  ON emp.department_id = dept.department_id;
  ```

  如果连接条件中的字段同名，则可以使用 USING 连接：

  ```SQL
  SELECT emp.*, dept.*
  FROM employees emp JOIN departments dept
  ON USING(department_id);
  ```

  USING 连接的灵活度比自然连接 NATURAL JOIN 更强，但其只能用在两张表的同名字段进行等值连接。如果连接条件的字段不同名 ，就无法使用 USING 连接，其依然具有一定的局限性



------

## 单行函数

### 数值函数





| 函数                    | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| ABS(x)                  | 返回 x 的绝对值                                              |
| SIGN(x)                 | 返回 x 的符号，正数返回 1，负数返回 -1，0 返回 0             |
| PI()                    | 返回圆周率的值                                               |
| CEIL(x), CEILING(x)     | 返回大于或等于某个值的最小整数                               |
| FLOOR(x)                | 返回小于或等于某个值的最大整数                               |
| LEAST(e1, e2, e3...)    | 返回列表中的最小值                                           |
| GREATEST(e1, e2, e3...) | 返回列表中的最大值                                           |
| MOD(x, y)               | 返回 x 除以 y 之后的余数                                     |
| RAND()                  | 返回 0 - 1 的随机值                                          |
| RAND(x)                 | 返回 0 - 1 的随机值，其中 x 的值作为种子值，相同的 x 值会产生相同的随机数 |
| ROUND(x)                | 返回一个对 x 进行四舍五入之后，最接近 x 的整数               |
| ROUND(x, y)             | 返回一个对 x 进行四舍五入之后最接近 x 的值，y 为保留的小数位 |
| TRUNCATE(x, y)          | 返回数字 x 截断 y 位小数之后的结果                           |
| SQRT(x)                 | 返回 x 的平方根，当 x 的值为负数时，返回 NULL                |



------

### 字符串函数



| 函数                       | 作用                                                         |
| -------------------------- | ------------------------------------------------------------ |
| CONCAT(str1,str2,...,strn) | 将 str1,str2,...,strn 连接为一个完整的字符串                 |
| INSERT(str,x,y,instr)      | 将字符串 str 从第 x 开始，y 个字符串长度的子串替换为字符串 instr |
| LOWER(str)                 | 将字符串 str 中的所有字母变成小写                            |
| UPPER(str)                 | 将字符串 str 中的所有字母变成大写                            |
| LEFT(str,x)                | 返回字符串最左边的 x 个字符                                  |
| RIGHT(str,x)               | 返回字符串最右边的 x 个字符                                  |
| LPAD(str,n,pad)            | 使用字符串 pad 对字符串 str 最左边进行填充，直到长度为 n 个字符长度 |
| RPAD(str,n,pad)            | 使用字符串 pad 对字符串 str 最右边进行填充，直到长度为 n 个字符长度 |
| LTRIM(str)                 | 去掉 str 左边的空格                                          |
| RTRIM(str)                 | 去掉 str 右边的空格                                          |
| REPEAT(str,x)              | 返回字符串 str 重复x次的结果                                 |
| REPLACE(str,a,b)           | 使用字符串 b 替换字符串 str 中所有出现的字符串 a             |
| STRCMP(str1,str2)          | 比较字符串 str1和 str2                                       |
| TRIM(str)                  | 去掉字符串行头和行尾的空格                                   |
| SUBSTRING(str,x,y)         | 返回字符串 str 中从 x 位置起 y 个字符串长度的字符串          |



------

### 流程控制函数



| 函数                                                         | 作用                                                 |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| IF(value, value1, value2)                                    | 如果 value 为 true 则返回 value1，否则返回 value2    |
| IFNULL(value1, value2)                                       | 如果 value1 不为 NULL 则返回 value1，否则返回 value2 |
| CASE WHEN 条件1 THEN 结果1 WHEN 条件2 THEN 结果2 ... [ELSE result] END | 相当于 java 的 if else 语句                          |
| CASE expr 常量1 THEN 值1 WHEN 常量2 THEN 值2 ... [ELSE 值n] END | 相当于 java 的 switch 语句                           |



------

### 加密解密函数



| 函数                         | 作用                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| PASSWORD(str)                | 返回字符串 str 的加密版本，41 位长的字符串。加密结果不可逆，常用于用户的密码加密 |
| MD5(str)                     | 返回字符串 str MD5 签名之后的值，若参数为 NULL 则返回 NULL   |
| SHA(str)                     |                                                              |
| ENCODE(value, password_seed) |                                                              |
| DECODE(value, password_seed) |                                                              |



------

## 创建和管理表

### 标识符命名规则



* 数据库名、表名不得超过 30 个字符，变量名限制为 29 个
* 必须只能包含 A–Z, a–z, 0–9, _ 共 63 个字符
* 数据库名、表名、字段名等对象名中间不要包含空格
* 同一个 MySQL 软件中，数据库不能同名；同一个库中，表不能重名；同一个表中，字段不能重名
* 必须保证你的字段没有和保留字、数据库系统或常用方法冲突。如果坚持使用，请在 SQL 语句中使 用`（着重号）引起来
* 保持字段名和类型的一致性：在命名字段并为其指定数据类型的时候一定要保证一致性，假如数据 类型在一个表里是整数，那在另一个表里可就别变成字符型了



------

### MySQL 数据类型



| 类型             | 类型举例                                                     |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | TINYINT、SMALLINT、MEDIUMINT、INT(或INTEGER)、BIGINT         |
| 浮点类型         | FLOAT、DOUBLE                                                |
| 定点数类型       | DECIMAL                                                      |
| 位类型           | BIT                                                          |
| 日期时间类型     | YEAR、TIME、DATE、DATETIME、TIMESTAMP                        |
| 文本字符串类型   | CHAR、VARCHAR、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT          |
| 枚举类型         | ENUM                                                         |
| 集合类型         | SET                                                          |
| 二进制字符串类型 | BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB      |
| JSON类型         | JSON对象、JSON数组                                           |
| 空间数据类型     | 单值：GEOMETRY、POINT、LINESTRING、POLYGON；<br />集合：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、 GEOMETRYCOLLECTION |



------

### 创建和管理数据库



* 创建数据库

  ```sql
  CREATE DATABASE 数据库名;
  ```

* 创建数据库并指定字符集

  ```sql
  CREATE DATABASE 数据库名 CHARACTER SET 字符集;
  ```

* 判断数据库是否已经存在，不存在则创建数据库

  ```sql
  CREATE DATABASE IF NOT EXISTS 数据库名;
  ```

* 查看当前所有的数据库

  ```sql
  SHOW DATABASES; #有一个S，代表多个数据库
  ```

* 查看当前正在使用的数据库

  ```sql
  SELECT DATABASE(); #使用的一个 mysql 中的全局函数
  ```

* 查看指定库下所有的表

  ```sql
  SHOW TABLES FROM 数据库名;
  ```

* 查看数据库的创建信息

  ```sql
  SHOW CREATE DATABASE 数据库名;
  ```

* 使用/切换数据库

  ```sql
  USE 数据库名;
  ```

* 更改数据库字符集

  ```sql
  ALTER DATABASE 数据库名 CHARACTER SET 字符集; #比如：gbk、utf8等
  ```

* 删除数据库

  ```sql
  DROP DATABASE 数据库名;
  DROP DATABASE IF EXISTS 数据库名;
  ```



------

### 创建表



* 语法

  ```sql
  CREATE TABLE [IF NOT EXISTS] 表名( 
  	字段1, 数据类型 [约束条件] [默认值], 
  	字段2, 数据类型 [约束条件] [默认值], 
  	字段3, 数据类型 [约束条件] [默认值], 
  	…… [表约束条件]
  );
  ```

* 创建表举例

  ```sql
  -- 创建表 CREATE TABLE emp ( 
  	-- int类型 
  	emp_id INT, 
  	-- 最多保存20个中英文字符 
  	emp_name VARCHAR(20), 
  	-- 总位数不超过15位 
  	salary DOUBLE, 
  	-- 日期类型 
  	birthday DATE
  );
  ```



------

### 修改表



* 追加一个列

  ```sql
  ALTER TABLE 表名 ADD 【COLUMN】 字段名 字段类型 【FIRST|AFTER 字段名】;
  ```

* 修改一个列

  ```sql
  ALTER TABLE 表名 MODIFY 【COLUMN】 字段名1 字段类型 【DEFAULT 默认值】【FIRST|AFTER 字段名 2】;
  ```

* 重命名一个列

  ```sql
  ALTER TABLE 表名 CHANGE 【column】 列名 新列名 新数据类型;
  ```

* 删除一个列

  ```sql
  ALTER TABLE 表名 DROP 【COLUMN】字段名
  ```

* 重命名表

  ```sql
  RENAME TABLE emp TO myemp;
  ```

* 删除表

  ```sql
  DROP TABLE [IF EXISTS] 数据表1 [, 数据表2, …, 数据表n];
  ```

* 清空表

  ```sql
  TRUNCATE TABLE detail_dept;
  ```



------

### MySQL8新特性—DDL的原子化



在MySQL 8.0版本中，InnoDB表的DDL支持事务完整性，即 DDL操作要么成功要么回滚 。DDL操作回滚日志 写入到data dictionary数据字典表mysql.innodb_ddl_log（该表是隐藏的表，通过show tables无法看到） 中，用于回滚操作。通过设置参数，可将DDL操作日志打印输出到MySQL错误日志中



------

## 数据处理之增删改

### 增删改



* 插入数据

  ```sql
  INSERT INTO 表名 VALUES (value1,value2,....);
  INSERT INTO departments VALUES (70, 'Pub', 100, 1700);
  ```

* 更新数据

  ```sql
  UPDATE employees SET department_id = 70 WHERE employee_id = 113;
  ```

* 删除数据

  ```sql
  DELETE FROM departments WHERE department_name = 'Finance';
  ```



------

### MySQL8新特性：计算列



什么叫计算列呢？简单来说就是某一列的值是通过别的列计算得来的。例如，a列值为1、b列值为2，c列 不需要手动插入，定义a+b的结果为c的值，那么c就是计算列，是通过别的列计算得来的
在MySQL 8.0中，CREATE TABLE 和 ALTER TABLE 中都支持增加计算列

```SQL
CREATE TABLE tb1( 
	id INT, 
	a INT, 
	b INT, 
	c INT GENERATED ALWAYS AS (a + b) VIRTUAL
);
```



------

## MySQL8 新特性

### MySQL8新特性概述



* 更简便的NoSQL支持

  从5.6版本开始，MySQL就开始支持简单的NoSQL存储功能。 MySQL 8对这一功能做了优化，以更灵活的方式实现NoSQL功能，不再依赖模式（schema）

* 更好的索引

  在查询中，正确地使用索引可以提高查询的效率。MySQL 8中新增了 隐藏索引和降序索引 。隐藏索引可以用来测试去掉索引对查询性能的影响。在查询中混合存在多列索引时，使用降序索引可以提高查询的性能

* 更完善的JSON支持

  MySQL 从 5.7 开始支持原生 JSON 数据的存储，MySQL 8对这一功能做了优化，增加 了聚合函数 JSON_ARRAYAGG() 和 JSON_OBJECTAGG() ，将参数聚合为JSON 数组或对象，新增了行内操作符  ->>，是列路径运算符 ->的增强，对 JSON 排序做了提升，并优化了 JSON 的更新操作

* 安全和账户管理

  MySQL 8 中新增了 caching_sha2_password 授权插件、角色、密码历史记录和 FIPS 模式支持，这些特性提高了数据库的安全性和性能，使数据库管理员能够更灵活地进行账户管理工作

* InnoDB 的变化

  InnoDB 是 MySQL 默认的存储引擎 ，是事务型数据库的首选引擎，支持事务安全表 （ACID），支持行锁定和外键。在 MySQL 8 版本中，InnoDB 在自增、索引、加密、死锁、共享锁等方面做了大量的改进和优化 ，并且支持原子数据定义语言（DDL），提高了数据安全性，对事务提供更好的支持

* 数据字典

  在之前的 MySQL 版本中，字典数据都存储在元数据文件和非事务表中。从 MySQL 8 开始新增 了事务数据字典，在这个字典里存储着数据库对象信息，这些数据字典存储在内部事务表中

* 原子数据定义语句

  MySQL 8 开始支持原子数据定义语句（Automic DDL），即原子DDL 。目前，只有 InnoDB 存储引擎支持原子 DDL

* 资源管理

  MySQL 8 开始支持创建和管理资源组，允许将服务器内运行的线程分配给特定的分组，以便线程根据组内可用资源执行

* 字符集支持

  MySQL 8 中默认的字符集由 latin1 更改为 utf8mb4 ，并首次增加了日语所特定使用的集合，utf8mb4_ja_0900_as_cs

* 优化器增强

  MySQL 优化器开始支持隐藏索引和降序索引。隐藏索引不会被优化器使用，验证索引的必 要性时不需要删除索引，先将索引隐藏，如果优化器性能无影响就可以真正地删除索引。降序索引允许 优化器对多个列进行排序，并且允许排序顺序不一致

* 公用表表达式

  公用表表达式（Common Table Expressions）简称为CTE，MySQL 现在支持递归和非递 归两种形式的 CTE。CTE 通过在 SELECT 语句或其他特定语句前使用WITH 语句对临时结果集进行命名

* 窗口函数

  MySQL 8 开始支持窗口函数。在之前的版本中已存在的大部分聚合函数 在MySQL 8中也可以 作为窗口函数来使用
  
  ![image-20230211125900835](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E5%BA%93/MySQL%E4%BB%8E%E8%8F%9C%E9%B8%9F%E5%88%B0%E5%A4%A7%E7%A5%9E%E4%B9%8B%E5%88%9D%E7%BA%A7%E7%AF%87/image-20230211125900835.png)
  
* 正则表达式支持

  MySQL 在 8.0.4 以后的版本中采用支持 Unicode 的国际化组件库实现正则表达式操作， 这种方式不仅能提供完全的 Unicode 支持，而且是多字节安全编码。MySQL 增加了 REGEXP_LIKE()、 EGEXP_INSTR()、REGEXP_REPLACE()和 REGEXP_SUBSTR() 等函数来提升性能。另外，regexp_stack_limit 和 regexp_time_limit 系统变量能够通过匹配引擎来控制资源消耗

* 内部临时表

  TempTable 存储引擎取代 MEMORY 存储引擎成为内部临时表的默认存储引擎

* 日志记录

  在 MySQL 8 中错误日志子系统由一系列 MySQL 组件构成。这些组件的构成由系统变量 log_error_services 来配置，能够实现日志事件的过滤和写入

* 增强的 MySQL 复制

  MySQL 8 复制支持对 JSON 文档 进行部分更新的二进制日志记录 ，该记录使用紧凑的二进制格式 ，从而节省记录完整 JSON 文档的空间
