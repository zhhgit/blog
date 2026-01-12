---
layout: post
title: "面试题 -- 大数据篇"
description: 面试题 -- 大数据篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# 基础知识

1.大数据处理流程

(1)数据收集

大数据处理的第一步是数据的收集。现在的中大型项目通常采用微服务架构进行分布式部署，所以数据的采集需要在多台服务器上进行，且采集过程不能影响正常业务的开展。基于这种需求，就衍生了多种日志收集工具，如 Flume 、Logstash、Kibana 等，它们都能通过简单的配置完成复杂的数据收集和数据聚合。

(2)数据存储

收集到数据后，下一个问题就是：数据该如何进行存储？通常大家最为熟知是 MySQL、Oracle 等传统的关系型数据库，它们的优点是能够快速存储结构化的数据，并支持随机访问。但大数据的数据结构通常是半结构化（如日志数据）、甚至是非结构化的（如视频、音频数据），为了解决海量半结构化和非结构化数据的存储，衍生了 Hadoop HDFS 、KFS、GFS 等分布式文件系统，它们都能够支持结构化、半结构和非结构化数据的存储，并可以通过增加机器进行横向扩展。

分布式文件系统完美地解决了海量数据存储的问题，但是一个优秀的数据存储系统需要同时考虑数据存储和访问两方面的问题，比如你希望能够对数据进行随机访问，这是传统的关系型数据库所擅长的，但却不是分布式文件系统所擅长的，那么有没有一种存储方案能够同时兼具分布式文件系统和关系型数据库的优点，基于这种需求，就产生了 HBase、MongoDB。

(3)数据分析

大数据处理最重要的环节就是数据分析，数据分析通常分为两种：批处理和流处理。

+ **批处理**：对一段时间内海量的离线数据进行统一的处理，对应的处理框架有 Hadoop MapReduce、Spark、Flink 等；
+ **流处理**：对运动中的数据进行处理，即在接收数据的同时就对其进行处理，对应的处理框架有 Storm、Spark Streaming、Flink Streaming 等。

批处理和流处理各有其适用的场景，时间不敏感或者硬件资源有限，可以采用批处理；时间敏感和及时性要求高就可以采用流处理。随着服务器硬件的价格越来越低和大家对及时性的要求越来越高，流处理越来越普遍，如股票价格预测和电商运营数据分析等。

上面的框架都是需要通过编程来进行数据分析，那么如果你不是一个后台工程师，是不是就不能进行数据的分析了？当然不是，大数据是一个非常完善的生态圈，有需求就有解决方案。为了能够让熟悉 SQL 的人员也能够进行数据的分析，查询分析框架应运而生，常用的有 Hive 、Spark SQL 、Flink SQL、 Pig、Phoenix 等。这些框架都能够使用标准的 SQL 或者 类 SQL 语法灵活地进行数据的查询分析。这些 SQL 经过解析优化后转换为对应的作业程序来运行，如 Hive 本质上就是将 SQL 转换为 MapReduce 作业，Spark SQL 将 SQL 转换为一系列的 RDDs 和转换关系（transformations），Phoenix 将 SQL 查询转换为一个或多个 HBase Scan。

(4)数据应用

数据分析完成后，接下来就是数据应用的范畴，这取决于你实际的业务需求。比如你可以将数据进行可视化展现，或者将数据用于优化你的推荐算法，这种运用现在很普遍，比如短视频个性化推荐、电商商品推荐、头条新闻推荐等。当然你也可以将数据用于训练你的机器学习模型，这些都属于其他领域的范畴，都有着对应的框架和技术栈进行处理，这里就不一一赘述。

(5)其他框架

上面是一个标准的大数据处理流程所用到的技术框架。但是实际的大数据处理流程比上面复杂很多，针对大数据处理中的各种复杂问题分别衍生了各类框架：

+ 单机的处理能力都是存在瓶颈的，所以大数据框架都是采用集群模式进行部署，为了更方便的进行集群的部署、监控和管理，衍生了 Ambari、Cloudera Manager 等集群管理工具；
+ 想要保证集群高可用，需要用到 ZooKeeper ，ZooKeeper 是最常用的分布式协调服务，它能够解决大多数集群问题，包括首领选举、失败恢复、元数据存储及其一致性保证。同时针对集群资源管理的需求，又衍生了 Hadoop YARN ;
+ 复杂大数据处理的另外一个显著的问题是，如何调度多个复杂的并且彼此之间存在依赖关系的作业？基于这种需求，产生了 Azkaban 和 Oozie 等工作流调度框架；
+ 大数据流处理中使用的比较多的另外一个框架是 Kafka，它可以用于消峰，避免在秒杀等场景下并发数据对流处理程序造成冲击；
+ 另一个常用的框架是 Sqoop ，主要是解决了数据迁移的问题，它能够通过简单的命令将关系型数据库中的数据导入到 HDFS 、Hive 或 HBase 中，或者从 HDFS 、Hive 导出到关系型数据库上。

2.常用框架

**日志收集框架**：Flume、Logstash、Filebeat

**分布式文件存储系统**：Hadoop HDFS

**数据库系统**：Mongodb、HBase

**分布式计算框架**：

+ 批处理框架：Hadoop MapReduce
+ 流处理框架：Storm
+ 混合处理框架：Spark、Flink

**查询分析框架**：Hive 、Spark SQL 、Flink SQL、 Pig、Phoenix

**集群资源管理器**：Hadoop YARN

**分布式协调服务**：Zookeeper

**数据迁移工具**：Sqoop

**任务调度框架**：Azkaban、Oozie

**集群部署和监控**：Ambari、Cloudera Manager

3.离线数仓5层结构

(1)STG（Staging）
作用：临时缓冲，把源库增量数据“原样”落地，不做任何清洗。
文件格式：TextFile 或 LZO，按 “库_表/yyyy-MM-dd-HH” 分区。
生命周期：下游 ODS 成功就删，最多保留 48 h。
注意：STG 不算正式数仓层，但如果不设，直接抽进 ODS 会把“抽数失败重跑”和“数据质量”耦合在一起，排障很痛苦。

(2)ODS（Operational Data Store）
作用：原始镜像层，保留完整业务系统快照，支持重放。
建模：完全贴源，字段名、类型、主键保持一致；加 4 个固定字段
etl_date、etl_src、etl_op（I/U/D）、md5_src_row。
文件格式：ORC + SNAPPY，按天分区。
常见坑：
源库 delete 后，ODS 要软删（etl_op='D'），否则下游重跑会丢数。
大表（>1 亿行）首次全量用 “sqoop-split-by 主键 + 并行 4 mappers” 抽，后续每天增量 where 更新时间 ≥ yesterday。

(3)DWD（Data Warehouse Detail）
作用：明细+维度统一、清洗、标准化，形成“唯一原子事实”。
建模：
事实表：采用“星型”或“Data Vault”，只保留最小粒度事务；命名 dwd_业务_过程_di。
维度表：退化常用维度（user_status、product_category）到事实表，减少 join；缓慢变化维用 SCD2（加 start_date/end_date/is_current）。
质量门：空值率、主键唯一性、枚举值域、时间连续性 4 项必须 100 % 通过，否则下游任务自动熔断。
性能：dwd_order_di 50 亿行场景，用 ORC + ZLIB + 桶排序（cluster by order_id），查询命中 1 % 数据量可 30 s 内返回。

