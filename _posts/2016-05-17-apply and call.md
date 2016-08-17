---
layout: post
title: "apply与call"
description: apply与call
modified: 2016-05-17
category: JavaScript
tags: [JavaScript]
featured: true
---

传递参数并非apply()和call()真正的用武之地；它们真正强大的地方是能够扩充函数赖以运行的作用域。参考《JavaScript高级程序设计》P116。

# apply

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

# call

call()方法与apply()方法的作用相同，它们的区别仅在于接收参数的方式不同。对于call()方法而言，第一个参数是this 值没有变化，变化的是其余参数都直接传递给函数。

	function sum(num1, num2){
		return num1 + num2;
	}
	function callSum(num1, num2){
		return sum.call(this, num1, num2);
	}
	alert(callSum(10,10)); //20



