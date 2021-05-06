---
layout: post
title: "后端开发面试题 -- 框架篇"
description: 后端开发面试题 -- 框架篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# Spring

1.使用Spring的好处和原因

(1)低侵入式设计，代码污染极低。
(2)独立于各种应用服务器，基于Spring框架的应用，可以真正实现Write Once,Run Anywhere的承诺。
(3)Spring的DI机制降低了业务对象替换的复杂性，提高了组件之间的解耦。IOC（控制反转）创建对象不是通过new方式来实现，而是交给Spring配置来创建对象。
(4)Spring的AOP（面向切面编程）支持允许将一些通用任务如安全、事务、日志等进行集中式管理，从而提供了更好的复用。
(5)Spring的ORM和DAO提供了与第三方持久层框架的良好整合，并简化了底层的数据库访问。
(6)Spring并不强制应用完全依赖于Spring，开发者可自由选用Spring框架的部分或全部。Spring是开源的轻量级一站式框架，内部支持对多种优秀开源框架的集成。

2.Spring Bean的生命周期

(1)实例化一个Bean－－也就是我们常说的new；
(2)按照Spring上下文对实例化的Bean进行配置－－也就是IOC注入，例如属性；
(3)如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String)方法，此处传递的就是Spring配置文件中Bean的id值
(4)如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory(BeanFactory)传递的是Spring工厂自身（可以用这个方式来获取其它Bean，只需在Spring配置文件中配置一个普通的Bean就可以）；
(5)如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext)方法，传入Spring上下文（同样这个方式也可以实现步骤4的内容，但比4更好，因为ApplicationContext是BeanFactory的子接口，有更多的实现方法）；
(6)如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessBeforeInitialization(Object obj, String s)方法，BeanPostProcessor经常被用作是Bean内容的更改，并且由于这个是在Bean初始化结束时调用那个的方法，也可以被应用于内存或缓存技术；
(7)如果这个Bean实现InitializingBean接口，并且增加afterPropertiesSet()方法。
(8)如果Bean在Spring配置文件中配置了init-method属性会自动调用其配置的初始化方法。
(9)如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessAfterInitialization(Object obj, String s)方法。
注：以上工作完成以后就可以应用这个Bean了，那这个Bean是一个Singleton的，所以一般情况下我们调用同一个id的Bean会是在内容地址相同的实例，当然在Spring配置文件中也可以配置非Singleton。
(10)当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用那个其实现的destroy()方法；
(11)最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法。

3.Spring-bean的循环依赖以及解决方式

循环依赖其实就是循环引用，也就是两个或两个以上的bean互相持有对方，最终形成闭环。比如A依赖于B，B依赖于C，C又依赖于A。注意，这里不是函数的循环调用，是对象的相互依赖关系。循环调用其实就是一个死循环，除非有终结条件。
Spring中循环依赖场景有： （1）构造器的循环依赖 （2）field属性的循环依赖。
检测循环依赖相对比较容易，Bean在创建的时候可以给该Bean打标，如果递归调用回来发现正在创建中的话，即说明了循环依赖了。
Spring的循环依赖的理论依据其实是基于Java的引用传递，当我们获取到对象的引用时，对象的field或属性是可以延后设置的(但是构造器必须是在获取引用之前)。

Spring的单例对象的初始化主要分为三步： 
（1）createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象
（2）populateBean：填充属性，这一步主要是对bean的依赖属性进行填充
（3）initializeBean：调用spring xml中的init方法。

循环依赖主要发生在第一、第二步。也就是构造器循环依赖和field循环依赖。那么我们要解决循环引用也应该从初始化过程着手，对于单例来说，在Spring容器整个生命周期内，有且只有一个对象，所以很容易想到这个对象应该存在Cache中，Spring为了解决单例的循环依赖问题，使用了三级缓存。
三级缓存主要指：

    //  Cache of singleton objects: bean name --> bean instance 单例对象的cache
    private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);
    
    //  Cache of early singleton objects: bean name --> bean instance 提前曝光的单例对象的Cache 
    private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
    
    // Cache of singleton factories: bean name --> ObjectFactory 单例对象工厂的cache 
    private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);
    
