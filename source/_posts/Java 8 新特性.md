---
title: Java 8 新特性
date: 2023-08-17 17:24:25
tags: JAVA
categories: 
  - JAVA
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%208%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/JAVA/Java%208%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg 
---



# Java 8 新特性

## Lambda 表达式

### 简介



Lambda 表达式是一个匿名函数，我们可以把 Lambda 表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。可以写出更简介、更灵活的代码，作为一种更紧凑的代码风格，使 Java 语言的表达能力得到了提升



比如在之前，如果我们像创建一个 TreeSet，并使用匿名内部类重写它的 compare 方法，代码如下：

```java
@Test
public void test() {
	Comparator<Integer> com = new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return Integer.compare(o1, o2);
        }
    }
    
    TreeSet set = new TreeSet<>(com);
}
```

如果使用 Lambda 表达式的话，这么写即可:

```java
@Test
public void test() {
	Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
    
    TreeSet set = new TreeSet<>(com);
}
```



------

### 基础语法



Java 8 中引入了一个新的操作符 "->"，该操作符称为箭头操作符或 Lambda 操作符，箭头操作符将 Lambda 表达式拆成了两个部分：

* 左侧：Lambda 表达式的参数列表
* 右侧：Lambda 表达式中所需执行的功能，即 Lambda 体



* 语法格式一：无参数，无返回值

  ```java
  @Test
  public void test() {
      // 在 JDK1.7 以前，匿名内部类中如果要使局部变量，则这个变量必须是 final
      // 在 JDK1.8 以后，变量无需添加 final，但本质上仍旧是个 final 类型，只不过编译器默认替我们加上了
      int num = 1;
      
      // 匿名内部类的实现
      Runnable r1 = new Runnable() {
          @Override
          public void run() {
              System.out.println("r1" + num);
          }
      }
      r1.run();
      
      // Lambda 的实现
      Runnable r2 = () -> System.out.println("r2" + num);
      r2.run();
  }
  ```

* 语法格式二：有参数，无返回值

  ```java
  @Test
  public void test() {
  	Consumer<String> con1 = (x) -> System.out.println(x);
  	con1.accept("Hello World");
      
      // 只有一个参数的时候，小括号可以省略不写
      Consumer<String> con2 = y -> System.out.println(y);
  	con2.accept("Hello World");
  }
  ```

* 语法格式三：有参数，有返回值，且 Lambda 体中有多条语句

  ```java
  @Test
  public void test() {
  	Comparator<Integer> com1 = (x, y) -> {
  		System.out.println(x + ":" + y);
  		return Integer.compare(x, y);
  	}
      
      // 如果 Lambda 体中只有一条语句，则大括号和 return 都可以省略
      Comparator<Integer> com2 = (x, y) -> Integer.compare(x, y);
  }
  ```



------

## 函数式接口

### 简介



函数式接口在 Java 中是指：有且仅有一个抽象方法的接口

函数式接口，即适用于函数式编程场景的接口。而 Java 中函数式编程体现的场景就是 Lambda，所以函数式接口就是可以适用于 Lambda 使用。只有确保接口中有且仅有一个抽象方法，Java 中的 Lambda 才能顺利进行推导



可以使用 @FunctionInterface 注解来表明当前接口是一个函数式接口，如果该接口不满足函数式接口的条件的话则会报错



------

### Lambda 的延迟执行



有些场景的代码执行后，结果不一定会被使用，从而造成性能的浪费。而 Lambda 表达式是延迟执行的，正好可以作为解决方案提升性能

比如在日志输出场景中，如果使用以下代码输出日志，那么不管日志级别如何，都会先拼接字符串，产生性能和内存的消耗

```
log.info("A:" + a);
```

但是如果使用以下方式，那么只有在日志级别满足的时候才会拼接字符串并产生日志，避免额外的消耗

```
log.info("A: {}", a);
```



