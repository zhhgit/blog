---
layout: post
title: "Linux上安装NodeJS"
description: Linux上安装NodeJS
modified: 2017-02-06
category: Tips
tags: [Tips]
---

# 一、安装NodeJS

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

# 二、参考

1.[linux下部署nodejs（两种方式）](http://www.cnblogs.com/dubaokun/p/3558848.html)
