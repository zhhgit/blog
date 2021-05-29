---
layout: post
title: "后端开发面试题 -- JVM篇"
description: 后端开发面试题 -- JVM篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# 类加载器

1.类加载过程

加载：将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内，然后在内存上创建一个java.lang.Class对象用来封装类在方法区内的数据结构作为这个类的各种数据的访问入口。
验证：主要是为了确保class文件中的字节流包含的信息是否符合当前JVM的要求，且不会危害JVM自身安全，比如校验文件格式、是否是cafe baby魔术、字节码验证等等。
准备：为类变量分配内存并设置类变量（是被static修饰的变量，变量不是常量，所以不是final的，就是static的）初始值的阶段。这些变量所使用的内存在方法区中进行分配。比如private static int age = 26;类变量age会在准备阶段过后为其分配四个（int四个字节）字节的空间，并且设置初始值为0，而不是26。若是final的，则在编译期就会设置上最终值。
解析：JVM会在此阶段把类的二进制数据中的符号引用替换为直接引用。
初始化：初始化阶段是执行类构造器<clinit>()方法的过程，到了初始化阶段，才真正开始执行类定义的Java程序代码（或者说字节码 ）。比如准备阶段的那个age初始值是0，到这一步就设置为26。
使用：对象都出来了，业务系统直接调用阶段。
卸载：用完了，可以被GC回收了。

2.类加载器种类以及加载范围

启动类加载器（Bootstrap ClassLoader）：最顶层类加载器，他的父类加载器是个null，也就是没有父类加载器。负责加载jvm的核心类库，比如java.lang.*等，从系统属性中的sun.boot.class.path所指定的目录中加载类库。他的具体实现由Java虚拟机底层C++代码实现。
扩展类加载器（Extension ClassLoader）：父类加载器是Bootstrap ClassLoader。从java.ext.dirs系统属性所指定的目录中加载类库，或者从JDK的安装目录的jre/lib/ext子目录（扩展目录）下加载类库，如果把用户的jar文件放在这个目录下，也会自动由扩展类加载器加载。继承自java.lang.ClassLoader。
应用程序类加载器（Application ClassLoader）：父类加载器是Extension ClassLoader。从环境变量classpath或者系统属性java.class.path所指定的目录中加载类。继承自java.lang.ClassLoader。
自定义类加载器（User ClassLoader）：除了上面三个自带的以外，用户还能制定自己的类加载器，但是所有自定义的类加载器都应该继承自java.lang.ClassLoader。比如热部署、tomcat都会用到自定义类加载器。

3.双亲委派

如果一个类加载器收到了类加载的请求，它首先会从自己缓存里查找是否之前加载过这个class，加载过直接返回，没加载过的话它不会自己亲自去加载，它会把这个请求委派给父类加载器去完成，每一层都是如此，类似递归，一直递归到顶层父类。
也就是Bootstrap ClassLoader，只要加载完成就会返回结果，如果顶层父类加载器无法加载此class，则会返回去交给子类加载器去尝试加载，若最底层的子类加载器也没找到，则会抛出ClassNotFoundException。
源码在java.lang.ClassLoader#loadClass(java.lang.String, boolean)

4、为啥要有双亲委派
防止内存中出现多份同样的字节码，安全。

比如自己重写个java.lang.Object并放到Classpath中，没有双亲委派的话直接自己执行了，那不安全。双亲委派可以保证这个类只能被顶层Bootstrap Classloader类加载器加载，从而确保只有JVM中有且仅有一份正常的java核心类。如果有多个的话，那么就乱套了。比如相同的类instance of可能返回false，因为可能父类不是同一个类加载器加载的Object。

5、为什么需要破坏双亲委派模型
Jdbc

Jdbc为什么要破坏双亲委派模型？

以前的用法是未破坏双亲委派模型的，比如Class.forName("com.mysql.cj.jdbc.Driver");

而在JDBC4.0以后，开始支持使用spi的方式来注册这个Driver，具体做法就是在mysql的jar包中的META-INF/services/java.sql.Driver文件中指明当前使用的Driver是哪个，然后使用的时候就不需要我们手动的去加载驱动了，我们只需要直接获取连接就可以了。Connection con = DriverManager.getConnection(url, username, password );

首先，理解一下为什么JDBC需要破坏双亲委派模式，原因是原生的JDBC中Driver驱动本身只是一个接口，并没有具体的实现，具体的实现是由不同数据库类型去实现的。例如，MySQL的mysql-connector-*.jar中的Driver类具体实现的。

原生的JDBC中的类是放在rt.jar包的，是由Bootstrap加载器进行类加载的，在JDBC中的Driver类中需要动态去加载不同数据库类型的Driver类，而mysql-connector-*.jar中的Driver类是用户自己写的代码，那Bootstrap类加载器肯定是不能进行加载的，既然是自己编写的代码，那就需要由Application类加载器去进行类加载。

这个时候就引入线程上下文件类加载器(Thread Context ClassLoader)，通过这个东西程序就可以把原本需要由Bootstrap类加载器进行加载的类由Application类加载器去进行加载了。

Tomcat

Tomcat为什么要破坏双亲委派模型？

因为一个Tomcat可以部署N个web应用，但是每个web应用都有自己的classloader，互不干扰。比如web1里面有com.test.A.class，web2里面也有com.test.A.class，如果没打破双亲委派模型的话，那么web1加载完后，web2在加载的话会冲突。

因为只有一套classloader，却出现了两个重复的类路径，所以tomcat打破了，他是线程级别的，不同web应用是不同的classloader。

Java spi 方式，比如jdbc4.0开始就是其中之一。

热部署的场景会破坏，否则实现不了热部署。

6、如何破坏双亲委派模型
重写loadClass方法，别重写findClass方法，因为loadClass是核心入口，将其重写成自定义逻辑即可破坏双亲委派模型。

7、如何自定义一个类加载器
只需要继承java.lang.Classloader类，然后覆盖他的findClass(String name)方法即可，该方法根据参数指定的类名称，返回对应 的Class对象的引用。

8、热部署原理
采取破坏双亲委派模型的手段来实现热部署，默认的loadClass()方法先找缓存，你改了class字节码也不会热加载，所以自定义ClassLoader，去掉找缓存那部分，直接就去加载，也就是每次都重新加载。

9、常见笔试题
问题：输出结果是什么？

答案：编译报错。

原因：因为静态语句块中只能访问定义在静态语句块之前的变量，定义在他之后的 变量在前面的静态语句块中可以赋值，但是不能访问。

/**
 * Description: 编译报错
 *
 * @author TongWei.Chen 2021-01-08 17:37:44
 */
public class Test1 {
    static {
        // 编译没报错
        i = 2;
        // 编译报错Illegal forward reference
        System.out.println(i);
    }
    private static int i =1;
}
问题：输出结果是什么？

答案 ：1、3

原因：因为类加载过程中会先准备类变量（也就是静态变量），准备阶段是赋初始值阶段，也就是test2=null，value1=0，value2=0，然后进入初始化阶段的时候test2=new Test2()，会执行构造器，结果是value1 = 1，value2 = 4，然后执行value1和value2这两句，value1没变化，value2被重新赋值成了3，所以结果1和3。

public class Test2 {
    private static Test2 test2 = new Test2();
    private static int value1;
    private static int value2 = 3;

    private Test2() {
        value1 ++;
        value2 ++;
    }

    public static void main(String[] args) {
        // 1
        System.out.println(test2.value1);
        // 3
        System.out.println(test2.value2);
    }
}
那如果把private static Test2 test2 = new Test2();放到private static int value2 = 3;下面的话结果就是1和4了。

public class Test3 {
    private static int value1;
    private static int value2 = 3;
    private static Test3 test3 = new Test3();
    
    private Test3() {
        value1 ++;
        value2 ++;
    }
    
    public static void main(String[] args) {
        // 1
        System.out.println(test3.value1);
        // 4
        System.out.println(test3.value2);
    }
}










# JVM内存区域

1.JVM 整体组成可分为以下四个部分：类加载器（ClassLoader），运行时数据区（Runtime Data Area），执行引擎（Execution Engine），本地库接口（Native Interface）。
程序在执行之前先要把java代码转换成字节码（class文件），jvm首先需要把字节码通过一定的方式，类加载器（ClassLoader）把文件加载到内存中（运行时数据区（Runtime Data Area）） ，而字节码文件是jvm的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器（执行引擎（Execution Engine））将字节码翻译成底层系统指令再交由CPU去执行，而这个过程中需要调用其他语言的接口（本地库接口（Native Interface））来实现整个程序的功能，这就是这4个主要组成部分的职责与功能。

2.运行时数据区组成：程序计数器（Program Counter Register）、Java虚拟机栈（Java Virtual Machine Stacks）、本地方法栈（Native Method Stack）、Java堆（Java Heap）、方法区（Methed Area）

(1)Java程序计数器是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里，字节码解析器的工作是通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。
内存私有。由于jvm的多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，也就是任何时刻，一个处理器（或者说一个内核）都只会执行一条线程中的指令。因此为了线程切换后能恢复到正确的执行位置，每个线程都有独立的程序计数器。
如果线程正在执行Java中的方法，程序计数器记录的就是正在执行虚拟机字节码指令的地址，如果是Native方法，这个计数器就为空（undefined），因此该内存区域是唯一一个在Java虚拟机规范中没有规定OutOfMemoryError的区域。
  
