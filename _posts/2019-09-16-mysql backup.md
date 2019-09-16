---
layout: post
title: "MySQL备份"
description: MySQL备份
modified: 2019-09-16
category: Database
tags: [Database]
---

# 一、MySQL备份

备份单表结构和数据

    mysqldump -h 127.0.0.1 -P 9000 -u username database_name table_name -p > ./table_name_new.sql

备份单表结构

    mysqldump -h 127.0.0.1 -P 9000 -u username -d database_name table_name -p > ./table_name_new.sql

导入到新库中

    create database database_name_new;
    use database_name_new;
    source ./table_name_new.sql

备份指定库结构和数据

    mysqldump -h 127.0.0.1 -P 9000 -u username --databases database_name -p > ./table_name_new.sql

# 三、参考

1.[备份或导出数据库命令mysqldump怎么使用](https://jingyan.baidu.com/article/b2c186c804be48c46ff6ff6a.html)