---
layout: post
title: "Android连接问题"
description: Android连接问题
modified: 2016-09-22
category: Java
tags: [Java]
---

连接不上：

断开连接（不开HDB）-->撤销USB调试授权-->重新连接-->授权

chrome找不到device:

参考[这里](http://stackoverflow.com/questions/20408996/native-usb-debugging-on-chrome-32-doesnt-detect-device)

1.Download the Android SDK

2.Locate ADB.exe, found in the platform-tools folder

3.Open the file using command prompt

	cd c:\path\to\platform-tools\adb.exe

4.Make sure your phone is disconnected from USB

5.Type the following commands

	adb devices
	adb kill-server
	adb start-server

6.Reconnect your phone, authorise your PC and enjoy the USB debugging