---
layout: post
title: "典型正则表达式整理"
description: 典型正则表达式整理
modified: 2016-05-04
category: FrontEnd
tags: [FrontEnd]
---

# 1.创建

	new RegExp("regexp","g")

或者

	/regexp/g

# 2.字符串前后有空格

	/^\s+|\s+$/g

解释：

	\s： space， 空格；+： 一个或多个；^： 开始，^\s，以空格开始；$： 结束，\s$，以空格结束；|：或者；/g：global， 全局

# 3.一行数字

	/^\d+$/

解释：

	^和$用来匹配位置：^表示行首$表示行尾，\d表示数字，即0-9，+表示重复1次以上。
	综合起来，/^\d+$/这个正则表达式就是匹配一整行1个以上的数字。

# 4.URL中一个参数

	/\b([^&=]*)=([^&=]*)/g

解释：

	\b：单词边界，不参与匹配。[^&=]*：不为&=的0个或多个。

# 5.form表单

	/<form [\s\S]*<\/form>/g

解释：

	\s\S匹配空或非空字符，也就是所有字符。

# 6.限定长度数字

	/^\d{16,19}$/

解释：

	16-19位的数字

# 7.指定长度的小数

	/^\d{0,9}(?:\.\d{0,2})?$/

解释：

	整数部分最多九位，小数部分最多两位。

# 8.卡号格式

	eleCardNo.val(cardNo.replace(/\D/g, "").replace(/(\d{4})(?=\d)/g, '$1 '));

解释：

	只能输入数字，每四位一个空格。

# 9.金额

	 /^[0-9]*(\.[0-9]{2})$/

解释：

	小数点后两位。

# 10.四位验证码

	 /^[a-zA-Z0-9]{4}$/

解释：

	数字字母组成的四位验证码。

# 参考

1.[JavaScript RegExp 对象](http://www.w3school.com.cn/jsref/jsref_obj_regexp.asp)

2.[正则表达式语法](http://www.runoob.com/regexp/regexp-syntax.html)




	