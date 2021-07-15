---
layout: post
title: "后端开发面试题 -- 计算机基础篇"
description: 后端开发面试题 -- 计算机基础篇
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

2.常用的Linux命令
  
    vim 打开文件修改内容
    find 搜索文件
    mkdir 创建目录
    rm 删除目录或文件
    kill 杀掉进程
    cp 拷贝
    ping ip 查看与某台机子的连接情况
    service network restart 重启网络
    ls：用户查看目录下的文件，ls -a可以用来查看隐藏文件，ls -l可以用于查看文件的详细信息，包括权限、大小、所有者等信息。
    touch：用于创建文件。如果文件不存在，则创建一个新的文件，如果文件已存在，则会修改文件的时间戳。
    cat：cat是英文concatenate的缩写，用于查看文件内容。使用cat查看文件的话，不管文件的内容有多少，都会一次性显示，所以他不适合查看太大的文件。
    more：more和cat有点区别，more用于分屏显示文件内容。可以用空格键向下翻页，b键向上翻页
    less：和more类似，less用于分行显示
    tail：可能是平时用的最多的命令了，查看日志文件基本靠它。tail -fn 100 xx.log查看最后的100行内容
    chmod：修改权限命令。一般用+号添加权限，-号删除权限，x代表执行权限，r代表读取权限，w代表写入权限，常见写法比如chmod +x 文件名添加执行权限。
    chown：用于修改文件和目录的所有者和所属组。一般用法chown user 文件名用于修改文件所有者，chown user:user 文件名修改文件所有者和组，冒号前面是所有者，后面是组。往期面试题汇总：250期面试资料
    zip：压缩zip文件命令，比如zip test.zip 文件名可以把文件压缩成zip文件，如果压缩目录的话则需添加 -r 选项。
    unzip：与zip对应，解压zip文件命令。unzip xxx.zip直接解压，还可以通过 -d 选项指定解压目录。
    gzip：用于压缩带.gz后缀的文件，gzip命令不能打包目录。需要注意的是直接使用gzip 文件名这个命令会导致源文件会消失，如果要保留源文件，可以使用gzip -c 文件名 > xx.gz，解压缩直接使用gzip -d xx.gz
    tar：tar命令可以为linux的文件和目录创建档案。利用tar，可以为某一特定文件创建档案（备份文件），也可以在档案中改变文件，或者向档案中加入新的文件。tar常用几个选项，-x 解打包，-c 打包，-f 指定压缩包文件名，-v 显示打包文件过程，一般常用tar -cvf xx.tar 文件名来打包，解压则使用tar -xvf xx.tar。使用命令tar -zcvf xx.tar.gz 文件名来打包压缩，使用命令tar -zxvf xx.tar.gz来解压缩。

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

4.内存泄漏和内存溢出

(1)内存泄漏（memory leak）

内存泄漏是指程序中已动态分配的堆内存由于某种原因未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统奔溃等严重后果。
一次内存泄漏似乎不会有大的影响，但内存泄漏后堆积的结果就是内存溢出。
内存泄漏具有隐蔽性，积累性的特征，比其他内存非法访问错误更难检测。这是因为内存泄漏产生的原因是内存块未被释放，属于遗漏型缺陷而不是过错型缺陷。此外，内存泄漏不会直接产生可观察的错误，而是逐渐积累，降低系统的整体性性能。
如何有效的进行内存分配和释放，防止内存泄漏，是软件开发人员的关键问题，比如一个服务器应用软件要长时间服务多个客户端，若存在内存泄漏，则会逐渐堆积，导致一系列严重后果。

(2)内存泄漏原因

没有释放动态分配的存储空间而造成内存泄漏，是动态存储变量的主要问题。
常用内存管理函数如下：malloc，free，calloc，recalloc等，来完成动态存储变量存储空间的分配和释放。

常见的内存管理错误如下：

分配一个内存块并使用其中未经初始化的内容；
释放一个内存块，但继续引用其中的内容；
子函数分配的内存空间在主函数出现异常中断时，或主函数对子函数返回的信息使用结束时，没有对分配的内存进行释放；
程序实现过程中分配的临时内存在程序结束时，没有释放临时内存；
内存错误一般是不可在现的，开发人员不易在程序调式和测试阶段发现，即使花费了很多的时间和精力，也无法消除。

