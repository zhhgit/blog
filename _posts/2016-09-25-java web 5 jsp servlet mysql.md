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

完整代码如下：

	package test;

	// 加载必需的库
	import java.io.*;
	import java.util.*;
	import javax.servlet.*;
	import javax.servlet.http.*;
	import java.sql.*;
	 
	public class ShowStudent extends HttpServlet{
	    
	  public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException
	  {
	      //JDBC驱动器名称和数据库的 URL
	      final String JDBC_DRIVER="com.mysql.jdbc.Driver";
	      final String DB_URL="jdbc:mysql://localhost:3306/student";

	      //数据库的凭据
	      final String USER = "root";
	      final String PASS = "root";

	      //设置响应内容类型
	      response.setContentType("text/html;charset=utf8");
	      PrintWriter out = response.getWriter();
	      String title = "数据库结果";
	      String docType =
	        "<!doctype html public \"-//w3c//dtd html 4.0 " +
	         "transitional//en\">\n";
	      out.println(docType +
	         "<html>\n" +
	         "<head><title>" + title + "</title></head>\n" +
	         "<body bgcolor=\"#f0f0f0\">\n" +
	         "<h1 align=\"center\">" + title + "</h1>\n");
	      Connection conn = null;
	      Statement stmt = null;
	      try{
	         //注册JDBC驱动器
	         Class.forName(JDBC_DRIVER);
	         //打开一个连接
	         conn = DriverManager.getConnection(DB_URL,USER,PASS);
	         //执行SQL查询
	         stmt = conn.createStatement();
	         String sql;
	         sql = "SELECT * FROM newstudent";
	         ResultSet rs = stmt.executeQuery(sql);
	         //从结果集中提取数据
	         while(rs.next()){
	            //根据列名称检索
	            int age = rs.getInt("age");
	            String name = rs.getString("name");
	            String phone = rs.getString("phone");
	            //显示值
	            out.println("Age: " + age + "<br>");
	            out.println("Name: " + name + "<br>");
	            out.println("Phone: " + phone + "<br>");
	            out.println("<hr>");
	         }
	         out.println("</body></html>");
	         //清理环境
	         rs.close();
	         stmt.close();
	         conn.close();
	      }catch(SQLException se){
	         //处理JDBC错误
	         se.printStackTrace();
	      }catch(Exception e){
	         //处理Class.forName错误
	         e.printStackTrace();
	      }finally{
	         //最后是用于关闭资源的块
	         try{
	            if(stmt!=null)
	               stmt.close();
	         }catch(SQLException se2){
	         }
	         try{
	            if(conn!=null)
	            conn.close();
	         }catch(SQLException se){
	            se.printStackTrace();
	         }
	      }
	   }
	}  

# 四、参考

1.[demo4](https://github.com/zhhgit/java_web_demos/tree/master/demo4-jsp%20servlet%20mysql)

2.[在eclipse导入Java的jar包的方法JDBC](http://www.cnblogs.com/taoweiji/archive/2012/12/11/2812295.html)

3.[JSP标准标签库（JSTL）](http://www.runoob.com/jsp/jsp-jstl.html)

4.[JSP连接数据库](http://www.runoob.com/jsp/jsp-database-access.html)

5.[Servlet数据库访问](http://www.runoob.com/servlet/servlet-database-access.html)






