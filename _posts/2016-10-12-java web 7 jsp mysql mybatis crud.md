---
layout: post
title: "Java Web(7):Spring&MyBatis操作MySQL"
description: Java Web(7):Spring&MyBatis操作MySQL
modified: 2016-10-12
category: Java
tags: [Java]
---

# 一、org.dao包

与上一个demo的差别在于，使用了MyBatis后不需要自己写DAO实现类，只要写DAO接口类StudentDao.java:

	package org.dao;

	import org.springframework.stereotype.Repository;
	import java.util.List;
	import org.entity.*;

	@Repository
	public interface StudentDao {

	    //查询所有记录
		public List<Student> queryAll();
		
		//增加一条记录
		public void addOne(Student entity);

		//修改一条记录
		public void updateOne(Student entity);
		
		//删除一条记录
		public void deleteOne(int id);
		
	}

# 二、MyBatis配置

root-context.xml为：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
					http://www.springframework.org/schema/context
					 http://www.springframework.org/schema/context/spring-context-3.2.xsd
					http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">
		<!-- Root Context: defines shared resources visible to all other web components -->

		<!-- 将数据库配置文件读取到容器中，交给Spring管理 -->
		<bean id="propertyConfigurer"
			class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
			<property name="locations">
				<list>
					<value>classpath:jdbc.properties</value>
				</list>
			</property>
		</bean>
		
		 <!-- 数据源定义 -->  
	    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
			init-method="init" destroy-method="close"> 
	        <property name="driverClassName" value="${jdbc.driverClassName}"></property>  
	        <property name="url" value="${jdbc.url}"></property>  
	        <property name="username" value="${jdbc.username}"></property>  
	        <property name="password" value="${jdbc.password}"></property>  
	    </bean>  
	    
	     <!-- MyBatis配置 -->  
		<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">  
	    	<property name="mapperLocations" value="classpath:mybatis.xml" /> 
	    	<property name="dataSource" ref="dataSource" />  
		</bean>
		
		<bean id="mapper" class="org.mybatis.spring.mapper.MapperFactoryBean">  
	    	<property name="mapperInterface" value="org.dao.StudentDao" />  
	    	<property name="sqlSessionFactory" ref="sqlSessionFactory" />  
		</bean>

	</beans>

mybatis.xml中写具体的增删改查语句

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"    
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">  
	<mapper namespace="org.dao.StudentDao">
	   
	    <!--查询所有记录-->
	    <select id="queryAll" resultType="org.entity.Student">
	    	select * from newstudent2 
	    </select>

	    <!--插入一条记录-->
	    <insert id="addOne" parameterType="org.entity.Student">
	    	insert into newstudent2 (id,name,phone) values (#{id},#{name},#{phone}) 
	    </insert>
	    
	    <!--修改一条记录  -->  
	    <update id="updateOne" parameterType="org.entity.Student">   
	        update newstudent2 set name = #{name},phone = #{phone} where id = #{id} 
	    </update>
	    
	    <!--删除一条记录  -->  
	    <delete id="deleteOne" parameterType="int">  
	       delete from newstudent2 where id = #{id}  
	    </delete>

	</mapper>

# 三、参考

1.[demo6](https://github.com/zhhgit/Java_web_demos/tree/master/demo6-jsp%20mysql%20mybatis%20crud)

2.[Mybatis整合Spring](http://elim.iteye.com/blog/1843309)

3.[SSM框架——详细整合教程（Spring+SpringMVC+MyBatis）](http://blog.csdn.net/gebitan505/article/details/44455235/)

4.[Spring和MyBatis环境整合](http://www.cnblogs.com/jyh317/p/3834142.html)




