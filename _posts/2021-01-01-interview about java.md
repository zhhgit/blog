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

(5)运行时常量池。String类有一个intern()方法，它的作用就是将字符串存入常量池中，并且方法执行完后将这个字符串对象返回。如果已经存在，直接返回常量池中的对象。

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

    Object():构造方法
    registerNatives():为了使JVM发现本机功能，它们被一定的方式命名。例如，对于java.lang.Object.registerNatives，对应的C函数命名为Java_java_lang_Object_registerNatives。通过使用registerNatives（或者更确切地说，JNI函数RegisterNatives），可以命名任何你想要你的C函数。
    clone():用来另存一个当前存在的对象。只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。
    getClass():final方法，用于获得运行时的类型。该方法返回的是此Object对象的类对象/运行时类对象Class。效果与Object.class相同。
    equals():用来比较两个对象的内容是否相等。默认情况下(继承自Object类)，equals和==是一样的，除非被覆写(override)了。
    hashCode():用来返回其所在对象的物理地址（哈希码值），常会和equals方法同时重写，确保相等的两个对象拥有相等的hashCode。作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。
    toString():返回该对象的字符串表示。
    wait():导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法。
    wait(long timeout):导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法，或者超过指定的时间量。
    wait(long timeout, int nanos):导致当前的线程等待，直到其他线程调用此对象的notify()方法或notifyAll()方法，或者其他某个线程中断当前线程，或者已超过某个实际时间量。
    notify():唤醒在此对象监视器上等待的单个线程。
    notifyAll():唤醒在此对象监视器上等待的所有线程。
    finalize():当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。

6.Comparable与Comparator

如果实现类没有实现Comparable接口，又想对两个类进行比较（或者实现类实现了Comparable接口，但是对compareTo方法内的比较算法不满意），那么可以实现Comparator接口，自定义一个比较器，写比较算法。
实现Comparable接口的方式比实现Comparator接口的耦合性要强一些，如果要修改比较算法，要修改Comparable接口的实现类，而实现Comparator的类是在外部进行比较的，不需要对实现类有任何修改。因此：
对于一些普通的数据类型（比如 String, Integer, Double…），它们默认实现了Comparable接口，实现了compareTo方法，我们可以直接使用。
而对于一些自定义类，它们可能在不同情况下需要实现不同的比较策略，我们可以新创建Comparator接口，然后使用特定的Comparator实现进行比较。

    // Comparable
    public class Domain implements Comparable<Domain>{
       private String str;
    
       public Domain(String str){
           this.str = str;
       }
    
       public int compareTo(Domain domain){
           if (this.str.compareTo(domain.str) > 0)
               return 1;
           else if (this.str.compareTo(domain.str) == 0)
               return 0;
           else 
               return -1;
       }
    
       public String getStr(){
           return str;
       }
    }

    // Comparator
    public class DomainComparator implements Comparator<Domain>{
       public int compare(Domain domain1, Domain domain2){
           if (domain1.getStr().compareTo(domain2.getStr()) > 0)
               return 1;
           else if (domain1.getStr().compareTo(domain2.getStr()) == 0)
               return 0;
           else 
               return -1;
       }
    }

如何对一组对象进行排序？
如果我们需要对一个对象数组进行排序，我们可以使用Arrays.sort()方法。如果我们需要排序一个对象列表，我们可以使用Collections.sort()方法。
两个类都有用于自然排序（使用Comparable）或基于标准的排序（使用Comparator）的重载方法sort()。Collections内部使用数组排序方法，所以它们两者都有相同的性能，只是Collections需要花时间将列表转换为数组。

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

# Java IO

1.InputStream：
三种基本的介质流ByteArrayInputStream，StringBufferInputStream，FileInputStream，它们分别从Byte数组、StringBuffer、和本地文件中读取数据。
PipedInputStream从与其它线程共用的管道中读取数据。
ObjectInputStream和所有FilterInputStream的子类都是装饰流。
FilterInputStream子类包括BufferedInputStream能为输入流提供缓冲区，DataInputStream可以从输入流中读取Java基本类型数据，PushBackInputStream可以把读取到的字节重新推回到InputStream中。

2.OutputStream：ByteArrayOutputStream、FileOutputStream是两种基本的介质流，它们分别向Byte数组和本地文件中写入数据。
PipedOutputStream是向与其它线程共用的管道中写入数据。
ObjectOutputStream 和所有FilterOutputStream的子类都是装饰流。

3.File类

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

4.序列化与反序列化

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
    
为什么需要序列化与反序列化？

(1)对象序列化可以实现分布式对象。主要应用例如：RMI(即远程调用Remote Method Invocation)要利用对象序列化运行远程主机上的服务，就像在本地机上运行对象时一样。
(2)java对象序列化不仅保留一个对象的数据，而且递归保存对象引用的每个对象的数据。可以将整个对象层次写入字节流中，可以保存在文件中或在网络连接上传递。利用对象序列化可以进行对象的"深复制"，即复制对象本身及引用的对象本身。序列化一个对象可能得到整个对象序列。
(3)序列化可以将内存中的类写入文件或数据库中。

5.零拷贝：如果简单用java里面对象的概念来理解的话，其实就是使用的都是对象的引用，每个引用对象的地方对其改变就都能改变此对象，永远只存在一份对象。
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
    