(2)Java虚拟机栈描述的是Java方法执行的内存模型，每个方法在执行的同时都会创建一个栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息，每个方法从调用直至执行完成的过程，都对应着一个线帧在虚拟机栈中入栈到出栈的过程。
内存私有，它的生命周期和线程相同。异常规定：StackOverflowError、OutOfMemoryError。如果线程请求的栈深度大于虚拟机所允许的栈深度就会抛出StackOverflowError异常。如果虚拟机是可以动态扩展的，如果扩展时无法申请到足够的内存就会抛出OutOfMemoryError异常。
  
(3)本地方法栈与虚拟机栈的作用是一样的，只不过虚拟机栈是服务Java方法的，而本地方法栈是为虚拟机调用Native方法服务的。在Java虚拟机规范中对于本地方法栈没有特殊的要求，虚拟机可以自由的实现它，因此在Sun HotSpot虚拟机直接把本地方法栈和虚拟机栈合二为一了。特性和异常同虚拟机栈。
  
(4)Java堆是Java虚拟机中内存最大的一块，是被所有线程共享的，在虚拟机启动时候创建，Java堆唯一的目的就是存放对象实例，几乎所有的对象实例都在这里分配内存，随着JIT编译器的发展和逃逸分析技术的逐渐成熟，栈上分配、标量替换优化的技术将会导致一些微妙的变化，所有的对象都分配在堆上渐渐变得不那么“绝对”了。
特性：内存共享。异常规定：OutOfMemoryError。如果在堆中没有内存完成实例分配，并且堆不可以再扩展时，将会抛出OutOfMemoryError。
Java虚拟机规范规定，Java堆可以处在物理上不连续的内存空间中，只要逻辑上连续即可，就像我们的磁盘空间一样。在实现上也可以是固定大小的，也可以是可扩展的，不过当前主流的虚拟机都是可扩展的，通过-Xmx和-Xms控制。

(5)方法区用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。
误区：方法区不等于永生代。很多人原因把方法区称作“永久代”（Permanent Generation），本质上两者并不等价，只是HotSpot虚拟机垃圾回收器团队把GC分代收集扩展到了方法区，或者说是用来永久代来实现方法区而已，这样能省去专门为方法区编写内存管理的代码，但是在Jdk8也移除了“永久代”，使用Native Memory来实现方法区。
特性：内存共享。异常规定：OutOfMemoryError。当方法无法满足内存分配需求时会抛出OutOfMemoryError异常。
运行时常量池是方法区的一部分，Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池（Constant Pool Table）用于存放编译期生成的各种字面量和符号引用，这部分在类加载后进入方法区的运行时常量池中，如String类的intern()方法。

3.栈和堆的区别

数据结构中的栈和堆：栈（FILO），堆是一种完全二叉树或者近似完全二叉树。

系统中的栈和堆：
(1)栈：由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
堆：是一个可动态申请的内存空间(其记录空闲内存空间的链表由操作系统维护)，在java中,所有使用new xxx()构造出来的对象都在堆中存储一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收 。注意它与数据结构中的堆是两回事，分配方式倒是类似于链表。

(2)申请响应
栈：只要栈的剩余空间大于所申请空间，系统将为程序提供内存，否则将报异常提示栈溢出。
堆：首先应该知道操作系统有一个记录空闲内存地址的链表，当系统收到程序的申请时，会遍历该链表，寻找第一个空间大于所申请空间的堆结点，然后将该结点从空闲结点链表中删除，并将该结点的空间分配给程序，另外，对于大多数系统，会在这块内存空间中的首地址处记录本次分配的大小，这样，代码中的delete语句才能正确的释放本内存空间。另外，由于找到的堆结点的大小不一定正好等于申请的大小，系统会自动的将多余的那部分重新放入空闲链表中。

(3)申请限制
栈：在Windows下,栈是向低地址扩展的数据结构，是一块连续的内存的区域。这句话的意思是栈顶的地址和栈的最大容量是系统预先规定好的，在 WINDOWS下，栈的大小是2M（也有的说是1M，总之是一个编译时就确定的常数），如果申请的空间超过栈的剩余空间时，将提示overflow。因此，能从栈获得的空间较小。
堆：堆是向高地址扩展的数据结构，是不连续的内存区域。这是由于系统是用链表来存储的空闲内存地址的，自然是不连续的，而链表的遍历方向是由低地址向高地址。堆的大小受限于计算机系统中有效的虚拟内存。由此可见，堆获得的空间比较灵活，也比较大。

(4)堆栈缓存方式
栈使用的是一级缓存， 它们通常都是被调用时处于存储空间中，调用完毕立即释放。
堆则是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定（并不是一旦成为孤儿对象就能被回收）。所以调用这些对象的速度要相对来得低一些。

4.JVM的内存被分为了三个主要部分：新生代，老年代和永久代。

(1)新生代：所有新产生的对象全部都在新生代中，Eden区保存最新的对象，有两个 SurvivorSpace——S1和S0，三个区域的比例大致为 8:1:1。当新生代的Eden区满了，将触发一次GC，我们把新生代中的GC称为minor garbage collections。minor gc是一种Stop the world事件，指发生在新生代的垃圾收集动作，主要是由于Eden区域不够分配了。大多数Java对象都是朝生夕灭，所以Minor GC是非常频繁的，一般回收速度也比较快。
当eden区满了，触发minor gc，这时还有被引用的对象，就会被分配到S0区域，剩下没有被引用的对象就都会被清除。
再一次GC时，S0区的部分对象很可能会出现没有引用的，被引用的对象以及S0中的存活对象，会被一起移动到S1中。eden和S0中的未引用对象会被全部清除。
接下来就是无限循环上面的步骤了，当新生代中存活的对象超过了一定的【年龄】，会被分配至老年代的Tenured区中。这个年龄可以通过参数MaxTenuringThreshold设定，默认值为15。

(2)老年代：老年代用来存储活时间较长的对象，老年代区域的GC是major garbage collection，老年代中的内存不够时，就会触发一次。这也是一个stop the world事件，但是看名字就知道，这个回收过程会相当慢，因为这包括了对新生代和老年代所有对象的回收，也叫FullGC。
老年代管理内存最早采用的算法为标记-清理算法，这个算法很好理解，结合GC Root的定义，我们会把所有不可达的对象全部标记进行清除。
这个算法的劣势很好理解：对，会在标记清除的过程中产生大量的内存碎片，Java在分配内存时通常是按连续内存分配，这样我们会浪费很多内存。所以，现在的JVM GC在老年代都是使用标记-压缩清除方法，在清除后的内存进行整理和压缩，以保证内存连续，虽然这个算法的效率是三种算法里最低的。

(3)永久代：永久代位于方法区，主要存放元数据，例如Class、 Method的元信息，与GC要回收的对象其实关系并不是很大，我们可以几乎忽略其对GC的影响。除了JavaHotSpot这种较新的虚拟机技术，会回收无用的常量和的类，以免大量运用反射这类频繁自定义ClassLoader的操作时方法区溢出。

5.新生代中为什么除了Eden区，还要设置两个Survivor区？

(1)为什么要有Survivor区
如果没有Survivor，Eden区每进行一次Minor GC，存活的对象就会被送到老年代。老年代很快被填满，触发Major GC（因为Major GC一般伴随着Minor GC，也可以看做触发了Full GC）。
老年代的内存空间远大于新生代，进行一次Full GC消耗的时间比Minor GC长得多。频发的Full GC消耗的时间是非常可观的，这一点会影响大型程序的执行和响应速度，更不要说某些连接会因为超时发生连接错误了。
Survivor的存在意义，就是减少被送到老年代的对象，进而减少Full GC的发生，Survivor的预筛选保证，只有经历16次Minor GC还能在新生代中存活的对象，才会被送到老年代。

(2)为什么要设置两个Survivor区
设置两个Survivor区最大的好处就是解决了碎片化。假设只有一个survivor区，Minor GC时，Eden区的存活对象硬放到Survivor区，很明显这两部分对象所占有的内存是不连续的，也就导致了内存碎片化。
碎片化带来的风险是极大的，严重影响JAVA程序的性能。堆空间被散布的对象占据不连续的内存，最直接的结果就是，堆中没有足够大的连续内存空间。
建立两块Survivor区，刚刚新建的对象在Eden中，经历一次Minor GC，Eden中的存活对象就会被移动到第一块survivor space S0，Eden被清空；等Eden区再满了，就再触发一次Minor GC，Eden和S0中的存活对象又会被复制送入第二块survivor space S1。这个过程非常重要，因为这种复制算法保证了S1中来自S0和Eden两部分的存活对象占用连续的内存空间，避免了碎片化的发生。

N.参考

(1)[讲一讲JVM的组成](https://www.cnblogs.com/vipstone/p/10681211.html)

(2)[java堆、栈、堆栈，常量池的区别，史上最全总结](https://cloud.tencent.com/developer/article/1453511)

(3)[数据结构：堆](https://www.jianshu.com/p/6b526aa481b1)

# GC垃圾回收

1.如何判断一个对象是否存活/GC对象的判定方法

可达性分析算法:基本思路是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），如果目标对象到GC Roots是连接着的，我们则称该目标对象是可达的，如果目标对象不可达，则说明目标对象是可以被回收的对象。
可以作为 GC Root的对象可以主要分为四种。(1)JVM栈中引用的对象；(2)方法区中，静态属性引用的对象；(3)方法区中，常量引用的对象；(4)本地方法栈中，JNI（即Native方法）引用的对象。

回收方法区:Java虚拟机规范中确实说过可以不要求虚拟机在方法区中实现垃圾回收，而且在方法区中进行垃圾回收的“性价比”一般比较低，方法区的垃圾收集主要回收两部分内容：废弃的常量和无用的类。
废弃的常量，以常量池中字面量的回收为例，假如一个字符串“abc”已经进入常量池中，但是当前系统已经没有任何一个String对象叫做“abc”的，也没有任何其他地方引用这个字面量，这个“abc”常量就会被清理出常量池。
判断一个无用的类需要同时满足下面3个条件才能算是“无用的类”:a)该类的所有实例都已经被回收，b)加载该类的ClassLoader已经被回收，c)该类对应的java.lang.Class对象已经没有任何地方被引用，无法在任何地方通过反射访问该类的方法。

