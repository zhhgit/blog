




3、synchronized 实现原理？
synchronized 可以保证方法或者代码块在运行时，同一时刻只有一个进程可以访问，同时它还可以保证共享变量的内存可见性。

Java 中每一个对象都可以作为锁，这是 synchronized 实现同步的基础：

普通同步方法，锁是当前实例对象

静态同步方法，锁是当前类的 class 对象

同步方法块，锁是括号里面的对象

同步代码块：monitorenter 指令插入到同步代码块的开始位置，monitorexit指令插入到同步代码块的结束位置，JVM 需要保证每一个monitorenter都有一个monitorexit与之相对应。任何对象都有一个 Monitor 与之相关联，当且一个 Monitor 被持有之后，他将处于锁定状态。线程执行到monitorenter 指令时，将会尝试获取对象所对应的 Monitor 所有权，即尝试获取对象的锁。

同步方法：synchronized 方法则会被翻译成普通的方法调用和返回指令如：invokevirtual、areturn指令，在 VM 字节码层面并没有任何特别的指令来实现被synchronized修饰的方法，而是在 Class 文件的方法表中将该方法的access_flags字段中的synchronized 标志位置设置为 1，表示该方法是同步方法，并使用调用该方法的对象或该方法所属的 Class 在 JVM 的内部对象表示 Klass 作为锁对象。

synchronized 是重量级锁，在 JDK1.6 中进行优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销。

相关文章参考

死磕Synchronized底层实现--概论

死磕Synchronized底层实现--偏向锁

死磕Synchronized底层实现--轻量级锁

死磕Synchronized底层实现--重量级锁
4、volatile 的实现原理？
volatile 是轻量级的锁，它不会引起线程上下文的切换和调度。

volatile可见性：对一个volatile 的读，总可以看到对这个变量最终的写。

volatile 原子性：volatile对单个读 / 写具有原子性（32 位 Long、Double），但是复合操作除外，例如i++ 。

JVM 底层采用“内存屏障”来实现 volatile 语义，防止指令重排序。

volatile 经常用于两个两个场景：状态标记变量、Double Check 。

相关文章参考
Java并发编程：volatile关键字解析

5、Java 内存模型（JMM）
JMM 规定了线程的工作内存和主内存的交互关系，以及线程之间的可见性和程序的执行顺序。

一方面，要为程序员提供足够强的内存可见性保证。

另一方面，对编译器和处理器的限制要尽可能地放松。JMM 对程序员屏蔽了 CPU 以及 OS 内存的使用问题，能够使程序在不同的 CPU 和 OS 内存上都能够达到预期的效果。

Java 采用内存共享的模式来实现线程之间的通信。编译器和处理器可以对程序进行重排序优化处理，但是需要遵守一些规则，不能随意重排序。

在并发编程模式中，势必会遇到上面三个概念：
原子性：一个操作或者多个操作要么全部执行要么全部不执行。

可见性：当多个线程同时访问一个共享变量时，如果其中某个线程更改了该共享变量，其他线程应该可以立刻看到这个改变。

有序性：程序的执行要按照代码的先后顺序执行。

通过 volatile、synchronized、final、concurrent 包等 实现。

相关文章参考
深入理解 Java 虚拟机【1】JVM 内存模型

6、有关队列 AQS 队列同步器
AQS 是构建锁或者其他同步组件的基础框架（如 ReentrantLock、ReentrantReadWriteLock、Semaphore 等）, 包含了实现同步器的细节（获取同步状态、FIFO 同步队列）。AQS 的主要使用方式是继承，子类通过继承同步器，并实现它的抽象方法来管理同步状态。

维护一个同步状态 state。当 state > 0时，表示已经获取了锁；当state = 0 时，表示释放了锁。

AQS 通过内置的 FIFO 同步队列来完成资源获取线程的排队工作：

如果当前线程获取同步状态失败（锁）时，AQS 则会将当前线程以及等待状态等信息构造成一个节点（Node）并将其加入同步队列，同时会阻塞当前线程

当同步状态释放时，则会把节点中的线程唤醒，使其再次尝试获取同步状态。

AQS 内部维护的是** CLH 双向同步队列**

相关文章参考
AbstractQueuedSynchronizer源码分析之条件队列

7、锁的特性
可重入锁：指的是在一个线程中可以多次获取同一把锁。 ReentrantLock 和 synchronized 都是可重入锁。

可中断锁：顾名思义，就是可以相应中断的锁。synchronized 就不是可中断锁，而 Lock 是可中断锁。

公平锁：即尽量以请求锁的顺序来获取锁。synchronized 是非公平锁，ReentrantLock 和 ReentrantReadWriteLock，它默认情况下是非公平锁，但是可以设置为公平锁。

相关文章参考

Java多线程编程 — 锁优化

并发编程之死锁解析

Java读写锁实现原理

8、ReentrantLock 锁
ReentrantLock，可重入锁，是一种递归无阻塞的同步机制。它可以等同于 synchronized的使用，但是 ReentrantLock 提供了比synchronized 更强大、灵活的锁机制，可以减少死锁发生的概率。

ReentrantLock 实现 Lock 接口，基于内部的 Sync 实现。

Sync 实现 AQS ，提供了 FairSync 和 NonFairSync 两种实现。

Condition
Condition 和 Lock 一起使用以实现等待/通知模式，通过 await()和singnal() 来阻塞和唤醒线程。

Condition 是一种广义上的条件队列。他为线程提供了一种更为灵活的等待 / 通知模式，线程在调用 await 方法后执行挂起操作，直到线程等待的某个条件为真时才会被唤醒。Condition 必须要配合 Lock 一起使用，因为对共享状态变量的访问发生在多线程环境下。一个 Condition 的实例必须与一个 Lock 绑定，因此 Condition 一般都是作为 Lock 的内部实现。

相关文章参考

ReentrantLock源码分析

9、ReentrantReadWriteLock
读写锁维护着一对锁，一个读锁和一个写锁。通过分离读锁和写锁，使得并发性比一般的排他锁有了较大的提升：

在同一时间，可以允许多个读线程同时访问。

但是，在写线程访问时，所有读线程和写线程都会被阻塞。

读写锁的主要特性：

公平性：支持公平性和非公平性。

重入性：支持重入。读写锁最多支持 65535 个递归写入锁和 65535 个递归读取锁。

锁降级：遵循获取写锁，再获取读锁，最后释放写锁的次序，如此写锁能够降级成为读锁。

ReentrantReadWriteLock 实现 ReadWriteLock 接口，可重入的读写锁实现类。

在同步状态上，为了表示两把锁，将一个 32 位整型分为高 16 位和低 16 位，分别表示读和写的状态

10、Synchronized 和 Lock 的区别
Lock 是一个接口，而 synchronized 是 Java 中的关键字，synchronized 是内置的语言实现；

synchronized 在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而 Lock 在发生异常时，如果没有主动通过 unLock() 去释放锁，则很可能造成死锁现象，因此使用 Lock 时需要在 finally 块中释放锁；

Lock 可以让等待锁的线程响应中断，而 synchronized 却不行，使用 synchronized 时，- 等待的线程会一直等待下去，不能够响应中断；

通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到。

Lock 可以提高多个线程进行读操作的效率。

更深的：

与 synchronized 相比，ReentrantLock 提供了更多，更加全面的功能，具备更强的扩展性。例如：时间锁等候，可中断锁等候，锁投票。