两者的区别在于前者是将完整的日志内容作为参数输入的，那么不管方法是否执行，拼接字符串这一步都已经完成了，产生了消耗。而后者是将多个参数输入方法，只有在满足条件时才会拼接字符串，避免了额外的性能消耗



------

### 内置函数式接口



Java 内置了四个核心函数式接口，在需要通过函数式接口来进行编码的时候，如果所需的接口类型与内置的函数式接口一样的话，就不需要自己创建接口了，直接引用即可

|           函数式接口           | 参数类型 | 返回值类型 |                            用途                            |
| :----------------------------: | :------: | :--------: | :--------------------------------------------------------: |
| Consumer`<T>`<br />消费型接口  |    T     |    void    |     有参数无返回值方法<br />包含方法：void accept(T t)     |
| Supplier`<T>`<br />供给型接口  |    无    |     T      |         无参数有放回值方法<br />包含方法：T get()          |
| Function<T, R><br />函数型接口 |    T     |     R      |       有参数有返回值方法<br />包含方法：R apply(T t)       |
| Predicate`<T>`<br />断定型接口 |    T     |  Boolean   | 有参数，返回值类型为 boolean 的方法<br />Boolean test(T t) |



------

### 使用案例



* Consumer

  ```java
  @Test
  public void test() {
  	happyTime(40, money -> System.out.println("我有了" + money + "钱"));
  	happyTime(400, money -> System.out.println("我竟然有了" + money + "钱"));
  }
  
  private void happyTime(double money, Consumer<Double> consumer) {
  	consumer.accept(money);
  }
  ```

* Predicate

  ```java
  @Test
  public void test() {
  	List<String> list = Arrays.asList("A", "B", "C");
  	List<String> filterList = filterString(list, str -> str.contains("B"));
  	System.out.println(filterList);
  }
  
  /**
  * 根据给定的规则过滤集合中的字符串，过滤规则由 Predicate 方法决定
  **/
  private List<String> filterString(List<String> list, Predicate<String> predicate) {
  	ArrayList<String> filterList = new ArrayList();
  	for(String str : list) {
  		if (predicate.test(str)) {
  			filterList.add(str);
  		}
  	}
  }
  ```




------

### 其它的函数式接口



|                          函数式接口                          |         参数类型          |         返回类型          |                             用途                             |
| :----------------------------------------------------------: | :-----------------------: | :-----------------------: | :----------------------------------------------------------: |
|                     BiFunction<T, U, R>                      |           T, U            |             R             | 对类型为 T，U 参数应用操作，返回 R 类型的结果<br />方法：R apply(T t, U u) |
|           UnaryOperator`<T>`<br />Function 子接口            |             T             |             T             | 对类型为 T 的对象进行一元运算，并返回 T 类型的结果<br />方法：T apply(T t) |
|          BinaryOperator`<T>`<br />BiFunction 子接口          |           T, T            |             T             | 对类型为 T 的对象进行二元运算，并返回 T 类型的结果<br />方法：T apply(T t1, T t2) |
|                       BiConsumer<T, U>                       |           T, U            |           void            |   对类型为 T，U 参数应用操作<br />方法：void accept(T, U)    |
|                      BiPredicate<T, U>                       |           T, U            |          boolean          |   对类型为 T，U 参数应用操作<br />方法：boolean test(T, U)   |
| ToIntFunction`<T>`<br />ToLongFunction`<T>`<br />ToDoubleFunction<T> |             T             | int<br />long<br />double |             分别计算 int，long，double 值的函数              |
| IntFunction`<R>`<br />LongFunction`<R>`<br />DoubleFunction`<R>` | int<br />long<br />double |             R             |            参数分别为 int，long，double 值的函数             |



------

## 方法引用

### 简介



当要传递给 Lambda 体的操作，已经有实现方法了，可以使用方法引用