对象死亡过程：即使在可达性分析算法中不可达的对象，也并非是“非死不可”的，这时候它们暂时处于“缓刑”阶段，要真正宣告一个对象死亡，至少要经历两次标记过程：如果对象在进行可达性分析后发现没有与GC Roots相连接的引用，那它将会被第一次标记并且进行一次筛选，筛选的条件是此对象是否是否有必要执行finalize()方法。当对象没有覆盖finalize()方法，或者finalize()方法已经被虚拟机调用过，虚拟机将这两种情况都视为“没有必要执行”。
如果这个对象被判定为有必要执行finalize()方法，那么这个对象将会放置在一个叫做F-Queue的队列之中。并在稍后由一个虚拟机自动建立的，低优先级的Finalizer线程去执行它。这里所谓“执行”是指虚拟机会触发这个方法，但并不承诺会等待它运行结束，这样做的原因是，如果有一个对象在finalize()方法中执行缓慢，或者发生死循环，将可能会导致F-Queue队列中其他对象永久处于等待，甚至导致整个内存回收系统崩溃。
finalize()方法是对象逃脱死亡命运的最后一次机会，稍后GC将对F-Queue中的对象进行第二次小规模的标记，如果对象这个时候，未被重新引用，那它基本上就真的被回收了。

2.GC收集器

(1)Serial：使用了标记-复制的算法，可以用 -XX:+UseSerialGC使用单线程的串行收集器。但是在GC进行时，程序会进入长时间的暂停时间，一般不太建议使用。

(2)Parallel：-XX:+UseParallelGC-XX:+UseParallelOldGCParallel也使用了标记-复制的算法，但是我们称之为吞吐量优先的收集器，因为Parallel最主要的优势在于并行使用多线程去完成垃圾清理工作，这样可以充分利用多核的特性，大幅降低gc时间。当你的程序场景吞吐量较大，例如消息队列这种应用，需要保证有效利用CPU资源，可以忍受一定的停顿时间，可以优先考虑这种方式。

(3)CMS (ConcurrentMarkSweep)：-XX:+UseParNewGC-XX:+UseConcMarkSweepGC CMS使用了标记-清除的算法，当应用尤其重视服务器的响应速度（比如Apiserver），希望系统停顿时间最短，以给用户带来较好的体验，那么可以选择CMS。CMS收集器在MinorGC时会暂停所有的应用线程，并以多线程的方式进行垃圾回收。在FullGC时不暂停应用线程，而是使用若干个后台线程定期的对老年代空间进行扫描，及时回收其中不再使用的对象。

(4)G1（GarbageFirst）：-XX:+UseG1GC 在堆比较大的时候，如果full gc频繁，会导致停顿，并且调用方阻塞、超时、甚至雪崩的情况出现，所以降低full gc的发生频率和需要时间，非常有必要。G1的诞生正是为了降低FullGC的次数，而相较于CMS， G1使用了标记-压缩清除算法，这可以大大降低较大内存（4GB以上） GC时产生的内存碎片。

G1提供了两种GC模式，YoungGC和MixedGC，两种都是StopTheWorld(STW)的。YoungGC主要是对Eden区进行GC，MixGC不仅进行正常的新生代垃圾收集，同时也回收部分后台扫描线程标记的老年代分区。
另外有趣的一点，G1将新生代、老年代的物理空间划分取消了，而是将堆划分为若干个区域（region），每个大小都为2的倍数且大小全部一致，最多有2000个。除此之外， G1专门划分了一个Humongous区，它用来专门存放超过一个region 50%大小的巨型对象。在正常的处理过程中，对象从一个区域复制到另外一个区域，同时也完成了堆的压缩。

# JVM调优

1.JVM的内存溢出

内存溢出：内存空间不足导致，新对象无法分配到足够的内存。
内存泄漏：应该释放的对象没有被释放，多见于自己使用容器保存元素的情况下。

(1)堆内存溢出，java.lang.OutOfMemoryError: Java heap space
解决方案：JDK自带的jvisualvm.exe工具可以分析.hprof和.dump文件。首先需要找出最大的对象，判断最大对象的存在是否合理，如何合理就需要调整JVM内存大小。如果不合理，那么这个对象的存在，就是最有可能是引起内存溢出的根源。通过GC Roots的引用链信息，就可以比较准确地定位出泄露代码的位置。

(2)超出GC开销限制，当出现java.lang.OutOfMemoryError: GC overhead limit exceeded异常信息时，表示超出了GC开销限制。当超过98%的时间用来做GC，但是却回收了不到2%的堆内存时会抛出此异常。
解决方案：通过-XX:-UseGCOverheadLimit参数来禁用这个检查，但是并不能从根本上来解决内存溢出的问题，最后还是会报出java.lang.OutOfMemoryError: Java heap space异常；调整JVM内存大小（-Xmx与-Xms）。

(3)虚拟机栈和本地方法栈溢出，如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常。如果虚拟机在扩展栈时无法申请到足够的内存空间，则抛出OutOfMemoryError异常。这里把异常分成两种情况，看似更加严谨，但却存在着一些互相重叠的地方：当栈空间无法继续分配时，到底是内存太小，还是已使用的栈空间太大，其本质上只是对同一件事情的两种描述而已。
StackOverflowError：主要原因有两点：单个线程请求的栈深度大于虚拟机所允许的最大深度；创建的线程过多。

a)单个线程请求的栈深度大于虚拟机所允许的最大深度
主要表现有以下几点：存在递归调用，存在循环依赖调用方法调用链路很深，比如使用装饰器模式的时候，对已经装饰后的对象再进行装饰。
影响递归的深度因素有：单个线程的栈空间大小（-Xss），局部变量表的大小。单个线程请求的栈深度超过内存限制导致的栈内存溢出，一般是由于非正确的编码导致的。

b)创建的线程过多
不断地建立线程也可能导致栈内存溢出，因为我们机器的总内存是有限制的，所以虚拟机栈和本地方法栈对应的内存也是有最大限制的。如果单个线程的栈空间越大，那么整个应用允许创建的线程数就越少。异常信息java.lang.OutOfMemoryError: unable to create new native thread。
虚拟机栈和本地方法栈内存 ≈ 操作系统内存限制 - 最大堆容量(Xmx) - 最大方法区容量(MaxPermSize)
Java的线程是映射到操作系统的内核线程上，因此过多地创建线程有较大的风险，可能会导致操作系统假死。

(4)元数据区域的内存溢出，报错java.lang.OutOfMemoryError: Metaspace
元数据区域或方法区是用于存放Class的相关信息，如类名、访问修饰符、常量池、字段描述、方法描述等。我们可以通过在运行时产生大量的类去填满方法区，直到溢出，如：代理的使用(CGlib)、大量JSP或动态产生JSP文件的应用（JSP第一次运行时需要编译为Java类）、基于OSGi的应用（即使是同一个类文件，被不同的加载器加载也会视为不同的类）等。

(5)运行时常量池的内存溢出，包括java.lang.OutOfMemoryError: PermGen space
String.intern()是一个Native方法，它的作用是：如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象；否则，将此String对象包含的字符串添加到常量池中，并且返回此String对象的引用。

(6)直接内存溢出，由DirectMemory导致的内存溢出，一个明显的特征是在Heap Dump文件中不会看见明显的异常，如果读者发现OOM之后Dump文件很小，而程序中又直接或间接使用了NIO，那就可以考虑检查一下是不是这方面的原因。
DirectMemory容量可通过-XX:MaxDirectMemorySize指定，如果不指定，则默认与Java堆最大值（-Xmx指定）一样。

    public class DirectMemoryOutOfMemoryErrorTest {
    
        public static void main(String[] args) throws IllegalAccessException {
            int _1M = 1024 * 1024;
            Field unsafeField = Unsafe.class.getDeclaredFields()[0];
            unsafeField.setAccessible(true);
            Unsafe unsafe = (Unsafe) unsafeField.get(null);
            while (true) {
                unsafe.allocateMemory(_1M);
            }
        }
    }

2.JVM参数配置

    -verbose:gc 
    -Xms20m 最小内存
    -Xmx20m 最大内存
    -XX:+HeapDumpOnOutOfMemoryError 
    -XX:HeapDumpPath=D:\dump 堆Dump文件路径
    -XX:-UseGCOverheadLimit 取消GC开销检查
    -Xss1m 单个线程栈空间大小
    -XX:MetaspaceSize=5m 元数据区大小
    -XX:MaxMetaspaceSize=5m 元数据区最大大小
    -XX:MaxDirectMemorySize 直接内存容量





























二、Java内存区域
1、Java内存结构
图片

内存结构
程序计数器
当前线程所执行字节码的行号指示器。若当前方法是native的，那么程序计数器的值就是undefined。

线程私有，Java内存区域中唯一一块不会发生OOM或StackOverflow的区域。

虚拟机栈
就是常说的Java栈，存放栈帧，栈帧里存放局部变量表等信息，方法执行到结束对应着一个栈帧的入栈到出栈。

线程私有，会发生StackOverflow。

本地方法栈
与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的。

