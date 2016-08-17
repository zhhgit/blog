---
layout: post
title: "JS中的字符串的连接、分割、替换"
description: JS中的字符串的连接、分割、替换
modified: 2015-08-05
category: JavaScript
tags: [JavaScript]
featured: true
---

# 一、字符串连接

使用join()函数，默认用逗号，括号中带参数时候，就用相应的字符连接。

例子：

	<html>
	<body>
	<script type="text/javascript">
	var arr = new Array(3);
	arr[0] = "George"
	arr[1] = "John"
	arr[2] = "Thomas"
	document.write(arr.join());
	document.write("<br />");
	document.write(arr.join("."));
	</script>
	</body>
	</html>

输出应该是

	George,John,Thomas
	George.John.Thomas

# 二、字符串分割

函数原型：

	stringObject.split(separator,howmany)

例子：

	<html>
	<body>
	<script type="text/javascript">
	var str="How are you doing today?"
	document.write(str.split(" ") + "<br />")
	document.write(str.split("") + "<br />")
	document.write(str.split(" ",3))
	</script>
	</body>
	</html>

返回一个字符串数组，括号中第二个参数表示返回的个数

	How,are,you,doing,today?
	H,o,w, ,a,r,e, ,y,o,u, ,d,o,i,n,g, ,t,o,d,a,y,?
	How,are,you

# 三、替换字符串

函数原型：

	stringObject.replace(regexp/substr,replacement)

例子：

	<html>
	<body>
	<script type="text/javascript">
	var str="Visit Microsoft!"
	document.write(str.replace(/Microsoft/,"W3School"))
	</script>
	</body>
	</html>

只替换第一个匹配的，要想所有匹配的都替换，加上/g

	<html>
	<body>
	<script type="text/javascript">
	var str="Welcome to Microsoft! ";
	str=str + "We are proud to announce that Microsoft has ";
	str=str + "one of the largest Web Developers sites in the world.";
	document.write(str.replace(/Microsoft/g, "W3School"));
	</script>
	</body>
	</html>

特殊的，如果是要替换全部+号，要用\+

	str.replace(/\+/g," ");






