---
title: JDK 8 新特性
date: 2022-11-02 01:07:35
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%208%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Java/%E8%AF%AD%E8%A8%80%E6%A0%B8%E5%BF%83/JDK%208%20%E6%96%B0%E7%89%B9%E6%80%A7/preview.jpg
tags:
  - Java
  - JDK
categories: Java
---





# Lambda 表达式

## 什么是 Lambda 表达式



**Lambda 表达式**本质上是一个**匿名函数**，它没有方法名，但拥有参数列表、方法体和返回值。Lambda 表达式允许将代码作为数据进行传递，从而实现更灵活的编程逻辑

以前的 Java 只能把对象作为参数传递，JDK 8 之后，可以把“行为”作为参数传递

最简单的形式：

```Java
参数 -> 方法体
```

可以将 Lambda 表达式理解为：**一段可以作为参数传递的可执行代码**



---

## 为什么需要 Lambda 表达式



传统 Java 开发中，当需要传递一段逻辑时，通常需要创建一个类或匿名内部类来实现，这会导致代码冗余且可读性差。以下通过员工筛选的示例展示传统写法的痛点



**需求**：从员工列表中筛选出符合特定条件的员工

**传统写法**：

```Java
class Employee {
    private String name;
    private int age;
    private double salary;

    public Employee(String name, int age, double salary) {
        this.name = name;
        this.age = age;
        this.salary = salary;
    }

    // getter方法
    public String getName() { return name; }
    public int getAge() { return age; }
    public double getSalary() { return salary; }
}

public class TraditionalFilterDemo {
    // 筛选年龄大于30岁的员工
    public static List<Employee> filterByAge(List<Employee> employees) {
        List<Employee> result = new ArrayList<>();
        for (Employee emp : employees) {
            if (emp.getAge() > 30) {
                result.add(emp);
            }
        }
        return result;
    }

    // 筛选工资大于8000的员工
    public static List<Employee> filterBySalary(List<Employee> employees) {
        List<Employee> result = new ArrayList<>();
        for (Employee emp : employees) {
            if (emp.getSalary() > 8000) {
                result.add(emp);
            }
        }
        return result;
    }

    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("张三", 25, 5000));
        employees.add(new Employee("李四", 35, 8000));
        employees.add(new Employee("王五", 40, 10000));

        List<Employee> ageResult = filterByAge(employees);
        List<Employee> salaryResult = filterBySalary(employees);
    }
}
```

上述代码中，`filterByAge`和`filterBySalary`方法除了判断条件不同外，其余逻辑完全一致。当需求继续变化时，需要不断新增筛选方法，导致代码严重冗余



**使用 Lambda 表达式的写法**：

```Java
public class LambdaFilterDemo {
    // 通用筛选方法，接收一个判断条件作为参数
    public static List<Employee> filterEmployees(List<Employee> employees, Predicate<Employee> predicate) {
        List<Employee> result = new ArrayList<>();
        for (Employee emp : employees) {
            if (predicate.test(emp)) {
                result.add(emp);
            }
        }
        return result;
    }

    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("张三", 25, 5000));
        employees.add(new Employee("李四", 35, 8000));
        employees.add(new Employee("王五", 40, 10000));

        // 筛选年龄大于30岁的员工
        List<Employee> ageResult = filterEmployees(employees, emp -> emp.getAge() > 30);
        
        // 筛选工资大于8000的员工
        List<Employee> salaryResult = filterEmployees(employees, emp -> emp.getSalary() > 8000);
        
        // 筛选年龄大于30且工资大于8000的员工
        List<Employee> combinedResult = filterEmployees(employees, emp -> emp.getAge() > 30 && emp.getSalary() > 8000);
    }
}
```

通过 Lambda 表达式，将变化的判断条件作为参数传递给通用筛选方法，无需新增任何方法即可满足不同的筛选需求，代码简洁且易于扩展



---

## 基本语法与用法



Lambda 表达式的标准语法格式：

```
(参数列表) -> { 方法体 }
```

语法说明：

- `(参数列表)`：与普通方法的参数列表一致，参数类型可省略
- `->`：Lambda 操作符，用于分隔参数列表与方法体
- `{ 方法体 }`：方法的具体实现逻辑，可以是一条或多条语句



Lambda 表达式支持多种简化写法，开发者可根据实际情况选择最简洁的形式：

1. **参数类型可省略**：编译器可根据上下文自动推断参数类型

   ```Java
   // 完整写法
   (Employee emp) -> emp.getAge() > 30
   
   // 简化写法
   (emp) -> emp.getAge() > 30
   ```

2. **单个参数可省略括号**：当参数列表只有一个参数时，括号可省略

   ```Java
   // 完整写法
   (emp) -> emp.getAge() > 30
   
   // 简化写法
   emp -> emp.getAge() > 30
   ```

3. **单条语句可省略大括号与 return**：当方法体只有一条语句时，大括号和 return 关键字可同时省略

   ```Java
   // 完整写法
   emp -> { return emp.getAge() > 30; }
   
   // 简化写法
   emp -> emp.getAge() > 30
   ```

4. **无参数必须保留括号**：当方法没有参数时，括号不能省略

   ```Java
   // 正确写法
   () -> System.out.println("Hello Lambda")
   
   // 错误写法
   -> System.out.println("Hello Lambda")
   ```

   

**常见 Lambda 表达式示例**

```Java
// 无参数无返回值
Runnable runnable = () -> System.out.println("线程执行");

// 单个参数无返回值
Consumer<String> consumer = s -> System.out.println(s);

// 两个参数有返回值
BinaryOperator<Integer> add = (a, b) -> a + b;

// 多条语句有返回值
BinaryOperator<Integer> calculate = (a, b) -> {
    int sum = a + b;
    int product = a * b;
    return sum + product;
};
```



---

## 实际应用场景



Lambda 表达式在实际项目中主要应用于以下场景：



**场景 1：集合遍历**

```Java
public class CollectionIterationDemo {
    public static void main(String[] args) {
        List<String> languages = Arrays.asList("Java", "Python", "C++", "Go");

        // 传统增强for循环
        for (String lang : languages) {
            System.out.println(lang);
        }

        // Lambda表达式写法
        languages.forEach(lang -> System.out.println(lang));
    }
}
```



**场景 2：集合排序**

```Java
public class CollectionSortDemo {
    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("张三", 25, 5000));
        employees.add(new Employee("李四", 35, 8000));
        employees.add(new Employee("王五", 40, 10000));

        // 传统匿名内部类写法
        Collections.sort(employees, new Comparator<Employee>() {
            @Override
            public int compare(Employee o1, Employee o2) {
                return o1.getAge() - o2.getAge();
            }
        });

        // Lambda表达式写法
        Collections.sort(employees, (o1, o2) -> o1.getAge() - o2.getAge());

        // 更简洁的List.sort写法
        employees.sort((o1, o2) -> o1.getAge() - o2.getAge());
    }
}
```



