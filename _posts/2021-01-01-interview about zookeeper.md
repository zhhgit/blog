---
layout: post
title: "后端开发面试题 -- 中间件篇"
description: 后端开发面试题 -- 中间件篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# ZooKeeper

1.ZooKeeper是什么？

ZooKeeper是一款开源的分布式协调服务框架，为分布式环境提供了一致性服务的功能，常见应用场景有：发布订阅，主动通知，文件管理，集群管理，分布式锁等功能。zk在设计的时候满足了cp两要素，即一致性和分区容错性。
ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，它是集群的管理者，监视着集群中各个节点的状态根据节点提交的反馈进行下一步合理操作。最终，将简单易用的接口和性能高效、功能稳定的系统提供给用户。
客户端的读请求可以被集群中的任意一台机器处理，如果读请求在节点上注册了监听器，这个监听器也是由所连接的zookeeper机器来处理。
对于写请求，这些请求会同时发给其他zookeeper机器并且达成一致后，请求才会返回成功。因此，随着zookeeper的集群机器增多，读请求的吞吐会提高但是写请求的吞吐会下降。
有序性是zookeeper中非常重要的一个特性，所有的更新都是全局有序的，每个更新都有一个唯一的时间戳，这个时间戳称为zxid（Zookeeper Transaction Id）。而读请求只会相对于更新有序，也就是读请求的返回结果中会带有这个zookeeper最新的zxid。

2.ZooKeeper提供了什么？

文件系统、通知机制

3.Zookeeper文件系统

Zookeeper提供一个多层级的节点命名空间（节点称为znode）。与文件系统不同的是，这些节点都可以设置关联的数据，而文件系统中只有文件节点可以存放数据而目录节点不行。
Zookeeper为了保证高吞吐和低延迟，在内存中维护了这个树状的目录结构，这种特性使得Zookeeper不能用于存放大量的数据，每个节点的存放数据上限为1M。

4.四种类型的znode

    (1)PERSISTENT:持久化目录节点，客户端与zookeeper断开连接后，该节点依旧存在
    (2)PERSISTENT-SEQUENTIAL:持久化顺序编号目录节点，客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号
    (3)EPHEMERAL:临时目录节点，客户端与zookeeper断开连接后，该节点被删除
    (4)EPHEMERAL-SEQUENTIAL:临时顺序编号目录节点，客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号

5.Zookeeper通知机制

client端会对某个znode建立一个watcher事件，当该znode发生变化时，这些client会收到zk的通知，然后client可以根据znode变化来做出业务上的改变等。

6.ZooKeeper功能

(1)命名服务（依赖于文件系统）：

命名服务是指通过指定的名字来获取资源或者服务的地址，利用zk创建一个全局的路径，即是唯一的路径，这个路径就可以作为一个名字，指向集群中的集群，提供的服务的地址，或者一个远程的对象等等。

(2)配置管理（依赖于文件系统、通知机制）：

程序分布式的部署在不同的机器上，将程序的配置信息放在zk的znode下，当有配置发生改变时，也就是znode发生变化时，可以通过改变zk中某个目录节点的内容，利用watcher通知给各个客户端，从而更改配置。

(3)集群管理（依赖于文件系统、通知机制）：

所谓集群管理无在乎两点：是否有机器退出和加入、选举master。
对于第一点，所有机器约定在父目录下创建临时目录节点，然后监听父目录节点的子节点变化消息。一旦有机器挂掉，该机器与zookeeper的连接断开，其所创建的临时目录节点被删除，所有其他机器都收到通知：某个兄弟目录被删除。新机器加入也是类似，所有机器收到通知：新兄弟目录加入。
对于第二点，我们稍微改变一下，所有机器创建临时顺序编号目录节点，每次选取编号最小的机器作为master就好。

(4)分布式锁（文件系统、通知机制）:

有了zookeeper的一致性文件系统，锁的问题变得容易。锁服务可以分为两类，一个是保持独占，另一个是控制时序。
对于第一类，我们将zookeeper上的一个znode看作是一把锁，通过createznode的方式来实现。所有客户端都去创建/distribute_lock节点，最终成功创建的那个客户端也即拥有了这把锁。用完删除掉自己创建的distribute_lock节点就释放出锁。
对于第二类，/distribute_lock已经预先存在，所有客户端在它下面创建临时顺序编号目录节点，和选master一样，编号最小的获得锁，用完删除，依次方便。

获取分布式锁的流程:
在获取分布式锁的时候在locker节点下创建临时顺序节点，释放锁的时候删除该临时节点。客户端调用createNode方法在locker下创建临时顺序节点，然后调用getChildren(“locker”)来获取locker下面的所有子节点，注意此时不用设置任何Watcher。
客户端获取到所有的子节点path之后，如果发现自己创建的节点在所有创建的子节点序号最小，那么就认为该客户端获取到了锁。如果发现自己创建的节点并非locker所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到比自己小的那个节点，然后对其调用exist()方法，同时对其注册事件监听器。之后，让这个被关注的节点删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点是否是locker子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听。当前这个过程中还需要许多的逻辑判断。

