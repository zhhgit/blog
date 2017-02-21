---
layout: post
title: "生产监控"
description: 生产监控
modified: 2016-12-20
category: Linux
tags: [Linux]
---

# 一、观察点

1.APP实际操作，看速度是否快，是否有报错。

2.如果加载速度缓慢，或者下载等操作失败。让运维看Apache连接数、Memcache连接数是否正常。

3.看不同Apache的连接数是否均衡，如果有的机器没有，有的机器数量很大，负载均衡就有问题（可能是PHP中配置不对）。

# 二、分析方法

1.运维通过脚本统计不同PHP请求消耗时间，例如：

    XXX/ch_seckill/logs> grep "13/Dec/2016:10:0" access_log_20161213 | grep -i "downloadseckillforios" | awk 'if(length($10)>=7){print $10})' | wc -l

解释：在access_log_20161213日志文件中搜"downloadseckillforios"关键字（比较重要的还有"ValidateSeckillPhoneV2.php"），">=7"对应于大于1s，">=8"对应于大于10s（以1e-6s位单位）。脚本执行后输出大于1s的请求数量。

2.在PHP代码中加log，对不同业务逻辑代码块记录消耗时间，如验证请求参数部分、校验验证码部分、下载请求部分等。如果哪部分执行时间过长，就要分析代码中的问题。

3.测试机连接wifi走代理，通过Fiddler或者Charles抓包，看各个请求时间。主要关注seckill/php/下的SeckillDetail.php，Captcha.php，ValidateSeckillPhoneV2.php这几个。