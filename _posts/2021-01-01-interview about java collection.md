---
layout: post
title: "后端开发面试题 -- Java集合篇"
description: 后端开发面试题 -- Java集合篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

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

(a)Vector、HashTable、Properties和Stack是同步类，所以它们是线程安全的，可以在多线程环境下使用。
(b)Java1.5并发包（java.util.concurrent）包含线程安全集合类，允许在迭代时修改集合，是fail-safe的，不会抛出ConcurrentModificationException。一部分类为：CopyOnWriteArrayList、 ConcurrentHashMap、CopyOnWriteArraySet。

5.当一个集合被作为参数传递给一个函数时，如何才可以确保函数不能修改它？

在作为参数传递之前，我们可以使用Collections.unmodifiableCollection(Collection c)方法创建一个只读集合，这将确保改变集合的任何操作都会抛出UnsupportedOperationException。
UnsupportedOperationException是用于表明操作不支持的异常。在JDK类中已被大量运用，在集合框架java.util.Collections.UnmodifiableCollection将会在所有add和remove操作中抛出这个异常。

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

常见的函数：

    sort(Collection)
    shuffle(Collection)
    reverse(Collection)
    fill(Collection,Object)
    copy(List, List)
    rotate(Collection,int)
    swap(List,int,int)
    indexOfSublist(List,List)
    lastIndexOfSublist(List,List)
    max(Collection,Comparator)
    min(Collection,Comparator)
    unmodifiableCollection(Collection c)
    synchronizedMap(Map<K,V> m)

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
    