(4)DWS（Data Warehouse Summary）
作用：公共汇总，把“相同统计粒度+相同维度”提前算好，消除重复汇总。
建模：
按“日、周、月、年”4 张表分别建，命名 dws_业务_主题_统计周期。
维度只保留退化键（user_id, product_id, city_id…），把 name 类长字符砍掉，减少 30 % 体积。
指标必须“可累加”或“半累加”，不允许存“比率”这类不可累加指标。
落地：
日表：Hive ORC；
周/月表：如果>10 亿行，用 ClickHouse 物化视图，查询从 3 min 降到 8 s。
常见坑：
把“下单”与“支付”两个业务过程混在一张宽表，导致后续财务、运营各要各的口径，互相污染。正确做法：一个业务过程一张宽表，下游 ADS 再 join。

(5)ADS（Application Data Service）
作用：面向应用、面向报表，直接给 BI、API、Excel 用。
建模：完全“需求驱动”，可以有大宽表、也可以有比率指标；命名 ads_应用_场景_描述。
技术选型：
并发 < 100、数据 < 1 亿 → MySQL 单表 + 索引。
并发 100–1000、数据 1–10 亿 → ClickHouse MergeTree。
并发 > 1000、点查 < 50 ms → Redis/StarRocks 主键表。
生命周期：需求下线 7 天内自动 drop，防止“僵尸表”膨胀。

总结：
离线：
STG(48h) → ODS(全量+增量镜像) → DWD(清洗+标准化) → DWS(公共汇总) → ADS(应用/报表)
实时：
Source(Kafka) → ODS_RT(原始) → DWD_RT(明细+维表join) → DWS_RT(分钟级汇总) → ADS_RT(API/大屏)
维度总线：dim_xxx 表每天离线更新 → 同步到 Redis/HBase → 实时 join 用。

# Hadoop

1.基本环境配置

    # 安装CentOS 8并运行容器
    docker pull centos:8
    docker run -d --name=java_ssh_proto --privileged centos:8 /usr/sbin/init
    docker exec -it java_ssh_proto bash
    # 配置镜像
    sed -e "s|^mirrorlist=|#mirrorlist=|g" \
        -e "s|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/8.4.2105|g" \
        -e "s|^#baseurl=http://mirror.centos.org/\$contentdir/\$releasever|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/8.4.2105|g" \
        -i.bak \
        /etc/yum.repos.d/CentOS-*.repo
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

4.参考

