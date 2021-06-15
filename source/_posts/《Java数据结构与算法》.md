---
title: 《Java数据结构与算法》
date: 2020-09-22 23:44:50
tags: 数据结构与算法
categories: 数据结构与算法
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/franki-chamaki-1K6IQsQbizI-unsplash.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/franki-chamaki-1K6IQsQbizI-unsplash.jpg
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

![image-20201103001513219](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201103001513219.png)



在上图中，X 轴代表 n 值，Y 轴代表时间复杂度。时间复杂度随着 n 值得变化而变化，可以看到，O(n)、O(logn)、O(√n )、O(nlogn )随着 n 的增加，复杂度提升不大，而 O(2ⁿ) 和 O(n!)  在 n 值较大时变化非常之大。实际上，在 n 等于 50 时，O(2ⁿ) 和 O(n!)  的复杂度就已经突破十位数了



常用的时间复杂度按照耗费的时间从小到大依次是：

O(1) < O(logn) < O(n) < O(nlogn) < O(n²) < O(n³) < O(2ⁿ) < O(n!)



------



## 数组

### 数组的概念



![image-20201104232327992](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201104232327992.png)



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



![image-20201107180130865](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201107180130865.png)

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



![image-20201110211256134](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201110211256134.png)



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





![image-20201115151509347](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201115151509347.png)

**此图只是对ADT的简单展示，Java中数据结构中的层次远比这个复杂得多**



不同的 ADT 之间也有着不同的关系，如上图所示，可以有层级关系，如 List 和 Queue 之间；也有并列关系，如 List 和 Map 之间；还有可能什么关系也没有。我们可以根据自己的需求去选用合适的 ADT 实现类，如果我们想要个 List 集合的话，就去找一个实现了 List 集合接口的类；如果想要个 Queue 的话，就去找一个实现了 Queue 接口的类；如果当前存在的都不满足需求的话，我们也可以自己实现一个，只要实现了对应的 ADT 接口即可



------



### 栈ADT

#### 栈模型



栈（Stack）是限制插入和删除只能在一个位置上进行的表，该位置是表的末端，叫做栈的顶（top）。对栈的基本操作有 push（进栈）和 pop（出栈），前者相当于插入，后者则是删除最后插入的元素。最后插入的元素可以通过 top 例程在执行 pop 之前进行考察。对空栈进行 top 或 pop 操作一般被认为栈 ADT 中的一个错误。另一方面，当运行 push 时空间用尽是一个实现限制，但不是 ADT 错误



栈有时又叫做 LIFO（后进先出）表。普通的清空栈的操作和判断是否空栈的操作都是栈的操作系统指令的一部分。但是，我们对栈所能够做的，基本上也就是 pop 和 push 操作



![image-20201115170259354](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201115170259354.png)

![image-20201115170423180](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201115170423180.png)



------

### 队列ADT

#### 队列模型



像栈一样，队列也是表。但是，使用队列时插入在一段进行而删除则在另一端进行



队列的基本操作是 enqueue（入队），它是在表的末端（叫做队尾 rear）插入一个元素；以及 dequeue（出队），它是返回并删除在表开头（叫做对头 front）的元素。队列，也叫做 FIFO（先进先出）表



![image-20201115195545751](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/image-20201115195545751.png)



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



![image-20201119210337396](C:\Users\A\Desktop\image-20201119210337396.png)



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



![image-20201121224210165](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20201121224210165.png)



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

![image-20201124204551109](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20201124204551109.png)



树是一种抽象数据类型或是实现这种抽象数据类型的数据结构，用来模拟具有树状结构性质的数据集合。它是由n（n>0）个有限节点组成一个具有层次关系的集合。把它叫做“树”是因为它看起来像一棵倒挂的树，也就是说它是根朝上，而叶朝下的。它具有以下的特点：

1. 每个节点有零个或多个子节点；
2. 没有父节点的节点称为根节点；
3. 每一个非根节点有且只有一个父节点；
4. 除了根节点外，每个子节点可以分为多个不相交的子树；



------

#### 树的术语



![image-20201124221707828](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20201124221707828.png)

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



![image-20201211175500008](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20201211175500008.png)





------

#### 二叉树的性质



1. 二叉树中，第 i 层最多有 2^(i-1) 个结点
2. 如果二叉树的深度为 K，那么此二叉树最多有 2^(K-1) 个结点
3. 二叉树中，终端结点数（叶子结点数）为 n0，度为 2 的结点数为 n2，则 n0=n2+1



------

#### 满二叉树



如果二叉树中除了叶子结点，每个结点的度都为 2，则此二叉树称为满二叉树



![image-20201211180057924](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20201211180057924.png)



满二叉树除了满足普通二叉树的性质，还具有以下性质：

