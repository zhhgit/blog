---
layout: post
title: "Spring+SpringMVC+MyBatis+Maven整合"
description: Spring+SpringMVC+MyBatis+Maven整合
modified: 2017-06-12
category: Java
tags: [Java]
---

# 一、整合概要

1.使用Maven管理依赖包，构建Web工程。整合过程主要参考[使用Maven搭建SpringMVC](http://www.cnblogs.com/zawier/p/5605040.html)和[Maven学习（三）-使用Maven构建Web项目](http://blog.csdn.net/yuguiyang1990/article/details/8796726)。主要关注Properties----Deployment Assembly/Java Build Path/Project Facets这几项的设置。

pom.xml如下

	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	  <modelVersion>4.0.0</modelVersion>
	  <groupId>cn.zhanghao90</groupId>
	  <artifactId>spring_maven</artifactId>
	  <packaging>war</packaging>
	  <version>0.0.1-SNAPSHOT</version>
	  <name>spring_maven Maven Webapp</name>
	  <url>http://maven.apache.org</url>
	  <properties>  
	      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>  
	  </properties>  
	  <dependencies>
	    <dependency>
	      <groupId>junit</groupId>
	      <artifactId>junit</artifactId>
	      <version>3.8.1</version>
	      <scope>test</scope>
	    </dependency>
	    
	    <!-- 配置Spring -->  
	    <dependency>  
	        <groupId>org.springframework</groupId>  
	        <artifactId>spring-core</artifactId>  
	        <version>4.3.0.RELEASE</version>  
	    </dependency>  
	      
	    <dependency>  
	        <groupId>org.springframework</groupId>  
	        <artifactId>spring-beans</artifactId>  
	        <version>4.3.0.RELEASE</version>  
	    </dependency>  
	      
	    <dependency>  
	        <groupId>org.springframework</groupId>  
	        <artifactId>spring-context</artifactId>  
	        <version>4.3.0.RELEASE</version>  
	    </dependency>  
	      
	    <dependency>  
	        <groupId>org.springframework</groupId>  
	        <artifactId>spring-jdbc</artifactId>  
	        <version>4.3.0.RELEASE</version>  
	    </dependency>  
	    
	    <!-- 配置Spring MVC -->
	    <dependency>
	  		<groupId>org.springframework</groupId>
	 		<artifactId>spring-webmvc</artifactId>
	  		<version>4.3.0.RELEASE</version>
		</dependency>
		
		<!-- 配置GSON -->
		<dependency>
	 		<groupId>com.google.code.gson</groupId>
	  		<artifactId>gson</artifactId>
	  		<version>2.8.0</version>
		</dependency>
		
		<!-- 配置MyBatis -->
		<dependency>
	  		<groupId>org.mybatis</groupId>
	  		<artifactId>mybatis</artifactId>
	  		<version>3.4.0</version>
		</dependency>
		
		<dependency>
	  		<groupId>org.mybatis</groupId>
	  		<artifactId>mybatis-spring</artifactId>
	  		<version>1.3.0</version>
		</dependency>
		
		<!-- 数据库连接池 -->
		<dependency>
	  		<groupId>com.mchange</groupId>
	  		<artifactId>c3p0</artifactId>
	  		<version>0.9.5.2</version>
		</dependency>
		
		<!-- JDBC -->
		<dependency>
	  		<groupId>mysql</groupId>
	  		<artifactId>mysql-connector-java</artifactId>
	  		<version>5.1.5</version>
		</dependency>
	   
	  </dependencies>
	  <build>
	    <finalName>spring_maven</finalName>
	  </build>
	</project>

2.连接数据库时用到了一个与此前不同的库c3p0。

3.配置文件都统一放置到src/main/resources目录。

4.Dynamic Web Module的版本需要在.settings文件夹中的org.eclipse.wst.common.project.facet.core.xml中进行修改，修改后重启eclipse。

5.首次使用需要在Window----Maven----Installations中配置成自己Maven。Window----Maven----User Settings设置成D:\Program Files\apache-maven-3.2.1\conf\settings.xml。其中可以添加阿里云镜像。

6.Export成war包直接放到Tomcat的webapps目录，自动解压。

# 二、参考

1.[zhhgit/SpringMVC_maven_framework](https://github.com/zhhgit/SpringMVC_maven_framework)

2.[Maven Repository](http://mvnrepository.com/)

3.[Maven学习（三）-使用Maven构建Web项目](http://blog.csdn.net/yuguiyang1990/article/details/8796726)

4.[使用Maven搭建SpringMVC](http://www.cnblogs.com/zawier/p/5605040.html)

5.[eclipse构建maven的web项目](http://blog.csdn.net/smilevt/article/details/8215558/)