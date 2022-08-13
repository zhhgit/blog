---
layout: post
title: "面试题 -- 消息队列篇"
description: 面试题 -- 消息队列篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# 消息队列基础

1.什么是MQ

MQ全称为Message Queue, 消息队列（MQ）是一种应用程序对应用程序的通信方法。应用程序通过读写出入队列的消息（针对应用程序的数据）来通信，而无需专用连接来链接它们。
消息传递指的是程序之间通过在消息中发送数据进行通信，而不是通过直接调用彼此来通信，直接调用通常是用于诸如远程过程调用的技术。
队列的使用除去了接收和发送应用程序同时执行的要求。

2.消息队列使用场景

(1)解耦，A系统发送个数据到BCD三个系统，接口调用发送，那如果E系统也要这个数据呢？那如果C系统现在不需要了呢？现在A系统又要发送第二种数据了呢？A系统要时时刻刻考虑BCDE四个系统如果挂了怎么办？那要不要重发？
使用了MQ之后的解耦场景：一个系统或者一个模块，调用了多个系统或者模块，相互之间的调用很复杂，维护起来很麻烦。但是其实这个调用是不需要直接同步调用接口的，如果MQ给他异步化解耦也是可以的，你就需要去考虑在你的项目里是不是可以运用这个MQ去进行系统解耦 。

(2)异步提升效率，场景描述：系统A接受一个请求，需要在自己本地写库，还需要在系统BCD三个系统写库，自己本地写库需要3ms。BCD分别需要300ms、450ms、200ms。最终总好时长：953ms，接近1s。给用户的体验感觉一点也不好。
使用MQ异步化之后的接口性能优化，提高系统响应速度，使用了消息队列，生产者一方，把消息往队列里一扔，就可以立马返回，响应用户了。无需等待处理结果。处理结果可以让用户稍后自己来取，如医院取化验单。
如果用mq来传递非常核心的消息，比如说计费，扣费的一些消息，计费系统是很重的一个业务，操作是很耗时的，将计费做成异步化的，然后中间就是加了一个MQ。

(3)削峰，场景描述：有突发流量，使用MQ来进行削峰。

3.使用消息队列的优点，缺点？

优点：解耦、异步、削峰

缺点：
系统可用性降低：系统引入的外部依赖越多，越容易挂掉，本来你就是A系统调用BCD三个系统的接口就好了，人ABCD四个系统好好的没什么问题，你偏加个MQ进来，万一MQ挂了怎么办，整套系统崩溃了，就完蛋了。
系统复杂性提高：硬生生加个MQ进来，你怎么保证消息没有重复消费？怎么处理消息丢失的情况？怎么保证消息传递的顺序性？
一致性问题：系统A处理完了直接返回成功了，人家都认为你这个请求成功了；但问题是，要是BCD三个系统哪里BD系统成功了，结果C系统写库失败了，咋整？数据就不一致了，

4.kafka、activemq、rabbitmq、rocketmq都有什么优缺点？

(1)ActiveMQ：

单机吞吐量万级，吞吐量比RocketMQ和Kafka要低了一个数量级。时效性ms级。可用性高，基于主从架构实现高可用性。消息可靠性有较低的概率丢失数据。功能支持MQ领域的功能极其完备。
非常成熟，功能强大，在业内大量的公司以及项目中都有应用。偶尔会有较低概率丢失消息。而且现在社区以及国内应用都越来越少，官方社区现在对ActiveMQ 5.x维护越来越少，几个月才发布一个版本。而且确实主要是基于解耦和异步来用的，较少在大规模吞吐的场景中使用。

(2)RabbitMQ：

单机吞吐量万级，吞吐量比RocketMQ和Kafka要低了一个数量级。时效性微秒级，这是rabbitmq的一大特点，延迟是最低的。可用性高，基于主从架构实现高可用性。功能支持基于erlang开发，所以并发能力很强，性能极其好，延时很低。
erlang语言开发，性能极其好，延时很低，吞吐量到万级，MQ功能比较完备，而且开源提供的管理界面非常棒，用起来很好用，社区相对比较活跃，几乎每个月都发布几个版本。在国内一些互联网公司近几年用rabbitmq也比较多一些。但是问题也是显而易见的，RabbitMQ确实吞吐量会低一些，这是因为他做的实现机制比较重。
而且erlang开发，国内有几个公司有实力做erlang源码级别的研究和定制？如果说你没这个实力的话，确实偶尔会有一些问题，你很难去看懂源码，你公司对这个东西的掌控很弱，基本职能依赖于开源社区的快速维护和修复bug。而且rabbitmq集群动态扩展会很麻烦。其实主要是erlang语言本身带来的问题。很难读源码，很难定制和掌控。

(3)RocketMQ：

