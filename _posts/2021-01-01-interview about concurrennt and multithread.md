---
layout: post
title: "后端开发面试题 -- 并发与多线程篇"
description: 后端开发面试题 -- 并发与多线程篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# 并发与多线程

1.并发与并行
并发，指的是多个事情，在同一时间段内同时发生了。并行，指的是多个事情，在同一时间点上同时发生了。
并发的多个任务之间是互相抢占资源的。并行的多个任务之间是不互相抢占资源的。只有在多CPU的情况中，才会发生并行。否则，看似同时发生的事情，其实都是并发执行的。

2.线程和进程的区别

进程：计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。在早期面向进程设计的计算机结构中，进程是程序的基本执行实体；在当代面向线程设计的计算机结构中，进程是线程的容器。程序是指令、数据及其组织形式的描述，进程是程序的实体。
线程：进程的一个实体，是CPU调度和分派的基本单位，它是比进程更小的能独立运行的基本单位。线程自己基本上不拥有系统资源，只拥有一点在运行中必不可少的资源(如程序计数器，一组寄存器和栈)，但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源。

区别：进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

(1)简而言之，一个程序至少有一个进程，一个进程至少有一个线程。
(2)线程的划分尺度小于进程，使得多线程程序的并发性高。
(3)另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
(4)线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
(5)从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

3.守护线程

守护线程（即daemon thread），是个服务线程，准确地来说就是服务其他的线程，这是它的作用——而其他的线程只有一种，那就是用户线程。所以java里线程分2种：守护线程，比如垃圾回收线程，就是最典型的守护线程。用户线程，就是应用程序里的自定义线程。
当JVM中不存在任何一个正在运行的非守护线程时，则JVM进程即会退出。

4.创建线程方式

(1)继承Thread类
(2)实现Runnable接口
(3)应用程序可以使用Executor框架来创建线程池
(4)实现Callable接口，通过如下方式获取线程执行结果

    FutureTask<Boolean> futureTask = new FutureTask<Boolean>(new SubscribeThreadWithResult(product, config));
    Thread thread = new Thread(futureTask);
    thread.start();
    bollean result = futureTask.get();
    
实现Runnable接口这种方式更受欢迎，因为这不需要继承Thread类。在应用设计中已经继承了别的对象的情况下，这需要多继承（而Java不支持多继承），只能实现接口。同时，线程池也是非常高效的，很容易实现和使用。

5.线程状态

