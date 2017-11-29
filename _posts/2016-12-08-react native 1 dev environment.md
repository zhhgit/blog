---
layout: post
title: "React Native(1):Windows上Android开发环境搭建"
description: React Native(1):Windows上Android开发环境搭建
modified: 2016-12-08
category: React Native
tags: [React Native]
---

# 一、开发环境搭建

1.配置好Android运行环境，确保一个正常的Android工程能正常run起来。

2.全局安装react和react-native-cli，并初始化一个项目。

    npm install -g react
    npm install -g react-native-cli
    react-native init Test1
    cd Test1
    npm install
    
或者参考[这里](https://segmentfault.com/q/1010000004033633)初始化一个项目，会自动生成一系列的目录和文件。

    node -e "require('react-native/local-cli/cli').init('.','Test1')"

3.连接真机，用如下命令运行项目

    react-native run-android

此时相当于执行了

    （1）adb -s XXXXX reverse tcp:8081 tcp:8081使真机成功连接
    （2）react-native start启动packager server
    （3）执行gradle，打包apk

gradle执行失败可能是需要修改\Test1\android\app\build.gradle

    compileSdkVersion 23
    buildToolsVersion "23.0.3"
    aaptOptions.cruncherEnabled = false
    aaptOptions.useNewCruncher = false
    
修改\Test1\android\build.gradle

    classpath 'com.android.tools.build:gradle:2.3.0'
    
修改\Test1\android\gradle\wrapper\gradle-wrapper.properties

    distributionUrl=https\://services.gradle.org/distributions/gradle-3.3-all.zip

4.可能出现到执行到97%真机无法安装apk的问题，直接通过adb安装

    cd D:\Work\RN\rndemos\Test1\android\app\build\outputs\apk
    adb install app-debug.apk

5.摇手机，fetch JS bundle。出现失败的情况，可能需要更新react

    npm install react --save

6.如果是AVD，直接apk拖进模拟器安装，双击r来reload。

# 二、参考

1.[React Native中文网](http://reactnative.cn/)

2.[React native配置后，一直Installing react-native package from npm](https://segmentfault.com/q/1010000004033633)

3.[react-native-guide](https://github.com/reactnativecn/react-native-guide)

4.[React Native for Android 实践 -- 实现知乎日报客户端](http://www.race604.com/react-native-android-practice/)




