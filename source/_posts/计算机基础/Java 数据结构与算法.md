---
title: Java 数据结构与算法
date: 2020-09-22 23:44:50
tags: 
  - 计算机基础
  - 数据结构与算法
categories: 计算机基础
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/franki-chamaki-1K6IQsQbizI-unsplash%20%281%29.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/franki-chamaki-1K6IQsQbizI-unsplash%20%281%29.jpg
---





## 如何区分算法的好坏

### 算法的效率

虽然计算机能快速的完成运算处理，但实际上，它也需要根据输入数据的大小和算法效率来消耗一定的处理器资源。要想编写出能高效运行的程序，我们就需要考虑到算法的效率



算法的效率主要由以下两个复杂度来评估： 
**时间复杂度**：评估执行程序所需的时间。可以估算出程序对处理器的使用程度
**空间复杂度**：评估执行程序所需的存储空间。可以估算出程序对计算机内存的使用程度



设计算法时，一般是要先考虑系统环境，然后权衡时间复杂度和空间复杂度，选取一个平衡点。不过，时间复杂度要比空间复杂度更容易产生问题，因此算法研究的主要也是时间复杂度，不特别说明的情况下，复杂度就是指时间复杂度



------



### 时间复杂度

#### 大O表示法

算法的时间复杂度通常用大O符号表述，定义为T[n] = O(f(n))。称函数T(n)以f(n)为界或者称T(n)受限于f(n)。  如果一个问题的规模是n，解这一问题的某一算法所需要的时间为T(n)。T(n)称为这一算法的“时间复杂度”。当输入量n逐渐加大时，时间复杂度的极限情形称为算法的“渐近时间复杂度”



**推导大O阶**

1. 用常数1来取代运行时间中所有加法常数。 
2. 修改后的运行次数函数中，只保留最高阶项 
3. 如果最高阶项存在且不是1，则去除与这个项相乘的常数



------



#### 常见时间复杂度

**常数阶**

```java
// 3行代码均只执行一次
int sum = 0, a = 1;
sum = (a + 1) * 2;
System.out.println(sum);
```

上面算法的运行的次数的函数为 f(n)=3，根据推导大O阶的规则1，我们需要将常数3改为1，则这个算法的时间复杂度为 O(1)。如果 sum = (a + 1) * 2 这条语句再执行10次，这个算法的复杂度仍为 O(1)，因此称之为常数阶



**线性阶**

```java
for(int i=0; i<n; i++) {
    // 时间复杂度为O(1)的算法
    ...
}
```

上面算法循环体中的代码执行了 n 次，因此这个算法的时间复杂度为 O(n)



**对数阶**

```java
int a = 1;
while(a < n) {
	a = a * 2;
	// 时间复杂度为O(1)的算法
    ...
}
```

上面算法中，随着 a 的每次乘 2，其数值都会越来越接近 n，直到大于 n，那么就将跳出循环。假设这个算法循环了 x 次，那么 2^x = n，可算得 x = logn。因此这个算法得时间复杂度为 O(logn)



**平方阶**

```java
for(int i=0; i<n; i++) {
	for (int j=0; j<n; j++) {
		 // 时间复杂度为O(1)的算法
    	...
	}
}
```

上面算法中，我们已知内层循环得时间复杂度为 O(n)，再经过外层循环 n 次，因此这个算法的时间复杂度为 O(n²)



如果我们算得某个算法的时间复杂度为 f(n) = 5n³ + 3n + 2，那么根据推导大O阶的第二条规则，只保留最高阶，那么这个算法的时间复杂度就变成了 f(n) = 5n³，再根据推导大O阶的第三条规则，去掉常数项，因此这个算法最终算得的时间复杂度应该是 O(n³)



------



#### 其它常见时间复杂度

除了常数阶、线性阶、平方阶、对数阶，还有如下时间复杂度

* f(n) = nlogn 时，时间复杂度为 O(nlogn)，称为 nlogn 阶
* f(n) = n³ 时，时间复杂度为 O(n³)，称为立方阶
* f(n) = 2ⁿ 时，时间复杂度为 O(2ⁿ)，称为指数阶
* f(n) = n! 时，时间复杂度为 O(n!)，称为阶乘阶
* f(n) = √n 时，时间复杂度为 O(√n)，称为平方根阶



------



#### 时间复杂度的比较

![image-20201103001513219](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201103001513219.png)



在上图中，X 轴代表 n 值，Y 轴代表时间复杂度。时间复杂度随着 n 值得变化而变化，可以看到，O(n)、O(logn)、O(√n )、O(nlogn )随着 n 的增加，复杂度提升不大，而 O(2ⁿ) 和 O(n!)  在 n 值较大时变化非常之大。实际上，在 n 等于 50 时，O(2ⁿ) 和 O(n!)  的复杂度就已经突破十位数了



常用的时间复杂度按照耗费的时间从小到大依次是：

O(1) < O(logn) < O(n) < O(nlogn) < O(n²) < O(n³) < O(2ⁿ) < O(n!)



------



## 数组

### 数组的概念



![image-20201104232327992](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201104232327992.png)



定义：数组（Array）是一种线性表数据结构，他用一组连续的内存空间，来存储一组具有相同类型的数据



1. 线性表

   所谓线性表，指的是每个元素最多只有前后两个方向，像一条线一样进行排列，除了数组，链表、队列、栈等都是线性表

2. 连续的内存空间和相同类型的数据



正是因为这两点，才使得数组拥有了它的核心特性 -- 随机访问



**随机访问**

所谓随机访问，指的是能够随意访问数组中的任意一个元素，比如我想访问第 n 个元素，那么该元素的起始位置就是：

​		第 n 个元素的起始位置 = 第 1 个元素的起始位置  + 下标（从0开始）* 元素的长度

而不是从第 1 个元素开始，依次访问第 2 个、第 3个 ...，直到访问到第 n 个为止



------

### 数组的操作



* 数组支持随机访问，根据下标获取指定元素的时间复杂度为 O(1)
* 如果想获取数组中指定某个值的元素的话，时间复杂度就变成了 O(n)；如果是有序数组的话，使用二分法，可以将时间复杂度将为 O(logn)
* 数组为了保证内存数据的连续性，会导致插入、删除这两个操作比较低效



#### 数组的插入

1. 从头部和中间插入
   * 如果是无序数组的话，最简单的插入方式是将要插入的第 k 个位置的元素移到最末尾，然后将新元素放入第 k 个位置，这种插入方法的时间复杂度为 O(1)
   * 如果是有序数组的话，向第 k 个位置插入元素，那么位置 k 后的所有元素都需要向后移动一位，这种情况下最坏的时间复杂度可达到 O(n)
2. 从尾部插入
   * 从尾部插入数据的话只需要直接在数据末尾添加一个元素即可，不需要移动其它元素，时间复杂度为 O(1)



#### 数组的删除

1. 删除头部和中间元素
   * 为了保证数组中数据的连续性，数组中间的第 k 个元素删除之后，位置 k 之后的所有元素都需要向前移动一位，最坏时间复杂度为 O(n)
2. 删除尾部元素
   * 删除尾部元素不需要移动元素，只需要将末尾元素删除即可，时间复杂度为 O(1)



------

### ArrayList

#### 介绍



在 Java 中，我们可以自己创建并维护一个数组，也可以使用 Java 提供的数组容器类 ArrayList。相比于自建数组，ArrayList 提供了一些高级功能，比如自动扩容、泛型、更方便的处理元素等等



在我们使用数组时，除非已知数组的所有元素并且几乎不再更改，否则都会使用 ArrayList 来进行数组的相关操作



ArrayList 并不是线程安全的，如果我们想要保障 ArrayList 的线程安全，需要自己使用 Synchronized 关键字维护，或者调用 Collections.synchronizedList() 方法，获得一个线程安全的 ArryList，或者直接使用 JUC 包中的 CopyOnWriteArrayList



ArrayList 继承了 RandomAccess 接口，进入这个接口会发现 RandomAccess 接口中并无任何的变量或方法。事实上，这是一个“标志”接口，标志这 ArrayList 这个类具备随机访问的特性



------

#### 主要的成员变量



```java
/**
 * Default initial capacity.
 */
 private static final int DEFAULT_CAPACITY = 10;
```

ArrayList 的初始数组长度为 10



------

#### 构造方法



![image-20201107180130865](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201107180130865.png)

* 无参构造方法

  返回一个空数组，在第一次往数组里添加元素的时候才赋予数组长度，初始长度为 0

* int 类型参数构造方法

  返回一个指定长度的数组，入参为 0 的时候返回一个空数组，与无参构造方法类似，入参小于 0 则抛出异常

* Collection 类型参数构造方法

  传入一个 Collection 集合，将该集合转为 ArrayList 数组



------

####   扩容机制



调用 ArrayList 的 add() 方法时，ArrayList 都会先判断一下当前数组是否已满，如果已经满了的话，就会调用 grow() 方法进行扩容



```java
private Object[] grow(int minCapacity) {
     int oldCapacity = elementData.length;
     if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
         int newCapacity = ArraysSupport.newLength(oldCapacity,
                minCapacity - oldCapacity, /* minimum growth */
                oldCapacity >> 1           /* preferred growth */);
        return elementData = Arrays.copyOf(elementData, newCapacity);
    } else {
        return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];
    }
}
```



add() 方法在调用 grow() 方法时，传入的 minCapacity 的值为 size+1，但是 ArraysSupport.newLength() 会判断 minCapacity 的值和 oldCapacity >> 1（即原数组长度的一半）哪个更大，原数组长度加上两者之间的大者即为扩容后的新数组的长度。因此通常情况下扩容后的容量为原容量的 1.5 倍



**自动扩容带来的问题**

数字的自动扩容会带来一些空间资源的浪费，有时候只是想往数组中添加一个元素，长度加 1 即可，但是却让数组长度变成了原来的 1.5倍。在数组长度较小的时候，些许的浪费无伤大雅，但是当数据量较大时，带来的资源浪费就显得没必要了



**解决方法**

* 如果在创建数组时便知道数组的长度，就可以使用 ArrayList 的 int 类型参数构造方法来创建一个指定长度的数组了
* 如果在创建数组的时候并不知道数组的长度，但是在插入数组的时候知道了，那么可以使用 ensureCapacity() 方法来将数组扩容到想要的长度
* 还可以在数组插入完成之后，调用 trimToSize() 方法，将数组多余的长度清空掉



------

#### 迭代器



迭代器 Iterator 是一种设计模式，所有的 Collections 集合类都有实现这种设计模式，为得是提供一套统一的 API 来操作元素



迭代器有两种属性，fail-fast 和 fail-safe

* fail-fast

  ArrayList 会维护一个 modCount 变量，每次对数组的增删改操作都会将 modCount 加 1。迭代器在遍历元素时直接获取原有集合的内容，在操作过程中如果发现 modCount 的值发生了变化，就会抛出异常

* fail-safe

  迭代器在遍历元素时，会将原有集合复制出来一份，然后在复制出来的这个集合上进行操作。这样做避免了抛出异常，但是原有集合上的操作并不能被迭代器检测到

  

------

## 链表

### 链表的概念



数组是使用最广泛的一种通用数据结构，但是也不可避免的存在着一些缺陷。在无序数组中，搜索是低效的；而在有序数组中，插入效率又很低；而不管再哪一种数组中，删除效率都很低；并且一个数组在创建后，它的大小是不可改变的。而链表则可以解决上面的一些问题



链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的。每个元素节点除了存储自己的数据域之外，还存储着下一个节点的指针域



![image-20201110211256134](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201110211256134.png)



链表需通过头元素一个一个遍历才能找到目标元素，不支持随机访问。但是链表的增删操作只需要改变节点的指针域即可，不需要移动元素，增删速度较快。除非需要频繁通过下标访问元素，否则很多使用数组的地方都可以使用链表代替。而链表也是除数组外运用第二广泛的通用数据结构



------

### 链表的操作

#### 链表的基本结构



链表作为一个集合，首先需要知道头节点的位置，否则即使查询也不知道从何查起。而集合中存储着一系列的节点，这些节点既需要保存着自己的数据域，还需要保存着下一个节点的位置



```java
public class LinkList {
    // 头节点
    private Link fisrt;
    
    class Link {
        // 指向下一个节点
        private Link next;
        // 数据域
        private Object data;
    }
    ...
}
```



这是一个链表最基本的组成，还可以通过添加其它属性和方法来丰富这个链表，比如添加 size 属性，获得这个链表的长度；还可添加 Link last 属性，来更快的找到尾节点等等



------

#### 链表的查询



不管是查询指定值的节点，还是查询第 k 个元素，链表都需要从头节点一个个遍历，因此链表查询的时间复杂度固定为 O(n)



------

#### 链表的插入和删除



链表删除节点 k，只需要将节点 k-1 的指针域执行节点 k+1 即可，时间复杂度为 O(1)

```java
public void remove(Link removeLink) {
    // 获取被删除节点的上一个节点
    Link preLink = removeLink.pre;
    // 将上一个节点的指针域只想被删除节点的下一个节点
    preLink.next = removeLink.next;
}
```