(5)队列管理（文件系统、通知机制）:

两种类型的队列：
第一类：同步队列，当一个队列的成员都聚齐时，这个队列才可用，否则一直等待所有成员到达。在约定目录下创建临时目录节点，监听节点数目是否是我们要求的数目。
第二类：队列按照FIFO方式进行入队和出队操作，和分布式锁服务中的控制时序场景基本原理一致，入列有编号，出列按编号。在特定的目录下创建PERSISTENT_SEQUENTIAL节点，创建成功时Watcher通知等待的队列，队列删除序列号最小的节点用以消费。此场景下Zookeeper的znode用于消息存储，znode存储的数据就是消息队列中的消息内容，SEQUENTIAL序列号就是消息的编号，按序取出即可。由于创建的节点是持久化的，所以不必担心队列消息的丢失问题。

7.Zookeeper数据复制

Zookeeper作为一个集群提供一致的数据服务，自然，它要在所有机器间做数据复制。数据复制的好处：
容错：一个节点出错，不致于让整个系统停止工作，别的节点可以接管它的工作；
提高系统的扩展能力 ：把负载分布到多个节点上，或者增加节点来提高系统的负载能力；
提高性能：让客户端本地访问就近的节点，提高用户访问速度。

从客户端读写访问的透明度来看，数据复制集群系统分下面两种：
写主(WriteMaster) ：对数据的修改提交给指定的节点。读无此限制，可以读取任何一个节点。这种情况下客户端需要对读与写进行区别，俗称读写分离；
写任意(Write Any)：对数据的修改可提交给任意的节点，跟读一样。这种情况下，客户端对集群节点的角色与变化透明。
对zookeeper来说，它采用的方式是写任意。通过增加机器，它的读吞吐能力和响应能力扩展性非常好，而写，随着机器的增多吞吐能力肯定下降（这也是它建立observer的原因），而响应能力则取决于具体实现方式，是延迟复制保持最终一致性，还是立即复制快速响应。

8.Zookeeper如何保证主从节点的数据状态同步

在zk集群中，保证各个server端的数据一致性其实是依靠了一个叫做zab的基础协议。zab协议的全称是zookeeper atomic broadcast zk原子广播协议，可以理解为是一种用于保证分布式环境下事务一致性的一种协议。
在zk的集群环境中，是由一台主机器用于接受集群的请求指令（暂时称之为leader），然后通过leader将指令复制传递给各个follower角色进行数据复制。
Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。
当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和leader的状态同步以后，恢复模式就结束了。
状态同步保证了leader和Server具有相同的系统状态。

9.zookeeper是如何保证事务的顺序一致性的？

zookeeper采用了递增的事务Id来标识，所有的proposal（提议）都在被提出的时候加上了zxid，zxid实际上是一个64位的数字。
高32位是epoch（时期; 纪元; 世; 新时代）用来标识leader是否发生改变，如果有新的leader产生出来，epoch会自增，低32位用来递增计数。
当新产生proposal的时候，会依据数据库的两阶段过程，首先会向其他的server发出事务执行请求，如果超过半数的机器都能执行并且能够成功，那么就会开始执行。
当leader将数据成功写入超过半数以上的节点的时候就算是写请求提交成功了。

10.Zookeeper下Server工作状态

每个Server在工作过程中有三种状态：

    LOOKING：当前Server不知道leader是谁，正在搜寻。
    LEADING：当前Server即为选举出来的leader。
    FOLLOWING：leader已经选举出来，当前Server与之同步。

11.zookeeper选举

ZK集群至少要有三台。这两种场景下会有选举发生：服务节点初始化；或者半数以上的节点无法和leader进行连接建立。集群里面的zk机器均具有读和写的功能。

在讲解选举之前，我们需要先了解一下什么是zxid。在zk的节点数据中，每次发生数据变动都会有一个流水id做递增的记录，这个id我们称之为zxid，不同机器的zxid可能会有所不同，越大代表当前的数据越新。
实际上每个zk节点都有两个用于记录更新的id，分别是czxid和mzxid。通过名称的缩写可以翻译为：czxid：创建节点时候的xid。mzxid：修改节点数据时候的xid。

投票整体思路：

第一轮投票全部投给自己；第二轮投票给zxid比自己大的相邻节点，如果得票超过半数，选举结束。
假设现在有3台机器参与选举，分别是1，2，3号机器，启动顺序是1，2，3。
第一轮投票：1号机器投票给到1自己，2号机器投票给到2自己，3号机器投票给到3自己。
第二轮投票：1号机器的id< 2号机器的id <3号机器的id，1号机器投票给到2号机器，此时2号获取选票大于总机器数目的一半，所以2号成为leader，2号投票给到3号机器，由于此时2号机器已经是leader了，所以3号机器依然只能和1号机器一起保持为follower角色。所以经过一轮选举之后，2号机当选leader。

