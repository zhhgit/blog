---
layout: post
title: "Docker基础"
description: Docker基础
modified: 2021-02-03
category: Docker
tags: [Docker]
---

# 一、Docker基础

镜像相关命令：

    // 拉取镜像
    docker pull hub.c.163.com/library/hello-world:latest
    docker pull hub.c.163.com/library/nginx
    // 查看镜像
    docker images
    // 运行镜像
    docker run hub.c.163.com/library/hello-world
    // 后台运行Nginx
    docker run -d hub.c.163.com/library/nginx
    // 指定Nginx本地端口为8080，容器中端口80
    docker run -d -p 8080:80 hub.c.163.com/library/nginx
    // 删除镜像
    docker rmi xxxx(镜像ID)
    // 搜索镜像
    docker search httpd
    // 为镜像添加一个新的标签。
    docker tag xxxx(镜像ID) test/ubuntu:v2

容器相关命令：
    
    // 进入容器，其中xxxx代表run之后看到的容器id
    docker exec -it xxxx bash
    // 查看容器运行情况
    docker ps -a
    // 停止容器
    docker stop xxxx
    // 查看容器内的标准输出
    docker logs xxxx
    // 删除容器
    docker rm xxxx
    // 启动一个已停止的容器
    docker start xxxx
    // 重启容器
    docker restart xxxx
    // 导出容器快照到本地文件
    docker export xxxx > ubuntu.tar
    // 将快照文件ubuntu.tar导入到镜像test/ubuntu:v1:
    cat ubuntu.tar | docker import - test/ubuntu:v1
    // 清理掉所有处于终止状态的容器。
    docker container prune
    //查看容器端口的映射情况。
    docker port xxxx
    // 查看容器内部运行的进程
    docker top xxxx
    // 来查看容器的底层信息
    docker inspect xxxx

容器状态有7种：

    created（已创建）
    restarting（重启中）
    running 或 Up（运行中）
    removing（迁移中）
    paused（暂停）
    exited（停止）
    dead（死亡）

# 二、制作镜像

方法1：编写Dockerfile

    // 编写Dockerfile
    from hub.c.163.com/library/tomcat:latest
    MAINTAINER zhanghao zhh900601@sina.com
    COPY horizon.war  /usr/local/tomcat/webapps
    // 编译镜像
    docker build . -t zh-horizon
    // 运行镜像，然后就可以在http://localhost:8080/horizon/，访问到应用了。
    docker run -d -p 8080:8080 zh-horizon
    //推送本地镜像到阿里云镜像仓库
    docker login --username=xxx registry.cn-shanghai.aliyuncs.com
    docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/zhhaliyun/horizonproj:[镜像版本号]
    docker push registry.cn-shanghai.aliyuncs.com/zhhaliyun/horizonproj:[镜像版本号]

方法2：从已创建容器更新镜像

    docker commit -m="has update" -a="zhanghao" xxxx(容器ID) test/ubuntu:v2

# 三、参考

1.[网易云镜像中心](https://c.163yun.com/hub#/m/home/)

2.[阿里云开源镜像站](https://opsx.alibaba.com/)

3.[菜鸟docker教程](https://www.runoob.com/docker/docker-tutorial.html)

4.[阿里云容器镜像服务](https://help.aliyun.com/document_detail/60743.html)