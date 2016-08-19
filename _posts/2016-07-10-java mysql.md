---
layout: post
title: "Java连接数据库"
description: Java连接数据库
modified: 2016-07-10
category: Java
tags: [Java]
---

# 一、JSP

安装JDBC的包，可以参考[这里](http://www.cnblogs.com/taoweiji/archive/2012/12/11/2812295.html)，也可以建立的Dynamic web project，直接把解压缩后的jar包放在F:\JavaEE\TomcatTest\WebContent\WEB-INF\lib目录下。

安装JSTL的包，参考[这里](http://www.runoob.com/jsp/jsp-jstl.html),连接MySQL参考[这里](http://www.runoob.com/jsp/jsp-database-access.html)

sql标签要符合自己时间数据库的情况，注意url中的端口号：

	<sql:setDataSource var="snapshot" driver="com.mysql.jdbc.Driver" 
	url="jdbc:mysql://localhost:3306/test" 
	user="root"  password="" />

最后在浏览器中访问

	http://localhost:8081/TomcatTest/test.jsp

完整代码如下：

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
	 
	<sql:setDataSource var="snapshot" driver="com.mysql.jdbc.Driver" 
	url="jdbc:mysql://localhost:3306/test" 
	user="root"  password="" />
	 
	<sql:query dataSource="${snapshot}" var="result">
	SELECT * from employees;
	</sql:query>
	 
	<table border="1" width="100%">
	<tr>
	   <th>Emp ID</th>
	   <th>First Name</th>
	   <th>Last Name</th>
	   <th>Age</th>
	</tr>
	<c:forEach var="row" items="${result.rows}">
	<tr>
	   <td><c:out value="${row.id}"/></td>
	   <td><c:out value="${row.first}"/></td>
	   <td><c:out value="${row.last}"/></td>
	   <td><c:out value="${row.age}"/></td>
	</tr>
	</c:forEach>
	</table>
	 
	</body>
	</html>

# 二、Servlet

同样的要先装JDBC的包。Servlet位于F:\JavaEE\TomcatTest\src\testJava\DatabaseAccess.java，testJava是package名。

同样注意要修改端口号。中文乱码时候注意这句：

	response.setContentType("text/html;charset=utf8");

F:\JavaEE\TomcatTest\WebContent\WEB-INF\web.xml文件中要建立访问的URL与对应的Servlet的class的对应关系，增加如下部分：

	<servlet>
	     <servlet-name>DatabaseAccess</servlet-name>
	     <servlet-class>testJava.DatabaseAccess</servlet-class>
	 </servlet>
	 
	 <servlet-mapping>
	     <servlet-name>DatabaseAccess</servlet-name>
	     <url-pattern>/testJava/DatabaseAccess</url-pattern>
	 </servlet-mapping>

最后在浏览器中访问

	http://localhost:8081/TomcatTest/testJava/DatabaseAccess

完整代码如下：

	package testJava;

	// 加载必需的库
	import java.io.*;
	import java.util.*;
	import javax.servlet.*;
	import javax.servlet.http.*;
	import java.sql.*;
	 
	public class DatabaseAccess extends HttpServlet{
	    
	  public void doGet(HttpServletRequest request,
	                    HttpServletResponse response)
	            throws ServletException, IOException
	  {
	      // JDBC 驱动器名称和数据库的 URL
	      final String JDBC_DRIVER="com.mysql.jdbc.Driver";  
	      final String DB_URL="jdbc:mysql://localhost:3306/test";

	      //  数据库的凭据
	      final String USER = "root";
	      final String PASS = "";

	      // 设置响应内容类型
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
	         // 注册 JDBC 驱动器
	         Class.forName("com.mysql.jdbc.Driver");

	         // 打开一个连接
	         conn = DriverManager.getConnection(DB_URL,USER,PASS);

	         // 执行 SQL 查询
	         stmt = conn.createStatement();
	         String sql;
	         sql = "SELECT id, first, last, age FROM Employees";
	         ResultSet rs = stmt.executeQuery(sql);

	         // 从结果集中提取数据
	         while(rs.next()){
	            // 根据列名称检索
	            int id  = rs.getInt("id");
	            int age = rs.getInt("age");
	            String first = rs.getString("first");
	            String last = rs.getString("last");

	            // 显示值
	            out.println("ID: " + id + "<br>");
	            out.println("Age: " + age + "<br>");
	            out.println("First: " + first + "<br>");
	            out.println("Last: " + last + "<br>");
	            out.println("<hr>");
	         }
	         out.println("</body></html>");

	         // 清理环境
	         rs.close();
	         stmt.close();
	         conn.close();
	      }catch(SQLException se){
	         // 处理 JDBC 错误
	         se.printStackTrace();
	      }catch(Exception e){
	         // 处理 Class.forName 错误
	         e.printStackTrace();
	      }finally{
	         // 最后是用于关闭资源的块
	         try{
	            if(stmt!=null)
	               stmt.close();
	         }catch(SQLException se2){
	         }// 我们不能做什么
	         try{
	            if(conn!=null)
	            conn.close();
	         }catch(SQLException se){
	            se.printStackTrace();
	         }//end finally try
	      } //end try
	   }
	} 