6.serialVersionUID

serialVersionUID适用于Java的序列化机制。简单来说，Java的序列化机制是通过判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常，即是InvalidCastException。

生成方式：
默认的1L，比如：private static final long serialVersionUID = 1L;
根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段，比如：private static final  long   serialVersionUID = xxxxL;
当实现java.io.Serializable接口的类没有显式地定义一个serialVersionUID变量时候，Java序列化机制会根据编译的Class自动生成一个serialVersionUID作序列化版本比较用，这种情况下，如果Class文件(类名，方法明等)没有发生变化(增加空格，换行，增加注释等等)，就算再编译多次，serialVersionUID也不会变化的。

几种情况：
情况一：A端序列化后传输到B端反序列化。如果A端增加一个字段，A端增加的字段丢失(被B端忽略)。
情况二：A端序列化后传输到B端反序列化。如果B端减少一个字段，A端不变，A端多的字段值丢失(被B端忽略)。
情况三：A端序列化后传输到B端反序列化。如果B端增加一个字段，A端不变，B端新增加的int字段被赋予了默认值0。
情况四：将对象序列化后，修改静态变量的数值，再将序列化对象读取出来，然后通过读取出来的对象获得静态变量的数值并打印出来。应该是修改后的值。因为序列化时，并不保存静态变量，这其实比较容易理解，序列化保存的是对象的状态，静态变量属于类的状态，因此序列化并不保存静态变量。
情况五：父类的序列化。一个子类实现了Serializable接口，它的父类没有实现Serializable接口，序列化该子类对象，然后反序列化后输出父类定义的某变量的数值，该变量数值与序列化时的数值不同。因为在父类没有实现Serializable接口时，虚拟机是不会序列化父对象的，而一个Java对象的构造必须先有父对象，才有子对象，反序列化也不例外。所以反序列化时，为了构造父对象，只能调用父类的无参构造函数作为默认的父对象。因此当我们取父对象的变量值时，它的值是调用父类无参构造函数后的值。如果考虑到这种序列化的情况，在父类无参构造函数中对变量进行初始化，否则的话，父类变量值都是默认声明的值，如int型的默认是0，string型的默认是null。
情况六：Transient关键字。其作用是控制变量的序列化，在变量声明前加上该关键字，可以阻止该变量被序列化到文件中，在被反序列化后，transient变量的值被设为初始值，如int型的是0，对象型的是null。

7.IO模型

BIO/NIO/AIO

(a)BIO：阻塞。如果服务端连接多个客户端，则需要多个线程分别从套接字读取数据。
(b)NIO：非阻塞。NIO = I/O多路复用 + 非阻塞式I/O

NIO线程模型

(a)Reactor单线程模型：由一个线程监听连接事件、读写事件，并完成数据读写。
(b)Reactor多线程模型：一个Acceptor线程专门监听各种事件，再由专门的线程池负责处理真正的IO数据读写
(c)主从Reactor多线程模型：一个线程监听连接事件，线程池的多个线程监听已经建立连接的套接字的数据读写事件，另外和多线程模型一样有专门的线程池处理真正的IO操作。

# Java集合

1.Java集合类

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

2.Java集合框架基本概念、优点

最初的Java版本包含几种集合类：Vector、Stack、HashTable和Array。Java1.2提出了囊括所有集合接口、实现和算法的集合框架。
集合框架的优点：使用核心集合类降低开发成本，随着使用经过严格测试的集合框架类，代码质量会得到提高，复用性和可操作性。
集合框架中的泛型的优点：Java1.5引入了泛型，所有的集合接口和实现都大量地使用它。泛型允许我们为集合提供一个可以容纳的对象类型，因此如果你添加其它类型的任何元素，它会在编译时报错。这避免了在运行时出现ClassCastException，因为将会在编译时得到报错信息。泛型也使得代码整洁，我们不需要使用显式转换和instanceOf操作符。它也给运行时带来好处，因为不会产生类型检查的字节码指令。

3.哪些集合类提供对元素的随机访问？

ArrayList、HashMap、TreeMap和HashTable类提供对元素的随机访问。

4.哪些集合类是线程安全的？

Vector、HashTable、Properties和Stack是同步类，所以它们是线程安全的，可以在多线程环境下使用。
Java1.5并发包（java.util.concurrent）包含线程安全集合类，允许在迭代时修改集合，是fail-safe的，不会抛出ConcurrentModificationException。
一部分类为：CopyOnWriteArrayList、 ConcurrentHashMap、CopyOnWriteArraySet。

5.当一个集合被作为参数传递给一个函数时，如何才可以确保函数不能修改它？

UnsupportedOperationException是用于表明操作不支持的异常。在JDK类中已被大量运用，在集合框架java.util.Collections.UnmodifiableCollection将会在所有add和remove操作中抛出这个异常。
在作为参数传递之前，我们可以使用Collections.unmodifiableCollection(Collection c)方法创建一个只读集合，这将确保改变集合的任何操作都会抛出UnsupportedOperationException。

6.Iterator

Iterator接口提供遍历任何Collection的接口。我们可以从一个Collection中使用迭代器方法来获取迭代器实例。迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者在迭代过程中移除元素，而Enumeration不能做到。

