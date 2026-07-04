---
title: JDK 12 ~ JDK 17 新特性
date: 2023-03-14 00:01:44
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%2012%20~%20JDK%2017%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%2012%20~%20JDK%2017%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
tags:
  - Java
  - JDK
categories: Java
---





# JDK 12

## 简介



JDK 12 是 Java 开启半年发布周期后的第三个正式版本，属于非长期支持（Non-LTS）版本，承上启下连接了 JDK 11 LTS 与后续的 Java 现代化语法体系。它最具标志性的贡献是首次引入 Switch 表达式（预览版），开启了 Java 语法向简洁化、表达式化演进的重要里程碑；同时在垃圾回收领域再添新军，加入Shenandoah 低延迟垃圾收集器，进一步丰富了 Java 低延迟技术选型

除核心特性外，JDK 12 还带来了紧凑数字格式化、G1 回收算法优化、JMH 基准测试套件原生集成、常量 API 增强等大量务实改进，在开发效率、性能优化、工具链完善三个维度同步推进。虽然并非 LTS 版本，但它孵化的多项核心特性（如 Switch 表达式）在后续 JDK 17 LTS 中正式落地，是理解 Java 语法演进脉络的关键版本



---

## 语法里程碑：Switch 表达式（预览版）



Switch 表达式是 JDK 12 最重磅、最具代表性的特性，它将传统的 Switch 语句升级为表达式，大幅简化了分支逻辑的代码编写，是 Java 语法现代化的重要一步

传统的`switch`是语句（Statement），没有返回值，需要通过 break 控制分支，代码冗余且容易出现 "穿透" 问题。JDK 12 引入的 Switch 表达式（预览版）将其升级为表达式（Expression），可以直接返回值，并且新增了箭头语法（`case L ->`），一个分支执行完毕自动终止，无需手动写 break

该特性在 JDK 12 为预览状态，需要添加编译和运行参数`--enable-preview`才能使用，后续版本持续优化，最终在 JDK 14 正式转正



传统 Switch 语句存在多个长期被诟病的痛点：

```Java
public class TraditionalSwitchDemo {
    public static void main(String[] args) {
        String day = "MONDAY";
        String result;

        // 痛点1：每个case都要写break，遗漏会导致穿透bug
        // 痛点2：代码冗长，大量样板代码
        // 痛点3：不能直接返回值，需要外部变量承接结果
        switch (day) {
            case "MONDAY":
            case "TUESDAY":
            case "WEDNESDAY":
            case "THURSDAY":
            case "FRIDAY":
                result = "工作日";
                break;
            case "SATURDAY":
            case "SUNDAY":
                result = "周末";
                break;
            default:
                throw new IllegalArgumentException("无效的星期：" + day);
        }

        System.out.println(result);
    }
}
```



**Switch 表达式解决方案**：

```Java
public class SwitchExpressionDemo {
    public static void main(String[] args) {
        String day = "MONDAY";

        // 箭头语法 + 多Case匹配 + 直接返回值
        String result = switch (day) {
            case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> "工作日";
            case "SATURDAY", "SUNDAY" -> "周末";
            default -> throw new IllegalArgumentException("无效的星期：" + day);
        };

        System.out.println(result);
    }
}
```



---

##  紧凑数字格式化



`CompactNumberFormat`是 JDK 12 新增的数字格式化类，位于`java.text`包下，用于将大数字格式化为简洁易读的紧凑形式，例如将`1000`显示为`1K`，`1000000`显示为`1M`，自动适配不同语言和地区的习惯

在此之前，实现类似功能需要手动编写工具类，或者依赖第三方库，原生 API 的补齐大幅降低了开发成本



```Java
public class CompactNumberDemo {
    public static void main(String[] args) {
        // 获取中文环境的紧凑数字格式（短格式）
        NumberFormat cnShort = NumberFormat.getCompactNumberInstance(Locale.CHINA, NumberFormat.Style.SHORT);
        System.out.println(cnShort.format(1000));      // 1,000？不，输出：1000
        System.out.println(cnShort.format(10000));     // 1万
        System.out.println(cnShort.format(12345));     // 1万
        System.out.println(cnShort.format(1000000));   // 100万
        System.out.println(cnShort.format(1234567));   // 123万
        System.out.println(cnShort.format(100000000)); // 1亿

        System.out.println("------------------------");

        // 获取英文环境的紧凑数字格式（短格式）
        NumberFormat usShort = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);
        System.out.println(usShort.format(1000));      // 1K
        System.out.println(usShort.format(1000000));   // 1M
        System.out.println(usShort.format(1000000000));// 1B

        System.out.println("------------------------");

        // 长格式
        NumberFormat cnLong = NumberFormat.getCompactNumberInstance(Locale.CHINA, NumberFormat.Style.LONG);
        System.out.println(cnLong.format(10000));     // 10,000？不，输出：1万
        System.out.println(cnLong.format(1000000));   // 1百万
    }
}
```



---

## Shenandoah 低延迟垃圾收集器（实验性）



