---
layout: post
title: "Redis安装与使用"
description: Redis安装与使用
modified: 2016-11-01
category: Java
tags: [Java]
---

# 一、安装并使用

下载并安装Redis-x64-3.2.100.msi。安装好后进入C:\Program Files\Redis目录，启动Redis

	redis-server.exe redis.windows.conf

另开一个命令窗口，进行简单数据存取

	//存
	set age 26
	//读
	get age

# 二、使用Jedis

	//TODO

# 三、参考

1.[Redis教程](http://www.runoob.com/redis/redis-tutorial.html)

2.[Redis命令参考](http://redisdoc.com/)

3.[Redis入门很简单](http://hello-nick-xu.iteye.com/category/314998)

4.[Jedis API](http://tool.oschina.net/uploads/apidocs/)

5.[Windows下Redis的安装使用](https://my.oschina.net/lujianing/blog/204103)

6.[使用Spring+Jedis集成Redis](https://my.oschina.net/u/866380/blog/521658)

7.[什么是redis，redis能做什么，redis应用场景](http://www.daixiaorui.com/read/188.html)

8.[Redis是什么？](https://yq.aliyun.com/articles/45777)

9.[jedis](https://github.com/xetorthio/jedis)

