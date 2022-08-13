---
layout: post
title: "大数据系列 -- Sqoop"
description: 大数据系列 -- Sqoop
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

    docker run -d --name=sqoop_single --privileged hive_proto /usr/sbin/init
    docker exec -it sqoop_single bash

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
    wget "http://archive.apache.org/dist/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz"
    tar -zxf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
    mv sqoop-1.4.7.bin__hadoop-2.6.0 /usr/local/sqoop
    chown -R hadoop /usr/local/sqoop

    echo "export SQOOP_HOME=/usr/local/sqoop" >> /etc/bashrc
    echo "export PATH=$PATH:$SQOOP_HOME/bin" >> /etc/bashrc
    
    cd /usr/local/sqoop/conf
    cp sqoop-env-template.sh ./sqoop-env.sh
    vim sqoop-env.sh

    #Set path to where bin/hadoop is available
    export HADOOP_COMMON_HOME=/usr/local/hadoop
    #Set path to where hadoop-*-core.jar is available
    export HADOOP_MAPRED_HOME=/usr/local/hadoop
    #set the path to where bin/hbase is available
    export HBASE_HOME=/usr/local/hbase
    #Set the path to where bin/hive is available
    export HIVE_HOME=/usr/local/hive

    cd /
    cp mysql-connector-java-8.0.26.jar /usr/local/sqoop/lib

# 二、基础

1.什么是Sqoop

Apache Sqoop是在Hadoop生态体系和RDBMS体系之间传送数据的一种工具。来自于Apache软件基金会提供。
Sqoop工作机制是将导入或导出命令翻译成mapreduce程序来实现。在翻译出的mapreduce中主要是对inputformat和outputformat进行定制。
Hadoop生态系统包括：HDFS、Hive、Hbase等
RDBMS体系包括：Mysql、Oracle、DB2等
Sqoop可以理解为：“SQL到Hadoop和Hadoop到SQL”。
站在Apache立场看待数据流转问题，可以分为数据的导入导出:
Import：数据导入。RDBMS----->Hadoop
Export：数据导出。Hadoop---->RDBMS

2.验证安装成功

    # 查看帮助
    cd /usr/local/sqoop/bin
    sqoop help

    # 连接mysql并显示所有DB
    cd /usr/local/sqoop
    bin/sqoop list-databases \
    --connect jdbc:mysql://172.17.0.3:3306/spark?useSSL=false \
    --username root \
    --password 123456

    bin/sqoop list-tables \
    --connect jdbc:mysql://172.17.0.3:3306/spark?useSSL=false \
    --username root \
    --password 123456

3.导入

“导入工具”导入单个表从RDBMS到HDFS。表中的每一行被视为HDFS的记录。所有记录都存储为文本文件的文本数据或者在阿夫罗(Avro )和序列文件的二进制数据。

    $ sqoop import (generic-args) (import-args) 
    $ sqoop-import (generic-args) (import-args)

导入MySQL中表到HDFS文件：

    # 执行导入
    # -m是maptask的数量设置， --bindir是为了生成的类放入lib文件夹
    cd /usr/local/sqoop/
    # 执行第二次才能成功，第一次ClassNotFoundException
    bin/sqoop import \
    --connect jdbc:mysql://172.17.0.3:3306/spark?useSSL=false \
    --username root \
    --password 123456 \
    --table student \
    --bindir /usr/local/sqoop/lib \
    --target-dir /sqoopdata \
    --delete-target-dir \
    --num-mappers 1

    # 查看导入到HDFS的数据
    hadoop fs -ls /sqoopdata
    hadoop fs -cat /sqoopdata/part-m-00000

4.导出

默认的操作是从输入文件到数据库表，使用INSERT语句插入所有记录。在更新模式，Sqoop生成替换现有记录到数据库的UPDATE语句。

    $ sqoop export (generic-args) (export-args)
    $ sqoop-export (generic-args) (export-args)

导入到MySQL：

    # 新建一个表
    show create table student \G

    CREATE TABLE `student_new` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `name` char(20) DEFAULT NULL,
    `gender` char(4) DEFAULT NULL,
    `age` int(4) DEFAULT NULL,
    PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=latin1

    # 导出，执行第二次才能成功，第一次ClassNotFoundException
    bin/sqoop export \
    --connect jdbc:mysql://172.17.0.3:3306/spark?useSSL=false \
    --username root \
    --password 123456 \
    --table student_new \
    --export-dir /sqoopdata/part-m-00000 \
    --bindir /usr/local/sqoop/lib

5.作业

Sqoop作业创建并保存导入和导出命令。它指定参数来识别并调用已保存的工作。这种重新调用或重新执行用在增量导入，可以从RDBMS表到HDFS导入更新的行。语法：

    $ sqoop job (generic-args) (job-args) [-- [subtool-name] (subtool-args)]
    $ sqoop-job (generic-args) (job-args) [-- [subtool-name] (subtool-args)]

创建并使用导入Job：

    # 创建，一定注意import之前的空格！！
    cd /usr/local/sqoop
    bin/sqoop job --create myjob \
    -- import \
    --connect jdbc:mysql://172.17.0.3:3306/spark?useSSL=false \
    --username root \
    --password 123456 \
    --table student \
    --bindir /usr/local/sqoop/lib \
    --target-dir /sqoopdata \
    --delete-target-dir \
    --num-mappers 1

    # 查看作业列表
    bin/sqoop job --list

    # 执行作业，会要求输入MySQL密码！！
    bin/sqoop job --exec myjob

# N、参考

1.[易百Sqoop教程](https://www.yiibai.com/sqoop)

2.[Apache Sqoop官网](https://sqoop.apache.org/)

3.[阿里云镜像站CentOS 8](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.73db1b11siwlNO)

4.[sqoop--基本使用](https://blog.51cto.com/kinglab/2447711)

5.[sqoop从hive导入数据到Mysql报错ClassNotFoundException:class tablename not found](https://blog.csdn.net/mbest6/article/details/105606166/)

6.[Sqoop 启动任务时报错 java.lang.ClassNotFoundException: Class 表名 not found](https://blog.csdn.net/weixin_42067918/article/details/120814065)

7.[Sqoop简介（1.4.7 最新版本）](https://blog.csdn.net/xiaohu21/article/details/109137364)，其中的架构图很好！！

8.[sqoop执行job报错（org/json/JSONObject）](https://www.cnblogs.com/byfboke/p/10000578.html)
