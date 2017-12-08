---
layout: post
title: "Reflux架构实例"
description: Reflux架构实例
modified: 2016-08-15
category: React
tags: [React]
---

一个简单的添加、删除列表项的demo。

# 一、views目录

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

# 二、actions目录

ListAction.js中注册具体的action。

	import Reflux from "reflux";

	var ListAction = Reflux.createActions([
	    "addItem",
	    "deleteItem"
	]);

	export default ListAction;

# 三、stores目录

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

# 四、具体参考

1.[demo8](https://github.com/zhhgit/react_coupons/tree/master/demo8-react%20and%20reflux)

2.[Creating a Note Taking App with React and Flux](https://www.sitepoint.com/creating-note-taking-app-react-flux/)

3.[Flux架构入门教程](http://www.ruanyifeng.com/blog/2016/01/flux.html)

4.[使用React和Flux创建一个记事本应用](http://www.jcodecraeer.com/a/javascript/2015/0311/2581.html)

5.[React应用的架构模式Flux](http://stylechen.com/react-flux.html)
