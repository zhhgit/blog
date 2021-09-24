---
layout: post
title: "grunt&gulp"
description: grunt&gulp
modified: 2016-08-19
category: FrontEnd
tags: [FrontEnd]
---

# 一、Grunt

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


# 二、Gulp

典型gulpfile.js:

	var gulp = require('gulp'),
	    uglify = require('gulp-uglify'),
	    minifycss = require('gulp-clean-css'),
	    minifyhtml = require('gulp-html-minifier'),
	    rev = require('gulp-rev-append'),
	    sourcemaps = require('gulp-sourcemaps');

	var paths = {
	    // 需要打包的所有JS文件
	    js: [
	        'web/!(400|test)/**/!(config).js',
	        'web/*.js'
	    ],
	    css: [
	        'web/!(400|test)/**/*.css'
	    ],
	    html: [
	        'web/!(400|test)/**/*.html',
	        'web/!(400|test)/**/*.tpl'
	    ],
	    file: [
	        'web/!(400|test)/**/*.jpg',
	        'web/!(400|test)/**/*.png',
	        'web/!(400|test)/**/*.gif',
	        'web/!(400|test)/**/*.manifest',
	        'web/!(400|test)/**/config.js'
	    ]
	};

	var destDir = './wallet_web/web';
	var mapDir = '../../wallet_web_maps/web';

	gulp.task('release', function () {
	    // 打包所有JS文件
	    gulp.src(paths.js)
	        .pipe(sourcemaps.init())
	        .pipe(uglify({
	            compress: {
	                // 移除console语句
	                drop_console: true
	            },
	            mangle: {except: ['require', 'exports', 'module', '$']}
	        }))
	        .pipe(sourcemaps.write(mapDir, {addComment: false}))
	        .pipe(gulp.dest(destDir));

	    // 打包所有CSS文件
	    gulp.src(paths.css)
	        .pipe(sourcemaps.init())
	        .pipe(minifycss({
	            compatibility: '*',
	            advanced: false
	        }))
	        .pipe(sourcemaps.write(mapDir, {addComment: false}))
	        .pipe(gulp.dest(destDir));

	    gulp.src(paths.html)
	        .pipe(rev())
	        .pipe(sourcemaps.init())
	        .pipe(minifyhtml({
	            // 压缩空格
	            collapseWhitespace: true,
	            minifyJS: true,
	            minifyCSS: true,
	            removeComments: true
	        }))
	        .pipe(sourcemaps.write(mapDir, {addComment: false}))
	        .pipe(gulp.dest(destDir));

	    // 打包所有无需预处理的文件
	    gulp.src(paths.file)
	        .pipe(gulp.dest(destDir));
	});

	gulp.task('default', ['release']);

写一个gulp web server如下：

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

一旦修改，自动重新加载。

1.[使用Gulp构建本地开发Web服务器](http://www.tuicool.com/articles/qUvyEj)
