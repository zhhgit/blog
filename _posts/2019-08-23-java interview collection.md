---
layout: post
title: "Java后端面试题整理"
description: Java后端面试题整理
modified: 2019-08-23
category: Resources
tags: [Resources]
---

# Java基础

1.==/equals()/hashCode()

(1)==比较左右两侧是否为同一对象，比较的是对象的地址。如果比较的是阿拉伯数字，则值相等则为true。
(2)equals()继承自Object对象，默认为==的比较。可Override。
(3)hashCode()继承自Object对象，用于计算对象散列值。Object对象默认hashCode为调用JVM的JNI方法，根据内存地址得到的值。
通常如果equals() override了，hashCode()也应该override，即equals相等，散列也应该相等。hashCode是用于散列数据的快速存取，如利用HashSet/HashMap/Hashtable类来存储数据时，都会根据存储对象的hashCode值来进行判断是否相同的。

当不会创建“类对应的散列表”：不会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中用到该类，例如不会创建该类的HashSet集合，在这种情况下，该类的“hashCode()和equals()没有半毛钱关系的。equals()用来比较该类的两个对象是否相等，而hashCode() 则根本没有任何作用。
当会创建“类对应的散列表”：会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中用到该类。例如会创建该类的HashSet集合。在这种情况下，该类的“hashCode()和equals()”是有关系的，如果两个对象相等，那么它们的hashCode()值一定相同。这里的相等是指，通过equals()比较两个对象时返回true。如果两个对象hashCode()相等，它们并不一定相等。因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。补充说一句：“两个不同的键值对，哈希值相等”，这就是哈希冲突。此外，在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。

2.运算

(1)Math.round()计算方法为先+0.5，然后向下取整。

(2)Java中float的精度为6-7位有效数字。double的精度为15-16位。我们在使用BigDecimal时，使用它的BigDecimal(String)构造器创建对象才有意义。其他的如BigDecimal b = new BigDecimal(1)这种，还是会发生精度丢失的问题。
所以我们一般使用BigDecimal来解决商业运算上丢失精度的问题的时候，声明BigDecimal对象的时候一定要使用它构造参数为String的类型的构造器。
同时这个原则Effective Java和MySQL 必知必会中也都有提及。float和double只能用来做科学计算和工程计算。商业运算中我们要使用BigDecimal。

    构造器                   描述
    BigDecimal(int)       创建一个具有参数所指定整数值的对象。
    BigDecimal(double)    创建一个具有参数所指定双精度值的对象。
    BigDecimal(long)      创建一个具有参数所指定长整数值的对象。
    BigDecimal(String)    创建一个具有参数所指定以字符串表示的数值的对象。
    
    函数：
    方法                    描述
    add(BigDecimal)       BigDecimal对象中的值相加，然后返回这个对象。
    subtract(BigDecimal)  BigDecimal对象中的值相减，然后返回这个对象。
    multiply(BigDecimal)  BigDecimal对象中的值相乘，然后返回这个对象。
    divide(BigDecimal)    BigDecimal对象中的值相除，然后返回这个对象。
    toString()            将BigDecimal对象的数值转换成字符串。
    doubleValue()         将BigDecimal对象中的值以双精度数返回。
    floatValue()          将BigDecimal对象中的值以单精度数返回。
    longValue()           将BigDecimal对象中的值以长整数返回。
    intValue()            将BigDecimal对象中的值以整数返回。
    
3.字符串

(1)反转：(a)字符数组反向拼接；(b)递归；(c)StringBuffer的reverse()方法

4.抽象

(1)抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。

5.Object类的方法

(1)Object():构造方法
(2)registerNatives():为了使JVM发现本机功能，他们被一定的方式命名。例如，对于java.lang.Object.registerNatives，对应的C函数命名为Java_java_lang_Object_registerNatives。通过使用registerNatives（或者更确切地说，JNI函数RegisterNatives），可以命名任何你想要你的C函数。
(3)clone():用来另存一个当前存在的对象。只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。
(4)getClass():final方法，用于获得运行时的类型。该方法返回的是此Object对象的类对象/运行时类对象Class。效果与Object.class相同。
(5)equals():用来比较两个对象的内容是否相等。默认情况下(继承自Object类)，equals和==是一样的，除非被覆写(override)了。
(6)hashCode():用来返回其所在对象的物理地址（哈希码值），常会和equals方法同时重写，确保相等的两个对象拥有相等的hashCode。作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。
(7)toString():返回该对象的字符串表示。
(8)wait():导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法。
(9)wait(long timeout):导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法，或者超过指定的时间量。
(10)wait(long timeout, int nanos):导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法，或者其他某个线程中断当前线程，或者已超过某个实际时间量。
(11)notify():唤醒在此对象监视器上等待的单个线程。
(12)notifyAll():唤醒在此对象监视器上等待的所有线程。
(13)finalize():当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。

6.Comparable与Comparator

如果实现类没有实现Comparable接口，又想对两个类进行比较（或者实现类实现了Comparable接口，但是对compareTo方法内的比较算法不满意），那么可以实现Comparator接口，自定义一个比较器，写比较算法。
实现Comparable接口的方式比实现Comparator接口的耦合性要强一些，如果要修改比较算法，要修改Comparable接口的实现类，而实现Comparator的类是在外部进行比较的，不需要对实现类有任何修改。因此：
对于一些普通的数据类型（比如 String, Integer, Double…），它们默认实现了Comparable接口，实现了compareTo方法，我们可以直接使用。
而对于一些自定义类，它们可能在不同情况下需要实现不同的比较策略，我们可以新创建Comparator接口，然后使用特定的Comparator实现进行比较。

N.参考