Enumeration和Iterator接口的区别：
Enumeration的速度是Iterator的两倍，也使用更少的内存。Enumeration是非常基础的，也满足了基础的需要。但是与Enumeration相比，Iterator更加安全，因为当一个集合正在被遍历的时候，它会阻止其它线程去修改集合。

为何没有像Iterator.add()这样的方法：
语义不明，已知的是Iterator的协议不能确保迭代的次序。ListIterator有add()方法。

Iterater和ListIterator之间的区别：
可以使用Iterator来遍历Set和List集合，而ListIterator只能遍历List。
Iterator只可以向前遍历，而LIstIterator可以双向遍历。
ListIterator从Iterator接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

迭代器的fail-fast属性：
每次我们尝试获取下一个元素的时候，Iterator fail-fast属性检查当前集合结构里的任何改动。如果发现任何改动，它抛出ConcurrentModificationException。Collection中所有Iterator的实现都是按fail-fast来设计的（ConcurrentHashMap和CopyOnWriteArrayList这类并发集合类除外）。
java.util.concurrent中的集合类都为fail-safe的，迭代器不抛出ConcurrentModificationException。

如何避免ConcurrentModificationException：
在遍历一个集合的时候我们可以使用并发集合类来避免ConcurrentModificationException，比如使用CopyOnWriteArrayList，而不是ArrayList。

为何Iterator接口没有具体的实现：
Iterator接口定义了遍历集合的方法，但它的实现则是集合实现类的责任。每个能够返回用于遍历的Iterator的集合类都有它自己的Iterator实现内部类。
这就允许集合类去选择迭代器是fail-fast还是fail-safe的。比如，ArrayList迭代器是fail-fast的，而CopyOnWriteArrayList迭代器是fail-safe的。

7.Collection和Collections有什么区别

Collection是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式，其直接继承接口有List与Set。
Collections则是集合类的一个工具类，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。
常见的函数

    sort(Collection),shuffle(Collection),reverse(Collection),
    fill(Collection,Object),copy(List, List),rotate(Collection,int),swap(List,int,int),
    indexOfSublist(List,List),lastIndexOfSublist(List,List),max(Collection,Comparator),min(Collection,Comparator)

8.为何Collection不实现Cloneable和Serializable接口？
当与具体实现打交道的时候，克隆或序列化的语义和含义才发挥作用。所以，具体实现应该决定如何对它进行克隆或序列化，或它是否可以被克隆或序列化。在所有的实现中授权克隆和序列化，最终导致更少的灵活性和更多的限制。

9.为何Map接口不继承Collection接口？

尽管Map接口和它的实现也是集合框架的一部分，但Map不是集合，集合也不是Map。因此，Map继承Collection毫无意义，反之亦然。
如果Map继承Collection接口，那么元素去哪儿？Map包含key-value对，它提供抽取key或value列表集合的方法，但是它不适合“一组对象”规范。

10.Map接口提供了哪些不同的集合视图？

(1)Set keySet()：返回map中包含的所有key的一个Set视图。集合是受map支持的，map的变化会在集合中反映出来，反之亦然。当一个迭代器正在遍历一个集合时，若map被修改了（除迭代器自身的移除操作以外），迭代器的结果会变为未定义。
(2)Collection values()：返回一个map中包含的所有value的一个Collection视图。这个collection受map支持的，map的变化会在collection中反映出来，反之亦然。当一个迭代器正在遍历一个collection时，若map被修改了（除迭代器自身的移除操作以外），迭代器的结果会变为未定义。
(3)Set<Map.Entry<K,V>> entrySet()：返回一个map包含的所有映射的一个集合视图。这个集合受map支持的，map的变化会在集合中反映出来，反之亦然。当一个迭代器正在遍历一个集合时，若map被修改了（除迭代器自身的移除操作，以及对迭代器返回的entry进行setValue外），迭代器的结果会变为未定义。

11.HashMap

构造函数：

    (a)HashMap(): 构建一个空的哈希映像，默认长度16
    (b)HashMap(Map m): 构建一个哈希映像，并且添加映像m的所有映射
    (c)HashMap(int initialCapacity): 构建一个拥有特定容量的空的哈希映像
    (d)HashMap(int initialCapacity, float loadFactor): 构建一个拥有特定容量和加载因子的空的哈希映像

为什么线程不安全：
HashMap不是线程安全的，如果想要线程安全的HashMap，可以通过Collections类的静态方法synchronizedMap获得线程安全的HashMap。除了不同步和允许使用null之外，HashMap类与Hashtable大致相同。
在JDK7中，在多线程环境下，扩容时会造成环形链或数据丢失。使用头插法，在扩容时，其转移过程会rehash，并对链表进行反转。
在JDK8中，在多线程环境下，会发生数据覆盖的情况。两条不同的数据hash值一样，并且该位置数据为null，所以两个线程会数据覆盖。