单机吞吐量10万级，RocketMQ也是可以支撑高吞吐的一种MQ。topic可以达到几百，几千个的级别，吞吐量会有较小幅度的下降，这是RocketMQ的一大优势，在同等机器下，可以支撑大量的topic。时效性ms级。可用性非常高，分布式架构。消息可靠性经过参数优化配置，可以做到0丢失。功能支持MQ功能较为完善，还是分布式的，扩展性好。
接口简单易用，而且毕竟在阿里大规模应用过，有阿里品牌保障，日处理消息上百亿之多，可以做到大规模吞吐，性能也非常好，分布式扩展也很方便，社区维护还可以，可靠性和可用性都是ok的，还可以支撑大规模的topic数量，支持复杂MQ业务场景，而且一个很大的优势在于，阿里出品都是java系的，我们可以自己阅读源码，定制自己公司的MQ，可以掌控，社区活跃度相对较为一般，不过也还可以，文档相对来说简单一些，然后接口这块不是按照标准JMS规范走的有些系统要迁移需要修改大量代码，还有就是阿里出台的技术，你得做好这个技术万一被抛弃，社区黄掉的风险，那如果你们公司有技术实力我觉得用RocketMQ挺好的。

(4)Kafka：

单机吞吐量10万级别，这是kafka最大的优点，就是吞吐量高。topic从几十个到几百个的时候，吞吐量会大幅度下降。所以在同等机器下，kafka尽量保证topic数量不要过多。如果要支撑大规模topic，需要增加更多的机器资源。时效性延迟在ms级以内。可用性非常高，kafka是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用。消息可靠性经过参数优化配置，消息可以做到0丢失。功能支持功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用，是事实上的标准。
kafka的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展。同时kafka最好是支撑较少的topic数量即可，保证其超高吞吐量。而且kafka唯一的一点劣势是有可能消息重复消费，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略。这个特性天然适合大数据实时计算以及日志收集。

5.如何保证消息不被重复消费（如何保证消息消费时的幂等性）

kafka实际上有个offset的概念，就是每个消息写进去，都有一个offset，代表它的序号，然后consumer消费了数据之后，每隔一段时间，会把自己消费过的消息的offset提交一下，代表我已经消费过了，下次我要是重启啥的，你就让我继续从上次消费到的offset来继续消费吧。
但是凡事总有意外，比如我们之前生产经常遇到的，就是你有时候重启系统，看你怎么重启了，如果碰到点着急的，直接kill进程了，再重启。这会导致consumer有些消息处理了，但是没来得及提交offset，尴尬了。重启之后，少数消息会再次消费一次。
幂等性，通俗点说，就一个数据，或者一个请求，给你重复来多次，你得确保对应的数据是不会改变的，不能出错。
比如你拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update一下好吧。
比如你是写redis，那没问题了，反正每次都是set，天然幂等性。
比如你不是上面两个场景，那做的稍微复杂一点，你需要让生产者发送每条数据的时候，里面加一个全局唯一的id，类似订单id之类的东西，然后你这里消费到了之后，先根据这个id去比如redis里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个id写redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。
如何保证MQ的消费是幂等性的，需要结合具体的业务来看。

6.如何保证消息的可靠传输（如何处理消息丢失的问题）？

(1)消费端弄丢了数据。
唯一可能导致消费者弄丢数据的情况，就是说消费到了这个消息，然后消费者那边自动提交了offset，让kafka以为你已经消费好了这个消息，其实你刚准备处理这个消息，你还没处理，你自己就挂了，此时这条消息就丢咯。
这不是一样么，大家都知道kafka会自动提交offset，那么只要关闭自动提交offset，在处理完之后自己手动提交offset，就可以保证数据不会丢。但是此时确实还是会重复消费，比如你刚处理完，还没提交offset，结果自己挂了，此时肯定会重复消费一次，自己保证幂等性就好了。
生产环境碰到的一个问题，就是说我们的kafka消费者消费到了数据之后是写到一个内存的queue里先缓冲一下，结果有的时候，你刚把消息写入内存queue，然后消费者会自动提交offset。然后此时我们重启了系统，就会导致内存queue里还没来得及处理的数据就丢失了。

解决方法：kafka会自动提交offset，那么只要关闭自动提交offset，在处理完之后自己手动提交，可以保证数据不会丢。但是此时确实还是会重复消费，比如刚好处理完，还没提交offset，结果自己挂了，此时肯定会重复消费一次 ，做好幂等即可。

(2)kafka弄丢了数据
这是比较常见的一个场景，就是kafka某个broker宕机，然后重新选举partiton的leader时。大家想想，要是此时其他的follower刚好还有些数据没有同步，结果此时leader挂了，然后选举某个follower成leader之后，不就少了一些数据？这就丢了一些数据啊。
生产环境也遇到过，我们也是，之前kafka的leader机器宕机了，将follower切换为leader之后，就会发现说这个数据就丢了。

