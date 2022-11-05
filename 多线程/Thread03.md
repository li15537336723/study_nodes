# ConcurrentHashMap

JDK 1.8 时，底层数据结构改变为采用数组 + 链表 + 红黑树的数据形式



1. **table**`volatile Node<K,V>[] table`:

装载Node的数组，作为ConcurrentHashMap的数据容器，采用懒加载的方式，直到第一次插入数据的时候才会进行初始化操作，数组的大小总是为2的幂次方。

2. **nextTable**`volatile Node<K,V>[] nextTable;`

扩容时使用，平时为null，只有在扩容的时候才为非null

3. **sizeCtl**`volatile int sizeCtl;`

该属性用来控制table数组的大小，根据是否初始化和是否正在扩容有几种情况：

- **当值为负数时：** 如果为-1表示正在初始化，如果为-N则表示当前正有N-1个线程进行扩容操作；
- **当值为正数时：** 如果当前数组为null的话表示table在初始化过程中，sizeCtl表示为需要新建数组的长度；
- 若已经初始化了，表示当前数据容器（table数组）可用容量也可以理解成临界值（插入节点数超过了该临界值就需要扩容），具体指为数组的长度n 乘以 加载因子loadFactor；
- 当值为0时，即数组长度为默认初始值。



**整体而言，为了解决线程安全的问题，ConcurrentHashMap 使用了 synchronized 和 CAS 的方式**



# ThreadLocal

**线程安全问题的核心在于多个线程会对同一个临界区共享资源进行操作**，让多个线程达到隔离的状态，这样就不会出现线程安全的问题

事实上，这就是一种“**空间换时间**”的方案，

从ThreadLocal **这个名词可以进行理解，表示线程的”本地变量“，即每个线程都拥有该变量副本，达到人手一份的效果，各用各的这样就可以避免共享资源的竞争**



## ThreadLocal 实现原理

`void set(T value)`

**set 方法设置当前线程中 threadLocal 变量的值**

```java
public void set(T value) {
	  //1. 获取当前线程实例对象
    Thread t = Thread.currentThread();

	  //2. 通过当前线程实例获取到ThreadLocalMap对象
    ThreadLocalMap map = getMap(t);

    if (map != null)
			//3. 如果Map不为null,则以当前threadLocl实例为key,值为value进行存入
    	map.set(this, value);
    else
			//4.map为null,则新建ThreadLocalMap并存入value
      createMap(t, value);
}
```

**数据 value 是真正的存放在了 Thread LocalMap 这个容器中了，并且是以当前 threadLocal 实例为 key**

ThreadLocalMap 的引用是作为 Thread 的一个成员变量，被 Thread 进行维护的

`careteMap` 是 new 一个ThreadLocalMap 实例对象，然后同样以当前 threadLocal 实例作为 key，值为 value 存放到 threadLocalMap 中，然后将当前线程对象的 threadLocals 赋值为 threadLocalMap

总结一下：**通过当前线程对象 thread 获取该 thread 所维护的 threadLocalMap，若 threadLocalMap 不为null，则以 threadLocal 实例为 key，值为 value 的键值对存入 threadLocalMap，若 threadLocalMap 为 null 的话，就新建 threadLocalMap 然后在以 threadLocal 为键，值为 value 的键值对存入即可**



> T get()

get 方法是获取当前线程中 threadLocal 变量的值

```java
public T get() {
	//1. 获取当前线程的实例对象
  Thread t = Thread.currentThread();

	//2. 获取当前线程的threadLocalMap
  ThreadLocalMap map = getMap(t);
  if (map != null) {
		//3. 获取map中当前threadLocal实例为key的值的entry
    ThreadLocalMap.Entry e = map.getEntry(this);

    if (e != null) {
      @SuppressWarnings("unchecked")
			//4. 当前entitiy不为null的话，就返回相应的值value
      T result = (T)e.value;
      return result;
    }
  }
	//5. 若map为null或者entry为null的话通过该方法初始化，并返回该方法返回的value
  return setInitialValue();
}
```

**流程总结：**通过当前线程 thread 实例获取到它所维护的 threadLocalMap，然后以当前 threadLocal 实例为 key 获取该map 中的键值对（entry），若 Entry 不为 null 则返回 Entry 的value。如果获取 threadLocalMap 为null或者 Entry 为 null 的话看，就以当前 threadLocal 为key，value 为 null 存入 map 后，并返回 null

## ThreadLocalMap

### Entry 数据结构