底层实现：
JDK7实现，基于数组和链表来实现的，通过计算散列码来决定存储的位置。HashMap中主要是通过key的hashCode来计算hash值的。如果存储的对象对多了，就有可能不同的对象所算出来的hash值是相同的，这就出现了所谓的hash冲突。HashMap底层是通过链表来解决hash冲突的。哈希数组中每个元素都是一个单链表的头节点，链表是用来解决冲突的，如果不同的key映射到了数组的同一位置处，就将其放入单链表中。数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null），那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n)，首先遍历链表，存在即覆盖，否则新增；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，HashMap中的链表出现越少，性能才会越好。
JDK8实现，基于位桶+链表/红黑树的方式实现，也是非线程安全的。当某个位桶的链表的长度达到某个阀值(大于等于8)的时候，这个链表就将转换成红黑树。为什么要用红黑树，而不用平衡二叉树？插入效率比平衡二叉树高，查询效率比普通二叉树高。所以选择性能相对折中的红黑树。

JDK7与JDK8中差异：
1.7中采用数组+链表，1.8采用的是数组+链表/红黑树，即在1.7中链表长度超过一定长度后就改成红黑树存储。 
1.7扩容时需要重新计算哈希值和索引位置，1.8并不重新计算哈希值，巧妙地采用和扩容后容量进行&操作来计算新的索引位置，即h&(length-1)，保证低位全为1，而扩容后只有一位差异，也就是多出了最左位的1，这样在通过 h&(length-1)的时候，只要h对应的最左边的那一个差异位为0，就能保证得到的新的数组索引和老数组索引一致，大大减少了之前已经散列良好的老数组的数据位置重新调换。 
1.7是采用表头插入法插入链表，1.8采用的是尾部插入法。 在1.7中采用表头插入法，在扩容时会改变链表中元素原本的顺序，以至于在并发场景下导致链表成环的问题；在1.8中采用尾部插入法，在扩容时会保持链表元素原本的顺序，就不会出现链表成环的问题了。

loadFactor加载因子：
是表示Hash表中元素的填满的程度。若加载因子越大填满的元素越多，好处是空间利用率高了但冲突的机会加大了。链表长度会越来越长，查找效率降低。因此，必须在 "冲突的机会"与"空间利用率"之间寻找一种平衡与折衷。不过一般我们都不用去设置它，让它取默认值0.75就好了。

为什么int，String适合最为key？
int和String的好处在于hash出来的值不会改变。如果是一个对象，那么他们可能会因为内部引用的改变而hashCode值的改变，会导致存储重复的数据或找不到数据的情况。

遍历：
遍历HashMap的时候，若使用remove方法删除元素时会抛出ConcurrentModificationException异常，用iterator不会。
在HashMap中有一个名为 modCount 的变量，它用来表示集合被修改的次数，修改指的是插入元素或删除元素在最后都会对modCount进行自增。
当我们在遍历HashMap时，每次遍历下一个元素前都会对modCount进行判断，若和原来的不一致说明集合结果被修改过了，然后就会抛出异常，这是Java集合的一个特性。

    for (String s : map.keySet()) {  
        if (s.equals("2"))  
            map.remove("2");  
    }

HashMap为什么不直接使用对象的原始hash值呢?通过如下方法，而不是通过key.hashCode()方法获取。通过移位和异或运算，可以让hash变得更复杂，进而影响 hash 的分布性。

    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }

12.HashMap中的链表与红黑树

链表树化：
指的就是把链表转换成红黑树，树化需要满足以下两个条件：(1)链表长度大于等于8链表树化。桶中数量少于6则从树转成链表。(2)table数组长度大于等于64。为什么table数组容量大于等于64才树化？因为当table数组容量比较小时，键值对节点hash的碰撞率可能会比较高，进而导致链表长度较长。这个时候应该优先扩容，而不是立马树化。

链表过深问题为什么不用二叉查找树代替，而选择红黑树？为什么不一直使用红黑树？
之所以选择红黑树是为了解决二叉查找树的缺陷，二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成很深的问题），遍历查找会非常慢。
而红黑树在插入新数据后可能需要通过左旋，右旋、变色这些操作来保持平衡，引入红黑树就是为了查找数据快，解决链表查询深度的问题，我们知道红黑树属于平衡二叉树，但是为了保持“平衡”是需要付出代价的，但是该代价所损耗的资源要比遍历线性链表要少，所以当长度大于8的时候，会使用红黑树，如果链表长度很短的话，根本不需要引入红黑树，引入反而会慢。

13.HashMap和HashTable区别？

(1)HashMap允许key和value为null，而HashTable不允许。
(2)HashTable是同步的，而HashMap不是。所以HashMap适合单线程环境，HashTable适合多线程环境。HashTable的方法是Synchronize的，而HashMap不是，在多个线程访问Hashtable时，不需要自己为它的方法实现同步，而HashMap就必须为之提供外同步。
(3)在Java1.4中引入了LinkedHashMap，HashMap的一个子类，假如你想要遍历顺序，你很容易从HashMap转向LinkedHashMap，但是HashTable不是这样的，它的顺序是不可预知的。
(4)HashMap提供对key的Set进行遍历，因此它是fail-fast的，但HashTable提供对key的Enumeration进行遍历，它不支持fail-fast。
(5)HashTable被认为是个遗留的类，如果你寻求在迭代的时候修改Map，你应该使用CocurrentHashMap。
(6)HashMap把Hashtable的contains方法去掉了，改成containsValue和containsKey。因为contains方法容易让人引起误解。

14.TreeMap

