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
    
    "a" + "b" + "c"得到的"abc"也在常量池中。
    
    String st1 = "ab";
    String st2 = "abc";
    String st3 = st1 + "c";
    此时st3并非常量池中的"abc"，任何数据和字符串进行加号（+）运算，最终得到是一个拼接的新的字符串。这个拼接的原理是由StringBuilder或者StringBuffer类和里面的append方法实现拼接，然后调用toString（）把拼接的对象转换成字符串对象，最后把得到字符串对象的地址赋值给变量。

(6)String的hashCode()和equals()

    private int hash; // Default to 0
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
    
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }

31是一个素数，素数作用就是如果我用一个数字来乘以这个素数，那么最终的出来的结果只能被素数本身和被乘数还有1来整除。31可以由i*31 == (i<<5)-1来表示,现在很多虚拟机里面都有做相关优化。
选择系数的时候要选择尽量大的系数。因为如果计算出来的hash地址越大，所谓的“冲突”就越少，查找起来效率也会提高。并且31只占用5bits,相乘造成数据溢出的概率较小。

(7)原则

equals()的设计原则：

    对称性: 如果x.equals(y)返回是true，那么y.equals(x)也应该返回是true。
    反射性: x.equals(x)必须返回是true。
    类推性: 如果x.equals(y)返回是true，而且y.equals(z)返回是true，那么z.equals(x)也应该返回是true。
    一致性: 如果x.equals(y)返回是true，只要x和y内容一直不变，不管你重复x.equals(y)多少次，返回都是true。
    非空性: x.equals(null)，永远返回是false；x.equals(和x不同类型的对象)永远返回是false。
    
hashCode()的设计原则：

    在一个Java应用的执行期间，如果一个对象提供给equals做比较的信息没有被修改的话，该对象多次调用hashCode()方法，该方法必须始终如一返回同一个integer。
    如果两个对象根据equals(Object)方法是相等的，那么调用二者各自的hashCode()方法必须产生同一个integer结果。
    并不要求根据equals(java.lang.Object)方法不相等的两个对象，调用二者各自的hashCode()方法必须产生不同的integer结果。然而，程序员应该意识到对于不同的对象产生不同的integer结果，有可能会提高hash table的性能。

2.运算

(1)Math.round()计算方法为先+0.5，然后向下取整。

(2)Java中float的精度为6-7位有效数字。double的精度为15-16位。
我们在使用BigDecimal时，使用它的BigDecimal(String)构造器创建对象才有意义。其他的如BigDecimal b = new BigDecimal(1)这种，还是会发生精度丢失的问题。
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

抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。

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

8.DO,DTO,VO等

DO（Domain Object），领域对象，也就是ORM框架中对应数据库的对象，业务实体。
VO（Value Object），就是用于保存数据的对象；在提供给页面使用的时候，也有人解释为View Object，就是对应页面展示数据的对象。
DTO（Data Transfer Object），数据传输对象，顾名思义就是用于传输数据的对象，通常用于处于不同架构层次或者不同子系统之间的数据传递，或者用于外部接口参数传递，以便提供不同粒度不同信息的数据。

例如：
controller层：public List<UserVO> getUsers(UserDTO userDto);
Service层：List<UserDTO> getUsers(UserDTO userDto);
DAO层：List<UserDTO> getUsers(UserDO userDo);
     
9.为什么Integer用==比较时127相等而128不相等？

因为自动装箱机制的存在，在为Integer类型的变量赋int类型值时，Java会自动将int类型转换为Integer类型，即Integer a = Integer.valueOf(1);valueOf()方法返回一个Integer类型值，并将其赋值给变量a。这就是int的自动装箱。
从0到127不同时候自动装箱得到的是同一个对象。就只能有一种解释：自动装箱并不一定new出新的对象。
-128到127之间的值都是直接从缓存中取出的。看看是怎么实现的：如果int型参数i在IntegerCache.low和IntegerCache.high范围内，则直接由IntegerCache返回；否则new一个新的对象返回。似乎IntegerCache.low就是-128，IntegerCache.high就是127了。

10.StringBuilder是线程不安全的，是什么原因

StringBuilder不是线程安全的，StringBuffer是线程安全的
在分析这个问题之前我们要知道StringBuilder和StringBuffer的内部实现跟String类一样，都是通过一个char数组存储字符串的，不同的是String类里面的char数组是final修饰的，是不可变的，而StringBuilder和StringBuffer的char数组是可变的。
首先通过一段代码去看一下多线程操作StringBuilder对象会出现什么问题

    public class StringBuilderDemo {
    
        public static void main(String[] args) throws InterruptedException {
            StringBuilder stringBuilder = new StringBuilder();
            for (int i = 0; i < 10; i++){
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        for (int j = 0; j < 1000; j++){
                            stringBuilder.append("a");
                        }
                    }
                }).start();
            }
    
            Thread.sleep(100);
            System.out.println(stringBuilder.length());
        }
    }
    
能看到这段代码创建了10个线程，每个线程循环1000次往StringBuilder对象里面append字符。正常情况下代码应该输出10000，但是实际运行会输出什么呢？
看到输出了“9326”，小于预期的10000，并且还抛出了一个ArrayIndexOutOfBoundsException异常（异常不是必现）。

(1)为什么输出值跟预期值不一样

先看一下StringBuilder的两个成员变量（这两个成员变量实际上是定义在AbstractStringBuilder里面的，StringBuilder和StringBuffer都继承了AbstractStringBuilder）

    //存储字符串的具体内容
    char[] value;
    //已经使用的字符数组的数量
    int count;
    
再看StringBuilder的append()方法，StringBuilder的append()方法调用的父类AbstractStringBuilder的append()方法

    @Override
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    }
    
    public AbstractStringBuilder append(String str) {
        if (str == null)
            return appendNull();
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
    }
    
我们先不管代码的第五行和第六行干了什么，直接看第七行，count += len不是一个原子操作。假设这个时候count值为10，len值为1，两个线程同时执行到了第七行，拿到的count值都是10，执行完加法运算后将结果赋值给count，所以两个线程执行完后count值为11，而不是12。这就是为什么测试代码输出的值要比10000小的原因。

(2)为什么会抛出ArrayIndexOutOfBoundsException异常。

AbstractStringBuilder的append()方法源码的第五行，ensureCapacityInternal()方法是检查StringBuilder对象的原char数组的容量能不能盛下新的字符串，如果盛不下就调用expandCapacity()方法对char数组进行扩容。
扩容的逻辑就是new一个新的char数组，新的char数组的容量是原来char数组的两倍再加2，再通过System.arrayCopy()函数将原数组的内容复制到新数组，最后将指针指向新的char数组。
假设现在有两个线程同时执行了StringBuilder的append()方法，两个线程都执行完了第五行的ensureCapacityInternal()方法，此刻count=5。这个时候线程1的cpu时间片用完了，线程2继续执行。线程2执行完整个append()方法后count变成6了
线程1继续执行第六行的str.getChars()方法的时候拿到的count值就是6了，执行char数组拷贝的时候就会抛出ArrayIndexOutOfBoundsException异常。

    private void ensureCapacityInternal(int minimumCapacity) {
            // overflow-conscious code
        if (minimumCapacity - value.length > 0)
            expandCapacity(minimumCapacity);
    }
    void expandCapacity(int minimumCapacity) {
        //计算新的容量
        int newCapacity = value.length * 2 + 2;
        //中间省略了一些检查逻辑
        ...
        value = Arrays.copyOf(value, newCapacity);
    }
    public static char[] copyOf(char[] original, int newLength) {
        char[] copy = new char[newLength];
        //拷贝数组
        System.arraycopy(original, 0, copy, 0,
                             Math.min(original.length, newLength));
        return copy;
    }
    public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
        //中间省略了一些检查
        ...   
        System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
    }

那么StringBuffer用什么手段保证线程安全的？synchronized的append方法。

