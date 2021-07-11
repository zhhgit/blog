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

并发，指的是多个事情，在同一时间段内同时发生了。并发的多个任务之间是互相抢占资源的。
并行，指的是多个事情，在同一时间点上同时发生了。并行的多个任务之间是不互相抢占资源的。只有在多CPU的情况中，才会发生并行。否则，看似同时发生的事情，其实都是并发执行的。

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

(2)实现Runnable接口。

(3)应用程序可以使用Executor框架来创建线程池。

(4)实现Callable接口，通过如下方式获取线程执行结果。

    private class MyCallable implements Callable<Boolean> {

        private String product;
        private JSONObject config;

        public MyCallable(String product, JSONObject config) {
            this.product = product;
            this.config = config;
        }

        @Override
        public Boolean call() throws Exception {
            return false;
        }
    }

    FutureTask<Boolean> futureTask = new FutureTask<Boolean>(new MyCallable(product, config));
    Thread thread = new Thread(futureTask);
    thread.start();
    bollean result = futureTask.get();

采用实现Runnable、Callable接口的方式创建多线程时，优势是线程类只是实现了Runnable接口或Callable接口，还可以继承其他类。在这种方式下，多个线程可以共享同一个target对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU、代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
线程池也是非常高效的，很容易实现和使用。

5.线程状态

