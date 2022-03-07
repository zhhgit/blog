---
layout: post
title: "大数据系列 -- HBase"
description: 大数据系列 -- HBase
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

    docker cp /Users/zhanghao/zhhroot/download/software/hbase-2.2.7-bin.tar.gz hbase_single:/
    docker exec -it hbase_single su hadoop
    # root用户安装然后chown
    tar -zxf hbase-2.2.7-bin.tar.gz
    mv hbase-2.2.7 /usr/local/hbase
    # 修改hbase-env.sh
    cd /usr/local/hbase/conf
    vim hbase-env.sh
    # 配置JAVA_HOME
    export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.302.b08-0.el8_4.x86_64
    # 修改hbase-site.xml，修改配置
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <name>hbase.tmp.dir</name>
        <value>./tmp</value>
    </property>
    <property>
        <name>hbase.unsafe.stream.capability.enforce</name>
        <value>false</value>
    </property>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://172.17.0.2:9000/hbase</value>
    </property>

    chown -R hadoop /usr/local/hbase
    # 启动hbase
    cd /usr/local/hbase/bin
    sh start-hbase.sh
    # 检查在HDFS的HBase目录
    hadoop fs -ls /hbase
    # 启动HBase shell
    cd /usr/local/hbase
    ./bin/hbase shell
    # 停止容器，并保存为一个名为hbase_proto的镜像：
    docker stop hbase_single
    docker commit hbase_single hbase_proto

# N、参考

1.[我终于看懂了HBase，太不容易了](https://zhuanlan.zhihu.com/p/145551967)

2.[易百HBase教程](https://www.yiibai.com/hbase)

