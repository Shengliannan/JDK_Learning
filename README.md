# JDK_Learning
JDK源码学习

## Java 比较地址

```
"=="
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

### Deque

全称：double ended queue

***

### LinkedList

LinkedList实现了Deque接口，Deque继承了Queue接口。

特点：双向链表，不同步，动态扩容。



#### 检索

- peek() 

检索，返回第一个元素，不删除，链表为空，返回null

- element()

检索，返回第一个元素，不删除，链表为空，抛出异常，NoSuchElementException

#### 插入

- add()

插入到尾部，插入成功，返回true，没有异常。

- offer()

插入到尾部，插入成功，返回true，没有异常。

#### 删除

- poll()

删除元素，返回第一个元素，链表为空，返回null

- pop()

删除元素，返回第一个元素，链表为空，抛出异常：java.util.NoSuchElementException

- remove()

删除元素，返回第一个元素，链表为空，抛出异常：java.util.NoSuchElementException





## HashMap

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



### equals()相同，hashcod()必相同；hashcode()相同，equals()不一定相同

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

### Integer 

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



#### 树专题

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