**场景 3：线程创建**

```Java
public class ThreadCreationDemo {
    public static void main(String[] args) {
        // 传统匿名内部类写法
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("线程启动");
            }
        }).start();

        // Lambda表达式写法
        new Thread(() -> System.out.println("线程启动")).start();
    }
}
```



**场景 4：GUI 事件处理**

```Java
public class EventHandlingDemo {
    public static void main(String[] args) {
        JButton button = new JButton("点击");

        // 传统匿名内部类写法
        button.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println("按钮被点击");
            }
        });

        // Lambda表达式写法
        button.addActionListener(e -> System.out.println("按钮被点击"));
    }
}
```



---

## 变量捕获



Lambda 表达式可以访问外部作用域中的变量，但需满足以下条件：

1. 变量必须是`final`类型
2. 变量必须是**事实上的 final**（即变量在初始化后未被重新赋值）



```Java
public class VariableCaptureDemo {
    public static void main(String[] args) {
        // 事实上的final变量
        int factor = 2;

        // Lambda表达式访问外部变量
        Runnable runnable = () -> {
            // 可以访问factor
            System.out.println(factor * 10);
            
            // 错误：不能修改外部变量
            // factor = 3;
        };

        runnable.run();

        // 错误：如果此处修改factor，它将不再是事实上的final
        // factor = 3;
    }
}
```

**设计原因**：Lambda 表达式可能在不同线程中执行，若允许修改外部变量，会引发线程安全问题



---

## 和匿名内部类的区别



| 特性      | Lambda 表达式          | 匿名内部类                            |
| --------- | ---------------------- | ------------------------------------- |
| this 指向 | 指向外部类对象         | 指向匿名内部类对象本身                |
| 编译方式  | 编译为外部类的私有方法 | 编译为独立的类文件（外部类 $1.class） |
| 适用范围  | 只能实现函数式接口     | 可以实现任何接口或继承抽象类          |
| 内存占用  | 较低                   | 较高                                  |



---

# 函数式接口

## 什么是函数式接口



Lambda 表达式不能独立存在，它必须依附于**函数式接口**。函数式接口定义了 Lambda 表达式的类型约束，是 Lambda 表达式的本质

**函数式接口**是指**仅包含一个抽象方法**的接口。Lambda 表达式的本质就是函数式接口的实例，只要一个对象是函数式接口的实例，就可以用 Lambda 表达式来表示

JDK 8 提供了`@FunctionalInterface`注解，用于标记函数式接口。该注解不是必需的，但它可以让编译器检查接口是否符合函数式接口的规范，若接口包含多个抽象方法，编译器将报错

```Java
@FunctionalInterface
public interface Runnable {
    void run();
}
```



---

## 为什么需要函数式接口



函数式接口为 Lambda 表达式提供了**类型系统**。没有函数式接口，编译器无法推断 Lambda 表达式的参数类型、返回值类型和异常类型，Lambda 表达式将无法使用

可以将函数式接口理解为 Lambda 表达式的 "契约"，它规定了 Lambda 表达式必须遵循的方法签名



---

## 自定义函数式接口



开发者可以根据业务需求自定义函数式接口：

```Java
@FunctionalInterface
public interface Calculator<T, R> {
    R calculate(T t1, T t2);
}
```



使用自定义函数式接口：

```Java
public class CustomFunctionalInterfaceDemo {
    public static void main(String[] args) {
        // 实现加法运算
        Calculator<Integer, Integer> add = (a, b) -> a + b;
        System.out.println(add.calculate(10, 5)); // 输出15

        // 实现乘法运算
        Calculator<Integer, Integer> multiply = (a, b) -> a * b;
        System.out.println(multiply.calculate(10, 5)); // 输出50
    }
}
```



---

## JDK 内置函数式接口



为了避免开发者重复定义函数式接口，JDK 8 在`java.util.function`包中提供了大量内置函数式接口。其中最核心的四个接口如下：

| 函数式接口       | 抽象方法            | 功能描述                                 |
| ---------------- | ------------------- | ---------------------------------------- |
| `Consumer<T>`    | `void accept(T t)`  | 消费一个 T 类型的参数，无返回值          |
| `Supplier<T>`    | `T get()`           | 提供一个 T 类型的结果，无参数            |
| `Function<T, R>` | `R apply(T t)`      | 接收一个 T 类型参数，返回一个 R 类型结果 |
| `Predicate<T>`   | `boolean test(T t)` | 接收一个 T 类型参数，返回 boolean 值     |

这四个接口覆盖了绝大多数业务场景，开发者应优先使用内置接口，而非自定义接口



---

## 实际使用场景



**场景 1：使用 Consumer 消费数据**

```Java
public class ConsumerDemo {
    public static <T> void processList(List<T> list, Consumer<T> consumer) {
        for (T t : list) {
            consumer.accept(t);
        }
    }

    public static void main(String[] args) {
        List<String> names = Arrays.asList("张三", "李四", "王五");
        
        // 打印每个名字
        processList(names, name -> System.out.println(name));
        
        // 打印每个名字的长度
        processList(names, name -> System.out.println(name.length()));
    }
}
```



**场景 2：使用 Supplier 生成数据**

```Java
public class SupplierDemo {
    public static <T> List<T> generateList(int count, Supplier<T> supplier) {
        List<T> list = new ArrayList<>();
        for (int i = 0; i < count; i++) {
            list.add(supplier.get());
        }
        return list;
    }

    public static void main(String[] args) {
        // 生成5个0-100之间的随机整数
        List<Integer> randomNumbers = generateList(5, () -> (int) (Math.random() * 100));
        System.out.println(randomNumbers);
    }
}
```



**场景 3：使用 Function 转换数据**

```Java
public class FunctionDemo {
    public static <T, R> List<R> transformList(List<T> list, Function<T, R> function) {
        List<R> result = new ArrayList<>();
        for (T t : list) {
            result.add(function.apply(t));
        }
        return result;
    }

    public static void main(String[] args) {
        List<String> stringNumbers = Arrays.asList("1", "2", "3", "4", "5");
        
        // 将字符串转换为整数
        List<Integer> integers = transformList(stringNumbers, s -> Integer.parseInt(s));
        System.out.println(integers); // 输出[1, 2, 3, 4, 5]
    }
}
```



**场景 4：使用 Predicate 过滤数据**

```Java
public class PredicateDemo {
    public static <T> List<T> filterList(List<T> list, Predicate<T> predicate) {
        List<T> result = new ArrayList<>();
        for (T t : list) {
            if (predicate.test(t)) {
                result.add(t);
            }
        }
        return result;
    }

    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // 筛选偶数
        List<Integer> evenNumbers = filterList(numbers, n -> n % 2 == 0);
        System.out.println(evenNumbers); // 输出[2, 4, 6, 8, 10]
    }
}
```



