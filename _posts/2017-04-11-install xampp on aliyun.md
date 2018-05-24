---
layout: post
title: "阿里云XAMPP开发环境安装配置"
description: 阿里云XAMPP开发环境安装配置
modified: 2017-04-11
category: Linux
tags: [Linux]
---

# 一、安装XAMPP

下载64位的包xampp-linux-x64-5.6.30-0-installer.run，通过FileZilla将文件上传到阿里云的机器上。

执行如下命令，套件将被安装到/opt/lampp目录下

    chmod 777 xampp-linux-x64-5.6.30-0-installer.run
    ./xampp-linux-x64-5.6.30-0-installer.run
    
# 二、配置集成环境

1.启动停止的命令分别为

    /opt/lampp/lampp start
    /opt/lampp/lampp stop

2.启动mysql客户端应该进入/opt/lampp/bin目录执行以下语句，初始用户root，密码为空

    ./mysql -uroot -p

可能会出现root用户没有mysql这个数据库访问权限的情况，应该参考[这里](https://blog.csdn.net/jt_121217/article/details/78247265)删除空用户数据。

先停止MySQL服务，用mysqld命令启动MySQL服务端，在另一个窗口中无需密码地启动MySQL客户端，此时可以看到并操作mysql这个数据库中的user表。

    -------------------------------停止服务
    /opt/lampp/lampp stop
    -------------------------------启动服务端
    cd /opt/lampp/sbin
    ./mysqld --skip-grant-table
    -------------------------------启动客户端
    cd /opt/lampp/bin
    ./mysql
    -------------------------------删除空用户
    delete from user where user='';
    flush privileges;

之后再启动MySQL客户端

    mysql -uroot -p
    use mysql;
    -------------------------------将root密码设置为123456
    update user set password=password('123456') where user="root";
    flush privileges;
    quit
    
3.用DbVisualizer远程连接MySQL可能出现如下问题，参考[解决方法](http://blog.csdn.net/langzi7758521/article/details/51729735)

    message from server: "Host xxx is not allowed to connect to this MySQL server

也可能会提示通信失败，参考[阿里云MySQL远程连接不上问题](https://www.cnblogs.com/funnyboy0128/p/7966531.html)，主要是添加阿里云的安全组。
    
4.MySQL数据可能出现乱码，需要修改/opt/lampp/etc/my.cnf文件中的配置。

5.Apache配置文件路径为

    httpd.conf位于：/opt/lampp/etc/httpd.conf
    httpd-ssl.conf位于：/opt/lampp/etc/extra/httpd-ssl.conf
    
主要对httpd-ssl.conf的VirtualHost进行配置，参考[HTTPS配置](http://zhanghao90.cn/blog/web/https-configuration)

# 三、参考

1.[linux下xampp安装](http://jingyan.baidu.com/article/afd8f4de7976b034e286e90c.html)

2.[Mysql root用户密码重置](http://jingyan.baidu.com/article/63f236280a11680208ab3d91.html)
