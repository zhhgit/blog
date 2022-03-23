---
layout: post
title: "React基础"
description: React基础
modified: 2020-12-31
category: FrontEnd
tags: [FrontEnd]
---

# 一、使用方式

1.安装Node v14.18.1，通过create-react-app初始化一个结合了Webpack和ES6的React工程。

    npm install -g npx
    npx create-react-app my-app
    // Starts the development server.
    cd my-app
    npm start
    // Bundles the app into static files for production.
    npm run build
    // Starts the test runner.
    npm test
    // Removes this tool and copies build dependencies, configuration files,and scripts into the app directory. If you do this, you can’t go back!
    npm run eject

2.安装React-devtools

    git clone https://github.com/facebook/react-devtools.git
    cd react-devtools
    git checkout v3
    npm --registry https://registry.npm.taobao.org install
    npm run build:extension:chrome
    cd shells/chrome/build/unpacked
    然后Chrome“加载已解压的扩展程序”

# 二、React组件

1.简单组件

SimpleComponent.js:

    import React, { Component } from 'react';
    import AnotherComponent from './AnotherComponent.js';

    export default class SimpleComponent extends Component {
        constructor(props) {
            super(props);
            this.state = {
                name: "Zhang Hao",
            };
        }
        
        componentDidMount(){
            $.ajax({
                type: 'GET',
                url: "http://zhanghao90.top/interface1",
                contentType: "application/json; charset=utf-8",
                data: {},
                dataType: 'json',
                success: function (resp) {
                    this.setState({
                        name: resp
                    });
                },
                error: function(err) {}
            });
        }
        
        handleClick = () => {
        
        }

        render() {
            return (
                <div>
                    {this.state.name}
                    <AnotherComponent />
                </div>
            )
        }
    }

N.参考

