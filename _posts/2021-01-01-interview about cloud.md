---
layout: post
title: "面试题 -- 云技术篇"
description: 面试题 -- 云技术篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# K8S

1.k8s是什么？请说出你的了解？

Kubenetes是一个针对容器应用，进行自动部署，弹性伸缩和管理的开源系统。主要功能是生产环境中的容器编排。
K8S是Google公司推出的，它来源于由Google公司内部使用了15年的Borg系统，集结了Borg的精华。

2.K8s架构的组成是什么？

和大多数分布式系统一样，K8S集群至少需要一个主节点（Master）和多个计算节点（Node）。
主节点主要用于暴露API，调度部署和节点的管理；
计算节点运行一个容器运行环境，一般是docker环境（类似docker环境的还有rkt），同时运行一个K8s的代理（kubelet）用于和master通信。计算节点也会运行一些额外的组件，像记录日志，节点监控，服务发现等等。计算节点是k8s集群中真正工作的节点。

(1)Master节点（默认不参加实际工作）

Kubectl：客户端命令行工具，作为整个K8s集群的操作入口；
Api Server：在K8s架构中承担的是“桥梁”的角色，作为资源操作的唯一入口，它提供了认证、授权、访问控制、API注册和发现等机制。客户端与k8s群集及K8s内部组件的通信，都要通过Api Server这个组件；
Controller-manager：负责维护群集的状态，比如故障检测、自动扩展、滚动更新等；
Scheduler：负责资源的调度，按照预定的调度策略将pod调度到相应的node节点上；
Etcd：担任数据中心的角色，保存了整个群集的状态；

(2)Node节点：

Kubelet：负责维护容器的生命周期，同时也负责Volume和网络的管理，一般运行在所有的节点，是Node节点的代理，当Scheduler确定某个node上运行pod之后，会将pod的具体信息（image，volume）等发送给该节点的kubelet，kubelet根据这些信息创建和运行容器，并向master返回运行状态。（自动修复功能：如果某个节点中的容器宕机，它会尝试重启该容器，若重启无效，则会将该pod杀死，然后重新创建一个容器）；
Kube-proxy：Service在逻辑上代表了后端的多个pod。负责为Service提供cluster内部的服务发现和负载均衡（外界通过Service访问pod提供的服务时，Service接收到的请求后就是通过kube-proxy来转发到pod上的）；
container-runtime：是负责管理运行容器的软件，比如docker。
Pod：是k8s集群里面最小的单位。每个pod里边可以运行一个或多个container（容器），如果一个pod中有两个container，那么container的USR（用户）、MNT（挂载点）、PID（进程号）是相互隔离的，UTS（主机名和域名）、IPC（消息队列）、NET（网络栈）是相互共享的。我比较喜欢把pod来当做豌豆夹，而豌豆就是pod中的container；

3.容器和主机部署应用的区别是什么？

容器的中心思想就是秒级启动；一次封装、到处运行；这是主机部署应用无法达到的效果，但同时也更应该注重容器的数据持久化问题。
另外，容器部署可以将各个服务进行隔离，互不影响，这也是容器的另一个核心概念。

4.请你说一下kubenetes针对pod资源对象的健康监测机制？

K8s中对于pod资源对象的健康状态检测，提供了三类probe（探针）来执行对pod的健康监测：

    (1)livenessProbe探针：可以根据用户自定义规则来判定pod是否健康，如果livenessProbe探针探测到容器不健康，则kubelet会根据其重启策略来决定是否重启，如果一个容器不包含livenessProbe探针，则kubelet会认为容器的livenessProbe探针的返回值永远成功。
    (2)ReadinessProbe探针：同样是可以根据用户自定义规则来判断pod是否健康，如果探测失败，控制器会将此pod从对应service的endpoint列表中移除，从此不再将任何请求调度到此Pod上，直到下次探测成功。
    (3)startupProbe探针：启动检查机制，应用一些启动缓慢的业务，避免业务长时间启动而被上面两类探针kill掉，这个问题也可以换另一种方式解决，就是定义上面两类探针机制时，初始化时间定义的长一些即可。

每种探测方法能支持以下几个相同的检查参数，用于设置控制检查时间：

    initialDelaySeconds：初始第一次探测间隔，用于应用启动的时间，防止应用还没启动而健康检查失败
    periodSeconds：检查间隔，多久执行probe检查，默认为10s；
    timeoutSeconds：检查超时时长，探测应用timeout后为失败；
    successThreshold：成功探测阈值，表示探测多少次为健康正常，默认探测1次。

