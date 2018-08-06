---
layout: post
title: "Java定时任务"
description: Java定时任务
modified: 2018-08-06
category: Java
tags: [Java]
---

# 一、配置

在SpringMVC配置文件中，设置定时任务是注解驱动的。

	<!-- 设置定时任务 -->
	<task:annotation-driven/>

# 二、定时任务类

定时任务类通过@Component注解来实例化，通过@Scheduled注解和cron表达式来定义定时任务何时执行。

	package zhanghao90.horizon.task;

	import org.springframework.scheduling.annotation.Scheduled;
	import org.springframework.stereotype.Component;
	import org.apache.http.client.utils.DateUtils;
	import java.util.Date;

	@Component
	public class TimerTask {

	    /**
	     * 每分钟执行一次
	     */
	    @Scheduled(cron = "0 * * * * ?")
	    public void doTask(){
	        System.out.println(DateUtils.formatDate(new Date(), "[hh:mm:ss,SSS]：") + "定时任务执行");
	    }

	}

# 三、参考

1.[Spring Task定时任务的配置和使用详解](https://www.jb51.net/article/110541.htm)

2.[Spring框架实现定时任务调度](https://blog.csdn.net/qq_35733535/article/details/79131561)