解决方案：
所以此时一般是要求起码设置如下4个参数：
给这个topic设置replication.factor参数：这个值必须大于1，要求每个partition必须有至少2个副本
在kafka服务端设置min.insync.replicas参数：这个值必须大于1，这个是要求一个leader至少感知到有至少一个follower还跟自己保持联系，没掉队，这样才能确保leader挂了还有一个follower吧。
在producer端设置acks=all：这个是要求每条数据，必须是写入所有replica之后，才能认为是写成功了。
在producer端设置retries=MAX（很大很大很大的一个值，无限次重试的意思）：这个是要求一旦写入失败，就无限重试，卡在这里了。
我们生产环境就是按照上述要求配置的，这样配置之后，至少在kafka broker端就可以保证在leader所在broker发生故障，进行leader切换时，数据不会丢失

(3)生产者会不会弄丢数据
如果按照上述的思路设置了ack=all，一定不会丢，要求是，你的leader接收到消息，所有的follower都同步到了消息之后，才认为本次写成功了。如果没满足这个条件，生产者会自动不断的重试，重试无限次。

7.消息队列中，如何保证消息的顺序性

错乱场景：
Kafka，mysql里增删改一条数据，对应出来了增删改3条binlog，接着这三条binlog发送到MQ里面，到消费出来依次执行，起码得保证人家是按照顺序来的吧？不然本来是：增加、修改、删除；你楞是换了顺序给执行成删除、修改、增加，不全错了么。
一个topic，一个partition，一个consumer，内部多线程，这不也明显乱了。

解决方案：
(1)Kafka，一个topic，一个partition，一个consumer，内部单线程消费。如果处理比较耗时，处理一条消息是几十ms，一秒钟只能处理几十条数据，这个吞吐量太低了。一般不会用这个。
(2)写入一个partition中的数据一定是有顺序的。生产者在写的时候，可以指定一个key，比如订单id作为key,那么订单相关的数据，一定会被分发到一个partition中区，此时这个partition中的数据一定是有顺序的。Kafka中一个partition只能被一个消费者消费。消费者从partition中取出数据的时候 ，一定是有顺序的。写N个内存queue，具有相同key的数据都到同一个内存queue；然后对于N个线程，每个线程分别消费一个内存queue即可，这样就能保证顺序性。

8.如何解决消息队列的延时以及过期失效问题？消息队列满了以后该怎么处理？有几百万消息持续积压几小时，说说怎么解决？

(1)大量消息在mq里积压
这个是我们真实遇到过的一个场景，确实是线上故障了，这个时候要不然就是修复consumer的问题，让它恢复消费速度，然后傻傻的等待几个小时消费完毕。一个消费者一秒是1000条，一秒3个消费者是3000条，一分钟是18万条，1000多万条。所以如果你积压了几百万到上千万的数据，即使消费者恢复了，也需要大概1小时的时间才能恢复过来。
一般这个时候，只能操作临时紧急扩容了，具体操作步骤和思路如下：
1）先修复consumer的问题，确保其恢复消费速度，然后将现有consumer都停掉。
2）新建一个topic，partition是原来的10倍，临时建立好原先10倍或者20倍的queue数量。
3）然后写一个临时的分发数据的consumer程序，这个程序部署上去消费积压的数据，消费之后不做耗时的处理，直接均匀轮询写入临时建立好的10倍数量的queue。
4）接着临时征用10倍的机器来部署consumer，每一批consumer消费一个临时queue的数据。
5）这种做法相当于是临时将queue资源和consumer资源扩大10倍，以正常的10倍速度来消费数据。
6）等快速消费完积压数据之后，得恢复原先部署架构，重新用原先的consumer机器来消费消息。

(2)大量消息丢失
假设你用的是rabbitmq，rabbitmq是可以设置过期时间的，就是TTL，如果消息在queue中积压超过一定的时间就会被rabbitmq给清理掉，这个数据就没了。那这就是第二个坑了。这就不是说数据会大量积压在mq里，而是大量的数据会直接搞丢。
这个情况下，就不是说要增加consumer消费积压的消息，因为实际上没啥积压，而是丢了大量的消息。我们可以采取一个方案，就是批量重导。这个时候我们就开始写程序，将丢失的那批数据，写个临时程序，一点一点的查出来，然后重新灌入mq里面去，把白天丢的数据给他补回来。也只能是这样了。

(3)如果消息积压在mq里很长时间都没处理掉，此时导致mq都快写满了
临时写程序，接入数据来消费，消费一个丢弃一个，都不要了，快速消费掉所有的消息。后期再补数据吧。

# JMS

1.JMS介绍

