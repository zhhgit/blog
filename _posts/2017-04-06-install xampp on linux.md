---
layout: post
title: "Linux上安装XAMPP"
description: Linux上安装XAMPP
modified: 2017-04-06
category: Linux
tags: [Linux]
---

# 一、安装NodeJS

下载64位的包，放到共享目录/mnt/share下。拷贝到/software目录下

    cp /mnt/share/xampp-linux-x64-5.6.30-0-installer.run /software

执行如下命令，套件将被安装到/opt/lampp目录下

    chmod -x xampp-linux-x64-5.6.30-0-installer.run
    ./xampp-linux-x64-5.6.30-0-installer.run
   
# 二、参考

1.[linux下xampp安装](http://jingyan.baidu.com/article/afd8f4de7976b034e286e90c.html)
