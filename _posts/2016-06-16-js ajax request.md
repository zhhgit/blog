---
layout: post
title: "JS AJAX请求"
description: JS AJAX请求
modified: 2016-06-16
category: JavaScript
tags: [JavaScript]
featured: true
---

典型获取数据

	function fetchBillList(){
        var server = (function(){
                return {
                    0: 'http://...', //生产地址
                    1: 'http://...', //测试地址
                    2: 'http://...', //联调地址
                    3: 'http://...'  //本地地址
                }['3']
            }
        )();

        //请求地址
        var billListUrl = server + '/1.0/wechat.billList';

        //要发送的json数据
        var dataJson= {"openId":urlParams["openId"], "token":urlParams["token"]};

        //发送billList的ajax请求
        $.ajax({
            type: 'POST',
            url: billListUrl,
            contentType: "application/json; charset=utf-8",
            data: JSON.stringify(dataJson),
            dataType: 'json',
            success: function (resp) {
                if(resp.resp == '00') {
                    var accountList = resp.params.accountList;
                    if (accountList && accountList.length){
                    	//数据非空，成功处理
                    }
                    else{
                        //数据空，成功处理
                    }
                }
                else{
                    //失败处理
                }
            },
            error: function (err) {
                //失败回调
            }
        });
    }

