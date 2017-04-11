---
layout: post
title: "阿里云XAMPP开发环境安装配置"
description: 阿里云XAMPP开发环境安装配置
modified: 2017-04-11
category: Linux
tags: [Linux]
---

# 一、安装XAMPP

下载64位的包，放到共享目录/mnt/share下。拷贝到/software目录下

    cp /mnt/share/xampp-linux-x64-5.6.30-0-installer.run /software

执行如下命令，套件将被安装到/opt/lampp目录下

    chmod -x xampp-linux-x64-5.6.30-0-installer.run
    ./xampp-linux-x64-5.6.30-0-installer.run
    
# 二、配置集成环境

1.启动停止的命令分别为

    /opt/lampp/lampp start
    /opt/lampp/lampp stop

2.MySQL 安装好后，初始用户root，密码为空。应该通过命令行重置密码

    mysql -uroot -p   ----要求输入密码时，直接回车即可。
    > use mysql;
    > update user set password=PASSWORD('12345678') where user="root";    ---将root密码设置为12345678
    > flush privileges;
    > quit
    
3.用DbVisualizer远程连接可能出现如下错误，参考[解决方法](http://blog.csdn.net/langzi7758521/article/details/51729735)

    message from server: "Host xxx is not allowed to connect to this MySQL server
    
4.MySQL数据可能出现乱码，需要修改my.cnf文件中的配置。

5.Apache配置文件路径为

    httpd.conf位于：/opt/lampp/etc/httpd.conf
    httpd-ssl.conf位于：/opt/lampp/etc/extra/httpd-ssl.conf
    
主要对httpd-ssl.conf的VirtualHost进行配置，参考[HTTPS配置](http://zhanghao90.cn/Blog/web/https-configuration)

# 三、参考

1.[linux下xampp安装](http://jingyan.baidu.com/article/afd8f4de7976b034e286e90c.html)

2.[Mysql root用户密码重置](http://jingyan.baidu.com/article/63f236280a11680208ab3d91.html)