Shenandoah 是由 RedHat 主导开发的低延迟垃圾收集器，JDK 12 正式加入 OpenJDK（实验性）。它的核心设计目标是将 GC 停顿时间控制在毫秒级，且停顿时间不随堆内存大小增长，支持从百 MB 到数十 TB 的堆内存规模

与 ZGC 类似，Shenandoah 也是一款以低延迟为核心目标的并发收集器，但实现路径不同：Shenandoah 采用 Brooks 指针 技术实现并发整理，而 ZGC 采用着色指针技术



---

# JDK 13

## 简介



JDK 13 是 Java 开启半年发布周期后的第四个正式版本，属于非长期支持（Non-LTS）版本，是 Java 现代化语法演进道路上的重要打磨版本。它延续了 JDK 12 的语法革新方向，对 Switch 表达式进行了第二次预览优化，正式引入`yield`关键字规范返回值语义；同时重磅推出**文本块（Text Blocks）预览特性**，彻底解决了 Java 多行字符串书写繁琐、可读性差的历史痛点

除语法层面的核心升级外，JDK 13 在性能优化上持续深耕：ZGC 新增内存归还能力，可动态将闲置内存返还给操作系统，大幅提升容器环境的资源利用率；动态 CDS 归档大幅简化了类数据共享的使用流程，降低了性能优化的门槛。此外，Socket API 底层重写、Unicode 升级等平台级改进，也进一步夯实了 Java 的运行稳定性与开发效率



---

## Switch 表达式（第二次预览）



Switch 表达式在 JDK 12 首次预览后，JDK 13 进行了语法优化与语义规范，核心变化是引入`yield`关键字专门用于返回值，彻底解决了`break`语义歧义的问题，语法形态趋于最终稳定

JDK 12 的 Switch 表达式使用`break 值`的方式返回结果，但`break`本身既有 “跳出循环 / 分支” 的语义，又承担了 “返回值” 的功能，存在语义歧义。JDK 13 引入专门的 `yield`关键字 ，用于在 Switch 表达式的代码块中返回结果，职责更单一，语义更清晰

该特性在 JDK 13 仍为预览状态，需添加`--enable-preview`参数启用，最终在 JDK 14 正式转正



JDK 12 写法的痛点：break 语义重载，可读性差，易与传统 break 混淆

```Java
// JDK 12 写法：break 既跳出又返回值，语义模糊
String result = switch (day) {
    case "MONDAY", "FRIDAY", "SUNDAY" -> 6;
    case "TUESDAY" -> 7;
    case "THURSDAY", "SATURDAY" -> 8;
    case "WEDNESDAY" -> 9;
    default -> {
        // 用break返回值，容易和传统break混淆
        break "无效";
    }
};
```

`break`关键字同时承担了 “终止分支” 和 “返回结果” 两个职责，对于熟悉传统 Switch 的开发者来说容易产生误解，也不利于代码的可读性与维护性



JDK 13 的解决方案：引入`yield`专门负责返回值

```Java
// JDK 13 写法：yield 专门用于返回值，语义清晰
String result = switch (day) {
    case "MONDAY", "FRIDAY", "SUNDAY" -> 6;
    case "TUESDAY" -> 7;
    default -> {
        // yield 专门用于返回表达式结果
        yield "无效的星期：" + day;
    }
};
```

`yield`职责单一，语义明确，与传统的`break`彻底区分，代码可读性显著提升，也降低了学习成本



**yield 与 return 的区别**

- `yield`：仅用于 Switch 表达式内部，作用是结束当前 Switch 分支并返回结果给表达式
- `return`：用于方法内部，作用是结束整个方法并返回结果
- 在 Switch 表达式的代码块中，不能使用`return`，只能使用`yield`



---

## 文本块（预览版）



文本块（Text Blocks）是 JDK 13 最受关注的新特性之一，它以原生语法支持多行字符串，彻底解决了 Java 多行字符串书写繁琐、可读性差的长期痛点

文本块是一种特殊的字符串字面量，使用三个双引号`"""`作为起止标记，可以直接书写多行内容，无需手动拼接字符串、无需转义换行符，编译器会自动处理换行与缩进，最终生成的仍然是普通的`String`对象

该特性在 JDK 13 为首次预览，JDK 14 二次预览，JDK 15 正式转正，是 Java 字符串处理的重大改进



在 JDK 13 之前，书写多行字符串需要使用字符串拼接、换行转义符，代码极其丑陋，可读性极差，尤其是 SQL、JSON、HTML 等多行文本场景

```Java
public class TraditionalMultiLineString {
    public static void main(String[] args) {
        // 痛点1：需要+拼接，大量\n转义，代码臃肿
        String sql = "SELECT id, name, age " +
                     "FROM user " +
                     "WHERE age > 18 " +
                     "ORDER BY create_time DESC";

        // 痛点2：JSON格式完全无法直观展示
        String json = "{\"name\":\"张三\", " +
                      "\"age\":25, " +
                      "\"email\":\"zhangsan@example.com\"}";

        System.out.println(sql);
        System.out.println(json);
    }
}
```



文本块解决方案：

