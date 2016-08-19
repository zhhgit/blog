---
layout: post
title: "PHP MySQL实例"
description: PHP MySQL实例
modified: 2016-06-06
category: PHP
tags: [PHP]
---

# 一、实例

先手动新建了数据库test，表名为user，分别有7个字段：id userid sex age tel email address。

	<!DOCTYPE html>
	<html>
	<head>
	    <title>TEST</title>
	</head>

	<body>
	<table cellspacing=0 bordercolordark=#FFFFFF width="95%" bordercolorlight=#000000 border=1 align="center" cellpadding="2">
	    <caption style="padding-bottom:10px">Table</caption>
	    <tr bgcolor="#6b8ba8" style="color:FFFFFF">
	        <td align="center" valign="bottom">ID</td>
	        <td align="center" valign="bottom">姓名</td>
	        <td align="center" valign="bottom">性别</td>
	        <td align="center" valign="bottom">年龄</td>
	        <td align="center" valign="bottom">联系电话</td>
	        <td align="center" valign="bottom">电子邮件</td>
	        <td align="center" valign="bottom">家庭住址</td>
	    </tr>
	<?php
	    header("Content-type: text/html; charset=utf8");
	    //连接到本地mysql数据库，地址为localhost，用户名为root，密码暂时未设，缺省
	    $myconn=mysql_connect("localhost","root");
	    //指定数据库字符集，一般放在连接数据库后面
	    mysql_query("set names 'utf8'");
	    //选择test数据库
	    mysql_select_db("test",$myconn);
	    //删除一条记录
	    // mysql_query("DELETE FROM user WHERE id='1'");
	    //增加一条记录
	    $addSql="INSERT INTO user (id, userid, sex, age, tel, email, address) VALUES ('1', 'Zhh', '男', '26', '15100000000', 'xxx@126.com', 'Shanghai')";
	    mysql_query($addSql,$myconn); 
	    //用mysql_query函数从user表里读取数据
	    $strSql="select * from user";
	    $result=mysql_query($strSql,$myconn);
	    //通过循环读取数据内容
	    echo $result;
	    while($row=mysql_fetch_array($result)){
	?>
	<tr>
	    <td align="center"><?php echo $row["id"]?></td>
	    <td align="center"><?php echo $row["userid"]?></td>
	    <td align="center"><?php echo $row["sex"]?></td>
	    <td align="center"><?php echo $row["age"]?></td>
	    <td align="center"><?php echo $row["tel"]?></td>
	    <td align="center"><?php echo $row["email"]?></td>
	    <td align="center"><?php echo $row["address"]?></td>
	</tr>
	<?php
	    }
	    //关闭对数据库的连接
	    mysql_close($myconn);
	?>
	</table>
	</body>
	</html>

# 二、参考

1.[php数据库操作实例](http://www.cnblogs.com/qiantuwuliang/archive/2009/11/11/1600896.html)

2.中文乱码问题参见[xampp/wamp集成环境安装后，如何修改mysql的默认编码格式的方法整理](http://blog.csdn.net/cselmu9/article/details/43150361)，my.ini文件要改成utf8，在PHP代码中也要设置成utf8，统一。