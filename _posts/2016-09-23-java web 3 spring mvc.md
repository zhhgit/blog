---
layout: post
title: "Java Web(3):使用Spring MVC"
description: Java Web(3):使用Spring MVC
modified: 2016-09-23
category: Java
tags: [Java]
---

# 一、配置

同样，建立Dynamic web project，在WebContent/WEB-INF/lib目录下放入一系列jar包，需要的jar包参见[这里](https://github.com/zhhgit/java_web_demos/tree/master/demo2-spring%20mvc)（只会多不会少）。

配置文件web.xml为：

	<?xml version="1.0" encoding="UTF-8"?>  
	<web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee"  
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
	    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee   
	    http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">  
	    <display-name></display-name>  
	      
	    
	    <!-- 监听spring上下文容器 -->  
	    <listener>  
	        <listener-class>  
	            org.springframework.web.context.ContextLoaderListener  
	        </listener-class>  
	    </listener>  
	      
	    <!-- 加载spring的xml配置文件到 spring的上下文容器中 -->  
	    <context-param>  
	        <param-name>contextConfigLocation</param-name>  
	        <param-value>classpath:root-context.xml</param-value>  
	    </context-param>  
	      
	    <!-- 配置Spring MVC DispatcherServlet -->  
	    <servlet>  
	        <servlet-name>MVC</servlet-name>  
	        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
	        <!-- 初始化参数 -->  
	        <init-param>  
	            <!-- 加载SpringMVC的xml到 spring的上下文容器中 -->  
	            <param-name>contextConfigLocation</param-name>  
	            <param-value>  
	                /WEB-INF/classes/mvc*.*  
	            </param-value>  
	        </init-param>  
	        <load-on-startup>1</load-on-startup>  
	    </servlet>  
	  
	    <!-- 配置DispatcherServlet所需要拦截的 url -->  
	    <servlet-mapping>  
	        <servlet-name>MVC</servlet-name>  
	        <url-pattern>*.html</url-pattern>  
	    </servlet-mapping>  
	  
	    <welcome-file-list>  
	        <welcome-file>index.html</welcome-file>  
	    </welcome-file-list>  
	  
	  
	</web-app>  

在Java Resources/src目录下，Spring MVC的配置文件mvc-context.xml和Spring全局配置文件root-context.xml分别为：

root-context.xml:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
					http://www.springframework.org/schema/context
					 http://www.springframework.org/schema/context/spring-context-3.2.xsd
					http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">
		<!-- Root Context: defines shared resources visible to all other web components -->
		 
	</beans>

mvc-context.xml:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans:beans xmlns="http://www.springframework.org/schema/mvc"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:beans="http://www.springframework.org/schema/beans"
		xmlns:p="http://www.springframework.org/schema/p" xmlns:aop="http://www.springframework.org/schema/aop"
		xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
					http://www.springframework.org/schema/aop
					http://www.springframework.org/schema/aop/spring-aop-3.2.xsd http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
		<!-- 加载Spring的全局配置文件 -->
		<beans:import resource="root-context.xml" />
		
		<!-- SpringMVC配置 -->
		
		<!-- 通过component-scan 让Spring扫描org.controller下的所有的类，让Spring的代码注解生效 -->
		<context:component-scan base-package="org.controller"></context:component-scan>
		
		<!-- 配置SpringMVC的视图渲染器， 让其前缀为:/ 后缀为.jsp  将视图渲染到/page/<method返回值>.jsp中 -->
		<beans:bean
			class="org.springframework.web.servlet.view.InternalResourceViewResolver"
			p:prefix="/page/" p:suffix=".jsp">
			</beans:bean>
	</beans:beans>

# 二、Controller

在Java Resources/src/org.controller包下分别为HomeController.java、UserController.java、OtherController：

HomeController.java如下，当请求url为index.html时进入该controller，进入index()函数，返回WebContent/page/目录下的home.jsp页面

	package org.controller;

	import org.springframework.stereotype.Controller;
	import org.springframework.web.servlet.ModelAndView;
	import org.springframework.web.bind.annotation.RequestMapping;

	@Controller 
	public class HomeController {		
		/***
		 * 首页 返回至/page/home.jsp页面
		 * @return
		 */
		@RequestMapping("index")
		public ModelAndView index(){
			//创建模型跟视图，用于渲染页面。并且指定要返回的页面为home页面
			ModelAndView mav = new ModelAndView("home");
			return mav;
		}
	}

home.jsp如下，很常规。一个表单用post方式提交到login.html。

	<%@ page language="java" contentType="text/html; charset=GB18030"
	    pageEncoding="GB18030"%>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=GB18030">
	<title>home</title>
	</head>
	<body>
	<h2>spring mvc 实例</h2>

	<form action="login.html" method="post">
		username:<input type="text" name="username" />
		<p>
		password:<input type="password" name="password"/>
		<p>
		<input type="submit" value="submit" />
	</form>

	<a href="other.html">other page</a>
	</body>
	</html>

拦截login.html进入UserController.java，接收post请求，进入login(String username,String password)函数，返回WebContent/page/目录下的succ.jsp页面。如下：

	package org.controller;

	import javax.servlet.http.HttpServletRequest;

	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestMethod;
	import org.springframework.web.servlet.ModelAndView;

	@Controller
	public class UserController {

		/***
		 * 用户登陆
		 * <p>注解配置，只允许POST提交到该方法
		 * @param username
		 * @param password
		 * @return
		 */
		@RequestMapping(value="login",method=RequestMethod.POST)
		public ModelAndView login(String username,String password){
			//验证传递过来的参数是否正确，否则返回到登陆页面。
			if(this.checkParams(new String[]{username,password})){
				//指定要返回的页面为succ.jsp
				ModelAndView mav = new ModelAndView("succ");
				//将参数返回给页面
				mav.addObject("username",username);
				mav.addObject("password", password);
				return mav;
			}
			return new ModelAndView("home");
		}
	//	/***
	//	 * 另一种参数传递的形式
	//	 * 通过request来处理请求过来的参数。
	//	 * @param username
	//	 * @param password
	//	 * @param request
	//	 * @return
	//	 */
	//	@RequestMapping(value="login",method=RequestMethod.POST)
	//	public ModelAndView login(String username,String password,HttpServletRequest request){
	//		request.setAttribute("username", username);
	//		request.setAttribute("password", password);
	//		return new ModelAndView("succ");
	//	}
		
		/***
		 * 验证参数
		 * @param params
		 * @return
		 */
		private boolean checkParams(String[] params){
			for(String param:params){
				if(param==""||param==null||param.isEmpty()){
					return false;
				}
			}
			return true;
		}
	}

succ.jsp页面中通过${variable}的形式获取具体的参数值：

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=GB18030">
	<title>succ</title>
	</head>
	<body>
	<h2>登陆</h2>
	 
	username:${username }
	<p>
	password:${password }
	 
	</body>
	</html>

拦截other.html进入OtherController.java，返回WebContent/page/目录下的other.jsp页面。

	<%@ page language="java" contentType="text/html; charset=GB18030"
	    pageEncoding="GB18030"%>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=GB18030">
	<title>other</title>
	</head>
	<body>
	<h2>other page</h2>
	</body>
	</html>

# 三、参考

1.[demo2](https://github.com/zhhgit/java_web_demos/tree/master/demo2-spring%20mvc)

2.[SpringMVC基础教程简单入门实例](http://blog.csdn.net/swingpyzf/article/details/8904205)

