---
layout: post
title: "Docker基础"
description: Docker基础
modified: 2019-09-07
category: Docker
tags: [Docker]
---

# 一、Docker基础

拉取并运行

    // 拉取镜像
    docker pull hub.c.163.com/library/hello-world:latest
    // 查看镜像
    docker images
    // 运行镜像
    docker run hub.c.163.com/library/hello-world

Nginx镜像

    docker pull hub.c.163.com/library/nginx
    // 后台运行Nginx
    docker run -d hub.c.163.com/library/nginx
    // 进入容器，其中xxxx代表run之后看到的容器id
    docker exec -it xxxx bash
    // 查看容器运行情况
    docker ps
    // 停止镜像
    docker stop xxxx
    // 指定Nginx本地端口为8080，容器中端口80
    docker run -d -p 8080:80 hub.c.163.com/library/nginx

# 二、制作镜像

编写Dockerfile

    from hub.c.163.com/library/tomcat:latest
    MAINTAINER zhanghao zhh900601@sina.com
    COPY horizon.war  /usr/local/tomcat/webapps

编译镜像

    docker build . -t zh-horizon

运行镜像

    docker run -d -p 8080:8080 zh-horizon

然后就可以在http://localhost:8080/horizon/，访问到应用了。

# 三、参考

1.[网易云镜像中心](https://c.163yun.com/hub#/m/home/)

2.[阿里云开源镜像站](https://opsx.alibaba.com/)

3.[菜鸟docker教程](https://www.runoob.com/docker/docker-tutorial.html)