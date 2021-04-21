---
layout: post
title: "后端开发面试题 -- Spring篇"
description: 后端开发面试题 -- Spring篇
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
(4)如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory(setBeanFactory(BeanFactory)传递的是Spring工厂自身（可以用这个方式来获取其它Bean，只需在Spring配置文件中配置一个普通的Bean就可以）；
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

基于注解：

    TODO

基于配置文件：

    	<aop:config>
    		  <aop:aspect id="myAop" ref="auditLogAop">
    		  	 <aop:pointcut id="myAuditLog" expression="execution(* com.zhanghao90.horizon.common.service..*.*(..))" />
    		     <aop:around method="addAuditLog" pointcut-ref="myAuditLog"/>
    		 </aop:aspect>
    	</aop:config>

N.参考

(1)[Spring 官网](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)

(2)[Spring Initializer](https://start.spring.io/)

(3)[详解IOC](https://www.cnblogs.com/xb1223/p/10148503.html)

(4)[详解AOP](https://www.cnblogs.com/xb1223/p/10169220.html)

(5)[面试题思考：解释一下什么叫AOP](https://www.cnblogs.com/songanwei/p/9417343.html)

(6)[Spring-bean的循环依赖以及解决方式](https://blog.csdn.net/u010853261/article/details/77940767)

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