---

## 高级用法与技巧



JDK 内置的函数式接口提供了丰富的默认方法，用于组合多个函数式接口，实现复杂的逻辑



**Predicate 接口的默认方法**：

- `and(Predicate other)`：逻辑与运算
- `or(Predicate other)`：逻辑或运算
- `negate()`：逻辑非运算

```Java
public class PredicateDefaultMethodDemo {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        Predicate<Integer> isEven = n -> n % 2 == 0;
        Predicate<Integer> greaterThan5 = n -> n > 5;

        // 筛选大于5的偶数
        List<Integer> result1 = filterList(numbers, isEven.and(greaterThan5));
        System.out.println(result1); // 输出[6, 8, 10]

        // 筛选偶数或大于5的数
        List<Integer> result2 = filterList(numbers, isEven.or(greaterThan5));
        System.out.println(result2); // 输出[2, 4, 6, 7, 8, 9, 10]

        // 筛选奇数
        List<Integer> result3 = filterList(numbers, isEven.negate());
        System.out.println(result3); // 输出[1, 3, 5, 7, 9]
    }
}
```



**Function 接口的默认方法**：

- `andThen(Function after)`：先执行当前 Function，再执行 after Function
- `compose(Function before)`：先执行 before Function，再执行当前 Function

```Java
public class FunctionDefaultMethodDemo {
    public static void main(String[] args) {
        Function<Integer, Integer> add2 = n -> n + 2;
        Function<Integer, Integer> multiply3 = n -> n * 3;

        // 先加2，再乘3：(5+2)*3=21
        Function<Integer, Integer> f1 = add2.andThen(multiply3);
        System.out.println(f1.apply(5)); // 输出21

        // 先乘3，再加2：(5*3)+2=17
        Function<Integer, Integer> f2 = add2.compose(multiply3);
        System.out.println(f2.apply(5)); // 输出17
    }
}
```



---

# 方法引用和构造器引用

## 什么是方法引用



**方法引用**是 Lambda 表达式的简化形式，它直接引用已有的方法作为 Lambda 表达式的实现。方法引用的本质与 Lambda 表达式完全相同，都是函数式接口的实例

方法引用和构造器引用是 Lambda 表达式的语法糖，当 Lambda 表达式的方法体仅调用一个已存在的方法时，可以使用方法引用或构造器引用进一步简化代码

当 Lambda 表达式的方法体仅调用一个已存在的方法时，使用方法引用可以让代码更加简洁、可读性更高

```Java
// Lambda表达式写法
list.forEach(s -> System.out.println(s));

// 方法引用写法
list.forEach(System.out::println);
```



---

## 方法引用



方法引用使用双冒号`::`作为分隔符，语法格式为：

```
引用者::方法名
```



根据引用者的不同，方法引用分为三种类型：



**类型 1：对象 :: 实例方法**

引用一个已存在对象的实例方法：

```Java
public class ObjectMethodReferenceDemo {
    public static void main(String[] args) {
        // Lambda表达式写法
        Consumer<String> consumer1 = s -> System.out.println(s);
        consumer1.accept("Hello Lambda");

        // 方法引用写法
        Consumer<String> consumer2 = System.out::println;
        consumer2.accept("Hello Method Reference");
    }
}
```



**类型 2：类 :: 静态方法**

引用一个类的静态方法：

```Java
public class StaticMethodReferenceDemo {
    public static void main(String[] args) {
        // Lambda表达式写法
        Function<String, Integer> function1 = s -> Integer.parseInt(s);
        Integer num1 = function1.apply("123");
        System.out.println(num1); // 输出123

        // 方法引用写法
        Function<String, Integer> function2 = Integer::parseInt;
        Integer num2 = function2.apply("456");
        System.out.println(num2); // 输出456
    }
}
```



**类型 3：类 :: 实例方法**

引用一个类的实例方法，此时 Lambda 表达式的第一个参数将成为方法的调用者，其余参数作为方法的参数：

```Java
public class ClassInstanceMethodReferenceDemo {
    public static void main(String[] args) {
        // Lambda表达式写法
        BiFunction<String, String, Boolean> function1 = (s1, s2) -> s1.equals(s2);
        Boolean result1 = function1.apply("Hello", "Hello");
        System.out.println(result1); // 输出true

        // 方法引用写法
        BiFunction<String, String, Boolean> function2 = String::equals;
        Boolean result2 = function2.apply("Hello", "World");
        System.out.println(result2); // 输出false
    }
}
```



---

## 构造器引用



构造器引用是方法引用的特殊形式，用于引用类的构造器。语法格式为：

```
类名::new
```



编译器会根据函数式接口的方法签名，自动匹配对应的构造器：

```Java
public class ConstructorReferenceDemo {
    public static void main(String[] args) {
        // 引用无参构造器
        Supplier<Employee> supplier1 = () -> new Employee();
        Supplier<Employee> supplier2 = Employee::new;
        Employee emp1 = supplier2.get();

        // 引用单参数构造器
        Function<String, Employee> function1 = name -> new Employee(name);
        Function<String, Employee> function2 = Employee::new;
        Employee emp2 = function2.apply("张三");
    }
}

class Employee {
    private String name;
    private int age;

    public Employee() {}

    public Employee(String name) {
        this.name = name;
    }

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```



---

## 数组引用



数组引用是构造器引用的特殊形式，用于创建数组。语法格式为：

```
数组类型[]::new
```



```Java
public class ArrayReferenceDemo {
    public static void main(String[] args) {
        // Lambda表达式写法
        Function<Integer, int[]> function1 = length -> new int[length];
        int[] array1 = function1.apply(5);
        System.out.println(array1.length); // 输出5

        // 数组引用写法
        Function<Integer, int[]> function2 = int[]::new;
        int[] array2 = function2.apply(10);
        System.out.println(array2.length); // 输出10
    }
}
```



---

## 实际引用场景



**场景 1：集合遍历**

```Java
public class IterationReferenceDemo {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("Java", "Python", "C++");
        
        // 方法引用写法
        list.forEach(System.out::println);
    }
}
```



**场景 2：集合元素转换**

```Java
public class TransformReferenceDemo {
    public static void main(String[] args) {
        List<String> stringNumbers = Arrays.asList("1", "2", "3", "4", "5");
        
        // 方法引用写法
        List<Integer> integers = stringNumbers.stream()
                                              .map(Integer::parseInt)
                                              .collect(Collectors.toList());
        System.out.println(integers);
    }
}
```



**场景 3：对象创建**

```Java
public class ObjectCreationReferenceDemo {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("张三", "李四", "王五");
        
        // 构造器引用写法
        List<Employee> employees = names.stream()
                                        .map(Employee::new)
                                        .collect(Collectors.toList());
    }
}
```



**场景 4：集合排序**