```Java
public class TextBlockDemo {
    public static void main(String[] args) {
        // SQL文本块：格式清晰，直接书写
        String sql = """
                     SELECT id, name, age
                     FROM user
                     WHERE age > 18
                     ORDER BY create_time DESC
                     """;

        // JSON文本块：无需转义双引号
        String json = """
                      {
                          "name": "张三",
                          "age": 25,
                          "email": "zhangsan@example.com"
                      }
                      """;

        System.out.println(sql);
        System.out.println(json);
    }
}
```

代码格式与最终输出格式一致，直观易读，编写和修改都非常方便，彻底告别拼接与转义的繁琐



---

## ZGC 增强与 GC 优化



JDK 13 在垃圾回收领域持续深耕，对主力低延迟收集器 ZGC 进行了重要能力升级，同时对 G1 等收集器做了细节优化，进一步提升了生产环境的性能表现

在 JDK 12 及之前，ZGC 一旦分配了堆内存，就不会再归还给操作系统，即使应用负载很低、大量内存闲置，也会一直占用。这在容器化部署、按内存计费的云环境中，会造成严重的资源浪费

JDK 13 为 ZGC 新增了未使用内存动态归还能力：当应用负载降低、堆内存使用率不高时，ZGC 会自动将空闲的内存页归还给操作系统，降低应用的实际内存占用；当负载回升时，再重新分配内存



---

## 核心 API 与平台增强



**动态 CDS 归档（Dynamic CDS Archives）**

JDK 10 开源了 AppCDS（应用类数据共享），但使用流程繁琐，需要先运行应用生成类列表，再根据列表生成归档文件

JDK 13 引入动态 CDS 归档，大幅简化了使用流程：只需在应用退出时自动将已加载的类信息保存为归档文件，下次启动直接使用即可，无需手动生成类列表，一步完成 CDS 归档

```SH
# 第一次运行：退出时自动生成归档文件 app.jsa
java -XX:ArchiveClassesAtExit=app.jsa -jar app.jar

# 后续运行：直接使用归档文件，提升启动速度
java -XX:SharedArchiveFile=app.jsa -jar app.jar
```



**Socket API 底层重新实现**

JDK 13 重新实现了`java.net.Socket`和`java.net.ServerSocket`的底层实现，用更现代、更简洁的 NIO 机制替换了沿用几十年的老旧原生代码



**Unicode 12.1 支持**

JDK 13 将 Unicode 支持版本升级到 12.1，新增了大量字符、表情符号和脚本支持，更好地满足国际化业务需求



---

# JDK 14

## 简介



JDK 14 的核心定位是语法爆发迭代 + GC 格局重塑 + 体验全面优化，是现代 Java 语法体系的奠基版本。核心变革分为四大板块

1. 语法体系重大升级：Switch 表达式正式转正，instanceof 模式匹配、Record 记录类两大特性首次预览，大幅削减样板代码，现代 Java 语法雏形初现
2. GC 生态迭代重塑：ZGC 实现跨平台支持，G1 新增 NUMA 架构优化，CMS 正式退役，垃圾收集器格局进一步清晰
3. 开发体验深度优化：文本块二次预览完善语法，空指针异常精准定位，调试效率显著提升
4. 底层能力持续夯实：JFR 流式监控、隐藏类、安全体系升级等底层能力持续完善，为框架与上层应用赋能



---

## Switch 表达式（正式转正）



经过 JDK 12、JDK 13 两轮预览迭代与社区反馈优化，Switch 表达式在 JDK 14 中正式成为 Java 语言标准特性，不再需要添加`--enable-preview`参数即可使用，语法形态完全稳定

Switch 表达式是对传统 Switch 语句的现代化升级，它将 Switch 从 “语句” 升级为 “表达式”，支持直接返回值，配套箭头语法、多 Case 匹配、yield 返回值三大核心设计，彻底解决了传统 Switch 代码冗余、break 易遗漏、无返回值等长期痛点

正式版与 JDK 13 预览版语法基本一致，同时补充了枚举类型全分支覆盖校验等细节优化，生产环境可稳定使用



---

## instanceof 模式匹配（预览版）



模式匹配是现代编程语言的核心特性之一，JDK 14 首次将其引入 Java，率先落地了 instanceof 的模式匹配能力，简化了类型判断后的强转样板代码

传统的`instanceof`只能判断对象是否为某个类型，判断通过后仍需手动强转才能使用对应类型的方法，存在重复的类型声明。模式匹配优化了这一流程：在判断类型的同时，直接定义对应类型的局部变量，省去手动强转步骤，代码更简洁且不易出错

该特性为 JDK 14 预览版，需添加`--enable-preview`参数启用，经过 JDK 15、16 持续优化后，在 JDK 16 正式转正



类型判断 + 强转是 Java 开发中的高频操作，传统写法存在严重的样板代码冗余，且强转逻辑重复，容易写错

```Java
public int getLength(Object obj) {
    // 先判断类型
    if (obj instanceof String) {
        // 再手动强转，重复声明类型
        String s = (String) obj;
        return s.length();
    }
    if (obj instanceof Integer) {
        Integer num = (Integer) obj;
        return num.toString().length();
    }
    return 0;
}
```



