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

2.Spring，SpringMVC，SpringBoot，SpringCloud的区别和联系

Spring是核心，提供了基础功能。Spring是一个一站式的轻量级的java开发框架，核心是控制反转（IOC）和面向切面（AOP），针对于开发的WEB层(springMvc)、业务层(Ioc)、持久层(jdbcTemplate)等都提供了多种配置解决方案。
Spring MVC是基于Spring的一个MVC框架。SpringMVC是Spring基础之上的一个MVC框架，主要处理web开发的路径映射和视图渲染，属于Spring框架中WEB层开发的一部分。
Spring Boot是为简化Spring配置的快速开发整合包。SpringBoot使用了默认大于配置的理念，集成了快速开发的Spring多个插件，同时自动过滤不需要配置的多余的插件，简化了项目的开发配置流程，一定程度上取消xml配置，是一套快速配置开发的脚手架，能快速开发单个微服务。更专注于开发微服务后台接口，不开发前端视图。
Spring Cloud是构建在Spring Boot之上的服务治理框架。SpringCloud大部分的功能插件都是基于SpringBoot去实现的，SpringCloud关注于全局的微服务整合和管理，将多个SpringBoot单体微服务进行整合以及管理；SpringCloud依赖于SpringBoot开发，而SpringBoot可以独立开发。

3.Spring Bean的生命周期

容器启动阶段：

(1)BeanDefenitionReader读取配置元信息（例如xml），生成BeanDefenition。
(2)将BeanDefination注册到BeanDefinationRegistry中，BeanDefinationRegistry就是一个存放BeanDefination的大篮子，它也是一种键值对的形式，通过特定的Bean定义的id，映射到相应的BeanDefination。
(3)BeanFactoryPostProcessor是容器启动阶段Spring提供的一个扩展点，主要负责对注册到BeanDefinationRegistry中的一个个的BeanDefination进行一定程度上的修改与替换。BeanFactoryPostProcessor实现类实例化。BeanFactoryPostProcessor主要是在Spring刚加载完配置文件，还没来得及初始化Bean的时候做一些操作。比如篡改某个Bean在配置文件中配置的内容。BeanFactoryPostProcessor调用postProcessBeanFactory方法。

Bean实例化阶段：

(1)InstantiationAwareBeanPostProcessorAdapter实现类实例化。基本没什么用，Bean初始化后，还没有设置属性值时调用，和BeanFactoryPostProcessor一样，可以篡改配置文件加载到内存中的信息。
(2)BeanPostProcessor实现类实例化。没什么用，Spring框架内部使用的比较猛，像什么AOP，动态代理，都是在这搞事。
(3)InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法。
(4)InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法。
(5)如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String)方法，此处传递的就是Spring配置文件中Bean的id值
(6)如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory(BeanFactory)传递的是Spring工厂自身（可以用这个方式来获取其它Bean，只需在Spring配置文件中配置一个普通的Bean就可以）；
(7)如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext)方法，传入Spring上下文（同样这个方式也可以实现步骤4的内容，但比4更好，因为ApplicationContext是BeanFactory的子接口，有更多的实现方法）；
(8)如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessBeforeInitialization(Object obj, String s)方法，BeanPostProcessor经常被用作是Bean内容的更改，并且由于这个是在Bean初始化结束时调用那个的方法，也可以被应用于内存或缓存技术；
(9)如果这个Bean实现InitializingBean接口，并且增加afterPropertiesSet()方法。
(10)如果Bean在Spring配置文件中配置了init-method属性会自动调用其配置的初始化方法。
(11)InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法。
(12)如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessAfterInitialization(Object obj, String s)方法。
注：以上工作完成以后就可以应用这个Bean了，那这个Bean是一个Singleton的，所以一般情况下我们调用同一个id的Bean会是在内容地址相同的实例，当然在Spring配置文件中也可以配置非Singleton。
(13)当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用那个其实现的destroy()方法；
(14)最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法。

4.Spring-bean的循环依赖以及解决方式

循环依赖其实就是循环引用，也就是两个或两个以上的bean互相持有对方，最终形成闭环。比如A依赖于B，B依赖于C，C又依赖于A。注意，这里不是函数的循环调用，是对象的相互依赖关系。循环调用其实就是一个死循环，除非有终结条件。
Spring中循环依赖场景有：（1）构造器的循环依赖 （2）field属性的循环依赖。
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

5.AOP

传统的OOP开发中的代码逻辑是至上而下的过程中会产生一些横切性问题，这些横切性的问题和我们的主业务逻辑关系不大，会散落在代码的各个地方，造成难以维护。
AOP的编程思想就是把业务逻辑和横切的问题进行分离，从而达到解耦的目的，使代码的重用性和开发效率高（目的是重用代码，把公共的代码抽取出来）。

应用场景：
日志记录、权限验证、效率检查（实现校验，例如redis分布式锁等功能）、事务管理（spring 的事务就是用AOP实现的）

底层是怎样实现的：
JDK动态代理、CGLIB代理。
在运行期进行织入，生成字节码，再加载到虚拟机中，JDK是利用反射原理，CGLIB使用了ASM原理。
初始化的时候(不是获取对象时)，已经将目标对象进行代理，放入到spring容器中。
Spring默认，如果实现了接口的类，是使用jdk动态代理。如果没实现接口，就使用cglib代理。

Spring AOP和AspectJ的关系：
两者都是为了实现AOP这个目的而出现的技术，Spring aop参考AspectJ编程风格。
原本spring aop初期的时候所用的编程风格，让人用起来，很不方便，而且让人看不懂。后来，spring aop就开始取用了Aspectj的编程风格去进行编程。

AOP中的切面、切点、连接点、通知，四者的关系？

    aspect 切面
    pointcut 切点，表示连接点的集合（类似一个表）。
    joinpoint 连接点，目标对象中的方法（每一条记录）。
    weaving 把代理逻辑加入到目标对象上的过程叫做织入
    advice 通知

Spring中定义了五种类型的通知：

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

6.@Transactional事务注解

@Transactional是spring中声明式事务管理的注解配置方式，@Transactional注解可以帮助我们把事务开启、提交或者回滚的操作，通过aop的方式进行管理。
通过@Transactional注解就能让spring为我们管理事务，免去了重复的事务管理逻辑，减少对业务代码的侵入，使我们开发人员能够专注于业务层面开发。
我们知道实现@Transactional原理是基于spring aop，aop又是动态代理模式的实现。