JMS即Java消息服务（Java Message Service）应用程序接口，是一个Java平台中关于面向消息中间件（MOM）的API，类似于JDBC。用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。消息模型有点对点，发布订阅两种模型。
它提供创建、发送、接收、读取消息的服务。由Sun公司和它的合作伙伴设计的应用程序接口和相应语法，使得Java程序能够和其他消息组件进行通信。
JMS是一个消息服务的标准或者说是规范，允许应用程序组件基于JavaEE平台创建、发送、接收和读取消息。它使分布式通信耦合度更低，消息服务更加可靠以及异步性。
在Java中，目前基于JMS实现的消息队列常见技术有ActiveMQ、RabbitMQ、RocketMQ。值得注意的是，RocketMQ并没有完全遵守JMS规范，并且Kafka不是JMS的实现。

2.JMS消费

(1)同步：订阅者/接收方通过调用receive()来接收消息。在receive()方法中，线程会阻塞直到消息到达或者到指定时间后消息仍未到达。

(2)异步：消息订阅者需注册一个消息监听者，类似于事件监听器，只要消息到达，JMS服务提供者会通过调用监听器的onMessage()递送消息。

3.JMS API接口

JMS编程模型非常类似于JDBC。

    ConnectionFactory：客户端用来创建连接的受管对象；可以通过JNDI来查找ConnectionFactory对象。
    Connection：客户端到JMS提供者之间的活动连接。
    Session：发送和接收消息的一个单线程上下文
    Destination：由Session创建的Queue或Topic对象。
    MessageProducer：由Session创建的对象，用于发送消息到Queue或Topic。
    MessageCosumer 由Session 创建的对象，用于接收Queue或Topic中的消息。
    Message：消费者和生产者之间传送的数据。
    MessageListener：消息监听器，消费者注册消息监听器，有消息到达，将调用该接口的onMessage方法。

4.JMS的优势

    (1)松耦合：我们可以很容易地开发松耦合应用程序。这意味着JMS API是所有JMS提供程序都应实施的标准或规范，以便我们可以将现有的JMS提供程序更改为新的JMS提供程序，而只需进行很少的更改（即仅配置），而无需更改我们的JMS应用程序代码。
    (2)异步：我们可以非常轻松地开发异步消息应用程序。 这意味着JMS Sender可以发送消息并继续自己的工作。 它不等待JMS Receiver完成消息使用。
    (3)健壮且可靠：JMS确保将一条消息仅传递到目标系统一次。 因此，我们可以非常轻松地开发可靠的应用程序。
    (4)互操作性：JMS API允许其他Java平台语言（例如Scala和Groovy）之间的互操作性。

5.JMS组件

    (1)JMS客户端：用于发送（或产生或发布）或接收（或使用或订阅）消息的Java程序。

    (2)JMS Sender：用于将消息发送到目标系统的JMS客户端。JMS发送者也称为JMS生产者或JMS发布者。

    (3)JMS接收器：JMS客户端，用于从源系统接收消息。JMS接收器也称为JMS使用者或JMS订阅者。

    (4)JMS Provider：JMS API是一组通用接口，不包含任何实现。 JMS Provider是第三方系统，负责实施JMS API以向客户端提供消息传递功能。JMS Provider也称为MOM（面向消息的中间件）软件或Message Broker或JMS Server或Messaging Server。JMS Provider还提供了一些UI组件来管理和控制此MOM软件。
    
    (5)JMS管理对象 ：管理员为使用JMS客户端而预先配置的JMS对象。 它们是ConnectionFactory和目标对象。
         ConnectionFactory：ConnectionFactory对象用于在Java应用程序和JMS Provider之间创建连接。 应用程序使用它与JMS Provider进行通信。
         目标：目标也是JMS客户端使用的JMS对象，用于指定其发送的消息的目的地和接收的消息的源。有两种类型的目标：队列和主题。

    (6)JMS消息：一个对象，其中包含在JMS客户端之间传输的数据。JMS消息由以下三部分组成：
         消息头（header）—JMS消息头包含许多字段，它们是消息发送后由JMS提供者或消息发送者产生的，用来表示消息、设置优先权和失效时间等等，并且为消息确定路由。
         属性（property）—用来添加删除消息头以外的附加消息。
         消息体（body）—JMS中定义了5种消息体：ByteMessage、MapMessage、ObjectMessage、StreamMessage和TextMessage。

6.JMS消息的可靠性

消息的可靠性通过三个方面保证：消息的持久化、事务、消息的签收。

(1)消息的持久化

消息的持久化是通过设置DeliveryMode实现的，DeliveryMode有两种模式：

    DeliveryMode.PERSISTENT：持久化，服务器宕机重启后消息依然存在
    DeliveryMode.NON_PERSISTENT：非持久化，服务器宕机再重启消息将不存在