模式匹配解决方案：

```Java
public int getLength(Object obj) {
    if (obj instanceof String s) {
        // 直接使用s，无需强转
        return s.length();
    }
    if (obj instanceof Integer num) {
        return num.toString().length();
    }
    return 0;
}
```

判断与变量定义一步完成，消除了冗余的强转代码，逻辑更紧凑，也避免了强转类型写错的低级错误



---

## Record 记录类（预览版）



Record 是 JDK 14 引入的全新类型，专门用于定义不可变数据载体类，可以自动生成构造器、访问器、equals、hashCode、toString 等样板方法，极大简化了纯数据类的代码编写



Record 是一种特殊的类，使用`record`关键字声明，在类头中直接声明所有字段。编译器会自动生成：

- 私有 final 的实例字段，保证不可变性
- 全参构造器
- 与字段同名的访问器方法（如`x()`，而非`getX()`）
- 基于所有字段的 equals ()、hashCode () 方法
- 包含类名与字段信息的 toString () 方法

Record 的设计目标是成为 “透明的数据载体”，语义明确，专门用于承载数据，不包含业务逻辑。该特性在 JDK 14 首次预览，JDK 15 二次预览，JDK 16 正式转正



传统 Java 定义一个纯数据类（如 DTO、VO）需要编写大量重复的样板代码：私有字段、构造器、getter、equals、hashCode、toString，一个简单的二维点类就要几十行代码，要么靠 IDE 生成，要么依赖 Lombok 等第三方工具

```Java
// 传统写法：近30行代码实现一个简单数据类
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Point)) return false;
        Point point = (Point) o;
        return x == point.x && y == point.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }

    @Override
    public String toString() {
        return "Point{x=" + x + ", y=" + y + '}';
    }
}
```

Record 解决方案：

```Java
// 一行代码完成所有功能，语义完全一致
public record Point(int x, int y) {}
```

代码量减少 90% 以上，语义明确，默认不可变，避免了手动编写方法的疏漏



**基础定义**

```Java
// 声明Record：类名后括号内为所有组件（字段）
public record User(Long id, String username, Integer age) {}

// 使用方式和普通类完全一致
public class RecordDemo {
    public static void main(String[] args) {
        User user = new User(1L, "张三", 25);
        
        // 访问器方法：字段名()，不是get字段名()
        System.out.println(user.id());       // 1
        System.out.println(user.username()); // 张三
        
        // 自动生成的toString
        System.out.println(user); // User[id=1, username=张三, age=25]
        
        // 基于字段的equals
        User user2 = new User(1L, "张三", 25);
        System.out.println(user.equals(user2)); // true
    }
}
```



**紧凑构造器（参数校验）**

如果需要在构造时做参数校验，可以使用紧凑构造器，无需声明参数列表，直接编写校验逻辑，执行完毕后自动为字段赋值

```Java
public record User(Long id, String username, Integer age) {
    // 紧凑构造器：无需写参数列表
    public User {
        if (id == null || id <= 0) {
            throw new IllegalArgumentException("ID非法");
        }
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("用户名不能为空");
        }
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("年龄不合法");
        }
    }
}
```



---

## JVM 与 GC 重磅特性



**ZGC 正式支持 macOS 与 Windows**

JDK 11 引入 ZGC 时仅支持 Linux 平台，开发人员本地测试 ZGC 需要部署 Linux 环境，门槛较高。JDK 14 将 ZGC 正式移植到 macOS 和 Windows 平台，实现三大主流操作系统全覆盖



**G1 收集器 NUMA 感知优化**

NUMA（非统一内存访问）是多 CPU 服务器的主流架构：每个 CPU 有专属的本地内存，访问本地内存速度远快于跨 CPU 访问远端内存

JDK 14 为 G1 收集器新增了 NUMA 感知能力：G1 在分配内存时，会优先将对象分配到当前 CPU 的本地内存区域，减少跨 NUMA 节点的内存访问延迟，显著提升大内存多 CPU 服务器的应用性能



**CMS 垃圾收集器正式移除**

CMS（Concurrent Mark Sweep）曾是服务端最主流的低延迟垃圾收集器，但其实现复杂、内存碎片化严重、维护成本极高。从 JDK 9 开始被标记为废弃，JDK 14 正式彻底移除，相关的启动参数也一并失效



---

## 文本块（第二次预览）



文本块在 JDK 13 首次预览后，JDK 14 迎来第二次预览迭代。本次迭代重点优化了附带空白的计算逻辑，修复了边缘场景的缩进计算偏差，进一步规范了转义序列行为，语法形态趋于稳定，为 JDK 15 正式转正做好了准备

核心功能与 JDK 13 保持一致：三引号`"""`标记、自动去除共有前导空白、支持行尾反斜杠取消换行、`\s`保留末尾空格



---

## 空指针异常精准提示（Helpful NPE）



传统的`NullPointerException`只会报出异常所在行号，但一行代码中如果有多个链式调用（如`user.getAddress().getCity().length()`），开发者无法直接判断到底是哪个变量为 null，调试效率低