11.Java深拷贝和浅拷贝

(1)引用拷贝：创建一个指向对象的引用变量的拷贝。它们的地址值是相同的，那么它们肯定是同一个对象。

    Teacher teacher = new Teacher("Taylor",26);
    Teacher otherteacher = teacher;

(2)对象拷贝：创建对象本身的一个副本。它们的地址是不同的，也就是说创建了新的对象，而不是把原对象的地址赋给了一个新的引用变量,这就叫做对象拷贝。

    Teacher teacher = new Teacher("Swift",26); 
    Teacher otherteacher = (Teacher)teacher.clone(); 

注：深拷贝和浅拷贝都是对象拷贝

(3)浅拷贝：被复制对象的所有变量都含有与原来的对象相同的值，而所有的对其他对象的引用仍然指向原来的对象。
即对象的浅拷贝会对“主”对象进行拷贝，但不会复制主对象里面的对象。“里面的对象”会在原来的对象和它的副本之间共享。
简而言之，浅拷贝仅仅复制所考虑的对象，而不复制它所引用的对象。

(4)深拷贝：深拷贝是一个整个独立的对象拷贝，深拷贝会拷贝所有的属性,并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。
简而言之，深拷贝把要复制的对象所引用的对象都复制了一遍。


N.参考

(1)[Java 7 API](https://docs.oracle.com/javase/7/docs/api/)

(2)[廖雪峰Java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)

(3)[Java 8 Optional类](https://www.runoob.com/java/java8-optional-class.html)

(4)[理解、学习与使用JAVA中的 OPTIONAL](https://www.cnblogs.com/zhangboyu/p/7580262.html)

# Java IO

1.流的特性

(1)先进先出：最先写入输出流的数据最先被输入流读取到。
(2)顺序存取：可以一个接一个地往流中写入一串字节，读出时也将按写入顺序读取一串字节，不能随机访问中间的数据。（RandomAccessFile除外）
(3)只读或只写：每个流只能是输入流或输出流的一种，不能同时具备两个功能，输入流只能进行读操作，对输出流只能进行写操作。在一个数据传输通道中，如果既要写入数据，又要读取数据，则要分别提供两个流。

2.IO流分类

按数据流的方向：输入流、输出流
按处理数据单位：字节流、字符流
按功能：节点流（介质流）、处理流（装饰流）

3.字节流与字符流

字节流和字符流的用法几乎完成全一样，区别在于字节流和字符流所操作的数据单元不同，字节流操作的单元是数据单元是8位的字节，字符流操作的是数据单元为16位的字符。

为什么要有字符流？
Java中字符是采用Unicode标准，Unicode编码中，一个英文为一个字节，一个中文为两个字节。而在UTF-8编码中，一个中文字符是3个字节。
那么问题来了，如果使用字节流处理中文，如果一次读写一个字符对应的字节数就不会有问题，一旦将一个字符对应的字节分裂开来，就会出现乱码了。为了更方便地处理中文这些字符，Java就推出了字符流。

字节流和字符流的其他区别：
字节流一般用来处理图像、视频、音频、PPT、Word等类型的文件。字符流一般用于处理纯文本类型的文件，如TXT文件等，但不能处理图像视频等非文本文件。用一句话说就是：字节流可以处理一切文件，而字符流只能处理纯文本文件。
字节流本身没有缓冲区，缓冲字节流相对于字节流，效率提升非常高。而字符流本身就带有缓冲区，缓冲字符流相对于字符流效率提升就不是那么大了。

4.节点流和处理流

节点流：直接操作数据读写的流类，比如FileInputStream
处理流：对一个已存在的流的链接和封装，通过对数据进行处理为程序提供功能强大、灵活的读写功能，例如BufferedInputStream（缓冲字节流）
处理流和节点流应用了Java的装饰者设计模式。处理流是对节点流的封装，最终的数据处理还是由节点流完成的。
在诸多处理流中，有一个非常重要，那就是缓冲流。
我们知道，程序与磁盘的交互相对于内存运算是很慢的，容易成为程序的性能瓶颈。减少程序与磁盘的交互，是提升程序效率一种有效手段。缓冲流，就应用这种思路：普通流每次读写一个字节，而缓冲流在内存中设置一个缓存区，缓冲区先存储足够的待操作数据后，再与内存或磁盘进行交互。这样，在总数据量不变的情况下，通过提高每次交互的数据量，减少了交互次数。

5.InputStream

    ByteArrayInputStream，StringBufferInputStream，FileInputStream，三种基本的介质流它们分别从Byte数组、StringBuffer、和本地文件中读取数据。
    PipedInputStream从与其它线程共用的管道中读取数据。
    
    ObjectInputStream和所有FilterInputStream的子类都是装饰流。
    FilterInputStream子类包括BufferedInputStream能为输入流提供缓冲区，DataInputStream可以从输入流中读取Java基本类型数据，PushBackInputStream可以把读取到的字节重新推回到InputStream中。

6.OutputStream

    ByteArrayOutputStream、FileOutputStream是两种基本的介质流，它们分别向Byte数组和本地文件中写入数据。
    PipedOutputStream是向与其它线程共用的管道中写入数据。
    
    ObjectOutputStream 和所有FilterOutputStream的子类都是装饰流。FilterOutputStream子类包括BufferedOutputStream、DataOutputStream。
    PrintStream
    
实例：

    public class IOTest {
         public static void write(File file) throws IOException {
          // 缓冲字节流，提高了效率
          BufferedOutputStream bis = new BufferedOutputStream(new FileOutputStream(file, true));
        
          // 要写入的字符串
          String string = "松下问童子，言师采药去。只在此山中，云深不知处。";
          // 写入文件
          bis.write(string.getBytes());
          // 关闭流
          bis.close();
         }
        
         public static String read(File file) throws IOException {
          BufferedInputStream fis = new BufferedInputStream(new FileInputStream(file));
        
          // 一次性取多少个字节
          byte[] bytes = new byte[1024];
          // 用来接收读取的字节数组
          StringBuilder sb = new StringBuilder();
          // 读取到的字节数组长度，为-1时表示没有数据
          int length = 0;
          // 循环取数据
          while ((length = fis.read(bytes)) != -1) {
           // 将读取的内容转换成字符串
           sb.append(new String(bytes, 0, length));
          }
          // 关闭流
          fis.close();
        
          return sb.toString();
         }
    }    

7.Reader

    FileReader, ByteArrayReader, PipedReader
    BufferedReader, InputStreamReader

8.Writer

    FileWriter, ByteArrayWriter, PipedWriter
    BufferedWriter, InputStreamWriter, PrintWriter
    
实例：

    public class IOTest {
         public static void write(File file) throws IOException {
          // BufferedWriter fw = new BufferedWriter(new OutputStreamWriter(new
          // FileOutputStream(file, true), "UTF-8"));
          // FileWriter可以大幅度简化代码
          BufferedWriter bw = new BufferedWriter(new FileWriter(file, true));
        
          // 要写入的字符串
          String string = "松下问童子，言师采药去。只在此山中，云深不知处。";
          bw.write(string);
          bw.close();
         }
        
         public static String read(File file) throws IOException {
          BufferedReader br = new BufferedReader(new FileReader(file));
          // 用来接收读取的字节数组
          StringBuilder sb = new StringBuilder();
        
          // 按行读数据
          String line;
          // 循环取数据
          while ((line = br.readLine()) != null) {
           // 将读取的内容转换成字符串
           sb.append(line);
          }
          // 关闭流
          br.close();
        
          return sb.toString();
         }
    }

9.File类

(1)创建

    createNewFile()在指定位置创建一个空文件，成功就返回true，如果已存在就不创建，然后返回false。
    mkdir() 在指定位置创建一个单级文件夹。
    mkdirs() 在指定位置创建一个多级文件夹。
    renameTo(File dest)如果目标文件与源文件是在同一个路径下，那么renameTo的作用是重命名， 如果目标文件与源文件不是在同一个路径下，那么renameTo的作用就是剪切，而且还不能操作文件夹。

(2)删除

    delete() 删除文件或者一个空文件夹，不能删除非空文件夹，马上删除文件，返回一个布尔值。
    deleteOnExit()jvm退出时删除文件或者文件夹，用于删除临时文件，无返回值。

(3)判断

    exists() 文件或文件夹是否存在。
    isFile() 是否是一个文件，如果不存在，则始终为false。
    isDirectory() 是否是一个目录，如果不存在，则始终为false。
    isHidden() 是否是一个隐藏的文件或是否是隐藏的目录。
    isAbsolute() 测试此抽象路径名是否为绝对路径名。

(4)获取

    getName() 获取文件或文件夹的名称，不包含上级路径。
    getAbsolutePath()获取文件的绝对路径，与文件是否存在没关系
    length() 获取文件的大小（字节数），如果文件不存在则返回0L，如果是文件夹也返回0L。
    getParent() 返回此抽象路径名父目录的路径名字符串；如果此路径名没有指定父目录，则返回null。
    lastModified()获取最后一次被修改的时间。

(5)文件夹相关

    static File[] listRoots()列出所有的根目录（Window中就是所有系统的盘符）
    list() 返回目录下的文件或者目录名，包含隐藏文件。对于文件这样操作会返回null。
    listFiles() 返回目录下的文件或者目录对象（File类实例），包含隐藏文件。对于文件这样操作会返回null。
    list(FilenameFilter filter)返回指定当前目录中符合过滤条件的子文件或子目录。对于文件这样操作会返回null。
    listFiles(FilenameFilter filter)返回指定当前目录中符合过滤条件的子文件或子目录。对于文件这样操作会返回null。
    
(6)实例

    public class FileTest {
     public static void main(String[] args) throws IOException {
      File file = new File("C:/fileTest.txt");
    
      // 判断文件是否存在
      if (!file.exists()) {
       // 不存在则创建
       file.createNewFile();
      }
      System.out.println("文件的绝对路径：" + file.getAbsolutePath());
      System.out.println("文件的大小：" + file.length());
    
      // 刪除文件
      file.delete();
     }
    }

10.序列化与反序列化

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
    
(3)为什么需要序列化与反序列化？

(a)对象序列化可以实现分布式对象。主要应用例如：RMI(即远程调用Remote Method Invocation)要利用对象序列化运行远程主机上的服务，就像在本地机上运行对象时一样。
(b)java对象序列化不仅保留一个对象的数据，而且递归保存对象引用的每个对象的数据。可以将整个对象层次写入字节流中，可以保存在文件中或在网络连接上传递。利用对象序列化可以进行对象的"深复制"，即复制对象本身及引用的对象本身。序列化一个对象可能得到整个对象序列。
(c)序列化可以将内存中的类写入文件或数据库中。

(4)实现序列化和反序列化为什么要实现Serializable接口，为什么还要显示指定serialVersionUID的值

在Java中实现了Serializable接口后，JVM会在底层帮我们实现序列化和反序列化。
如果不显示指定serialVersionUID, JVM在序列化时会根据属性自动生成一个serialVersionUID, 然后与属性一起序列化, 再进行持久化或网络传输. 在反序列化时, JVM会再根据属性自动生成一个新版serialVersionUID, 然后将这个新版serialVersionUID与序列化时生成的旧版serialVersionUID进行比较, 如果相同则反序列化成功, 否则报错。
如果显示指定了serialVersionUID, JVM在序列化和反序列化时仍然都会生成一个serialVersionUID, 但值为我们显示指定的值, 这样在反序列化时新旧版本的serialVersionUID就一致了。
在实际开发中, 不显示指定serialVersionUID的情况会导致什么问题? 如果我们的类写完后不再修改, 那当然不会有问题, 但这在实际开发中是不可能的, 我们的类会不断迭代, 一旦类被修改了, 那旧对象反序列化就会报错. 所以在实际开发中, 我们都会显示指定一个serialVersionUID, 值是多少无所谓, 只要不变就行。

(5)为什么不建议使用Java序列化

(a)无法跨语言：通过Java的原生Serializable接口与ObjectOutputStream实现的序列化，只有java语言自己能通过ObjectInputStream来解码，其他语言，如C、C++、Python等等，都无法对其实现解码。而在我们实际开发生产中，有时不可避免的需要基于不同语言编写的应用程序之间进行通信，这个时候Java自带的序列化就无法搞定了。
(b)性能差：Java自带的序列化比NIO中的ByteBuffer编码的性能差。性能指的是速度。
(c)序列后的码流太大：java序列化的大小是二进制编码的5倍多！序列化后的二进制数组越大，占用的存储空间就越多，如果我们是进行网络传输，相对占用的带宽就更多，也会影响系统的性能。

正是由于Java自带的序列化存在这些问题，开源社区涌现出很多优秀的序列化框架。

    Protobuf
    结构化数据存储格式（xml,json等）
    高性能编解码技术
    语言和平台无关，扩展性好，支持java、C++、Python三种语言。
    Google开源
    
    Thrift
    支持多种语言（C++、C#、Cocoa、Erlang、Haskell、java、Ocami、Perl、PHP、Python、Ruby和SmallTalk）
    使用了组建大型数据交换及存储工具，对于大型系统中的内部数据传输，相对于Json和xml在性能上和传输大小上都有明显的优势。
    支持通用二进制编码，压缩二进制编码，优化的可选字段压缩编解码等三种方式。
    FaceBook开源
    
    Jackson
    Jackson所依赖的jar包较少，简单易用并且性能也要相对高些。
    对于复杂类型的json转换bean会出现问题，一些集合Map，List的转换出现问题。
    Jackson对于复杂类型的bean转换Json，转换的json格式不是标准的Json格式
    
    Gson
    Gson是目前功能最全的Json解析神器
    Gson的应用主要为toJson与fromJson两个转换函数，无依赖，不需要例外额外的jar
    类里面只要有get和set方法，Gson完全可以将复杂类型的json到bean或bean到json的转换，是JSON解析的神器
    
    FastJson
    无依赖，不需要例外额外的jar，能够直接跑在JDK上。
    FastJson在复杂类型的Bean转换Json上会出现一些问题，可能会出现引用的类型，导致Json转换出错，需要制定引用。
    FastJson采用独创的算法，将parse的速度提升到极致，超过所有json库。
    频繁爆漏洞，被相当部分公司禁止使用

11.零拷贝

普通IO，可以把磁盘的文件经过内核空间，读到JVM空间，然后进行各种操作，最后再写到磁盘或是发送到网络，效率较慢但支持数据文件操作。
零拷贝则是直接在内核空间完成文件读取并转到磁盘（或发送到网络）。由于它没有读取文件数据到JVM这一环，因此程序无法操作该文件数据，尽管效率很高！
NIO的零拷贝由transferTo()方法实现。transferTo()方法将数据从FileChannel对象传送到可写的字节通道（如Socket Channel等）。
在内部实现中，由native方法transferTo0()来实现，它依赖底层操作系统的支持。在UNIX和Linux系统中，调用这个方法将会引起sendfile()系统调用。

    File file = new File("test.zip");
    RandomAccessFile raf = new RandomAccessFile(file, "rw");
    FileChannel fileChannel = raf.getChannel();
    SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("", 1234));
    // 直接使用了transferTo()进行通道间的数据传输
    fileChannel.transferTo(0, fileChannel.size(), socketChannel);
    
使用场景一般是：

    较大，读写较慢，追求速度
    内存不足，不能加载太大数据
    带宽不够，即存在其他程序或线程存在大量的IO操作，导致带宽本来就小

以上都建立在不需要进行数据文件操作的情况下，如果既需要这样的速度，也需要进行数据操作怎么办？那么使用NIO的直接内存！

    File file = new File("test.zip");
    RandomAccessFile raf = new RandomAccessFile(file, "rw");
    FileChannel fileChannel = raf.getChannel();
    MappedByteBuffer buffer = fileChannel.map(FileChannel.MapMode.READ_ONLY, 0, fileChannel.size());
    
    // 或者
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
    
直接内存则介于两者之间，效率一般且可操作文件数据。直接内存（mmap技术）将文件直接映射到内核空间的内存，返回一个操作地址，它解决了文件数据需要拷贝到JVM才能进行操作的窘境。而是直接在内核空间直接进行操作，省去了内核空间拷贝到用户空间这一步操作。
MappedByteBuffer：java nio提供的FileChannel提供了map()方法，该方法可以在一个打开的文件和MappedByteBuffer之间建立一个虚拟内存映射，MappedByteBuffer继承于ByteBuffer，类似于一个基于内存的缓冲区，只不过该对象的数据元素存储在磁盘的一个文件中；调用get()方法会从磁盘中获取数据，此数据反映该文件当前的内容，调用put()方法会更新磁盘上的文件，并且对文件做的修改对其他阅读者也是可见的。
NIO的直接内存是由MappedByteBuffer实现的。核心即是map()方法，该方法把文件映射到内存中，获得内存地址addr，然后通过这个addr构造MappedByteBuffer类，以暴露各种文件操作API。
由于MappedByteBuffer申请的是堆外内存，因此不受Minor GC控制，只能在发生Full GC时才能被回收。而DirectByteBuffer改善了这一情况，它是MappedByteBuffer类的子类，同时它实现了DirectBuffer接口，维护一个Cleaner对象来完成内存回收。因此它既可以通过Full GC来回收内存，也可以调用clean()方法来进行回收。
另外，直接内存的大小可通过jvm参数来设置：-XX:MaxDirectMemorySize。
NIO的MappedByteBuffer还有一个兄弟叫做HeapByteBuffer。顾名思义，它用来在堆中申请内存，本质是一个数组。由于它位于堆中，因此可受GC管控，易于回收。
    
12.serialVersionUID

serialVersionUID适用于Java的序列化机制。简单来说，Java的序列化机制是通过判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常，即是InvalidCastException。

生成方式：

默认的1L，比如：private static final long serialVersionUID = 1L;
根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段，比如：private static final long serialVersionUID = xxxxL;
当实现java.io.Serializable接口的类没有显式地定义一个serialVersionUID变量时候，Java序列化机制会根据编译的Class自动生成一个serialVersionUID作序列化版本比较用，这种情况下，如果Class文件(类名，方法明等)没有发生变化(增加空格，换行，增加注释等等)，就算再编译多次，serialVersionUID也不会变化的。

几种情况：

情况一：A端序列化后传输到B端反序列化。如果A端增加一个字段，A端增加的字段丢失(被B端忽略)。
情况二：A端序列化后传输到B端反序列化。如果B端减少一个字段，A端不变，A端多的字段值丢失(被B端忽略)。
情况三：A端序列化后传输到B端反序列化。如果B端增加一个字段，A端不变，B端新增加的int字段被赋予了默认值0。
情况四：将对象序列化后，修改静态变量的数值，再将序列化对象读取出来，然后通过读取出来的对象获得静态变量的数值并打印出来。应该是修改后的值。因为序列化时，并不保存静态变量，这其实比较容易理解，序列化保存的是对象的状态，静态变量属于类的状态，因此序列化并不保存静态变量。
情况五：父类的序列化。一个子类实现了Serializable接口，它的父类没有实现Serializable接口，序列化该子类对象，然后反序列化后输出父类定义的某变量的数值，该变量数值与序列化时的数值不同。因为在父类没有实现Serializable接口时，虚拟机是不会序列化父对象的，而一个Java对象的构造必须先有父对象，才有子对象，反序列化也不例外。所以反序列化时，为了构造父对象，只能调用父类的无参构造函数作为默认的父对象。因此当我们取父对象的变量值时，它的值是调用父类无参构造函数后的值。如果考虑到这种序列化的情况，在父类无参构造函数中对变量进行初始化，否则的话，父类变量值都是默认声明的值，如int型的默认是0，string型的默认是null。
情况六：Transient关键字。其作用是控制变量的序列化，在变量声明前加上该关键字，可以阻止该变量被序列化到文件中，在被反序列化后，transient变量的值被设为初始值，如int型的是0，对象型的是null。

13.IO模型

BIO/NIO/AIO

(a)BIO：阻塞。如果服务端连接多个客户端，则需要多个线程分别从套接字读取数据。
(b)NIO：非阻塞。NIO = I/O多路复用 + 非阻塞式I/O

NIO线程模型

(a)Reactor单线程模型：由一个线程监听连接事件、读写事件，并完成数据读写。
(b)Reactor多线程模型：一个Acceptor线程专门监听各种事件，再由专门的线程池负责处理真正的IO数据读写。
(c)主从Reactor多线程模型：一个线程监听连接事件，线程池的多个线程监听已经建立连接的套接字的数据读写事件，另外和多线程模型一样有专门的线程池处理真正的IO操作。

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

Java的所有异常可以分为受检异常（checked exception）和非受检异常（unchecked exception）。

受检异常：编译器要求必须处理的异常。正确的程序在运行过程中，经常容易出现的、符合预期的异常情况。一旦发生此类异常，就必须采用某种方式进行处理。除RuntimeException及其子类外，其他的Exception异常都属于受检异常。编译器会检查此类异常，也就是说当编译器检查到应用中的某处可能会此类异常时，将会提示你处理本异常——要么使用try-catch捕获，要么使用方法签名中用throws关键字抛出，否则编译不通过。
非受检异常：编译器不会进行检查并且不要求必须处理的异常，也就说当程序中出现此类异常时，即使我们没有try-catch捕获它，也没有使用throws抛出该异常，编译也会正常通过。该类异常包括运行时异常（RuntimeException极其子类）和错误（Error）。

2.异常相关关键字

    try – 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。
    catch – 用于捕获异常。catch用来捕获try语句块中发生的异常。
    finally – finally语句块总是会被执行。它主要用于回收在try块里打开的物理资源(如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。
    throw – 用在方法内部，只能用于抛出一种异常，用来抛出方法或代码块中的异常，受查异常和非受查异常都可以被抛出。
    throws – 用在方法签名中，用于声明该方法可能抛出的异常。

throw和throws都是消极处理异常的方式，只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。

3.异常的处理

异常的处理机制分为捕获异常、声明异常、抛出异常。

(1)捕获异常：程序通常在运行之前不报错，但是运行后可能会出现某些未知的错误，但是还不想直接抛出到上一级，那么就需要通过try…catch…的形式进行异常捕获，之后根据不同的异常情况来进行相应的处理。
(2)声明异常：在方法签名处使用throws关键字声明可能会抛出的异常。注意：非检查异常（Error、RuntimeException 或它们的子类）不可使用throws关键字来声明要抛出的异常。一个方法出现编译时异常，就需要try-catch或throws处理，否则会导致编译错误。
(3)抛出异常：如果你觉得解决不了某些异常问题，且不需要调用者处理，那么你可以抛出异常。throw关键字作用是在方法内部抛出一个Throwable类型的异常。任何Java代码都可以通过throw语句抛出异常。

    是否能解决异常？
        是 --> 捕获异常
        否 --> 调用者是否必须处理（是否受检异常）？
            是 --> 声明受检异常
            否 --> 抛出运行时异常

4.自定义异常

当需要一些跟特定业务相关的异常信息类时。可以继承Exception来定义一个受检异常。也可以继承自RuntimeException或其子类来定义一个非受检异常。

5.异常与return

finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是记录下返回值待finally代码块执行完毕之后再向调用者返回其值。
然后如果在finally中修改了返回值，就会返回修改后的值。显然，在finally中返回或者修改返回值会对程序造成很大的困扰。
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

6.try-with-resource

JAVA7提供了更优雅的方式来实现资源的自动释放，自动释放的资源需要是实现了AutoCloseable接口的类。try代码块退出时，会自动调用scanner.close方法，和把scanner.close方法放在finally代码块中不同的是，若scanner.close抛出异常，则会被抑制，抛出的仍然为原始异常。

    private  static void tryWithResourceTest(){
        try (Scanner scanner = new Scanner(new FileInputStream("c:/abc"),"UTF-8")){
            // code
        } catch (IOException e){
            // handle exception
        }
    }
    
7.NoClassDefFoundError和ClassNotFoundException区别？

NoClassDefFoundError是一个Error类型的异常，是由JVM引起的，不应该尝试捕获这个异常。引起该异常的原因是JVM或ClassLoader尝试加载某类时在内存中找不到该类的定义，该动作发生在运行期间，即编译时该类存在，但是在运行时却找不到了，可能是变异后被删除了等原因导致。
ClassNotFoundException是一个受查异常，需要显式地使用try-catch对其进行捕获和处理，或在方法签名中用throws关键字进行声明。当使用Class.forName, ClassLoader.loadClass或ClassLoader.findSystemClass动态加载类到内存的时候，通过传入的类路径参数没有找到该类，那么就会导致JVM抛出ClassNotFoundException；另一种抛出该异常的可能原因是某个类已经由一个类加载器加载至内存中，另一个加载器又尝试去加载它。

8.try-catch-finally中哪个部分可以省略？

catch可以省略。更为严格的说法其实是：try只适合处理运行时异常，try+catch适合处理运行时异常+普通异常（受检异常）。
也就是说，如果你只用try去处理普通异常却不加以catch处理，编译是通不过的，因为编译器硬性规定，普通异常如果选择捕获，则必须用catch显示声明以便进一步处理。
而运行时异常在编译时没有如此规定，所以catch可以省略，你加上catch编译器也觉得无可厚非。
理论上，编译器看任何代码都不顺眼，都觉得可能有潜在的问题，所以你即使对所有代码加上try，代码在运行期时也只不过是在正常运行的基础上加一层皮。
但是你一旦对一段代码加上try，就等于显示地承诺编译器，对这段代码可能抛出的异常进行捕获而非向上抛出处理。如果是普通异常，编译器要求必须用catch捕获以便进一步处理；如果运行时异常，捕获然后丢弃并且+finally扫尾处理，或者加上catch捕获以便进一步处理。
至于加上finally，则是在不管有没捕获异常，都要进行的“扫尾”处理。

N.参考

(1)[【249期】关于Java中的异常，面试可以问的都在这里了！](https://mp.weixin.qq.com/s/IuopmEa4soPxeIQFTQKtkg)

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

    bype/char/short/int/long/boolean/float/double.class及void.class
    数组int[].class及String[].class
    对象MyObject.class

4.常用方法

    Class clazz = Class.forName("some.package.MyClassName"); // className必须为全名，也就是得包含包名
    Object obj= clazz.newInstance(); //如果类有无参数公共构造函数，直接可以使用类的字节码实例就创建对象的实例。否则用constructor对象调用newInstance(Object... initargs)

    //--------------------------- Class对象的方法（即clazz的方法）---------------------------
    // 构造器Constructor有newInstance方法
    Constructor getConstructor(Class[] params) //根据指定参数获得public构造器
    Constructor[] getConstructors() //获得public的所有构造器
    Constructor getDeclaredConstructor(Class[] params) //根据指定参数获得public和非public的构造器
    Constructor[] getDeclaredConstructors() //获得public和非public的所有构造器

    // 方法Method有invoke方法
    Method getMethod(String name, Class[] params) //根据方法名，参数类型获得方法
    Method[] getMethods() //获得所有的public方法
    Method getDeclaredMethod(String name, Class[] params) //根据方法名和参数类型，获得public和非public的方法
    Method[] getDeclaredMethods() //获得所有的public和非public方法

    // 属性Field有get,set方法
    Field getField(String name) //根据变量名得到相应的public变量
    Field[] getFields() //获得类中所有public的方法
    Field getDeclaredField(String name) //根据方法名获得public和非public变量
    Field[] getDeclaredFields() //获得类中所有的public和非public方法

5.什么是SPI

(1)SPI

全称为Service Provider Interface，是一种服务发现机制。它通过在ClassPath路径下的META-INF/services文件夹查找文件，自动加载文件里所定义的类。
这一机制为很多框架扩展提供了可能，比如在Dubbo、JDBC中都使用到了SPI机制。我们先通过一个很简单的例子来看下它是怎么用的。
回忆一下JDBC获取数据库连接的过程。在早期版本中，需要先设置数据库驱动的连接，再通过DriverManager.getConnection获取一个Connection。 在较新版本中设置数据库驱动连接这一步骤就不再需要，那么它是怎么分辨是哪种数据库的呢？答案就在SPI。
java spi提供这样的一个机制：为某个接口寻找服务实现的机制。有点类似IOC的思想，就是将装配的控制权移到代码之外。
即SPI机制就是有一个接口，然后有几个实现类，代码运行的时候要确定运行哪个实现类，而这些实现类也是可插拔的。
当服务的提供者(provider)，提供了一个接口多种实现时，一般会在jar包的META-INF/services/目录下，创建该接口的同名文件。该文件里面的内容就是该服务接口的具体实现类的名称。
而当外部加载这个模块的时候，就能通过该jar包META-INF/services/里的配置文件得到具体的实现类名，并加载实例化，完成模块的装配。

(2)实例

写一个接口Command

    public interface Command {  
        public void execute();  
    }

分别写两个接口的实现类 TurnOnCommand和TurnOffCommand

    public class TurnOnCommand implements Command {
        public void execute() {  
            System.out.println("Trun on....");
        }  
    }
    
    public class TurnOffCommand implements Command {
        public void execute() {  
            System.out.println("Trun off....");
        }  
    }

在项目的resources\\META-INF\\services目录下新建一个com.abc.spi.Command的spi配置文件，内容为接口的两个实现类

    com.abc.spi.TurnOnCommand
    com.abc.spi.TurnOffCommand

编写测试类Test

    public class Test {
        public static void main(String[] args) {
            // 加载jdk的spi接口Command.class
            ServiceLoader<Command> serviceLoader=ServiceLoader.load(Command.class);
            // 遍历加载的jdk的接口
            for(Command command:serviceLoader){
                command.execute();  
            }  
        }
    }

(3)JDK的SPI机制的缺点

JDK标准的SPI会一次性实例化扩展点所有实现，如果有扩展实现初始化很耗时，但如果没用上也加载，会很浪费资源；
JDK的SPI机制没有Ioc和AOP的支持，因此dubbo用了自己的spi机制：增加了对扩展点IoC和AOP的支持，一个扩展点可以直接setter注入其它扩展点。

N.参考

(1)[什么是反射](https://www.cnblogs.com/abcdjava/p/11146473.html)

# Java注解

1.什么是注解

Annontation是Java5开始引入的新特征，中文名称叫注解。它提供了一种安全的类似注释的机制，用来将任何的信息或元数据（metadata）与程序元素（类、方法、成员变量等）进行关联。
为程序的元素（类、方法、成员变量）加上更直观更明了的说明，这些说明信息是与程序的业务逻辑无关，并且供指定的工具或框架使用。
Annontation像一种修饰符一样，应用于包、类型、构造方法、方法、成员变量、参数及本地变量的声明语句中。
Java注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。包含在java.lang.annotation包中。

2.注解的用处

(1)生成文档。这是最常见的，也是java最早提供的注解。常用的有@param @return 等
(2)跟踪代码依赖性，实现替代配置文件功能。
(3)在编译时进行格式检查。如@override放在方法前，如果你这个方法并不是覆盖了超类方法，则编译时就能检查出。

3.注解的原理

注解本质是一个继承了Annotation的特殊接口，其具体实现类是Java运行时生成的动态代理类。而我们通过反射获取注解时，返回的是Java运行时生成的动态代理对象$Proxy1。
通过代理对象调用自定义注解（接口）的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会从memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池。

4.元注解

java.lang.annotation提供了四种元注解，专门注解其他的注解（在自定义注解的时候，需要使用到元注解）：

    @Documented – 注解是否将包含在JavaDoc中
    @Retention – 什么时候使用该注解
    @Target – 注解用于什么地方
    @Inherited – 是否允许子类继承该注解
    
(1)@Documented：一个简单的Annotations标记注解，表示是否将注解信息添加在java文档中。

(2)@Retention：定义该注解的生命周期：

    RetentionPolicy.SOURCE : 在编译阶段丢弃。这些注解在编译结束之后就不再有任何意义，所以它们不会写入字节码。@Override, @SuppressWarnings都属于这类注解。
    RetentionPolicy.CLASS : 在类加载的时候丢弃。在字节码文件的处理中有用。注解默认使用这种方式。
    RetentionPolicy.RUNTIME : 始终不会丢弃，运行期也保留该注解，因此可以使用反射机制读取该注解的信息。我们自定义的注解通常使用这种方式。

(3)@Target：表示该注解用于什么地方。默认值为任何元素，表示该注解用于什么地方。可用的ElementType参数包括：

    ElementType.CONSTRUCTOR: 用于描述构造器
    ElementType.FIELD: 成员变量、对象、属性（包括enum实例）
    ElementType.LOCAL_VARIABLE: 用于描述局部变量
    ElementType.METHOD: 用于描述方法
    ElementType.PACKAGE: 用于描述包
    ElementType.PARAMETER: 用于描述参数
    ElementType.TYPE: 用于描述类、接口(包括注解类型) 或enum声明

(4)@Inherited：定义该注释和子类的关系：
@Inherited元注解是一个标记注解，@Inherited 阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。

5.常见标准的Annotation

(1)Override：java.lang.Override是一个标记类型注解，它被用作标注方法。它说明了被标注的方法重载了父类的方法，起到了断言的作用。如果我们使用了这种注解在一个没有覆盖父类方法的方法时，java 编译器将以一个编译错误来警示。
(2)Deprecated：Deprecated 也是一种标记类型注解。当一个类型或者类型成员使用@Deprecated修饰的话，编译器将不鼓励使用这个被标注的程序元素。所以使用这种修饰具有一定的“延续性”：如果我们在代码中通过继承或者覆盖的方式使用了这个过时的类型或者成员，虽然继承或者覆盖后的类型或者成员并不是被声明为@Deprecated，但编译器仍然要报警。
(3)SuppressWarnings：SuppressWarnings不是一个标记类型注解。它有一个类型为String[] 的成员，这个成员的值为被禁止的警告名。对于javac编译器来讲，被-Xlint选项有效的警告名也同样对@SuppressWarings有效，同时编译器忽略掉无法识别的警告名。例如@SuppressWarnings("unchecked");

6.自定义注解

自定义注解类编写的一些规则：

(1)Annotation型定义为@interface, 所有的Annotation会自动继承java.lang.Annotation这一接口,并且不能再去继承别的类或是接口。
(2)参数成员只能用public 或默认(default) 这两个访问权修饰。
(3)参数成员只能用基本类型byte、short、char、int、long、float、double、boolean八种基本数据类型和String、Enum、Class、annotations等数据类型，以及这一些类型的数组。
(4)要获取类方法和字段的注解信息，必须通过Java的反射技术来获取Annotation对象，因为除此之外没有别的获取注解对象的方法。
(5)注解也可以没有定义成员，不过这样注解就没啥用了。
(6)自定义注解需要使用到元注解

自定义注解实例：

    /**
     * 水果名称注解
     */
    @Target(FIELD)
    @Retention(RUNTIME)
    @Documented
    public @interface FruitName {
        String value() default "";
    }
    
    /**
     * 水果颜色注解
     */
    @Target(FIELD)
    @Retention(RUNTIME)
    @Documented
    public @interface FruitColor {
        /**
         * 颜色枚举
         */
        public enum Color{BLUE,RED,GREEN};
    
        /**
         * 颜色属性
         */
        Color fruitColor() default Color.GREEN;
    
    }
    
    /**
     * 水果供应者注解
     */
    @Target(FIELD)
    @Retention(RUNTIME)
    @Documented
    public @interface FruitProvider {
        /**
         * 供应商编号
         */
        public int id() default -1;
    
        /**
         * 供应商名称
         */
        public String name() default "";
    
        /**
         * 供应商地址
         */
        public String address() default "";
    }
    
    /**
     * 注解处理器
     */
    public class FruitInfoUtil {
        public static void getFruitInfo(Class<?> clazz){
    
            String strFruitName=" 水果名称：";
            String strFruitColor=" 水果颜色：";
            String strFruitProvicer="供应商信息：";
    
            Field[] fields = clazz.getDeclaredFields();
    
            for(Field field :fields){
                if(field.isAnnotationPresent(FruitName.class)){
                    FruitName fruitName = (FruitName) field.getAnnotation(FruitName.class);
                    strFruitName=strFruitName+fruitName.value();
                    System.out.println(strFruitName);
                }
                else if(field.isAnnotationPresent(FruitColor.class)){
                    FruitColor fruitColor= (FruitColor) field.getAnnotation(FruitColor.class);
                    strFruitColor=strFruitColor+fruitColor.fruitColor().toString();
                    System.out.println(strFruitColor);
                }
                else if(field.isAnnotationPresent(FruitProvider.class)){
                    FruitProvider fruitProvider= (FruitProvider) field.getAnnotation(FruitProvider.class);
                    strFruitProvicer=" 供应商编号："+fruitProvider.id()+" 供应商名称："+fruitProvider.name()+" 供应商地址："+fruitProvider.address();
                    System.out.println(strFruitProvicer);
                }
            }
        }
    }
    
    /**
     * 注解使用
     */
    public class Apple {
    
        @FruitName("Apple")
        private String appleName;
    
        @FruitColor(fruitColor=Color.RED)
        private String appleColor;
    
        @FruitProvider(id=1,name="陕西红富士集团",address="陕西省西安市延安路89号红富士大厦")
        private String appleProvider;
    
        public void setAppleColor(String appleColor) {
            this.appleColor = appleColor;
        }
        public String getAppleColor() {
            return appleColor;
        }
    
        public void setAppleName(String appleName) {
            this.appleName = appleName;
        }
        public String getAppleName() {
            return appleName;
        }
    
        public void setAppleProvider(String appleProvider) {
            this.appleProvider = appleProvider;
        }
        public String getAppleProvider() {
            return appleProvider;
        }
    
        public void displayName(){
            System.out.println("水果的名字是：苹果");
        }
    }
    
    /**
     * 输出结果
     */
    public class FruitRun {
        public static void main(String[] args) {
            FruitInfoUtil.getFruitInfo(Apple.class);
        }
    }
    
    运行结果是：
    
    水果名称：Apple
    水果颜色：RED
    供应商编号：1 供应商名称：陕西红富士集团 供应商地址：陕西省西安市延安路89号红富士大厦

# Java Web

1.JSP和Servlet

JSP是Servlet技术的扩展，本质上就是Servlet的简易方式。JSP编译后是“类servlet”。Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件。JSP侧重于视图，Servlet主要用于控制逻辑。
JSP执行过程：当服务器启动后，当Web浏览器端发送过来一个页面请求时，Web服务器先判断是否是JSP页面请求。如果该页面只是一般的HTML/XML页面请求，则直接将HTML/XML页面代码传给Web浏览器端。
如果请求的页面是JSP页面，则由JSP引擎检查该JSP页面，如果该页面是第一次被请求、或不是第一次被请求但已被修改，则JSP引擎将此JSP页面代码转换成Servlet代码，然后JSP引擎调用服务器端的Java编译器对Servlet代码进行编译，把它变成字节码(.class)文件，然后再调用JAVA虚拟机执行该字节码文件，然后将执行结果传给Web浏览器端。
如果该JSP页面不是第一次被请求，且没有被修改过，则直接由JSP引擎调用JAVA虚拟机执行已编译过的字节码.class文件，然后将结果传送Web浏览器端。

2.JSP的4种作用域

    (1)page:代表页面上下文，范围是一个页面及其静态包含的内容。
    (2)request:代表请求上下文，范围是一个请求涉及的几个页面，通常是一个页面和其包含的内容以及forward动作转向的页面。
    (3)session:代表客户的一次会话上下文，范围是一个用户在会话有效期内多次请求所涉及的页面。
    (4)application:全局作用域，代表Web应用程序上下文，范围是整个Web应用中所有请求所涉及的页面。

3.JSP内置对象

9个内置对象：

    pageContext:网页的属性在这里管理。
    page：表示从该页面产生的一个servlet实例。
    out：是javax.jsp.JspWriter的一个实例，并提供了几个方法使你能用于向浏览器回送输出结果。
    config：表示一个javax.servlet.ServletConfig对象，该对象用于存取servlet实例的初始化参数。
    request:表示HttpServletRequest对象，它包含了有关浏览器请求的信息，并且提供了几个用于获取cookie,header和session数据的有用方法。
    response:表示HttpServletResponse对象，并提供了几个用于设置送回浏览器的响应的方法（如cookies,头信息等。）
    session：表示一个请求的javax.servlet.http.HttpSession对象，session可以存储用户的状态信息。
    application:表示一个javax.servlet.ServletContext对象，这有助于查找有关servlet引擎和servlet环境的信息。
    exception：针对错误网页，未捕捉的例外。

4.过滤器和拦截器

(1)Filter需要在web.xml中配置，依赖于Servlet；Interceptor需要在SpringMVC中配置，依赖于框架。
(2)Filter的执行顺序在Interceptor之前，具体的流程

    Filter1.doFilter()的chain.doFilter(request, response)之前逻辑
    Filter2.doFilter()的chain.doFilter(request, response)之前逻辑
    Interceptor1.preHandle()
    Interceptor2.preHandle()
    正常业务逻辑
    Interceptor2.postHandle()
    Interceptor1.postHandle()
    Interceptor2.afterCompletion()
    Interceptor1.afterCompletion()
    Filter2.doFilter()的chain.doFilter(request, response)之后逻辑
    Filter1.doFilter()的chain.doFilter(request, response)之后逻辑
      
5.Servlet实例

通常要编写一个自己的Servlet然后继承自HttpServlet,然后重写其doGet()与doPost()方法。这两个方法都会将HttpServletRequest与HttpServletResponse作为参数传递进去，然后我们从Request中提取前端传来的参数，在相应的doXXX方法内调用事先编写好的Service接口，Dao接口即可将数据准备好放置到Response中并跳转到指定的页面即可，跳转的方式可以选择转发或者重定向。
Servlet使用的是模板方法的设计模式，在Servlet顶层将会调用service方法，该方法会构造HttpServletRequest与HttpServletResponse对象作为参数调用子类重写的doXXX()方法。然后返回请求。
最后我们需要将我们编写的自定义Servlet注册到web.xml中，在web.xml中配置servlet-mapping来为该servlet指定处理哪些请求。

    <web-app xmlns="http://java.sun.com/xml/ns/j2ee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
        version="2.4">
        <servlet>
                <servlet-name>ShoppingServlet</servlet-name>
                <servlet-class>com.myTest.ShoppingServlet</servlet-class>
        </servlet>
    
        <servlet-mapping>
                <servlet-name>ShoppingServlet</servlet-name>
                <url-pattern>/shop/ShoppingServlet</url-pattern>
        </servlet-mapping>
    </web-app>

    // 扩展 HttpServlet 类
    public class ShoppingServlet extends HttpServlet {
    
      public void doGet(HttpServletRequest request,
                        HttpServletResponse response)
                throws ServletException, IOException{
       
      }
      
      public void doPost(HttpServletRequest request,
                        HttpServletResponse response)
                throws ServletException, IOException{
       
      }
    }

N.参考

(1)[JSP内置对象——pageContext对象](https://www.jellythink.com/archives/208)

(2)[JSP九大内置对象及其作用域](https://my.oschina.net/hp2017/blog/1932026)

# Java新特性

1.JDK 8中的Optional

常用API：
    
    ofNullable()
    of()
    isPresent()
    orElse()
    get()
    
实例：

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
    
2.Java8中Stream对列表去重

distinct()是Java8中Stream提供的方法，返回的是由该流中不同元素组成的流。distinct()使用hashCode()和eqauls()方法来获取不同的元素。
因此，需要去重的类必须实现hashCode()和equals()方法。换句话讲，我们可以通过重写定制的hashCode()和equals()方法来达到某些特殊需求的去重。

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

实体类列表的去重，代码中我们使用了Lombok插件的@Data注解，可自动覆写equals()以及hashCode()方法。

    @Data
    public class Student {
      private String stuNo;
      private String name;
    }
    
    @Test
    public void listDistinctByStreamDistinct() throws JsonProcessingException {
        ObjectMapper objectMapper = new ObjectMapper();
        // 1. 对于Student列表去重
        List<Student> studentList = getStudentList();
        out.print("去重前：");
        out.println(objectMapper.writeValueAsString(studentList));
        studentList = studentList.stream().distinct().collect(Collectors.toList());
        out.print("去重后：");
        out.println(objectMapper.writeValueAsString(studentList));
     }

3.Java8 Stream

Java8的API中添加了一个新的特性：流，即stream。stream是将数组或者集合的元素视为流，流在管道中流动过程中，对数据进行筛选、排序和其他操作。

(1)流的特性

    (a)stream不存储数据，而是按照特定的规则对数据进行计算，一般会输出结果；
    (b)stream不会改变数据源，通常情况下会产生一个新的集合；
    (c)stream具有延迟执行特性，只有调用终端操作时，中间操作才会执行。
    (d)对stream操作分为终端操作和中间操作，那么这两者分别代表什么呢？终端操作：会消费流，这种操作会产生一个结果的，如果一个流被消费过了，那它就不能被重用的。中间操作：中间操作会产生另一个流。因此中间操作可以用来创建执行一系列动作的管道。一个特别需要注意的点是：中间操作不是立即发生的。相反，当在中间操作创建的新流上执行完终端操作后，中间操作指定的操作才会发生。所以中间操作是延迟发生的，中间操作的延迟行为主要是让流API能够更加高效地执行。
    (e)stream不可复用，对一个已经进行过终端操作的流再次调用，会抛出异常。

(2)创建Stream

通过数组创建流

    public static void main(String[] args) {
        //1.通过Arrays.stream
        //1.1基本类型
        int[] arr = new int[]{1,2,34,5};
        IntStream intStream = Arrays.stream(arr);
        //1.2引用类型
        Student[] studentArr = new Student[]{new Student("s1",29),new Student("s2",27)};
        Stream<Student> studentStream = Arrays.stream(studentArr);
        //2.通过Stream.of
        Stream<Integer> stream1 = Stream.of(1,2,34,5,65);
        //注意生成的是int[]的流
        Stream<int[]> stream2 = Stream.of(arr,arr);
        stream2.forEach(System.out::println);
    }
    
通过集合创建流

    public static void main(String[] args) {
        List<String> strs = Arrays.asList("11212","dfd","2323","dfhgf");
        //创建普通流
        Stream<String> stream  = strs.stream();
        //创建并行流
        Stream<String> stream1 = strs.parallelStream();
    }
    
(3)常用方法
 
(a)筛选和匹配

流的筛选，即filter，是按照一定的规则校验流中的元素，将符合条件的元素提取出来的操作。filter通常要配合collect（收集），将筛选结果收集成一个新的集合。
流的匹配，与筛选类似，也是按照规则提取元素，不同的是，匹配返回的是单个元素或单个结果。

    // 普通类型筛选
     List<Integer> collect = intList.stream().filter(x -> x > 7).collect(Collectors.toList());
    // 引用类型筛选
     List<Person> collect = personList.stream().filter(x -> x.getSalary() > 8000).collect(Collectors.toList());
     // 匹配第一个
     Optional<Integer> findFirst = list.stream().filter(x -> x > 6).findFirst();
     // 匹配任意（适用于并行流）
     Optional<Integer> findAny = list.parallelStream().filter(x -> x > 6).findAny();
     // 是否包含
     boolean anyMatch = list.stream().anyMatch(x -> x < 6);

(b)聚合

在stream中，针对流进行计算后得出结果，例如求和、求最值等，这样的操作被称为聚合操作。聚合操作在广义上包含了max、min、count等方法和reduce、collect。

    // 获取String集合中最长的元素
    Optional<String> max = list.stream().max(Comparator.comparing(String::length));
    // 获取Integer集合中的最大值
    Optional<Integer> reduce = list.stream().max(new Comparator<Integer>() {
      @Override
      public int compare(Integer o1, Integer o2) {
          return o1.compareTo(o2);
      }
     });
    // 对象集合最值（Person见演示数据）
    Optional<Person> max = list.stream().max(Comparator.comparingInt(Person::getSalary));
    // count
    long count = list.stream().filter(x -> x > 6).count();
    
    ////////////////////////////////////////////////////////
    // 缩减操作，就是把一个流缩减成一个值，比如对一个集合求和、求乘积等。
    List<Integer> list = Arrays.asList(1, 3, 2);
    // 求和
    Integer sum = list.stream().reduce(1, (x, y) -> x + y);
    // 结果是7，也就是list元素求和再加上1
    System.out.println(sum);
    // 写法2
    Integer sum2 = list.stream().reduce(1, Integer::sum);
    System.out.println(sum2);  // 结果：7
   
    // 求最值
    Integer max = list.stream().reduce(6, (x, y) -> x > y ? x : y);
    System.out.println(max);  // 结果：6
    // 写法2
    Integer max2 = list.stream().reduce(1, Integer::max);
    System.out.println(max2); // 结果：3
    
    // 对象集合求和、求最值：
    System.out.println(personList.stream().map(Person::getSalary).reduce(Integer::sum));
    // 求最值-方式1
    Person person = personList.stream().reduce((p1, p2) -> p1.getSalary() > p2.getSalary() ? p1 : p2).get();
    // 预期结果：Lily:9000
    System.out.println(person.getName() + ":" + person.getSalary());
    // 求最值-方式2
    System.out.println(personList.stream().map(Person::getSalary).reduce(Integer::max));
    // 求最值-方式3：
    System.out.println(personList.stream().max(Comparator.comparingInt(Person::getSalary)).get().getSalary());

    ////////////////////////////////////////////////////////
    // 收集（collect）
    // averaging系列，averagingDouble、averagingInt、averagingLong三个方法处理过程是相同的，都是返回stream的平均值，只是返回结果的类型不同。
    Double averageSalary = personList.stream().collect(Collectors.averagingDouble(Person::getSalary));
    
    // summarizing系列，summarizingDouble、summarizingInt、summarizingLong三个方法可以返回stream的一个统计结果map，不同之处也是结果map中的value类型不一样，分别是double、int、long类型。
    DoubleSummaryStatistics collect = personList.stream().collect(Collectors.summarizingDouble(Person::getSalary));
    System.out.println(collect);
    // 输出结果：
    // DoubleSummaryStatistics{count=3, sum=24900.000000, min=7000.000000, average=8300.000000, max=9000.000000}
    
    //joining，joining可以将stream中的元素用特定的连接符（没有的话，则直接连接）连接成一个字符串。
    String names = personList.stream().map(p -> p.getName()).collect(Collectors.joining(","));
    
    // reduce
    Integer sumSalary = personList.stream().collect(Collectors.reducing(0, Person::getSalary, (i, j) -> i + j));
    Optional<Integer> sumSalary2 = list.stream().map(Person::getSalary).reduce(Integer::sum);

    //groupingBy方法可以将stream中的元素按照规则进行分组，类似mysql中groupBy语句。 
    // 单级分组
    Map<String, List<Person>> group = personList.stream().collect(Collectors.groupingBy(Person::getName));
    // 多级分组：先以name分组，再以salary分组：
    Map<String, Map<Integer, List<Person>>> group2 = personList.stream().collect(Collectors.groupingBy(Person::getName, Collectors.groupingBy(Person::getSalary)));

    //Collectors内置的toList等方法可以十分便捷地将stream中的元素收集成想要的集合，是一个非常常用的功能，通常会配合filter、map等方法使用。
    // toList
    List<String> names = personList.stream().map(Person::getName).collect(Collectors.toList());
    // toSet
    Set<String> names2 = personList.stream().map(Person::getName).collect(Collectors.toSet());
    // toMap
    Map<String, Person> personMap = personList.stream().collect(Collectors.toMap(Person::getName, p -> p));

(c)映射（map）

Stream流中，map可以将一个流的元素按照一定的映射规则映射到另一个流中。

    // 数据>>数据
    Arrays.stream(strArr).map(x -> x.toUpperCase()).forEach(System.out::println);
    // 对象集合>>数据
    personList.stream().map(person -> person.getSalary()).forEach(System.out::println);
    // 对象集合>>对象集合
    // 这种写法改变了原有的personList。
    List<Person> collect = personList.stream().map(person -> {
        person.setName(person.getName());
        person.setSalary(person.getSalary() + 10000);
        return person;
    }).collect(Collectors.toList());
    System.out.println(collect.get(0).getSalary());
    System.out.println(personList.get(0).getSalary());
    // 这种写法并未改变原有personList。
    List<Person> collect2 = personList.stream().map(person -> {
        Person personNew = new Person(null, 0);
        personNew.setName(person.getName());
        personNew.setSalary(person.getSalary() + 10000);
        return personNew;
    }).collect(Collectors.toList());
    System.out.println(collect2.get(0).getSalary());
    System.out.println(personList.get(0).getSalary());

(d)排序（sorted）

Sorted方法是对流进行排序，并得到一个新的stream流，是一种中间操作。Sorted方法可以使用自然排序或特定比较器。

    // 自然排序
    Arrays.stream(strArr).sorted().collect(Collectors.toList())
    // 自定义排序
    // 1、按长度自然排序，即长度从小到大
    Arrays.stream(strArr).sorted(Comparator.comparing(String::length)).forEach(System.out::println);
    // 2、按长度倒序，即长度从大到小
    Arrays.stream(strArr).sorted(Comparator.comparing(String::length).reversed()).forEach(System.out::println);
    // 3、首字母倒序
    Arrays.stream(strArr).sorted(Comparator.reverseOrder()).forEach(System.out::println);
    // 4、首字母自然排序
    Arrays.stream(strArr).sorted(Comparator.naturalOrder()).forEach(System.out::println);

(e)提取流和组合流

    // 可以把两个stream合并成一个stream（合并的stream类型必须相同）,只能两两合并
    Stream.concat(stream1,stream2).distinct().forEach(System.out::println);
    // limit，限制从流中获得前n个数据
    Stream.iterate(1,x->x+2).limit(10).forEach(System.out::println);
    // skip，跳过前n个数据
    Stream.iterate(1,x->x+2).skip(1).limit(5).forEach(System.out::println);
