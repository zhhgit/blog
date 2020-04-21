---
layout: post
title: "Nginx基础"
description: Nginx基础
modified: 2020-04-21
category: Nginx
tags: [Nginx]
---

# 一、Nginx基础

1.常用命令

	nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
	nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
	nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
	nginx -s reopen     重新打开日志文件。
	nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
	nginx -t            不运行，仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
	nginx -v            显示 nginx 的版本。
	nginx -V            显示 nginx 的版本，编译器版本和配置参数。

2.startup.bat脚本

	@echo off
	rem 如果启动前已经启动nginx并记录下pid文件，会kill指定进程
	nginx.exe -s stop

	rem 测试配置文件语法正确性
	nginx.exe -t -c conf/nginx.conf

	rem 显示版本信息
	nginx.exe -v

	rem 按照指定配置去启动nginx
	nginx.exe -c conf/nginx.conf
	
3.反向代理
  
http下配置upstream

    upstream zh_servers{
        server 172.21.111.18:21000;
    }

http/server/location下配置proxy_pass

	location / {
		root   html;
		index  index.html index.htm;
		proxy_pass http://zh_servers;
	}
	
4.负载均衡策略

轮询

	upstream bck_testing_01 {
	  # 默认所有服务器权重为 1
	  server 192.168.250.220:8080
	  server 192.168.250.221:8080
	  server 192.168.250.222:8080
	}

加权轮询

	upstream bck_testing_01 {
	  server 192.168.250.220:8080   weight=3
	  server 192.168.250.221:8080              # default weight=1
	  server 192.168.250.222:8080              # default weight=1
	}

最少连接

	upstream bck_testing_01 {
	  least_conn;

	  # with default weight for all (weight=1)
	  server 192.168.250.220:8080
	  server 192.168.250.221:8080
	  server 192.168.250.222:8080
	}

加权最少连接

	upstream bck_testing_01 {
	  least_conn;
	  
	  server 192.168.250.220:8080   weight=3
	  server 192.168.250.221:8080              # default weight=1
	  server 192.168.250.222:8080              # default weight=1
	}

IP Hash

	upstream bck_testing_01 {

	  ip_hash;

	  # with default weight for all (weight=1)
	  server 192.168.250.220:8080
	  server 192.168.250.221:8080
	  server 192.168.250.222:8080
	}

普通Hash

	upstream bck_testing_01 {

	  hash $request_uri;

	  # with default weight for all (weight=1)
	  server 192.168.250.220:8080
	  server 192.168.250.221:8080
	  server 192.168.250.222:8080
	}

# 二、参考

1.[Nginx教程](https://dunwu.github.io/nginx-tutorial/#/README)

2.[官网](http://nginx.org/en/download.html)