当leader崩溃或者leader失去大多数的follower，这时zk进入恢复模式，恢复模式需要重新选举出一个新的leader，让所有的Server都恢复到一个正确的状态。
Zk的选举算法有两种：一种是基于basic paxos实现的，另外一种是基于fast paxos算法实现的。系统默认的选举算法为fast paxos。

fast paxos：
在选举过程中，某Server首先向所有Server提议自己要成为leader，当其它Server收到提议以后，解决epoch和zxid的冲突，并接受对方的提议，然后向对方发送接受提议完成的消息。
重复这个流程，最后一定能选举出Leader。

basic paxos：
(1)选举线程由当前Server发起选举的线程担任，其主要功能是对投票结果进行统计，并选出推荐的Server；
(2)选举线程首先向所有Server发起一次询问(包括自己)；
(3)选举线程收到回复后，验证是否是自己发起的询问(验证zxid是否一致)，然后获取对方的id(myid)，并存储到当前询问对象列表中，最后获取对方提议的leader相关信息(id,zxid)，并将这些信息存储到当次选举的投票记录表中；
(4)收到所有Server回复以后，就计算出zxid最大的那个Server，并将这个Server相关信息设置成下一次要投票的Server；
(5)线程将当前zxid最大的Server设置为当前Server要推荐的Leader，如果此时获胜的Server获得n/2 + 1的Server票数，设置当前推荐的leader为获胜的Server，将根据获胜的Server相关信息设置自己的状态，否则，继续这个过程，直到leader被选举出来。通过流程分析我们可以得出：要使Leader获得多数Server的支持，则Server总数必须是奇数2n+1，且存活的Server的数目不得少于n+1. 每个Server启动后都会重复以上流程。在恢复模式下，如果是刚从崩溃状态恢复的或者刚启动的server还会从磁盘快照中恢复数据和会话信息，zk会记录事务日志并定期进行快照，方便在恢复时进行状态恢复。

12.Zookeeper同步流程

选完Leader以后，zk就进入状态同步过程。

(1)Leader等待server连接；
(2)Follower连接leader，将最大的zxid发送给leader；
(3)Leader根据follower的zxid确定同步点；
(4)完成同步后通知follower已经成为uptodate状态；
(5)Follower收到uptodate消息后，又可以重新接受client的请求进行服务了。

13.分布式通知和协调

对于系统调度来说：操作人员发送通知实际是通过控制台改变某个节点的状态，然后zk将这些变化发送给注册了这个节点的watcher的所有客户端。
对于执行情况汇报：每个工作进程都在某个目录下创建一个临时节点。并携带工作的进度数据，这样汇总的进程可以监控目录子节点的变化获得工作进度的实时的全局情况。

14.机器中为什么会有leader？

在分布式环境中，有些业务逻辑只需要集群中的某一台机器进行执行，其他的机器可以共享这个结果，这样可以大大减少重复计算，提高性能，于是就需要进行leader选举。

15.zk节点宕机如何处理？

Zookeeper本身也是集群，推荐配置不少于3个服务器。Zookeeper自身也要保证当一个节点宕机时，其他节点会继续提供服务。
如果是一个Follower宕机，还有2台服务器提供访问，因为Zookeeper上的数据是有多个副本的，数据并不会丢失；
如果是一个Leader宕机，Zookeeper会选举出新的Leader。ZK集群的机制是只要超过半数的节点正常，集群就能正常提供服务。只有在ZK节点挂得太多，只剩一半或不到一半节点能工作，集群才失效。
所以3个节点的cluster可以挂掉1个节点(leader可以得到2票>1.5)。2个节点的cluster就不能挂掉任何1个节点了(leader可以得到1票<=1)

16.zookeeper负载均衡和nginx负载均衡区别

zookeeper:不存在单点问题，zab机制保证单点故障可重新选举一个leader。只负责服务的注册与发现，不负责转发，减少一次数据交换（消费方与服务方直接通信）。需要自己实现相应的负载均衡算法。
nginx:存在单点问题，单点负载高数据量大。每次负载，都充当一次中间人转发角色，增加网络负载量（消费方与服务方间接通信）。自带负载均衡算法。

17.zookeeper watch机制

Watch机制官方声明：一个Watch事件是一个一次性的触发器，当被设置了Watch的数据发生了改变的时候，则服务器将这个改变发送给设置了Watch的客户端，以便通知它们。Zookeeper机制的特点：

