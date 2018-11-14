---
layout: post
title: "Java并发编程小结"
description: Java并发编程小结
modified: 2018-11-13
category: Java
tags: [Java]
---

读书小结。

# 一、线程

1.语法1：

	public class NewThread extends Thread {
	    @Override
	    public void run(){
	    
	    }
	}

	Thread thread1 = new NewThread();
	thread1.start();

2.语法2：

    public class NewRunnable implements Runnable{
	    @Override
	    public void run(){
	    
	    }
    }

    Thread thread1 = new Thread(new NewRunable());
    thread1.start();

# 二、线程中断

1.当线程被阻塞时(调用了sleep()或wait())无法检测线程状态，此时如果调用interrupt()方法，会产生InterruptedException。常用结构：

    public void run(){
        try{
            if (!Thread.currentThread().isInterrupted() && otherCondition()){
                doSomething();
            }
        }
        catch(InterruptedException e){
        
        }
        finally{
        
        }
    }
    
2.中断不等于线程终止，中断一个线程会引起它的注意，线程可以决定如何响应中断，普遍的情况线程会将中断作为一个终止的请求。

# 三、线程状态

1.New,Runnable,Blocked（请求内部锁）,Waiting（等待一个条件通知）,Timed Waiting（等待超时或者一个条件通知）,Terminated。

2.join()：当调用某个线程的join()方法时，这个方法会挂起调用线程，直到被调用线程结束执行，调用线程才会继续执行。让父线程等待子线程结束之后才能继续运行。

3.Thread.State getState()方法，获得线程当前状态。

# 四、线程属性

1.线程优先级，setPriority()方法

2.守护线程为其他线程服务，只剩下守护线程时虚拟机退出。

    t.setDaemon(true)

3.可以为线程设置未捕获异常处理器，所谓未捕获是指没有通过try...catch...的语法显式捕获，因为这些异常是未检查异常(unchecked exception)，例如数组越界，空指针等，本可以避免不需要try...catch...这样的语法。

    t.setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler handler)
 
# 五、可重入锁ReentrantLock

1.语法：

	Lock mylock = new ReentrantLock();
	mylock.lock();
	try {
	    doSomething();
	}
	finally {
	    mylock.unlock();
	}

2.可重入意味着同一线程可以重复获得已经持有的锁，锁保持一个持有计数来追踪对lock()方法的嵌套调用。

3.new ReentrantLock(true)将构造一个持有公平策略的锁，偏爱等待时间最长的线程。

# 六、条件对象Condition

1.一个锁对象可以有一个或多个相关的锁对象。

2.考虑要转账金额低于余额的场景，一直循环等待，希望有其他线程转入金额使得while循环等待结束。但是其他线程因为无法获得锁，而不可能进入临界区。

3.语法：
	
	Lock mylock = new ReentrantLock();
	Condition sufficientFunds = mylock.newCondition();
	
	mylock.lock();
	try {
	    while(accounts[from] < amount){
	        sufficientFunds.await();
	    }
	    finishTransfer();
	    sufficientFunds.signalAll();
	}
	finally {
	    mylock.unlock();
	}

4.调用await()方法后，线程进入该条件对象的等待集，当锁可用时不能马上解除阻塞，直达另一线程调用同一条件对象的signalAll()方法。

# 七、synchronized关键字

1.语法

	public synchronized void method(){
	    doSomething();
	}

2.从Java 1.0开始每个对象都有一个内部锁，用synchronized关键字修饰的方法，对象的锁将保护整个方法，等效于ReentrantLock。内部锁只有一个条件对象，Object对象的final方法wait(),notify(),notifyAll()方法等价于Condition对象的await(),signal(),signalAll()。

# 八、同步阻塞(synchronized block)

1.语法：

	synchronized (obj){
	    doSomething();
	}

2.同步阻塞是脆弱不推荐使用的，因为其依赖于一个事实，锁定对象的所有可修改方法都是使用内部锁，是原子操作。

3.同步格言：如果向一个变量写入值，而这个变量接下来可能被另一个线程读取，或者从一个变量读取值，而这个值可能是之前由另一个线程写入的，此时必须使用同步。

# 九、volatile关键字

1.volatile关键字为实例域的同步访问提供了一种免锁机制。声明为volatile，则编译器和虚拟机就知道该域可能被多个线程并发更新。

# 十、死锁

1.所有线程都不满足执行条件，处在Waiting等待执行的状态。

# 十一、线程局部变量

1.ThreadLocal为每个线程提供各自的实例，有get(),set(),remove()方法，语法：

    public static final ThreadLocal<SimpleDateFormat> dateFormat = new ThreadLocal<SimpleDateFormat>(){
        protected SimpleDateFormat initalValue(){
            return new SimpleDateFormat("yyyy-MM--dd");
        }
    }
    String dateStamp = dateFormat.get().format(new Date());
    
# 十二、锁测试与超时

1.tryLock()方法尝试获得一个锁，如果成功就返回true，否则立即返回false，线程立即离开去做其他事情。

    if(mylock.tryLock()){
        try {
            doSomething();
        }
        finally{
            mylock.unlock();
        }
    }
    else{
        doSomethingElse();
    }

2.boolean tryLock(100, TimeUnit.MILLISECONDS)，尝试获得锁，阻塞时间不会超过给定值，成功就返回true。
void lock()，获得锁，如果一个线程在等待获得锁时被中断，在获得锁之前一直阻塞。如果死锁，lock()方法就无法终止。
void lockInterruptibly()，获得锁，但是给定时间无限长，可能一直阻塞，如果线程被中断会抛出InterruptedException。

# 十三、读写锁

1.ReentrantReadWriteLock读写锁。多个读操作共用读锁，但是排斥写操作。写锁排斥其他读、写操作。语法：

    private int age;
    private ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
    private Lock readLock = rwl.readlock();
    private Lock writeLock = rwl.writelock();
    
    public int getAge(){
        readLock.lock();
        try{
        }
        finally{
            readLock.unlock();
        }
        return age;
    }
    
    public void setAge(age){
        writeLock.lock();
        try{
            this.age = age
        }
        finally{
            writeLock.unlock();
        }
    }
    
# 十四、stop()与suspend()的弃用

1.stop方法会导致不一致，考虑转账的场景已经从一个账户转出，但是未对另一个账户转入。

2.suspend方法容易导致死锁，挂起的线程持有锁但是等待被恢复，而将其挂起的线程等待获得锁。

# 十五、阻塞队列

1.队列操作可以分为三类：

(1).add(),remove(),element()，抛异常。
(2).offer(),pull().peek()，返回true/false或者null。
(3).put(),take()，阻塞线程。

2.当生产者线程比消费者线程快时，队列满了后再put，生产者阻塞。当生产者线程比消费者线程慢时，队列空了后再take，消费者阻塞。

    BlockingQueue<File> queue = new ArrayBlockingQueue<File>(100);
    
3.实现类包括：ArrayBlockingQueue,LinkedBlockingQueue,LinkedBlockingDeque,PriorityBlockingQueue,DelayQueue,BlockingQueue,BlockingDeque

# N、参考

1.Java核心技术卷1，十四章-多线程。