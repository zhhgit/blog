---
layout: post
title: "后端开发面试题 -- 计算机基础、架构篇"
description: 后端开发面试题 -- 计算机基础、架构篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# Linux

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

# 综合参考

1.[Java最常见的200+面试题及自己梳理的答案--面试必备（一）](https://www.cnblogs.com/cocoxu1992/p/10460251.html)

2.[久伴_不离](https://www.jianshu.com/u/837b81b0eaa9)