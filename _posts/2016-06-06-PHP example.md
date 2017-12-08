---
layout: post
title: "PHP实例"
description: PHP实例
modified: 2016-06-06
category: PHP
tags: [PHP]
---

# 一、实例

数据库vote，表名tbl_paper，有3个字段：PAPER_INDEX, NAME, VOTE_NUM。mysqli现在基本取代了mysql。

	<?php
	/**
	 * Created by PhpStorm.
	 * User: ZhangHao
	 * Date: 2017/4/9
	 * Time: 13:43
	 */
	include_once "../dao/rankItem.php";

	header("Content-type: text/html; charset=utf8");
	$con = mysqli_connect("localhost","root","root","vote");
	if (mysqli_connect_errno($con)) {
	    $ret = json_encode([
	        "code" => "01",
	        "msg" => "获取排名失败",
	        "data" => []
	    ]);
	}
	else{
	    $result = mysqli_query($con,"SELECT PAPER_INDEX, NAME, VOTE_NUM FROM tbl_paper ORDER BY VOTE_NUM DESC");
	    $data = array();
	    while($row = mysqli_fetch_array($result,MYSQLI_ASSOC)) {
	        $paperItem = new paperItem();
	        $paperItem->paper_index = $row["PAPER_INDEX"];
	        $paperItem->name = $row["NAME"];
	        $paperItem->vote_num = $row["VOTE_NUM"];
	        array_push($data,$paperItem);
	    }
	    mysqli_close($con);

	    $ret = json_encode([
	        "code" => "00",
	        "msg" => "获取排名成功",
	        "data" => $data
	    ]);
	}

	echo $ret;

# 二、调试

修改C:\xampp\php目录下的php.ini，打开error_log功能，去掉下面这行的分号，修改后重启Apache。

	;error_log = php_errors.log

error_log会打印在C:\xampp\php\logs\php_error_log文件中。Windows下也用tail命令查看，并监视文件变化。参考[linux tail命令的使用方法详解](http://www.cnblogs.com/mfryf/p/3336804.html)和[Windows下tail 查看日志命令工具分享](http://www.cnblogs.com/hantianwei/archive/2012/03/14/2395634.html)。

	cd C:\xampp\php\logs
	tail -f php_error_log

在PHP的代码中，调用以下函数可以打印log信息。

	Util::log($params);

该静态方法的实现为

	class Util {
	    public static function log($msg){
	      if(!is_string($msg)) {
	          $msg = json_encode($msg);
	      }
	      error_log(__FILE__ . ' [log]: ' . $msg . ' on line ' . __LINE__);
	    }
	}

log时区可能不对，可以参考[PHP的php.ini时区设置问题 解决时间相差8小时问题](http://www.smsyun.com/home-index-page-id-169.html)，改完重启Apache。

# 三、参考

1.[php数据库操作实例](http://www.cnblogs.com/qiantuwuliang/archive/2009/11/11/1600896.html)

2.中文乱码问题参见[xampp/wamp集成环境安装后，如何修改mysql的默认编码格式的方法整理](http://blog.csdn.net/cselmu9/article/details/43150361)，my.ini文件要改成utf8，在PHP代码中也要设置成utf8，统一。