分析getSingleton()的整个过程，Spring首先从一级缓存singletonObjects中获取。如果获取不到，并且对象正在创建中，就再从二级缓存earlySingletonObjects中获取。如果还是获取不到且允许singletonFactories通过getObject()获取，就从三级缓存singletonFactory.getObject()(三级缓存)获取。
如果获取到了则从singletonFactories中移除，并放入earlySingletonObjects中。其实也就是从三级缓存移动到了二级缓存。
从上面三级缓存的分析，我们可以知道，Spring解决循环依赖的诀窍就在于singletonFactories这个三级cache。这个cache的类型是ObjectFactory。
这里就是解决循环依赖的关键，这段代码发生在createBeanInstance之后，也就是说单例对象此时已经被创建出来(调用了构造器)。这个对象已经被生产出来了，虽然还不完美（还没有进行初始化的第二步和第三步），但是已经能被人认出来了（根据对象引用能定位到堆中的对象），所以Spring此时将这个对象提前曝光出来让大家认识，让大家使用。
这样做有什么好处呢？让我们来分析一下。A的某个field或者setter依赖了B的实例对象，同时B的某个field或者setter依赖了A的实例对象”这种循环依赖的情况。A首先完成了初始化的第一步，并且将自己提前曝光到singletonFactories中，此时进行初始化的第二步，发现自己依赖对象B，此时就尝试去get(B)，发现B还没有被create，所以走create流程，B在初始化第一步的时候发现自己依赖了对象A，于是尝试get(A)，尝试一级缓存singletonObjects(肯定没有，因为A还没初始化完全)，尝试二级缓存earlySingletonObjects（也没有），尝试三级缓存singletonFactories，由于A通过ObjectFactory将自己提前曝光了，所以B能够通过ObjectFactory.getObject拿到A对象(虽然A还没有初始化完全，但是总比没有好呀)，B拿到A对象后顺利完成了初始化阶段1、2、3，完全初始化之后将自己放入到一级缓存singletonObjects中。此时返回A中，A此时能拿到B的对象顺利完成自己的初始化阶段2、3，最终A也完成了初始化，进去了一级缓存singletonObjects中，而且更加幸运的是，由于B拿到了A的对象引用，所以B现在hold住的A对象完成了初始化。
知道了这个原理时候，肯定就知道为啥Spring不能解决“A的构造方法中依赖了B的实例对象，同时B的构造方法中依赖了A的实例对象”这类问题了！因为加入singletonFactories三级缓存的前提是执行了构造器，所以构造器的循环依赖没法解决。

4.SpringMVC执行原理

HelloController这个类需要实现Controller这个接口，并且覆写handleRequest这个方法。

    public class HelloController implements Controller {
        @Override 
        public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
            ModelAndView mv = new ModelAndView();
            String msg="HelloSpringmvc!";
            mv.addObject("msg",msg);
            mv.setViewName("test");
            return mv;
        }
    }
    
在资源路径下创建springmvc的配置文件。

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
           <!-- 配置处理器映射器-->
           <bean id="beanNameUrlHandlerMapping" class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
           <!-- 配置处理器适配器-->
           <bean id="controllerHandlerAdapter" class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
            <!--配置视图解析器-->
           <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
                <!--路径前缀-->
               <property name="prefix" value="/WEB-INF/jsp/"/>
                <!--路径后缀-->
               <property name="suffix" value=".jsp"/>
           </bean>
            <!--BeanNameUrlHandlerMapping这个类会自动找到与请求一致的类-->
           <bean id="/hello" class="com.zhang.controller.HelloController"/>
    </beans>
    
在web.xml中配置springmvc的核心控制器DispatchServlet。

    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
             version="4.0">
        <!--配置前端控制器-->
        <servlet>
            <servlet-name>dispatcherServlet</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <!--初始化时加载配置文件-->
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:springmvc.xml</param-value>
            </init-param>
            <!--开启服务器时启动-->
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatcherServlet</servlet-name>
            <!--/和/*的区别
                /:只会去匹配请求,不会匹配jsp页面
                /*:会匹配所有请求
            -->
            <url-pattern>/</url-pattern>
        </servlet-mapping>
    </web-app>

执行原理：

(1)DispatcherServlet：前端控制器，作为整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。
(2)HandlerMapping：处理器映射器，DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url去查找对应的处理。
(3)HandlerExecution：具体的handler(处理)，将解析后的url传递给DispatcherServlet。
(4)HandlerAdapter：处理器适配器，将DispatcherServlet传递的信息去执行相应的controller。
(5)Controller层中调用service层，获得数据放在ModelAndView对象中，并给ModelAndView设置页面信息。
(6)HandlerAdapter将视图名传递给DispatcherServlet。
(7)DispatcherServlet调用视图解析器来解析HandlerAdapter传递的视图名。
(8)视图解析器将解析的视图名传给DispatcherServlet。
(9)DispatcherServlet根据视图解析器返回的视图名调用具体的视图。
(10)用户获得视图。

5.AOP

Spring中定义了五种类型的通知，它们分别是
前置通知 (@Before)
返回通知 (@AfterReturning)
异常通知 (@AfterThrowing)
后置通知 (@After)
环绕通知 (@Around)

执行顺序：
代码正常结束：@Around的jp.proceed()之前的逻辑 --> @Before --> jp.proceed()，也就是原函数执行 --> @Around的jp.proceed()之后的逻辑 --> @After --> @AfterReturning
代码出现异常：@Around的jp.proceed()之前的逻辑 --> @Before --> jp.proceed()，也就是原函数执行 --> @Around的jp.proceed()之后的逻辑 --> @After --> @AfterThrowing

基于注解：

    @Aspect
    @Component
    public class SysTimeAspect {
    
     /**
      * 切入点
      */
     @Pointcut("bean(sysMenuServiceImpl)")
     public void doTime(){}
    
     @Before("doTime()")
     public void doBefore(JoinPoint jp){
      System.out.println("time doBefore()");
     }
     @After("doTime()")
     public void doAfter(){//类似于finally{}代码块
      System.out.println("time doAfter()");
     }
     /**核心业务正常结束时执行
      * 说明：假如有after，先执行after,再执行returning*/
     @AfterReturning("doTime()")
     public void doAfterReturning(){
      System.out.println("time doAfterReturning");
     }
     /**核心业务出现异常时执行
      * 说明：假如有after，先执行after,再执行Throwing*/
     @AfterThrowing("doTime()")
     public void doAfterThrowing(){
      System.out.println("time doAfterThrowing");
     }
     @Around("doTime()")
     public Object doAround(ProceedingJoinPoint jp)
       throws Throwable{
      System.out.println("doAround.before");
      try {
      Object obj=jp.proceed();
      return obj;
      }catch(Throwable e) {
      System.out.println("doAround.error-->"+e.getMessage());
      throw e;
      }finally {
      System.out.println("doAround.after");
      }
     }
    }

