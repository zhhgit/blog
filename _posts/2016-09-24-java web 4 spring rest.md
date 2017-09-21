---
layout: post
title: "Java Web(4):使用Spring REST接口"
description: Java Web(4):使用Spring REST接口
modified: 2016-09-24
category: Java
tags: [Java]
---

# 一、配置

WebContent/WEB-INF/目录下配置文件web.xml如下，拦截*.do的请求。

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

# 二、REST接口

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


# 三、接口调用页面

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

# 四、参考

1.[demo3](https://github.com/zhhgit/java_web_demos/tree/master/demo3-spring%20rest)