(1)[菜鸟Hadoop教程](https://www.runoob.com/w3cnote/hadoop-tutorial.html)

(2)[Hadoop教程](https://www.w3cschool.cn/hadoop/)

(3)[Hadoop FileSystem Shell](https://hadoop.apache.org/docs/r3.1.4/hadoop-project-dist/hadoop-common/FileSystemShell.html)

(4)[执行start-dfs.sh，namenode无法启动](https://www.cnblogs.com/jichui/p/7777832.html)

(5)[CentOS Vault软件仓库](https://mirrors.tuna.tsinghua.edu.cn/help/centos-vault/)

# HBase

1.安装

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

1.安装

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

# Impala

1.安装

    # 搭建Impala 4.0，安装MobaXterm软件。从以下地址下载文件quickstart.yml放到Xterm的家目录(即cd ~)
    https://github.com/apache/impala/blob/master/docker/quickstart.yml

    # 在Xterm的家目录新建文件夹quickstart_conf，并从以下地址下载hive.xml文件放到该目录
    https://github.com/apache/impala/blob/master/docker/quickstart_conf/hive-site.xml

    # 运行以下命令进行Impala的Docker环境的前置设置
    docker network create -d bridge quickstart-network
    export QUICKSTART_LISTEN_ADDR=0.0.0.0
    export IMPALA_QUICKSTART_IMAGE_PREFIX="apache/impala:4.0.0-"

    # 运行以下命令创建Impala 4.0的集群，会同时创建4个docker容器，包括Catalogd, StateStore和Impalad各一个，还有一个HMS
    docker-compose -f quickstart.yml up -d

    # 导入测试数据并登录impala-shell，执行以下命令导入数据
    docker-compose -f ./quickstart.yml -f ./quickstart-load-data.yml up -d

    # 登录到home_impalad-1_1的CLI获取该docker的ip地址
    docker exec -it home_impalad-1_1 bash
    ip addr | grep 172
    # IP 地址为172.18.0.5

    # 将该ip地址和docker_impalad-1_1的主机名配置到自己Windows的hosts文件中
    172.18.0.5  docker_impalad-1_1

    # 使用以下命令登录到impala-shell
    docker run --network=quickstart-network -it ${IMPALA_QUICKSTART_IMAGE_PREFIX}impala_quickstart_client impala-shell

    # 或者退出之后重新启动这个容器，并执行
    docker restart xxxxxxxxx
    docker exec -it xxxxxxxxx bash
    impala-shell --protocol=hs2 --history_file=/tmp/impalahistory -i docker_impalad-1_1

    # 验证安装成功
    show databases;

2.什么是Impala

Impala是用于处理存储在Hadoop集群中的大量数据的MPP（大规模并行处理）SQL查询引擎。 它是一个用C++和Java编写的开源软件。与其他Hadoop的SQL引擎相比，它提供了高性能和低延迟。
换句话说，Impala是性能最高的SQL引擎（提供类似RDBMS的体验），它提供了访问存储在Hadoop分布式文件系统中的数据的最快方法。
Impala通过使用标准组件（如HDFS，HBase，Metastore，YARN和Sentry）将传统分析数据库的SQL支持和多用户性能与Apache Hadoop的可扩展性和灵活性相结合。
使用Impala，与其他SQL引擎（如Hive）相比，用户可以使用SQL查询以更快的方式与HDFS或HBase进行通信。
Impala可以读取Hadoop使用的几乎所有文件格式，如Parquet，Avro，RCFile。
Impala将相同的元数据，SQL语法（Hive SQL），ODBC驱动程序和用户界面（Hue Beeswax）用作Apache Hive，为面向批量或实时查询提供熟悉且统一的平台。
与Apache Hive不同，Impala不基于MapReduce算法。它实现了一个基于守护进程的分布式架构，它负责在同一台机器上运行的查询执行的所有方面。
因此，它减少了使用MapReduce的延迟，这使Impala比Apache Hive快。

Impala的优点：

    使用impala，您可以使用传统的SQL知识以极快的速度处理存储在HDFS中的数据。
    由于在数据驻留（在Hadoop集群上）时执行数据处理，因此在使用Impala时，不需要对存储在Hadoop上的数据进行数据转换和数据移动。
    使用Impala，您可以访问存储在HDFS，HBase和Amazon s3中的数据，而无需了解Java（MapReduce作业）。您可以使用SQL查询的基本概念访问它们。
    为了在业务工具中写入查询，数据必须经历复杂的提取 - 变换负载（ETL）周期。但是，使用Impala，此过程缩短了。加载和重组的耗时阶段通过新技术克服，如探索性数据分析和数据发现，使过程更快。
    Impala正在率先使用Parquet文件格式，这是一种针对数据仓库场景中典型的大规模查询进行优化的柱状存储布局。

Impala的缺点：

    Impala不提供任何对序列化和反序列化的支持。
    Impala只能读取文本文件，而不能读取自定义二进制文件。
    每当新的记录/文件被添加到HDFS中的数据目录时，该表需要被刷新。

3.关系数据库和Impala

    Impala	关系型数据库
    Impala使用类似于HiveQL的类似SQL的查询语言。	关系数据库使用SQL语言。
    在Impala中，您无法更新或删除单个记录。	在关系数据库中，可以更新或删除单个记录。
    Impala不支持事务。	关系数据库支持事务。
    Impala不支持索引。	关系数据库支持索引。
    Impala存储和管理大量数据（PB）。	 与Impala相比，关系数据库处理的数据量较少（TB）。

4.Hive，Hbase和Impala

虽然Cloudera Impala使用与Hive相同的查询语言，元数据和用户界面，但在某些方面它与Hive和HBase不同。 下表介绍了HBase，Hive和Impala之间的比较分析。

    HBase	Hive	Impala
    HBase是基于Apache Hadoop的宽列存储数据库。 它使用BigTable的概念。	Hive是一个数据仓库软件。使用它，我们可以访问和管理基于Hadoop的大型分布式数据集。	Impala是一个管理，分析存储在Hadoop上的数据的工具。
    HBase的数据模型是宽列存储。	Hive遵循关系模型。	Impala遵循关系模型。
    HBase是使用Java语言开发的。	Hive是使用Java语言开发的。	Impala是使用C++开发的。
    HBase的数据模型是无模式的。	Hive的数据模型是基于模式的。	Impala的数据模型是基于模式的。
    HBase提供Java，RESTful和Thrift API。	Hive提供JDBC，ODBC，Thrift API。	Impala提供JDBC和ODBC API。
    支持C，C＃，C++，Groovy，Java PHP，Python和Scala等编程语言。	支持C++，Java，PHP和Python等编程语言。	Impala支持所有支持JDBC/ODBC的语言。
    HBase提供对触发器的支持。	Hive不提供任何触发器支持。	Impala不提供对触发器的任何支持。

5.Impala架构

Impala是在Hadoop集群中的许多系统上运行的MPP（大规模并行处理）查询执行引擎。
与传统存储系统不同，impala与其存储引擎解耦。 它有三个主要组件，即Impala daemon（Impalad），Impala Statestore和Impala元数据或metastore。

(1)Impala daemon（Impalad）

Impala daemon（也称为impalad）在安装Impala的每个节点上运行。 它接受来自各种接口的查询，如impala shell，hue browser等，并处理它们。
每当将查询提交到特定节点上的impalad时，该节点充当该查询的“协调器节点”。Impalad还在其他节点上运行多个查询。
接受查询后，Impalad读取和写入数据文件，并通过将工作分发到Impala集群中的其他Impala节点来并行化查询。
当查询处理各种Impalad实例时，所有查询都将结果返回到中央协调节点。
根据需要，可以将查询提交到专用Impalad或以负载平衡方式提交到集群中的另一Impalad。

(2)Impala存储的状态

Impala有另一个称为Impala State存储的重要组件，它负责检查每个Impalad的运行状况，然后经常将每个Impala Daemon运行状况中继给其他守护程序。这可以在运行Impala服务器或群集中的其他节点的同一节点上运行。
Impala State存储守护进程的名称为存储的状态。Impalad将其运行状况报告给Impala State存储守护程序，即存储的状态。
在由于任何原因导致节点故障的情况下，Statestore将更新所有其他节点关于此故障，并且一旦此类通知可用于其他impalad，则其他Impala守护程序不会向受影响的节点分配任何进一步的查询。

(3)Impala元数据和元存储

Impala元数据和元存储是另一个重要组件。Impala使用传统的MySQL或PostgreSQL数据库来存储表定义。诸如表和列信息和表定义的重要细节存储在称为元存储的集中式数据库中。
每个Impala节点在本地缓存所有元数据。 当处理极大量的数据和/或许多分区时，获得表特定的元数据可能需要大量的时间。 因此，本地存储的元数据缓存有助于立即提供这样的信息。
当表定义或表数据更新时，其他Impala后台进程必须通过检索最新元数据来更新其元数据缓存，然后对相关表发出新查询。

(4)查询处理接口

    Impala-shell - 使用Cloudera VM设置Impala后，可以通过在编辑器中键入impala-shell命令来启动Impala shell。 我们将在后续章节中更多地讨论Impala shell。
    Hue界面 - 您可以使用Hue浏览器处理Impala查询。 在Hue浏览器中，您有Impala查询编辑器，您可以在其中键入和执行impala查询。 要访问此编辑器，首先，您需要登录到Hue浏览器。
    ODBC/JDBC驱动程序 - 与其他数据库一样，Impala提供ODBC/JDBC驱动程序。 使用这些驱动程序，您可以通过支持这些驱动程序的编程语言连接到impala，并构建使用这些编程语言在impala中处理查询的应用程序。

(5)查询执行过程

每当用户使用提供的任何接口传递查询时，集群中的Impalad之一就会接受该查询。 此Impalad被视为该特定查询的协调程序。
在接收到查询后，查询协调器使用Hive元存储中的表模式验证查询是否合适。稍后，它从HDFS名称节点收集关于执行查询所需的数据的位置的信息，并将该信息发送到其他impalad以便执行查询。
所有其他Impala守护程序读取指定的数据块并处理查询。一旦所有守护程序完成其任务，查询协调器将收集结果并将其传递给用户。

6.基本语句

    CREATE DATABASE IF NOT EXISTS my_database;
    use my_database;

    CREATE TABLE IF NOT EXISTS student
    (name STRING, age INT, contact INT );

    describe student;

    insert into student values ('zhanghao', 32,  5285);

    select * from student;

7.参考

(1)[如何使用Docker在Windows下快速构建Impala4.0环境](https://www.modb.pro/db/108902)

(2)[github impala docker quickstart](https://github.com/apache/impala/tree/master/docker)

(3)[w3cschool impala教程](https://www.w3cschool.cn/impala/impala_overview.html)

# Flume

1.安装

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

2.什么是Flume

Apache Flume是一种工具/服务/数据提取机制，用于收集聚合并将大量流数据（例如日志文件，事件（等等））从各种源传输到集中式数据存储。
Flume是一种高度可靠，分布式和可配置的工具。它主要用于将流数据（日志数据）从各种Web服务器复制到HDFS。

3.Flume的优点

    使用Apache Flume，我们可以将数据存储到任何集中存储（HBase，HDFS）中。
    当传入数据的速率超过可以将数据写入目标的速率时，Flume充当数据生成器和集中存储之间的中介，并在它们之间提供稳定的数据流。
    Flume提供了上下文路由的功能。
    Flume中的交易是基于渠道的，其中为每条消息维护两个交易（一个发送者和一个接收者）。它保证了可靠的消息传递。
    Flume可靠，容错，可扩展，易于管理和可定制。

4.概念

    Event：一个事件是内部传送的数据的基本单元。它包含一个字节数组的有效负载，它将从源传输到目标，并附带可选的头。
    Agent：一个代理是一个独立的守护进程（JVM）。它从客户端或其他代理接收数据（事件），并将其转发到下一个目标（接收器或代理）。Agent包含三个主要组件，即源， 通道和接收器。
    Source：源是从数据生成器，并将其传送接收数据到一个或多个通道在Flume事件的形式的代理的组件。Flume支持多种类型的源，每个源从指定的数据生成器接收事件。
    Channel：通道是从所述源接收的事件和缓冲它们直到它们由汇消耗的瞬时存储。它充当源和接收器之间的桥梁。例如JDBC通道，文件系统通道，内存通道等。
    Sink：接收器将数据存储到HBase和HDFS等集中存储中。它消耗来自通道的数据（事件）并将其传送到目的地。接收器的目的地可能是另一个代理或中央存储。

5.配置

在Flume配置文件中，我们需要如下配置。通常我们可以在Flume中拥有多个代理。我们可以使用唯一名称区分每个代理。使用此名称，我们必须配置每个代理。

    命名当前代理的组件。
    描述/配置源。
    描述/配置接收器。
    描述/配置频道。
    将源和接收器绑定到通道。

6.命令

    # 查看flume版本
    flume-ng version

    # 启动
    # --conf，-c 在conf目录中使用配置文件；-f  指定配置文件路径；--name，-n 代理的名称；-D property = value 设置Java系统属性值。
    ./bin/flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/conf/netcat.conf --name NetcatAgent -Dflume.root.logger=INFO,console

7.示例

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

8.参考

(1)[Flume 1.9.0的安装](https://www.cnblogs.com/ronnieyuan/p/12011731.html)

(2)[codingdict Apache Flume教程](https://codingdict.com/article/9414)

(3)[Flume入门详解](http://www.hainiubl.com/topics/12)

# Kettle

1.参考

(1)[Kettle中文网示例](http://www.kettle.org.cn/category/demo)

(2)[Kettle下载安装使用教程](https://blog.csdn.net/qq_36135335/article/details/86538688)

(3)[Kettle下载地址](https://sourceforge.net/projects/pentaho/files/Pentaho-9.2/client-tools/pdi-ce-9.2.0.0-290.zip/download)

# Sqoop

1.安装

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

2.什么是Sqoop

Apache Sqoop是在Hadoop生态体系和RDBMS体系之间传送数据的一种工具。来自于Apache软件基金会提供。
Sqoop工作机制是将导入或导出命令翻译成mapreduce程序来实现。在翻译出的mapreduce中主要是对inputformat和outputformat进行定制。
Hadoop生态系统包括：HDFS、Hive、Hbase等
RDBMS体系包括：Mysql、Oracle、DB2等
Sqoop可以理解为：“SQL到Hadoop和Hadoop到SQL”。
站在Apache立场看待数据流转问题，可以分为数据的导入导出:
Import：数据导入。RDBMS----->Hadoop
Export：数据导出。Hadoop---->RDBMS

3.验证安装成功

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

4.导入

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

5.导出

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

6.作业

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

7.参考

(1)[易百Sqoop教程](https://www.yiibai.com/sqoop)

(2)[Apache Sqoop官网](https://sqoop.apache.org/)

(3)[阿里云镜像站CentOS 8](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.73db1b11siwlNO)

(4)[sqoop--基本使用](https://blog.51cto.com/kinglab/2447711)

(5)[sqoop从hive导入数据到Mysql报错ClassNotFoundException:class tablename not found](https://blog.csdn.net/mbest6/article/details/105606166/)

(6)[Sqoop 启动任务时报错 java.lang.ClassNotFoundException: Class 表名 not found](https://blog.csdn.net/weixin_42067918/article/details/120814065)

(7)[Sqoop简介（1.4.7 最新版本）](https://blog.csdn.net/xiaohu21/article/details/109137364)，其中的架构图很好！！

(8)[sqoop执行job报错（org/json/JSONObject）](https://www.cnblogs.com/byfboke/p/10000578.html)

# Pig

1.安装

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

2.什么是Apache Pig？

Apache Pig是MapReduce的一个抽象。它是一个工具/平台，用于分析较大的数据集，并将它们表示为数据流。Pig通常与Hadoop一起使用；我们可以使用Apache Pig在Hadoop中执行所有的数据处理操作。
要编写数据分析程序，Pig提供了一种称为Pig Latin的高级语言。该语言提供了各种操作符，程序员可以利用它们开发自己的用于读取，写入和处理数据的功能。 所有这些脚本都在内部转换为Map和Reduce任务。
Apache Pig有一个名为Pig Engine的组件，它接受Pig Latin脚本作为输入，并将这些脚本转换为MapReduce作业。
Apache Pig历史：在2006年时，Apache Pig是作为Yahoo的研究项目开发的，特别是在每个数据集上创建和执行MapReduce作业。在2007时，Apache Pig是通过Apache孵化器开源的。在2008时，Apache Pig的第一个版本出来了。在2010时，Apache Pig获得为Apache顶级项目。

# Spark

1.安装

    docker run -d --name=spark_single --privileged hdfs_proto /usr/sbin/init
    docker cp E:\software\bigdata\spark-3.1.2-bin-without-hadoop.tgz spark_single:/
    docker exec -it spark_single bash
    tar -zxf spark-3.1.2-bin-without-hadoop.tgz
    mv spark-3.1.2-bin-without-hadoop /usr/local/spark
    chown -R hadoop /usr/local/spark

    # 配置环境变量，退出docker容器并重新进入才生效！！
    echo "export SPARK_HOME=/usr/local/spark" >> /etc/bashrc
    echo "export PATH=$PATH:$SPARK_HOME/bin" >> /etc/bashrc
    echo "export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/pyspark.zip:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip" >> /etc/bashrc

    cd /usr/local/spark/conf
    cp spark-env.sh.template spark-env.sh
    vim spark-env.sh
    # 添加如下
    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath)
    
    cd ~
    spark-shell
    :quit

    # 安装python3，否则pyspark无法运行，root用户
    yum -y install python36

    docker stop spark_single
    docker commit spark_single spark_proto

    docker run -d --name=spark_single2 -p 50022:22 -p 57077:7077 -p 58080:8080 --privileged spark_proto /usr/sbin/init

2.基础

(1)执行过程

编写Spark应用与之前实现在Hadoop上的其他数据流语言类似。代码写入一个惰性求值的驱动程序（driver program）中，通过一个动作（action），驱动代码被分发到集群上，由各个RDD分区上的worker来执行。
然后结果会被发送回驱动程序进行聚合或编译。本质上，驱动程序创建一个或多个RDD，调用操作来转换RDD，然后调用动作处理被转换后的RDD。
定义一个或多个RDD，可以通过获取存储在磁盘上的数据（HDFS，Cassandra，HBase，Local Disk），并行化内存中的某些集合，转换（transform）一个已存在的RDD，或者，缓存或保存。
通过传递一个闭包（函数）给RDD上的每个元素来调用RDD上的操作。Spark提供了除了Map和Reduce的80多种高级操作。
使用结果RDD的动作（action）（如count、collect、save等）。动作将会启动集群上的计算。
当Spark在一个worker上运行闭包时，闭包中用到的所有变量都会被拷贝到节点上，但是由闭包的局部作用域来维护。
Spark提供了两种类型的共享变量，这些变量可以按照限定的方式被所有worker访问。广播变量会被分发给所有worker，但是是只读的。累加器这种变量，worker可以使用关联操作来“加”，通常用作计数器。

(2)RDD操作

Transformation：转换的作用是从现有数据集创建新数据集。转换是惰性的，因为它们仅在动作需要将结果返回到驱动程序时才计算。

    map(func) - 它返回一个新的分布式数据集， 该数据集是通过函数func传递源的每个元素而形成的。
    filter(func) - 它返回一个新数据集， 该数据集是通过选择函数func返回true的源元素而形成的。
    flatMap(func) - 这里，每个输入项可以映射到零个或多个输出项， 因此函数func应该返回序列而不是单个项。
    mapPartitions(func) - 它类似于map，但是在RDD的每个分区(块)上单独运行， 因此当在类型T的RDD上运行时， func必须是Iterator <T> => Iterator <U>类型。
    mapPartitionsWithIndex(func) - 它类似于mapPartitions，它为func提供了一个表示分区索引的整数值，因此当在类型T的RDD上运行时，func必须是类型(Int，Iterator <T>)=> Iterator <U>。
    sample(withReplacement, fraction, seed) - 它使用给定的随机数生成器种子对数据的分数部分进行采样，有或没有替换。
    union(otherDataset) - 它返回一个新数据集，其中包含源数据集和参数中元素的并集。
    intersection(otherDataset) - 它返回一个新的RDD，其中包含源数据集和参数中的元素的交集。
    distinct([numPartitions])) - 它返回一个新数据集，其中包含源数据集的不同元素。
    groupByKey([numPartitions]) - 它接收键值对(K，V)作为输入，基于键对值进行分组，并生成(K，Iterable)对的数据集作为输出。
    reduceByKey(func, [numPartitions]) - 当调用(K，V)对的数据集时，返回(K，V)对的数据集，其中使用给定的reduce函数func聚合每个键的值，该函数必须是类型(V，V)=>V。
    aggregateByKey(zeroValue)(seqOp, combOp, [numPartitions]) - 当调用(K，V)对的数据集时，返回(K，U)对的数据集，其中使用给定的组合函数和中性“零”值聚合每个键的值。
    sortByKey([ascending], [numPartitions]) - 它接收键值对(K，V)作为输入，按升序或降序对元素进行排序，并按顺序生成数据集。
    join(otherDataset, [numPartitions])-当调用类型(K，V)和(K，W)的数据集时，返回(K，(V，W))对的数据集以及每个键的所有元素对。通过leftOuterJoin，rightOuterJoin和fullOuterJoin支持外连接。
    cogroup(otherDataset, [numPartitions])-当调用类型(K，V)和(K，W)的数据集时，返回(K，(Iterable，Iterable))元组的数据集。此操作也称为groupWith。
    cartesian(otherDataset)-生成两个数据集的笛卡尔积，并返回所有可能的对组合。
    pipe(command, [envVars])-通过shell命令管道RDD的每个分区，例如， 一个Perl或bash脚本。
    coalesce(numPartitions)-它将RDD中的分区数减少到numPartitions。
    repartition(numPartitions) -它随机重新调整RDD中的数据，以创建更多或更少的分区，并在它们之间进行平衡。
    repartitionAndSortWithinPartitions(partitioner) - 它根据给定的分区器对RDD进行重新分区，并在每个生成的分区中键对记录进行排序。

Action：动作的作用是在对数据集运行计算后将值返回给驱动程序。

    reduce(func)它使用函数func(它接受两个参数并返回一个)来聚合数据集的元素。该函数应该是可交换的和关联的，以便可以并行正确计算。
    collect()它将数据集的所有元素作为数组返回到驱动程序中。在过滤器或其他返回足够小的数据子集的操作之后，这通常很有用。
    count()它返回数据集中的元素数。
    first()它返回数据集的第一个元素(类似于take(1))。
    take(n)它返回一个包含数据集的前n个元素的数组。
    takeSample(withReplacement, num, [seed])它返回一个数组，其中包含数据集的num个元素的随机样本，有或没有替换，可选地预先指定随机数生成器种子。
    takeOrdered(n, [ordering])它使用自然顺序或自定义比较器返回RDD的前n个元素。
    saveAsTextFile(path)它用于将数据集的元素作为文本文件(或文本文件集)写入本地文件系统，HDFS或任何其他Hadoop支持的文件系统的给定目录中。
    saveAsSequenceFile(path)它用于在本地文件系统，HDFS或任何其他Hadoop支持的文件系统中的给定路径中将数据集的元素编写为Hadoop SequenceFile。
    saveAsObjectFile(path)它用于使用Java序列化以简单格式编写数据集的元素，然后可以使用SparkContext.objectFile()加载。
    countByKey()它仅适用于类型(K，V)的RDD。因此，它返回(K，Int)对的散列映射与每个键的计数。
    foreach(func)它在数据集的每个元素上运行函数func以获得副作用，例如更新累加器或与外部存储系统交互。

(3)RDD持久化

在Spark中，RDD采用惰性求值的机制，每次遇到行动操作，都会从头开始执行计算。如果整个Spark程序中只有一次行动操作，这当然不会有什么问题。
但是，在一些情形下，我们需要多次调用不同的行动操作，这就意味着，每次调用行动操作，都会触发一次从头开始的计算。这对于迭代计算而言，代价是很大的，迭代计算经常需要多次重复使用同一组数据。
可以通过持久化（缓存）机制避免这种重复计算的开销。可以使用persist()方法对一个RDD标记为持久化，之所以说“标记为持久化”，是因为出现persist()语句的地方，并不会马上计算生成RDD并把它持久化，而是要等到遇到第一个行动操作触发真正计算以后，才会把计算结果进行持久化，持久化后的RDD将会被保留在计算节点的内存中被后面的行动操作重复使用。

Spark通过在操作中将其持久保存在内存中，提供了一种处理数据集的便捷方式。在持久化RDD的同时，每个节点都存储它在内存中计算的任何分区。也可以在该数据集的其他任务中重用它们。
我们可以使用persist()或cache()方法来标记要保留的RDD。Spark的缓存是容错的。在任何情况下，如果RDD的分区丢失，它将使用最初创建它的转换自动重新计算。
存在可用于存储持久RDD的不同存储级别。通过将StorageLevel对象(Scala，Java，Python)传递给persist()来使用这些级别。但是，cache()方法用于默认存储级别，即StorageLevel.MEMORY_ONLY。

    MEMORY_ONLY 它将RDD存储为JVM中的反序列化Java对象。这是默认级别。如果RDD不适合内存，则每次需要时都不会缓存和重新计算某些分区。
    MEMORY_AND_DISK 它将RDD存储为JVM中的反序列化Java对象。如果RDD不适合内存，请存储不适合的分区到磁盘，并在需要时从那里读取它们。
    MEMORY_ONLY_SER 它将RDD存储为序列化Java对象(即每个分区一个字节的数组)。这通常比反序列化的对象更节省空间。
    MEMORY_AND_DISK_SER 它类似于MEMORY_ONLY_SER，但是将内存中不适合的分区溢出到磁盘而不是重新计算它们。
    DISK_ONLY 它仅将RDD分区存储在磁盘上。
    MEMORY_ONLY_2, MEMORY_AND_DISK_2 它与上面的级别相同，但复制两个群集节点上的每个分区。
    OFF_HEAP 它类似于MEMORY_ONLY_SER，但将数据存储在堆外内存中。必须启用堆外内存。

(4)共享变量

在默认情况下，当Spark在集群的多个不同节点的多个任务上并行运行一个函数时，它会把函数中涉及到的每个变量，在每个任务上都生成一个副本。但是，有时候，需要在多个任务之间共享变量，或者在任务（Task）和任务控制节点（Driver Program）之间共享变量。
为了满足这种需求，Spark提供了两种类型的变量：广播变量（broadcast variables）和累加器（accumulators）。广播变量用来把变量在所有节点的内存之间进行共享。累加器则支持在所有不同节点之间进行累加计算（比如计数或者求和）。

a.广播变量：

广播变量（broadcast variables）允许程序开发人员在每个机器上缓存一个只读的变量，而不是为机器上的每个任务都生成一个副本。
通过这种方式，就可以非常高效地给每个节点（机器）提供一个大的输入数据集的副本。Spark的“动作”操作会跨越多个阶段（stage），对于每个阶段内的所有任务所需要的公共数据，Spark都会自动进行广播。
通过广播方式进行传播的变量，会经过序列化，然后在被任务使用时再进行反序列化。
这就意味着，显式地创建广播变量只有在下面的情形中是有用的：当跨越多个阶段的那些任务需要相同的数据，或者当以反序列化方式对数据进行缓存是非常重要的。
可以通过调用SparkContext.broadcast(v)来从一个普通变量v中创建一个广播变量。这个广播变量就是对普通变量v的一个包装器，通过调用value方法就可以获得这个广播变量的值。

    broadcastVar = sc.broadcast([1, 2, 3])
    broadcastVar.value

b.累加器：

累加器是仅仅被相关操作累加的变量，通常可以被用来实现计数器（counter）和求和（sum）。Spark原生地支持数值型（numeric）的累加器，程序开发人员可以编写对新类型的支持。如果创建累加器时指定了名字，则可以在Spark UI界面看到，这有利于理解每个执行阶段的进程。
一个数值型的累加器，可以通过调用SparkContext.accumulator()来创建。运行在集群中的任务，就可以使用add方法来把数值累加到累加器上，但是，这些任务只能做累加操作，不能读取累加器的值，只有任务控制节点（Driver Program）可以使用value方法来读取累加器的值。

    accum = sc.accumulator(0)
    sc.parallelize([1, 2, 3, 4]).foreach(lambda x : accum.add(x))
    accum.value

3.实例

(1)在容器中运行代码，新建~/test1.py

    from pyspark import SparkContext
    sc = SparkContext( 'local', 'test')
    logFile = "file:///usr/local/spark/README.md"
    logData = sc.textFile(logFile, 2).cache()
    numAs = logData.filter(lambda line: 'a' in line).count()
    numBs = logData.filter(lambda line: 'b' in line).count()
    print('Lines with a: %s, Lines with b: %s' % (numAs, numBs))

两种方式运行。

方式一：

    # hadoop用户执行
    spark-submit ~/test1.py 2>&1 | grep "Lines with a"

方式二：

    # root用户配置，否则可能出现“ModuleNotFoundError: No module named 'pyspark'”
    echo "export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/pyspark.zip:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip" >> /etc/bashrc

    # hadoop用户执行
    python ~/test1.py

(2)词频统计，新建~/test2.py

    from pyspark import SparkContext
    sc = SparkContext( 'local', 'test')
    logFile = "file:///home/hadoop/wordcount/data.txt"
    logData = sc.textFile(logFile, 2).cache()
    wordCount = logData.flatMap(lambda line: line.split(" ")).map(lambda word: (word,1)).reduceByKey(lambda a, b : a + b)
    print(wordCount.collect())

(3)PyCharm远程调试，配置SSH Interpreter。

    from pyspark import SparkContext
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test') #或者sc = SparkContext( 'local', 'test')，6504c1d2a2ca为容器ID
    logFile = "file:///home/hadoop/hello.txt"
    logData = sc.textFile(logFile, 2).cache()
    wordCount = logData.flatMap(lambda line: line.split(" ")).map(lambda word: (word,1)).reduceByKey(lambda a, b : a + b)
    print(wordCount.collect())

    // 注意是启动spark的命令的是
    cd /usr/local/spark/sbin
    sh ./start-all.sh

    // 并不是直接start-all.sh，这样会执行Hadoop的启动。注意区分！

    // 重要！！run configuration的环境变量中添加配置：PYTHONUNBUFFERED=1;SPARK_HOME=/usr/local/spark;PYTHONPATH=/usr/local/spark/python:/usr/local/spark/python/lib/pyspark.zip:/usr/local/spark/python/lib/py4j-0.10.9-src.zip

(4)Spark访问HBase

Spark运行在172.17.0.3，HBase运行在172.17.0.2。并且两个容器中都要start-dfs.sh启动Hadoop。
参照[Spark2.1.0+入门：读写HBase数据(Python版)](http://dblab.xmu.edu.cn/blog/1715-2/)，将172.17.0.2中相关jar包拷贝到172.17.0.3中。

    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath):/usr/local/spark/jars/hbase/*

test_hbase.py：

    from pyspark import SparkContext

    # http://dblab.xmu.edu.cn/blog/1715-2/
    # spark运行在172.0.0.3，hbase运行在172.0.0.2。
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test')

    host = '172.17.0.2'
    table = 'student'
    # 从HBase自带的ZK获取HBase相关信息
    conf = {"hbase.zookeeper.quorum": host, "hbase.mapreduce.inputtable": table}

    # 用于类型转换
    keyConv = "org.apache.spark.examples.pythonconverters.ImmutableBytesWritableToStringConverter"
    valueConv = "org.apache.spark.examples.pythonconverters.HBaseResultToStringConverter"

    hbase_rdd = sc.newAPIHadoopRDD("org.apache.hadoop.hbase.mapreduce.TableInputFormat",
                                   "org.apache.hadoop.hbase.io.ImmutableBytesWritable",
                                   "org.apache.hadoop.hbase.client.Result",
                                   keyConverter=keyConv,
                                   valueConverter=valueConv,
                                   conf=conf)
    count = hbase_rdd.count()
    hbase_rdd.cache()
    output = hbase_rdd.collect()
    for (k, v) in output:
        print(k, v)

注意在172.17.0.3的/etc/hosts文件中加上：

    172.17.0.2      1e79fcc0828a

4.Spark SQL

(1)基础

    from pyspark import SparkContext
    from pyspark.sql.session import SparkSession
    
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test')
    spark=SparkSession(sc)
    df = spark.read.json("file:///home/hadoop/people.json")
    df.show()
    
    # 打印模式信息
    df.printSchema()
    # 选择多列
    df.select(df.name, df.age + 1).show()
    # 条件过滤
    df.filter(df.age > 20).show()
    # 分组聚合
    df.groupBy("age").count().show()
    # 排序
    df.sort(df.age.desc()).show()
    # 多列排序
    df.sort(df.age.desc(), df.name.asc()).show()
    # 对列进行重命名
    df.select(df.name.alias("username"), df.age).show()

(2)访问MySQL，注意先拷贝mysql-connector-java-8.0.27.jar到/usr/local/spark/jars/

    from pyspark import SparkContext
    from pyspark.sql.session import SparkSession

    sc = SparkContext('spark://6504c1d2a2ca:7077', 'test')
    spark = SparkSession(sc)

    prop = {'user': 'root',
            'password': '123456',
            'driver': 'com.mysql.cj.jdbc.Driver'}
    url = 'jdbc:mysql://172.17.0.3:3306/spark?useSSL=false'
    # 读取表
    df = spark.read.jdbc(url=url, table='student', properties=prop)
    df.show()
    df.printSchema()

5.Spark Streaming

(1)基础

Spark Streaming最主要的抽象是DStream（Discretized Stream，离散化数据流），表示连续不断的数据流。
在内部实现上，Spark Streaming的输入数据按照时间片（如1秒）分成一段一段的DStream，每一段数据转换为Spark中的RDD，并且对DStream的操作都最终转变为对相应的RDD的操作。
例如进行单词统计时，每个时间片的数据（存储句子的RDD）经flatMap操作，生成了存储单词的RDD。
整个流式计算可根据业务的需求对这些中间的结果进一步处理，或者存储到外部设备中。
Spark Streaming和Storm最大的区别在于，Spark Streaming无法实现毫秒级的流计算，而Storm可以实现毫秒级响应。
Spark Streaming无法实现毫秒级的流计算，是因为其将流数据按批处理窗口大小（通常在0.5~2秒之间）分解为一系列批处理作业，在这个过程中，会产生多个Spark 作业，且每一段数据的处理都会经过Spark DAG图分解、任务调度过程，因此，无法实现毫秒级响应。
Spark Streaming难以满足对实时性要求非常高（如高频实时交易）的场景，但足以胜任其他流式准实时计算场景。相比之下，Storm处理的单位为Tuple，只需要极小的延迟。

(2)DStream转换操作包括无状态转换和有状态转换。
无状态转换：每个批次的处理不依赖于之前批次的数据。例如文件流实例。
有状态转换：当前批次的处理需要使用之前批次的数据或者中间结果。有状态转换包括基于滑动窗口的转换和追踪状态变化的转换(updateStateByKey)。

(3)文件流实例

    from operator import add
    from pyspark import SparkContext
    from pyspark.streaming import StreamingContext
    
    sc = SparkContext('spark://793c4b434a2c:7077', 'test')
    ssc = StreamingContext(sc, 1)
    lines = ssc.textFileStream('file:///home/hadoop/spark/streaming')
    words = lines.flatMap(lambda line: line.split(' '))
    wordCounts = words.map(lambda x : (x,1)).reduceByKey(add)
    wordCounts.pprint()
    # 开始自动进入循环监听状态，监听新放入文件夹中的文件。
    ssc.start()
    ssc.awaitTermination()

(4)套接字实例，并updateStateByKey

    import sys
    from pyspark import SparkContext
    from pyspark.streaming import StreamingContext
    
    if __name__ == "__main__":
        if len(sys.argv) != 3:
            exit(-1)
    
        sc = SparkContext('spark://793c4b434a2c:7077', 'test')
        ssc = StreamingContext(sc, 1)
        ssc.checkpoint("file:///home/hadoop/spark/streaming/log/")
    
        # RDD with initial state (key, value) pairs
        initialStateRDD = sc.parallelize([(u'hello', 1), (u'world', 1)])
    
        def updateFunc(new_values, last_sum):
            return sum(new_values) + (last_sum or 0)
    
        lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
        running_counts = lines.flatMap(lambda line: line.split(" ")) \
            .map(lambda word: (word, 1)) \
            .updateStateByKey(updateFunc, initialRDD=initialStateRDD)
    
        running_counts.pprint()
    
        ssc.start()
        ssc.awaitTermination()

开一个新窗口，使用如下命令，在这个窗口中手动输入一些单词

    nc -lk 9999

6.MLlib

(1)基于大数据的机器学习

传统的机器学习算法，由于技术和单机存储的限制，只能在少量数据上使用。即以前的统计/机器学习依赖于数据抽样。但实际过程中样本往往很难做好随机，导致学习的模型不是很准确，在测试数据上的效果也可能不太好。
随着 HDFS(Hadoop Distributed File System) 等分布式文件系统出现，存储海量数据已经成为可能。在全量数据上进行机器学习也成为了可能，这顺便也解决了统计随机性的问题。然而，由于MapReduce自身的限制，使得使用MapReduce来实现分布式机器学习算法非常耗时和消耗磁盘IO。
因为通常情况下机器学习算法参数学习的过程都是迭代计算的，即本次计算的结果要作为下一次迭代的输入，这个过程中，如果使用MapReduce，我们只能把中间结果存储磁盘，然后在下一次计算的时候从新读取，这对于迭代 频发的算法显然是致命的性能瓶颈。

在大数据上进行机器学习，需要处理全量数据并进行大量的迭代计算，这要求机器学习平台具备强大的处理能力。Spark立足于内存计算，天然的适应于迭代式计算。即便如此，对于普通开发者来说，实现一个分布式机器学习算法仍然是一件极具挑战的事情。
幸运的是，Spark提供了一个基于海量数据的机器学习库，它提供了常用机器学习算法的分布式实现，开发者只需要有Spark基础并且了解机器学习算法的原理，以及方法相关参数的含义，就可以轻松的通过调用相应的 API 来实现基于海量数据的机器学习过程。
其次，Spark-Shell的即席查询也是一个关键。算法工程师可以边写代码边运行，边看结果。spark提供的各种高效的工具正使得机器学习过程更加直观便捷。比如通过sample函数，可以非常方便的进行抽样。
当然，Spark发展到后面，拥有了实时批计算，批处理，算法库，SQL、流计算等模块等，基本可以看做是全平台的系统。把机器学习作为一个模块加入到Spark中，也是大势所趋。

(2)MLlib

MLlib是Spark的机器学习（Machine Learning）库，旨在简化机器学习的工程实践工作，并方便扩展到更大规模。MLlib由一些通用的学习算法和工具组成，包括分类、回归、聚类、协同过滤、降维等，同时还包括底层的优化原语和高层的管道API。具体来说，其主要包括以下几方面的内容：

    算法工具：常用的学习算法，如分类、回归、聚类和协同过滤；
    特征化工具：特征提取、转化、降维，和选择工具；
    管道(Pipeline)：用于构建、评估和调整机器学习管道的工具;
    持久性：保存和加载算法，模型和管道;
    实用工具：线性代数，统计，数据处理等工具。

Spark 机器学习库从 1.2 版本以后被分为两个包：

    spark.mllib包含基于RDD的原始算法API。Spark MLlib 历史比较长，在1.0 以前的版本即已经包含了，提供的算法实现都是基于原始的RDD。
    spark.ml则提供了基于DataFrames高层次的API，可以用来构建机器学习工作流（PipeLine）。ML Pipeline 弥补了原始 MLlib 库的不足，向用户提供了一个基于DataFrame的机器学习工作流式API套件。

(3)几个重要概念：

DataFrame：使用SparkSQL中的DataFrame作为数据集，它可以容纳各种数据类型。较之RDD，包含了schema信息，更类似传统数据库中的二维表格。它被MLPipeline用来存储源数据。例如，DataFrame中的列可以是存储的文本，特征向量，真实标签和预测的标签等。
Transformer：翻译成转换器，是一种可以将一个DataFrame转换为另一个DataFrame的算法。比如一个模型就是一个Transformer。它可以把一个不包含预测标签的测试数据集DataFrame打上标签，转化成另一个包含预测标签的DataFrame。技术上，Transformer实现了一个方法transform（），它通过附加一个或多个列将一个DataFrame转换为另一个DataFrame。
Estimator：翻译成估计器或评估器，它是学习算法或在训练数据上的训练方法的概念抽象。在Pipeline里通常是被用来操作DataFrame数据并生产一个Transformer。从技术上讲，Estimator实现了一个方法fit（），它接受一个DataFrame并产生一个转换器。如一个随机森林算法就是一个Estimator，它可以调用fit（），通过训练特征数据而得到一个随机森林模型。
Parameter：Parameter被用来设置Transformer或者Estimator的参数。现在，所有转换器和估计器可共享用于指定参数的公共API。ParamMap是一组（参数，值）对。
PipeLine：翻译为工作流或者管道。工作流将多个工作流阶段（转换器和估计器）连接在一起，形成机器学习的工作流，并获得结果输出。

(4)工作流实例，查找出所有包含”spark”的句子

    from pyspark import SparkContext
    from pyspark.sql.session import SparkSession
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    
    
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test')
    spark = SparkSession(sc)
    
    # Prepare training documents from a list of (id, text, label) tuples.
    # 我们的目的是查找出所有包含”spark”的句子，即将包含”spark”的句子的标签设为1，没有”spark”的句子的标签设为0。
    training = spark.createDataFrame([
        (0, "a b c d e spark", 1.0),
        (1, "b d", 0.0),
        (2, "spark f g h", 1.0),
        (3, "hadoop mapreduce", 0.0)
    ], ["id", "text", "label"])
    
    # 定义Pipeline中的各个工作流阶段PipelineStage，包括转换器和评估器，具体的，包含tokenizer, hashingTF和lr三个步骤。
    #  Tokenizer.transform（）方法将原始文本文档拆分为单词，向DataFrame添加一个带有单词的新列。
    #  HashingTF.transform（）方法将字列转换为特征向量，向这些向量添加一个新列到DataFrame。
    #  现在，由于LogisticRegression是一个Estimator，Pipeline首先调用LogisticRegression.fit（）产生一个LogisticRegressionModel。 如果流水线有更多的阶段，则在将DataFrame传递到下一个阶段之前，将在DataFrame上调用LogisticRegressionModel的transform（）方法。
    
    tokenizer = Tokenizer(inputCol="text", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.001)
    # 有了这些处理特定问题的转换器和评估器，接下来就可以按照具体的处理逻辑有序的组织PipelineStages 并创建一个Pipeline。
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    # 现在构建的Pipeline本质上是一个Estimator，在它的fit（）方法运行之后，它将产生一个PipelineModel，它是一个Transformer。
    model = pipeline.fit(training)
    
    # 我们可以看到，model的类型是一个PipelineModel，这个管道模型将在测试数据的时候使用。所以接下来，我们先构建测试数据。
    test = spark.createDataFrame([
        (4, "spark i j k"),
        (5, "l m n"),
        (6, "spark hadoop spark"),
        (7, "apache hadoop")
    ], ["id", "text"])
    
    # 然后，我们调用我们训练好的PipelineModel的transform（）方法，让测试数据按顺序通过拟合的工作流，生成我们所需要的预测结果。
    prediction = model.transform(test)
    selected = prediction.select("id", "text", "probability", "prediction")
    
    for row in selected.collect():
        rid, text, prob, prediction = row
        print("(%d, %s) --> prob=%s, prediction=%f" % (rid, text, str(prob), prediction))

7.参考

(1)[易百Spark教程](https://www.yiibai.com/spark)

(2)[w3cschool Spark编程指南](https://www.w3cschool.cn/spark/)

(3)[w3cschool spark sql](https://www.w3cschool.cn/spark_sql/)

(4)[RDD Programming Guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html)

(5)[Spark编程指南简体中文版](https://endymecy.gitbooks.io/spark-programming-guide-zh-cn/content/)

(6)[子雨大数据之Spark入门教程(Python版)](http://dblab.xmu.edu.cn/blog/1709-2/)

(7)[pyspark对Mysql数据库进行读写](https://zhuanlan.zhihu.com/p/136777424)

# Flink

1.安装

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

2.参考

(1)[Apache Flink文档](https://nightlies.apache.org/flink/flink-docs-release-1.14/zh/)

# Clickhouse

1.安装

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

2.参考

(1)[window下安装clickhouse](https://www.cnblogs.com/huanghanyu/p/15322960.html)

(2)[clickhouse修改用户密码](https://segmentfault.com/a/1190000021512811)

(3)[clickhouse官方文档](https://clickhouse.com/docs/zh/)

# CDH

1.安装

    docker pull cloudera/quickstart:latest
    # 或者如下方式离线导入镜像
    # wget https://downloads.cloudera.com/demo_vm/docker/cloudera-quickstart-vm-5.13.0-0-beta-docker.tar.gz
    # tar xzf cloudera-quickstart-vm-5.13.0-0-beta-docker.tar.gz
    # docker import - cloudera/quickstart:latest < cloudera-quickstart-vm-5.13.0-0-beta-docker/*.tar
	
	# 修改本地hosts，quickstart.cloudera指向宿主机的IP
    127.0.0.1 quickstart.cloudera

    # 启动容器，当出现容器一启动就exit的情况，通过配置wsl2的配置文件解决
    docker run -i -t -d --name cdh --hostname=quickstart.cloudera --privileged=true -p 8020:8020 -p 8022:8022 -p 7180:7180 -p 21050:21050 -p 50070:50070 -p 50075:50075 -p 50010:50010 -p 50020:50020 -p 8890:8890 -p 60010:60010 -p 10002:10002 -p 25010:25010 -p 25020:25020 -p 18088:18088 -p 8088:8088 -p 19888:19888 -p 7187:7187 -p 11000:11000 -p 8888:8888 cloudera/quickstart /bin/bash -c '/usr/bin/docker-quickstart'
	# 进入容器
	docker exec -it 容器名 bin/bash
	
	# 容器内时钟同步
	vi etc/profile
	# 文件末尾添加一行
	TZ='Asia/Shanghai'; export TZ
	# 生效
	source etc/profile
	# 最后启动时钟同步服务：
	service ntpd start
	service ntpd status
	
	# 启动cloudera-manager，内存小于8G，加上--force参数
	/home/cloudera/cloudera-manager --express --force

N.参考

(1)[使用Docker镜像快速部署hadoop集群](https://www.modb.pro/db/131767)

(2)[Windows使用Docker出现exit 139错误](https://www.cnblogs.com/mxnote/p/16805455.html)

(3)[20分钟自动搭建大数据平台](https://www.jianshu.com/p/5ecf73668b4d)

(4)[保姆级教程：docker下安装CDH（单机版）](https://blog.51cto.com/u_15294985/9805211)

(5)[CDH5.16.2在线搭建](https://blog.csdn.net/qq_44119575/article/details/118766030)

# TODO

flink、pig、azkaban、kafka