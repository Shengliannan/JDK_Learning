# JDK_Learning
[TOC]

JDK源码学习

## Java Object 方法有哪些

- registerNavtives():void
- getClass():Class<?>
- hashCode():int
- equals(Object):boolean
- clone():Object
- toString():String
- notify():void
- notifyAll():void
- wait(long):void
- wait(long,int):void
- wait():void
- finalize():void

## Java 比较地址

```
"=="
```

## 深拷贝和浅拷贝

## Java的引用类型
### Integer
```Java
//输出 true true false
//Java 的Integer类中有一个静态内部类IntegerCache，会实例化-128~127的实例对象，当从使用-128~127的常量时，会从缓存池中取出，不会新创建对象。
public class IntegerTest {
    public static void main(String[] args) {
        Integer i1 = 1;
        Integer i2 = 1;
        System.out.println(i1==i2);
        Integer i3 = 127;
        Integer i4 = 127;
        System.out.println(i3==i4);
        Integer i5 = 128;
        Integer i6 = 128;
        System.out.println(i5==i6);
    }
}


public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

## Java中的不可变类

### final类

```
BigInteger、BigDecimal、String、Integer、Long、Short、Byte、Character、Boolean、Float和Double
```

#### 如何创建一个不可变类？

1.  所有***成员***都是private  
2.  不提供对***成员***的改变方法，例如：setXXXX  
3.  确保所有的方法不会被重载。手段有两种：使用final Class(强不可变类)，或者将所有类方法加上final(弱不可变类)。  
4.  如果某一个类成员不是原始变量(primitive)或者不可变类，必须通过在成员初始化(in)或者get方法(out)时通过深度clone方法，来确保类的不可变。

为什么要创建不可变类？

不可变类是实例创建后就不可以改变成员遍历的值。这种特性使得不可变类提供了***线程安全***的特性但同时也带来了***对象创建的开销***，每更改一个属性都是重新创建一个新的对象。JDK内部也提供了很多不可变类如Integer、Double、String等。String的不可变特性主要为了满足常量池、线程安全、类加载的需求。合理使用不可变类可以带来极大的好处。

***注意：虽然String对象将value设置为final,并且还通过各种机制保证其成员变量不可改变。但是还是可以通过反射机制的手段改变其值。***

> [不可变类讲解](https://www.cnblogs.com/jaylon/p/5721571.html)
>
> [BigInteger不可变类讲解](https://www.jb51.net/article/37889.htm)



## 集合

Java的集合类主要由两个接口派生而出：Collection和Map。

Java集合框架的4个主要体系：set、list、Queue、Map

Java8对集合改进：Lambda表达式简化集合编程、集合的Stream编程。

[参考](https://wenku.baidu.com/view/29a7ebdd4793daef5ef7ba0d4a7302768e996f6a.html)



![Java集合框架图](https://raw.githubusercontent.com/Shengliannan/JDK_Learning/master/img/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%9B%BE.jpg)

***注：***

1. 继承类：实线+空心箭头
2. 实线接口：虚线+空心箭头
3. 关联：实线+实心箭头
4. 依赖：虚线+实心箭头


### Collection

![Collection](https://github.com/Shengliannan/JDK_Learning/blob/master/img/collection.png?raw=true)
#### List
##### ArrayList

```java
// 默认初始容量为10
private static final int DEFAULT_CAPACITY = 10;