ReentrantLock 还提供了条件 Condition ，对线程的等待、唤醒操作更加详细和灵活，所以在多个条件变量和高度竞争锁的地方，ReentrantLock 更加适合（以后会阐述 Condition）。

ReentrantLock 提供了可轮询的锁请求。它会尝试着去获取锁，如果成功则继续，否则可以等到下次运行时处理，而 synchronized则一旦进入锁请求要么成功要么阻塞，所以相比synchronized 而言，ReentrantLock 会不容易产生死锁些。

ReentrantLock 支持更加灵活的同步代码块，但是使用 synchronized时，只能在同一个synchronized块结构中获取和释放。注意，ReentrantLock 的锁释放一定要在finally 中处理，否则可能会产生严重的后果。

ReentrantLock 支持中断处理，且性能较 synchronized 会好些。

11、Java 中线程同步的方式
sychronized 同步方法或代码块

volatile

Lock

ThreadLocal

阻塞队列（LinkedBlockingQueue）

使用原子变量（java.util.concurrent.atomic）

变量的不可变性



相关文章参考

Java并发编程：同步容器

java实现同步的几种方式（总结）

12、CAS 是一种什么样的同步机制？多线程下为什么不使用 int 而使用 AtomicInteger？
Compare And Swap，比较交换。可以看到 synchronized 可以保证代码块原子性，很多时候会引起性能问题，volatile也是个不错的选择，但是volatile 不能保证原子性，只能在某些场合下使用。所以可以通过 CAS 来进行同步，保证原子性。

我们在读 Concurrent 包下的类的源码时，发现无论是 ReentrantLock 内部的 AQS，还是各种 Atomic 开头的原子类，内部都应用到了 CAS。

在 CAS 中有三个参数：内存值 V、旧的预期值 A、要更新的值 B ，当且仅当内存值 V 的值等于旧的预期值 A 时，才会将内存值 V 的值修改为 B，否则什么都不干。其伪代码如下：

if (this.value == A) {
this.value = B
return true;
} else {
return false;
}
CAS 可以保证一次的读-改-写操作是原子操作。

在多线程环境下，int 类型的自增操作不是原子的，线程不安全，可以使用 AtomicInteger 代替。

// AtomicInteger.java
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;
static {
try {
valueOffset = unsafe.objectFieldOffset
(AtomicInteger.class.getDeclaredField("value"));
} catch (Exception ex) { throw new Error(ex); }
}
private volatile int value;