基于配置文件：

    <aop:config>
          <aop:aspect id="myAop" ref="auditLogAop">
             <aop:pointcut id="myAuditLog" expression="execution(* com.zhanghao90.horizon.common.service..*.*(..))" />
             <aop:around method="addAuditLog" pointcut-ref="myAuditLog"/>
         </aop:aspect>
    </aop:config>

6.事务注解失效的场景

注解@Transactional配置的方法非public权限修饰；
注解@Transactional所在类非Spring容器管理的bean；
注解@Transactional所在类中，注解修饰的方法被类内部方法调用；
业务代码抛出异常类型非RuntimeException，事务失效；
业务代码中存在异常时，使用try…catch…语句块捕获，而catch语句块没有throw new RuntimeExecption异常；
注解@Transactional中Propagation属性值设置错误即Propagation.NOT_SUPPORTED；
mysql关系型数据库，且存储引擎是MyISAM而非InnoDB，则事务会不起作用；

7.DI与IOC

所谓的依赖注入，则是，甲方开放接口，在它需要的时候，能够讲乙方传递进来(注入)
所谓的控制反转，甲乙双方不相互依赖，交易活动的进行不依赖于甲乙任何一方，整个活动的进行由第三方负责管理(Spring容器)。

N.参考

(1)[Spring 官网](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)