Queue中的消息默认是持久化的，Topic中的消息默认是非持久化的。
对于Topic，消费者采用MessageConsumer和采用TopicSubscriber消费消息是不同的，JMS不会将MessageConsumer对象持久化(也就无法记录时间节点)，但是会将TopicSubscriber对象持久化，这样就可以记录每个订阅者的订阅时间点，即使消费者掉线，也能在恢复后消费掉线时产生的消息；采用TopicSubscriber方式消费消息时需要消息持久化。
消息的持久化和消息的订阅模式是完全不同的两个概念，它们之间没有任何关系，只不过消息的持久化是否有意义需要参考消息的消费方式。
消息的持久化可以在两个地方设置：

    a、调用生产者的MessageProducer.setDeliveryMode()方法，设置该生产者生产的所有消息的持久化模式，除非单独为某个消息设置了持久化模式
    b、调用消息的Message.setJMSDeliveryMode()，只设置这一条消息的持久化模式

(2)事务

在通过Connection创建Session的时候可以指明这个Session下的消息生产者和消息消费者是否以事务的方式发送和消费消息：

    Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

事务中的操作作为一个原子的整体，要么一次性全部提交，要么全部回滚。以事务的方式发送和消费消息，需要显式的提交和回滚事务，在事务未提交之前是不会生效的。

    try {
        ...
        session.commit();
    } catch (JMSException e) {
        session.rollback();
        e.printStackTrace();
    }

(3)消息的签收

消息的签收是消息被消费的标志，消息的签收机制是为了避免消息的重复消费，因此消息的签收是相对于消费者的，对于生产者几乎没有意义。
对于Queue中的消息，一旦消息被签收则这条消息的状态就会从待消费状态(Pending Messages)变为已消费状态(Messages Dequeued)，从而从待消费队列中移除。
对于Topic中的消息，若采用MessageConsumer消费消息则签收机制是没有意义的，因为MessageConsumer只能消费Topic中自消费者在MQ服务器注册之后推送到Topic中的消息，至于这之前的消息签收与否MessageConsumer不关心(因为看不到之前的消息)，也就是说使用MessageConsumer消费Topic中的消息时是不会存在重复消费的问题的；
若采用TopicSubscriber消费消息，签收机制避免重复消费消息的作用就凸显出来了，此时消息的签收将会作为某个订阅者(以Connection的ClientID作为标识)已消费过Topic中的某个消息的标志，也就是说消费者每次上线后都只会消费订阅的Topic中未被签收的消息，已签收的消息则不会被重复消费。

消息的签收机制有四种：

    a、Session.AUTO_ACKNOWLEDGE：值为 1，自动签收，消费一条签收一条
    b、Session.CLIENT_ACKNOWLEDGE：值为 2，客户端手动签收，需显示调用Message.acknowledge()方法完成签收
    c、Session.DUPS_OK_ACKNOWLEDGE：值为 3，不必必须签收，消息可能会重复发送。在第二次重新传递消息的时候，消息头的JmsDelivered会被置为true标示当前消息已经传送过一次，客户端需要进行消息的重复处理控制。
    d、Session.SESSION_TRANSACTED：值为 0，以事务的方式签收，该种方式创建Session时事务必须设置为 true。

事务对消息签收的影响：消息签收是事务控制的一部分

    a、若创建Session时是以事务的方式创建的，此时只要事务提交就会将所有消息的签收状态置为已签收，只要事务不提交则消息的签收状态就不起作用
    b、若创建Session时是以非事务的方式创建的，则对消息的签收有没有影响

N.参考

