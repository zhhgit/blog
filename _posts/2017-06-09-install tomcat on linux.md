---
layout: post
title: "Linux上安装Tomcat"
description: Linux上安装Tomcat
modified: 2017-06-09
category: Linux
tags: [Linux]
---

# 一、安装Tomcat

下载apache-tomcat-8.5.15.zip(core,zip包)，解压后放到/usr/local/目录下

    upzip apache-tomcat-8.5.15.zip
    cp ./apache-tomcat-8.5.15 /usr/local/apache-tomcat-8.5.15

修改端口号，默认为8080，找到对应位置，修改为8081

    cd /usr/local/apache-tomcat-8.5.15/conf
    grep "8080" server.xml

通过浏览器登录时需要修改conf/tomcat-users.xml，添加用户

    <role rolename="manager-gui"/>
    <user username="user" password="password" roles="manager-gui"/>

启动和停止命令分别为

    cd /usr/local/apache-tomcat-8.5.15/bin
    //启动
    sh startup.sh
    //停止
    sh shutdown.sh
    //命令无法执行时可能需要增加执行权限
    chmmod +x *.sh

war包需要部署到webapps目录下，会自动解压。

# 二、参考

1.[在mac系统安装Apache Tomcat的详细步骤](http://blog.csdn.net/huyisu/article/details/38372663)