TreeMap<K,V>的Key值是要求实现java.lang.Comparable，所以迭代的时候TreeMap默认是按照Key值升序排序的；TreeMap的实现是基于红黑树结构。适用于按自然顺序或自定义顺序遍历键（key）。添加到SortedMap实现类的元素必须实现Comparable接口，否则必须给它的构造函数提供一个Comparator接口的实现。
基于红黑树实现。TreeMap没有调优选项，因为该树总处于平衡状态。

    (a)TreeMap()：构建一个空的映像树
    (b)TreeMap(Map m): 构建一个映像树，并且添加映像m中所有元素
    (c)TreeMap(Comparator c): 构建一个映像树，并且使用特定的比较器对关键字进行排序
    (d)TreeMap(SortedMap s): 构建一个映像树，添加映像树s中所有映射，并且使用与有序映像s相同的比较器排序

如何决定选用HashMap还是TreeMap：
对于在Map中插入、删除和定位元素这类操作，HashMap是最好的选择。然而，假如你需要对一个有序的key集合进行遍历，TreeMap是更好的选择。基于你的collection的大小，也许向HashMap中添加元素会更快，将map换为TreeMap进行有序key的遍历。

HashMap和TreeMap都是非线程安全：
HashMap继承AbstractMap抽象类，TreeMap继承自SortedMap接口。
AbstractMap抽象类：覆盖了equals()和hashCode()方法以确保两个相等映射返回相同的哈希码。如果两个映射大小相等、包含同样的键且每个键在这两个映射中对应的值都相同，则这两个映射相等。映射(指的是Entry)的哈希码是映射元素哈希码的总和，其中每个元素是Map.Entry接口的一个实现。因此，不论映射内部顺序如何，两个相等映射会报告相同的哈希码。
SortedMap接口：它用来保持键的有序顺序。SortedMap接口为映像的视图(子集)，包括两个端点提供了访问方法。除了排序是作用于映射的键以外，处理SortedMap和处理SortedSet一样。添加到SortedMap实现类的元素必须实现Comparable接口，否则您必须给它的构造函数提供一个Comparator接口的实现。TreeMap类是它的唯一一个实现。

HashMap & TreeMap & LinkedHashMap使用场景：
HashMap：在Map中插入、删除和定位元素时；
TreeMap：在需要按自然顺序或自定义顺序遍历键的情况下；
LinkedHashMap：在需要输出的顺序和输入的顺序相同的情况下。

15.ConcurrentHashMap

底层实现：
JDK7实现，ConcurrentHashMap的数据结构是由一个Segment数组和多个HashEntry组成，Segment数组的意义就是将一个大的table分割成多个小的table来进行加锁，也就是锁分离技术，而每一个Segment元素存储的是HashEntry数组+链表，这个和HashMap的数据存储结构一样。ConcurrentHashMap采用了分段锁技术，其中Segment继承于ReentrantLock。不会像HashTable那样不管是put还是get操作都需要做同步处理，理论上ConcurrentHashMap支持CurrencyLevel(Segment 数组数量)的线程并发。每当一个线程占用锁访问一个Segment时，不会影响到其他的Segment。
JDK8实现，摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用Synchronized（如果存在hash冲突，就加锁来保证线程安全，两种情况：一种是链表形式就直接遍历到尾端插入，一种是红黑树就按照红黑树结构插入）和CAS（如果没有hash冲突就直接CAS无锁插入）来操作，整个看起来就像是优化过且线程安全的HashMap，虽然在JDK8中还能看到Segment的数据结构，但是已经简化了属性，只是为了兼容旧版本Node是ConcurrentHashMap存储结构的基本单元，继承于HashMap中的Entry，用于存储数据。

JDK7与JDK8中差异：
JDK8的实现降低锁的粒度，JDK7版本锁的粒度是基于Segment的，包含多个HashEntry，而JDK8锁的粒度就是HashEntry（首节点）。
JDK8版本的数据结构变得更加简单，使得操作也更加清晰流畅，因为已经使用synchronized来进行同步，所以不需要分段锁的概念，也就不需要Segment这种数据结构了，由于粒度的降低，实现的复杂度也增加了。
JDK8使用红黑树来优化链表，基于长度很长的链表的遍历是一个很漫长的过程，而红黑树的遍历效率是很快的，代替一定阈值的链表，这样形成一个最佳拍档。

JDK8为什么使用内置锁synchronized来代替重入锁ReentrantLock：
因为粒度降低了，在相对而言的低粒度加锁方式，synchronized并不比ReentrantLock差，在粗粒度加锁中ReentrantLock可能通过Condition来控制各个低粒度的边界，更加的灵活，而在低粒度中，Condition的优势就没有了。
JVM的开发团队从来都没有放弃synchronized，而且基于JVM的synchronized优化空间更大，使用内嵌的基于JVM的关键字比使用API更加自然。
在大量的数据操作下，对于JVM的内存压力，基于API的ReentrantLock会开销更多的内存，虽然不是瓶颈，但是也是一个选择依据。

ConcurrentHashMap的限制：
诸如size、isEmpty和containsValue等聚合方法，在并发下可能会反映ConcurrentHashMap的中间状态。因此在并发情况下，这些方法的返回值只能用作参考，而不能用于流程控制。如果需要确保需要手动加锁。诸如putAll这样的聚合方法也不能确保原子性，在putAll的过程中去获取数据可能会获取到部分数据。
ConcurrentHashMap这篮子本身，可以确保多个工人在装东西进去时，不会相互影响干扰，但无法确保工人A看到还需要装100个桔子但是还未装时，工人B就看不到篮子中的桔子数量。你往这个篮子装100个桔子的操作不是原子性的，在别人看来可能会有一个瞬间篮子里有964个桔子，还需要补36个桔子。

