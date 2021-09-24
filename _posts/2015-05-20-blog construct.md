---
layout: post
title: "Github Pages静态blog"
description: Github Pages静态blog
modified: 2015-05-20
category: Tools
tags: [Tools]
---

# 一、jekyll安装

依次安装Ruby、Ruby DevKit、Jekyll。_config.yml修改如下：

	port:        4000
	baseurl:     /
	url:         http://localhost:4000

然后启动jekyll:

	jekyll serve -w

Github Pages需要注意仓库分支为gh-pages，才可以访问。同步时记得修改配置文件_config.yml到自己的网址。

# 二、参考

1.[jekyll主页](http://jekyll.bootcss.com/)