private void grow(int minCapacity)
{
    int oldCapacity = elementData.length;
    //注意此处扩充capacity的方式是将其向右一位再加上原来的数，实际上是扩充了1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}

//如果想ArrayList中添加大量元素，可使用ensureCapacity方法一次性增加capacity，可以减少增加重分配的次数提高性能。
//eg:自动扩容和使用ensureCapacity方法比较，后者效率明显提高。
public class EnsureCapacityTest {
    public static void main(String[] args) {
        ArrayList<Object> list = new ArrayList<Object>();
        final int N =10000000;
        long startTime = System.currentTimeMillis();
        for(int i = 0;i<N;i++) {
            list.add(i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println(endTime-startTime);

        list = new ArrayList<Object>();
        long startTime1 = System.currentTimeMillis();
        list.ensureCapacity(N);
        for(int i = 0;i<N;i++) {
            list.add(i);
        }
        long endTime1 = System.currentTimeMillis();
        System.out.println(endTime1-startTime1);
    }
}

output：
3946
1345

原文：https://blog.csdn.net/zzzzzz1238/article/details/78480104 

```

##### 数组和ArrayList的互转

1）数组转ArrayList

```Java
//arrayList.get(0) 是一个一维数组，size=1
ArrayList<Integer> arrayList = new ArrayList(Arrays.asList(array));

// 下面这种是错误的，原因待查
ArrayList<Integer> arrayList = new ArrayList<Integer>(Arrays.asList(array));
```

2）ArrayList转数组

```

```





##### LinkedList




#### Queue
##### Deque

全称：double ended queue

***

##### LinkedList

LinkedList实现了Deque接口，Deque继承了Queue接口。

特点：双向链表，不同步，动态扩容。



###### 检索

- peek() 

检索，返回第一个元素，不删除，链表为空，返回null

- element()

检索，返回第一个元素，不删除，链表为空，抛出异常，NoSuchElementException

###### 插入

- add()

插入到尾部，插入成功，返回true，没有异常。

- offer()

插入到尾部，插入成功，返回true，没有异常。

###### 删除

- poll()

删除元素，返回第一个元素，链表为空，返回null

- pop()

删除元素，返回第一个元素，链表为空，抛出异常：java.util.NoSuchElementException

- remove()

删除元素，返回第一个元素，链表为空，抛出异常：java.util.NoSuchElementException



##### PriorityQueue

1. 默认是一个小顶堆
2. PriorityQueue是非线程安全的，所以Java提供了PriorityBlockingQueue（实现[BlockingQueue接口](http://www.journaldev.com/1034/java-blockingqueue-example-implementing-producer-consumer-problem)）用于[Java多线程环境](http://www.journaldev.com/1079/java-thread-tutorial)。 
3. 大顶堆的PriorityQueue实现

```java
//构造函数中重写比较器
PriorityQueue<Integer> priorityQueue = new PriorityQueue<Integer>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2-o1;
            }
        });
```

4. PriorityQueue的问题

   > [PriorityQueue的问题](https://blog.csdn.net/cyp331203/article/details/25310733 )

```Java
import java.util.Iterator;
import java.util.PriorityQueue;
// 期望输出2,4,6,7，实际输出2,6,4,7，PriorityQueue只保证堆的性质，如果想要保证输出的顺序，需要依次poll()
public class PriorityQueueTest {
    public static void main(String[] args) {
        PriorityQueue<Integer> priorityQueue = new PriorityQueue<>();
        priorityQueue.add(6);
        priorityQueue.add(4);
        priorityQueue.add(2);
        priorityQueue.add(7);
        Iterator iterator = priorityQueue.iterator();
        while(iterator.hasNext()){
            System.out.print(iterator.next()+" ");
        }
        //这里取出size()大小，因为每一次poll()之后size大小都会变化，所以不能作为for循环的判断条件
        int len = priorityQueue.size();
		for(int i=0;i<len;i++){
			System.out.print(priorityQueue.poll()+" "); //每次执行poll()都会重写调整，满足堆定义
		}
    }
}
```



***



### Map

![Map](https://github.com/Shengliannan/JDK_Learning/blob/master/img/map.png?raw=true)

#### HashMap

HashMap简介

> [源码解析](https://blog.csdn.net/qazwyc/article/details/76686915)

HashMap是一个***散列表***，它存储的内容是键值对（key-value）映射。

特点：

- 不同步
- key、value都可以为null
- HashMap的映射不是有序的

HashMap的实例有两个参数影响其性能：“初始容量”和“加载因子”。

当哈希表中的条目超出了加载因子与当前容量的乘积时，则要对哈希表进行rehash操作（即重建内部数据结构），从而哈希表将具有大于两倍的桶数。

HashMap是采用***拉链法***实现的，每一个Entry本质上是一个单链表。

rehash规则？(在1.8后使用了resize()函数)

初始容量：

默认的初始化容量是16，必须是2的幂；

通过tableSizeFor()，保证了HashMap容量始终是2的次方，在通过hash寻找index时就可以用逻辑运算来替代取余，即hash％n用hash&(n -1)替代

在HashMap的构造函数中并没有对数组`Node<K,V>[] table`初始化，而是简单的设置参数，在首次put时调用resize()分配内存。

put/get

put

计算hash值，计算逻辑：

(key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);（为什么这么计算）

```
if ((tab = table) == null || (n = tab.length) == 0)    //第一次put或者hashmap中没值。
    n = (tab = resize()).length;   
    