```Java
public class SortReferenceDemo {
    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("张三", 25));
        employees.add(new Employee("李四", 35));
        employees.add(new Employee("王五", 40));

        // 方法引用写法
        employees.sort(Comparator.comparing(Employee::getAge));
    }
}
```



---

# Stream API

## 什么是 Stream API



**Stream（流）** 是一个来自数据源的元素队列，它支持对元素进行一系列的聚合操作。Stream 不是数据结构，它不存储数据，而是通过管道的方式对数据进行处理



Stream 具有以下核心特点：

1. **不存储数据**：Stream 只是数据的视图，数据仍然存储在原集合或数组中
2. **不改变原数据**：所有 Stream 操作都会返回一个新的 Stream，不会修改原数据源
3. **延迟执行**：中间操作不会立即执行，只有当调用终止操作时，才会触发实际的计算
4. **支持并行处理**：无需编写多线程代码，只需调用`parallelStream()`即可实现并行计算



Stream API 是 JDK 8 最实用、最强大的特性之一，它提供了一种声明式的方式来处理集合数据，支持过滤、映射、排序、聚合等多种操作，并且可以轻松实现并行处理



---

## 为什么需要 Stream API



在 JDK 8 之前，处理集合数据需要编写大量的循环代码，代码冗长且容易出错。以下通过一个复杂的员工处理示例展示传统写法的痛点

**需求**：从员工列表中筛选出年龄大于 30 岁的员工，按工资从高到低排序，提取前 3 名员工的姓名

**传统写法**：

```Java
public class TraditionalCollectionDemo {
    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("张三", 25, 5000));
        employees.add(new Employee("李四", 35, 8000));
        employees.add(new Employee("王五", 40, 10000));
        employees.add(new Employee("赵六", 32, 7500));
        employees.add(new Employee("钱七", 28, 6000));

        // 1. 筛选年龄大于30岁的员工
        List<Employee> filtered = new ArrayList<>();
        for (Employee emp : employees) {
            if (emp.getAge() > 30) {
                filtered.add(emp);
            }
        }

        // 2. 按工资从高到低排序
        Collections.sort(filtered, new Comparator<Employee>() {
            @Override
            public int compare(Employee o1, Employee o2) {
                return Double.compare(o2.getSalary(), o1.getSalary());
            }
        });

        // 3. 提取前3名员工的姓名
        List<String> names = new ArrayList<>();
        for (int i = 0; i < Math.min(3, filtered.size()); i++) {
            names.add(filtered.get(i).getName());
        }

        System.out.println(names); // 输出[王五, 李四, 赵六]
    }
}
```

上述代码包含三个独立的循环，逻辑分散且可读性差。每增加一个操作，就需要新增一个循环，代码复杂度会急剧上升



**Stream API 解决方案：**

```Java
public class StreamDemo {
    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("张三", 25, 5000));
        employees.add(new Employee("李四", 35, 8000));
        employees.add(new Employee("王五", 40, 10000));
        employees.add(new Employee("赵六", 32, 7500));
        employees.add(new Employee("钱七", 28, 6000));

        List<String> names = employees.stream()
                                     .filter(emp -> emp.getAge() > 30)
                                     .sorted((o1, o2) -> Double.compare(o2.getSalary(), o1.getSalary()))
                                     .limit(3)
                                     .map(Employee::getName)
                                     .collect(Collectors.toList());

        System.out.println(names); // 输出[王五, 李四, 赵六]
    }
}
```

使用 Stream API，所有操作通过链式调用完成，代码逻辑清晰、简洁，可读性大幅提升。开发者只需关注 "做什么"，而非 "怎么做"



---

## 基本语法和用法



Stream 操作分为三个阶段：

1. **创建 Stream**：从数据源（集合、数组等）获取一个流
2. **中间操作**：对 Stream 进行处理，返回一个新的 Stream（可以有多个中间操作）
3. **终止操作**：执行所有中间操作，产生一个最终结果（只能有一个终止操作）



**创建 Stream**

常见的 Stream 创建方式：

```Java
public class StreamCreationDemo {
    public static void main(String[] args) {
        // 方式1：从集合创建
        List<String> list = Arrays.asList("Java", "Python", "C++");
        Stream<String> stream1 = list.stream();

        // 方式2：从数组创建
        String[] array = {"Java", "Python", "C++"};
        Stream<String> stream2 = Arrays.stream(array);

        // 方式3：创建空流
        Stream<String> stream3 = Stream.empty();

        // 方式4：创建无限流
        Stream<Integer> evenStream = Stream.iterate(0, n -> n + 2); // 偶数流
        Stream<Double> randomStream = Stream.generate(Math::random); // 随机数流
    }
}
```



**中间操作**

中间操作返回一个新的 Stream，支持链式调用。常用的中间操作如下：

| 操作方法               | 功能描述                                     |
| ---------------------- | -------------------------------------------- |
| `filter(Predicate p)`  | 过滤流中不符合条件的元素                     |
| `map(Function f)`      | 将流中的每个元素转换为另一种类型             |
| `flatMap(Function f)`  | 将流中的每个元素转换为一个流，然后合并所有流 |
| `distinct()`           | 去除流中的重复元素（基于 equals 方法）       |
| `sorted()`             | 对流中的元素进行自然排序                     |
| `sorted(Comparator c)` | 对流中的元素进行自定义排序                   |
| `limit(long maxSize)`  | 截取流中的前 maxSize 个元素                  |
| `skip(long n)`         | 跳过流中的前 n 个元素                        |
| `peek(Consumer c)`     | 对流中的每个元素执行操作，主要用于调试       |



**终止操作**

终止操作会触发所有中间操作的执行，并产生一个最终结果。常用的终止操作如下：

| 操作方法                 | 功能描述                                    |
| ------------------------ | ------------------------------------------- |
| `forEach(Consumer c)`    | 遍历流中的每个元素                          |
| `collect(Collector c)`   | 将流转换为其他数据结构（List、Set、Map 等） |
| `count()`                | 返回流中元素的个数                          |
| `max(Comparator c)`      | 返回流中的最大值                            |
| `min(Comparator c)`      | 返回流中的最小值                            |
| `anyMatch(Predicate p)`  | 检查是否至少有一个元素符合条件              |
| `allMatch(Predicate p)`  | 检查是否所有元素都符合条件                  |
| `noneMatch(Predicate p)` | 检查是否没有元素符合条件                    |
| `findFirst()`            | 返回流中的第一个元素                        |
| `findAny()`              | 返回流中的任意一个元素                      |



---

## 实际应用场景



**场景 1：数据过滤与转换**

