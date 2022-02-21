---
layout: post
title: "面试题 -- 前端篇"
description: 面试题 -- 前端篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# JavaScript

1.apply&call&bind

传递参数并非apply()和call()真正的用武之地；它们真正强大的地方是能够扩充函数赖以运行的作用域。参考《JavaScript高级程序设计》P116。

(1)apply

apply()方法接收两个参数：一个是在其中运行函数的作用域，另一个是参数数组。

	function sum(num1, num2){
		return num1 + num2;
	}
	function callSum1(num1, num2){
		return sum.apply(this, arguments); // 传入arguments 对象
	}
	function callSum2(num1, num2){
		return sum.apply(this, [num1, num2]); // 传入数组
	}
	alert(callSum1(10,10)); //20
	alert(callSum2(10,10)); //20

(2)call

call()方法与apply()方法的作用相同，它们的区别仅在于接收参数的方式不同。对于call()方法而言，第一个参数是this 值没有变化，变化的是其余参数都直接传递给函数。

	function sum(num1, num2){
		return num1 + num2;
	}
	function callSum(num1, num2){
		return sum.call(this, num1, num2);
	}
	alert(callSum(10,10)); //20

(3)bind

[Javascript中bind()方法的使用与实现](https://segmentfault.com/a/1190000002662251)

[bind 方法 (Function) (JavaScript)](https://msdn.microsoft.com/zh-cn/library/ff841995)

2.JavaScript模块化

不同于C，Java等语言，当前浏览器广泛支持的JavaScript版本ECMAScript 5并没有类似于include，import这样的关键字来实现代码的模块化管理。但是随着前端代码的越发复杂和工程化，JS模块化编程已经成为一种迫切的需求。
JS模块化有助于前端代码的解耦与重用。下面分成如下三个部分介绍。

(1)常用模式

a.第一种常用的模式是通过一个构造函数，将一些可以被使用的公开方法暴露出去。例如commonUI.js为：

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

b.第二种常用的模式是通过匿名闭包，这种模式是当前前端代码中最常用的JS模块化的方式。通过匿名闭包的模式可以避免对全局的污染，将用到的变量限制在function作用域。如下的commonUI.js为一段典型的匿名闭包代码，一个匿名函数被定义后立即执行。在全局变量window下的子模块window.UP.W.UI中定义了showLoading(),showToast(),showAlert这三种常用的UI方法。

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

(2)模块规范

第一部分中实现JS的模块化主要依赖于JavaScript的语言特性。上述的模式，尤其是匿名闭包简单好用，但是也存在一定的问题。当需要引用的模块逐渐增多时，HTML代码中就要引入一堆的JS文件。而且这样的方式不易管理模块之间的依赖关系。
为了解决这样的问题，主要发展出了三种Javascript模块规范：CommonJS规范、AMD(Asynchronous Module Definition)规范、CMD(Common Module Definition)规范。

a.CommonJS规范

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

b.AMD规范

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

c.CMD规范

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

(3)ES6中的模块

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

(4)参考链接

常用模式：

[Javascript模块化编程（一）：模块的写法](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)

SeaJS：

[使用SeaJS实现模块化JavaScript开发](http://blog.codinglabs.org/articles/modularized-javascript-with-seajs.html)

[模块化JS编程 seajs简单例子](http://www.itnose.net/detail/6074887.html)

[快速上手seajs——简单易用Seajs](http://www.tuicool.com/articles/3uIZzy)

[Seajs简易文档](http://yslove.net/seajs/)

RequireJS：

[JS模块化工具requirejs教程](http://www.runoob.com/w3cnote/requirejs-tutorial-1.html)

AMD & CMD：

[AMD 和 CMD 的区别有哪些？](http://www.zhihu.com/question/20351507/answer/14859415)

[SeaJS与RequireJS最大的区别](https://www.douban.com/note/283566440/)

[CommonJS，AMD，CMD区别](http://zccst.iteye.com/blog/2215317)

ES6：

[ECMAScript 6 Module](http://es6.ruanyifeng.com/#docs/module)

3.JavaScript数字精度

(1)IEEE 754规范

《JavaScript权威指南》中的说法：和其他编程语言不同，JavaScript不区分整数值和浮点数。JavaScript中的所有数字均用浮点值表示。JavaScript采用IEEE 754标准定义的64位浮点格式表示数字。实数有无数个，但JavaScript通过浮点数的形式只能表示其中有限的个数。
也就是说，当在JavaScript中使用实数的时候，通常只是真实值的一个近似表示。IEEE 754二进制表示法，可以精确地表示分数，比如1/2,1/4等。遗憾的是，我们常用的分数（特别是金融计算方面）都是十进制分数1/10,1/100等。二进制浮点数表示法并不能精确表示类似0.1这样简单的数字。
这个问题不只在JavaScript中才会出现，理解这一点非常重要：在任何使用二进制浮点数的编程语言中都有这个问题。这种计算结果可以胜任绝大多数的计算任务，这个问题也只有在比较两个值是否相等的时候才会出现。

根据国际标准IEEE 754，任意一个二进制浮点数V可以表示成下面的形式，对于64位的浮点数，最高的1位是符号位S，接着的11位是指数E，剩下的52位为有效数字M。

    (-1)^s表示符号位，当s=0，V为正数；当s=1，V为负数。
    2^E表示指数位。
    M表示有效数字，大于等于1，小于2。

![ieee754](../images/jsNum/ieee754.png)

因为M可以写成1.xxxxxxxxx的形式，其中xxxxxxxxx表示小数部分。IEEE 754规定，在计算机内部保存M时，默认这个数的第一位总是1，因此可以被舍去，只保存后面的xxxxxxxxx部分。比如保存1.01的时候，只保存01，等到读取的时候，再把第一位的1加上去。

如果E为11位，它的取值范围为0~2047。但是科学计数法中的E是可以出现负数的，所以IEEE 754规定，E的真实值必须再减去一个中间数，对于11位的E，这个中间数是1023。比如，2^10的E是10，所以保存成64位浮点数时，必须保存成10+1023=1033，即10000001001。

指数E还可以再分成三种情况：

    (1)E不全为0或不全为1。这时，浮点数就采用上面的规则表示，即指数E的计算值减去1023，得到真实值，再将有效数字M前加上第一位的1；
    (2)E全为0。这时，浮点数的指数E等于1-1023，有效数字M不再加上第一位的1，而是还原为0.xxxxxxxxx的小数。这样做是为了表示±0，以及接近于0的很小的数字；
    (3)E全为1。这时，如果有效数字M全为0，表示±无穷大（正负取决于符号位s）；如果有效数字M不全为0，表示这个数不是一个数（NaN）。

(2)典型问题和解决办法

a.不稳定的toFixed()方法

NumberObject.toFixed(num)：num必需。规定小数的位数，是 0 ~ 20 之间的值，包括 0 和 20，有些实现可以支持更大的数值范围。如果省略了该参数，将用 0 代替。

并非期望中的四舍五入

![example1](../images/jsNum/example1.png)

解决思路：把小数乘以数倍，放大到整数，再缩小回原来精度。

	Number.prototype.toFixed = function( fractionDigits )  {
	    //没有对fractionDigits做任何处理，假设它是合法输入 
	    return (parseInt(this * Math.pow( 10, fractionDigits  ) + 0.5)/Math.pow(10,fractionDigts)).toString();  
	}

或者

	function round2(num,fractionDigits){
		return Math.round(num*Math.pow(10,fractionDigits))/Math.pow(10,fractionDigits); 
	}

b.浮点数比较

![example2](../images/jsNum/example2.png)

	0.1  ===>  0.0001  1001  1001  1001…（1001循环）
	0.2  ===>  0.0011  0011  0011  0011…（0011循环）

建议：(1)不要直接进行数值的比较；(2)放大到整数后处理；(3)使用Math对象的round()/floor()/ceil()等方法。

c.大数问题

尾数位最大是 52 位，因此 JS 中能精准表示的最大整数是 Math.pow(2, 53)，十进制即 9007199254740992，大于 9007199254740992 的可能会丢失精度。

![example3](../images/jsNum/example3.png)

	9007199254740992  	===>  10000000000000...000 // 共计 53 个 0
	9007199254740992 + 1  ===>  10000000000000...001 // 中间 52 个 0
	9007199254740992 + 2  ===>  10000000000000...010 // 中间 51 个 0
	9007199254740992 + 3  ===>  10000000000000...011 // 中间 51 个 0
	9007199254740992 + 4  ===>  10000000000000...100 // 中间 50 个 0

d.浮点数运算误差积累

parseInt()的输入要求是字符串，所以JavaScript对刚才那两个计算结果做toString()类型转换操作。并且，当parseInt发现有字符串中有一个字母不是数字，就把这个字母和后面的都丢弃。

![example4](../images/jsNum/example4.png)

可以用Math.round(1097.12*100)

(3)参考

[JavaScript数字精度丢失问题总结](https://www.cnblogs.com/snandy/p/4943138.html)

[程序员必知之浮点数运算原理详解](http://blog.csdn.net/tercel_zhang/article/details/52537726)

4.JavaScript原型

(1)例子

	function Base(name,age) {
	    this.name = name;
	    this.age = age;
	    this.type = "Base";
	    this.sayHello = function () {
	        console.log("Hello " + this.name);
	    };
	}
	Base.prototype.sayName = function () {
	    console.log("My name is " + this.name);
	};
	baseObj = new Base("zhanghao1",27);

	console.log("----------------Base-----------------");
	console.log(Base);

	console.log("-----------------Base.prototype----------------");
	console.log(Base.prototype);

	console.log("----------------Base.__proto__-----------------");
	console.log(Base.__proto__);

	console.log("----------------baseObj-----------------");
	console.log(baseObj);

	console.log("----------------baseObj.prototype-----------------");
	console.log(baseObj.prototype);

	console.log("----------------baseObj.__proto__-----------------");
	console.log(baseObj.__proto__);

显示如下：

<img src="../images/prototype/demo.jpg" class="post-img"/>

(2)分析

Base是函数对象，所以输出的是一个函数。Base.prototype是Base的原型，该对象有三个属性。constructor指向Base函数对象。sayName属性是动态添加的。__proto__指向Object.prototype。因为函数对象也是一个对象。Base.__proto__为Function.prototype，是Function这个函数对象的原型。baseObj为new出来的Base实例，拥有构造函数中的属性和方法。当调用sayName方法时，在属性中无法找到，就去__proto__中找，所谓“原型链”。baseObj.prototype为undefined，因为baseObj不是函数对象，所以没有prototype。baseObj.__proto__保存的是构造函数的原型，也就是Base.prototype。

借用两张图和比较经典的总结

<img src="../images/prototype/1.png" class="post-img"/>

<img src="../images/prototype/2.png" class="post-img"/>

    1.所有的对象都有"\_\_proto\_\_"属性，该属性对应该对象的原型。
    2.所有的函数对象都有"prototype"属性，该属性的值会被赋值给该函数创建的对象的"\_\_proto\_\_"属性。
    3.所有的原型对象都有"constructor"属性，该属性对应创建所有指向该原型的实例的构造函数。
    4.函数对象和原型对象通过"prototype"和"constructor"属性进行相互关联。

(3)参考

[彻底理解JavaScript原型](http://blog.csdn.net/wxw_317/article/details/49617767)

# 跨域

1.Chrome开启跨域模式

Win10：新建ChromeData目录，在chrome快捷方式，目标里如下设置

    C:\Users\xyzq\AppData\Local\Google\Chrome\Application\chrome.exe --disable-web-security --user-data-dir=C:\Users\xyzq\AppData\Local\Google\ChromeData

2.解决方法

(1)[JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)

(2)[js中几种实用的跨域方法原理详解](http://www.cnblogs.com/2050/p/3191744.html)

(3)[jsonp](http://baike.baidu.com/link?url=vHvnmBChdWRbahcsoHl-RXzfRUg1VU1YXMqqySm4pstfUHJnHeU15IwQUxNgltn_cbroiwtKxQoRFHwBayfQAa)

(4)[JSONP 教程](http://www.runoob.com/json/json-jsonp.html)

(5)[AJAX 跨域请求 - JSONP获取JSON数据](http://justcoding.iteye.com/blog/1366102/)

# 正则表达式

1.典型正则

    // 创建
    new RegExp("regexp","g")
    // 或者
    /regexp/g

    // 字符串前后有空格
    // 解释：\s： space， 空格；+： 一个或多个；^： 开始，^\s，以空格开始；$： 结束，\s$，以空格结束；|：或者；/g：global， 全局
    /^\s+|\s+$/g

    // 一行数字
    // 解释：^和$用来匹配位置：^表示行首$表示行尾，\d表示数字，即0-9，+表示重复1次以上。综合起来，/^\d+$/这个正则表达式就是匹配一整行1个以上的数字。
	/^\d+$/

    // URL中一个参数
    // 解释：\b：单词边界，不参与匹配。[^&=]*：不为&=的0个或多个。
	/\b([^&=]*)=([^&=]*)/g

    // form表单
    // 解释：\s\S匹配空或非空字符，也就是所有字符。
	/<form [\s\S]*<\/form>/g
    
    // 限定长度数字
    // 解释：16-19位的数字
	/^\d{16,19}$/

    // 指定长度的小数
    // 解释：整数部分最多九位，小数部分最多两位。
	/^\d{0,9}(?:\.\d{0,2})?$/

    // 卡号格式
    // 解释：只能输入数字，每四位一个空格。
	eleCardNo.val(cardNo.replace(/\D/g, "").replace(/(\d{4})(?=\d)/g, '$1 '));
    
    // 金额
    // 解释：小数点后两位。
    /^[0-9]*(\.[0-9]{2})$/

    // 四位验证码
    // 解释：数字字母组成的四位验证码。
    /^[a-zA-Z0-9]{4}$/

2.参考

(1)[JavaScript RegExp 对象](http://www.w3school.com.cn/jsref/jsref_obj_regexp.asp)

(2)[正则表达式语法](http://www.runoob.com/regexp/regexp-syntax.html)

# WebSocket

1.浏览器与微信小程序的跨端聊天室，如图。

![demo](../images/websocket/demo.gif)

(1)浏览器WebSocket

client.html:

    <!DOCTYPE html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0"/>
        <title>WebSocket Demo</title>
        <link type="text/css" href="./client.css" rel="stylesheet" />
    </head>
    <body>
        <div class="pageContainer">
            <div class="topArea" id="topArea">
            </div>
            <div class="bottomArea">
                <div class="inputArea">
                    <input type="text" placeholder="连接服务器中..." class="message" id="inputMessage"/>
                </div>
                <button class="sendButton" id="sendButton">发送</button>
            </div>
        </div>
        <script type="text/javascript" src="./zepto.min.js"></script>
        <script type="text/javascript" src="client.js"></script>
    </body>
    </html>

client.js:

    (function($){
        console.log("将要连接服务器。");
        var wsUri ="ws://localhost:8080/";
        var socketOpen = false;
        var messageArray = [];
        websocket = new WebSocket(wsUri);
        websocket.onopen = function(res) {
            console.log("连接服务器成功。");
            $("#inputMessage").attr("placeholder","连接服务器成功，请输入姓名。");
            socketOpen = true;
        };
        websocket.onclose = function(res) {
            console.log("断开连接。");

        };
        websocket.onmessage = function(res) {
            console.log('收到服务器内容：' + res.data);
            var data = res.data;
            var dataArray = data.split("_");
            var newMessage = {
                type:dataArray[0],
                name:dataArray[1],
                time:dataArray[2],
                message:dataArray[3]
            };
            var newArray = messageArray.concat(newMessage);
            messageArray = newArray;
            reBuild(messageArray);
        };

        websocket.onerror = function(res) {
            console.log("连接错误。");
        };

        $("#sendButton").on("click",function () {
            var message = $("#inputMessage").val();
            if(message!=''){
                $("#inputMessage").attr("placeholder","请输入信息");
                sendSocketMessage(message);
                $("#inputMessage").val("");
            }
        });

        function sendSocketMessage(message) {
            if(socketOpen){
                websocket.send(message);
            }
        }

        function reBuild(messageArray) {
            $("#topArea").empty();
            var htmlDom = "";
            for(var i in messageArray){
                if(messageArray[i].type == 'self'){
                    htmlDom+='<div class="selfMessage"><div class="nameInfo">'
                        + messageArray[i].name+ " "
                        + messageArray[i].time
                        + '</div><div class="detailMessage">'
                        + messageArray[i].message
                        + '</div></div>';
                }
                else{
                    htmlDom+='<div class="otherMessage"><div class="nameInfo">'
                        + messageArray[i].name+ " "
                        + messageArray[i].time
                        + '</div><div class="detailMessage">'
                        + messageArray[i].message
                        + '</div></div>';
                }
            }
            htmlDom+='<div class="clear"></div>'
            $("#topArea").append(htmlDom);
        }

    })(Zepto);

(2)微信小程序WebSocket

index.wxml:

    <view class="pageContainer">
        <view class="topArea">
            <view wx:for="{ {messageArray} }" wx:for-index="idx" wx:for-item="itemName">
                <view class="selfMessage" wx:if="{ {itemName.type == 'self'} }">
                    <view class="nameInfo">{ {itemName.name+ " " + itemName.time} }</view>
                    <view class="detailMessage">{ {itemName.message} }</view>
                </view>
                <view class="otherMessage" wx:else>
                    <view class="nameInfo">{ {itemName.name+ " " + itemName.time} }</view>
                    <view class="detailMessage">{ {itemName.message} }</view>
                </view>
                <view class="clear"></view>
            </view>
        </view>
        <view class="bottomArea">
            <form bindreset="send">
                <view class="inputArea">
                    <input type="text" placeholder="{ {placeholderText} }" class="message" bindinput="bindKeyInput"/>
                </view>
                <button size="default" type="primary" form-type="reset" class="sendButton">发送</button>
            </form>
        </view>
    </view>

index.js:

    Page({
        data: {
            placeholderText:"连接服务器中...",
            messageArray:[],
            socketOpen:false,
            inputValue:""
        },
        onLoad: function(options) {
            var self = this;
            console.log("将要连接服务器。");
            wx.connectSocket({
                url: 'ws://localhost:8080'
            });

            wx.onSocketOpen(function(res) {
                console.log("连接服务器成功。");
                self.setData({
                    placeholderText:"连接服务器成功，请输入姓名。",
                    socketOpen:true
                });
            });

            wx.onSocketMessage(function(res) {
                console.log('收到服务器内容：' + res.data);
                var data = res.data;
                var dataArray = data.split("_");
                var newMessage = {
                    type:dataArray[0],
                    name:dataArray[1],
                    time:dataArray[2],
                    message:dataArray[3]
                };
                var newArray = self.data.messageArray.concat(newMessage);
                self.setData({
                    messageArray:newArray,
                    placeholderText:"请输入信息"
                });
            });
        },

        onUnload: function() {
            wx.closeSocket();
        },

        bindKeyInput: function(e) {
            this.setData({
                inputValue: e.detail.value
            });
        },

        send: function() {
            if(this.data.inputValue!=""){
                this.sendSocketMessage(this.data.inputValue);
                this.setData({
                    inputValue:""
                });
            }
        },

        sendSocketMessage:function (msg){
            if (this.data.socketOpen) {
                wx.sendSocketMessage({
                    data:msg
                })
            }
        }
    });

(3)服务端

WebSocket服务端基于[ws](https://github.com/websockets/ws)库实现，server.js:

    const WebSocket = require('ws');
    const util = require("util");
    const wss = new WebSocket.Server({ port: 8080 });
    console.log("WebSocket服务端已启动。");

    wss.on('connection', function connection(socket) {
        console.log("有新客户端连接!");

        // 构造客户端对象
        var newclient = {
            socket:socket,
            name:false
        };

        socket.on('message', function incoming(msg) {
            var currentTime = getTime();
            // 判断是不是第一次连接，以第一条消息作为用户名
            if(!newclient.name){
                newclient.name = msg;
                wss.clients.forEach(function each(client) {
                    if (client.readyState === WebSocket.OPEN) {
                        client.send("welcome_系统管理员_" + currentTime + "_欢迎" + msg + "加入聊天！");
                    }
                });
                console.log(newclient.name + "加入聊天。");
            }
            else{
                wss.clients.forEach(function each(client) {
                    if (client !== socket && client.readyState === WebSocket.OPEN) {
                        client.send("other_" + newclient.name + "_" + currentTime + "_" + msg);
                    }
                    else if(client == socket){
                        client.send("self_" + newclient.name + "_" + currentTime + "_" + msg);
                    }
                    console.log(newclient.name + "于" + currentTime + "说：" + msg);
                });
            }
        });

        socket.on('close', function close() {
            var currentTime = getTime();
            wss.clients.forEach(function each(client) {
                if (client.readyState === WebSocket.OPEN) {
                    client.send("leave_系统管理员_" + currentTime + "_" + newclient.name + "离开聊天！");
                }
                console.log(newclient.name + "离开聊天。");
            });
        });
    });

    var getTime=function(){
      var date = new Date();
      return date.getHours()+":"+date.getMinutes()+":"+date.getSeconds();
    };

2.参考

(1)[ws](https://github.com/websockets/ws)

(2)[Socket.io在线聊天室](http://blog.fens.me/nodejs-socketio-chat/)

(3)[socket.io](https://github.com/socketio/socket.io)

(4)[weapp-demo-websocket](https://github.com/CFETeam/weapp-demo-websocket)

(5)[刨根问底HTTP和WebSocket协议](http://www.jianshu.com/p/0e5b946880b4)

# 浏览器缓存

1.缓存机制

浏览器缓存主要包含两类：

(1)缓存协商：Last-modified,Etag

这两种都需要跟cache-control配合使用，就是当max-age过期了，再判断这两个。

Last-modified/if-modified-since 是一个精确到秒的时间，浏览器向服务器询问页面是否被修改过（带上这个头信息），如果没有修改就返回304，浏览器直接浏览本地缓存文件。否则服务器返回新内容。

Etag/if-none-matched 差不多，只不过etag是由索引节(Inode)，大小(size)，最后修改时间(MTime)计算得到的hash值 。

(2)彻底缓存：cache-control,Expires

通过Expires设置缓存失效时间，值类似于2015-09-02 11:00:00，通过与当前的date比较来判断是否重新请求，在失效之前不需要再跟服务器请求交互。是http1.0的。

cache-control包括多种值，比如max-age=300，单位为秒。是http1.1标准，会覆盖Expires。

附两个很好的图如下

首次联网：

![Pic1](../images/cache/fig1.png)

再次联网：

![Pic2](../images/cache/fig2.png)

2.存储形式

通过

	chrome://version/

来查看个人资料路径在

	C:\Users\Zhang Hao\AppData\Local\Google\Chrome\User Data\Default

在其中的./cache文件夹中就是一系列的缓存后的文件，文件名已经被编号。

也可以用以下命令可以查看缓存的文件

	chrome://appcache-internals

	chrome://cache

缓存后的文件包括三个部分，从上到下依次是：文件路径、头信息、编码后的文档。

3.服务器配置

Expires和Cache-control：启用Apache的mod_expires模块并在httpd.conf文件中做相应配置

[Etag和Expires性能调优](http://www.jb51.net/article/33214.htm)

[Apache增加mod_expires模块+配置指南](http://blog.it985.com/910.html)

[apache中mod_expires](http://blog.csdn.net/foreverme/article/details/7946915)

ETag：在目录中添加.htaccess文件

[关于ETAG的使用](http://blog.sina.com.cn/s/blog_7f2122c50100v0nz.html)

[.htaccess文件如何创建？](http://jingyan.baidu.com/album/e9fb46e1aaaf1c7521f766dd.html)

4.参考

(1)[浏览器缓存机制](http://www.cnblogs.com/skynet/archive/2012/11/28/2792503.html)

(2)[浏览器缓存详解:expires,cache-control,last-modified,etag详细说明](http://blog.csdn.net/eroswang/article/details/8302191)

(3)[查看Chrome浏览器缓存的方法](http://my.oschina.net/xiaojichao/blog/137538)

(4)[缓存Cache详解](http://www.cnblogs.com/futan/archive/2013/04/21/cachehuancun.html)

(5)[作为前端应当了解的Web缓存知识](https://yq.aliyun.com/articles/50624?&utm_campaign=sys&utm_medium=market&utm_source=edm_email&msctype=email&mscmsgid=119416052600117638&)

(6)[Apache中关于页面缓存的设置](http://www.cnblogs.com/yyyyy5101/articles/1899350.html)

(7)[在php编程中使用header()函数发送文件头，设置浏览器缓存，加快站点的访问速度](http://www.lampweb.org/seo/4/11.html)

# 打包工具

1.Grunt

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

参考：

[grunt整合版,30分钟学会使用grunt打包前端代码](http://www.cnblogs.com/yexiaochai/p/3603389.html)

[前端工程的构建工具对比 Gulp vs Grunt](https://segmentfault.com/a/1190000002491282)

2.Gulp

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

参考：

[使用Gulp构建本地开发Web服务器](http://www.tuicool.com/articles/qUvyEj)

# 其他

1.Github Pages静态blog

(1)jekyll安装

依次安装Ruby、Ruby DevKit、Jekyll。_config.yml修改如下：

	port:        4000
	baseurl:     /
	url:         http://localhost:4000

然后启动jekyll:

	jekyll serve -w

Github Pages需要注意仓库分支为gh-pages，才可以访问。同步时记得修改配置文件_config.yml到自己的网址。

(2)参考

[jekyll主页](http://jekyll.bootcss.com/)