方法引用可以看作是 Lambda 表达式深层次的表达。换句话说，方法引用就是 Lambda 表达式，也就是函数式接口的一个实例，通过方法的名字来指向一个方法，可以认为是 Lambda 表达式的一个语法糖

要求：实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的方法的参数列表和返回值类型一致

格式：使用操作符 "::" 将类（或对象）与方法隔开来

如下三种主要使用情况：

* 对象::静态实例名
* 类::静态方法名
* 类::实例方法名



------

### 示例



* 对象::静态方法名

  ```java
  @Test
  public void test() {
  	Consumer<String> consumer1 = str -> System.out.println(str);
  	consumer1.accept("对象::静态方法名");
  	
  	// 可以这么写
  	PrintStream stream = System.out;
  	Consumer<String> consumer2 = stream::println;
  	consumer1.accept("对象::静态方法名");
  }
  ```

* 类::静态方法名

  ```java
  @Test
  public void test() {
  	Comparator<Integer> comparator1 = (t1, t2) -> Integer.compare(t1, t2);
  	System.out.println(comparator1.compare(12, 13));
  	
  	// 可以这么写
  	Comparator<Integer> comparator2 = Integer::compare;
  	System.out.println(comparator2.compare(12, 13));
  }
  ```

* 类::实例方法

  ```java
  @Test
  public void test() {
  	Comparator<String> comparator1 = (s1, s2) -> s1.compareTo(s2);
  	System.out.println(comparator1.compare("123", 321));
  	
  	// 可以这么写
  	Comparator<Integer> comparator2 = String::compareTo;
  	System.out.println(comparator2.compare("123", "321"));
  }
  ```



## 构造器引用

### 示例



```java
@Test
public void test() {
	Supplier<Employee> supplier1 = () -> new Employee();
	System.out.println(supplier1.get());
	
	// 可以这么写
	Supplier<Employee> supplier2 = Employee::new;
	System.out.println(supplier2.get());
}
```





------

## 数组引用

### 示例



```java
@Test
public void test() {
	Function<Integer, String[]> functn1 = length -> new String[length];
	String[] array1 = function1.apply(5);
	System.out.println(Arrays.toString(array1));
	
	// 可以这么写
	Function<Integer, String[]> functn2 = String[]::new;
	String[] array2 = function2.apply(5);
	System.out.println(Arrays.toString(array2));
}
```



------

## Stream API

### 简介



Java 8 中两大最为重要的改变。第一个是 Lambda 表达式，另一个则是 Stream API

Stream API 把真正的函数式编程风格引入到 Java 中，这是目前为止对 Java 类库最好的补充，因为 Stream API 可以极大提供 Java 程序员的生产力

Stream 是 Java 8 中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。使用 Stream API 对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。使用 Stream API 对集合数据进行操作，就类似于执行 SQL 的数据库查询，也可以使用 Stream API 并行执行操作。简言之，Stream API 提供了一种高效且易于使用的数据处理方式



Stream 和 Collection 集合的区别：Collection 是一种静态的内存数据，而 Stream 是有关计算的。前者是主要面向内存，存储在内存中，后者主要是面向 CPU，通过 CPU 实现计算



Stream 是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列

* Stream 自己不会存储元素
* Stream 不会改变源对象，相反它们会返回一个持有结果的新 Stream
* Stream 操作是延迟执行的，这意味着它们会等到需要结果时才执行



Stream 的操作分为三个步骤：实例化，中间操作和终止操作



------

### 实例化



* 通过集合

  ```java
  @Test
  public void test() {
  	// Java 8 中的 Collection 接口被扩展，提供了两个获取流的方式
  	
  	List<User> users = User.getUsers();
  	// 获取一个顺序流
  	Stream<User> stream = users.stream();
  	// 获取一个并行流
  	Stream<User> parallelStream = users.parallelStream();
  }
  ```

* 通过数组

  ```java
  @Test
  public void test() {
  	// 调用 Arrays 类的 static<T> Stream<T> stream(T[] array)
  	List<User> users = User.getUsers();
  	Stream<User> stream = arrays.stream(users);
  }
  ```