(3)内存泄漏产生方式的分类

常发性内存泄漏：发生内存泄漏的代码会被多次执行到，每次被执行时都会导致一块内存泄漏。
偶发性内存泄漏：发生内存泄漏的代码只有在某些特定环境或操作过程下才会发生。常发性和偶发性是相对的。对于特定的环境，偶发性的也许就变成了常发性的。所以测试环境和测试方法对检测内存泄漏至关重要。
一次性内存泄漏：发生内存泄漏的代码只会被执行一次，或者由于算法上的缺陷，导致总会有一块且仅有一块内存发生泄漏。
隐式内存泄漏：程序在运行过程中不停的分配内存，但是直到结束的时候才释放内存。严格的说这里并没有发生内存泄漏，因为最终程序释放了所有申请的内存。

但是对于一个服务器程序，需要运行几天，几周甚至几个月，不及时释放内存也可能导致最终耗尽系统的所有内存。所以，我们称这类内存泄漏为隐式内存泄漏。
从用户使用程序的角度来看，内存泄漏本身不会产生什么危害，作为一般的用户，根本感觉不到内存泄漏的存在。真正有危害的是内存泄漏的堆积，这会最终耗尽系统所有的内存。
从这个角度来说，一次性内存泄漏并没有什么危害，因为它不会堆积，而隐式内存泄漏危害性则非常大，因为较之于常发性和偶发性内存泄漏它更难被检测到。

(4)内存溢出

指程序在申请内存时，没有足够的内存供申请者使用，或者说，给了你一块存储int类型数据的存储空间，但是你却存储long类型的数据，就会导致内存不够用，报错OOM，即出现内存溢出的错误。

(5)内存溢出的原因

内存中加载的数据量过于庞大，如一次从数据库取出过多数据；
集合类中有对对象的引用，使用完后未清空，使得JVM不能回收；
代码中存在死循环或循环产生过多重复的对象实体；
使用的第三方软件中的BUG；
启动参数内存值设定的过小。

(6)内存溢出的解决方案

第一步，修改JVM启动参数，直接增加内存。(-Xms，-Xmx参数一定不要忘记加。)
第二步，检查错误日志，查看“OutOfMemory”错误前是否有其它异常或错误。
第三步，对代码进行走查和分析，找出可能发生内存溢出的位置。

(7)内存泄漏和内存溢出的联系

内存泄漏的堆积最终会导致内存溢出；
内存溢出就是你要的内存空间超过了系统实际分配给你的空间，此时系统相当于没法满足你的需求，就会报内存溢出的错误。
内存泄漏是指你向系统申请分配内存进行使用(new)，可是使用完了以后却不归还(delete)，结果你申请到的那块内存你自己也不能再访问（也许你把它的地址给弄丢了），而系统也不能再次将它分配给需要的程序。就相当于你租了个带钥匙的柜子，你存完东西之后把柜子锁上之后，把钥匙丢了或者没有将钥匙还回去，那么结果就是这个柜子将无法供给任何人使用，也无法被垃圾回收器回收，因为找不到他的任何信息。
内存溢出可以这样理解，一个盘子用尽各种方法只能装4个果子，你装了5个，结果掉倒地上不能吃了。这就是溢出。比方说栈，栈满时再做进栈必定产生空间溢出，叫上溢，栈空时再做退栈也产生空间溢出，称为下溢。就是分配的内存不足以放下数据项序列,称为内存溢出。说白了就是我承受不了那么多，那我就报错。

5.top命令CPU相关参数

    0.3%  us 用户空间占用CPU百分比
    1.0%  sy 内核空间占用CPU百分比
    0.0%  ni 用户进程空间内改变过优先级的进程占用CPU百分比
    98.7% id 空闲CPU百分比
    0.0%  wa 等待输入输出的CPU时间百分比
    0.0%  hi 硬中断
    0.0%  si 软中断

