---
layout: post
title: "JS事件监听"
description: JS事件监听
modified: 2015-08-27
category: JavaScript
tags: [JavaScript]
featured: true
---

# 一、典型例子

addEventHandler和removeEventHandler分别对浏览器进行兼容。

	<!DOCTYPE html>
	<html>
	    <head>
	        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	        <title>Event</title>
	        <script type="text/javascript">
	            function addEventHandler(target,type,func){
	                if(target.addEventListener){
	                    //监听IE9，谷歌和火狐
	                    target.addEventListener(type, func, false);
	                }else if(target.attachEvent){
	                    target.attachEvent("on" + type, func);
	                }else{
	                    target["on" + type] = func;
	                } 
	            }
	            function removeEventHandler(target, type, func) {
	                if (target.removeEventListener){
	                    //监听IE9，谷歌和火狐
	                    target.removeEventListener(type, func, false);
	                } else if (target.detachEvent){
	                    target.detachEvent("on" + type, func);
	                }else {
	                    delete target["on" + type];
	                }
	            }
	            var eventOne = function(){
	                alert("第一个监听事件");
	            }
	            function eventTwo(){
	                alert("第二个监听事件");
	            }
	            window.onload = function(){
	                var bindEventBtn = document.getElementById("bindEvent");
	                //监听eventOne事件
	                addEventHandler(bindEventBtn,"click",eventOne);
	                //监听eventTwo事件
	                addEventHandler(bindEventBtn,"click",eventTwo );
	                //监听本身的事件
	                addEventHandler(bindEventBtn,"click",function(){
	                    alert("第三个监听事件");
	                });
	                //取消第一个监听事件
	                removeEventHandler(bindEventBtn,"click",eventOne);
	                //取消第二个监听事件
	                removeEventHandler(bindEventBtn,"click",eventTwo);
	            }
	        </script>
	    </head>
	    <body>
	        <input type="button" value="测试" id="bindEvent">
	    </body>
	</html>

# 二、参考

[js事件的监听器的使用](http://blog.csdn.net/myjlvzlp/article/details/8121696)