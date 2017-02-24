---
layout: post
title: "Android demos"
description: Android demos
modified: 2017-02-22
category: Android
tags: [Android]
---

# 一、Demos

## Demo1

点击activity1中的按钮通过intent将信息传递到activity2并显示

用到的:
Button,
Intent,
ViewGroup,
TextView

## Demo2

Activity中加载一个WebView

用到的：
WebView,
Toast,
后退

## Demo3

点击按钮去获取网页内容

用到的：
Thread,
Handler,
Message,
HttpURLConnection,
InputStream

## Demo4

点击按钮去获取图片

用到的：
Thread,
Handler,
Message,
HttpURLConnection,
InputStream,
ByteArrayOutputStream,
ImageView,
BitmapFactory,
Bitmap

## Demo5

两个按钮，一个拍照存默认相册并显示，一个拍照存自定义文件并显示。build.gradle中targetSdkVersion 22，否则targetSdkVersion 23时文件IO在6.0上有权限问题。

用到的：
View.OnClickListener,
MediaStore.ACTION_IMAGE_CAPTURE,
ImageView,
Bitmap,
File

# 二、参考

1.[zhhgit/Android-demos](https://github.com/zhhgit/Android-demos)

2.[Android官网](https://developer.android.google.cn/index.html)

3.[Android中文API](http://www.android-doc.com/)

4.[菜鸟Android教程](http://www.runoob.com/w3cnote/android-tutorial-intro.html)