链表想在节点 k 和 k+1 之间插入一个元素，需要将节点 k 的指针域指向插入的节点，然后将插入节点的指针域指向节点 k+1，时间复杂度为 O(1)

```java
public void insert(Link preLink, Link insertLink) {
    // preLink为节点k，通过节点k获取节点k+1
    Link afterLink = preLink.next;
    // 将节点k的指针域指向插入的节点
    preLink.next = insertLink;
    // 将插入节点的指针域指向节点k+1
    insertLink.next = afterLink;
}
```



------

### 不同类型的链表



* 单向链表

  像上面这样只能从上一个节点获取下一个节点的链表，称为单项链表

* 双向链表

  如果链表中的节点不仅存储着下一个节点的指针域，还存储着上一个节点的指针域，这种链表称为双向链表

* 循环链表

  链表的尾节点又重新指向头节点，形成了一个圈，这种链表称为循环链表



------

### LinkedList

#### 主要的成员变量



```java
transient int size = 0;

/**
 * Pointer to first node.
 */
transient Node<E> first;

/**
 * Pointer to last node.
 */
transient Node<E> last;
```



LinkedList 只有三个成员变量，size 用于记录集合中的元素个数，first 和 last 分别指向头节点和尾节点



#### Node节点元素



```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```



可以看出，LinkedList 的节点元素既可以指向上一个元素，也可以指向下一个元素，所以 LinkedList 是一个双向链表



------

#### 构造方法



**无参构造方法**

```java
public LinkedList() {
}
```

Java 15 中，LinkedList 的无参构造方法会返回一个空集合，头节点和尾节点都是 null



**有参构造方法**

```java
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```

LinkedList 的有参构造方法可以传入一个集合，将这个集合转为 LinkedList



相比与 ArrayList，LinkedList 无须扩容，新增和删除的实现方式也比较简单



------

## 栈和队列

### 抽象数据类型（ADT）



抽象数据类型（Abstract Data Type，ADT）是带有一组操作的一些对象的集合。抽象数据类型是数学的抽象，在 ADT 的定义中没有地方提到关于这组操作是如何实现的具体解释。比如我们定义了线性表（List）这么一个 ADT，要求元素像一条线一样只有前后两个方向排列，那么只要满足了这个要求的，都可以看作线性表 ADT 的具体实现



Java 也考虑了 ADT 的实现，不过适当了隐藏了实现的细节。最直观的方式就是“接口”，Java 中有 List 接口，只有实现了这个接口的类，都是 List 的具体实现类，例如 ArrayList、LinkedList，还有后续的 Deque、Stack等，都可以看作一个List。而在 Java 中，这就是对象多态的体现



```java
public static void main(String[] args) {

    List<String> list1 = new ArrayList<>();
    List<String> list2 = new LinkedList<>();
        
    list1.add("list1");
    list2.add("list2");

}
```

如上所示，我们可以只需要获得一个 List集合，然后支持 List 集合的方法即可，而不关系它究竟是 ArrayList 还是 LinkedList





![image-20201115151509347](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201115151509347.png)

**此图只是对ADT的简单展示，Java中数据结构中的层次远比这个复杂得多**



不同的 ADT 之间也有着不同的关系，如上图所示，可以有层级关系，如 List 和 Queue 之间；也有并列关系，如 List 和 Map 之间；还有可能什么关系也没有。我们可以根据自己的需求去选用合适的 ADT 实现类，如果我们想要个 List 集合的话，就去找一个实现了 List 集合接口的类；如果想要个 Queue 的话，就去找一个实现了 Queue 接口的类；如果当前存在的都不满足需求的话，我们也可以自己实现一个，只要实现了对应的 ADT 接口即可



------



### 栈ADT

#### 栈模型



栈（Stack）是限制插入和删除只能在一个位置上进行的表，该位置是表的末端，叫做栈的顶（top）。对栈的基本操作有 push（进栈）和 pop（出栈），前者相当于插入，后者则是删除最后插入的元素。最后插入的元素可以通过 top 例程在执行 pop 之前进行考察。对空栈进行 top 或 pop 操作一般被认为栈 ADT 中的一个错误。另一方面，当运行 push 时空间用尽是一个实现限制，但不是 ADT 错误



栈有时又叫做 LIFO（后进先出）表。普通的清空栈的操作和判断是否空栈的操作都是栈的操作系统指令的一部分。但是，我们对栈所能够做的，基本上也就是 pop 和 push 操作



![image-20201115170259354](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201115170259354.png)

![image-20201115170423180](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201115170423180.png)



------

### 队列ADT

#### 队列模型



像栈一样，队列也是表。但是，使用队列时插入在一段进行而删除则在另一端进行



队列的基本操作是 enqueue（入队），它是在表的末端（叫做队尾 rear）插入一个元素；以及 dequeue（出队），它是返回并删除在表开头（叫做对头 front）的元素。队列，也叫做 FIFO（先进先出）表



![image-20201115195545751](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201115195545751.png)



------

### 栈的具体实现

#### 栈的实现



由于栈是一个表，因此任何实现表的方法都能实现栈。显然，ArrayList 和 LinkedList 都支持栈操作，99% 的时间它们都是最合理的选择。偶尔设计一种特殊目的的实现可能会更快。因为栈操作的时间复杂度为 O(1)，所以，除非在非常独特的环境下，这是不可能产生任何明显的改进的。对于这些特殊的时机，一般给出了两种流行的实现方法，一种方法使用链式结构，而另一种方法使用数组，二者均简化了在 ArrayList 和 LinkedList 中的逻辑



------

#### 栈的链式实现



栈的第一种实现方法是使用单链表，通过在表的顶端插入来实现 push，通过删除表顶端元素来实现 pop。top 操作只是考查表顶端元素并返回它的值，有时 pop 操作和 top操作合二为一



------

#### 栈的数组实现



另一种方式避免了链并且可能是更流行的解决方案。由于模仿 ArrayList 的 add 操作，因此相应的实现方式非常简单。与每个栈相关联的操作是 theArray 和 topOfStack，对于空栈它是 -1，这就是空栈初始化的做法。为将某个元素 x 推入栈中，我们是 topOfStack 增 1 然后置 theArray[topOfStack] = x。为了弹出栈元素，我们置返回值为 theArray[topOfStack] 然后使 topOfStack 减 1



注意，这些操作不仅以常数时间运行，而且是以非常快的常数时间运行。在某些机器上，若在带有自增和自减寻址功能的寄存器上操作，则（整数的）pop 和 push 都可以写成一条机器指令。最现代化的计算机将栈操作作为它指令系统的一部分，这个事实强化了这样一种观念，即栈很可能是计算机科学中在数组之后的最基本的数据结构



------

### 队列的具体实现

#### 队列的链表实现



同栈一样，任何表都可以实现队列，而使用链表来实现队列是最简单的。入队对应的就是 LinkedList 的 add 操作，而出队则是取出 first 元素，并将 first 指向后一个元素即可



------

#### 队列的数组实现



对于每一个队列数据结构，我们保留一个数组 theArray 以及位置 front 和 back，他们代表队列的两端。还要记录实际存在与队列中的元素个数 currentSize，下图表示处于某个中间状态的队列



为使一个元素 x 入队（即执行 enqueue），我们让 currentSize 和 back 置 1，然后置 theArray[back] = x。若是元素  dequeue（出队），我们置返回值为 theArray[front]，且 currentSize 和 front 都增 1



上述实现存在着一个潜在的问题，经过 10 次 enqueue 之后队列似乎是满了，因为 back 现在是数组的最后一个下标，而下一次再 enqueue 就会是一个不存在的位置。然而，队列中也许只存在几个元素，因为若干元素可能已经出队了。像栈一样，即使在有许多操作的情况下队列也常常不是很大



简单的解决办法是，只要 front 或 back 到达数组的尾端，它就又绕回开头，这就是循环数组



------

#### 双端队列



栈和队列都是线性表的一种，且二者添加元素都是在线性表的尾部新增元素。不同的是，取出元素的时候，栈是从尾部取出的，而队列是从头部取出的。那么如果有一个线性表，既可以从头部取出元素，也可以从尾部取出元素，那么它就具备了栈和队列的双重特性，而像这样可以从两端弹出元素的线性表，则称之为双端队列



双端队列让我们可以更自由的操作线性表中的元素，还可以减少代码的实现。如果想使用队列，则只从表的头部取出元素，如果想使用栈，则只从尾部取出元素。双端队列将栈和队列的抽象从数据结构层转移到了代码层，如果我们想固定使用某一种结构的话，就需要严格调用该结构对应的方法，否则就会使线性表中的元素混乱



Java 将双端队列定义为一个接口 Deque，以下是 Deque 接口常用的一些方法：

* push：像尾部添加元素
* pop：移除尾部元素并返回它的值
* poll：将头部元素弹出，如果表为空就返回 null
* peek：查看头部元素，不会移除，如果是空就返回 null
* element：查看头部元素，不会移除，如果是空就抛出异常



------

### JAVA 中的栈和队列

#### LinkedList



查看 LinkedList 的源码，会发现它实现了 Deque 接口，这意味着 LinkedList 也是也是一个双端队列。所以由链表实现的栈和由链表实现的队列都是 LinkedList



------

#### Stack



Stack 是由数组实现的栈，它继承了 Vector 类，Vector 是线程安全的 ArrayList，Stack 调用的也是它的方法，这意味着 Stack 也是线程安全的栈



------

#### ArrayQueue



ArrayQueue 是用数组实现的队列，底层的数据结构也是上文提到的循环数组



![image-20201121224210165](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201121224210165.png)



上图分别是队空时和队满时的两种状态，当队空时，头坐标和尾坐标指向同一个元素，即 front = rear；当队满时，尾坐标在头坐标的前一个位置，而头坐标指向的位置没有放置元素，这意味着循环数组能够放置的元素数量会比数组的长度小一个



我们也可用一个状态标识 empty 来区分当前队列是空还是满，队列初始化时 full为 false，而当 rear 在 front 前一个位置时，我们再往其中添加一个元素，此时 full 就变成了 true，此时如果有元素出队的话，full 再重新变为 false

</br>

**成员变量**

```java
// 队列可容纳元素的数量
private int capacity;
// 放置元素的数组
private T[] queue;
// 头元素下标
private int head;
// 尾元素下标
private int tail;
```



**构造方法**

```java
public ArrayQueue(int capacity) {
    // 为了让队列可容纳元素的数量为构造方法传入的值，将传入的capacity的值加1
    this.capacity = capacity + 1;
    this.queue = newArray(capacity + 1);
    // 初始化时头尾坐标均指向0
    this.head = 0;
    this.tail = 0;
}
```



**扩容**

当 ArrayQueue 满了的时候，我们再往里面添加元素会抛出异常，如果当前队列的大小不满足需求的话，需要我们调用 resize() 方法进行扩容

```java
public void resize(int newcapacity) {
    // resize方法不仅可以扩容，还可以缩容，如果新队列的长度小于实际元素个数的话就会抛出异常
    int size = size();
    if (newcapacity < size)
        throw new IndexOutOfBoundsException("Resizing would lose data");
    // 队列长度为目标长度+1
    newcapacity++;
    if (newcapacity == this.capacity)
        return;
    T[] newqueue = newArray(newcapacity);
    // get(i)是循环将头节点和尾节点的元素取出
    for (int i = 0; i < size; i++)
        newqueue[i] = get(i);
    this.capacity = newcapacity;
    this.queue = newqueue;
    // 新队列的头节点为0
    this.head = 0;
    this.tail = size;
}
```

</br>

**入队**

```java
public boolean add(T o) {
    // 先给队尾添加值，再判断是否队满
    queue[tail] = o;
    // 判断队满的方式：(tail + 1) % capacity == head
    int newtail = (tail + 1) % capacity;
    if (newtail == head)
        throw new IndexOutOfBoundsException("Queue full");
    // 队列没满的情况下，队尾下标才会后移一位
    tail = newtail;
    return true; // we did add something
}
```

先插入元素再判断队列有没有满，这样队尾下标元素实际上是有值的，但是我们无法取得而已。并且每次添加元素都会把之前的值覆盖掉

</br>

**出队**

```java
public T remove(int i) {
    // 队列只能从头元素开始取
    if (i != 0)
        throw new IllegalArgumentException("Can only remove head of queue");
    // 当 head == tail 的时候队列为空
    if (head == tail)
        throw new IndexOutOfBoundsException("Queue empty");
    // 获取头元素
    T removed = queue[head];
    // 将头元素置空
    queue[head] = null;
    // 头元素下标加1
    head = (head + 1) % capacity;
    return removed;
}
```

## 二叉树

### 什么是树

#### 树的简介

![image-20201124204551109](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201124204551109.png)



树是一种抽象数据类型或是实现这种抽象数据类型的数据结构，用来模拟具有树状结构性质的数据集合。它是由n（n>0）个有限节点组成一个具有层次关系的集合。把它叫做“树”是因为它看起来像一棵倒挂的树，也就是说它是根朝上，而叶朝下的。它具有以下的特点：

