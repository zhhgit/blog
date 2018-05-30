---
layout: post
title: "使用Cordova"
description: 使用Cordova
modified: 2017-09-12
category: Cordova
tags: [Cordova]
---

工作中使用Cordova已久，但是一直集中于：与客户端同事定好Cordova插件名和方法 ==》 封装JS插件 ==》 使用JS插件，理解并不深刻。新开Cordova系列，争取弄明白Android上Cordova的原理吧。

按照官网说法，Cordova提供两个基本的工作流用来创建移动App。跨平台(CLI)的工作流，和平台为中心的工作流。

“跨平台(CLI)的工作流:如果你想你的App运行在尽可能多的移动操作系统，那么就使用这个工作流，你只需要很少的特定平台开发。 这个工作流围绕这'cordova'CLI(命令行)。CLI是一个高级别的工具，他允许一次构建多个平台的项目，抽象了很多功能性的低级别shell脚本。CLI把公用的web资源复制到每个移动平台的子目录，根据每个平台做必要的配置变化，运行构建脚本生成2进制文件。CLI统一也提供通用接口，将插件应用在app中。如果要入门可以按照 创建你的第一个App指南中的步骤来 。除非你有一个以平台为中心的工作流，否则建议你使用跨平台工作流。”

# 一、使用Cordova CLI

安装Node，安装Cordova

    npm install -g cordova
    
创建项目

    cordova create MyApp
    
添加平台
    
    cd MyApp
    cordova platform add android
    
检查你当前平台设置状况

    cordova platform ls
    
MyApp/platform目录官方建议不要改动里面的内容。检测是否满足构建平台的要求

    cordova requirements

Android平台会检查Java JDK，Android SDK，Android target，Gradle这几个的安装情况。通常装好Android Studio并能正常build、run项目，前三个就已经装好。Gradle需要添加系统环境变量path，比如：

    C:\Users\zhanghao1\.gradle\wrapper\dists\gradle-3.3-all\55gk2rcmfc6p2dg9u9ohc3hw9\gradle-3.3\bin

修改MyApp/www/目录下的前端代码，按照官网描述CLI可以把公用的web资源复制到每个移动平台的子目录。运行Android打包，手机连接状态可以直接安装。

    cordova run android

# 二、使用插件

核心插件API的用法看起来很明确，直接参照文档上每个插件的用法，安装例如

    cordova plugin add cordova-plugin-battery-status

然后修改www目录中的JS代码，重新执行cordova run android

# 三、开发Cordova Android插件

前端JS中调用Cordova插件的形式是

    exec(<successFunction>, <failFunction>, <service>, <action>, [<args>]);

几个参数分别为成功回调、失败回调、插件名，插件方法，参数。所以一个新的Echo插件在JS中的调用为

    cordova.exec(function(resp) {
                    alert("success");
                    alert(resp);
                },
                function(resp) {
                    alert("fail");
                    alert(resp);
                },
                "Echo",
                "echo",
                ["hehe"]);

当使用Cordova CLI时，执行cordova run android命令会同步www文件夹中的前端代码到platforms/android/assets/www目录。