1.  满二叉树中第 i 层的节点数为 2^(n-1) 个
2. 深度为 k 的满二叉树必有 2^(k-1) 个节点 ，叶子数为 2^(k-1)
3. 满二叉树中不存在度为 1 的节点，每一个分支点中都两棵深度相同的子树，且叶子节点都在最底层
4. 具有 n 个节点的满二叉树的深度为 log2(n+1)



------

#### 完全二叉树



如果二叉树中除去最后一层节点为满二叉树，且最后一层的结点依次从左到右分布，则此二叉树被称为完全二叉树

![image-20201212192543825](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20201212192543825.png)



对于任意一个完全二叉树来说，如果将含有的结点按照层次从左到右依次标号，对于任意一个结点 i ，完全二叉树还有以下几个结论成立：

1. 当 i>1 时，父亲结点为结点 [i/2] 。（i=1 时，表示的是根结点，无父亲结点）
2.  如果 2*i>n（总结点的个数） ，则结点 i 肯定没有左孩子（为叶子结点）；否则其左孩子是结点 2*i 
3.  如果 2*i+1>n ，则结点 i 肯定没有右孩子；否则右孩子是结点 2*i+1 



------

### 二叉树的结构

#### 二叉树的顺序储存结构



二叉树的顺序存储，指的是使用数组存储二叉树。需要注意的是，顺序存储只适用于完全二叉树。换句话说，只有完全二叉树才可以使用顺序表存储。因此，如果我们想顺序存储普通二叉树，需要提前将普通二叉树转化为完全二叉树



完全二叉树的顺序存储，仅需从根节点开始，按照层次依次将树中节点存储到数组即可



![image-20210103150636489](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210103150636489.png)



------

#### 二叉树的链式存储结构



其实二叉树并不适合用数组存储，因为并不是每个二叉树都是完全二叉树，普通二叉树使用顺序表存储或多或多会存在空间浪费的现象。而一棵普通的二叉树，若将其采用链式存储，则只需从树的根节点开始，将各个节点及其左右孩子使用链表存储即可



![image-20210103151203203](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210103151203203.png)



采用链式存储二叉树时，其节点结构由三部分组成

* 指向左孩子节点的指针（leftChild）
* 节点存储的数据（data）
* 指向右孩子节点的指针（rightChild）

![image-20210103151359861](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210103151359861.png)

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

![image-20210103183839223](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210103183839223.png)

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



![image-20210110194451416](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210110194451416.png)



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



![image-20210117135411299](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210117135411299.png)



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

![image-20210124190212712](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210124190212712.png)

如图所示，我们可以使用二叉树的叶子节点来表示英文字母，00 表示 A，10 表示 B，我们根据传过来的编码去读取二叉树的节点，当一个节点是叶子叶子节点的时候，读取该叶子节点表示的英文字母就可以了

这样，我们就需要一个 14 层的二叉树来表示 25 个英文字母，最短的是 A 和 B，而最长的是 Z

可是在实际的英文会话中，使用最多的是英文字母 E，使用普通的二叉树表示可以让不同的英文字母的字节码长度不同，却无法让使用最多的英文字母用最短的长度来表示

</br>

**方法三：哈夫曼树**

假设在一个文本中只出现了 ABCDEFGH 8个字母，并且每个字母出现的频数如下

![image-20210124200018018](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210124200018018.png)

我们在二叉树中，根据各个字母的出现频数排列该字母在二叉树中的位置

![image-20210124204148246](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210124204148246.png)

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



![image-20210127235520764](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210127235520764.png)





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

   ![image-20210310235256739](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210310235256739.png)

   这种情况直接删除叶子结点即可

   

2. 要删除的结点有左结点但是没有右结点，或者有右结点但是没有左结点，如图：

   ![image-20210310235337053](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210310235337053.png)

   这种情况将被删除结点的子结点的值赋给被删除结点，然后删掉被删除结点的子结点

   

3. 删除的节点既有左节点又有右节点，在这种情况下，我们只需要将找到待删节点的右子树中值最小的节点（或者左子树中值最大的结点），将其删除并且获取其值，并用其值替换待删节点的值即可

   ![image-20210310235636379](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210310235636379.png)



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

![image-20210314230400388](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210314230400388.png)

在数据结构中，将左右子树高度差不超过一的树称为平衡树，而 AVL 树就是始终保持平衡状态的二叉搜索树，也叫平衡二叉树



AVL树是最早被发明的自平衡二叉查找树。在AVL树中，任一节点对应的两棵子树的最大高度差为1，因此它也被称为高度平衡树



------

#### 平衡二叉树的性质



1. 可以是空树
2. 假如不是空树，任何一个结点的左子树与右子树都是平衡二叉树，并且高度之差的绝对值不超过 1



