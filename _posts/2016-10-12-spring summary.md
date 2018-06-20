---
layout: post
title: "Spring使用小结"
description: Spring使用小结
modified: 2016-10-12
category: Java
tags: [Java]
---

# 一、安装Tomcat

图形化安装后将Tomcat和Eclipse相关联，建立Dynamic web project直接在Eclipse启动项目并部署到Tomcat。

# 二、Spring Beans

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
	
# 三、Spring MVC

配置如下。建立Dynamic web project，在WebContent/WEB-INF/lib目录下放入一系列jar包，需要的jar包参见[这里](https://github.com/zhhgit/java_web_demos/tree/master/demo2-spring%20mvc)（只会多不会少）。

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
	
# 四、Spring REST接口

配置如下。WebContent/WEB-INF/目录下配置文件web.xml如下，拦截*.do的请求。

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" version="3.0">
	  <display-name></display-name>
	  <listener>
	    <listener-class>
				org.springframework.web.context.ContextLoaderListener
			</listener-class>
	  </listener>
	  <context-param>
	    <param-name>contextConfigLocation</param-name>
	    <param-value>classpath:root-context.xml</param-value>
	  </context-param>
	  <servlet>
	    <servlet-name>Rest</servlet-name>
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	    <init-param>
	      <param-name>contextConfigLocation</param-name>
	      <param-value>
					/WEB-INF/classes/rest*.*
				</param-value>
	    </init-param>
	    <load-on-startup>1</load-on-startup>
	  </servlet>
	  <servlet-mapping>
	    <servlet-name>Rest</servlet-name>
	    <url-pattern>*.do</url-pattern>
	  </servlet-mapping>
	    <servlet-mapping>
	    <servlet-name>Rest</servlet-name>
	    <url-pattern>*.html</url-pattern>
	  </servlet-mapping>
	  
	  <welcome-file-list>
	    <welcome-file>index.html</welcome-file>
	  </welcome-file-list>
	</web-app>

Java Resources/src/目录下的mvc-context.xml和root-context.xml与上一篇中相同。

Java Resources/src/org.controller包中的UserController.java为如下，可以分别接收GET、POST请求并返回JSON数据。GET方式从参数中取数据，POST方式通过Java Resources/src/org.model包中UserInput.java类来接收发送过来的JSON数据。

UserController.java：

	package org.controller;

	import java.util.HashMap;
	import java.util.Map;
	import javax.servlet.http.HttpServletRequest;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestBody;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestMethod;
	import org.springframework.web.bind.annotation.RequestParam;
	import org.springframework.web.bind.annotation.ResponseBody;
	import com.google.gson.Gson;
	import org.model.*;

	@Controller
	public class UserController {
		
		//接受GET请求
		@RequestMapping(value = "/loginget", method = { RequestMethod.GET })
		@ResponseBody
		public String loginget(@RequestParam("name") String name, @RequestParam("password") String password) {
			String code = "00";
			String msg = "success_get";
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("code", code);
			responseMap.put("msg", msg);
			responseMap.put("name", name);
			responseMap.put("password", password);
			return new Gson().toJson(responseMap);
		}
		
		//接受POST请求
		@RequestMapping(value = "/loginpost", method = { RequestMethod.POST })
		@ResponseBody
		public String loginpost(@RequestBody String params) {
			Gson gson = new Gson();
			UserInput userinput = gson.fromJson(params, UserInput.class);
			String code = "00";
			String msg = "success_post";
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("code", code);
			responseMap.put("msg", msg );
			responseMap.put("name", userinput.getName());
			responseMap.put("password", userinput.getPassword());
			return gson.toJson(responseMap);
		}
	}

UserInput.java:

	package org.model;

	public class UserInput {
		private String name;

		private String password;

		public void setName(String value) {
			this.name = value;
		}
		public String getName() {
			return this.name;
		}
		
		public void setPassword(String value) {
			this.password = value;
		}
		public String getPassword() {
			return this.password;
		}

	}

Java Resources/src/org.controller包中的还有HomeController.java，拦截为index.html的url，返回home.jsp。

WebContent/WEB-INF/page目录下的home.jsp如下，有两个按钮，分别发送GET和POST请求到REST接口，并将返回的JSON数据Alert出来。

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=0">
	    <title>Title</title>
	</head>
	<body>
	<input type="button" value="发送get请求" id="getButton" />
	<br/>
	<input type="button" value="发送post请求" id="postButton" />
	<script src="<%=request.getContextPath()%>/page/zepto.min.js"></script>
	<script>
	    $("#getButton").on("click",function(){
	        var dataJson= {
	            "name":"zhang",
	            "password":"1234"
	        };
	        $.ajax({
	            type: 'GET',
	            url: "http://localhost:8081/Spring_rest/loginget.do",
	            contentType: "application/json; charset=utf-8",
	            data: dataJson,
	            dataType: 'json',
	            success: function(resp){
	                alert("GET请求成功" + JSON.stringify(resp));
	            },
	            error: function (err) {
	                alert("GET请求失败");
	            }
	        });
	    });

	    $("#postButton").on("click",function(){
	        var dataJson= {
	            "name":"zhang",
	            "password":"1234"
	        };
	        $.ajax({
	            type: 'POST',
	            url: "http://localhost:8081/Spring_rest/loginpost.do",
	            contentType: "application/json; charset=utf-8",
	            data: JSON.stringify(dataJson),
	            dataType: 'json',
	            success: function(resp){
	                alert("POST请求成功" + JSON.stringify(resp));
	            },
	            error: function (err) {
	                alert("POST请求失败");
	            }
	        });
	    });
	</script>
	</body>
	</html>

值得注意的是加载静态资源的路径：

	<script src="<%=request.getContextPath()%>/page/zepto.min.js"></script>
	
# 五、JSP&Servlet操作MySQL

两种方式，JSP和Servlet。JSP方式可以理解为HTML中嵌入Java代码，Servlet方式可以理解为Java代码中输出html代码。只进行“查”操作。

安装bitnami-wampstack-5.6.25-0-windows-installer.exe套件，可以通过phpMyAdmin来图形化地管理MySQL数据库。可以直接将[这里](https://github.com/zhhgit/Java_web_demos/tree/master/demo4-jsp%20servlet%20mysql)的student.sql文件导入到新建的student数据库。其中newstudent表中有三条记录，分别有age，name，phone字段。

JSP：

建立Dynamic web project，安装JDBC包，下载mysql-connector-java.jar包放在WebContent/WEB-INF/lib目录下。安装JSTL，将standard.jar和jstl.jar包放在WebContent/WEB-INF/lib目录下。sql:setDataSource标签要符合自己数据库的情况，注意url中的端口号：

	<sql:setDataSource var="snapshot" driver="com.mysql.jdbc.Driver" url="jdbc:mysql://localhost:3306/student" user="root"  password="root" />

在浏览器中访问如下url可以显示数据库中的记录。

	http://localhost:8081/JSP_Servlet_MySQL/index.jsp

index.jsp:

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	<%@ page import="java.io.*,java.util.*,java.sql.*"%>
	<%@ page import="javax.servlet.http.*,javax.servlet.*" %>
	<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
	<%@ taglib uri="http://java.sun.com/jsp/jstl/sql" prefix="sql"%>
	 
	<html>
	<head>
	<title>SELECT 操作</title>
	</head>
	<body>
	 
	<sql:setDataSource var="snapshot" driver="com.mysql.jdbc.Driver" url="jdbc:mysql://localhost:3306/student" user="root"  password="root" />
	 
	<sql:query dataSource="${snapshot}" var="result">
	SELECT * from newstudent;
	</sql:query>
	 
	<table border="1" width="100%">
	<tr>
	   <th>Age</th>
	   <th>Name</th>
	   <th>Phone</th>
	</tr>
	<c:forEach var="row" items="${result.rows}">
	<tr>
	   <td><c:out value="${row.age}"/></td>
	   <td><c:out value="${row.name}"/></td>
	   <td><c:out value="${row.phone}"/></td>
	</tr>
	</c:forEach>
	</table>
	 
	</body>
	</html>

其中

	<sql:setDataSource>	指定数据源
	<sql:query>	运行SQL查询语句
	<c:forEach>	基础迭代标签，接受多种集合类型
	<c:out>	用于在JSP中显示数据，就像<%= ... >

Servlet：

同样要先装JDBC的包。Servlet是Java Resources/src/test包下的ShowStudent.java。中文乱码时注意这句：

	response.setContentType("text/html;charset=utf8");

WebContent/WEB-INF/web.xml文件中要建立访问的URL与对应的Servlet的class的对应关系，增加如下部分：

	<servlet>
		<servlet-name>student</servlet-name>
		<servlet-class>test.ShowStudent</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>student</servlet-name>
		<url-pattern>/student</url-pattern>
	</servlet-mapping>

最后在浏览器中访问

	http://localhost:8081/JSP_Servlet_MySQL/student
	
# 六、Spring JdbcTemplate操作MySQL

配置数据源：

jdbc.properties为:

	jdbc.driverClassName=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/student
	jdbc.username=root
	jdbc.password=root

root-context.xml为:

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
	      
	    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate" abstract="false" lazy-init="false" autowire="default" >  
	        <property name="dataSource">  
	            <ref bean="dataSource" />  
	        </property>  
	    </bean>
	    
	    <bean id="studentDao" class="org.dao.StudentDao">  
	       <property name="template">  
	          <ref bean="jdbcTemplate" />  
	       </property>  
	    </bean>

	</beans>

解决中文乱码：

mvc-context.xml为:

	<?xml version="1.0" encoding="UTF-8"?>
	<beans
		xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
		xmlns:beans="http://www.springframework.org/schema/beans"
		xmlns:p="http://www.springframework.org/schema/p"
		xmlns:aop="http://www.springframework.org/schema/aop"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc" 
		xsi:schemaLocation="
				http://www.springframework.org/schema/mvc
				http://www.springframework.org/schema/mvc/spring-mvc.xsd
				http://www.springframework.org/schema/aop
				http://www.springframework.org/schema/aop/spring-aop-3.2.xsd 
				http://www.springframework.org/schema/beans 
				http://www.springframework.org/schema/beans/spring-beans.xsd
				http://www.springframework.org/schema/context 
				http://www.springframework.org/schema/context/spring-context.xsd" >
				
		<!-- 加载Spring的全局配置文件 -->
		<import resource="root-context.xml" />
		
		<!-- SpringMVC配置 -->
		
		<!-- 通过component-scan 让Spring扫描org.controller，org.service，org.dao下的所有的类，让Spring的代码注解生效 -->
		<context:component-scan base-package="org.controller"></context:component-scan>
		<context:component-scan base-package="org.service"></context:component-scan>
		<context:component-scan base-package="org.dao"></context:component-scan>
		
		<!-- 配置SpringMVC的视图渲染器， 让其前缀为:/ 后缀为.jsp  将视图渲染到/page/<method返回值>.jsp中 -->
		<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" p:prefix="/page/" p:suffix=".jsp"></bean>		
			
		<!-- 解决中文乱码问题 -->
		<mvc:annotation-driven>
		    <mvc:message-converters>
		        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
		            <property name="supportedMediaTypes">
		                <list>
		                    <value>application/json;charset=UTF-8</value>
		                </list>
		            </property>
		        </bean>
		    </mvc:message-converters>
		</mvc:annotation-driven>

	</beans>

org.controller包：

当访问student2.html时进入HomeController.java:

	package org.controller;

	import java.util.HashMap;
	import java.util.List;
	import java.util.Map;
	import org.entity.*;
	import org.service.*;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.servlet.ModelAndView;
	import org.springframework.web.bind.annotation.RequestMapping;


	@Controller 
	public class HomeController {
		
		@Autowired
		private StudentService studentService;
		
		@RequestMapping("student2")
		public ModelAndView index(){
			List<Student> studentList = studentService.queryAll();
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("rows", studentList);	
			ModelAndView mav = new ModelAndView("student2");
			mav.addObject("result", responseMap);
			return mav;
		}
	}

用于处理增删改查请求的StudentController.java如下，其中调用StudentService.java

	package org.controller;

	import java.util.HashMap;
	import java.util.List;
	import java.util.Map;

	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestBody;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestMethod;
	import org.springframework.web.bind.annotation.ResponseBody;
	import com.google.gson.Gson;
	import org.service.*;
	import org.entity.*;


	@Controller
	public class StudentController {
		
		@Autowired
		private StudentService studentService;
		
		//查询所有记录
		@ResponseBody
		@RequestMapping(value = "/queryAll", method = { RequestMethod.POST }, produces = "application/json; charset=UTF-8")
		public String getList() {
			String code = "01";
			String msg = "fail";
			Gson gson = new Gson();
			List<Student> studentList = studentService.queryAll();
			if (studentList != null && studentList.size() > 0) {
				code = "00";
				msg = "success";
			}

			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("code", code);
			responseMap.put("msg", msg);
			responseMap.put("rows", studentList);
			return gson.toJson(responseMap);
		}
		
		//增加一条记录
		@ResponseBody
		@RequestMapping(value = "/addOne", method = { RequestMethod.POST }, produces = "application/json; charset=UTF-8")
		public String addOne(@RequestBody String params) {
			String code = "01";
			String msg = "fail";
			Gson gson = new Gson();
			Student studentParams = gson.fromJson(params, Student.class);
			String callbackResult = studentService.addOne(studentParams);
			if (callbackResult == "success") {
				code = "00";
				msg = "success";
			}
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("code", code);
			responseMap.put("msg", msg);
			return gson.toJson(responseMap);
		}
		
		//更新一条记录
		@ResponseBody
		@RequestMapping(value = "/updateOne", method = { RequestMethod.POST }, produces = "application/json; charset=UTF-8")
		public String updateOne(@RequestBody String params) {
			String code = "01";
			String msg = "fail";
			Gson gson = new Gson();
			Student studentParams = gson.fromJson(params, Student.class);
			String callbackResult = studentService.updateOne(studentParams);
			if (callbackResult == "success") {
				code = "00";
				msg = "success";
			}
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("code", code);
			responseMap.put("msg", msg);
			return gson.toJson(responseMap);
		}
		
		//删除一条记录
		@ResponseBody
		@RequestMapping(value = "/deleteOne", method = { RequestMethod.POST }, produces = "application/json; charset=UTF-8")
		public String deleteOne(@RequestBody String params) {
			String code = "01";
			String msg = "fail";
			Gson gson = new Gson();
			IdEntity idParams = gson.fromJson(params, IdEntity.class);
			String callbackResult = studentService.deleteOne(idParams.getId());
			if (callbackResult == "success") {
				code = "00";
				msg = "success";
			}
			Map<String, Object> responseMap = new HashMap<String, Object>();
			responseMap.put("code", code);
			responseMap.put("msg", msg);
			return gson.toJson(responseMap);
		}
		
	}

org.service包：

StudentService.java对DAO层做了一个包装：

	package org.service;

	import java.util.List;

	import org.entity.*;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;
	import org.dao.*;

	@Service("studentService")
	public class StudentService {
		
		@Autowired
		private StudentDao studentDao;
		
		//查询所有记录
		public List<Student> queryAll() {
			return studentDao.queryAll();
		}
		
		//增加一条记录
		public String addOne(Student entity) {
			return studentDao.addOne(entity);
		}
		
		//修改一条记录
		public String updateOne(Student entity) {
			return studentDao.updateOne(entity);
		}
		
		//删除一条记录
		public String deleteOne(int id) {
			return studentDao.deleteOne(id);
		}
		
	}

org.dao包：

StudentDao.java进行具体的增删改查操作：

	package org.dao;

	import org.springframework.jdbc.core.JdbcTemplate;
	import org.springframework.jdbc.core.RowCallbackHandler;
	import org.springframework.stereotype.Repository;

	import java.sql.ResultSet;
	import java.sql.SQLException;
	import java.util.ArrayList;
	import java.util.List;
	import org.entity.*;

	@Repository
	public class StudentDao {
		
		private JdbcTemplate template;
		
		public JdbcTemplate getTemplate() {  
	        return template;  
	    }  
	  
	    public void setTemplate(JdbcTemplate template) {  
	        this.template = template;  
	    }  
		
	    //查询所有记录
		public List<Student> queryAll() {
			final ArrayList<Student> list = new ArrayList<Student>();
			String strSql = "select * from newstudent2 ";
			template.query(strSql, new Object[] {}, new RowCallbackHandler() {
				public void processRow(ResultSet rs) throws SQLException {
					Student entity = new Student();
					entity.setId(rs.getInt("id"));
					entity.setName(rs.getString("name"));
					entity.setPhone(rs.getString("phone"));
					list.add(entity);
				}
			});
			return list;
		}
		
		//增加一条记录
		public String addOne(Student entity) {
			String callbackResult = "fail";
			String strSql = "insert into newstudent2 (id,name,phone) values (?,?,?)";
			Object obj[] = {entity.getId(), entity.getName(), entity.getPhone()};
			int ret = template.update(strSql, obj);
			if (ret > 0) {
				callbackResult = "success";
			}
			return callbackResult;
		}

		//修改一条记录
		public String updateOne(Student entity) {
			String callbackResult = "fail";
			String strSql = "update newstudent2 set name=?,phone=? where id=?";
			Object obj[] = {entity.getName(), entity.getPhone(), entity.getId()};
			int ret = template.update(strSql, obj);
			if (ret > 0) {
				callbackResult = "success";
			}
			return callbackResult;
		} 
		
		//删除一条记录
		public String deleteOne(int id) {
			String callbackResult = "fail";
			String strSql = "delete from newstudent2 where id=?";
			Object obj[] = {id};
			int ret = template.update(strSql, obj);
			if (ret > 0) {
				callbackResult = "success";
			}
			return callbackResult;
		}
		
	}

org.entity包：

Student.java为实体类：

	package org.entity;

	public class Student {
		private int id;

		private String name;
		
		private String phone;
		
		public void setId(int value) {
			this.id = value;
		}
		public int getId() {
			return this.id;
		}

		public void setName(String value) {
			this.name = value;
		}
		public String getName() {
			return this.name;
		}
		
		public void setPhone(String value) {
			this.phone = value;
		}
		public String getPhone() {
			return this.phone;
		}
	}
	
# 七、Spring&MyBatis操作MySQL

org.dao包：

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

MyBatis配置：

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

# 八、参考

1.[Eclipse JSP/Servlet环境搭建](http://www.runoob.com/jsp/eclipse-jsp.html)

2.[Tomcat安装及配置教程](http://jingyan.baidu.com/article/870c6fc33e62bcb03fe4be90.html)

3.[SpringMVC基础教程简单入门实例](http://blog.csdn.net/swingpyzf/article/details/8904205)

4.[在eclipse导入Java的jar包的方法JDBC](http://www.cnblogs.com/taoweiji/archive/2012/12/11/2812295.html)

5.[JSP标准标签库（JSTL）](http://www.runoob.com/jsp/jsp-jstl.html)

6.[JSP连接数据库](http://www.runoob.com/jsp/jsp-database-access.html)

7.[Servlet数据库访问](http://www.runoob.com/servlet/servlet-database-access.html)

8.[Mybatis整合Spring](http://elim.iteye.com/blog/1843309)

9.[SSM框架——详细整合教程（Spring+SpringMVC+MyBatis）](http://blog.csdn.net/gebitan505/article/details/44455235/)

10.[Spring和MyBatis环境整合](http://www.cnblogs.com/jyh317/p/3834142.html)
