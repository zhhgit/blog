---
layout: post
title: "Gulp web server"
description: Gulp web server
modified: 2016-07-11
category: Tool
tags: [Tool]
featured: true
---

用npm init命令，自动生成package.json文件。在当中再添加dependencies，版本号可以在[npm网站](https://www.npmjs.com/)上查询。最后package.json文件为：

	{
	  "name": "gulptest",
	  "version": "1.0.0",
	  "description": "",
	  "main": "gulpfile.js",
	  "dependencies": {
	    "gulp": "^3.9.1",
	    "gulp-connect": "^4.1.0"
	  },
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1"
	  },
	  "author": "",
	  "license": "ISC"
	}

用如下命令在线下载node modules目录

	npm install --save-dev

gulpfile.js文件参考[这里](http://www.tuicool.com/articles/qUvyEj)，完整如下：

	var gulp = require('gulp');
	var connect = require('gulp-connect');

	//创建watch任务去检测html文件,其定义了当html改动之后，去调用一个Gulp的Task
	gulp.task('watch', function () {
	  gulp.watch(['./www/*.html'], ['html']);
	});

	//使用connect启动一个Web服务器
	gulp.task('connect', function () {
	  connect.server({
	    root: 'www',
	    port: 8083,
	    livereload: true
	  });
	});

	gulp.task('html', function () {
	  gulp.src('./www/*.html')
	    .pipe(connect.reload());
	});

	//运行Gulp时，默认的Task
	gulp.task('default', ['connect', 'watch']);

test.html在目录www下，为：

	<html>
	<head>
	<title>页面</title>
	</head>
	<body>
	<p>内容</p>
	</body>
	</html>

一旦修改，自动重新加载。

1.[使用Gulp构建本地开发Web服务器](http://www.tuicool.com/articles/qUvyEj)