线程私有，会发生StackOverflow。

堆
Java 虚拟机中内存最大的一块，几乎所有的对象实例都在这里分配内存。

是被所有线程共享的，会发生OOM。

方法区
也称非堆，用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。

是被所有线程共享的，会发生OOM。

运行时常量
是方法区的一部分，存常量（比如static final修饰的，比如String 一个字符串）和符号引用。

是被所有线程共享的，会发生OOM。

2、对象创建时堆内存分配算法
指针碰撞
前提要求堆内存的绝对工整的。

所有用过的内存放一边，没用过的放另一边，中间放一个分界点的指示器，当有对象新生时就已经知道大小了，指示器只需要像没用过的内存那边移动与对象等大小的内存区域即可。

图片

指针碰撞
空闲列表
假设堆内存并不工整，那么空闲列表最合适。

JVM维护一个列表 ，记录哪些内存块是可用的，当对象创建时从列表中找到一块足够大的空间划分给新生对象，并将这块内存标记为已用内存。

图片

空闲列表
3、对象在内存中的存储布局
分为三部分：

对象头
包含两部分：自身运行时数据和类型指针。

自身运行时数据包含：hashcode、gc分代年龄、锁状态标识、线程持有的锁、偏向线程ID、偏向时间戳等

对象指针就是对象指向它的类元数据的指针，虚拟机通过这个指针来确定对象是哪个类的实例

实例数据
用来存储对象真正的有效信息（包括父类继承下来的和自己定义的）

对齐填充
JVM要求对象起始地址必须是8字节的整数倍（8字节对齐），所以不够8字节就由这部分来补充。

4、对象怎么定位
如下两种，具体用哪种有JVM来选择，hotspot虚拟机采取的直接指针方式来定位对象。

直接指针
栈上的引用直接指向堆中的对象。好处就是速度快。没额外开销。

句柄
Java堆中会单独划分出一块内存空间作为句柄池，这么一来栈上的引用存储的就是句柄地址，而不是真实对象地址，而句柄中包含了对象的实例数据等信息。好处就是即使对象在堆中的位置发生移动，栈上的引用也无需变化。因为中间有个句柄。

5、判断对象是否能被回收的算法
引用计数法
给对象添加一个引用计数器，每当有一个地方引用他的时候该计数器的值就+1，当引用失效的时候该计数器的值就-1；当计数器的值为0的时候，jvm判定此对象为垃圾对象。存在内存泄漏的bug，比如循环引用的时候，所以jvm虚拟机采取的是可达性分析法。

可达性分析法
有一些根节点GC Roots作为对象起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连的时候，则证明此对象为垃圾对象。

补充：哪些可作为GC Roots？

虚拟机栈中的引用的对象
方法区中的类静态属性引用的对象
方法区中常量引用的对象
本地方法栈中JNI（native方法）引用的对象
6、如何判断对象是否能被回收
该对象没有与GC Roots相连

该对象没有重写finalize()方法或finalize()已经被执行过则直接回收（第一次标记）、否则将对象加入到F-Queue队列中（优先级很低的队列）在这里finalize()方法被执行，之后进行第二次标记，如果对象仍然应该被GC则GC，否则移除队列。（在finalize方法中，对象很可能和其他 GC Roots中的某一个对象建立了关联，那就自救了，就不会被GC掉了，finalize方法只会被调用一次，且不推荐使用finalize方法）

7、Java堆内存组成部分
图片

堆组成部分
堆大小 = 新生代 + 老年代。如果是Java8则没有Permanent Generation，Java8将此区域换成了Metaspace。

其中新生代(Young) 被分为 Eden和S0（from)和S1(to)。

默认情况下Edem : from : to = 8 : 1 : 1﻿，此比例可以通过 –XX:SurvivorRatio 来设定

8、什么时候抛出StackOverflowError
方法运行的时候栈的深度超过了虚拟机容许的最大深度的时候，所以不推荐递归的原因之一也是因为这个，效率低，死归的话很容易就StackOverflowError了。

9、Java中会存在内存泄漏吗，请简单描述。
虽然Java会自动GC，但是使用不当的话还是存在内存泄漏的，比如ThreadLocal忘记remove的情况。（ThreadLocal篇幅过长，不适合放到这里，懂者自懂，不懂Google）

10、栈帧是什么？包含哪些东西
栈帧中存放的是局部变量、操作数栈、动态链接、方法出口等信息，栈帧中的局部变量表存放基本类型+对象引用+returnAddress，局部变量所需的内存空间在编译期间就完成分配了，因为基本类型和对象引用等都能确定占用多少slot，在运行期间也是无法改变这个大小的。

11、简述一个方法的执行流程
方法的执行到结束其实就是栈帧的入栈到出栈的过程，方法的局部变量会存到栈帧中的局部变量表里，递归的话会一直压栈压栈，执行完后进行出栈，所以效率较低，因为一直在压栈，栈是有深度的。

12、方法区会被回收吗
方法区回收价值很低，主要回收废弃的常量和无用的类。

如何判断无用的类：

该类所有实例都被回收（Java堆中没有该类的对象）

加载该类的ClassLoader已经被回收

该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方利用反射访问该类

13、一个对象包含多少个字节
会占用16个字节。比如

Object obj = new Object();

因为obj引用占用栈的4个字节，new出来的对象占用堆中的8个字节，4+8=12，但是对象要求都是8的倍数，所以对象的字节对齐（Padding）部分会补齐4个字节，也就是占用16个 字节。

再比如：

public class NewObj {
    int count;
    boolean flag;
    Object obj;
}
NewObj obj = new NewObj();
这个对象大小为：空对象8字节+int类型4字节+boolean类型1字节+对象的引用4字节=17字节，需要8的倍数，所以字节对齐需要补充7个字节，也就是这段程序占用24字节。

14、为什么把堆栈分成两个
栈代表了处理逻辑，堆代表了存储数据，分开后逻辑更清晰，面向对象模块化思想。

栈是线程私有，堆是线程共享区，这样分开也节省了空间，比如多个栈中的地址指向同一块堆内存中的对象。

栈是运行时的需要，比如方法执行到结束，栈只能向上增长，因此会限制住栈存储内容的能力，而堆中的对象是可以根据需要动态增长的。

15、栈的起始点是哪
main函数，也是程序的起始点。

16、为什么基本类型不放在堆里
因为基本类型占用的空间一般都是1-8个字节（所需空间很少），而且因为是基本类型，所以不会出现动态增长的情况（长度是固定的），所以存到栈上是比较合适的。反而存到可动态增长的堆上意义不大。

17、Java参数传递是值传递还是引用传递
值传递。

基本类型作为参数被传递时肯定是值传递；引用类型作为参数被传递时也是值传递，只不过“值”为对应的引用。假设方法参数是个对象引用，当进入被调用方法的时候，被传递的这个引用的值会被程序解释到堆中的对象，这个时候才对应到真正的对象，若此时进行修改，修改的是引用对应的对象，而不是引用本身，也就是说修改的是堆中的数据，而不是栈中的引用。

18、为什么不推荐递归
因为递归一直在入栈入栈，短时间无法出栈，导致栈的压力会很大，栈也有深度的，容易爆掉，所以效率低下。

19、为什么参数大于2个要放到对象里
因为除了double和long类型占用局部变量表2个slot外，其他类型都占用1个slot大小，如果参数太多的话会导致这个栈帧变大，因为slot大，放个对象的引用上去的话只会占用1个slot，增加堆的压力减少栈的压力，堆自带GC，所以这点压力可以忽略。

20、常见笔试题
问题：输出结果是什么？

答案：aaa、aaa、abc

原因：其实也是值传递还是引用传递的问题。具体核心原因：main函数的str引用和zcd在栈上，而其对应的值在方法区或堆上。test1、test2、test3的参数也在栈上，这个空间和main上的不是同一块，是不同的栈帧。所以你修改test方法的数据对于main函数其实是无感知的。但是对象的引用的话修改的是堆内存中的对象属性值，所以有感知，那为什么test2输出的是aaa而不是abc呢？因为test2把堆中的对象都给换了，重新生成一个全新对象，对main上的引用来讲是看不到的，具体如下三幅图：

（1）aaa

图片

题1
（2）aaa

图片

题2
（3）abc

图片

题3
public class TestChuandi {
    public static void main(String[] args) {
        String str = "aaa";
        test1(str);
        // aaa
        System.out.println(str);

        Zhichuandi zcd = new Zhichuandi();
        zcd.setName("aaa");

        // aaa
        test2(zcd);
        System.out.println(zcd.getName());

        // abc
        test3(zcd);
        System.out.println(zcd.getName());
    }

    private static void test1(String s) {
        s = "abc";
    }

    private static void test2(Zhichuandi zcd) {
        zcd = new Zhichuandi();
        zcd.setName("abc");
    }

    private static void test3(Zhichuandi zcd) {
        zcd.setName("abc");
    }
}

class Zhichuandi {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}





三、GC垃圾回收

1、GC是什么？为什么要GC
GC：垃圾收集，GC能帮助我们释放jvm内存，可以一定程度避免OOM问题，但是也无法完全避免。Java的GC是自动工作的，不像C++需要主动调用。

当new对象的时候，GC就开始监控这个对象的地址大小和使用情况了，通过可达性分析算法寻找不可达的对象然后进行标记看看是否需要GC回收掉释放内存。

2、你能保证GC执行吗？
不能，我只能通过手动执行System.gc()方法通知GC执行，但是他是否执行的未知的。

3、对象的引用类型有哪几种，分别介绍下
强引用
发生GC的时候不会回收强引用所关联的对象。比如new就是强引用。