1. 每个节点有零个或多个子节点；
2. 没有父节点的节点称为根节点；
3. 每一个非根节点有且只有一个父节点；
4. 除了根节点外，每个子节点可以分为多个不相交的子树；



------

#### 树的术语



![image-20201124221707828](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201124221707828.png)

* 结点：使用树存储结构存储的每一个数据元素都被称为结点
* 路径：顺着连接结点的边从一个结点走到另一个节点，所经过的结点顺序排列就称为路径
* 根结点：树顶端的结点称为根结点，如果一个结点没有父结点，那么它就是整棵树的根节点，上图的根结点就是 A
* 父结点：每个结点（除根结点）都恰好有一条边向上连接到另一个结点，上面这个结点就称为下面这个结点的父结点
* 子结点：与父结点相反，比如 B 是 E 的父结点，那么 E 就是 B 的子结点
* 叶结点：如果结点没有任何子结点，那么此结点称为叶结点
* 空树：如果集合本身为空，那么构成的树就为空树，空树没有结点
* 子树：全部由子结点构成的树称为子树，如上图，整个是一棵树，B、E、F、K、L 也可看作一棵树，E、K、L也可看作一棵树，它们都可看作这棵树的子树
* 结点的度：对于一个结点，拥有的子树数（结点有多少分支）称为结点的度
* 结点的层次：从一棵树的树根开始，树根所在层为第一层，根的孩子结点所在的层为第二层，依次类推
* 树的深度：一棵树的深度（高度）是树中结点所在的最大的层次
* 有序树和无序树：如果树中结点的子树从左到右看，谁在左边，谁在右边，是有规定的，这棵树称为有序树；反之称为无序树
* 森林：由 m（m >= 0）个互不相交的树组成的集合被称为森林

------

### 二叉树

#### 二叉树的定义



简单地理解，满足以下两个条件的树就是二叉树：

1. 本身是有序树
2. 树中包含的各个节点的度不能超过 2，即只能是 0、1 或者 2



![image-20201211175500008](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201211175500008.png)





------

#### 二叉树的性质



1. 二叉树中，第 i 层最多有 2^(i-1) 个结点
2. 如果二叉树的深度为 K，那么此二叉树最多有 2^(K-1) 个结点
3. 二叉树中，终端结点数（叶子结点数）为 n0，度为 2 的结点数为 n2，则 n0=n2+1



------

#### 满二叉树



如果二叉树中除了叶子结点，每个结点的度都为 2，则此二叉树称为满二叉树



![image-20201211180057924](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201211180057924.png)



满二叉树除了满足普通二叉树的性质，还具有以下性质：

1.  满二叉树中第 i 层的节点数为 2^(n-1) 个
2.  深度为 k 的满二叉树必有 2^(k-1) 个节点 ，叶子数为 2^(k-1)
3.  满二叉树中不存在度为 1 的节点，每一个分支点中都两棵深度相同的子树，且叶子节点都在最底层
4.  具有 n 个节点的满二叉树的深度为 log2(n+1)



------

#### 完全二叉树



如果二叉树中除去最后一层节点为满二叉树，且最后一层的结点依次从左到右分布，则此二叉树被称为完全二叉树

![image-20201212192543825](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20201211180057924.png)



对于任意一个完全二叉树来说，如果将含有的结点按照层次从左到右依次标号，对于任意一个结点 i ，完全二叉树还有以下几个结论成立：

1. 当 i>1 时，父亲结点为结点 [i/2] 。（i=1 时，表示的是根结点，无父亲结点）
2. 如果 2*i>n（总结点的个数） ，则结点 i 肯定没有左孩子（为叶子结点）；否则其左孩子是结点 2*i 
3. 如果 2*i+1>n ，则结点 i 肯定没有右孩子；否则右孩子是结点 2*i+1 



------

### 二叉树的结构

#### 二叉树的顺序储存结构



二叉树的顺序存储，指的是使用数组存储二叉树。需要注意的是，顺序存储只适用于完全二叉树。换句话说，只有完全二叉树才可以使用顺序表存储。因此，如果我们想顺序存储普通二叉树，需要提前将普通二叉树转化为完全二叉树



完全二叉树的顺序存储，仅需从根节点开始，按照层次依次将树中节点存储到数组即可



![image-20210103150636489](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210103150636489.png)



------

#### 二叉树的链式存储结构



其实二叉树并不适合用数组存储，因为并不是每个二叉树都是完全二叉树，普通二叉树使用顺序表存储或多或多会存在空间浪费的现象。而一棵普通的二叉树，若将其采用链式存储，则只需从树的根节点开始，将各个节点及其左右孩子使用链表存储即可



![image-20210103151203203](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210103151203203.png)



采用链式存储二叉树时，其节点结构由三部分组成

* 指向左孩子节点的指针（leftChild）
* 节点存储的数据（data）
* 指向右孩子节点的指针（rightChild）

![image-20210103151359861](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210103151359861.png)

我们还可以在该节点中添加一个指针，指向该结点的父节点。这样的链表结构，称为三叉链表



```java
public class Node{
    private Node leftChild;
    private Object Data;
    private Node rightChild;
    
    private Node parent;
}
```



------

### 二叉树的遍历



所谓遍历(Traversal)是指沿着某条搜索路线，依次对树中每个结点均做一次且仅做一次访问。访问结点所做的操作依赖于具体的应用问题。 遍历是二叉树上最重要的运算之一，是二叉树上进行其它运算之基础



二叉树的遍历主要有以下四种方式：

* 前序遍历
* 中序遍历
* 后序遍历
* 层序遍历



#### 前序遍历

![image-20210103183839223](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210103183839223.png)

二叉树前序遍历的实现思想是：

1. 访问根节点
2. 访问当前节点的左子树
3. 若当前节点无左子树或左子树已访问过，继续访问当前节点的右子树

因此，以上二叉树采用前序遍历得到的序列为：1245367



```java
/**
* 前序遍历代码实现
*/
public void preOrderTraveral(Node node) {
    if (node == null) {
        return;
    }
    System.out.println(node.data.toString());
    preOrderTraveral(node.leftChild);
    preOrderTraveral(node.rightChild);
}
```



------

#### 中序遍历



二叉树中序遍历的实现思想是：

1. 访问当前节点的左子树
2. 访问根节点
3. 访问当前节点的右子树

因此，以上二叉树采用中序遍历得到的序列为：4251637



```java
/**
* 中序遍历代码实现
*/
public void inOrderTraveral(Node node) {
    if (node == null) {
        return;
    }
    inOrderTraveral(node.leftChild);
    System.out.println(node.data.toString());
    inOrderTraveral(node.rightChild);
}
```



------

#### 后序遍历



二叉树前序遍历的实现思想是：

1. 访问当前节点的左子树
2. 访问当前节点的右子树
3. 访问当前节点

因此，以上二叉树采用中序遍历得到的序列为：1376254



```java
/**
* 后序遍历代码实现
*/
public void postOrderTraveral(Node node) {
    if (node == null) {
        return;
    }
    postOrderTraveral(node.leftChild);
    postOrderTraveral(node.rightChild);
    System.out.println(node.data.toString());
}
```



------

#### 层序遍历



![image-20210110194451416](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210110194451416.png)



二叉树层序遍历的实现思想是：

1. 通过使用队列的数据结构，从树的根节点开始，先入队，再出队
2. 出队的同时，将根节点的左右子结点分别入队
3. 此后每次出队操作，都将该节点的左右孩子节点入队，知道树中的所有结点都出队

```java
/**
* 层序遍历代码实现
*/
public void levelOrder(Node root) {
    LinkedList<Node> queue = new LinkedList<>();
    queue.add(root);
    while (!queue.isEmpty()) {
        node = queue.pop();
        System.out.println(node.data.toString());
        if (node.leftChild != null) {
            queue.add(node.leftChild);
        }
        if (node.rightChild != null) {
            queue.add(node.rightChild);
        }
    }
}
```



## 特殊的二叉树

### 线索二叉树

#### 定义



普通的二叉树，不管形态如何，空链域的个数总是多过非空链域的个数。所谓空链域，指的是孩子节点为 null 的指针域。实际上，n 个节点的二叉树共有 2n 个链域，其中非空链域有 n - 1 个，而空链域却有 n + 1 个



因此，我们可以利用这些空链域，指向该节点的前驱节点或者后继节点，这样就可以充分利用到这些空链域，提升遍历速度。所谓前驱节点和后继节点，指的是该二叉树遍历完成后顺序输出的顺序中，该节点的前一个节点和后一个节点。众所周知，使用不同的遍历方法遍历出来的节点顺序是不一样的，因此同一个二叉树使用不同的线索顺序，同一个节点的前驱和后继节点也可能是不一样的。这里我们主要介绍一下中序遍历线索二叉树



使用空链域来指向前驱后继节点，我们就需要区分该节点的左右孩子节点指向的是自己的孩子还是前驱后继节点，因此我们需要增加两个标识位，leftTag 和 rightTag，当 tag 的值为 0 时说明指向的是自己的孩子节点，值为 1 时说明指向的是前驱或者后继节点



![image-20210117135411299](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210117135411299.png)



```java
public class Node {
    private Object data;
    private Node leftChild;
    private Node rightChild;
    private Integer leftTag;
    private Integer rightTag;
}
```



------

#### 线索化



将一棵普通的二叉树转为线索二叉树，即将二叉树所有的空链域指向自己的前驱或者后继节点，这个过程称为线索化

```java
public class ThreadTree {
    private Node root;
    // 前驱节点
    private Node pre = null;
    
 	/**
 	* 将以root为根节点的二叉树线索化 中序法
 	**/
	public void inThread(Node root) {
    	if (root != null) {
        	// 找到该二叉树的最左孩子节点，该节点即为该二叉树的最前驱节点
        	inThread(root.leftChild);
        	if (root.leftChild == null) {
            	root.setLeftTag(1);
                root.setLeftChild(pre);
        	}
            if (pre != null && null == pre.rightChild) {
                pre.setRightTag(1);
                pre.setRightChild(root);
            }
            pre = root;
            inThread(root.rightChild);
    	}
	}
}
```



------

#### 中序遍历



```java
/**
* 中序遍历代码实现
*/
public void inOrderTraveral(Node root) {
    inOrderTraveral(node.leftChild);
    System.out.println(node.data.toString());
    inOrderTraveral(node.rightChild);
}
```



------

### 哈夫曼树（最优二叉树）

#### 什么是哈夫曼树



哈夫曼树最常用的地方是编码压缩。举个例子，如果我们需要传送一份全英文电报，需要将里面的英文字母全部转成二进制字节码，需要怎么表示

</br>

**方法一：字节码表示**

最简单的方法，就是直接使用字节数组表示。26 个英文字母，我们至少需要一个 5 位长度的数组，从  到 25 分别表示 A 到 Z

比如 00000 表示 A，00001 表示 B，...，11001 表示 Z

这种方式实现起来最为简单，但是会造成一些资源浪费，而且所有的字母都需要读取 5 位字节，速度也会比较慢

</br>

**方法二：二叉树表示**

![image-20210124190212712](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210124190212712.png)

如图所示，我们可以使用二叉树的叶子节点来表示英文字母，00 表示 A，10 表示 B，我们根据传过来的编码去读取二叉树的节点，当一个节点是叶子叶子节点的时候，读取该叶子节点表示的英文字母就可以了

这样，我们就需要一个 14 层的二叉树来表示 25 个英文字母，最短的是 A 和 B，而最长的是 Z

可是在实际的英文会话中，使用最多的是英文字母 E，使用普通的二叉树表示可以让不同的英文字母的字节码长度不同，却无法让使用最多的英文字母用最短的长度来表示

</br>

**方法三：哈夫曼树**

假设在一个文本中只出现了 ABCDEFGH 8个字母，并且每个字母出现的频数如下

![image-20210124200018018](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210124200018018.png)

我们在二叉树中，根据各个字母的出现频数排列该字母在二叉树中的位置

![image-20210124204148246](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210124204148246.png)

可以看到，使用最多的字母 C，它的字节码长度只有 2 位（01），而使用最少的字母 H 则在最后，它的字节码长度有 4 位（1111）

而这种根据权值安排节点路径，从而使树的带权路径最短的二叉树，则被称为哈夫曼树，也叫最优二叉树



------

#### 与哈夫曼树有关的概念



* **路径**

  树的一个节点到另一个节点之间的分支构成这两个节点之间的路径

* **路径长度**

  路径上的分支数目称为长度

* **树的路径长度**

  从树根到另一个节点的路径长度之和

* **权值**

  权值就是定义的路径上面的值，可以理解为字符对应二进制编码出现的概率，权值越大表明出现的概率越大

* **节点的带权路径长度**

  在树中，如果其节点上附带一个权值，通常把该节点的路径长度与该节点上的权值之积称为该节点的带权路径长度