ThreadLocalMap 是 threadLocal 一个静态内部类，和大多数容器一样内部维护了一个数组，同样的threadLocalMap 内部维护了一个 Entry 类型的 table 数组

```java
private Entry[] table;
// 数组的长度为 2 的 幂次方
```

Entry

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

Entry 是一个以 ThreadLocal 为 key，Object 为 value 的键值对，另外需要注意的是这里的 threadLocal 是**弱引用，因为 Entry 继承了 weakReference，在 Entry 的构造方法中，调用了 super(k) 方法就会将 threadLocal 实例包装成一个 weakReference**

**set 方法**

threadLocalMap 是采用散列表进行实现的。如果出现散列冲突的情况，**ThreadLcoalMap 采用的是开放地址发来处理散列冲突**

> 开放地址法

开放地址法不会创建链表，当关键字散列到数组单元已经被另外一个关键字占用的时候，就会尝试在数组中寻找其他的单元，直到找到一个空的单元



## ThreadLocal 使用场景

**ThreadLocal 不是用来解决共享对象的多线程访问问题的**，数据实质是放在每个 thread 实例引用的 threadLocalMap ，也就是说 **每个不同的线程都拥有属于自己的数据容器，彼此不影响**

因此 threadLocal 只适用于 **共享独享会造成线程安全** 的业务场景



# BlockingQueue

阻塞队列被广泛用在 ”生产者-消费者“ 问题中，**当队列容器已满，生产者线程会被阻塞，直到队列未满；当队列容器为空时，消费者线程会被阻塞，直到队列非空时为止。**



## ArrayBlockingQueue

ArrayBlockingQueue 是由数组实现的有界阻塞队列，该队列命令元素FIFO（先进先出），ArrayBlockingQueue 一旦创建，容量不能改变

当队列容量满时，尝试将元素放入队列将导致操作阻塞，尝试从一个空队列中取一个元素也会同样阻塞

ArrayBlockingQueue默认情况下是不能保证线程访问队列的公平性

**1）put 方法详解**

`put(E e)` 源码如下

```java
public void put(E e) throws InterruptedException {
    checkNotNull(e);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
		//如果当前队列已满，将线程移入到notFull等待队列中
        while (count == items.length)
            notFull.await();
		//满足插入数据的要求，直接进行入队操作
        enqueue(e);
    } finally {
        lock.unlock();
    }
}
```

**2）take 方法详解**

toke 源码如下

```java
public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
		//如果队列为空，没有数据，将消费者线程移入等待队列中
        while (count == 0)
            notEmpty.await();
		//获取数据
        return dequeue();
    } finally {
        lock.unlock();
    }
}
```

take 方法主要做了两步

1. 如果当前队列为空的话，则将获取数据的消费者线程移入等待队列中
2. 若队列不为空则获取数据，即完成出队操作`dequeue`



## LinkedBlockingQueue

LinkedBlockingQueue 使用链表实现的有界阻塞队列，同样满足 FIFO 的特性，与 ArrayBlockingQueue 相比起来具有更高的吞吐量

LinkedBlockingQueue 的 put 与 take 方法与 ArrayBlockingQueue 实现思路基本上差不多

两者比较

**相同点**：都是通过 condition 通知机制来实现可阻塞式插入和删除元素，并满足线程安全的特性

**不同点：**

1. ArrayBlockingQueue 底层是采用数组进行实现，而 LinkedBlockingQueue 是采用链表数据结构
2. ArrayBlockingQueue 插入和删除数据，只采用了一个 lock，而 LinkedBlockingQueue 则是在插入和删除分别采用了 `pubLock` 和 `takeLock` 这样可以降低线程由于线程无法获取到 lock 而进入 Waiting 状态的可能性，从而提高了线程并发执行的效率



# 线程池

使用线程池主要有以下三个原因

1. 创建/销毁线程需要消耗系统资源，线程可以**复用自己创建的线程**
2. **控制并发的数量**，并发数量过多，可能会导致资源消耗过多，从而造成服务器崩溃
3. **可以对线程做同一管理**



## 线程池原理

Java 中的线程池顶层接口 `Executor` 接口，`ThreadPollExecutor` 是这个接口的实现类

**`ThreadPoolExecutor` **提供四个构造方法，参数 5——7个

- int corePoolSize：线程池中 **核心线程数最大值**
- int maximumPoolSize：该线程池中 **线程总数最大值**
- long keepAliveTime：**非核心线程闲置超时时长**
- TimeUnit unit：keepAliveTime 的单位
- BlockingQueue workQueue：阻塞队列，维护着 **等待执行的 Runnable 任务对象**
- ThreadFactory threadFactory
  - 创建线程的工厂，用于批量创建线程，同一在创建线程时设置一些参数，如是否守护线程，线程的优先级
