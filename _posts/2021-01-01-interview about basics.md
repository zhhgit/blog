---
layout: post
title: "后端开发面试题 -- 计算机基础、架构篇"
description: 后端开发面试题 -- 计算机基础、架构篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# Linux

1.Linux环境下的network IO模型

对于一个network IO (这里我们以read举例)，它会涉及到两个系统对象，一个是调用这个IO的process (or thread)，另一个就是系统内核(kernel)。
当一个read操作发生时，它会经历两个阶段：等待数据准备、将数据从内核拷贝到进程中。记住这两点很重要，因为这些IO Model的区别就是在两个阶段上各有不同的情况。

blocking：
当用户进程调用了recvfrom这个系统调用，kernel就开始了IO的第一个阶段：准备数据。对于network io来说，很多时候数据在一开始还没有到达（比如，还没有收到一个完整的UDP包），这个时候kernel就要等待足够的数据到来。而在用户进程这边，整个进程会被阻塞。当kernel一直等到数据准备好了，它就会将数据从kernel中拷贝到用户内存，然后kernel返回结果，用户进程才解除block的状态，重新运行起来。
所以，blocking IO的特点就是在IO执行的两个阶段都被block了。

non-blocking：
linux下，可以通过设置socket使其变为non-blocking。当用户进程发出read操作时，如果kernel中的数据还没有准备好，那么它并不会block用户进程，而是立刻返回一个error。从用户进程角度讲 ，它发起一个read操作后，并不需要等待，而是马上就得到了一个结果。用户进程判断结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦kernel中的数据准备好了，并且又再次收到了用户进程的system call，那么它马上就将数据拷贝到了用户内存，然后返回。
所以，用户进程其实是需要不断的主动询问kernel数据好了没有。

IO multiplexing：
这个词可能有点陌生，但是如果我说select，epoll，大概就都能明白了。有些地方也称这种IO方式为event driven IO。我们都知道，select/epoll的好处就在于单个process就可以同时处理多个网络连接的IO。它的基本原理就是select/epoll这个function会不断的轮询所负责的所有socket，当某个socket有数据到达了，就通知用户进程。
当用户进程调用了select，那么整个进程会被block，而同时，kernel会“监视”所有select负责的socket，当任何一个socket中的数据准备好了，select就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程。这个图和blocking IO的图其实并没有太大的不同，事实上，还更差一些。因为这里需要使用两个system call (select 和 recvfrom)，而blocking IO只调用了一个system call (recvfrom)。但是，用select的优势在于它可以同时处理多个connection。
如果处理的连接数不是很高的话，使用select/epoll的web server不一定比使用multi-threading + blocking IO的web server性能更好，可能延迟还更大。select/epoll的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接。
在IO multiplexing Model中，实际中，对于每一个socket，一般都设置成为non-blocking，但是，如上图所示，整个用户的process其实是一直被block的。只不过process是被select这个函数block，而不是被socket IO给block。

Asynchronous I/O：
linux下的asynchronous IO其实用得很少。用户进程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从kernel的角度，当它受到一个asynchronous read之后，首先它会立刻返回，所以不会对用户进程产生任何block。然后，kernel会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，kernel会给用户进程发送一个signal，告诉它read操作完成了。


blocking和non-blocking的区别？
调用blocking IO会一直block住对应的进程直到操作完成，而non-blocking IO在kernel还准备数据的情况下会立刻返回。

synchronous IO和asynchronous IO的区别？
两者的区别就在于synchronous IO做”IO operation”的时候会将process阻塞。按照这个定义，之前所述的blocking IO，non-blocking IO，IO multiplexing都属于synchronous IO。
有人可能会说，non-blocking IO并没有被block啊。这里有个非常“狡猾”的地方，定义中所指的”IO operation”是指真实的IO操作，就是例子中的recvfrom这个system call。non-blocking IO在执行recvfrom这个system call的时候，如果kernel的数据没有准备好，这时候不会block进程。
但是，当kernel中数据准备好的时候，recvfrom会将数据从kernel拷贝到用户内存中，这个时候进程是被block了，在这段时间内，进程是被block的。而asynchronous IO则不一样，当进程发起IO 操作之后，就直接返回再也不理睬了，直到kernel发送一个信号，告诉进程说IO完成。在这整个过程中，进程完全没有被block。
asynchronous IO则完全不同。它就像是用户进程将整个IO操作交给了他人（kernel）完成，然后他人做完后发信号通知。在此期间，用户进程不需要去检查IO操作的状态，也不需要主动的去拷贝数据。

N.参考