软引用
有用但非必须的对象，在OOM之前会把这些对象列进回收范围之中进行第二次回收，若第二次回收还没有足够的内存，则会抛出OOM。也就是第一次快要发生OOM的时候不会立马抛出OOM，而是会回收掉这些软引用，然后再看内存是否足够，若还不够才会抛出OOM。

弱引用
有用但非必须的对象，比软引用更弱一些，只要开始GC，不管你内存够不够，都会将 弱引用所关联的对象给回收掉。

虚引用
也叫幽灵引用/幻影引用，无法通过虚引用获得对象，他的意义在于能在这个对象被GC掉时收到一个系统通知，仅此而已。

4、垃圾收集算法有哪些
标记清除
分为两步：标记和清除。

首先需要标记出所有需要回收的对象，然后进行清除回收变为可用内存。

缺点：效率低，会产生垃圾碎片 。

图片

标记清除01
图片

标记清除
复制算法
将可用堆内存按照容量分为大小相等的两块，每次只用一块，当这块内存快用完了，就将还存活着的对象复制到另一块上面，然后再把已使用过的内存一次清理掉。

年轻代from/to（s1/s2）采取的就是此种算法。老年代一般不会采取此种算法，因为老年代都是大对象且存活的久的，空间压缩一半代价略高。

优点：效率较高、不会产生碎片。

缺点：将内存缩小为原来的一半，代价略高。

图片

复制算法-1
图片

复制算法-1
标记整理
分为两步：标记和整理。

整理其实也是两步：整理+清除。

整理让所有存活的对象都移动到一端，然后清理掉边界以外的内存。

优点：不会产生碎片问题，适合年老代的大对象存储，不像复制算法那样浪费空间。

缺点：效率赶不上复制算法。

图片

标记整理-1
图片

标记整理-1
分代算法
并不是新算法，而是根据对象存活周期的不同将内存划分为几块，一般是新生代和老年代，新生代基本采用复制算法，老年代采用标记整理算法。

5、为什么要分代
因为在不进行对象存活时间区分的情况下，每次垃圾回收都是对整个堆空间进行回收，花费时间会相对较长，也有很多对象完全没必要遍历，比如大对象存活的时间更长，遍历下来发现不需要回收，这样更浪费时间。所以才有了分代，分治的思想，进行区域划分，把不同生命周期的对象放在不同的区域，不同的区域采取最适合他的垃圾回收方式进行回收。

6、分代垃圾回收是怎么工作的
分代回收基于这样一个理念：不同的对象的生命周期是不一样的，因此根据对象存活周期的不同将内存划分为几块，一般是新生代和老年代，新生代基本采用复制算法，老年代采用标记整理算法。这样来提高回收效率。

新生代执行流程：

把 Eden + From Survivor（S1） 存活的对象放入 To Survivor（S2） 区；
清空 Eden 和S1 区；
S1 和 S2 区交换，S1 变 S2，S2变S1。
每次在S1到S2移动时都存活的对象，年龄就 +1，当年龄到达 15（默认配置是 15）时，升级为老年代。大对象也会直接进入老年代。

老年代当空间占用到达某个值之后就会触发全局垃圾收回，一般使用标记整理的执行算法。

7、垃圾回收器有哪些
图片

垃圾收集器
Serial
采取复制算法，用于新生代，单线程收集器，所以在他工作时会产生StopTheWorld。单线程情况下效率更高，比如用于GUI小程序

ParNew
采取复制算法，用于新生代，是Serial的多线程版本，多个GC线程同时工作，但是也会产生StopTheWorld，因为不能和工作线程并行。

Parallel Scavenge
采取复制算法，用于新生代，和ParNew一样，所以也会产生STW，多线程收集器，他是吞吐量优先的收集器，提供了很多参数来调节吞吐量。

Serial Old
采取标记整理算法，用于老年代，单线程收集器，所以在他工作时会产生StopTheWorld。单线程情况下效率更高，比如用于GUI小程序

Parallel Old
采取标记整理算法，用于老年代，Parallel Scavenge收集器的老年代版本，吞吐量优先。

CMS
采取标记清除算法，老年代并行收集器，号称以最短STW时间为目标的收集器，并发高、停顿低、STW时间短的优点。主流垃圾收集器之一。

G1
采取标记整理算法，并行收集器。对比CMS的好处之一就是不会产生内存碎片，此外，G1收集器不同于之前的收集器的一个重要特点是：G1回收的范围是整个Java堆(包括新生代，老年代)，而前六种收集器回收的范围仅限于新生代或老年代。而且他的STW停顿时间是可以手动控制一个长度为M毫秒的时间片段（可以用JVM参数 -XX:MaxGCPauseMillis指定），设置完后垃圾收集的时长不得超过这个（近实时）。

8、详细介绍一下 CMS 垃圾回收器？
采取标记清除算法，老年代并行收集器，号称以最短STW时间为目标的收集器，并发高、停顿低、STW时间短的优点。主流垃圾收集器之一。

主要分为四阶段：

初始标记：只是标记一下 GC Roots 能直接关联的对象，速度很快，仍然需要暂停所有的工作线程。所以此阶段会STW，但时间很短。
并发标记：进行 GC Roots 跟踪的过程，和用户线程一起工作，不需要暂停工作线程。不会STW。
重新标记：为了修正在并发标记期间，因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，仍然需要暂停所有的工作线程。STW时间会比第一阶段稍微长点，但是远比并发标记短，效率也很高。
并发清除：清除GC Roots不可达对象，和用户线程一起工作，不需要暂停工作线程。
图片

cms
所以CMS的优点是：

并发高
停顿低
STW时间短。
缺点：

对cpu资源非常敏感（并发阶段虽然不会影响用户线程，但是会一起占用CPU资源，竞争激烈的话会导致程序变慢）。

无法处理浮动垃圾，当剩余内存不能满足程序运行要求时，系统将会出现 Concurrent Mode Failure，失败后而导致另一次Full GC的产生，由于CMS并发清除阶段用户线程还在运行，伴随程序的运行自然会有新的垃圾产生，这一部分垃圾是出现在标记过程之后的，CMS无法在本次去处理他们，所以只好留在下一次GC时候将其清理掉。

内存碎片问题（因为是标记清除算法）。当剩余内存不能满足程序运行要求时，系统将会出现 Concurrent Mode Failure，临时 CMS 会采用 Serial Old 回收器进行垃圾清除，此时的性能将会被降低。

9、详细介绍一下 G1 垃圾回收器？
采取标记整理算法，并行收集器。

特点：

并行与并发执行：利用多CPU的优势来缩短STW时间，在GC工作的时候，用户线程可以并行执行。
分代收集：无需其他收集器配合，自己G1会进行分代收集。
空间整合：不会像CMS那样产生内存碎片。
可预测的停顿：可以手动控制一个长度为M毫秒的时间片段（可以用JVM参数 -XX:MaxGCPauseMillis指定），设置完后垃圾收集的时长不得超过这个（近实时）。
原理：

G1并不是简单的把堆内存分为新生代和老年代两部分，而是把整个堆划分为多个大小相等的独立区域（Region），新生代和老年代也是一部分不需要连续Region的集合。G1跟踪各个Region里面的垃圾堆积的价值大小，在后台维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的Region。

补充：

Region不是孤立的，也就是说一个对象分配在某个Region中，他并非只能被本Region中的其他对象引用，而是整个堆中任意的对象都可以相互引用，那么在【可达性分析法】来判断对象是否存活的时候也无需扫描整个堆，Region之间的对象引用以及其他手机其中新生代和老年代之间的对象引用虚拟机都是使用Remembered Set来避免全堆扫描的。

步骤：

初始标记：仅仅标记GCRoots能直接关联到的对象，且修改TAMS的值让下一阶段用户程序并发运行时能正确可用的Region中创建的新对象。速度很快，会STW。
并发标记：进行 GC Roots 跟踪的过程，和用户线程一起工作，不需要暂停工作线程。不会STW。
最终标记：为了修正在并发标记期间，因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，仍然需要暂停所有的工作线程。STW时间会比第一阶段稍微长点，但是远比并发标记短，效率也很高。
筛选回收：首先对各个Region的回收价值和成本进行排序，根据用户所期望的GC停顿时间来制定回收计划。
图片

G1
10、GC日志分析
图片

GClog-1
11、Minor GC与Full GC分别在什么时候发生
新生代内存（Eden区）不够用时候发生Minor GC也叫YGC。

Full GC发生情况：

老年代被写满
持久代被写满
System.gc()被显示调用（只是会告诉需要GC，什么时候发生并不知道）
12、新生代垃圾回收器和老年代垃圾回收器都有哪些？有什么区别？
新生代回收器：Serial、ParNew、Parallel Scavenge
老年代回收器：Serial Old、Parallel Old、CMS
整堆回收器：G1
新生代垃圾回收器一般采用的是复制算法，复制算法的优点是效率高，缺点是内存利用率低；老年代回收器一般采用的是标记-整理的算法进行垃圾回收。标记整理很适合大对象，不会产生空间碎片。

13、栈上分配是什么意思
JVM允许将线程私有的对象分配在栈上，而不是分配在堆上。分配在栈上的好处是栈上分配不需要考虑垃圾回收，因为出栈的时候对象就顺带着一起出去了，没了，而不需要垃圾回收器的介入，从而提高系统性能。

补充1：对象逃逸。
逃逸的目的是判断对象的作用域是否有可能逃出函数体。例如下面的代码就显示了一个逃逸的对象：