* **树的带权路径长度**

  如果树的每一个叶子上都带有一个权值，则把树中所有叶子的带权路径长度之和称为树的带权路径长度



一般来说，用 n(n>0) 个带权值的叶子来构造二叉树，限定二叉树中除了这 n 个叶子外只能出现度为 2 的节点，那么符合这样条件的二叉树往往可构造出许多棵，其中带权路径长度最小的二叉树就称为哈夫曼树或者最优二叉树



------

#### 哈夫曼算法



根据哈夫曼树的定义，一棵二叉树要使其带权路径长度最小，必须使权值越大的叶子节点越靠近根节点，而权值越小的叶子节点越原理根节点



哈夫曼依据这一特点提出了一种构造最优二叉树的方法，其基本思想如下：

1. 根据给定的 n 个权值{w1,w2,...,wn}，构造 n 棵只有根节点的二叉树，令起权值为 wj
2. 在森林中选取两棵根节点权值最小的树为左右子树，构造一棵新的二叉树，置新二叉树根节点权值为其左右子树根节点权值之和
3. 在森林中删除这两棵树，同时将新得到的二叉树加入森林中
4. 重复上述两步，知道只含一棵树为止，这棵树即哈夫曼树



![image-20210127235520764](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210127235520764.png)





------

#### 哈夫曼树的java实现



**Node 节点**

相比于普通的二叉树节点，哈夫曼树的结点会多一个域来存储该结点的权值

```java
public class Node {
	private Object data;
    private Node leftChild;
    private Node rightChild;
    private double weight;
}
```



**构造哈夫曼树**

```java
public Node createTree(List<Node> nodes) {
    // 只要nodes数组中还有两个以上结点
    while (nodes.size() > 1) {
        // 根据权值大小从大到小排序
        sort(nodes);
        // 获取权值最小的两个结点
        Node left = nodes.get(nodes.size() - 1);
        Node right = nodes.get(nodes.size() - 2);
        
        // 生成新结点，新结点的权值为两个子结点的权值之和
        Node parent = new Node(null, left.weight, right.weight);
        
        // 让新结点作为两个权值最小结点的父结点
        parent.leftChild = left;
        parent.rightChild = right;
        // 删除权值最小的两个结点
        nodes.remove(nodes.size() - 1);
        nodes.remove(nodes.size() - 1);
        // 将新结点加入到集合中
        nodes.add(parent);
    }
    return nodes.get(0);
}

private void sort(List<Node> nodes) {
    subSort(nodes, 0, nodes.size() - 1);
}

/**
 * 快速排序
 **/
private void subSort(List<Node> nodes, int start, int end) {
    if (start < end) {
        // 以第一个元素作为分界值
        Node base = nodes.get(start);
        // i 从左边搜索，搜索大于分界值的元素的索引
        int i = start;
        // j 从右边搜索，搜索小于分界值的元素的索引
        int j = end;
        while (true) {
            // 找到大于分界值的元素的索引，或者 i 已经到了end 处
            while (i < end && nodes.get(++i).weight >= base.weight) {}
            // 找到小于分界值的元素的索引，或者 j 已经到了 start 处
            while (j > start && nodes.get(--j).weight <= base.weight) {}
            if (i < j) {
                swap(nodes, i, j);
            } else {
                break;
            }
        }
        swap(nodes, start, j);
        // 递归左边子序列
        subSort(nodes, start, j - 1);
        // 递归右边子序列
        subStart(nodes, i + 1, end)
    }
}


```



------

### 二叉查找树（二叉搜索树、二叉排序树）

#### 什么是二叉查找树



在二叉查找树中，根节点的值大于其左子树中任意一个节点的值，小于其右节点中任意一节点的值，这一规则适用于二叉查找树中的每一个节点



二叉查找树，也叫二叉排序树是满足以下条件的二叉树：

1. 左子树上的所有节点值均小于根节点值
2. 右子树上的所有节点值均不小于根节点值
3. 左右子树也满足上述两个条件



------

#### 二叉查找树结点的插入



二叉查找树的插入过程如下：

1. 若当前的二叉查找树为空，则插入的元素为根节点
2. 若插入的元素值小于根节点值，则将元素插入到左子树中
3. 若插入的元素值不小于根节点值，则将元素插入到右子树中



```java
public void insert(Node node) {
    if (root == null) {
        // 树为空，则把第一个结点置为根节点
        root = node;
    } else {
        Node parent = root;
        while (true) {
            if (node.data < parent.data) {
                if (parent.leftChild == null) {
                    parent.leftChild = node;
                    return;
                } else {
                    parent = parent.leftChild;
                }
            } else {
                if (parent.rightChild == null) {
                    parent.rightChild = node;
                    return;
                } else {
                    parent = parent.rightChild;
                }
            }
        }
    }
}
```



------

#### 二叉查找树结点的删除

在删除节点的时候我们只需考虑一下三种情况：

1. 要删除的结点是叶子结点，如图：

   ![image-20210310235256739](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210310235256739.png)

   这种情况直接删除叶子结点即可

   

2. 要删除的结点有左结点但是没有右结点，或者有右结点但是没有左结点，如图：

   ![image-20210310235337053](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210310235337053.png)

   这种情况将被删除结点的子结点的值赋给被删除结点，然后删掉被删除结点的子结点

   

3. 删除的节点既有左节点又有右节点，在这种情况下，我们只需要将找到待删节点的右子树中值最小的节点（或者左子树中值最大的结点），将其删除并且获取其值，并用其值替换待删节点的值即可

   ![image-20210310235636379](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210310235636379.png)



```java
public void delete (Node node) {
	if (node.leftChild == null && node.rightChild == null) {
        // 第一种情况
        node = null;
    } else if (node.leftChild != null && node.rightChild != null) {
        // 第三种情况
        Node rightLeaf = node.rightChild;
        while (rightLeaf.leftChild != null) {
            rightLeaf = rightChild.leftChild;
        }
        node.data = rightLeaf.data;
        if (rightLeaf.rightChild != null) {
            if (rightLeaf.parent == node) {
                node.rightChild == rightLeaf.rightChild;
            } else {
                rightLeaf.parent.leftChild = rightLeaf.rightChild;
            }
        }
        rightLeaf == null;
    } else {
        // 第二种情况
        if (node.leftChild != null) {
            node.data = node.leftChild.data;
            node.leftChild = null;
        } else {
            node.data = node.rightChild.data;
            node.rightChild = null;
        }
    }
}
```



------

### 平衡二叉树（AVL树）

#### 什么是平衡二叉树



二叉搜索树一定程度上可以提高搜索效率，但当出入的结点为有序数列时，二叉树就会退化成链表，如图

![image-20210314230400388](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210314230400388.png)

在数据结构中，将左右子树高度差不超过一的树称为平衡树，而 AVL 树就是始终保持平衡状态的二叉搜索树，也叫平衡二叉树



AVL树是最早被发明的自平衡二叉查找树。在AVL树中，任一节点对应的两棵子树的最大高度差为1，因此它也被称为高度平衡树



------

#### 平衡二叉树的性质



1. 可以是空树
2. 假如不是空树，任何一个结点的左子树与右子树都是平衡二叉树，并且高度之差的绝对值不超过 1



------

#### 平衡因子



某节点的左子树与右子树的高度(深度)差即为该节点的平衡因子，平衡二叉树中不存在平衡因子大于 1 的节点。在一棵平衡二叉树中，节点的平衡因子只能取 0 、1 或者 -1 ，分别对应着左右子树等高，左子树比较高，右子树比较高





![image-20210325215503400](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210325215503400.png)

![image-20210325215512109](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210325215512109.png)

![image-20210325215522378](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210325215522378.png)

------

#### 平衡二叉树的结点构造



```java
public class Node {
    // 深度
    private int depth;
    // 夫结点
    private Node parent;
    // 左右孩子结点
    private Node leftChild;
    private Node rightChild;
    // 结点数据
    private Object data;
}
```



------

#### 平衡二叉树插入时的失衡与调整



这是一棵平衡二叉树

![image-20210405194558019](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210405194558019.png)

在这棵树中插入结点 99，树的结构变成：

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/v2-2ddb0f9b832fff594e294dffc299b373_b.gif)



最小失衡子树：

在新插入的结点向上查找，以第一个平衡因子的绝对值超过 1 的结点为根的子树称为最小不平衡子树。也就是说，一棵失衡的树，是有可能有多棵子树同时失衡的。而这个时候，我们只需要调整最小的不平衡子树，就能够将不平衡的树调整为平衡的树



平衡二叉树的失衡调整主要是通过旋转最小失衡子树来实现的。根据旋转的方向有两种处理方式，左旋与右旋。旋转的目的就是减少高度，通过降低整棵树的高度来平衡。哪边的树高，就把那边的树向上旋转



------

#### 左旋和右旋



以下两张图分别为左旋和右旋的动图，便于理解和记忆：

![左旋](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/20180829143451434.gif)

![右旋](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/20180829143509110.gif)



左旋就是将节点的右支往左拉，右子节点变成父节点，并把晋升之后多余的左子节点出让给降级节点的右子节点；

而右旋就是反过来，将节点的左支往右拉，左子节点变成了父节点，并把晋升之后多余的右子节点出让给降级节点的左子节点

即左旋就是往左变换，右旋就是往右变换。不管是左旋还是右旋，旋转的目的都是将节点多的一支出让节点给另一个节点少的一支



------

#### 左旋



![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/v2-0a757b848f85f3531b0789fd3d79f0b3_720w.jpg)

如上图所示，加入新节点 99 后， 节点 66 的左子树高度为 1，右子树高度为 3，此时平衡因子为 -2。为保证树的平衡，此时需要对节点 66 做出旋转，因为右子树高度高于左子树，对节点进行左旋操作，流程如下：

1. 节点的右孩子替代此节点位置
2. 右孩子的左子树变为该节点的右子树
3. 节点本身变为右孩子的左子树

动图如下：

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/v2-0a757b848f85f3531b0789fd3d79f0b3_720w.jpg)

------

#### 右旋



右旋操作与左旋类似，操作流程为：

1. 节点的左孩子代表此节点
2. 节点的左孩子的右子树变为节点的左子树
3. 将此节点作为左孩子节点的右子树

动图如下：

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/v2-05246384c1c16537ca6176983bdb2627_b.gif)

------

#### AVL 树插入结点的方式



| 插入方式 |                      描述                       |   旋转方式   |
| :------: | :---------------------------------------------: | :----------: |
|    LL    | 在 A 的左子树根节点的左子树上插入节点而破坏平衡 |    右旋转    |
|    RR    | 在 A 的右子树根节点的右子树上插入节点而破坏平衡 |    左旋转    |
|    LR    | 在 A 的左子树根节点的右子树上插入节点而破坏平衡 | 先左旋后右旋 |
|    RL    | 在 A 的右子树根节点的左子树上插入节点而破坏平衡 | 先右旋后左旋 |



------

#### LL(A的左孩子的左子树插入结点) 代码实现

只需要执行一次右旋即可

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/v2-373766641d1c03a78f3d7eac803d1f57_b.gif)



```java
/**
* LL型 右旋转
* 结点如上图对应
**/
private Node LL_rotate(Node A) {
    // 获取结点的左子树
    Node B = A.leftChild;
    // 将左子树的右子树变为结点的左子树
    A.leftChild = B.rightChild;
    // 将结点变为左孩子子树的右子树
    B.rightChild = A;
    // 更新AB结点的深度
    A.depth = Math.max(height(A.leftChild), height(A.rightChild)) + 1;
    B.depth = Math.max(height(B.leftChild), height(A)) + 1;
    
    return B;
}
```



------

#### RR(A的右孩子的右子树插入结点) 代码实现

只需要执行一次左旋即可

![img](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/v2-e7044e4965ba640ee9ef35beac407cdc_b.gif)

```java
/**
* RR型 左旋转
* 结点如上图对应
**/
private Node RR_rotate(Node A) {
    // 获取结点的右子树
    Node C = A.rightChild;
    // 将右子树的左子树变为结点的右子树
    A.rightChild = C.leftChild;
    // 将结点变为左孩子子树的右子树
    C.leftChild = A;
    // 更新AB结点的深度
    A.depth = Math.max(height(A.leftChild), height(A.rightChild)) + 1;
    C.depth = Math.max(height(A), height(C.rightChild)) + 1;
    
    return C;
}
```



------

#### LR(A的左孩子的右子树插入结点)代码实现



若 A 的左孩子节点 B 的右子树 E 插入节点 F ，导致节点 A 失衡，如图：

![image-20210410204030943](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210410204030943.png)

旋转流程如下：

![image-20210410204728820](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210410204728820.png)

![image-20210410204740726](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210410204740726.png)

也就是说，经过这两步操作，使得原来根节点的左孩子的右孩子 E 节点成为了新的根节点



```java
/**
* LR型 右旋转之后再左旋
* 结点如上图对应
**/
private Node LR_rotate(Node A) {
    Node E = RR_rotate(A.leftChild);
    
    return LL_rotate(E);
}
```



