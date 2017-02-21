---
layout: post
title: "Virtualbox中Ubuntu共享文件夹"
description: Virtualbox中Ubuntu共享文件夹
modified: 2015-06-13
category: Linux
tags: [Linux]
---

# 一、Virtualbox中设置

在Virtualbox“设置中找到共享文件夹，添加，不要勾选只读分配和自动挂载。

# 二、开机后设置

需要使用下面的命令

	mount -t vboxsf share /mnt/share
	
其中"share"是之前创建的共享文件夹的名字

# 三、可能问题

可能会出现提示说不是root用户无法mount，就要用到

	sudo su

进行一下切换即可。
