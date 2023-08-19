# 创建线程的三种方式

**1、 为什么要重写 `run` 方法**

因为 `run` 方法使用来封装被线程执行的代码



**2、`run()` 方法和 `start()` 方法有什么区别**

- `run` 封装线程执行的代码，直接调用相当于普通方法
- `start` 启动线程，然后由 JVM 调用此线程的 `run()` 方法



**3、通过继承 Thread 的方法和实现 Runnable 接口的方式创建多线程，哪个好？**

实现 Runable 接口好，原因有两个

- 避免了 Java 单继承的局限性
- 适合多个相同的程序代码去处理统一资源的情况，把线程，代码和数据有效的分离，更符合面向对象的设计思想



**join()：** 等这个线程执行完成后才会轮到后续线程得到 cpu 的执行权

```java
//启动线程
t1.start();
try {
    t1.join(); //等待t1执行完才会轮到t2，t3抢
} catch (InterruptedException e) {
    e.printStackTrace();
}
t2.start();
t3.start();
```



# Java线程的6种状态

> 1. **初始(NEW)**：新创建了一个线程对象，但还没有调用start()方法。
> 2. **运行(RUNNABLE)**：Java线程中将就绪（ready）和运行中（running）两种状态笼统的称为“运行”。
>     线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）。
>
> 3. **阻塞(BLOCKED)**：表示线程阻塞于锁。
>
> 4. **等待(WAITING)**：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
>
> 5. **超时等待(TIMED_WAITING)**：该状态不同于WAITING，它可以在指定的时间后自行返回。
>
> 6. **终止(TERMINATED)**：表示该线程已经执行完毕。





### NEW

处于 NEW 状态的线程此时尚未启动。这里的尚未启动指还没有调用 Thread 实例的 start() 方法

```java
public void testStateNew() {
    Thread thread = new Thread(() -> {});
    System.out.println(thread.getState());	// 输出 NEW
}
```



### RUNNABLE 

表示当前线程正在运行中。处于 RUNNABLE 状态的线程在 Java虚拟机种运行，也有可能在等待 CPU 分配资源



### BLOCKED

阻塞状态。处于 BLOCKED 状态的线程正等待锁的释放以进入同步区



### WAITING

等待状态。处于等待状态的线程变成 RUNNABLE 状态需要其他线程唤醒

- Object.wait()：使当前线程处于等待状态直到另一个线程唤醒它
- Thread.join()：等待线程执行完毕，底层调用的是 Object 实例的 wait 方法
- LockSupport.park()：除非获得调用许可，否则禁用当前线程进行线程调度



### TIME_WAITING

超时等待状态。线程等待一个具体的时间，时间到后会被自动唤醒



### TERMINATED

终止状态。此时线程已经执行完毕



### WAITING状态与RUNNABLE状态的转换

使线程从 RUNNABLE 状态转为 WAITING 状态，主要介绍 `Object.wait()` 和 `Thread.join()`

`Object.wait()`

> 调用 wait() 方法之前必须持有对象的锁
>
> 线程调用 wait() 方法时，会释放当前的锁，直到有其他线程调用 notify()/notifyAll() 方法唤醒等待锁的线程

`Thread.join()`

> 调用 join() 方法，会一直等待这个线程执行完毕



### TIMED_WAITING与RUNNABLE状态转换

`Object.wait(long)`

> wait() 方法使线程进入 TIMED_WAITING 状态。这里的 wait(long) 方法与无参方法 wait() 相同的地方是，都可以通过其他线程调用 notify() 或 notifyAll() 方法来唤醒

`Thread.join(long)`

> join(long) 使当前线程执行指定时间，并且使线程进入 TIME_WAITING 状态







# 线程组和线程优先级

每个Thread 必然存在于一个 TreadGroup 中，Tread 不能独立于 ThreadGroup 存在

```java
public class Demo {
    public static void main(String[] args) {
        Thread testThread = new Thread(() -> {
            System.out.println("testThread当前线程组名字：" +
                    Thread.currentThread().getThreadGroup().getName());
            System.out.println("testThread线程名字：" +
                    Thread.currentThread().getName());
        });

        testThread.start();
  System.out.println("执行main所在线程的线程组名字： " + Thread.currentThread().getThreadGroup().getName());
        System.out.println("执行main方法线程名字：" + Thread.currentThread().getName());
    }
}

/*  执行结果   */
执行main所在线程的线程组名字： main
执行main方法线程名字：main
testThread当前线程组名字：main
testThread线程名字：Thread-0
```