上面两种探针都支持以下三种探测方法：

(1)Exec：通过执行命令的方式来检查服务是否正常，比如使用cat命令查看pod中的某个重要配置文件是否存在，若存在，则表示pod健康。反之异常。Exec探测方式的yaml文件语法如下：
在上面的配置文件中，探测机制为在容器运行5秒后，每隔五秒探测一次，如果cat命令返回的值为“0”，则表示健康，如果为非0，则表示异常。

    spec:
      containers:
      - name: liveness
        image: k8s.gcr.io/busybox
        args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
        livenessProbe:         #选择livenessProbe的探测机制
          exec:                      #执行以下命令
            command:
            - cat
            - /tmp/healthy
          initialDelaySeconds: 5          #在容器运行五秒后开始探测
          periodSeconds: 5               #每次探测的时间间隔为5秒

(2)Httpget：通过发送http/htps请求检查服务是否正常，返回的状态码为200-399则表示容器健康（注http get类似于命令curl -I）。Httpget探测方式的yaml文件语法如下：
上述配置文件中，探测方式为项容器发送HTTP GET请求，请求的是8080端口下的healthz文件，返回任何大于或等于200且小于400的状态码表示成功。任何其他代码表示异常。

    spec:
      containers:
      - name: liveness
        image: k8s.gcr.io/liveness
        livenessProbe:              #采用livenessProbe机制探测
          httpGet:                  #采用httpget的方式
            scheme:HTTP         #指定协议，也支持https
            path: /healthz          #检测是否可以访问到网页根目录下的healthz网页文件
            port: 8080              #监听端口是8080
          initialDelaySeconds: 3     #容器运行3秒后开始探测
          periodSeconds: 3                #探测频率为3秒
          
(3)tcpSocket： 通过容器的IP和Port执行TCP检查，如果能够建立TCP连接，则表明容器健康，这种方式与HTTPget的探测机制有些类似，tcpsocket健康检查适用于TCP业务。tcpSocket探测方式的yaml文件语法如下：
在yaml配置文件中，两类探针都使用了，在容器启动5秒后，kubelet将发送第一个readinessProbe探针，这将连接容器的8080端口，如果探测成功，则该pod为健康，十秒后，kubelet将进行第二次连接。除了readinessProbe探针外，在容器启动15秒后，kubelet将发送第一个livenessProbe探针，仍然尝试连接容器的8080端口，如果连接失败，则重启容器。

    spec:
      containers:
      - name: goproxy
        image: k8s.gcr.io/goproxy:0.1
        ports:
    - containerPort: 8080 #这里两种探测机制都用上了，都是为了和容器的8080端口建立TCP连接
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20

探针探测的结果无外乎以下三者之一：

    Success：Container通过了检查；
    Failure：Container没有通过检查；
    Unknown：没有执行检查，因此不采取任何措施（通常是我们没有定义探针检测，默认为成功）。

5.如何控制滚动更新过程？

可以通过下面的命令查看到更新时可以控制的参数：

    [root@master yaml]# kubectl explain deploy.spec.strategy.rollingUpdate
    
maxSurge：此参数控制滚动更新过程，副本总数超过预期pod数量的上限。可以是百分比，也可以是具体的值。默认为1。（上述参数的作用就是在更新过程中，值若为3，那么不管三七二一，先运行三个pod，用于替换旧的pod，以此类推）
maxUnavailable：此参数控制滚动更新过程中，不可用的Pod的数量。（这个值和上面的值没有任何关系，举个例子：我有十个pod，但是在更新的过程中，我允许这十个pod中最多有三个不可用，那么就将这个参数的值设置为3，在更新的过程中，只要不可用的pod数量小于或等于3，那么更新过程就不会停止）。

6.K8s中镜像的下载策略是什么？

可通过命令“kubectl explain pod.spec.containers”来查看imagePullPolicy这行的解释。
K8s的镜像下载策略有三种：Always、Never、IFNotPresent；

    Always：镜像标签为latest时，总是从指定的仓库中获取镜像；
    Never：禁止从仓库中下载镜像，也就是说只能使用本地镜像；
    IfNotPresent：仅当本地没有对应镜像时，才从目标仓库中下载。
    
