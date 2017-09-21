---
layout: post
title: "React(3):webpack&react-router"
description: React(3):webpack&react-router
modified: 2016-07-28
category: React
tags: [React]
---

# 一、webpack

参考[一小时包教会——webpack入门指南](http://www.w2bc.com/Article/50764)和[webpack中使用babel](http://babeljs.cn/docs/setup/#webpack)，配置文件webpack.config.js为

	var config = {
	   entry: './src/routerTest.js',
		
	   output: {
	      path:'./lib/',
	      filename: 'routerTest.js',
	   },

	   devServer: {
	      inline: true,
	      port: 7777
	   },
		
	   module: {
	      loaders: [ {
	         test: /\.js$/,
	         exclude: /node_modules/,
	         loader: 'babel',
				
	         query: {
	            presets: ['es2015', 'react']
	         }
	      }]
	   }
		
	}

	module.exports = config;

这里使用了webpack-dev-server，在7777端口下。也可以用如下命令，直接生成lib文件夹下的打包后的文件。

	webpack --display-error-details

# 二、配置package.json文件

	{
	  "name": "routerTest",
	  "version": "1.0.0",
	  "description": "",
	  "main": "routerTest.js",
	  "babel": {
	    "presets": [
	      "es2015",
	      "react"
	    ]
	  },
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1",
	    "start": "webpack-dev-server --hot"
	  },
	  "author": "",
	  "license": "ISC",
	  "devDependencies": {
	    "babel-core": "^6.11.4",
	    "babel-loader": "^6.2.4",
	    "babel-preset-es2015": "^6.9.0",
	    "babel-preset-react": "^6.11.1",
	    "react-dom": "^15.2.1",
	    "webpack": "^1.13.1"
	  },
	  "dependencies": {
	    "react": "^15.2.1",
	    "react-router": "^2.6.0"
	  }
	}

用npm start命令可以启动服务器。

# 三、HTML与JS文件

demo4.html为

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="UTF-8" />
	    <title>demo4</title>
	</head>
	 <body>
	 	<div id="app"></div>
	    <script type="text/javascript" src="./lib/routerTest.js"></script>
	  </body>
	</html>

注意可以不再引入react文件了。

/src/routerTest.js为

	import React from 'react'
	import ReactDOM from 'react-dom'
	import { Router, Route, hashHistory, Link } from 'react-router'

	const App = React.createClass({
	  render() {
	    return (
	      <div>
	        <h1>App</h1>
	        <ul>
	          <li><Link to="/about">About</Link></li>
	          <li><Link to="/inbox">Inbox</Link></li>
	        </ul>
	        {this.props.children}
	      </div>
	    )
	  }
	})

	const About = React.createClass({
	  render() {
	    return <h3>About</h3>
	  }
	})

	const Inbox = React.createClass({
	  render() {
	    return (
	      <div>
	        <h2>Inbox</h2>
	        {this.props.children || "Welcome to your Inbox"}
	      </div>
	    )
	  }
	})

	const Message = React.createClass({
	  render() {
	    return <h3>Message {this.props.params.id}</h3>
	  }
	})

	ReactDOM.render((
	  <Router history={hashHistory}>
	    <Route path="/" component={App}>
	      <Route path="about" component={About} />
	      <Route path="inbox" component={Inbox}>
	        <Route path="messages/:id" component={Message} />
	      </Route>
	    </Route>
	  </Router>
	), document.getElementById('app'))

出现过这样的[报错](http://qiaolevip.iteye.com/blog/2302162)。

# 四、React Router

参考如下：

1.[React Router 使用教程](http://www.ruanyifeng.com/blog/2016/05/react_router.html)

2.[React Router Tutorial](https://github.com/reactjs/react-router-tutorial)

3.[从 React Router 谈谈路由的那些事](http://stylechen.com/react-router.html)

4.[深入理解 react-router 路由系统](https://segmentfault.com/a/1190000004075348?utm_source=tuicool&utm_medium=referral)

具体参考[demo4](https://github.com/zhhgit/react_coupons/tree/master/demo4-webpack%20and%20react%20router)