JDK 14 增强了 NPE 的异常信息，会精准指出导致空指针的具体变量与调用链，大幅提升问题定位效率



```Java
User user = new User();
int len = user.getAddress().getCity().length();
```

传统 NPE 信息：

```
Exception in thread "main" java.lang.NullPointerException
    at Demo.main(Demo.java:8)
```

JDK 14 增强后：

```
Exception in thread "main" java.lang.NullPointerException:
  Cannot invoke "String.length()" because the return value of "Address.getCity()" is null
    at Demo.main(Demo.java:8)
```

异常信息明确说明是`getCity()`的返回值为 null，无需逐行排查



通过参数`-XX:+ShowCodeDetailsInExceptionMessages`控制，JDK 14 默认开启

---

# JDK 15

## 简介



JDK 15 是 Java 开启半年发布周期后的第六个正式版本，属于非长期支持（Non-LTS）版本，是 Java 现代化进程中特性集中落地、语法体系持续完善的重要节点。经过多轮预览打磨，文本块、ZGC、Shenandoah GC、隐藏类四大核心特性正式转正，结束实验阶段成为 Java 标准能力；同时重磅推出密封类（Sealed Classes）首次预览，填补了 Java 类型系统中 “受控继承” 的空白，与 Record、模式匹配形成现代 Java 语法的铁三角

除语法与 GC 的核心升级外，JDK 15 在底层基建层面持续推进：DatagramSocket API 完成底层重写，与 JDK 13 的 Socket 重写形成完整的网络 API 升级；Nashorn JavaScript 引擎正式移除，偏向锁永久禁用，JDK 生态进一步轻量化、现代化。整体来看，JDK 15 承上启下，既沉淀了前几个版本的核心创新，又为 JDK 17 LTS 的特性定型打下了坚实基础



---

## 文本块（正式转正）



经过 JDK 13 首次预览、JDK 14 二次预览优化，文本块（Text Blocks）在 JDK 15 中正式成为 Java 语言标准特性，语法完全稳定，可直接用于生产环境，无需任何预览参数

文本块是 Java 原生的多行字符串语法，使用三个双引号`"""`作为起止标记，支持直接书写多行内容，自动处理换行与缩进，无需手动拼接字符串、转义换行符与双引号。转正后的文本块语法稳定、语义明确，彻底解决了 Java 多行字符串书写繁琐、可读性差的长期痛点



---

## 两大低延迟 GC 正式转正



JDK 15 迎来了垃圾收集器领域的重要里程碑：ZGC 与 Shenandoah GC 双双结束实验阶段，正式成为 Java 标准特性，无需额外解锁参数即可启用。至此 Java 形成了 “G1 通用均衡、Parallel 高吞吐、ZGC/Shenandoah 低延迟” 的完整 GC 选型体系

ZGC 是 Oracle 主导研发的低延迟垃圾收集器，核心设计目标是停顿时间不超过 10ms，且与堆内存大小无关，支持从 MB 级到 TB 级的堆内存。经过 JDK 11 到 JDK 14 的多轮迭代优化，稳定性与性能逐步成熟，JDK 15 正式转正

Shenandoah 是 RedHat 主导研发的低延迟垃圾收集器，核心目标与 ZGC 一致 —— 实现极低的 GC 停顿，采用 Brooks 指针技术实现并发内存整理。JDK 12 首次加入实验版本，JDK 15 正式转正



---

## 密封类（首次预览）



密封类（Sealed Classes）是 JDK 15 推出的重磅语法特性，首次预览即引发广泛关注。它填补了 Java 类型系统的长期空白，允许开发者精确控制一个类或接口可以被哪些类继承 / 实现，实现 “受控继承”，是现代 Java 类型体系的重要组成部分



传统 Java 中，类的继承只有两种状态：

- `final`修饰：完全禁止继承
- 无 `final` 修饰：任何类都可以继承（同包 / 跨包均可）

密封类提供了中间状态：类可以被继承，但只能被指定的子类继承。通过`sealed`关键字声明密封类，配合`permits`关键字指定允许的子类列表，实现对继承关系的精确控制。

该特性在 JDK 15 首次预览，JDK 16 二次预览，JDK 17 正式转正，是模式匹配、Switch 模式增强的重要基础



**密封类声明**

使用`sealed`关键字修饰类，`permits`关键字列出所有允许的子类：

```Java
// 密封类Shape，只允许被Circle、Rectangle、Triangle三个类继承
public sealed class Shape permits Circle, Rectangle, Triangle {
    public abstract double area();
}
```



**子类的三种修饰符**

继承密封类的子类，必须显式声明以下三种修饰符之一：

- `final`：子类不能再被继承，终止继承链
- `sealed`：子类也是密封类，可以继续指定允许的子类
- `non-sealed`：子类开放继承，回到传统的任意继承状态

