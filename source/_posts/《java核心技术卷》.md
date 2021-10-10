---
title: 《java核心技术卷》
date: 2021-09-15 23:07:42
tags:
---



## Java的基本程序设计结构

### 数据类型

#### 整型



![image-20210919190127213](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210919190127213.png)



长整型数值有一个后缀 L，十六进制数值有一个前缀 0x，八进制有一个前缀 0。很显然，八进制表示法比较容易混淆，所以最好不要使用



从 java7开始，加上前缀 0b 可表示二进制数，还可以为数字字面量加下划线，如 1_000_000 等同于 1000000



------

#### 浮点型



![image-20210919223606459](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210919223606459.png)



double 类型的数值精度是 float 类型的两倍，float 类型的数值有一个后缀 F，没有后缀 F 的浮点类型默认为 double 类型，也可以在浮点类型后面加 D



Double.POSITIVE_INFINITY、Double.NEGATIVE_INFINITY 和 Double.NaN 分别表示正无穷大、负无穷大和不是一个数字，但在实际中很少遇到。值得一提的是，不能用 x == Double.NaN 这个方式来检测一个值是否是一个数字，而应该用 Double.NaN(x) 这种方法



------

#### char 类型



char 类型用于表示单个字符，在 java 中，char 类型用 UTF-16 编码描述一个代码单元



强烈建议不要在代码中使用 char 类型，除非确实需要对 UTF-16 编码进行操作，最好将需要处理的字符串用抽象类型进行表示