(1)一次性触发数据发生改变时，一个watcher event会被发送到client，但是client只会收到一次这样的信息。
(2)watcher event异步发送watcher的通知事件从server发送到client是异步的，这就存在一个问题，不同的客户端和服务器之间通过socket进行通信，由于网络延迟或其他因素导致客户端在不通的时刻监听到事件，由于Zookeeper本身提供了ordering guarantee，即客户端监听事件后，才会感知它所监视znode发生了变化。所以我们使用Zookeeper不能期望能够监控到节点每次的变化。Zookeeper只能保证最终的一致性，而无法保证强一致性。
(3)数据监视Zookeeper有数据监视和子数据监视。getdata() and exists()设置数据监视，getchildren()设置了子节点监视。
(4)注册watcher getData、exists、getChildren
(5)触发watcher create、delete、setData
(6)setData()会触发znode上设置的data watch（如果set成功的话）。一个成功的create() 操作会触发被创建的znode上的数据watch，以及其父节点上的child watch。而一个成功的delete()操作将会同时触发一个znode的data watch和child watch（因为这样就没有子节点了），同时也会触发其父节点的child watch。
(7)当一个客户端连接到一个新的服务器上时，watch将会被以任意会话事件触发。当与一个服务器失去连接的时候，是无法接收到watch的。而当client重新连接时，如果需要的话，所有先前注册过的watch，都会被重新注册。通常这是完全透明的。只有在一个特殊情况下，watch可能会丢失：对于一个未创建的znode的exist watch，如果在客户端断开连接期间被创建了，并且随后在客户端连接上之前又删除了，这种情况下，这个watch事件可能会被丢失。
(8)Watch是轻量级的，其实就是本地JVM的Callback，服务器端只是存了是否有设置了Watcher的布尔类型

18.ZK分布式锁

使用zk的临时节点和有序节点，每个线程获取锁就是在zk创建一个临时有序的节点，比如在/lock/目录下。
创建节点成功后，获取/lock目录下的所有临时节点，再判断当前线程创建的节点是否是所有的节点的序号最小的节点。
如果当前线程创建的节点是所有节点序号最小的节点，则认为获取锁成功。
如果当前线程创建的节点不是所有节点序号最小的节点，则对节点序号的前一个节点添加一个事件监听。
比如当前线程获取到的节点序号为/lock/003,然后所有的节点列表为/lock/001,/lock/002,/lock/003,则对/lock/002这个节点添加一个事件监听器。
如果锁释放了，会唤醒下一个序号的节点，然后重新执行第3步，判断是否自己的节点序号是最小。
比如/lock/001释放了，/lock/002监听到事件，此时节点集合为[/lock/002,/lock/003],则/lock/002为最小序号节点，获取到锁。

19.事件监听

在读取数据时，我们可以同时对节点设置事件监听，当节点数据或结构变化时，zookeeper会通知客户端。当前zookeeper有如下四种事件：节点创建、节点删除、节点数据修改、子节点变更。
当客户端链接到zk服务端的时候，某个节点如果出现了数据变动，之前有监听过这个节点数据的客户端都会接收到相关信号。
这种主动通知是一种弱最终一致性，而且只会发送一次通知，并不能保证更新的实时准确性。

20.Curator

Curator是一个zookeeper的开源客户端，也提供了分布式锁的实现。使用方式也比较简单：
   
    InterProcessMutex interProcessMutex = new InterProcessMutex(client,"/anyLock");
    interProcessMutex.acquire();
    interProcessMutex.release();

21.Redis分布式锁与ZK分布式锁的优缺点比较

目前流行的是zookeeper和redis，两者各有好处，redis流行的内存缓存，且能进行水平扩容同时还能提高请求负载，面对高并分布式锁数据的读写请求能高速响应，同时有aof,哨兵机制可以防止某台宕机分布式锁数据丢失带来的问题。
zookeeper是分布式一致性算法paxos算法的实现，面对高负载请求毫无压力，同时某一台宕机毫不影响分布式锁数据一致性，且附带了监听机制，当某一程序释放某一个锁后，其他程序可以及时得到通知来获得对该分布式锁的控制权，这里的轮询实现不需要我们去开发了。

Redis：
缺点：它获取锁的方式简单粗暴，获取不到锁直接不断尝试获取锁，比较消耗性能。Redis的设计定位决定了它的数据并不是强一致性的，在某些极端情况下，可能会出现问题。锁的模型不够健壮。即便使用redlock算法来实现，在某些复杂场景下，也无法保证其实现100%没有问题。
优点：使用redis实现分布式锁在很多企业中非常常见，而且大部分情况下都不会遇到所谓的“极端复杂场景”。所以使用redis作为分布式锁也不失为一种好的方案，最重要的一点是redis的性能很高，可以支撑高并发的获取、释放锁操作。
    
ZK：
缺点：如果有较多的客户端频繁的申请加锁、释放锁，对于zk集群的压力会比较大。
优点：zookeeper天生设计定位就是分布式协调，强一致性。锁的模型健壮、简单易用、适合做分布式锁。如果获取不到锁，只需要添加一个监听器就可以了，不用一直轮询，性能消耗较小。

22.Zookeeper命令

(1)客户端、服务器命令

