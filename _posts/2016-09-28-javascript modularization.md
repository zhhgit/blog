---
layout: post
title: "JavaScript模块化"
description: JavaScript模块化
modified: 2016-09-28
category: JavaScript
tags: [JavaScript]
---

不同于C，Java等语言，当前浏览器广泛支持的JavaScript版本ECMAScript 5并没有类似于include，import这样的关键字来实现代码的模块化管理。但是随着前端代码的越发复杂和工程化，JS模块化编程已经成为一种迫切的需求。JS模块化有助于前端代码的解耦与重用。下面分成如下三个部分介绍。

JavaScript模块化
│ 
├─ 1.常用模式
│      │ 
│      ├─1.1.构造函数
│      │  
│      └─1.2.匿名闭包 
│
│
├─ 2.模块规范
│      │ 
│      ├─2.1.CommonJS规范
│      │  
│      ├─2.2.AMD规范
│      │  
│      └─2.3.CMD规范
│
│
└─ 3.ES6中的模块



# 1.常用模式

1.1.第一种常用的模式是通过一个构造函数，将一些可以被使用的公开方法暴露出去。例如commonUI.js为：

	var CommonUI = function () {

		//这里可以定义一些私有的变量和函数

	    return {
	    	//暴露公开的成员

	        //显示Loading弹层
	        showLoading : function (msg) {
	            //TODO Loading具体实现
	        },
	        //显示Toast弹层
	        showToast : function (msg, time) {
	            //TODO Toast具体实现
	        },
	        //显示Alert弹层
	        showAlert : function (message, okCallback, cancelCallback, okText, cancelText,titleText) {
	            //TODO Alert具体实现
	        }
	    };

	};

在HTML页面中引用该JS文件，在另外的JS文件中通过如下方式可以使用CommonUI.js中暴露出去的方法。每次用的时候都要通过new新产生一个实例，然后通过该实例调用方法。

	var commonui = new CommonUI();
	commonui.showLoading("加载中");

1.2.第二种常用的模式是通过匿名闭包，这种模式是当前钱包前端代码中最常用的JS模块化的方式。通过匿名闭包的模式可以避免对全局的污染，将用到的变量限制在function作用域。如下的commonUI.js为一段典型的匿名闭包代码，一个匿名函数被定义后立即执行。在全局变量window下的子模块window.UP.W.UI中定义了showLoading(),showToast(),showAlert这三种常用的UI方法。

	(function ($, UP) {

	    //全局变量window下的UI子模块
	    UP.W = UP.W || {};
	    UP.W.UI = UP.W.UI || {};

	    //显示Loading弹层
	    UP.W.UI.showLoading = function (msg) {
	        //TODO Loading具体实现
	    };

	    //显示Toast弹层
	    UP.W.UI.showToast = function (msg, time) {
	        //TODO Toast具体实现
	    };

	    //显示Alert弹层
	    UP.W.UI.showAlert = function (message, okCallback, cancelCallback, okText, cancelText,titleText) {
	        //TODO Alert具体实现
	    };

	})(jQuery, window.UP = window.UP || {});

让当前模块成为全局变量的一个子模块意味着：只要在HTML页面中引用该JS文件，不需要其他的特别处理，在其他的JS文件中可以直接调用该JS文件中定义的方法。

	window.UP.W.UI.showLoading("加载中");


# 2.模块规范

第一部分中实现JS的模块化主要依赖于JavaScript的语言特性。上述的模式，尤其是匿名闭包简单好用，但是也存在一定的问题。当需要引用的模块逐渐增多时，HTML代码中就要引入一堆的JS文件。而且这样的方式不易管理模块之间的依赖关系。为了解决这样的问题，主要发展出了三种Javascript模块规范：CommonJS规范、AMD(Asynchronous Module Definition)规范、CMD(Common Module Definition)规范。

2.1.CommonJS规范

CommonJS规范主要应用于JavaScript服务器端编程，Node.js就是符合CommomJS规范的。CommonUI.js可以如下定义，通过exports向外暴露一些方法。

	exports = {
	    //显示Loading弹层
	    showLoading : function (msg) {
	        //TODO Loading具体实现
	    },
	    //显示Toast弹层
	    showToast : function (msg, time) {
	        //TODO Toast具体实现
	    },
	    //显示Alert弹层
	    showAlert : function (message, okCallback, cancelCallback, okText, cancelText,titleText) {
	        //TODO Alert具体实现
	    }
	};