默认的镜像下载策略是：当镜像标签是latest时，默认策略是Always；
当镜像标签是自定义时（也就是标签不是latest），那么默认策略是IfNotPresent。

7.image的状态有哪些？

    Running：Pod所需的容器已经被成功调度到某个节点，且已经成功运行，
    Pending：APIserver创建了pod资源对象，并且已经存入etcd中，但它尚未被调度完成或者仍然处于仓库中下载镜像的过程
    Unknown：APIserver无法正常获取到pod对象的状态，通常是其无法与所在工作节点的kubelet通信所致。

8.pod的重启策略是什么？

可以通过命令“kubectl explain pod.spec”查看pod的重启策略。（restartPolicy字段）

    Always：但凡pod对象终止就重启，此为默认策略。
    OnFailure：仅在pod对象出现错误时才重启
    
9.Service这种资源对象的作用是什么？

用来给相同的多个pod对象提供一个固定的统一访问接口，常用于服务发现和服务访问。

10.版本回滚相关的命令？

    [root@master httpd-web]# kubectl apply -f httpd2-deploy1.yaml  --record #运行yaml文件，并记录版本信息；
    [root@master httpd-web]# kubectl rollout history deployment httpd-devploy1 #查看该deployment的历史版本
    [root@master httpd-web]# kubectl rollout undo deployment httpd-devploy1 --to-revision=1 #执行回滚操作，指定回滚到版本1

    #在yaml文件的spec字段中，可以写以下选项（用于限制最多记录多少个历史版本）：
    spec:
      revisionHistoryLimit: 5
    
    #这个字段通过 kubectl explain deploy.spec  命令找到revisionHistoryLimit   <integer>行获得
    
11.标签与标签选择器的作用是什么？

是当相同类型的资源对象越来越多的时候，为了更好的管理，可以按照标签将其分为一个组，为的是提升资源对象的管理效率。
标签选择器：就是标签的查询过滤条件。目前API支持两种标签选择器：
基于等值关系的，如：“=”、“”“==”、“！=”（注：“==”也是等于的意思，yaml文件中的matchLabels字段）；
基于集合的，如：in、notin、exists（yaml文件中的matchExpressions字段）；注：in:在这个集合中；notin：不在这个集合中；exists：要么全在（exists）这个集合中，要么都不在（notexists）；

两种标签选择器类型（基于等值、基于集合的书写方法）：
使用空值的标签选择器，意味着每个资源对象都被选中（如：标签选择器的键是“A”，两个资源对象同时拥有A这个键，但是值不一样，这种情况下，如果使用空值的标签选择器，那么将同时选中这两个资源对象）。

    selector:
      matchLabels:           #基于等值
        app: nginx
      matchExpressions:         #基于集合
        - {key: name,operator: In,values: [zhangsan,lisi]}     #key、operator、values这三个字段是固定的
        - {key: age,operator: Exists,values:}   #如果指定为exists，那么values的值一定要为空
        
12.常用的标签分类有哪些？

标签分类是可以自定义的，但是为了能使他人可以达到一目了然的效果，一般会使用以下一些分类：
版本类标签（release）：stable（稳定版）、canary（金丝雀版本，可以将其称之为测试版中的测试版）、beta（测试版）；
环境类标签（environment）：dev（开发）、qa（测试）、production（生产）、op（运维）；
应用类（app）：ui、as、pc、sc；
架构类（tier）：frontend（前端）、backend（后端）、cache（缓存）；
分区标签（partition）：customerA（客户A）、customerB（客户B）；
品控级别（Track）：daily（每天）、weekly（每周）。

13.有几种查看标签的方式？

答：常用的有以下三种查看方式：

    [root@master ~]# kubectl get pod --show-labels    #查看pod，并且显示标签内容
    [root@master ~]# kubectl get pod -L env,tier      #显示资源对象标签的值
    [root@master ~]# kubectl get pod -l env,tier      #只显示符合键值资源对象的pod，而“-L”是显示所有的pod
    
14.添加、修改、删除标签的命令？

    #对pod标签的操作
    [root@master ~]# kubectl label pod label-pod abc=123     #给名为label-pod的pod添加标签
    [root@master ~]# kubectl label pod label-pod abc=456 --overwrite       #修改名为label-pod的标签
    [root@master ~]# kubectl label pod label-pod abc-             #删除名为label-pod的标签
    [root@master ~]# kubectl get pod --show-labels
    
    #对node节点的标签操作
    [root@master ~]# kubectl label nodes node01 disk=ssd      #给节点node01添加disk标签
    [root@master ~]# kubectl label nodes node01 disk=sss –overwrite    #修改节点node01的标签
    [root@master ~]# kubectl label nodes node01 disk-         #删除节点node01的disk标签