private User user;
private void hello(){
   user = new User();
}
对象实例 user 是类的成员变量，可以被任何线程访问，因此它属于逃逸对象。但如果我们将代码稍微改动一下，该对象就可以线程非逃逸的了。

private void hello(){
   User user = new User();
}
可以看到 user 实例作用域只在 hello 函数中，不会被其他线程访问到，也不会访问。所以该 user 实例对象的作用域只在该函数中，因此它并未发生逃逸。对于这样的情况，虚拟机就有可能将其分配在栈上，而不在堆上。

补充2：TLAB，自行Google。
简单点说，就是将本来应该分配在堆中的对象，让其分配在线程私有的栈上。通过这种方式，减少垃圾回收的压力，提高虚拟机的运行效率。

14、简述下对象的分配规则
对象优先分配在Eden区，如果Eden区没有足够的空间时，虚拟机执行一次YGC。并将还活着的对象放到from/to区，若本次YGC后还是没有足够的空间，则将启用分配担保机制在老年代中分配内存。
大对象直接进入老年代（大对象是指需要大量连续内存空间的对象）。这样做的目的是避免在Eden区和两个Survivor区之间发生大量的内存拷贝（新生代采用复制算法收集内存）。
长期存活的对象进入老年代。虚拟机为每个对象定义了一个年龄计数器，如果对象经过了1次YGC那么对象会进入Survivor区，之后每经过一次YGC那么对象的年龄加1，直到达到阀值对象进入老年区。默认阈值是15。可以通过-XX:MaxTenuringThreshold参数来设置。
动态判断对象的年龄。如果Survivor区中相同年龄的所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象可以直接进入老年代。无需等到-XX:MaxTenuringThreshold参数要求的年龄。
动态年龄判断是有歧义的，要想面试加分，必看这个

https://www.jianshu.com/p/989d3b06a49d

空间分配担保。每次进行YGC时，JVM会计算Survivor区移至老年区的对象的平均大小，如果这个值大于老年区的剩余值大小则进行一次Full GC，如果小于检查HandlePromotionFailure设置，如果true则只进行YGC，如果false则进行Full GC。
图片

对象创建








四、实战调优
1、你在项目中都使用了哪些参数打印GC？
具体的参数名称记不清楚了，但是我一般在项目中输出详细的 GC 日志，并加上可读性强的 GC 日志的时间戳。特别情况下我还会追加一些反映对象晋升情况和堆详细信息的日志，这些会单独打到gc.log文件中用来排查问题。另外，OOM 时自动 Dump 堆栈，我一般也会进行配置。

2、常用的调优工具有哪些？
JDK内置的命令行：jps（查看jvm进程信息）、jstat（监视jvm运行状态的，比如gc情况、jvm内存情况、类加载情况等）、jinfo（查看jvm参数的，也可动态调整）、jmap（生成dump文件的，在dump的时候会影响线上服务）、jhat（分析dump的，但是一般都将dump导出放到mat上分析）、jstack（查看线程的）。

JDK内置的可视化界面：JConsole、VisualVM，这两个在QA环境压测的时候很有用。

阿里巴巴开源的arthas：神器，线上调优很方便，安装和显示效果都很友好。

3、如果有一个系统，内存一直消耗不超过10%，但是观察GC日志，发现FGC总是频繁产生，会是什么引起的？
检查下系统是否存在System.gc() ;

4、线上一个系统跑一段时间就栈溢出了，怎么办 ？
1.首先检查下是否有死归这种无限递归的程序或者递归方法太多

2.可以看下栈大小，若太小则可以指定-Xss参数设置栈大小

5、系统CPU经常100%，如何调优？
CPU100%，那肯定是有线程一直在占用着系统资源，所以具体方法如下:

找出哪个进程cpu占用高（top命令）
该进程中的哪个线程cpu占用高（top -Hp $pid命令）
将十进制的tid转化为十六进制（printf %x $tid命令）
导出该线程的堆栈 (jstack $pid >$pid.log命令)
查找哪个方法（栈帧）消耗时间 (less $pid.log)
可以确认工作线程占比高还是垃圾回收线程占比高
修改代码
6、系统内存飙高，如何查找问题？
找出哪个进程内存占用高（top命令）
查看jvm进程号（jps命令）
导出堆内存 (jmap命令生成dump文件，注意：线上系统，内存特别大，jmap执行期间会对进程产生很大影响，甚至卡顿，所以操作前最好先从负载均衡里摘掉。)
分析dump文件 (比如mat软件)
7、大型项目如何进行性能瓶颈调优
1.数据库与SQL优化：一般dba负责数据库优化，比如集群主从等。研发负责SQL优化，比如索引、分库分表等。

2.集群优化：一般OP负责，让整个集群可以很容易的水平扩容，再比如tomcat/nginx的一些配置优化等。

3.硬件升级：选择最合适的硬件，充分利用资源。

4.代码优化：很多细节，可以参照阿里巴巴规范手册和安装sonar插件这种检测代码质量的工具。也可以适当的运用并行，比如CountDownLatch等工具。

5.jvm优化：内存区域大小设置、对象年龄达到次数晋升老年代参数的调整、选择合适的垃圾收集器以及合适的垃圾收集器参数、打印详细的GC日志和oom的时候自动生成dump。

6.操作系统优化

8、你实际遇到调优的场景
我们之前都是采取zipkin做分布式链路追踪，后来换成了skywalking，所以将zipkin相关配置和代码都移除了，但是忘记移除maven坐标了，运行一段时间后导致了频繁full gc ，最后oom了。

因为配置了oom后自动生成dump文件，所以分析dump文件发现大对象是zipkin包里的ConcurrentHashMap$Node，通过观察zipkin的自动配置类ZipkinAutoConfiguration发现即使没有任何zipkin的配置，只要有zipkin的依赖都会创建一个异步报告者，默认的采样率是10%，所以即使不配置相关配置项，也会以默认采样率10%，发送到zipkin，这是默认的地址是http://localhost:9411/，此时发送到localhost:9411显然会连接拒绝。导致度量中的异常实例堆积，从而OOM。

private float probability = 0.1f;

@ConfigurationProperties("spring.zipkin")
public class ZipkinProperties {
    private String baseUrl = "http://localhost:9411/";
}
附录
GC常用参数
-Xmn：年轻代
-Xms：最小堆
-Xmx ：最大堆
-Xss：栈空间
-XX:+UseTLAB：使用TLAB，默认打开
-XX:+PrintTLAB：打印TLAB的使用情况
-XX:TLABSize：设置TLAB大小
-XX:+DisableExplictGC：禁用System.gc()不管用 ，防止FGC
-XX:+PrintGC：打印GC日志
-XX:+PrintGCDetails：打印GC详细日志信息
-XX:+PrintHeapAtGC：打印GC前后的详细堆栈信息
-XX:+PrintGCTimeStamps：打印时间戳
-XX:+PrintGCApplicationConcurrentTime：打印应用程序时间
-XX:+PrintGCApplicationStoppedTime：打印暂停时长
-XX:+PrintReferenceGC：记录回收了多少种不同引用类型的引用
-verbose:class：类加载详细过程
-XX:+PrintVMOptions：jvm参数
-XX:+PrintFlagsFinal：-XX:+PrintFlagsInitial 必须会用
-Xloggc:opt/log/gc.log：gc日志的路径以及文件名称
-XX:MaxTenuringThreshold：升代年龄，最大值15
Parallel常用参数
-XX:SurvivorRatio：年轻代中eden和from/to的比值。比如设置3就是eden:survivor=3:2，也就是from和to各占1，eden占用3
-XX:PreTenureSizeThreshold：大对象到底多大
-XX:MaxTenuringThreshold：升代年龄，最大值15
-XX:+ParallelGCThreads：并行收集器的线程数，同样适用于CMS，一般设为和CPU核数相同
-XX:+UseAdaptiveSizePolicy：自动选择各区大小比例
CMS常用参数
-XX:+UseConcMarkSweepGC：设置年老代为并发收集
-XX:ParallelCMSThreads：CMS线程数量
-XX:CMSInitiatingOccupancyFraction：使用多少比例的老年代后开始CMS收集，默认是68%(近似值)，如果频繁发生SerialOld卡顿，应该调小，（频繁CMS回收）
-XX:+UseCMSCompactAtFullCollection：在FGC时进行压缩
-XX:CMSFullGCsBeforeCompaction：多少次FGC之后进行压缩
-XX:+CMSClassUnloadingEnabled：年老代启用CMS，但默认是不会回收永久代(Perm)的。此处对Perm区启用类回收，防止Perm区内存满。
-XX:CMSInitiatingPermOccupancyFraction：达到什么比例时进行Perm回收
GCTimeRatio：设置GC时间占用程序运行时间的百分比
-XX:MaxGCPauseMillis：停顿时间，是一个建议时间，GC会尝试用各种手段达到这个时间，比如减小年轻代
G1常用参数
-XX:+UseG1GC：开启G1
-XX:MaxGCPauseMillis：建议值，G1会尝试调整Young区的块数来达到这个值
-XX:GCPauseIntervalMillis：GC的间隔时间
-XX:+G1HeapRegionSize：分区大小，建议逐渐增大该值，1 2 4 8 16 32。随着size增加，垃圾的存活时间更长，GC间隔更长，但每次GC的时间也会更长 ZGC做了改进（动态区块大小）
G1NewSizePercent：新生代最小比例，默认为5%
G1MaxNewSizePercent：新生代最大比例，默认为60%
GCTimeRatio：GC时间建议比例，G1会根据这个值调整堆空间
ConcGCThreads：线程数量
InitiatingHeapOccupancyPercent：启动G1的堆空间占用比例