切换至zookeeper安装目录下的bin目录输入以下命令启动服务器或者客户端

    启动ZK服务: ./zkServer.sh start
    查看ZK服务状态: ./zkServer.sh status
    停止ZK服务: ./zkServer.sh stop
    重启ZK服务: ./zkServer.sh restart
    连接内部客户端: ./zkCli.sh或者./zkCli.sh -server 127.0.0.1(指定连接服务器IP):2181
    
(2)节点属性

学习zookeeper常用命令之前先介绍一下节点属性的含义。

    cZxid：当前数据结点创建时的事务ID——针对于zookeeper数据结点的管理：我们对结点数据的一些写操作都会导致zookeeper自动地为我们去开启一个事务，并且自动地去为每一个事务维护一个事务ID
    ctime：当前数据结点创建时的时间
    mZxid：当前数据结点最后一次更新时的事务ID
    mtime：当前数据结点最后一次更新时的时间
    pZxid：当前数据节点最后一次修改其子节点**更改的zxid。修改指(增加子节点、删除子节点)，并不指其子节点的数据发生改变；
    cversion：当前数据节点对应子结点的更改次数
    dataVersion：当前结点数据的发生更改的次数
    aclVersion：当前结点的ACL更改次数——类似linux的权限列表，维护的是当前结点的权限列表被修改的次数
    ephemeralOwner：如果结点是临时结点，则表示创建该结点的会话的SessionID；如果是持久结点，该属性值为0
    dataLength：当前节点的数据内容长度
    numChildren：当前数据结点的子结点个数
    
(3)help命令

zookeeper基本常用命令通过help查看，遇到错误命令可以直接查询语法。

(4)新增、查询节点

    新增命令：create [-s] [-e] path data，其中 -s 为有序结点，-e 临时结点（默认是持久结点）
    查询命令：get [-s] [-w] path。-s 查看节点所有信息:数据信息+节点属性值；-w 查看节点数据信息
        
    //创建持久化节点node1
    [zk: localhost:2181(CONNECTED) 0] create /node1 "123"
    Created /node1
    
    //查看node1节点属性
    [zk: localhost:2181(CONNECTED) 1] get -s /node1
    123
    cZxid = 0x43
    ctime = Wed Jul 29 21:27:31 CST 2020
    mZxid = 0x43
    mtime = Wed Jul 29 21:27:31 CST 2020
    pZxid = 0x43
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 3
    numChildren = 0
    
    //创建有序持久化节点
    [zk: localhost:2181(CONNECTED) 2] create -s /seqNode1 "seq1"
    Created /seqNode10000000011
    
    //查看有序持久化节点信息
    [zk: localhost:2181(CONNECTED) 3] get -s /seqNode10000000011
    seq1
    cZxid = 0x44
    ctime = Wed Jul 29 21:28:25 CST 2020
    mZxid = 0x44
    mtime = Wed Jul 29 21:28:25 CST 2020
    pZxid = 0x44
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 4
    numChildren = 0
    
    //创建临时节点
    [zk: localhost:2181(CONNECTED) 4] create -s -e /tmpNode1 "tmp"
    Created /tmpNode10000000012
    [zk: localhost:2181(CONNECTED) 5] get -s /tmpNode10000000012
    tmp
    cZxid = 0x45
    ctime = Wed Jul 29 21:35:28 CST 2020
    mZxid = 0x45
    mtime = Wed Jul 29 21:35:28 CST 2020
    pZxid = 0x45
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x10029ab39130008
    dataLength = 3
    numChildren = 0
    
(5)修改节点

    set [-s] [-v version] path data，可以直接进行修改;也可以选择使用版本号，-v + 版本号，类似乐观锁原理；

    [zk: localhost:2181(CONNECTED) 13] set /node1 "456"
    [zk: localhost:2181(CONNECTED) 14] get -w /node1
    456
    [zk: localhost:2181(CONNECTED) 15] set -v 0 /node1 "234"

    WATCHER::
    
    WatchedEvent state:SyncConnected type:NodeDataChanged path:/node1
    [zk: localhost:2181(CONNECTED) 16] get -w /node1
    234
    
(6)删除节点

    delete [-v version] path:可以直接删除，也可以指定版本号删除，此命令只能删除单个节点，如果存在子节点，则需要依次删除子节点
    deleteall path：直接删除指定的所有节点
    
    [zk: localhost:2181(CONNECTED) 0] delete /node1
    [zk: localhost:2181(CONNECTED) 1] get -s /node1
    org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /node1
    [zk: localhost:2181(CONNECTED) 4] create /node1 "node1"
    Created /node1
    [zk: localhost:2181(CONNECTED) 5] create /node1/node11 "node11"
    Created /node1/node11
    //使用delete删除存在子节点的节点，删除失败
    [zk: localhost:2181(CONNECTED) 6] delete /node1
    Node not empty: /node1
    [zk: localhost:2181(CONNECTED) 7] get -s /node1
    node1
    cZxid = 0x4f
    ctime = Wed Jul 29 21:53:37 CST 2020
    mZxid = 0x4f
    mtime = Wed Jul 29 21:53:37 CST 2020
    pZxid = 0x50
    cversion = 1
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 5
    numChildren = 1
    [zk: localhost:2181(CONNECTED) 8] deleteall /node1
    [zk: localhost:2181(CONNECTED) 9] get /node1
    org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /node1
    