ThreadGroup 管理着它下面的 Thread，ThreadGroup 是一个标准的**向下引用**的树状结构，这样设计的原因是 **防止”上级“线程被”下级“线程引用而无法有效的被 GC 回收**



## 线程的优先级

高优先级的线程将会比低优先级的线程有**更高的几率**得到执行

有了优先级，是不是就可以在业务实现时，采用这种方法来指定一些线程执行的先后顺序？答案是**不可以！！**

Java中的优先级来说不是特别的可靠，**Java 程序中对线程所设置的优先级只是给操作系统一个建议，操作系统不一定会采纳。而真正的调用顺序，是由操作系统的线程调度算法决定的**



当线程和线程组的优先级不一致的时候将会是怎样？

```java
public static void main(String[] args) {
    ThreadGroup threadGroup = new ThreadGroup("t1");
    threadGroup.setMaxPriority(6);
    Thread thread = new Thread(threadGroup,"thread");
    thread.setPriority(9);
    System.out.println("我是线程组的优先级"+threadGroup.getMaxPriority());
    System.out.println("我是线程的优先级"+thread.getPriority());
}

/*  输出结果  */
// 我是线程组的优先级6
// 我是线程的优先级6
```



# 进程与线程

进程：进程就是应用程序在内存中分配的空间，也就是正在运行的程序，各个进程之间互不干扰。**进程让操作系统的并发成为了可能**

**线程的提出**：**让一个线程执行一个子任务，这样一个进程就包含了多个线程，每个线程复制一个单独的子任务**

==进行让操作系统的并发成为了可能，而线程让进行内部并发成为了可能==



多进行的方式可以实现并发，为什么我们要使用多线程？

- 进程间的通信比较复杂，而线程间的通信比较简单，通常情况下，我们需要使用共享资源，这些资源在线程间的通信比较容易。
- 进程是重量级的，而线程是轻量级的，故多线程方式的系统开销更小



**进程与线程的区别**

本质区别是 **是否单独占有内存地址空间及其它系统资源（比如 I/O）**

另一个重要区别：**进程是操作系统进行资源分配的基本单位，而线程是操作系统进行调度的基本单位**



# 并发编程带来了哪些问题？

多线程需要的问题有三类：`线程安全问题`，`活跃性问题`，`性能问题`。

### 线程安全问题

**原子性：**举例银行转账问题

**原子操作概念: **即不会被线程调度机制打断的操作，没有上写文切换



**可见性**

指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。

为了解决可见性问题，Java 语言提供了 `volatile` 这个关键字。当一个共享变量被 volatile 修饰时，他会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它回去内存中读取新值。

当然 Java 的锁机制如 synchronize 和 lock 也是可以保证可见性的，加锁可以保证在同一时刻只有一个线程在执行同步代码块，释放锁之前会将变量刷回至主存，这样也就保证了可见性。



### 活跃性问题

活跃性是指某件正确的事情最终会发生，当某个操作无法继续下去的时候，就会发生活跃性的问题

活跃性的问题一般有 `死锁`，`活锁`，`饥饿问题` 这几类。

**（1）死锁**

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220922110417244.png" alt="image-20220922110417244" style="zoom:50%;" />

**（2）活锁**

当多个线程都在运行并且修改各自的状态，而其他线程批次依赖这个状态，导致任何一个线程都无法继续执行，只能重复着自身的动作和修改自身的状态，这种场景就是发生了活锁

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220922110557867.png" alt="image-20220922110557867" style="zoom:50%;" />

**（3）饥饿**

如果一个线程无其他异常却迟迟不能继续运行，那基本就是处于饥饿状态了。

常见的场景：

- 高优先级的线程一直在运行消耗 CPU，所有的低优先级线程一直处于等待



### 性能问题

`创建线程` 和 `上下文切换` 的开销会影响性能

创建线程是直接向系统申请资源的，对操作系统来说创建线程的代价是十分昂贵的，需要给它分配内存、列入调度等

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220922111208763.png" alt="image-20220922111208763" style="zoom:50%;" />



# Java内存模型 JMM

### 并发编程模型的两个关键问题

