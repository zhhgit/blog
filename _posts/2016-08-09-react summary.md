---
layout: post
title: "React使用小结"
description: React使用小结
modified: 2016-08-09
category: FrontEnd
tags: [FrontEnd]
---

# 一、使用React的方式

1.通过react-tools将转化jsx

	npm install -g react-tools
	jsx --watch script/ js/
	
2.通过create-react-app初始化一个结合了Webpack和ES6的React工程。

    npm install -g create-react-app
    create-react-app my-app
    cd my-app
    npm start

# 二、React组件

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
                url: "http://zhanghao90.cn/interface1",
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

# 四、参考

1.[React菜鸟教程](http://www.runoob.com/react/react-tutorial.html)

2.[React官网](https://reactjs.org/)

3.[React 入门实例教程](http://www.ruanyifeng.com/blog/2015/03/react.html)

4.[Babel官网](http://babeljs.io/)

5.[React 组件之间如何交流](http://www.tuicool.com/articles/AzQzEbq)

6.[结合ES6+开发React组件](http://www.oschina.net/question/2012764_242688?fromerr=FNP2HGiK)

7.[React Router使用教程](http://www.ruanyifeng.com/blog/2016/05/react_router.html)

8.[从React Router谈谈路由的那些事](http://stylechen.com/react-router.html)

9.[深入理解react-router路由系统](https://segmentfault.com/a/1190000004075348?utm_source=tuicool&utm_medium=referral)

10.[zhhgit/react_coupons](https://github.com/zhhgit/react_coupons)

11.[demo8](https://github.com/zhhgit/react_coupons/tree/master/demo8-react%20and%20reflux)

12.[Creating a Note Taking App with React and Flux](https://www.sitepoint.com/creating-note-taking-app-react-flux/)

13.[Flux架构入门教程](http://www.ruanyifeng.com/blog/2016/01/flux.html)

14.[使用React和Flux创建一个记事本应用](http://www.jcodecraeer.com/a/javascript/2015/0311/2581.html)

15.[React应用的架构模式Flux](http://stylechen.com/react-flux.html)