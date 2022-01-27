---
layout: post
title: "HTTPS配置"
description: HTTPS配置
modified: 2016-11-28
category: Linux
tags: [Linux]
---

# 一、二级域名

在添加一条域名解析，A类型，主机记录为二级域名称，例如wechat，记录值为IP地址，例如一台阿里云服务器的外网IP。

# 二、HTTPS证书

通过腾讯云SSL证书管理申请，针对某个二级域名。验证的方式是通过添加一条新的域名解析，CNAME类型，主机记录例如为s2hw3l7imz8ihypx307fdm7fxicap6pj.wechat，记录值例如为s20161121111701.wechat.zhanghao90.top。(腾讯云验证方式有变化，具体看其官方文档)

# 三、Apache配置

SSL证书安装参考[这里](https://www.qcloud.com/document/product/400/4143#1.-apache-2.x.E8.AF.81.E4.B9.A6.E9.83.A8.E7.BD.B2)，修改conf/extra/httpd-ssl.conf文件如下：

	<VirtualHost wechat.zhanghao90.top:443>
	    DocumentRoot "C:/xampp/htdocs"
	    ServerName wechat.zhanghao90.top:443
	    SSLEngine on
	    SSLCertificateFile "sslConfig/Apache/2_www.domain.com_cert.crt"
	    SSLCertificateKeyFile "sslConfig/Apache/3_www.domain.com.key"
	    SSLCertificateChainFile "sslConfig/Apache/1_root_bundle.crt"
	</VirtualHost>

需要注意的是一定要用默认的443端口！至此已经可以通过https://wechat.zhanghao90.top访问htdocs目录下的文件。

在阿里云Linux机器上安装XAMPP后，需要这样配置才能生效。

	<VirtualHost *:443>
	
# 四、反向代理

通过Apache反向代理到另一个端口上的Tomcat。首先是要打开conf/httpd.conf文件中的一些模块。参考[利用Apache的转发模块实现反向代理服务器](http://blog.csdn.net/smstong/article/details/48976333)，因为已经配置了https，所有此时的ProxyPass和ProxyPassReverse应该写在conf/extra/httpd-ssl.conf文件中。例如

	ProxyPass / http://localhost:8081
	ProxyPassReverse / http://localhost:8081

可以同时做多个反向代理，但是要写在这条的上面

	ProxyPass /console-web http://1.2.3.4:8081/console-web
	ProxyPassReverse /console-web http://1.2.3.4:8081/console-web




	