```Java
public class FilterMapDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("张三", 25, 5000, "技术部"),
            new Employee("李四", 35, 8000, "市场部"),
            new Employee("王五", 40, 10000, "技术部"),
            new Employee("赵六", 32, 7500, "技术部")
        );

        // 筛选技术部员工，提取姓名并转换为大写
        List<String> techNames = employees.stream()
                                          .filter(emp -> "技术部".equals(emp.getDepartment()))
                                          .map(Employee::getName)
                                          .map(String::toUpperCase)
                                          .collect(Collectors.toList());

        System.out.println(techNames); // 输出[张三, 王五, 赵六]
    }
}
```



**场景 2：数据排序与分页**

```Java
public class SortLimitDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("张三", 25, 5000, "技术部"),
            new Employee("李四", 35, 8000, "市场部"),
            new Employee("王五", 40, 10000, "技术部"),
            new Employee("赵六", 32, 7500, "技术部")
        );

        // 按工资降序排序，取第2-3条数据（分页）
        List<Employee> pagedEmployees = employees.stream()
                                                 .sorted(Comparator.comparing(Employee::getSalary).reversed())
                                                 .skip(1)
                                                 .limit(2)
                                                 .collect(Collectors.toList());

        pagedEmployees.forEach(emp -> System.out.println(emp.getName() + ": " + emp.getSalary()));
        // 输出：
        // 李四: 8000.0
        // 赵六: 7500.0
    }
}
```



**场景 3：数据聚合统计**

```Java
public class AggregationDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("张三", 25, 5000, "技术部"),
            new Employee("李四", 35, 8000, "市场部"),
            new Employee("王五", 40, 10000, "技术部"),
            new Employee("赵六", 32, 7500, "技术部")
        );

        // 计算技术部员工的平均工资
        double avgSalary = employees.stream()
                                    .filter(emp -> "技术部".equals(emp.getDepartment()))
                                    .mapToDouble(Employee::getSalary)
                                    .average()
                                    .orElse(0.0);

        System.out.println("技术部平均工资：" + avgSalary); // 输出7500.0

        // 统计所有员工的工资信息
        DoubleSummaryStatistics stats = employees.stream()
                                                 .collect(Collectors.summarizingDouble(Employee::getSalary));

        System.out.println("最高工资：" + stats.getMax()); // 输出10000.0
        System.out.println("最低工资：" + stats.getMin()); // 输出5000.0
        System.out.println("工资总和：" + stats.getSum()); // 输出30500.0
        System.out.println("员工人数：" + stats.getCount()); // 输出4
    }
}
```



**场景 4：数据分组与分区**

```Java
public class GroupingDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("张三", 25, 5000, "技术部"),
            new Employee("李四", 35, 8000, "市场部"),
            new Employee("王五", 40, 10000, "技术部"),
            new Employee("赵六", 32, 7500, "技术部"),
            new Employee("孙七", 28, 6000, "市场部")
        );

        // 按部门分组
        Map<String, List<Employee>> employeesByDept = employees.stream()
                                                               .collect(Collectors.groupingBy(Employee::getDepartment));

        System.out.println("技术部员工：" + employeesByDept.get("技术部").size()); // 输出3
        System.out.println("市场部员工：" + employeesByDept.get("市场部").size()); // 输出2

        // 按工资是否大于7000分区
        Map<Boolean, List<Employee>> partitioned = employees.stream()
                                                            .collect(Collectors.partitioningBy(emp -> emp.getSalary() > 7000));

        System.out.println("工资大于7000的员工：" + partitioned.get(true).size()); // 输出3
        System.out.println("工资小于等于7000的员工：" + partitioned.get(false).size()); // 输出2
    }
}
```



---

## 高级用法与技巧



**并行流**

Stream API 支持并行处理，只需将`stream()`替换为`parallelStream()`即可：

```Java
// 串行流
long count1 = employees.stream()
                       .filter(emp -> emp.getAge() > 30)
                       .count();

// 并行流
long count2 = employees.parallelStream()
                       .filter(emp -> emp.getAge() > 30)
                       .count();
```

并行流会自动将任务分配给多个线程执行，充分利用多核 CPU 的优势。但需注意以下几点：

- 数据量较小时，并行流的开销可能大于收益
- 确保操作是线程安全的，避免在中间操作中修改外部变量
- 避免使用有状态的中间操作（如`distinct()`、`sorted()`），它们会降低并行处理的效率



**flatMap 操作**

`flatMap`用于将流中的每个元素转换为一个流，然后将所有流合并为一个流。它常用于处理嵌套集合的场景

**需求**：从单词列表中提取所有不重复的字符

```Java
public class FlatMapDemo {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("Hello", "World");

        // 使用map：得到Stream<Stream<Character>>
        // 使用flatMap：得到Stream<Character>
        List<Character> distinctChars = words.stream()
                                              .flatMap(word -> word.chars()
                                                                   .mapToObj(c -> (char) c))
                                              .distinct()
                                              .collect(Collectors.toList());

        System.out.println(distinctChars); // 输出[H, e, l, o, W, r, d]
    }
}
```



**收集器的高级用法**

`Collectors`类提供了丰富的收集器，除了常用的`toList()`、`groupingBy()`外，还有以下实用的收集器：

```Java
public class AdvancedCollectorDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("张三", 25, 5000, "技术部"),
            new Employee("李四", 35, 8000, "市场部"),
            new Employee("王五", 40, 10000, "技术部")
        );

        // 拼接字符串
        String names = employees.stream()
                                .map(Employee::getName)
                                .collect(Collectors.joining(", "));

        System.out.println(names); // 输出张三, 李四, 王五

        // 按部门分组，统计每个部门的平均工资
        Map<String, Double> avgSalaryByDept = employees.stream()
                                                       .collect(Collectors.groupingBy(
                                                           Employee::getDepartment,
                                                           Collectors.averagingDouble(Employee::getSalary)
                                                       ));

        System.out.println("技术部平均工资：" + avgSalaryByDept.get("技术部")); // 输出7500.0
    }
}
```



---

# Optional 类

## 什么是 Optional 类



空指针异常（NullPointerException）是 Java 程序中最常见的异常之一，据统计，空指针异常占 Java 运行时异常的 70% 以上。JDK 8 引入的 Optional 类，从设计层面提供了空指针异常的解决方案

**Optional**是一个容器类，它可以包含一个非空的值，也可以表示空。Optional 类的设计思想是**强制开发者显式地处理空值的情况**，从而避免空指针异常

可以将 Optional 理解为：**一个可能为空的值的包装器**。它不允许包含 null 值（除非使用`ofNullable()`方法），并且提供了一系列方法来安全地处理空值



---

## 为什么需要 Optional 类



在 JDK 8 之前，处理空值需要编写大量的`if (obj!= null)`判断，代码冗长且容易遗漏。以下通过一个嵌套对象的示例展示传统写法的痛点

**需求**：获取一个人的汽车保险的名称

```Java
class Person {
    private Car car;
    public Car getCar() { return car; }
}

class Car {
    private Insurance insurance;
    public Insurance getInsurance() { return insurance; }
}

class Insurance {
    private String name;
    public String getName() { return name; }
}
```



