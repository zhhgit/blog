---
layout: post
title: "Java并发编程小结"
description: Java并发编程小结
modified: 2018-11-13
category: Java
tags: [Java]
---

# 一.可重入锁ReentrantLock

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

# 二.条件对象Condition

1.一个锁对象可以有一个或多个相关的锁对象。

2.考虑要转账金额低于余额的场景，一致循环等待，希望有其他线程转入金额使得while循环等待结束。但是其他线程因为无法获得锁，而不可能进入临界区。

3.语法
	
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

# 三、synchronized关键字

1.语法

	public synchronized void method(){
	    doSomething();
	}

2.从Java 1.0开始每个对象都有一个内部锁，用synchronized关键字修饰的方法，对象的锁将保护整个方法，等效于ReentrantLock。内部锁只有一个条件对象，Object对象的final方法wait(),notify(),notifyAll()方法等价于Condition对象的await(),signal(),signalAll()。

# N、参考

1.Java核心技术卷1，十四章-多线程。