* Stream 的静态方法

  ```java
  @Test
  public void test() {
  	// 调用 Stream 的 of 方法
  	Stream<Integer> stream = Stream.of(1, 2, 3, 4);
  }
  ```

* 无限流

  ```java
  @Test
  public void test() {
  	// 遍历前 10 个偶数
  	Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out::println);
  	// 随机生成 10 个偶数
  	Stream.generate(Math.random).limit(10).forEach(System.out::println);
  }
  ```




------

### 中间操作



多个中间操作可以连接起来形成一个流水线，除非流水线上除非终止操作，否则中间操作不会执行任何的处理，而在终止操作时一次性全部处理，称为“惰性求值”



* 筛选与切片

  ```java
  /**
  * filter(Predicate p)：接收 Lambda，从流中排除某些元素
  **/
  public static void main(String[] args) {
      List<Employee> employees = getEmployeeDataList();
      Stream<Employee> stream = employees.stream();
      stream.filter(e -> e.getSalary() > 8000).forEach(t->{
          System.out.println("工资大于八千的员工->>>"+t);
      });
  }
  ```

  ```java
  /**
  * limit(long maxSize)：截断流，使元素不超过给定的数量
  **/
  public static void main(String[] args) {
      List<Employee> employees = getEmployeeDataList();
      employees.stream().limit(3).forEach(t-> System.out.println("输出集合元素数量->>>"+t));
  }
  ```

  ```java
  /**
  * skip(long n)：跳过元素，返回一个扔了前面 n 个元素的流，若流中元素不足 n 个，则返回一个空流，与 limit 互补
  **/
  public static void main(String[] args) {
      List<Employee> employees = getEmployeeDataList();
      employees.stream().skip(2).forEach(t-> System.out.println("过滤掉前面的2个元素->>>"+t));
  }
  ```

  ```java
  /**
  * distinct()：筛选，通过 hashCode() 和 equals() 去重
  **/
  public static void main(String[] args) {
      List<Employee> list = new ArrayList<>();
      list.add(new Employee(1,"张三",20,8500D,1));
      list.add(new Employee(1,"张三",20,8500D,1));
      list.add(new Employee(1,"张三",20,8500D,1));
      list.add(new Employee(1,"张三",20,8500D,1));
      list.add(new Employee(1,"张三",20,8500D,1));
      list.stream().distinct().forEach(t-> System.out.println("集合去重->>>"+t));
  }
  ```

* 映射

  ```java
  /**
  * map(Function f)：接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新元素
  **/
  public static void main(String[] args) {
      List<String> list = Arrays.asList("a", "b", "c", "d");
      list.stream().map(str -> str.toUpperCase(Locale.ROOT)).forEach(t-> System.out.println("大小写转换->>>"+t));
  }
  ```

* 排序

  ```java
  /**
  * sorted()：产生一个新流，按自然顺序排序
  * sorted(Comparator c)：产生一个新流，按给定的规则排序
  **/
  public static void main(String[] args) {
     List<Employee> list = getEmployeeDataList();
     list.stream().sorted((e1,e2)->{
         int age = Integer.compare(e1.getAge(),e2.getAge());
         if(age != 0){
             return age;
         }else {
             return Double.compare(e1.getSalary(),e2.getSalary());
         }
     }).forEach(System.out::println);
  }
  ```



------

### 终止操作



终止操作会从流的流水线生成结果，其结果可以是不是流的任何值，比如 List、Integer 甚至是 void

流进行了终止操作后不能再继续使用