- RejectedExecutionHandler handler
  - **拒绝处理策略**，线程数量大于最大线程数就会采用拒绝处理策略

### ThreadPoolExecutor 的策略

线程池本身有一个调度线程，这个线程就是用于管理布控整个线程池里的各种任务和事务，例如创建线程，销毁线程，任务队列管理等

线程池有自己的状态，使用了一些 `final int` 常量变量来标识线程池的状态，分别为 RUNNING，SHUTDOWN，STOP，TIDYING，TERMINATED

- 线程池创建后处于 **RUNNING** 状态
- 调用 shutdown() 方法后处于 **SHUTDOWN** 状态，线程不能接受新的任务，清楚一些空闲 worker，不会等待阻塞队列的任务完成
- 调用 shutdownNow()  方法后处于 **STOP** 状态 ，线程池不能接受新的任务，中断所有线程，阻塞队列中没有被执行的任务全部丢弃
- 当所有的任务已终止，ctl 记录的 “任务数量” 为0，线程池会变成 TIDYING 状态。接着会hi执行 terminated() 函数
- 线程池处在 TIDYING 状态时，**执行完 terminated()方法之后**，就会由 TIDYING -> TERMINATED，线程池被设置为 TERMINATED 状态



### 线程池主要的任务处理流程

处理任务的核心方法是 `execute`

在源码中，入队前进行了一次 `isRunning` 判断，入队之后，又进行了一次 `isRunning` 判断

**为什么要二次检查线程池的状态**

在多线程的环境下，线程池的状态是时刻发生变化的。很有可能刚获取到线程池状态后线程就改变了。判断是否将 `command` 加入 `workqueue` 是线程池之前的状态。倘若没有二次检查，万一线程池处于非 **RUNNING** 状态（在多线程环境下很有可能发生），那么 `command` 永远不会执行

**整个处理流程**

1. 线程总数量 < corePoolSize，无论线程是否空闲，都会新建一个核心线程执行任务（让核心线程数量快速达到 corePoolSize，在核心线程数量 < corePoolSize 时）。**注意，这一步需要获取全局锁**
2. 线程总数量 >= corePoolSize 时，新来的线程任务进入任务队列中等待，然后空闲的核心线程会一次去缓存队列中取任务来执行。（体现了 **线程复用**）
3. 当缓存队列满了，说明这个任务已经多到爆棚，需要一些“临时工”来执行这些任务了。于是会创建非核心线程去执行这个任务。**注意，这一步需要获得全局锁**
4. 缓存队列满了，且总线程数达到了 maximumPoolSize，则会采取上面提到的拒绝策略进行处理

![image-20221003155513414](https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20221003155513414.png)



### 线程池如何做到线程线程复用的

ThreadPoolExecutor 在创建线程时，会将线程封装成 **工作线程worker**，并放入 **工作线程组中**，然后这个worker 反复从阻塞队列中拿任务去执行。



### 四种常见的线程池

**newCachedThreadPool**

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

运行流程

1. 提交任务进线程池
2. 因为 corePoolSize 为 0 的关系，不创建核心线程，线程池最大为 Integer.MAX_VALUE
3. 尝试将任务添加到 SynchronousQueue 队列
4. 如果 SynchronousQueue 入队列成功，等待被当前运行的线程空闲后拉取执行。如果当前没有空闲线程，那么就创建一个非核心线程，然后从 SynchronousQueue 拉取任务并在当前线程执行。
5. 如果 SynchronousQueue 已有任务在等待，入列操作将会阻塞

**newFixedThreadPool**

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
}
```

核心线程数量和线程数量相等，都是传入的参数 nThreads，所以只能创建核心线程，不能创建非核心线程。因为 LinkedBlockingQueue 的默认大小是 Integer.MAX_VALUE，故如果核心线程空闲，则交给核心线程处理；如果核心线程不空闲，则入列等待，直到核心线程空闲





**newSingleThreadExecutor**

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

有且仅有一个核心线程，使用了 LinkedBlockingQueue ，所以，**不会创建非核心线程**。所有任务按照 **先来先执行** 的顺序执行。



**newScheduledThreadPool**

创建一个定长线程池，支持定时及周期性任务执行

```java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}

//ScheduledThreadPoolExecutor():
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE,
          DEFAULT_KEEPALIVE_MILLIS, MILLISECONDS,
          new DelayedWorkQueue());
}
```





