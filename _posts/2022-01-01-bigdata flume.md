---
layout: post
title: "大数据系列 -- Flume"
description: 大数据系列 -- Flume
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

    docker run -d --name=flume_single --privileged hive_proto /usr/sbin/init
    docker exec -it flume_single bash

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

1.什么是Flume

Apache Flume是一种工具/服务/数据提取机制，用于收集聚合并将大量流数据（例如日志文件，事件（等等））从各种源传输到集中式数据存储。
Flume是一种高度可靠，分布式和可配置的工具。它主要用于将流数据（日志数据）从各种Web服务器复制到HDFS。

2.Flume的优点

    使用Apache Flume，我们可以将数据存储到任何集中存储（HBase，HDFS）中。
    当传入数据的速率超过可以将数据写入目标的速率时，Flume充当数据生成器和集中存储之间的中介，并在它们之间提供稳定的数据流。
    Flume提供了上下文路由的功能。
    Flume中的交易是基于渠道的，其中为每条消息维护两个交易（一个发送者和一个接收者）。它保证了可靠的消息传递。
    Flume可靠，容错，可扩展，易于管理和可定制。

3.概念

    Event：一个事件是内部传送的数据的基本单元。它包含一个字节数组的有效负载，它将从源传输到目标，并附带可选的头。
    Agent：一个代理是一个独立的守护进程（JVM）。它从客户端或其他代理接收数据（事件），并将其转发到下一个目标（接收器或代理）。Agent包含三个主要组件，即源， 通道和接收器。
    Source：源是从数据生成器，并将其传送接收数据到一个或多个通道在Flume事件的形式的代理的组件。Flume支持多种类型的源，每个源从指定的数据生成器接收事件。
    Channel：通道是从所述源接收的事件和缓冲它们直到它们由汇消耗的瞬时存储。它充当源和接收器之间的桥梁。例如JDBC通道，文件系统通道，内存通道等。
    Sink：接收器将数据存储到HBase和HDFS等集中存储中。它消耗来自通道的数据（事件）并将其传送到目的地。接收器的目的地可能是另一个代理或中央存储。

4.配置

在Flume配置文件中，我们需要如下配置。通常我们可以在Flume中拥有多个代理。我们可以使用唯一名称区分每个代理。使用此名称，我们必须配置每个代理。

    命名当前代理的组件。
    描述/配置源。
    描述/配置接收器。
    描述/配置频道。
    将源和接收器绑定到通道。

5.命令
    
    # 查看flume版本
    flume-ng version

    # 启动
    # --conf，-c 在conf目录中使用配置文件；-f  指定配置文件路径；--name，-n 代理的名称；-D property = value 设置Java系统属性值。
    ./bin/flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/conf/netcat.conf --name NetcatAgent -Dflume.root.logger=INFO,console

5.示例

生成事件并随后将它们记录到控制台中。为此我们使用NetCat源和记录器接收器。

    # Naming the components on the current agent
    NetcatAgent.sources = Netcat
    NetcatAgent.channels = MemChannel
    NetcatAgent.sinks = LoggerSink
    
    # Describing/Configuring the source
    NetcatAgent.sources.Netcat.type = netcat
    NetcatAgent.sources.Netcat.bind = localhost
    NetcatAgent.sources.Netcat.port = 56565
    
    # Describing/Configuring the sink
    NetcatAgent.sinks.LoggerSink.type = logger
    
    # Describing/Configuring the channel
    NetcatAgent.channels.MemChannel.type = memory
    NetcatAgent.channels.MemChannel.capacity = 1000
    NetcatAgent.channels.MemChannel.transactionCapacity = 100
    
    # Bind the source and sink to the channel
    NetcatAgent.sources.Netcat.channels = MemChannel
    NetcatAgent.sinks.LoggerSink.channel = MemChannel

启动agent

    ./bin/flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/conf/netcat.conf --name NetcatAgent -Dflume.root.logger=INFO,console

打开一个单独的终端并使用curl命令连接到源（56565）。连接成功后，您将收到“已连接”消息。现在可以逐行输入数据（在每行之后，必须按Enter键）。NetCat源将每条线路作为单独的事件接收，将收到“OK”的消息。

    $ curl telnet://localhost:56565
    connected

# 三、参考

1.[Flume 1.9.0的安装](https://www.cnblogs.com/ronnieyuan/p/12011731.html)

2.[codingdict Apache Flume教程](https://codingdict.com/article/9414)

3.[Flume入门详解](http://www.hainiubl.com/topics/12)
