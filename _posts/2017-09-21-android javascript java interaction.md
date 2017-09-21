---
layout: post
title: "Android中JavaScript与Java交互"
description: Android中JavaScript与Java交互
modified: 2017-09-21
category: Android
tags: [Android]
---

# 一、JavaScript与Java交互

activity_main.xml中有一个webview和一个button

	<?xml version="1.0" encoding="utf-8"?>
	<RelativeLayout
	    android:layout_width="fill_parent"
	    android:layout_height="fill_parent"
	    xmlns:android="http://schemas.android.com/apk/res/android">
	    <WebView
	        android:id="@+id/webview1"
	        android:layout_width="fill_parent"
	        android:layout_height="fill_parent"
	        android:layout_above="@+id/button1"/>
	    <Button
	        android:id="@+id/button1"
	        android:layout_width="200dip"
	        android:layout_height="40dip"
	        android:layout_alignParentBottom="true"
	        android:layout_centerHorizontal="true"
	        android:text="android调用html5方法"
	        android:textAllCaps="false"/>
	</RelativeLayout>

MainActivity.java中，jsKit绑定到JS的全局变量globalParams上，供JS调用。原生按钮button1会调用JS方法androidToHtml5。

	package cn.zhanghao90.demo1;

	import android.annotation.SuppressLint;
	import android.app.Activity;
	import android.os.Bundle;
	import android.os.Handler;
	import android.view.View;
	import android.view.View.OnClickListener;
	import android.webkit.WebChromeClient;
	import android.webkit.WebView;
	import android.widget.Button;

	@SuppressLint("SetJavaScriptEnabled")
	public class MainActivity extends Activity {

	    private WebView webview1;
	    private Button button1;
	    private JSKit jsKit;
	    private Handler mHandler = new Handler();

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	        //初始化控件
	        webview1 = (WebView) findViewById(R.id.webview1);
	        button1 = (Button) findViewById(R.id.button1);
	        //实例化jsKit对象
	        jsKit = new JSKit(this);

	        //把jsKit绑定到全局的globalParams上，globalParams的作用域是全局的，初始化后可随处使用
	        webview1.getSettings().setJavaScriptEnabled(true);
	        webview1.addJavascriptInterface(jsKit, "globalParams");
	        webview1.loadUrl("file:///android_asset/www/test.html");

	        //内容的渲染需要webviewChromeClient去实现，设置webviewChromeClient基类，解决js中alert不弹出的问题和其他内容渲染问题
	        webview1.setWebChromeClient(new WebChromeClient());
	        //android调用html5中JS方法
	        button1.setOnClickListener(new OnClickListener() {
	            @Override
	            public void onClick(View v) {
	                mHandler.post(new Runnable() {
	                    @Override
	                    public void run() {
	                        webview1.loadUrl("javascript:androidToHtml5()");
	                    }
	                });
	            }
	        });
	    }
	}

JSKit.java中定义两个供JS调用的方法，html5ToAndroid方法显示Toast，startNewActivity方法新开一个新的Activity，注意注解@JavascriptInterface


	package cn.zhanghao90.demo1;

	import android.content.Intent;
	import android.webkit.JavascriptInterface;
	import android.widget.Toast;

	public class JSKit {
	    private MainActivity ma;
	    public JSKit(MainActivity context) {
	        this.ma = context;
	    }

	    @JavascriptInterface
	    public void html5ToAndroid(String msg) {
	        Toast.makeText(ma, msg, Toast.LENGTH_SHORT).show();
	    }

	    @JavascriptInterface
	    public void startNewActivity() {
	        Intent intent = new Intent(ma,Main2Activity.class);
	        ma.startActivity(intent);
	    }
	}

res/www/test.html中两个按钮分别调用JSKit中定义的两个方法

	<!DOCTYPE html>
	<HTML>
	<HEAD>
	    <meta name="viewport" content="width=device-width, target-densitydpi=device-dpi" />
	    <META http-equiv="Content-Type" content="text/html; charset=UTF-8">
	    <script>
	   function androidToHtml5(){
	      alert("android调用html5方法");
	   }
	   function html5ToAndroid(){
	      globalParams.html5ToAndroid('html5调用android方法');
	   }
	   function startNewActivity(){
	      globalParams.startNewActivity();
	   }
	</script>
	</HEAD>
	<BODY>
	<button onclick='html5ToAndroid()'>html5调用android方法</button>
	<button onclick='startNewActivity()'>新开Activity</button>
	</BODY>
	</HTML>

# 二、参考

1.[android cordova混合开发（交互部分）](http://blog.csdn.net/u010819959/article/details/50608273)

2.[zhhgit/android_js_java_bridge](https://github.com/zhhgit/android_js_java_bridge)