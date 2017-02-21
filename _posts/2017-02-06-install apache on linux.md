---
layout: post
title: "Linux上安装Apache"
description: Linux上安装Apache
modified: 2017-02-06
category: Linux
tags: [Linux]
---

# 一、安装Apache

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

# 二、用Vim编辑文档

安装Vim

    sudo apt-get install vim
    
新建、编辑、保存文件

    cd /home/zhanghao
    mkdir test1
    touch app.js
    vim app.js
    
按i进入Insert模式，按Esc退出Insert模式。在一般模式下保存并退出Vim

    ：wq

# 三、参考

1.[Linux安装配置apache](http://www.cnblogs.com/fly1988happy/archive/2011/12/14/2288064.html)