------

#### RL(A的右孩子的左子树插入结点)代码实现



右孩子插入左节点的过程与左孩子插入右节点过程类似，也是需要执行两步操作，使得旋转之后为原来根结点的右孩子的左孩子作为新的根节点

![image-20210411170748939](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210411170748939.png)

![image-20210411170846005](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210411170846005.png)

![image-20210411170856146](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210411170856146.png)

```java
/**
* RL型 左旋转之后再右旋
* 结点如上图对应
**/
private Node RL_rotate(Node A) {
    Node D = LL_rotate(A.leftChild);
    
    return RR_rotate(D);
}
```



在所有的不平衡情况中，都是按照先寻找最小不平衡树，然后 寻找所属的不平衡类别，再 根据 4 种类别进行固定化程序的操作



------

#### AVL树的四种删除节点方式



AVL 树和二叉查找树的删除操作情况一致，都分为四种情况：

1. 删除叶子节点
2. 删除的节点只有左子树
3. 删除的节点只有右子树
4. 删除的节点既有左子树又有右子树

只不过 AVL 树在删除节点后需要重新检查平衡性并修正，同时，删除操作与插入操作后的平衡修正区别在于，插入操作后只需要对插入栈中的弹出的第一个非平衡节点进行修正，而删除操作需要修正栈中的所有非平衡节点



删除操作的大致步骤如下：

* 以前三种情况为基础尝试删除节点，并将访问节点入栈
* 如果尝试删除成功，则依次检查栈顶节点的平衡状态，遇到非平衡节点，即进行旋转平衡，直到栈空
* 如果尝试删除失败，证明是第四种情况。这时先找到被删除节点的右子树最小节点并删除它，将访问节点继续入栈
* 再依次检查栈顶节点的平衡状态和修正直到栈空



对于删除操作造成的非平衡状态的修正，可以这样理解：对左或者右子树的删除操作相当于对右或者左子树的插入操作，然后再对应上插入的四种情况选择相应的旋转就好了



------

### 红黑树

#### 什么是红黑树



红黑树也是一种自平衡二叉树，而它的结点会有红和黑两种颜色，如图，就是一棵典型的红黑树

![image-20210411191110563](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210411191110563.png)



红黑树具备以下性质：

1. 节点是红色或黑色
2. 根节点是黑色
3. 每个叶子节点都是黑色的空节点（空结点）
4. 每个红色节点的两个子节点都是黑色。(从每个叶子到根的所有路径上不能有两个连续的红色节点)
5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点



由红黑树的性质可知，红黑树并不追求绝对的平衡而只追求黑色结点的平衡，目的是通过用非严格的平衡来减少增删结点时旋转的次数，而与 AVL 树相比，红黑树调整树的平衡除了旋转之外，还多了“变色”这么一个步骤



------

#### 红黑树结点的插入



以下是我们在操作红黑树时的约定叫法：

![image-20210411195729876](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210411195729876.png)



以下是关于红黑树结点插入时的归纳整理：

![image-20210411233823776](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210411233823776.png)

情景 1、2、3 都是比较好处理的，而情景 4 中，4.2 和 4.3 又是相反的情况，懂得一个就可以推出另一个



代码实现

```java
/**
*  插入结点  node为插入的结点，root最开始为根节点
**/
public void insert(Node node, Node root) {
    // 情景4.1，如果根节点为空，则将插入的结点变成黑色即可
    if (root == NIL) {
        node.color = Color.Black;
        this.root = node;
        return;
    }
    // 记录插入结点的父结点
    Node parent = root;
    while (node.value != root.value) {
        // 更新parent
        parent = root;
        if (node.value > root.value) {
            // 插入右子树
            root = root.right;
            // 到达NIL结点，当作右儿子插入
            if (root == NIL) {
                node.parent = parent;
                parent.right = node;
                break;
            }
        } else {
            // 插入到左子树
            root = root.left;
            // 到达NIL结点，当作右儿子插入
            if (root == NIL) {
                node.parent = parent;
                parent.left = node;
                break;
            }
        }
    }
    // 结点插入完成，进行树的修复工作
    insertFixUp(node);
}

/**
* Node结点插入之后的修复工作
**/
private void insertFixUp(Node node) {
    // 添加修复操作不会超过两次
    // node结点经过前一次处理后上升到根节点，颜色为红色，染成黑色，更新根节点指针
    if (node.parent == null) {
        node.color = Color.Black;
        this.root = node;
        return;
    }
    // node结点的父结点为黑色，无需调整
    if (node.parent.color = Color.Black) {
        return;
    }
    // 得到node结点的父结点、祖父结点和叔叔结点
    Node parent = node.parent;
    Node grandParent = node.parent.parent;
    Node uncle = getUncle(node);
    if (uncle.color = Color.Red) {
        // 情景4.1，node 结点的父结点和叔叔结点为红色，祖父结点为黑色
        // 将父结点和叔叔结点染黑，祖父结点染成红，此时的祖父结点可能与其父结点颜色冲突，递归对其进行修复处理
        parent.color = Color.Black;
        uncle.color = Color.Black;
        garentParent.color = Color.Red;
        // 递归修复祖父结点
        insertFixUp(garentParent);
    } else if (node == parent.leftChild && parent == garentParent.leftChild) {
        // 情景4.2.1
        // 将父结点染黑，祖父结点染红，右旋转
        parent.color = Color.Black;
        grandParent.color = Color.Red;
        LL_rotate(grandParent);
    } else if (node == parent.leftChild && parent == grantParent.rightChild) {
        // 情景4.2.2
        // 将父结点染黑，祖父结点染红，右旋转之后再左旋转
        parent.color = Color.Black;
        grandParent.color = Color.Red;
        RL_rotate(grandParent);
    } else if (node == parent.rightChild && parent == grantParent.rightChild) {
        // 情景4.3.1
        // 将父结点染黑，祖父结点染红，左旋转
        parent.color = Color.Black;
        grandParent.color = Color.Red;
        RR_rotate(grandParent);
    } else  {
        // 情景4.3.2
        // 将父结点染黑，祖父结点染红，左旋转之后再右旋转
        parent.color = Color.Black;
        grandParent.color = Color.Red;
        LR_rotate(grandParent);
    }
}
```



------

#### 红黑树结点的删除



二叉树删除结点有 3 种情景：

1. 若删除结点无子结点，直接删除
2. 若删除结点只有一个子结点，用子结点替换删除结点
3. 若删除结点有两个子结点，用后继结点（大于删除结点的最小结点）替换删除结点

所谓大于删除结点的最小结点，即被删除结点的右子树的最左结点



有一个查询二叉树前驱和后继结点最直观的方式，即把二叉树所有结点投射在X轴上，所有结点都是从左到右排好序的，所有目标结点的前后结点就是对应前继和后继结点

![image-20210415201950282](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210415201950282.png)



红黑树删除结点最重要的思路：删除结点被替代后，在不考虑结点的键值的情况下，对于树来说，可以认为删除的是替代结点。

如图：

![image-20210415221027607](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210415221027607.png)

基于此，上面所说的 3 种二叉树的删除情景可以相互转换并且最终都是转换为情景 1

* 情景2：删除结点用其唯一的子结点替换，子结点替换为删除结点后，可以认为删除的是子结点，若子结点又有两个子结点，那么相当于转换为情景3，一直自顶向下转换，总是能转换为情景1。（对于红黑树来说，根据性质 5.1，只存在一个子结点的结点肯定在树末了）
* 情景3：删除结点用后继结点（肯定不存在左结点），如果后继结点有右子结点，那么相当于转换为情景 2，否则转为为情景 1

综上所述，删除操作删除的结点可以看作删除替代结点，而替代结点最后总是在树末



![image-20210615224601746](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210615224601746.png)

#### 红黑树与AVL树的比较



1. 红黑树不追求"完全平衡"，即不像AVL那样要求节点的 `|balFact| <= 1`，它只要求部分达到平衡，但是提出了为节点增加颜色，红黑是用非严格的平衡来换取增删节点时候旋转次数的降低，任何不平衡都会在三次旋转之内解决，而AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多

2. 就插入节点导致树失衡的情况，AVL 树和红黑树都是最多两次树旋转来实现复衡，旋转的量级是O(1)

   删除节点导致失衡，AVL 需要维护从被删除节点到根节点 root 这条路径上所有节点的平衡，旋转的量级为O(logN)，而红黑树最多只需要旋转3次实现复衡，只需O(1)，所以说红黑树删除节点的复衡的效率更高，开销更小

3. AVL 树的结构相较于红黑树更为平衡，插入和删除引起失衡，如2所述，红黑树复衡效率更高；当然，由于AVL高度平衡，因此AVL的Search效率更高

因此，实际应用中，若搜索的次数远远大于插入和删除，那么选择AVL，如果搜索，插入删除次数几乎差不多，应该选择红黑树



------

### B树

#### 为什么需要 B 树



从算法逻辑上来讲，AVL 树的查找速度和比较次数都是最小的，但是，我们不得不考虑一个现实问题：磁盘IO。例如数据库索引，当数据量比较大时，索引的大小可能有几个 G 甚至更多。因此我们利用索引进行查询时，显然不能直接把索引全部加载到内存中，只能逐一加载每一个磁盘页查询对应的索引结点



磁盘 IO 的速度远小于内存的处理速度，因此我们需要尽量减少磁盘 IO 的次数，这就需要我们将原本“瘦高”的二叉树结构变得“矮胖”



------

#### B 树的特征



一个 m 阶（一个结点存储元素的数量称为阶）的 B 树具有如下几个特征：

1. 根结点至少有两个子女
2. 每个中间节点都包含 k - 1 个元素和 k 个孩子，其中 m/2 <= k <= m
3. 每一个叶子节点都包含 k - 1个元素，其中 m/2 <= k <= m
4. 所有的叶子结点都位于同一层
5. 每个节点中的元素从小到大排列，节点当中 k - 1个元素正好是k个孩子包含的元素的值域分划



------

#### B 树结点的插入



若结点的关键字个数小于 m - 1，直接在最底层插入

![image-20210617224251305](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210617224251305.png)

若结点的关键字个数等于 m - 1，此种情况m-1+1=m溢出，把出问题的那一分支的中间结点插入到父结点中，如果父结点也溢出，递归解决，显然树高可能因此增加一层

![image-20210617224431851](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210617224431851.png)



------

#### B 树结点的删除



如图，删除 B 树中的结点 11，首先通过向下搜索找到该结点

![image-20210619192010420](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210619192010420.png)

删除11后，节点12只有一个孩子，不符合B树规范。因此找出12,13,15三个节点的中位数13，取代节点12，而节点12自身下移成为第一个孩子。（即左旋）

![image-20210619192048164](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210619192048164.png)



实际上 B 树结点的删除要比这复杂的多，需要跟红黑树一样区分删除结点的类型，但是对于 B 树和 B+ 树，更多的是对它的了解，了解索引的工作原理即可



------

### B+树

#### B 树和 B+ 树的区别



B+Tree是在B-Tree基础上的一种优化，使其更适合实现外存储索引结构。B+Tree与B-Tree的结构很像，但是也有几个自己的特性：

1. 所有的非叶子节点只存储关键字信息
2. 所有具体数据都存在叶子结点中
3. 所有的叶子结点中包含了全部元素的信息
4. 所有叶子节点之间都有一个链指针



B 树：

![image-20210619201702331](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210619201702331.png)



B+ 树：

![image-20210619201735541](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210619201735541.png)



------

## 图

### 什么是图

#### 什么是图



数据之间的关系有 3 种，分别是 "一对一"、"一对多" 和 "多对多"，前两种关系的数据可分别用线性表和树结构存储，而 "多对多" 的关系就需要用图来表示了，以下就是图的结构示意图：

![image-20210621001000736](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210621001000736.png)



------

#### 图的术语



邻接：如果两个顶点被同一条边连接，就称这两个顶点是邻接的

路径：两个顶点之间边的序列叫做路径,可能会有多种路径

连通图：如果至少有一条路径可以连接所有的顶点，那么这个图被称作连通的

有向图：如果图的边只能向一个方向移动，那么这个图被称作有向图

带权图：如果图的边被赋予了权值，那么这个图被称作有向图

入度：有向图的某个顶点作为终点的次数和

出度：有向图的某个顶点作为起点的次数和



------

#### 生成树和生成森林



对连通图进行遍历，过程中所经过的边和顶点的组合可以看做是一棵普通树，通常称为生成树

![image-20210626191303608](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210626191303608.png)

连通图中，由于任意两顶点之间可能含有多条通路，遍历连通图的方式有多种，往往一张连通图可能有多种不同的生成树与之对应



连通图的生成树必须满足以下两个条件：

1. 包含连通图中所有的顶点
2. 任意两顶点之间有且仅有一条通路

因此，连通图的生成树具有这样的特征，即生成树中`边的数量 = 顶点数 - 1`



生成树是对应连通图来说，而生成森林是对应非连通图来说的