HashMap为什么不直接使用对象的原始hash值呢?通过如下方法，而不是通过key.hashCode()方法获取。通过移位和异或运算，可以让hash变得更复杂，进而影响hash的分布性。

    static final int hash(Object key) {

        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    
给定字符串作为key及哈希表的大小，hash函数如下：

    public class Solution {
        /**
         * @param key: A string you should hash
         * @param HASH_SIZE: An integer
         * @return: An integer
         */
        public int hashCode(char[] key, int HASH_SIZE) {
            // write your code here
            long ans = 0;
            for(int i = 0; i < key.length; i++){
                ans = (ans * 33 + (int)(key[i])) % HASH_SIZE;
            }
            return (int)(ans);
        }
    }

12.HashMap中的链表与红黑树

链表树化：
指的就是把链表转换成红黑树，树化需要满足以下两个条件：
(1)链表长度大于等于8链表树化。桶中数量少于6则从树转成链表。
(2)table数组长度大于等于64。为什么table数组容量大于等于64才树化？因为当table数组容量比较小时，键值对节点hash的碰撞率可能会比较高，进而导致链表长度较长。这个时候应该优先扩容，而不是立马树化。

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

HashMap&ConcurrentHashMap的区别？
除了加锁，原理上无太大区别。另外，HashMap的键值对允许有null，但是ConCurrentHashMap都不允许。

ConcurrentHashMap的并发度是什么？
程序运行时能够同时更新ConccurentHashMap且不产生锁竞争的最大线程数。默认为16，且可以在构造函数中设置。当用户设置并发度时，ConcurrentHashMap会使用大于等于该值的最小2幂指数作为实际并发度（假如用户设置并发度为17，实际并发度则为32）

16.HashTable与ConcurrentHashMap

同样是线程安全，HashTable是使用synchronize关键字加锁的原理（就是对对象加锁），使用一把锁（锁住整个链表结构）处理并发问题，多个线程竞争一把锁，容易阻塞。
ConcurrentHashMap，在JDK1.7中使用分段锁（ReentrantLock + Segment + HashEntry），相当于把一个HashMap分成多个段，每段分配一把锁，这样支持多线程访问。锁粒度：基于Segment，包含多个HashEntry。在JDK1.8 中使用CAS + synchronized + Node + 红黑树。锁粒度：Node（首结点）（实现 Map.Entry<K,V>）。锁粒度降低了。

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
    
    //这是一颗语法糖，编译后相当于：
    for(Iterator i = platformList.iterator();i.hasNext();){
        String platform = (String)i.next();
        if(platform.equals("aaa")){
            platformList.remove(s);
        }
    }
    
报错原因在i.next()中，Iterator取下一个值时候会先判断modCount是否和expectedModCount一样，不一样就报错。
这里的modCount是删除的元素的数量计数，expectedModCount是Iterator期望的删除数量，使用Iterator的remove()方法的时候，Iterator会将调用ArrayList.this.remove(lastRet)删除元素同时使得modCount++，然后将modCount的值赋给expectedModCount，确保它们一样。
所以到这里我们就可以发现问题了，在forEach循环体里，我们直接使用的是lists.remove(“3”)的方法来删除元素，导致了expectedModCount和modCount不一致。
所以要在遍历的时候删除元素，不能使用forEach遍历的方式，要使用Iterator的方法。

    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }
    final void checkForComodification() {
        if (modCount != expectedModCount)
           throw new ConcurrentModificationException();
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
(2)当我们试图把某个类的对象当成HashMap的key，或试图将这个类的对象放入HashSet中保存时，重写该类的equals(Object obj)方法和hashCode()方法很重要，而且这两个方法的返回值必须保持一致：当该类的两个的hashCode()返回值相同时，它们通过equals()方法比较也应该返回true。通常来说，所有参与计算hashCode()返回值的关键属性，都应该用于作为equals()比较的标准。
(3)HashSet的其他操作都是基于HashMap的。

25.写时复制(Copy-On-Write)在Java中应用

写时复制（Copy-on-write，简称COW）是一种计算机程序设计领域的优化策略。
其核心思想是，如果有多个调用者同时请求相同资源（如内存或磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者试图修改资源的内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这个过程对其他的调用者是透明的（transparently）。
此做法的主要优点是如果调用者没有修改该资源，就不会有副本（private copy）被建立，因此多个调用者只是读取操作时可以共享同一份资源。
COW技术的应用场景很多，Linux通过Copy On Write技术极大地减少了Fork的开销。文件系统通过Copy On Write技术一定程度上保证数据的完整性。数据库服务器也一般采用了写时复制策略，为用户提供一份snapshot。

(1)Vector和synchronizedList

我们知道ArrayList是线程不安全的，而Vector是线程安全的容器。查看源码可以知道，Vector之所以线程安全，是因为它几乎在每个方法声明处都加了synchronized关键字来使整体方法原子化。
另外，使用Collections.synchronizedList(new ArrayList())修饰后，新建出来的ArrayList也是安全的，它是如何实现的呢？查看源码发现，它也是几乎在每个方法都加上synchronized关键字使方法原子化，只不过它不是把synchronized加在方法的声明处，而是加在方法的内部。
容器是线程安全的，并不意味着就可以在多线程环境下放心大胆地随便用了，来看下面这段使用Vector的代码。以下代码抛出ConcurrentModificationException。

    @Test
    public void testVectorConcurrentReadWrite() {
        Vector<Integer> vector = new Vector<>();
        vector.add(1);
        vector.add(2);
        vector.add(3);
        vector.add(4);
        vector.add(5);
        for (Integer item : vector) {
            new Thread(vector::clear).start();
            System.out.println(item);
        }
    }
    
在一个线程中使用Iterator迭代器遍历vector，同时另一个线程对vector作修改时，会抛出java.util.ConcurrentModificationException异常。
很多人不理解，因为Vector的所有方法都加了synchronized关键字来修饰，包括迭代器方法，理论上应该是线程安全的呀。
两个关键变量：expectedModCount：表示对List修改次数的期望值，它的初始值与modCount相等。modCount：表示List集合结构被修改次数，是AbstractList类中的一个成员变量，初始值为0。
看过ArrayList的源码就知道，每次调用add()和remove()方法时就会对modCount进行加1操作。而我们上面的测试代码中调用了Vector类的clear()方法，这个方法中对modCount进行了加1，而迭代器中的expectedModCount依然等于0，两者不等，因此抛了异常。这就是集合中的fail-fast机制，fail-fast 机制用来防止在对集合进行遍历过程当中，出现意料之外的修改，会通过Unchecked异常暴力的反应出来。
虽然Vector的方法都采用了synchronized进行了同步，但是实际上通过Iterator访问的情况下，每个线程里面返回的是不同的iterator，也即是说expectedModCount变量是每个线程私有。如果此时有2个线程，线程1在进行遍历，线程2在进行修改，那么很有可能导致线程2修改后导致Vector中的modCount自增了，线程2的expectedModCount也自增了，但是线程1的expectedModCount没有自增，此时线程1遍历时就会出现expectedModCount不等于modCount的情况了。
同样地，SynchronizedList在使用迭代器遍历的时候同样会有问题的，源码中的注释已经提醒我们要手动加锁了。
foreach循环里不能调用集合的remove/add/clear方法这一条规约不仅对非线程安全的ArrayList/LinkedList适用，对于线程安全的Vector以及synchronizedList也同样适用！！因此，要想解决以上问题，只能在遍历前（无论用不用iterator）加锁。

    synchronized (vector) {
        for (int i = 0; i < vector.size(); i++) {
            System.out.println(vector.get(i));
    }
    //或者
    synchronized (vector) {
        for (Integer item : vector) {
            System.out.println(item);
    }
    
仅仅是遍历一下容器都要上锁，性能必然不好。
其实并非只有遍历前加锁这一种解决方法，使用并发容器CopyOnWriteArrayList也能避免以上问题。

(2)CopyOnWriteArrayList介绍

一般来说，我们会认为：CopyOnWriteArrayList是同步List的替代品，CopyOnWriteArraySet是同步Set的替代品。
无论是Hashtable–>ConcurrentHashMap，还是说Vector–>CopyOnWriteArrayList。JUC下支持并发的容器与老一代的线程安全类相比，总结起来就是加锁粒度的问题。
Hashtable与Vector加锁的粒度大，直接在方法声明处使用synchronized。
ConcurrentHashMap、CopyOnWriteArrayList的加锁粒度小。用各种方式来实现线程安全，比如我们知道的ConcurrentHashMap用了CAS、+ volatile等方式来实现线程安全。
JUC下的线程安全容器在遍历的时候不会抛出ConcurrentModificationException异常。
所以一般来说，我们都会使用JUC包下给我们提供的线程安全容器，而不是使用老一代的线程安全容器。
下面我们来看看CopyOnWriteArrayList是怎么实现的，为什么使用迭代器遍历的时候就不用额外加锁，也不会抛出ConcurrentModificationException异常。
实现原理：Copy-on-write是解决并发的的一种思路，指的是实行读写分离，如果执行的是写操作，则复制一个新集合，在新集合内添加或者删除元素。待一切修改完成之后，再将原集合的引用指向新的集合。
这样的好处就是，可以高并发地对COW进行读和遍历操作，而不需要加锁，因为当前集合不会添加任何元素。
写时复制（copy-on-write）的这种思想，这种机制，并不是始于Java集合之中，在Linux、Redis、文件系统中都有相应思想的设计，是一种计算机程序设计领域的优化策略。
CopyOnWriteArrayList的核心理念就是读写分离，写操作在一个复制的数组上进行，读操作还是在原始数组上进行，读写分离，互不影响。写操作需要加锁，防止并发写入时导致数据丢失。写操作结束之后需要让数组指针指向新的复制数组。
看一下其读写的源码，写操作加锁，防止并发写入时导致数据丢失，并复制一个新数组，增加操作在新数组上完成，将array指向到新数组中，最后解锁。至于读操作，则是直接读取array数组中的元素。

    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
    
    final void setArray(Object[] a) {
        array = a;
    }
    
    public E get(int index) {
        return get(getArray(), index);
    }
    
    final Object[] getArray() {
        return array;
    }
    
(3)遍历COWIterator

到现在，还是没有解释为什么CopyOnWriteArrayList在遍历时，对其进行修改而不抛出异常。
不管是foreach循环还是直接写Iterator来遍历，实际上都是使用Iterator遍历。那么就直接来看下CopyOnWriteArrayList的iterator()方法。

    public Iterator<E> iterator() {
        return new COWIterator<E>(getArray(), 0);
    }

可以发现的是，迭代器所有的操作都基于snapshot数组，而snapshot是传递进来的array数组。
也就是说在使用COWIterator进行遍历的时候，如果修改了集合，集合内部的array就指向了新的一个数组对象，而COWIterator内部的那个snapshot还是指向初始化时传进来的旧数组，所以不会抛异常，因为旧数组永远没变过，旧数组读操作永远可靠且安全。

(4)CopyOnWriteArrayList与synchronizedList性能测试

写单元测试来对CopyOnWriteArrayList与synchronizedList的并发写性能作测试，由于CopyOnWriteArrayList写时直接复制新数组，可以预想到其写操作性能不高，会劣于synchronizedList。同样条件下的写耗时，CopyOnWriteArrayList是synchronizedList的30多倍。
同样地，写单元测试来对CopyOnWriteArrayList与synchronizedList的并发读性能作测试，由于CopyOnWriteArrayList读操作不加锁，可以预想到其读操作性能明显会优于synchronizedList。同等条件下的读耗时，CopyOnWriteArrayList只有synchronizedList的一半。

(5)CopyOnWriteArrayList优缺点总结
优点：
对于一些读多写少的数据，写入时复制的做法就很不错，例如配置、黑名单、物流地址等变化非常少的数据，这是一种无锁的实现。可以帮我们实现程序更高的并发。
CopyOnWriteArrayList并发安全且性能比Vector好。Vector是增删改查方法都加了synchronized 来保证同步，但是每个方法执行的时候都要去获得锁，性能就会大大下降，而CopyOnWriteArrayList只是在增删改上加锁，但是读不加锁，在读方面的性能就好于Vector。

缺点：
数据一致性问题。CopyOnWrite容器只能保证数据的最终一致性，不能保证数据的实时一致性。比如线程A在迭代CopyOnWriteArrayList容器的数据。线程B在线程A迭代的间隙中将CopyOnWriteArrayList部分的数据修改了，但是线程A迭代出来的是旧数据。
内存占用问题。如果CopyOnWriteArrayList经常要增删改里面的数据，并且对象比较大，频繁地写会消耗内存，从而引发Java的GC问题，这个时候，我们应该考虑其他的容器，例如ConcurrentHashMap。

N.参考

(1)[Java容器详解](https://www.jianshu.com/p/8ef342da8732)

(2)[Java容器（接口）](https://cloud.tencent.com/developer/article/1334703)

(3)[Java容器（实现）](https://cloud.tencent.com/developer/article/1334702)

(4)[HashMap？ConcurrentHashMap？相信看完这篇没人能难住你！](https://blog.csdn.net/weixin_44460333/article/details/86770169)

(5)[ConcurrentHashMap底层实现原理(JDK1.7 & 1.8)](https://www.jianshu.com/p/865c813f2726)

(6)[JDK7与JDK8中HashMap的实现](https://my.oschina.net/hosee/blog/618953)

(7)[ConcurrentHashMap总结](https://my.oschina.net/hosee/blog/675884)

(8)[30个Java集合面试必备的问题和答案](https://mp.weixin.qq.com/s/5JbhrM677q3aDQmErnYAGw)