(7)查看子节点列表

    ls [-s] [-w] [-R] path:
    ls2 path [watch]
    
    [zk: localhost:2181(CONNECTED) 19] ls /
    [a0000000001, b0000000002, c, hadoop, seqNode10000000011, zookeeper]
    [zk: localhost:2181(CONNECTED) 20] ls -s /
    [a0000000001, b0000000002, c, hadoop, seqNode10000000011, zookeeper]
    cZxid = 0x0
    ctime = Thu Jan 01 08:00:00 CST 1970
    mZxid = 0x0
    mtime = Thu Jan 01 08:00:00 CST 1970
    pZxid = 0x53
    cversion = 22
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 0
    numChildren = 6
    
    [zk: localhost:2181(CONNECTED) 21] create /node1 "node1"
    Created /node1
    //当前节点下没有子节点，返回空数组
    [zk: localhost:2181(CONNECTED) 22] ls /node1
    []
    [zk: localhost:2181(CONNECTED) 23] create /node1/node11 "node11"
    Created /node1/node11
    [zk: localhost:2181(CONNECTED) 24] ls /node1
    [node11]

(8)查看节点状态

使用stat命令查看节点状态，与get命令的区别是此命令不返回数据信息；

    [zk: localhost:2181(CONNECTED) 25] stat /node1
    cZxid = 0x55
    ctime = Wed Jul 29 22:05:16 CST 2020
    mZxid = 0x55
    mtime = Wed Jul 29 22:05:16 CST 2020
    pZxid = 0x56
    cversion = 1
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 5
    numChildren = 1
    
(9)监听器

使用 get [-s] [-w] path注册的监听器能够在结点内容发生改变的时候，向客户端发出通知。需要注意的是zookeeper的触发器是一次性的(One-time trigger)，即触发一次后就会立即失效。

    //一个窗口监听，新打开一个窗口修改节点数据
    [zk: localhost:2181(CONNECTED) 29] get -w /node1
    node1
    //收到修改信息
    [zk: localhost:2181(CONNECTED) 30]
    WATCHER::
    
    WatchedEvent state:SyncConnected type:NodeDataChanged path:/node1
    
    //另一个窗口修改节点：
    [zk: localhost:2181(CONNECTED) 0] set /node1 "set node1"

(10)权限控制

zookeeper类似文件系统，client可以创建结点、更新结点、删除结点，那么如何做到结点的权限控制呢？zookeeper的access control list访问控制列表可以做到这一点。
acl权限控制，使用scheme：id：permission来标识，主要涵盖3个方面：权限模式(scheme)：授权的策略；授权对象(id)：授权的对象；权限(permission)：授予的权限。

权限模式(scheme)：

    world：只有一个用户anyone，代表所有人
    ip：对客户端使用IP地址认证
    auth：使用已添加认证的用户认证
    digest：使用用户，密码认证

授权对象(id)：给谁授予权限，授权对象ID是指，权限赋予的实体，例如：IP地址或用户。

权限(permission)：create、delete、read、writer、admin也就是 增、删、查、改、管理权限，这5种权限简写为c d r w a，注意：这五种权限中，有的权限并不是对结点自身操作的例如：delete是指对子结点的删除权限。可以试图删除父结点，但是子结点必须删除干净，所以delete的权限也是很有用的。
授权的相关命令：
    
    getAcl：获取ACL权限
    setAcl：设置ACL权限
    addAuth：添加认证用户

world模式：

    [zk: localhost:2181(CONNECTED) 31] getAcl /node1
    'world,'anyone
    : cdrwa
    [zk: localhost:2181(CONNECTED) 32] setAcl /node1 world:anyone:drwa
    [zk: localhost:2181(CONNECTED) 33] create /node1/node2 "node2"
    Authentication is not valid : /node1/node2
    [zk: localhost:2181(CONNECTED) 34] setAcl /node1 world:anyone:cdrwa
    [zk: localhost:2181(CONNECTED) 35] create /node1/node2 "node2"
    Created /node1/node2
    
IP模式：

需要两台虚拟机一起授权的话需要用逗号将授权列表隔开：setAcl /ipNode ip:192.168.103.133:cdrwa,ip:192.168.103.132:cdrwa

    [zk: localhost:2181(CONNECTED) 8] create /ipNode "ipNode"
    Created /ipNode
    [zk: localhost:2181(CONNECTED) 9] get -s /ipNode
    ipNode
    cZxid = 0x65
    ctime = Wed Jul 29 23:22:23 CST 2020
    mZxid = 0x65
    mtime = Wed Jul 29 23:22:23 CST 2020
    pZxid = 0x65
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 6
    numChildren = 0
    [zk: localhost:2181(CONNECTED) 10] setAcl /ipNode ip:192.168.16.81:ra
    [zk: localhost:2181(CONNECTED) 11] get -s /ipNode
    org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /ipNode

