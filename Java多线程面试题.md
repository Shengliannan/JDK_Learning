# Java多线程面试题

## 一、为什么用线程池

有时候，系统需要处理非常多的执行时间很短的请求，如果每一个请求都开启一个新线程的话，系统叫不断的进行线程的创建和销毁，有时候花在创建和销毁线程上的时间会比线程真正执行的时间还长。

而且当线程太多了，系统不一定能受得了。

使用线程池主要是为了解决一下几个问题：

通过重用线程池中的线程，来减少每个线程创建和销毁的性能开销。

对线程进行一些维护和管理，比如定时开启，周期执行，并发控制等等。

## 二、线程池中的ThreadPoolExecutor参数

```java
ThradPoolExecutor参数：
ThreadPoolExecutor = new ThreadPoolExecutor(
	corePoolSize, //核心线程数
	keepAliveTime, //闲置线程存活时间
	TimeUnit.MILLISECODES, //时间单位
	new LinkBlockingDeque<Runnable>(), //线程队列
	Executor.defaultThreadFactory()， //线程工厂
	new AbortPolicy() //队列已满，而且当前线程数已经超过最大线程数
)


unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性：
TimeUnit.DAYS;               //天
TimeUnit.HOURS;             //小时
TimeUnit.MINUTES;           //分钟
TimeUnit.SECONDS;           //秒
TimeUnit.MILLISECONDS;      //毫秒
TimeUnit.MICROSECONDS;      //微妙
TimeUnit.NANOSECONDS;       //纳秒
```

### 2.1 ThreadPoolExecutor线程池参数的设置

1. ThreadPoolExecutor的重要参数

```java
corePoolSize：核心线程数
   
     （1）核心线程数会一直存活，即使没有任务需要执行
   
     （2）当线程数小于核心线程数时，即使有线程空闲，线程池也会优先创建新线程处理。
   
     （3）设置allowCoreThreadTimeout=true（默认false）时，核心线程会超时关闭
   
queueCapacity：任务队列容量（阻塞队列）
   
     当核心线程数达到最大时，新任务会放在队列中排队等待执行
   
maxPoolSize：最大线程数
   
     （1）当线程数>=corePoolSize，且任务队列已满时，线程池会创建新线程来处理任务。
   
     （2）当线程数=maxPoolSize,且任务队列已满时，线程池会拒绝处理任务而抛出异常。
   
keepAliveTime：线程空闲时间
   
     （1）当线程空闲时间达到keepAliveTime时,线程会退出，直到线程数量=corePoolSize
   
     （2）如果allowCoreThreadTimeout=true,则知道线程数量=0
   
allowCoreThreadTimeout：允许核心线程超时
   
rejectedExecutionHandler：任务拒绝处理器
   
两种情况会拒绝任务处理器：
   
     （1）当线程数已经达到maxPoolSize，且队列已满，会拒绝新任务
   
     （2）当线程池被调用shutdown()后，会等待线程池里的任务执行完毕，再shutdown。如果在调用shutdown()和线程池真正的shutdown之间提交任务，会拒绝新任务。
   
线程池会调用rejectedExecutionHandler来处理这个任务，如果没有设置默认是AbortPolicy，会抛出异常。
   
ThreadPoolExecutor类有几个内部类实现类来处理这类情况：
   
       （1）AbortPolicy丢弃任务，抛运行时异常
   
       （2）CallerRunsPolicy执行任务
   
       （3）DiscardPolicy忽视，什么都不会发生
   
       （4）DiscardOldestPolicy从队列中踢出最先进入队列（最后一个执行）的任务
   
实现RejectedExecutionHanlder，可自定义处理器
```
2. ThreadPoolExecutor执行顺序：

![线程池的处理流程](https://github.com/Shengliannan/JDK_Learning/blob/master/img/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B.png?raw=true)

```

    线程池按以下行为执行任务
    （1）当线程数小于核心线程数时，创建线程。
    （2）当线程数大于等于核心线程数，且任务队列未满时，将任务放入任务队列。
    （3）当线程数大于等于核心线程数，且任务队列已满。
    （4）若线程数小于最大线程数，创建线程。
    （5）若线程数等于最大线程数，抛出异常，拒绝任务。
```

3. LinkedBlockingQueue源码

   FIFO

   

```java
/** Current number of elements */
private final AtomicInteger count = new AtomicInteger();

/** Lock held by take, poll, etc */
private final ReentrantLock takeLock = new ReentrantLock();

/** Lock held by put, offer, etc */
private final ReentrantLock putLock = new ReentrantLock();

/**
  * Creates a {@code LinkedBlockingQueue} with a capacity of
  * {@link Integer#MAX_VALUE}.
  */
//默认为Integer.MAX_VALUE
    public LinkedBlockingQueue() {
        this(Integer.MAX_VALUE);
    }
```

4. AtomicInteger（原子操作类）

   ​        对于Java中的运算操作，例如自增或自减，若没有进行额外的同步操作，在多线程环境下就是线程不安全的。

   非阻塞同步

   同步：多线程并发访问共享数据时，保证共享数据在同一个时刻只被一个或一些线程使用。

   我们知道，阻塞同步和非阻塞同步都是实现线程安全的两个保障手段，***非阻塞同步相对于阻塞同步而言主要是解决了阻塞同步中线程阻塞和唤醒带来的性能问题***，那什么叫做非阻塞同步呢？在并发环境下，某个线程对共享变量先进行操作，如果没有其他线程争用数据那操作就成功；如果存在数据争用，那就采用补偿措施，比如不断的重试机制，直到成功为止，因为这种乐观的并发策略不需要把线程挂起，也就把这种同步操作称为非阻塞同步（操作和冲突检测具备原子性）。在硬件指令集的发展驱动下，使得“操作和冲突检测”这种看起来需要多次操作的行为只需要一条处理器指令便可以完成，这些指令就包括了非常著名的CAS指令（Compare-And-Set比较并交换）。

   

   

5. CAS机制（一种无锁技术）（V==A?V=B:V=V)

   CAS指令需要有3个操作数，分别是内存位置（在Java中可以简单的理解为变量的内存地址，用V表示）、旧的预期值（用A表示）和新值（用B表示）。CAS指令执行时，当且仅当V符合旧预期值A时，处理器用新值B更新V的值，否则它就不执行更新，但是***无论是否更新了V的值,都会返回V的旧值***，上述的处理过程是一个原子操作。

   在JDK1.5之后，Java程序中才可以使用CAS操作，该操作使用sun.misc.unsafe类里面的compareAndSwapInt()和compareAndSwapLong()等几个方法包装提供，虚拟机在内部对这些方法做了特殊处理，即时编译出来的结果就是一条平台相关的处理器CAS指令，没有方法调用的过程，或者可以认为无条件内联进去了。

   由于Unsafe类不是提供给用户程序调用的类(Unsafe.getUnsafe()代码中限制了只有启动类加载器(Bootstrap ClassLoader)加载的Class才能访问它)，因此，如果不采用反射手段，我们只能通过其他的Java API来间接使用它，如JUC包里面的整数原子类，其中的compareAndSet()和getAndIncrement()等方法都使用了Unsafe类的CAS操作。

6. ReentrantLock

7. AQS

   > [AQS详解](https://blog.csdn.net/mulinsen77/article/details/84583716)

## 三、 线程池的实现

## 四、消费者生产者实现

> [参考](https://www.cnblogs.com/chentingk/p/6497107.html)
>
> [参考2](https://www.cnblogs.com/fankongkong/p/7339848.html) 比较好