---
layout: post
title: "后端开发面试题 -- Java篇"
description: 后端开发面试题 -- Java篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# Java基础

1.==/equals()/hashCode()

(1)==比较左右两侧是否为同一对象，比较的是对象的地址。如果比较的是阿拉伯数字，则值相等则为true。
(2)equals()继承自Object对象，默认为==的比较。可Override。
(3)hashCode()继承自Object对象，用于计算对象散列值。Object对象默认hashCode为调用JVM的JNI方法，根据内存地址得到的值。
通常如果equals() override了，hashCode()也应该override，即equals相等，散列也应该相等。hashCode是用于散列数据的快速存取，如利用HashSet/HashMap/Hashtable类来存储数据时，都会根据存储对象的hashCode值来进行判断是否相同的。
(4)hashCode()和equals()之间的关系:
当不会创建“类对应的散列表”，即不会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中用到该类。在这种情况下，该类的“hashCode()和equals()没有半毛钱关系的。equals()用来比较该类的两个对象是否相等，而hashCode()则根本没有任何作用。
当会创建“类对应的散列表”，即会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中用到该类。在这种情况下，该类的“hashCode()和equals()”是有关系的，如果两个对象相等，那么它们的hashCode()值一定相同。这里的相等是指，通过equals()比较两个对象时返回true。如果两个对象hashCode()相等，它们并不一定相等。因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。补充说一句：“两个不同的键值对，哈希值相等”，这就是哈希冲突。此外，在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。

(4)运行时常量池。String类有一个intern()方法，它的作用就是将字符串存入常量池中，并且方法执行完后将这个字符串对象返回。如果已经存在，直接返回常量池中的对象。

    public class TestDemo {
        
        @Test
        public void test01() {
            //
            String str1 = new StringBuilder("hello").append("World").toString();
            System.out.println(str1.intern());
            System.out.println(str1 == str1.intern());
    
            String str2 = new StringBuilder("ja").append("va").toString();
            System.out.println(str2.intern());
            System.out.println(str2 == str2.intern());
    
            String str3 = new StringBuilder("hello").toString();
            System.out.println(str3.intern());
            System.out.println(str3 == str3.intern());
        }
        
    }
    
结果输出

    helloWorld 
    true // 因为str1被存入了常量池。左侧是new出来的对象，右侧常量池中存的也是new出来的对象
    java
    false // 因为"java"本来就存在与常量池中，左侧是new出来的对象，右侧是已经存在在常量池中的对象
    hello
    false // 因为hello此前已经存在常量池中，左侧是new出来的对象，右侧是在前面已经存在在常量池中的对象

2.运算

(1)Math.round()计算方法为先+0.5，然后向下取整。

(2)Java中float的精度为6-7位有效数字。double的精度为15-16位。我们在使用BigDecimal时，使用它的BigDecimal(String)构造器创建对象才有意义。其他的如BigDecimal b = new BigDecimal(1)这种，还是会发生精度丢失的问题。
所以我们一般使用BigDecimal来解决商业运算上丢失精度的问题的时候，声明BigDecimal对象的时候一定要使用它构造参数为String的类型的构造器。
同时这个原则Effective Java和MySQL必知必会中也都有提及。float和double只能用来做科学计算和工程计算。商业运算中我们要使用BigDecimal。

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

(1)反转：(a)字符数组反向拼接；(b)递归；(c)StringBuffer的reverse()方法。

(2)拼接：String类作为java语言中最常见的字符串类被广泛使用，如果在做大量字符串拼接效率时变得比较低，因为虚拟机需要不断地将对象引用指向新的地址。因此，一般方法内的私有变量推荐使用stringBuilder来完成，如果是多线程需要同步的自然选用stringBuffer。

(3)String有长度限制吗？
首先字符串的内容是由一个字符数组char[]来存储的，由于数组的长度及索引是整数，且String类中返回字符串长度的方法length()的返回值也是int，所以通过查看java源码中的类Integer我们可以看到Integer的最大范围是2^31-1,由于数组是从0开始的，所以数组的最大长度可以使【0~2^31】通过计算是大概4GB。
但是通过翻阅java虚拟机手册对class文件格式的定义以及常量池中对String类型的结构体定义我们可以知道对于索引定义了u2，就是无符号占2个字节，2个字节可以表示的最大范围是2^16-1=65535。
其实是65535，但是由于JVM需要1个字节表示结束指令，所以这个范围就为65534了。超出这个范围在编译时期是会报错的，但是运行时拼接或者赋值的话范围是在整形的最大范围。

4.抽象

(1)抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。

5.Object类的方法

(1)Object():构造方法
(2)registerNatives():为了使JVM发现本机功能，它们被一定的方式命名。例如，对于java.lang.Object.registerNatives，对应的C函数命名为Java_java_lang_Object_registerNatives。通过使用registerNatives（或者更确切地说，JNI函数RegisterNatives），可以命名任何你想要你的C函数。
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