auth模式：

    addauth digest <user>:<password>
    setAcl <path> auth:<user>:<acl>
    
    //认证用户
    [zk: localhost:2181(CONNECTED) 36] addauth digest qxy:123456
    [zk: localhost:2181(CONNECTED) 37] get -s /node1
    set node1
    cZxid = 0x55
    ctime = Wed Jul 29 22:05:16 CST 2020
    mZxid = 0x58
    mtime = Wed Jul 29 22:31:29 CST 2020
    pZxid = 0x5c
    cversion = 2
    dataVersion = 1
    aclVersion = 2
    ephemeralOwner = 0x0
    dataLength = 9
    numChildren = 2
    //设置认证用户
    [zk: localhost:2181(CONNECTED) 38] setAcl /node1 auth:qxy:cdrwa
    //退出，重新进入
    [zk: localhost:2181(CONNECTED) 39] quit
    
    WATCHER::
    
    WatchedEvent state:Closed type:None path:null
    2020-07-29 22:58:56,574 [myid:] - INFO  [main:ZooKeeper@1422] - Session: 0x10029ab39130009 closed
    2020-07-29 22:58:56,574 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@524] - EventThread shut down for session: 0x10029ab39130009
    //未用户认证，无法获取节点信息
    [zk: localhost:2181(CONNECTED) 0] get -s /node1
    org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /node1
    //认证用户，注意此处密码错误，不会提示错误，但是无法访问节点
    [zk: localhost:2181(CONNECTED) 1] addauth digest qxy:123456
    [zk: localhost:2181(CONNECTED) 2] get -s /node1
    set node1
    cZxid = 0x55
    ctime = Wed Jul 29 22:05:16 CST 2020
    mZxid = 0x58
    mtime = Wed Jul 29 22:31:29 CST 2020
    pZxid = 0x5c
    cversion = 2
    dataVersion = 1
    aclVersion = 3
    ephemeralOwner = 0x0
    dataLength = 9
    numChildren = 2

Digest模式：

    setAcl <path> digest:<user>:<password>:<acl>
    
    密码是经过SHA1以及BASE64处理的密文，在shell 中可以通过以下命令计算：
    
     echo -n <user>:<password> | openssl dgst -binary -sha1 | openssl base64
    建立新的窗口，计算密码
    
    [root@izbp14najjyuhkvm4qbic7z bin]# echo -n qxy:123456 | openssl dgst -binary -sha1 | openssl base64
    hDF4uLZvMJqOX2ekKFa6kSz9HNo=
    实战：
    
    [zk: localhost:2181(CONNECTED) 5] create /digestNode "digestNode"
    Created /digestNode
    [zk: localhost:2181(CONNECTED) 2] setAcl /digestNode digest:qxy:hDF4uLZvMJqOX2ekKFa6kSz9HNo=:cdrwa
    [zk: localhost:2181(CONNECTED) 3] get /digestNode
    org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /digestNode
    [zk: localhost:2181(CONNECTED) 2] setAcl /digestNode digest:qxy:hDF4uLZvMJqOX2ekKFa6kSz9HNo=:cdrwa
    [zk: localhost:2181(CONNECTED) 3] get /digestNode
    org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /digestNode
    [zk: localhost:2181(CONNECTED) 4] getAcl /digestNode
    Authentication is not valid : /digestNode
    [zk: localhost:2181(CONNECTED) 5] addauth digest qxy:123456
    [zk: localhost:2181(CONNECTED) 6] getAcl /digestNode
    'digest,'qxy:hDF4uLZvMJqOX2ekKFa6kSz9HNo=
    : cdrwa
    [zk: localhost:2181(CONNECTED) 7] get /digestNode
    digestNode

23.zookeeper的设计理念

(1)一致性：所有的客户端一旦连接到了集群环境中，不论访问的zk是leader角色还是follower角色，每个zk节点的数据都是相同的。假设某一时刻，zk的某个节点数据被修改了，那么此时必须要将每个节点的数据都做同步之后才能继续提供外界读取节点的功能。
(2)有头：在集群环境中，一定会有一个leader的角色充当集群领头。一旦leader挂了，就会重新选举新的机器当选leader。
(3)数据树：zk内部存储数据是采用了树状结构，这一点有些类似于文件系统的设计，每个树状节点底下存放的子节点可以是有序排列的状态。

24.zk集群环境中写入数据过程中发生了什么？

客户端首先连接到zk集群环境，然后将需要写入的数据提交给任意一台zk服务器，如果当前服务器是leader，则由leader接受，然后将数据广播给集群的每一台follower机器。如果接受指令的是follower，则会将请求转给leader，再广播给各个follower机器。

