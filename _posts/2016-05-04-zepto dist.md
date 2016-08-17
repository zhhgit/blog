---
layout: post
title: "Zepto生成Dist"
description: Zepto生成Dist
modified: 2016-05-04
category: Tool
tags: [Tool]
featured: true
---

升级node.js到4.4.2，cmd中切换到有package.json的目录，运行

	npm install

会在当前目录下生成node modules文件夹。然后按照readme.md中的说明操作

	npm run-script dist

或者选择一些模块

	SET MODULES=zepto event data
	npm run-script dist

就会生成dist目录。



	