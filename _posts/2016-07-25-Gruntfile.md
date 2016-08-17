---
layout: post
title: "gruntfile.js"
description: gruntfile.js
modified: 2016-07-25
category: Tool
tags: [Tool]
featured: true
---

package.json文件为：

	{
	  "name": "grunttest",
	  "version": "1.0.0",
	  "description": "",
	  "main": "gruntfile.js",
	  "devDependencies": {
	    "grunt": "^1.0.1",
	    "grunt-contrib-uglify": "^2.0.0",
	    "grunt-contrib-jshint": "^1.0.0",
	    "grunt-contrib-concat": "^1.0.1",
	    "grunt-contrib-watch": "^1.0.0"
	  },
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1"
	  },
	  "author": "",
	  "license": "ISC"
	}

用如下命令在线下载node modules目录

	npm install --save-dev

gruntfile.js如下：

	module.exports = function(grunt) {
	grunt.initConfig({
	    concat: {
	        'dist/all.js': ['src/*.js']
	    },
	    uglify: {
	        'dist/all.min.js': ['dist/all.js']
	    },
	    jshint: {
	        files: ['gruntfile.js', 'src/*.js']
	    },
	    watch: {
	        files: ['gruntfile.js', 'src/*.js'],
	        tasks: ['jshint', 'concat', 'uglify']
	    }
	});

	// Load Plugins
	grunt.loadNpmTasks('grunt-contrib-jshint');
	grunt.loadNpmTasks('grunt-contrib-concat');
	grunt.loadNpmTasks('grunt-contrib-uglify');
	grunt.loadNpmTasks('grunt-contrib-watch');

	// Register Default Task
	grunt.registerTask('default', ['jshint', 'concat', 'uglify']);
	};

1.[grunt整合版,30分钟学会使用grunt打包前端代码](http://www.cnblogs.com/yexiaochai/p/3603389.html)

2.[前端工程的构建工具对比 Gulp vs Grunt](https://segmentfault.com/a/1190000002491282)