---
layout: post
title: "Cordova(3):Android整合Cordova"
description: Cordova(3):Android整合Cordova
modified: 2017-09-20
category: Cordova
tags: [Cordova]
---

# 一、Android整合Cordova

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

5.直接使用Android Studio来build和安装。

# 二、参考

1.[Android项目里集成Cordova详解](http://blog.csdn.net/u013491677/article/details/51985390)

2.[zhhgit/cordova_android_integration](https://github.com/zhhgit/cordova_android_integration)