```

threshold

在HashMap类中，threshold有时会存储初始容量值。

如果使用不指定初始容量的构造函数，会在resize()中使用默认的DEFAULT_INITIAL_CAPACITY（16）初始化数组。

指定初始容量，会用不大于给定初始容量的2的幂数倍来初始化数组，如若初始容量为11，则用12来初始化数组；如果初始容量为31，用32来初始化数组。

```
newCap = DEFAULT_INITIAL_CAPACITY;  //DEFAULT_INITIAL_CAPACITY；//16
newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY); //16*0.75=12
```



resize

```java
   /**
     * Initializes or doubles table size.  If null, allocates in
     * accord with initial capacity target held in field threshold.
     * Otherwise, because we are using power-of-two expansion, the
     * elements from each bin must either stay at same index, or move
     * with a power of two offset in the new table.
     * 初始化或者扩充2倍数组的大小。如果数组一开始为空，分配
     * @return the table
     */
    final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```




















```

若“key不为null”，则计算该key的哈希值，然后将其添加到该哈希值对应的链表中。
// 若“该key”对应的键值对已经存在，则用新的value取代旧的value。然后退出！
对应的键值已经存在即hashcode相等&&equals()
```



#### equals()相同，hashcod()必相同；hashcode()相同，equals()不一定相同

```java
System.out.println("Ma".hashCode()=="NB".hashCode());  //输出true
```

> [参考](https://www.jianshu.com/p/44e878e987bf)



Entry 实际上就是一个单向链表。这也是为什么我们说HashMap是通过拉链法解决哈希冲突的。尾插法。

单链表?具体？

怎么解决冲突？

HashMap put

size是看hashmap种有多少键值对的，而不是数组多大的。



hashcode相等，equals不等

hashcode不相等，equals一定不等。

hashcode，equals相等



```java
//判断插入位置，用&不用% ，hash值%表长->hash值&表长
p = tab[i = (n - 1) & hash]) == null
```

#### Integer 

```
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```





Integer

IntegerCache

```
IntegerCache
```







红黑树(Java TreeSet/TreeMap)

> [参考](https://www.cnblogs.com/skywang12345/p/3245399.html#a1)
>
> https://www.cnblogs.com/CarpenterLee/p/5503882.html

自平衡二叉查找树

红黑树和AVL（二叉查找树）类似，都是在插入和删除操作时通过特定的操作保持二叉树的平衡，从而获得较高的查找性能。

它虽然是复杂的，但它的最坏情况运行时间也是非常良好的，它可以在O(log n)时间内查找，插入和删除，这里n是树中元素的数目。

- **红黑树的特性**:
  **（1）每个节点或者是黑色，或者是红色。**
  **（2）根节点是黑色。**
  **（3）每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]**
  **（4）如果一个节点是红色的，则它的子节点必须是黑色的。**
  **（5）从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。**

- 隐含性质：

  任意一颗以黑色节点为根的子树也必定是一颗红黑树（递归定义）

  左（右）子树的高度最多是右（左）子树的两倍，即：若H(left)>H(right),

  则H(left)<=2*H(right)+1

  

**红黑树的时间复杂度为: O(lgn)**

*红黑树的*操作时间跟二叉查找*树的时间复杂度*是一样的,执行查找、插入、删除等操作的时间复杂度为O(logn)。



## 树专题

二分查找和二叉查找树的区别：

> [参考](https://blog.csdn.net/m0_38075425/article/details/81745544)

RBT （红黑树）

BST（二叉查找树、二叉排序树）

AVL（平衡二叉树）

> [参考](https://blog.csdn.net/sup_heaven/article/details/39313731)



Sprark Streaming 实时处理数据的链路

Spark Streaming如何实现bactch

Spark Streaming与Kafka的两种集成方式与优缺点

Hive的底层原理

HBase的底层原理

MR原理

虚拟内存的理解，分页，分段，段页

String与StringBuilder不同（读源码）

写一个消费生产模式

二分查找

归并排序





### 多线程

#### ThreadLocal

> [码农翻身](https://mp.weixin.qq.com/s/aM03vvSpDpvwOdaJ8u3Zgw )
>
> [正确理解ThreadLocal](https://www.iteye.com/topic/103804 )
>
> [ThreadLocalMap里的弱引用](https://blog.csdn.net/vicoqi/article/details/79743112 )

为什么要维护本地线程变量？


**ThreadLocal是用来维护本线程的变量的，并不能解决共享变量的并发问题。**

ThreadLocalMap是ThreadLocal的静态内部类

Thread类中持有ThreadLocal.ThreadLocalMap的引用

每个Thread类会持有自己的ThreadLocalMap引用。不同Thread的ThreadLocalMap实例不相同

实际上ThreadLocal中并没有存放任何的对象或引用，在上面的的代码中ThreadLocal的实例threadSession只相当于一个标记的作用。而存放对象的真正位置是正在运行的Thread线程对象，每个Thread对象中都存放着一个ThreadLocalMap类型threadLocals对象，这是一个映射表map，这个map的键是一个ThreadLocal对象，值就是我们想存的局部对象。 

ThreadLocal set(T value) -> ThreadLocalMap set(ThreadLocal<?> key, Object value)

ThreadLocalMap和HashMap的区别？

```Java
//HashMap
transient Node<K,V>[] table;
//node是一个链表节点
-----------------------------
| hash | key | vlaue | next |
-----------------------------
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }


//ThreadLocalMap
private Entry[] table;
//Entry是一个键值对
-------------------------
| ThradLocal类型引用 | v |
-------------------------
static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }

/**
* 在寻址冲突时，ThreadLocalMap并没有使用链表或红黑树等方式链地址来解决，而是当前地址不可用，就在当前map
* 的数据数组中继续查找下一个可用的地址。
*/
```





ThreadLocal 的set方法

```Java
//ThreadLocal
public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
    
//ThreadLocalMap
private void set(ThreadLocal<?> key, Object value) {

            // We don't use a fast path as with get() because it is at
            // least as common to use set() to create new entries as
            // it is to replace existing ones, in which case, a fast
            // path would fail more often than not.

            Entry[] tab = table;
            int len = tab.length;
            int i = key.threadLocalHashCode & (len-1);

            for (Entry e = tab[i];
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                ThreadLocal<?> k = e.get();

                if (k == key) {
                    e.value = value;
                    return;
                }

                if (k == null) {
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }

            tab[i] = new Entry(key, value);
            int sz = ++size;
            if (!cleanSomeSlots(i, sz) && sz >= threshold)
                rehash();
        }
```



```Java
//Thread需要保存多个本地线程变量，就要声明多个ThradLocal类变量
/**
* 注：可以看出，存数据的实际上是ThreadLocalMap，而Thread存有ThreadLocalMap的引用，所以虽然ThreadLocal
* 的实例不同，但是对应的ThreadLocaLMap是一个。
*/
package com.jdkStudy.threadLocal;

import java.lang.reflect.Method;

public class ThreadLocalTest {
    ThreadLocal<String> threadLocal = new ThreadLocal<String>();
    ThreadLocal<String> threadLocal2 = new ThreadLocal<String>();
    public static void main(String[] args) {
        ThreadLocalTest threadLocalTest = new ThreadLocalTest();
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                //System.out.println("t1:"+ threadLocalTest.threadLocal.get());
                threadLocalTest.threadLocal.set("t1 local variable");
                threadLocalTest.threadLocal2.set("t1-2 local variable");
                System.out.println("t1:"+ threadLocalTest.threadLocal.get());
                System.out.println("t1:"+ threadLocalTest.threadLocal2.get());

                /*通过反射查看ThreadLocalMap*/
                try {
                    Method getMapMethod = threadLocalTest.threadLocal.getClass().getDeclaredMethod("getMap",Thread.class);
                    getMapMethod.setAccessible(true);
                    Object threadLocalMap = getMapMethod.invoke(threadLocalTest.threadLocal,Thread.currentThread());
                    System.out.println("ThreadLocalMap  info:"+threadLocalMap);
                    Method getMapMethod2 = threadLocalTest.threadLocal2.getClass().getDeclaredMethod("getMap",Thread.class);
                    getMapMethod2.setAccessible(true);
                    Object threadLocalMap2 = getMapMethod.invoke(threadLocalTest.threadLocal2,Thread.currentThread());
                    System.out.println("ThreadLocalMap2 info:"+threadLocalMap2);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        t1.start();
    }
}

//输出
t1:t1 local variable
t1:t1-2 local variable
ThreadLocalMap  info:java.lang.ThreadLocal$ThreadLocalMap@638c33e6
ThreadLocalMap2 info:java.lang.ThreadLocal$ThreadLocalMap@638c33e6


```

### ThreadLocalMap的弱引用(key回收，value无法被回收，造成内存泄漏)

![ThreadLocalMap弱引用](https://github.com/Shengliannan/JDK_Learning/blob/master/img/ThreadLocalMap%E5%BC%B1%E5%BC%95%E7%94%A8.png?raw=true)

ThreadLocalMap使用ThreadLocal的弱引用作为key，如果一个ThreadLocal没有外部强引用来引用它，那么系统 GC 的时候，这个ThreadLocal势必会被回收，这样一来，ThreadLocalMap中就会出现key为null的Entry，就没有办法访问这些key为null的Entry的value，如果当前线程再迟迟不结束的话，这些key为null的Entry的value就会一直存在一条强引用链：Thread Ref -> Thread -> ThreaLocalMap -> Entry -> value永远无法回收，造成内存泄漏。

总的来说就是，ThreadLocal里面使用了一个存在弱引用的map, map的类型是ThreadLocal.ThreadLocalMap.Map中的key为一个threadlocal实例。这个Map的确使用了弱引用，不过弱引用只是针对key。每个key都弱引用指向threadlocal。 当把threadlocal实例置为null以后，没有任何强引用指向threadlocal实例，所以threadlocal将会被gc回收。

但是，***我们的value却不能回收，而这块value永远不会被访问到了，所以存在着内存泄露***。因为存在一条从current thread连接过来的强引用。只有当前thread结束以后，current thread就不会存在栈中，强引用断开，Current Thread、Map value将全部被GC回收。最好的做法是将调用threadlocal的remove方法。


### final

final在多线程中的使用
如果一个类变量被final修饰，那么这个变量值就不能被修改，可是实现共享读



### AOP,Hibernate中使用ThreadLocal





### ThreadLocal的应用场景

- 在需要线程隔离的场合应用很广泛

