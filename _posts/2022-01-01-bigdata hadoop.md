---
layout: post
title: "大数据系列 -- Hadoop"
description: 大数据系列 -- Hadoop
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

1.基本环境配置

    # 安装CentOS 8并运行容器
    docker pull centos:8
    docker run -d --name=java_ssh_proto --privileged centos:8 /usr/sbin/init
    docker exec -it java_ssh_proto bash
    # 配置镜像
    sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/$contentdir|baseurl=https://mirrors.ustc.edu.cn/centos|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-Linux-AppStream.repo \
         /etc/yum.repos.d/CentOS-Linux-BaseOS.repo \
         /etc/yum.repos.d/CentOS-Linux-Extras.repo \
         /etc/yum.repos.d/CentOS-Linux-PowerTools.repo \
         /etc/yum.repos.d/CentOS-Linux-Plus.repo
    yum makecache
    # 安装OpenJDK8和SSH服务，然后启用SSH服务
    yum install -y java-1.8.0-openjdk-devel openssh-clients openssh-server
    systemctl enable sshd && systemctl start sshd
    # 停止容器，并保存为一个名为java_ssh的镜像：
    docker stop java_ssh_proto
    docker commit java_ssh_proto java_ssh

2.安装Hadoop

    # 创建容器，解压
    docker run -d --name=hadoop_single --privileged java_ssh /usr/sbin/init
    docker cp /Users/zhanghao/zhhroot/download/software/hadoop-3.1.4.tar.gz hadoop_single:/
    docker exec -it hadoop_single bash
    tar -zxf hadoop-3.1.4.tar.gz
    mv hadoop-3.1.4 /usr/local/hadoop
    # 配置环境变量，退出docker容器并重新进入才生效！！
    echo "export HADOOP_HOME=/usr/local/hadoop" >> /etc/bashrc
    echo "export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin" >> /etc/bashrc
    # 配置hadoop-env.sh
    echo "export JAVA_HOME=/usr" >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
    echo "export HADOOP_HOME=/usr/local/hadoop" >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
    # 查看是否成功
    hadoop version
    # 新建hadoop用户，安装一个小工具用于修改用户密码和权限管理，设置hadoop用户密码，修改hadoop安装目录所有人为hadoop用户，
    adduser hadoop
    yum install -y passwd sudo
    passwd hadoop
    chown -R hadoop /usr/local/hadoop
    # 然后用文本编辑器修改/etc/sudoers文件，在root ALL=(ALL) ALL之后添加一行
    hadoop  ALL=(ALL)       ALL
    # 关闭并提交容器hadoop_single到镜像hadoop_proto
    docker stop hadoop_single
    docker commit hadoop_single hadoop_proto
    # 创建新容器 hdfs_single ：
    docker run -d --name=hdfs_single --privileged hadoop_proto /usr/sbin/init
    # 现在进入刚建立的容器
    docker exec -it hdfs_single su hadoop
    # 生成SSH密钥，这里可以一直按回车直到生成结束，然后将生成的密钥添加到信任列表
    ssh-keygen -t rsa
    ssh-copy-id hadoop@172.17.0.2
    # 其中查看容器IP地址
    ip addr | grep 172
    # 在启动HDFS以前我们对其进行一些简单配置，
    cd $HADOOP_HOME/etc/hadoop
    # 修改两个文件：core-site.xml，hdfs-site.xml

    cd /usr/local/hadoop
    mkdir hadoop_tmp
    # 在core-site.xml中在标签下添加属性
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://172.17.0.2:9000</value>
    </property>
    # 解决namenode无法启动问题，添加如下
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/usr/local/hadoop/hadoop_tmp</value>
    </property>

    # 在hdfs-site.xml中的标签下添加属性
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    # 格式化文件结构,然后启动HDFS，启动分三个步骤，分别启动NameNode、DataNode和Secondary NameNode。
    hdfs namenode -format
    start-dfs.sh
    # 我们可以运行jps来查看Java进程
    jps

3.HDFS Shell

    # 显示根目录 / 下的文件和子目录，绝对路径
    hadoop fs -ls /
    # 新建文件夹，绝对路径
    hadoop fs -mkdir /hello
    # 上传文件
    hadoop fs -put hello.txt /hello/
    # 下载文件
    hadoop fs -get /hello/hello.txt
    # 输出文件内容
    hadoop fs -cat /hello/hello.txt

# N、参考

1.[菜鸟Hadoop教程](https://www.runoob.com/w3cnote/hadoop-tutorial.html)

2.[Hadoop教程](https://www.w3cschool.cn/hadoop/)

3.[Hadoop FileSystem Shell](https://hadoop.apache.org/docs/r3.1.4/hadoop-project-dist/hadoop-common/FileSystemShell.html)

4.[执行start-dfs.sh，namenode无法启动](https://www.cnblogs.com/jichui/p/7777832.html)