7.Integer类型当正整数小于128时是在内存栈中创建值的，并将对象指向这个值，这样当比较两个栈引用时因为是同一地址引用两者则相等。
当大于127时将会调用new Integer()，两个整数对象地址引用不相等了。这就是为什么当值为128时不相等，当值为100时相等了。

8.JDK 8中的Optional

    import java.util.Optional;
    
    public class Java8Tester {
        public static void main(String args[]){
        
              Java8Tester java8Tester = new Java8Tester();
              Integer value1 = null;
              Integer value2 = new Integer(10);
                
              // Optional.ofNullable - 允许传递为 null 参数
              Optional<Integer> a = Optional.ofNullable(value1);
                
              // Optional.of - 如果传递的参数是 null，抛出异常 NullPointerException
              Optional<Integer> b = Optional.of(value2);
              System.out.println(java8Tester.sum(a,b));
        }
        
        public Integer sum(Optional<Integer> a, Optional<Integer> b){
        
              // Optional.isPresent - 判断值是否存在
                
              System.out.println("第一个参数值存在: " + a.isPresent());
              System.out.println("第二个参数值存在: " + b.isPresent());
                
              // Optional.orElse - 如果值存在，返回它，否则返回默认值
              Integer value1 = a.orElse(new Integer(0));
                
              //Optional.get - 获取值，值需要存在
              Integer value2 = b.get();
              return value1 + value2;
        }
    }
    
9.DO,DTO,VO等

DO（Domain Object），领域对象，也就是ORM框架中对应数据库的对象，业务实体。
VO（Value Object），就是用于保存数据的对象；在提供给页面使用的时候，也有人解释为View Object，就是对应页面展示数据的对象。
DTO（Data Transfer Object），数据传输对象，顾名思义就是用于传输数据的对象，通常用于处于不同架构层次或者不同子系统之间的数据传递，或者用于外部接口参数传递，以便提供不同粒度不同信息的数据。

例如：
controller层：public List<UserVO> getUsers(UserDTO userDto);
Service层：List<UserDTO> getUsers(UserDTO userDto);
DAO层：List<UserDTO> getUsers(UserDO userDo);

10.Java8中Stream对列表去重

distinct()是Java8中Stream提供的方法，返回的是由该流中不同元素组成的流。distinct()使用hashCode()和eqauls()方法来获取不同的元素。
因此，需要去重的类必须实现 hashCode() 和 equals() 方法。换句话讲，我们可以通过重写定制的hashCode()和equals()方法来达到某些特殊需求的去重。

对于String列表的去重

    @Test
    public void listDistinctByStreamDistinct() {
      // 1. 对于 String 列表去重
      List<String> stringList = new ArrayList<String>() {{
        add("A");
        add("A");
        add("B");
        add("B");
        add("C");
      }};
      out.print("去重前：");
      for (String s : stringList) {
        out.print(s);
      }
      out.println();
      stringList = stringList.stream().distinct().collect(Collectors.toList());
      out.print("去重后：");
      for (String s : stringList) {
        out.print(s);
      }
      out.println();
    }

实体类列表的去重，代码中我们使用了 Lombok 插件的 @Data注解，可自动覆写equals()以及hashCode()方法。

    @Data
    public class Student {
      private String stuNo;
      private String name;
    }
    
    @Test
    public void listDistinctByStreamDistinct() throws JsonProcessingException {
        ObjectMapper objectMapper = new ObjectMapper();
        // 1. 对于 Student 列表去重
        List<Student> studentList = getStudentList();
        out.print("去重前：");
        out.println(objectMapper.writeValueAsString(studentList));
        studentList = studentList.stream().distinct().collect(Collectors.toList());
        out.print("去重后：");
        out.println(objectMapper.writeValueAsString(studentList));
     }

N.参考