按照官网的说法，[Plugin Development Guide](http://cordova.apache.org/docs/en/latest/guide/hybrid/plugins/index.html)，添加一个插件是需要写一个plugin.xml文件的，当执行cordova run android时，会将其中feature标签中的内容同步到Android工程的res/xml/config.xml文件中。

    <feature name="<service_name>">
        <param name="android-package" value="<full_name_including_namespace>" />
    </feature>

对于Echo插件，就是插入了一条

    <feature name="Echo">
            <param name="android-package" value="org.apache.cordova.echo.Echo"/>
    </feature>

新建src\org\apache\cordova\echo\Echo.java如下

    package org.apache.cordova.echo;

    import org.apache.cordova.CallbackContext;
    import org.apache.cordova.CordovaPlugin;
    import org.json.JSONArray;
    import org.json.JSONException;
    /**
     * This class echoes a string called from JavaScript.
     */
    public class Echo extends CordovaPlugin {

        @Override
        public boolean execute(String action, JSONArray args, CallbackContext callbackContext) throws JSONException {
            if (action.equals("echo")) {
                String message = args.getString(0);
                this.echo(message, callbackContext);
                return true;
            }
            return false;
        }

        private void echo(String message, CallbackContext callbackContext) {
            if (message != null && message.length() > 0) {
                callbackContext.success(message);
            } else {
                callbackContext.error("Expected one non-empty string argument.");
            }
        }
    }

直接使用Android Studio来build和安装，就可以调用Echo插件。

# 四、Android整合Cordova

1.通过Cordova CLI下载的项目中，/platforms/android项目目录中有CordovaLib目录。新建的Android工程，通过File--New--Import Module导入CordovaLib，会将整个CordovaLib目录自动拷贝过来。再添加依赖，工程右键--Open Module Settings--app--Dependancies--添加Module Dependancy--选择CordovaLib。

2.自定义插件ZHToast继承CordovaPlugin，ZHToast是插件名，getToast是插件方法。

    package cn.zhanghao90.demo1;

    import android.widget.Toast;
    import org.apache.cordova.CallbackContext;
    import org.apache.cordova.CordovaPlugin;
    import org.json.JSONArray;
    import org.json.JSONException;

    public class ZHToast extends CordovaPlugin {
        @Override
        public boolean execute(String action, JSONArray args, CallbackContext callbackContext) throws JSONException {
            if("getToast".equals(action)){
                showToast(args.getString(0),args.getInt(1));
            }
            return true;
        }

        private void showToast(String content, int type){
            Toast.makeText(this.cordova.getActivity(),content,type).show();
        }
    }

3.添加res/xml目录，添加config.xml文件。主要是feature标签中定义插件，value是全类名。content标签指定了顶级Web目录中起始页面，默认为index.html，通常在顶级Web目录的www目录中。

    <?xml version='1.0' encoding='utf-8'?>
    <widget id="cn.zhanghao90.demo1" version="1.0.0" xmlns="http://www.w3.org/ns/widgets" xmlns:cdv="http://cordova.apache.org/ns/1.0">
        <feature name="ZHToast">
            <param name="android-package" value="cn.zhanghao90.demo1.ZHToast"/>
        </feature>
        <name>Demo1</name>
        <description>
            Demo1
        </description>
        <author email="zhh900601@sina.com" href="http://zhanghao90.cn">
            zhanghao
        </author>
        <content src="index.html" />
        <access origin="*" />
        <allow-intent href="http://*/*" />
        <allow-intent href="https://*/*" />
        <allow-intent href="tel:*" />
        <allow-intent href="sms:*" />
        <allow-intent href="mailto:*" />
        <allow-intent href="geo:*" />
        <allow-intent href="market:*" />
        <preference name="loglevel" value="DEBUG" />
    </widget>

4.添加assets/www目录，其中放入前端代码，记得js中放入一个cordova.js。JS中插件调用形式还是如下

    cordova.exec(function(resp) {
                    alert("success");
                    alert(resp);
                },
                function(resp) {
                    alert("fail");
                    alert(resp);
                },
                "ZHToast",
                "getToast",
                ["this is a test",0]
    );

5.修改MainActivity继承CordovaActivity。

    package cn.zhanghao90.demo1;

    import android.os.Bundle;
    import org.apache.cordova.*;

    public class MainActivity extends CordovaActivity
    {
        @Override
        public void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            // Set by <content src="index.html" /> in config.xml
            loadUrl(launchUrl);
        }
    }

6.修改AndroidManifest.xml

    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="cn.zhanghao90.demo1">

        <application
            android:hardwareAccelerated="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name">
            <activity
                android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale"
                android:label="@string/activity_name"
                android:launchMode="singleTop"
                android:name="MainActivity"
                android:theme="@android:style/Theme.DeviceDefault.NoActionBar"
                android:windowSoftInputMode="adjustResize">
                <intent-filter android:label="@string/launcher_name">
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
        </application>

    </manifest>

7.直接使用Android Studio来build和安装。

# 五、JavaScript与Java交互

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


# 六、参考

1.[Cordova官网](http://cordova.apache.org/)

2.[Cordova中文网](http://cordova.axuer.com/)

3.[Android Plugin Development Guide](http://cordova.apache.org/docs/en/latest/guide/platforms/android/plugin.html)

4.[zhhgit/cordova_cli_android_demo](https://github.com/zhhgit/cordova_cli_android_demo)

5.[Android项目里集成Cordova详解](http://blog.csdn.net/u013491677/article/details/51985390)

6.[zhhgit/cordova_android_integration](https://github.com/zhhgit/cordova_android_integration)

7.[android cordova混合开发（交互部分）](http://blog.csdn.net/u010819959/article/details/50608273)

8.[zhhgit/android_js_java_bridge](https://github.com/zhhgit/android_js_java_bridge)