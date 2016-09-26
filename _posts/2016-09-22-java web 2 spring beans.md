---
layout: post
title: "Java Web(2):使用Spring beans"
description: Java Web(2):使用Spring beans
modified: 2016-09-22
category: Java
tags: [Java]
---

建立Dynamic web project，在WebContent/WEB-INF/lib目录下放入一系列jar包。

在Java Resources/src目录下新建bean.xml如下。获取类并实例化，通过该配置文件配置实例的相关属性。

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns="http://www.springframework.org/schema/beans"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans
	    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
	    <bean id="person" class="com.beans.Person">
	        <property name="name" value="ZhangHao"/>
	        <property name="age" value="26"/>
	    </bean>
	    <bean id="animal" class="com.beans.Animal">
	        <property name="kind" value="Cat"/>
	        <property name="sound" value="miaomiao"/>
	    </bean>
	</beans>

在src/com.beans包下，有两个简单bean，分别为:

Animal.java:

	package com.beans;

	public class Animal {
	    
	    private String kind;
	    private String sound;
	    
	    public String getKind() {
	        return kind;
	    }
	    public void setKind(String kind) {
	        this.kind = kind;
	    }
	    public String getSound() {
	        return sound;
	    }
	    public void setSound(String sound) {
	        this.sound = sound;
	    }
	    public void info(){
	        System.out.println("kind:"+getKind()+" sound:"+getSound());
	    }

	}

Person.java

	package com.beans;

	public class Person {
	    
	    private String name;
	    private int age;
	    
	    public String getName() {
	        return name;
	    }
	    public void setName(String name) {
	        this.name = name;
	    }
	    public int getAge() {
	        return age;
	    }
	    public void setAge(int age) {
	        this.age = age;
	    }
	    public void info(){
	        System.out.println("name:"+getName()+" age:"+getAge());
	    }
	}

在src/com.test包下为主函数，通过读取配置文件将对象实例化并调用其方法。

	package com.test;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;

	import com.beans.Animal;
	import com.beans.Person;

	public class Test {
	    public static void main(String[] args){
	        ApplicationContext ctx = new ClassPathXmlApplicationContext("bean.xml");
	        Person p = ctx.getBean("person",Person.class);
	        p.info();
	        Animal a = ctx.getBean("animal",Animal.class);
	        a.info();
	        ((ClassPathXmlApplicationContext) ctx).close();
	    }
	}

控制台会输出：

name:ZhangHao age:26
kind:Cat sound:miaomiao

参见[demo1](https://github.com/zhhgit/Java_web_demos/tree/master/demo1-spring%20beans)