Unsafe 是 CAS 的核心类，Java 无法直接访问底层操作系统，而是通过本地 native` 方法来访问。不过尽管如此，JVM 还是开了一个后门：Unsafe ，它提供了硬件级别的原子操作。

valueOffset 为变量值在内存中的偏移地址，Unsafe 就是通过偏移地址来得到数据的原值的。

value当前值，使用volatile 修饰，保证多线程环境下看见的是同一个。



// AtomicInteger.java
public final int addAndGet(int delta) {
return unsafe.getAndAddInt(this, valueOffset, delta) + delta;
}

// Unsafe.java
// compareAndSwapInt（var1, var2, var5, var5 + var4）其实换成 compareAndSwapInt（obj, offset, expect, update）比较清楚，意思就是如果 obj 内的 value 和 expect 相等，就证明没有其他线程改变过这个变量，那么就更新它为 update，如果这一步的 CAS 没有成功，那就采用自旋的方式继续进行 CAS 操作，取出乍一看这也是两个步骤了啊，其实在 JNI 里是借助于一个 CPU 指令完成的。所以还是原子操作。
public final int getAndAddInt(Object var1, long var2, int var4) {
int var5;
do {
var5 = this.getIntVolatile(var1, var2);
} while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
return var5;
}
// 该方法为本地方法，有四个参数，分别代表：对象、对象的地址、预期值、修改值
public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);
13、HashMap 是不是线程安全？如何体现？如何变得安全？
由于添加元素到 map 中去时，数据量大产生扩容操作，多线程会导致 HashMap 的 node 链表形成环状的数据结构产生死循环。所以 HashMap 是线程不安全的。

如何变得安全：
Hashtable：通过 synchronized 来保证线程安全的，独占锁，悲观策略。吞吐量较低，性能较为低下

SynchronizedHashMap ：通过 Collections.synchronizedMap() 方法对 HashMap 进行包装，返回一个 SynchronizedHashMap 对象，在源码中 SynchronizedHashMap 也是用过 synchronized 来保证线程安全的。但是实现方式和 Hashtable 略有不同（前者是 synchronized 方法，后者是通过 synchronized 对互斥变量加锁实现）

ConcurrentHashMap：JUC 中的线程安全容器，高效并发。ConcurrentHashMap 的 key、value 都不允许为 null。



相关文章参考

HashMap实现原理浅析

Java中HashMap底层数据结构

集合系列—HashMap源码分析

14、ConcurrentHashMap 的实现方式？
ConcurrentHashMap 的实现方式和 Hashtable 不同，不采用独占锁的形式，更高效，其中在 jdk1.7 和 jdk1.8 中实现的方式也略有不同。

Jdk1.7 中采用分段锁和 HashEntry 使锁更加细化。ConcurrentHashMap 采用了分段锁技术，其中 Segment 继承于 ReentrantLock。不会像 HashTable 那样不管是 put 还是 get 操作都需要做同步处理，理论上 ConcurrentHashMap 支持 CurrencyLevel (Segment 数组数量）的线程并发。

Jdk1.8 利用 CAS+Synchronized 来保证并发更新的安全，当然底层采用数组+链表+红黑树的存储结构。

table 中存放 Node 节点数据，默认 Node 数据大小为 16，扩容大小总是 2^N。

为了保证可见性，Node 节点中的 val 和 next 节点都用 volatile 修饰。

当链表长度大于 8 时，会转换成红黑树，节点会被包装成 TreeNode放在TreeBin 中。

put()：1. 计算键所对应的 hash 值；2. 如果哈希表还未初始化，调用 initTable() 初始化，否则在 table 中找到 index 位置，并通过 CAS 添加节点。如果链表节点数目超过 8，则将链表转换为红黑树。如果节点总数超过，则进行扩容操作。

get()：无需加锁，直接根据 key 的 hash 值遍历 node。



相关文章参考

Java并发系列 | ConcurrentHashMap源码分析

15、CountDownLatch 和 CyclicBarrier 的区别？ 并发工具类
CyclicBarrier 它允许一组线程互相等待，直到到达某个公共屏障点 (Common Barrier Point)。在涉及一组固定大小的线程的程序中，这些线程必须不时地互相等待，此时 CyclicBarrier 很有用。因为该 Barrier 在释放等待线程后可以重用，所以称它为循环 ( Cyclic ) 的 屏障 ( Barrier ) 。

每个线程调用 #await() 方法，告诉 CyclicBarrier 我已经到达了屏障，然后当前线程被阻塞。当所有线程都到达了屏障，结束阻塞，所有线程可继续执行后续逻辑。

CountDownLatch 能够使一个线程在等待另外一些线程完成各自工作之后，再继续执行。使用一个计数器进行实现。计数器初始值为线程的数量。当每一个线程完成自己任务后，计数器的值就会减一。当计数器的值为 0 时，表示所有的线程都已经完成了任务，然后在 CountDownLatch 上等待的线程就可以恢复执行任务。

两者区别：
CountDownLatch 的作用是允许 1 或 N 个线程等待其他线程完成执行；而 CyclicBarrier 则是允许 N 个线程相互等待。

CountDownLatch 的计数器无法被重置；CyclicBarrier 的计数器可以被重置后使用，因此它被称为是循环的 barrier 。

Semaphore 是一个控制访问多个共享资源的计数器，和 CountDownLatch 一样，其本质上是一个“共享锁”。一个计数信号量。从概念上讲，信号量维护了一个许可集。

如有必要，在许可可用前会阻塞每一个 acquire，然后再获取该许可。

每个 release 添加一个许可，从而可能释放一个正在阻塞的获取者。


相关文章参考

Java并发系列 | CountDownLatch源码分析

16、怎么控制线程，尽可能减少上下文切换？
减少上下文切换的方法有无锁并发编程、CAS算法、使用最少线程和使用协程。

无锁并发编程。多线程竞争锁时，会引起上下文切换，所以多线程处理数据时，可以使用一些方法来避免使用锁。如将数据的ID按照Hash算法取模分段，不同的线程处理不同段的数据。

CAS算法。Java的Atomic包使用CAS算法来更新数据，而不需要加锁。

使用最少线程。避免创建不需要的线程，比如任务很少，但是创建了很多线程来处理，这样会造成大量线程都处于等待状态。

协程。在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。

17、什么是乐观锁和悲观锁？
像 synchronized这种独占锁属于悲观锁，它是在假设一定会发生冲突的，那么加锁恰好有用，除此之外，还有乐观锁，乐观锁的含义就是假设没有发生冲突，那么我正好可以进行某项操作，如果要是发生冲突呢，那我就重试直到成功，乐观锁最常见的就是CAS。

18、阻塞队列
阻塞队列实现了 BlockingQueue 接口，并且有多组处理方法。

抛出异常：add(e) 、remove()、element()
返回特殊值：offer(e) 、pool()、peek()
阻塞：put(e) 、take()

JDK 8 中提供了七个阻塞队列可供使用：

ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。

LinkedBlockingQueue ：一个由链表结构组成的无界阻塞队列。

PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。

DelayQueue：一个使用优先级队列实现的无界阻塞队列。

SynchronousQueue：一个不存储元素的阻塞队列。

LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。

LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

ArrayBlockingQueue，一个由数组实现的有界阻塞队列。该队列采用 FIFO 的原则对元素进行排序添加的。内部使用可重入锁 ReentrantLock + Condition 来完成多线程环境的并发操作。

相关文章参考
阻塞队列 BlockingQueue

Java并发编程：阻塞队列

19、线程池
线程池有五种状态：RUNNING, SHUTDOWN, STOP, TIDYING, TERMINATED。

RUNNING：接收并处理任务。

SHUTDOWN：不接收但处理现有任务。

STOP：不接收也不处理任务，同时终端当前处理的任务。

TIDYING：所有任务终止，线程池会变为 TIDYING 状态。当线程池变为 TIDYING 状态时，会执行钩子函数 terminated()。

TERMINATED：线程池彻底终止的状态。

内部变量** ctl **定义为 AtomicInteger ，记录了“线程池中的任务数量”和“线程池的状态”两个信息。共 32 位，其中高 3 位表示”线程池状态”，低 29 位表示”线程池中的任务数量”。

线程池创建参数
corePoolSize
线程池中核心线程的数量。当提交一个任务时，线程池会新建一个线程来执行任务，直到当前线程数等于 corePoolSize。如果调用了线程池的 prestartAllCoreThreads() 方法，线程池会提前创建并启动所有基本线程。

maximumPoolSize
线程池中允许的最大线程数。线程池的阻塞队列满了之后，如果还有任务提交，如果当前的线程数小于 maximumPoolSize，则会新建线程来执行任务。注意，如果使用的是无界队列，该参数也就没有什么效果了。

keepAliveTime
线程空闲的时间。线程的创建和销毁是需要代价的。线程执行完任务后不会立即销毁，而是继续存活一段时间：keepAliveTime。默认情况下，该参数只有在线程数大于 corePoolSize 时才会生效。

unit
keepAliveTime 的单位。TimeUnit

workQueue
用来保存等待执行的任务的阻塞队列，等待的任务必须实现 Runnable 接口。我们可以选择如下几种：

ArrayBlockingQueue：基于数组结构的有界阻塞队列，FIFO。

LinkedBlockingQueue：基于链表结构的有界阻塞队列，FIFO。

SynchronousQueue：不存储元素的阻塞队列，每个插入操作都必须等待一个移出操作，反之亦然。

PriorityBlockingQueue：具有优先界别的阻塞队列。

threadFactory
用于设置创建线程的工厂。该对象可以通过 Executors.defaultThreadFactory()。他是通过 newThread() 方法提供创建线程的功能，newThread() 方法创建的线程都是“非守护线程”而且“线程优先级都是 Thread.NORM_PRIORITY”。

handler
RejectedExecutionHandler，线程池的拒绝策略。所谓拒绝策略，是指将任务添加到线程池中时，线程池拒绝该任务所采取的相应策略。当向线程池中提交任务时，如果此时线程池中的线程已经饱和了，而且阻塞队列也已经满了，则线程池会选择一种拒绝策略来处理该任务。

线程池提供了四种拒绝策略：
AbortPolicy：直接抛出异常，默认策略；

CallerRunsPolicy：用调用者所在的线程来执行任务；

DiscardOldestPolicy：丢弃阻塞队列中靠最前的任务，并执行当前任务；

DiscardPolicy：直接丢弃任务；

当然我们也可以实现自己的拒绝策略，例如记录日志等等，实现 RejectedExecutionHandler 接口即可。

当添加新的任务到线程池时：
线程数量未达到 corePoolSize，则新建一个线程（核心线程）执行任务

线程数量达到了 corePoolSize，则将任务移入队列等待

队列已满，新建线程（非核心线程）执行任务

队列已满，总线程数又达到了 maximumPoolSize，就会由 handler 的拒绝策略来处理

线程池可通过 Executor 框架来进行创建：
FixedThreadPool
public static ExecutorService newFixedThreadPool(int nThreads) {
return new ThreadPoolExecutor(nThreads, nThreads,
0L, TimeUnit.MILLISECONDS,
new LinkedBlockingQueue<Runnable>());
}
corePoolSize 和 maximumPoolSize 都设置为创建 FixedThreadPool 时指定的参数 nThreads，意味着当线程池满时且阻塞队列也已经满时，如果继续提交任务，则会直接走拒绝策略，该线程池不会再新建线程来执行任务，而是直接走拒绝策略。FixedThreadPool 使用的是默认的拒绝策略，即 AbortPolicy，则直接抛出异常。

但是 workQueue 使用了无界的 LinkedBlockingQueue, 那么当任务数量超过 corePoolSize 后，全都会添加到队列中而不执行拒绝策略。

SingleThreadExecutor
public static ExecutorService newSingleThreadExecutor() {
return new FinalizableDelegatedExecutorService
(new ThreadPoolExecutor(1, 1,
0L, TimeUnit.MILLISECONDS,
new LinkedBlockingQueue<Runnable>()));
}
作为单一 worker 线程的线程池，SingleThreadExecutor 把 corePool 和 maximumPoolSize 均被设置为 1，和 FixedThreadPool 一样使用的是无界队列 LinkedBlockingQueue, 所以带来的影响和 FixedThreadPool 一样。

CachedThreadPool
CachedThreadPool是一个会根据需要创建新线程的线程池 ，他定义如下：

public static ExecutorService newCachedThreadPool() {
return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
60L, TimeUnit.SECONDS,
new SynchronousQueue<Runnable>());
}
这个线程池，当任务提交是就会创建线程去执行,执行完成后线程会空闲60s,之后就会销毁。但是如果主线程提交任务的速度远远大于 CachedThreadPool 的处理速度，则 CachedThreadPool 会不断地创建新线程来执行任务，这样有可能会导致系统耗尽 CPU 和内存资源，所以在使用该线程池是，一定要注意控制并发的任务数，否则创建大量的线程可能导致严重的性能问题。

相关文章参考

JAVA线程池原理详解（1）

JAVA线程池原理详解（2）

Java多线程和线程池

Java线程池总结

20、为什么要使用线程池？
创建/销毁线程伴随着系统开销，过于频繁的创建/销毁线程，会很大程度上影响处理效率。线程池缓存线程，可用已有的闲置线程来执行新任务(keepAliveTime)

线程并发数量过多，抢占系统资源从而导致阻塞。运用线程池能有效的控制线程最大并发数，避免以上的问题。

对线程进行一些简单的管理(延时执行、定时循环执行的策略等)

21、生产者消费者问题
实例代码用 Object 的 wait()和notify() 实现，也可用 ReentrantLock 和 Condition 来完成。或者直接使用阻塞队列。

public class ProducerConsumer {
public static void main(String[] args) {
ProducerConsumer main = new ProducerConsumer();
Queue<Integer> buffer = new LinkedList<>();
int maxSize = 5;
new Thread(main.new Producer(buffer, maxSize), "Producer1").start();
new Thread(main.new Consumer(buffer, maxSize), "Comsumer1").start();
new Thread(main.new Consumer(buffer, maxSize), "Comsumer2").start();
}

    class Producer implements Runnable {
        private Queue<Integer> queue;
        private int maxSize;

        Producer(Queue<Integer> queue, int maxSize) {
            this.queue = queue;
            this.maxSize = maxSize;
        }

        @Override
        public void run() {
            while (true) {
                synchronized (queue) {
                    while (queue.size() == maxSize) {
                        try {
                            System.out.println("Queue is full");
                            queue.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    Random random = new Random();
                    int i = random.nextInt();
                    System.out.println(Thread.currentThread().getName() + " Producing value : " + i);
                    queue.add(i);
                    queue.notifyAll();
                }
            }
        }
    }

    class Consumer implements Runnable {
        private Queue<Integer> queue;
        private int maxSize;

        public Consumer(Queue<Integer> queue, int maxSize) {
            super();
            this.queue = queue;
            this.maxSize = maxSize;
        }

        @Override
        public void run() {
            while (true) {
                synchronized (queue) {
                    while (queue.isEmpty()) {
                        try {
                            System.out.println("Queue is empty");
                            queue.wait();
                        } catch (Exception ex) {
                            ex.printStackTrace();
                        }
                    }
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + " Consuming value : " + queue.remove());
                    queue.notifyAll();
                }
            }
        }
    }
}








【127期】​​面试官：你说使用过ZooKeeper，那来说说他的基本原理吧
阿凡卢 Java面试题精选 2020-07-11
点击上方“Java面试题精选”，关注公众号

面试刷图，查缺补漏

>>号外：往期面试题，10篇为一个单位归置到本公众号菜单栏->面试题，有需要的欢迎翻阅

阶段汇总集合：++小Flag实现，一百期面试题汇总++

ZooKeeper简介
ZooKeeper是一个开放源码的分布式应用程序协调服务，它包含一个简单的原语集，分布式应用程序可以基于它实现同步服务，配置维护和命名服务等。

图片

ZooKeeper设计目的
最终一致性：client不论连接到哪个Server，展示给它都是同一个视图，这是zookeeper最重要的性能。

可靠性：具有简单、健壮、良好的性能，如果消息m被到一台服务器接受，那么它将被所有的服务器接受。

实时性：Zookeeper保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。但由于网络延时等原因，Zookeeper不能保证两个客户端能同时得到刚更新的数据，如果需要最新数据，应该在读数据之前调用sync()接口。

等待无关（wait-free）：慢的或者失效的client不得干预快速的client的请求，使得每个client都能有效的等待。

原子性：更新只能成功或者失败，没有中间状态。

顺序性：包括全局有序和偏序两种：全局有序是指如果在一台服务器上消息a在消息b前发布，则在所有Server上消息a都将在消息b前被发布；偏序是指如果一个消息b在消息a后被同一个发送者发布，a必将排在b前面。

ZooKeeper数据模型
Zookeeper会维护一个具有层次关系的数据结构，它非常类似于一个标准的文件系统，如图所示：

图片

Zookeeper这种数据结构有如下这些特点：

1）每个子目录项如NameService都被称作为znode，这个znode是被它所在的路径唯一标识，如Server1这个znode的标识为/NameService/Server1。

2）znode可以有子节点目录，并且每个znode可以存储数据，注意EPHEMERAL（临时的）类型的目录节点不能有子节点目录。

3）znode是有版本的（version），每个znode中存储的数据可以有多个版本，也就是一个访问路径中可以存储多份数据，version号自动增加。

4）znode的类型：

Persistent 节点，一旦被创建，便不会意外丢失，即使服务器全部重启也依然存在。每个 Persist 节点即可包含数据，也可包含子节点。

Ephemeral 节点，在创建它的客户端与服务器间的 Session 结束时自动被删除。服务器重启会导致 Session 结束，因此 Ephemeral 类型的 znode 此时也会自动删除。

Non-sequence 节点，多个客户端同时创建同一 Non-sequence 节点时，只有一个可创建成功，其它匀失败。并且创建出的节点名称与创建时指定的节点名完全一样。

Sequence 节点，创建出的节点名在指定的名称之后带有10位10进制数的序号。多个客户端创建同一名称的节点时，都能创建成功，只是序号不同。

5）znode可以被监控，包括这个目录节点中存储的数据的修改，子节点目录的变化等，一旦变化可以通知设置监控的客户端，这个是Zookeeper的核心特性，Zookeeper的很多功能都是基于这个特性实现的。

6）ZXID：每次对Zookeeper的状态的改变都会产生一个zxid（ZooKeeper Transaction Id），zxid是全局有序的，如果zxid1小于zxid2，则zxid1在zxid2之前发生。

ZooKeeper Session
Client和Zookeeper集群建立连接，整个session状态变化如图所示：

图片

如果Client因为Timeout和Zookeeper Server失去连接，client处在CONNECTING状态，会自动尝试再去连接Server，如果在session有效期内再次成功连接到某个Server，则回到CONNECTED状态。

注意：如果因为网络状态不好，client和Server失去联系，client会停留在当前状态，会尝试主动再次连接Zookeeper Server。client不能宣称自己的session expired，session expired是由Zookeeper Server来决定的，client可以选择自己主动关闭session。

ZooKeeper Watch
Zookeeper watch是一种监听通知机制。Zookeeper所有的读操作getData(), getChildren()和 exists()都可以设置监视(watch)，监视事件可以理解为一次性的触发器

官方定义如下：

a watch event is one-time trigger, sent to the client that set the watch, whichoccurs when the data for which the watch was set changes。

Watch的三个关键点：

（一次性触发）One-time trigger

当设置监视的数据发生改变时，该监视事件会被发送到客户端，例如，如果客户端调用了getData("/znode1", true) 并且稍后 /znode1 节点上的数据发生了改变或者被删除了，客户端将会获取到 /znode1 发生变化的监视事件，而如果 /znode1 再一次发生了变化，除非客户端再次对/znode1 设置监视，否则客户端不会收到事件通知。

（发送至客户端）Sent to the client

Zookeeper客户端和服务端是通过 socket 进行通信的，由于网络存在故障，所以监视事件很有可能不会成功地到达客户端，监视事件是异步发送至监视者的，Zookeeper 本身提供了顺序保证(ordering guarantee)：即客户端只有首先看到了监视事件后，才会感知到它所设置监视的znode发生了变化(a client will never see a change for which it has set a watch until it first sees the watch event)。

网络延迟或者其他因素可能导致不同的客户端在不同的时刻感知某一监视事件，但是不同的客户端所看到的一切具有一致的顺序。

（被设置 watch 的数据）The data for which the watch was set

这意味着znode节点本身具有不同的改变方式。你也可以想象 Zookeeper 维护了两条监视链表：数据监视和子节点监视(data watches and child watches) getData() 和exists()设置数据监视，getChildren()设置子节点监视。或者你也可以想象 Zookeeper 设置的不同监视返回不同的数据，getData() 和 exists() 返回znode节点的相关信息，而getChildren() 返回子节点列表。

因此，setData() 会触发设置在某一节点上所设置的数据监视（假定数据设置成功），而一次成功的create() 操作则会出发当前节点上所设置的数据监视以及父节点的子节点监视。一次成功的 delete操作将会触发当前节点的数据监视和子节点监视事件，同时也会触发该节点父节点的child watch。

Zookeeper 中的监视是轻量级的，因此容易设置、维护和分发。当客户端与 Zookeeper 服务器失去联系时，客户端并不会收到监视事件的通知，只有当客户端重新连接后，若在必要的情况下，以前注册的监视会重新被注册并触发，对于开发人员来说这通常是透明的。

只有一种情况会导致监视事件的丢失，即：通过exists()设置了某个znode节点的监视，但是如果某个客户端在此znode节点被创建和删除的时间间隔内与zookeeper服务器失去了联系，该客户端即使稍后重新连接 zookeeper服务器后也得不到事件通知。

Consistency Guarantees
Zookeeper是一个高效的、可扩展的服务，read和write操作都被设计为快速的，read比write操作更快。

顺序一致性（Sequential Consistency）：从一个客户端来的更新请求会被顺序执行。

原子性（Atomicity）：更新要么成功要么失败，没有部分成功的情况。

唯一的系统镜像（Single System Image）：无论客户端连接到哪个Server，看到系统镜像是一致的。

可靠性（Reliability）：更新一旦有效，持续有效，直到被覆盖。

时间线（Timeliness）：保证在一定的时间内各个客户端看到的系统信息是一致的。

ZooKeeper的工作原理
在zookeeper的集群中，各个节点共有下面3种角色和4种状态：

角色：leader,follower,observer

状态：leading,following,observing,looking

Zookeeper的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议（ZooKeeper Atomic Broadcast protocol）。Zab协议有两种模式，它们分别是恢复模式（Recovery选主）和广播模式（Broadcast同步）。

当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态。

为了保证事务的顺序一致性，zookeeper采用了递增的事务id号（zxid）来标识事务。所有的提议（proposal）都在被提出的时候加上了zxid。

实现中zxid是一个64位的数字，它高32位是epoch用来标识leader关系是否改变，每次一个leader被选出来，它都会有一个新的epoch，标识当前属于那个leader的统治时期。低32位用于递增计数。

每个Server在工作过程中有4种状态：

LOOKING：当前Server不知道leader是谁，正在搜寻。

LEADING：当前Server即为选举出来的leader。

FOLLOWING：leader已经选举出来，当前Server与之同步。

OBSERVING：observer的行为在大多数情况下与follower完全一致，但是他们不参加选举和投票，而仅仅接受(observing)选举和投票的结果。

Leader Election
当leader崩溃或者leader失去大多数的follower，这时候zk进入恢复模式，恢复模式需要重新选举出一个新的leader，让所有的Server都恢复到一个正确的状态。Zk的选举算法有两种：一种是基于basic paxos实现的，另外一种是基于fast paxos算法实现的。系统默认的选举算法为fast paxos。先介绍basic paxos流程：

选举线程由当前Server发起选举的线程担任，其主要功能是对投票结果进行统计，并选出推荐的Server；

选举线程首先向所有Server发起一次询问（包括自己）；

选举线程收到回复后，验证是否是自己发起的询问（验证zxid是否一致），然后获取对方的id（myid），并存储到当前询问对象列表中，最后获取对方提议的leader相关信息（id,zxid），并将这些信息存储到当次选举的投票记录表中；

收到所有Server回复以后，就计算出zxid最大的那个Server，并将这个Server相关信息设置成下一次要投票的Server；

线程将当前zxid最大的Server设置为当前Server要推荐的Leader，如果此时获胜的Server获得n/2 + 1的Server票数，设置当前推荐的leader为获胜的Server，将根据获胜的Server相关信息设置自己的状态，否则，继续这个过程，直到leader被选举出来。

通过流程分析我们可以得出：要使Leader获得多数Server的支持，则Server总数必须是奇数2n+1，且存活的Server的数目不得少于n+1.

每个Server启动后都会重复以上流程。在恢复模式下，如果是刚从崩溃状态恢复的或者刚启动的server还会从磁盘快照中恢复数据和会话信息，zk会记录事务日志并定期进行快照，方便在恢复时进行状态恢复。

fast paxos流程是在选举过程中，某Server首先向所有Server提议自己要成为leader，当其它Server收到提议以后，解决epoch和zxid的冲突，并接受对方的提议，然后向对方发送接受提议完成的消息，重复这个流程，最后一定能选举出Leader。

往期：100期面试题汇总

Leader工作流程
Leader主要有三个功能：

恢复数据；

维持与follower的心跳，接收follower请求并判断follower的请求消息类型；

follower的消息类型主要有PING消息、REQUEST消息、ACK消息、REVALIDATE消息，根据不同的消息类型，进行不同的处理。

说明：

PING消息是指follower的心跳信息；REQUEST消息是follower发送的提议信息，包括写请求及同步请求；
ACK消息是follower的对提议的回复，超过半数的follower通过，则commit该提议；
REVALIDATE消息是用来延长SESSION有效时间。

Follower工作流程
Follower主要有四个功能：

向Leader发送请求（PING消息、REQUEST消息、ACK消息、REVALIDATE消息）；

接收Leader消息并进行处理；

接收Client的请求，如果为写请求，发送给Leader进行投票；

返回Client结果。

Follower的消息循环处理如下几种来自Leader的消息：

PING消息：心跳消息

PROPOSAL消息：Leader发起的提案，要求Follower投票

COMMIT消息：服务器端最新一次提案的信息

UPTODATE消息：表明同步完成

REVALIDATE消息：根据Leader的REVALIDATE结果，关闭待revalidate的session还是允许其接受消息

SYNC消息：返回SYNC结果到客户端，这个消息最初由客户端发起，用来强制得到最新的更新。

Zab: Broadcasting State Updates
Zookeeper Server接收到一次request，如果是follower，会转发给leader，Leader执行请求并通过Transaction的形式广播这次执行。Zookeeper集群如何决定一个Transaction是否被commit执行？通过“两段提交协议”（a two-phase commit）：

Leader给所有的follower发送一个PROPOSAL消息。

一个follower接收到这次PROPOSAL消息，写到磁盘，发送给leader一个ACK消息，告知已经收到。

当Leader收到法定人数（quorum）的follower的ACK时候，发送commit消息执行。

Zab协议保证：

如果leader以T1和T2的顺序广播，那么所有的Server必须先执行T1，再执行T2。

如果任意一个Server以T1、T2的顺序commit执行，其他所有的Server也必须以T1、T2的顺序执行。

“两段提交协议”最大的问题是如果Leader发送了PROPOSAL消息后crash或暂时失去连接，会导致整个集群处在一种不确定的状态（follower不知道该放弃这次提交还是执行提交）。Zookeeper这时会选出新的leader，请求处理也会移到新的leader上，不同的leader由不同的epoch标识。切换Leader时，需要解决下面两个问题：

1. Never forget delivered messages

Leader在COMMIT投递到任何一台follower之前crash，只有它自己commit了。新Leader必须保证这个事务也必须commit。

2. Let go of messages that are skipped

Leader产生某个proposal，但是在crash之前，没有follower看到这个proposal。该server恢复时，必须丢弃这个proposal。

Zookeeper会尽量保证不会同时有2个活动的Leader，因为2个不同的Leader会导致集群处在一种不一致的状态，所以Zab协议同时保证：

在新的leader广播Transaction之前，先前Leader commit的Transaction都会先执行。

在任意时刻，都不会有2个Server同时有法定人数（quorum）的支持者。
这里的quorum是一半以上的Server数目，确切的说是有投票权力的Server（不包括Observer）。

总结
简单介绍了Zookeeper的基本原理，数据模型，Session，Watch机制，一致性保证，Leader Election，Leader和Follower的工作流程和Zab协议。

往期：100期面试题汇总






【128期】一道搜狗面试题：IO多路复用中select、poll、epoll之间的区别
至尊宝 Java面试题精选 2020-07-13
点击上方“Java面试题精选”，关注公众号

面试刷图，查缺补漏

>>号外：往期面试题，10篇为一个单位归置到本公众号菜单栏->面试题，有需要的欢迎翻阅

阶段汇总集合：++小Flag实现，一百期面试题汇总++

(1)select==>时间复杂度O(n)
它仅仅知道了，有I/O事件发生了，却并不知道是哪那几个流（可能有一个，多个，甚至全部），我们只能无差别轮询所有流，找出能读出数据，或者写入数据的流，对他们进行操作。所以select具有O(n)的无差别轮询复杂度，同时处理的流越多，无差别轮询时间就越长。

(2)poll==>时间复杂度O(n)
poll本质上和select没有区别，它将用户传入的数组拷贝到内核空间，然后查询每个fd对应的设备状态， 但是它没有最大连接数的限制，原因是它是基于链表来存储的.

(3)epoll==>时间复杂度O(1)
epoll可以理解为event poll，不同于忙轮询和无差别轮询，epoll会把哪个流发生了怎样的I/O事件通知我们。所以我们说epoll实际上是事件驱动（每个事件关联上fd）的，此时我们对这些流的操作都是有意义的。（复杂度降低到了O(1)）

select，poll，epoll都是IO多路复用的机制。I/O多路复用就通过一种机制，可以监视多个描述符，一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。但select，poll，epoll本质上都是同步I/O，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。

epoll跟select都能提供多路I/O复用的解决方案。在现在的Linux内核里有都能够支持，其中epoll是Linux所特有，而select则应该是POSIX所规定，一般操作系统均有实现

select：
select本质上是通过设置或者检查存放fd标志位的数据结构来进行下一步处理。这样所带来的缺点是：

1、 单个进程可监视的fd数量被限制，即能监听端口的大小有限。

一般来说这个数目和系统内存关系很大，具体数目可以cat /proc/sys/fs/file-max察看。32位机默认是1024个。64位机默认是2048.

2、 对socket进行扫描时是线性扫描，即采用轮询的方法，效率较低：

当套接字比较多的时候，每次select()都要通过遍历FD_SETSIZE个Socket来完成调度,不管哪个Socket是活跃的,都遍历一遍。这会浪费很多CPU时间。如果能给套接字注册某个回调函数，当他们活跃时，自动完成相关操作，那就避免了轮询，这正是epoll与kqueue做的。

3、需要维护一个用来存放大量fd的数据结构，这样会使得用户空间和内核空间在传递该结构时复制开销大

poll：
poll本质上和select没有区别，它将用户传入的数组拷贝到内核空间，然后查询每个fd对应的设备状态，如果设备就绪则在设备等待队列中加入一项并继续遍历，如果遍历完所有fd后没有发现就绪设备，则挂起当前进程，直到设备就绪或者主动超时，被唤醒后它又要再次遍历fd。这个过程经历了多次无谓的遍历。

它没有最大连接数的限制，原因是它是基于链表来存储的，但是同样有一个缺点：

大量的fd的数组被整体复制于用户态和内核地址空间之间，而不管这样的复制是不是有意义。

poll还有一个特点是“水平触发”，如果报告了fd后，没有被处理，那么下次poll时会再次报告该fd。

epoll:
epoll有EPOLLLT和EPOLLET两种触发模式，LT是默认的模式，ET是“高速”模式。LT模式下，只要这个fd还有数据可读，每次 epoll_wait都会返回它的事件，提醒用户程序去操作，而在ET（边缘触发）模式中，它只会提示一次，直到下次再有数据流入之前都不会再提示了，无 论fd中是否还有数据可读。

所以在ET模式下，read一个fd的时候一定要把它的buffer读光，也就是说一直读到read的返回值小于请求值，或者 遇到EAGAIN错误。还有一个特点是，epoll使用“事件”的就绪通知方式，通过epoll_ctl注册fd，一旦该fd就绪，内核就会采用类似callback的回调机制来激活该fd，epoll_wait便可以收到通知。

epoll为什么要有EPOLLET触发模式？
如果采用EPOLLLT模式的话，系统中一旦有大量你不需要读写的就绪文件描述符，它们每次调用epoll_wait都会返回，这样会大大降低处理程序检索自己关心的就绪文件描述符的效率.。而采用EPOLLET这种边沿触发模式的话，当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。

如果这次没有把数据全部读写完(如读写缓冲区太小)，那么下次调用epoll_wait()时，它不会通知你，也就是它只会通知你一次，直到该文件描述符上出现第二次可读写事件才会通知你！！！这种模式比水平触发效率高，系统不会充斥大量你不关心的就绪文件描述符

epoll的优点：

没有最大并发连接的限制，能打开的FD的上限远大于1024（1G的内存上能监听约10万个端口）；
效率提升，不是轮询的方式，不会随着FD数目的增加效率下降。只有活跃可用的FD才会调用callback函数；即Epoll最大的优点就在于它只管你“活跃”的连接，而跟连接总数无关，因此在实际的网络环境中，Epoll的效率就会远远高于select和poll。
内存拷贝，利用mmap()文件映射内存加速与内核空间的消息传递；即epoll使用mmap减少复制开销。
select、poll、epoll 区别总结：
1、支持一个进程所能打开的最大连接数
select
单个进程所能打开的最大连接数有FD_SETSIZE宏定义，其大小是32个整数的大小（在32位的机器上，大小就是3232，同理64位机器上FD_SETSIZE为3264），当然我们可以对进行修改，然后重新编译内核，但是性能可能会受到影响，这需要进一步的测试。

poll
poll本质上和select没有区别，但是它没有最大连接数的限制，原因是它是基于链表来存储的

epoll
虽然连接数有上限，但是很大，1G内存的机器上可以打开10万左右的连接，2G内存的机器可以打开20万左右的连接

2、FD剧增后带来的IO效率问题
select
因为每次调用时都会对连接进行线性遍历，所以随着FD的增加会造成遍历速度慢的“线性下降性能问题”。

poll
同上

epoll
因为epoll内核中实现是根据每个fd上的callback函数来实现的，只有活跃的socket才会主动调用callback，所以在活跃socket较少的情况下，使用epoll没有前面两者的线性下降的性能问题，但是所有socket都很活跃的情况下，可能会有性能问题。

3、 消息传递方式
select
内核需要将消息传递到用户空间，都需要内核拷贝动作

poll
同上

epoll
epoll通过内核和用户空间共享一块内存来实现的。

往期：100期面试题汇总

总结：
综上，在选择select，poll，epoll时要根据具体的使用场合以及这三种方式的自身特点。

1、表面上看epoll的性能最好，但是在连接数少并且连接都十分活跃的情况下，select和poll的性能可能比epoll好，毕竟epoll的通知机制需要很多函数回调。

2、select低效是因为每次它都需要轮询。但低效也是相对的，视情况而定，也可通过良好的设计改善

今天对这三种IO多路复用进行对比，参考网上和书上面的资料，整理如下：

1、select实现
select的调用过程如下所示：

图片

使用copy_from_user从用户空间拷贝fd_set到内核空间
注册回调函数__pollwait
遍历所有fd，调用其对应的poll方法（对于socket，这个poll方法是sock_poll，sock_poll根据情况会调用到tcp_poll,udp_poll或者datagram_poll） -以tcp_poll为例，其核心实现就是__pollwait，也就是上面注册的回调函数。
__pollwait的主要工作就是把current（当前进程）挂到设备的等待队列中，不同的设备有不同的等待队列，对于tcp_poll来说，其等待队列是sk->sk_sleep（注意把进程挂到等待队列中并不代表进程已经睡眠了）。在设备收到一条消息（网络设备）或填写完文件数据（磁盘设备）后，会唤醒设备等待队列上睡眠的进程，这时current便被唤醒了。
poll方法返回时会返回一个描述读写操作是否就绪的mask掩码，根据这个mask掩码给fd_set赋值。
如果遍历完所有的fd，还没有返回一个可读写的mask掩码，则会调用schedule_timeout是调用select的进程（也就是current）进入睡眠。当设备驱动发生自身资源可读写后，会唤醒其等待队列上睡眠的进程。如果超过一定的超时时间（schedule_timeout指定），还是没人唤醒，则调用select的进程会重新被唤醒获得CPU，进而重新遍历fd，判断有没有就绪的fd。
把fd_set从内核空间拷贝到用户空间。

往期：100期面试题汇总
总结：
select的几大缺点：

每次调用select，都需要把fd集合从用户态拷贝到内核态，这个开销在fd很多时会很大
同时每次调用select都需要在内核遍历传递进来的所有fd，这个开销在fd很多时也很大
select支持的文件描述符数量太小了，默认是1024
2、poll实现
poll的实现和select非常相似，只是描述fd集合的方式不同，poll使用pollfd结构而不是select的fd_set结构，其他的都差不多,管理多个描述符也是进行轮询，根据描述符的状态进行处理，但是poll没有最大文件描述符数量的限制。

poll和select同样存在一个缺点就是，包含大量文件描述符的数组被整体复制于用户态和内核的地址空间之间，而不论这些文件描述符是否就绪，它的开销随着文件描述符数量的增加而线性增大。

3、epoll
epoll既然是对select和poll的改进，就应该能避免上述的三个缺点。那epoll都是怎么解决的呢？在此之前，我们先看一下epoll和select和poll的调用接口上的不同，select和poll都只提供了一个函数——select或者poll函数。

而epoll提供了三个函数，epoll_create,epoll_ctl和epoll_wait，epoll_create是创建一个epoll句柄；epoll_ctl是注册要监听的事件类型；epoll_wait则是等待事件的产生。

对于第一个缺点，epoll的解决方案在epoll_ctl函数中。每次注册新的事件到epoll句柄中时（在epoll_ctl中指定EPOLL_CTL_ADD），会把所有的fd拷贝进内核，而不是在epoll_wait的时候重复拷贝。epoll保证了每个fd在整个过程中只会拷贝一次。

对于第二个缺点，epoll的解决方案不像select或poll一样每次都把current轮流加入fd对应的设备等待队列中，而只在epoll_ctl时把current挂一遍（这一遍必不可少）并为每个fd指定一个回调函数，当设备就绪，唤醒等待队列上的等待者时，就会调用这个回调函数，而这个回调函数会把就绪的fd加入一个就绪链表）。epoll_wait的工作实际上就是在这个就绪链表中查看有没有就绪的fd（利用schedule_timeout()实现睡一会，判断一会的效果，和select实现中的第7步是类似的）。

对于第三个缺点，epoll没有这个限制，它所支持的FD上限是最大可以打开文件的数目，这个数字一般远大于2048,举个例子,在1GB内存的机器上大约是10万左右，具体数目可以cat /proc/sys/fs/file-max察看,一般来说这个数目和系统内存关系很大。

往期：100期面试题汇总

总结：
（1）select，poll实现需要自己不断轮询所有fd集合，直到设备就绪，期间可能要睡眠和唤醒多次交替。而epoll其实也需要调用epoll_wait不断轮询就绪链表，期间也可能多次睡眠和唤醒交替，但是它是设备就绪时，调用回调函数，把就绪fd放入就绪链表中，并唤醒在epoll_wait中进入睡眠的进程。

虽然都要睡眠和交替，但是select和poll在“醒着”的时候要遍历整个fd集合，而epoll在“醒着”的时候只要判断一下就绪链表是否为空就行了，这节省了大量的CPU时间。这就是回调机制带来的性能提升。

（2）select，poll每次调用都要把fd集合从用户态往内核态拷贝一次，并且要把current往设备等待队列中挂一次，而epoll只要一次拷贝，而且把current往等待队列上挂也只挂一次（在epoll_wait的开始，注意这里的等待队列并不是设备等待队列，只是一个epoll内部定义的等待队列）。这也能节省不少的开销。





【130期】面试官：你能说清楚分布式锁，进程锁，线程锁的区别吗？
大宅洋 Java面试题精选 2020-07-16
点击上方“Java面试题精选”，关注公众号

面试刷图，查缺补漏


>>号外：往期面试题，10篇为一个单位归置到本公众号菜单栏->面试题，有需要的欢迎翻阅

阶段汇总集合：++小Flag实现，一百期面试题汇总++

在分布式集群系统的开发中，线程锁往往并不能支持全部场景的使用，必须引入新的技术方案分布式锁。

线程锁，进程锁，分布式锁
线程锁:大家都不陌生，主要用来给方法、代码块加锁。当某个方法或者代码块使用锁时，那么在同一时刻至多仅有有一个线程在执行该段代码。当有多个线程访问同一对象的加锁方法/代码块时，同一时间只有一个线程在执行，其余线程必须要等待当前线程执行完之后才能执行该代码段。但是，其余线程是可以访问该对象中的非加锁代码块的。

进程锁:也是为了控制同一操作系统中多个进程访问一个共享资源，只是因为程序的独立性，各个进程是无法控制其他进程对资源的访问的，但是可以使用本地系统的信号量控制（操作系统基本知识）。

分布式锁:当多个进程不在同一个系统之中时，使用分布式锁控制多个进程对资源的访问。

分布式锁到底是什么，怎么实现？
intsmaze说简单点，实现分布式锁必须要依靠第三方存储介质来存储锁的元数据等信息。比如分布式集群要操作某一行数据时，这个数据的流水号是唯一的，那么我们就把这个流水号作为一把锁的id，当某进程要操作该数据时，先去第三方存储介质中看该锁id是否存在，如果不存在，则将该锁id写入，然后执对该数据的操作；当其他进程要访问这个数据时，会先到第三方存储介质中查看有没有这个数据的锁id,有的话就认为这行数据目前已经有其他进程在使用了，就会不断地轮询第三方存储介质看其他进程是否释放掉该锁；当进程操作完该数据后，该进程就到第三方存储介质中把该锁id删除掉，这样其他轮询的进程就能得到对该锁的控制。

Redis中当然不能通过get,set操作判断，get,set操作不是一个原子的，可以使用redis的jedis.set(String key, String value, String nxxx, String expx, int time)命令来保证原子性。

具体实现方案：https://www.cnblogs.com/linjiqin/p/8003838.html

说了这么多，再补充一点，线程锁，进程锁，分布式锁的作用都是一样的，只是作用的范围大小不同。范围大小:分布式锁——大于——进程锁——大于——线程锁。能用线程锁，进程锁情况下使用分布式锁也是可以的，能用线程锁的情况下使用进程锁也是可以的。只是范围越大技术复杂度就越大。

多年j2EE开发生涯从未感觉到分布式锁的痛点!!!
关于分布式锁，有过javaEE开发经验的就会说了，系统为了应对高并发，会搭建一个比如tomcat集群，集群内服务都是访问的同一台数据库，有多台服务器同时修改同一条数据库数据的操作，但是我们并没有在服务器中使用分布式锁？按照上面对分布式锁的解释，两个不同系统上的JVM进程同时访问数据库的同一个资源，这个时候我们应该使用分布式锁进行控制。

这说的没有错，但是我们忘记了数据库的特性了。如果两台服务器仅仅是直接访问（通过url）并操作某台服务器硬盘中某个文件同一行数据，这个时候我们必须用分布式锁。但是因为这两台服务器访问的数据是存储在数据库中的（数据库本身就是一个服务程序，多线程的接收外部系统发来的请求），两台服务器的请求通过网络IO发送到数据库服务器后，然后把请求交给数据库服务的进程处理，数据库服务器是多线程接收请求并处理的，这个时候关于某表某一行数据的多线程访问控制是由数据库服务进行控制的（就是数据库服务的代码中进行了线程上的加锁处理），这就是数据库服务器的行锁等特性，因为数据库那一端已经对外部多个系统的请求进行了一个锁操作，所以不需要我们在应用服务端进行分布式锁的开发。

那如果想同时更新数据库的多行数据，这个时候数据库的行锁就无法保证了。这个时候我们就要使用分布式锁，是的这个时候就可以使用，注意我用的是可以。为什么说可以呢？因为数据库本身就提供了这个机制，事务以及他的隔离级别。当然你也可以不用数据库提供的事务，用分布式锁。


分布式锁的设计不需要考虑业务吗?
分布式锁的设计并不是完全美好的，只能针对某些业务场景下使用，如果要对所有业务使用，必须充分理解业务需求合理的设计，至于原因就和各位j2ee开发时mybatis的二级缓存以命名空间为单位所要注意的业务问题时一样的。

intsmaze使用分布式锁，我们会把某表的第二第三行作为id来锁住，如果有相同的操作时更新该表第二第三行，我们才不让他修改，必须让他拿到锁才可以。但是如果有个操作仅仅是修改第二行，这个时候他就获得了对该行的操作，而且等数据库释放掉之前操作对该行的锁后。所以分布式锁并不是随处可用的，只是在某些场景下可以使用。比如业务系统不会存在单独修改第二行的操作。

分布式锁用于hbase存储系统
实际开发场景中，我们会对hbase操作进行分布式锁，hbase作为一款优秀的非内存数据库，传统数据库一样提供了事务的概念，只是HBase的事务是行级事务，可以保证行级数据的原子性、一致性、隔离性以及持久性，即通常所说的ACID特性。为了实现事务特性，HBase采用了各种并发控制策略，包括各种锁机制、MVCC机制等。因为hbase只支持行级事物，当业务需要并发操作两行甚至多行记录时，hbase本身就无法提供ACID的支持了。

往期：100期面试题汇总

数据库访问量过大除了主从还能如何负载压力？
数据库会为客户端的每一个请求创建一个线程，这些线程针对特定行数据修改必须获得该行的行锁，而其他客户端线程要想修改该数据的话，必须等待前面的线程释放锁后才被允许。如果客户端很多线程都要修改某行数据的话，没有拿到锁的线程都会在数据库端机器上不断轮询，增大数据库端的压力。

我们可以使用分布式锁，把对数据库行锁的等待获取的轮询放到每一个客户端机器上去实现，这样可以避免数据库端线程的不断轮询。比如，客户端在要发送对数据库的某行数据的操作请求前，在客户端机器上进行锁的争抢，没有获取到锁，就不会像数据库端发送操作请求，这样数据库端就没有了轮询的压力。当然分布式锁的引入一定要结合业务的需求来进行设计，不然会出现锁id的命名不全导致读取的数据不一致，数据过期失效等问题。

使用那种第三方介质存放分布式锁?
目前流行的是zookeeper和redis，两者各有好处，redis流行的内存缓存，且能进行水平扩容同时还能提高请求负载，面对高并分布式锁数据的读写请求能高速响应，同时有aof,哨兵机制可以防止某台宕机分布式锁数据丢失带来的问题。

zookeeper我是比较喜欢，因为他是分布式一致性算法paxos算法的实现，面对高负载请求毫无压力，同时某一台宕机毫不影响分布式锁数据一致性，且附带了监听机制，当某一程序释放某一个锁后，其他程序可以及时得到通知来获得对该分布式锁的控制权，这里的轮询实现不需要我们去开发了。

关于分布式锁与线程锁的介绍从一年前就在编辑中，一直没有时间以一种通俗明了的方式介绍给大家。本人在很多论坛中发现很多刚入大数据领域的新人都会提到分布式锁，但是并没有深刻明白分布式锁和线程锁的场景，以至于很多情况下明明线程锁就可以搞定的却引入了分布式锁，让整个系统设计的更加复杂了。

另外要说的，zookeeper笔者认为是很棒的技术，虽然在大数据领域只是作为某一个框架的一个协调者出现，导致很多开发者忽视了他的伟大性。但是我想说的，在当前火热的微服务中，其实会借助zookeeper实现很多功能，比如分布式锁，配置中心。