【240期】面试官：你了解JVM的内存溢出吗？
Java面试题精选 3月10日
图片

Java堆溢出
Java堆用于存储对象实例，只要不断地创建对象，当对象数量到达最大堆的容量限制后就会产生内存溢出异常。最常见的内存溢出就是存在大的容器，而没法回收，比如：Map，List等。

内存溢出：内存空间不足导致，新对象无法分配到足够的内存；
内存泄漏：应该释放的对象没有被释放，多见于自己使用容器保存元素的情况下。
出现下面信息就可以断定出现了堆内存溢出。

java.lang.OutOfMemoryError: Java heap space

保证GC Roots到对象之间有可达路径来避免垃圾回收机制清除这些对象

示例
设置JVM内存参数：

-verbose:gc -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
~

/**
 * java 堆内存溢出
 * <p>
 * VM Args: -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
 *
 * @author yuhao.wang3
 */
public class HeapOutOfMemoryErrorTest {
    public static void main(String[] args) throws InterruptedException {
        // 模拟大容器
        List<Object> list = Lists.newArrayList();
        for (long i = 1; i > 0; i++) {
            list.add(new Object());
            if (i % 100_000 == 0) {
                System.out.println(Thread.currentThread().getName() + "::" + i);
            }
        }
    }
}
运行结果

[GC (Allocation Failure)  5596K->1589K(19968K), 0.0422027 secs]
main::100000
main::200000
[GC (Allocation Failure)  7221K->5476K(19968K), 0.0144103 secs]
main::300000
[GC (Allocation Failure)  9190K->9195K(19968K), 0.0098252 secs]
main::400000
main::500000
[Full GC (Ergonomics)  17992K->13471K(19968K), 0.3431052 secs]
main::600000
main::700000
main::800000
[Full GC (Ergonomics)  17127K->16788K(19968K), 0.1581969 secs]
[Full GC (Allocation Failure)  16788K->16758K(19968K), 0.1994445 secs]
java.lang.OutOfMemoryError: Java heap space
Dumping heap to D:\dump\java_pid7432.hprof ...
Heap dump file created [28774262 bytes in 0.221 secs]
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
 at java.util.Arrays.copyOf(Arrays.java:3210)
 at java.util.Arrays.copyOf(Arrays.java:3181)
 at java.util.ArrayList.grow(ArrayList.java:261)
 at java.util.ArrayList.ensureExplicitCapacity(ArrayList.java:235)
 at java.util.ArrayList.ensureCapacityInternal(ArrayList.java:227)
 at java.util.ArrayList.add(ArrayList.java:458)
 at com.xiaolyuh.HeapOutOfMemoryErrorTest.main(HeapOutOfMemoryErrorTest.java:23)
Disconnected from the target VM, address: '127.0.0.1:61622', transport: 'socket'
分析工具
JDK自带的jvisualvm.exe工具可以分析.hprof和.dump文件。

首先需要找出最大的对象，判断最大对象的存在是否合理，如何合理就需要调整JVM内存大小。如果不合理，那么这个对象的存在，就是最有可能是引起内存溢出的根源。通过GC Roots的引用链信息，就可以比较准确地定位出泄露代码的位置。

1.查询最大对象
图片
2.找出具体的对象
图片
解决方案
优化代码，去除大对象；
调整JVM内存大小（-Xmx与-Xms）；
超出GC开销限制
当出现java.lang.OutOfMemoryError: GC overhead limit exceeded异常信息时，表示超出了GC开销限制。当超过98%的时间用来做GC，但是却回收了不到2%的堆内存时会抛出此异常。

异常栈
[Full GC (Ergonomics)  19225K->19225K(19968K), 0.1044070 secs]
[Full GC (Ergonomics)  19227K->19227K(19968K), 0.0684710 secs]
java.lang.OutOfMemoryError: GC overhead limit exceeded
Dumping heap to D:\dump\java_pid17556.hprof ...
Heap dump file created [34925385 bytes in 0.132 secs]
Exception in thread "main" java.lang.OutOfMemoryError: GC overhead limit exceeded
[Full GC (Ergonomics)  19257K->933K(19968K), 0.0403569 secs]
 at com.xiaolyuh.HeapOutOfMemoryErrorTest.main(HeapOutOfMemoryErrorTest.java:25)
ERROR: JDWP Unable to get JNI 1.2 environment, jvm->GetEnv() return code = -2
JDWP exit error AGENT_ERROR_NO_JNI_ENV(183):  [util.c:840]
解决方案
通过-XX:-UseGCOverheadLimit参数来禁用这个检查，但是并不能从根本上来解决内存溢出的问题，最后还是会报出java.lang.OutOfMemoryError: Java heap space异常；
调整JVM内存大小（-Xmx与-Xms）；
虚拟机栈和本地方法栈溢出
如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常。
如果虚拟机在扩展栈时无法申请到足够的内存空间，则抛出OutOfMemoryError异常。
这里把异常分成两种情况，看似更加严谨，但却存在着一些互相重叠的地方：当栈空间无法继续分配时，到底是内存太小，还是已使用的栈空间太大，其本质上只是对同一件事情的两种描述而已。

StackOverflowError
出现StackOverflowError异常的主要原因有两点：

单个线程请求的栈深度大于虚拟机所允许的最大深度
创建的线程过多
单个线程请求的栈深度过大
单个线程请求的栈深度大于虚拟机所允许的最大深度，主要表现有以下几点：

存在递归调用
存在循环依赖调用
方法调用链路很深，比如使用装饰器模式的时候，对已经装饰后的对象再进行装饰
异常信息java.lang.StackOverflowError。

装饰器示例：

Collections.unmodifiableList(
        Collections.unmodifiableList(
                Collections.unmodifiableList(
                        Collections.unmodifiableList(
                                Collections.unmodifiableList(
                                                        ...)))))));
递归示例：

/**
 * java 虚拟机栈和本地方法栈内存溢出测试
 * <p>
 * VM Args: -Xss128k
 *
 * @author yuhao.wang3
 */
public class StackOverflowErrorErrorTest {
    private int stackLength = 0;

    public void stackLeak() {
        stackLength++;
        stackLeak();
    }

    public static void main(String[] args) {
        StackOverflowErrorErrorTest sof = new StackOverflowErrorErrorTest();
        try {
            sof.stackLeak();
        } catch (Exception e) {
            System.out.println(sof.stackLength);
            e.printStackTrace();
        }
    }
}
运行结果：

stackLength::1372
java.lang.StackOverflowError
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
...
当增大栈空间的时候我们就会发现，递归深度会增加，修改栈空间-Xss1m，然后运行程序，运行结果如下：

stackLength::20641
java.lang.StackOverflowError
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
...
修改递归方法的参数列表后递归深度急剧减少：

public void stackLeak(String ags1, String ags2, String ags3) {
    stackLength++;
    stackLeak(ags1, ags2, ags3);
}
运行结果如下：

stackLength::13154
java.lang.StackOverflowError
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
 at com.xiaolyuh.StackOverflowErrorErrorTest.stackLeak(StackOverflowErrorErrorTest.java:16)
...
由此可见影响递归的深度因素有：

单个线程的栈空间大小（-Xss）
局部变量表的大小
单个线程请求的栈深度超过内存限制导致的栈内存溢出，一般是由于非正确的编码导致的。从上面的示例我们可以看出，当栈空间在-Xss128k的时候，调用层级都在1000以上，一般情况下方法的调用是达不到这个深度的。如果方法调用的深度确实有这么大，那么我们可以通过-Xss配置来增大栈空间大小。

创建的线程过多
不断地建立线程也可能导致栈内存溢出，因为我们机器的总内存是有限制的，所以虚拟机栈和本地方法栈对应的内存也是有最大限制的。如果单个线程的栈空间越大，那么整个应用允许创建的线程数就越少。异常信息java.lang.OutOfMemoryError: unable to create new native thread。

虚拟机栈和本地方法栈内存 ≈ 操作系统内存限制 - 最大堆容量(Xmx) - 最大方法区容量(MaxPermSize)

过多创建线程示例：

/**
 * java 虚拟机栈和本地方法栈内存溢出测试
 * <p>
 * 创建线程过多导致内存溢出异常
 * <p>
 * VM Args: -verbose:gc -Xss20M -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
 *
 * @author yuhao.wang3
 * @since 2019/11/30 17:09
 */
public class StackOutOfMemoryErrorTest {
    private static int threadCount;

    public static void main(String[] args) throws Throwable {
        try {
            while (true) {
                threadCount++;
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            Thread.sleep(1000 * 60 * 10);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }).start();
            }
        } catch (Throwable e) {
            e.printStackTrace();
            throw e;
        } finally {
            System.out.println("threadCount=" + threadCount);
        }
    }
}
Java的线程是映射到操作系统的内核线程上，因此上述代码执行时有较大的风险，可能会导致操作系统假死。

运行结果：

java.lang.OutOfMemoryError: unable to create new native thread
 at java.lang.Thread.start0(Native Method)
 at java.lang.Thread.start(Thread.java:717)
 at StackOutOfMemoryErrorTest.main(StackOutOfMemoryErrorTest.java:17)
threadCount=4131
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
 at java.lang.Thread.start0(Native Method)
 at java.lang.Thread.start(Thread.java:717)
 at StackOutOfMemoryErrorTest.main(StackOutOfMemoryErrorTest.java:17)
需要重新上述异常，最好是在32位机器上，因为我在64位机器没有重现。

在有限的内存空间里面，当我们需要创建更多的线程的时候，我们可以减少单个线程的栈空间大小。

