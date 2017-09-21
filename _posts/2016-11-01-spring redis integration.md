---
layout: post
title: "Spring Redis整合"
description: Spring Redis整合
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

src中包括三个例子。

src/org/simple包中，是最简单的使用Redis的例子，连接Redis并存取一个String数据。Run as Java Application:

	package org.simple;
	import redis.clients.jedis.*;

	public class RedisTest {
		public static void main(String[] args) {
			//连接 Redis
			Jedis jedis = new Jedis("localhost");
		    System.out.println("连接Redis成功");
		    //存储字符串
		    jedis.set("age", "26");
		    //获取存储的字符串
		    System.out.println("存储的字符串为"+ jedis.get("age"));
		    jedis.close();
		}
	}

src/org/test包中是对Key,String,Hash,List,Set等各种类型数据的操作实例。Run as JUnit Test:

	package org.test;
	import org.junit.Before;
	import org.junit.Test;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisPool;
	import redis.clients.jedis.JedisPoolConfig;

	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.List;
	import java.util.Map;

	public class RedisTest {

	        JedisPool pool;
	        Jedis jedis;
	        @Before
	        public void setUp() {
	            pool = new JedisPool(new JedisPoolConfig(), "localhost");
	            jedis = pool.getResource();
	        }
	        
	        
	        //Key
	        @Test
	        public void test() throws InterruptedException {
	        	System.out.println("---------------------开始测试Key---------------------");
	            System.out.println(jedis.keys("*")); //返回当前库中所有的key
	            System.out.println(jedis.exists("alpha"));//检查key是否存在
	        }
	        
	        //String
	        @Test
	        public void testBasicString(){
	            System.out.println("---------------------开始测试String---------------------");
	            //增、查
	            jedis.set("name","Zhang");
	            System.out.println(jedis.get("name"));

	            //改
	            jedis.append("name","Hao");
	            System.out.println(jedis.get("name"));
	            jedis.set("name","HaoZhang");
	            System.out.println(jedis.get("name"));

	            //删
	            jedis.del("name");
	            System.out.println(jedis.get("name"));
	        }

	        //Hash
	        @Test
	        public void testMap(){
	        	System.out.println("---------------------开始测试Hash---------------------");
	            Map<String,String> user=new HashMap<String,String>();
	            user.put("name","ZhangHao");
	            user.put("city","Shanghai");
	            jedis.hmset("user",user);
	            List<String> rsmap = jedis.hmget("user", "name", "city");
	            System.out.println(rsmap);
	            
	            System.out.println(jedis.hlen("user")); //返回key为user的键中存放的值的个数
	            System.out.println(jedis.exists("user"));//是否存在key为user的记录
	            System.out.println(jedis.hkeys("user"));//返回map对象中的所有key
	            System.out.println(jedis.hvals("user"));//返回map对象中的所有value

	            Iterator<String> iter=jedis.hkeys("user").iterator();
	            while (iter.hasNext()){
	                String key = iter.next();
	                System.out.println(key+":"+jedis.hmget("user",key));
	            }
	            jedis.del("user","name","city");
	        }

	        //List
	        @Test
	        public void testList(){
	        	System.out.println("---------------------开始测试List---------------------");
	            //增
	            jedis.lpush("sampleList","spring");
	            jedis.lpush("sampleList","struts");
	            jedis.lpush("sampleList","hibernate");
	            //查
	            System.out.println(jedis.lrange("sampleList",0,-1));
	            //删
	            jedis.del("sampleList");
	        }

	        //Set
	        @Test
	        public void testSet(){
	        	System.out.println("---------------------开始测试Set---------------------");
	            //增
	            jedis.sadd("alpha","AAA");
	            jedis.sadd("alpha","BBB");
	            jedis.sadd("alpha","CCC");
	            jedis.sadd("alpha","DDD");
	            jedis.srem("alpha","DDD");//删除一个元素
	            System.out.println(jedis.smembers("alpha"));//获取所有的元素
	            System.out.println(jedis.sismember("alpha", "AAA"));//判断是否是集合的元素
	            System.out.println(jedis.srandmember("alpha"));//随机返回一个元素
	            System.out.println(jedis.scard("alpha"));//返回集合的元素个数
	            jedis.del("alpha");
	        }
	}

第三个例子是对Spring、SpringMVC与Redis的整合。配置文件web.xml、mvc-context.xml与demo7基本一致。root-context.xml删除了数据库的配置，加入了对Redis的配置：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
					http://www.springframework.org/schema/context
					 http://www.springframework.org/schema/context/spring-context-3.2.xsd
					http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">
		<!-- Root Context: defines shared resources visible to all other web components -->
		
		<!-- 加载Redis配置 -->
	    <context:property-placeholder ignore-unresolvable="true" location="classpath:redis.properties" />

	    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
	        <property name="maxIdle" value="300"/> <!--最大能够保持idel状态的对象数-->
	        <property name="maxTotal" value="60000"/><!--最大分配的对象数-->
	        <property name="testOnBorrow" value="true"/><!--当调用borrow Oject方法时，是否进行有效性检查-->
	    </bean>

	    <bean id="jedisPool" class="redis.clients.jedis.JedisPool">
	        <constructor-arg index="0" ref="jedisPoolConfig"/>
	        <constructor-arg index="1" value="${redis.host}"/>
	        <constructor-arg index="2" value="${redis.port}" type="int"/>
	        <constructor-arg index="3" value="${redis.timeout}" type="int"/>
	    </bean>
	</beans>

其中的redis.properties文件是Redis配置文件：

	redis.host = localhost
	redis.port = 6379
	redis.timeout=30000

src/org/controller包中HomeController拦截index.html并返回index.jsp页面。RedisController拦截JSP页面发出的AJAX请求（stringTest.do），将JSP页面发过来的JSON数据分别存入Redis，再从Redis中读取数据并alert出来。RedisController.java为：

	package org.controller;

	import java.util.HashMap;
	import java.util.Map;
	import org.entity.Person;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestBody;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestMethod;
	import org.springframework.web.bind.annotation.ResponseBody;
	import com.google.gson.Gson;
	import redis.clients.jedis.*;


	@Controller
	public class RedisController {
		
		@Autowired
		private JedisPool jedisPool;
		
		@ResponseBody
		@RequestMapping(value = "/stringTest", method = { RequestMethod.POST }, produces = "application/json; charset=UTF-8")
		public String stringTest(@RequestBody String params){
			Gson gson = new Gson();
			Person personParams = gson.fromJson(params, Person.class);
			System.out.println("获取请求JSON成功");
			Jedis jedis = jedisPool.getResource();
			jedis.set("name", personParams.getName());
			jedis.set("place", personParams.getPlace());
			System.out.println("Redis存数据成功");
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("name", jedis.get("name"));
			responseMap.put("place", jedis.get("place"));
			System.out.println("Redis读数据成功");
			return gson.toJson(responseMap);
		}
	}

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

# 四、Github Demo

1.详见[Spring-redis](https://github.com/zhhgit/spring_redis)

