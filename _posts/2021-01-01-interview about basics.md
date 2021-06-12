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

(1)饱汉模式：第一次使用的时候再初始化，即“懒加载”。存在线程不安全问题。
可能解决方案：(a)静态方法getInstance上增加synchronized。(b)getInstance方法中外层套了一层check，加上synchronized内层的check，即所谓“双重检查锁”（Double Check Lock，简称DCL）。DCL仍然是线程不安全的，由于指令重排序，你可能会得到“半个对象”，即”部分初始化“问题，一部分域被初始化了。(c)DCL 2.0，在instance上增加了volatile关键字。

(2)饿汉模式：类加载时初始化单例，以后访问时直接返回即可。
(3)Holder模式：通过静态的Holder类持有真正实例，间接实现了懒加载。
(4)枚举模式：本质上和饿汉模式相同，区别仅在于公有的静态成员变量。

为什么是双重校验锁实现单例模式呢？

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
还有，这里的private static volatile Singleton singleton = null;中的volatile也必不可少，volatile关键字可以防止jvm指令重排优化，因为singleton = new Singleton() 这句话可以分为三步：为singleton分配内存空间；初始化singleton；将singleton指向分配的内存空间。
但是由于JVM具有指令重排的特性，执行顺序有可能变成 1-3-2。指令重排在单线程下不会出现问题，但是在多线程下会导致一个线程获得一个未初始化的实例。例如：线程T1执行了1和3，此时T2调用getInstance()后发现singleton不为空，因此返回singleton，但是此时的singleton还没有被初始化。使用volatile会禁止JVM指令重排，从而保证在多线程下也能正常执行。
这里还说一下volatile关键字的第二个作用，保证变量在多线程运行时的可见性：在JDK1.2之前，Java的内存模型实现总是从主存（即共享内存）读取变量，是不需要进行特别的注意的。而在当前的Java内存模型下，线程可以把变量保存本地内存（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。要解决这个问题，就需要把变量声明为volatile，这就指示JVM，这个变量是不稳定的，每次使用它都到主存中进行读取。

使用场景：

单例模式只允许创建一个对象，因此节省内存，加快对象访问速度，因此对象需要被公用的场合适合使用，如多个模块使用同一个数据源连接对象等等。如：
需要频繁实例化然后销毁的对象。创建对象时耗时过多或者耗资源过多，但又经常用到的对象。有状态的工具类对象。频繁访问数据库或文件的对象。
资源共享的情况下，避免由于资源操作时导致的性能或损耗等。如上述中的日志文件，应用配置。
控制资源的情况下，方便资源之间的互相通信。如线程池等。

4.工厂模式

这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
意图：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
所谓的工厂方法模式，就是定义一个工厂方法，通过传入的参数，返回某个实例，然后通过该实例来处理后续的业务逻辑。一般的，工厂方法的返回值类型是一个接口类型，而选择具体子类实例的逻辑则封装到了工厂方法中了。通过这种方式，来将外层调用逻辑与具体的子类的获取逻辑进行分离。

5.观察者模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知它的依赖对象。观察者模式属于行为型模式。
意图：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

6.装饰器模式

装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
意图：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。
主要解决：一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。

7.策略模式

策略模式就是一个接口下有多个实现类，而每种实现类会处理某一种情况。

8.实现低耦合就是对两类之间进行解耦，解耦的本质就是将类之间的直接关系转换成间接关系，不管是类向上转型，接口回调还是适配器模式都是在类之间加了一层，将原来的直接关系变成间接关系，使得两类对中间层是强耦合，两类之间变成弱耦合关系。

9.设计模式的7大原则

开放-封闭原则；
单一职责原则；
依赖倒转原则；
最小知识原则；
接口隔离原则；
合成/聚合复用原则；
里氏代换原则，任何基类可以出现的地方，子类一定可以出现。

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