失效的场景：

    注解@Transactional配置的方法非public权限修饰；
    注解@Transactional所在类非Spring容器管理的bean；
    注解@Transactional所在类中，注解修饰的方法被类内部方法调用；
    业务代码抛出异常类型非RuntimeException，事务失效；解决方案：@Transactional注解修饰的方法，加上rollbackfor属性值，指定回滚异常类型：@Transactional(propagation = Propagation.REQUIRED,rollbackFor = Exception.class)
    业务代码中存在异常时，使用try…catch…语句块捕获，而catch语句块没有throw new RuntimeExecption异常；
    注解@Transactional中Propagation属性值设置错误即Propagation.NOT_SUPPORTED；
    mysql关系型数据库，且存储引擎是MyISAM而非InnoDB，则事务会不起作用；

7.DI与IOC

不好的依赖：汽车依赖车身，车身依赖底盘，底盘依赖轮子。这样的设计看起来没问题，但是可维护性却很低。
依赖倒置：轮子依赖底盘， 底盘依赖车身， 车身依赖汽车。这就是依赖倒置原则——把原本的高层建筑依赖底层建筑“倒置”过来，变成底层建筑依赖高层建筑。高层建筑决定需要什么，底层去实现这样的需求，但是高层并不用管底层是怎么实现的。
依赖倒置原则的思路是控制反转，控制反转具体采用的方法是依赖注入。
依赖注入：就是把底层类作为参数传入上层类，实现上层类对下层类的“控制”。用构造方法将传递的依赖注入。另外两种方法：Setter传递和接口传递。
控制反转容器：对类进行初始化的那段代码发生的地方，就是控制反转容器。这个容器可以自动对代码进行初始化。原始的创建过程，我们需要了解整个Car/Framework/Bottom/Tire类构造函数是怎么定义的，才能一步一步new/注入。而IoC Container在进行这个工作的时候是反过来的，它先从最上层开始往下找依赖关系，到达最底层之后再往上一步一步new（有点像深度优先遍历）。IoC Container可以隐藏具体的创建实例的细节。

8.Bean是如何注入的？

@Autowired：@Autowired是Spring的注解，Autowired默认先按byType，如果发现找到多个bean，则又按照byName方式比对，如果还有多个，则报出异常；@Autowired结合@Qualifier来使用，如下：

    @Autowired
    @Qualifier("testServiceImpl")
    private TestService testService;                                                                                              
                                                                                                  
@Resource：@Resource是JDK1.6支持的注解，默认按照名称(Byname)进行装配, 如果没有指定name属性，当注解写在字段上时，默认取字段名，按照名称查找，如果注解写在setter方法上默认取属性名进行装配。当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配。

    @Resource(name = "testServiceImpl")
    private TestService testService;    

通过在实现类上添加@Primary注解来指定默认加载类。这样如果在使用@Autowired/@Resource获取实例时如果不指定bean的名字，就会默认获取TestServiceImpl2的bean，如果指定了bean的名字则以指定的为准。

    @Service
    @Primary
    public class TestServiceImpl2 implements TestService{
    
        @Override
        public String test() {
            return "TestServiceImpl2";
        }
    }

效率上来说@Autowired/@Resource差不多，不过推荐使用@Resource一点，因为当接口有多个实现时@Resource直接就能通过name属性来指定实现类，而@Autowired还要结合@Qualifier注解来使用，且@Resource是jdk的注解，可与Spring解耦。

为什么非要调用接口来多此一举，而不直接调用实现类serviceImpl的bean来得简单明了呢？
直接获取实现类serviceImpl的bean也是可以的；
至于加一层接口的原因：一是AOP程序设置思想指导，给别人调用的接口，调用者只想知道方法和功能，而对于这个方法内部逻辑怎么实现的并不关心；二是可以降低各个模块间的关联，实现松耦合、程序分层、高扩展性，使程序更加灵活，他除了在规范上有卓越贡献外，最精髓的是在多态上的运用；继承只能单一继承，接口却可以多实现。
当业务逻辑简单，变更较少，项目自用时，省略掉接口直接使用实现类更简单明了；反之则推荐使用接口。

9.Spring初始化过程

首先初始化上下文，生成ClassPathXmlApplicationContext对象，再获取resourcePatternResolver对象将xml解析成Resource对象。
利用生成的context、resource初始化工厂，并将resource解析成beandefinition, 再将beandefinition注册到beanfactory中。

10.Spring bean作用域

共5中，后3种是在web项目下才用到的。

    singleton：单例模式，当spring创建applicationContext容器的时候，spring会预初始化所有的该作用域实例，加上lazy-init就可以避免预处理。
    prototype：原型模式，每次通过getBean获取该bean就会新产生一个实例，创建后spring将不再对其管理。
    request：每次请求都新产生一个实例，和prototype不同就是创建后，接下来的管理，spring依然在监听。
    session：每次会话，同上。
    global session：全局的web域，类似于servlet中的application。