* 匹配与查找

  ```java
  /**
  * allMatch：检查是否匹配所有元素
  * 全部满足返回 true 、否则返回false
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      boolean allMatch = list.stream().allMatch(e -> e.getAge() > 18);
      System.out.println(allMatch);
  }
  ```

  ```java
  /**
  * anyMatch：检查是否至少匹配一个元素
  * 存在一个元素条件满足即可返回true
  **/
  public static void main(String[] args) {
     List<Employee> list = getEmployeeDataList();
      boolean anyMatch = list.stream().anyMatch(employee -> employee.getSalary() > 8000);
      System.out.println(anyMatch);
  }
  ```

  ```java
  /**
  * noneMatch：检查是否没有匹配的元素
  * 返回false，说明有，否则没有
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      boolean noneMatch = list.stream().noneMatch(employee -> employee.getName().startsWith("张"));
      System.out.println(noneMatch);
  }
  ```

  ```java
  /**
  * findFirst：返回第一个元素
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      Optional<Employee> first = list.stream().findFirst();
      System.out.println(first);
  }
  ```
  
  ```java
  /**
  * findAny：返回当前流中的任意元素
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      Optional<Employee> first = list.parallelStream().findAny();
      System.out.println(first);
  }
  ```
  
  ```java
  /**
  * count：返回流中元素的总个数
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      long count = list.stream().filter(employee -> employee.getSalary() > 8000).count();
      System.out.println(count);
  }
  ```
  
  ```java
  /**
  * max：返回流中的最大值
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      Stream<Double> doubleStream = list.stream().map(employee -> employee.getSalary());
      Optional<Double> max = doubleStream.max(Double::compare);
      System.out.println(max);
  }
  ```
  
  ```java
  /**
  * min：返回流中的最小值
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      Optional<Employee> min = list.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
      System.out.println(min);
  }
  ```
  
* 归约

  ```java
  /**
  * reduce：可以将流中的元素反复结合起来，得到一个值
  **/
  public static void main(String[] args) {
      List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
      // reduce的第一个参数0：代表初始值。
      Integer reduce = list.stream().reduce(0, Integer::sum);
      System.out.println(reduce);
  }
  ```

* 收集

  ```java
  /**
  * collection：返回一个集合
  **/
  public static void main(String[] args) {
      List<Employee> list = getEmployeeDataList();
      List<Employee> collect = list.stream().filter(employee -> employee.getSalary() > 8000).collect(Collectors.toList());
      System.out.println(collect);
  }
  ```



------

## Optional 类

### 简介



到目前为止，臭名昭著的空指针异常是导致 Java 应用程序失败最常见的原因。以前，为了解决空指针的异常，Google 著名的 Guava 项目引入了 Optional 类，Guava 通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。收到 Google Guava 的启发，Optional 类已经成为了 Java 8 类库的一部分

`Optional<T>` 类是一个容器类，可以保存类型 T 的值，代表这个值存在，或者仅仅保存 null，代表这个值不存在。相比于 null 表示一个值不存在，Optional 可以更好的表达这个概念，并且可以避免空指针异常

Optional 类 Javadoc 描述如下：这是一个可以为 null 的容器对象，如果值存在则 isPresent() 方法会返回 true，调用 get() 方法可以获得该对象



------

### 常用方法



创建 Optional 类对象的方法：

* Optional.of(T t) ：创建一个 Optional 实例，t 必须非空
* Optional.empty()：创建一个空的 Optional 实例
* Optional.ofNullable(T t)：t 可以为 null

判断 Optional 容器中是否包含对象：

* boolean isPresent()：判断是否包含对象
* void isPresent(Consumer consumer)：如果有值就执行 Consumer 代码的方法，并且该值会作为参数传给它

获取 Optional 容器的对象：

* T get()：如果调用对象包含值则返回该值，否则抛出异常
* T orElese(T other)：如果有值则将其返回，否则返回 other 对象
* T orElseGet(Supplier supplier)：如果有值则将其返回，否则返回由 Supplier 接口实现提供的对象
* T orElseThrow(Supplier supplier)：如果有值则将其返回，否则抛出由 Supplier 接口实现抛出的异常