(1)[JMS介绍](https://www.cnblogs.com/wuyongyin/p/14929636.html)

# Kafka

1.Kafka基本概念

Broker（节点）：Kafka服务节点，简单来说一个Broker就是一台Kafka服务器，一个物理节点。
Topic（主题）：在Kafka中消息以主题为单位进行归类，每个主题都有一个Topic Name，生产者根据Topic Name将消息发送到特定的Topic，消费者则同样根据Topic Name从对应的Topic进行消费。
Partition（分区）：Topic（主题）是消息归类的一个单位，但每一个主题还能再细分为一个或多个Partition（分区），一个分区只能属于一个主题。主题和分区都是逻辑上的概念，举个例子，消息1和消息2都发送到主题1，它们可能进入同一个分区也可能进入不同的分区（所以同一个主题下的不同分区包含的消息是不同的），之后便会发送到分区对应的Broker节点上。
Offset（偏移量）：分区可以看作是一个只进不出的队列（Kafka只保证一个分区内的消息是有序的），消息会往这个队列的尾部追加，每个消息进入分区后都会有一个偏移量，标识该消息在该分区中的位置，消费者要消费该消息就是通过偏移量来识别。

消息队列的架构设计理念：
高可扩展：broker -> topic -> partition，每个partition放一个机器，就存一部分数据。如果现在资源不够了，简单啊，给topic增加partition，然后做数据迁移，增加机器，不就可以存放更多数据，提供更高的吞吐量了。
持久化：落磁盘，才能保证别进程挂了数据就丢了。那落磁盘的时候怎么落啊？顺序写，这样就没有磁盘随机读写的寻址开销，磁盘顺序读写的性能是很高的，这就是kafka的思路。
高可用保障机制：多副本 -> leader & follower -> broker挂了重新选举leader即可对外服务。

2.kafka可以脱离zookeeper单独使用吗？为什么？

不可以，kafka必须要依赖一个zookeeper集群才能运行。kafka系群里面各个broker都是通过zookeeper来同步topic列表以及其它broker列表的，一旦连不上zookeeper，kafka也就无法工作。

3.kafka有几种数据保留的策略

Kafka Broker默认的消息保留策略是：要么保留一定时间，要么保留到消息达到一定大小的字节数。当消息达到设置的条件上限时，旧消息就会过期并被删除，所以，在任何时刻，可用消息的总量都不会超过配置参数所指定的大小。
topic可以配置自己的保留策略，可以将消息保留到不再使用他们为止。因为在一个大文件里查找和删除消息是很费时的事，也容易出错，所以，分区被划分为若干个片段。默认情况下，每个片段包含1G或者一周的数据，以较小的那个为准。在broker往leader分区写入消息时，如果达到片段上限，就关闭当前文件，并打开一个新文件。当前正在写入数据的片段叫活跃片段。当所有片段都被写满时，会清除下一个分区片段的数据，如果配置的是7个片段，每天打开一个新片段，就会删除一个最老的片段，循环使用所有片段。

4.kafka数据分区策略

第一种分区策略：给定了分区号，直接将数据发送到指定的分区里面去。
第二种分区策略：没有给定分区号，给定数据的key值，通过key取上hashCode进行分区。
第三种分区策略：既没有给定分区号，也没有给定key值，直接轮循进行分区。
第四种分区策略：自定义分区。

5.kafka高可用性

broker进程就是kafka在每台机器上启动的自己的一个进程。每台机器+机器上的broker进程，就可以认为是kafka集群中的一个节点。
kafka集群由多个broker组成，每个broker是一个节点；创建一个topic可以划分为多个partition，每个partition可以存在于不同的broker上，每个partition就放一部分数据，就是说一个topic的数据，是分散放在多个机器上的，每个机器就放一部分数据。
在Kafka 0.8版本以前，是没有多副本冗余机制的，一旦一个节点挂掉，那么这个节点上的所有Partition的数据就无法再被消费。这就等于发送到Topic的有一部分数据丢失了。
在0.8版本后引入副本机制，则很好地解决宕机后数据丢失的问题。副本是以Topic中每个Partition的数据为单位，每个Partition的数据会同步到其他物理节点上，形成多个副本。每个Partition的副本都包括一个Leader副本和多个Follower副本，Leader由所有的副本共同选举得出，其他副本则都为Follower副本。
在生产者写或者消费者读的时候，都只会与Leader打交道，在写入数据后Follower就会来拉取数据进行数据同步。
只能读写leader？很简单，要是你可以随意读写每个follower，那么就要care数据一致性的问题，系统复杂度太高，很容易出问题。kafka会均匀的将一个partition的所有replica分布在不同的机器上，这样才可以提高容错性。
如果某个broker宕机了，没事儿，那个broker上面的partition在其他机器上都有副本的，如果这上面有某个partition的leader，那么此时会重新选举一个新的leader出来，大家继续读写那个新的leader即可。这就有所谓的高可用性了。
写数据的时候，生产者就写leader，然后leader将数据落地写本地磁盘，接着其他follower自己主动从leader来pull数据。一旦所有follower同步好数据了，就会发送ack给leader，leader收到所有follower的ack之后，就会返回写成功的消息给生产者。（当然，这只是其中一种模式，还可以适当调整这个行为）
消费的时候，只会从leader去读，但是只有一个消息已经被所有follower都同步成功返回ack的时候，这个消息才会被消费者读到。
集群的数量不是越多越好，最好不要超过7个，因为节点越多，消息复制需要的时间就越长，整个群组的吞吐量就越低。集群数量最好是单数，因为超过一半故障集群就不能用了，设置为单数容错率更高。
多少个副本才算够用？ 副本肯定越多越能保证Kafka的高可用，但越多的副本意味着网络、磁盘资源的消耗更多，性能会有所下降，通常来说副本数为3即可保证高可用，极端情况下将replication-factor参数调大即可。

Follower和Lead之间没有完全同步怎么办？
 Follower和Leader之间并不是完全同步，但也不是完全异步，而是采用一种ISR机制（In-Sync Replica）。每个Leader会动态维护一个ISR列表，该列表里存储的是和Leader基本同步的Follower。如果有Follower由于网络、GC等原因而没有向Leader发起拉取数据请求，此时Follower相对于Leader是不同步的，则会被踢出ISR列表。所以说，ISR列表中的Follower都是跟得上Leader的副本。

一个节点宕机后Leader的选举规则是什么？ 
分布式相关的选举规则有很多，像Zookeeper的Zab、Raft、Viewstamped Replication、微软的PacificA等。而Kafka的Leader选举思路很简单，基于我们上述提到的ISR列表，当宕机后会从所有副本中顺序查找，如果查找到的副本在ISR列表中，则当选为Leader。另外还要保证前任Leader已经是退位状态了，否则会出现脑裂情况（有两个Leader）。怎么保证？Kafka通过设置了一个controller来保证只有一个Leader。

关于request.required.asks参数：
Asks这个参数是生产者客户端的重要配置，发送消息的时候就可设置这个参数。该参数有三个值可配置：0、1、All。
第一种是设为0，意思是生产者把消息发送出去之后，之后这消息是死是活咱就不管了，有那么点发后即忘的意思，说出去的话就不负责了。不负责自然这消息就有可能丢失，那就把可用性也丢失了。
第二种是设为1，意思是生产者把消息发送出去之后，这消息只要顺利传达给了Leader，其他Follower有没有同步就无所谓了。存在一种情况，Leader刚收到了消息，Follower还没来得及同步Broker就宕机了，但生产者已经认为消息发送成功了，那么此时消息就丢失了。注意，设为1是Kafka的默认配置。可见Kafka的默认配置也不是那么高可用，而是对高可用和高吞吐量做了权衡折中。
第三种是设为All（或者-1），意思是生产者把消息发送出去之后，不仅Leader要接收到，ISR列表中的Follower也要同步到，生产者才会任务消息发送成功。进一步思考，Asks=All就不会出现丢失消息的情况吗？答案是否。当ISR列表只剩Leader的情况下，Asks=All相当于Asks=1，这种情况下如果节点宕机了，还能保证数据不丢失吗？因此只有在Asks=All并且有ISR中有两个副本的情况下才能保证数据不丢失。

6.安装与简单使用

(1)安装

    // 参考[这里](https://blog.csdn.net/u010283894/article/details/77106159)。
    
    // 启动ZooKeeper
    cd D:\zookeeper\zookeeper-3.4.12\bin
    zkserver
    
    // 运行Kafka
    cd D:\kafka\kafka_2.11-0.10.0.0
    .\bin\windows\kafka-server-start.bat .\config\server.properties
    
    // 主题相关
    // 新建主题：
    cd D:\kafka\kafka_2.11-0.10.0.0\bin\windows
    kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test001
    // 显示已有主题：
    kafka-topics.bat --list --zookeeper localhost:2181
    // 显示主题详细信息：
    kafka-topics.bat --describe --zookeeper localhost:2181

    // 启动生产者
    cd D:\kafka\kafka_2.11-0.10.0.0\bin\windows
    kafka-console-producer.bat --broker-list localhost:9092 --topic test001

    // 启动消费者
    cd D:\kafka\kafka_2.11-0.10.0.0\bin\windows
    kafka-console-consumer.bat --zookeeper localhost:2181 --topic test001

    // 在生产者中输入任何信息，消费者中会显示相应的信息。

(2)Java实例

在应用启动实例化MessageController时，启动消费者线程。

    package zhanghao90.horizon.admin.controller.tool;

    import com.alibaba.fastjson.JSON;
    import com.alibaba.fastjson.JSONObject;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.*;
    import zhanghao90.horizon.admin.constants.AdminConstant;
    import zhanghao90.horizon.admin.controller.BaseController;
    import zhanghao90.horizon.common.common.Resp;
    import zhanghao90.horizon.common.exception.SimpleException;
    import zhanghao90.horizon.common.plugin.kafka.KafkaUtil;

    /**
     * kafka消息发送
     */

    @Controller
    @RequestMapping("message")
    public class MessageController extends BaseController{


        static {
            // 实例化controller时就启动消费者
            KafkaUtil.startMessageConsumer(AdminConstant.KAFKA_TOPIC);
        }

        /**
         * 消息页面
         * @return 页面
         */
        @RequestMapping(value = {"/","/index"})
        public String index(){
            return "/tool/message/index";
        }


        /**
         * 发送消息
         * @param params JSON参数
         * @return JSON应答
         */
        @RequestMapping(value = "sendMessage",method = RequestMethod.POST)
        public @ResponseBody String sendMessage(@RequestBody String params){
            JSONObject paramsObj = JSONObject.parseObject(params);
            String topic = AdminConstant.KAFKA_TOPIC;
            String message = paramsObj.getString("message");

            JSONObject data = new JSONObject();
            Resp resp = new Resp(AdminConstant.RESP_ERROR_CODE_DEFAULT, AdminConstant.RESP_ERROR_MSG_DEFAULT,data);
            try {
                KafkaUtil.sendMessage(topic,message);
                resp.setRespCode(AdminConstant.RESP_SUCCESS_CODE);
                resp.setRespMsg(AdminConstant.RESP_SUCCESS_MSG);
            }
            catch (SimpleException e){
                resp.setRespCode(e.getErrCode());
                resp.setRespMsg(e.getErrMsg());
            }
            catch (Exception e){
                e.printStackTrace();
                resp.setRespMsg(e.getMessage());
            }
            return JSON.toJSONString(resp);
        }

    }

调用KafkaUtil中的sendMessage方法可以发送消息。

    package zhanghao90.horizon.common.plugin.kafka;

    import org.apache.kafka.clients.consumer.ConsumerConfig;
    import org.apache.kafka.clients.consumer.ConsumerRecord;
    import org.apache.kafka.clients.consumer.ConsumerRecords;
    import org.apache.kafka.clients.consumer.KafkaConsumer;
    import org.apache.kafka.clients.producer.*;
    import org.apache.kafka.common.serialization.StringDeserializer;
    import org.apache.kafka.common.serialization.StringSerializer;
    import org.apache.log4j.Logger;
    import zhanghao90.horizon.common.constants.CommonConstant;
    import zhanghao90.horizon.common.exception.SimpleException;
    import zhanghao90.horizon.common.util.PropertiesUtil;
    import java.util.Arrays;
    import java.util.Properties;

    public class KafkaUtil {

        private static Logger logger = Logger.getLogger(KafkaUtil.class);
        private static Properties producerConfigs = initProducerConfig();
        private static Properties consumerConfigs = initConsumerConfig();
        private static KafkaProducer<String,String> producer = null;
        private static KafkaConsumer<String,String> consumer = null;


        /**
         * 生产者初始化配置
         * @return 配置
         */
        private static Properties initProducerConfig(){
            Properties properties = new Properties();
            String brokerList = PropertiesUtil.getConfigInfo("kafka.host") + ":" +  PropertiesUtil.getConfigInfo("kafka.port");
            properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, brokerList);
            properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
            properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());
            return properties;
        }

        /**
         * 消费者初始化配置
         * @return 配置
         */
        private static Properties initConsumerConfig(){
            Properties properties = new Properties();
            String brokerList = PropertiesUtil.getConfigInfo("kafka.host") + ":" +  PropertiesUtil.getConfigInfo("kafka.port");
            properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, brokerList);
            properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
            properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,StringDeserializer.class.getName());
            properties.setProperty(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
            /*properties.setProperty(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");*/
            properties.put(ConsumerConfig.GROUP_ID_CONFIG,"0");
            return properties;
        }


        /**
         * 发送kafka消息到指定的topic
         * @param topic topic
         * @param message 消息
         * @throws SimpleException 异常
         */
        @SuppressWarnings("unchecked")
        public static void sendMessage(String topic,String message) throws SimpleException{
            try {
                if(producer == null){
                    producer = new KafkaProducer<>(producerConfigs);
                }
                ProducerRecord record = new ProducerRecord<String, String>(topic, message);
                //发送消息
                producer.send(record, new Callback() {
                    @Override
                    public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                        if (null != e){
                            logger.info("send error" + e.getMessage());
                        }
                        else {
                            logger.info("send success:" + String.format("offset:%s,partition:%s",recordMetadata.offset(),recordMetadata.partition()));
                        }
                    }
                });
            }
            catch (Exception e){
                e.printStackTrace();
                producer.close();
                throw new SimpleException(CommonConstant.RESP_ERROR_CODE_KAFKA_SEND, CommonConstant.RESP_ERROR_MSG_KAFKA_SEND);
            }
        }

        /**
         * 启动消费者
         * @param topic 主题
         */
        public static void startMessageConsumer(String topic){
            new Thread(new KafkaConsumerRunnable(topic)).start();
        }

        /**
         * 消费者线程
         */
        private static class KafkaConsumerRunnable implements Runnable{
            String topic;

            public KafkaConsumerRunnable(String topic) {
                this.topic = topic;
            }

            @Override
            public void run() {
                try{
                    if (consumer == null){
                        consumer = new KafkaConsumer<>(consumerConfigs);
                        consumer.subscribe(Arrays.asList(this.topic));
                        while (true) {
                            ConsumerRecords<String, String> records = consumer.poll(10);
                            for (ConsumerRecord<String, String> record : records) {
                                logger.info("receive message:" + record.value());
                            }
                        }
                    }
                }
                catch (Exception e){
                    e.printStackTrace();
                    consumer.close();
                }
            }
        }
    }

(3)参考

[kafka在windows上的安装、运行](https://blog.csdn.net/u010283894/article/details/77106159)

[Kafka教程](https://www.w3cschool.cn/apache_kafka/)

# 参考

1.[消息队列MQ/JMS/Kafka介绍](https://blog.csdn.net/Pastxu/article/details/124533142)