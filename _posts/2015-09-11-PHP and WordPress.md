---
layout: post
title: "PHP运行环境配置与WordPress"
description: PHP运行环境配置与WordPress
modified: 2015-09-11
category: PHP
tags: [PHP]
featured: true
---

# 一、手动配置

分别安装Apache，PHP，比较复杂，要改地址还有根据自己电脑端口占用的情况设置。最后并没有这样来装。

1.[Apache服务器下载地址](http://httpd.apache.org/)

2.[如何从Apache官网下载windows版apache服务器](http://jingyan.baidu.com/article/29697b912f6539ab20de3cf8.html)

3.[最新php环境搭建](http://jingyan.baidu.com/article/154b46315242b328ca8f4101.html)

# 二、自动配置

安装XAMPP。安装版本xampp-win32-5.6.12-0-VC11-installer.exe

1.[XAMPP下载](https://www.apachefriends.org/download.html)

要改变端口设置，所有如下的设置在最右侧的config中只是起到校验的作用，真正要改，需要改变每个config文件，自己查找设置端口。现在的配置如下：

(1)Apache: 

main port:8082

SSL port:443

(2)MySQL

main port:3306

(3)FileZilla

main port:21

admin port:14147

(4)Mercury

Port1:25;Port2:79;Port3:105;Port4:106;Port5:110;Port6:143;Port7:2224;

(5)Tomcat

之前有单独装过Tomcat，安装在8081端口，所以XAMPP中一直报冲突，改动到如下端口号，但是暂时并没有用到套件中的Tomcat。

main port:8085

AJP port:8089

HTTP port:8083

# 三、PHP程序运行

D:\xampp\htdocs对应于Localhost:8082。

想要运行一个PHP程序，先建立文件夹D:\xampp\htdocs\learnPHP，然后把try1.php文件放在之中，运行MySQL和Apache，就可以通过如下地址访问页面。

<http://localhost:8082/learnPHP/try1.php>

# 四、WordPress安装

参考

1.[如何使用XAMPP本地搭建一个属于你自己的网站](http://www.chinaz.com/web/2012/0131/233229.shtml)

类似于第三部分，在如下地址下载后解压放到D:\xampp\htdocs\目录

2.[WordPress官网](https://cn.wordpress.org/)

需要先在如下地址建立用于存放数据的数据库

<http://localhost:8082/phpmyadmin/#PMAURL-0:index.php?db=&table=&server=1&target=&token=1f75d14a86ef2d95ca3b9342eea614f0>

之后一步步配置，傻瓜式操作。

# 五、其他参考

1.[W3School的PHP教程](http://www.w3school.com.cn/php/index.asp)

2.[菜鸟PHP教程](http://www.runoob.com/php/php-tutorial.html)




	