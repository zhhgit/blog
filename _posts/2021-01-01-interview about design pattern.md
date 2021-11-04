---
layout: post
title: "后端开发面试题 -- 设计模式篇"
description: 后端开发面试题 -- 设计模式篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

1.设计模式分类

设计模式是软件开发人员在软件开发过程中面临的一般问题的解决方案。这些解决方案是众多软件开发人员经过相当长的一段时间的试验和错误总结出来的。设计模式是代码可用性的延伸。

(1)创建型：工厂、抽象工厂、建造者、单例、原型

(2)结构型：适配器、装饰器、代理、外观、桥接、组合、享元

(3)行为型：策略、观察者、模板方法、迭代器、命令。

2.在JDK中几个常用的设计模式？

    单例模式（Singleton pattern）用于Runtime，Calendar和其他的一些类中。
    工厂模式（Factory pattern）被用于各种不可变的类如Boolean，像Boolean.valueOf。
    观察者模式（Observer pattern）被用于Swing和很多的事件监听中。
    装饰器设计模式（Decorator design pattern）被用于多个Java IO类中。

3.单例模式

单例模式（Singleton），也叫单子模式，是一种常用的软件设计模式。在应用这个模式时，单例对象的类必须保证只有一个实例存在。许多时候整个系统只需要拥有一个全局对象，这样有利于我们协调系统整体的行为。

优点：

在单例模式中，活动的单例只有一个实例，对单例类的所有实例化得到的都是相同的一个实例。这样就防止其它对象对自己的实例化，确保所有的对象都访问一个实例。
单例模式具有一定的伸缩性，类自己来控制实例化进程，类就在改变实例化进程上有相应的伸缩性。
提供了对唯一实例的受控访问。
由于在系统内存中只存在一个对象，因此可以节约系统资源，当需要频繁创建和销毁的对象时单例模式无疑可以提高系统的性能。
避免对共享资源的多重占用。

缺点：

不适用于变化的对象，如果同一类型的对象总是要在不同的用例场景发生变化，单例就会引起数据的错误，不能保存彼此的状态。
由于单例模式中没有抽象层，因此单例类的扩展有很大的困难。
单例类的职责过重，在一定程度上违背了“单一职责原则”。
滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为的单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；如果实例化的对象长时间不被利用，系统会认为是垃圾而被回收，这将导致对象状态的丢失。

使用注意事项：

使用时不能用反射模式创建单例，否则会实例化一个新的对象。
使用懒单例模式时注意线程安全问题。
饿单例模式和懒单例模式构造方法都是私有的，因而是不能被继承的，有些单例模式可以被继承（如登记式模式）。

使用场景：

单例模式只允许创建一个对象，因此节省内存，加快对象访问速度，因此对象需要被公用的场合适合使用，如多个模块使用同一个数据源连接对象等等。如：
需要频繁实例化然后销毁的对象。创建对象时耗时过多或者耗资源过多，但又经常用到的对象。有状态的工具类对象。频繁访问数据库或文件的对象。
资源共享的情况下，避免由于资源操作时导致的性能或损耗等。如上述中的日志文件，应用配置。
控制资源的情况下，方便资源之间的互相通信。如线程池等。

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

5.工厂模式

这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
意图：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。
所谓的工厂方法模式，就是定义一个工厂方法，通过传入的参数，返回某个实例，然后通过该实例来处理后续的业务逻辑。一般的，工厂方法的返回值类型是一个接口类型，而选择具体子类实例的逻辑则封装到了工厂方法中了。通过这种方式，来将外层调用逻辑与具体的子类的获取逻辑进行分离。

    public class PeopleFactory {
    
        public People getPeople(String name){
            if(name.equals("Xiaoming")){
                return new Xiaoming();
            }else if(name.equals("Xiaohong")){
                return new Xiaohong();
            }
            return null;
        }
    }

6.观察者模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知它的依赖对象。观察者模式属于行为型模式。
意图：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

7.装饰器模式

装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
意图：动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。
主要解决：一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。

8.策略模式

策略模式就是一个接口下有多个实现类，而每种实现类会处理某一种情况。
从工厂模式的代码中可以看到工厂模式主要是返回的接口实现类的实例化对象，最后返回的结果是接口实现类中的方法。
而策略模式是在实例化策略模式的时候已经创建好了，我们可以在策略模式中随意的拼接重写方法，而工厂模式是不管方法的拼接这些的，只关注最后的结果不注重过程，而策略模式注重的是过程。

    public class StrategySign {
    
        private People people;
     
        public StrategySign(People people){
            this.people = people;
        }
     
        public StrategySign(String name){
            if(name.equals("Xiaoming")){
                this.people = new Xiaoming();
            }else if(name.equals("Xiaohong")){
                this.people = new Xiaohong();
            }
        }
     
        public void run(){
            people.run();
        }
    }

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

11.Spring中用了哪些设计模式？

    工厂模式：在BeanFactory以及ApplicationContext创建中都有用到；
    代理模式：在Aop实现中用到了JDK的动态代理；
    单例模式：在创建bean的时候，默认单例；
    
12.代理模式

代理模式是java中最常用的设计模式之一，尤其是在spring框架中广泛应用。
对于java的代理模式，一般可分为：静态代理、动态代理、以及CGLIB实现动态代理。

(1)静态代理：静态代理需要针对被代理的方法提前写好代理类，如果被代理的方法非常多则需要编写很多代码，因此，对于上述缺点，通过动态代理的方式进行了弥补。

(2)JDK动态代理：动态代理主要是通过反射机制，在运行时动态生成所需代理的class。

    public class DynamicProxyTest {
    
        public static void main(String[] args) {
            Target target = new TargetImpl();
            DynamicProxyHandler handler = new DynamicProxyHandler(target);
            Target proxySubject = (Target) Proxy.newProxyInstance(TargetImpl.class.getClassLoader(),TargetImpl.class.getInterfaces(),handler);
            String result = proxySubject.execute();
            System.out.println(result);
        }
    
    }
    
    public class DynamicProxyHandler implements InvocationHandler{
    
        private Target target;
    
        public DynamicProxyHandler(Target target) {
            this.target = target;
        }
    
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("========before==========");
            Object result = method.invoke(target,args);
            System.out.println("========after===========");
            return result;
        }
    }

(3)CGLib代理：CGLib采用了非常底层的字节码技术，其原理是通过字节码技术为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑。
JDK动态代理与CGLib动态代理均是实现Spring AOP的基础。

    public class MyMethodInterceptor implements MethodInterceptor{
    
        @Override
        public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
            System.out.println(">>>>MethodInterceptor start...");
            Object result = proxy.invokeSuper(obj,args);
            System.out.println(">>>>MethodInterceptor ending...");s
            return "result";
        }
    }
    
    public class CglibTest {
    
        public static void main(String[] args) {
            System.out.println("***************");
            Target target = new Target();
            CglibTest test = new CglibTest();
            Target proxyTarget = (Target) test.createProxy(Target.class);
            String res = proxyTarget.execute();
            System.out.println(res);
        }
    
        public Object createProxy(Class targetClass) {
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(targetClass);
            enhancer.setCallback(new MyMethodInterceptor());
            return enhancer.create();
        }
    }

N.参考

(1)[单例模式有几种写法？](https://mp.weixin.qq.com/s/oltq10YKd_6pm4saqkOthA)