```Java
// final：终止继承
public final class Circle extends Shape {
    private final double radius;
    public Circle(double radius) { this.radius = radius; }
    @Override
    public double area() { return Math.PI * radius * radius; }
}

// sealed：继续密封，限定子类
public sealed class Rectangle extends Shape permits Square {
    private final double width, height;
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    @Override
    public double area() { return width * height; }
}

// non-sealed：开放继承
public non-sealed class Triangle extends Shape {
    // 任何类都可以继承Triangle
}

// 继承密封子类Rectangle
public final class Square extends Rectangle {
    public Square(double side) { super(side, side); }
}
```



**密封接口**

接口也可以声明为密封接口，限制允许的实现类：

```Java
public sealed interface Operation permits AddOp, SubOp, MulOp {
    int apply(int a, int b);
}

public final class AddOp implements Operation {
    @Override
    public int apply(int a, int b) { return a + b; }
}
```



密封类 + Record 是 Java 实现代数数据类型（ADT）的标准方式，二者配合可以简洁地定义不可变数据层级，是现代 Java 的典型写法



---

## Record 记录类（二次预览）



主要优化点

1. 支持本地 Record：允许在方法内部定义局部 Record，用于临时数据封装，作用域限制在方法内部
2. 嵌套 Record 支持：支持 Record 的嵌套定义，更灵活地组织复杂数据结构
3. 注解支持增强：完善了 Record 组件上的注解传递机制，注解可以正确映射到字段、访问器方法等位置
4. 紧凑构造器优化：简化了紧凑构造器的参数传递逻辑，代码编写更自然



```Java
public class LocalRecordDemo {
    public void processUsers(List<User> users) {
        // 方法内部定义本地Record，仅在当前方法内可用
        record UserStats(String name, int age, boolean isAdult) {}

        List<UserStats> stats = users.stream()
                .map(user -> new UserStats(
                        user.getName(),
                        user.getAge(),
                        user.getAge() >= 18
                ))
                .toList();
    }
}
```



---

## instanceof 模式匹配（二次预览）



主要优化点

1. 作用域规则优化：进一步明确了模式变量的作用域范围，修复了边缘场景下的作用域歧义问题
2. 表达式场景支持：拓展了模式变量可使用的表达式场景，语法更灵活
3. 类型推断增强：优化了泛型场景下的类型推断逻辑，匹配更精准



```Java
@Override
public boolean equals(Object o) {
    // 模式匹配+链式条件，简洁严谨
    return o instanceof User user
            && Objects.equals(id, user.id)
            && Objects.equals(username, user.username);
}
```



---

## 隐藏类（正式转正）



隐藏类（Hidden Classes）在 JDK 14 首次预览，JDK 15 正式转正。它是一种无法被其他类直接发现的特殊类，不能通过类名直接加载，主要服务于动态字节码生成框架（如 Spring CGLIB、动态代理、Lambda 表达式实现）



 核心价值

- 生命周期灵活：隐藏类可以和使用它的类一起被卸载，降低元空间内存占用，避免类加载泄漏。
- 安全性更高：无法被其他类反射访问，提升了动态生成类的安全性。
- 性能更优：减少了动态类的加载开销，提升框架运行效率。

普通业务开发者一般不会直接编写隐藏类，但主流框架升级后会自动受益，无需额外改造



---

# JDK 16

## 简介



JDK 16 是 Java 开启半年发布周期后的第七个正式版本，属于非长期支持（Non-LTS）版本，也是衔接 JDK 17 LTS 的最后一个过渡版本，承担了 “特性定型、能力收敛” 的关键作用。经过多轮预览打磨，instanceof 模式匹配、Record 记录类两大核心语法特性正式转正，成为 Java 语言标准能力；密封类完成第二次预览优化，语法形态完全稳定，为 JDK 17 转正做好最终准备

除语法层面的核心落地外，JDK 16 在 JVM 性能领域持续深耕：ZGC 实现并发线程栈处理，将停顿时间压缩至亚毫秒级；弹性元空间大幅优化元空间内存管理，降低内存碎片与资源占用。同时，Vector API、外部链接器 API 等孵化特性稳步推进，Project Panama 与 Project Vector 的成果逐步落地，为 Java 的高性能计算、原生交互场景打开了全新空间。整体来看，JDK 16 的特性成熟度极高，几乎是 JDK 17 LTS 的预演版本



---

## instanceof 模式匹配（正式转正）



经过 JDK 14、JDK 15 两轮预览迭代与社区反馈优化，instanceof 模式匹配在 JDK 16 中正式成为 Java 语言标准特性，语法完全稳定，无需任何预览参数即可在生产环境使用



---

## Record 记录类（正式转正）



经过 JDK 14、JDK 15 两轮预览优化，Record 记录类在 JDK 16 正式成为 Java 语言标准特性，成为不可变数据载体的官方标准写法



---

## 密封类（第二次预览）



密封类在 JDK 15 首次预览后，JDK 16 迎来第二次预览迭代。本次预览重点优化了类型校验规则、子类推断逻辑与访问控制细节，语法形态完全稳定，为 JDK 17 正式转正做好了最终准备



本次预览的核心优化点