非连通图可分解为多个连通分量，而每个连通分量又各自对应多个生成树（至少是 1 棵），因此与整个非连通图相对应的，是由多棵生成树组成的生成森林

![image-20210626220954153](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210626220954153.png)

这是一张非连通图，可分解为 3 个连通分量，其中各个连通分量对应的生成树如图 3所示：

![image-20210626221017981](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210626221017981.png)

多个连通分量对应的多棵生成树就构成了整个非连通图的生成森林



------

### 图的存储结构

#### 图的存储结构之邻接表



通常，图更多的是采用链表存储，具体的存储方法有 3 种，分别是邻接表、邻接矩阵和十字链表，首先看一下图的邻接表存储法。邻接表既适用于存储无向图，也适用于存储有向图



 在具体讲解邻接表存储图的实现方法之前，先普及一个"邻接点"的概念。在图中，如果两个点相互连通，即通过其中一个顶点，可直接找到另一个顶点，则称它们互为邻接点

邻接指的是图中顶点之间有边或者弧的存在

邻接表存储图的实现方式是，给图中的各个顶点独自建立一个链表，用节点存储该顶点，用链表中其他节点存储各自的临界点

与此同时，为了便于管理这些链表，通常会将所有链表的头节点存储到数组中（也可以用链表存储）。也正因为各个链表的头节点存储的是各个顶点，因此各链表在存储临界点数据时，仅需存储该邻接顶点位于数组中的位置下标即可



![image-20210627174519073](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210627174519073.png)

邻接表存储方式和 HashMap 类似，都是数组加链表的结构。拿顶点 V1 来说，与其相关的邻接点分别为 V2 和 V3，因此存储 V1 的链表中存储的是 V2 和 V3 在数组中的位置下标 1 和 2

因此，存储各顶点的节点结构分为两部分，数据域和指针域，如果是带权图的话，则还需要添加一个域来存储权值



------

#### 图的存储结构之邻接矩阵



图的邻接矩阵存储方式是用两个数组来表示图。一个一维数组存储图中顶点信息，一个二维数组（邻接矩阵）存储图中的边或弧的信息

设图G有n个顶点，则邻接矩阵是一个 n*n 的方阵，定义为：

![image-20210719223523430](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719223523430.png)

 看一个实例，下图左就是一个无向图

![image-20210719223544095](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719223544095.png)

从上面可以看出，无向图的边数组是一个对称矩阵。所谓对称矩阵就是n阶矩阵的元满足 aij = aji。即从矩阵的左上角到右下角的主对角线为轴，右上角的元和左下角相对应的元全都是相等的



从这个矩阵中，很容易知道图中的信息

1. 要判断任意两顶点是否有边无边就很容易了
2. 要知道某个顶点的度，其实就是这个顶点 vi 在邻接矩阵中第i行或（第i列）的元素之和
3. 求顶点vi的所有邻接点就是将矩阵中第i行元素扫描一遍，`arc[i][j]`为1就是邻接点

而有向图讲究入度和出度，顶点vi的入度为1，正好是第i列各数之和。顶点vi的出度为2，即第i行的各数之和



若图G是网图，有n个顶点，则邻接矩阵是一个n*n的方阵，定义为：

![image-20210719223830280](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719223830280.png)

这里的 wij 表示 (vi,vj) 上的权值。无穷大表示一个计算机允许的、大于所有边上权值的值，也就是一个不可能的极限值。下面左图就是一个有向网图，右图就是它的邻接矩阵

![image-20210719223957763](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719223957763.png)



------

#### 图的存储结构之十字链表



对于有向图来说，邻接表是有缺陷的。关心了出度问题，想了解入度就必须要遍历整个图才知道，反之，逆邻接表解决了入度却不了解出度情况。下面介绍的这种有向图的存储方法：十字链表，就是把邻接表和逆邻接表结合起来的



十字链表存储有向图（网）的方式与邻接表有一些相同，都以图（网）中各顶点为首元节点建立多条链表，同时为了便于管理，还将所有链表的首元节点存储到同一数组（或链表）中

其中，建立个各个链表中用于存储顶点的首元节点结构如图 1 所示：

![image-20210719234505866](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719234505866.png)

从图可以看出，首元节点中有一个数据域和两个指针域（分别用 firstin 和 firstout 表示）：

* firstin 指针用于连接以当前顶点为弧头的其他顶点构成的链表
* firstin 指针用于连接以当前顶点为弧头的其他顶点构成的链表
* data 用于存储该顶点中的数据

由此可以看出，十字链表实质上就是为每个顶点建立两个链表，分别存储以该顶点为弧头的所有顶点和以该顶点为弧尾的所有顶点



注意，存储图的十字链表中，各链表中首元节点与其他节点的结构并不相同，图 1 所示仅是十字链表中首元节点的结构，链表中其他普通节点的结构如图所示：

![image-20210719234705295](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719234705295.png)

![image-20210719234715239](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719234715239.png)

* tailvex 用于存储以首元节点为弧尾的顶点位于数组中的位置下标
* headvex 用于存储以首元节点为弧头的顶点位于数组中的位置下标
* hlink 指针：用于链接下一个存储以首元节点为弧头的顶点的节点
* tlink 指针：用于链接下一个存储以首元节点为弧尾的顶点的节点
* tlink 指针：用于链接下一个存储以首元节点为弧尾的顶点的节点



比如说，用十字链表存储图 3a) 中的有向图，存储状态如图所示：

![image-20210719235138640](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210719235138640.png)

拿图 3 中的顶点 V1 来说，通过构建好的十字链表得知，以该顶点为弧头的顶点只有存储在数组中第 3 位置的 V4（因此该顶点的入度为 1），而以该顶点为弧尾的顶点有两个，分别为存储数组第 1 位置的 V2 和第 2 位置的 V3（因此该顶点的出度为 2）

对于图各个链表中节点来说，由于表示的都是该顶点的出度或者入度，因此没有先后次序之分



------

### 图的遍历

#### 图的遍历之深度优先搜索



![image-20210720230148185](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210720230148185.png)

深度优先搜索的过程类似于树的先序遍历，首先从例子中体会深度优先搜索。例如上图是一个无向图，采用深度优先算法遍历这个图的过程为：

1. 首先任意找一个未被遍历过的顶点，例如从 V1 开始，由于 V1 率先访问过了，所以，需要标记 V1 的状态为访问过
2. 然后遍历 V1 的邻接点，例如访问 V2 ，并做标记，然后访问 V2 的邻接点，例如 V4 （做标记），然后 V8 ，然后 V5
3. 当继续遍历 V5 的邻接点时，根据之前做的标记显示，所有邻接点都被访问过了。此时，从 V5 回退到 V8 ，看 V8 是否有未被访问过的邻接点，如果没有，继续回退到 V4 ， V2 ， V1 
4. 通过查看 V1 ，找到一个未被访问过的顶点 V3 ，继续遍历，然后访问 V3 邻接点 V6 ，然后 V7
5. 由于 V7 没有未被访问的邻接点，所有回退到 V6 ，继续回退至 V3 ，最后到达 V1 ，发现没有未被访问的
6. 最后一步需要判断是否所有顶点都被访问，如果还有没被访问的，以未被访问的顶点为第一个顶点，继续依照上边的方式进行遍历



最后一步需要判断是否所有顶点都被访问，如果还有没被访问的，以未被访问的顶点为第一个顶点，继续依照上边的方式进行遍历：

V1 -> V2 -> V4 -> V8 -> V5 -> V3 -> V6 -> V7



所谓深度优先搜索，是从图中的一个顶点出发，每次遍历当前访问顶点的临界点，一直到访问的顶点没有未被访问过的临界点为止。然后采用依次回退的方式，查看来的路上每一个顶点是否有其它未被访问的临界点。访问完成后，判断图中的顶点是否已经全部遍历完成，如果没有，以未访问的顶点为起始点，重复上述过程

深度优先搜索是一个不断回溯的过程



------

#### 图的遍历之广度优先搜索



![image-20210720230910853](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210720230910853.png)

广度优先搜索类似于树的层次遍历。从图中的某一顶点出发，遍历每一个顶点时，依次遍历其所有的邻接点，然后再从这些邻接点出发，同样依次访问它们的邻接点。按照此过程，直到图中所有被访问过的顶点的邻接点都被访问到

最后还需要做的操作就是查看图中是否存在尚未被访问的顶点，若有，则以该顶点为起始点，重复上述遍历的过程



以上无向图为例，假设 V1 作为起始点，遍历其所有的邻接点 V2 和 V3 ，以 V2 为起始点，访问邻接点 V4 和 V5 ，以  V3 为起始点，访问邻接点 V6 、 V7 ，以 V4 为起始点访问 V8 ，以 V5 为起始点，由于 V5  所有的起始点已经全部被访问，所有直接略过， V6 和 V7 也是如此



以 V1 为起始点的遍历过程结束后，判断图中是否还有未被访问的点，由于图 1 中没有了，所以整个图遍历结束。遍历顶点的顺序为：

V1 -> V2 -> v3 -> V4 -> V5 -> V6 -> V7 -> V8



------

### 最小生成树算法

#### 最小生成树算法之普利姆算法



对于一个带权的无向连通图，其每个生成树所有边上的权值之和可能不同，我们把所有边上权值之和最小的生成树称为图的最小生成树
普里姆算法是以其中某一顶点为起点，逐步寻找各个顶点上最小权值的边来构建最小生成树
其中运用到了回溯，贪心的思想。





**算法思路**

设图G=(V,E),U是顶点集V的一个非空子集。假设(u,v)是一条具有最小权值的边。当中u∈U,v∈V-U,则必存在一棵包括边(u,v)的最小生成树

上述的性质能够通过反证法证明。假设(u,v)不包括在G的最小生成树T中。那么，T的路径中必定存在一条连通U和V-U的边，假设将这条边以(u,v)来替换，我们将获得一个权重更低的生成树，这与T是最小生成树矛盾.既然MST满足贪婪选择属性。那么。求解最小生成树的问题就简化了非常多

总结一下，详细的步骤大概例如以下：

1. 构建一棵空的最小生成树 U 和一个集合 V，集合 V 中包含着图中所有的点
2. 在 V 中随机选取一个点放入生成树 U 中，并将该点从集合 V 中删除，此时的集合为 V - T
3. 遍历集合 V，获得 V 中与生成树 U 中的点相连且权值最小的点，加入生成树 U 中，同时从 V 中删除该点
4. 重复 3 的操作，直至集合 V 为空



以上图为例，来对普利姆算法进行演示

![image-20210722002624890](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210722002624890.png)

第一步操作，随机选取一个点（此例从顶点A开始）

![image-20210722002805797](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210722002805797.png)

第二步，剩余集合中，只有B、G与集合 U 中的点相连，且 AB 间的权值最小，因此将 B 移入生成树 U 中

![image-20210722002938787](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210722002938787.png)

第三步，剩余集合中，有 C、F、G 与集合 U 中的点相连，且 BF 间权值最小，因此再将 F 点移入生成树 U 中

![image-20210722003104564](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210722003104564.png)

![image-20210722003114914](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210722003114914.png)

![image-20210722003128962](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210722003128962.png)

不断重复上述操作，直至剩余集合为空，此时的 U 即为该图的最小生成树



------

#### 最小生成树算法之迪杰斯特拉算法



**算法思路**

设G=(V,E)是一个带权有向图，把图中顶点集合V分成两组，第一组为已求出最短路径的顶点集合（用S表示，初始时S中只有一个源点，以后每求得一条最短路径 , 就将加入到集合S中，直到全部顶点都加入到S中，算法就结束了），第二组为其余未确定最短路径的顶点集合（用U表示），按最短路径长度的递增次序依次把第二组的顶点加入S中。在加入的过程中，总保持从源点v到S中各顶点的最短路径长度不大于从源点v到U中任何顶点的最短路径长度。此外，每个顶点对应一个距离，S中的顶点的距离就是从v到此顶点的最短路径长度，U中的顶点的距离，是从v到此顶点只包括S中的顶点为中间顶点的当前最短路径长度

总结一下，详细的步骤大概例如以下：

1. 初始时，S只包含起点s；U包含除s外的其他顶点，且U中顶点的距离为"起点s到该顶点的距离
2. 从U中选出"距离最短的顶点k"，并将顶点k加入到S中；同时，从U中移除顶点k
3. 更新U中各个顶点到起点s的距离。之所以更新U中顶点的距离，是由于上一步中确定了k是求出最短路径的顶点，从而可以利用k来更新其它顶点的距离
4. 重复步骤(2)和(3)，直到遍历完所有顶点



![image-20210724165755534](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724165755534.png)

以上图为例，来对迪杰斯特拉算法进行演示

![image-20210724171055451](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724171055451.png)

![image-20210724171306770](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724171306770.png)

![image-20210724171604106](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724171604106.png)

第一步，以点 1 为起始点，此时点 1 可以到达两个点，分别是点 2 和点 8，路径 1 - 2 长度为 4，路径 1 - 8 长度为 8 ，最短路径为 4，因此将点 1 - 2 连通