15.DaemonSet资源对象的特性？

DaemonSet这种资源对象会在每个k8s集群中的节点上运行，并且每个节点只能运行一个pod，这是它和deployment资源对象的最大也是唯一的区别。
所以，在其yaml文件中，不支持定义replicas，除此之外，与Deployment、RS等资源对象的写法相同。
它的一般使用场景如下：在去做每个节点的日志收集工作；监控每个节点的的运行状态；

16.说说你对Job这种资源对象的了解？

Job与其他服务类容器不同，Job是一种工作类容器（一般用于做一次性任务）。

    #提高Job执行效率的方法：
    spec:
      parallelism: 2           #一次运行2个
      completions: 8           #最多运行8个
      template:
    metadata:
    
17.描述一下pod的生命周期有哪些状态？

    Pending：表示pod已经被同意创建，正在等待kube-scheduler选择合适的节点创建，一般是在准备镜像；
    Running：表示pod中所有的容器已经被创建，并且至少有一个容器正在运行或者是正在启动或者是正在重启；
    Succeeded：表示所有容器已经成功终止，并且不会再启动；
    Failed：表示pod中所有容器都是非0（不正常）状态退出；
    Unknown：表示无法读取Pod状态，通常是kube-controller-manager无法与Pod通信。

18.创建一个pod的流程是什么？

客户端提交Pod的配置信息（可以是yaml文件定义好的信息）到kube-apiserver；
Apiserver收到指令后，通知给controller-manager创建一个资源对象；
Controller-manager通过api-server将pod的配置信息存储到ETCD数据中心中；
Kube-scheduler检测到pod信息会开始调度预选，会先过滤掉不符合Pod资源配置要求的节点，然后开始调度调优，主要是挑选出更适合运行pod的节点，然后将pod的资源配置单发送到node节点上的kubelet组件上。
Kubelet根据scheduler发来的资源配置单运行pod，运行成功后，将pod的运行信息返回给scheduler，scheduler将返回的pod运行状况的信息存储到etcd数据中心。

19.删除一个Pod会发生什么事情？

Kube-apiserver会接受到用户的删除指令，默认有30秒时间等待优雅退出，超过30秒会被标记为死亡状态，此时Pod的状态Terminating，kubelet看到pod标记为Terminating就开始了关闭Pod的工作；

关闭流程如下：
pod从service的endpoint列表中被移除；
如果该pod定义了一个停止前的钩子，其会在pod内部被调用，停止钩子一般定义了如何优雅的结束进程；
进程被发送TERM信号（kill -14）
当超过优雅退出的时间后，Pod中的所有进程都会被发送SIGKILL信号（kill -9）。

20.K8s的Service是什么？

Pod每次重启或者重新部署，其IP地址都会产生变化，这使得pod间通信和pod与外部通信变得困难，这时候，就需要Service为pod提供一个固定的入口。
Service的Endpoint列表通常绑定了一组相同配置的pod，通过负载均衡的方式把外界请求分配到多个pod上

21.k8s是怎么进行服务注册的？

Pod启动后会加载当前环境所有Service信息，以便不同Pod根据Service名进行通信。

22.k8s集群外流量怎么访问Pod？

可以通过Service的NodePort方式访问，会在所有节点监听同一个端口，比如：30000，访问节点的流量会被重定向到对应的Service上面。

23.k8s数据持久化的方式有哪些？

(1)EmptyDir（空目录）：
没有指定要挂载宿主机上的某个目录，直接由Pod内保部映射到宿主机上。类似于docker中的manager volume。

主要使用场景：只需要临时将数据保存在磁盘上，比如在合并/排序算法中；作为两个容器的共享存储，使得第一个内容管理的容器可以将生成的数据存入其中，同时由同一个webserver容器对外提供这些页面。
emptyDir的特性：同个pod里面的不同容器，共享同一个持久化目录，当pod节点删除时，volume的数据也会被删除。如果仅仅是容器被销毁，pod还在，则不会影响volume中的数据。
总结来说：emptyDir的数据持久化的生命周期和使用的pod一致。一般是作为临时存储使用。

(2)Hostpath：

