---
layout: post
title: "Java Web(5):JSP&Servlet操作MySQL"
description: Java Web(5):JSP&Servlet操作MySQL
modified: 2016-09-25
category: Java
tags: [Java]
---

两种方式，JSP和Servlet。JSP方式可以理解为HTML中嵌入Java代码，Servlet方式可以理解为Java代码中输出html代码。只进行“查”操作。

# 一、MySQL数据库

安装bitnami-wampstack-5.6.25-0-windows-installer.exe套件，可以通过phpMyAdmin来图形化地管理MySQL数据库。可以直接将[这里](https://github.com/zhhgit/Java_web_demos/tree/master/demo4-jsp%20servlet%20mysql)的student.sql文件导入到新建的student数据库。其中newstudent表中有三条记录，分别有age，name，phone字段。

# 二、JSP

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

# 三、Servlet

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

# 四、参考

1.[demo4](https://github.com/zhhgit/java_web_demos/tree/master/demo4-jsp%20servlet%20mysql)

2.[在eclipse导入Java的jar包的方法JDBC](http://www.cnblogs.com/taoweiji/archive/2012/12/11/2812295.html)

3.[JSP标准标签库（JSTL）](http://www.runoob.com/jsp/jsp-jstl.html)

4.[JSP连接数据库](http://www.runoob.com/jsp/jsp-database-access.html)

5.[Servlet数据库访问](http://www.runoob.com/servlet/servlet-database-access.html)






