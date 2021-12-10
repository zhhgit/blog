---
layout: post
title: "Hadoop, HBase, Hive安装"
description: Hadoop, HBase, Hive安装
modified: 2021-09-24
category: BigData
tags: [BigData]
---

# Hadoop

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
    在core-site.xml中在标签下添加属性
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://172.17.0.2:9000</value>
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

4.参考：

(1)[菜鸟Hadoop教程](https://www.runoob.com/w3cnote/hadoop-tutorial.html)

(2)[Hadoop教程](https://www.w3cschool.cn/hadoop/)

(3)[Hadoop FileSystem Shell](https://hadoop.apache.org/docs/r3.1.4/hadoop-project-dist/hadoop-common/FileSystemShell.html)

# HBase

1.安装HBase

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

2.参考

(1)[我终于看懂了HBase，太不容易了](https://zhuanlan.zhihu.com/p/145551967)

(2)[易百HBase教程](https://www.yiibai.com/hbase)

# Hive

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

4.参考

(1)[易百Hive教程](https://www.yiibai.com/hive)

(2)[Hive3.1.2安装指南](http://dblab.xmu.edu.cn/blog/2440-2/)