将宿主机上已存在的目录或文件挂载到容器内部。类似于docker中的bind mount挂载方式。
这种数据持久化方式，运用场景不多，因为它增加了pod与节点之间的耦合。
一般对于k8s集群本身的数据持久化和docker本身的数据持久化会使用这种方式，可以自行参考apiService的yaml文件，位于：/etc/kubernetes/main…目录下。

(3)PersistentVolume（简称PV）：

基于NFS服务的PV，也可以基于GFS的PV。它的作用是统一数据持久化目录，方便管理。
在一个PV的yaml文件中，可以对其配置PV的大小，指定PV的访问模式：
ReadWriteOnce：只能以读写的方式挂载到单个节点；
ReadOnlyMany：能以只读的方式挂载到多个节点；
ReadWriteMany：能以读写的方式挂载到多个节点。以及指定pv的回收策略，这里的回收策略指的是在PV被删除后，在这个PV下所存储的源文件是否删除）。
    recycle：清除PV的数据，然后自动回收；
    Retain：需要手动回收；
    delete：删除云存储资源，云存储专用；

24.已经用k8s来部署运维各个微服务的组件，是否可以不用整套微服务框架了？

可以，但要看情况。
以主流的SpringCloud为例，其提供的能力与Kubernetes既有重合也有互补，我们举几个例子逐一分析微服务各个核心组件。

微服务网关：网关几乎不涉及业务部分，各种实现也非常多，选哪个看业务契合度了。但Spring Cloud Gateway不能作为Kubernetes Ingress Controller，如果用不兼容Ingress的框架作为网关层，可能会损失一些Kubernetes Ingress带来的好处。
服务发现：Kubernetes自带的基于K8S Service和DNS的服务发现机制已经足够强大，底层其实是把元数据存在CAP取了CP的etcd中，更适合Kubernetes集群动态和弹性的特征。因此，用原生的服务发现能够在Pod退出前先下线流量。这与SpringCloud不矛盾，因为SpringCloud也提供了kubernetes集成的jar包，用原生的服务发现作为DiscoveryClient。Consul勉强可以和Kubernetes配合，而Eureka与Kubernetes就八字不合了，Eureka的弱一致性完全不适合每次部署Pod IP变化的Kubernetes环境。
负载均衡：负载均衡是基于服务发现的，如果用了Kubernetes Service/DNS作为服务发现的方案，丢掉微服务框架中的负载均衡组件吧，这个功能是完全重合的。
熔断、降级、限流：Kubernetes本身没有熔断器，因此微服务框架中的熔断组件是有必要的，即使引入了ServiceMesh做熔断，也不见得业务上熔断器的就可以省掉了，二者粒度不同、维度也不同，是互补的。Hystrix功能少一些，新的一些熔断组件比如阿里的Sentinel，也支持限流，这也是对Kubernetes原生能力中不足之处的补充，与ServiceMesh功能重合。
配置中心：配置中心试过Spring Cloud Config，Nacos，Kubernetes ConfigMap/Secret，从踩过的坑来看，丢掉各种GUI的配置中心吧。个人更倾向于最简单可靠的方案：Git维护配置的版本，CI/CD部署到Kubernetes集群的ConfigMap/Secret。

小结：
这几个例子可以发现，传统的微服务框架诞生于容器技术生态还没有繁荣的时期，因此像SpringCloud这样的微服务套件，不得不提供完整的微服务治理能力，从配置到服务发现，再到API网关等等。
而在容器生态步入主流之后，与业务功能关系不大的部分，Kubernetes以及其众多生态中的组件，做的更多更好。因此，像服务发现/负载均衡/配置中心/API网关 这些更偏向DevOps的关注点，是可以用Kubernetes取而代之的，这样业务开发者可以聚焦业务逻辑，把精力放在熔断降级时业务如何处理这类问题上。
最后，回到问题本身，是否可以不用整套微服务框架？新开发的微服务，我觉得是可以的，各类技术取长补短、有机组合，相比套用范本，会带来更长期的好处和和更高的投入产出比。
不过，如果是已经上线微服务系统的改造，可能会面临一些现实问题，比如：团队是否熟悉容器生态的技术和原理？去掉旧的组件会带来多大的益处和风险？人力是否足够？带来成本有多大？

# Docker

1.Docker操作命令