11.常用注解

    // 配置相关
    @SpringBootApplication：让spring boot自动给程序进行必要的配置，这个配置等同于：@Configuration ，@EnableAutoConfiguration和@ComponentScan三个配置。其中@ComponentScan让spring Boot扫描到Configuration类并把它加入到程序上下文。
    @EnableAutoConfiguration：Spring Boot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用它们。
    @ComponentScan：表示将该类自动发现扫描组件。如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。如果没有配置的话，Spring Boot会扫描启动类所在包下以及子包下的使用了@Service,@Repository等注解的类。
    @Configuration：相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类，可以使用@ImportResource注解加载xml配置文件。
    @Import：用来导入其他配置类。
    @ImportResource：用来加载xml配置文件。
    
    // Spring、SpringMVC相关
    @Controller：用于定义控制器类，在spring项目中由控制器负责将用户发来的URL请求转发到对应的服务接口（service层），一般这个注解在类中，通常方法需要配合注解@RequestMapping。
    @RestController：用于标注控制层组件，是@ResponseBody和@Controller的合集。
    @Service：一般用于修饰service层的组件
    @Repository：这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置。
    @Component：泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。
    @Bean:用@Bean标注方法等价于XML中配置的bean。放在方法的上面，而不是类，意思是产生一个bean,并交给spring管理。
    @Value：注入Spring boot application.properties配置的属性的值。
    @AutoWired：自动导入依赖的bean。byType方式。把配置好的Bean拿来用，完成属性、方法的组装，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。当加上（required=false）时，就算找不到bean也不报错。
    @Qualifier：当有多个同一类型的Bean时，可以用@Qualifier(“name”)来指定。与@Autowired配合使用。@Qualifier限定描述符除了能根据名字进行注入，但能进行更细粒度的控制如何选择候选者。
    @Resource(name=”name”,type=”type”)：没有括号内内容的话，默认byName。与@Autowired干类似的事。
    @Inject：等价于默认的@Autowired，只是没有required属性。
    @RequestMapping：提供路由信息，负责URL到Controller中的具体函数的映射。该注解有六个属性：params:指定request中必须包含某些参数值是，才让该方法处理。headers:指定request中必须包含某些指定的header值，才能让该方法处理请求。value:指定请求的实际地址，指定的地址可以是URI Template 模式。method:指定请求的method类型， GET、POST、PUT、DELETE等。consumes:指定处理请求的提交内容类型（Content-Type），如application/json,text/html。produces:指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回。
    @ResponseBody：表示该方法的返回结果直接写入HTTP response body中，一般在异步获取数据时使用，用于构建RESTful的api。在使用@RequestMapping后，返回值通常解析为跳转路径，加上@responsebody后返回结果不会被解析为跳转路径，而是直接写入HTTP response body中。比如异步获取json数据，加上@responsebody后，会直接返回json数据。该注解一般会配合@RequestMapping一起使用。
    @RequestParam：用在方法的参数前面。
    @PathVariable:路径变量。

    // JPA相关
    @Entity：@Table(name="XXX")：表明这是一个实体类。一般用于jpa这两个注解一般一块使用，但是如果表名和实体类名相同的话，@Table可以省略
    @MappedSuperClass:用在确定是父类的entity上。父类的属性子类可以继承。
    @NoRepositoryBean:一般用作父类的repository，有这个注解，spring不会去实例化该repository。
    @Column：如果字段名与列名相同，则可以省略。
    @Id：表示该属性为主键。
    @GeneratedValue(strategy = GenerationType.SEQUENCE,generator = “repair_seq”)：表示主键生成策略是sequence（可以为Auto、IDENTITY、native等，Auto表示可在多个数据库间切换，一般用AUTO），指定sequence的名字是repair_seq。
    @SequenceGeneretor(name = “repair_seq”, sequenceName = “seq_repair”, allocationSize = 1)：name为sequence的名称，以便使用，sequenceName为数据库的sequence名称，两个名称可以一致。
    @Transient：表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性。如果一个属性并非数据库表的字段映射,就务必将其标示为@Transient,否则,ORM框架默认其注解为@Basic。@Basic(fetch=FetchType.LAZY)：标记可以指定实体属性的加载方式
    @JsonIgnore：作用是json序列化时将Java bean中的一些属性忽略掉,序列化和反序列化都受影响。
    @JoinColumn（name=”loginId”）:一对一：本表中指向另一个表的外键。一对多：另一个表指向本表的外键。
    @OneToOne、@OneToMany、@ManyToOne：对应hibernate配置文件中的一对一，一对多，多对一。
    
    // 全局异常处理
    @ControllerAdvice：包含@Component。可以被扫描到。统一处理异常。
    @ExceptionHandler（Exception.class）：用在方法上面表示遇到这个异常就执行以下方法。
    
12.BeanFactory和FactoryBean的区别

(1)BeanFactory

BeanFactory以Factory结尾，表示它是一个工厂类接口， 它负责生产和管理bean的一个工厂。在Spring中，BeanFactory是IOC容器的核心接口，它的职责包括：实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。
BeanFactory只是个接口，并不是IOC容器的具体实现，但是Spring容器给出了很多种实现，如 DefaultListableBeanFactory、XmlBeanFactory、ApplicationContext等，其中XmlBeanFactory就是常用的一个，该实现将以XML方式描述组成应用的对象及对象间的依赖关系。XmlBeanFactory类将持有此XML配置元数据，并用它来构建一个完全可配置的系统或应用。
BeanFacotry是spring中比较原始的Factory。原始的BeanFactory无法支持spring的许多插件，如AOP功能、Web应用等。
BeanFactory和ApplicationContext就是spring框架的两个IOC容器，现在一般使用ApplicationnContext，其不但包含了BeanFactory的作用，同时还进行更多的扩展。ApplicationContext包含BeanFactory的所有功能，通常建议比BeanFactory优先。ApplicationContext以一种更向面向框架的方式工作以及对上下文进行分层和实现继承。
BeanFactory提供的方法及其简单，仅提供了六种方法供客户调用：

    boolean containsBean(String beanName) 判断工厂中是否包含给定名称的bean定义，若有则返回true
    Object getBean(String) 返回给定名称注册的bean实例。根据bean的配置情况，如果是singleton模式将返回一个共享实例，否则将返回一个新建的实例，如果没有找到指定bean,该方法可能会抛出异常
    Object getBean(String, Class) 返回以给定名称注册的bean实例，并转换为给定class类型
    Class getType(String name) 返回给定名称的bean的Class,如果没有找到指定的bean实例，则排除NoSuchBeanDefinitionException异常
    boolean isSingleton(String) 判断给定名称的bean定义是否为单例模式
    String[] getAliases(String name) 返回给定bean名称的所有别名

(2)FactoryBean

一般情况下，Spring通过反射机制利用<bean>的class属性指定实现类实例化Bean，在某些情况下，实例化Bean过程比较复杂，如果按照传统的方式，则需要在<bean>中提供大量的配置信息。配置方式的灵活性是受限的，这时采用编码的方式可能会得到一个简单的方案。
Spring为此提供了一个org.springframework.bean.factory.FactoryBean的工厂类接口，用户可以通过实现该接口定制实例化Bean的逻辑。FactoryBean接口对于Spring框架来说占用重要的地位，Spring自身就提供了70多个FactoryBean的实现。它们隐藏了实例化一些复杂Bean的细节，给上层应用带来了便利。从Spring3.0开始，FactoryBean开始支持泛型，即接口声明改为FactoryBean<T>的形式
以Bean结尾，表示它是一个Bean，不同于普通Bean的是：它是实现了FactoryBean<T>接口的Bean，FactoryBean是一个接口，当在IOC容器中的Bean实现了FactoryBean后，通过getBean(String BeanName)获取到的Bean对象并不是FactoryBean的实现类对象，而是这个实现类中的getObject()方法返回的对象。要想获取FactoryBean的实现类，就要getBean(&BeanName)，在BeanName之前加上&。
在该接口中还定义了以下3个方法：

    T getObject()：返回由FactoryBean创建的Bean实例，如果isSingleton()返回true，则该实例会放到Spring容器中单实例缓存池中；
    boolean isSingleton()：返回由FactoryBean创建的Bean实例的作用域是singleton还是prototype；
    Class<T> getObjectType()：返回FactoryBean创建的Bean类型。

N.参考

(1)[Spring 官网](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)

