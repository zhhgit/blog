---
layout: post
title: "大数据系列 -- Hive"
description: 大数据系列 -- Hive
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

1.安装Hive

    docker run -d --name=hive_single --privileged hbase_proto /usr/sbin/init
    docker cp /Users/zhanghao/zhhroot/download/software/apache-hive-3.1.2-bin.tar.gz hive_single:/
    docker exec -it hive_single bash
    tar -zxf apache-hive-3.1.2-bin.tar.gz
    mv apache-hive-3.1.2-bin /usr/local/hive
    # 配置环境变量
    vim /etc/bashrc
    # 如下，重新进入容器生效
    export HADOOP_HOME=/usr/local/hadoop
    export HIVE_HOME=/usr/local/hive
    export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/hadoop/bin:/usr/local/hadoop/sbin:/usr/local/hive/bin:/usr/local/hbase/bin
    export CLASSPATH=$CLASSPATH:/usr/local/hadoop/lib/*:.
    export CLASSPATH=$CLASSPATH:/usr/local/hive/lib/*:.
    # 配置文件
    cd /usr/local/hive/conf
    mv hive-default.xml.template hive-default.xml
    # 新建一个配置文件hive-site.xml，配置Hive的Metastore，为在另一个容器172.17.0.3中启动的MySQL实例
    vim hive-site.xml
    
    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
      <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://172.17.0.3:3306/hive?useSSL=FALSE</value>
        <description>JDBC connect string for a JDBC metastore</description>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
        <description>Driver class name for a JDBC metastore</description>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
        <description>username to use against metastore database</description>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>123456</value>
        <description>password to use against metastore database</description>
      </property>
    </configuration>

    # 配置mysql jdbc包
    docker cp /Users/zhanghao/zhhroot/download/software/mysql-connector-java-8.0.26-bin.jar /usr/local/hive/lib

    chown -R hadoop /usr/local/hive
    su - hadoop
    
    # Metastore初始化
    cd /usr/local/hive
    # 可能出现错误，见特别注意
    ./bin/schematool -dbType mysql -initSchema
    # 配置HDFS文件夹权限
    hadoop fs -mkdir /tmp
    hadoop fs -mkdir /user/hive/warehouse
    hadoop fs -chmod g+w /tmp
    hadoop fs -chmod g+w /user/hive/warehouse
    # 运行Hive
    cd $HIVE_HOME
    ./bin/hive

    # 停止容器，并保存为镜像：
    docker stop hive_single
    docker commit hive_single hive_proto

2.配置MySQL Metastore

    docker pull hub.c.163.com/library/mysql:5.7
    docker run --name mysql_single -e MYSQL_ROOT_PASSWORD=123456 -d hub.c.163.com/library/mysql:5.7
    # 从172.17.0.2上用MySQL客户端连接MySQL服务
    yum install mysql
    mysql -h 172.17.0.3 -P 3306 -u root -p123456 --ssl-mode=DISABLED
    create database hive;
    # 将所有数据库的所有表的所有权限赋给root用户，后面的hive是配置hive-site.xml中配置的连接密码
    grant all privileges on *.* to 'root'@'%' identified by '123456';
    # 刷新mysql系统权限关系表
    flush privileges;

3.特别注意

    Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V
    at org.apache.hadoop.conf.Configuration.set(Configuration.java:1380)
    ...
    at org.apache.hadoop.util.RunJar.main(RunJar.java:236)

    原因：
    hadoop和hive的两个guava.jar版本不一致
    两个位置分别位于下面两个目录：
    /usr/local/hive/lib/
    /usr/local/hadoop/share/hadoop/common/lib/

    解决办法：
    删除低版本的那个，将高版本的复制到低版本目录下

# N、参考

1.[易百Hive教程](https://www.yiibai.com/hive)

2.[Hive3.1.2安装指南](http://dblab.xmu.edu.cn/blog/2440-2/)