------

#### 平衡因子



某节点的左子树与右子树的高度(深度)差即为该节点的平衡因子，平衡二叉树中不存在平衡因子大于 1 的节点。在一棵平衡二叉树中，节点的平衡因子只能取 0 、1 或者 -1 ，分别对应着左右子树等高，左子树比较高，右子树比较高





![image-20210325215503400](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210325215503400.png)

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

![image-20210405194558019](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210405194558019.png)

在这棵树中插入结点 99，树的结构变成：

![img](https://pic4.zhimg.com/v2-2ddb0f9b832fff594e294dffc299b373_b.webp)



最小失衡子树：

在新插入的结点向上查找，以第一个平衡因子的绝对值超过 1 的结点为根的子树称为最小不平衡子树。也就是说，一棵失衡的树，是有可能有多棵子树同时失衡的。而这个时候，我们只需要调整最小的不平衡子树，就能够将不平衡的树调整为平衡的树



平衡二叉树的失衡调整主要是通过旋转最小失衡子树来实现的。根据旋转的方向有两种处理方式，左旋与右旋。旋转的目的就是减少高度，通过降低整棵树的高度来平衡。哪边的树高，就把那边的树向上旋转



------

#### 左旋和右旋



以下两张图分别为左旋和右旋的动图，便于理解和记忆：

![左旋](https://img-blog.csdn.net/20180829143451434?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1OTQwOTIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![右旋](https://img-blog.csdn.net/20180829143509110?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1OTQwOTIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



左旋就是将节点的右支往左拉，右子节点变成父节点，并把晋升之后多余的左子节点出让给降级节点的右子节点；

而右旋就是反过来，将节点的左支往右拉，左子节点变成了父节点，并把晋升之后多余的右子节点出让给降级节点的左子节点

即左旋就是往左变换，右旋就是往右变换。不管是左旋还是右旋，旋转的目的都是将节点多的一支出让节点给另一个节点少的一支



------

#### 左旋



![img](https://pic4.zhimg.com/80/v2-0a757b848f85f3531b0789fd3d79f0b3_720w.jpg)

如上图所示，加入新节点 99 后， 节点 66 的左子树高度为 1，右子树高度为 3，此时平衡因子为 -2。为保证树的平衡，此时需要对节点 66 做出旋转，因为右子树高度高于左子树，对节点进行左旋操作，流程如下：

1. 节点的右孩子替代此节点位置
2. 右孩子的左子树变为该节点的右子树
3. 节点本身变为右孩子的左子树

动图如下：

![img](https://pic4.zhimg.com/v2-db1cdb0da952a71f9b6d64b2608467eb_b.webp)

------

#### 右旋



右旋操作与左旋类似，操作流程为：

1. 节点的左孩子代表此节点
2. 节点的左孩子的右子树变为节点的左子树
3. 将此节点作为左孩子节点的右子树

动图如下：

![img](https://pic4.zhimg.com/v2-05246384c1c16537ca6176983bdb2627_b.webp)

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

![img](https://pic4.zhimg.com/v2-373766641d1c03a78f3d7eac803d1f57_b.webp)



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

![img](https://pic1.zhimg.com/v2-e7044e4965ba640ee9ef35beac407cdc_b.webp)

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

![image-20210410204030943](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210410204030943.png)

旋转流程如下：

![image-20210410204728820](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210410204728820.png)

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

![image-20210411170748939](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210411170748939.png)

![image-20210411170846005](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210411170846005.png)

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

![image-20210411191110563](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210411191110563.png)



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

![image-20210411195729876](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210411195729876.png)



以下是关于红黑树结点插入时的归纳整理：

![image-20210411233823776](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210411233823776.png)

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

![image-20210415201950282](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210415201950282.png)



红黑树删除结点最重要的思路：删除结点被替代后，在不考虑结点的键值的情况下，对于树来说，可以认为删除的是替代结点。

如图：

![image-20210415221027607](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210415221027607.png)

基于此，上面所说的 3 种二叉树的删除情景可以相互转换并且最终都是转换为情景 1

* 情景2：删除结点用其唯一的子结点替换，子结点替换为删除结点后，可以认为删除的是子结点，若子结点又有两个子结点，那么相当于转换为情景3，一直自顶向下转换，总是能转换为情景1。（对于红黑树来说，根据性质 5.1，只存在一个子结点的结点肯定在树末了）
* 情景3：删除结点用后继结点（肯定不存在左结点），如果后继结点有右子结点，那么相当于转换为情景 2，否则转为为情景 1

综上所述，删除操作删除的结点可以看作删除替代结点，而替代结点最后总是在树末



![image-20210615224601746](C:\Users\A\AppData\Roaming\Typora\typora-user-images\image-20210615224601746.png)

