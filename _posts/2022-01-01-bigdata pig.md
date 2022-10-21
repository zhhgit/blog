---
layout: post
title: "大数据系列 -- Pig"
description: 大数据系列 -- Pig
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

    docker run -d --name=pig_single --privileged hive_proto /usr/sbin/init
    docker exec -it pig_single bash

    # CentOS 8 过期了，要替换镜像源
    sed -e 's|^baseurl=https://mirrors.ustc.edu.cn/centos/$releasever|baseurl=https://mirrors.aliyun.com/centos-vault/8.4.2105|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-Linux-AppStream.repo \
         /etc/yum.repos.d/CentOS-Linux-BaseOS.repo \
         /etc/yum.repos.d/CentOS-Linux-Extras.repo \
         /etc/yum.repos.d/CentOS-Linux-PowerTools.repo \
         /etc/yum.repos.d/CentOS-Linux-Plus.repo
    yum makecache

    yum install -y wget

    cd /
    wget "http://archive.apache.org/dist/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz"
    tar -zxf apache-flume-1.9.0-bin.tar.gz
    mv apache-flume-1.9.0-bin /usr/local/flume
    chown -R hadoop /usr/local/flume

    echo "export FLUME_HOME=/usr/local/flume" >> /etc/bashrc
    echo "export PATH=$PATH:$FLUME_HOME/bin" >> /etc/bashrc
    
    cd /usr/local/flume/conf
    cp flume-env.sh.template ./flume-env.sh
    vim flume-env.sh

    # 配置JAVA_HOME
    export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-2.el8_5.x86_64

# 二、基础

1.什么是Apache Pig？

Apache Pig是MapReduce的一个抽象。它是一个工具/平台，用于分析较大的数据集，并将它们表示为数据流。Pig通常与Hadoop一起使用；我们可以使用Apache Pig在Hadoop中执行所有的数据处理操作。
要编写数据分析程序，Pig提供了一种称为Pig Latin的高级语言。该语言提供了各种操作符，程序员可以利用它们开发自己的用于读取，写入和处理数据的功能。 所有这些脚本都在内部转换为Map和Reduce任务。
Apache Pig有一个名为Pig Engine的组件，它接受Pig Latin脚本作为输入，并将这些脚本转换为MapReduce作业。
Apache Pig历史：在2006年时，Apache Pig是作为Yahoo的研究项目开发的，特别是在每个数据集上创建和执行MapReduce作业。在2007时，Apache Pig是通过Apache孵化器开源的。在2008时，Apache Pig的第一个版本出来了。在2010时，Apache Pig获得为Apache顶级项目。

# 三、参考

1.[]()
