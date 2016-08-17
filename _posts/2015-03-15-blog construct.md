---
layout: post
title: "Github Pages静态Blog"
description: Github Pages静态Blog
modified: 2015-03-15
category: Tool
tags: [Tool]
featured: true
---

在摸索的过程中看了些网上的博文，有一些确实有用，也有的可能是因为写的比较早版本问题，有些错误。因为网上已经有比较详细的叙述，这里就列举一下当时参考的一些网页。

# jekyll安装与在Github上的部署

首先当然要参考[jekyll的主页](http://jekyll.bootcss.com/)

在ubuntu上安装基本按照[这里](http://jiangxihj.github.io/2014/08/31/jekyllinstallation/),要注意的是可能会遇到如下[问题](http://www.cnblogs.com/wangyuyu/p/3264751.html)。运行jekyll serve的时候慢，最后直接每次commit到github上看效果。

在windows下安装，以及如何部署到Github上可以参考[这里](http://blog.fens.me/jekyll-bootstarp-github/)。Github pages[官方页面](https://pages.github.com/)当然也要参考。

其实在Windows下已经完全可以图形化操作，很方便。要强调的是主页的branches是master，子页面的branches是gh-pages。很重要！！

使用模板时候一定要修改配置文件_config.yml到自己的网址。


# 写博文

博文都在_post文件夹下

首先需要有一段配置描述，要有如下的配置

	---
	layout: post
	title: "Github Pages静态Blog"
	description: Github Pages静态Blog
	modified: 2015-03-15
	category: Tool
	tags: [Tool]
	imagefeature:
	mathjax: false
	chart:
	comments: false
	featured: true
	---

博文文件命名方式为YYYY-MM-DD-UniqueID.md，YYYY-MM-DD为博文发布时间，UniqueID是博文的唯一标识符。

一旦在github pages上部署好之后，再写博文完全就可以在windows下操作。直接用Notepad++等编辑器打开.md文件写就好了。