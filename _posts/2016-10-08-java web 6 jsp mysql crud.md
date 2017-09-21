---
layout: post
title: "Java Web(6):Spring JdbcTemplate操作MySQL"
description: Java Web(6):Spring JdbcTemplate操作MySQL
modified: 2016-10-08
category: Java
tags: [Java]
---

# 一、配置数据源

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

# 二、解决中文乱码

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

# 三、org.controller包

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

# 四、org.service包

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

# 五、org.dao包

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

# 六、org.entity包

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

# 七、参考

1.[demo5](https://github.com/zhhgit/java_web_demos/tree/master/demo5-jsp%20mysql%20crud)