1. permits 推断更智能：同模块、同包下的子类，编译器可更精准地自动推断，部分场景可省略 permits 关键字
2. 类型校验更严格：完善了密封层级的类型兼容性校验，避免非法继承
3. 嵌套密封类支持优化：内部类、嵌套类的密封声明规则更清晰
4. 与模式匹配协同优化：为后续 Switch 模式匹配的穷尽性校验打好了基础



---

## 集合与 Stream API 增强



JDK 16 对集合与 Stream API 进行了实用化补齐，新增了多个高频便捷方法，进一步简化日常集合操作，其中最具代表性的是`Stream.toList()`方法

JDK 16 为`Stream`接口新增了直接的`toList()`方法，可直接将 Stream 收集为 List，无需再通过`Collectors.toList()`收集，代码更简洁

返回的是不可变 List，不支持添加、删除、修改操作，且不允许包含 null 元素，与`List.of()`返回的集合特性一致



---

## JVM 与 GC 重磅优化



**ZGC 并发线程栈处理**

在 JDK 15 及之前，ZGC 的 GC 周期中，线程栈扫描阶段需要暂停所有用户线程（STW），这也是 ZGC 最主要的停顿来源之一

JDK 16 将线程栈扫描改为并发执行：GC 线程与用户线程并发执行栈扫描，无需 STW。这一优化将 ZGC 的典型停顿时间从毫秒级进一步压缩至亚毫秒级（<1ms），几乎达到了无感知的低延迟水平



**弹性元空间（Elastic Metaspace）**

元空间（Metaspace）用于存储类的元数据，传统元空间的内存管理较为粗放，存在内存碎片多、闲置内存归还不及时、内存占用偏高的问题

JDK 16 引入弹性元空间，重新设计了元空间的内存分配算法：

- 更细粒度的内存分片，大幅减少内存碎片
- 更及时地将未使用的元空间内存归还给操作系统
- 优化了类卸载后的内存回收逻辑
- 整体降低元空间的内存占用，典型场景可减少 20% 以上的元空间内存



---

## Vector API（首次孵化器）



Vector API 是 Project Vector 的核心成果，它提供了一套标准的向量计算 API，能够将 Java 代码自动编译为 CPU 的 SIMD（单指令多数据流）指令，实现数据并行计算，大幅提升数值计算、图像处理、大数据处理等场景的性能

传统 Java 只能通过循环逐次计算，而 Vector API 可以利用 CPU 的向量寄存器一次处理多个数据，性能可提升数倍甚至一个数量级



```Java
// 向量加法：一次计算多个元素
float[] a = new float[256];
float[] b = new float[256];
float[] c = new float[256];

// 传统写法：循环逐次计算
for (int i = 0; i < 256; i++) {
    c[i] = a[i] + b[i];
}

// Vector API写法：自动利用SIMD并行计算
var species = FloatVector.SPECIES_PREFERRED;
for (int i = 0; i < 256; i += species.length()) {
    var va = FloatVector.fromArray(species, a, i);
    var vb = FloatVector.fromArray(species, b, i);
    va.add(vb).intoArray(c, i);
}
```



普通业务开发使用较少，主要面向高性能计算领域



---

# JDK 17

## 简介



JDK 17 是 Java 开启半年发布周期后的第八个正式版本，也是继 JDK 8、JDK 11 之后的第三个长期支持版本（LTS），Oracle 官方提供长达 8 年的标准支持与扩展维护，是当前企业生产环境的首选版本，也是现代 Java 技术栈的核心基准。它集 JDK 9 以来所有特性迭代之大成，密封类等核心语法正式转正，Switch 模式匹配开启预览，JVM 性能、安全性、平台适配全方位升级，同时完成了内部 API 强封装等重要架构调整，标志着 Java 模块化转型的最终落地

作为承前启后的里程碑版本，JDK 17 沉淀了前序多个版本的预览与实验特性，形成了以「Record + 模式匹配 + 密封类」为核心的现代语法体系，同时在低延迟 GC、云原生适配、安全加固等生产能力上持续深化。无论是从 JDK 8/11 升级迁移，还是新项目技术选型，JDK 17 都是当前最稳妥、收益最高的版本选择



---

## 密封类（正式转正）



经过 JDK 15 首次预览、JDK 16 二次优化，密封类（Sealed Classes）在 JDK 17 中正式成为 Java 语言标准特性，语法完全稳定，无需任何预览参数即可用于生产环境。它与 Record、模式匹配共同构成了现代 Java 的类型体系铁三角



---

## Switch 模式匹配（首次预览）



Switch 模式匹配是 JDK 17 最重磅的语法预览特性，它将模式匹配能力引入 Switch 表达式，彻底替代了传统「instanceof 判断 + 强转 + if-else 分支」的冗余写法，让多类型分支逻辑简洁优雅。该特性后续会持续迭代，在 JDK 21 LTS 中正式转正

传统 Switch 只能匹配枚举、字符串、整数等简单常量值，面对多类型判断场景只能依赖多层 if-else + instanceof 强转，代码层级深、冗余度高。Switch 模式匹配扩展了 Switch 的匹配能力，支持类型模式匹配，可以直接匹配对象的类型并自动绑定变量，无需手动强转，同时支持守卫条件、null 处理等能力

