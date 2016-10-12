---
layout: post
title: "React(5):动态请求数据"
description: React(5):动态请求数据
modified: 2016-08-08
category: React
tags: [React]
---

# 一、动态获取数据

组件加载(执行了render()函数)后，在componentDidMount中通过AJAX请求数据，并通过setState更新state，state变化会重新调用render()函数。

billDetail.js：

	import React from 'react'

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
	    getInitialState: function() {
	        return {
	            billData:'',
	            billBranchData:''
	        };
	    },

	    componentDidMount: function() {
	        //获取详情页面票券数据
	        var billDetailUrl = "https://youhui.95516.com/static/bill_" + this.props.params.billDetail + ".json";
	        $.ajax({
	            type: 'GET',
	            url: billDetailUrl,
	            contentType: "application/json; charset=utf-8",
	            dataType: 'json',
	            success: function (resp) {
	                this.setState({
	                    billData: resp
	                });
	            }.bind(this),
	            error: function(err) {}
	        });

	        //获取详情页面门店数据
	        var billBranchUrl = "http://youhui.95516.com/wm-non-biz-web/restlet/bill/billBranchList";
	        var billId = this.props.params.billDetail.split("_")[1];
	        var dataJson= {
	            "billId":billId,
	            "cityCd": "310000",
	            "currentPage": "1",
	            "pageSize": "3",
	            "version": "1.0",
	            "source": "1"
	        };

	        $.ajax({
	            type: 'GET',
	            url: billBranchUrl,
	            contentType: "application/json; charset=utf-8",
	            data: dataJson,
	            dataType: 'json',
	            success: function (resp) {
	                this.setState({
	                    billBranchData: resp.data[0]
	                });
	            }.bind(this),
	            error: function(err) {}
	        });
	    },

	    handleBillData: function(billData) {
	        if (billData["data-brand-bill-list"] != undefined){
	            //data-brand-bill-list
	            var billItemDataArray = billData["data-brand-bill-list"]["data"];
	            var currentBillData;
	            //data-brand-detail
	            var billBrandData = billData["data-brand-detail"]["data"];
	            //data-comments-list
	            var billCommentDataArray = billData["data-comments-list"]["data"];

	            //-----------------处理data-brand-bill-list-----------------
	            if (Array.isArray(billItemDataArray) && billItemDataArray.length > 0) {
	                currentBillData = billItemDataArray[0];
	            }
	            //billItem数据
	            var billItemData = {
	                "billPicPath":currentBillData.billPicPath,
	                "brandNm":currentBillData.brandNm,
	                "ticketNm":currentBillData.ticketNm,
	                "leftNum":currentBillData.leftNum,
	                "originPrice":currentBillData.originPrice,
	                "salePrice":currentBillData.salePrice,
	                "downloadNum":currentBillData.downloadNum
	            };

	            //cardRequire数据
	            var cardRequireData = {
	                "billRestrictDesc":currentBillData.billRestrictDesc
	            };

	            //userNotes数据
	            var userNotesData = {
	                "billBeginDtYear":currentBillData.billBeginDt.substring(0,4),
	                "billBeginDtMouth":currentBillData.billBeginDt.substring(4,6),
	                "billBeginDtDay":currentBillData.billBeginDt.substring(6,8),
	                "billEndDtYear":currentBillData.billEndDt.substring(0,4),
	                "billEndDtMouth":currentBillData.billEndDt.substring(4,6),
	                "billEndDtDay":currentBillData.billEndDt.substring(6,8),
	                "billRule":currentBillData.billRule,
	                "userInstructions":currentBillData.userInstructions.replace(/；/g, "；<br />")
	            };

	            //clientService数据
	            var clientServiceData = {
	                "billId":currentBillData.billId
	            };

	            //relatedCoupon数据
	            var relatedCouponData = {
	                "billItemDataArray":billItemDataArray
	            };

	            //-----------------处理data-brand-detail-----------------

	            //merchant数据
	            var merchantData = {
	                "brandDesc":billBrandData.brandDesc,
	                "brandNm":billBrandData.brandNm
	            };

	            //-----------------处理data-comments-list-----------------
	            //comment数据
	            var commentData = {
	                "billCommentDataArray":billCommentDataArray
	            };

	            return ({
	                "billItemData":billItemData,
	                "cardRequireData":cardRequireData,
	                "userNotesData":userNotesData,
	                "clientServiceData":clientServiceData,
	                "relatedCouponData":relatedCouponData,
	                "merchantData":merchantData,
	                "commentData":commentData
	            })
	        }
	    },

	    handleBillBranchData:function(billBranchData){
	        if (billBranchData != undefined){
	            //nearPlace数据
	            var nearPlaceData = {
	                "addr":billBranchData.addr,
	                "phone":billBranchData.phone,
	                "bussHour":billBranchData.bussHour,
	                "name":billBranchData.name
	            };

	            return ({
	                "nearPlaceData":nearPlaceData
	            })
	        }
	    },

	    render() {
	        var billData = this.handleBillData(this.state.billData);
	        var billBranchData = this.handleBillBranchData(this.state.billBranchData);
	        if (billData != undefined && billBranchData != undefined){
	            return (
	                <div>
	                    <BillItem data = {billData.billItemData} />
	                    <ButtonArea />
	                    <CardRequire data = {billData.cardRequireData} />
	                    <UserNotes data = {billData.userNotesData} />
	                    <ClientService data = {billData.clientServiceData} />
	                    <NearPlace data = {billBranchData.nearPlaceData}/>
	                    <RelatedCoupon data={billData.relatedCouponData} />
	                    <Merchant data={billData.merchantData}/>
	                    <Comment data={billData.commentData} />
	                </div>
	            )
	        }
	        else{
	            return(
	                <div>
	                </div>
	            )
	        }

	    }
	});
	export default BillDetail;

具体参考[demo6](https://github.com/zhhgit/React-demos/tree/master/demo6-dynamic%20list%20and%20detail)