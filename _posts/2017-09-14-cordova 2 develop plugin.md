---
layout: post
title: "Cordova(1):开发Cordova Android插件"
description: Cordova(1):开发Cordova Android插件
modified: 2017-09-14
category: Cordova
tags: [Cordova]
---

# 一、开发Cordova Android插件

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

# 二、参考

1.[Android Plugin Development Guide](http://cordova.apache.org/docs/en/latest/guide/platforms/android/plugin.html)

2.[zhhgit/cordova_cli_android_demo](https://github.com/zhhgit/cordova_cli_android_demo)