**传统写法**：

```Java
public class TraditionalNullCheckDemo {
    public String getInsuranceName(Person person) {
        if (person!= null) {
            Car car = person.getCar();
            if (car!= null) {
                Insurance insurance = car.getInsurance();
                if (insurance!= null) {
                    return insurance.getName();
                }
            }
        }
        return "Unknown";
    }
}
```

上述代码包含三层嵌套的空值判断，代码结构丑陋且可读性差。只要有一个地方遗漏了空值判断，就会抛出空指针异常



**Optional 类解决方案**：

```Java
public class OptionalDemo {
    public String getInsuranceName(Person person) {
        return Optional.ofNullable(person)
                      .map(Person::getCar)
                      .map(Car::getInsurance)
                      .map(Insurance::getName)
                      .orElse("Unknown");
    }
}
```

使用 Optional 类，通过链式调用完成所有空值判断，代码简洁、优雅且不易出错。若任何一个环节返回空，都会直接返回默认值 "Unknown"



---

## 基本语法和用法



**创建 Optional 对象**

创建 Optional 对象有三种方式：

```Java
public class OptionalCreationDemo {
    public static void main(String[] args) {
        // 方式1：创建包含非空值的Optional
        // 若传入null，会抛出NullPointerException
        Optional<String> optional1 = Optional.of("Hello");

        // 方式2：创建可能包含空值的Optional
        // 若传入null，会创建一个空的Optional
        Optional<String> optional2 = Optional.ofNullable(null);

        // 方式3：创建一个空的Optional
        Optional<String> optional3 = Optional.empty();
    }
}
```

推荐使用方式 2：`ofNullable()`方法可以安全地处理 null 值，避免抛出空指针异常



**获取 Optional 中的值**

获取 Optional 中的值有四种方式：

```Java
public class OptionalGetValueDemo {
    public static void main(String[] args) {
        Optional<String> optional = Optional.of("Hello");

        // 方式1：get()
        // 若Optional为空，会抛出NoSuchElementException
        String value1 = optional.get();
        System.out.println(value1); // 输出Hello

        // 方式2：orElse(T other)
        // 若Optional为空，返回指定的默认值
        String value2 = optional.orElse("Default");
        System.out.println(value2); // 输出Hello

        // 方式3：orElseGet(Supplier<? extends T> other)
        // 若Optional为空，返回Supplier提供的值
        String value3 = optional.orElseGet(() -> "Default");
        System.out.println(value3); // 输出Hello

        // 方式4：orElseThrow(Supplier<? extends X> exceptionSupplier)
        // 若Optional为空，抛出指定的异常
        String value4 = optional.orElseThrow(() -> new RuntimeException("值不存在"));
        System.out.println(value4); // 输出Hello
    }
}
```

除非确定 Optional 包含非空值，否则不要使用`get()`方法，因为它会抛出异常。推荐使用`orElse()`、`orElseGet()`或`orElseThrow()`方法



**判断 Optional 是否为空**

```Java
public class OptionalIsPresentDemo {
    public static void main(String[] args) {
        Optional<String> optional = Optional.of("Hello");

        // 方式1：isPresent()
        // 若包含非空值，返回true
        if (optional.isPresent()) {
            System.out.println(optional.get());
        }

        // 方式2：ifPresent(Consumer<? super T> consumer)
        // 若包含非空值，执行Consumer操作
        optional.ifPresent(System.out::println);
    }
}
```



---

## 实际应用场景



**场景 1：处理嵌套对象的空值**

```Java
public class NestedObjectDemo {
    public String getInsuranceName(Person person) {
        return Optional.ofNullable(person)
                      .map(Person::getCar)
                      .map(Car::getInsurance)
                      .map(Insurance::getName)
                      .orElse("Unknown");
    }
}
```



**场景 2：处理集合的空值**

```Java
public class CollectionNullDemo {
    public List<String> getEmployeeNames(List<Employee> employees) {
        return Optional.ofNullable(employees)
                      .orElse(Collections.emptyList())
                      .stream()
                      .map(Employee::getName)
                      .collect(Collectors.toList());
    }
}
```



**场景 3：方法返回值**

将 Optional 作为方法的返回值，可以强制调用者处理空值的情况：

```
public class MethodReturnDemo {
    // 传统写法：可能返回null，调用者容易忽略空值判断
    public Employee findEmployeeByIdTraditional(List<Employee> employees, Long id) {
        for (Employee emp : employees) {
            if (emp.getId().equals(id)) {
                return emp;
            }
        }
        return null;
    }

    // Optional写法：返回Optional，强制调用者处理空值
    public Optional<Employee> findEmployeeById(List<Employee> employees, Long id) {
        return employees.stream()
                       .filter(emp -> emp.getId().equals(id))
                       .findFirst();
    }
}
```



**场景 4：参数校验**

```Java
public class ParameterValidationDemo {
    private String name;

    // 传统写法
    public void setNameTraditional(String name) {
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("姓名不能为空");
        }
        this.name = name;
    }

    // Optional写法
    public void setName(String name) {
        this.name = Optional.ofNullable(name)
                           .filter(n ->!n.trim().isEmpty())
                           .orElseThrow(() -> new IllegalArgumentException("姓名不能为空"));
    }
}
```



---

## 高级用法与技巧



**flatMap 操作**

当 Optional 中的值也是一个 Optional 时，需要使用`flatMap`操作：

```Java
class Person {
    private Optional<Car> car;
    public Optional<Car> getCar() { return car; }
}

class Car {
    private Optional<Insurance> insurance;
    public Optional<Insurance> getInsurance() { return insurance; }
}

class Insurance {
    private String name;
    public String getName() { return name; }
}

public class FlatMapDemo {
    public String getInsuranceName(Person person) {
        return Optional.ofNullable(person)
                      .flatMap(Person::getCar)
                      .flatMap(Car::getInsurance)
                      .map(Insurance::getName)
                      .orElse("Unknown");
    }
}
```



**map 与 flatMap 的区别**：

- `map`会将返回值包装在 Optional 中
- `flatMap`不会将返回值包装在 Optional 中，它要求函数返回一个 Optional



---

# 新的日期时间 API

## 为什么需要新的日期时间 API



JDK 8 引入了全新的日期时间 API，位于`java.time`包中，彻底解决了传统`Date`和`Calendar`类的设计缺陷

传统的`Date`和`Calendar`类存在以下严重问题：

1. 线程不安全：`Date`和`Calendar`是可变类，在多线程环境下会出现线程安全问题
2. 设计糟糕：`Date`类同时包含日期和时间，且年份从 1900 年开始，月份从 0 开始，非常反人类；`Calendar`类的 API 设计混乱，使用起来非常繁琐
3. 时区处理麻烦：处理时区需要编写大量的代码，且容易出错
4. 格式化类不安全：`SimpleDateFormat`是线程不安全的，在多线程环境下需要使用 ThreadLocal 包装



