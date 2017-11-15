---
layout: post
title: "React(1):使用jsx"
description: React(1):使用jsx
modified: 2016-07-15
category: React
tags: [React]
---

# 一、浏览器直接编译jsx

写在一个html中：

	<!DOCTYPE html>
	<html>
	  <head>
	    <meta charset="UTF-8" />
	    <title>React 实例</title>
	    <script src="../react/build/react.min.js"></script>
	    <script src="../react/build/react-dom.min.js"></script>
	    <script src="../browser.min.js"></script>
	  </head>
	  <body>
	    <div id="example"></div>
	    <script type="text/babel">
	    var HelloMessage = React.createClass({
	      getInitialState: function() {
	        return {value: 'Hello Runoob!'};
	      },
	      handleChange: function(event) {
	        this.setState({value: event.target.value});
	      },
	      render: function() {
	        var value = this.state.value;
	        return <div>
	                <input type="text" value={value} onChange={this.handleChange} /> 
	                <h4>{value}</h4>
	               </div>;
	      }
	    });
	    ReactDOM.render(
	      <HelloMessage />,
	      document.getElementById('example')
	    );
	    </script>
	  </body>
	</html>

写在.jsx文件中:

html为

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="UTF-8" />
	    <title>菜鸟教程 React 实例</title>
	    <link rel="stylesheet" type="text/css" href="./try1.css">
	    <script src="../react/build/react.min.js"></script>
	    <script src="../react/build/react-dom.min.js"></script>
	    <script src="../browser.min.js"></script>
	</head>
	 <body>
	    <div id="example"></div>
	    <script type="text/babel" src="./try1.jsx"></script>
	  </body>
	</html>

try1.jsx为

	var HelloMessage = React.createClass({
	  getInitialState: function() {
	    return {value: 'Hello Runoob!'};
	  },
	  handleChange: function(event) {
	    this.setState({value: event.target.value});
	  },
	  render: function() {
	    var value = this.state.value;
	    return <div>
	            <input type="text" value={value} onChange={this.handleChange} /> 
	            <h4>{value}</h4>
	           </div>;
	  }
	});
	ReactDOM.render(
	  <HelloMessage />,
	  document.getElementById('example')
	);

例子见[demo1](https://github.com/zhhgit/react_coupons/tree/master/demo1-browser%20compile%20jsx)

# 二、通过npm使用 React

参考[通过 npm 使用 React](http://www.runoob.com/react/react-install.html)，例子见[demo2](https://github.com/zhhgit/react_coupons/tree/master/demo2-node%20compile%20jsx)

# 三、通过jsx命令编译成JS

	npm install -g react-tools
	jsx --watch script/ js/