---
layout: post
title: "Java WebService"
description: Java WebService
modified: 2018-06-20
category: Java
tags: [Java]
---

# 一、WebService服务端

WebService接口

	package cn.zhanghao90.wsserver.service;

	import javax.jws.WebMethod;

	@javax.jws.WebService
	public interface WebService {
		@WebMethod
		String sayHello(String word);
	}

WebService实现类

	package cn.zhanghao90.wsserver.service;

	@javax.jws.WebService
	public class WebServiceImpl implements WebService {

		@Override
		public String sayHello(String word) {
			String ret = "hello " + word;
			return ret;
		}

	}

在Controller的publish方法中发布服务

	package cn.zhanghao90.wsserver.controller;

	import javax.xml.ws.Endpoint;

	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;

	@Controller
	public class HomeController {
		
		@RequestMapping("/index")
		public String index() {
			return "demo";
		}
		
		@RequestMapping("/publish")
		public void publish(){
			String address = "http://localhost:8082/wsserver/webservice";
			Endpoint.publish(address,new cn.zhanghao90.wsserver.service.WebServiceImpl());
			System.out.println("success publish");
		}
		
	}

在浏览器中输入http://localhost:8081/webserver/publish，发布webservice服务到http://localhost:8082/wsserver/webservice。
在浏览器中输入http://localhost:8082/wsserver/webservice?wsdl可以看到wsdl文件。

# 二、WebService客户端

使用wsimport工具可以根据wsdl文件生成客户端代码。命令如下，其中-s代表目标目录，-p代表package。

	wsimport -s D:\zhanghao\work\spare\workspace\ws_spare\wsclient\src\main\java -p cn.zhanghao90.wsclient.ws -keep http://localhost:8082/wsserver/webservice?wsdl

在另一个工程wsclient中调用sayHello方法

	package cn.zhanghao90.wsclient.controller;

	import java.util.HashMap;
	import java.util.Map;

	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;
	import cn.zhanghao90.wsclient.util.JsonUtil;
	import cn.zhanghao90.wsclient.ws.WebServiceImpl;
	import cn.zhanghao90.wsclient.ws.WebServiceImplService;
	import cn.zhanghao90.wsclient.wsglfil.FileSendManageWSImpl;
	import cn.zhanghao90.wsclient.wsglfil.FileSendManageWSImplService;

	@Controller
	public class HomeController {

		@RequestMapping("/call")
		public String call(String ret){
			WebServiceImplService webServiceImplService = new WebServiceImplService();
			WebServiceImpl port = webServiceImplService.getWebServiceImplPort();
			String retString = port.sayHello("world");
			return retString;
		}
		
	}

# 三、参考

1.[极致精简的webservice例子](https://www.cnblogs.com/fengwenzhee/p/6915606.html)