(1)[React菜鸟教程](http://www.runoob.com/react/react-tutorial.html)

(2)[React官网](https://zh-hans.reactjs.org/)

(3)[React 入门实例教程](http://www.ruanyifeng.com/blog/2015/03/react.html)

(4)[React组件之间如何交流](https://www.cnblogs.com/aivnfjgj/p/6180779.html)

(5)[结合ES6+开发React组件](http://www.oschina.net/question/2012764_242688?fromerr=FNP2HGiK)

(6)[React Router使用教程](http://www.ruanyifeng.com/blog/2016/05/react_router.html)

(7)[深入理解react-router路由系统](https://segmentfault.com/a/1190000004075348?utm_source=tuicool&utm_medium=referral)

(8)[Create React App中文文档](https://create-react-app.bootcss.com/)

(9)[Chrome插件安装之安装React-devtools](https://blog.csdn.net/qq_41956139/article/details/105816438)

(10)[react项目启动报错:Uncaught TypeError: Cannot read property 'forEach' of undefined](https://blog.csdn.net/feinifi/article/details/112448350)

# 三、Reflux架构实例

一个简单的添加、删除列表项的demo。

1.views目录

ListBox.js中通过ListStore.listen监听store中数据的变化，通过ListAction.addItem("new Item")通知到一个具体的action。

	import React from "react";
	import ListItem from "./ListItem.js";
	import ListAction from "../actions/ListAction.js";
	import ListStore from "../stores/ListStore.js"

	var ListBox = React.createClass({
	    getInitialState:function(){
	        return{
	            list:ListStore.getAll()
	        };
	    },

	    onChange: function(list) {
	        this.setState({
	            list: list
	        });
	    },

	    componentDidMount: function() {
	        this.unsubscribe = ListStore.listen(this.onChange);
	    },

	    componentWillUnmount: function() {
	        this.unsubscribe();
	    },

	    addItem: function(){
	        ListAction.addItem("new Item");
	    },

	    deleteItem: function(){
	        ListAction.deleteItem();
	    },

	    outputList: function(list){
	        var listNodes = list.map(function (item, index) {
	            return (
	                <ListItem key={index} text={item} />
	            );
	        });
	        return (
	            <ul>
	                {listNodes}
	            </ul>
	        )
	    },
	    render(){
	        return(
	            <div>
	                {this.outputList(this.state.list)}
	                <button type="button" onClick = {this.addItem}>增加</button>
	                <button type="button" onClick = {this.deleteItem}>删除</button>
	            </div>
	        )
	    }
	});

	export default ListBox;

2.actions目录

ListAction.js中注册具体的action。

	import Reflux from "reflux";

	var ListAction = Reflux.createActions([
	    "addItem",
	    "deleteItem"
	]);

	export default ListAction;

3.stores目录

ListStore.js中通过this.listenTo监听某个具体的action，通过this.trigger通知view变化。

	import Reflux from 'reflux';
	import ListAction from '../actions/ListAction.js';

	var _list = ["Item1","Item2","Item3"];

	var ListStore = Reflux.createStore({

	    init: function() {
	        this.listenTo(ListAction.addItem, this.addItem);
	        this.listenTo(ListAction.deleteItem, this.deleteItem);
	    },

	    addItem: function(text){
	        _list.push(text);
	        this.trigger(_list);
	    },

	    deleteItem: function(){
	        _list.pop();
	        this.trigger(_list);
	    },
	    
	    getAll: function(){
	        return _list;
	    }
	});

	export default ListStore;

N.参考

(1)[Creating a Note Taking App with React and Flux](https://www.sitepoint.com/creating-note-taking-app-react-flux/)

(2)[Flux架构入门教程](http://www.ruanyifeng.com/blog/2016/01/flux.html)

(3)[使用React和Flux创建一个记事本应用](http://www.jcodecraeer.com/a/javascript/2015/0311/2581.html)

# 四、React Native

1.开发环境搭建

(1)配置好Android运行环境，确保一个正常的Android工程能正常run起来。

(2)全局安装react和react-native-cli，并初始化一个项目。

    npm install -g react
    npm install -g react-native-cli
    react-native init Test1
    cd Test1
    npm install

或者参考[这里](https://segmentfault.com/q/1010000004033633)初始化一个项目，会自动生成一系列的目录和文件。

    node -e "require('react-native/local-cli/cli').init('.','Test1')"

(3)连接真机，用如下命令运行项目

    react-native run-android

此时相当于执行了

    adb -s XXXXX reverse tcp:8081 tcp:8081使真机成功连接
    react-native start启动packager server
    执行gradle，打包apk

gradle执行失败可能是需要修改\Test1\android\app\build.gradle

    compileSdkVersion 23
    buildToolsVersion "23.0.3"
    aaptOptions.cruncherEnabled = false
    aaptOptions.useNewCruncher = false

修改\Test1\android\build.gradle

    classpath 'com.android.tools.build:gradle:2.3.0'

修改\Test1\android\gradle\wrapper\gradle-wrapper.properties

    distributionUrl=https\://services.gradle.org/distributions/gradle-3.3-all.zip

(4)可能出现到执行到97%真机无法安装apk的问题，直接通过adb安装

    cd D:\Work\RN\rndemos\Test1\android\app\build\outputs\apk
    adb install app-debug.apk

(5)摇手机，fetch JS bundle。出现失败的情况，可能需要更新react

    npm install react --save

(6)如果是AVD，直接apk拖进模拟器安装，双击r来reload。

2.Android连接问题

(1)连接不上：断开连接（不开HDB）-->撤销USB调试授权-->重新连接-->授权

(2)Chrome找不到device:参考[这里](http://stackoverflow.com/questions/20408996/native-usb-debugging-on-chrome-32-doesnt-detect-device)

    Download the Android SDK

    Locate ADB.exe, found in the platform-tools folder

    Open the file using command prompt
        cd c:\path\to\platform-tools\adb.exe

    Make sure your phone is disconnected from USB

    Type the following commands
        adb devices
        adb kill-server
        adb start-server

    Reconnect your phone, authorise your PC and enjoy the USB debugging

N.参考

(1)[React Native中文网](http://reactnative.cn/)

(2)[React native配置后，一直Installing react-native package from npm](https://segmentfault.com/q/1010000004033633)

(3)[react-native-guide](https://github.com/reactnativecn/react-native-guide)

(4)[React Native for Android 实践 -- 实现知乎日报客户端](http://www.race604.com/react-native-android-practice/)

# 五、Ant Design

N.参考

(1)[Ant Design官网](https://ant.design/index-cn)

(2)[Ant Design Pro](https://pro.ant.design/zh-CN)