广播细节分为两个步骤：
Leader可以接受客户端新的事务Proposal请求，将新的Proposal请求广播给所有的Follower。如何确认广播出去的请求，follower确认接收呢？需要借助一个ack的标记信息响应进行反馈。
假设集群里面有一半的机器都返回了ack信号，那么此时leader就可以再次广播每个机器执行相应事务的commit操作了，并且执行之后需要在本地记录该行为到事务日志中。

崩溃恢复模式和消息广播模式：
崩溃模式，zk集群中的leader可能因为网络抖动或者某些异常问题，失去了和其余follower的联系，此时剩余的follower需要重新进行选举。或者如果集群中只有不到一半的机器能和leader进行通信，那么此时也算是进入到了崩溃模式，需要重新选举。
广播模式，由leader向各个follower发送同步数据的信号，发送各种事务信息和提交信号。

25.分布式集群管理实现

利用了zk里面的有序节点来实现分布式集群环境下的节点管理，设计的思路页比较简单，给每个应用启动的时候加入一个插件，实时将该程序运作的相关性能属性上报到zk的特定节点中，一旦服务下线，节点的数据也会按时消失。
往zk的一个节点（这里假设是：zk-agent 节点）下创建一批临时有序节点：

    zk-agent/server000001 //假设1号机器启动
    zk-agent/server000002 //假设2号机器启动
    zk-agent/server000003 //假设3号机器启动

每个节点都有相关的写入数据，查看的信息的时候只需要通过访问zk注册中心，读取节点下边的数据即可。当任意一台服务出现异常的时候，相关的服务也需要和zk断开连接，此时节点数据消失。

    {"serverIp":"127.0.0.1","totalCpu":12,"totalMemory":17179869184,"totalMemoryDesc":"16GB","memoryUsage":"58.9%"}
    
为什么不用mysql存储服务性能信息？

其实使用mysql，redis等数据库也是可行的，但是必须一点：服务断开之后数据能够及时同步更新。
而这一点，在使用zookeeper来落地更加适合，因为zookeeper内部的临时节点与生俱来就支持这一特性。

# 参考

1.[ZooKeeper面试题](https://blog.csdn.net/weixin_41847891/article/details/100734093)

2.[什么是ZooKeeper](https://cloud.tencent.com/developer/article/1418528)

3.[【28期】ZooKeeper面试那些事儿](https://mp.weixin.qq.com/s/nLVovl0EdfbfqnyC53nr4w)

# Nacos

1.什么是Nacos

Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。
Nacos帮助您更敏捷和容易地构建、交付和管理微服务平台。Nacos是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。
服务（Service）是Nacos世界的一等公民。Nacos 支持几乎所有主流类型的“服务”的发现、配置和管理：

    Kubernetes Service
    gRPC & Dubbo RPC Service
    Spring Cloud RESTful Service

2.Nacos的实现原理

架构：

    Provider APP：服务提供者
    Consumer APP：服务消费者
    Name Server：通过VIP（Virtual IP）或DNS的方式实现Nacos高可用集群的服务路由
    Nacos Server：Nacos服务提供者，里面包含的Open API是功能访问入口，Conig Service、Naming Service 是Nacos提供的配置服务、命名服务模块。Consitency Protocol是一致性协议，用来实现Nacos集群节点的数据同步，这里使用的是Raft算法（Etcd、Redis哨兵选举）
    Nacos Console：控制台
    
注册中心的原理：

服务实例在启动时注册到服务注册表，并在关闭时注销。
服务消费者查询服务注册表，获得可用实例。
服务注册中心需要调用服务实例的健康检查API来验证它是否能够处理请求。
Nacos提供了SDK和Open API两种形式来实现服务注册。这两种形式本质都一样，底层都是基于HTTP协议完成请求的。所以注册服务就是发送一个HTTP请求。
Nacos客户端通过Open API的形式发送服务注册请求。Nacos服务端收到请求后，做以下三件事：构建一个Service对象保存到ConcurrentHashMap集合中、使用定时任务对当前服务下的所有实例建立心跳检测机制、基于数据一致性协议服务数据进行同步。
    
SpringCloud完成注册的时机：

在Spring-Cloud-Common包中有一个类org.springframework.cloud. client.serviceregistry.ServiceRegistry ,它是Spring Cloud提供的服务注册的标准。集成到Spring Cloud中实现服务注册的组件,都会实现该接口。该接口有一个实现类是NacoServiceRegistry。
NacosAutoServiceRegistration继承了AbstractAutoServiceRegistration。Nacos是通过Spring的事件机制继承到SpringCloud中去的。
AbstractAutoServiceRegistration实现了onApplicationEvent抽象方法,并且监听WebServerInitializedEvent事件(当Webserver初始化完成之后) , 调用this.bind ( event )方法。最终会调用NacosServiceREgistry.register()方法进行服务注册。