- 线程间如何通信？即：线程之间以何种机制来交换信息
- 线程间如何同步？即：线程以何种机制来控制不同线程间操作发生的相对顺序

有两种模型可以解决这两个问题：

- 消息传递并发模型
- 共享内存并发模型

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220922112011260.png" alt="image-20220922112011260" style="zoom:50%;" />

在Java中，使用的是共享内存的并发模型

**JMM可以保证：可见性，原子性，有序性**

### 并发编程的三个重要特性

1. 原子性：一个或者多个操作，要么全部都执行，不会受到任何因素干扰而中断；要么全部都不执行
2. 可见性：当一个变量对共享变量进行了修改，那么另外的线程都是立即可以看到修改后的最新值
3. 有序性：代码在执行的过程中先后有序。Java 在编译器及运行期间的优化，代码的执行顺序未必就是编写代码时候的顺序



### Java内存模型的抽象结构

<img src="https://lixianghong.oss-cn-beijing.aliyuncs.com/typore/image-20220922112420658.png" alt="image-20220922112420658" style="zoom:50%;" />

对每一个线程来说，栈都是私有的，而堆是公有的。也就是是说在栈中的变量不会在线程之间共享，也就不会有内存可见性的问题。堆中的变量是共享的，称为共享变量

因此，内存可见性是针对的 **共享变量**

### 既然堆是共享的，为什么在堆中会有内存不可见问题？

为了高效，往往会在告诉缓存区中缓存共享变量，因为 cpu 访问缓存区比访问内存要快得多

线程之间得通信由Java内存模型（JMM）控制，JMM定义了线程和主内存之间的抽象关系

<img src="C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20220922112902173.png" alt="image-20220922112902173" style="zoom: 33%;" />

从图中可以看出：

1. 所有共享变量都存在主内存中
2. 每个线程都保存了一份该线程使用到得共享变量得副本
3. 如果线程 A 与线程 B 之间要通信得话，必须经历下面 2 个步骤
   1. 线程 A 将本地内存中更新过的共享变量刷新到主内存中去
   2. 线程 B 到主内存中去读取线程 A 之前已经更新过得共享变量

**所以，线程A无法直接访问线程B的工作内存，线程之间通信必须经过主内存**

**线程对共享变量的所有操作都必须在自己的本地内存中进行，不能直接从主内存中读取**

**JMM 通过控制主内存与每个本地内存之间的交互，来提供内存可见性保证**



### JMM 与 Java 内存区域划分的区别与联系

- 区别

  两者是不同的概念层次，JMM 是抽象的，它是用来描述一组规则，通过这个规则来控制各个变量的访问方式，围绕原子性，有序性，可见性等展开的。而 Java 运行时内存的划分是具体的，是 JVM 运行 Java 程序时，必要的内存划分

- 联系

  都存在私有数据区域和共享数据区域。一般来说，JMM 中的主内存属于共享区域，他是包含了堆和方法区，同样，JMM 中的本地内存属于私有数据区域，包含了程序计数器，本地方法栈，虚拟机栈



### 什么是重排序？

计算机在执行程序时，为了提高性能，编译器和处理器常常会对指令做从排序

**指令重排序提高 CPU 处理性能十分必要，虽然由此带来了乱序的问题，到那时这点牺牲是值得的**

指令重排序一般分为以下三种：

- 编译器优化重排
- 指令并行重排
- 内存系统重排

**指令重排序可以保证串行语义一致，但是没有义务保证多线程的语义也一致**



## happens-before

什么是 happens-before？

一方面，程序员需要 JMM 提供一个强的内存模型来编写代码；另一方面，编译器和处理器希望 JMM 对它门的束缚越少越好，这样它们就可以最可能多的做优化来提高性能

JMM 考虑了这两种需求，对编译器和处理器来说，**只要不改变程序的执行结果，编译器和处理器怎么优化都行**

happens-before 关系定义如下：

1. 如果一个操作 happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前
2. **两个操作之间存在 happens-before 关系，并不意味和 Java 平台的具体实现必须按照 happens-before 关系指定的顺序来执行。如果重排序之后的执行结果，与按 happens-before 关系来执行的结果一致，那么 JMM 也允许这样的重排序**

**如果操作 A happens-before 操作B，那么操作 A 在内存上所做的操作对操作 B 都是可见的，不管他们在不在一个线程**





















