(1)新建(new)：新创建了一个线程对象。
(2)可运行(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权。
(3)运行(running)：可运行状态(runnable )的线程获得了CPU时间片（timeslice），执行程序代码。
(4)阻塞(blocked)：阻塞状态是指线程因为某种原因放弃了CPU使用权，也即让出了CPU timeslice ，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice转到运行(running)状态。
阻塞的情况分三种：
等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。
同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。
其他阻塞: 运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。
(5)死亡(terminated)：线程run()、 main()方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

6.同步方法和同步代码块的区别

同步方法默认用this或者当前类class对象作为锁；
同步代码块可以选择以什么来加锁，比同步方法要更细颗粒度，我们可以选择只同步会发生同步问题的部分代码而不是整个方法；

7.线程池

(1)线程池优点：
(a)线程池的重用:线程的创建和销毁的开销是巨大的，而通过线程池的重用大大减少了这些不必要的开销，当然既然少了这么多消费内存的开销，其线程执行速度也是突飞猛进的提升。
(b)控制线程池的并发数:控制线程池的并发数可以有效的避免大量的线程池争夺CPU资源而造成堵塞。
(c)线程池可以对线程进行管理：线程池可以提供定时、定期、单线程、并发数控制等功能。

(2)ThreadPoolExecutor，构造函数如下

    public ThreadPoolExecutor(int corePoolSize,  
                              int maximumPoolSize,  
                              long keepAliveTime,  
                              TimeUnit unit,  
                              BlockingQueue<Runnable> workQueue,  
                              ThreadFactory threadFactory,  
                              RejectedExecutionHandler handler)
                               
七个参数的含义：
corePoolSize 线程池中核心线程的数量；
maximumPoolSize 线程池中最大线程数量；
keepAliveTime 非核心线程的超时时长，当系统中非核心线程闲置时间超过keepAliveTime之后，则会被回收。如果ThreadPoolExecutor的allowCoreThreadTimeOut属性设置为true，则该参数也表示核心线程的超时时长；
unit 第三个参数的单位，有纳秒、微秒、毫秒、秒、分、时、天等；
workQueue 线程池中的任务队列，该队列主要用来存储已经被提交但是尚未执行的任务。存储在这里的任务是由ThreadPoolExecutor的execute方法提交来的。
threadFactory 为线程池提供创建新线程的功能，这个我们一般使用默认即可；
handler 拒绝策略，当线程无法执行新任务时（一般是由于线程池中的线程数量已经达到最大数或者线程池关闭导致的），默认情况下，当线程池无法处理新线程时，会抛出一个RejectedExecutionException。

执行流程：
当currentSize<corePoolSize时，没什么好说的，直接启动一个核心线程并执行任务。
当currentSize>=corePoolSize、并且workQueue未满时，添加进来的任务会被安排到workQueue中等待执行。
当workQueue已满，但是currentSize<maximumPoolSize时，会立即开启一个非核心线程来执行任务。
当currentSize>=corePoolSize、workQueue已满、并且currentSize>maximumPoolSize时，调用handler，执行线程池饱和策略，默认抛出RejectExecutionExpection异常。

线程池饱和策略分为以下几种：
AbortPolicy:直接抛出一个异常，默认策略
DiscardPolicy: 直接丢弃任务
DiscardOldestPolicy:抛弃下一个将要被执行的任务(最旧任务)
CallerRunsPolicy:主线程中执行任务

几种典型的工作队列：
ArrayBlockingQueue:使用数组实现的有界阻塞队列，特性先进先出
LinkedBlockingQueue:使用链表实现的阻塞队列，特性先进先出，可以设置其容量，默认为Interger.MAX_VALUE，特性先进先出
PriorityBlockingQueue:使用平衡二叉树堆，实现的具有优先级的无界阻塞队列
DelayQueue:无界阻塞延迟队列，队列中每个元素均有过期时间，当从队列获取元素时，只有过期元素才会出队列。队列头元素是最块要过期的元素。
SynchronousQueue:一个不存储元素的阻塞队列，每个插入操作，必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态。

几种典型的线程池：
FixedThreadPool:Fixed中文解释为固定。结合在一起解释固定的线程池，说的更全面点就是，有固定数量线程的线程池。其corePoolSize=maximumPoolSize，且keepAliveTime为0，适合线程稳定的场景。使用LinkedBlockingQueue。
SingleThreadPool:Single中文解释为单一。结合在一起解释单一的线程池，说的更全面点就是，有固定数量线程的线程池，且数量为一，从数学的角度来看SingleThreadPool应该属于FixedThreadPool的子集。其corePoolSize=maximumPoolSize=1,且keepAliveTime为0，适合线程同步操作的场所。使用LinkedBlockingQueue。
CachedThreadPool:Cached中文解释为储存。结合在一起解释储存的线程池，说的更通俗易懂，既然要储存，其容量肯定是很大，所以它的corePoolSize=0，maximumPoolSize=Integer.MAX_VALUE(2^32-1一个很大的数字)。使用SynchronousQueue。
ScheduledThreadPool:Scheduled中文解释为计划。结合在一起解释计划的线程池，顾名思义既然涉及到计划，必然会涉及到时间。所以ScheduledThreadPool是一个具有定时定期执行任务功能的线程池。使用DelayedWorkQueue。

使用无界队列的线程池会导致内存飙升吗？
会的，newFixedThreadPool使用了无界的阻塞队列LinkedBlockingQueue，如果线程获取一个任务后，任务的执行时间比较长，会导致队列的任务越积越多，导致机器内存使用不停飙升， 最终导致OOM。

线程回收：
ThreadPoolExecutor回收工作线程，一条线程getTask()返回null，就会被回收。分两种场景。
(1)未调用shutdown() ，RUNNING状态下全部任务执行完成的场景：线程数量大于corePoolSize，线程获取任务但超时阻塞，超时唤醒后CAS减少工作线程数，如果CAS成功，返回null，线程回收。否则进入下一次循环。当工作者线程数量小于等于corePoolSize，就可以一直阻塞了。
(2)调用shutdown() ，全部任务执行完成的场景：shutdown()会向所有线程发出中断信号，这时有两种可能。
(2.1)所有线程都在阻塞，中断唤醒，进入循环，都符合第一个if判断条件（线程池的状态已经是STOP，TIDYING, TERMINATED，或者是SHUTDOWN且工作队列为空），都返回null，所有线程回收。
(2.2)任务还没有完全执行完，至少会有一条线程被回收。在processWorkerExit(Worker w, boolean completedAbruptly)方法里会调用tryTerminate()，向任意空闲线程发出中断信号。所有被阻塞的线程，最终都会被一个个唤醒，回收。

8.ThreadLocal

    static final ThreadLocal<T> sThreadLocal = new ThreadLocal<T>();
    sThreadLocal.set(T)
    sThreadLocal.get()
    sThreadLocal.remove()
    
ThreadLocal的静态内部类ThreadLocalMap为每个Thread都维护了一个数组table，ThreadLocal确定了一个数组下标，而这个下标就是value存储的对应位置。
对于某一ThreadLocal来讲，它的索引值i是确定的，在不同线程之间访问时访问的是不同的table数组的同一位置即都为table[i]，只不过这个不同线程之间的table是独立的。
对于同一线程的不同ThreadLocal来讲，这些ThreadLocal实例共享一个table数组，然后每个ThreadLocal实例在table中的索引i是不同的。
ThreadLocal和Synchronized都是为了解决多线程中相同变量的访问冲突问题，不同的点是Synchronized是通过线程等待，牺牲时间来解决访问冲突。ThreadLocal是通过每个线程单独一份存储空间，牺牲空间来解决冲突，并且相比于Synchronized，ThreadLocal具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问到想要的值。

9.Java的线程安全与不安全

多个线程之间是不能直接传递数据进行交互的，它们之间的交互只能通过共享变量来实现。
例如在多个线程之间共享了Count类的一个实例，这个对象是被创建在主内存（堆内存）中，每个线程都有自己的工作内存（线程栈），工作内存存储了主内存count对象的一个副本，当线程操作count对象时，首先从主内存复制count对象到工作内存中，然后执行代码count.count()，改变了num值，最后用工作内存中的count刷新主内存的count。当一个对象在多个工作内存中都存在副本时，如果一个工作内存刷新了主内存中的共享变量，其它线程也应该能够看到被修改后的值，此为可见性。
多个线程执行时，CPU对线程的调度是随机的，我们不知道当前程序被执行到哪步就切换到了下一个线程，一个最经典的例子就是银行汇款问题，一个银行账户存款100，这时一个人从该账户取10元，同时另一个人向该账户汇10元，那么余额应该还是100。那么此时可能发生这种情况，A线程负责取款，B线程负责汇款，A从主内存读到100，B从主内存读到100，A执行减10操作，并将数据刷新到主内存，这时主内存数据100-10=90，而B内存执行加10操作，并将数据刷新到主内存，最后主内存数据100+10=110，显然这是一个严重的问题，我们要保证A线程和B线程有序执行，先取款后汇款或者先汇款后取款，此为有序性。

在Web开发方面，Servlet是否是线程安全的呢？
Servlet不是线程安全的。要解释为什么Servlet为什么不是线程安全的，需要了解Servlet容器（如Tomcat）是如何响应HTTP请求的。当Tomcat接收到Client的HTTP请求时，Tomcat从线程池中取出一个线程，之后找到该请求对应的Servlet对象并进行初始化，之后调用service()方法。
要注意的是每一个Servlet对象在Tomcat容器中只有一个实例对象，即是单例模式。如果多个HTTP请求请求的是同一个Servlet，那么这两个HTTP请求对应的线程将并发调用Servlet的service()方法。如果的Thread1和Thread2调用了同一个Servlet1，Servlet1中定义了成员变量或静态变量，那么可能会发生线程安全问题（因为所有的线程都可能使用这些变量）。
像Servlet这样的类，在Web容器中创建以后，会被传递给每个访问Web应用的用户线程执行，这个类就不是线程安全的。但这并不意味着一定会引发线程安全问题，如果Servlet类里没有成员变量，即使多线程同时执行这个Servlet实例的方法，也不会造成成员变量冲突。
这种对象被称作无状态对象，也就是说对象不记录状态，执行这个对象的任何方法都不会改变对象的状态，也就不会有线程安全问题了。事实上，Web开发实践中，常见的Service类、DAO类，都被设计成无状态对象，所以虽然我们开发的Web应用都是多线程的应用，因为Web容器一定会创建多线程来执行我们的代码，但是我们开发中却可以很少考虑线程安全的问题。

10.多线程的常见应用场景

吞吐量：WEB容器帮你做了多线程，但是只能帮做请求层面的。简单的说，可能就是一个请求一个线程。或多个请求一个线程。如果是单线程，那同时只能处理一个用户的请求。
伸缩性：可以通过增加CPU核数来提升性能。如果是单线程，那程序执行到死也就利用了单核，肯定没办法通过增加CPU核数来提升性能。

后台任务，例如：定时向大量（100w以上）的用户发送邮件；
异步处理，例如：发微博、记录日志等；
分布式计算。

11.为什么Java线程没有Running状态？

Java虚拟机层面所暴露给我们的状态，与操作系统底层的线程状态是两个不同层面的事。有人常觉得Java线程状态中还少了个running状态，这其实是把两个不同层面的状态混淆了。对Java线程状态而言，不存在所谓的running状态，它的runnable状态包含了running状态。
Java线程状态均来自于Thread类下的State这一内部枚举类中所定义的状态，包括6种，New/Runnable/Blocked/Waiting/Timed_Waiting/Terminated。
现在的时分（time-sharing）多任务（multi-task）操作系统架构通常都是用所谓的“时间分片（time quantum or time slice）”方式进行抢占式（preemptive）轮转调度（round-robin式）。更复杂的可能还会加入优先级（priority）的机制。
这个时间分片通常是很小的，一个线程一次最多只能在cpu上运行比如10-20ms的时间（此时处于running状态），也即大概只有0.01秒这一量级，时间片用后就要被切换下来放入调度队列的末尾等待再次调度。（也即回到ready状态）。如果期间进行了I/O的操作还会导致提前释放时间分片，并进入等待队列。又或者是时间分片没有用完就被抢占，这时也是回到ready状态。
这一切换的过程称为线程的上下文切换（context switch），当然cpu不是简单地把线程踢开就完了，还需要把被相应的执行状态保存到内存中以便后续的恢复执行。
显然，10-20ms对人而言是很快的，不计切换开销（每次在1ms以内），相当于1秒内有50-100次切换。事实上时间片经常没用完，线程就因为各种原因被中断，实际发生的切换次数还会更多。也这正是单核CPU上实现所谓的“并发（concurrent）”的基本原理，但其实是快速切换所带来的假象。
时间分片也是可配置的，如果不追求在多个线程间很快的响应，也可以把这个时间配置得大一点，以减少切换带来的开销。如果是多核CPU，才有可能实现真正意义上的并发，这种情况通常也叫并行（pararell）。
通常，Java的线程状态是服务于监控的，如果线程切换得是如此之快，那么区分ready与running就没什么太大意义了。当你看到监控上显示是running时，对应的线程可能早就被切换下去了，甚至又再次地切换了上来，也许你只能看到ready与running两个状态在快速地闪烁。
现今主流的JVM实现都把Java线程一一映射到操作系统底层的线程上，把调度委托给了操作系统，我们在虚拟机层面看到的状态实质是对底层状态的映射及包装。JVM本身没有做什么实质的调度，把底层的ready及running状态映射上来也没多大意义，因此，统一成为runnable状态是不错的选择。
Java线程状态的改变通常只与自身显式引入的机制有关，如果JVM中的线程状态发生改变了，通常是自身机制引发的。比如synchronized机制有可能让线程进入BLOCKED状态，sleep，wait等方法则可能让其进入WATING之类的状态。
至少我们看到了，进行传统上的IO操作时，口语上我们也会说“阻塞”，但这个“阻塞”与线程的 BLOCKED 状态是两码事！线程状态其实是Runnable。处于IO阻塞，只是说cpu不执行线程了，但网卡可能还在监听呀，虽然可能暂时没有收到数据。所以JVM认为线程还在执行。而操作系统的线程状态是围绕着cpu这一核心去述说的，这与JVM的侧重点是有所不同的。

12.Java线程之间通信方式

分布式系统中说的两种通信机制：共享内存机制和消息通信机制。synchronized关键字和while轮询 “属于” 共享内存机制。wait/notify机制、管道通信，是消息传递机制，管道通信通过管道将一个线程中的消息发送给另一个。

(1)同步

这里讲的同步是指多个线程通过synchronized关键字这种方式来实现线程间的通信。
由于线程A和线程B持有同一个MyObject类的对象object，尽管这两个线程需要调用不同的方法，但是它们是同步执行的，比如：线程B需要等待线程A执行完了methodA()方法之后，它才能执行methodB()方法。这样，线程A和线程B就实现了通信。
这种方式，本质上就是“共享内存”式的通信。多个线程需要访问同一个共享变量，谁拿到了锁（获得了访问权限），谁就可以执行。

    public class MyObject {
    
        synchronized public void methodA() {
            //do something....
        }
    
        synchronized public void methodB() {
            //do some other thing
        }
    }
    
    public class ThreadA extends Thread {
    
        private MyObject object;
        //省略构造方法
        @Override
        public void run() {
            super.run();
            object.methodA();
        }
    }
    
    public class ThreadB extends Thread {
    
        private MyObject object;
        //省略构造方法
        @Override
        public void run() {
            super.run();
            object.methodB();
        }
    }
    
    public class Run {
        public static void main(String[] args) {
            MyObject object = new MyObject();
    
            //线程A与线程B 持有的是同一个对象:object
            ThreadA a = new ThreadA(object);
            ThreadB b = new ThreadB(object);
            a.start();
            b.start();
        }
    }

(2)while轮询的方式

在这种方式下，线程A不断地改变条件，线程ThreadB不停地通过while语句检测这个条件(list.size()==5)是否成立，从而实现了线程间的通信。
但是这种方式会浪费CPU资源。之所以说它浪费资源，是因为JVM调度器将CPU交给线程B执行时，它没做啥“有用”的工作，只是在不断地测试某个条件是否成立。就类似于现实生活中，某个人一直看着手机屏幕是否有电话来了，而不是：在干别的事情，当有电话来时，响铃通知TA电话来了。
轮询的条件的可见性问题：线程都是先把变量读取到本地线程栈空间，然后再去修改的本地变量。因此，如果线程B每次都在取本地的条件变量，那么尽管另外一个线程已经改变了轮询的条件，它也察觉不到，这样也会造成死循环。

    public class MyList {
    
        private List<String> list = new ArrayList<String>();
        public void add() {
            list.add("elements");
        }
        public int size() {
            return list.size();
        }
    }
    
    public class ThreadA extends Thread {
    
        private MyList list;
    
        public ThreadA(MyList list) {
            super();
            this.list = list;
        }
    
        @Override
        public void run() {
            try {
                for (int i = 0; i < 10; i++) {
                    list.add();
                    System.out.println("添加了" + (i + 1) + "个元素");
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public class ThreadB extends Thread {
    
        private MyList list;
    
        public ThreadB(MyList list) {
            super();
            this.list = list;
        }
    
        @Override
        public void run() {
            try {
                while (true) {
                    if (list.size() == 5) {
                        System.out.println("==5, 线程b准备退出了");
                        throw new InterruptedException();
                    }
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public class Test {
    
        public static void main(String[] args) {
            MyList service = new MyList();
    
            ThreadA a = new ThreadA(service);
            a.setName("A");
            a.start();
    
            ThreadB b = new ThreadB(service);
            b.setName("B");
            b.start();
        }
    }

(3)wait/notify机制

线程A要等待某个条件满足时(list.size()==5)，才执行操作。线程B则向list中添加元素，改变list的size。这里用到了Object类的wait()和notify()方法。
当条件未满足时(list.size() !=5)，线程A调用wait()放弃CPU，并进入阻塞状态。---不像②while轮询那样占用CPU。当条件满足时，线程B调用notify()通知线程A，所谓通知线程A，就是唤醒线程A，并让它进入可运行状态。
这种方式的一个好处就是CPU的利用率提高了。但是也有一些缺点：比如，线程B先执行，一下子添加了5个元素并调用了notify()发送了通知，而此时线程A才执行；当线程A执行并调用wait()时，那它永远就不可能被唤醒了。因为，线程B已经发了通知了，以后不再发通知了。这说明：通知过早，会打乱程序的执行逻辑。

    public class MyList {
    
        private static List<String> list = new ArrayList<String>();
    
        public static void add() {
            list.add("anyString");
        }
    
        public static int size() {
            return list.size();
        }
    }
    
    public class ThreadA extends Thread {
    
        private Object lock;
    
        public ThreadA(Object lock) {
            super();
            this.lock = lock;
        }
    
        @Override
        public void run() {
            try {
                synchronized (lock) {
                    if (MyList.size() != 5) {
                        System.out.println("wait begin "
                                + System.currentTimeMillis());
                        lock.wait();
                        System.out.println("wait end  "
                                + System.currentTimeMillis());
                    }
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    
    public class ThreadB extends Thread {
        private Object lock;
    
        public ThreadB(Object lock) {
            super();
            this.lock = lock;
        }
    
        @Override
        public void run() {
            try {
                synchronized (lock) {
                    for (int i = 0; i < 10; i++) {
                        MyList.add();
                        if (MyList.size() == 5) {
                            lock.notify();
                            System.out.println("已经发出了通知");
                        }
                        System.out.println("添加了" + (i + 1) + "个元素!");
                        Thread.sleep(1000);
                    }
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public class Run {
    
        public static void main(String[] args) {
    
            try {
                Object lock = new Object();
    
                ThreadA a = new ThreadA(lock);
                a.start();
    
                Thread.sleep(50);
    
                ThreadB b = new ThreadB(lock);
                b.start();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

(4)管道通信

就是使用java.io.PipedInputStream和java.io.PipedOutputStream进行通信。


13.如何停止一个正在运行的线程

3种方法：
(1)使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
(2)使用Thread.stop()方法强行终止，但是不推荐这个方法，这个方法是不安全的，因为stop和suspend及resume一样都是过期作废的方法。
(3)使用interrupt方法中断线程。

(1)停止不了的线程

interrupt()方法的使用效果并不像for+break语句那样，马上就停止循环。调用interrupt方法是在当前线程中打了一个停止标志，并不是真的停止线程。

    public class MyThread extends Thread {
        public void run(){
            super.run();
            for(int i=0; i<500000; i++){
                System.out.println("i="+(i+1));
            }
        }
    }
    
    public class Run {
        public static void main(String args[]){
            Thread thread = new MyThread();
            thread.start();
            try {
                Thread.sleep(2000);
                thread.interrupt();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    输出结果：
    ...
    i=499994
    i=499995
    i=499996
    i=499997
    i=499998
    i=499999
    i=500000
    
(2)判断线程是否停止状态

Thread.java类中提供了两种方法：

    this.interrupted(): 测试当前线程是否已经中断；
    this.isInterrupted(): 测试线程是否已经中断；

this.interrupted()方法的解释：测试当前线程是否已经中断，当前线程是指运行this.interrupted()方法的线程。
但从控制台打印的结果来看，线程并未停止，这也证明了interrupted()方法的解释，测试当前线程是否已经中断。这个当前线程是main，它从未中断过，所以打印的结果是两个false。

    public class MyThread extends Thread {
        public void run(){
            super.run();
            for(int i=0; i<500000; i++){
                i++;
                // System.out.println("i="+(i+1));
            }
        }
    }
    
    public class Run {
        public static void main(String args[]){
            Thread thread = new MyThread();
            thread.start();
            try {
                Thread.sleep(2000);
                thread.interrupt();
                System.out.println("stop 1??" + thread.interrupted());
                System.out.println("stop 2??" + thread.interrupted());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    运行结果：
    
    stop 1??false
    stop 2??false
  
如何使main线程产生中断效果呢？方法interrupted()的确判断出当前线程是否是停止状态。但为什么第2个布尔值是false呢？官方帮助文档中对interrupted方法的解释：测试当前线程是否已经中断。线程的中断状态由该方法清除。换句话说，如果连续两次调用该方法，则第二次调用返回false。

    public class Run2 {
        public static void main(String args[]){
            Thread.currentThread().interrupt();
            System.out.println("stop 1??" + Thread.interrupted());
            System.out.println("stop 2??" + Thread.interrupted());
    
            System.out.println("End");
        }
    }    
    运行效果为：
    
    stop 1??true
    stop 2??false
    End

inInterrupted()方法：isInterrupted()并未清除状态，所以打印了两个true。

    public class Run3 {
        public static void main(String args[]){
            Thread thread = new MyThread();
            thread.start();
            thread.interrupt();
            System.out.println("stop 1??" + thread.isInterrupted());
            System.out.println("stop 2??" + thread.isInterrupted());
        }
    }
    运行结果：
    
    stop 1??true
    stop 2??true

(3)能停止的线程--异常法
有了前面学习过的知识点，就可以在线程中用for语句来判断一下线程是否是停止状态，如果是停止状态，则后面的代码不再运行即可：

public class MyThread extends Thread {
    public void run(){
        super.run();
        for(int i=0; i<500000; i++){
            if(this.interrupted()) {
                System.out.println("线程已经终止， for循环不再执行");
                break;
            }
            System.out.println("i="+(i+1));
        }
    }
}

public class Run {
    public static void main(String args[]){
        Thread thread = new MyThread();
        thread.start();
        try {
            Thread.sleep(2000);
            thread.interrupt();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
运行结果：

...
i=202053
i=202054
i=202055
i=202056
线程已经终止， for循环不再执行

上面的示例虽然停止了线程，但如果for语句下面还有语句，还是会继续运行的。看下面的例子：

public class MyThread extends Thread {
    public void run(){
        super.run();
        for(int i=0; i<500000; i++){
            if(this.interrupted()) {
                System.out.println("线程已经终止， for循环不再执行");
                break;
            }
            System.out.println("i="+(i+1));
        }

        System.out.println("这是for循环外面的语句，也会被执行");
    }
}
使用Run.java执行的结果是：

...
i=180136
i=180137
i=180138
i=180139
线程已经终止， for循环不再执行

这是for循环外面的语句，也会被执行

如何解决语句继续运行的问题呢？看一下更新后的代码：

public class MyThread extends Thread {
    public void run(){
        super.run();
        try {
            for(int i=0; i<500000; i++){
                if(this.interrupted()) {
                    System.out.println("线程已经终止， for循环不再执行");
                        throw new InterruptedException();
                }
                System.out.println("i="+(i+1));
            }

            System.out.println("这是for循环外面的语句，也会被执行");
        } catch (InterruptedException e) {
            System.out.println("进入MyThread.java类中的catch了。。。");
            e.printStackTrace();
        }
    }
}
使用Run.java运行的结果如下：

...
i=203798
i=203799
i=203800
线程已经终止， for循环不再执行
进入MyThread.java类中的catch了。。。
java.lang.InterruptedException
    at thread.MyThread.run(MyThread.java:13)
4. 在沉睡中停止
如果线程在sleep()状态下停止线程，会是什么效果呢？

public class MyThread extends Thread {
    public void run(){
        super.run();

        try {
            System.out.println("线程开始。。。");
            Thread.sleep(200000);
            System.out.println("线程结束。");
        } catch (InterruptedException e) {
            System.out.println("在沉睡中被停止, 进入catch， 调用isInterrupted()方法的结果是：" + this.isInterrupted());
            e.printStackTrace();
        }

    }
}
使用Run.java运行的结果是：

线程开始。。。
在沉睡中被停止, 进入catch， 调用isInterrupted()方法的结果是：false
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at thread.MyThread.run(MyThread.java:12)
从打印的结果来看， 如果在sleep状态下停止某一线程，会进入catch语句，并且清除停止状态值，使之变为false。

前一个实验是先sleep然后再用interrupt()停止，与之相反的操作在学习过程中也要注意：

public class MyThread extends Thread {
    public void run(){
        super.run();
        try {
            System.out.println("线程开始。。。");
            for(int i=0; i<10000; i++){
                System.out.println("i=" + i);
            }
            Thread.sleep(200000);
            System.out.println("线程结束。");
        } catch (InterruptedException e) {
             System.out.println("先停止，再遇到sleep，进入catch异常");
            e.printStackTrace();
        }

    }
}

public class Run {
    public static void main(String args[]){
        Thread thread = new MyThread();
        thread.start();
        thread.interrupt();
    }
}
运行结果：

i=9998
i=9999
先停止，再遇到sleep，进入catch异常
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at thread.MyThread.run(MyThread.java:15)
5. 能停止的线程---暴力停止
使用stop()方法停止线程则是非常暴力的。

public class MyThread extends Thread {
    private int i = 0;
    public void run(){
        super.run();
        try {
            while (true){
                System.out.println("i=" + i);
                i++;
                Thread.sleep(200);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class Run {
    public static void main(String args[]) throws InterruptedException {
        Thread thread = new MyThread();
        thread.start();
        Thread.sleep(2000);
        thread.stop();
    }
}
运行结果：

i=0
i=1
i=2
i=3
i=4
i=5
i=6
i=7
i=8
i=9

Process finished with exit code 0
6.方法stop()与java.lang.ThreadDeath异常
调用stop()方法时会抛出java.lang.ThreadDeath异常，但是通常情况下，此异常不需要显示地捕捉。

public class MyThread extends Thread {
    private int i = 0;
    public void run(){
        super.run();
        try {
            this.stop();
        } catch (ThreadDeath e) {
            System.out.println("进入异常catch");
            e.printStackTrace();
        }
    }
}

public class Run {
    public static void main(String args[]) throws InterruptedException {
        Thread thread = new MyThread();
        thread.start();
    }
}
stop()方法以及作废，因为如果强制让线程停止有可能使一些清理性的工作得不到完成。另外一个情况就是对锁定的对象进行了解锁，导致数据得不到同步的处理，出现数据不一致的问题。

7. 释放锁的不良后果
使用stop()释放锁将会给数据造成不一致性的结果。如果出现这样的情况，程序处理的数据就有可能遭到破坏，最终导致程序执行的流程错误，一定要特别注意：

public class SynchronizedObject {
    private String name = "a";
    private String password = "aa";

    public synchronized void printString(String name, String password){
        try {
            this.name = name;
            Thread.sleep(100000);
            this.password = password;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

public class MyThread extends Thread {
    private SynchronizedObject synchronizedObject;
    public MyThread(SynchronizedObject synchronizedObject){
        this.synchronizedObject = synchronizedObject;
    }

    public void run(){
        synchronizedObject.printString("b", "bb");
    }
}

public class Run {
    public static void main(String args[]) throws InterruptedException {
        SynchronizedObject synchronizedObject = new SynchronizedObject();
        Thread thread = new MyThread(synchronizedObject);
        thread.start();
        Thread.sleep(500);
        thread.stop();
        System.out.println(synchronizedObject.getName() + "  " + synchronizedObject.getPassword());
    }
}
输出结果：

b  aa
由于stop()方法以及在JDK中被标明为“过期/作废”的方法，显然它在功能上具有缺陷，所以不建议在程序张使用stop()方法。

8. 使用return停止线程
将方法interrupt()与return结合使用也能实现停止线程的效果：

public class MyThread extends Thread {
    public void run(){
        while (true){
            if(this.isInterrupted()){
                System.out.println("线程被停止了！");
                return;
            }
            System.out.println("Time: " + System.currentTimeMillis());
        }
    }
}

public class Run {
    public static void main(String args[]) throws InterruptedException {
        Thread thread = new MyThread();
        thread.start();
        Thread.sleep(2000);
        thread.interrupt();
    }
}
输出结果：

...
Time: 1467072288503
Time: 1467072288503
Time: 1467072288503
线程被停止了！
不过还是建议使用“抛异常”的方法来实现线程的停止，因为在catch块中还可以将异常向上抛，使线程停止事件得以传播。

N.参考

(1)[线程进程区别](https://www.cnblogs.com/toria/p/11123130.html)

(2)[线程与进程的区别](https://www.cnblogs.com/cocoxu1992/p/10468317.html)

(3)[谈谈什么是守护线程以及作用](https://www.jianshu.com/p/3d6f32af5625)

(4)[ThreadLocal](https://www.jianshu.com/p/3c5d7f09dfbd)

# Java锁

1.公平锁/非公平锁

公平锁是指多个线程按照申请锁的顺序来获取锁。
非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能会造成优先级反转或者饥饿现象。
对于Java ReentrantLock而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。
对于Synchronized而言，也是一种非公平锁。由于其并不像ReentrantLock是通过AQS的来实现线程调度，所以并没有任何办法使其变成公平锁。

2.可重入锁：可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。
对于Java ReentrantLock而言, 从名字就可以看出是一个可重入锁，其名字是Re entrant Lock重新进入锁。
对于Synchronized而言，也是一个可重入锁。可重入锁的一个好处是可一定程度避免死锁。下面的代码就是一个可重入锁的一个特点，如果不是可重入锁的话，setB可能不会被当前线程执行，可能造成死锁。

    synchronized void setA() throws Exception{
        Thread.sleep(1000);
        setB();
    }
    
    synchronized void setB() throws Exception{
        Thread.sleep(1000);
    }

3.独享锁/共享锁

独享锁是指该锁一次只能被一个线程所持有。共享锁是指该锁可被多个线程所持有。
对于Java ReentrantLock而言，其是独享锁。但是对于Lock的另一个实现类ReadWriteLock，其读锁是共享锁，其写锁是独享锁。读锁的共享锁可保证并发读是非常高效的，读写，写读 ，写写的过程是互斥的。
独享锁与共享锁也是通过AQS来实现的，通过实现不同的方法，来实现独享或者共享。
对于Synchronized而言，当然是独享锁。

4.互斥锁/读写锁

上面讲的独享锁/共享锁就是一种广义的说法，互斥锁/读写锁就是具体的实现。
互斥锁在Java中的具体实现就是ReentrantLock。
读写锁在Java中的具体实现就是ReadWriteLock。

5.乐观锁/悲观锁

乐观锁与悲观锁不是指具体的什么类型的锁，而是指看待并发同步的角度。
悲观锁认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。因此对于同一个数据的并发操作，悲观锁采取加锁的形式。悲观的认为，不加锁的并发操作一定会出问题。
乐观锁则认为对于同一个数据的并发操作，是不会发生修改的。在更新数据的时候，会采用尝试更新，不断重试的方式更新数据。乐观的认为，不加锁的并发操作是没有事情的。
从上面的描述我们可以看出，悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。
悲观锁在Java中的使用，就是利用各种锁。
乐观锁在Java中的使用，是无锁编程，常常采用的是CAS算法，典型的例子就是原子类，通过CAS自旋实现原子操作的更新。

6.分段锁

分段锁其实是一种锁的设计，并不是具体的一种锁，对于ConcurrentHashMap而言，其并发的实现就是通过分段锁的形式来实现高效的并发操作。
我们以ConcurrentHashMap来说一下分段锁的含义以及设计思想，ConcurrentHashMap中的分段锁称为Segment，它即类似于HashMap（JDK7与JDK8中HashMap的实现）的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表；同时又是一个ReentrantLock（Segment继承了ReentrantLock)。
当需要put元素的时候，并不是对整个hashmap进行加锁，而是先通过hashcode来知道它要放在那一个分段中，然后对这个分段进行加锁，所以当多线程put的时候，只要不是放在一个分段中，就实现了真正的并行的插入。
但是，在统计size的时候，可就是获取hashmap全局信息的时候，就需要获取所有的分段锁才能统计。
分段锁的设计目的是细化锁的粒度，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。

7.偏向锁/轻量级锁/重量级锁

这三种锁是指锁的状态，并且是针对Synchronized。
在Java 5通过引入锁升级的机制来实现高效Synchronized。这三种锁的状态是通过对象监视器在对象头中的字段来表明的。
偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。
轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。
重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让其他申请的线程进入阻塞，性能降低。

8.自旋锁

在Java中，自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。

9.监视器

监视器和锁在Java虚拟机中是一块使用的。监视器监视一块同步代码块，确保一次只有一个线程执行同步代码块。每一个监视器都和一个对象引用相关联。线程在获取锁之前不允许执行同步代码。java还提供了显式监视器(Lock)和隐式监视器(synchronized)两种锁方案。

10.死锁

两个线程或两个以上线程都在等待对方执行完毕才能继续往下执行的时候就发生了死锁。结果就是这些线程都陷入了无限的等待中。
多线程产生死锁的四个必要条件：
互斥条件：一个资源每次只能被一个进程使用。
保持和请求条件：一个进程因请求资源而阻塞时，对已获得资源保持不放。
不可剥夺性：进程已获得资源，在未使用完成前，不能被剥夺。
循环等待条件（闭环）：若干进程之间形成一种头尾相接的循环等待资源关系。
只要破坏其中任意一个条件，就可以避免死锁，一种非常简单的避免死锁的方式就是：指定获取锁的顺序，并强制线程按照指定的顺序获取锁。因此，如果所有的线程都是以同样的顺序加锁和释放锁，就不会出现死锁了。