6.CPU利用率和CPU负载相关问题

(1)CPU使用率

就是程序对CPU时间片的占用情况，即CPU使用率 = CPU时间片被程序使用的时间 / 总时间。比如A进程占用10ms，然后B进程占用30ms，然后空闲60ms，再又是A进程占10ms，B进程占30ms，空闲60ms，如果在一段时间内都是如此，那么这段时间内的CPU占用率为40%。CPU利用率显示的是程序在运行期间实时占用的CPU百分比。
大多数操作系统的CPU占用率分为用户态CPU使用率和系统态CPU使用率。用户态CPU使用率是指执行应用程序代码的时间占总CPU时间的百分比。相比而言，系统态CPU使用率是指应用执行操作系统调用的时间占总CPU时间的百分比。系统态的CPU使用率高意味着共享资源有竞争或者I/O设备之间有大量的交互。

(2)CPU负载

CPU负载显示的是一段时间内正在使用和等待使用CPU的平均任务数。
简单理解，一个是CPU的实时使用情况，一个是CPU的当前以及未来一段时间的使用情况。举例来说：如果我有一个程序它需要一直使用CPU的运算功能，那么此时CPU的使用率可能达到100%，但是CPU的工作负载则是趋近于“1”，因为CPU仅负责一个工作嘛！如果同时执行这样的程序两个呢？CPU的使用率还是100%，但是工作负载则变成2了。所以也就是说，CPU的工作负载越大，代表CPU必须要在不同的工作之间进行频繁的工作切换。无论CPU的利用率是高是低，跟后面有多少任务在排队(CPU负载)没有必然关系。
如果单核CPU的话，负载达到1就代表CPU已经达到满负荷的状态了，超过1，后面的进行就需要排队等待处理了。如果是是多核多CPU，假设现在服务器是2个CPU，每个CPU有2个核，那么总负载不超过4都没什么问题。
可以通过uptime、w命令查看CPU平均负载，使用top命令还能看到CPU负载总体使用率以及各个进程占用CPU的比例。

    // 查看物理CPU个数
    cat /proc/cpuinfo| grep “physical id”| sort | uniq| wc -l
    // 查看每个物理CPU中core的个数(即核数)
    cat /proc/cpuinfo| grep “cpu cores” | uniq
    // 查看逻辑CPU的个数
    cat /proc/cpuinfo| grep “processor”| wc -l
    
(3)如果CPU负载很高，利用率却很低该怎么办

CPU负载很高，利用率却很低，说明处于等待状态的任务很多，负载越高，代表可能很多僵死的进程。通常这种情况是IO密集型的任务，大量任务在请求相同的IO，导致任务队列堆积。
生产环境造成CPU利用率低负载高的具体场景常见的有如下几种。

(a)场景一：磁盘读写请求过多就会导致大量I/O等待。
进程在cpu上面运行需要访问磁盘文件，这个时候cpu会向内核发起调用文件的请求，让内核去磁盘取文件，这个时候cpu会切换到其他进程或者空闲，这个任务就会转换为不可中断睡眠状态。当这种读写请求过多就会导致不可中断睡眠状态的进程过多，从而导致负载高，cpu低的情况。

(b)场景二：MySQL中存在没有索引的语句或存在死锁等情况。
我们都知道MySQL的数据是存储在硬盘中，如果需要进行sql查询，需要先把数据从磁盘加载到内存中。当在数据特别大的时候，如果执行的sql语句没有索引，就会造成扫描表的行数过大导致I/O阻塞，或者是语句中存在死锁，也会造成I/O阻塞，从而导致不可中断睡眠进程过多，导致负载过大。

同样，可以先通过top命令观察，假设发现现在确实是高负载低使用率。
然后，再通过命令ps -aux查看是否存在状态为D的进程，这个状态指的就是不可中断的睡眠状态的进程。处于这个状态的进程无法终止，也无法自行退出，只能通过恢复其依赖的资源或者重启系统来解决。