HashMap&ConcurrentHashMap 的区别？
除了加锁，原理上无太大区别。另外，HashMap的键值对允许有null，但是ConCurrentHashMap都不允许。

ConcurrentHashMap的并发度是什么？
程序运行时能够同时更新ConccurentHashMap且不产生锁竞争的最大线程数。默认为16，且可以在构造函数中设置。当用户设置并发度时，ConcurrentHashMap会使用大于等于该值的最小2幂指数作为实际并发度（假如用户设置并发度为17，实际并发度则为32）

16.HashTable与ConcurrentHashMap

同样是线程安全，HashTable是使用synchronize关键字加锁的原理（就是对对象加锁），使用一把锁（锁住整个链表结构）处理并发问题，多个线程竞争一把锁，容易阻塞。
ConcurrentHashMap，在JDK1.7中使用分段锁（ReentrantLock + Segment + HashEntry），相当于把一个 HashMap 分成多个段，每段分配一把锁，这样支持多线程访问。锁粒度：基于Segment，包含多个HashEntry。在JDK1.8 中使用CAS + synchronized + Node + 红黑树。锁粒度：Node（首结点）（实现 Map.Entry<K,V>）。锁粒度降低了。

17.关于ConcurrentHashMap类中使用的volatile

Java volatile关键字来保证可见性、有序性。但不保证原子性。
(1)普通的共享变量不能保证可见性，因为普通共享变量被修改之后，什么时候被写入主存是不确定的，当其他线程去读取时，此时内存中可能还是原来的旧值，因此无法保证可见性。volatile关键字对于基本类型的修改可以在随后对多个线程的读保持一致，但是对于引用类型如数组，实体bean，仅仅保证引用的可见性，但并不保证引用内容的可见性。
(2)禁止JVM进行指令重排序。

背景：
为了提高处理速度，处理器不直接和内存进行通信，而是先将系统内存的数据读到内部缓存（L1，L2或其他）后再进行操作，但操作完不知道何时会写到内存。
如果对声明了volatile的变量进行写操作，JVM就会向处理器发送一条指令，将这个变量所在缓存行的数据写回到系统内存。但是，就算写回到内存，如果其他处理器缓存的值还是旧的，再执行计算操作就会有问题。
在多处理器下，为了保证各个处理器的缓存是一致的，就会实现缓存一致性协议，当某个CPU在写数据时，如果发现操作的变量是共享变量，则会通知其他CPU告知该变量的缓存行是无效的，因此其他CPU在读取该变量时，发现其无效会重新从主存中加载数据。

Node<K,V>的元素val和指针next是用volatile修饰的，在多线程环境下线程A修改结点的val或者新增节点的时候是对线程B可见的。
在1.8中ConcurrentHashMap的get操作全程不需要加锁，这也是它比其他并发集合比如hashtable、用Collections.synchronizedMap()包装的hashmap安全效率高的原因之一。get操作全程不需要加锁是因为Node的成员val是用volatile修饰的。与数组（transient volatile Node<K,V>[] table;是指array的地址是volatile的而不是数组元素的值是volatile的。）用volatile修饰没有关系。
数组用volatile修饰主要是保证在数组扩容的时候保证可见性。

18.ArrayList和Vector的异同

相同：
(1)两者都是基于索引的，内部由一个数组支持。
(2)两者维护插入的顺序，我们可以根据插入顺序来获取元素。
(3)ArrayList和Vector的迭代器实现都是fail-fast的。
(4)ArrayList和Vector两者允许null值，也可以使用索引值对元素进行随机访问。

不同：
(1)Vector是线程安全的，源码中有很多的synchronized可以看出，而ArrayList不是。导致Vector效率无法和ArrayList相比。
(2)ArrayList与Vector都有一个初始的容量大小，当存储进它们里面的元素的个数超过了容量时，就需要增加ArrayList与Vector的存储空间，每次要增加存储空间时，不是只增加一个存储单元，而是增加多个存储单元，每次增加的存储单元的个数在内存空间利用与程序效率之间要取得一定的平衡。ArrayList和Vector都采用线性连续存储空间，当存储空间不足的时候，ArrayList默认增加为原来的50%，Vector默认增加为原来的一倍。
(3)Vector可以设置capacityIncrement，而ArrayList不可以，从字面理解就是capacity容量Increment增加，容量增长的参数。ArrayList与Vector都可以设置初始的空间大小，Vector还可以设置增长的空间大小，而ArrayList没有提供设置增长空间的方法。

19.ArrayList和LinkedList的区别

底层实现：
(1)ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表（双向链表）的数据结构。ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗相当的空间。

对于增删：
(2)对ArrayList和LinkedList而言，在列表末尾增加一个元素所花的开销都是固定的。对ArrayList而言，主要是在内部数组中增加一项，指向所添加的元素，偶尔可能会导致对数组重新进行分配；而对LinkedList而言，这个开销是统一的，分配一个内部Entry对象。
(3)在ArrayList的中间插入或删除一个元素意味着这个列表中剩余的元素都会被移动；而在LinkedList的中间插入或删除一个元素的开销是固定的。

