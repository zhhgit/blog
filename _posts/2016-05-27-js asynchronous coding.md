---
layout: post
title: "JS异步编程"
description: JS异步编程
modified: 2016-05-27
category: JavaScript
tags: [JavaScript]
featured: true
---

JavaScript所谓单线程，是指负责解释并执行JS代码的线程只有一个。我们不妨叫它主线程。其实还有其他很多线程的，比如进行ajax请求的线程、监控用户事件的线程、定时器线程、读写文件的线程(例如在NodeJS中)等等。
回调依次排队执行，如果有任务阻塞，还是会无法执行异步的其他任务。所谓同步：等待f1的回调执行后再执行f2。所谓异步：依次执行f1,f2，再执行f1回调。

参考：

1.[Javascript异步编程的4种方法](http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)

2.[Javascript 异步编程的4种方法](http://kb.cnblogs.com/page/167474/)

3.[谈谈JavaScript的异步实现](http://blog.csdn.net/a1003671336/article/details/17631131)

4.[JavaScript既然是单线程的，那么异步要怎么理解？](https://segmentfault.com/q/1010000004266993?_ea=549894)