(2)[Spring Initializer](https://start.spring.io/)

(3)[详解IOC](https://www.cnblogs.com/xb1223/p/10148503.html)

(4)[详解AOP](https://www.cnblogs.com/xb1223/p/10169220.html)

(5)[面试题思考：解释一下什么叫AOP](https://www.cnblogs.com/songanwei/p/9417343.html)

(6)[Spring-bean的循环依赖以及解决方式](https://blog.csdn.net/u010853261/article/details/77940767)

(7)[【242期】面试官：Spring AOP有哪些通知类型，它们的执行顺序是怎样的？](https://mp.weixin.qq.com/s/dOTtB8zF4Id6NhXZ8jS2xw)

(8)[【65期】Spring的IOC是啥?有什么好处?](https://mp.weixin.qq.com/s/KtuajQSAIRDYJ4axVX9gTw)

(9)[【270期】面试官：Spring的Bean实例化过程应该是怎样的？](https://mp.weixin.qq.com/s/n3kMn0C_AhMjjrhleLkSpg)

# Spring MVC

1.SpringMVC执行原理

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
    
在资源路径下创建springmvc的配置文件springmvc.xml。

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
    (3)HandlerExecution：具体的handler(处理)，将解析后的url对应的处理传递给DispatcherServlet。
    (4)HandlerAdapter：处理器适配器，将DispatcherServlet传递的信息去执行相应的controller。
    (5)Controller层中调用service层，获得数据放在ModelAndView对象中，并给ModelAndView设置页面信息。
    (6)HandlerAdapter将视图名传递给DispatcherServlet。
    (7)DispatcherServlet调用视图解析器来解析HandlerAdapter传递的视图名。
    (8)视图解析器将解析的视图名传给DispatcherServlet。
    (9)DispatcherServlet根据视图解析器返回的视图名调用具体的视图。
    (10)用户获得视图。

2.Controller是否是单例的？

默认是单例的，即@Scope(value = "singleton")
尽量不要在controller里面去定义属性，如果在特殊情况需要定义属性的时候，那么就在类上面加上注解@Scope("prototype")改为多例的模式。多线程访问是共用类里面的属性值的，肯定是不安全的。但是springmvc是基于方法的开发，都是用形参接收值，一个方法结束参数就销毁了，多线程访问每个线程都会有一块内存空间产生，里面的参数也是不会共用的，所有springmvc默认使用了单例。
只要controller中不定义属性，那么单例完全是安全的。springmvc这样设计主要的原因也是为了提高程序的性能和以后程序的维护只针对业务的维护就行。

# Spring Boot

1.Spring Boot特征

(1)创建独立的Spring应用。
(2)嵌入式Tomcat、 Jetty、 Undertow容器（无需部署war文件）。
(3)提供的starters简化构建配置。
(4)尽可能自动配置spring应用。
(5)提供生产指标,例如指标、健壮检查和外部化配置。
(6)完全没有代码生成和XML配置要求。

2.Spring Boot自动装配过程

SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入容器，自动配置类就生效，帮我们进行自动配置工作。
以前我们需要自己配置的东西，自动配置类都帮我们解决了。整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中。它将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中。
它会给容器中导入非常多的自动配置类(xxxAutoConfiguration），就是给容器中导入这个场景需要的所有组件，并配置好这些组件。有了自动配置类，免去了我们手动编写配置注入功能组件等的工作。
不是所有存在于spring,factories中的配置都进行加载，而是通过@ConditionalOnClass注解进行判断条件是否成立（只要导入相应的stater，条件就能成立），如果条件成立则加载配置类，否则不加载该配置类。

3.spring-boot的启动方式

    java -jar emample.jar --server.port=8081
    
4.常用的starter

    spring-boot-starter-data-jpa
    spring-boot-starter-security
    spring-boot-starter-test
    spring-boot-starter-web
    spring-boot-starter-thymeleaf
    
5.约定大于配置

说的具体点就是配置文件（.yml）应该放在哪个目录下，配置文件的命名规范，项目启动时扫描的Bean，组件的默认配置是什么样的（比如SpringMVC的视图解析器）等等这一系列的东西，都可以被称为约定。
spring的配置文件目录可以放在，这四个路径从上到下存在优先级关系。

    /config
    /(根目录)
    resource/config/
    resource/

SpringBoot默认配置文件的约定，可以加载以下三种配置文件，建议使用前两种作为项目的配置文件。

    application.yml
    application.yaml
    application.properties

项目启动时扫描包范围的约定：SpringBoot的注解扫描的默认规则是SpringBoot的入口类所在包及其子包。

application.yml配置是如何配置到一个个的配置类中的？
在application.yml中配置的东西，通常是一些存在与自动配置类中的属性。只要存在与spring.factories中的，我们都可以在application.yml中进行配置。
当然，这并不意味着不存在其中的我们就不能配置，这些配置类我们是可以进行自定义的，只要我们写了配置类，我们就可以在yml中配置我们需要的属性值，然后在配置类中直接读取这个配置文件，将其映射到配置类的属性上。
@ConfigurationProperties(prefix = "object")，标记在类上，该注解可以将yml文件中写好的值注入到我们类的属性中。

6.Spring Boot的启动原理
  
通过@SpringBootApplication注解启动初始化模块，加载基本的环境变量、资源、构造器等，配置信息等；
根据文件中配置的Jar包去扫描并加载项目所依赖的Jar包；
@SpringBootApplication注解包含@ComponentScan注解，可以进行组件扫描，把扫描到的Bean注入到注入Spring Context中，完成SpringBoot的启动。

# Spring Cloud

1.SpringCloud与Dubbo

没有好坏，只有适合不适合。

(1)dubbo的优势：
单一应用架构，当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的 数据访问框架（ORM）是关键。
垂直应用架构，当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架（MVC）是关键。
分布式服务架构，当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架（RPC）是关键。
流动计算架构，当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心（SOA）是关键。

(2)SpringCloud优势：

约定优于配置。
开箱即用、快速启动。
适用于各种环境。
轻量级的组件。
组件支持丰富，功能齐全。

(3)两者相比较：
dubbo由于是二进制的传输，占用带宽会更少。springCloud是http协议传输，带宽会比较多，同时使用http协议一般会使用JSON报文，消耗会更大。
dubbo的开发难度较大，原因是dubbo的jar包依赖问题很多大型工程无法解决。SpringCloud对第三方的继承可以一键式生成，天然集成。
springcloud的接口协议约定比较自由且松散，需要有强有力的行政措施来限制接口无序升级。
dubbo的注册中心可以选择zk,redis等多种，springcloud的注册中心只能用eureka或者自研。
严格来说，这两种方式各有优劣。虽然在一定程度上来说，SpringCloud牺牲了服务调用的性能，但也避免了上面提到的原生RPC带来的问题。而且REST相比RPC更为灵活，服务提供方和调用方的依赖只依靠一纸契约，不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更为合适。

2.怎么解决Eureka某一个服务挂掉的问题

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

3.什么是Spring Cloud

Spring Cloud是一系列框架的有序集合。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、智能路由、消息总线、负载均衡、断路器、数据监控等，都可以用Spring Boot的开发风格做到一键启动和部署。
Spring Cloud并没有重复制造轮子，它只是将各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。
设计目标是协调各个微服务，简化分布式系统开发。

优点：
产出于Spring大家族，Spring在企业级开发框架中无人能敌，来头很大，可以保证后续的更新、完善。
组件丰富，功能齐全。Spring Cloud 为微服务架构提供了非常完整的支持。例如、配置管理、服务发现、断路器、微服务网关等；
Spring Cloud社区活跃度很高，教程很丰富，遇到问题很容易找到解决方案。
服务拆分粒度更细，耦合度比较低，有利于资源重复利用，有利于提高开发效率。
可以更精准的制定优化服务方案，提高系统的可维护性。
减轻团队的成本，可以并行开发，不用关注其他人怎么开发，先关注自己的开发。
微服务可以是跨平台的，可以用任何一种语言开发。
适于互联网时代，产品迭代周期更短。

4.Spring Cloud主要项目

Spring Cloud的子项目，大致可分成两类，一类是对现有成熟框架"Spring Boot化"的封装和抽象，也是数量最多的项目；
第二类是开发了一部分分布式系统的基础设施的实现，如Spring Cloud Stream扮演的就是kafka, ActiveMQ这样的角色。

    Spring Cloud
        --服务注册与发现
            --Eureka：服务治理组件，包括服务端的注册中心和客户端的服务发现机制。
            --Spring Cloud Consul：基于Hashicorp Consul的服务治理组件。
            --Spring Cloud Zookeeper：基于Apache Zookeeper的服务治理组件。
        --客户端负载均衡
            --Ribbon：负载均衡的服务调用组件，具有多种负载均衡调用策略。
        --声明式服务调用
            --Feign：基于Ribbon和Hystrix的声明式服务调用组件。
            --Spring Cloud OpenFeign：基于Ribbon和Hystrix的声明式服务调用组件，可以动态创建基于Spring MVC注解的接口实现用于服务调用，在Spring Cloud 2.0中已经取代Feign成为了一等公民。
        --服务容错保护
            --Hystrix：服务容错组件，实现了断路器模式，为依赖服务的出错和延迟提供了容错能力。旨在隔离远程系统，服务和第三方库的访问点，当出现故障是不可避免的故障时，停止级联故障并在复杂的分布式系统中实现弹性。
        --API网关
            --Zuul：API网关组件，对请求提供路由及过滤功能。
            --Spring Cloud Gateway：是Spring Cloud官方推出的第二代网关框架，取代Zuul网关。网关作为流量的，在微服务系统中有着非常作用，网关常见的功能有路由转发、权限校验、限流控制等作用。
        --容器化
            --Docker
            --Kubernates
        --服务链路追踪
            --Spring Cloud Sleuth：Spring Cloud应用程序的分布式请求链路跟踪，支持使用Zipkin、HTrace和基于日志（例如ELK）的跟踪。
        --认证与授权
            --Spring Cloud Security：安全工具包，对Zuul代理中的负载均衡OAuth2客户端及登录认证进行支持。
            --Oauth2.0
        --消息
            --Spring Cloud Bus：用于传播集群状态变化的消息总线，使用轻量级消息代理链接分布式系统中的节点，可以用来动态刷新集群中的服务配置。这是通过将所有微服务连接到单个消息代理来实现的。无论何时刷新实例，此事件都会订阅到侦听此代理的所有微服务，并且它们也会刷新。可以通过使用端点/总线/刷新来实现对任何单个实例的刷新。
            --Spring Cloud Stream：轻量级事件驱动微服务框架，可以使用简单的声明式模型来发送及接收消息，主要实现为Apache Kafka及RabbitMQ。
        --分布式配置中心
            --Spring Cloud Config：集中配置管理工具，分布式系统中统一的外部配置管理，默认使用Git来存储配置，可以支持客户端配置的刷新及加密、解密操作。
        --命令行工具
            --Spring Cloud CLI
        --其他
            --Spring Cloud Task：用于快速构建短暂、有限数据处理任务的微服务框架，用于向应用中添加功能性和非功能性的特性。

5.Hystrix

在分布式环境中，不可避免地会出现某些依赖的服务发生故障的情况。Hystrix是这样的一个库，它通过添加容许时延和容错逻辑来帮助你控制这些分布式服务之间的交互。
Hystrix通过隔离服务之间的访问点，阻止跨服务的级联故障，并提供了退路选项，所有这些都可以提高系统的整体弹性。

(1)Hystrix的设计目的

    通过第三方客户端的库来为访问依赖服务时的潜在故障提供保护和控制；
    防止在复杂分布式系统中出现级联故障；
    快速失败和迅速恢复；
    在允许的情况下，提供退路对服务进行优雅降级；
    提供近实时的监控、报警和操作控制；

(2)使用Hystrix

(a)在pom.xml文件中引入Hystrix依赖;

(b)在启动类中又增加了@EnableCircuitBreaker注解，用来开启断路器功能。
如果你觉得启动类上的注解个数有点多的话，可以使用一个@SpringCloudApplication注解来代替@SpringBootApplication（或者@EnableEurekaServer）、@EnableDiscoveryClient、@EnableCircuitBreaker这三个注解。 
分别是SpringBoot注解、注册服务中心Eureka注解、断路器注解。对于SpringCloud来说，这是每一微服务必须应有的三个注解，所以才推出了@SpringCloudApplication这一注解集合。

(c)修改Controller,为MessageCenterController中的getMsg()接口增加断路器功能。
先启动Eureka，再启动一个8771端口的message-service服务，最后启动message-center。
访问http://localhost:8781/api/v1/center/msg/get ，返回结果表明服务调用成功。
然后停掉message-service服务，再次请求，可以看出fallback中的信息被直接返回了，表明Hystrix断路器调用成功。

    @GetMapping("/msg/get")
    @HystrixCommand(fallbackMethod = "getMsgFallback")
    public Object getMsg() {
        String msg = messageService.getMsg();
        return msg;
    }
    
    public Object getMsgFallback() {
        return "断路器返回";
    }

(3)Feign结合Hystrix

以MessageService的Feign客户端为例，为其添加Hystrix断路器功能。

(a)修改Feign客户端，通过配置@FeignClient注解的fallback属性来为MessageServiceClient指定一个自定义的fallback处理类（MessageServiceFallback）。

    @FeignClient(name = "message-service", fallback = MessageServiceFallback.class)
    public interface MessageServiceClient {
        @GetMapping("/api/v1/msg/get")
        public String getMsg();
    }

(b)创建Fallback处理类，MessageServiceFallback需要实现MessageServiceClient接口，并且在Spring容器中必须存在一个该类型的有效Bean。在这里，我们使用@Component注解将其注入到Spring容器中。

    @Component
    public class MessageServiceFallback implements MessageServiceClient {
        @Override
        public String getMsg() {
            System.out.println("调用消息接口失败，对其进行降级处理！");
            return "消息接口繁忙，请稍后重试！";
        }
    }

(c)修改配置，在新版本的Spring Cloud中，Feign默认关闭了对Hystrix的支持，需要在application.yml进行配置：

    feign:
        hystrix:
            enabled: true

(4)监控Hystrix

Actuator是Spring Boot提供的用来对应用系统进行自省和监控的功能模块，借助于Actuator开发者可以很方便地对应用系统某些监控指标进行查看、统计等。
若要使用Actuator对Hystrix流进行监控，除了需在工程POM文件中引入spring-boot-starter-actuator依赖，还需要在application.yml中添加配置。
另外，还可以启用Hystrix-Dashboard。使用Hystrix一个最大的好处就是它会为我们自动收集每一个HystrixCommand的信息，并利用Hystrix-Dashboard通过一种高效的方式对每一个断路器的健康状态进行展示。

N.参考

(1)[【182期】SpringCloud常见面试题（2020最新版）](https://mp.weixin.qq.com/s/JQLLPAJfR6yoHVKmxWz4Zw)

# Dubbo

1.Dubbo是什么，能做什么

Dubbo是阿里巴巴开源的基于Java的高性能RPC分布式服务框架。其核心部分包含：

(1)远程通讯：提供对多种基于长连接的NIO框架抽象封装，包括多种线程模型，序列化，以及“请求-响应”模式的信息交换方式。透明化的远程方法调用，就像调用本地方法一样调用远程方法，只需简单配置，没有任何API侵入。
(2)集群容错：提供基于接口方法的透明远程过程调用，包括多协议支持，以及软负载均衡，失败容错，地址路由，动态配置等集群支持。软负载均衡及容错机制，可在内网替代F5等硬件负载均衡器，降低成本，减少单点。
(3)自动发现：基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，使服务提供方可以平滑增加或减少机器。服务自动注册与发现，不再需要写死服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。

2.Dubbo内置了哪几种服务容器？

Spring Container，Jetty Container，Log4j Container

3.Dubbo核心的配置有哪些，什么关系

(1)Provider-side包括：dubbo:protocol, dubbo:provider, dubbo:service。
(2)Application-shared包括：dubbo:application, dubbo:registry, dubbo:monitor。
(3)Comsumer-side包括：dubbo:consummer, dubbo:reference。
(4)Sub-config包括：dubbo:method与service和reference关联，dubbo:argument与method关联。

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

7.注册中心全部宕掉后，Dubbo服务还能进行调用吗？

答案是可以的，启动dubbo时，消费者会从注册中心拉取注册的生产者的接口等数据，缓存到本地。每次调用时，按照本地存储的地址进行调用。
注册中心对等集群，任意一台宕掉后，将自动切换到另一台。注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯。
这里主要受益于Dubbo架构的健壮性：

(1)监控中心宕掉不影响使用，只是丢失部分采样数据。
(2)注册中心宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务。
(3)服务提供者无状态，任意一台宕掉后，不影响使用。服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复。

8.Dubbo的spi的约定

(1)dubbo的spi文件存放在哪里？

生成的dubbo的jar包下的dubbo-2.5.3.jar\META-INF\dubbo\internal目录下的文件全部是spi的扩展机制。

(2)dubbo的SPI有哪些约定

spi文件存储路径在META-INF\dubbo\internal目录下，并且文件名为接口的全路径名，就是=接口的包名+接口名，例如：dubbo-2.5.3.jar\META-INF\dubbo\internal\com.alibaba.dubbo.rpc.Protocol。
每个spi文件里面的格式定义为：扩展名=具体的类名，例如registries=com.alibaba.dubbo.registry.RegistriesPageHandlerpages。

(3)如何扩展dubbo的spi

比如自己要扩展dubbo的Protocol，要经过以下步骤:

(a)新建一个工程，在src/main/resources/META-INF/dubbo目录下新建一个文件比如org.apache.dubbo.rpc.Protocol，里面内容写一个：xxxProtocal=com.abc.XxxProtocol
(b)然后在这个工程里新建一个XxxProtocol类实现Protocol接口,并实现相关方法。

    package com.xxx;
    import org.apache.dubbo.rpc.Protocol;
    public class XxxProtocol implements Protocol {
        public <T> Exporter<T> export(Invoker<T> invoker) throws RpcException {
            return new XxxExporter(invoker);
        }
        public <T> Invoker<T> refer(Class<T> type, URL url) throws RpcException {
            return new XxxInvoker(type, url);
        }
    }

    package com.xxx;
    import org.apache.dubbo.rpc.support.AbstractExporter;
    public class XxxExporter<T> extends AbstractExporter<T> {
        public XxxExporter(Invoker<T> invoker) throws RemotingException{
            super(invoker);
            // ...
        }
        public void unexport() {
            super.unexport();
            // ...
        }
    }
    
    package com.xxx;
    import org.apache.dubbo.rpc.support.AbstractInvoker;
    public class XxxInvoker<T> extends AbstractInvoker<T> {
        public XxxInvoker(Class<T> type, URL url) throws RemotingException{
            super(type, url);
        }
        protected abstract Object doInvoke(Invocation invocation) throws Throwable {
            // ...
        }
    }

(c)可以把这个工程打包成jar包，然后在自己的dubbo provider工程里依赖刚才的那个spi的jar包，并在spring配置文件中配置如下：

    <dubbo:protocol name=”XxxProtocol” port=”20000” />

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

(1)Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。

(2)Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。

(3)Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

3.#{}与${}差别

差别是#是预编译处理，$是字符串替换。
(1)#将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。$将传入的数据直接显示生成在sql中。

(2)#方式在很大程度上能够防止sql注入。$方式无法防止sql注入。一般能用#的就别用$。$方式一般用于传入数据库对象，例如传入表名。

(3)#在预处理时，会把#{}用一个占位符?代替，调用PreparedStatement的set方法来赋值，按序给sql的?号占位符设置参数值，比如ps.setInt(0, parameterValue)。$只会做简单的字符串替换，在动态SQL解析阶段将会进行变量替换。${}是Properties文件中的变量占位符，它可以用于标签属性值和sql内部，属于静态文本替换。

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
不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复，毕竟namespace不是必须的，只是最佳实践而已。
原因就是namespace+id是作为Map<String, MapperStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

7.分页查询

(1)sql的limit语法，直接书写带有物理分页的参数来完成物理分页功能。

(2)使用Mybatis的RowBounds(int offset, int limit)对象进行分页，不适合数据量大。它是针对ResultSet结果集执行的内存分页，而非物理分页。

(3)自己实现拦截器，对执行的sql语句加limit条件。分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。举例：select * from student，拦截sql后重写为：select t.* from （select * from student）t limit 0，10

8.如何执行批量插入

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
    
Mybatis执行批量插入，能返回数据库主键列表吗？能，JDBC都能，Mybatis当然也能。

9.在mapper中如何传递多个参数

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
其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

11.Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？

<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>，加上动态sql的9个标签trim|where|set|foreach|if|choose|when|otherwise|bind等。
其中为sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增的主键生成策略标签。

12.一对一、一对多的关联查询

(1)MyBatis实现一对一有几种方式，具体怎么操作的？

有联合查询和嵌套查询。
联合查询是几个表联合查询，只查询一次,，通过在resultMap里面配置association节点配置一对一的类就可以完成。
嵌套查询是先查一个表，根据这个表里面的结果的外键id，去再另外一个表里面查询数据,也是通过association配置，但另外一个表的查询通过select属性配置。

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
    </mapper> 

(2)MyBatis实现一对多有几种方式，怎么操作的？

有联合查询和嵌套查询。
联合查询是几个表联合查询，只查询一次，通过在resultMap里面的collection节点配置一对多的类就可以完成。
嵌套查询是先查一个表，根据这个表里面的结果的外键id，去再另外一个表里面查询数据，也是通过配置collection,但另外一个表的查询通过select节点配置。

    <mapper namespace="com.lcb.mapping.userMapper">    
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

Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件。
Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
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

20.Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

第一种是使用<resultMap>标签，逐一定义列名和对象属性名之间的映射关系。
第二种是使用sql列的别名功能，将列别名书写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成T_NAME AS NaMe，Mybatis一样可以正常工作。
有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

21.Mybatis都有哪些Executor执行器？它们之间的区别是什么？

Mybatis有三种基本的Executor执行器，SimpleExecutor、ReuseExecutor、BatchExecutor。作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。
   
    SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。
    ReuseExecutor：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。
    BatchExecutor：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

在Mybatis配置文件中，可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数。例如：

    SqlSession sqlSession = sqlSessionFactory.openSession(Executortype.Batch);
    
22.Mybatis是否可以映射Enum枚举类？

Mybatis可以映射枚举类，不单可以映射枚举类，Mybatis可以映射任何对象到表的一列上。
映射方式为自定义一个TypeHandler，实现TypeHandler的setParameter()和getResult()接口方法。
TypeHandler有两个作用，一是完成从javaType至jdbcType的转换，二是完成jdbcType至javaType的转换，体现为setParameter()和getResult()两个方法，分别代表设置sql问号占位符参数和获取列查询结果。

23.Mybatis映射文件中，如果A标签通过include引用了B标签的内容，请问，B标签能否定义在A标签的后面，还是说必须定义在A标签的前面？

虽然Mybatis解析Xml映射文件是按照顺序解析的，但是，被引用的B标签依然可以定义在任何地方，Mybatis都可以正确识别。
原理是，Mybatis解析A标签，发现A标签引用了B标签，但是B标签尚未解析到，尚不存在，此时，Mybatis会将A标签标记为未解析状态，然后继续解析余下的标签，包含B标签，待所有标签解析完毕，Mybatis会重新解析那些被标记为未解析的标签，此时再解析A标签时，B标签已经存在，A标签也就可以正常解析完成了。

24.简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？

Mybatis将所有Xml配置信息都封装到All-In-One重量级对象Configuration内部。
在Xml映射文件中，<parameterMap>标签会被解析为ParameterMap对象，其每个子元素会被解析为ParameterMapping对象。
<resultMap>标签会被解析为ResultMap对象，其每个子元素会被解析为ResultMapping对象。
每一个<select>、<insert>、<update>、<delete>标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

N.参考

(1)[【250期】关于Mybatis知识点，面试可以问的都在这里了！](https://mp.weixin.qq.com/s/kReDxX3pA_ygyxzeWJDtoQ)

# Netty

1.什么是Netty的零拷贝

“零拷贝”是指计算机操作的过程中，CPU不需要为数据在内存之间的拷贝消耗资源。而它通常是指计算机在网络上发送文件时，不需要将文件内容拷贝到用户空间（User Space）而直接在内核空间（Kernel Space）中传输到网络的方式。
Zero Copy的模式中，避免了数据在用户空间和内存空间之间的拷贝，从而提高了系统的整体性能。Linux中的sendfile()以及Java NIO中的FileChannel.transferTo()方法都实现了零拷贝的功能，而在Netty中也通过在FileRegion中包装了NIO的FileChannel.transferTo()方法实现了零拷贝。

而在Netty中还有另一种形式的零拷贝，即Netty允许我们将多段数据合并为一整段虚拟数据供用户使用，而过程中不需要对数据进行拷贝操作。在实际应用中，很有可能一条完整的消息被分割为多个数据包进行网络传输，而单个的数据包对你而言是没有意义的，只有当这些数据包组成一条完整的消息时你才能做出正确的处理，而Netty可以通过零拷贝的方式将这些数据包组合成一条完整的消息供你来使用。而此时，零拷贝的作用范围仅在用户空间中。
以Netty 3.8.0.Final的源代码来进行说明ChannelBuffer接口，Netty为需要传输的数据制定了统一的ChannelBuffer接口。该接口的主要设计思路如下：
(1)使用getByte(int index)方法来实现随机访问。
(2)使用双指针的方式实现顺序访问。每个Buffer都有一个读指针（readIndex）和写指针（writeIndex），在读取数据时读指针后移，在写入数据时写指针后移。

定义了统一的接口之后，就是来做各种实现了。Netty主要实现了HeapChannelBuffer,ByteBufferBackedChannelBuffer等等，下面我们就来讲讲与Zero Copy直接相关的CompositeChannelBuffer类。CompositeChannelBuffer类的作用是将多个ChannelBuffer组成一个虚拟的ChannelBuffer来进行操作。
为什么说是虚拟的呢，因为CompositeChannelBuffer并没有将多个ChannelBuffer真正的组合起来，而只是保存了他们的引用，这样就避免了数据的拷贝，实现了Zero Copy。
所谓的CompositeChannelBuffer实际上就是将一系列的Buffer通过数组保存起来，然后实现了ChannelBuffer的接口，使得在上层看来，操作这些Buffer就像是操作一个单独的Buffer一样。

# Log4j2

1.Log4j2中RollingFile的文件滚动更新机制

(1)基础

RollingFileAppender是Log4j2中的一种能够实现日志文件滚动更新(rollover)的Appender。
rollover的意思是当满足一定条件(如文件达到了指定的大小，达到了指定的时间)后，就重命名原日志文件进行归档，并生成新的日志文件用于log写入。如果还设置了一定时间内允许归档的日志文件的最大数量，将对过旧的日志文件进行删除操作。
RollingFile实现日志文件滚动更新，依赖于TriggeringPolicy和RolloverStrategy。
其中，TriggeringPolicy为触发策略，其决定了何时触发日志文件的rollover，即When。
RolloverStrategy为滚动更新策略，其决定了当触发了日志文件的rollover时，如何进行文件的rollover，即How。Log4j2提供了默认的rollover策略DefaultRolloverStrategy。

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration status="warn">
      <Appenders>
        <RollingFile name="RollingFile" fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd HH}.log">
          <PatternLayout>
            <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
          </PatternLayout>
          <Policies>
            <TimeBasedTriggeringPolicy interval="1"/>
            <SizeBasedTriggeringPolicy size="250MB"/>
          </Policies>
        </RollingFile>
      </Appenders>
      <Loggers>
        <Root level="error">
          <AppenderRef ref="RollingFile"/>
        </Root>
      </Loggers>
    </Configuration>
    
上述配置文件中配置了一个RollingFile，日志写入logs/app.log文件中，每经过1小时或者当文件大小到达250M时，按照app-2017-08-01 12.log的格式对app.log进行重命名并归档，并生成新的文件用于写入log。
其中，fileName指定日志文件的位置和文件名称（如果文件或文件所在的目录不存在，会创建文件。）
filePattern指定触发rollover时，文件的重命名规则。filePattern中可以指定类似于SimpleDateFormat中的date/time pattern，如yyyy-MM-dd HH，或者%i指定一个整数计数器。

(2)TriggeringPolicy

RollingFile的触发rollover的策略有

    CronTriggeringPolicy(Cron表达式触发)
    OnStartupTriggeringPolicy(JVM启动时触发)
    SizeBasedTriggeringPolicy(基于文件大小)
    TimeBasedTriggeringPolicy(基于时间)
    CompositeTriggeringPolicy(多个触发策略的混合，如同时基于文件大小和时间)
    
其中，SizeBasedTriggeringPolicy(基于日志文件大小)、TimeBasedTriggeringPolicy(基于时间)或同时基于文件大小和时间的混合触发策略最常用。
SizeBasedTriggeringPolicy规定了当日志文件达到了指定的size时，触发rollover操作。size参数可以用KB、MB、GB等做后缀来指定具体的字节数，如20MB。
TimeBasedTriggeringPolicy规定了当日志文件名中的date/time pattern不再符合filePattern中的date/time pattern时，触发rollover操作。比如，filePattern指定文件重命名规则为app-%d{yyyy-MM-dd HH}.log，文件名为app-2017-08-25 11.log，当时间达到2017年8月25日中午12点（2017-08-25 12），将触发rollover操作。
CompositeTriggeringPolicy将多个TriggeringPolicy放到Policies中表示使用复合策略，同时使用了TimeBasedTriggeringPolicy、SizeBasedTriggeringPolicy，有一个条件满足，就会触发rollover。

    <Policies>
        <TimeBasedTriggeringPolicy />
        <SizeBasedTriggeringPolicy size="250 MB"/>
    </Policies>

(3)DefaultRolloverStrategy

DefaultRolloverStrategy指定了当触发rollover时的默认策略。
DefaultRolloverStrategy是Log4j2提供的默认的rollover策略，即使在log4j2.xml中没有显式指明，也相当于为RollingFile配置下添加了如下语句。DefaultRolloverStrategy默认的max为7。

    <DefaultRolloverStrategy max="7"/>
    
max参数指定了计数器的最大值。一旦计数器达到了最大值，过旧的文件将被删除。注意：不要认为max参数是需要保留的日志文件的最大数目。
max参数是与filePattern中的计数器%i配合起作用的，其具体作用方式与filePattern的配置密切相关。
如果filePattern中仅含有date/time pattern，每次rollover时，将用当前的日期和时间替换文件中的日期格式对文件进行重命名。max参数将不起作用。如，filePattern="logs/app-%d{yyyy-MM-dd}.log"。
如果filePattern中仅含有整数计数器（即%i），每次rollover时，文件重命名时的计数器将每次加1（初始值为1），若达到max的值，将删除旧的文件。如，filePattern="logs/app-%i.log"。
如果filePattern中既含有date/time pattern，又含有%i，每次rollover时，计数器将每次加1，若达到max的值，将删除旧的文件，直到data/time pattern不再符合，被替换为当前的日期和时间，计数器再从1开始。如，filePattern="logs/app-%d{yyyy-MM-dd HH-mm}-%i.log"。

(4)DeleteAction

DefaultRolloverStrategy制定了默认的rollover策略，通过max参数可控制一定时间范围内归档的日志文件的最大个数。
Log4j 2.5 引入了DeleteAction，使用户可以自己控制删除哪些文件，而不仅仅是通过DefaultRolloverStrategy的默认策略。
注意：通过DeleteAction可以删除任何文件，而不仅仅像DefaultRolloverStrategy那样，删除最旧的文件，所以使用的时候需要谨慎！

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration status="warn" name="MyApp" packages="">
      <Properties>
        <Property name="baseDir">logs</Property>
      </Properties>
      <Appenders>
        <RollingFile name="RollingFile" fileName="${baseDir}/app.log"
              filePattern="${baseDir}/app-%d{yyyy-MM-dd}.log.gz">
          <PatternLayout pattern="%d %p %c{1.} [%t] %m%n" />
          <CronTriggeringPolicy schedule="0 0 0 * * ?"/>
          <DefaultRolloverStrategy>
            <Delete basePath="${baseDir}" maxDepth="2">
              <IfFileName glob="*/app-*.log.gz" />
              <IfLastModified age="60d" />
            </Delete>
          </DefaultRolloverStrategy>
        </RollingFile>
      </Appenders>
      <Loggers>
        <Root level="error">
          <AppenderRef ref="RollingFile"/>
        </Root>
      </Loggers>
    </Configuration>
    
上述配置文件中，Delete部分便是配置DeleteAction的删除策略，指定了当触发rollover时，删除baseDir文件夹或其子文件下面的文件名符合app-*.log.gz且距离最后的修改日期超过60天的文件。
其中，basePath指定了扫描开始路径，为baseDir文件夹。maxDepth指定了目录扫描深度，为2表示扫描baseDir文件夹及其子文件夹。
IfFileName指定了文件名需满足的条件，IfLastModified指定了文件修改时间需要满足的条件。