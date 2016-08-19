---
layout: post
title: "Apache Cordova创建Android App"
description: Apache Cordova创建Android App
modified: 2015-08-07
category: Android
tags: [Android]
---

# 一、安装Cordova

参考[官方页面](http://cordova.apache.org/docs/en/5.0.0//guide_cli_index.md.html#The%20Command-Line%20Interface)，因为已经安装了Node.js，所以前面的步骤都没有问题，直到卡在了Build the App这个环节。问题在于Android SDK上。可以通过如下方式安装旧版本的平台，对应于自己现有的压缩包ADTEclipse。

	cordova platform add android@4.1.0 --save

# 二、Android SDK

我安装的cordova的版本是5.1.1(通过cordova -v可以看到)。一直提示要Android-22。本来电脑上有Android的Eclipse套件，为API 19的，弄了半天，升级什么的，不行。只能再去独立地装Android SDK，最新（installer_r24.3.3-windows，也就是Android SDK tools，版本对能升级的API有影响）。再在SDK manager中安装最新Android SDK platform tools和Android SDK build tools。Tools一共安装这三个。

高版本的SDK（对应不同API），Android-22对应于Android 5.1，其中需要安装对应的SDK platform和system images。SDK platform一定是对应版本。system images，也就是虚拟机，可以装不同版本，但暂未知有没有问题。这两个东西可以独立下载，再解压放到对应文件夹。[下载网址](http://www.androiddevtools.cn/)。

对于Android平台更详细的[配置介绍](http://cordova.apache.org/docs/en/5.0.0//guide_platforms_android_index.md.html#Android%20Platform%20Guide),需要配置系统的环境变量！

新建JAVA_HOME

	D:\Program Files\Java\jdk1.8.0_77

新建ANDROID_HOME

	D:\Program Files\ADTEclipse\adt-bundle-windows-x86_64-20140702\sdk

添加两个新的Path

	D:\Program Files\ADTEclipse\adt-bundle-windows-x86_64-20140702\sdk\platform-tools
	D:\Program Files\ADTEclipse\adt-bundle-windows-x86_64-20140702\sdk\tools

# 三、运行在虚拟机和Android手机上

碰到的问题是手机连接上了之后，一直用cordova run android --list命令都查找不到自己的手机。虚拟机也是费了一点劲，才用ADT bundle的Eclipse中新建了一个虚拟机，可以找到，但是启动一直waiting，不知道是电脑太慢了，还是有什么问题。有空再研究吧。

更新：在安装了最新API 22的x86虚拟机(需要安装extras中的Intel加速器)之后，可以顺利启动虚拟机，启动后再

	cordova emulate android

# 四、生成apk并安装

用下面的命令生成apk：

	cordova build android

apk生成的目录是

	F:\cordovatest\hello\platforms\android\build\outputs\apk

复制出来安装就可以了。

在第一次运行build的时候会发生下载gradle很慢的问题，参考[这里](http://stackoverflow.com/questions/29874564/ionic-build-android-error-when-download-gradle)，自己手动下载好放到本地的一个Apache地址上，修改build.js中相应的位置为localhost相应的地址。之后还会自动下载一些东西，第一次build很慢以后会很快。最简单的项目上传[github库](https://github.com/zhhgit/Cordova-Android-template)了，可以直接替换www文件夹中的内容较快生成App。。

	