对于访问：
(4)LinkedList不支持高效的随机元素访问。ArrayList是由Array所支持的基于一个索引（动态数组）的数据结构，所以它提供对元素的随机访问，复杂度为O(1)，LinkedList基于链表的数据结构，LinkedList存储一系列的节点数据，每个节点都与前一个和下一个节点相连接。所以，尽管有使用索引获取元素的方法，内部实现是从起始点开始遍历，遍历到索引的节点然后返回元素，时间复杂度为O(n)，比ArrayList要慢。

总结：
当操作是在一列数据的后面添加数据而不是在前面或中间，并且需要随机地访问其中的元素时，使用ArrayList会有更好的性能。当操作是在一列数据的前面或中间添加或删除数据,并且按照顺序访问其中的元素时，就应该使用LinkedList了。

20.Array和ArrayList的区别

(1)Array可以容纳基本类型和引用类型，而ArrayList只能容纳引用类型。
(2)Array不可以调整大小，而ArrayList大小不是固定的，通过内部方法自动调整容量。默认初始化容量为10。
(3)ArrayList是List接口的实现类，相比Array支持更多的方法和特性。Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。
(4)尽管ArrayList明显是更好的选择，但也有些时候Array比较好用：如果列表的大小已经指定，大部分情况下是存储和遍历它们。对于遍历基本数据类型，尽管Collections使用自动装箱来减轻编码任务，在指定大小的基本类型的列表上工作也会变得很慢。如果你要使用多维数组，使用[][]比List<List<>>更容易。

21.List如何一边遍历，一边删除？

错误做法：
Foreach，获取元素时，modCount和expectedModCount的值就不相等了，所以抛出了java.util.ConcurrentModificationException异常。

    for (String platform : platformList) {
        if (platform.equals("aaa")) {
            platformList.remove(platform);
        }
    }

正确做法：
(1)使用Iterator的remove()方法

    Iterator<String> iterator = platformList.iterator();
    while (iterator.hasNext()) {
        String platform = iterator.next();
        if (platform.equals("aaa")) {
            iterator.remove();
        }
    }

(2)使用for循环正序遍历，删除当前位置的值，下一个值就会补到当前位置，所以需要执行i = i - 1操作。

    for (int i = 0; i < platformList.size(); i++) {
        String item = platformList.get(i);
        if (item.equals("aaa")) {
            platformList.remove(i);
            i = i - 1;
        }
    }

(3)使用for循环倒序遍历，因为list删除只会导致当前元素之后的元素位置发生改变，所以采用倒序可以保证前面的元素没有变化。

    for (int i = platformList.size() - 1; i >= 0; i--) {
        String item = platformList.get(i);

        if (item.equals("aaa")) {
            platformList.remove(i);
        }
    }
    
22.Arrays.asList()方法返回的ArrayList不是java.util包下的，而是java.util.Arrays.ArrayList，显然它是Arrays类自己定义的一个内部类！这个内部类没有实现add()、remove()方法，而是直接使用它的父类AbstractList的相应方法，抛出UnsupportedOperationException。
Arrays.asList()返回的ArrayList继承自AbstractList，它仅支持那些不会改变数组大小的操作，所以任何对底层数据结构的尺寸进行修改的方法都会出现异常，Arrays.asList()返回固定尺寸的List。
  
如何才能避免这个错误：thinking in java给的解释是：把Arrays.asList()的结果作为构造器的参数传递给任何Collection。

    List<Integer> list = new ArrayList<>(Arrays.asList(1,2,3)); 
    
23.CopyOnWriteArrayList

CopyOnWriteArrayList适合于多线程场景下使用，其采用读写分离的思想，读操作不上锁，写操作上锁，且写操作效率较低。
CopyOnWriteArrayList基于fail-safe机制，每次修改都会在原先基础上复制一份，修改完毕后在进行替换。
CopyOnWriteArrayList采用的是ReentrantLock进行上锁。
CopyOnWriteArrayList虽然是一个线程安全版的ArrayList，但其每次修改数据时都会复制一份数据出来，所以只适用读多写少或无锁读场景。高并发写时，CopyOnWriteArray比同步ArrayList慢，高并发读时CopyOnWriteArray比同步ArrayList快。

24.HashSet的实现原理

(1)HashSet是基于HashMap实现的，默认构造函数是构建一个初始容量为16，负载因子为0.75 的HashMap。封装了一个HashMap对象来存储所有的集合元素，所有放入HashSet中的集合元素实际上由HashMap的key来保存，而HashMap的value则存储了一个PRESENT，它是一个静态的Object对象。
(2)当我们试图把某个类的对象当成HashMap的key，或试图将这个类的对象放入HashSet中保存时，重写该类的equals(Object obj)方法和hashCode()方法很重要，而且这两个方法的返回值必须保持一致：当该类的两个的 hashCode() 返回值相同时，它们通过equals()方法比较也应该返回true。通常来说，所有参与计算hashCode()返回值的关键属性，都应该用于作为equals()比较的标准。
(3)HashSet的其他操作都是基于HashMap的。

N.参考

