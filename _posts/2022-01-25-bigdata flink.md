---
layout: post
title: "大数据系列 -- Flink"
description: 大数据系列 -- Flink
modified: 2022-01-25
category: BigData
tags: [BigData]
---

# 一、安装

    docker run -d --name=flink_single --privileged hive_proto /usr/sbin/init
    docker exec -it flink_single bash
    cd /
    wget "https://archive.apache.org/dist/flink/flink-1.14.3/flink-1.14.3-bin-scala_2.11.tgz"
    tar -zxf flink-1.14.3-bin-scala_2.11.tgz
    mv flink-1.14.3 /usr/local/flink
    chown -R hadoop /usr/local/flink

    # hadoop用户登录
    cd /usr/local/flink
    # 启动本地集群
    ./bin/start-cluster.sh
    # 停止本地集群
    $ ./bin/stop-cluster.sh

# 二、基础

1.quickstart项目

    mvn archetype:generate
    # 过滤flink-quickstart-java并交互式生成项目

# 三、参考

1.[Apache Flink文档](https://nightlies.apache.org/flink/flink-docs-release-1.14/zh/)