(2)[Spring Initializer](https://start.spring.io/)

(3)[详解IOC](https://www.cnblogs.com/xb1223/p/10148503.html)

(4)[详解AOP](https://www.cnblogs.com/xb1223/p/10169220.html)

(5)[面试题思考：解释一下什么叫AOP](https://www.cnblogs.com/songanwei/p/9417343.html)

(6)[Spring-bean的循环依赖以及解决方式](https://blog.csdn.net/u010853261/article/details/77940767)

(7)[【242期】面试官：Spring AOP有哪些通知类型，它们的执行顺序是怎样的？](https://mp.weixin.qq.com/s/dOTtB8zF4Id6NhXZ8jS2xw)

# Spring Boot

1.Spring，SpringMVC，SpringBoot，SpringCloud的区别和联系

Spring是核心，提供了基础功能。
Spring MVC是基于Spring的一个MVC框架。
Spring Boot是为简化Spring配置的快速开发整合包。
Spring Cloud是构建在Spring Boot之上的服务治理框架。

2.Spring Boot特征

(1)创建独立的Spring应用。
(2)嵌入式Tomcat、 Jetty、 Undertow容器（无需部署war文件）。
(3)提供的starters简化构建配置。
(4)尽可能自动配置 spring应用。
(5)提供生产指标,例如指标、健壮检查和外部化配置。
(6)完全没有代码生成和XML配置要求。

3.Spring Boot自动装配过程

SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入容器，自动配置类就生效，帮我们进行自动配置工作。
以前我们需要自己配置的东西，自动配置类都帮我们解决了。整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中。它将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中。
它会给容器中导入非常多的自动配置类(xxxAutoConfiguration），就是给容器中导入这个场景需要的所有组件，并配置好这些组件。有了自动配置类，免去了我们手动编写配置注入功能组件等的工作。

# Spring Cloud

1.怎么解决Eureka某一个服务挂掉的问题

同一个服务部署了多个实例，在通过网关调用时会随机调用其中一个。但是，当某个服务挂掉之后，依然在注册中心中，依然会随机被调用到，调用时便会超时报错。（主要是开发测试或者演示时需要立即将失效的从注册中心剔除。）
(1)停止服务端，向eureka注册中心发送delete请求，到http://{eurekaIP:port}/eureka/apps/{application.name}/{http://serviceIP:port}
(2)如果你的eureka客户端是是一个spring boot应用，可以通过调用以下代码通知注册中心下线。DiscoveryManager.getInstance().shutdownComponent();
   
    @RestController
    public class HelloController {
       @Autowired
       private DiscoveryClient client;
    
       @RequestMapping(value = "/hello", method = RequestMethod.GET)
       public String index() {
           java.util.List<ServiceInstance> instances = client.getInstances("hello-service");       
           return "Hello World";
       }
       
       @RequestMapping(value = "/offline", method = RequestMethod.GET)
       public void offLine(){
           DiscoveryManager.getInstance().shutdownComponent();
       }   
    }

# Dubbo

1.Dubbo是什么，能做什么
Dubbo是阿里巴巴开源的基于Java的高性能RPC分布式服务框架。其核心部分包含:

(1)远程通讯: 提供对多种基于长连接的NIO框架抽象封装，包括多种线程模型，序列化，以及“请求-响应”模式的信息交换方式。透明化的远程方法调用，就像调用本地方法一样调用远程方法，只需简单配置，没有任何API侵入。
(2)集群容错: 提供基于接口方法的透明远程过程调用，包括多协议支持，以及软负载均衡，失败容错，地址路由，动态配置等集群支持。软负载均衡及容错机制，可在内网替代F5等硬件负载均衡器，降低成本，减少单点。
(3)自动发现: 基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，使服务提供方可以平滑增加或减少机器。服务自动注册与发现，不再需要写死服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。

2.Dubbo内置了哪几种服务容器？

Spring Container，Jetty Container，Log4j Container

3.Dubbo核心的配置有哪些，什么关系

Provider-side包括：dubbo:protocol, dubbo:provider, dubbo:service
Application-shared包括：dubbo:application, dubbo:registry, dubbo:monitor
Comsumer-side包括：dubbo:consummer, dubbo:reference
Sub-config包括：dubbo:method与service和reference关联，dubbo:argument与method关联

4.Dubbo有哪几种集群容错方案，默认是哪种？

failover:默认，失败自动切换，重试其他服务器。
failfast:快速失败，立即报错，只调用一次。
failsafe:安全失败，出现异常，直接忽略。
failback:失败自动恢复，记录失败请求，定时重发。
forking:并行调用多个，只要一个成功即返回。
broadcast:广播调用所有提供者，任意一个报错即报错。

5.Dubbo有哪几种负载均衡策略，默认是哪种？

random loadbalance:默认，按权重设置随机概率。
roundRobin loadbalance:轮循，按公约后的权重设置轮询比率。
leastActive loadbalance:最小活跃调用数，相同活跃数的随机。
consistentHash loadbalance:一致性哈希，相同参数的请求，总是到同一个提供者。

6.Dubbo默认使用的是什么通信框架，还有别的选择吗？
Dubbo默认使用Netty框架，也是推荐的选择，另外内容还集成有Mina、Grizzly。

7.你觉得用Dubbo好还是SpringCloud好？
没有好坏，只有适合不适合。

(1)dubbo的优势：
单一应用架构，当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的 数据访问框架（ORM）是关键。
垂直应用架构，当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架（MVC）是关键。
分布式服务架构，当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的 分布式服务框架（RPC）是关键。
流动计算架构当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心（SOA）是关键。

(2)SpringCloud优势：

约定优于配置。
开箱即用、快速启动。
适用于各种环境。
轻量级的组件。
组件支持丰富，功能齐全。

(3)两者相比较：
dubbo由于是二进制的传输，占用带宽会更少。springCloud是http协议传输，带宽会比较多，同时使用http协议一般会使用JSON报文，消耗会更大。
dubbo的开发难度较大，原因是dubbo的jar包依赖问题很多大型工程无法解决。
springcloud的接口协议约定比较自由且松散，需要有强有力的行政措施来限制接口无序升级。
dubbo的注册中心可以选择zk,redis等多种，springcloud的注册中心只能用eureka或者自研。

# Hibernate

1.Hibernate的优缺点

优点：
(1)Hibernate对JDBC访问数据库的代码做了封装，大大简化了数据访问层繁琐的重复性代码，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。
(2)Hibernate是一个基于JDBC的主流持久化框架，是一个优秀的ORM实现，很大程度上简化了dao层编码工作。
(3)Hibernate使用java反射机制，而不是字节码增强程序类实现透明性。
(4)Hibernate的性能非常好，因为它是一个轻量级框架。映射的灵活性很出色。它支持很多关系型数据库，从一对一到多对多的各种复杂关系。

缺点：
Hibernate在批量数据处理时有弱势，针对单一对象简单的增删查改，适合于Hibernate。而对于批量的修改，删除，不适合用Hibernate,这也是ORM框架的弱点。

2.Hibernate的工作原理
(1)通过Configuration().configure();读取并解析hibernate.cfg.xml配置文件。
(2)由hibernate.cfg.xml中的<mapping resource="com/xx/User.hbm.xml"/>读取并解析映射信息。
(3)通过config.buildSessionFactory();创建SessionFactory。
(4)sessionFactory.openSession();打开Session。
(5)session.beginTransaction();创建事务Transaction。
(6)持久化操作。
(7)session.getTransaction().commit();提交事务。
(8)关闭Session。
(9)关闭SessionFactory。

3.Hibernate的核心接口

一共5个，分别是Session、SessionFactory、Transaction、Query 和 Configuration。
(1)Session接口:Session接口负责执行被持久化对象的CRUD操作。但需要注意的是Session对象是非线程安全的。同时，Hibernate的session不同于JSP应用中的HttpSession。这里当使用session这个术语时，其实指的是Hibernate中的session，HttpSesion对象称为用户session。
(2)SessionFactory接口:SessionFactory接口负责初始化Hibernate。它充当数据存储源的代理，并负责创建Session对象。这里用到了工厂模式。需要注意的是SessionFactory并不是轻量级的，因为一般情况下， 一个项目通常只需要一个SessionFactory就够，当需要操作多个数据库时，可以为每个数据库指定一个SessionFactory。
(3)Configuration接口:Configuration接口负责配置并启动Hibernate，创建SessionFactory对象。在Hibernate的启动的过程中，Configuration类的实例首先定位映射文档位置、读取配置，然后创建SessionFactory对象。
(4)Transaction接口:Transaction接口负责事务相关的操作。它是可选的，开发人员也可以设计编写自己的底层事务处理代码。
(5)Query和Criteria接口:Query和Criteria接口负责执行各种数据库查询。它可以使用HQL语言或SQL语句两种表达方式。

# Mybatis

1.Mybatis，优缺点，适用场合

优点：
基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接。
很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）。能够与Spring很好的集成。
提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

缺点：
SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求。
SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

适用场合：
MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。
对性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。

2.MyBatis与Hibernate有哪些不同？
Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。
Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。
Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。


3.#{}与${}差别

差别是#是预编译处理，$是字符串替换。
(1)#将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。$将传入的数据直接显示生成在sql中。
(2)#方式在很大程度上能够防止sql注入。$方式无法防止sql注入。一般能用#的就别用$。$方式一般用于传入数据库对象，例如传入表名。
(3)#在预处理时，会把参数部分用一个占位符？代替，调用PreparedStatement的set方法来赋值。$只会做简单的字符串替换，在动态SQL解析阶段将会进行变量替换。

4.当实体类中的属性名和表中的字段名不一样怎么办

第1种：通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。

    <select id=”selectorder” parameterType=”int” resulteType=”me.gacl.domain.order”>         
          select order_id id, order_no orderno ,order_price price from orders where order_id=#{id};      
    </select>
    
第2种：通过来映射字段名和实体类属性名的一一对应的关系。

    <select id="getOrder" parameterType="int" resultMap="orderresultmap">  
        select * from orders where order_id=#{id}  
    </select>  
  
    <resultMap type=”me.gacl.domain.order” id=”orderresultmap”>  
        <!–用id属性来映射主键字段–>  
        <id property=”id” column=”order_id”>  
        <!–用result属性来映射非主键字段，property为实体类属性名，column为数据表中的属性–>  
        <result property = “orderno” column =”order_no”/>  
        <result property=”price” column=”order_price” />  
    </reslutMap>  
    
5.模糊查询like语句该怎么写?

    string wildcardname = “%smi%”;  
    list<name> names = mapper.selectlike(wildcardname);  
  
    <select id=”selectlike”>  
         select * from foo where bar like #{value}  
    </select>
    
6.通常一个Xml映射文件，都会写一个Dao接口与之对应，请问这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

Dao接口即Mapper接口。接口的全限名，就是映射文件中mapper的namespace的值；接口的方法名，就是映射文件中mapper的Statement的id值；接口方法内的参数，就是传递给sql的参数。
Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在Mybatis中，每一个<select>、<insert>、<update>、<delete>标签，都会被解析为一个MapperStatement对象。
举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id为findStudentById的MapperStatement。
Mapper接口里的方法，是不能重载的，因为是使用"全限名+方法名"的保存和寻找策略。Mapper接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Mapper接口生成代理对象proxy，代理对象会拦截接口方法，转而执行MapperStatement所代表的sql，然后将sql执行结果返回。
不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复。原因就是namespace+id是作为Map<String, MapperStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

7.分页查询

(1)sql的limit语法，直接书写带有物理分页的参数来完成物理分页功能。
(2)使用Mybatis的RowBounds(int offset, int limit)对象进行分页，不适合数据量大。它是针对ResultSet结果集执行的内存分页，而非物理分页。
(3)自己实现拦截器，对执行的sql语句加limit条件。分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

8.如何执行批量插入?

    <insert id=”insertname”>  
         insert into names (name) values (#{value})  
    </insert>  

    List<String> names = new Arraylist();  
    names.add(“fred”);  
    names.add(“barney”);  
    names.add(“betty”);  
    names.add(“wilma”);  
  
    // 注意这里 executortype.batch  
    SqlSession sqlSession = sqlSessionFactory.openSession(Executortype.Batch);  
    try {  
     NameMapper mapper = sqlSession.getMapper(NameMapper.class);  
     for (string name : names) {  
         mapper.insertname(name);  
     }  
     sqlSession.commit();  
    }catch(Exception e){  
     e.printStackTrace();  
     sqlSession.rollback();   
     throw e;   
    }  
     finally {  
       sqlSession.close();  
    }

9.在mapper中如何传递多个参数?
第一种：

    // DAO层的函数  
    Public UserselectUser(String name,String area);  
    // 对应的xml,#{0}代表接收的是dao层中的第一个参数，#{1}代表dao层中第二参数，更多参数一致往后加即可。  
    <select id="selectUser"resultMap="BaseResultMap">    
      select *  from user_t where user_name = #{0} and user_area=#{1}     
    </select>
    
第二种：使用@param注解:

    public interface UserMapper {  
       User selectUser(@Param(“username”) String username,@Param(“hashedpassword”) String hashedpassword);  
    }
      
    // 然后,就可以在xml像下面这样使用(推荐封装为一个map,作为单个参数传递给mapper):  
    <select id=”selectUser” resulttype=”user”>  
             select id, username, hashedpassword  
             from some_table  
             where username = #{username} and hashedpassword = #{hashedpassword}   
    </select>

第三种：多个参数封装成map

    try{  
        //映射文件的命名空间SQL片段的ID，就可以调用对应的映射文件中的SQL  
        //由于我们的参数超过了两个，而方法中只有一个Object参数收集，因此我们使用Map集合来装载我们的参数  
       Map<String, Object> map = new HashMap();       
       map.put("start", start);       
       map.put("end", end);       
       return sqlSession.selectList("StudentID.pagination", map);  
    }catch(Exception e){      
       e.printStackTrace();       
       sqlSession.rollback();      
       throw e; 
    }  
    finally{   
       MybatisUtil.closeSqlSession();   
    }
     
10.Mybatis动态sql有什么用？执行原理？有哪些动态sql？

Mybatis动态sql可以在Xml映射文件内，以标签的形式编写动态sql，执行原理是根据表达式的值完成逻辑判断并动态拼接sql的功能。
Mybatis提供了9种动态sql标签：trim | where | set | foreach | if | choose | when | otherwise | bind。

11.Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？
<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>，加上动态sql的9个标签。
其中为sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增的主键生成策略标签。

12.一对一、一对多的关联查询

    <mapper namespace="com.lcb.mapping.userMapper">    
        <!--association  一对一关联查询 -->    
        <select id="getClass" parameterType="int" resultMap="ClassesResultMap">    
            select * from class c,teacher t where c.teacher_id=t.t_id and c.c_id=#{id}    
        </select>    
      
        <resultMap type="com.lcb.user.Classes" id="ClassesResultMap">    
            <!-- 实体类的字段名和数据表的字段名映射 -->    
            <id property="id" column="c_id"/>    
            <result property="name" column="c_name"/>    
            <association property="teacher" javaType="com.lcb.user.Teacher">    
                <id property="id" column="t_id"/>    
                <result property="name" column="t_name"/>    
            </association>    
        </resultMap>    
      
      
        <!--collection  一对多关联查询 -->    
        <select id="getClass2" parameterType="int" resultMap="ClassesResultMap2">    
            select * from class c,teacher t,student s where c.teacher_id=t.t_id and c.c_id=s.class_id and c.c_id=#{id}    
        </select>    
      
        <resultMap type="com.lcb.user.Classes" id="ClassesResultMap2">    
            <id property="id" column="c_id"/>    
            <result property="name" column="c_name"/>    
            <association property="teacher" javaType="com.lcb.user.Teacher">    
                <id property="id" column="t_id"/>    
                <result property="name" column="t_name"/>    
            </association>    
      
            <collection property="student" ofType="com.lcb.user.Student">    
                <id property="id" column="s_id"/>    
                <result property="name" column="s_name"/>    
            </collection>    
        </resultMap>    
    </mapper> 
  
MyBatis实现一对一有几种方式?具体怎么操作的？
有联合查询和嵌套查询。
联合查询是几个表联合查询，只查询一次,，通过在resultMap里面配置association节点配置一对一的类就可以完成。
嵌套查询是先查一个表，根据这个表里面的结果的外键id，去再另外一个表里面查询数据,也是通过association配置，但另外一个表的查询通过select属性配置。

MyBatis实现一对多有几种方式,怎么操作的？
有联合查询和嵌套查询。
联合查询是几个表联合查询，只查询一次，通过在resultMap里面的collection节点配置一对多的类就可以完成。
嵌套查询是先查一个表，根据这个表里面的结果的外键id，去再另外一个表里面查询数据，也是通过配置collection,但另外一个表的查询通过select节点配置。

13.Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？
Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。
在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。
它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。
当然了，不光是Mybatis，几乎所有的框架包括Hibernate，支持延迟加载的原理都是一样的。

14.Mybatis的一级、二级缓存
一级缓存基于PerpetualCache的HashMap本地缓存，其存储作用域为Session，当Session flush或close之后，该Session中的所有Cache就将清空，默认打开一级缓存。
二级缓存与一级缓存其机制相同，默认也是采用PerpetualCache，HashMap存储，不同在于其存储作用域为Mapper(Namespace)，并且可自定义存储源，如Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态)，可在它的映射文件中配置<cache/> ；
对于缓存数据更新机制，当某一个作用域(一级缓存Session/二级缓存Namespaces)的进行了C/U/D操作后，默认该作用域下所有select中的缓存将被clear。

15.什么是MyBatis的接口绑定？有哪些实现方式？
接口绑定，就是在MyBatis中任意定义接口，然后把接口里面的方法和SQL语句绑定，我们直接调用接口方法就可以，这样比起原来了SqlSession提供的方法我们可以有更加灵活的选择和设置。
接口绑定有两种实现方式,一种是通过注解绑定，就是在接口的方法上面加上@Select、@Update等注解，里面包含Sql语句来绑定。
另外一种就是通过xml里面写SQL来绑定, 在这种情况下,要指定xml映射文件里面的namespace必须为接口的全路径名。当Sql语句比较简单时候用注解绑定，当SQL语句比较复杂时候用xml绑定,一般用xml绑定的比较多。

16.使用MyBatis的mapper接口调用时有哪些要求？
Mapper接口方法名和mapper.xml中定义的每个sql的id相同；
Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同；
Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同；
Mapper.xml文件中的namespace即是mapper接口的类路径。

17.Mapper编写有哪几种方式？

第一种：接口实现类继承SqlSessionDaoSupport：使用此种方法需要编写mapper接口，mapper接口实现类、mapper.xml文件。

在sqlMapConfig.xml中配置mapper.xml的位置

    <mappers>  
        <mapper resource="mapper.xml文件的地址" />  
        <mapper resource="mapper.xml文件的地址" />  
    </mappers>
    
定义mapper接口，实现类继承SqlSessionDaoSupport，mapper方法中可以this.getSqlSession()进行数据增删改查。
spring 配置

    <bean id="mapperImpl" class="mapper接口的实现">  
        <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>  
    </bean>

第二种：使用org.mybatis.spring.mapper.MapperFactoryBean：
在sqlMapConfig.xml中配置mapper.xml的位置，如果mapper.xml和mapper接口的名称相同且在同一个目录，这里可以不用配置

    <mappers>  
        <mapper resource="mapper.xml文件的地址" />  
        <mapper resource="mapper.xml文件的地址" />  
    </mappers>  
    
定义mapper接口：mapper.xml中的namespace为mapper接口的地址。mapper接口中的方法名和mapper.xml中的定义的statement的id保持一致。Spring中定义

    <bean id="mapperFactoryBean" class="org.mybatis.spring.mapper.MapperFactoryBean">  
        <property name="mapperInterface"   value="mapper接口地址" />   
        <property name="sqlSessionFactory" ref="sqlSessionFactory" />   
    </bean>  

第三种：使用mapper扫描器：
mapper.xml文件编写：mapper.xml中的namespace为mapper接口的地址；mapper接口中的方法名和mapper.xml中的定义的statement的id保持一致；如果将mapper.xml和mapper接口的名称保持一致则不用在sqlMapConfig.xml中进行配置。
定义mapper接口：注意mapper.xml的文件名和mapper的接口名称保持一致，且放在同一个目录。
配置mapper扫描器，使用扫描器后从spring容器中获取mapper的实现对象。

    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">  
        <property name="basePackage" value="mapper接口包地址"></property>  
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>   
    </bean>  

18.简述Mybatis的插件运行原理，以及如何编写一个插件。
Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
编写插件：实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。

19.流式查询

流式查询指的是查询成功后不是返回一个集合而是返回一个迭代器，应用每次从迭代器取一条查询结果。流式查询的好处是能够降低内存使用。
如果没有流式查询，我们想要从数据库取1000万条记录而又没有足够的内存时，就不得不分页查询，而分页查询效率取决于表设计，如果设计的不好，就无法执行高效的分页查询。因此流式查询是一个数据库访问框架必须具备的功能。
流式查询的过程当中，数据库连接是保持打开状态的，因此要注意的是：执行一个流式查询后，数据库访问框架就不负责关闭数据库连接了，需要应用在取完数据后自己关闭。
MyBatis提供了一个叫org.apache.ibatis.cursor.Cursor的接口类用于流式查询，这个接口继承了java.io.Closeable和java.lang.Iterable接口，由此可知：Cursor是可关闭的；Cursor是可遍历的。
除此之外，Cursor 还提供了三个方法：
isOpen()：用于在取数据之前判断Cursor对象是否是打开状态。只有当打开时Cursor才能取数据；
isConsumed()：用于判断查询结果是否全部取完。
getCurrentIndex()：返回已经获取了多少条数据。

错误示例，执行scanFoo0()时会报错：java.lang.IllegalStateException: A Cursor is already closed。这是因为我们前面说了在取数据的过程中需要保持数据库连接，而Mapper方法通常在执行完后连接就关闭了，因此Cursor也一并关闭了。

    @Mapper
    public interface FooMapper {
        @Select("select * from foo limit #{limit}")
        Cursor<Foo> scan(@Param("limit") int limit);
    }
    
    @GetMapping("foo/scan/0/{limit}")
    public void scanFoo0(@PathVariable("limit") int limit) throws Exception {
        try (Cursor<Foo> cursor = fooMapper.scan(limit)) {  // 1
            cursor.forEach(foo -> {});                      // 2
        }
    }

解决方案：

    //方案一：SqlSessionFactory我们可以用SqlSessionFactory来手工打开数据库连接，1处我们开启了一个SqlSession（实际上也代表了一个数据库连接），并保证它最后能关闭；2处我们使用SqlSession来获得Mapper对象。这样才能保证得到的Cursor对象是打开状态的。
    @GetMapping("foo/scan/1/{limit}")
    public void scanFoo1(@PathVariable("limit") int limit) throws Exception {
        try (
            SqlSession sqlSession = sqlSessionFactory.openSession();  // 1
            Cursor<Foo> cursor = sqlSession.getMapper(FooMapper.class).scan(limit)   // 2
        ) {
            cursor.forEach(foo -> { });
        }
    }
    
    //方案二：可以用TransactionTemplate来执行一个数据库事务，这个过程中数据库连接同样是打开的。1处我们创建了一个TransactionTemplate对象，2处执行数据库事务，而数据库事务的内容则是调用Mapper对象的流式查询。注意这里的Mapper对象无需通过SqlSession创建。
    @GetMapping("foo/scan/2/{limit}")
    public void scanFoo2(@PathVariable("limit") int limit) throws Exception {
        TransactionTemplate transactionTemplate =  new TransactionTemplate(transactionManager);  // 1
        transactionTemplate.execute(status -> {               // 2
            try (Cursor<Foo> cursor = fooMapper.scan(limit)) {
                cursor.forEach(foo -> { });
            } catch (IOException e) {
                e.printStackTrace();
            }
            return null;
        });
    }
    
    //方案三：@Transactional注解，它仅仅是在原来方法上面加了个@Transactional注解。这个方案看上去最简洁，但请注意Spring框架当中注解使用的坑：只在外部调用时生效。在当前类中调用这个方法，依旧会报错。
    @GetMapping("foo/scan/3/{limit}")
    @Transactional
    public void scanFoo3(@PathVariable("limit") int limit) throws Exception {
        try (Cursor<Foo> cursor = fooMapper.scan(limit)) {
            cursor.forEach(foo -> { });
        }
    }
    
N.参考

(1)[【250期】关于Mybatis知识点，面试可以问的都在这里了！](https://mp.weixin.qq.com/s/kReDxX3pA_ygyxzeWJDtoQ)