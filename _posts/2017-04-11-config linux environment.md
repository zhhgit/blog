---
layout: post
title: "Linux开发环境配置"
description: Linux开发环境配置
modified: 2017-04-11
category: Linux
tags: [Linux]
---

# 一、安装配置XAMPP

1.下载64位的包xampp-linux-x64-5.6.30-0-installer.run，通过FileZilla将文件上传到阿里云的机器上。

执行如下命令，套件将被安装到/opt/lampp目录下

    chmod 777 xampp-linux-x64-5.6.30-0-installer.run
    ./xampp-linux-x64-5.6.30-0-installer.run

2.启动停止的命令分别为

    /opt/lampp/lampp start
    /opt/lampp/lampp stop

3.启动mysql客户端应该进入/opt/lampp/bin目录执行以下语句，初始用户root，密码为空

    ./mysql -uroot -p

可能会出现root用户没有mysql这个数据库访问权限的情况，应该参考[这里](https://blog.csdn.net/jt_121217/article/details/78247265)删除空用户数据。

先停止MySQL服务，用mysqld命令启动MySQL服务端，在另一个窗口中无需密码地启动MySQL客户端，此时可以看到并操作mysql这个数据库中的user表。

    -------------------------------停止服务
    /opt/lampp/lampp stop
    -------------------------------启动服务端
    cd /opt/lampp/sbin
    ./mysqld --skip-grant-table
    -------------------------------启动客户端
    cd /opt/lampp/bin
    ./mysql
    -------------------------------删除空用户
    delete from user where user='';
    flush privileges;

之后再启动MySQL客户端

    mysql -uroot -p
    use mysql;
    -------------------------------将root密码设置为123456
    update user set password=password('123456') where user="root";
    flush privileges;
    quit
    
4.用DbVisualizer远程连接MySQL可能出现如下问题，参考[解决方法](http://blog.csdn.net/langzi7758521/article/details/51729735)

    message from server: "Host xxx is not allowed to connect to this MySQL server

也可能会提示通信失败，参考[阿里云MySQL远程连接不上问题](https://www.cnblogs.com/funnyboy0128/p/7966531.html)，主要是添加阿里云的安全组。
    
5.MySQL数据可能出现乱码，需要修改/opt/lampp/etc/my.cnf文件中的配置。

6.Apache配置文件路径为

    httpd.conf位于：/opt/lampp/etc/httpd.conf
    httpd-ssl.conf位于：/opt/lampp/etc/extra/httpd-ssl.conf
    
主要对httpd-ssl.conf的VirtualHost进行配置，参考[HTTPS配置](http://zhanghao90.cn/blog/web/https-configuration)

# 二、安装NodeJS

下载64位的Binary包node-v6.9.5-linux-x64.tar.xz，放到共享目录/mnt/share下。拷贝到/software目录下

    cp /mnt/share/node-v6.9.5-linux-x64.tar.xz /software

解压压缩文件

    xz -d node-v6.9.5-linux-x64.tar.xz
    tar -xvf node-v6.9.5-linux-x64.tar

进入bin目录可以看到node和npm 两个命令

    cd node-v6.9.5-linux-x64/bin

此时已经可以通过以下方式看到版本号

    ./node -v

但是在其他目录不能执行命令，需要在/usr/local/bin目录下建软链接

    ln -s /software/node-v6.9.5-linux-x64/bin/node /usr/local/bin/node
    ln -s /software/node-v6.9.5-linux-x64/bin/npm /usr/local/bin/npm

至此安装完成。在其他目录可以执行node和npm命令。

# 三、安装Apache

下载64位的源码httpd-2.2.32.tar.gz，放到共享目录/mnt/share下。拷贝到/software目录下

    cp /mnt/share/httpd-2.2.32.tar.gz /software

解压压缩文件

    tar -zxvf httpd-2.2.32.tar.gz

分别执行如下命令进行编译，/usr/local/apach2为安装的后的目录

    cd httpd-2.2.32
    ./configure --prefix=/usr/local/apache2 --enable-so --enable-rewrite
    make
    make install

控制Apache启动停止的命令为apachectl，需要建软链接

    ln -s /usr/local/apache2/bin/apachectl /usr/local/bin/apachectl

至此安装完成。在其他目录可以执行Apache的启动停止命令。

    apachectl start
    apachectl stop

Apache具体配置和Windows下一致。

# 四、安装Tomcat

下载apache-tomcat-8.5.15.zip(core,zip包)，解压后放到/usr/local/目录下

    upzip apache-tomcat-8.5.15.zip
    cp ./apache-tomcat-8.5.15 /usr/local/apache-tomcat-8.5.15

修改端口号，默认为8080，找到对应位置，修改为8081

    cd /usr/local/apache-tomcat-8.5.15/conf
    grep "8080" server.xml

通过浏览器登录时需要修改conf/tomcat-users.xml，添加用户

    <role rolename="manager-gui"/>
    <user username="user" password="password" roles="manager-gui"/>

启动和停止命令分别为

    cd /usr/local/apache-tomcat-8.5.15/bin
    //启动
    sh startup.sh
    //停止
    sh shutdown.sh
    //命令无法执行时可能需要增加执行权限
    chmmod +x *.sh

war包需要部署到webapps目录下，会自动解压。

配置全局JNDI数据源方法如下：

1.在conf/server.xml中GlobalNamingResources节点下加一个全局数据源

    <Resource name="jdbc/horizon" auth="Container"
                scope="jdbc/horizon"
                type="javax.sql.DataSource"
                driverClassName="com.mysql.jdbc.Driver"
                url="jdbc:mysql://localhost:3306/horizon"
                username="root"
                password="password"
                maxIdle="30" />

2.在conf/context.xml中Context节点下加一个ResourceLink节点对第一步配置的数据源进行引用

    <ResourceLink
             global="jdbc/horizon"
             name="jdbc/horizon"
             auth="Container"
             type="javax.sql.DataSource"/>

3.Spring配置文件中添加的bean为

    <bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean">
		<property name="jndiName">
			<value>java:comp/env/jdbc/horizon</value>
		</property>
	</bean>

# 五、安装JDK

    cd /usr/java/jdk
    tar -zxvf jdk-8u171-linux-x64.tar.gz
    //修改profile
    vim /etc/profile
    export JAVA_HOME=/usr/java/jdk/jdk1.8.0_171
    export JRE_HOME=/usr/java/jdk/jdk1.8.0_171/jre
    export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
    export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$JAVA_HOME:$PATH

# 六、用Vim编辑文档

安装Vim

    sudo apt-get install vim

新建、编辑、保存文件

    cd /home/zhanghao
    mkdir test1
    touch app.js
    vim app.js

按i进入Insert模式，按Esc退出Insert模式。

按v进入Visual模式，选择几行然后按y复制，Esc退出后再移动到希望插入的位置按p粘贴。

在一般模式下保存并退出Vim

    ：wq

# 七、搜索日志

    less server.log
    shift + g(即G) // 跳到文档最后
    ?keyword // 反向搜索
    n // 反向搜索下一个

# 八、参考

1.[linux下xampp安装](http://jingyan.baidu.com/article/afd8f4de7976b034e286e90c.html)

2.[Mysql root用户密码重置](http://jingyan.baidu.com/article/63f236280a11680208ab3d91.html)

3.[linux下部署nodejs（两种方式）](http://www.cnblogs.com/dubaokun/p/3558848.html)

4.[Linux安装配置apache](http://www.cnblogs.com/fly1988happy/archive/2011/12/14/2288064.html)

5.[在mac系统安装Apache Tomcat的详细步骤](http://blog.csdn.net/huyisu/article/details/38372663)