(1)[Java容器详解](https://www.jianshu.com/p/8ef342da8732)

(2)[Java容器（接口）](https://cloud.tencent.com/developer/article/1334703)

(3)[Java容器（实现）](https://cloud.tencent.com/developer/article/1334702)

(4)[【16期】你能谈谈HashMap怎样解决hash冲突吗](https://mp.weixin.qq.com/s/dgYriZ-obbm1CoUDjHofhg)

(5)[HashMap？ConcurrentHashMap？相信看完这篇没人能难住你！](https://blog.csdn.net/weixin_44460333/article/details/86770169)

(6)[ConcurrentHashMap底层实现原理(JDK1.7 & 1.8)](https://www.jianshu.com/p/865c813f2726)

(7)[JDK7与JDK8中HashMap的实现](https://my.oschina.net/hosee/blog/618953)

(8)[ConcurrentHashMap总结](https://my.oschina.net/hosee/blog/675884)

(9)[30个Java集合面试必备的问题和答案](https://mp.weixin.qq.com/s/5JbhrM677q3aDQmErnYAGw)

# Java反射

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

# Java异常

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
throw – 用在方法内部，只能用于抛出一种异常，用来抛出方法或代码块中的异常，受查异常和非受查异常都可以被抛出。
throws – 用在方法签名中，用于声明该方法可能抛出的异常。

throw和throws都是消极处理异常的方式，只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。

3.异常的处理

异常的处理机制分为声明异常，抛出异常和捕获异常。

捕获异常：程序通常在运行之前不报错，但是运行后可能会出现某些未知的错误，但是还不想直接抛出到上一级，那么就需要通过try…catch…的形式进行异常捕获，之后根据不同的异常情况来进行相应的处理。
声明异常：在方法签名处使用throws关键字声明可能会抛出的异常。注意：非检查异常（Error、RuntimeException 或它们的子类）不可使用throws关键字来声明要抛出的异常。一个方法出现编译时异常，就需要try-catch或throws处理，否则会导致编译错误。
抛出异常：如果你觉得解决不了某些异常问题，且不需要调用者处理，那么你可以抛出异常。throw关键字作用是在方法内部抛出一个Throwable类型的异常。任何Java代码都可以通过throw语句抛出异常。

    是否能解决异常？
        是 --> 捕获异常
        否 --> 调用者是否必须处理（是否受检异常）？
            是 --> 声明受检异常
            否 --> 抛出运行时异常

4.自定义异常：当需要一些跟特定业务相关的异常信息类时。可以继承继承Exception来定义一个受检异常。也可以继承自RuntimeException或其子类来定义一个非受检异常。

5.finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是记录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，就会返回修改后的值。显然，在finally中返回或者修改返回值会对程序造成很大的困扰。
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

6.JAVA7提供了更优雅的方式来实现资源的自动释放，自动释放的资源需要是实现了AutoCloseable接口的类。try代码块退出时，会自动调用scanner.close方法，和把scanner.close方法放在finally代码块中不同的是，若scanner.close抛出异常，则会被抑制，抛出的仍然为原始异常。

    private  static void tryWithResourceTest(){
        try (Scanner scanner = new Scanner(new FileInputStream("c:/abc"),"UTF-8")){
            // code
        } catch (IOException e){
            // handle exception
        }
    }
    
7.NoClassDefFoundError和ClassNotFoundException区别？

NoClassDefFoundError是一个Error类型的异常，是由JVM 引起的，不应该尝试捕获这个异常。引起该异常的原因是JVM或ClassLoader尝试加载某类时在内存中找不到该类的定义，该动作发生在运行期间，即编译时该类存在，但是在运行时却找不到了，可能是变异后被删除了等原因导致。
ClassNotFoundException是一个受查异常，需要显式地使用try-catch对其进行捕获和处理，或在方法签名中用throws关键字进行声明。当使用Class.forName, ClassLoader.loadClass或ClassLoader.findSystemClass动态加载类到内存的时候，通过传入的类路径参数没有找到该类，就会抛出该异常；另一种抛出该异常的可能原因是某个类已经由一个类加载器加载至内存中，另一个加载器又尝试去加载它。

8.try-catch-finally中哪个部分可以省略？

catch 可以省略。更为严格的说法其实是：try只适合处理运行时异常，try+catch适合处理运行时异常+普通异常（受检异常）。
也就是说，如果你只用try去处理普通异常却不加以catch处理，编译是通不过的，因为编译器硬性规定，普通异常如果选择捕获，则必须用catch显示声明以便进一步处理。
而运行时异常在编译时没有如此规定，所以catch可以省略，你加上catch编译器也觉得无可厚非。
理论上，编译器看任何代码都不顺眼，都觉得可能有潜在的问题，所以你即使对所有代码加上try，代码在运行期时也只不过是在正常运行的基础上加一层皮。但是你一旦对一段代码加上try，就等于显示地承诺编译器，对这段代码可能抛出的异常进行捕获而非向上抛出处理。如果是普通异常，编译器要求必须用catch捕获以便进一步处理；如果运行时异常，捕获然后丢弃并且+finally扫尾处理，或者加上catch捕获以便进一步处理。
至于加上finally，则是在不管有没捕获异常，都要进行的“扫尾”处理。

N.参考

(1)[【249期】关于Java中的异常，面试可以问的都在这里了！](https://mp.weixin.qq.com/s/IuopmEa4soPxeIQFTQKtkg)
