---
layout: post
title: "React(6):滚动加载更多"
description: React(6):滚动加载更多
modified: 2016-08-09
category: React
tags: [React]
---

# 一、滚动加载更多

billList.js

	import React from 'react'
	import { Router, Route, hashHistory, Link, IndexRedirect } from 'react-router'

	import BillItem from './billItem.js';

	var BillList = React.createClass({
	    //初始化state
	    getInitialState: function() {
	        return {
	            data:'',
	            totalPage:'',
	            dataJson: {
	                "cityCd": "310000",
	                "orderType": "4",
	                "currentPage": "1",
	                "pageSize": "10",
	                "version": "1.0",
	                "source": "1"
	            }
	        };
	    },

	    //组件挂载后请求第一页数据并监听滚动事件
	    componentDidMount: function() {
	        //第一次请求数据
	        var billListUrl = "https://youhui.95516.com/wm-non-biz-web/restlet/payBill/allBillList";
	        $.ajax({
	            type: 'GET',
	            url: billListUrl,
	            contentType: "application/json; charset=utf-8",
	            data: this.state.dataJson,
	            dataType: 'json',
	            success: function (resp) {
	                this.setState({
	                    data: resp.data,
	                    totalPage: resp.totalPage
	                });
	            }.bind(this),
	            error: function(err) {}
	        });

	        //监听滚动
	        $(window).on("scroll",this.loadNewData);

	        //第一次加载时候不要显示正在加载中
	        if ($(window).scrollTop()==0){
	            $(".loadingMore").hide();
	        }
	    },

	    //离开列表页时不再监听滚动
	    componentWillUnmount: function() {
	        //移除监听滚动
	        $(window).off("scroll",this.loadNewData);
	    },

	    //加载新一页数据
	    loadNewData:function () {
	        //显示loadingMore
	        if ($(window).scrollTop()!=0){
	            $(".loadingMore").show();
	        }

	        //显示返回顶部按钮
	        if($(window).scrollTop()>100){
	            $(".backTopBox").show();
	        }else{
	            $(".backTopBox").hide();
	        }
	        //请求新一页数据
	        if(this.checkLoading()){
	            var currentPage = parseInt(this.state.dataJson.currentPage)+1;
	            if (currentPage<=this.state.totalPage){
	                var billListUrl = "https://youhui.95516.com/wm-non-biz-web/restlet/payBill/allBillList";
	                this.setState({
	                    dataJson: {
	                        "cityCd": "310000",
	                        "orderType": "4",
	                        "currentPage": currentPage,
	                        "pageSize": "10",
	                        "version": "1.0",
	                        "source": "1"
	                    }
	                });
	                $.ajax({
	                        type: 'GET',
	                        url: billListUrl,
	                        contentType: "application/json; charset=utf-8",
	                        data: this.state.dataJson,
	                        dataType: 'json',
	                        success: function (resp) {
	                            var wholeData = this.state.data.concat(resp.data);
	                            this.setState({
	                                data: wholeData
	                            });
	                        }.bind(this),
	                        error: function(err) {}
	                });
	            }
	        }

	        //当完全加载后变化底部显示字
	        if (currentPage==this.state.totalPage){
	            $(".loadingMore").text("没有更多数据");
	        }
	    }


	    ,

	    //判断是否加载更多
	    checkLoading:function(){
	        if ($(window).scrollTop()==0){
	            return false;
	        }
	        else if ($(window).scrollTop()+$(window).height()>=$(document).height()){
	            return true;
	        }
	        else {
	            return false;
	        }
	    },

	    //回顶部
	    backTop:function(){
	        $(window).scrollTop(0);
	    },

	    //输出每张票券
	    handleData: function(data) {
	        if (Array.isArray(data) && data.length > 0) {
	            return data.map(function (item, index) {
	                var eachData = {
	                    "billPicPath":item.billPicPath,
	                    "brandNm":item.brandNm,
	                    "ticketNm":item.ticketNm,
	                    "leftNum":item.leftNum,
	                    "originPrice":item.originPrice,
	                    "salePrice":item.salePrice,
	                    "downloadNum":item.downloadNum
	                };
	                var linkUrl = "/billDetail/" + item.brandId + "_" + item.billId + "_" + "310000";
	                return (
	                    <Link key={item.billId} to={linkUrl}><BillItem data={eachData} /></Link>
	                )
	            }.bind(this));
	        }
	    },

	    render() {
	        return (
	            <div>
	                {this.handleData(this.state.data)}
	                <div className = "loadingMore">
	                    正在加载中
	                </div>
	                <div className = "backTopBox" onClick = {this.backTop}>
	                    <img className = "backTopImg" src = "../image/back-top.png"/>
	                </div>
	            </div>
	        )
	    }
	});
	export default BillList;