新的日期时间 API 具有以下优点：

1. 线程安全：所有的日期时间类都是不可变类，线程安全

2. 设计清晰：将日期、时间、时间戳、时区等概念清晰地分开

3. API 丰富：提供了大量的方法来处理日期时间的加减、比较、格式化等操作

4. 时区处理简单：内置了强大的时区处理功能

5. 格式化类安全：`DateTimeFormatter`是线程安全的，可以在多线程环境下直接使用



---

## 核心类介绍



新的日期时间 API 的核心类如下：

| 类名                | 功能描述                                              |
| ------------------- | ----------------------------------------------------- |
| `LocalDate`         | 表示日期（年 - 月 - 日），不包含时间和时区            |
| `LocalTime`         | 表示时间（时 - 分 - 秒），不包含日期和时区            |
| `LocalDateTime`     | 表示日期时间（年 - 月 - 日 时 - 分 - 秒），不包含时区 |
| `Instant`           | 表示时间戳（精确到纳秒），基于 UTC 时区               |
| `Duration`          | 表示两个时间之间的间隔（精确到纳秒）                  |
| `Period`            | 表示两个日期之间的间隔（精确到天）                    |
| `ZonedDateTime`     | 表示带时区的日期时间                                  |
| `DateTimeFormatter` | 日期时间格式化器，线程安全                            |



---

## 基本语法和用法



**LocalDate：处理日期**

```Java
public class LocalDateDemo {
    public static void main(String[] args) {
        // 获取当前日期
        LocalDate today = LocalDate.now();
        System.out.println("当前日期：" + today);

        // 创建指定日期
        LocalDate date = LocalDate.of(2023, Month.OCTOBER, 1);
        System.out.println("指定日期：" + date);

        // 获取年、月、日
        int year = today.getYear();
        int month = today.getMonthValue(); // 月份从1开始
        int day = today.getDayOfMonth();
        System.out.println("年：" + year + "，月：" + month + "，日：" + day);

        // 日期运算
        LocalDate tomorrow = today.plusDays(1);
        LocalDate yesterday = today.minusDays(1);
        LocalDate nextMonth = today.plusMonths(1);
        LocalDate lastYear = today.minusYears(1);

        System.out.println("明天：" + tomorrow);
        System.out.println("昨天：" + yesterday);
        System.out.println("下个月：" + nextMonth);
        System.out.println("去年：" + lastYear);

        // 日期比较
        boolean isBefore = today.isBefore(date);
        boolean isAfter = today.isAfter(date);
        boolean isEqual = today.isEqual(date);

        System.out.println("今天是否在指定日期之前：" + isBefore);
        System.out.println("今天是否在指定日期之后：" + isAfter);
        System.out.println("今天是否等于指定日期：" + isEqual);
    }
}
```



**LocalTime：处理时间**

```Java
public class LocalTimeDemo {
    public static void main(String[] args) {
        // 获取当前时间
        LocalTime now = LocalTime.now();
        System.out.println("当前时间：" + now);

        // 创建指定时间
        LocalTime time = LocalTime.of(14, 30, 0);
        System.out.println("指定时间：" + time);

        // 获取时、分、秒
        int hour = now.getHour();
        int minute = now.getMinute();
        int second = now.getSecond();
        System.out.println("时：" + hour + "，分：" + minute + "，秒：" + second);

        // 时间运算
        LocalTime nextHour = now.plusHours(1);
        LocalTime lastMinute = now.minusMinutes(1);

        System.out.println("一小时后：" + nextHour);
        System.out.println("一分钟前：" + lastMinute);
    }
}
```



**LocalDateTime：处理日期时间**

```Java
public class LocalDateTimeDemo {
    public static void main(String[] args) {
        // 获取当前日期时间
        LocalDateTime now = LocalDateTime.now();
        System.out.println("当前日期时间：" + now);

        // 创建指定日期时间
        LocalDateTime dateTime = LocalDateTime.of(2023, 10, 1, 14, 30, 0);
        System.out.println("指定日期时间：" + dateTime);

        // 转换为LocalDate和LocalTime
        LocalDate date = now.toLocalDate();
        LocalTime time = now.toLocalTime();

        System.out.println("日期部分：" + date);
        System.out.println("时间部分：" + time);

        // 日期时间运算
        LocalDateTime nextWeek = now.plusWeeks(1);
        LocalDateTime lastDay = now.minusDays(1);

        System.out.println("一周后：" + nextWeek);
        System.out.println("一天前：" + lastDay);
    }
}
```



**Instant：处理时间戳**

```Java
public class InstantDemo {
    public static void main(String[] args) {
        // 获取当前时间戳（基于UTC时区）
        Instant now = Instant.now();
        System.out.println("当前时间戳：" + now);

        // 从毫秒数创建时间戳
        Instant instant = Instant.ofEpochMilli(System.currentTimeMillis());
        System.out.println("指定时间戳：" + instant);

        // 获取毫秒数
        long millis = now.toEpochMilli();
        System.out.println("毫秒数：" + millis);

        // 时间戳运算
        Instant later = now.plusSeconds(60);
        Instant earlier = now.minusSeconds(60);

        System.out.println("60秒后：" + later);
        System.out.println("60秒前：" + earlier);
    }
}
```



**Duration 和 Period：处理时间间隔**

```Java
public class DurationPeriodDemo {
    public static void main(String[] args) {
        // Duration：处理时间间隔（时、分、秒、纳秒）
        LocalTime start = LocalTime.of(14, 0, 0);
        LocalTime end = LocalTime.of(16, 30, 0);

        Duration duration = Duration.between(start, end);
        System.out.println("时间间隔：" + duration);
        System.out.println("小时数：" + duration.toHours());
        System.out.println("分钟数：" + duration.toMinutes());
        System.out.println("秒数：" + duration.toSeconds());

        // Period：处理日期间隔（年、月、日）
        LocalDate startDate = LocalDate.of(2023, 1, 1);
        LocalDate endDate = LocalDate.of(2023, 10, 1);

        Period period = Period.between(startDate, endDate);
        System.out.println("日期间隔：" + period);
        System.out.println("年数：" + period.getYears());
        System.out.println("月数：" + period.getMonths());
        System.out.println("天数：" + period.getDays());

        // 计算两个日期之间的总天数
        long totalDays = ChronoUnit.DAYS.between(startDate, endDate);
        System.out.println("总天数：" + totalDays);
    }
}
```



**DateTimeFormatter：日期时间格式化**

```Java
public class DateTimeFormatterDemo {
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();

        // 格式化日期时间
        DateTimeFormatter formatter1 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String formatted1 = now.format(formatter1);
        System.out.println("格式化后的日期时间：" + formatted1);

        DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH时mm分ss秒");
        String formatted2 = now.format(formatter2);
        System.out.println("格式化后的日期时间：" + formatted2);

        // 解析字符串为日期时间
        String str = "2023-10-01 14:30:00";
        LocalDateTime dateTime = LocalDateTime.parse(str, formatter1);
        System.out.println("解析后的日期时间：" + dateTime);
    }
}
```



