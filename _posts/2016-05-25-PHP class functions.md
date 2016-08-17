---
layout: post
title: "PHP类方法"
description: PHP类方法
modified: 2016-05-25
category: PHP
tags: [PHP]
featured: true
---

	class Token {

		//公有静态方法A
	    public static function A(){
	    	$ret= self::C();
	    	return $ret;
	    }
	    //公有方法B
	    public function B(){
	    	$ret= self::C();
	    	return $ret;
	    }
	    //私有静态方法C
	    private static function C(){
	    	//details
	    }
	}
	//调用类Token中的静态方法A
	$ret1=Token::A();
	//先生成实例$token，再调用实例的方法B
	$token=new Token();
	$ret2=$token->B();