# 二、根据数据不同显示不同

render()函数中return的不同。

billItem.js：

	import React from 'react'

	var BillItem = React.createClass({

	    render() {
	        var finished = (parseInt(this.props.data.leftNum)==0);
	        if(finished){
	            return (
	                <div className = "billItem">
	                    <div className="billItemLeft">
	                        <img className = "billItemImg" src = {"https://youhui.95516.com/" +  this.props.data.billPicPath} />
	                        <div className="finishMark">已抢完</div>
	                    </div>
	                    <BillItemDes data={this.props.data} />
	                </div>
	            )
	        }
	        else{
	            return (
	                <div className = "billItem">
	                    <div className="billItemLeft">
	                        <img className = "billItemImg" src = {"https://youhui.95516.com/" +  this.props.data.billPicPath} />
	                    </div>
	                    <BillItemDes data={this.props.data} />
	                </div>
	            )
	        }
	        
	    }
	});

	var BillItemDes = React.createClass({
	    render() {
	        return (
	            <div className="billItemRight">
	                <div className="billItemRightContainer">
	                    <BillItemDesTitle data={this.props.data} />
	                    <BillItemDesReduce data={this.props.data} />
	                    <BillItemDesRemain data={this.props.data} />
	                    <BillItemDesGot data={this.props.data} />
	                </div>
	            </div>
	        )
	    }
	});

	var BillItemDesTitle = React.createClass({
	    render() {
	        return (
	            <div className="billItemDesTitle fl">{this.props.data.brandNm}</div>
	        )
	    }
	});

	var BillItemDesReduce = React.createClass({
	    render() {
	        return (
	            <div className="billItemDesReduce fl">{this.props.data.ticketNm}</div>
	        )
	    }
	});

	var BillItemDesRemain = React.createClass({
	    render() {
	        var leftNum = (parseInt(this.props.data.leftNum)<0) ? "":((parseInt(this.props.data.leftNum)==0) ? "已抢完":"剩余"+this.props.data.leftNum+"张");
	        return (
	            <div className="billItemDesRemain fl">{leftNum}</div>
	        )
	    }
	});

	var BillItemDesGot = React.createClass({
	    render() {
	        var salePrice = (parseInt(this.props.data.salePrice)==0) ? "免费获取":"￥"+this.props.data.salePrice;
	        var originPrice = (parseInt(this.props.data.salePrice)==0) ? " ":"￥"+this.props.data.originPrice;
	        return (
	            <div className="billItemDesGot fl">
	                <span className="billItemDesSalePrice">{salePrice}</span>
	                <span className="billItemDesOriginPrice">{originPrice}</span>
	                <span className="billItemDesGotR">{this.props.data.downloadNum}人获取</span>
	            </div>
	        )
	    }
	});

	export default BillItem;

具体参考

1.[demo7](https://github.com/zhhgit/React-demos/tree/master/demo7-scroll%20loading)

2.[jQuery插件实现网页底部自动加载-类似新浪微博](http://justcoding.iteye.com/blog/2108040)