通过全局性方法require()可以使用CommonUI.js模块中定义的方法。

	var ui= require("CommonUI");
	ui.showLoading("加载中");

CommonJS规范中代码是同步加载的，也就是说第一行require("CommonUI")加载完之后，才能执行第二行的showLoading方法。如果CommonJS.js很大，加载时间很长，整个应用出现“假死”状态。
这对服务器端不是一个问题，因为文件都是从磁盘读取的，速度很快。但在浏览器端，带宽成为了程序执行的主要瓶颈。因此浏览器端不建议使用CommonJS的规范。

2.2.AMD规范

符合AMD规范的模块管理工具主要有RequireJS。可以通过如下方式定义一个模块。

	define(function(){
	    return {
	        //显示Loading弹层
	        showLoading : function (msg) {
	            //TODO Loading具体实现
	        },
	        //显示Toast弹层
	        showToast : function (msg, time) {
	            //TODO Toast具体实现
	        },
	        //显示Alert弹层
	        showAlert : function (message, okCallback, cancelCallback, okText, cancelText,titleText) {
	            //TODO Alert具体实现
	        }
	    }
	});

使用CommonUI.js模块的方法如下。这里的require函数有两个参数，第一个参数是一个数组，里面的成员就是要加载的模块；第二个参数是加载成功之后的回调函数。所谓异步是指，require函数不会等待两个模块加载完成，而会继续执行后面的其他代码console.log("other")。当CommonUI.js模块加载完成之后，再通过回调函数调用showLoading方法。

	require(["CommonUI"], function (ui) {
	    ui.showLoading("加载中");
	});
	//TODO 其他代码
	console.log("other");

2.3.CMD规范

CMD规范是在SeaJS推广过程中产生的，由阿里的玉伯提出。一个符合CMD规范的模块如下。通过require函数引入需要使用的模块，通过module.exports向外暴露公共方法。

	define(function (requie, exports, module) {

	    //加载CommonUI模块
	    var ui = require('CommonUI');
	    ui.showLoading("加载中");

	    //加载CommonUtil模块
	    var util = require('CommonUtil');
	    util.add();

	    module.exports = {
	        demo : function(){
	            //TODO demo具体实现
	        }
	    }
	});

SeaJS和RequireJs的区别在于：RequireJS中依赖一开始就加载，SeaJS对模块的态度是懒执行, 依赖可以就近书写，当需要用到时再去加载。

# 3.ES6中的模块

ES6(ECMAScript 6)是JavaScript语言的下一代标准，已经于2015年6月正式发布。服务器端的Node.js中已经支持了ES6中的很多新特性，但是目前大部分的浏览器对于ES6的支持还不是很好，只能通过babel等工具将ES6语法转换成浏览器支持的ES5语法。ES6中原生地引入了模块的概念。相信用不了特别长的时间JS开发者就可以这样优雅地写一个JS模块。

	import {showLoading,showToast,showAlert} from './CommonUI'

	    showLoading("加载中");

	    var util = {
	        add : function () {
	            //TODO add具体实现
	        },
	        minus : function () {
	            //TODO minus具体实现
	        }
	    };

	export {util};

# 4.参考链接

常用模式

1.[Javascript模块化编程（一）：模块的写法](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)

SeaJS

1.[使用SeaJS实现模块化JavaScript开发](http://blog.codinglabs.org/articles/modularized-javascript-with-seajs.html)

2.[模块化JS编程 seajs简单例子](http://www.itnose.net/detail/6074887.html)

3.[快速上手seajs——简单易用Seajs](http://www.tuicool.com/articles/3uIZzy)

4.[Seajs简易文档](http://yslove.net/seajs/)

RequireJS

1.[JS模块化工具requirejs教程](http://www.runoob.com/w3cnote/requirejs-tutorial-1.html)

AMD & CMD

1.[AMD 和 CMD 的区别有哪些？](http://www.zhihu.com/question/20351507/answer/14859415)

2.[SeaJS与RequireJS最大的区别](https://www.douban.com/note/283566440/)

3.[CommonJS，AMD，CMD区别](http://zccst.iteye.com/blog/2215317)

ES6

1.[ECMAScript 6 Module](http://es6.ruanyifeng.com/#docs/module)