![image-20210724171848981](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724171848981.png)

![image-20210724172132803](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172132803.png)

第二步，连通了点 1 和点 2 之后，点 1 就又新增了两条可达路径，分别是 1 - 2 - 8，长度为 7，1 - 2 - 3，长度为 12；再未连通的点中， 1 - 2 - 8 的路径长度是最小的，因此将 1 - 2 - 8 相连

![image-20210724172217538](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172217538.png)

![image-20210724172357182](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172357182.png)

第三步，连通点 8 后，再次新增了两条路径，分别是 1 - 2 - 8 - 9，长度为 8；1 - 2 - 8 - 7，长度为 13，此时未连通的点中长度最短的为 1 -2 - 8 - 9，因此将 1 - 2 - 8 - 9 相连

![image-20210724172432871](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172432871.png)

![image-20210724172442401](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172442401.png)

![image-20210724172622655](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172622655.png)

![image-20210724172631930](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172631930.png)

![image-20210724172647733](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172647733.png)

![image-20210724172659724](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172659724.png)

![image-20210724172712219](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172712219.png)

![image-20210724172719635](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172719635.png)

![image-20210724172732265](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172732265.png)

![image-20210724172740814](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210724172740814.png)

最后，重复执行上述过程，直至将所有的点连通



------

#### 最小生成树算法之克鲁斯卡尔算法



**算法思路**

Prim算法是找点，Kruscal算法则是从边出发

1. 把所有的边和这条边代表的权值用一个数组存储起来，并按权值大小给数组排序（升序）
2. 按顺序从数组中拿出一条边，检查这条边是否与到目前为止形成的树构成环，如果形成了环，就丢弃它；如果没有，就把这条边加入树中
3. 重复步骤 2，直到树中有 *n-1* 条边为止



以下是步骤示意图

![image-20210725010226435](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725010226435.png)

第一步，在该图中，权值最小的边是 EF，因此连接 EF

![image-20210725010343373](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725010343373.png)

![image-20210725010355117](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725010355117.png)

第三步之后，权值最小的边是 CE，但是连接 CE 之后，CDE 之间就形成了一个闭环，因此跳过 CE 和 CF，连接 BF

![image-20210725010404816](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725010404816.png)

![image-20210725010557387](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725010557387.png)

![image-20210725010606982](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725010606982.png)

此时一共有 6 条边，正好等于 点的数量 - 1



在克鲁斯卡尔算法中，最复杂的地方在于如何判断加入一条边之后是否形成闭环

判断方式如下：

初次添加边之后，将其边两端的两个结点加入到一个集合中。此时再新加入一条边，如果该边的顶点与原有集合中的任一点可通，则加入的原有集合，否则的话创建一个新的集合。如果再次新加入一条边，边两端的顶点分别在两个集合中，则将这两个集合合并。如果一条边的两个顶点均在同一个集合中，则认为新加入的这条边形成了闭环

以下图为例：

![image-20210725011449311](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725011449311.png)

在上图中，存在这两个点的集合，分别是[1, 4] 和 [0, 2, 3, 5]，接下来权值最小的边是 5，假设连通点 0 - 3，点 0、3 均在同一个集合中，因此认为它形成了闭环

![image-20210725012225169](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725012225169.png)

点 2 - 3 也是如此，因此我们连通 1 - 2，点 1、2 分别在两个集合中，说明未形成闭环，所以连接点 1 - 2，而这两个点分别在两个集合中，所以将原有的点的集合合并成一个，集合为 [0, 1, 2, 3, 4, 5]，即为最终的结果

![image-20210725012446892](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210725012446892.png)



------

## 查找算法

### 查找算法简介

#### 什么是查找算法



查找是在大量的信息中寻找一个特定的信息元素，在计算机应用中，查找是常用的基本运算，例如编译程序中符号表的查找。本文简单概括性的介绍了常见的七种查找算法，说是七种，其实二分查找、插值查找以及斐波那契查找都可以归为一类——插值查找。插值查找和斐波那契查找是在二分查找的基础上的优化查找算法



查找算法的定义：根据给定的某个值，在查找表中确定一个其关键字等于给定值的数据元素（或记录）



------

#### 查找算法分类



1. 静态查找和动态查找

   静态或者动态都是针对查找表而言的。动态查找指查找表中有删除和插入操作的表

2. 无序查找和有序查找

   无序查找：被查找数列有序无序均可

   有序查找：被查找数列必须为有序数列



------

#### **平均查找长度**



平均查找长度（Average Search Length，ASL）：需和指定 key 进行比较的关键字的个数的期望值，称为查找算法在查找成功时的平均查找长度



对于含有n个数据元素的查找表，查找成功的平均查找长度为：ASL = Pi*Ci 的和。
Pi：查找表中第i个数据元素的概率。
Ci：找到第i个数据元素时已经比较过的次数



------

### 查找算法

#### 顺序查找



顺序查找是最简单的查找算法，按顺序遍历即可，顺序查找适合于存储结构为顺序存储或链接存储的线性表



顺序查找也称为线形查找，属于无序查找算法。从数据结构线形表的一端开始，顺序扫描，依次将扫描到的结点关键字与给定值k相比较，若相等则表示查找成功；若扫描结束仍没有找到关键字等于k的结点，表示查找失败



顺序查找的时间复杂度为O(n)



------

#### 二分查找



二分查找的元素必须是有序的，如果是无序的则要先进行排序操作



二分查找也称为是折半查找，属于有序查找算法。用给定值 k 先与中间结点的关键字比较，中间结点把线形表分成两个子表，若相等则查找成功；若不相等，再根据 k 与该中间结点关键字的比较结果确定下一步查找哪个子表，这样递归进行，直到查找到或查找结束发现表中没有这样的结点

折半查找的前提条件是需要有序表顺序存储，对于静态查找表，一次排序后不再变化，折半查找能得到不错的效率。但对于需要频繁执行插入或删除操作的数据集来说，维护有序的排序会带来不小的工作量，那就不建议使用



二分查找期望时间复杂度为O(log2^n)



------

#### 插值查找



在二分查找中，每次查找的关键字的位置都是傻瓜式的，比如在 1 ~ 1000 之间 100 个元素均匀分布的数组中查找值为 900 的元素，它也仍旧会按照 500、750、875 ... 的顺序慢慢查找，而并没有将每次查找的位置顺序向后偏移一点



二分查找中查找点计算如下：

mid = (low + high) / 2, 即 mid = low + 1/2 * (high-low)

通过类比，我们可以将查找的点改进为如下:

mid = low + (key-a[low]) / (a[high] - a[low]) * (high-low)

也就是将上述的比例参数1/2改进为自适应的，根据关键字在整个有序表中所处的位置，让mid值的变化更靠近关键字key，这样也就间接地减少了比较次数



对于表长较大，而关键字分布又比较均匀的查找表来说，插值查找算法的平均性能比折半查找要好的多。反之，数组中如果分布非常不均匀，那么插值查找未必是很合适的选择，插值查找期望时间复杂度也是 O(log2^n)



------

#### **斐波那契查找**



斐波那契查找也叫做黄金分割法查找，它也是根据折半查找算法来进行修改和改进的



对于斐波那契数列：1、1、2、3、5、8、13、21、34、55、89……（也可以从0开始），前后两个数字的比值随着数列的增加，越来越接近黄金比值0.618。比如这里的89，把它想象成整个有序表的元素个数，而89是由前面的两个斐波那契数34和55相加之后的和，也就是说把元素个数为89的有序表分成由前55个数据元素组成的前半段和由后34个数据元素组成的后半段，那么前半段元素个数和整个有序表长度的比值就接近黄金比值0.618，假如要查找的元素在前半段，那么继续按照斐波那契数列来看，55 = 34 + 21，所以继续把前半段分成前34个数据元素的前半段和后21个元素的后半段，继续查找，如此反复，直到查找成功或失败，这样就把斐波那契数列应用到查找算法中了

![image-20210726172312383](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210726172312383.png)

从图中可以看出，当有序表的元素个数不是斐波那契数列中的某个数字时，需要把有序表的元素个数长度补齐，让它成为斐波那契数列中的一个数值，当然把原有序表截断肯定是不可能的，不然还怎么查找。然后图中标识每次取斐波那契数列中的某个值时(F[k])，都会进行-1操作，这是因为有序表数组位序从0开始的，纯粹是为了迎合位序从0开始

斐波那契查找相比于二分查找，只是更新了分割的值而已，毕竟在计算下标时只涉及了加减法而没有除法，可以节省时间，但时间复杂度仍为 O(log2^n)，毕竟在代码的复杂程度上却大了不少



------

#### 树表查找



二叉查找树是先对待查找的数据进行生成树，确保树的左分支的值小于右分支的值，然后在就行和每个节点的父节点比较大小，查找最适合的范围。 这个算法的查找效率很高，但是如果使用这种查找方法要首先创建树



------

#### **分块查找**



分块查找又称索引顺序查找，它是顺序查找的一种改进方法



将n个数据元素"按块有序"划分为m块（m ≤ n）。每一块中的结点不必有序，但块与块之间必须"按块有序"；即第1块中任一元素的关键字都必须小于第2块中任一元素的关键字；而第2块中任一元素又都必须小于第3块中的任一元素，……



算法实现：

1. 先选取各块中的最大关键字构成一个索引表
2. 查找分两个部分：先对索引表进行二分查找或顺序查找，以确定待查记录在哪一块中；然后，在已确定的块中用顺序法进行查找



------

#### 哈希查找



即利用哈希算法来存储和查找元素，最典型的实现方式即哈希表，时间复杂度最理想情况可达到 O(1)



------

## 排序算法

### 算法简介

#### 排序算法分类



十种常见排序算法可以分为两大类：

* 比较类排序：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此也称为非线性时间比较类排序
* 非比较类排序：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序



![image-20210731184908024](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210731184908024.png)

------

#### 算法复杂度



![image-20210731184956353](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210731184956353.png)



------

#### **相关概念**



* 稳定：如果 a 原本在 b 前面，而 a=b，排序之后 a 仍然在 b 的前面
* 不稳定：如果 a 原本在 b 的前面，而 a=b，排序之后 a 可能会出现在 b 的后面



------

### 十大经典排序算法

#### 冒泡排序



冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端



算法步骤：

1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数
3. 针对所有的元素重复以上的步骤，除了最后一个
4. 重复步骤1~3，直到排序完成



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015223238449-2146169197.gif)



**代码实现**

```java
public List<Integer> bubbleSort(List<Integer> list) {
    int length = list.size();
    for(int i = 0; i < length - 1; i++) {
        for(int j = 0; j < length - i - 1; j++) {
            // 相邻元素比较大小
            if (list[j]  > list[j+1]) {
                // 元素位置交换
                Integer temp = list[j];
                list[j] = list[j+1];
                list[j+1] = temp;
            }
        }
    }
    return list;
}
```



------

#### 选择排序



选择排序(Selection-sort)是一种简单直观的排序算法。它的工作原理：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕



**算法步骤：**

1. 初始状态：无序区为R[1..n]，有序区为空
2. 第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中-选出关键字最小的记录 R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+1..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区
3. n-1趟结束，数组有序化了



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015224719590-1433219824.gif)



**代码实现**

```java
public List<Integer> selectionSort(List<Integer> list) {
    int length = list.size();
    int mixIndex = -1;
    Integer temp = -1;
    for(int i = 0; i > length -1; i++) {
        minIndex = i;
        for(int j = i + 1; j < length; j++) {
            if (list[j] < list[minIndex]) {
                minIndex = j;
            }
        }
        temp = list[i];
        list[i] = list[j];
        list[j] = temp;
    }
    return list;
}
```



选择排序是表现最稳定的排序算法之一，因为无论什么数据进去都是O(n2)的时间复杂度，所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。理论上讲，选择排序可能也是平时排序一般人想到的最多的排序方法了吧



------

#### 插入排序



插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入



**算法描述**

* 从第一个元素开始，该元素可以认为已经被排序
* 取出下一个元素，在已经排序的元素序列中从后向前扫描
* 如果该元素（已排序）大于新元素，将该元素移到下一位置
* 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置
* 将新元素插入到该位置后
* 重复步骤2~5



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015225645277-1151100000.gif)



**代码实现**

```java
public List<Integer> insertionSort(List<Integer> list) {
    int length = list.size();
    int preIndex = 0;
    int current = 0;
    for (int i = 1; i < length; i++) {
        preIndex = i - 1;
        current = list[i];
        while (preIndex >= 0 && list[preIndex] > current) {
            list[preIndex + 1] = list[preIndex];
            preIndex--;
        }
        list[preIndex + 1] = current;
    }
    return list;
}
```



------

#### 希尔排序



1959年Shell发明，第一个突破O(n^2)的排序算法，是简单插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫缩小增量排序



**算法描述**

先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，具体算法描述：