该特性在 JDK 17 为首次预览版，需添加`--enable-preview`参数启用，语法形态会在后续版本小幅优化，但核心设计思想保持一致



**传统写法示例**：

```Java
public String formatValue(Object obj) {
    if (obj == null) {
        return "空值";
    }
    if (obj instanceof String s) {
        return "字符串：" + s;
    } else if (obj instanceof Integer i) {
        return "整数：" + i;
    } else if (obj instanceof Double d) {
        return "浮点数：" + d;
    } else if (obj instanceof Boolean b) {
        return "布尔值：" + b;
    } else {
        return "未知类型：" + obj.getClass().getSimpleName();
    }
}
```



**Switch 模式方案**：

```Java
public String formatValue(Object obj) {
    return switch (obj) {
        case null -> "空值";
        case String s -> "字符串：" + s;
        case Integer i -> "整数：" + i;
        case Double d -> "浮点数：" + d;
        case Boolean b -> "布尔值：" + b;
        default -> "未知类型：" + obj.getClass().getSimpleName();
    };
}
```

代码量减少近 40%，结构扁平清晰，类型判断与变量绑定一步完成，null 处理优雅直观



---

## 增强伪随机数生成器体系



JDK 17 对随机数生成器进行了体系化重构（JEP 356），新增了`RandomGenerator`统一接口，整合了所有随机数生成器的公共方法；同时新增了多种高质量、高性能的随机数算法（如 Xoroshiro、Xoshiro 系列），替代老旧的`Random`实现

重构后既保持了对旧 API 的完全兼容，又提供了更灵活、更高性能的随机数能力，支持按算法名获取实例、随机数流生成、可拆分随机数等多种能力



```Java
public class RandomDemo {
    public static void main(String[] args) {
        // 获取默认随机数生成器
        RandomGenerator rng = RandomGenerator.getDefault();

        // 生成各种类型的随机数，API统一规范
        int intVal = rng.nextInt(100);
        double doubleVal = rng.nextDouble(0, 1);
        long longVal = rng.nextLong();
        boolean flag = rng.nextBoolean();
    }
}
```



通过算法名获取对应实现，灵活选择不同特性的随机数生成器：

```Java
// 高性能Xoshiro算法
RandomGenerator xoshiro = RandomGenerator.of("Xoshiro256PlusPlus");

// 加密安全的随机数生成器
RandomGenerator secure = RandomGenerator.of("SecureRandom");
```



---

## HexFormat 十六进制格式化工具



JDK 17 新增`java.util.HexFormat`类，专门用于字节数组与十六进制字符串之间的相互转换。在此之前，开发者需要手动编写转换工具，或借助 BigDecimal、第三方库实现，代码繁琐且容易出错

HexFormat 支持自定义分隔符、大小写、前缀后缀等配置，功能完善且性能优异，是字节处理场景的官方标准方案



```Java
public class HexFormatDemo {
    public static void main(String[] args) {
        HexFormat hex = HexFormat.of();

        // 1. 字节数组转十六进制字符串
        byte[] bytes = {1, 2, 3, (byte) 0xff, (byte) 0xab};
        String hexStr = hex.formatHex(bytes);
        System.out.println(hexStr); // 输出：010203ffab

        // 2. 十六进制字符串转字节数组
        byte[] parsed = hex.parseHex("010203ffab");

        // 3. 带分隔符的格式化
        HexFormat delimiterHex = HexFormat.ofDelimiter(":").withUpperCase();
        String formatted = delimiterHex.formatHex(bytes);
        System.out.println(formatted); // 输出：01:02:03:FF:AB

        // 4. 单字节转换
        String singleByte = hex.toHexDigits((byte) 15);
        System.out.println(singleByte); // 输出：0f
    }
}
```



---

## JVM 与 GC 性能优化



**G1 收集器深度优化**

作为默认垃圾收集器，G1 的优化对绝大多数应用透明生效，升级 JDK 17 即可获得更低的 GC 停顿、更少的内存占用、更稳定的运行表现，无需任何参数调整



**ZGC 低延迟能力再升级**

1. 并发处理能力增强：进一步扩大并发执行的 GC 阶段，STW 停顿持续压缩，典型场景下停顿稳定在 1ms 以内
2. 类卸载优化：并发类卸载更高效，减少元空间碎片化，降低长时间运行的内存膨胀
3. 容器场景适配：优化容器环境下的资源感知与内存归还策略，云原生场景资源利用率更高
4. 多平台性能对齐：Linux、Windows、macOS 三大平台的 ZGC 性能趋于一致，开发生产环境表现更统一



**Shenandoah GC 持续优化**

1. 停顿时间进一步压缩：优化并发回收流程，减少必要的 STW 阶段，停顿表现与 ZGC 趋近
2. 内存碎片治理优化：更高效的并发整理策略，降低内存碎片率，减少 Full GC 触发概率
3. 中小堆场景优化：优化小堆内存下的回收效率，拓展适用场景范围