Linux上进程的五种状态

    R (TASK_RUNNING)：可执行状态，只有在该状态的进程才可能在CPU上运行。而同一时刻可能有多个进程处于可执行状态。
    S (TASK_INTERRUPTIBLE)：可中断的睡眠状态，处于这个状态的进程因为等待某某事件的发生（比如等待socket连接、等待信号量），而被挂起。
    D (TASK_UNINTERRUPTIBLE)：不可中断的睡眠状态，进程处于睡眠状态，但是此刻进程是不可中断的。TASK_UNINTERRUPTIBLE状态存在的意义就在于，内核的某些处理流程是不能被打断的。
    T (TASK_STOPPED or TASK_TRACED)：暂停状态或跟踪状态。
    Z (TASK_DEAD - EXIT_ZOMBIE)：退出状态，进程成为僵尸进程。进程已终止，但进程描述还在，直到父进程调用wait4()系统调用后释放。
    
(4)如果CPU负载很低，利用率却很高该怎么办

这表示CPU的任务并不多，但是任务执行的时间很长，大概率就是写的代码本身有问题，通常是计算密集型任务，生成了大量耗时短的计算任务。
怎么排查？直接top命令找到CPU使用率最高的进程，定位到去看看就行了。如果代码没有问题，那么过段时间CPU使用率就会下降的。

(5)CPU利用率达到100%怎么排查问题

    通过top找到CPU占用率高的进程
    通过top -Hp pid命令查看CPU占比靠前的线程ID
    再把线程ID转化为16进制，printf “0x%x\n” 74317，得到0x1224d
    通过命令jstack 72700 | grep ‘0x1224d’ -C5 --color找到有问题的代码

N.参考

(1)[记一次线上商城系统高并发的优化](https://www.cnblogs.com/wangjiming/p/13225544.html)

# 面经

1.向面试官提问

技术面：做哪块业务，团队规模，人员分工，该岗位职责，团队技术栈。
业务leader面：产品发展方向，盈利模式，团队规模。
HR面：除了薪水外其他福利待遇、公积金、补充医疗、年终奖、职级评定、调薪、股票期权、预期薪水。

2.之前项目中使用过哪些技术，评价一下自己掌握的程度
  
框架Spring, SpringMVC, SpringBoot, Mybatis, Hibernate, Dubbo。
数据库MySQL,Redis。
中间件ZooKeeper,Kafka。
搜索引擎Elasticsearch
  
掌握程度的话，基本上在搭建及使用上没有问题，具备独立开发的能力，底层原理看过一些技术博客。

3.知识体系

    Java
        --Java基础
        --Java IO
        --Java异常
        --Java反射
        --Java注解
        --Java Web
        --Java新特性
        --Java集合
    
    Java JVM
        --类加载器
        --内存管理
        --垃圾回收
        --执行过程
        --JVM调优
        
    并发与多线程
        --并发与多线程基础
        --线程池
        --Java锁机制
        --JUC
        
    应用框架
        --Spring
        --Spring MVC
        --Spring Boot
        --Spring Cloud
        --Dubbo
        --Mybatis
        --Hibernate
        --Netty
        --Log4j2
        
    数据库
        --数据库基础
        --数据库索引
        --数据库锁
        --数据库事务
        --数据库存储引擎
        --数据库日志
        --数据库性能优化
        --数据库架构
        
    消息队列
        --消息队列基础
        --Kafka
        
    Redis
        --Redis基础
        --Redis数据结构与操作
        --Redis持久化
        --Redis内存管理
        --Redis分布式锁
        --Redis架构
        --Redis性能优化
        
    注册中心
        --ZooKeeper
        --Nacos

    Elasticsearch
    
    Nginx

    算法与数据结构
        --数据结构
        --算法--基础
        --算法--排序
        --算法--链表
        
    设计模式
    
    网络
        
    架构与系统设计
        --系统架构
        --系统设计
        
    云技术
        --K8S
        --Docker
        
    基础
        --Linux
        --问题排查与调优
        --面经
        
# 综合参考

1.[Java最常见的200+面试题及自己梳理的答案--面试必备（一）](https://www.cnblogs.com/cocoxu1992/p/10460251.html)

2.[久伴_不离](https://www.jianshu.com/u/837b81b0eaa9)