---

# 接口的默认方法和静态方法

## 为什么需要默认方法



在 JDK 8 之前，接口中只能包含抽象方法和常量。JDK 8 对接口进行了增强，允许接口包含默认方法和静态方法，这一特性极大地增强了接口的扩展能力

在 JDK 8 之前，如果为一个已有的接口添加新的抽象方法，那么所有实现了该接口的类都必须实现这个新方法，否则会导致编译错误。这对于已经广泛使用的接口来说，是一个灾难性的问题

例如，JDK 8 想为`Collection`接口添加一个`forEach`方法，用于遍历集合。如果按照传统的方式添加抽象方法，那么所有实现了`Collection`接口的类（如`ArrayList`、`LinkedList`、`HashSet`等）都必须实现`forEach`方法，这显然是不现实的

默认方法的出现解决了这个问题。开发者可以在接口中为新方法提供默认的实现，这样现有的实现类不需要做任何修改就可以使用这个新方法

默认方法的主要作用是：**在不破坏现有实现的情况下，为接口添加新的方法**



---

## 实际应用场景



**场景 1：为接口添加新方法**

```Java
// 旧版本的接口
public interface UserService {
    User getUserById(Long id);
}

// 新版本的接口：添加默认方法
public interface UserService {
    User getUserById(Long id);

    // 新添加的默认方法
    default List<User> getAllUsers() {
        throw new UnsupportedOperationException("getAllUsers方法未实现");
    }
}
```

这样，现有的实现类不需要做任何修改就可以继续使用，而新的实现类可以选择重写`getAllUsers`方法



**场景 2：提供工具方法**

```Java
public interface CollectionUtils {
    static <T> boolean isEmpty(Collection<T> collection) {
        return collection == null || collection.isEmpty();
    }

    static <T> boolean isNotEmpty(Collection<T> collection) {
        return!isEmpty(collection);
    }
}

// 使用
public class CollectionUtilsDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        if (CollectionUtils.isEmpty(list)) {
            System.out.println("列表为空");
        }
    }
}
```



**场景 3：解决多继承冲突**

当一个类实现了多个接口，而这些接口中有同名的默认方法时，会出现冲突。解决冲突的规则如下：

1. 类中的方法优先：如果类中重写了该方法，那么优先使用类中的实现
2. 子接口优先：如果一个接口继承了另一个接口，并且重写了默认方法，那么优先使用子接口的实现
3. 显式指定：如果以上规则都不适用，那么实现类必须显式指定使用哪个接口的默认方法

```Java
// 接口A
public interface InterfaceA {
    default void hello() {
        System.out.println("Hello from InterfaceA");
    }
}

// 接口B
public interface InterfaceB {
    default void hello() {
        System.out.println("Hello from InterfaceB");
    }
}

// 实现类：必须显式解决冲突
public class MyClass implements InterfaceA, InterfaceB {
    @Override
    public void hello() {
        // 显式指定使用InterfaceA的默认方法
        InterfaceA.super.hello();
        
        // 或者提供自己的实现
        // System.out.println("Hello from MyClass");
    }
}
```



---

# 其它重要特性

## CompletableFuture



`CompletableFuture`是 JDK 8 引入的一个强大的异步编程工具，它实现了`Future`和`CompletionStage`接口，提供了丰富的 API 来处理异步任务

传统的`Future`接口存在以下局限性：

- 不能手动完成任务
- 不能链式调用多个任务
- 不能组合多个任务的结果
- 没有异常处理机制

`CompletableFuture`解决了这些问题，它支持链式调用、任务组合、异常处理等功能，大大简化了异步编程的复杂度



**基本用法**：

```Java
public class CompletableFutureDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 异步执行任务
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            // 模拟耗时操作
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello CompletableFuture";
        });

        // 当任务完成时执行
        future.thenAccept(result -> System.out.println(result));

        // 等待任务完成
        future.get();
    }
}
```



**链式调用**：

```Java
CompletableFuture.supplyAsync(() -> "Hello")
                .thenApply(s -> s + " World")
                .thenApply(String::toUpperCase)
                .thenAccept(System.out::println); // 输出HELLO WORLD
```



**任务组合**

```Java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");

CompletableFuture<String> combinedFuture = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
combinedFuture.thenAccept(System.out::println); // 输出Hello World
```



---

## Base64



JDK 8 终于内置了 Base64 编码和解码的支持，不需要再依赖第三方库（如 Apache Commons Codec）

Base64 工具类位于`java.util.Base64`包中，它提供了三种类型的 Base64 编码：

- 基本型：标准的 Base64 编码
- URL 安全型：将`+`和`/`替换为`-`和`_`，适合在 URL 中使用
- MIME 型：每行输出 76 个字符，以换行符分隔，适合在 MIME 协议中使用



**基本用法**：

```Java
public class Base64Demo {
    public static void main(String[] args) {
        String original = "Hello World";

        // 编码
        String encoded = Base64.getEncoder().encodeToString(original.getBytes());
        System.out.println("编码后：" + encoded); // 输出SGVsbG8gV29ybGQ=

        // 解码
        byte[] decodedBytes = Base64.getDecoder().decode(encoded);
        String decoded = new String(decodedBytes);
        System.out.println("解码后：" + decoded); // 输出Hello World

        // URL安全型编码
        String urlEncoded = Base64.getUrlEncoder().encodeToString(original.getBytes());
        System.out.println("URL安全编码后：" + urlEncoded);
    }
}
```



---

## 重复注解



在 JDK 8 之前，同一个注解不能在同一个地方重复使用。JDK 8 引入了重复注解的支持，允许在同一个地方多次使用同一个注解



要使用重复注解，需要先定义一个容器注解：

```Java
// 容器注解
@Retention(RetentionPolicy.RUNTIME)
public @interface Roles {
    Role[] value();
}

// 重复注解
@Repeatable(Roles.class)
@Retention(RetentionPolicy.RUNTIME)
public @interface Role {
    String value();
}
```



使用重复注解：

```Java
@Role("admin")
@Role("user")
public class UserService {
    // 业务方法
}
```



---

## 类型注解



JDK 8 扩展了注解的使用范围，现在注解可以用在几乎任何地方：

- 泛型类型：`List<@NonNull String>`
- 强制类型转换：`(@NonNull String) obj`
- `new`操作符：`new @NonNull MyObject()`
- 方法参数：`void method(@NonNull String param)`
- 异常声明：`void method() throws @NonNull Exception`

类型注解主要用于静态代码分析工具（如 FindBugs、Checker Framework），帮助它们在编译时发现更多的错误