(1)[Java 7 API](https://docs.oracle.com/javase/7/docs/api/)

(2)[廖雪峰Java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)

# IO

1.InputStream：
三种基本的介质流ByteArrayInputStream,StringBufferInputStream,FileInputStream，它们分别从Byte数组、StringBuffer、和本地文件中读取数据。
PipedInputStream从与其它线程共用的管道中读取数据。
ObjectInputStream和所有FilterInputStream的子类都是装饰流。
FilterInputStream子类包括BufferedInputStream能为输入流提供缓冲区，DataInputStream可以从输入流中读取Java基本类型数据，PushBackInputStream可以把读取到的字节重新推回到InputStream中。

2.OutputStream：ByteArrayOutputStream、FileOutputStream是两种基本的介质流，它们分别向Byte数组和本地文件中写入数据。
PipedOutputStream是向与其它线程共用的管道中写入数据。
ObjectOutputStream 和所有FilterOutputStream的子类都是装饰流。

3.BIO/NIO/AIO

(a)BIO：阻塞。如果服务端连接多个客户端，则需要多个线程分别从套接字读取数据。
(b)NIO：非阻塞。NIO = I/O多路复用 + 非阻塞式I/O

4.NIO线程模型

(a)Reactor单线程模型：由一个线程监听连接事件、读写事件，并完成数据读写。
(b)Reactor多线程模型：一个Acceptor线程专门监听各种事件，再由专门的线程池负责处理真正的IO数据读写
(c)主从Reactor多线程模型：一个线程监听连接事件，线程池的多个线程监听已经建立连接的套接字的数据读写事件，另外和多线程模型一样有专门的线程池处理真正的IO操作。

5.File类

(1)创建：
createNewFile()在指定位置创建一个空文件，成功就返回true，如果已存在就不创建，然后返回false。
mkdir() 在指定位置创建一个单级文件夹。
mkdirs() 在指定位置创建一个多级文件夹。
renameTo(File dest)如果目标文件与源文件是在同一个路径下，那么renameTo的作用是重命名， 如果目标文件与源文件不是在同一个路径下，那么renameTo的作用就是剪切，而且还不能操作文件夹。

(2)删除：
delete() 删除文件或者一个空文件夹，不能删除非空文件夹，马上删除文件，返回一个布尔值。
deleteOnExit()jvm退出时删除文件或者文件夹，用于删除临时文件，无返回值。

(3)判断：
exists() 文件或文件夹是否存在。
isFile() 是否是一个文件，如果不存在，则始终为false。
isDirectory() 是否是一个目录，如果不存在，则始终为false。
isHidden() 是否是一个隐藏的文件或是否是隐藏的目录。
isAbsolute() 测试此抽象路径名是否为绝对路径名。

(4)获取：
getName() 获取文件或文件夹的名称，不包含上级路径。
getAbsolutePath()获取文件的绝对路径，与文件是否存在没关系
length() 获取文件的大小（字节数），如果文件不存在则返回0L，如果是文件夹也返回0L。
getParent() 返回此抽象路径名父目录的路径名字符串；如果此路径名没有指定父目录，则返回null。
lastModified()获取最后一次被修改的时间。

(5)文件夹相关：
static File[] listRoots()列出所有的根目录（Window中就是所有系统的盘符）
list() 返回目录下的文件或者目录名，包含隐藏文件。对于文件这样操作会返回null。
listFiles() 返回目录下的文件或者目录对象（File类实例），包含隐藏文件。对于文件这样操作会返回null。
list(FilenameFilter filter)返回指定当前目录中符合过滤条件的子文件或子目录。对于文件这样操作会返回null。
listFiles(FilenameFilter filter)返回指定当前目录中符合过滤条件的子文件或子目录。对于文件这样操作会返回null。

6.序列化与反序列化

(1)序列化：对象序列化的最主要的用处就是在传递和保存对象的时候，保证对象的完整性和可传递性。序列化是把对象转换成有序字节流，以便在网络上传输或者保存在本地文件中。核心作用是对象状态的保存与重建。
(2)反序列化：客户端从文件中或网络上获得序列化后的对象字节流，根据字节流中所保存的对象状态及描述信息，通过反序列化重建对象。

    public class ObjectOutputStreamDemo { //序列化
        public static void main(String[] args) throws Exception {
            //序列化后生成指定文件路径
            File file = new File("D:" + File.separator + "person.ser");
            ObjectOutputStream oos = null;
            //装饰流（流）
            oos = new ObjectOutputStream(new FileOutputStream(file));

            //实例化类，需要实现Serializabel接口
            Person per = new Person("张三", 30);
            oos.writeObject(per); //把类对象序列化
            oos.close();
        }
    }
    
7.IO模型

blocking：对于network io来说，很多时候数据在一开始还没有到达（比如，还没有收到一个完整的UDP包），这个时候kernel就要等待足够的数据到来。而在用户进程这边，整个进程会被阻塞。当kernel一直等到数据准备好了，它就会将数据从kernel中拷贝到用户内存，然后kernel返回结果，用户进程才解除block的状态，重新运行起来。
non-blocking：当用户进程发出read操作时，如果kernel中的数据还没有准备好，那么它并不会block用户进程，而是立刻返回一个error。从用户进程角度讲 ，它发起一个read操作后，并不需要等待，而是马上就得到了一个结果。用户进程判断结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦kernel中的数据准备好了，并且又再次收到了用户进程的system call，那么它马上就将数据拷贝到了用户内存，然后返回。
IO multiplexing：当用户进程调用了select，那么整个进程会被block，而同时，kernel会“监视”所有select负责的socket，当任何一个socket中的数据准备好了，select就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程。这个图和blocking IO的图其实并没有太大的不同，事实上，还更差一些。因为这里需要使用两个system call (select 和 recvfrom)，而blocking IO只调用了一个system call (recvfrom)。但是，用select的优势在于它可以同时处理多个connection。
Asynchronous I/O：用户进程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从kernel的角度，当它受到一个asynchronous read之后，首先它会立刻返回，所以不会对用户进程产生任何block。然后，kernel会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，kernel会给用户进程发送一个signal，告诉它read操作完成了。

# 容器

1.Java容器

    Collection
        --Set(继承)
            --AbstractSet(继承)
                --HashSet(实现)
                    --LinkedHashSet(继承)
            --SortedSet(继承)
                --TreeSet(实现，同时实现了AbstractSet)

        --List(继承)
            --AbstractList(继承)
                --Vector(实现)
                    --Stack(继承)
                --ArrayList(实现)
                --LinkedList(实现，同时实现Queue)

        --Queue(继承)
            --PriorityQueue(实现)
            --LinkedList(实现，同时实现List)

    Map
        --AbstractMap(继承)
            --HashMap(实现)
                --LinkedHashMap(继承)
            --HashTable(实现)
            --WeakHashMap(实现)
            --IdentityHashMap(实现)
        --SortedMap(继承)
            --TreeMap(实现，同时实现了AbstractMap)


2.Collection和Collections有什么区别

Collection是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式，其直接继承接口有List与Set。
Collections则是集合类的一个工具类，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。
常见的函数

    sort(Collection),shuffle(Collection),reverse(Collection),
    fill(Collection,Object),copy(List, List),rotate(Collection,int),swap(List,int,int),
    indexOfSublist(List,List),lastIndexOfSublist(List,List),max(Collection,Comparator),min(Collection,Comparator)

3.HashMap与TreeMap

(1)TreeMap<K,V>的Key值是要求实现java.lang.Comparable，所以迭代的时候TreeMap默认是按照Key值升序排序的；TreeMap的实现是基于红黑树结构。适用于按自然顺序或自定义顺序遍历键（key）。添加到SortedMap实现类的元素必须实现Comparable接口，否则必须给它的构造函数提供一个Comparator接口的实现。
基于红黑树实现。TreeMap没有调优选项，因为该树总处于平衡状态。

(a)TreeMap()：构建一个空的映像树
(b)TreeMap(Map m): 构建一个映像树，并且添加映像m中所有元素
(c)TreeMap(Comparator c): 构建一个映像树，并且使用特定的比较器对关键字进行排序
(d)TreeMap(SortedMap s): 构建一个映像树，添加映像树s中所有映射，并且使用与有序映像s相同的比较器排序

(2)HashMap<K,V>的Key值实现散列hashCode()，分布是散列的、均匀的，不支持排序；数据结构主要是桶(数组)，链表或红黑树。适用于在Map中插入、删除和定位元素。
HashMap：基于哈希表实现。使用HashMap要求添加的键类明确定义了hashCode()和equals()，可以重写hashCode()和equals()，为了优化HashMap空间的使用，可以调优初始容量和负载因子。

(a)HashMap(): 构建一个空的哈希映像
(b)HashMap(Map m): 构建一个哈希映像，并且添加映像m的所有映射
(c)HashMap(int initialCapacity): 构建一个拥有特定容量的空的哈希映像
(d)HashMap(int initialCapacity, float loadFactor): 构建一个拥有特定容量和加载因子的空的哈希映像

4.HashMap怎样解决hash冲突

HashMap基于哈希表的Map接口的实现。此实现提供所有可选的映射操作，并允许使用null值和null键。（除了不同步和允许使用null之外，HashMap类与Hashtable大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。
值得注意的是HashMap不是线程安全的，如果想要线程安全的HashMap，可以通过Collections类的静态方法synchronizedMap获得线程安全的HashMap。

数据结构:HashMap的底层主要是基于数组和链表来实现的，它之所以有相当快的查询速度主要是因为它是通过计算散列码来决定存储的位置。HashMap中主要是通过key的hashCode来计算hash值的，只要hashCode相同，计算出来的hash值就一样。如果存储的对象对多了，就有可能不同的对象所算出来的hash值是相同的，这就出现了所谓的hash冲突。
解决hash冲突的方法有很多，HashMap底层是通过链表来解决hash冲突的。哈希数组中每个元素都是一个单链表的头节点，链表是用来解决冲突的，如果不同的key映射到了数组的同一位置处，就将其放入单链表中。

loadFactor加载因子是表示Hash表中元素的填满的程度。
若:加载因子越大,填满的元素越多,好处是,空间利用率高了,但:冲突的机会加大了。链表长度会越来越长,查找效率降低。
反之,加载因子越小,填满的元素越少,好处是:冲突的机会减小了,但:空间浪费多了。表中的数据将过于稀疏（很多空间还没用，就开始扩容了）。
冲突的机会越大,则查找的成本越高。因此,必须在 "冲突的机会"与"空间利用率"之间寻找一种平衡与折衷.。这种平衡与折衷本质上是数据结构中有名的"时-空"矛盾的平衡与折衷.
如果机器内存足够，并且想要提高查询速度的话可以将加载因子设置小一点；相反如果机器内存紧张，并且对查询速度没有什么要求的话可以将加载因子设置大一点。不过一般我们都不用去设置它，让它取默认值0.75就好了。

HashMap是线程不安全的，其主要体现:在jdk1.7中，在多线程环境下，扩容时会造成环形链或数据丢失。在jdk1.8中，在多线程环境下，会发生数据覆盖的情况。

5.ArrayList和LinkedList

(1)对ArrayList和LinkedList而言，在列表末尾增加一个元素所花的开销都是固定的。
对ArrayList而言，主要是在内部数组中增加一项，指向所添加的元素，偶尔可能会导致对数组重新进行分配；而对LinkedList而言，这个开销是统一的，分配一个内部Entry对象。
(2)在ArrayList的中间插入或删除一个元素意味着这个列表中剩余的元素都会被移动；而在LinkedList的中间插入或删除一个元素的开销是固定的。
(3)LinkedList不支持高效的随机元素访问。
(4)ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗相当的空间。

总结：当操作是在一列数据的后面添加数据而不是在前面或中间,并且需要随机地访问其中的元素时,使用ArrayList会有更好的性能；当操作是在一列数据的前面或中间添加或删除数据,并且按照顺序访问其中的元素时,就应该使用LinkedList了。

N.参考

(1)[Java容器详解](https://www.jianshu.com/p/8ef342da8732)

(2)[Java容器（接口）](https://cloud.tencent.com/developer/article/1334703)

(3)[Java容器（实现）](https://cloud.tencent.com/developer/article/1334702)

(4)[【16期】你能谈谈HashMap怎样解决hash冲突吗](https://mp.weixin.qq.com/s/dgYriZ-obbm1CoUDjHofhg)

# 反射

1.什么是反射

在jvm运行阶段，动态的获取类的信息（字节码实例，构造器，方法，字段），动态进行对象的创建，方法执行，字段操作。
对象有编译类型和运行类型，Object obj = new java.util.Date();编译类型Object，运行类型java.util.Date。如果对象obj调用Date类中的一个方法toLocaleString，编译阶段去编译类型Object中检查是否有该方法，若没有则编译失败。

2.获取Class实例三种方式

在反射操作某一个类之前，应该先获取这个类的字节码实例（同一个类在JVM的字节码实例只有一份），有三种方式

(1)MyDemo.class
(2)myDemoObj.getClass()
(3)Class.forName("somepackage.MyDemo")

3.字节码实例

bype/char/short/int/long/boolean/float/double.class及void.class
数组int[].class及String[].class
对象MyObject.class

4.常用方法

    Class clazz = Class.forName("className"); // className必须为全名，也就是得包含包名
    Object obj= clazz.newInstance(); //如果类有无参数公共构造函数，直接可以使用类的字节码实例就创建对象的实例。否则用constructor对象调用newInstance(Object... initargs)

    //--------------------------- Class对象的方法（即clazz的方法）---------------------------
    // 构造器Constructor有newInstance方法
    Constructor getConstructor(Class[] params) //根据指定参数获得public构造器
    Constructor[] getConstructors() //获得public的所有构造器
    Constructor getDeclaredConstructor(Class[] params) //根据指定参数获得public和非public的构造器
    Constructor[] getDeclaredConstructors() //获得public的所有构造器

    // 方法Method有invoke方法
    Method getMethod(String name, Class[] params) //根据方法名，参数类型获得方法
    Method[] getMethods() //获得所有的public方法
    Method getDeclaredMethod(String name, Class[] params) //根据方法名和参数类型，获得public和非public的方法
    Method[] getDeclaredMethods() //获得所以的public和非public方法

    // 属性Field有get,set方法
    Field getField(String name) //根据变量名得到相应的public变量
    Field[] getFields() //获得类中所以public的方法
    Field getDeclaredField(String name) //根据方法名获得public和非public变量
    Field[] getDeclaredFields() //获得类中所有的public和非public方法

N.参考

(1)[什么是反射](https://www.cnblogs.com/abcdjava/p/11146473.html)

# Java Web

1.JSP和Servlet

JSP是Servlet技术的扩展，本质上就是Servlet的简易方式。JSP编译后是“类servlet”。Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件。JSP侧重于视图，Servlet主要用于控制逻辑。
JSP执行过程：当服务器启动后，当Web浏览器端发送过来一个页面请求时，Web服务器先判断是否是JSP页面请求。如果该页面只是一般的HTML/XML页面请求，则直接将HTML/XML页面代码传给Web浏览器端。如果请求的页面是JSP页面，则由JSP引擎检查该JSP页面，如果该页面是第一次被请求、或不是第一次被请求但已被修改，则JSP引擎将此JSP页面代码转换成Servlet代码，然后JSP引擎调用服务器端的Java编译器对Servlet代码进行编译，把它变成字节码(.class)文件，然后再调用JAVA虚拟机执行该字节码文件，然后将执行结果传给Web浏览器端。如果该JSP页面不是第一次被请求，且没有被修改过，则直接由JSP引擎调用JAVA虚拟机执行已编译过的字节码.class文件，然后将结果传送Web浏览器端。

2.JSP的4种作用域

(1)page:代表页面上下文，范围是一个页面及其静态包含的内容
(2)request:代表请求上下文，范围是一个请求涉及的几个页面，通常是一个页面和其包含的内容以及forward动作转向的页面
(3)session:代表客户的一次会话上下文，范围是一个用户在会话有效期内多次请求所涉及的页面
(4)application:全局作用域，代表Web应用程序上下文，范围是整个Web应用中所有请求所涉及的页面

3.JSP内置对象

pageContext:网页的属性在这里管理。
page：表示从该页面产生的一个servlet实例。
out：是javax.jsp.JspWriter的一个实例，并提供了几个方法使你能用于向浏览器回送输出结果。
config：表示一个javax.servlet.ServletConfig对象，该对象用于存取servlet实例的初始化参数。
request:表示HttpServletRequest对象，它包含了有关浏览器请求的信息，并且提供了几个用于获取cookie,header和session数据的有用方法。
response:表示HttpServletResponse对象，并提供了几个用于设置送回浏览器的响应的方法（如cookies,头信息等。）
session：表示一个请求的javax.servlet.http.HttpSession对象，session可以存储用户的状态信息。
application:表示一个javax.servlet.ServletContext对象，这有助于查找有关servlet引擎和servlet环境的信息。
exception：针对错误网页，未捕捉的例外。

N.参考

(1)[JSP内置对象——pageContext对象](https://www.jellythink.com/archives/208)

(2)[JSP九大内置对象及其作用域](https://my.oschina.net/hp2017/blog/1932026)

# 异常

1.throw和throws的区别？
(1)throws出现在方法函数头；而throw出现在函数体。
(2)throws表示出现异常的一种可能性，并不一定会发生这些异常；throw则是抛出了异常，执行throw则一定抛出了某种异常对象。
(3)两者都是消极处理异常的方式（这里的消极并不是说这种方式不好），只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。

# 多线程

1.并发与并行
并发，指的是多个事情，在同一时间段内同时发生了。并行，指的是多个事情，在同一时间点上同时发生了。
并发的多个任务之间是互相抢占资源的。并行的多个任务之间是不互相抢占资源的。只有在多CPU的情况中，才会发生并行。否则，看似同时发生的事情，其实都是并发执行的。

2.线程和进程的区别

进程：计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。在早期面向进程设计的计算机结构中，进程是程序的基本执行实体；在当代面向线程设计的计算机结构中，进程是线程的容器。程序是指令、数据及其组织形式的描述，进程是程序的实体。
线程：进程的一个实体，是CPU调度和分派的基本单位，它是比进程更小的能独立运行的基本单位。线程自己基本上不拥有系统资源，只拥有一点在运行中必不可少的资源(如程序计数器，一组寄存器和栈)，但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源。

区别：进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

(1)简而言之，一个程序至少有一个进程，一个进程至少有一个线程。
(2)线程的划分尺度小于进程，使得多线程程序的并发性高。
(3)另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
(4)线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
(5)从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

3.守护线程

守护线程（即daemon thread），是个服务线程，准确地来说就是服务其他的线程，这是它的作用——而其他的线程只有一种，那就是用户线程。所以java里线程分2种：守护线程，比如垃圾回收线程，就是最典型的守护线程。用户线程，就是应用程序里的自定义线程。
当JVM中不存在任何一个正在运行的非守护线程时，则JVM进程即会退出。

4.创建线程方式

(1)继承Thread类
(2)实现Runnable接口
(3)应用程序可以使用Executor框架来创建线程池

实现Runnable接口这种方式更受欢迎，因为这不需要继承Thread类。在应用设计中已经继承了别的对象的情况下，这需要多继承（而Java不支持多继承），只能实现接口。同时，线程池也是非常高效的，很容易实现和使用。

5.线程状态

(1)新建(new)：新创建了一个线程对象。
(2)可运行(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权。
(3)运行(running)：可运行状态(runnable )的线程获得了CPU时间片（timeslice），执行程序代码。
(4)阻塞(blocked)：阻塞状态是指线程因为某种原因放弃了CPU使用权，也即让出了CPU timeslice ，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice转到运行(running)状态。
阻塞的情况分三种：
等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。
同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。
其他阻塞: 运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。
(5)死亡(terminated)：线程run()、 main()方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

6.同步方法和同步代码块的区别

同步方法默认用this或者当前类class对象作为锁；
同步代码块可以选择以什么来加锁，比同步方法要更细颗粒度，我们可以选择只同步会发生同步问题的部分代码而不是整个方法；

7.死锁

两个线程或两个以上线程都在等待对方执行完毕才能继续往下执行的时候就发生了死锁。结果就是这些线程都陷入了无限的等待中。
多线程产生死锁的四个必要条件：
互斥条件：一个资源每次只能被一个进程使用。
保持和请求条件：一个进程因请求资源而阻塞时，对已获得资源保持不放。
不可剥夺性：进程已获得资源，在未使用完成前，不能被剥夺。
循环等待条件（闭环）：若干进程之间形成一种头尾相接的循环等待资源关系。
只要破坏其中任意一个条件，就可以避免死锁，一种非常简单的避免死锁的方式就是：指定获取锁的顺序，并强制线程按照指定的顺序获取锁。因此，如果所有的线程都是以同样的顺序加锁和释放锁，就不会出现死锁了。

8.监视器

监视器和锁在Java虚拟机中是一块使用的。监视器监视一块同步代码块，确保一次只有一个线程执行同步代码块。每一个监视器都和一个对象引用相关联。线程在获取锁之前不允许执行同步代码。java还提供了显式监视器(Lock)和隐式监视器(synchronized)两种锁方案。

9.线程池的好处

(1)线程池的重用:线程的创建和销毁的开销是巨大的，而通过线程池的重用大大减少了这些不必要的开销，当然既然少了这么多消费内存的开销，其线程执行速度也是突飞猛进的提升。
(2)控制线程池的并发数:控制线程池的并发数可以有效的避免大量的线程池争夺CPU资源而造成堵塞。
(3)线程池可以对线程进行管理:线程池可以提供定时、定期、单线程、并发数控制等功能。

10.具体线程池

(1)ThreadPoolExecutor，构造函数如下

    public ThreadPoolExecutor(int corePoolSize,  
                              int maximumPoolSize,  
                              long keepAliveTime,  
                              TimeUnit unit,  
                              BlockingQueue<Runnable> workQueue,  
                              ThreadFactory threadFactory,  
                              RejectedExecutionHandler handler)
                               
七个参数的含义：
corePoolSize 线程池中核心线程的数量；
maximumPoolSize 线程池中最大线程数量；
keepAliveTime 非核心线程的超时时长，当系统中非核心线程闲置时间超过keepAliveTime之后，则会被回收。如果ThreadPoolExecutor的allowCoreThreadTimeOut属性设置为true，则该参数也表示核心线程的超时时长；
unit 第三个参数的单位，有纳秒、微秒、毫秒、秒、分、时、天等；
workQueue 线程池中的任务队列，该队列主要用来存储已经被提交但是尚未执行的任务。存储在这里的任务是由ThreadPoolExecutor的execute方法提交来的。
threadFactory 为线程池提供创建新线程的功能，这个我们一般使用默认即可；
handler 拒绝策略，当线程无法执行新任务时（一般是由于线程池中的线程数量已经达到最大数或者线程池关闭导致的），默认情况下，当线程池无法处理新线程时，会抛出一个RejectedExecutionException。

当currentSize<corePoolSize时，没什么好说的，直接启动一个核心线程并执行任务。
当currentSize>=corePoolSize、并且workQueue未满时，添加进来的任务会被安排到workQueue中等待执行。
当workQueue已满，但是currentSize<maximumPoolSize时，会立即开启一个非核心线程来执行任务。
当currentSize>=corePoolSize、workQueue已满、并且currentSize>maximumPoolSize时，调用handler默认抛出RejectExecutionExpection异常。

(2)FixedThreadPool:Fixed中文解释为固定。结合在一起解释固定的线程池，说的更全面点就是，有固定数量线程的线程池。其corePoolSize=maximumPoolSize，且keepAliveTime为0，适合线程稳定的场所。
(3)SingleThreadPool:Single中文解释为单一。结合在一起解释单一的线程池，说的更全面点就是，有固定数量线程的线程池，且数量为一，从数学的角度来看SingleThreadPool应该属于FixedThreadPool的子集。其corePoolSize=maximumPoolSize=1,且keepAliveTime为0，适合线程同步操作的场所。
(4)CachedThreadPool:Cached中文解释为储存。结合在一起解释储存的线程池，说的更通俗易懂，既然要储存，其容量肯定是很大，所以他的corePoolSize=0，maximumPoolSize=Integer.MAX_VALUE(2^32-1一个很大的数字)
(5)ScheduledThreadPool:Scheduled中文解释为计划。结合在一起解释计划的线程池，顾名思义既然涉及到计划，必然会涉及到时间。所以ScheduledThreadPool是一个具有定时定期执行任务功能的线程池。

N.参考

(1)[线程进程区别](https://www.cnblogs.com/toria/p/11123130.html)

(2)[线程与进程的区别](https://www.cnblogs.com/cocoxu1992/p/10468317.html)

(3)[谈谈什么是守护线程以及作用](https://www.jianshu.com/p/3d6f32af5625)

# JVM

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
栈使用的是一级缓存， 他们通常都是被调用时处于存储空间中，调用完毕立即释放。
堆则是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定（并不是一旦成为孤儿对象就能被回收）。所以调用这些对象的速度要相对来得低一些。

4.如何判断一个对象是否存活/GC对象的判定方法

可达性分析算法:基本思路是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），如果目标对象到GC Roots是连接着的，我们则称该目标对象是可达的，如果目标对象不可达，则说明目标对象是可以被回收的对象。
可以作为 GC Root的对象可以主要分为四种。(1)JVM栈中引用的对象；(2)方法区中，静态属性引用的对象；(3)方法区中，常量引用的对象；(4)本地方法栈中，JNI（即Native方法）引用的对象。

回收方法区:Java虚拟机规范中确实说过可以不要求虚拟机在方法区中实现垃圾回收，而且在方法区中进行垃圾回收的“性价比”一般比较低，方法区的垃圾收集主要回收两部分内容：废弃的常量和无用的类。
废弃的常量，以常量池中字面量的回收为例，假如一个字符串“abc”已经进入常量池中，但是当前系统已经没有任何一个String对象叫做“abc”的，也没有任何其他地方引用这个字面量，这个“abc”常量就会被清理出常量池。
判断一个无用的类需要同时满足下面3个条件才能算是“无用的类”:a)该类的所有实例都已经被回收，b)加载该类的ClassLoader已经被回收，c)该类对应的java.lang.Class对象已经没有任何地方被引用，无法在任何地方通过反射访问该类的方法。

对象死亡过程：即使在可达性分析算法中不可达的对象，也并非是“非死不可”的，这时候它们暂时处于“缓刑”阶段，要真正宣告一个对象死亡，至少要经历两次标记过程：如果对象在进行可达性分析后发现没有与GC Roots相连接的引用，那它将会被第一次标记并且进行一次筛选，筛选的条件是此对象是否是否有必要执行finalize()方法。当对象没有覆盖finalize()方法，或者finalize()方法已经被虚拟机调用过，虚拟机将这两种情况都视为“没有必要执行”。
如果这个对象被判定为有必要执行finalize()方法，那么这个对象将会放置在一个叫做F-Queue的队列之中。并在稍后由一个虚拟机自动建立的，低优先级的Finalizer线程去执行它。这里所谓“执行”是指虚拟机会触发这个方法，但并不承诺会等待它运行结束，这样做的原因是，如果有一个对象在finalize()方法中执行缓慢，或者发生死循环，将可能会导致F-Queue队列中其他对象永久处于等待，甚至导致整个内存回收系统崩溃。
finalize()方法是对象逃脱死亡命运的最后一次机会，稍后GC将对F-Queue中的对象进行第二次小规模的标记，如果对象这个时候，未被重新引用，那它基本上就真的被回收了。

5.JVM的内存被分为了三个主要部分：新生代，老年代和永久代。

(1)新生代：所有新产生的对象全部都在新生代中，Eden区保存最新的对象，有两个 SurvivorSpace——S1和S0，三个区域的比例大致为 8:1:1。当新生代的Eden区满了，将触发一次GC，我们把新生代中的GC称为minor garbage collections。minor gc是一种 Stop the world事件，指发生在新生代的垃圾收集动作，主要是由于Eden区域不够分配了。大多数Java对象都是朝生夕灭，所以Minor GC是非常频繁的，一般回收速度也比较快。
当eden区满了，触发minor gc，这时还有被引用的对象，就会被分配到S0区域，剩下没有被引用的对象就都会被清除。
再一次GC时，S0区的部分对象很可能会出现没有引用的，被引用的对象以及S0中的存活对象，会被一起移动到S1中。eden和S0中的未引用对象会被全部清除。
接下来就是无限循环上面的步骤了，当新生代中存活的对象超过了一定的【年龄】，会被分配至老年代的Tenured区中。这个年龄可以通过参数MaxTenuringThreshold设定，默认值为15。

(2)老年代：老年代用来存储活时间较长的对象，老年代区域的GC是major garbage collection，老年代中的内存不够时，就会触发一次。这也是一个stop the world事件，但是看名字就知道，这个回收过程会相当慢，因为这包括了对新生代和老年代所有对象的回收，也叫FullGC。
老年代管理内存最早采用的算法为标记-清理算法，这个算法很好理解，结合GC Root的定义，我们会把所有不可达的对象全部标记进行清除。
这个算法的劣势很好理解：对，会在标记清除的过程中产生大量的内存碎片，Java在分配内存时通常是按连续内存分配，这样我们会浪费很多内存。所以，现在的JVM GC在老年代都是使用标记-压缩清除方法，在清除后的内存进行整理和压缩，以保证内存连续，虽然这个算法的效率是三种算法里最低的。

(3)永久代：永久代位于方法区，主要存放元数据，例如Class、 Method的元信息，与GC要回收的对象其实关系并不是很大，我们可以几乎忽略其对GC的影响。除了JavaHotSpot这种较新的虚拟机技术，会回收无用的常量和的类，以免大量运用反射这类频繁自定义ClassLoader的操作时方法区溢出。

6.GC收集器

(1)Serial：使用了标记-复制的算法，可以用 -XX:+UseSerialGC使用单线程的串行收集器。但是在GC进行时，程序会进入长时间的暂停时间，一般不太建议使用。

(2)Parallel：-XX:+UseParallelGC-XX:+UseParallelOldGCParallel也使用了标记-复制的算法，但是我们称之为吞吐量优先的收集器，因为Parallel最主要的优势在于并行使用多线程去完成垃圾清理工作，这样可以充分利用多核的特性，大幅降低gc时间。当你的程序场景吞吐量较大，例如消息队列这种应用，需要保证有效利用CPU资源，可以忍受一定的停顿时间，可以优先考虑这种方式。

(3)CMS (ConcurrentMarkSweep)：-XX:+UseParNewGC-XX:+UseConcMarkSweepGC CMS使用了标记-清除的算法，当应用尤其重视服务器的响应速度（比如Apiserver），希望系统停顿时间最短，以给用户带来较好的体验，那么可以选择CMS。CMS收集器在MinorGC时会暂停所有的应用线程，并以多线程的方式进行垃圾回收。在FullGC时不暂停应用线程，而是使用若干个后台线程定期的对老年代空间进行扫描，及时回收其中不再使用的对象。

(4)G1（GarbageFirst）：-XX:+UseG1GC 在堆比较大的时候，如果full gc频繁，会导致停顿，并且调用方阻塞、超时、甚至雪崩的情况出现，所以降低full gc的发生频率和需要时间，非常有必要。G1的诞生正是为了降低FullGC的次数，而相较于CMS， G1使用了标记-压缩清除算法，这可以大大降低较大内存（4GB以上） GC时产生的内存碎片。

G1提供了两种GC模式，YoungGC和MixedGC，两种都是StopTheWorld(STW)的。YoungGC主要是对Eden区进行GC，MixGC不仅进行正常的新生代垃圾收集，同时也回收部分后台扫描线程标记的老年代分区。
另外有趣的一点，G1将新生代、老年代的物理空间划分取消了，而是将堆划分为若干个区域（region），每个大小都为2的倍数且大小全部一致，最多有2000个。除此之外， G1专门划分了一个Humongous区，它用来专门存放超过一个region 50%大小的巨型对象。在正常的处理过程中，对象从一个区域复制到另外一个区域，同时也完成了堆的压缩。

N.参考

(1)[讲一讲JVM的组成](https://www.cnblogs.com/vipstone/p/10681211.html)

(2)[java堆、栈、堆栈，常量池的区别，史上最全总结](https://cloud.tencent.com/developer/article/1453511)

(3)[数据结构：堆](https://www.jianshu.com/p/6b526aa481b1)

# Spring

1.使用Spring的好处和原因
(1)低侵入式设计，代码污染极低。
(2)独立于各种应用服务器，基于Spring框架的应用，可以真正实现Write Once,Run Anywhere的承诺。
(3)Spring的DI机制降低了业务对象替换的复杂性，提高了组件之间的解耦。IOC（控制反转）创建对象不是通过new方式来实现，而是交给Spring配置来创建对象。
(4)Spring的AOP（面向切面编程）支持允许将一些通用任务如安全、事务、日志等进行集中式管理，从而提供了更好的复用。
(5)Spring的ORM和DAO提供了与第三方持久层框架的良好整合，并简化了底层的数据库访问。
(6)Spring并不强制应用完全依赖于Spring，开发者可自由选用Spring框架的部分或全部。Spring是开源的轻量级一站式框架，内部支持对多种优秀开源框架的集成。

2.Spring，SpringMVC，SpringBoot，SpringCloud的区别和联系

Spring是核心，提供了基础功能。
Spring MVC是基于Spring的一个MVC框架。
Spring Boot是为简化Spring配置的快速开发整合包。
Spring Cloud是构建在Spring Boot之上的服务治理框架。

3.Spring Bean的生命周期

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

4.Spring Boot特征

(1)创建独立的Spring应用。
(2)嵌入式Tomcat、 Jetty、 Undertow容器（无需部署war文件）。
(3)提供的starters简化构建配置。
(4)尽可能自动配置 spring应用。
(5)提供生产指标,例如指标、健壮检查和外部化配置。
(6)完全没有代码生成和XML配置要求。

N.参考

(1)[Spring 官网](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)

(2)[Spring Initializer](https://start.spring.io/)

(3)[详解IOC](https://www.cnblogs.com/xb1223/p/10148503.html)

(4)[详解AOP](https://www.cnblogs.com/xb1223/p/10169220.html)

(5)[面试题思考：解释一下什么叫AOP](https://www.cnblogs.com/songanwei/p/9417343.html)

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

1.#{}与${}差别

(1)#将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。$将传入的数据直接显示生成在sql中。
(2)#方式在很大程度上能够防止sql注入。$方式无法防止sql注入。一般能用#的就别用$。$方式一般用于传入数据库对象，例如传入表名。
(3)#在预处理时，会把参数部分用一个占位符？代替。$只会做简单的字符串替换，在动态SQL解析阶段将会进行变量替换。

2.Mybatis分页
(1)数组分页查询所有然后取subList。
(2)sql的limit语法。
(3)使用Mybatis的RowBounds(int offset, int limit)，不适合数据量大。
(4)自己实现拦截器，对执行的sql语句加limit条件。

# 数据库

1.三大范式

(1)1NF：每一列属性都是不可再分的属性值，确保每一列的原子性。
(2)2NF：满足2NF的前提是必须满足1NF。此外，关系模式需要包含两部分内容，一是必须有一个（及以上）主键；二是没有包含在主键中的列必须全部依赖于全部主键，而不能只依赖于主键的一部分而不依赖全部主键。
非主键列全部依赖于部分主键，非主键列部分依赖于全部主键，非主键列部分依赖于部分主键都是不符合2NF的。
(3)3NF：满足3NF的前提是必须满足2NF。另外关系模式的非主键列必须直接依赖于主键，不能存在传递依赖。即不能存在：非主键列m既依赖于全部主键，又依赖于非主键列n的情况。

2.limit用法

SELECT * FROM table LIMIT [offset,] rows | rows OFFSET offset。解释：筛选出结果的第offset行后的rows行。如果offset不填也是可以的，默认为0。

    select * from tbl_user limit 1,5; // 跳过1行，从第2行开始的5行
    select * from tbl_user limit 6; // 从第1行开始的6行
    select * from tbl_user limit 5 offset 1; // 跳过1行，从第2行开始的5行
    
3.数据库版本

select version();

4.一张自增表里面总共有7条数据，删除了最后2条数据，重启MySQL数据库，又插入了一条数据，此时id是几？

如果表的类型是InnoDB，不重启mysql的情况下这条记录的id是8。但是如果重启这条记录的ID是6。因为InnoDB表只把自增主键的最大ID记录到内存中，所以重启数据库或者对表OPTIMIZE操作，都会使最大ID丢失。
如果表的类型是MyISAM，那么这条记录的ID就是8。因为MylSAM表会把自增主键的最大ID记录到数据文件里面，重启MYSQL后，自增主键的最大ID也不会丢失。

5.存储过程

    create procedure insertInnoDb()
    begin
    set @i = 1;
    while @i <= 1000000
    do
    insert into testinnodb(name) values(concat("wy", @i));
    set @i = @i + 1;
    end while;
    end

调用存储过程。对于存储引擎为InnoDB的表。默认开启了autocommit = 1。调用存储过程时，需要先关闭，调用存储过程，再开启。

    set autocommit = 0;
    call insertInnoDb;
    set autocommit = 1;
    
6.数据库事务ACID原则

原子性(Atomicity)：是指一个事务要么全部执行，要么不执行，也就是说一个事务不可能只执行了一半就停止了。
一致性(Consistency)：是指事务的运行并不改变数据库中数据的一致性。例如，完整性约束了a+b=10，一个事务改变了a，那么b也应该随之改变。
独立性(Isolation）：事务的独立性也有称作隔离性，是指两个以上的事务不会出现交错执行的状态。因为这样可能会导致数据不一致。
持久性(Durability）：事务的持久性是指事务执行成功以后，该事务对数据库所作的更改便是持久的保存在数据库之中，不会无缘无故的回滚。

7.事务隔离级别

查看事务隔离级别使用select @@tx_isolation
四个隔离级别：read uncommitted, read committed, repeatable read, serializable

8.分库分表后id主键如何处理

(1)自增id，往一个库的一个表里插入一条没什么业务含义的数据，然后获取一个数据库自增的一个id。拿到这个id之后再往对应的分库分表里去写入。不适合高并发场景。
(2)设置数据库sequence或者表的自增字段步长来进行水平伸缩。比如说，现在有8个服务节点，每个服务节点使用一个sequence功能来产生ID，每个sequence的起始ID不同，并且依次递增，步长都是8。
(3)snowflake算法:开源的分布式id生成算法，是把一个 64 位的long型的id，1个bit 是不用的，用其中的41bit作为毫秒数，用10bit作为工作机器id，12bit作为序列号。

9.MySQL查询字段区不区分大小写，如果区分

不区分。区分方法：
(1)创建表时，直接设置表的collate属性为utf8_general_cs或者utf8_bin；如果已经创建表，则直接修改字段的Collation属性为utf8_general_cs或者utf8_bin。
(2)修改SQL语句

    -- 在每一个条件前加上binary关键字
    select * from user where binary username = 'admin' and binary password = 'admin';

    -- 将参数以binary('')包围
    select * from user where username like binary('admin') and password like binary('admin');

10.MySQL innodb有多少种日志

(1)错误日志：记录出错信息，也记录一些警告信息或者正确的信息。
(2)查询日志：记录所有对数据库请求的信息，不论这些请求是否得到了正确的执行。
(3)慢查询日志：设置一个阈值，将运行时间超过该值的所有SQL语句都记录到慢查询的日志文件中。
(4)二进制日志：记录对数据库执行更改的所有操作。
(5)中继日志：中继日志也是二进制日志，用来给slave库恢复
(6)事务日志：重做日志redo和回滚日志undo

11.事务是如何通过日志来实现的
   
事务日志是通过redo和innodb的存储引擎日志缓冲（Innodb log buffer）来实现的，当开始一个事务的时候，会记录该事务的lsn(log sequence number)号;
当事务执行时，会往InnoDB存储引擎的日志的日志缓存里面插入事务日志；
当事务提交时，必须将存储引擎的日志缓冲写入磁盘（通过innodb_flush_log_at_trx_commit来控制），也就是写数据前，需要先写日志。这种方式称为“预写日志方式”

12.MySQL binlog的几种日志录入格式以及区别

(1)Statement：每一条会修改数据的sql都会记录在binlog中。
优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。相比row能节约多少性能与日志量，这个取决于应用的SQL情况，正常同一条记录修改或者插入row格式所产生的日志量还小于Statement产生的日志量，但是考虑到如果带条件的update操作，以及整表删除，alter表等操作，ROW格式会产生大量日志，因此在考虑是否使用ROW格式日志时应该根据应用的实际情况，其所产生的日志量会增加多少，以及带来的IO性能问题。
缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的一些相关信息，以保证所有语句能在slave得到和在master端执行时候相同的结果。
另外mysql的复制,像一些特定函数功能，slave可与master上要保持一致会有很多相关问题(如sleep()函数， last_insert_id()，以及user-defined functions(udf)会出现问题)。
   
(2)Row:不记录sql语句上下文相关信息，仅保存哪条记录被修改。
优点：binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以row level的日志内容会非常清楚的记录下每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题
缺点：所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容。比如一条update语句，修改多条记录，则binlog中每一条修改都会有记录，这样造成binlog日志量会很大，特别是当执行alter table之类的语句的时候，由于表结构修改，每条记录都发生改变，那么该表每一条记录都会记录到日志中。
   
(3)Mixedlevel: 以上两种level的混合使用。一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则采用row格式保存binlog,MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择一种。
新版本的MySQL中对row level模式也被做了优化，并不是所有的修改都会以row level来记录，像遇到表结构变更的时候就会以statement模式来记录。至于update或者delete等修改数据的语句，还是会记录所有行的变更。

N.参考

(1)[Java面试题之数据库三范式是什么？](https://www.cnblogs.com/marsitman/p/10162231.html)

(2)[三张图搞透第一范式(1NF)、第二范式(2NF)和第三范式(3NF)的区别](https://blog.csdn.net/weixin_43971764/article/details/88677688)

(3)[真正理解Mysql的四种隔离级别](https://www.jianshu.com/p/8d735db9c2c0)

# Redis

1.key/value单线程非关系型数据库、NoSQL、不支持事务(流水线支持事务！)、高并发、内存存储、支持持久化。

2.应用场景：
(1)计数器:可以对String进行自增自减运算，从而实现计数器功能。Redis这种内存型数据库的读写性能非常高，很适合存储频繁读写的计数量。
(2)缓存:将热点数据放到内存中，设置内存的最大使用量以及淘汰策略来保证缓存的命中率。
(3)查找表:例如DNS记录就很适合使用Redis进行存储。查找表和缓存类似，也是利用了Redis快速的查找特性。但是查找表的内容不能失效，而缓存的内容可以失效，因为缓存不作为可靠的数据来源。
(4)消息队列:List是一个双向链表，可以通过lpush和rpop写入和读取消息，不过最好使用Kafka、RabbitMQ等消息中间件。
(5)会话缓存:可以使用Redis来统一存储多台应用服务器的会话信息。当应用服务器不再存储用户的会话信息，也就不再具有状态，一个用户可以请求任意一个应用服务器，从而更容易实现高可用性以及可伸缩性。
(6)分布式锁实现:在分布式场景下，无法使用单机环境下的锁来对多个节点上的进程进行同步。可以使用Redis自带的SETNX命令实现分布式锁，除此之外，还可以使用官方提供的RedLock分布式锁实现。
(7)其它:Set可以实现交集、并集等操作，从而实现共同好友等功能。ZSet可以实现有序性操作，从而实现排行榜等功能。

3.数据结构，常用的共有5种；

(1)字符串string
(a)存SET key value EX PX NX|XX，EX设置过期时间单位秒，PX设置过期时间单位毫秒，NX只有键key不存在的时候才会设置key的值，XX只有键key存在的时候才会设置key的值。
(b)取GET key

(2)散列hash
(a)hgetall key；
(b)hget key field；
(c)hexist key field
(d)hset key field value
(e)hdel key field

(3)列表list
(a)lpop key；lpush key value；rpop key；rpush key value；
(b)lrange start end 返回存储在key的列表里指定范围内的元素。start,end为索引序号。
(c)lindex key index 返回列表里的元素的索引index存储在key里面。 
(d)linsert key before|after pivot value 把value插入存于key的列表中在基准值pivot的前面或后面。

(4)集合set
(a)sadd key member
(b)srem key member
(c)sismember key member
(d)smembers key

(5)有序集合sorted set
(a)zadd key score member
(b)zincrby key increment member
(c)zrem key member
(d)zscore key member

4.Redis与MemCached区别

(1)数据操作不同
MemCached只支持简单的key-value存储，不支持枚举，不支持持久化和复制等功能。Redis拥有更多的数据结构和并支持更丰富的数据操作，支持list、set、sorted set、hash等众多数据结构，还同时提供了持久化和复制等功能。
Redis单个value的最大限制是1GB，而MemCached则只能保存1MB内的数据。

(2)内存管理机制不同
在Redis中，并不是所有的数据都一直存储在内存中的。这是和MemCached相比一个最大的区别。当物理内存用完时，Redis可以将一些很久没用到的value交换到磁盘。
Redis只会缓存所有的key的信息，如果Redis发现内存的使用量超过了某一个阀值，将触发swap的操作，Redis根据“swappability = age*log(size_in_memory)”计算出哪些key对应的value需要swap到磁盘，然后再将这些key对应的value持久化到磁盘中，同时在内存中清除。这种特性使得Redis可以保持超过其机器本身内存大小的数据。
MemCached默认使用Slab Allocation机制管理内存，其主要思想是按照预先规定的大小，将分配的内存分割成特定长度的块以存储相应长度的key-value数据记录，以完全解决内存碎片问题。

(3)性能不同
由于Redis只使用单核，而MemCached可以使用多核，所以平均每一个核上Redis在存储小数据时比MemCached性能更高。而在100k以上的数据中，MemCached性能要高于Redis，虽然Redis也在存储大数据的性能上进行了优化，但是比起MemCached，还是稍有逊色。

(4)集群管理不同
MemCached本身并不支持分布式，因此只能在客户端通过像一致性哈希这样的分布式算法来实现MemCached的分布式存储。相较于MemCached只能采用客户端实现分布式存储，Redis更偏向于在服务器端构建分布式存储。

5.Redis持久化

快照RDB:将某个时间点的所有数据都存放到硬盘上。可以将快照复制到其它服务器从而创建具有相同数据的服务器副本。如果系统发生故障，将会丢失最后一次创建快照之后的数据。如果数据量很大，保存快照的时间会很长。

写文件AOF:将写命令添加到AOF文件（Append Only File）的末尾。
使用AOF持久化需要设置同步选项，从而确保写命令什么时候会同步到磁盘文件上。这是因为对文件进行写入并不会马上将内容同步到磁盘上，而是先存储到缓冲区，然后由操作系统决定什么时候同步到磁盘。
有以下同步选项：

    always	每个写命令都同步，会严重减低服务器的性能；
    everysec	每秒同步一次，选项比较合适，可以保证系统崩溃时只会丢失一秒左右的数据，并且Redis每秒执行一次同步对服务器性能几乎没有任何影响；
    no	让操作系统来决定何时同步，选项并不能给服务器性能带来多大的提升，而且也会增加系统崩溃时数据丢失的数量。

随着服务器写请求的增多，AOF文件会越来越大。Redis提供了一种将AOF重写的特性，能够去除AOF文件中的冗余写命令。

6.Redis为什么是单线程的还很快
(1)Redis是基于内存的，内存的读写速度非常快。
(2)Redis是单线程的，省去了很多上下文切换线程的时间。
(3)Redis使用多路复用技术，可以处理并发的连接。

7.Redis实现分布式锁

应该满足(a)互斥性:在任意时刻，只有一个客户端能持有锁。(b)不能死锁:客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。(c)容错性只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。

    //获取锁（unique_value可以是UUID等）
    SET resource_name unique_value NX PX 30000

    //释放锁（lua脚本中，一定要比较value，防止误解锁）
    if redis.call("get",KEYS[1]) == ARGV[1] then
        return redis.call("del",KEYS[1])
    else
        return 0
    end
    
set命令要用set key value px milliseconds nx，替代setnx + expire需要分两次执行命令的方式，保证了原子性.
value要具有唯一性，可以使用UUID.randomUUID().toString()方法生成，用来标识这把锁是属于哪个请求加的，在解锁的时候就可以有依据。
释放锁时要验证value值，防止误解锁。
通过Lua脚本来避免Check And Set模型的并发问题，因为在释放锁的时候因为涉及到多个Redis操作（利用了eval命令执行Lua脚本的原子性）；

8.缓存雪崩/缓存穿透/缓存并发竞争/缓存和数据库双写不一致

(1)缓存雪崩:通常会使用缓存用于缓冲对DB的冲击，如果缓存宕机，所有请求将直接打在DB，造成DB宕机——从而导致整个系统宕机。
解决：2种策略同时使用。(a)对缓存做高可用，防止缓存宕机。(b)使用断路器，如果缓存宕机，为了防止系统全部宕机，限制部分流量进入DB，保证部分可用，其余的请求返回断路器的默认值。

(2)缓存穿透:缓存查询一个没有的key，同时数据库也没有，如果黑客大量的使用这种方式，那么就会导致DB宕机。或者大量请求查询一个刚刚失效的key，导致DB压力倍增，可能导致宕机，但实际上，查询的都是相同的数据。
解决：我们可以使用一个默认值来防止，例如，当访问一个不存在的key，然后再去访问数据库，还是没有，那么就在缓存里放一个占位符，下次来的时候，检查这个占位符，如果发生时占位符，就不去数据库查询了，防止DB宕机。

(3)缓存并发竞争:多个客户端写一个key，如果顺序错了，数据就不对了。但是顺序我们无法控制。
解决：使用分布式锁，例如zk，同时加入数据的时间戳。同一时刻，只有抢到锁的客户端才能写入，同时，写入时，比较当前数据的时间戳和缓存中数据的时间戳。

(4)缓存和数据库双写不一致:连续写数据库和缓存，但是操作期间，出现并发了，数据不一致了。
(a)先更新数据库，再更新缓存。当有2个请求同时更新数据，那么如果不使用分布式锁，将无法控制最后缓存的值到底是多少。也就是并发写的时候有问题。
(b)先删缓存，再更新数据库。这么做的问题：如果在删除缓存后，有客户端读数据，将可能读到旧数据，并有可能设置到缓存中，导致缓存中的数据一直是老数据。
有2种解决方案：使用“双删”，即删更删，最后一步的删除作为异步操作，就是防止有客户端读取的时候设置了旧值。或使用队列，当这个key不存在时，将其放入队列，串行执行，必须等到更新数据库完毕才能读取数据。总的来讲，比较麻烦。
(c)先更新数据库，再删除缓存。这个实际是常用的方案，但是有很多人不知道，这里介绍一下，这个叫Cache Aside Pattern，老外发明的。如果先更新数据库，再删除缓存，那么就会出现更新数据库之前有瞬间数据不是很及时。
同时，如果在更新之前，缓存刚好失效了，读客户端有可能读到旧值，然后在写客户端删除结束后再次设置了旧值，非常巧合的情况。
有2个前提条件：缓存在写之前的时候失效，同时，在写客户度删除操作结束后，放置旧数据——也就是读比写慢，甚至有的写操作还会锁表。

9.Redis的过期策略

(1)定期删除策略:Redis会将每个设置了过期时间的key放入到一个独立的字典中，默认每100ms进行一次过期扫描：随机抽取20个key，删除这20个key中过期的key，如果过期的key比例超过1/4，就重复步骤，继续删除。
不能全部扫描，因为Redis是单线程，全部扫描岂不是卡死了。而且为了防止每次扫描过期key比例都超过1/4，导致不停循环卡死线程，Redis为每次扫描添加了上限时间，默认是25ms。
(2)从库的过期策略:从库不会进行过期扫描，从库对过期的处理是被动的。主库在key到期时，会在AOF文件里增加一条del指令，同步到所有的从库，从库通过执行这条del指令来删除过期的key。
因为指令同步是异步进行的，所以主库过期的key的del指令没有及时同步到从库的话，会出现主从数据的不一致，主库没有的数据在从库里还存在。
(3)懒惰删除策略:删除指令del会直接释放对象的内存，大部分情况下，这个指令非常快，没有明显延迟。不过如果删除的key是一个非常大的对象，比如一个包含了千万元素的hash，又或者在使用FLUSHDB和FLUSHALL删除包含大量键的数据库时，那么删除操作就会导致单线程卡顿。
redis 4.0引入了lazyfree的机制，它可以将删除键或数据库的操作放在后台线程里执行，从而尽可能地避免服务器阻塞。
指令为：unlink key/flushall async
(4)内存淘汰机制:Redis的内存占用会越来越高。Redis为了限制最大使用内存，提供了redis.conf中的配置参数maxmemory。当内存超出maxmemory，Redis提供了几种内存淘汰机制让用户选择，配置maxmemory-policy：
          
    noeviction：当内存超出 maxmemory，写入请求会报错，但是删除和读请求可以继续。（使用这个策略，疯了吧）
    allkeys-lru：当内存超出 maxmemory，在所有的 key 中，移除最少使用的key。只把 Redis 既当缓存是使用这种策略。（推荐）。
    allkeys-random：当内存超出 maxmemory，在所有的 key 中，随机移除某个 key。（应该没人用吧）
    volatile-lru：当内存超出 maxmemory，在设置了过期时间 key 的字典中，移除最少使用的 key。把 Redis 既当缓存，又做持久化的时候使用这种策略。
    volatile-random：当内存超出 maxmemory，在设置了过期时间 key 的字典中，随机移除某个key。
    volatile-ttl：当内存超出 maxmemory，在设置了过期时间 key 的字典中，优先移除 ttl 小的。
    
10.事务

一个事务包含了多个命令，服务器在执行事务期间，不会改去执行其它客户端的命令请求。
事务中的多个命令被一次性发送给服务器，而不是一条一条发送，这种方式被称为流水线，它可以减少客户端与服务器之间的网络通信次数从而提升性能。
Redis 最简单的事务实现方式是使用MULTI和EXEC命令将事务操作包围起来。

11.事件

Redis服务器是一个事件驱动程序。
(1)文件事件:服务器通过套接字与客户端或者其它服务器进行通信，文件事件就是对套接字操作的抽象。Redis基于Reactor模式开发了自己的网络事件处理器，使用I/O多路复用程序来同时监听多个套接字，并将到达的事件传送给文件事件分派器，分派器会根据套接字产生的事件类型调用相应的事件处理器。
(2)时间事件:服务器有一些操作需要在给定的时间点执行，时间事件是对这类定时操作的抽象。时间事件又分为：定时事件（是让一段程序在指定的时间之内执行一次），周期性事件（是让一段程序每隔指定时间就执行一次）。Redis将所有时间事件都放在一个无序链表中，通过遍历整个链表查找出已到达的时间事件，并调用相应的事件处理器。

12.复制

通过使用slaveof host port命令来让一个服务器成为另一个服务器的从服务器。一个从服务器只能有一个主服务器，并且不支持主主复制。
连接过程：主服务器创建快照文件，发送给从服务器，并在发送期间使用缓冲区记录执行的写命令。快照文件发送完毕之后，开始向从服务器发送存储在缓冲区中的写命令；从服务器丢弃所有旧数据，载入主服务器发来的快照文件，之后从服务器开始接受主服务器发来的写命令；主服务器每执行一次写命令，就向从服务器发送相同的写命令。
主从链：随着负载不断上升，主服务器可能无法很快地更新所有从服务器，或者重新连接和重新同步从服务器将导致系统超载。为了解决这个问题，可以创建一个中间层来分担主服务器的复制工作。中间层的服务器是最上层服务器的从服务器，又是最下层服务器的主服务器。

13.Sentinel

Sentinel（哨兵）可以监听集群中的服务器，并在主服务器进入下线状态时，自动从从服务器中选举出新的主服务器。

14.分片

分片是将数据划分为多个部分的方法，可以将数据存储到多台机器里面，这种方法在解决某些问题时可以获得线性级别的性能提升。
根据执行分片的位置，可以分为三种分片方式：
客户端分片：客户端使用一致性哈希等算法决定键应当分布到哪个节点。
代理分片：将客户端请求发送到代理上，由代理转发请求到正确的节点上。
服务器分片：Redis Cluster。

N.参考

(1)[Redis中文官网](http://www.redis.cn/)

(2)[Redis持久化 - RDB和AOF](https://segmentfault.com/a/1190000016021217)

(3)[Redis分布式锁的正确实现方式（Java版）](https://blog.csdn.net/yb223731/article/details/90349502)

(4)[【99期】中高级开发面试必问的Redis，看这篇就够了！](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247484524&idx=1&sn=7ae13d7805f70592245464a7d9512e57&chksm=e80db21adf7a3b0c6b7c8e1367f4c9e772666e1e764a00ad2f4771c87520fdeff68da57f5f07&scene=21#wechat_redirect)

# ZooKeeper

1.ZooKeeper是什么？

ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，它是集群的管理者，监视着集群中各个节点的状态根据节点提交的反馈进行下一步合理操作。最终，将简单易用的接口和性能高效、功能稳定的系统提供给用户。
客户端的读请求可以被集群中的任意一台机器处理，如果读请求在节点上注册了监听器，这个监听器也是由所连接的zookeeper机器来处理。
对于写请求，这些请求会同时发给其他zookeeper机器并且达成一致后，请求才会返回成功。因此，随着zookeeper的集群机器增多，读请求的吞吐会提高但是写请求的吞吐会下降。
有序性是zookeeper中非常重要的一个特性，所有的更新都是全局有序的，每个更新都有一个唯一的时间戳，这个时间戳称为zxid（Zookeeper Transaction Id）。而读请求只会相对于更新有序，也就是读请求的返回结果中会带有这个zookeeper最新的zxid。

2.ZooKeeper提供了什么？

文件系统、通知机制

3.Zookeeper文件系统

Zookeeper提供一个多层级的节点命名空间（节点称为znode）。与文件系统不同的是，这些节点都可以设置关联的数据，而文件系统中只有文件节点可以存放数据而目录节点不行。
Zookeeper为了保证高吞吐和低延迟，在内存中维护了这个树状的目录结构，这种特性使得Zookeeper不能用于存放大量的数据，每个节点的存放数据上限为1M。

4.四种类型的znode

(1)PERSISTENT:持久化目录节点，客户端与zookeeper断开连接后，该节点依旧存在
(2)PERSISTENT-SEQUENTIAL:持久化顺序编号目录节点，客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号
(3)EPHEMERAL:临时目录节点，客户端与zookeeper断开连接后，该节点被删除
(4)EPHEMERAL-SEQUENTIAL:临时顺序编号目录节点，客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号

5.Zookeeper通知机制

client端会对某个znode建立一个watcher事件，当该znode发生变化时，这些client会收到zk的通知，然后client可以根据znode变化来做出业务上的改变等。

6.ZooKeeper功能

(1)命名服务（依赖于文件系统）:命名服务是指通过指定的名字来获取资源或者服务的地址，利用zk创建一个全局的路径，即是唯一的路径，这个路径就可以作为一个名字，指向集群中的集群，提供的服务的地址，或者一个远程的对象等等。

(2)配置管理（依赖于文件系统、通知机制）:程序分布式的部署在不同的机器上，将程序的配置信息放在zk的znode下，当有配置发生改变时，也就是znode发生变化时，可以通过改变zk中某个目录节点的内容，利用watcher通知给各个客户端，从而更改配置。

(3)集群管理（依赖于文件系统、通知机制）:
所谓集群管理无在乎两点：是否有机器退出和加入、选举master。
对于第一点，所有机器约定在父目录下创建临时目录节点，然后监听父目录节点的子节点变化消息。一旦有机器挂掉，该机器与zookeeper的连接断开，其所创建的临时目录节点被删除，所有其他机器都收到通知：某个兄弟目录被删除。新机器加入也是类似，所有机器收到通知：新兄弟目录加入。
对于第二点，我们稍微改变一下，所有机器创建临时顺序编号目录节点，每次选取编号最小的机器作为master就好。

(4)分布式锁（文件系统、通知机制）:
有了zookeeper的一致性文件系统，锁的问题变得容易。锁服务可以分为两类，一个是保持独占，另一个是控制时序。
对于第一类，我们将zookeeper上的一个znode看作是一把锁，通过createznode的方式来实现。所有客户端都去创建/distribute_lock节点，最终成功创建的那个客户端也即拥有了这把锁。用完删除掉自己创建的distribute_lock节点就释放出锁。
对于第二类，/distribute_lock已经预先存在，所有客户端在它下面创建临时顺序编号目录节点，和选master一样，编号最小的获得锁，用完删除，依次方便。

获取分布式锁的流程:
在获取分布式锁的时候在locker节点下创建临时顺序节点，释放锁的时候删除该临时节点。客户端调用createNode方法在locker下创建临时顺序节点，然后调用getChildren(“locker”)来获取locker下面的所有子节点，注意此时不用设置任何Watcher。
客户端获取到所有的子节点path之后，如果发现自己创建的节点在所有创建的子节点序号最小，那么就认为该客户端获取到了锁。如果发现自己创建的节点并非locker所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到比自己小的那个节点，然后对其调用exist()方法，同时对其注册事件监听器。之后，让这个被关注的节点删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点是否是locker子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听。当前这个过程中还需要许多的逻辑判断。

(5)队列管理（文件系统、通知机制）:

两种类型的队列：
第一类：同步队列，当一个队列的成员都聚齐时，这个队列才可用，否则一直等待所有成员到达。在约定目录下创建临时目录节点，监听节点数目是否是我们要求的数目。
第二类：队列按照FIFO方式进行入队和出队操作，和分布式锁服务中的控制时序场景基本原理一致，入列有编号，出列按编号。在特定的目录下创建PERSISTENT_SEQUENTIAL节点，创建成功时Watcher通知等待的队列，队列删除序列号最小的节点用以消费。此场景下Zookeeper的znode用于消息存储，znode存储的数据就是消息队列中的消息内容，SEQUENTIAL序列号就是消息的编号，按序取出即可。由于创建的节点是持久化的，所以不必担心队列消息的丢失问题。

7.Zookeeper数据复制

Zookeeper作为一个集群提供一致的数据服务，自然，它要在所有机器间做数据复制。数据复制的好处：
容错：一个节点出错，不致于让整个系统停止工作，别的节点可以接管它的工作；
提高系统的扩展能力 ：把负载分布到多个节点上，或者增加节点来提高系统的负载能力；
提高性能：让客户端本地访问就近的节点，提高用户访问速度。

从客户端读写访问的透明度来看，数据复制集群系统分下面两种：
写主(WriteMaster) ：对数据的修改提交给指定的节点。读无此限制，可以读取任何一个节点。这种情况下客户端需要对读与写进行区别，俗称读写分离；
写任意(Write Any)：对数据的修改可提交给任意的节点，跟读一样。这种情况下，客户端对集群节点的角色与变化透明。
对zookeeper来说，它采用的方式是写任意。通过增加机器，它的读吞吐能力和响应能力扩展性非常好，而写，随着机器的增多吞吐能力肯定下降（这也是它建立observer的原因），而响应能力则取决于具体实现方式，是延迟复制保持最终一致性，还是立即复制快速响应。

8.Zookeeper工作原理

Zookeeper的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。
当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和leader的状态同步以后，恢复模式就结束了。
状态同步保证了leader和Server具有相同的系统状态。

9.zookeeper是如何保证事务的顺序一致性的？

zookeeper采用了递增的事务Id来标识，所有的proposal（提议）都在被提出的时候加上了zxid，zxid实际上是一个64位的数字，高32位是epoch（时期; 纪元; 世; 新时代）用来标识leader是否发生改变，如果有新的leader产生出来，epoch会自增，低32位用来递增计数。
当新产生proposal的时候，会依据数据库的两阶段过程，首先会向其他的server发出事务执行请求，如果超过半数的机器都能执行并且能够成功，那么就会开始执行。

10.Zookeeper下Server工作状态

每个Server在工作过程中有三种状态：
LOOKING：当前Server不知道leader是谁，正在搜寻。
LEADING：当前Server即为选举出来的leader。
FOLLOWING：leader已经选举出来，当前Server与之同步。

11.zookeeper是如何选取主leader的？

当leader崩溃或者leader失去大多数的follower，这时zk进入恢复模式，恢复模式需要重新选举出一个新的leader，让所有的Server都恢复到一个正确的状态。Zk的选举算法有两种：一种是基于basic paxos实现的，另外一种是基于fast paxos算法实现的。系统默认的选举算法为fast paxos。

basic paxos:
(1)选举线程由当前Server发起选举的线程担任，其主要功能是对投票结果进行统计，并选出推荐的Server；
(2)选举线程首先向所有Server发起一次询问(包括自己)；
(3)选举线程收到回复后，验证是否是自己发起的询问(验证zxid是否一致)，然后获取对方的id(myid)，并存储到当前询问对象列表中，最后获取对方提议的leader相关信息(id,zxid)，并将这些信息存储到当次选举的投票记录表中；
(4)收到所有Server回复以后，就计算出zxid最大的那个Server，并将这个Server相关信息设置成下一次要投票的Server；
(5)线程将当前zxid最大的Server设置为当前Server要推荐的Leader，如果此时获胜的Server获得n/2 + 1的Server票数，设置当前推荐的leader为获胜的Server，将根据获胜的Server相关信息设置自己的状态，否则，继续这个过程，直到leader被选举出来。通过流程分析我们可以得出：要使Leader获得多数Server的支持，则Server总数必须是奇数2n+1，且存活的Server的数目不得少于n+1. 每个Server启动后都会重复以上流程。在恢复模式下，如果是刚从崩溃状态恢复的或者刚启动的server还会从磁盘快照中恢复数据和会话信息，zk会记录事务日志并定期进行快照，方便在恢复时进行状态恢复。

fast paxos:
在选举过程中，某Server首先向所有Server提议自己要成为leader，当其它Server收到提议以后，解决epoch和zxid的冲突，并接受对方的提议，然后向对方发送接受提议完成的消息，重复这个流程，最后一定能选举出Leader。

12.Zookeeper同步流程

选完Leader以后，zk就进入状态同步过程。

(1)Leader等待server连接；
(2)Follower连接leader，将最大的zxid发送给leader；
(3)Leader根据follower的zxid确定同步点；
(4)完成同步后通知follower已经成为uptodate状态；
(5)Follower收到uptodate消息后，又可以重新接受client的请求进行服务了。

13.分布式通知和协调

对于系统调度来说：操作人员发送通知实际是通过控制台改变某个节点的状态，然后zk将这些变化发送给注册了这个节点的watcher的所有客户端。
对于执行情况汇报：每个工作进程都在某个目录下创建一个临时节点。并携带工作的进度数据，这样汇总的进程可以监控目录子节点的变化获得工作进度的实时的全局情况。

14.机器中为什么会有leader？

在分布式环境中，有些业务逻辑只需要集群中的某一台机器进行执行，其他的机器可以共享这个结果，这样可以大大减少重复计算，提高性能，于是就需要进行leader选举。

15.zk节点宕机如何处理？

Zookeeper本身也是集群，推荐配置不少于3个服务器。Zookeeper自身也要保证当一个节点宕机时，其他节点会继续提供服务。
如果是一个Follower宕机，还有2台服务器提供访问，因为Zookeeper上的数据是有多个副本的，数据并不会丢失；
如果是一个Leader宕机，Zookeeper会选举出新的Leader。ZK集群的机制是只要超过半数的节点正常，集群就能正常提供服务。只有在ZK节点挂得太多，只剩一半或不到一半节点能工作，集群才失效。
所以3个节点的cluster可以挂掉1个节点(leader可以得到2票>1.5)。2个节点的cluster就不能挂掉任何1个节点了(leader可以得到1票<=1)

16.zookeeper负载均衡和nginx负载均衡区别

zookeeper:不存在单点问题，zab机制保证单点故障可重新选举一个leader。只负责服务的注册与发现，不负责转发，减少一次数据交换（消费方与服务方直接通信）。需要自己实现相应的负载均衡算法。
nginx:存在单点问题，单点负载高数据量大。每次负载，都充当一次中间人转发角色，增加网络负载量（消费方与服务方间接通信）。自带负载均衡算法。

17.zookeeper watch机制

Watch机制官方声明：一个Watch事件是一个一次性的触发器，当被设置了Watch的数据发生了改变的时候，则服务器将这个改变发送给设置了Watch的客户端，以便通知它们。Zookeeper机制的特点：

(1)一次性触发数据发生改变时，一个watcher event会被发送到client，但是client只会收到一次这样的信息。
(2)watcher event异步发送watcher的通知事件从server发送到client是异步的，这就存在一个问题，不同的客户端和服务器之间通过socket进行通信，由于网络延迟或其他因素导致客户端在不通的时刻监听到事件，由于Zookeeper本身提供了ordering guarantee，即客户端监听事件后，才会感知它所监视znode发生了变化。所以我们使用Zookeeper不能期望能够监控到节点每次的变化。Zookeeper只能保证最终的一致性，而无法保证强一致性。
(3)数据监视Zookeeper有数据监视和子数据监视。getdata() and exists()设置数据监视，getchildren()设置了子节点监视。
(4)注册watcher getData、exists、getChildren
(5)触发watcher create、delete、setData
(6)setData()会触发znode上设置的data watch（如果set成功的话）。一个成功的create() 操作会触发被创建的znode上的数据watch，以及其父节点上的child watch。而一个成功的delete()操作将会同时触发一个znode的data watch和child watch（因为这样就没有子节点了），同时也会触发其父节点的child watch。
(7)当一个客户端连接到一个新的服务器上时，watch将会被以任意会话事件触发。当与一个服务器失去连接的时候，是无法接收到watch的。而当client重新连接时，如果需要的话，所有先前注册过的watch，都会被重新注册。通常这是完全透明的。只有在一个特殊情况下，watch可能会丢失：对于一个未创建的znode的exist watch，如果在客户端断开连接期间被创建了，并且随后在客户端连接上之前又删除了，这种情况下，这个watch事件可能会被丢失。
(8)Watch是轻量级的，其实就是本地JVM的Callback，服务器端只是存了是否有设置了Watcher的布尔类型

N.参考

(1)[ZooKeeper面试题](https://blog.csdn.net/weixin_41847891/article/details/100734093)

(2)[什么是ZooKeeper](https://cloud.tencent.com/developer/article/1418528)

(3)[【28期】ZooKeeper面试那些事儿](https://mp.weixin.qq.com/s/nLVovl0EdfbfqnyC53nr4w)

# Kafka

1.kafka可以脱离zookeeper单独使用吗？为什么？

不可以，kafka必须要依赖一个zookeeper集群才能运行。kafka系群里面各个broker都是通过zookeeper来同步topic列表以及其它broker列表的，一旦连不上zookeeper，kafka也就无法工作。

2.kafka有几种数据保留的策略

Kafka Broker默认的消息保留策略是：要么保留一定时间，要么保留到消息达到一定大小的字节数。当消息达到设置的条件上限时，旧消息就会过期并被删除，所以，在任何时刻，可用消息的总量都不会超过配置参数所指定的大小。
topic可以配置自己的保留策略，可以将消息保留到不再使用他们为止。因为在一个大文件里查找和删除消息是很费时的事，也容易出错，所以，分区被划分为若干个片段。默认情况下，每个片段包含1G或者一周的数据，以较小的那个为准。在broker往leader分区写入消息时，如果达到片段上限，就关闭当前文件，并打开一个新文件。当前正在写入数据的片段叫活跃片段。当所有片段都被写满时，会清除下一个分区片段的数据，如果配置的是7个片段，每天打开一个新片段，就会删除一个最老的片段，循环使用所有片段。

3.什么情况会导致kafka运行变慢

CPU性能瓶颈、磁盘读写瓶颈、网络瓶颈
 
4.使用kafka集群需要注意什么？

集群的数量不是越多越好，最好不要超过7个，因为节点越多，消息复制需要的时间就越长，整个群组的吞吐量就越低。集群数量最好是单数，因为超过一半故障集群就不能用了，设置为单数容错率更高。

5.数据分区策略

第一种分区策略：给定了分区号，直接将数据发送到指定的分区里面去。
第二种分区策略：没有给定分区号，给定数据的key值，通过key取上hashCode进行分区。
第三种分区策略：既没有给定分区号，也没有给定key值，直接轮循进行分区。
第四种分区策略：自定义分区。

6.消息队列中，如何保证消息的顺序性

错乱场景：
(1)RabbitMQ:一个queue，多个consumer。比如，生产者向RabbitMQ里发送了三条数据，顺序依次是data1/data2/data3，压入的是RabbitMQ的一个内存队列。有三个消费者分别从MQ中消费这三条数据中的一条，结果消费者2先执行完操作，把data2存入数据库，然后是data1/data3。这不明显乱了。
(2)Kafka：比如说我们建了一个topic，有三个partition。生产者在写的时候，其实可以指定一个key，比如说我们指定了某个订单id作为key，那么这个订单相关的数据，一定会被分发到同一个partition中去，而且这个partition中的数据一定是有顺序的。消费者从partition中取出来数据的时候，也一定是有顺序的。到这里，顺序还是ok的，没有错乱。接着，我们在消费者里可能会搞多个线程来并发处理消息。因为如果消费者是单线程消费处理，而处理比较耗时的话，比如处理一条消息耗时几十ms，那么1秒钟只能处理几十条消息，这吞吐量太低了。而多个线程并发跑的话，顺序可能就乱掉了。

解决方案:
(1)RabbitMQ:拆分多个queue，每个queue一个consumer，就是多一些queue而已，确实是麻烦点；或者就一个queue但是对应一个consumer，然后这个consumer内部用内存队列做排队，然后分发给底层不同的worker来处理。
(2)Kafka:一个topic，一个partition，一个consumer，内部单线程消费，单线程吞吐量太低，一般不会用这个。写 N个内存queue，具有相同key的数据都到同一个内存queue；然后对于N个线程，每个线程分别消费一个内存queue即可，这样就能保证顺序性。

7.使用消息队列的好处

(1)提高系统响应速度:使用了消息队列，生产者一方，把消息往队列里一扔，就可以立马返回，响应用户了。无需等待处理结果。处理结果可以让用户稍后自己来取，如医院取化验单。也可以让生产者订阅（如：留下手机号码或让生产者实现listener接口、加入监听队列），有结果了通知。获得约定将结果放在某处，无需通知。
(2)提高系统稳定性:考虑电商系统下订单，发送数据给生产系统的情况。电商系统和生产系统之间的网络有可能掉线，生产系统可能会因维护等原因暂停服务。如果不使用消息队列，电商系统数据发布出去，顾客无法下单，影响业务开展。两个系统间不应该如此紧密耦合。应该通过消息队列解耦。同时让系统更健壮、稳定。
(3)异步化、解耦、消除峰值。

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

# 网络

1.HTTP响应码301和302代表的是什么，有什么区别，为什么尽量用301？

301代表永久性转移(Permanently Moved)，302代表暂时性转移(Temporarily Moved )。
301和302状态码都表示重定向，就是说浏览器在拿到服务器返回的这个状态码后会自动跳转到一个新的URL地址，这个地址可以从响应的Location首部中获取（用户看到的效果就是他输入的地址A瞬间变成了另一个地址B）——这是它们的共同点。
它们的不同在于，301表示旧地址A的资源已经被永久地移除了（这个资源不可访问了），搜索引擎在抓取新内容的同时也将旧的网址交换为重定向之后的网址；302表示旧地址A的资源还在（仍然可以访问），这个重定向只是临时地从旧地址A跳转到地址B，搜索引擎会抓取新的内容而保存旧的网址。
尽量要使用301跳转是为了防止网址劫持，从网址A做一个302重定向到网址B时，主机服务器的隐含意思是网址A随时有可能改主意，重新显示本身的内容或转向其他的地方。大部分的搜索引擎在大部分情况下，当收到302重定向时，一般只要去抓取目标网址就可以了，也就是说网址B。如果搜索引擎在遇到302转向时，百分之百的都抓取目标网址B的话，就不用担心网址URL劫持了。问题就在于，有的时候搜索引擎，尤其是Google，并不能总是抓取目标网址。比如说，有的时候A网址很短，但是它做了一个302重定向到B网址，而B网址是一个很长的乱七八糟的URL网址，甚至还有可能包含一些问号之类的参数。很自然的，A网址更加用户友好，而B网址既难看，又不用户友好。这时Google很有可能会仍然显示网址A。由于搜索引擎排名算法只是程序而不是人，在遇到302重定向的时候，并不能像人一样的去准确判定哪一个网址更适当，这就造成了网址URL劫持的可能性。也就是说，一个不道德的人在他自己的网址A做一个302重定向到你的网址B，出于某种原因，Google搜索结果所显示的仍然是网址A，但是所用的网页内容却是你的网址B上的内容，这种情况就叫做网址URL劫持。
简单理解是，从网站A（网站比较烂）上做了一个302跳转到网站B（搜索排名很靠前），这时候有时搜索引擎会使用网站B的内容，但却收录了网站A的地址，这样在不知不觉间，网站B在为网站A作贡献，网站A的排名就靠前了。

# 设计模式

1.单例模式有几种写法

(1)饱汉模式：第一次使用的时候再初始化，即“懒加载”。存在线程不安全问题。
可能解决方案：
(a)静态方法getInstance上增加synchronized。
(b)getInstance方法中外层套了一层check，加上synchronized内层的check，即所谓“双重检查锁”（Double Check Lock，简称DCL）。DCL仍然是线程不安全的，由于指令重排序，你可能会得到“半个对象”，即”部分初始化“问题，一部分域被初始化了。
(c)DCL 2.0，在instance上增加了volatile关键字。
(2)饿汉模式：类加载时初始化单例，以后访问时直接返回即可。
(3)Holder模式：通过静态的Holder类持有真正实例，间接实现了懒加载。
(4)枚举模式：本质上和饿汉模式相同，区别仅在于公有的静态成员变量。

2.单例模式使用场景

单例模式只允许创建一个对象，因此节省内存，加快对象访问速度，因此对象需要被公用的场合适合使用，如多个模块使用同一个数据源连接对象等等。如：
需要频繁实例化然后销毁的对象。
创建对象时耗时过多或者耗资源过多，但又经常用到的对象。
有状态的工具类对象。
频繁访问数据库或文件的对象。
资源共享的情况下，避免由于资源操作时导致的性能或损耗等。如上述中的日志文件，应用配置。
控制资源的情况下，方便资源之间的互相通信。如线程池等。

N.参考

(1)[单例模式有几种写法？](https://mp.weixin.qq.com/s/oltq10YKd_6pm4saqkOthA)

# 分布式

1.幂等性:系统对某接口的多次请求，都应该返回同样的结果！避免因为各种原因，重复请求导致的业务重复处理
场景案例:(a)客户端第一次请求后，网络异常导致收到请求执行逻辑但是没有返回给客户端，客户端的重新发起请求。(b)客户端迅速点击按钮提交，导致同一逻辑被多次发送到服务器。
对于查询，内部不包含其他操作，属于只读性质的那种业务必然符合幂等性要求的。
对于删除，重复做删除请求至少不会造成数据杂乱，不过也有些场景更希望重复点击提示的是删除成功，而不是目标不存在的提示。
对于新增，需要避免重复插入。
对于修改，需要避免进行无效的重复修改。
实现方式：客户端做某一请求的时候带上识别参数标识，服务端对此标识进行识别，重复请求则重复返回第一次的结果即可。比如添加请求的表单里，在打开添加表单页面的时候，就生成一个AddId标识，这个AddId跟着表单一起提交到后台接口。后台接口根据这个AddId，服务端就可以进行缓存标记并进行过滤，缓存值可以是AddId作为缓存key，返回内容作为缓存Value，这样即使添加按钮被多次点下也可以识别出来。这个AddId什么时候更新呢？只有在保存成功并且清空表单之后，才变更这个AddId标识，从而实现新数据的表单提交。

# 其他

1.单点登录

(1)当用户第一次登录时，将用户名密码发送给验证服务；
(2)验证服务将用户标识OpenId返回到客户端；
(3)客户端进行存储；
(4)访问子系统时，将OpenId发送到子系统；
(5)子系统将OpenId转发到验证服务；
(6)验证服务将用户认证信息返回给子系统；
(7)子系统构建用户验证信息后将授权后的内容返回给客户端。

# 综合参考

1.[Java最常见的200+面试题及自己梳理的答案--面试必备（一）](https://www.cnblogs.com/cocoxu1992/p/10460251.html)

2.[久伴_不离](https://www.jianshu.com/u/837b81b0eaa9)	

公众号文章到28