---
layout: post
title: "InfoQ架构师笔记"
description: InfoQ架构师笔记
modified: 2020-02-19
category: 架构
tags: [架构]
---

# 201901
1.flink实时流处理。阿里巴巴blink。批处理全量与流处理增量。

2.系统间通信，同步使用RPC框架例如Dubbo，异步使用消息队列例如Kafka。

3.客户端一致性：

    begin tx开启本地事务
    do work执行业务操作
    insert message向同实例消息库插入消息
    end tx事务提交
    send message网络向server发送消息
    reponse server回应消息
    delete message如果server回复成功则删除消息
    scan messages补偿任务扫描未发送消息
    send message补偿任务补偿消息
    delete messages补偿任务删除补偿成功的消息

4.Kafka等消息中间件都是基于patition存储模型的，comsumer个数应该与同一subject的partition对应。不利于扩容缩容。

5.技术发展
在软件架构领域，经历了从单体应用到SOA再到微服务；
在云计算领域，经历了从虚拟机到容器；
在数据库领域，从关系数据库到NoSQL再到NewSQL；
在大数据领域，从批处理到流处理；
在运维领域，从手工运维到DevOps、AIOps；
在前端领域，从jQuery到React等三大框架；

6.Web网站屏蔽底层硬件差异，管理网络编排。

# 201902

1.滴滴的跨端框架Chameleon。

2.Kubeflow旨在支持多种机器学习框架运行在Kubernetes之上，比如Tensorflow, Pytorch, Caffe等常见框架。

3.Dubbo的实践和演进。

4.一个比较典型的DevOps的流水线过程是：项目开始开发时，用VS Code开发代码，然后把代码推送到GitLab里存储，通过GitLab的hook使Jenkins执行一些CI的过程，比如做一些单元测试，构建Docker image，再把这个Docker image调用helm部署到开发环境或测试环境中。在测试环境里通过Jenkins触发一个集成测试的功能，完成后就可以把它部署到生产环境里，通过Kubernetes addon的方式，把Prometheus、Grafana等监控组件部署到集群里，就实现了一整套从CI到CD的监控过程。

5.云主机IO加速技术极大提升了机械盘随机写的处理能力。