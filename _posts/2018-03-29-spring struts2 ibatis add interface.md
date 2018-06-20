---
layout: post
title: "在Spring Struts2 iBatis整合的工程中新增接口"
description: 在Spring Struts2 iBatis整合的工程中新增接口
modified: 2018-03-29
category: Java
tags: [Java]
---

# 数据层：

1.修改 resources/ibatis/sqlmap-config-updsdb.xml  //指定新增的ibatis的xml文件，也就是2

2.新增 resources/ibatis/TblPpdataAt.xml   //新增的ibatis的xml文件

3.修改 resources/spring/db/updsdb.xml //实例化数据层DAO，也就是4

4.新增 com.unionpay.nmg.service.dao.TblPpdataAtDao    //新增的数据访问DAO

5.新增 com.unionpay.nmg.service.entity.TblPpdataAt    //新增的领域对象，数据实体


# 服务层：

6.新增 resources/spring/bo/ppdata.xml //新增的服务层spring xml文件，实例化服务层对象，也就是8

7.新增 com.unionpay.nmg.service.bo.PpdataAtBo //新增的服务层接口

8.新增 com.unionpay.nmg.service.bo.impl.PpdataAtBoImpl    //新增的服务层实现类

# 控制层：

9.修改 resources/struts.xml   //指定新增的控制层struts的xml文件，也就是10

10.新增 resources/struts/ppdata.xml   //新增的控制层struts xml文件

11.修改 resources/spring/action/action.xml    //实例化控制层action，也就是12

12.新增 com.unionpay.nmg.service.action.PpdataAction  //新增的控制层action

# 访问权限：

13.修改 com.unionpay.nmg.service.interceptor.LoginInterceptor		//修改访问权限控制