镜像相关命令：

    // 拉取镜像
    docker pull hub.c.163.com/library/hello-world:latest
    docker pull hub.c.163.com/library/nginx
    // 查看镜像
    docker images
    // 删除镜像
    docker rmi xxxx(镜像ID)
    // 搜索镜像
    docker search httpd
    // 为镜像添加一个新的标签。
    docker tag xxxx(镜像ID) test/ubuntu:v2
    // 查看指定镜像的创建历史
    docker history hub.c.163.com/library/nginx
    // 将指定镜像保存成tar归档文件
    docker save -o my_ubuntu_v3.tar test/ubuntu:v3
    // 从归档文件中创建镜像
    docker import my_ubuntu_v3.tar test/ubuntu:v4

容器相关命令：

    // 创建但是不启动容器，使用docker镜像nginx:latest创建一个容器,并将容器命名为test
    docker create --name test nginx:latest
    // 运行镜像
    docker run hub.c.163.com/library/hello-world
    // 后台运行Nginx
    docker run -d hub.c.163.com/library/nginx
    // 指定Nginx本地端口为8080，容器中端口80，-P是容器内部端口随机映射到主机的高端口。-p是容器内部端口绑定到指定的主机端口。
    docker run -d -p 8080:80 hub.c.163.com/library/nginx
    // 进入容器，其中xxxx代表run之后看到的容器id
    docker exec -it xxxx bash
    // 查看容器运行情况
    docker ps -a
    // 停止容器
    docker stop xxxx
    // 查看容器内的标准输出
    docker logs xxxx
    // 删除容器
    docker rm xxxx
    // 启动一个已停止的容器
    docker start xxxx
    // 重启容器
    docker restart xxxx
    // 导出容器快照到本地文件
    docker export xxxx > ubuntu.tar
    // 将快照文件ubuntu.tar导入到镜像test/ubuntu:v1:
    cat ubuntu.tar | docker import - test/ubuntu:v1
    // 清理掉所有处于终止状态的容器。
    docker container prune
    //查看容器端口的映射情况。
    docker port xxxx
    // 查看容器内部运行的进程
    docker top xxxx
    // 来查看容器的底层信息
    docker inspect xxxx
    // 拷贝文件进容器
    docker cp /users/zhanghao/localdir xxxx:/users/containerUser/
    // 杀掉一个运行中的容器
    docker kill xxxx
    // 暂停容器中所有的进程
    docker pause xxxx
    // 恢复容器中所有的进程
    docker unpause xxxx
    // 从服务器获取实时事件
    docker events --since="2016-07-01"

容器状态有7种：

    created（已创建）
    restarting（重启中）
    running 或 Up（运行中）
    removing（迁移中）
    paused（暂停）
    exited（停止）
    dead（死亡）

网络相关

    // 创建网络 
    docker network create -d bridge test-net
    // 查看网络列表
    docker network ls
    // 运行一个容器并连接到新建的test-net网络:
    docker run -itd --name test1 --network test-net ubuntu /bin/bash

2.制作镜像

方法1：编写Dockerfile

    // 编写Dockerfile
    from hub.c.163.com/library/tomcat:latest
    MAINTAINER zhanghao zhh900601@sina.com
    COPY horizon.war  /usr/local/tomcat/webapps
    // 编译镜像
    docker build . -t zh-horizon
    // 运行镜像，然后就可以在http://localhost:8080/horizon/，访问到应用了。
    docker run -d -p 8080:8080 zh-horizon
    //推送本地镜像到阿里云镜像仓库
    docker login --username=xxx registry.cn-shanghai.aliyuncs.com
    docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/zhhaliyun/horizonproj:[镜像版本号]
    docker push registry.cn-shanghai.aliyuncs.com/zhhaliyun/horizonproj:[镜像版本号]

方法2：从已创建容器更新镜像

    docker commit -m="has update" -a="zhanghao" xxxx(容器ID) test/ubuntu:v2