* 选择一个增量序列t1，t2，…，tk，其中ti>tj，tk=1
* 按增量序列个数 k，对序列进行 k 趟排序
* 每趟排序，根据对应的增量ti，将待排序列分割成若干长度为m 的子序列，分别对各子表进行直接插入排序。仅增量因子为1 时，整个序列作为一个表来处理，表长度即为整个序列的长度



**动图演示**

![img](https://images2018.cnblogs.com/blog/849589/201803/849589-20180331170017421-364506073.gif) 



**代码实现**

```java
public static void shellSort(int []arr){
    //增量gap，并逐步缩小增量
    for(int gap=arr.length/2; gap>0; gap/=2){
        //从第gap个元素，逐个对其所在组进行直接插入排序操作
        for(int i=gap;i<arr.length;i++){
            int j = i;
            int temp = arr[j];
            if(arr[j]<arr[j-gap]){
                while(j-gap>=0 && temp<arr[j-gap]){
                    //移动法
                    arr[j] = arr[j-gap];
                    j-=gap;
                }
                arr[j] = temp;
            }
        }
    }
}
```



------

#### 归并排序



归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and  Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为2-路归并



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015230557043-37375010.gif)



**代码实现**

```java
public int[] mergeSort(int[] list, int low, int high) {
    int mid = (low + high)/2;
    if （low < high） {
        mergeSort(list, low, mid);
        mergeSort(list, mid, high);
        // 左右归并
        merge(list, low, mid, high);
    }
    return list;
}

public void merge(int[] list, int low, int mid, int high) {
    int[] temp = new int[high - low + 1];
    int i = low;
    int j = mid + 1;
    int k = 0;
    // 把较小的数移入到新数组中
    while (i <= mid && j <= high) {
        if (a[i] < a[j]) {
            temp[k++] = a[i++];
        } else {
            temp[k++] = a[j++];
        }
    }
    // 把左边剩余的数移入数组 
    while(i<=mid){
        temp[k++] = a[i++];
    }
    // 把右边边剩余的数移入数组
    while(j<=high){
        temp[k++] = a[j++];
    }
    // 把新数组中的数覆盖nums数组
    for(int x=0;x<temp.length;x++){
        a[x+low] = temp[x];
    }
}
```



归并排序是一种稳定的排序方法。和选择排序一样，归并排序的性能不受输入数据的影响，但表现比选择排序好的多，因为始终都是O(nlogn）的时间复杂度。代价是需要额外的内存空间



------

#### 快速排序



快速排序的基本思想：通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序



**算法描述**

1. 在待排序的N个记录中任取一个元素(通常取第一个记录)作为基准，称为基准记录
2. 定义两个索引 left 和 right 分别表示“首索引” 和 “尾索引”，key  表示“基准值”
3. 首先，尾索引向前扫描，直到找到比基准值小的记录(left != righ)，并替换首索引对应的值
4. 然后，首索引向后扫描，直到找到比基准值大于的记录(left != righ)，并替换尾索引对应的值
5. 若在扫描过程中首索引等于尾索引(left = right),则一趟排序结束；将基准值(key)替换首索引所对应的值
6. 再进行下一趟排序时，待排序列被分成两个区:[0,left-1],[righ+1,end]
7. 对每一个分区重复步骤2~6，直到所有分区中的记录都有序，排序成功



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015230936371-1413523412.gif)



**代码实现**

```java
public void quickSort(int[] list, int low, int high) {
    int i, j, temp, t;
    if (low > high) {
        return;
    }
    i = low;
    j = high;
    // temp 就是基准位
    temp = list[low];
    
    while (i < j) {
        // 先从右边开始，依次往左边递减
        while (temp <= list[j] && i < j) {
            j--;
        }
        // 再从右边开始，依次往左边递减
        while (temp >= list[i] && i < j) {
            i++;
        }
        // 如果满足条件则交换
        if (i < j) {
            t = list[j];
            list[j] = list[i];
            list[i] = t;
        }
    }
    // 最后将基准位与i、j相等的数值位进行交换
    list[low] = list[i];
    list[i] = temp;
    // 递归调用左半数组
    quickSort(list, low, j - 1);
    // 递归调用右半数组
    quickSort(list, j + 1, high);
}
```



------

#### 堆排序



堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点



**算法描述**

1. 将初始待排序关键字序列(R1,R2….Rn)构建成大顶堆，此堆为初始的无序区
2. 将堆顶元素R[1]与最后一个元素R[n]交换，此时得到新的无序区(R1,R2,……Rn-1)和新的有序区(Rn),且满足R[1,2…n-1]<=R[n]
3. 由于交换后新的堆顶R[1]可能违反堆的性质，因此需要对当前无序区(R1,R2,……Rn-1)调整为新堆，然后再次将R[1]与无序区最后一个元素交换，得到新的无序区(R1,R2….Rn-2)和新的有序区(Rn-1,Rn)。不断重复此过程直到有序区的元素个数为n-1，则整个排序过程完成



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015231308699-356134237.gif)



**代码实现**

```java
/**
 * 堆排序利用大根堆（或小根堆）堆顶记录的关键字最大（或最小）这一特征，来从当前无需集合中获取最大（或最小）元素变得简单
 */
public int[] heapSort(int[] arrays) {
    for(int i = 0; i < arrays.length - 1 - i; i++) {
        // 建大根堆
        createMaxHeap(arrays, arrays.length - 1 - i);
        // 将当前无需区的堆顶元素与该区间的最后一个元素交换
        swap(arrays, arrays.length - 1 - i);
    }
    return arrays;
}

private void createMaxHeap(int[] arrays, int lastIndex) {
    for (int i = (lastIndex - 1)/2; i >= 0; i--) {
        // 保存当前正在判断的节点
        int k = i;
        // 若当前节点的子节点存在
        while (2 * k + 1 <= lastIndex) {
            // biggerIndex 总是记录较大节点的值，先赋值为当前判断节点的左子节点
            int biggerIndex = 2 + k + 1;
            if (biggerIndex < lastIndex) {
                // 若右子节点存在，则此时biggerIndex应该等于lastIndex
                if (arrays[biggerIndex] < arrays[biggerIndex + 1]) {
                    // 若右子节点比左子节点值大，则biggerIndex记录的是右子节点的值
                    biggerIndex++;
                }
            }
            if (arrays[k] < arrays[biggerIndex]) {
                // 若当前节点值比子节点最大值小，则交换两者的值，交换后将biggerIndex的值赋给k
                swap(arrays, k, biggerIndex);
                k = biggerIndex;
            } else {
                break;
            }
        }
    }
}

private void swap(int[] arrays, int i, int j) {
    int temp = arrays[i];
    arrays[i] = arrays[j];
    arrays[j] = temp;
}
```



------

#### 计数排序



计数排序不是基于比较的排序算法，其核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。 作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数



**算法描述**

1. 找出待排序的数组中最大和最小的元素
2. 统计数组中每个值为 i 的元素出现的次数，存入数组 C 的第 i 项；
3. 对所有的计数累加（从C中的第一个元素开始，每一项和前一项相加）
4. 反向填充目标数组：将每个元素 i 放在新数组的第 C(i) 项，每放一个元素就将 C(i) 减去 1



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015231740840-6968181.gif)



**代码实现**

```java
public int[] countSort(int[] arrays) {
    // 求取最大值和最小值
    int max = arrays[0];
    int min = arrays[0];
    for(int i : arrays) {
        if (i > max) {
            max = i;
        }
        if (i < min) {
            min = i;
        }
    }
    // 计算中间数组的长度
    int[] temp = new int[max - min + 1];
    // 循环遍历旧数组，将统计出来的值出现的频率记录在中间数组中
    for (int i : arrays) {
        temp[i - min] += 1;
    }
    // 创建最终返回的数组，长度与原数组相同，但是是排序的
    int[] result = new int[arrays.length];
    int index = 0;
    for (int i = 0; i < temp.length; i++) {
        for (int j = 0; j < temp[i]; j++) {
            result[index++] = i + min;
        }
    }
    
    return result;
}
```



数排序是一个稳定的排序算法。当输入的元素是 n 个 0到 k 之间的整数时，时间复杂度是O(n+k)，空间复杂度也是O(n+k)，其排序速度快于任何比较排序算法。当k不是很大并且序列比较集中时，计数排序是一个很有效的排序算法



------

#### 桶排序



桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。桶排序 (Bucket  sort)的工作的原理：假设输入数据服从均匀分布，将数据分到有限数量的桶里，每个桶再分别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排序）



**算法描述**

1. 设置一个定量的数组当作空桶
2. 遍历输入数据，并且把数据一个一个放到对应的桶里去
3. 对每个不是空的桶进行排序
4. 从不是空的桶里把排好序的数据拼接起来



**图片演示**

![image-20210914224234768](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/Java%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95/image-20210914224234768.png)



**代码实现**

```java
public double[] bucketSort(double[] array) {
    // 求取最大值和最小值，并计算出差值
    double max = array[0];
    double min = array[0];
    for (int i = 1; i < array.length; i++) {
        if (array[i] > max) {
            max = i;
        }
        if (array[i] < min) {
            min = i
        }
    }
    double diff = max - min;
    
    // 初始化桶
    int bucketNum = array.length;
    ArrayList<LinkedList<Double>> bucketList = new ArrayList<LinkedList<Double>>(bucketNum);
    for (int i = 0; i < bucketNum; i++) {
        buicketList.add(new LinkedList<Double>());
    }
    
    // 遍历原始数组将每个元素放入桶中
    for (int i = 0; i < bucketNum; i++) {
        int num = (int)((array[i] - min) * (bucketNum - 1) / diff);
        bucketList.get(num).add(array[i]);
    }
    
    // 对每个桶进行内部排序
    for (int i = 0; i < bucketList.size(); i++) {
        // 使用Collection,sort，其底层基于归并排序或归并排序的优化版本
        Collections.sort(bucketList.get(i));
    }
    
    // 输出全部元素
    double[] result = new double[array.length];
    int index = 0;
    for (LinkedList<Double> list : bucketList) {
        for (double element : list) {
            result[index] = element;
            index++;
        }
    }
    return result;
}
```



桶排序最好情况下使用线性时间O(n)，桶排序的时间复杂度，取决与对各个桶之间数据进行排序的时间复杂度，因为其它部分的时间复杂度都为O(n)。很显然，桶划分的越小，各个桶之间的数据越少，排序所用的时间也会越少。但相应的空间消耗就会增大



------

#### **基数排序**



基数排序是按照低位先排序，然后收集；再按照高位排序，然后再收集；依次类推，直到最高位。有时候有些属性是有优先级顺序的，先按低优先级排序，再按高优先级排序。最后的次序就是高优先级高的在前，高优先级相同的低优先级高的在前



**算法描述**

1. 取得数组中的最大数，并取得位数
2. arr 为原始数组，从最低位开始取每个位组成 radix 数组
3. 对 radix 进行计数排序（利用计数排序适用于小范围数的特点）



**动图演示**

![img](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015232453668-1397662527.gif)



**代码实现**

```java
public int[] radixSort(int[] array) {
    //找到数组中的最大值 
    int max = array[0]; 
    for(int i=0;i<array.length;i++){ 
        if(array[i]>max){
            max = array[i]; 
        } 
    } 
    
    //关键字的个数，我们使用个位、十位、百位...当做关键字，所以关键字的个数就是最大值的位数
    int keysNum = 0;  
    while(max>0){ 
        max /= 10; 
        keysNum++; 
    } 
               
    List<ArrayList<Integer>> buckets = new ArrayList<ArrayList<Integer>>();
    //每位可能的数字为0~9，所以设置10个桶 
    for(int i=0;i<10;i++){ 
        buckets.add(new ArrayList<Integer>());
    } 
        
    //由最次关键字开始，依次按照关键字进行分配 
    for(int i=0;i<keysNum;i++){ 
        //扫描所有数组元素，将元素分配到对应的桶中
        for(int j=0;j<array.length;j++){  
            //取出该元素对应第i+1位上的数字，比如258，现在要取出十位上的数字，258%100=58,58/10=5 
            int key =array[j]%(int)Math.pow(10, i+1)/(int)Math.pow(10, i); 
            buckets.get(key).add(array[j]); //将该元素放入关键字为key的桶中 
        } 
            
        //分配完之后，将桶中的元素依次复制回数组 
        int counter = 0; //元素计数器 
        for(int j=0;j<10;j++){ 
            ArrayList<Integer> bucket =buckets.get(j); //关键字为j的桶 
            while(bucket.size()>0){ 
                array[counter++] = bucket.remove(0); //将桶中的第一个元素复制到数组，并移除 
            } 
        } 
    } 
}
```



基数排序基于分别排序，分别收集，所以是稳定的。但基数排序的性能比桶排序要略差，每一次关键字的桶分配都需要O(n)的时间复杂度，而且分配之后得到新的关键字序列又需要O(n)的时间复杂度。假如待排数据可以分为d个关键字，则基数排序的时间复杂度将是O(d*2n) ，当然d要远远小于n，因此基本上还是线性级别的。

基数排序的空间复杂度为O(n+k)，其中k为桶的数量。一般来说n>>k，因此额外空间需要大概n个左右