(1)[【179期】这些最常用的Linux命令都不会，你怎么敢去面试？](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247486062&idx=1&sn=fa33b0f42303ab221f376dc5d72f0676&chksm=e80dbc18df7a350e3454b424ee7ff551dcb3ef9d489ff1affbaf717104758af72054003420ed&scene=21#wechat_redirect)

(2)[Linux命令速查手册](https://mp.weixin.qq.com/s/wmLeQrorGiIFq1uDQOQbqQ)

(3)[【256期】面试官常考的21条Linux命令](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247489274&idx=1&sn=00347609e6b24b1d0dfdd393f562dec3&chksm=e80da08cdf7a299a40ecb331afbf5b42593964952aa391f375815796390746876b95a88cb1bb&scene=21#wechat_redirect)

# 问题排查与调优

1.常用命令

    // 查看当前登录活动用户情况
    w
    // 系统启动时间与负载
    uptime
    // 请求数
    natstat -na | wc -l
    // CPU
    top
    // 内存
    free -m
    // 查看GC
    jstat -gc PID
    // 查看堆栈
    jstat -l PID
    jstack -l PID
    
2.free命令详解

    (1)Mem：系统内存使用情况的全局描述
    total：系统的物理内存总量，total = used + free
    used：已使用的物理内存，used = shared + buffers + cached + (-/+ buffers/cache那栏的used）
    free：空闲的物理内存，即既没有被进程使用，也没有用作操作系统的buffers和cached。
    shared：共享内存用量，如存放共享库。
    buffers：用于缓冲操作系统的目录文件，inode的值，如使用ls命令查看大目录时，这个值会增加
    cached：用于操作系统页缓存，主要用于缓存已打开的文件。操作系统为了避免频繁的磁盘读写操作，会尽可能使用空闲的内存来缓存已打开的文件，即从磁盘读取出来的文件。如果频繁进行文件读写操作，则这个值会增大。Linux会把内存当作是硬盘的高速缓存。

    (2)-/+ buffers/cache：进程的内存使用情况
    used：进程所使用的内存大小，由于Mem中的buffers和cached在内存不足时，即无法满足进程的内存使用需求时，可以被操作系统自动回收，所以实际的进程内存使用量为：Mem那栏的：used - buffers - cached，如上面的统计：5.5G - 1.5G - 21M 约等于 4G。
    free：可供进程使用的内存大小，由于buffers和cached均可以被自动回收，故实际进程可用的内存量为：Mem那栏的：free + buffers + cached，如上面统计：194M + 21M + 1.5G 约等于 1.7G。
    所以在怀疑系统内存不足时，主要关注这里的used和free即可，如果该栏的free较大，则说明目前还有较多的可用内存，而不是关注Mem那栏的free。
    
    (3)Swap：交换分区的使用情况
    used：已使用的交换分区量。如果这个值比较大，一般是某个时刻内存不够用了，将大量内存的数据换出到交换分区。如果之后内存变为可用，将内容重新加载回了内存，这个值也不会马上变小，即该内容并没有被交换分区马上删除。这样做主要是为了在之后如果需要将该内容重新换出，由于交换分区还有，故不需要重新进行将该内容写出的操作，提供系统性能。
    free：可使用的交换分区量

3.CPU飙升问题排查步骤

执行top命令：查看所有进程占系统CPU的排序。极大可能排第一个的就是咱们的java进程（COMMAND列）。PID那一列就是进程号。
执行top -Hp 进程号命令：查看java进程下的所有线程占CPU的情况。
执行printf "%x\n" 线程号命令 ：后续查看线程堆栈信息展示的都是十六进制，为了找到咱们的线程堆栈信息，咱们需要把线程号转成16进制。例如,printf "%x\n 10-》打印：a，那么在jstack中线程号就是0xa。
执行jstack 进程号 | grep 线程ID 查找某进程下-》线程ID（jstack堆栈信息中的nid）=0xa的线程状态。如果"VM Thread" os_prio=0 tid=0x00007f871806e000 nid=0xa runnable，第一个双引号圈起来的就是线程名，如果是“VM Thread”这就是虚拟机GC回收线程了。
执行jstat -gcutil 进程号 统计间隔毫秒 统计次数（缺省代表一致统计），查看某进程GC持续变化情况，如果发现返回中FGC很大且一直增大-》确认Full GC! 也可以使用jmap -heap 进程ID查看一下进程的堆内从是不是要溢出了，特别是老年代内从使用情况一般是达到阈值(具体看垃圾回收器和启动时配置的阈值)就会进程Full GC。
执行jmap -dump:format=b,file=filename 进程ID，导出某进程下内存heap输出到文件中。可以通过eclipse的mat工具（内存泄露工具）查看内存中有哪些对象比较多。

原因分析：

(1)内存消耗过大，导致Full GC次数过多。
执行步骤1-5：多个线程的CPU都超过了100%，通过jstack命令可以看到这些线程主要是垃圾回收线程-》上一节步骤2。
通过jstat命令监控GC情况，可以看到Full GC次数非常多，并且次数在不断增加。--》上一节步骤5
确定是Full GC,接下来找到具体原因：
生成大量的对象，导致内存溢出-》执行步骤6，查看具体内存对象占用情况。
或内存占用不高，但是Full GC次数还是比较多，此时可能是代码中手动调用 System.gc()导致GC次数过多，这可以通过添加 -XX:+DisableExplicitGC来禁用JVM对显示GC的响应。

(2)代码中有大量消耗CPU的操作，导致CPU过高，系统运行缓慢；
执行步骤1-4：在步骤4jstack，可直接定位到代码行。例如某些复杂算法，甚至算法BUG，无限循环递归等等。

(3)由于锁使用不当，导致死锁。
执行步骤1-4：如果有死锁，会直接提示。关键字：deadlock.步骤四，会打印出业务死锁的位置。
造成死锁的原因：最典型的就是2个线程互相等待对方持有的锁。

(4)随机出现大量线程访问接口缓慢。
代码某个位置有阻塞性的操作，导致该功能调用整体比较耗时，但出现是比较随机的；平时消耗的CPU不多，而且占用的内存也不高。
首先找到该接口，通过压测工具不断加大访问力度，大量线程将阻塞于该阻塞点。
执行步骤1-4：找到业务代码阻塞点，这里业务代码使用了TimeUnit.sleep()方法，使线程进入了TIMED_WAITING(期限等待)状态。

    "http-nio-8080-exec-4" #31 daemon prio=5 os_prio=31 tid=0x00007fd08d0fa000 nid=0x6403 waiting on condition [0x00007000033db000]
       java.lang.Thread.State: TIMED_WAITING (sleeping)-》期限等待
        at java.lang.Thread.sleep(Native Method)
        at java.lang.Thread.sleep(Thread.java:340)
        at java.util.concurrent.TimeUnit.sleep(TimeUnit.java:386)
        at com.*.user.controller.UserController.detail(UserController.java:18)-》业务代码阻塞点
        
(5)某个线程由于某种原因而进入WAITING状态，此时该功能整体不可用，但是无法复现。
执行步骤1-4：jstack多查询几次，每次间隔30秒，对比一直停留在parking导致的WAITING状态的线程。
例如CountDownLatch倒计时器，使得相关线程等待->AQS->LockSupport.park()。

    "Thread-0" #11 prio=5 os_prio=31 tid=0x00007f9de08c7000 nid=0x5603 waiting on condition [0x0000700001f89000]   
    java.lang.Thread.State: WAITING (parking) ->无期限等待
    at sun.misc.Unsafe.park(Native Method)    
    at java.util.concurrent.locks.LockSupport.park(LockSupport.java:304)    
    at com.*.SyncTask.lambda$main$0(SyncTask.java:8)-》业务代码阻塞点
    at com.*.SyncTask$$Lambda$1/1791741888.run(Unknown Source)    
    at java.lang.Thread.run(Thread.java:748)

N.参考

(1)[记一次线上商城系统高并发的优化](https://www.cnblogs.com/wangjiming/p/13225544.html)

# 网络

1.HTTP响应码301和302代表的是什么，有什么区别，为什么尽量用301？

301代表永久性转移(Permanently Moved)，302代表暂时性转移(Temporarily Moved )。
301和302状态码都表示重定向，就是说浏览器在拿到服务器返回的这个状态码后会自动跳转到一个新的URL地址，这个地址可以从响应的Location首部中获取（用户看到的效果就是他输入的地址A瞬间变成了另一个地址B）——这是它们的共同点。
它们的不同在于，301表示旧地址A的资源已经被永久地移除了（这个资源不可访问了），搜索引擎在抓取新内容的同时也将旧的网址交换为重定向之后的网址；302表示旧地址A的资源还在（仍然可以访问），这个重定向只是临时地从旧地址A跳转到地址B，搜索引擎会抓取新的内容而保存旧的网址。
尽量要使用301跳转是为了防止网址劫持，从网址A做一个302重定向到网址B时，主机服务器的隐含意思是网址A随时有可能改主意，重新显示本身的内容或转向其他的地方。大部分的搜索引擎在大部分情况下，当收到302重定向时，一般只要去抓取目标网址就可以了，也就是说网址B。如果搜索引擎在遇到302转向时，百分之百的都抓取目标网址B的话，就不用担心网址URL劫持了。问题就在于，有的时候搜索引擎，尤其是Google，并不能总是抓取目标网址。
比如说，有的时候A网址很短，但是它做了一个302重定向到B网址，而B网址是一个很长的乱七八糟的URL网址，甚至还有可能包含一些问号之类的参数。很自然的，A网址更加用户友好，而B网址既难看，又不用户友好。这时Google很有可能会仍然显示网址A。由于搜索引擎排名算法只是程序而不是人，在遇到302重定向的时候，并不能像人一样的去准确判定哪一个网址更适当，这就造成了网址URL劫持的可能性。也就是说，一个不道德的人在他自己的网址A做一个302重定向到你的网址B，出于某种原因，Google搜索结果所显示的仍然是网址A，但是所用的网页内容却是你的网址B上的内容，这种情况就叫做网址URL劫持。
简单理解是，从网站A（网站比较烂）上做了一个302跳转到网站B（搜索排名很靠前），这时候有时搜索引擎会使用网站B的内容，但却收录了网站A的地址，这样在不知不觉间，网站B在为网站A作贡献，网站A的排名就靠前了。

2.HTTPS为什么是安全的？

HTTP协议存在一个致命的缺点：不安全，中间人攻击篡改报文。
HTTPS其实是SSL+HTTP的简称，当然现在SSL基本已经被TLS取代了，不过接下来我们还是统一以SSL作为简称，SSL协议其实不止是应用在HTTP协议上，还在应用在各种应用层协议上，例如：FTP、WebSocket。
其实SSL协议大致就和上一节非对称加密的性质一样，握手的过程中主要也是为了交换秘钥，然后再通讯过程中使用对称加密进行通讯。
服务器是通过SSL证书来传递公钥，客户端会对SSL证书进行验证，其中证书认证体系就是确保SSL安全的关键。
在CA认证体系中，所有的证书都是由权威机构来颁发，而权威机构的CA证书都是已经在操作系统中内置的，我们把这些证书称之为CA根证书。
我们的应用服务器如果想要使用SSL的话，需要通过权威认证机构来签发CA证书，我们将服务器生成的公钥和站点相关信息发送给CA签发机构，再由CA签发机构通过服务器发送的相关信息用CA签发机构进行加签，由此得到我们应用服务器的证书，证书会对应的生成证书内容的签名，并将该签名使用CA签发机构的私钥进行加密得到证书指纹，并且与上级证书生成关系链。
当客户端(浏览器)做证书校验时，会一级一级的向上做检查，直到最后的根证书，如果没有问题说明服务器证书是可以被信任的。那么客户端(浏览器)又是如何对服务器证书做校验的呢，首先会通过层级关系找到上级证书，通过上级证书里的公钥来对服务器的证书指纹进行解密得到签名(sign1)，再通过签名算法算出服务器证书的签名(sign2)，通过对比sign1和sign2，如果相等就说明证书是没有被篡改也不是伪造的。

3.SSL证书生成

root.crt:根证书，server.key:服务证书私钥，server.crt:服务证书

根证书生成

    # 生成一个RSA私钥
    openssl genrsa -out root.key 2048
    # 通过私钥生成一个根证书
    openssl req -sha256 -new -x509 -days 365 -key root.key -out root.crt \
        -subj "/C=CN/ST=GD/L=SZ/O=lee/OU=work/CN=fakerRoot"
        
服务器证书生成

    # 生成一个RSA私钥
    openssl genrsa -out server.key 2048
    # 生成一个带SAN扩展的证书签名请求文件
    openssl req -new \
        -sha256 \
        -key server.key \
        -subj "/C=CN/ST=GD/L=SZ/O=lee/OU=work/CN=xxx.com" \
        -reqexts SAN \
        -config <(cat /etc/pki/tls/openssl.cnf \
            <(printf "[SAN]\nsubjectAltName=DNS:*.xxx.com,DNS:*.test.xxx.com")) \
        -out server.csr
    # 使用之前生成的根证书做签发
    openssl ca -in server.csr \
        -md sha256 \
        -keyfile root.key \
        -cert root.crt \
        -extensions SAN \
        -config <(cat /etc/pki/tls/openssl.cnf \
            <(printf "[SAN]\nsubjectAltName=DNS:xxx.com,DNS:*.test.xxx.com")) \
        -out server.crt

4.TCP/IP协议分层模型

(1)物理层将二进制的0和1和电压高低，光的闪灭和电波的强弱信号进行转换。
(2)链路层代表驱动。
(3)网络层。使用IP协议，IP协议基于IP转发分包数据。IP协议是个不可靠协议，不会重发。IP协议发送失败会使用ICMP协议通知失败。ARP解析IP中的MAC地址，MAC地址由网卡出厂提供。IP还隐含链路层的功能，不管双方底层的链路层是啥，都能通信。
(4)传输层。TCP协议面向有连接，能正确处理丢包，传输顺序错乱的问题，但是为了建立与断开连接，需要至少7次的发包收包，资源浪费。UDP面向无连接，不管对方有没有收到，如果要得到通知，需要通过应用层。
(5)会话层以上分层，TCP/IP分层中，会话层，表示层，应用层集中在一起。网络管理通过SNMP协议。

5.TCP三次握手和四次挥手

三次握手：

    客户端–发送带有SYN标志的数据包–一次握手–服务端
    服务端–发送带有SYN/ACK标志的数据包–二次握手–客户端
    客户端–发送带有带有ACK标志的数据包–三次握手–服务端

四次挥手：

    客户端-发送一个FIN，用来关闭客户端到服务器的数据传送
    服务器-收到这个FIN，它发回一个ACK，确认序号为收到的序号加1 。和SYN一样，一个FIN将占用一个序号
    服务器-关闭与客户端的连接，发送一个FIN给客户端
    客户端-发回ACK报文确认，并将确认序号设置为收到序号加1

6.HTTP协议

Http协议是建立在TCP协议基础之上的，当浏览器需要从服务器获取网页数据的时候，会发出一次Http请求。Http会通过TCP建立起一个到服务器的连接通道，当本次请求需要的数据完毕后，Http会立即将TCP连接断开，这个过程是很短的。所以Http连接是一种短连接，是一种无状态的连接。
http传输流：发送端在层与层间传输数据时，没经过一层都会被加上首部信息，接收端每经过一层都会删除一条首部。
所谓的无状态，是指浏览器每次向服务器发起请求的时候，不是通过一个连接，而是每次都建立一个新的连接。如果是一个连接的话，服务器进程中就能保持住这个连接并且在内存中记住一些信息状态。而每次请求结束后，连接就关闭，相关的内容就释放了，所以记不住任何状态，成为无状态连接。
HTTP协议的无状态性是指服务器的协议层无需为不同的请求之间建立任何相关关系，它特指的是协议层的无状态性。但是这并不代表建立在HTTP协议之上的应用程序就无法维持状态。
应用层可以通过会话Session来跟踪用户请求之间的相关性，服务器会为每个会话对象绑定一个唯一的会话ID，浏览器可以将会话ID记录在本地缓存LocalStorage或者Cookie，在后续的请求都带上这个会话ID，服务器就可以为每个请求找到相应的会话状态。

7.HTTP状态码

    2XX 成功
        200 OK，表示从客户端发来的请求在服务器端被正确处理
        204 No content，表示请求成功，但响应报文不含实体的主体部分
        206 Partial Content，进行范围请求
    
    3XX 重定向
        301 moved permanently，永久性重定向，表示资源已被分配了新的URL
        302 found，临时性重定向，表示资源临时被分配了新的URL
        303 see other，表示资源存在着另一个URL，应使用GET 方法定向获取资源
        304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
        307 temporary redirect，临时重定向，和302含义相同
    
    4XX 客户端错误
        400 bad request，请求报文存在语法错误
        401 unauthorized，表示发送的请求需要有通过HTTP认证的认证信息
        403 forbidden，表示对请求资源的访问被服务器拒绝
        404 not found，表示在服务器上没有找到请求的资源
    
    5XX 服务器错误
        500 internal sever error，表示服务器端在执行请求时发生了错误
        503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求

8.HTTP协议格式

HTTP的请求和响应的消息协议是一样的，分为三个部分，起始行、消息头和消息体。这三个部分以CRLF作为分隔符。最后一个消息头有两个CRLF，用来表示消息头部的结束。
HTTP请求的起始行称为请求行，形如GET /index.html HTTP/1.1。HTTP响应的起始行称为状态行，形如200 ok
消息头部有很多键值对组成，多个键值对之间使用CRLF作为分隔符，也可以完全没有键值对。形如Content-Encoding: gzip。
消息体是一个字符串，字符串的长度是由消息头部的Content-Length键指定的。如果没有Content-Length字段说明没有消息体，譬如GET请求就是没有消息体的，POST请求的消息体一般用来放置表单数据。GET请求的响应返回的页面内容也是放在消息体里面的。我们平时调用API返回的JSON内容都是放在消息体里面的。

9.输入url到页面加载都发生了什么事情

    输入地址
    浏览器查找域名的IP地址 这一步包括DNS具体的查找过程，包括：浏览器缓存->系统缓存->路由器缓存...
    浏览器向web服务器发送一个HTTP请求
    服务器的永久重定向响应（从 http://example.com 到 http://www.example.com）
    浏览器跟踪重定向地址
    服务器处理请求
    服务器返回一个 HTTP 响应
    浏览器显示HTML
    浏览器发送请求获取嵌入在 HTML 中的资源（如图片、音频、视频、CSS、JS等等）
    浏览器发送异步请求

# 设计模式

1.设计模式分类

创建型：工厂、抽象工厂、建造者、单例、原型

结构型：适配器、装饰器、代理、外观、桥接、组合、享元

行为型：策略、观察者、模板方法、迭代器、命令、

2.在JDK中几个常用的设计模式？

单例模式（Singleton pattern）用于Runtime，Calendar和其他的一些类中。
工厂模式（Factory pattern）被用于各种不可变的类如 Boolean，像Boolean.valueOf。
观察者模式（Observer pattern）被用于Swing和很多的事件监听中。
装饰器设计模式（Decorator design pattern）被用于多个Java IO类中。

3.单例模式

保证一个类仅有一个实例，并提供一个访问它的全局访问点。

(1)饱汉模式：第一次使用的时候再初始化，即“懒加载”。存在线程不安全问题。

(a)静态方法getInstance上增加synchronized。这种写法能够在多线程中很好的工作，而且看起来它也具备很好的lazy loading，但是，遗憾的是，效率很低，99%情况下不需要同步。

    public class Singleton {
    private static Singleton instance;
    private Singleton (){}
    
        public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
        }
    }
(b)getInstance方法中外层套了一层check，加上synchronized内层的check，即所谓“双重检查锁”（Double Check Lock，简称DCL）。DCL仍然是线程不安全的，由于指令重排序，你可能会得到“半个对象”，即”部分初始化“问题，一部分域被初始化了。
(c)DCL 2.0，在instance上增加了volatile关键字。见下个问题。

(2)饿汉模式

类加载时初始化单例，以后访问时直接返回即可。
这种方式基于classloder机制避免了多线程的同步问题，instance在类装载时就实例化。目前java单例是指一个虚拟机的范围，因为装载类的功能是虚拟机的，所以一个虚拟机在通过自己的ClassLoader装载饿汉式实现单例类的时候就会创建一个类的实例。
这就意味着一个虚拟机里面有很多ClassLoader，而这些classloader都能装载某个类的话，就算这个类是单例，也能产生很多实例。当然如果一台机器上有很多虚拟机，那么每个虚拟机中都有至少一个这个类的实例的话，那这样 就更不会是单例了。(这里讨论的单例不适合集群！)

    public class Singleton {  
        private static Singleton instance = new Singleton();  
        private Singleton (){}  
        public static Singleton getInstance() {  
            return instance;  
        }  
    }

(3)Holder模式（静态内部类）

通过静态的Holder类持有真正实例，间接实现了懒加载。
这种方式同样利用了classloder的机制来保证初始化instance时只有一个线程，这种方式是Singleton类被装载了，instance不一定被初始化。因为SingletonHolder类没有被主动使用，只有显示通过调用getInstance方法时，才会显示装载SingletonHolder类，从而实例化instance。
想象一下，如果实例化instance很消耗资源，我想让他延迟加载！这个时候，这种方式相比饿汉模式就显得很合理。

    public class Singleton {  
        private static class SingletonHolder {  
            private static final Singleton INSTANCE = new Singleton();  
        }  
        private Singleton (){}  
        public static final Singleton getInstance() {  
            return SingletonHolder.INSTANCE;  
        }  
    }

(4)枚举模式

本质上和饿汉模式相同，区别仅在于公有的静态成员变量。
这种方式是Effective Java作者Josh Bloch提倡的方式，它不仅能避免多线程同步问题，而且还能防止反序列化重新创建新的对象，可谓是很坚强的壁垒啊，不过，个人认为由于1.5中才加入enum特性，用这种方式写不免让人感觉生疏，在实际工作中，也很少看见有人这么写过。

    public enum Singleton {
        INSTANCE;
        public void whateverMethod() {
        }
    }

(5)两个问题需要注意

如果单例由不同的类装载器装入，那便有可能存在多个单例类的实例。假定不是远端存取，例如一些servlet容器对每个servlet使用完全不同的类装载器，这样的话如果有两个servlet访问一个单例类，它们就都会有各自的实例。修复办法：

    private static Class getClass(String classname) throws ClassNotFoundException {   
        ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
    
          if(classLoader == null)   
             classLoader = Singleton.class.getClassLoader();   
    
          return (classLoader.loadClass(classname));   
        }   
    }

如果Singleton实现了java.io.Serializable接口，那么这个类的实例就可能被序列化和复原。不管怎样，如果你序列化一个单例类的对象，接下来复原多个那个对象，那你就会有多个单例类的实例。修复办法：

    public class Singleton implements java.io.Serializable {   
        public static Singleton INSTANCE = new Singleton();
        protected Singleton() {}   
        private Object readResolve() {   
            return INSTANCE;   
        }  
    }

4.为什么是双重校验锁实现单例模式呢？

    public class Singleton {
    
        private static volatile Singleton singleton = null;
    
        private Singleton() {
        }
    
        public static Singleton getInstance(){
            //第一次校验singleton是否为空
            if(singleton==null){
                synchronized (Singleton.class){
                    //第二次校验singleton是否为空
                    if(singleton==null){
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }
    
    
        public static void main(String[] args) {
            for (int i = 0; i < 100; i++) {
                new Thread(new Runnable() {
                    public void run() {
                        System.out.println(Thread.currentThread().getName()+" : "+Singleton.getInstance().hashCode());
                    }
                }).start();
            }
        }
    }

第一次校验： 也就是第一个if（singleton==null），这个是为了代码提高代码执行效率，由于单例模式只要一次创建实例即可，所以当创建了一个实例之后，再次调用getInstance方法就不必要进入同步代码块，不用竞争锁。直接返回前面创建的实例即可。
第二次校验： 也就是第二个if（singleton==null），这个校验是防止二次创建实例，假如有一种情况，当singleton还未被创建时，线程t1调用getInstance方法，由于第一次判断singleton==null，此时线程t1准备继续执行，但是由于资源被线程t2抢占了，此时t2页调用getInstance方法。
同样的，由于singleton并没有实例化，t2同样可以通过第一个if，然后继续往下执行，同步代码块，第二个if也通过，然后t2线程创建了一个实例singleton。此时t2线程完成任务，资源又回到t1线程，t1此时也进入同步代码块，如果没有这个第二个if，那么，t1就也会创建一个singleton实例，那么，就会出现创建多个实例的情况，但是加上第二个if，就可以完全避免这个多线程导致多次创建实例的问题。所以说：两次校验都必不可少。
还有，这里的private static volatile Singleton singleton = null;中的volatile也必不可少，volatile关键字可以防止jvm指令重排优化，因为singleton = new Singleton()这句话可以分为三步：为singleton分配内存空间；初始化singleton；将singleton指向分配的内存空间。
但是由于JVM具有指令重排的特性，执行顺序有可能变成 1-3-2。指令重排在单线程下不会出现问题，但是在多线程下会导致一个线程获得一个未初始化的实例。例如：线程T1执行了1和3，此时T2调用getInstance()后发现singleton不为空，因此返回singleton，但是此时的singleton还没有被初始化。使用volatile会禁止JVM指令重排，从而保证在多线程下也能正常执行。
这里还说一下volatile关键字的第二个作用，保证变量在多线程运行时的可见性：在JDK1.2之前，Java的内存模型实现总是从主存（即共享内存）读取变量，是不需要进行特别的注意的。而在当前的Java内存模型下，线程可以把变量保存本地内存（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。要解决这个问题，就需要把变量声明为volatile，这就指示JVM，这个变量是不稳定的，每次使用它都到主存中进行读取。

使用场景：

单例模式只允许创建一个对象，因此节省内存，加快对象访问速度，因此对象需要被公用的场合适合使用，如多个模块使用同一个数据源连接对象等等。如：
需要频繁实例化然后销毁的对象。创建对象时耗时过多或者耗资源过多，但又经常用到的对象。有状态的工具类对象。频繁访问数据库或文件的对象。
资源共享的情况下，避免由于资源操作时导致的性能或损耗等。如上述中的日志文件，应用配置。
控制资源的情况下，方便资源之间的互相通信。如线程池等。

5.工厂模式

这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
意图：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
所谓的工厂方法模式，就是定义一个工厂方法，通过传入的参数，返回某个实例，然后通过该实例来处理后续的业务逻辑。一般的，工厂方法的返回值类型是一个接口类型，而选择具体子类实例的逻辑则封装到了工厂方法中了。通过这种方式，来将外层调用逻辑与具体的子类的获取逻辑进行分离。

6.观察者模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知它的依赖对象。观察者模式属于行为型模式。
意图：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

7.装饰器模式

装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
意图：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。
主要解决：一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。

8.策略模式

策略模式就是一个接口下有多个实现类，而每种实现类会处理某一种情况。

9.实现低耦合就是对两类之间进行解耦，解耦的本质就是将类之间的直接关系转换成间接关系，不管是类向上转型，接口回调还是适配器模式都是在类之间加了一层，将原来的直接关系变成间接关系，使得两类对中间层是强耦合，两类之间变成弱耦合关系。

10.设计模式的7大原则

(1)单一职责原则SRP

单一职责原则的定义是就一个类而言，应该仅有一个引起他变化的原因。也就是说一个类应该只负责一件事情。如果一个类负责了方法M1,方法M2两个不同的事情，当M1方法发生变化的时候，我们需要修改这个类的M1方法，但是这个时候就有可能导致M2方法不能工作。这个不是我们期待的，但是由于这种设计却很有可能发生。所以这个时候，我们需要把M1方法，M2方法单独分离成两个类。让每个类只专心处理自己的方法。
单一职责原则的好处如下：可以降低类的复杂度，一个类只负责一项职责，这样逻辑也简单很多 提高类的可读性，和系统的维护性，因为不会有其他奇怪的方法来干扰我们理解这个类的含义 当发生变化的时候，能将变化的影响降到最小，因为只会在这个类中做出修改。

(2)开放-封闭原则OCP

开闭原则和单一职责原则一样，是非常基础而且一般是常识的原则。开闭原则的定义是软件中的对象(类，模块，函数等)应该对于扩展是开放的，但是对于修改是关闭的。
当需求发生改变的时候，我们需要对代码进行修改，这个时候我们应该尽量去扩展原来的代码，而不是去修改原来的代码，因为这样可能会引起更多的问题。
这个准则和单一职责原则一样，是一个大家都这样去认为但是又没规定具体该如何去做的一种原则。
开闭原则我们可以用一种方式来确保他，我们用抽象去构建框架，用实现扩展细节。这样当发生修改的时候，我们就直接用抽象了派生一个具体类去实现修改。

(3)里氏代换原则LSP

任何基类可以出现的地方，子类一定可以出现。
如果对每一个类型为T1的对象o1,都有类型为T2的对象o2,使得以T1定义的所有程序P在所有对象o1都替换成o2的时候，程序P的行为都没有发生变化，那么类型T2是类型T1的子类型。
所有引用基类的地方必须能够透明地使用其子类的对象。 里氏替换原则通俗的去讲就是：子类可以去扩展父类的功能，但是不能改变父类原有的功能。他包含以下几层意思：
子类可以实现父类的抽象方法，但是不能覆盖父类的非抽象方法。
子类可以增加自己独有的方法。
当子类的方法重载父类的方法时候，方法的形参要比父类的方法的输入参数更加宽松。
当子类的方法实现父类的抽象方法时，方法的返回值要比父类更严格。
里氏替换原则之所以这样要求是因为继承有很多缺点，他虽然是复用代码的一种方法，但同时继承在一定程度上违反了封装。父类的属性和方法对子类都是透明的，子类可以随意修改父类的成员。这也导致了，如果需求变更，子类对父类的方法进行一些复写的时候，其他的子类无法正常工作。所以里氏替换法则被提出来。
确保程序遵循里氏替换原则可以要求我们的程序建立抽象，通过抽象去建立规范，然后用实现去扩展细节，这个是不是很耳熟，对，里氏替换原则和开闭原则往往是相互依存的。

(4)接口隔离原则ISP

接口隔离原则的定义是客户端不应该依赖他不需要的接口。
换一种说法就是类间的依赖关系应该建立在最小的接口上。这样说好像更难懂。我们通过一个例子来说明。我们知道在Java中一个具体类实现了一个接口，那必然就要实现接口中的所有方法。如果我们有一个类A和类B通过接口I来依赖，类B是对类A依赖的实现，这个接口I有5个方法。但是类A与类B只通过方法1,2,3依赖，然后类C与类D通过接口I来依赖，类D是对类C依赖的实现但是他们却是通过方法1,4,5依赖。那么是必在实现接口的时候，类B就要有实现他不需要的方法4和方法5 而类D就要实现他不需要的方法2，和方法3。这简直就是一个灾难的设计。
所以我们需要对接口进行拆分，就是把接口分成满足依赖关系的最小接口，类B与类D不需要去实现与他们无关接口方法。比如在这个例子中，我们可以把接口拆成3个，第一个是仅仅由方法1的接口，第二个接口是包含2,3方法的，第三个接口是包含4,5方法的。这样，我们的设计就满足了接口隔离原则。

(5)依赖倒转原则DIP

依赖倒置原则指的是一种特殊的解耦方式，使得高层次的模块不应该依赖于低层次的模块的实现细节的目的，依赖模块被颠倒了。
高层模块不应该依赖底层模块，两者都应该依赖其抽象。抽象不应该依赖细节，细节应该依赖抽象。
在Java中抽象指的是接口或者抽象类，两者皆不能实例化。而细节就是实现类，也就是实现了接口或者继承了抽象类的类。他是可以被实例化的。高层模块指的是调用端，底层模块是具体的实现类。
在Java中，依赖倒置原则是指模块间的依赖是通过抽象来发生的，实现类之间不发生直接的依赖关系，其依赖关系是通过接口是来实现的。这就是俗称的面向接口编程。
通过面向接口编程，我们的代码就有了很高的扩展性，降低了代码之间的耦合度，提高了系统的稳定性。

(6)最小知识原则

迪米特原则也被称为最小知识原则，他的定义是一个对象应该对其他对象保持最小的了解。
因为类与类之间的关系越密切，耦合度越大，当一个类发生改变时，对另一个类的影响也越大，所以这也是我们提倡的软件编程的总的原则：低耦合，高内聚。迪米特法则还有一个更简单的定义。
只与直接的朋友通信。首先来解释一下什么是直接的朋友：每个对象都会与其他对象有耦合关系，只要两个对象之间有耦合关系，我们就说这两个对象之间是朋友关系。耦合的方式很多，依赖、关联、组合、聚合等。其中，我们称出现成员变量、方法参数、方法返回值中的类为直接的朋友，而出现在局部变量中的类则不是直接的朋友。也就是说，陌生的类最好不要作为局部变量的形式出现在类的内部。

N.参考

(1)[单例模式有几种写法？](https://mp.weixin.qq.com/s/oltq10YKd_6pm4saqkOthA)

# 面经

1.向面试官提问

技术面：做哪块业务，团队规模，人员分工，该岗位职责，团队技术栈。
业务leader面：产品发展方向，盈利模式，团队规模。
HR面：除了薪水外其他福利待遇、公积金、补充医疗、年终奖、职级评定、调薪、股票期权、预期薪水。

# 综合参考

1.[Java最常见的200+面试题及自己梳理的答案--面试必备（一）](https://www.cnblogs.com/cocoxu1992/p/10460251.html)

2.[久伴_不离](https://www.jianshu.com/u/837b81b0eaa9)