(1)[Java 7 API](https://docs.oracle.com/javase/7/docs/api/)

(2)[廖雪峰Java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)

(3)[Java 8 Optional类](https://www.runoob.com/java/java8-optional-class.html)

(4)[理解、学习与使用JAVA中的 OPTIONAL](https://www.cnblogs.com/zhangboyu/p/7580262.html)

# IO

1.InputStream：
三种基本的介质流ByteArrayInputStream，StringBufferInputStream，FileInputStream，它们分别从Byte数组、StringBuffer、和本地文件中读取数据。
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

8.零拷贝：如果简单用java里面对象的概念来理解的话，其实就是使用的都是对象的引用，每个引用对象的地方对其改变就都能改变此对象，永远只存在一份对象。
MappedByteBuffer：java nio提供的FileChannel提供了map()方法，该方法可以在一个打开的文件和MappedByteBuffer之间建立一个虚拟内存映射，MappedByteBuffer继承于ByteBuffer，类似于一个基于内存的缓冲区，只不过该对象的数据元素存储在磁盘的一个文件中；调用get()方法会从磁盘中获取数据，此数据反映该文件当前的内容，调用put()方法会更新磁盘上的文件，并且对文件做的修改对其他阅读者也是可见的。

    public class MappedByteBufferTest {  
      
        public static void main(String[] args) throws Exception {  
            File file = new File("D://db.txt");  
            long len = file.length();  
            byte[] ds = new byte[(int) len];  
            MappedByteBuffer mappedByteBuffer = new FileInputStream(file).getChannel().map(FileChannel.MapMode.READ_ONLY, 0,  len);  
            for (int offset = 0; offset < len; offset++) {  
                byte b = mappedByteBuffer.get();  
                ds[offset] = b;  
            }  
            Scanner scan = new Scanner(new ByteArrayInputStream(ds)).useDelimiter(" ");  
            while (scan.hasNext()) {  
                System.out.print(scan.next() + " ");  
            }  
        }  
    }  

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

3.TreeMap

TreeMap<K,V>的Key值是要求实现java.lang.Comparable，所以迭代的时候TreeMap默认是按照Key值升序排序的；TreeMap的实现是基于红黑树结构。适用于按自然顺序或自定义顺序遍历键（key）。添加到SortedMap实现类的元素必须实现Comparable接口，否则必须给它的构造函数提供一个Comparator接口的实现。
基于红黑树实现。TreeMap没有调优选项，因为该树总处于平衡状态。

    (a)TreeMap()：构建一个空的映像树
    (b)TreeMap(Map m): 构建一个映像树，并且添加映像m中所有元素
    (c)TreeMap(Comparator c): 构建一个映像树，并且使用特定的比较器对关键字进行排序
    (d)TreeMap(SortedMap s): 构建一个映像树，添加映像树s中所有映射，并且使用与有序映像s相同的比较器排序

4.HashMap

构造函数：

    (a)HashMap(): 构建一个空的哈希映像
    (b)HashMap(Map m): 构建一个哈希映像，并且添加映像m的所有映射
    (c)HashMap(int initialCapacity): 构建一个拥有特定容量的空的哈希映像
    (d)HashMap(int initialCapacity, float loadFactor): 构建一个拥有特定容量和加载因子的空的哈希映像

关于线程安全：HashMap不是线程安全的，如果想要线程安全的HashMap，可以通过Collections类的静态方法synchronizedMap获得线程安全的HashMap。除了不同步和允许使用null之外，HashMap类与Hashtable大致相同。在JDK7中，在多线程环境下，扩容时会造成环形链或数据丢失。在JDK8中，在多线程环境下，会发生数据覆盖的情况。

JDK7实现：基于数组和链表来实现的，通过计算散列码来决定存储的位置。HashMap中主要是通过key的hashCode来计算hash值的。如果存储的对象对多了，就有可能不同的对象所算出来的hash值是相同的，这就出现了所谓的hash冲突。HashMap底层是通过链表来解决hash冲突的。哈希数组中每个元素都是一个单链表的头节点，链表是用来解决冲突的，如果不同的key映射到了数组的同一位置处，就将其放入单链表中。

JDK8实现：基于位桶+链表/红黑树的方式实现，也是非线程安全的。当某个位桶的链表的长度达到某个阀值的时候，这个链表就将转换成红黑树。

loadFactor加载因子：是表示Hash表中元素的填满的程度。若加载因子越大填满的元素越多，好处是空间利用率高了但冲突的机会加大了。链表长度会越来越长，查找效率降低。因此，必须在 "冲突的机会"与"空间利用率"之间寻找一种平衡与折衷。不过一般我们都不用去设置它，让它取默认值0.75就好了。

5.ArrayList和LinkedList

(1)对ArrayList和LinkedList而言，在列表末尾增加一个元素所花的开销都是固定的。
对ArrayList而言，主要是在内部数组中增加一项，指向所添加的元素，偶尔可能会导致对数组重新进行分配；而对LinkedList而言，这个开销是统一的，分配一个内部Entry对象。
(2)在ArrayList的中间插入或删除一个元素意味着这个列表中剩余的元素都会被移动；而在LinkedList的中间插入或删除一个元素的开销是固定的。
(3)LinkedList不支持高效的随机元素访问。
(4)ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗相当的空间。

总结：当操作是在一列数据的后面添加数据而不是在前面或中间,并且需要随机地访问其中的元素时,使用ArrayList会有更好的性能；当操作是在一列数据的前面或中间添加或删除数据,并且按照顺序访问其中的元素时,就应该使用LinkedList了。

6.List中删除元素

(1)倒序循环，因为list删除只会导致当前元素之后的元素位置发生改变，所以采用倒序可以保证前面的元素没有变化；

(2)顺序循环时，删除当前位置的值，下一个值就会补到当前位置，所以需要执行i–操作；

(3)注意必须用迭代器的remove()方法，不要用list的remove，不然会发生java.util.ConcurrentModificationException异常。

    if (null != list && list.size() > 0) {
        Iterator it = list.iterator();  
        while(it.hasNext()){
            Student stu = (Student)it.next(); 
            if (stu.getStudentId() == studentId) {
                it.remove(); //移除该对象
            }
        }
    }
    
7.Arrays.asList()方法返回的ArrayList不是java.util包下的，而是java.util.Arrays.ArrayList，显然它是Arrays类自己定义的一个内部类！这个内部类没有实现add()、remove()方法，而是直接使用它的父类AbstractList的相应方法，抛出UnsupportedOperationException。
Arrays.asList()返回的ArrayList继承自AbstractList，它仅支持那些不会改变数组大小的操作，所以任何对底层数据结构的尺寸进行修改的方法都会出现异常，Arrays.asList()返回固定尺寸的List。
  
如何才能避免这个错误：thinking in java给的解释是：把Arrays.asList()的结果作为构造器的参数传递给任何Collection。

    List<Integer> list = new ArrayList<>(Arrays.asList(1,2,3)); 
    
8.ConcurrentHashMap

JDK7实现：ConcurrentHashMap的数据结构是由一个Segment数组和多个HashEntry组成，Segment数组的意义就是将一个大的table分割成多个小的table来进行加锁，也就是锁分离技术，而每一个Segment元素存储的是HashEntry数组+链表，这个和HashMap的数据存储结构一样。
ConcurrentHashMap采用了分段锁技术，其中Segment继承于ReentrantLock。不会像HashTable那样不管是put还是get操作都需要做同步处理，理论上ConcurrentHashMap支持CurrencyLevel(Segment 数组数量)的线程并发。每当一个线程占用锁访问一个Segment时，不会影响到其他的Segment。

JDK8实现：摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用Synchronized和CAS来操作，整个看起来就像是优化过且线程安全的HashMap，虽然在JDK8中还能看到Segment的数据结构，但是已经简化了属性，只是为了兼容旧版本
Node是ConcurrentHashMap存储结构的基本单元，继承于HashMap中的Entry，用于存储数据。

ConcurrentHashMap的限制：诸如size、isEmpty和containsValue等聚合方法，在并发下可能会反映ConcurrentHashMap的中间状态。因此在并发情况下，这些方法的返回值只能用作参考，而不能用于流程控制。如果需要确保需要手动加锁。诸如putAll这样的聚合方法也不能确保原子性，在putAll的过程中去获取数据可能会获取到部分数据。
ConcurrentHashMap这篮子本身，可以确保多个工人在装东西进去时，不会相互影响干扰，但无法确保工人A看到还需要装100个桔子但是还未装时，工人B就看不到篮子中的桔子数量。你往这个篮子装100个桔子的操作不是原子性的，在别人看来可能会有一个瞬间篮子里有964个桔子，还需要补36个桔子。

小结：
JDK8的实现降低锁的粒度，JDK7版本锁的粒度是基于Segment的，包含多个HashEntry，而JDK8锁的粒度就是HashEntry（首节点）。
JDK8版本的数据结构变得更加简单，使得操作也更加清晰流畅，因为已经使用synchronized来进行同步，所以不需要分段锁的概念，也就不需要Segment这种数据结构了，由于粒度的降低，实现的复杂度也增加了。
JDK8使用红黑树来优化链表，基于长度很长的链表的遍历是一个很漫长的过程，而红黑树的遍历效率是很快的，代替一定阈值的链表，这样形成一个最佳拍档。
JDK8为什么使用内置锁synchronized来代替重入锁ReentrantLock，我觉得有以下几点
因为粒度降低了，在相对而言的低粒度加锁方式，synchronized并不比ReentrantLock差，在粗粒度加锁中ReentrantLock可能通过Condition来控制各个低粒度的边界，更加的灵活，而在低粒度中，Condition的优势就没有了。
JVM的开发团队从来都没有放弃synchronized，而且基于JVM的synchronized优化空间更大，使用内嵌的关键字比使用API更加自然。
在大量的数据操作下，对于JVM的内存压力，基于API的ReentrantLock会开销更多的内存，虽然不是瓶颈，但是也是一个选择依据。

9.CopyOnWriteArrayList

CopyOnWriteArrayList适合于多线程场景下使用，其采用读写分离的思想，读操作不上锁，写操作上锁，且写操作效率较低。
CopyOnWriteArrayList基于fail-safe机制，每次修改都会在原先基础上复制一份，修改完毕后在进行替换。
CopyOnWriteArrayList采用的是ReentrantLock进行上锁。
CopyOnWriteArrayList虽然是一个线程安全版的ArrayList，但其每次修改数据时都会复制一份数据出来，所以只适用读多写少或无锁读场景。高并发写时，CopyOnWriteArray比同步ArrayList慢，高并发读时CopyOnWriteArray比同步ArrayList快。

N.参考

(1)[Java容器详解](https://www.jianshu.com/p/8ef342da8732)

(2)[Java容器（接口）](https://cloud.tencent.com/developer/article/1334703)

(3)[Java容器（实现）](https://cloud.tencent.com/developer/article/1334702)

(4)[【16期】你能谈谈HashMap怎样解决hash冲突吗](https://mp.weixin.qq.com/s/dgYriZ-obbm1CoUDjHofhg)

(5)[HashMap？ConcurrentHashMap？相信看完这篇没人能难住你！](https://blog.csdn.net/weixin_44460333/article/details/86770169)

(6)[ConcurrentHashMap底层实现原理(JDK1.7 & 1.8)](https://www.jianshu.com/p/865c813f2726)

(7)[JDK7与JDK8中HashMap的实现](https://my.oschina.net/hosee/blog/618953)

(8)[ConcurrentHashMap总结](https://my.oschina.net/hosee/blog/675884)

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

bype/char/short/int/long
/boolean/float/double.class及void.class
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

1.异常的分类：

(1)Throwable：
所有错误与异常的超类。两个子类：Error（错误）和 Exception（异常），包含了其线程创建时线程执行堆栈的快照，它提供了printStackTrace()等接口用于获取堆栈跟踪数据等信息。

(2)Error：
程序中无法处理的错误，表示运行应用程序中出现了严重的错误。
此类错误一般表示代码运行时JVM出现问题。通常有VirtualMachineError（虚拟机运行错误）（比如 OutOfMemoryError：内存不足错误；StackOverflowError：栈溢出错误）、NoClassDefFoundError（类定义错误）等。此类错误发生时，JVM将终止线程。
这些错误是不受检异常，非代码性错误。因此，当此类错误发生时，应用程序不应该去处理此类错误。按照Java惯例，我们是不应该实现任何新的Error子类的！

(3)Exception：
程序本身可以捕获并且可以处理的异常。分为两类：运行时异常和编译时异常。

RuntimeException：运行时异常表示JVM在运行期间可能出现的异常。这类异常是编程人员的逻辑问题。
Java编译器不会检查它，也就是说当程序中可能出现这类异常时，倘若既没有通过throws声明抛出它，也没有用try-catch语句捕获它，还是会编译通过。（可写可不写）
比如NullPointerException空指针异常、ArrayIndexOutBoundException数组下标越界异常、ClassCastException类型转换异常、ArithmeticExecption算术异常。
RuntimeException异常会由Java虚拟机自动抛出并自动捕获（就算我们没写异常捕获语句运行时也会抛出错误！！），此类异常的出现绝大数情况是代码本身有问题应该从逻辑上去解决并改进代码。

非RuntimeException：编译时异常，除RuntimeException及其子类之外的异常。这类异常是由一些外部的偶然因素所引起的。
Java编译器会检查它。如果程序中出现此类异常，要么通过throws进行声明抛出，要么通过try-catch进行捕获处理，否则不能通过编译。在程序中，通常不会自定义该类异常，而是直接使用系统提供的异常类。该异常我们必须手动在代码里添加捕获语句来处理该异常。
例如Exception，FileNotFoundException，IOException，SQLException。

(4)受检异常与非受检异常
Java 的所有异常可以分为受检异常（checked exception）和非受检异常（unchecked exception）。

受检异常：编译器要求必须处理的异常。正确的程序在运行过程中，经常容易出现的、符合预期的异常情况。一旦发生此类异常，就必须采用某种方式进行处理。除RuntimeException及其子类外，其他的Exception异常都属于受检异常。编译器会检查此类异常，也就是说当编译器检查到应用中的某处可能会此类异常时，将会提示你处理本异常——要么使用try-catch捕获，要么使用方法签名中用throws关键字抛出，否则编译不通过。
非受检异常：编译器不会进行检查并且不要求必须处理的异常，也就说当程序中出现此类异常时，即使我们没有try-catch捕获它，也没有使用throws抛出该异常，编译也会正常通过。该类异常包括运行时异常（RuntimeException极其子类）和错误（Error）。

2.异常相关关键字

try – 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。
catch – 用于捕获异常。catch用来捕获try语句块中发生的异常。
finally – finally语句块总是会被执行。它主要用于回收在try块里打开的物力资源(如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。
throw – 用于抛出异常。
throws – 用在方法签名中，用于声明该方法可能抛出的异常。

throw和throws都是消极处理异常的方式，只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。

3.异常的处理方式：
(1)捕获并处理：在异常的代码附近用try/catch进行处理,运行时系统捕获后会查询相应的catch处理块，再catch处理块中对该异常进行处理。
(2)查看发生异常的方法是否有向上声明异常，有向上声明，向上级查询处理语句，如果没有向上声明，JVM中断程序的运行并处理。用throws向外声明。throws可以声明方法可能会抛出一个或多个异常，异常之间用'，'隔开。



6.自定义异常：当需要一些跟特定业务相关的异常信息类时。可以继承继承Exception来定义一个受检异常。也可以继承自RuntimeException或其子类来定义一个非受检异常。

7.finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是记录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，就会返回修改后的值。显然，在finally中返回或者修改返回值会对程序造成很大的困扰。
即使catch中包含了return语句，finally子句依然会执行。若finally中也包含return语句，finally中的return会覆盖前面的return。

    public static int getInt() {
        int a = 10;
        try {
            System.out.println(a / 0);
            a = 20;
        } catch (ArithmeticException e) {
            a = 30;
            return a;
            /*
             * return a 在程序执行到这一步的时候，这里不是return a 而是 return 30；这个返回路径就形成了
             * 但是呢，它发现后面还有finally，所以继续执行finally的内容，a=40
             * 再次回到以前的路径,继续走return 30，形成返回路径之后，这里的a就不是a变量了，而是常量30
             */
        } finally {
            a = 40;
        }
     return a;
    }
    
执行结果：30

    public static int getInt() {
        int a = 10;
        try {
            System.out.println(a / 0);
            a = 20;
        } catch (ArithmeticException e) {
            a = 30;
            return a;
        } finally {
            a = 40;
            //如果这样，就又重新形成了一条返回路径，由于只能通过1个return返回，所以这里直接返回40
            return a; 
        }
    
    }
    
执行结果：40

8.JAVA 7提供了更优雅的方式来实现资源的自动释放，自动释放的资源需要是实现了AutoCloseable接口的类。try代码块退出时，会自动调用scanner.close方法，和把scanner.close方法放在finally代码块中不同的是，若scanner.close抛出异常，则会被抑制，抛出的仍然为原始异常。

    private  static void tryWithResourceTest(){
        try (Scanner scanner = new Scanner(new FileInputStream("c:/abc"),"UTF-8")){
            // code
        } catch (IOException e){
            // handle exception
        }
    }

# 并发与多线程

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
(4)实现Callable接口，通过如下方式获取线程执行结果

    FutureTask<Boolean> futureTask = new FutureTask<Boolean>(new SubscribeThreadWithResult(product, config));
    Thread thread = new Thread(futureTask);
    thread.start();
    bollean result = futureTask.get();
    

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

9.线程池

(1)线程池优点：
(a)线程池的重用:线程的创建和销毁的开销是巨大的，而通过线程池的重用大大减少了这些不必要的开销，当然既然少了这么多消费内存的开销，其线程执行速度也是突飞猛进的提升。
(b)控制线程池的并发数:控制线程池的并发数可以有效的避免大量的线程池争夺CPU资源而造成堵塞。
(c)线程池可以对线程进行管理：线程池可以提供定时、定期、单线程、并发数控制等功能。

(2)ThreadPoolExecutor，构造函数如下

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

执行流程：
当currentSize<corePoolSize时，没什么好说的，直接启动一个核心线程并执行任务。
当currentSize>=corePoolSize、并且workQueue未满时，添加进来的任务会被安排到workQueue中等待执行。
当workQueue已满，但是currentSize<maximumPoolSize时，会立即开启一个非核心线程来执行任务。
当currentSize>=corePoolSize、workQueue已满、并且currentSize>maximumPoolSize时，调用handler，执行线程池饱和策略，默认抛出RejectExecutionExpection异常。

线程池饱和策略分为以下几种：
AbortPolicy:直接抛出一个异常，默认策略
DiscardPolicy: 直接丢弃任务
DiscardOldestPolicy:抛弃下一个将要被执行的任务(最旧任务)
CallerRunsPolicy:主线程中执行任务

几种典型的工作队列：
ArrayBlockingQueue:使用数组实现的有界阻塞队列，特性先进先出
LinkedBlockingQueue:使用链表实现的阻塞队列，特性先进先出，可以设置其容量，默认为Interger.MAX_VALUE，特性先进先出
PriorityBlockingQueue:使用平衡二叉树堆，实现的具有优先级的无界阻塞队列
DelayQueue:无界阻塞延迟队列，队列中每个元素均有过期时间，当从队列获取元素时，只有过期元素才会出队列。队列头元素是最块要过期的元素。
SynchronousQueue:一个不存储元素的阻塞队列，每个插入操作，必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态。

几种典型的线程池：
FixedThreadPool:Fixed中文解释为固定。结合在一起解释固定的线程池，说的更全面点就是，有固定数量线程的线程池。其corePoolSize=maximumPoolSize，且keepAliveTime为0，适合线程稳定的场景。使用LinkedBlockingQueue。
SingleThreadPool:Single中文解释为单一。结合在一起解释单一的线程池，说的更全面点就是，有固定数量线程的线程池，且数量为一，从数学的角度来看SingleThreadPool应该属于FixedThreadPool的子集。其corePoolSize=maximumPoolSize=1,且keepAliveTime为0，适合线程同步操作的场所。使用LinkedBlockingQueue。
CachedThreadPool:Cached中文解释为储存。结合在一起解释储存的线程池，说的更通俗易懂，既然要储存，其容量肯定是很大，所以它的corePoolSize=0，maximumPoolSize=Integer.MAX_VALUE(2^32-1一个很大的数字)。使用SynchronousQueue。
ScheduledThreadPool:Scheduled中文解释为计划。结合在一起解释计划的线程池，顾名思义既然涉及到计划，必然会涉及到时间。所以ScheduledThreadPool是一个具有定时定期执行任务功能的线程池。使用DelayedWorkQueue。

使用无界队列的线程池会导致内存飙升吗？
会的，newFixedThreadPool使用了无界的阻塞队列LinkedBlockingQueue，如果线程获取一个任务后，任务的执行时间比较长，会导致队列的任务越积越多，导致机器内存使用不停飙升， 最终导致OOM。

11.锁的分类与区别

(1)公平锁/非公平锁：
公平锁是指多个线程按照申请锁的顺序来获取锁。
非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能会造成优先级反转或者饥饿现象。
对于Java ReentrantLock而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。
对于Synchronized而言，也是一种非公平锁。由于其并不像ReentrantLock是通过AQS的来实现线程调度，所以并没有任何办法使其变成公平锁。

(2)可重入锁：可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。
对于Java ReentrantLock而言, 从名字就可以看出是一个可重入锁，其名字是Re entrant Lock重新进入锁。
对于Synchronized而言，也是一个可重入锁。可重入锁的一个好处是可一定程度避免死锁。下面的代码就是一个可重入锁的一个特点，如果不是可重入锁的话，setB可能不会被当前线程执行，可能造成死锁。

    synchronized void setA() throws Exception{
        Thread.sleep(1000);
        setB();
    }
    
    synchronized void setB() throws Exception{
        Thread.sleep(1000);
    }

(3)独享锁/共享锁：
独享锁是指该锁一次只能被一个线程所持有。共享锁是指该锁可被多个线程所持有。
对于Java ReentrantLock而言，其是独享锁。但是对于Lock的另一个实现类ReadWriteLock，其读锁是共享锁，其写锁是独享锁。读锁的共享锁可保证并发读是非常高效的，读写，写读 ，写写的过程是互斥的。
独享锁与共享锁也是通过AQS来实现的，通过实现不同的方法，来实现独享或者共享。
对于Synchronized而言，当然是独享锁。

(4)互斥锁/读写锁
上面讲的独享锁/共享锁就是一种广义的说法，互斥锁/读写锁就是具体的实现。
互斥锁在Java中的具体实现就是ReentrantLock。
读写锁在Java中的具体实现就是ReadWriteLock。

(5)乐观锁/悲观锁
乐观锁与悲观锁不是指具体的什么类型的锁，而是指看待并发同步的角度。
悲观锁认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。因此对于同一个数据的并发操作，悲观锁采取加锁的形式。悲观的认为，不加锁的并发操作一定会出问题。
乐观锁则认为对于同一个数据的并发操作，是不会发生修改的。在更新数据的时候，会采用尝试更新，不断重试的方式更新数据。乐观的认为，不加锁的并发操作是没有事情的。
从上面的描述我们可以看出，悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。
悲观锁在Java中的使用，就是利用各种锁。
乐观锁在Java中的使用，是无锁编程，常常采用的是CAS算法，典型的例子就是原子类，通过CAS自旋实现原子操作的更新。

(6)分段锁
分段锁其实是一种锁的设计，并不是具体的一种锁，对于ConcurrentHashMap而言，其并发的实现就是通过分段锁的形式来实现高效的并发操作。
我们以ConcurrentHashMap来说一下分段锁的含义以及设计思想，ConcurrentHashMap中的分段锁称为Segment，它即类似于HashMap（JDK7与JDK8中HashMap的实现）的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表；同时又是一个ReentrantLock（Segment继承了ReentrantLock)。
当需要put元素的时候，并不是对整个hashmap进行加锁，而是先通过hashcode来知道它要放在那一个分段中，然后对这个分段进行加锁，所以当多线程put的时候，只要不是放在一个分段中，就实现了真正的并行的插入。
但是，在统计size的时候，可就是获取hashmap全局信息的时候，就需要获取所有的分段锁才能统计。
分段锁的设计目的是细化锁的粒度，当操作不需要更新整个数组的时候，就仅仅针对数组中的一项进行加锁操作。

(7)偏向锁/轻量级锁/重量级锁
这三种锁是指锁的状态，并且是针对Synchronized。
在Java 5通过引入锁升级的机制来实现高效Synchronized。这三种锁的状态是通过对象监视器在对象头中的字段来表明的。
偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。
轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。
重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让其他申请的线程进入阻塞，性能降低。

(8)自旋锁
在Java中，自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。

12.ThreadLocal

    static final ThreadLocal<T> sThreadLocal = new ThreadLocal<T>();
    sThreadLocal.set(T)
    sThreadLocal.get()
    sThreadLocal.remove()
    
ThreadLocal的静态内部类ThreadLocalMap为每个Thread都维护了一个数组table，ThreadLocal确定了一个数组下标，而这个下标就是value存储的对应位置。
对于某一ThreadLocal来讲，它的索引值i是确定的，在不同线程之间访问时访问的是不同的table数组的同一位置即都为table[i]，只不过这个不同线程之间的table是独立的。
对于同一线程的不同ThreadLocal来讲，这些ThreadLocal实例共享一个table数组，然后每个ThreadLocal实例在table中的索引i是不同的。
ThreadLocal和Synchronized都是为了解决多线程中相同变量的访问冲突问题，不同的点是Synchronized是通过线程等待，牺牲时间来解决访问冲突。ThreadLocal是通过每个线程单独一份存储空间，牺牲空间来解决冲突，并且相比于Synchronized，ThreadLocal具有线程隔离的效果，只有在线程内才能获取到对应的值，线程外则不能访问到想要的值。

13.Java的线程安全与不安全

多个线程之间是不能直接传递数据进行交互的，它们之间的交互只能通过共享变量来实现。
例如在多个线程之间共享了Count类的一个实例，这个对象是被创建在主内存（堆内存）中，每个线程都有自己的工作内存（线程栈），工作内存存储了主内存count对象的一个副本，当线程操作count对象时，首先从主内存复制count对象到工作内存中，然后执行代码count.count()，改变了num值，最后用工作内存中的count刷新主内存的count。当一个对象在多个工作内存中都存在副本时，如果一个工作内存刷新了主内存中的共享变量，其它线程也应该能够看到被修改后的值，此为可见性。
多个线程执行时，CPU对线程的调度是随机的，我们不知道当前程序被执行到哪步就切换到了下一个线程，一个最经典的例子就是银行汇款问题，一个银行账户存款100，这时一个人从该账户取10元，同时另一个人向该账户汇10元，那么余额应该还是100。那么此时可能发生这种情况，A线程负责取款，B线程负责汇款，A从主内存读到100，B从主内存读到100，A执行减10操作，并将数据刷新到主内存，这时主内存数据100-10=90，而B内存执行加10操作，并将数据刷新到主内存，最后主内存数据100+10=110，显然这是一个严重的问题，我们要保证A线程和B线程有序执行，先取款后汇款或者先汇款后取款，此为有序性。

在Web开发方面，Servlet是否是线程安全的呢？
Servlet不是线程安全的。要解释为什么Servlet为什么不是线程安全的，需要了解Servlet容器（如Tomcat）是如何响应HTTP请求的。当Tomcat接收到Client的HTTP请求时，Tomcat从线程池中取出一个线程，之后找到该请求对应的Servlet对象并进行初始化，之后调用service()方法。
要注意的是每一个Servlet对象在Tomcat容器中只有一个实例对象，即是单例模式。如果多个HTTP请求请求的是同一个Servlet，那么这两个HTTP请求对应的线程将并发调用Servlet的service()方法。如果的Thread1和Thread2调用了同一个Servlet1，Servlet1中定义了成员变量或静态变量，那么可能会发生线程安全问题（因为所有的线程都可能使用这些变量）。
像Servlet这样的类，在Web容器中创建以后，会被传递给每个访问Web应用的用户线程执行，这个类就不是线程安全的。但这并不意味着一定会引发线程安全问题，如果Servlet类里没有成员变量，即使多线程同时执行这个Servlet实例的方法，也不会造成成员变量冲突。
这种对象被称作无状态对象，也就是说对象不记录状态，执行这个对象的任何方法都不会改变对象的状态，也就不会有线程安全问题了。事实上，Web开发实践中，常见的Service类、DAO类，都被设计成无状态对象，所以虽然我们开发的Web应用都是多线程的应用，因为Web容器一定会创建多线程来执行我们的代码，但是我们开发中却可以很少考虑线程安全的问题。

N.参考

(1)[线程进程区别](https://www.cnblogs.com/toria/p/11123130.html)

(2)[线程与进程的区别](https://www.cnblogs.com/cocoxu1992/p/10468317.html)

(3)[谈谈什么是守护线程以及作用](https://www.jianshu.com/p/3d6f32af5625)

(4)[ThreadLocal](https://www.jianshu.com/p/3c5d7f09dfbd)

(5)[【260期】Java线程池，这篇能让你和面试官聊了半小时](https://mp.weixin.qq.com/s/fUcTHmYE8yAZX041LEiajQ)

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
栈使用的是一级缓存， 它们通常都是被调用时处于存储空间中，调用完毕立即释放。
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