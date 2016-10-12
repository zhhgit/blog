---
layout: post
title: "React(2):使用babel"
description: React(2):使用babel
modified: 2016-07-27
category: React
tags: [React]
---

# 一、写ES6文件

参考[这里](http://babeljs.io/),分别写两个例子test1.js和test2.js如下

test1.js

	console.log("test1");
	[1,2,4].map(n => n+1);

test2.js

	export default React.createClass({
	  getInitialState() {
	    return { num: this.getRandomNumber() };
	  },

	  getRandomNumber(): number {
	    return Math.ceil(Math.random() * 6);
	  },

	  render(): any {
	    return <div>
	      Your dice roll:
	      {this.state.num}
	    </div>;
	  }
	});

# 二、配置package.json文件

	{
	  "name": "es6test",
	  "version": "1.0.0",
	  "description": "",
	  "babel": {
	    "presets": [
	      "es2015",
	      "react"
	    ]
	  },
	  "main": "script-compiled.js",
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1",
	    "build": "babel src --out-dir lib"
	  },
	  "author": "",
	  "license": "ISC",
	  "devDependencies": {
	    "babel-preset-es2015": "^6.9.0",
	    "babel-preset-react": "^6.11.1"
	  }
	}

其中

	"babel": {
	"presets": [
	  "es2015",
	  "react"
	]
	},

为必须(没用到react时候，可以不写react为preset)。在es6test目录下用命令安装本地node modules(全局安装似乎不行)

	npm install --save-dev babel-preset-es2015
	npm install --save-dev babel-preset-react

# 三、执行编译

既可以写在package.json中

	"build": "babel src --out-dir lib"

然后执行命令，则在lib文件夹下生成对应的转换后的js文件。

	npm run build

也可以参考[这里](http://babeljs.io/docs/usage/cli/)，直接编译某个文件或者目录。

具体参考[demo3](https://github.com/zhhgit/React-demos/tree/master/demo3-use%20babel)


