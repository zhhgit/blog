---
layout: post
title: "大数据系列 -- Clickhouse"
description: 大数据系列 -- Clickhouse
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

    docker pull yandex/clickhouse-server
    docker run -d --name=single-clickhouse-server -p 8123:8123 -p 9000:9000 -p 9009:9009 --ulimit nofile=262144:262144 yandex/clickhouse-server

    docker exec -it single-clickhouse-server /bin/bash
    // 生成SHA256秘钥
    PASSWORD=$(base64 < /dev/urandom | head -c8); echo "123456"; echo -n "123456" | sha256sum | tr -d '-'

    cd /etc/clickhouse-server 
    vim config.xml
    // 添加如下
    <listen_host>127.0.0.1</listen_host>

    vim users.xml
    // 添加如下
    <zhanghao>
        <password_sha256_hex>8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92</password_sha256_hex>
        <networks incl="networks" replace="replace">
           <ip>::/0</ip>
        </networks>
        <profile>default</profile>
        <quota>default</quota>
    </zhanghao>

    docker restart single-clickhouse-server
    
    // 然后就可以使用DBeaver连接，用户zhanghao，密码123456
    
# N、参考

1.[window下安装clickhouse](https://www.cnblogs.com/huanghanyu/p/15322960.html)

2.[clickhouse修改用户密码](https://segmentfault.com/a/1190000021512811)

3.[clickhouse官方文档](https://clickhouse.com/docs/zh/)