3.Dockerfile

    FROM 定制的镜像都是基于FROM的镜像
    RUN 用于执行后面跟着的命令行命令，两种格式：RUN <命令行命令> 等同于在终端操作的shell命令。RUN ["可执行文件", "参数1", "参数2"]
    COPY 复制指令，从上下文目录中复制文件或者目录到容器里指定路径。
    CMD 类似于RUN 指令，用于运行程序，但二者运行的时间点不同:CMD在docker run时运行。RUN是在docker build。为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD指令指定的程序可被docker run命令行参数中指定要运行的程序所覆盖。如果Dockerfile中如果存在多个CMD指令，仅最后一个生效。
    ENTRYPOINT 类似于CMD指令，但其不会被docker run的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给ENTRYPOINT指令指定的程序。如果 Dockerfile 中如果存在多个ENTRYPOINT指令。一般是变参用CMD，定参用ENTRYPOINT。
    ENV 设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。
    ARG 构建参数，与ENV作用一至。不过作用域不一样。ARG设置的环境变量仅对Dockerfile内有效，也就是说只有docker build的过程中有效，构建好的镜像内不存在此环境变量。
    EXPOSE 仅仅只是声明端口。帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。在运行时使用随机端口映射时，也就是docker run -P 时，会自动随机映射EXPOSE的端口。
    WORKDIR 指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。（WORKDIR 指定的工作目录，必须是提前创建好的）。docker build构建镜像过程中的，每一个RUN命令都是新建的一层。只有通过WORKDIR创建的目录才会一直存在。
    USER 用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。
    ONBUILD 用于延迟构建命令的执行。简单的说，就是Dockerfile里用ONBUILD指定的命令，在本次构建镜像的过程中不会执行（假设镜像为test-build）。当有新的Dockerfile使用了之前构建的镜像FROM test-build，这是执行新镜像的Dockerfile构建时候，会执行test-build的Dockerfile里的ONBUILD指定的命令。

4.Docker Swarm

    // 创建swarm集群管理节点（manager）
    docker-machine create -d virtualbox swarm-manager
    // 创建2个worker节点
    docker-machine create -d virtualbox swarm-worker1
    docker-machine create -d virtualbox swarm-worker2
    // 查看节点
    docker-machine ls
    // 登录并初始化swarm集群
    docker-machine ssh swarm-manager
    （swarm-manager中）docker swarm init --advertise-addr 192.168.99.107
    // 分别进入两个机器里，指定添加至上一步中创建的集群
    docker-machine --native-ssh ssh swarm-worker1 "docker swarm join --token SWMTKN-1-3p8yil4ufeun3cctk0m1ip14caei2tykmv79sdp18u3yphz66r-5v2lfqmy1925s0eo5sf6jfvw6 192.168.99.100:2377"
    docker-machine --native-ssh ssh swarm-worker2 "docker swarm join --token SWMTKN-1-3p8yil4ufeun3cctk0m1ip14caei2tykmv79sdp18u3yphz66r-5v2lfqmy1925s0eo5sf6jfvw6 192.168.99.100:2377"
    // 查看当前集群的信息
    （swarm-manager中）docker info
    // 查看节点信息
    （swarm-manager中）docker node ls
    // 拉取镜像
    （swarm-manager中）docker pull hub.c.163.com/library/nginx:latest
    // 部署服务到集群中
    （swarm-manager中）docker service create --replicas 3 -p 8088:80 --name nginx hub.c.163.com/library/nginx:latest
    // 查看服务
    （swarm-manager中）docker service ls
    （swarm-manager中）docker service ps nginx
    （swarm-manager中）docker service inspect --pretty nginx
    // 调整服务节点个数
    （swarm-manager中）docker service scale nginx=2
    // 删除服务
    （swarm-manager中）docker service rm nginx
    // 停止、重新激活节点接收任务
    （swarm-manager中）docker node update --availability drain swarm-worker1
    （swarm-manager中）docker node update --availability active swarm-worker1

N.参考

(1)[网易云镜像中心](https://c.163yun.com/hub#/m/home/)

(2)[阿里云开源镜像站](https://opsx.alibaba.com/)

(3)[菜鸟docker教程](https://www.runoob.com/docker/docker-tutorial.html)

(4)[阿里云容器镜像服务](https://help.aliyun.com/document_detail/60743.html)

(5)[docker命令大全](https://www.runoob.com/docker/docker-command-manual.html)

(6)[Swarm集群管理](https://www.runoob.com/docker/docker-swarm.html)

(7)[Docker Swarm从入门到放弃](https://www.cnblogs.com/sword-successful/p/12267163.html)

(8)[【253期】面试官：熟悉Docker操作吗？说几个常用的Docker命令吧](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247489173&idx=1&sn=2882d3378130c3f3c95d835778fa4214&chksm=e80da0e3df7a29f5ffb21ab68cd578d320309355ac9d6af3de8878f6ae8a8869d5a4aa75a53f&scene=21#wechat_redirect)