---
layout: post
title: "Python系列 -- 量化"
description: Python系列 -- 量化
modified: 2022-01-01
category: Python
tags: [Python]
---

# Ricequant

1.基本框架

    # 在这个方法中编写任何的初始化逻辑。context对象将会在你的算法策略的任何方法之间做传递。
    def init(context):
        # 在context中保存全局变量
        context.s1 = "000001.XSHE"
        # 实时打印日志
        logger.info("RunInfo: {}".format(context.run_info))
    
    # before_trading此函数会在每天策略交易开始前被调用，当天只会被调用一次
    def before_trading(context):
        pass

    # 选择的证券的数据更新将会触发此段逻辑，例如日或分钟历史数据切片或者是实时数据切片更新
    def handle_bar(context, bar_dict):
        # 开始编写你的主要的算法逻辑
        # bar_dict[order_book_id] 可以拿到某个证券的bar信息
        # context.portfolio 可以拿到现在的投资组合信息
    
        # 使用order_shares(id_or_ins, amount)方法进行落单
    
        # TODO: 开始编写你的算法吧！
        order_shares(context.s1, 1000)
    
    # after_trading函数会在每天交易结束后被调用，当天只会被调用一次
    def after_trading(context):
        pass

2.常用数据函数

    ############################## 股票
    industry - 获取某行业股票列表
    sector - 获取某板块股票列表
    concept - 获取某概念股票列表
    get_fundamentals - 查询财务数据（退役中）
    get_factor - 获取因子

    ############################## 指数
    index_components - 获取指数成分股列表

3.策略开发相关函数

    ############################## 数据查询
    history_bars - 某一合约历史数据

    ############################## scheduler定时器
    scheduler.run_daily - 每天运行
    scheduler.run_weekly - 每周运行
    scheduler.run_monthly - 每月运行

    ############################## 交易
    order_shares - 指定股数交易（股票专用）
    order_lots - 指定手数交易（股票专用）
    order_value - 指定价值交易（股票专用）
    order_percent - 一定比例下单（股票专用）
    order_target_value - 目标价值下单（股票专用）

    ############################## 重要对象
    context.portfolio 投资组合对象
        cash 可用资金，为子账户可用资金的加总
        market_value 投资组合当前的市场价值，为子账户市场价值的加总
        total_value	float 总权益，为子账户总权益加总

    context.portfolio.positions 仓位信息对象
        order_book_id 合约代码
        quantity 当前持仓股数

    context.stock_account 股票账户信息
        cash 可用资金
        market_value 投资组合当前所有证券仓位的市值的加总
        total_value 总权益
        positions 一个包含股票子组合仓位的字典，以 order_book_id作为键，position对象作为值

N.参考

1.[RQData文档](https://www.ricequant.com/doc/rqdata/python/)

2.[RQ量化平台文档](https://www.ricequant.com/doc/quant/)

# vn.py

# 策略