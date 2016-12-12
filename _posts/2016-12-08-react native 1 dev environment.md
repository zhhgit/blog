---
layout: post
title: "React Native(1):Windows上Android开发环境搭建"
description: React Native(1):Windows上Android开发环境搭建
modified: 2016-12-08
category: React Native
tags: [React Native]
---

# 一、开发环境搭建

1.安装配置Java、Android Studio，配置AVD，配置真机连接。

2.使用npm install安装React和React Native。cnpm貌似运行后存在路径找不到的问题，还是用npm，安装一次后备份好，以后直接解压。

3.参考[这里](https://segmentfault.com/q/1010000004033633)，使用如下命令对项目初始化，会自动生成Android，IOS目录、入口文件等。

    node -e "require('react-native/local-cli/cli').init('.','Test1')"

4.连接真机，用如下命令运行项目

    react-native run-android

此时相当于执行了

    （1）adb -s XXXXX reverse tcp:8081 tcp:8081使真机成功连接
    （2）react-native start启动packager server
    （3）执行gradle，打包apk

gradle执行失败可能是需要修改D:\Work\RN\rndemos\Test1\android\app目录中build.gradle

    compileSdkVersion 23
    buildToolsVersion "23.0.3"
    aaptOptions.cruncherEnabled = false
    aaptOptions.useNewCruncher = false

5.会出现到执行到97%真机无法安装apk的问题，直接通过adb安装

    cd D:\Work\RN\rndemos\Test1\android\app\build\outputs\apk
    adb install app-debug.apk

6.摇手机，fetch JS bundle

7.如果是AVD，直接apk拖进模拟器安装，双击r来reload。

# 二、参考

1.[React Native中文网](http://reactnative.cn/)

2.[
React native配置后，一直Installing react-native package from npm](https://segmentfault.com/q/1010000004033633)

3.[react-native-guide](https://github.com/reactnativecn/react-native-guide)

4.[React Native for Android 实践 -- 实现知乎日报客户端](http://www.race604.com/react-native-android-practice/)