元数据区域的内存溢出
元数据区域或方法区是用于存放Class的相关信息，如类名、访问修饰符、常量池、字段描述、方法描述等。我们可以通过在运行时产生大量的类去填满方法区，直到溢出，如：代理的使用(CGlib)、大量JSP或动态产生JSP文件的应用（JSP第一次运行时需要编译为Java类）、基于OSGi的应用（即使是同一个类文件，被不同的加载器加载也会视为不同的类）等。

/**
 * java 元数据区域/方法区的内存溢出
 * <p>
 * VM Args JDK 1.6: set JAVA_OPTS=-verbose:gc -XX:PermSize=10m -XX:MaxPermSize=10m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
 * <p>
 * VM Args JDK 1.8: set JAVA_OPTS=-verbose:gc -Xmx20m -XX:MetaspaceSize=5m -XX:MaxMetaspaceSize=5m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
 *
 * @author yuhao.wang3
 */
public class MethodAreaOutOfMemoryErrorTest {

    static class MethodAreaOOM {
    }

    public static void main(String[] args) {
        while (true) {
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(MethodAreaOOM.class);
            enhancer.setCallback(new MethodInterceptor() {
                @Override
                public Object intercept(Object obj, Method method, Object[] params, MethodProxy proxy) throws Throwable {
                    return proxy.invokeSuper(obj, params);
                }
            });
            enhancer.create();
        }
    }
}
运行结果：

[GC (Last ditch collection)  1283K->1283K(16384K), 0.0002585 secs]
[Full GC (Last ditch collection)  1283K->1226K(19968K), 0.0075856 secs]
java.lang.OutOfMemoryError: Metaspace
Dumping heap to D:\dump\java_pid18364.hprof ...
Heap dump file created [2479477 bytes in 0.015 secs]
[GC (Metadata GC Threshold)  1450K->1354K(19968K), 0.0003906 secs]
[Full GC (Metadata GC Threshold)  1354K->976K(19968K), 0.0073752 secs]
[GC (Last ditch collection)  976K->976K(19968K), 0.0002921 secs]
[Full GC (Last ditch collection)  976K->973K(19968K), 0.0045243 secs]
Exception in thread "main" java.lang.OutOfMemoryError: Metaspace
 at java.lang.ClassLoader.defineClass1(Native Method)
 at java.lang.ClassLoader.defineClass(ClassLoader.java:763)
 at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
 at java.net.URLClassLoader.defineClass(URLClassLoader.java:467)
 at java.net.URLClassLoader.access$100(URLClassLoader.java:73)
 at java.net.URLClassLoader$1.run(URLClassLoader.java:368)
 at java.net.URLClassLoader$1.run(URLClassLoader.java:362)
 at java.security.AccessController.doPrivileged(Native Method)
 at java.net.URLClassLoader.findClass(URLClassLoader.java:361)
 at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
 at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
 at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
 at org.springframework.cglib.core.internal.LoadingCache.createEntry(LoadingCache.java:52)
 at org.springframework.cglib.core.internal.LoadingCache.get(LoadingCache.java:34)
 at org.springframework.cglib.core.AbstractClassGenerator$ClassLoaderData.get(AbstractClassGenerator.java:116)
 at org.springframework.cglib.core.AbstractClassGenerator.create(AbstractClassGenerator.java:291)
 at org.springframework.cglib.core.KeyFactory$Generator.create(KeyFactory.java:221)
 at org.springframework.cglib.core.KeyFactory.create(KeyFactory.java:174)
 at org.springframework.cglib.core.KeyFactory.create(KeyFactory.java:153)
 at org.springframework.cglib.proxy.Enhancer.<clinit>(Enhancer.java:73)
 at com.xiaolyuh.MethodAreaOutOfMemoryErrorTest.main(MethodAreaOutOfMemoryErrorTest.java:26)
运行时常量池的内存溢出
String.intern()是一个Native方法，它的作用是：如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象；否则，将此String对象包含的字符串添加到常量池中，并且返回此String对象的引用。

在JDK 1.6的时候，运行时常量池是在方法区中，所以直接限制了方法区中大小就可以模拟出运行池常量池的内存溢出。

/**
 * java 方法区和运行时常量池溢出
 * <p>
 * VM Args JDK 1.6: set JAVA_OPTS=-verbose:gc -XX:PermSize10 -XX:MaxPermSize10m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
 *
 * @author yuhao.wang3
 */
public class RuntimeConstantOutOfMemoryErrorTest {

    public static void main(String[] args) {
        // 使用List保存着常量池的引用，避免Full GC 回收常量池行为
        List<String> list = new ArrayList<>();
        for (int i = 0; ; i++) {
            list.add(String.valueOf(i).intern());
        }
    }
}
运行结果：

Exception in thread "main" java.lang.OutOfMemoryError: PermGen space
    at java.lang.String.intern(Native Method)
    at RuntimeConstantOutOfMemoryErrorTest.main(RuntimeConstantOutOfMemoryErrorTest.java:18)
直接内存溢出
DirectMemory容量可通过-XX:MaxDirectMemorySize指定，如果不指定，则默认与Java堆最大值（-Xmx指定）一样。

/**
 * java 直接内存溢出
 * <p>
 * VM Args JDK 1.6: set JAVA_OPTS=-verbose:gc -Xms20m -XX:MaxDirectMemorySize=10m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:\dump
 *
 * @author yuhao.wang3
 */
public class DirectMemoryOutOfMemoryErrorTest {

    public static void main(String[] args) throws IllegalAccessException {
        int _1M = 1024 * 1024;
        Field unsafeField = Unsafe.class.getDeclaredFields()[0];
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        while (true) {
            unsafe.allocateMemory(_1M);
        }
    }
}
运行结果：

Exception in thread "main" java.lang.OutOfMemoryError
 at sun.misc.Unsafe.allocateMemory(Native Method)
 at com.xiaolyuh.DirectMemoryOutOfMemoryErrorTest.main(DirectMemoryOutOfMemoryErrorTest.java:23)
由DirectMemory导致的内存溢出，一个明显的特征是在Heap Dump文件中不会看见明显的异常，如果读者发现OOM之后Dump文件很小，而程序中又直接或间接使用了NIO，那就可以考虑检查一下是不是这方面的原因。

解决方案
通过-XX:MaxDirectMemorySize指定直接内存大小。

源码

https://github.com/wyh-spring-ecosystem-student/spring-boot-student/tree/releases









【268期】美团面试题：当你的JVM 堆内存溢出后，其他线程是否可继续工作？
Java面试题精选 4月24日
图片

最近网上出现一个美团面试题：“一个线程OOM后，其他线程还能运行吗？”

我看网上出现了很多不靠谱的答案。这道题其实很有难度，涉及的知识点有jvm内存分配、作用域、gc等，不是简单的是与否的问题。

由于题目中给出的OOM，java中OOM又分很多类型；比如：堆溢出（“java.lang.OutOfMemoryError: Java heap space”）、永久带溢出（“java.lang.OutOfMemoryError:Permgen space”）、不能创建线程（“java.lang.OutOfMemoryError:Unable to create new native thread”）等很多种情况。

本文主要是分析堆溢出对应用带来的影响。

先说一下答案，答案是还能运行。

代码如下：

public class JvmThread {
    public static void main(String[] args) {
        new Thread(() -> {
            List<byte[]> list = new ArrayList<byte[]>();
            while (true) {
                System.out.println(new Date().toString() + Thread.currentThread() + "==");
                byte[] b = new byte[1024 * 1024 * 1];
                list.add(b);
                try {
                    Thread.sleep(1000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
 
        // 线程二
        new Thread(() -> {
            while (true) {
                System.out.println(new Date().toString() + Thread.currentThread() + "==");
                try {
                    Thread.sleep(1000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
结果展示：

Wed Nov 07 14:42:18 CST 2018Thread[Thread-1,5,main]==
Wed Nov 07 14:42:18 CST 2018Thread[Thread-0,5,main]==
Wed Nov 07 14:42:19 CST 2018Thread[Thread-1,5,main]==
Wed Nov 07 14:42:19 CST 2018Thread[Thread-0,5,main]==
Exception in thread "Thread-0" java.lang.OutOfMemoryError: Java heap space
at com.gosaint.util.JvmThread.lambda$main$0(JvmThread.java:21)
at com.gosaint.util.JvmThread$$Lambda$1/521645586.run(Unknown Source)
at java.lang.Thread.run(Thread.java:748)
Wed Nov 07 14:42:20 CST 2018Thread[Thread-1,5,main]==
Wed Nov 07 14:42:21 CST 2018Thread[Thread-1,5,main]==
Wed Nov 07 14:42:22 CST 2018Thread[Thread-1,5,main]==
JVM启动参数设置：

图片
图片
上图是JVM堆空间的变化。我们仔细观察一下在14:42:05~14:42:25之间曲线变化，你会发现使用堆的数量，突然间急剧下滑！这代表这一点，当一个线程抛出OOM异常后，它所占据的内存资源会全部被释放掉，从而不会影响其他线程的运行！

讲到这里大家应该懂了，此题的答案为一个线程溢出后，进程里的其他线程还能照常运行。注意了，这个例子我只演示了堆溢出的情况。如果是栈溢出，结论也是一样的，大家可自行通过代码测试。

总结：其实发生OOM的线程一般情况下会死亡，也就是会被终结掉，该线程持有的对象占用的heap都会被gc了，释放内存。因为发生OOM之前要进行gc，就算其他线程能够正常工作，也会因为频繁gc产生较大的影响。