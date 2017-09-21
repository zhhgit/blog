---
layout: post
title: "React(4):页面组件化"
description: React(4):页面组件化
modified: 2016-08-03
category: React
tags: [React]
---

# 一、页面组件化

两个页面，分别是两个大组件，由路由切换

index.js:

	import React from 'react'
	import ReactDOM from 'react-dom'
	import { Router, Route, hashHistory, Link, IndexRedirect } from 'react-router'

	import BillBox from './billBox.js';
	import BillList from './billList.js';
	import BillDetail from './billDetail.js';


	ReactDOM.render((
	  <Router history={hashHistory}>
	    <Route path="/" component={BillBox}>
	        <IndexRedirect to="/billList" />
	        <Route path="billList" component={BillList} />
	        <Route path="billDetail" component={BillDetail} />
	    </Route>
	  </Router>
	), document.getElementById('billBox'));

大组件由多个小组件构成

billDetail.js:

	import React from 'react'
	import ReactDOM from 'react-dom'

	import BillItem from './billItem.js';
	import ButtonArea from './buttonArea.js';
	import CardRequire from './cardRequire.js';
	import UserNotes from './userNotes.js';
	import ClientService from './clientService.js';
	import NearPlace from './nearPlace.js';
	import RelatedCoupon from './relatedCoupon.js';
	import Merchant from './merchant.js';
	import Comment from './comment.js';

	var BillDetail = React.createClass({
	    render() {
	        var data={
	            billPicPath:"image/pic1.jpg"
	        };
	        return (
	            <div>
	                <BillItem data={data} />
	                <ButtonArea />
	                <CardRequire />
	                <UserNotes />
	                <ClientService />
	                <NearPlace />
	                <RelatedCoupon />
	                <Merchant />
	                <Comment />
	            </div>
	        )
	    }
	});
	export default BillDetail;

每个小组件结构大致如下

buttonArea.js:

	import React from 'react'
	import ReactDOM from 'react-dom'

	var ButtonArea = React.createClass({
	    render() {
	        return (
	            <div>
	                <div className="buttonArea">
	                    <GetButton />
	                </div>
	                <div className="waveArea"></div>
	            </div>
	        )
	    }
	});

	var GetButton = React.createClass({
	    render() {
	        return(
	            <button className="getButton">立即获取</button>
	        )
	    }
	});

	export default ButtonArea;

# 二、打包

写repack.bat方便修改后重新运行webpack

	cd C:\xampp\htdocs\React\demo5
	webpack --display-error-details

具体参考[demo5](https://github.com/zhhgit/react_coupons/tree/master/demo5-static%20list%20and%20detail)
