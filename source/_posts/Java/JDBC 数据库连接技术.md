---
title: JDBC 数据库连接技术
date: 2023-03-27 21:39:45
tags: 
  - Java
  - JDBC
categories: 
  - Java
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/JDBC%20%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%8A%80%E6%9C%AF/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/JDBC%20%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%8A%80%E6%9C%AF/preview.jpg

---

#  JDBC 数据库连接技术

## 概述

### 简介



Java数据库连接，（Java Database Connectivity，简称 JDBC）是 Java 语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如查询和更新数据库中数据的方法。JDBC 也是 Sun Microsystems 的商标。我们通常说的 JDBC 是面向关系型数据库的

使用 JDBC，只需要学习 JDBC 规范接口的方法,即可操作大部分数据库软件，项目中期切换数据库软件,只需要更换对应的数据库驱动 jar 包,不需要更改代码。为了项目代码的可移植性，可维护性，SUN 公司从最初就制定了 Java 程序连接各种数据库的统一接口规范。这样的话，不管是连接哪一种 DBMS 软件，Java 代码可以保持一致性



------

### 核心类及接口



1. DriverManager
   * 将第三方数据库厂商的实现驱动 jar 注册到程序中
   * 可以根据数据库连接信息获取connection
2. Connection
   * 和数据库建立的连接,在连接对象上,可以多次执行数据库 curd 动作
   * 可以获取 statement 和 preparedstatement，callablestatement 对象
3. Statement | PreparedStatement | CallableStatement
   * 具体发送 SQL 语句到数据库管理软件的对象
4. Result
   * 面向对象思维的产物(抽象成数据库的查询结果表)
   - 存储 DQL 查询数据库结果的对象

   - 需要我们进行解析,获取具体的数据库数据



------

## 核心 API

### JDBC 使用的基本步骤



1. 注册驱动
2. 获取连接
3. 创建发送sql语句对象
4. 发送sql语句，并获取返回结果
5. 结果集解析
6. 资源关闭



------

### 基于 Statement 方式执行 SQL



```java
/**
 * 执行 select * from t_user where account = ? 的 sql 并打印输出结果
 */
public class JdbcStatementLoginPart {

    public static void main(String[] args) throws ClassNotFoundException, SQLException {

        String account = "test";

        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "root", "root");
        // 3. 创建statement
        Statement statement = connection.createStatement();
        // 4. 发送 sql 语句
        String sql = "select * from t_user where account = '"+account;
        ResultSet resultSet = statement.executeQuery(sql);
        // 5. 打印查询结果
        Systeml.out.println(resultSet.toString());
        // 6. 关闭资源
        resultSet.close();
        statement.close();
        connection.close();
        
    }

}
```



------

### 基于 PreparedStatement 方式执行 SQL



```java
/**
 * 执行 select * from t_user where account = ? 的 sql 并打印输出结果
 */
public class JdbcStatementLoginPart {

    public static void main(String[] args) throws ClassNotFoundException, SQLException {

        String account = "test";

        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "root", "root");
        // 3.创建preparedstatement并且传入SQL语句结构
        String sql = "select * from t_user where account = ? ;";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1,account);
        // 4. 打印查询结果
        Systeml.out.println(resultSet.toString());
        // 5. 关闭资源
        resultSet.close();
        statement.close();
        connection.close();
        
    }

}
```



------

### 二者的区别



![image-20230327223413023](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/JDBC%20%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5%E6%8A%80%E6%9C%AF/image-20230327223413023.png)



------

## JDBC 扩展提升

### 自增主键回显



java 程序获取插入数据时 mysql 维护自增长维护的主键id值,这就是主键回显

```java
@Test
public void  returnPrimaryKey() throws Exception{

    //1.注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");
    //2.获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///test?user=root&password=root");
    //3.编写SQL语句结构
    String sql = "insert into t_user (account,password,nickname) values (?,?,?);";
    //4.创建预编译的statement，传入SQL语句结构
    PreparedStatement statement = connection.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
    //5.占位符赋值
    statement.setObject(1,"towgog");
    statement.setObject(2,"123456");
    statement.setObject(3,"二狗子");
    //6.执行SQL语句
    int i = statement.executeUpdate();
    //7.结果集解析
    System.out.println("i = " + i);

    //一行一列的数据！里面就装主键值！
    ResultSet resultSet = statement.getGeneratedKeys();
    resultSet.next();
    int anInt = resultSet.getInt(1);
    System.out.println("anInt = " + anInt);


    //8.释放资源
    statement.close();
    connection.close();
}
```



------

### 批量插入优化



```java
@Test
public void  batchInsertYH() throws Exception{

    //1.注册驱动
    Class.forName("com.mysql.cj.jdbc.Driver");
    //2.获取连接
    Connection connection = DriverManager.getConnection("jdbc:mysql:///tesst?rewriteBatchedStatements=true",
            "root","root");
    //3.编写SQL语句结构
    String sql = "insert into t_user (account,password,nickname) values (?,?,?)";
    //4.创建预编译的statement，传入SQL语句结构
    long start = System.currentTimeMillis();
    PreparedStatement statement = connection.prepareStatement(sql);
    for (int i = 0; i < 10000; i++) {
        //5.占位符赋值
        statement.setObject(1,"test"+i);
        statement.setObject(2,"test");
        statement.setObject(3,"Simon"+i);
        //6.组装
        statement.addBatch();
    }

    // 批量操作
    statement.executeBatch();

    long end = System.currentTimeMillis();

    System.out.println("消耗时间："+(end - start));
    //7.结果集解析
    //8.释放资源
    connection.close();
}
```

