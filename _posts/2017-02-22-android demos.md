---
layout: post
title: "Android demos"
description: Android demos
modified: 2017-02-22
category: Android
tags: [Android]
---

# 一、Demo说明

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

## Demo6

右上角按钮。

用到的：
Menu,
Toast

## Demo7

所有Activity继承自BasicActivity，当中打印log。ActivityController管理所有的Activity，可以一次性finish。

用到的：
BasicActivity,
ActivityController

## Demo8

常用组件使用。

用到的：
Toast,
EditText,
Button,
ProgressBar,
AlertDialog,
ProgressDialog

## Demo9

include方式引入自定义布局。自定义控件。

用到的：
LayoutInflater,
ActionBar

## Demo10

简单ListView，自定义ListView。

用到的：
LayoutInflater,
ImageView,
TextView,
ArrayAdapter,
AdapterView.OnItemClickListener

## Demo11

纵向RecyclerView，横向RecyclerView，瀑布RecyclerView，绑定事件。

用到的：
LayoutInflater,
ImageView,
TextView,
RecyclerView.Adapter,
RecyclerView.ViewHolder,
LinearLayoutManager,
StaggeredGridLayoutManager

# 二、参考

1.[zhhgit/Android-demos](https://github.com/zhhgit/Android-demos)

2.[Android官网](https://developer.android.google.cn/index.html)

3.[Android中文API](http://www.android-doc.com/)

4.[菜鸟Android教程](http://www.runoob.com/w3cnote/android-tutorial-intro.html)