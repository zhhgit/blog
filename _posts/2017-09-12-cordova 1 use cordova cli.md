---
layout: post
title: "Cordova(1):使用Cordova CLI"
description: Cordova(1):使用Cordova CLI
modified: 2017-09-12
category: Cordova
tags: [Cordova]
---

工作中使用Cordova已久，但是一直集中于：与客户端同事定好Cordova插件名和方法 ==》 封装JS插件 ==》 使用JS插件，理解并不深刻。新开Cordova系列，争取弄明白Android上Cordova的原理吧。

按照官网说法，Cordova提供两个基本的工作流用来创建移动App。跨平台(CLI)的工作流，和平台为中心的工作流。

“跨平台(CLI)的工作流:如果你想你的App运行在尽可能多的移动操作系统，那么就使用这个工作流，你只需要很少的特定平台开发。 这个工作流围绕这'cordova'CLI(命令行)。CLI是一个高级别的工具，他允许一次构建多个平台的项目，抽象了很多功能性的低级别shell脚本。CLI把公用的web资源复制到每个移动平台的子目录，根据每个平台做必要的配置变化，运行构建脚本生成2进制文件。CLI统一也提供通用接口，将插件应用在app中。如果要入门可以按照 创建你的第一个App指南中的步骤来 。除非你有一个以平台为中心的工作流，否则建议你使用跨平台工作流。”

# 一、使用Cordova CLI

安装Node，安装Cordova

    npm install -g cordova
    
创建项目

    cordova create MyApp
    
添加平台
    
    cd MyApp
    cordova platform add android
    
检查你当前平台设置状况

    cordova platform ls
    
MyApp/platform目录官方建议不要改动里面的内容。检测是否满足构建平台的要求

    cordova requirements

Android平台会检查Java JDK，Android SDK，Android target，Gradle这几个的安装情况。通常装好Android Studio并能正常build、run项目，前三个就已经装好。Gradle需要添加系统环境变量path，比如：

    C:\Users\zhanghao1\.gradle\wrapper\dists\gradle-3.3-all\55gk2rcmfc6p2dg9u9ohc3hw9\gradle-3.3\bin

修改MyApp/www/目录下的前端代码，按照官网描述CLI可以把公用的web资源复制到每个移动平台的子目录。运行Android打包，手机连接状态可以直接安装。

    cordova run android

# 二、使用插件

核心插件API的用法看起来很明确，直接参照文档上每个插件的用法，安装例如

    cordova plugin add cordova-plugin-battery-status

然后修改www目录中的JS代码，重新执行cordova run android

# 三、参考

1.[Cordova官网](http://cordova.apache.org/)

2.[Cordova中文网](http://cordova.axuer.com/)