(1)新建(new)：新创建了一个线程对象。当线程创建完成时为新建状态，即new Thread(…)，还没有调用start方法时，线程处于新建状态。
(2)可运行(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权。
(3)运行(running)：可运行状态(runnable )的线程获得了CPU时间片（timeslice），执行程序代码。
(4)阻塞(blocked)：阻塞状态是指线程因为某种原因放弃了CPU使用权，也即让出了CPU timeslice ，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice转到运行(running)状态。阻塞的情况分三种：
等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。
同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。
其他阻塞: 运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。
(5)死亡(terminated)：线程run()、 main()方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

6.同步方法和同步代码块的区别

同步方法默认用this或者当前类class对象作为锁。
同步代码块可以选择以什么来加锁，比同步方法要更细颗粒度，我们可以选择只同步会发生同步问题的部分代码而不是整个方法。

7.ThreadLocal

    static final ThreadLocal<T> sThreadLocal = new ThreadLocal<T>();
    sThreadLocal.set(T)
    sThreadLocal.get()
    sThreadLocal.remove()
    
ThreadLocal的静态内部类ThreadLocalMap为每个Thread都维护了一个数组table，ThreadLocal确定了一个数组下标，而这个下标就是value存储的对应位置。
对于某一ThreadLocal来讲，它的索引值i是确定的，在不同线程之间访问时访问的是不同的table数组的同一位置即都为table[i]，只不过这个不同线程之间的table是独立的。
对于同一线程的不同ThreadLocal来讲，这些ThreadLocal实例共享一个table数组，然后每个ThreadLocal实例在table中的索引i是不同的。
ThreadLocal和Synchronized都是为了解决多线程中相同变量的访问冲突问题，不同的点是Synchronized是通过线程等待，牺牲时间来解决访问冲突。ThreadLocal是通过每个线程单独一份存储空间，牺牲空间来解决冲突，并且相比于Synchronized，ThreadLocal具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问到想要的值。

8.Java的线程安全与不安全

多个线程之间是不能直接传递数据进行交互的，它们之间的交互只能通过共享变量来实现。
例如在多个线程之间共享了Count类的一个实例，这个对象是被创建在主内存（堆内存）中，每个线程都有自己的工作内存（线程栈），工作内存存储了主内存count对象的一个副本，当线程操作count对象时，首先从主内存复制count对象到工作内存中，然后执行代码count.count()，改变了num值，最后用工作内存中的count刷新主内存的count。当一个对象在多个工作内存中都存在副本时，如果一个工作内存刷新了主内存中的共享变量，其它线程也应该能够看到被修改后的值，此为可见性。
多个线程执行时，CPU对线程的调度是随机的，我们不知道当前程序被执行到哪步就切换到了下一个线程，一个最经典的例子就是银行汇款问题，一个银行账户存款100，这时一个人从该账户取10元，同时另一个人向该账户汇10元，那么余额应该还是100。那么此时可能发生这种情况，A线程负责取款，B线程负责汇款，A从主内存读到100，B从主内存读到100，A执行减10操作，并将数据刷新到主内存，这时主内存数据100-10=90，而B内存执行加10操作，并将数据刷新到主内存，最后主内存数据100+10=110，显然这是一个严重的问题，我们要保证A线程和B线程有序执行，先取款后汇款或者先汇款后取款，此为有序性。

在Web开发方面，Servlet是否是线程安全的呢？
Servlet不是线程安全的。要解释为什么Servlet为什么不是线程安全的，需要了解Servlet容器（如Tomcat）是如何响应HTTP请求的。当Tomcat接收到Client的HTTP请求时，Tomcat从线程池中取出一个线程，之后找到该请求对应的Servlet对象并进行初始化，之后调用service()方法。
要注意的是每一个Servlet对象在Tomcat容器中只有一个实例对象，即是单例模式。如果多个HTTP请求请求的是同一个Servlet，那么这两个HTTP请求对应的线程将并发调用Servlet的service()方法。如果的Thread1和Thread2调用了同一个Servlet1，Servlet1中定义了成员变量或静态变量，那么可能会发生线程安全问题（因为所有的线程都可能使用这些变量）。
像Servlet这样的类，在Web容器中创建以后，会被传递给每个访问Web应用的用户线程执行，这个类就不是线程安全的。但这并不意味着一定会引发线程安全问题，如果Servlet类里没有成员变量，即使多线程同时执行这个Servlet实例的方法，也不会造成成员变量冲突。
这种对象被称作无状态对象，也就是说对象不记录状态，执行这个对象的任何方法都不会改变对象的状态，也就不会有线程安全问题了。事实上，Web开发实践中，常见的Service类、DAO类，都被设计成无状态对象，所以虽然我们开发的Web应用都是多线程的应用，因为Web容器一定会创建多线程来执行我们的代码，但是我们开发中却可以很少考虑线程安全的问题。

9.多线程的常见应用场景

吞吐量：WEB容器帮你做了多线程，但是只能帮做请求层面的。简单的说，可能就是一个请求一个线程。或多个请求一个线程。如果是单线程，那同时只能处理一个用户的请求。
伸缩性：可以通过增加CPU核数来提升性能。如果是单线程，那程序执行到死也就利用了单核，肯定没办法通过增加CPU核数来提升性能。

后台任务，例如：定时向大量（100w以上）的用户发送邮件；
异步处理，例如：发微博、记录日志等；
分布式计算。

10.为什么Java线程没有Running状态？

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

11.Java线程之间通信方式

分布式系统中说的两种通信机制：共享内存机制和消息通信机制。
synchronized关键字和while轮询属于共享内存机制。
wait/notify机制、管道通信，是消息传递机制，管道通信通过管道将一个线程中的消息发送给另一个。

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

12.如何停止一个正在运行的线程

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
  
如何使main线程产生中断效果呢？
方法interrupted()的确判断出当前线程是否是停止状态。但为什么第2个布尔值是false呢？官方帮助文档中对interrupted方法的解释：测试当前线程是否已经中断。线程的中断状态由该方法清除。换句话说，如果连续两次调用该方法，则第二次调用返回false。

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
    
当使用isInterrupted()，isInterrupted()并未清除状态，所以打印了两个true。

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
    
(4)在沉睡中停止

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
        
(5)能停止的线程---暴力停止

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
    
(6)方法stop()与java.lang.ThreadDeath异常

调用stop()方法时会抛出java.lang.ThreadDeath异常，但是通常情况下，此异常不需要显式地捕捉。

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
    
stop()方法已经作废，因为如果强制让线程停止有可能使一些清理性的工作得不到完成。另外一个情况就是对锁定的对象进行了解锁，导致数据得不到同步的处理，出现数据不一致的问题。

(7)释放锁的不良后果

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

(8)使用return停止线程

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

13.wait/notify/notifyAll

(1)wait

    wait()方法的作用是将当前运行的线程挂起（即让其进入阻塞状态），直到notify或notifyAll方法来唤醒线程。
    wait(long timeout)，该方法与wait()方法类似，唯一的区别就是在指定时间内，如果没有notify或notifAll方法的唤醒，也会自动唤醒。
    wait(long timeout,long nanos)，本意在于更精确的控制调度时间，不过从目前版本来看，该方法貌似没有完整的实现该功能，从源码来看，JDK8中对纳秒的处理，只做了四舍五入，所以还是按照毫秒来处理的，可能在未来的某个时间点会用到纳秒级别的精度。

虽然JDK提供了这三个版本，其实最后都是调用wait(long timeout)方法来实现的，wait()方法与wait(0)等效，而wait(long timeout,int nanos)从上面的源码可以看到也是通过wait(long timeout)来完成的。
wait方法的使用必须在同步的范围内，否则就会抛出IllegalMonitorStateException异常。wait方法是一个本地方法，其底层是通过一个叫做监视器锁的对象来完成的。之所以会抛出异常IllegalMonitorStateException，是因为在调用wait方式时没有获取到monitor对象的所有权。
那如何获取monitor对象所有权？Java中只能通过Synchronized关键字来完成，修改包含wait的函数或者代码库，增加Synchronized关键字。
调用wait方法后，线程是会释放对monitor对象的所有权的。

(2)notify/notifyAll

有了对wait方法原理的理解，notify方法和notifyAll方法就很容易理解了。既然wait方式是通过对象的monitor对象来实现的，所以只要在同一对象上去调用notify/notifyAll方法，就可以唤醒对应对象monitor上等待的线程了。
notify和notifyAll的区别在于前者只能唤醒monitor上的一个线程，对其他线程没有影响，而notifyAll则唤醒所有的线程，看下面的例子很容易理解这两者的差别：
一个通过wait方法阻塞的线程，必须同时满足以下两个条件才能被真正执行：线程需要被唤醒（超时唤醒或调用notify/notifyll）。线程唤醒后需要竞争到锁（monitor）。

14.sleep/yield/join

这几个方法都位于Thread类中，而wait/notify/notifyAll都位于Object类中。

(1)sleep

sleep方法的作用是让当前线程暂停指定的时间（毫秒），sleep方法是最简单的方法，在上述的例子中也用到过，比较容易理解。唯一需要注意的是其与wait方法的区别。
最简单的区别是，wait方法依赖于同步，而sleep方法可以直接调用。而更深层次的区别在于sleep方法只是暂时让出CPU的执行权，并不释放锁。而wait方法则需要释放锁。
通过sleep方法实现的暂停，程序是顺序进入同步块的，只有当上一个线程执行完成的时候，下一个线程才能进入同步方法，sleep暂停期间一直持有monitor对象锁，其他线程是不能进入的。
而wait方法则不同，当调用wait方法后，当前线程会释放持有的monitor对象锁，因此，其他线程还可以进入到同步方法，线程被唤醒后，需要竞争锁，获取到锁之后再继续执行。

(2)yield

yield方法的作用是暂停当前线程，以便其他线程有机会执行，不过不能指定暂停的时间，并且也不能保证当前线程马上停止。yield方法只是将Running状态转变为Runnable状态。
通过yield方法来实现两个线程的交替执行。不过请注意：这种交替并不一定能得到保证。
调度器可能会忽略该方法。使用的时候要仔细分析和测试，确保能达到预期的效果。很少有场景要用到该方法，主要使用的地方是调试和测试。　　

(3)join方法

join方法的作用是父线程等待子线程执行完成后再执行，换句话说就是将异步执行的线程合并为同步的线程。
JDK中提供三个版本的join方法，其实现与wait方法类似，join()方法实际上执行的join(0)，而join(long millis, int nanos)也与wait(long millis, int nanos)的实现方式一致，暂时对纳秒的支持也是不完整的。
join方法就是通过wait方法来将线程的阻塞，如果join的线程还在执行，则将当前线程阻塞起来，直到join的线程执行完成，当前线程才能执行。
不过有一点需要注意，这里的join只调用了wait方法，却没有对应的notify方法，原因是Thread的start方法中做了相应的处理，所以当join的线程执行完成以后，会自动唤醒主线程继续往下执行。
在没有使用join方法之前，线程是并发执行的，而使用join方法后，所有线程是顺序执行的。

15.阻塞队列

(1)什么是阻塞队列？

当队列为空时，消费者挂起，队列已满时，生产者挂起，这就是生产-消费者模型，阻塞其实就是将线程挂起。
因为生产者的生产速度和消费者的消费速度之间的不匹配，就可以通过阻塞队列让速度快的暂时阻塞。
如生产者每秒生产两个数据，而消费者每秒消费一个数据，当队列已满时，生产者就会阻塞（挂起），等待消费者消费后，再进行唤醒。
阻塞队列会通过挂起的方式来实现生产者和消费者之间的平衡，这是和普通队列最大的区别。

(2)如何实现阻塞队列？BlockingQueue如何使用？

jdk其实已经帮我们提供了实现方案，java5增加了concurrent包，concurrent包中的BlockingQueue就是阻塞队列，我们不需要关心BlockingQueue如何实现阻塞，一切都帮我们封装好了，只需要做一个没有感情的API调用者就行。
BlockingQueue本身只是一个接口，规定了阻塞队列的方法，主要依靠几个实现类实现。
BlockingQueue主要方法:

按功能分类：

    插入数据：
    
        offer(E e)：如果队列没满，返回true，如果队列已满，返回false（不阻塞）
        offer(E e, long timeout, TimeUnit unit)：可以设置等待时间，如果队列已满，则进行等待。超过等待时间，则返回false
        put(E e)：无返回值，一直等待，直至队列空出位置
    
    获取数据：
    
        poll()：如果有数据，出队，如果没有数据，返回null
        poll(long timeout, TimeUnit unit)：可以设置等待时间，如果没有数据，则等待，超过等待时间，则返回null
        take()：如果有数据，出队。如果没有数据，一直等待（阻塞）

按特点分类：

    抛出异常：add(e) 、remove()、element()
    返回特殊值：offer(e) 、pool()、peek()
    阻塞：put(e) 、take()

(3)BlockingQueue主要实现类

    ArrayBlockingQueue：ArrayBlockingQueue是基于数组实现的，通过初始化时设置数组长度，是一个有界队列，而且ArrayBlockingQueue和LinkedBlockingQueue不同的是，ArrayBlockingQueue只有一个锁对象，而LinkedBlockingQueue是两个锁对象，一个锁对象会造成要么是生产者获得锁，要么是消费者获得锁，两者竞争锁，无法并行。
    LinkedBlockingQueue：LinkedBlockingQueue是基于链表实现的，无界阻塞队列，和ArrayBlockingQueue不同的是，大小可以初始化设置，如果不设置，默认设置大小为Integer.MAX_VALUE，LinkedBlockingQueue有两个锁对象，可以并行处理。
    DelayQueue：DelayQueue是基于优先级的一个无界队列，队列元素必须实现Delayed接口，支持延迟获取，元素按照时间排序，只有元素到期后，消费者才能从队列中取出。
    PriorityBlockingQueue：PriorityBlockingQueue是基于优先级的一个无界队列，底层是基于数组存储元素的，元素按照优选级顺序存储，优先级是通过Comparable的compareTo方法来实现的（自然排序），和其他阻塞队列不同的是，其只会阻塞消费者，不会阻塞生产者，数组会不断扩容，这就是一个彩蛋，使用时要谨慎。
    SynchronousQueue：SynchronousQueue是一个特殊的队列，其内部是没有容器的，所以生产者生产一个数据，就阻塞了，必须等消费者消费后，生产者才能再次生产，称其为队列有点不合适，现实生活中，多个人才能称为队，一个人称为队有些说不过去。
    LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
    LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

16.线程状态转换的方法

    New-->Running及Runnable
        --Thread.start()

    Running及Runnable-->Waiting
        --Object.wait()
        --Thread.join()
        --LockSupport.park()

    Waiting-->Running及Runnable
        --Object.notify()
        --Object.notifyAll()
        --LockSupport.unpark()

    Running及Runnable-->Timed_Waiting
        --Object.wait(long)
        --Thread.join(long)
        --LockSupport.parkUtil()
        --LockSupport.parkNanos()
        --Thread.sleep(long)

    Timed_Waiting-->Running及Runnable
        --Object.notify()
        --Object.notifyAll()
        --LockSupport.unpark()
        --超时时间到

    Running及Runnable-->Blocked
        --等待进入synchronized标识的方法或代码块

    Blocked-->Running及Runnable
        --获取到锁

    Running及Runnable-->Terminated
        --执行完成

17.并发特性

    原子性：即一个操作或者多个操作 要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。
    可见性：指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。
    有序性：即程序执行的顺序按照代码的先后顺序执行，不进行指令重排列。

18.怎么控制线程，尽可能减少上下文切换？

减少上下文切换的方法有无锁并发编程、CAS算法、使用最少线程和使用协程。

    无锁并发编程。多线程竞争锁时，会引起上下文切换，所以多线程处理数据时，可以使用一些方法来避免使用锁。如将数据的ID按照Hash算法取模分段，不同的线程处理不同段的数据。
    CAS算法。Java的Atomic包使用CAS算法来更新数据，而不需要加锁。
    使用最少线程。避免创建不需要的线程，比如任务很少，但是创建了很多线程来处理，这样会造成大量线程都处于等待状态。
    协程。在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。

N.参考

(1)[线程进程区别](https://www.cnblogs.com/toria/p/11123130.html)

(2)[线程与进程的区别](https://www.cnblogs.com/cocoxu1992/p/10468317.html)

(3)[谈谈什么是守护线程以及作用](https://www.jianshu.com/p/3d6f32af5625)

(4)[ThreadLocal](https://www.jianshu.com/p/3c5d7f09dfbd)

# 线程池

1.线程池优点/为什么使用线程池

为每个请求创建新线程的服务器在创建和销毁线程上花费的时间和消耗的系统资源要比花在处理实际的用户请求的时间和资源更多。容易引起资源不足，造成浪费。为解决单个任务处理时间很短而请求的数目巨大的问题，引出线程池。
通过对多个任务重用线程，线程创建的开销被分摊到了多个任务上。其好处是，因为在请求到达时线程已经存在，所以无意中也消除了线程创建所带来的延迟，使应用程序响应更快；
通过适当地调整线程池中的线程数目，也就是当请求的数目超过某个阈值时，就强制其它任何新到的请求一直等待，直到获得一个线程来处理为止，从而可以防止资源不足。控制线程池的并发数可以有效的避免大量的线程池争夺CPU资源而造成阻塞。
线程池可以对线程进行管理：线程池可以提供定时、定期、单线程、并发数控制等功能。

2.使用线程池的风险

用线程池构建的应用程序容易遭受任何其它多线程应用程序容易遭受的所有并发风险，诸如同步错误和死锁，它还容易遭受特定于线程池的少数其它风险，诸如与池有关的死锁、资源不足和线程泄漏。

(1)死锁：任何多线程应用程序都有死锁风险。当一组进程或线程中的每一个都在等待一个只有该组中另一个进程才能引起的事件时，我们就说这组进程或线程死锁了。
虽然任何多线程程序中都有死锁的风险，但线程池却引入了另一种死锁可能，在那种情况下，所有池线程都在执行已阻塞的等待队列中另一任务的执行结果的任务，但这一任务却因为没有未被占用的线程而不能运行。

(2)资源不足：线程池的一个优点在于，相对于其它替代调度机制而言，它们通常执行得很好。但只有恰当地调整了线程池大小时才是这样的。
线程消耗包括内存和其它系统资源在内的大量资源。除了Thread对象所需的内存之外，每个线程都需要两个可能很大的执行调用堆栈。除此以外，JVM可能会为每个Java线程创建一个本机线程，这些本机线程将消耗额外的系统资源。最后，虽然线程之间切换的调度开销很小，但如果有很多线程，环境切换也可能严重地影响程序的性能。
如果线程池太大，那么被那些线程消耗的资源可能严重地影响系统性能。在线程之间进行切换将会浪费时间，而且使用超出比您实际需要的线程可能会引起资源匮乏问题，因为池线程正在消耗一些资源，而这些资源可能会被其它任务更有效地利用。除了线程自身所使用的资源以外，服务请求时所做的工作可能需要其它资源，例如JDBC连接、套接字或文件。这些也都是有限资源，有太多的并发请求也可能引起失效，例如不能分配JDBC连接。

(3)线程泄漏：各种类型的线程池中一个严重的风险是线程泄漏，当从池中除去一个线程以执行一项任务，而在任务完成后该线程却没有返回池时，会发生这种情况。发生线程泄漏的一种情形出现在任务抛出一个RuntimeException或一个Error时。如果池类没有捕捉到它们，那么线程只会退出而线程池的大小将会永久减少一个。当这种情况发生的次数足够多时，线程池最终就为空，而且系统将停止，因为没有可用的线程来处理任务。
有些任务可能会永远等待某些资源或来自用户的输入，而这些资源又不能保证变得可用，用户可能也已经回家了，诸如此类的任务会永久停止，而这些停止的任务也会引起和线程泄漏同样的问题。如果某个线程被这样一个任务永久地消耗着，那么它实际上就被从池除去了。对于这样的任务，应该要么只给予它们自己的线程，要么只让它们等待有限的时间。

3.有效使用线程池的准则

不要对那些同步等待其它任务结果的任务排队。这可能会导致上面所描述的那种形式的死锁，在那种死锁中，所有线程都被一些任务所占用，这些任务依次等待排队任务的结果，而这些任务又无法执行，因为所有的线程都很忙。
在为时间可能很长的操作使用合用的线程时要小心。如果程序必须等待诸如 I/O 完成这样的某个资源，那么请指定最长的等待时间，以及随后是失效还是将任务重新排队以便稍后执行。这样做保证了：通过将某个线程释放给某个可能成功完成的任务，从而将最终取得某些进展。
理解任务。要有效地调整线程池大小，您需要理解正在排队的任务以及它们正在做什么。它们是 CPU 限制的（CPU-bound）吗？它们是 I/O 限制的（I/O-bound）吗？您的答案将影响您如何调整应用程序。如果您有不同的任务类，这些类有着截然不同的特征，那么为不同任务类设置多个工作队列可能会有意义，这样可以相应地调整每个池。

4.线程池的大小设置

调整线程池的大小基本上就是避免两类错误：线程太少或线程太多。
虽然线程池大小的设置受到很多因素影响，但是这里给出一个参考公式：最佳线程数目 = （（线程等待时间+线程CPU时间）/线程CPU时间 ）* CPU数目
比如平均每个线程CPU运行时间为0.5s，而线程等待时间（非CPU运行时间，比如IO）为1.5s，CPU核心数为8，那么根据上面这个公式估算得到：((0.5+1.5)/0.5)*8=32。这个公式进一步转化为：最佳线程数目 = （线程等待时间与线程CPU时间之比 + 1）* CPU数目
线程等待时间所占比例越高，需要越多线程。线程CPU时间所占比例越高，需要越少线程。

5.ThreadPoolExecutor

ThreadPoolExecutor里面使用到JUC同步器框架AbstractQueuedSynchronizer（俗称AQS）、大量的位操作、CAS操作。ThreadPoolExecutor提供了固定活跃线程（核心线程）、额外的线程（线程池容量 - 核心线程数这部分额外创建的线程，下面称为非核心线程）、任务队列以及拒绝策略这几个重要的功能。

构造函数及七个参数的含义：

    public ThreadPoolExecutor(int corePoolSize,  
                              int maximumPoolSize,  
                              long keepAliveTime,  
                              TimeUnit unit,  
                              BlockingQueue<Runnable> workQueue,  
                              ThreadFactory threadFactory,  
                              RejectedExecutionHandler handler)

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

使用无界队列的线程池会导致内存飙升吗？
会的，newFixedThreadPool使用了无界的阻塞队列LinkedBlockingQueue，如果线程获取一个任务后，任务的执行时间比较长，会导致队列的任务越积越多，导致机器内存使用不停飙升， 最终导致OOM。

线程回收：
ThreadPoolExecutor回收工作线程，一条线程getTask()返回null，就会被回收。分两种场景。
(1)未调用shutdown() ，RUNNING状态下全部任务执行完成的场景：线程数量大于corePoolSize，线程获取任务但超时阻塞，超时唤醒后CAS减少工作线程数，如果CAS成功，返回null，线程回收。否则进入下一次循环。当工作者线程数量小于等于corePoolSize，就可以一直阻塞了。
(2)调用shutdown() ，全部任务执行完成的场景：shutdown()会向所有线程发出中断信号，这时有两种可能。
(2.1)所有线程都在阻塞，中断唤醒，进入循环，都符合第一个if判断条件（线程池的状态已经是STOP，TIDYING, TERMINATED，或者是SHUTDOWN且工作队列为空），都返回null，所有线程回收。
(2.2)任务还没有完全执行完，至少会有一条线程被回收。在processWorkerExit(Worker w, boolean completedAbruptly)方法里会调用tryTerminate()，向任意空闲线程发出中断信号。所有被阻塞的线程，最终都会被一个个唤醒，回收。

6.典型的线程池

(1)newFixedThreadPool

创建一个指定工作线程数量的线程池。每当提交一个任务就创建一个工作线程，如果工作线程数量达到线程池初始的最大数，则将提交的任务存入到池队列中。
其corePoolSize=maximumPoolSize，且keepAliveTime为0，适合线程稳定的场景。使用LinkedBlockingQueue。
FixedThreadPool是一个典型且优秀的线程池，它具有线程池提高程序效率和节省创建线程时所耗的开销的优点。但是，在线程池空闲时，即线程池中没有可运行任务时，它不会释放工作线程，还会占用一定的系统资源。

    public class ThreadPoolExecutorTest {
     public static void main(String[] args) {
      ExecutorService fixedThreadPool = Executors.newFixedThreadPool(3);
      for (int i = 0; i < 10; i++) {
       final int index = i;
       fixedThreadPool.execute(new Runnable() {
        public void run() {
         try {
          System.out.println(index);
          Thread.sleep(2000);
         } catch (InterruptedException e) {
          e.printStackTrace();
         }
        }
       });
      }
     }
    }

(2)newSingleThreadExecutor

Single中文解释为单一。结合在一起解释单一的线程池，说的更全面点就是，有固定数量线程的线程池，且数量为一，从数学的角度来看SingleThreadPool应该属于FixedThreadPool的子集。其corePoolSize=maximumPoolSize=1,且keepAliveTime为0，适合线程同步操作的场所。使用LinkedBlockingQueue。
创建一个单线程化的Executor，即只创建唯一的工作者线程来执行任务，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。如果这个线程异常结束，会有另一个取代它，保证顺序执行。单工作线程最大的特点是可保证顺序地执行各个任务，并且在任意给定的时间不会有多个线程是活动的。

    public class ThreadPoolExecutorTest {
     public static void main(String[] args) {
      ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
      for (int i = 0; i < 10; i++) {
       final int index = i;
       singleThreadExecutor.execute(new Runnable() {
        public void run() {
         try {
          System.out.println(index);
          Thread.sleep(2000);
         } catch (InterruptedException e) {
          e.printStackTrace();
         }
        }
       });
      }
     }
    }

(3)newCachedThreadPool

Cached中文解释为储存。结合在一起解释储存的线程池，说的更通俗易懂，既然要储存，其容量肯定是很大，所以它的corePoolSize=0，maximumPoolSize=Integer.MAX_VALUE(2^32-1一个很大的数字)。使用SynchronousQueue。
创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。这种类型的线程池特点是：工作线程的创建数量几乎没有限制(其实也有限制的,数目为Interger. MAX_VALUE), 这样可灵活的往线程池中添加线程。
如果长时间没有往线程池中提交任务，即如果工作线程空闲了指定的时间(默认为1分钟)，则该工作线程将自动终止。终止后，如果你又提交了新的任务，则线程池重新创建一个工作线程。
在使用CachedThreadPool时，一定要注意控制任务的数量，否则，由于大量线程同时运行，很有会造成系统瘫痪。

    public class ThreadPoolExecutorTest {
     public static void main(String[] args) {
      ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
      for (int i = 0; i < 10; i++) {
       final int index = i;
       try {
        Thread.sleep(index * 1000);
       } catch (InterruptedException e) {
        e.printStackTrace();
       }
       cachedThreadPool.execute(new Runnable() {
        public void run() {
         System.out.println(index);
        }
       });
      }
     }
    }

(4)newScheduleThreadPool

创建一个定长的线程池，而且支持定时的以及周期性的任务执行，支持定时及周期性任务执行。
Scheduled中文解释为计划。结合在一起解释计划的线程池，顾名思义既然涉及到计划，必然会涉及到时间。所以ScheduledThreadPool是一个具有定时定期执行任务功能的线程池。使用DelayedWorkQueue。

    // 表示延迟1秒后每3秒执行一次
    public class ThreadPoolExecutorTest {
     public static void main(String[] args) {
      ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);
      scheduledThreadPool.scheduleAtFixedRate(new Runnable() {
       public void run() {
        System.out.println("delay 1 seconds, and excute every 3 seconds");
       }
      }, 1, 3, TimeUnit.SECONDS);
     }
    }
    // 延迟3秒执行
    public class ThreadPoolExecutorTest {
     public static void main(String[] args) {
      ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);
      scheduledThreadPool.schedule(new Runnable() {
       public void run() {
        System.out.println("delay 3 seconds");
       }
      }, 3, TimeUnit.SECONDS);
     }
    }

7.线程池中多余的线程回收

ThreadPoolExecutor回收工作线程，一条线程getTask()返回null，就会被回收。两种场景如下：

(1)未调用shutdown()，RUNNING状态下全部任务执行完成的场景：线程数量大于corePoolSize，线程超时阻塞，超时唤醒后CAS减少工作线程数，如果CAS成功，返回null，线程回收。否则进入下一次循环。当工作者线程数量小于等于corePoolSize，就可以一直阻塞了。
(2)调用shutdown()，全部任务执行完成的场景：shutdown()会向所有线程发出中断信号，这时有两种可能。
(a)所有线程都在阻塞，中断唤醒，进入循环，都符合第一个if判断条件，都返回null，所有线程回收。
(b)任务还没有完全执行完，至少会有一条线程被回收。在processWorkerExit(Worker w, boolean completedAbruptly)方法里会调用tryTerminate()，向任意空闲线程发出中断信号。所有被阻塞的线程，最终都会被一个个唤醒，回收。

8.线程池有五种状态

    RUNNING：接收并处理任务。
    SHUTDOWN：不接收但处理现有任务。
    STOP：不接收也不处理任务，同时中断当前处理的任务。
    TIDYING：所有任务终止，线程池会变为TIDYING状态。当线程池变为TIDYING状态时，会执行钩子函数terminated()。
    TERMINATED：线程池彻底终止的状态。

# Java锁

1.公平锁/非公平锁

(1)公平锁是指多个线程按照申请锁的顺序来获取锁。
(2)非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能会造成优先级反转或者饥饿现象。

对于Java ReentrantLock而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。
对于Synchronized而言，也是一种非公平锁。由于其并不像ReentrantLock是通过AQS的来实现线程调度，所以并没有任何办法使其变成公平锁。

2.可重入锁

可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。对于Java ReentrantLock而言, 从名字就可以看出是一个可重入锁。
对于Synchronized而言，也是一个可重入锁。可重入锁的一个好处是可一定程度避免死锁。下面的代码就是一个可重入锁的一个特点，如果不是可重入锁的话，setB可能不会被当前线程执行，可能造成死锁。

    synchronized void setA() throws Exception{
        Thread.sleep(1000);
        setB();
    }
    
    synchronized void setB() throws Exception{
        Thread.sleep(1000);
    }

3.独享锁/共享锁（也见数据库相关内容）

(1)独享锁是指该锁一次只能被一个线程所持有。对于Java ReentrantLock而言，其是独享锁、互斥锁。对于Synchronized而言，当然是独享锁。
(2)共享锁是指该锁可被多个线程所持有。对于Lock的另一个实现类读写锁ReadWriteLock，其读锁是共享锁，其写锁是独享锁。读锁的共享锁可保证并发读是非常高效的，读写，写读 ，写写的过程是互斥的。

独享锁与共享锁也是通过AQS来实现的，通过实现不同的方法，来实现独享或者共享。

4.乐观锁/悲观锁（也见数据库相关内容）

乐观锁与悲观锁不是指具体的什么类型的锁，而是指看待并发同步的角度。
(1)悲观锁：认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。因此对于同一个数据的并发操作，悲观锁采取加锁的形式。悲观的认为，不加锁的并发操作一定会出问题。悲观锁在Java中的使用，就是利用各种锁。悲观锁适合写操作非常多的场景。
(2)乐观锁则认为对于同一个数据的并发操作，是不会发生修改的。在更新数据的时候，会采用尝试更新，不断重试的方式更新数据。乐观的认为，不加锁的并发操作是没有事情的。乐观锁在Java中的使用，是无锁编程，常常采用的是CAS算法，典型的例子就是原子类，通过CAS自旋实现原子操作的更新。乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。

5.分段锁

分段锁其实是一种锁的设计，并不是具体的一种锁，对于ConcurrentHashMap而言，其并发的实现就是通过分段锁的形式来实现高效的并发操作。
我们以ConcurrentHashMap来说一下分段锁的含义以及设计思想，ConcurrentHashMap中的分段锁称为Segment，它即类似于HashMap（JDK7与JDK8中HashMap的实现）的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表；同时又是一个ReentrantLock（Segment继承了ReentrantLock)。
当需要put元素的时候，并不是对整个hashmap进行加锁，而是先通过hashcode来知道它要放在那一个分段中，然后对这个分段进行加锁，所以当多线程put的时候，只要不是放在一个分段中，就实现了真正的并行的插入。
但是，在统计size的时候，可就是获取hashmap全局信息的时候，就需要获取所有的分段锁才能统计。
分段锁的设计目的是细化锁的粒度，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。

6.偏向锁/轻量级锁/重量级锁

这三种锁是指锁的状态，并且是针对Synchronized。
在Java 5通过引入锁升级的机制来实现高效Synchronized。这三种锁的状态是通过对象监视器在对象头中的字段来表明的。

(1)偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。
(2)轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。
(3)重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让其他申请的线程进入阻塞，性能降低。

7.自旋锁

在Java中，自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。
java线程其实是映射在内核之上的，线程的挂起和恢复会极大的影响开销。并且jdk官方人员发现，很多线程在等待锁的时候，在很短的一段时间就获得了锁，所以它们在线程等待的时候，并不需要把线程挂起，而是让它无目的的循环，一般设置10次。这样就避免了线程切换的开销，极大的提升了性能。
而适应性自旋，是赋予了自旋一种学习能力，它并不固定自旋10次一下。可以根据它前面线程的自旋情况，从而调整它的自旋，甚至是不经过自旋而直接挂起。

8.监视器

监视器和锁在Java虚拟机中是一块使用的。监视器监视一块同步代码块，确保一次只有一个线程执行同步代码块。每一个监视器都和一个对象引用相关联。线程在获取锁之前不允许执行同步代码。
java还提供了显式监视器(Lock)和隐式监视器(synchronized)两种锁方案。

9.死锁

两个线程或两个以上线程都在等待对方执行完毕才能继续往下执行的时候就发生了死锁。结果就是这些线程都陷入了无限的等待中。
多线程产生死锁的四个必要条件。只要破坏其中任意一个条件，就可以避免死锁，一种非常简单的避免死锁的方式就是：指定获取锁的顺序，并强制线程按照指定的顺序获取锁。因此，如果所有的线程都是以同样的顺序加锁和释放锁，就不会出现死锁了。

(1)互斥条件：一个资源每次只能被一个进程使用。
(2)保持和请求条件：一个进程因请求资源而阻塞时，对已获得资源保持不放。
(3)不可剥夺性：进程已获得资源，在未使用完成前，不能被剥夺。
(4)循环等待条件（闭环）：若干进程之间形成一种头尾相接的循环等待资源关系。

10.Lock

Lock主要方法

    lock()：获取锁，如果锁被暂用则一直等待
    unlock()：释放锁
    tryLock(): 注意返回类型是boolean，如果获取锁的时候锁被占用就返回false，否则返回true
    tryLock(long time, TimeUnit unit)：比起tryLock()就是给了一个时间期限，保证等待参数时间
    lockInterruptibly()：用该锁的获得方式，如果线程在获取锁的阶段进入了等待，那么可以中断此线程，先去做别的事

例子，注意ReentrantLock是Lock接口的实现。ReentrantLock默认的是非公平锁。

lock()：

    public class LockTest {
        private Lock lock = new ReentrantLock();
    
        //需要参与同步的方法
        private void method(Thread thread){
            lock.lock();
            try {
                System.out.println("线程名"+thread.getName() + "获得了锁");
            }catch(Exception e){
                e.printStackTrace();
            } finally {
                System.out.println("线程名"+thread.getName() + "释放了锁");
                lock.unlock();
            }
        }
    
        public static void main(String[] args) {
            LockTest lockTest = new LockTest();
    
            //线程1
            Thread t1 = new Thread(new Runnable() {
    
                @Override
                public void run() {
                    lockTest.method(Thread.currentThread());
                }
            }, "t1");
    
            Thread t2 = new Thread(new Runnable() {
    
                @Override
                public void run() {
                    lockTest.method(Thread.currentThread());
                }
            }, "t2");
    
            t1.start();
            t2.start();
        }
    }
    //执行情况：线程名t1获得了锁
    //         线程名t1释放了锁
    //         线程名t2获得了锁
    //         线程名t2释放了锁

tryLock()：
    
    public class LockTest {
        private Lock lock = new ReentrantLock();
    
        //需要参与同步的方法
        private void method(Thread thread){    
            if(lock.tryLock()){
                try {
                    System.out.println("线程名"+thread.getName() + "获得了锁");
                }catch(Exception e){
                    e.printStackTrace();
                } finally {
                    System.out.println("线程名"+thread.getName() + "释放了锁");
                    lock.unlock();
                }
            }else{
                System.out.println("我是"+Thread.currentThread().getName()+"有人占着锁，我就不要啦");
            }
        }
    
        public static void main(String[] args) {
            LockTest lockTest = new LockTest();
    
            //线程1
            Thread t1 = new Thread(new Runnable() {
    
                @Override
                public void run() {
                    lockTest.method(Thread.currentThread());
                }
            }, "t1");
    
            Thread t2 = new Thread(new Runnable() {
    
                @Override
                public void run() {
                    lockTest.method(Thread.currentThread());
                }
            }, "t2");
    
            t1.start();
            t2.start();
        }
    }
    //执行结果： 线程名t2获得了锁
    //         我是t1有人占着锁，我就不要啦
    //         线程名t2释放了锁

11.锁消除

锁消除就是把不必要的同步在编译阶段进行移除。
这里所说的锁消除并不一定指代是你写的代码的锁消除，通过指针逃逸分析（就是变量不会外泄），发现在这段代码并不存在线程安全问题，这个时候就会把这个同步锁消除。

12.线程锁，进程锁，分布式锁

线程锁：主要用来给方法、代码块加锁。当某个方法或者代码块使用锁时，那么在同一时刻至多仅有有一个线程在执行该段代码。当有多个线程访问同一对象的加锁方法/代码块时，同一时间只有一个线程在执行，其余线程必须要等待当前线程执行完之后才能执行该代码段。但是，其余线程是可以访问该对象中的非加锁代码块的。
进程锁：也是为了控制同一操作系统中多个进程访问一个共享资源，只是因为程序的独立性，各个进程是无法控制其他进程对资源的访问的，但是可以使用本地系统的信号量控制（操作系统基本知识）。
分布式锁：当多个进程不在同一个系统之中时，使用分布式锁控制多个进程对资源的访问。实现分布式锁必须要依靠第三方存储介质来存储锁的元数据等信息。比如分布式集群要操作某一行数据时，这个数据的流水号是唯一的，那么我们就把这个流水号作为一把锁的id，当某进程要操作该数据时，先去第三方存储介质中看该锁id是否存在，如果不存在，则将该锁id写入，然后执对该数据的操作；当其他进程要访问这个数据时，会先到第三方存储介质中查看有没有这个数据的锁id,有的话就认为这行数据目前已经有其他进程在使用了，就会不断地轮询第三方存储介质看其他进程是否释放掉该锁；当进程操作完该数据后，该进程就到第三方存储介质中把该锁id删除掉，这样其他轮询的进程就能得到对该锁的控制。

线程锁，进程锁，分布式锁的作用都是一样的，只是作用的范围大小不同。范围大小:分布式锁——大于——进程锁——大于——线程锁。能用线程锁，进程锁情况下使用分布式锁也是可以的，能用线程锁的情况下使用进程锁也是可以的。只是范围越大技术复杂度就越大。

搭建一个比如tomcat集群，集群内服务都是访问的同一台数据库，有多台服务器同时修改同一条数据库数据的操作，但是我们并没有在服务器中使用分布式锁？
按照上面对分布式锁的解释，两个不同系统上的JVM进程同时访问数据库的同一个资源，这个时候我们应该使用分布式锁进行控制。
这说的没有错，但是我们忘记了数据库的特性了。如果两台服务器仅仅是直接访问（通过url）并操作某台服务器硬盘中某个文件同一行数据，这个时候我们必须用分布式锁。
但是因为这两台服务器访问的数据是存储在数据库中的（数据库本身就是一个服务程序，多线程的接收外部系统发来的请求），两台服务器的请求通过网络IO发送到数据库服务器后，然后把请求交给数据库服务的进程处理，数据库服务器是多线程接收请求并处理的，这个时候关于某表某一行数据的多线程访问控制是由数据库服务进行控制的（就是数据库服务的代码中进行了线程上的加锁处理），这就是数据库服务器的行锁等特性，因为数据库那一端已经对外部多个系统的请求进行了一个锁操作，所以不需要我们在应用服务端进行分布式锁的开发。
那如果想同时更新数据库的多行数据，这个时候数据库的行锁就无法保证了。这个时候我们就要使用分布式锁，是的这个时候就可以使用，注意我用的是可以。为什么说可以呢？因为数据库本身就提供了这个机制，事务以及他的隔离级别。当然你也可以不用数据库提供的事务，用分布式锁。

分布式锁用于hbase存储系统：

实际开发场景中，我们会对hbase操作进行分布式锁，hbase作为一款优秀的非内存数据库，传统数据库一样提供了事务的概念，只是HBase的事务是行级事务，可以保证行级数据的原子性、一致性、隔离性以及持久性，即通常所说的ACID特性。
为了实现事务特性，HBase采用了各种并发控制策略，包括各种锁机制、MVCC机制等。因为hbase只支持行级事物，当业务需要并发操作两行甚至多行记录时，hbase本身就无法提供ACID的支持了。

13.Synchronized

(1)几种情况

    无Synchronized修饰普通方法：线程1和线程2是同时执行。
    Synchronized修饰普通方法：线程2需要等待线程1的method1执行完成才能开始执行method2方法。锁是当前实例对象。
    Synchronized修饰静态方法：对静态方法的同步本质上是对类的同步（静态方法本质上是属于类的方法，而不是对象上的方法），所以即使test和test2属于不同的对象，但是它们都属于SynchronizedTest类的实例，所以也只能顺序的执行method1和method2，不能并发执行。锁是当前类的class对象。
    Synchronized修饰代码块：虽然线程1和线程2都进入了对应的方法开始执行，但是线程2在进入同步块之前，需要等待线程1中同步块执行完成。锁是括号里面的对象。

(2)Synchronized的实现原理

Synchronized的语义底层是通过一个monitor的对象来完成，其实wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步的块或者方法中才能调用wait/notify等方法，否则会抛出java.lang.IllegalMonitorStateException的异常的原因。
synchronized是重量级锁，在JDK1.6中进行优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销。

同步代码块：
monitorenter指令插入到同步代码块的开始位置，monitorexit指令插入到同步代码块的结束位置，JVM需要保证每一个monitorenter都有一个monitorexit与之相对应。任何对象都有一个Monitor与之相关联，当且一个Monitor被持有之后，它将处于锁定状态。线程执行到monitorenter指令时，将会尝试获取对象所对应的Monitor所有权，即尝试获取对象的锁。

同步方法：
synchronized方法则会被翻译成普通的方法调用和返回指令如：invokevirtual、areturn指令，在VM字节码层面并没有任何特别的指令来实现被synchronized修饰的方法，而是在Class文件的方法表中将该方法的access_flags字段中的synchronized标志位置设置为1，表示该方法是同步方法，并使用调用该方法的对象或该方法所属的Class在JVM的内部对象表示Klass作为锁对象。
从反编译的结果来看，方法的同步并没有通过指令monitorenter和monitorexit来完成（理论上其实也可以通过这两条指令来实现），不过相对于普通方法，其常量池中多了ACC_SYNCHRONIZED标示符。
JVM就是根据该标示符来实现方法的同步的：当方法调用时，调用指令将会检查方法的ACC_SYNCHRONIZED访问标志是否被设置，如果设置了，执行线程将先获取monitor，获取成功之后才能执行方法体，方法执行完后再释放monitor。
在方法执行期间，其他任何线程都无法再获得同一个monitor对象。其实本质上没有区别，只是方法的同步是一种隐式的方式来实现，无需通过字节码来完成。

(3)monitorenter与monitorexit指令

monitorenter指令：
每个对象有一个监视器锁（monitor）。当monitor被占用时就会处于锁定状态，线程执行monitorenter指令时尝试获取monitor的所有权，过程如下：
如果monitor的进入数为0，则该线程进入monitor，然后将进入数设置为1，该线程即为monitor的所有者。如果线程已经占有该monitor，只是重新进入，则进入monitor的进入数加1。
如果其他线程已经占用了monitor，则该线程进入阻塞状态，直到monitor的进入数为0，再重新尝试获取monitor的所有权。

monitorexit指令：
执行monitorexit的线程必须是objectref所对应的monitor的所有者。
指令执行时，monitor的进入数减1，如果减1后进入数为0，那线程退出monitor，不再是这个monitor的所有者。其他被这个monitor阻塞的线程可以尝试去获取这个monitor的所有权。

14.volatile

volatile是轻量级的锁，它不会引起线程上下文的切换和调度。

(1)防止重排序，有序性

从一个最经典的例子来分析重排序问题。大家应该都很熟悉单例模式的实现，而在并发环境下的单例实现方式，我们通常可以采用双重检查加锁（DCL）的方式来实现。
现在我们分析一下为什么要在变量singleton之间加上volatile关键字。要理解这个问题，先要了解对象的构造过程，实例化一个对象其实可以分为三个步骤：分配内存空间。初始化对象。将内存空间的地址赋值给对应的引用。
但是由于操作系统可以对指令进行重排序，所以上面的过程也可能会变成如下过程：分配内存空间。将内存空间的地址赋值给对应的引用。初始化对象。如果是这个流程，多线程环境下就可能将一个未初始化的对象引用暴露出来，从而导致不可预料的结果。因此，为了防止这个过程的重排序，我们需要将变量设置为volatile类型的变量。

    public class Singleton {
        public static volatile Singleton singleton;

        /**
         * 构造函数私有，禁止外部实例化
         */
        private Singleton() {};

        public static Singleton getInstance() {
            if (singleton == null) {
                synchronized (singleton) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }
    }

(2)实现可见性

可见性问题主要指一个线程修改了共享变量值，而另一个线程却看不到。引起可见性问题的主要原因是每个线程拥有自己的一个高速缓存区——线程工作内存。volatile关键字能有效的解决这个问题。
一个线程修改数据，另一个线程打印，如果将a和b都改成volatile类型的变量再执行，则再也不会出现b=3;a=1的结果了。
可见性实现：线程本身并不直接与主内存进行数据的交互，而是通过线程的工作内存来完成相应的操作。这也是导致线程间数据不可见的本质原因。因此要实现volatile变量的可见性，直接从这方面入手即可。
对volatile变量的写操作与普通变量的主要区别有两点：修改volatile变量时会强制将修改后的值刷新的主内存中。修改volatile变量后会导致其他线程工作内存中对应的变量值失效。因此，再读取该变量值的时候就需要重新从读取主内存中的值。

(3)保证原子性

对volatile变量的单次读/写操作可以保证原子性的。但是不能保证i++这种操作的原子性，因为本质上i++是读、写两次操作。
因为long和double两种数据类型的操作可分为高32位和低32位两部分，因此普通的long或double类型读/写可能不是原子的。因此，鼓励大家将共享的long和double变量设置为volatile类型，这样能保证任何情况下对long和double的单次读/写操作都具有原子性。
由1000个线程执行i++操作，结果并非1000。volatile是无法保证i++是具有原子性的，我们可以通过AtomicInteger或者Synchronized来保证+1操作的原子性。

(4)内存屏障

为了实现volatile可见性和happen-before的语义。JVM底层是通过一个叫做“内存屏障”的东西来完成。内存屏障，也叫做内存栅栏，是一组处理器指令，用于实现对内存操作的顺序限制。
下面是完成上述规则所要求的内存屏障：

    LoadLoad 屏障
    执行顺序：Load1—>Loadload—>Load2
    确保Load2及后续Load指令加载数据之前能访问到Load1加载的数据。
    
    StoreStore 屏障
    执行顺序：Store1—>StoreStore—>Store2
    确保Store2以及后续Store指令执行前，Store1操作的数据对其它处理器可见。
    
    LoadStore 屏障
    执行顺序：Load1—>LoadStore—>Store2
    确保Store2和后续Store指令执行前，可以访问到Load1加载的数据。
    
    StoreLoad 屏障
    执行顺序: Store1—> StoreLoad—>Load2
    确保Load2和后续的Load指令读取之前，Store1的数据对其他处理器是可见的。

15.可中断锁

顾名思义，就是可以相应中断的锁。synchronized就不是可中断锁，Lock是可中断锁。

16.ReentrantLock

ReentrantLock，可重入锁，是一种递归无阻塞的同步机制。它可以等同于synchronized的使用，但是ReentrantLock提供了比synchronized更强大、灵活的锁机制，可以减少死锁发生的概率。
ReentrantLock是可重入锁，可重入锁就是当前持有该锁的线程能够多次获取该锁，无需等待。可重入锁是如何实现的呢？这要从ReentrantLock的一个内部类Sync的父类说起，Sync的父类是AQS。
ReentrantLock的架构主要包括一个Sync的内部抽象类以及Sync抽象类的两个实现类。
另外、Sync的两个实现类分别是NonfairSync和FairSync，一个是用于实现公平锁，一个是用于实现非公平锁。那么Sync为什么要被设计成内部类呢？Sync被设计成为安全的外部不可访问的内部类，使得ReentrantLock中所有涉及对AQS的访问都要经过Sync，其实，Sync被设计成为内部类主要是为了安全性考虑，这也是作者在AQS的comments上强调的一点。

17.Condition

Condition和Lock一起使用以实现等待/通知模式，通过await()和signal()来阻塞和唤醒线程。
Condition是一种广义上的条件队列。它为线程提供了一种更为灵活的等待/通知模式，线程在调用await方法后执行挂起操作，直到线程等待的某个条件为真时才会被唤醒。
Condition必须要配合Lock一起使用，因为对共享状态变量的访问发生在多线程环境下。一个Condition的实例必须与一个Lock绑定，因此Condition一般都是作为Lock的内部实现。

18.ReentrantReadWriteLock

读写锁维护着一对锁，一个读锁和一个写锁。通过分离读锁和写锁，使得并发性比一般的排他锁有了较大的提升：
在同一时间，可以允许多个读线程同时访问。但是在写线程访问时，所有读线程和写线程都会被阻塞。
ReentrantReadWriteLock实现ReadWriteLock接口，是可重入的读写锁实现类。
在同步状态上，为了表示两把锁，将一个32位整型分为高16位和低16位，分别表示读和写的状态。

读写锁的主要特性：

    公平性：支持公平性和非公平性。
    重入性：支持重入。读写锁最多支持65535个递归写入锁和65535个递归读取锁。
    锁降级：遵循获取写锁，再获取读锁，最后释放写锁的次序，如此写锁能够降级成为读锁。

19.synchronized与Lock的区别

    类别	synchronized	            Lock
    存在层次	Java的关键字，在jvm层面上	            是一个类
    锁的释放	1、以获取锁的线程执行完同步代码，释放锁 2、线程执行发生异常，jvm会让线程释放锁	            在finally中必须释放锁，不然容易造成线程死锁
    锁的获取	假设A线程获得锁，B线程等待。如果A线程阻塞，B线程会一直等待	            分情况而定，Lock有多个锁获取的方式，具体下面会说道，大致就是可以尝试获得锁，线程可以不用一直等待
    锁状态	无法判断	            可以判断
    锁类型	可重入 不可中断 非公平	            可重入 可判断 可公平（两者皆可）
    性能	少量同步	大量同步

Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
Lock可以提高多个线程进行读操作的效率。

更深的：
与synchronized相比，ReentrantLock提供了更多，更加全面的功能，具备更强的扩展性。例如：时间锁等候，可中断锁等候，锁投票。
ReentrantLock还提供了条件Condition，对线程的等待、唤醒操作更加详细和灵活，所以在多个条件变量和高度竞争锁的地方，ReentrantLock更加适合。
ReentrantLock提供了可轮询的锁请求。它会尝试着去获取锁，如果成功则继续，否则可以等到下次运行时处理，而synchronized则一旦进入锁请求要么成功要么阻塞，所以相比synchronized而言，ReentrantLock会不容易产生死锁些。
ReentrantLock支持更加灵活的同步代码块，但是使用synchronized时，只能在同一个synchronized块结构中获取和释放。注意，ReentrantLock的锁释放一定要在finally中处理，否则可能会产生严重的后果。
ReentrantLock支持中断处理，且性能较synchronized会好些。

20.Java中线程同步的方式

    sychronized同步方法或代码块
    volatile
    Lock
    ThreadLocal
    阻塞队列（LinkedBlockingQueue）
    使用原子变量（java.util.concurrent.atomic）
    变量的不可变性

# JUC

1.JUC

J.U.C即java.util.concurrent包，为我们提供了很多高性能的并发类，可以说是java并发的核心。
Concurrent包下所有类底层都是依靠CAS操作来实现，而sun.misc.Unsafe为我们提供了一系列的CAS操作。
AQS框架是J.U.C中实现锁及同步机制的基础，其底层是通过调用LockSupport.unpark()和LockSupport.park()实现线程的阻塞和唤醒。
J.U.C的整个框架分为5个部分：tools、locks、collections、executor和atomic。

(1)Atomic

该包下主要是一些原子变量类，仅依赖于Unsafe，并且被其他模块所依赖。

(2)Locks

该包下主要是关于锁及其相关类，仅依赖于Unsafe或内部依赖，并且被其他高级模块所依赖。由于LockSupport类底层逻辑简单且仅依赖Unsafe，同时为其他高级模块所依赖，所以需要先了解LockSupport类的运行原理，然后重点研究AbstractQueuedSynchronizer框架，理解独占锁和共享锁的实现原理，并清楚Condition如何与AbstractQueuedSynchronizer进行协作，最后很容易就能理解ReentrantLock是如何实现的。

(3)Collections

该包会依赖Unsafe和前两个基础模块，并且模块内部各个容器间相互较为独立，所以没有固定的学习顺序，理解编程中常用的集合类原理即可：
ConcurrentHashMap、CopyOnWriteArrayList、CopyOnWriteArraySet、ArrayBlockingQueue、LinkedBlockingQueue（阻塞队列在线程池中有使用，所以理解常用阻塞队列的特性很重要）。

(4)Executor

这一部分的核心是线程池的运行原理，也是实际应用中较多的部分，会依赖于前几个模块。首先了解Callable、Future、RunnableFuture三个接口间的关系以及FutureTask的实现原理，然后研究如何创建ThreadPoolExecutor，如何运行一个任务，如何管理自身的线程，同时了解RejectedExecutionHandler的四种实现差异，最后，在实际应用中学习如何通过调整ThreadPoolExecutor的参数来优化线程池。

(5)Tools

这一部分是以前面几个模块为基础的高级特性模块，实际应用的场景相对较少，主要应用在多线程间相互依赖执行结果场景，没有具体的学习顺序。
最好CountDownLatch、CyclicBarrier、Semaphore、Exchanger、Executors都了解下，对后面学习Guava的框架有帮助。

2.AQS抽象队列同步器

(1)AQS框架

AQS（AbstractQueuedSynchronizer，抽象队列同步器）是JDK1.5提供的一个基于FIFO等待队列实现的一个用于实现同步器的基础框架。
这个基础框架的重要性可以这么说，JUC包里面几乎所有的有关锁、多线程并发以及线程同步器等重要组件的实现都是基于AQS这个框架。AQS的主要使用方式是继承，子类通过继承同步器，并实现它的抽象方法来管理同步状态。
AQS的核心思想是基于volatile int state这样的一个属性同时配合Unsafe工具对其原子性的操作来实现对当前锁的状态进行修改。当state的值为0的时候，标识该Lock不被任何线程所占有。当 state>0时，表示已经获取了锁。
AQS维护了一个volatile int state域和一个FIFO线程等待队列（利用双向链表实现，多线程争用资源被阻塞时会进入此队列）。如果当前线程获取同步状态失败（锁）时，AQS则会将当前线程以及等待状态等信息构造成一个节点（Node）并将其加入同步队列，同时会阻塞当前线程。当同步状态释放时，则会把节点中的线程唤醒，使其再次尝试获取同步状态。
AQS的父类AOS(AbstractOwnableSynchronizer)主要提供一个exclusiveOwnerThread属性，用于关联当前持有该锁的线程。

主要的域如下：

    private transient volatile Node head; //同步队列的head节点
    private transient volatile Node tail; //同步队列的tail节点
    private volatile int state; //同步状态

AQS提供的可以修改同步状态的3个方法：

    protected final int getState();　　//获取同步状态
    protected final void setState(int newState);　　//设置同步状态
    protected final boolean compareAndSetState(int expect, int update);　　//CAS设置同步状态

这三种叫做均是原子操作，其中compareAndSetState的实现依赖于Unsafe的compareAndSwapInt()方法。代码实现如下：

    private volatile int state;
    protected final int getState() {
        return state;
    }
    protected final void setState(int newState) {
        state = newState;
    }
    protected final boolean compareAndSetState(int expect, int update) {
        // See below for intrinsics setup to support this
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }

(2)自定义资源共享方式

AQS定义两种资源共享方式：Exclusive（独占，只有一个线程能执行，如ReentrantLock）和Share（共享，多个线程可同时执行，如Semaphore/CountDownLatch(CountDownLatch是并发的)）。
不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。自定义同步器实现时主要实现以下几种方法：

    isHeldExclusively()：该线程是否正在独占资源。只有用到condition才需要去实现它。
    tryAcquire(int)：独占方式。尝试获取资源，成功则返回true，失败则返回false。
    tryRelease(int)：独占方式。尝试释放资源，成功则返回true，失败则返回false。
    tryAcquireShared(int)：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
    tryReleaseShared(int)：共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。

以ReentrantLock为例，state初始化为0，表示未锁定状态。A线程lock()时，会调用tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这就是可重入的概念。但要注意，获取多少次就要释放多么次，这样才能保证state是能回到零态的。
再以CountDownLatch以例，任务分为N个子线程去执行，state也初始化为N（注意N要与线程个数一致）。这N个子线程是并行执行的，每个子线程执行完后countDown()一次，state会CAS减1。等到所有子线程都执行完后(即state=0)，会unpark()主调用线程，然后主调用线程就会从await()函数返回，继续后余动作。
一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现tryAcquire-tryRelease、tryAcquireShared-tryReleaseShared中的一种即可。但AQS也支持自定义同步器同时实现独占和共享两种方式，如ReentrantReadWriteLock。

3.CAS是一种什么样的同步机制？多线程下为什么不使用int而使用AtomicInteger？

Compare And Swap，比较交换。可以看到synchronized可以保证代码块原子性，很多时候会引起性能问题，volatile也是个不错的选择，但是volatile不能保证原子性，只能在某些场合下使用。所以可以通过CAS来进行同步，保证原子性。
我们在读Concurrent包下的类的源码时，发现无论是ReentrantLock内部的AQS，还是各种Atomic开头的原子类，内部都应用到了CAS。
在CAS中有三个参数：内存值V、旧的预期值A、要更新的值B ，当且仅当内存值V的值等于旧的预期值A时，才会将内存值V的值修改为B，否则什么都不干。 其伪代码如下：

    if (this.value == A) {
        this.value = B
        return true;
    } else {
        return false;
    }

CAS可以保证一次的读-改-写操作是原子操作。

在多线程环境下，int类型的自增操作不是原子的，线程不安全，可以使用AtomicInteger代替。

    // AtomicInteger.java
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
    static {
        try {
            valueOffset = unsafe.objectFieldOffset(AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex){ 
            throw new Error(ex); 
        }
    }
    private volatile int value;


Unsafe是CAS的核心类，Java无法直接访问底层操作系统，而是通过本地native方法来访问。
不过尽管如此，JVM还是开了一个后门：Unsafe，它提供了硬件级别的原子操作。
valueOffset为变量值在内存中的偏移地址，Unsafe就是通过偏移地址来得到数据的原值的。
value当前值，使用volatile修饰，保证多线程环境下看见的是同一个。

    // AtomicInteger.java
    public final int addAndGet(int delta) {
        return unsafe.getAndAddInt(this, valueOffset, delta) + delta;
    }
    
    // Unsafe.java
    // compareAndSwapInt（var1, var2, var5, var5 + var4）其实换成 compareAndSwapInt（obj, offset, expect, update）比较清楚，意思就是如果obj内的value和expect相等，就证明没有其他线程改变过这个变量，那么就更新它为update，如果这一步的CAS没有成功，那就采用自旋的方式继续进行CAS操作，取出乍一看这也是两个步骤了啊，其实在JNI里是借助于一个CPU指令完成的。所以还是原子操作。
    public final int getAndAddInt(Object var1, long var2, int var4) {
        int var5;
        do {
            var5 = this.getIntVolatile(var1, var2);
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
        return var5;
    }
    // 该方法为本地方法，有四个参数，分别代表：对象、对象的地址、预期值、修改值
    public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);

4.CountDownLatch、CyclicBarrier、Semaphore

CyclicBarrier它允许一组线程互相等待，直到到达某个公共屏障点 (Common Barrier Point)。在涉及一组固定大小的线程的程序中，这些线程必须不时地互相等待，此时CyclicBarrier很有用。因为该Barrier在释放等待线程后可以重用，所以称它为循环(Cyclic)的屏障 (Barrier)。
每个线程调用#await()方法，告诉CyclicBarrier我已经到达了屏障，然后当前线程被阻塞。当所有线程都到达了屏障，结束阻塞，所有线程可继续执行后续逻辑。

CountDownLatch能够使一个线程在等待另外一些线程完成各自工作之后，再继续执行。使用一个计数器进行实现。计数器初始值为线程的数量。当每一个线程完成自己任务后，计数器的值就会减一。当计数器的值为0时，表示所有的线程都已经完成了任务，然后在CountDownLatch上等待的线程就可以恢复执行任务。

Semaphore是一个控制访问多个共享资源的计数器，和CountDownLatch一样，其本质上是一个“共享锁”。一个计数信号量。从概念上讲，信号量维护了一个许可集。如有必要，在许可可用前会阻塞每一个acquire，然后再获取该许可。每个release添加一个许可，从而可能释放一个正在阻塞的获取者。

两者区别：
CountDownLatch的作用是允许1或N个线程等待其他线程完成执行；而CyclicBarrier则是允许N个线程相互等待。
CountDownLatch的计数器无法被重置；CyclicBarrier的计数器可以被重置后使用，因此它被称为是循环的barrier 。
