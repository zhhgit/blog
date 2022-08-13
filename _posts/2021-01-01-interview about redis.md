---
layout: post
title: "面试题 -- Redis篇"
description: 面试题 -- Redis篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# Redis基础

1.特点、优势

key-value非关系型数据库，高性能的内存存储，单线程、支持持久化，丰富的数据类型，所有操作都是原子性的，支持数据备份。

具体：
高性能：数据在内存中，读写速度非常快，支持并发10WQPS。
单线程：单进程单线程，是线程安全的，采用IO多路复用机制。
支持持久化：可以将内存中数据保存在磁盘中，重启时加载。
丰富的数据类型：支持字符串（string）、散列（hash）、列表（list）、集合（set）、有序集合（sorted set）等。
支持数据备份：主从复制，哨兵，高可用。

2.应用场景

(1)计数器:可以对String进行自增自减运算，从而实现计数器功能。Redis这种内存型数据库的读写性能非常高，很适合存储频繁读写的计数量。
(2)缓存:将热点数据放到内存中，设置内存的最大使用量以及淘汰策略来保证缓存的命中率。
(3)查找表:例如DNS记录就很适合使用Redis进行存储。查找表和缓存类似，也是利用了Redis快速的查找特性。但是查找表的内容不能失效，而缓存的内容可以失效，因为缓存不作为可靠的数据来源。
(4)消息队列:List是一个双向链表，可以通过lpush和rpop写入和读取消息，不过最好使用Kafka、RabbitMQ等消息中间件。
(5)会话缓存:可以使用Redis来统一存储多台应用服务器的会话信息。当应用服务器不再存储用户的会话信息，也就不再具有状态，一个用户可以请求任意一个应用服务器，从而更容易实现高可用性以及可伸缩性。
(6)分布式锁实现:在分布式场景下，无法使用单机环境下的锁来对多个节点上的进程进行同步。可以使用Redis自带的SETNX命令实现分布式锁，除此之外，还可以使用官方提供的RedLock分布式锁实现。
(7)其它:Set可以实现交集、并集等操作，从而实现共同好友等功能。ZSet可以实现有序性操作，从而实现排行榜等功能。

3.Redis为什么是单线程的还很快？达到10万+的QPS。

(1)Redis是基于内存的，内存的读写速度非常快。
(2)Redis是单线程的，避免了不必要的上下文切换和竞争条件，不存在多线程导致的CPU切换，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有死锁问题导致的性能消耗。
(3)Redis使用IO多路复用技术，可以处理并发的连接，非阻塞IO。
(4)数据结构简单，对数据操作也简单。

4.Redis与MemCached区别

(1)数据操作不同
MemCached只支持简单的key-value存储，不支持枚举，数据全部存在内存之中，不支持持久化和复制等功能。Redis拥有更多的数据结构和并支持更丰富的数据操作，支持list、set、sorted set、hash等众多数据结构，还同时提供了持久化和复制等功能。
Redis单个value的最大限制是1GB，而MemCached则只能保存1MB内的数据。

(2)内存管理机制不同
在Redis中，并不是所有的数据都一直存储在内存中的。这是和MemCached相比一个最大的区别。当物理内存用完时，Redis可以将一些很久没用到的value交换到磁盘。
Redis只会缓存所有的key的信息，如果Redis发现内存的使用量超过了某一个阀值，将触发swap的操作，Redis根据“swappability = age*log(size_in_memory)”计算出哪些key对应的value需要swap到磁盘，然后再将这些key对应的value持久化到磁盘中，同时在内存中清除。这种特性使得Redis可以保持超过其机器本身内存大小的数据。
MemCached默认使用Slab Allocation机制管理内存，其主要思想是按照预先规定的大小，将分配的内存分割成特定长度的块以存储相应长度的key-value数据记录，以完全解决内存碎片问题。

(3)性能不同
由于Redis只使用单核，而MemCached可以使用多核，所以平均每一个核上Redis在存储小数据时比MemCached性能更高。而在100k以上的数据中，MemCached性能要高于Redis，虽然Redis也在存储大数据的性能上进行了优化，但是比起MemCached，还是稍有逊色。

(4)集群管理不同
MemCached本身并不支持分布式，因此只能在客户端通过像一致性哈希这样的分布式算法来实现MemCached的分布式存储。相较于MemCached只能采用客户端实现分布式存储，Redis更偏向于在服务器端构建分布式存储。

5.Redis与MongoDB区别

MongoDB用来大数据量级的存储。
MongoDB是一个“存储数据”的系统，增删改查可以添加很多条件，就像SQL数据库一样灵活。
MongoDB和Redis都是NoSQL，采用结构型数据存储。二者在使用场景中，存在一定的区别，这也主要由于二者在内存映射的处理过程，持久化的处理方法不同。MongoDB建议集群部署，更多的考虑到集群方案，Redis更偏重于进程顺序写入，虽然支持集群，也仅限于主-从模式。

6.事务与流水线

一个事务包含了多个命令，服务器在执行事务期间，不会改去执行其它客户端的命令请求。Redis最简单的事务实现方式是使用MULTI和EXEC命令将事务操作包围起来。

因为redis的客户端和服务器的连接时基于TCP的，默认每次连接都时只能执行一个命令。流水线则是允许利用一次连接来处理多条命令，从而可以节省一些tcp连接的开销。流水线和事务的差异在于流水线是为了节省通信的开销，但是并不会保证原子性。
pipeline只是把多个redis指令一起发出去，redis并没有保证这些指定的执行是原子的；multi相当于一个redis的transaction的，保证整个操作的原子性，避免由于中途出错而导致最后产生的数据不一致。

Redis事务功能是通过MULTI、EXEC、DISCARD和WATCH四个原语实现的。Redis会将一个事务中的所有命令序列化，然后按顺序执行。
MULTI命令：用于开启一个事务，它总是返回OK。MULTI执行之后，客户端可以继续向服务器发送任意多条命令，这些命令不会立即被执行，而是被放到一个队列中，当EXEC命令被调用时，所有队列中的命令才会被执行。
EXEC命令：执行所有事务块内的命令。返回事务块内所有命令的返回值，按命令执行的先后顺序排列。当操作被打断时(其他客户端的操作)，返回空值nil。
DISCARD命令：在执行exec之前执行该命令，提交事务会失败，执行的命令会进行回滚。客户端可以清空事务队列，并放弃执行事务，并且客户端会从事务状态中退出。redis的discard只是结束本次事务，其他正确命令造成的影响仍然存在。
WATCH命令：可以为Redis事务提供check-and-set（CAS）行为。可以监控一个或多个键，一旦其中有一个键被修改（或删除），之后的事务就不会执行，监控一直持续到EXEC命令。

Redis在事务失败时不进行回滚，而是继续执行余下的命令，所以Redis的内部可以保持简单且快速。如果在一个事务中的命令出现错误，那么所有的命令都不会执行；如果在一个事务中出现运行错误，那么正确的命令会被执行。redis支持事务，但属于弱事务，中间的一些异常可能会导致事务失效。
命令错误，语法不正确，导致事务不能正常结束：exec提交事务异常，此前正确的命令，无法执行。
运行错误，语法正确，但类型错误，事务可以正常结束：例如类型异常，此前正确的命令，正常执行。

7.pipeline

使用Pipeline模式，客户端可以一次性的发送多个命令，无需等待服务端返回。这样就大大的减少了网络往返时间，提高了系统性能。
pipeline是多条命令的组合，使用PIPELINE可以解决网络开销的问题，原理也非常简单，将多个指令打包后一次性提交到Redis, 网络通信只有一次。
redis的延迟主要出现在网络请求的IO次数上，因此我们在使用redis的时候，尽量减少网络IO次数，通过pipeline的方式将多个指令封装在一个命令里执行。

8.事件(或者问Redis的线程模型)

Redis服务器是一个事件驱动程序。
(1)文件事件:服务器通过套接字与客户端或者其它服务器进行通信，文件事件就是对套接字操作的抽象。Redis基于Reactor模式开发了自己的网络事件处理器，使用I/O多路复用程序来同时监听多个套接字，并将到达的事件传送给文件事件分派器，分派器会根据套接字产生的事件类型调用相应的事件处理器。
(2)时间事件:服务器有一些操作需要在给定的时间点执行，时间事件是对这类定时操作的抽象。时间事件又分为：定时事件（是让一段程序在指定的时间之内执行一次），周期性事件（是让一段程序每隔指定时间就执行一次）。Redis将所有时间事件都放在一个无序链表中，通过遍历整个链表查找出已到达的时间事件，并调用相应的事件处理器。

文件事件处理器包括分别是套接字、I/O多路复用程序、 文件事件分派器（dispatcher）、以及事件处理器。
使用I/O多路复用程序来同时监听多个套接字，并根据套接字目前执行的任务来为套接字关联不同的事件处理器。当被监听的套接字准备好执行连接应答（accept）、读取（read）、写入（write）、关闭（close）等操作时，与操作相对应的文件事件就会产生，这时文件事件处理器就会调用套接字之前关联好的事件处理器来处理这些事件。
I/O多路复用程序负责监听多个套接字，并向文件事件分派器传送那些产生了事件的套接字。尽管多个文件事件可能会并发地出现，但I/O 多路复用程序总是会将所有产生事件的套接字都入队到一个队列里面，然后通过这个队列，以有序（sequentially）、同步（synchronously）、每次一个套接字的方式向文件事件分派器传送套接字。当上一个套接字产生的事件被处理完毕之后（该套接字为事件所关联的事件处理器执行完毕），I/O 多路复用程序才会继续向文件事件分派器传送下一个套接字。如果一个套接字又可读又可写的话，那么服务器将先读套接字，后写套接字。

9.使用Redis的方式

    UP：使用RedisConnector工具类，JedisBalancer线程池中获取连接，直接用Jedis。
    RedisTemplate：opsForValue()、opsForHash()、opsForList()、opsForSet()、opsForZSet()
    缓存注解：使用Spring Cache集成Redis（也就是注解的方式）。

10.关于Redis新版本开始引入多线程

存在问题：
若客户端向Redis发送一条耗时较长的命令，比如删除一个含有上百万对象的Set键，或者执行flushdb，flushall操作，Redis服务器需要回收大量的内存空间，导致服务器卡住好几秒，对负载较高的缓存系统而言将会是个灾难。

Lazy Free：
为了解决这个问题，在Redis 4.0版本引入了Lazy Free，将慢操作异步化。将大键的删除操作异步化，采用非阻塞删除（对应命令UNLINK），大键的空间回收交由单独线程实现，主线程只做关系解除，可以快速返回，继续处理其他事件，避免服务器长时间阻塞。
异步删除，Redis在回收对象时，会先计算回收收益，只有回收收益在超过一定值时，采用封装成Job加入到异步处理队列中，否则直接同步回收，这样效率更高。回收收益计算也很简单，比如String类型，回收收益值就是1，而Set类型，回收收益就是集合中元素个数。

多线程I/O：
Redis在6.0版本实现了多线程I/O。Redis的性能瓶颈并不在CPU上，而是在内存和网络上。因此6.0发布的多线程并未将事件处理改成多线程，而是在I/O上，此外如果把事件处理改成多线程，不但会导致锁竞争，而且会有频繁的上下文切换，即使用分段锁来减少竞争，对Redis内核也会有较大改动，性能也不一定有明显提升。
Redis实现的多线程部分，利用多核来分担I/O读写负荷。在事件处理线程每次获取到可读事件时，会将所有就绪的读事件分配给I/O线程（多个），并进行等待，在所有I/O线程完成读操作后，事件处理线程开始执行任务处理，在处理结束后，同样将写事件分配给I/O线程，等待所有I/O线程完成写操作。
局限性是6.0版本的多线程并非彻底的多线程，I/O线程只能同时执行读或者同时执行写操作，期间事件处理线程一直处于等待状态，并非流水线模型，有很多轮训等待开销。
从压测性能看，多线程版本性能是单线程版本的2倍。

11.缓存注解

用缓存要注意，启动类要加上一个注解开启缓存
 
     @SpringBootApplication(exclude=DataSourceAutoConfiguration.class)
     @EnableCaching
     public class Application {
     
         public static void main(String[] args) {
             SpringApplication.run(Application.class, args);
         }
     
 }
 
@Cacheable：
注解在方法上，表示该方法的返回结果是可以缓存的，也就是返回结果被放在缓存中，以便于以后用相同参数调用该方法时，返回缓存中的值，而不执行该方法。注意：参数相同，参数相同！！
Key：缓存的Key，可以为空，如果指定要按照SPEL表达式编写，如果不指定，则按照方法的所有参数进行组合。Value：缓存的名称，必须指定至少一个（如 @Cacheable (value='user')或者 @Cacheable(value={'user1','user2'})），当有多个缓存名，关联的每一个缓存都会被检查，只要至少其中一个命中缓存，就会被返回。Condition：缓存的条件，可以为空，使用SPEL编写，返回true或者false，只有为true才进行缓存。
注意这里与Redis中Key、Value概念的区分。这里缓存名称时相当于Redis中的Key。这里的Key类似于Redis Hash中的field，这里的Value与Redis Hash的value概念相同。
 
@CachePut：
根据方法的请求参数对其结果进行缓存，和@Cacheable不同的是，它每次都会触发真实方法的调用。参数描述见上。
 
@CacheEvict：根据条件对缓存进行清空。Key：同上。Value：同上。Condition：同上。allEntries：是否清空所有缓存内容，缺省为false，如果指定为true，则方法调用后将立即清空所有缓存。beforeInvocation：是否在方法执行前就清空，缺省为false，如果指定为true，则在方法还没有执行的时候就清空缓存。缺省情况下，如果方法执行抛出异常，则不会清空缓存。
 
     @Service
     public class UserServiceImpl implements UserService{
     
         public static Logger logger = LogManager.getLogger(UserServiceImpl.class);
     
         private static Map<Integer, User> userMap = new HashMap<>();
         static {
             userMap.put(1, new User(1, "aaa", 25));
             userMap.put(2, new User(2, "bbb", 26));
             userMap.put(3, new User(3, "ccc", 24));
         }
     
         @CachePut(value ="user", key = "#user.id")
         @Override
         public User save(User user) {
             userMap.put(user.getId(), user);
             logger.info("进入save方法，当前存储对象：{}", user.toString());
             return user;
         }
     
         @CacheEvict(value="user", key = "#id")
         @Override
         public void delete(int id) {
             userMap.remove(id);
             logger.info("进入delete方法，删除成功");
         }
     
         @Cacheable(value = "user", key = "#id")
         @Override
         public User get(Integer id) {
             logger.info("进入get方法，当前获取对象：{}", userMap.get(id)==null?null:userMap.get(id).toString());
             return userMap.get(id);
         }
     }

12.Redis如何保证操作的原子性

对于Redis而言，命令的原子性指的是：一个操作的不可以再分，操作要么执行，要么不执行。
Redis的操作之所以是原子性的，是因为Redis是单线程的。（Redis新版本已经引入多线程，这里基于旧版本的Redis）
Redis本身提供的所有API都是原子操作，例如可以将get和set改成单命令操作incr。
Redis中的事务其实是要保证批量操作的原子性，或者使用Redis+Lua的方式实现。

13.redis发布与订阅

redis提供了“发布、订阅”模式的消息机制，其中消息订阅者与发布者不直接通信，发布者向指定的频道（channel）发布消息，订阅该频道的每个客户端都可以接收到消息。

    subscribe channel [channel …] 订阅一个或多个频道
    unsubscribe [channel [channel …]] 退订频道，如果没有指定频道，则退订所有的频道
    publish channel message 给指定的频道发消息
    psubscribe pattern [pattern …] 订阅给定模式相匹配的所有频道
    punsubscribe [pattern [pattern …]] 退订给定的模式，如果没有指定模式，则退订所有模式

缺点：
(1)如果一个客户端订阅了频道，但自己读取消息的速度却不够快的话，那么不断积压的消息会使redis输出缓冲区的体积变得越来越大，这可能使得redis本身的速度变慢，甚至直接崩溃。 
(2)这和数据传输可靠性有关，如果在订阅方断线，那么他将会丢失所有在断线期间发布者发布的消息，这个让绝不多数人都很失望吧。
(3)和很多专业的消息队列（kafka rabbitmq）,redis的发布订阅显得很lower, 比如无法实现消息规程和回溯， 但就是简单，如果能满足应用场景，用这个也可以。

14.键的迁移

键迁移大家可能用的不是很多，因为一般都是使用redis主从同步。不过对于我们做数据统计分析使用的时候，可能会使用到，比如用户标签。为了避免key批量删除导致的redis雪崩，一般都是通过一个计算使用的redis和一个最终业务使用的redis，通过将计算时用的redis里的键值通过迁移的方式一个一个的更新到业务redis中，使其对业务冲击最小化。

(1)move：move指令将redis一个库中的数据迁移到另外一个库中。如果key在目标数据库中已存在，那么什么也不会发生。这种模式不建议在生产环境使用，在同一个reids里可以使用

    move key db  //reids有16个库， 编号为0－15
    set name DK;  move name 5 //迁移到第6个库
    elect 5 ;//数据库切换到第6个库，
    get name  可以取到james1
    
(2)dump：DUMP命令用于将key给序列化 ，并返回被序列化的值。用于导入到其他服务中。一般通过dump命令导出，使用restore命令导入。

    //在A服务器上
    set name james;
    dump name;       //  得到"\x00\x05james\b\x001\x82;f\"DhJ"
    //在B服务器上
    restore name 0 "\x00\x05james\b\x001\x82;f\"DhJ"    //0代表没有过期时间
    get name //返回james
    
(3)migrate：migrate用于在Redis实例间进行数据迁移，实际上migrate命令是将dump、restore、del三个命令进行组合，从而简化了操作流程。migrate命令具有原子性，从Redis 3.0.6版本后已经支持迁移多个键的功能。migrate命令的数据传输直接在源Redis和目标Redis上完成，目标Redis完成restore后会发送OK给源Redis。

    //比如把111上的name键值迁移到112上的redis，name为键，0为目标库，1000为超时时间，copy代表迁移后不删除原键，replace不管目标库是否存在该键都迁移成功
    192.168.42.111:6379> migrate 192.168.42.112 6379 name 0 1000 copy replace

15.自定义命令封装

当我们使用jedis或者jdbctemplate时，想执行键迁移的指令的时候，发现根本没有给我们封装相关指令，这个时候我们该怎么办呢？除了框架帮我们封装的方法外，我们自己也可以通过反射的方式进行命令的封装，主要步骤如下：
建立Connection链接，使用Connection连接Redis。
通过反射获取Connection中的sendCommand方法（protected Connection sendCommand(Command cmd, String... args)）。
调用connection的sendCommand方法，第二个参数为执行的命令（比如set,get,client等），第三个参数为命令的参数。可以看到ProtocolCommand这个枚举对象包含了redis的所有指令，即所有的指令都可以通过这个对象获取到。并封装执行。
执行invoke方法，并且按照redis的指令封装参数。
获取Redis的命令执行结果。

    public class RedisKeyMove {
     
        public static void main(String[] args) throws IOException {
            //1.使用Connection连接Redis
            try (Connection connection = new Connection("10.1.253.188", 6379)) {
                // 2. 通过反射获取Connection中的sendCommand方法（protected Connection sendCommand(Command cmd, String... args)）。
                Method method = Connection.class.getDeclaredMethod("sendCommand", Protocol.Command.class, String[].class);
                method.setAccessible(true); // 设置可以访问private和protected方法
                // 3. 调用connection的sendCommand方法，第二个参数为执行的命令（比如set,get,client等），第三个参数为命令的参数。
                // 3.1 该命令最终对应redis中为: set test-key test-value
                method.invoke(connection, Protocol.Command.MIGRATE,
                        new String[] {"10.1.253.69", "6379", "name", "0", "1000", "copy"});
                // 4.获取Redis的命令执行结果
                System.out.println(connection.getBulkReply());
            } catch (NoSuchMethodException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            }
        }
    }

16.安装与使用

下载并安装Redis-x64-3.2.100.msi。安装好后进入C:\Program Files\Redis目录，启动Redis

	redis-server.exe redis.windows.conf

另开一个命令窗口，进行简单数据存取

	//存
	set age 26
	//读
	get age

17.Spring中配置

root-context.xml中Redis的配置：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
					http://www.springframework.org/schema/context
					 http://www.springframework.org/schema/context/spring-context-3.2.xsd
					http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">
		<!-- Root Context: defines shared resources visible to all other web components -->
		
		<!-- 加载Redis配置 -->
	    <context:property-placeholder ignore-unresolvable="true" location="classpath:redis.properties" />

	    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
	        <property name="maxIdle" value="300"/> <!--最大能够保持idel状态的对象数-->
	        <property name="maxTotal" value="60000"/><!--最大分配的对象数-->
	        <property name="testOnBorrow" value="true"/><!--当调用borrow Oject方法时，是否进行有效性检查-->
	    </bean>

	    <bean id="jedisPool" class="redis.clients.jedis.JedisPool">
	        <constructor-arg index="0" ref="jedisPoolConfig"/>
	        <constructor-arg index="1" value="${redis.host}"/>
	        <constructor-arg index="2" value="${redis.port}" type="int"/>
	        <constructor-arg index="3" value="${redis.timeout}" type="int"/>
	    </bean>
	</beans>

其中的redis.properties文件是Redis配置文件：

	redis.host = localhost
	redis.port = 6379
	redis.timeout=30000

# Redis数据结构与操作

1.连接与配置

    // 启动服务端
    ./redis-server ../redis.conf
    // 客户端连接
    ./redis-cli -h 127.0.0.1 -p 6379 -a "password"
    // 连接之后，配置项配置与查看
    config set CONFIG_SETTING_NAME NEW_CONFIG_VALUE
    config get CONFIG_SETTING_NAME
    config get *
  
配置项参考[Redis配置](https://www.runoob.com/redis/redis-conf.html)

2.键

(1)Redis Key是二进制安全的，也就是说，你可以使用任何形式的二进制序列来作为key，比如一个string，或者一个jpg图片的数据，需要说明的是，空字符串也是一个有效的key。
(2)不建议使用过长的key，影响内存占用及数据查性能，对于过长的key，可以通过hash（例如SHA1）处理转换。
(3)建议使用有意义及统一格式的key。
(4)最大允许key大小为512M。

    del key
    // 返回序列号之后的值
    dump key
    // 判断是否存在key，若key存在返回1，否则返回0。
    exists key
    // 设置过期时间，设置成功返回1，否则返回0。TIME_IN_UNIX_TIMESTAMP例如1293840000，TIME_IN_MILLISECONDS_IN_UNIX_TIMESTAMP例如1555555555005，过期时间相关，不管使用哪一个命令，最终底层都是使用pexpireat命令来实现的。
    expire key seconds
    pexpire key milliseconds
    expireat key TIME_IN_UNIX_TIMESTAMP
    pexpireat key TIME_IN_MILLISECONDS_IN_UNIX_TIMESTAMP
    // 返回key的剩余过期时间
    ttl key
    pttl key
    // 查找所有符合给定模式的key，获取所有的key可用使用keys *
    keys pattern
    // 将当前数据库的key移动到给定的数据库db当中。MOVE song 1 将song移动到数据库1
    move key DESTINATION_DATABASE
    // 移除给定key的过期时间，使得key永不过期。
    persist key
    // 当前数据库中随机返回一个key 
    randomkey
    // 修改key的名称，当oldkey newkey相同，或者当oldkey不存在时，返回一个错误。当newkey已经存在时将覆盖旧值。
    rename oldkey newkey
    // 在新的key不存在时修改key的名称
    renamenx oldkey newkey
    // 返回所储存的值的类型
    type key
    // SCAN命令用于迭代数据库中的数据库键。是一个基于游标的迭代器，每次被调用之后，都会向用户返回一个新的游标，用户在下次迭代时需要使用这个新游标作为SCAN命令的游标参数，以此来延续之前的迭代过程。
    // SCAN 返回一个包含两个元素的数组， 第一个元素是用于进行下一次迭代的新游标， 而第二个元素则是一个数组， 这个数组中包含了所有被迭代的元素。如果新游标返回0表示迭代已结束。count是遍历key的数量，注意：不是想匹配到的key数量。
    // 例如：scan 0 match key1111* count 20
    scan cursor [MATCH pattern] [COUNT count]
    
3.string类型

最大512M，是二进制安全的，即可以包含任何数据（比如图片或者序列化的对象）。value不仅可以是字符串，也可以是数字。

应用：
(1)作为原子计数器：incr、decr、incrby。
(2)结合append命令，作为基于时间的增量序列。
(3)随机访问及获取值区域，getrange、setrange。
(4)分布式锁。

    // EX设置过期时间，单位秒，PX设置过期时间，单位毫秒，NX只有键key不存在的时候才会设置key的值，XX只有键key存在的时候才会设置key的值。
    set key value EX 10 PX 10000 NX|XX
    mset key1 value1 key2 value2
    // 指定的key设置值及其过期时间（秒、毫秒）
    setex key TIMEOUT value
    psetex key EXPIRY_IN_MILLISECONDS value
    // 命令在指定的key不存在时，为key设置指定的值
    setnx key value
    msetnx key1 value1 key2 value2
    // 用指定的字符串覆盖给定key所储存的字符串值，覆盖的位置从偏移量offset开始，返回值为被修改后的字符串长度。
    setrange key offset value
    // 为指定的key追加值。如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。如果key不存在，APPEND就简单地将给定key设为value。返回值为新长度。
    append key appendvalue
    // 对key所储存的字符串值，设置或清除指定偏移量上的位，bitvalue为0或1
    setbit key offset bitvalue
    // 设置指定key的值，并返回key的旧值。 key不存在时，返回nil 。当key存在但不是字符串类型时，返回一个错误。
    getset key value   
    // 将key中储存的数字值增一。如果key不存在，那么key的值会先被初始化为0，然后再执行INCR操作。如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。
    incr key
    incrby key increment
    incrbyfloat key float_increment
    decr key
    decrby decrement
    // 获取
    get key
    mget key1 key2 key3
    // 用于获取存储在指定key中字符串的子字符串。字符串的截取范围由start和end两个偏移量决定(包括start和end在内)
    getrange key start end    
    // 对key所储存的字符串值，获取指定偏移量上的位。
    getbit key offset
    // 获取长度
    strlen key

4.hash类型

是一个string类型的field和value的映射表，特别适合用于存储对象。每个hash可以存储2^32 -1键值对（40多亿）。
关于hash类型的内部编码：ziplist（压缩列表） & hashtable（哈希表）。配置：hash-max-ziplist-entries（默认512，field/value对的条目数量为512）、hash-max-ziplist-value（默认64，每个key/value的长度为64字节）。
redis采用何种结构取决于hash中元素数及元素值的大小，当同时满足小于配置时，redis使用ziplist编码存储，否则会转化为hashtable。ziplist编码使用更加紧凑的结构实现多个元素的连续存储，因此占用的内存更小。当数据类型无法满足配置条件，此时使用ziplist编码存储读写效率会下降，所以转换使用hashtable编码存储（O(1)时间复杂度)。

    hset key field value
    hmset key field1 value1 [field2 value2 ] 
    // 为哈希表中不存在的的字段赋值
    hsetnx key field value
    hincrby key field increment
    hincrbyfloat key field increment 
    // 返回哈希表中，所有的字段和值
    hgetall key
    hget key field
    hmget key field1 field2
    hkeys key
    hvals key
    hlen key
    hexist key field
    // 类似于scan，迭代哈希表中的键值对
    hscan cursor [MATCH pattern] [COUNT count]
    hdel key field1 field2

5.list

字符串列表，按照插入顺序排序，列表最多可存储2^32 - 1元素。其实现基于双向链表，即可以支持正向、反向查找和遍历，更方便操作，不过带来了额外的内存开销。

    // 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
    blpop key timeout
    brpop key timeout
    lpop key
    rpop key
    // 从列表中取出最后一个元素，并插入到另外一个列表的头部； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
    brpoplpush list1 anotherlist timeout
    rpoplpush list1 anotherlist
    // 移除列表中与参数value相等的元素。COUNT的值可以是以下几种：
    // count > 0 : 从表头开始向表尾搜索，移除与value相等的元素，数量为COUNT 。count < 0 : 从表尾开始向表头搜索，移除与value相等的元素，数量为COUNT的绝对值。count = 0 : 移除表中所有与value相等的值。
    lrem key count value
    // 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除
    ltrim key start stop
    // 压入
    lpush key value
    rpush key value
    // 将一个值插入到已存在的列表头部，列表不存在时操作无效
    lpushx key value
    rpushx key value
    // 把value插入存于key的列表中在基准值pivot的前面或后面。
    linsert key before|after pivot value
    // 根据索引设置值
    lset key idnex value
    // 返回存储在key的列表里指定范围内的元素。start,end为索引序号。end索引的数据也返回。
    lrange key start end
    // 返回列表里的元素的索引index存储在key里面。 
    lindex key index
    // 获取列表长度
    llen key

6.set

是string类型的无序集合，是去重的。集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。集合中最大的成员数为2^32 - 1。

    sadd key member1 member2
    // 用于移除集合中的一个或多个成员元素，不存在的成员元素会被忽略
    srem key member1 member2
    // 将指定成员member元素从source集合移动到destination集合，是原子性操作。
    // 如果source集合不存在或不包含指定的member元素，则SMOVE命令不执行任何操作，仅返回0 。否则，member元素从source集合中被移除，并添加到destination集合中去。
    // 当destination集合已经包含member元素时，SMOVE命令只是简单地将source集合中的member元素删除。
    smove source destination member
    // 将随机元素从集合中移除并返回
    spop key [count]
    // 仅仅返回随机元素，而不对集合进行任何改动
    srandmember key [count]
    sismember key member
    smembers key
    // 返回集合中元素的数量
    scard key
    // 返回第一个集合与其他集合之间的差异，也可以认为说第一个集合中独有的元素。
    sdiff key otherkey1 otherkey2
    // 将给定集合之间的差集存储在指定的集合destinationkey中
    sdiffstore destinationkey key otherkey1
    // 返回key与所有给定集合otherkey1的交集，交集成员的列表
    sinter key otherkey1
    // 返回给定所有集合的交集并存储在destinationkey中
    sinterstore destinationkey key otherkey1
    // 返回给定集合的并集。
    sunion key1 key2 key3
    // 返回给定集合的并集。
    sunionstore destination key1 key2 key3
    // 迭代集合中键的元素，参考scan
    sscan cursor [MATCH pattern] [COUNT count]

7.zset(sorted set)

zset和set一样也是string类型元素的集合，且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数score。redis正是通过分数来为集合中的成员进行从小到大的排序。
其实现使用HashMap和跳跃表（skipList）来保证数据的存储和有序，HashMap里放的是成员到Score的映射。而跳跃表里存放的是所有的成员，排序依据是HashMap里存的Score，使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。

    zadd key score member
    zincrby key increment member
    zrem key member
    zscore key member
    // 默认是闭区间，即min <= score <= max，开区间可以zrangebyscore key (min (max，正负无穷为+inf，-inf，withscores表示显示整个有序集及成员的score值
    zrangebyscore key min max [withscores]

8.bitmap：更细化的一种操作，以bit为单位。

9.hyperloglog

Redis在2.8.9版本添加了 HyperLogLog 结构。Redis HyperLogLog是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。
在Redis里面，每个 HyperLogLog 键只需要花费12KB 内存，就可以计算接近2^64个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。
但是，因为HyperLogLog只会根据输入元素来计算基数，而不会储存输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素。
什么是基数?比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。

10.Redis字符串的实现

Redis虽然是用C语言写的，但却没有直接用C语言的字符串，而是自己实现了一套字符串。目的就是为了提升速度，提升性能，可以看出Redis为了高性能也是煞费苦心。
Redis构建了一个叫做简单动态字符串（Simple Dynamic String），简称SDS。Redis的字符串也会遵守C语言的字符串的实现规则，即最后一个字符为空字符。然而这个空字符不会被计算在len里头。
动态变化长度：计算出大小是否足够，开辟空间至满足所需大小，空间预分配，当修改后的字符串长度len < 1M，则会分配与len相同长度的未使用的空间(free)，当修改后的字符串长度len >= 1M，则会分配1M长度的未使用的空间。
Redis字符串的性能优势：快速获取字符串长度，空间预分配避免缓冲区溢出，降低空间分配次数提升内存使用效率。惰性删除机制，字符串缩减后空间不释放，作为预分配空间保留。

    struct sdshdr{
        //  记录已使用长度
        int len;
        // 记录空闲未使用的长度
        int free;
        // 字符数组
        char[] buf;
    };

11.Redis内部存储数据结构

    typedef struct redisObject {
        unsigned type:4;
        unsigned encoding:4;
        unsigned lru:LRU_BITS; /* lru time (relative to server.lruclock) */
        int refcount;
        void *ptr;
    } robj;

(1)type：数据类型、例如sting、hash、list、set、zset等，值类型查看命令"type"。
(2)encoding：是不同数据类型在Redis内部的存储方式。可以通过object encoding key指令查看值对象的编码

    #define OBJ_ENCODING_RAW 0     /* Raw representation -- 简单动态字符串sds*/
    #define OBJ_ENCODING_INT 1     /* Encoded as integer -- long类型的整数*/
    #define OBJ_ENCODING_HT 2      /* Encoded as hash table -- 字典dict*/
    #define OBJ_ENCODING_ZIPMAP 3  /* Encoded as zipmap -- 3.2.5版本后不再使用 */
    #define OBJ_ENCODING_LINKEDLIST 4 /* Encoded as regular linked list -- 双向链表*/
    #define OBJ_ENCODING_ZIPLIST 5 /* Encoded as ziplist -- 压缩列表*/
    #define OBJ_ENCODING_INTSET 6  /* Encoded as intset -- 整数集合*/
    #define OBJ_ENCODING_SKIPLIST 7  /* Encoded as skiplist -- 跳表*/
    #define OBJ_ENCODING_EMBSTR 8  /* Embedded sds string encoding -- embstr编码的sds*/
    #define OBJ_ENCODING_QUICKLIST 9 /* Encoded as linked list of ziplists -- 由双端链表和压缩列表构成的高速列表*/
    
同一种数据类型，可以有多种不同内部编码存储形式。具体redis采用那种编码形式与实际应用的数据值类型相关。数据的编码类型在数据写入的时候确定，不可变换，且只能向大内存编码形式转换。

    string:raw/int/embstr
    hash:ziplist/hashtable
    list:linkedlist/ziplist/quicklist
    set:hashtable/intset
    zset:ziplist/skiplist

(3)lru：最后一次被访问时间，辅助回收，可以通过object idletime {key}在不更新lru属性情况下查看key的空闲时间。
(4)refcount：当前对象被引用次数，辅助回收，可以通过object refcount {key}查看引用数，当对象为整数且值在范围在0-9999时，redis可以通过共享对象的方式来节省内存。目前共享对象池只对整数设置了0~9999个共享对象，一方面整数对象池复用率最大，同时等值判断上时间复杂度为O(1)。
(5)*ptr：数据存储或指向，数据本身或者指向数据的指针，redis3.0之后，长度在39以内的字符串数据，内部编码为embstr，内存创建时，字符串和redisObject一起分配，减少一次内存分配。

12.ziplist

hash、list、zset底层都有应用这种存储结构。

基本特点：
连续性内存存储。
可以模拟双向链表，O(1)时间复杂度内出入队操作。
读写性能跟数据的元素个数及值长度相关，适合存储小对象和长度有限的数据。
数据增删涉及复杂的内存操作。

ziplist基本结构：

    zlbytes：int32类型、4字节，ziplist整体字节长度。
    zltail：int32类型、4字节，记录距离尾节点偏移量，用于尾节点弹出。
    zllen：int16类型，2字节，ziplist节点数量。
    entry：数据节点，长度不定。entry 即链表node，内部结构包含以下。
        prev_entry_bytes_length：前一个节点所占用的空间，用户快速定位。
        encoding：当前数据节点编码类型及长度，前两位标识编码类型，其余标识数据长度。
        contents：节点数据值。
    zlend：1字节，记录列表结尾。
    
13.skiplist

是有序集合的底层实现之一。跳跃表是基于多指针有序链表实现的，可以看成多个有序链表。在查找时，从上层指针开始查找，找到对应的区间之后再到下一层去查找。
与红黑树等平衡树相比，跳跃表具有以下优点：插入速度非常快速，因为不需要进行旋转等操作来维护平衡性；更容易实现；支持无锁操作。

# Redis持久化

1.RDB

快照形式是直接把内存中的数据保存到磁盘的一个二进制文件dump.rdb中，定时保存。Redis默认是RDB的持久化方式。通过配置文件中的save参数来定义快照的周期。
Redis会fork一个子进程，子进程将数据写到磁盘上一个临时RDB文件中。当子进程完成写临时文件后，将原来的RDB替换掉。
可以将快照复制到其它服务器从而创建具有相同数据的服务器副本。如果系统发生故障，将会丢失最后一次创建快照之后的数据。如果数据量很大，保存快照的时间会很长。
数据库备份和灾难恢复：定时生成RDB快照非常便于进行数据库备份，并且RDB恢复数据集的速度也要比AOF恢复的速度快。

2.AOF

将写命令添加到AOF文件appendonly.aof的末尾，是命令的集合。当Redis重启的时候，它会优先使用AOF文件来还原数据集，因为AOF文件保存的数据集通常比RDB文件所保存的数据集更完整。
AOF能够恢复的是全量数据，非增量数据！
Redis支持同时开启RDB和AOF！
缺点是对于相同的数据集来说，AOF的文件体积通常要大于RDB文件的体积。随着服务器写请求的增多，AOF文件会越来越大。Redis提供了一种将AOF重写的特性，能够去除AOF文件中的冗余写命令。

开启AOF

    appendonly yes

使用AOF持久化需要设置同步选项，从而确保写命令什么时候会同步到磁盘文件上。这是因为对文件进行写入并不会马上将内容同步到磁盘上，而是先存储到缓冲区，然后由操作系统决定什么时候同步到磁盘。

    appendfsync always	每个写命令都同步，会严重减低服务器的性能；
    appendfsync everysec	每秒同步一次，选项比较合适，可以保证系统崩溃时只会丢失一秒左右的数据，并且Redis每秒执行一次同步对服务器性能几乎没有任何影响。该策略为AOF的缺省策略。
    appendfsync no	让操作系统来决定何时同步，选项并不能给服务器性能带来多大的提升，而且也会增加系统崩溃时数据丢失的数量。

N.参考

(1)[Redis持久化 - RDB和AOF](https://segmentfault.com/a/1190000016021217)

# Redis内存管理

1.定期删除策略

定期删除策略是定时删除策略和惰性删除策略的一种整合折中方案。定期删除策略每隔一段时间执行一次删除过期键操作，并通过限制删除操作执行的时长和频率来减少删除操作对CPU时间的影响，同时，通过定期删除过期键，也有效地减少了因为过期键而带来的内存浪费。
过期键的定期删除策略由activeExpireCycle函数实现，每当Redis服务器的周期性操作serverCron函数执行时，activeExpireCycle函数就会被调用，它在规定的时间内，分多次遍历服务器中的各个数据库，从数据库的expires字典中随机检查一部分键的过期时间，并删除其中的过期键。函数每次运行时，都从一定数量的数据库中随机取出一定数量的键进行检查，并删除其中的过期键，比如先从0号数据库开始检查，下次函数运行时，可能就是从1号数据库开始检查，直到15号数据库检查完毕，又重新从0号数据库开始检查，这样可以保证每个数据库都被检查到。
Redis会将每个设置了过期时间的key放入到一个独立的字典中，默认每100ms进行一次过期扫描：随机抽取20个key，删除这20个key中过期的key，如果过期的key比例超过1/4，就重复步骤，继续删除。
为什不扫描所有的key？Redis是单线程，全部扫描岂不是卡死了。而且为了防止每次扫描过期的key比例都超过1/4，导致不停循环卡死线程，Redis为每次扫描添加了上限时间，默认是25ms。
如果客户端将超时时间设置的比较短，比如10ms，那么就会出现大量的链接因为超时而关闭，业务端就会出现很多异常。而且这时你还无法从Redis的slowlog中看到慢查询记录，因为慢查询指的是逻辑处理过程慢，不包含等待时间。
如果在同一时间出现大面积key过期，Redis循环多次扫描过期词典，直到过期的key比例小于1/4，这会导致卡顿，而且在高并发的情况下，可能会导致缓存雪崩。
为什么Redis为每次扫描添的上限时间是25ms，还会出现上面的情况？因为Redis是单线程，每个请求处理都需要排队，而且由于Redis每次扫描都是25ms，也就是每个请求最多25ms，100个请求就是2500ms。如果有大批量的key过期，要给过期时间设置一个随机范围，而不宜全部在同一时间过期，分散过期处理的压力。

2.懒惰删除策略

Redis采用的是定期删除 + 懒惰删除策略。

懒惰删除两方面的概念：一、读写时检查。二、异步删除特性。

过期键的惰性删除策略由expireIfNeeded函数实现，所有读写数据库的Redis命令在执行之前都会调用expireIfNeeded函数对输入键进行检查。如果输入键已经过期，那么将输入键从数据库中删除。如果输入键未过期，那么不做任何处理。

删除指令del会直接释放对象的内存，大部分情况下，这个指令非常快，没有明显延迟。不过如果删除的key是一个非常大的对象，比如一个包含了千万元素的hash，又或者在使用FLUSHDB和FLUSHALL删除包含大量键的数据库时，那么删除操作就会导致单线程卡顿。
redis 4.0引入了lazyfree的机制，它可以将删除键或数据库的操作放在后台线程里执行，从而尽可能地避免服务器阻塞。
异步队列：主线程将对象的引用从“大树”中摘除后，会将这个key的内存回收操作包装成一个任务，塞进异步任务队列，后台线程会从这个异步队列中取任务。任务队列被主线程和异步线程同时操作，所以必须是一个线程安全的队列。不是所有的unlink操作都会延后处理，如果对应key所占用的内存很小，延后处理就没有必要了，这时候Redis会将对应的key内存立即回收，跟del指令一样。


    // unlink 指令，它能对删除操作进行懒处理，丢给后台线程来异步回收内存。
    unlink key
    // flushdb 和 flushall 指令，用来清空数据库，这也是极其缓慢的操作。Redis 4.0 同样给这两个指令也带来了异步化，在指令后面增加 async 参数就可以将整棵大树连根拔起，扔给后台线程慢慢焚烧。
    flushall async
    
Redis回收内存除了del指令和flush之外，还会存在于在key的过期、LRU 淘汰、rename指令以及从库全量同步时接受完rdb文件后会立即进行的flush操作。Redis4.0为这些删除点也带来了异步删除机制，打开这些点需要额外的配置选项。如下

    slave-lazy-flush 从库接受完rdb文件后的flush操作
    lazyfree-lazy-eviction 内存达到maxmemory时进行淘汰
    lazyfree-lazy-expire key过期删除
    lazyfree-lazy-server-del 针对有些指令在处理已存在的键时，会带有一个隐式的DEL键的操作。如rename命令，当目标键已存在,redis会先删除目标键，如果这些目标键是一个big key,那就会引入阻塞删除的性能问题。

3.主从复制对过期键的处理

从库不会进行过期扫描，从库对过期的处理是被动的。主库在key到期时，会在AOF文件里增加一条del指令，同步到所有的从库，从库通过执行这条del指令来删除过期的key。因为指令同步是异步进行的，所以主库过期的key的del指令没有及时同步到从库的话，会出现主从数据的不一致，主库没有的数据在从库里还存在。
在主从复制模式下，从服务器的过期键删除动作由主服务器控制。主服务器在删除一个过期键后，会显式地向所有从服务器发送一个DEL命令，告知从服务器删除这个过期键。
从服务器在执行客户端发送的读命令时，即使发现该键已过期也不会删除该键，照常返回该键的值。从服务器只有接收到主服务器发送的DEL命令后，才会删除过期键。

4.内存淘汰机制

Redis的内存占用会越来越高。Redis为了限制最大使用内存，提供了redis.conf中的配置参数maxmemory设置占用内存大小，如果不设置最大内存大小或者设置最大内存大小为0，在64位操作系统下不限制内存大小，在32位操作系统下最多使用3GB内存。

    //配置文件redis.conf，设置Redis最大占用内存大小为100M
    maxmemory 100mb
    // 通过命令配置，设置Redis最大占用内存大小为100M
    127.0.0.1:6379> config set maxmemory 100mb
    //获取设置的Redis能使用的最大内存大小
    127.0.0.1:6379> config get maxmemory
    
当内存超出maxmemory，Redis提供了几种内存淘汰机制让用户选择，配置maxmemory-policy。当使用volatile-lru、volatile-random、volatile-ttl这三种策略时，如果没有key可以被淘汰，则和noeviction一样返回错误

    // 配置文件
    maxmemory-policy allkeys-lru
    // 获取当前内存淘汰策略
    127.0.0.1:6379> config get maxmemory-policy
    // 通过命令修改淘汰策略
    127.0.0.1:6379> config set maxmemory-policy allkeys-lru

LRU(Least Recently Used)，即最近最少使用，是一种缓存置换算法。在使用内存作为缓存的时候，缓存的大小一般是固定的。当缓存被占满，这个时候继续往缓存里面添加数据，就需要淘汰一部分老的数据，释放内存空间用来存储新的数据。
这个时候就可以使用LRU算法了。其核心思想是：如果一个数据在最近一段时间没有被用到，那么将来被使用到的可能性也很小，所以就可以被淘汰掉。

    noeviction：当内存超出maxmemory，写入请求会报错，但是删除和读请求可以继续。默认策略。
    allkeys-lru：当内存超出maxmemory，在所有的key中，移除最少使用的key。只把Redis既当缓存是使用这种策略。（推荐）。
    allkeys-random：当内存超出maxmemory，在所有的key中，随机移除某个key。（应该没人用吧）
    volatile-lru：当内存超出maxmemory，在设置了过期时间key的字典中，移除最少使用的key。把Redis既当缓存，又做持久化的时候使用这种策略。
    volatile-random：当内存超出maxmemory，在设置了过期时间key的字典中，随机移除某个key。
    volatile-ttl：当内存超出maxmemory，在设置了过期时间key的字典中，根据key的过期时间进行淘汰，越早过期的越优先被淘汰。
    
传统的LRU算法存在2个问题：需要额外的空间进行存储。可能存在某些key值使用很频繁，但是最近没被使用，从而被LRU算法删除。
Redis使用的是近似LRU算法，它跟常规的LRU算法还不太一样。近似LRU算法通过随机采样法淘汰数据，每次随机出5（默认）个key，从里面淘汰掉最近最少使用的key。可以通过maxmemory-samples参数修改采样数量。maxmenory-samples配置的越大，淘汰的结果越接近于严格的LRU算法。当抽样数达到10个，已经和传统的LRU算法非常接近了。

    maxmemory-samples 10
    
Redis使用的并不是完全LRU算法。不使用LRU算法，是为了节省内存，Redis采用的是随机LRU算法，给每个key增加了一个额外增加了一个24bit的字段(unsigned lru:LRU_BITS;//记录对象最后一次被应用程序访问的时间（24位=3字节）)，用来存储该key最后一次被访问的时间。
注意Redis的LRU淘汰策略是懒惰处理，也就是不会主动执行淘汰策略，当Redis执行写操作时，发现内存超出maxmemory，就会执行LRU淘汰算法。这个算法就是随机采样出5(默认值)个key，然后移除最旧的key，如果移除后内存还是超出maxmemory，那就继续随机采样淘汰，直到内存低于maxmemory为止。
lru属性是创建对象的时候写入，对象被访问到时也会进行更新。正常人的思路就是最后决定要不要删除某一个键肯定是用当前时间戳减去lru，差值最大的就优先被删除。但是Redis里面并不是这么做的，Redis中维护了一个全局属性lru_clock，这个属性是通过一个全局函数serverCron每隔100毫秒执行一次来更新的，记录的是当前unix时间戳。
最后决定删除的数据是通过lru_clock减去对象的lru属性而得出的。那么为什么Redis要这么做呢？直接取全局时间不是更准确吗？这是因为这么做可以避免每次更新对象的lru属性的时候可以直接取全局属性，而不需要去调用系统函数来获取系统时间，从而提升效率。
redisObject对象中的lru属性只有24位，24位只能存储194天的时间戳大小，一旦超过194天之后就会重新从0开始计算，所以这时候就可能会出现redisObject对象中的lru属性大于全局的lru_clock属性的情况。
正因为如此，所以计算的时候也需要分为2种情况：当全局lruclock > lru，则使用lruclock - lru得到空闲时间。当全局lruclock < lru，则使用lruclock_max（即 194 天） - lru + lruclock 得到空闲时间。需要注意的是，这种计算方式并不能保证抽样的数据中一定能删除空闲时间最长的。这是因为首先超过194天还不被使用的情况很少，再次只有lruclock第2轮继续超过lru属性时，计算才会出问题。
    
LFU算法是Redis4.0里面新加的一种淘汰策略。它的全称是Least Frequently Used，它的核心思想是根据key的最近被访问的频率进行淘汰，很少被访问的优先被淘汰，被访问的多的则被留下来。
LFU算法能更好的表示一个key被访问的热度。假如你使用的是LRU算法，一个key很久没有被访问到，只刚刚是偶尔被访问了一次，那么它就被认为是热点数据，不会被淘汰，而有些key将来是很有可能被访问到的则被淘汰了。如果使用LFU算法则不会出现这种情况，因为使用一次并不会使一个key成为热点数据。
LFU一共有两种策略：

    volatile-lfu：在设置了过期时间的key中使用LFU算法淘汰key
    allkeys-lfu：在所有的key中使用LFU算法淘汰数据
    
当我们采用LFU回收策略时，lru属性的高16位用来记录访问时间（last decrement time：ldt，单位为分钟），低8位用来记录访问频率（logistic counter：logc），简称counter。
LFU计数器每个键只有8位，它能表示的最大值是255，所以Redis使用的是一种基于概率的对数器来实现counter的递增。
给定一个旧的访问频次，当一个键被访问时，counter按以下方式递增：
提取0和1之间的随机数R。counter - 初始值（默认为 5），得到一个基础差值，如果这个差值小于0，则直接取0，为了方便计算，把这个差值记为baseval。概率P计算公式为：1/(baseval * lfu_log_factor + 1)。如果R < P 时，频次进行递增（counter++）。公式中的 lfu_log_factor 称之为对数因子，默认是10 ，可以通过参数来进行控制：lfu_log_factor 10
当对数因子lfu_log_factor为100时，大概是10M（1000万）次访问才会将访问counter增长到255，而默认的10也能支持到1M（100万）次访问counter才能达到255上限，这在大部分场景都是足够满足需求的。
如果访问频次counter只是一直在递增，那么迟早会全部都到255，也就是说counter一直递增不能完全反应一个key的热度的，所以当某一个key一段时间不被访问之后，counter也需要对应减少。counter的减少速度由参数lfu-decay-time进行控制，默认是1，单位是分钟。默认值1表示：N分钟内没有访问，counter就要减N。lfu-decay-time 1。取出当前的时间戳和对象中的 lru 属性进行对比，计算出当前多久没有被访问到，比如计算得到的结果是100分钟没有被访问，然后再去除配置参数lfu_decay_time，如果这个配置默认为1也即是 100/1=100，代表100分钟没访问，所以counter就减少100。

5.持久化对过期键的处理

(1)RDB方式

生成RDB文件：在执行SAVE命令或者BGSAVE命令创建一个新的RDB文件时，程序会对数据库中的键进行检查，已过期的键不会被保存到新创建的RDB文件中。举个例子，如果数据库中包含3个键k1、k2、k3，并且k2已经过期，那么创建新的RDB文件时，程序只会将k1和k3保存到RDB文件中，k2则会被忽略。
载入RDB文件：在启动Redis服务器时，如果服务器只开启了RDB持久化，那么服务器将会载入RDB文件：如果服务器以主服务器模式运行，在载入RDB文件时，程序会对文件中保存的键进行检查，未过期的键会被载入到数据库中，过期键会被忽略。如果服务器以从服务器模式运行，在载入RDB文件时，文件中保存的所有键，不论是否过期，都会被载入到数据库中。因为主从服务器在进行数据同步（完整重同步）的时候，从服务器的数据库会被清空，所以一般情况下，过期键对载入RDB文件的从服务器不会造成影响。

(2)AOF方式

AOF文件写入：如果数据库中的某个键已经过期，并且服务器开启了AOF持久化功能，当过期键被惰性删除或者定期删除后，程序会向AOF文件追加一条DEL命令，显式记录该键已被删除。举个例子，如果客户端执行命令GET message访问已经过期的message键，那么服务器将执行以下3个动作：从数据库中删除message键，追加一条DEL message命令到AOF文件，向执行GET message命令的客户端返回空回复。
AOF文件重写：在执行AOF文件重写时，程序会对数据库中的键进行检查，已过期的键不会被保存到重写后的AOF文件中。

6.内存分析

在理想情况下， used_memory_rss的值应该只比used_memory稍微高一点儿。当rss > used ，且两者的值相差较大时，表示存在（内部或外部的）内存碎片。内存碎片的比率可以通过 mem_fragmentation_ratio 的值看出。
当used > rss 时，表示 Redis 的部分内存被操作系统换出到交换空间了，redis使用硬盘作为内存，因为硬盘的速度，redis性能会受到极大的影响，操作可能会产生明显的延迟。
当Redis释放内存时，分配器可能会，也可能不会，将内存返还给操作系统。如果Redis释放了内存，却没有将内存返还给操作系统，那么used_memory的值可能和操作系统显示的Redis内存占用并不一致。查看used_memory_peak的值可以验证这种情况是否发生。

    >>info memory

    used_memory : 由 Redis 分配器分配的内存总量，以字节（byte）为单位
    used_memory_human : 以人类可读的格式返回 Redis 分配的内存总量
    used_memory_rss : 从操作系统的角度，返回 Redis 已分配的内存总量（俗称常驻集大小）。这个值和 top 、 ps 等命令的输出一致。
    used_memory_peak : Redis 的内存消耗峰值（以字节为单位）
    used_memory_peak_human : 以人类可读的格式返回 Redis 的内存消耗峰值
    used_memory_lua : Lua 引擎所使用的内存大小（以字节为单位）
    mem_fragmentation_ratio : used_memory_rss 和 used_memory 之间的比率
    mem_allocator : 在编译时指定的， Redis所使用的内存分配器。可以是libc、jemalloc或者tcmalloc。
    
7.redis内存消耗

(1)自身内存
redis空进程自身消耗非常的少，可以忽略不计，优化内存可以不考虑此处的因素。

(2)键值对象内存
对象内存，也即真实存储的数据所占用的内存。redis k-v结构存储，对象占用可以简单的理解为k-size + v-size。
redis的键统一都为字符串类型，值包含多种类型：string、list、hash、set、zset五种基本类型及基于string的Bitmaps和HyperLogLog类型等。
在实际的应用中，一定要做好kv的构建形式及内存使用预期。

(3)缓冲区内存：
(a)客户端缓存
接入redis服务器的TCP连接输入输出缓冲内存占用，TCP输入缓冲占用是不受控制的，最大允许空间为1G。输出缓冲占用可以通过client-output-buffer-limit参数配置。
redis客户端主要分为从客户端、订阅客户端和普通客户端。
从客户端连接占用：也就是我们所说的slave，主节点会为每一个从节点建立一条连接用于命令复制，缓冲配置为：client-output-buffer-limit slave 256mb 64mb 60。主从之间的间络延迟及挂载的从节点数量是影响内存占用的主要因素。因此在涉及需要异地部署主从时要特别注意，另外，也要避免主节点上挂载过多的从节点（<=2）；
订阅客户端内存占用：发布订阅功能连接客户端使用单独的缓冲区，默认配置：client-output-buffer-limit pubsub 32mb 8mb 60。当消费慢于生产时会造成缓冲区积压，因此需要特别注意消费者角色配比及生产、消费速度的监控。
普通客户端内存占用：除了上述之外的其它客户端，如我们通常的应用连接，默认配置：client-output-buffer-limit normal 1000。可以看到，普通客户端没有配置缓冲区限制，通常一般的客户端内存消耗也可以忽略不计。但是当redis服务器响应较慢时，容易造成大量的慢连接，主要表现为连接数的突增，如果不能及时处理，此时会严重影响redis服务节点的服务及恢复。
关于此，在实际应用中需要注意几点：maxclients最大连接数配置必不可少。合理预估单次操作数据量（写或读）及网络时延ttl。禁止线上大吞吐量命令操作，如keys等。高并发应用情景下，redis内存使用需要有实时的监控预警机制。

(b)复制积压缓冲区
v2.8之后提供的一个可重用的固定大小缓冲区，用以实现向从节点的部分复制功能，避免全量复制。配置单数：repl-backlog-size，默认1M。单个主节点配置一个复制积压缓冲区。

(c)AOF缓冲区
AOF重写期间增量的写入命令保存，此部分缓存占用大小取决于AOF重写时间及增量。

(4)内存碎片：固定范围内存块儿分配。
redis默认使用jemalloc内存分配器，其它包括glibc、tcmalloc。内存分配器会首先将可管理的内存分配为规定不同大小的内存块以备不同的数据存储需求，但是，我们知道实际应用中需要存储的数据大小不一，规范不一，内存分配器只能选择最接近数据需求大小的内存块儿进行分配，这样就伴随着“占不满”空间的碎片浪费。jemalloc针对内存碎片有相应的优化策略，正常碎片率为mem_fragmentation_ratio在1.03左右。
另外，对string值的频繁append及range操作会会导致内存碎片问题，SDS惰性内存回收也会导致内存碎片，同时过期键内存回收也伴随着所释放空间的无法充分利用，导致内存碎片率上升的问题。
碎片处理：应用层面，尽量避免差异化的键值使用，做好数据对齐。redis服务层面，可以通过重启服务，进行碎片整理。

8.redis子进程内存消耗

子进程即redis执行持久化（RDB/AOF）时fork的子任务进程。
  
(1)关于linux系统的写时复制机制：父子进程会共享相同的物理内存页，父进程处理写请求时会对需要修改的页复制一份副本进行修改，子进程读取的内存则为fork时的父进程内存快照，因此，子进程的内存消耗由期间的写操作增量决定。
(2)关于linux的透明大页机制THP（Transparent Huge Page）：THP机制会降低fork子进程的速度；写时复制内存页由4KB增大至2M。高并发情境下，写时复制内存占用消耗影响会很大，因此需要选择性关闭。
(3)关于linux配置：一般需要配置linux系统 vm.overcommit_memory=1，以允许系统可以分配所有的物理内存。防止fork任务因内存而失败。

# Redis分布式锁

1.常规实现

满足以下条件：
(a)互斥性：在任意时刻，只有一个客户端能持有锁。
(b)不能死锁：客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
(c)容错性：只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。

实现方法：
获取锁。set命令要用set key value px milliseconds nx，替代setnx + expire需要分两次执行命令的方式，保证了原子性。
value要具有唯一性，可以使用UUID.randomUUID().toString()方法生成，用来标识这把锁是属于哪个请求加的，在解锁的时候就可以有依据。

    SET resource_name unique_value NX PX 30000

释放锁。通过Lua脚本实现解锁（利用了eval命令执行Lua脚本的原子性），Lua脚本中，一定要比较value，防止误解锁

    if redis.call("get",KEYS[1]) == ARGV[1] then
        return redis.call("del",KEYS[1])
    else
        return 0
    end
    
存在的风险：
如果存储锁对应key的那个节点挂了的话，就可能存在丢失锁的风险，导致出现多个客户端持有锁的情况，这样就不能实现资源的独享了。
客户端A从master获取到锁。在master将锁同步到slave之前，master宕掉了（Redis的主从同步通常是异步的）。
主从切换，slave节点被晋级为master节点。客户端B取得了同一个资源被客户端A已经获取到的另外一个锁。导致存在同一时刻存不止一个线程获取到锁的情况。

Java实现：

    @Override
    public boolean lock(String key, String uuid) {
 
        Jedis jedis = null;
        long start = System.currentTimeMillis();
        try {
            jedis = jedisPool.getResource();
            while (true) {
                //使用setnx是为了保持原子性
                String result = jedis.set(key, uuid, "NX", "PX", expireTime);
 
                //OK标示获得锁，null标示其他任务已经持有锁
                if (LOCK_SUCCESS.equals(result)) {
                    return true;
                }
                //在timeout时间内仍未获取到锁，则获取失败
                long time = System.currentTimeMillis() - start;
                if (time >= timeout) {
                    return false;
                }
                //增加睡眠时间可能导致结果分散不均匀，测试时可以不用睡眠
                Thread.sleep(1);
            }
        }catch (InterruptedException e1) {
            log.error("redis竞争锁，线程sleep异常");
            return false;
        } catch (Exception e) {
            log.error("redis竞争锁失败");
            throw e;
        } finally {
            if (jedis != null) {
                jedis.close();
            }
        }
    }
 
    @Override
    public boolean unlock(String key, String uuid) {
        Jedis jedis = null;
        try {
            jedis = jedisPool.getResource();
            //lua脚本，使用lua脚本是为了保持原子性
            String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
            Object result = jedis.eval(script, Collections.singletonList(key), Collections.singletonList(uuid));
 
            if (RELEASE_SUCCESS.equals(result)) {
                return true;
            }
            return false;
        } catch (Exception e) {
            log.error("redis解锁失败");
            throw e;
        } finally {
            if (jedis != null) {
                jedis.close();
            }
        }
    }

2.Redisson实现分布式锁

Redisson是一个在Redis的基础上实现的Java驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的Java常用对象，还实现了可重入锁（Reentrant Lock）、公平锁（Fair Lock、联锁（MultiLock）、 红锁（RedLock）、 读写锁（ReadWriteLock）等，还提供了许多分布式服务。
Redisson提供了使用Redis的最简单和最便捷的方法。Redisson的宗旨是促进使用者对Redis的关注分离（Separation of Concern），从而让使用者能够将精力更集中地放在处理业务逻辑上。
Redisson所有指令都通过lua脚本执行，redis支持lua脚本原子性执行。

Redisson分布式重入锁用法，以单点模式为例：

    // 1.构造redisson实现分布式锁必要的Config
    Config config = new Config();
    config.useSingleServer().setAddress("redis://127.0.0.1:5379").setPassword("123456").setDatabase(0);
    // 2.构造RedissonClient
    RedissonClient redissonClient = Redisson.create(config);
    // 3.获取锁对象实例（无法保证是按线程的顺序获取到）
    RLock rLock = redissonClient.getLock(lockKey);
    try {
        /**
         * 4.尝试获取锁
         * waitTimeout 尝试获取锁的最大等待时间，超过这个值，则认为获取锁失败
         * leaseTime   锁的持有时间,超过这个时间锁会自动失效（值应设置为大于业务处理的时间，确保在锁有效期内业务能处理完）
         */
        boolean res = rLock.tryLock((long)waitTimeout, (long)leaseTime, TimeUnit.SECONDS);
        if (res) {
            //成功获得锁，在这里处理业务
        }
    } catch (Exception e) {
        throw new RuntimeException("aquire lock fail");
    }finally{
        //无论如何, 最后都要解锁
        rLock.unlock();
    }
    
我们可以看到，RedissonLock是可重入的，并且考虑了失败重试，可以设置锁的最大等待时间， 在实现上也做了一些优化，减少了无效的锁申请，提升了资源的利用率。
需要特别注意的是，RedissonLock同样没有解决节点挂掉的时候，存在丢失锁的风险的问题。而现实情况是有一些场景无法容忍的，所以Redisson提供了实现了redlock算法的RedissonRedLock，RedissonRedLock真正解决了单点失败的问题，代价是需要额外的为RedissonRedLock搭建Redis环境。
所以，如果业务场景可以容忍这种小概率的错误，则推荐使用RedissonLock， 如果无法容忍，则推荐使用RedissonRedLock。

Redission设置一个key的默认过期时间为30s,如果某个客户端持有一个锁超过了30s怎么办？
Redission中有一个watchdog的概念，翻译过来就是看门狗，它会在你获取锁之后，每隔10秒帮你把key的超时时间设为30s。这样的话，就算一直持有锁也不会出现key过期了，其他线程获取到锁的问题了。redisson的“看门狗”逻辑保证了没有死锁发生。如果机器宕机了，看门狗也就没了。此时就不会延长key的过期时间，到了30s之后就会自动过期了，其他线程可以获取到锁。

3.redlock算法

防止锁丢失出现的算法。
这个场景是假设有一个redis cluster，有5个redis master实例。然后执行如下步骤获取一把锁：
获取当前时间戳，单位是毫秒；
跟上面类似，轮流尝试在每个master节点上创建锁，过期时间较短，一般就几十毫秒；
尝试在大多数节点上建立一个锁，比如5个节点就要求是3个节点 n / 2 + 1；
客户端计算建立好锁的时间，如果建立锁的时间小于超时时间，就算建立成功了；
要是锁建立失败了，那么就依次之前建立过的锁删除；
只要别人建立了一把分布式锁，你就得不断轮询去尝试获取锁。

4.解决锁超时问题

当锁已超时而业务逻辑还未执行完的问题，这样会导致:A线程超时时间设为10s(为了解决死锁问题)，但代码执行时间可能需要30s，然后redis服务端10s后将锁删除，此时B线程恰好申请锁，redis服务端不存在该锁，可以申请也执行了代码，那么问题来了，A、B线程都同时获取到锁并执行业务逻辑，这与分布式锁最基本的性质相违背：在任意一个时刻，只有一个客户端持有锁，即独享。
调用lock同时, 立即开启PostponeTask线程, 线程等待超时时间的2/3时间后, 开始执行锁延时代码, 如果延时成功, add_information_lock这个key会一直存在于redis服务端, 直到业务逻辑执行完毕, 因此在此过程中, 其他线程无法获取到锁, 也即保证了线程安全性

    @Component
    public class DistributedLock {
    
        @Autowired
        private RedisTemplate<Serializable, Object> redisTemplate;
    
        private static final Long RELEASE_SUCCESS = 1L;
        private static final Long POSTPONE_SUCCESS = 1L;
    
        private static final String LOCK_SUCCESS = "OK";
        private static final String SET_IF_NOT_EXIST = "NX";
        private static final String SET_WITH_EXPIRE_TIME = "EX";
        // 解锁脚本(lua)
        private static final String RELEASE_LOCK_SCRIPT = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        // 延时脚本
        private static final String POSTPONE_LOCK_SCRIPT = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('expire', KEYS[1], ARGV[2]) else return '0' end";
    
        /**
         * 分布式锁
         * @param key
         * @param value
         * @param expireTime 单位: 秒
         * @return
         */
        public boolean lock(String key, String value, long expireTime) {
            // 加锁
            Boolean locked = redisTemplate.execute((RedisCallback<Boolean>) redisConnection -> {
                Jedis jedis = (Jedis) redisConnection.getNativeConnection();
                String result = jedis.set(key, value, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
                if (LOCK_SUCCESS.equals(result)) {
                    return Boolean.TRUE;
                }
                return Boolean.FALSE;
            });
    
            if (locked) {
                // 加锁成功, 启动一个延时线程, 防止业务逻辑未执行完毕就因锁超时而使锁释放
                PostponeTask postponeTask = new PostponeTask(key, value, expireTime, this);
                Thread thread = new Thread(postponeTask);
                thread.setDaemon(Boolean.TRUE);
                thread.start();
            }
    
            return locked;
        }
    
        /**
         * 解锁
         * @param key
         * @param value
         * @return
         */
        public Boolean unLock(String key, String value) {
            return redisTemplate.execute((RedisCallback<Boolean>) redisConnection -> {
                Jedis jedis = (Jedis) redisConnection.getNativeConnection();
                Object result = jedis.eval(RELEASE_LOCK_SCRIPT, Collections.singletonList(key), Collections.singletonList(value));
                if (RELEASE_SUCCESS.equals(result)) {
                    return Boolean.TRUE;
                }
                return Boolean.FALSE;
            });
        }
    
        /**
         * 锁延时
         * @param key
         * @param value
         * @param expireTime
         * @return
         */
        public Boolean postpone(String key, String value, long expireTime) {
            return redisTemplate.execute((RedisCallback<Boolean>) redisConnection -> {
                Jedis jedis = (Jedis) redisConnection.getNativeConnection();
                Object result = jedis.eval(POSTPONE_LOCK_SCRIPT, Lists.newArrayList(key), Lists.newArrayList(value, String.valueOf(expireTime)));
                if (POSTPONE_SUCCESS.equals(result)) {
                    return Boolean.TRUE;
                }
                return Boolean.FALSE;
            });
        }
    
    }
    
    public class PostponeTask implements Runnable {
    
        private String key;
        private String value;
        private long expireTime;
        private boolean isRunning;
        private DistributedLock distributedLock;
    
        public PostponeTask() {
        }
    
        public PostponeTask(String key, String value, long expireTime, DistributedLock distributedLock) {
            this.key = key;
            this.value = value;
            this.expireTime = expireTime;
            this.isRunning = Boolean.TRUE;
            this.distributedLock = distributedLock;
        }
    
        @Override
        public void run() {
            long waitTime = expireTime * 1000 * 2 / 3;// 线程等待多长时间后执行
            while (isRunning) {
                try {
                    Thread.sleep(waitTime);
                    if (distributedLock.postpone(key, value, expireTime)) {
                        System.out.println("延时成功...........................................................");
                    } else {
                        this.stop();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    
        private void stop() {
            this.isRunning = Boolean.FALSE;
        }
    
    }

N.参考

(1)[Redis分布式锁实例](https://blog.csdn.net/weixin_43841693/article/details/99677422)

(2)[Redis分布式锁的正确实现方式（Java版）](https://blog.csdn.net/yb223731/article/details/90349502)

# Redis架构

1.缓存雪崩

通常会使用缓存用于缓冲对DB的冲击，如果缓存宕机或者同一时间大面积失效，瞬间Redis跟没有一样，所有请求将直接打在DB，造成DB宕机——从而导致整个系统宕机。

解决方案：
事前，redis高可用，主从 + 哨兵，redis cluster，避免全盘崩溃。在批量往Redis存数据的时候，把每个Key的失效时间都加个随机值就好了，这样可以保证数据不会再同一时间大面积失效。或者设置热点数据永不过期，有更新操作就更新缓存就好了（不要设置过期时间），电商首页的数据也可以用这个操作，保险。
事中，使用断路器限流（在服务层），如果缓存宕机，为了防止系统全部宕机，限制部分流量进入DB，保证部分可用，其余的请求返回断路器的默认值。本地ehcache缓存 + hystrix限流&降级，避免MySQL被打死。
事后，redis持久化，一旦重启，自动从磁盘上加载数据，快速恢复缓存数据。

优化后：
用户发送一个请求，系统收到请求后，先查本地ehcache缓存，如果没查到再查redis。如果ehcache和redis都没有，再查数据库，将数据库中的结果，写入ehcache和redis中。
限流组件，可以设置每秒的请求，有多少能通过组件，剩余的未通过的请求，怎么办？走降级！可以返回一些默认的值，或者友情提示，或者空白的值。

好处：
数据库绝对不会死，限流组件确保了每秒只有多少个请求能通过。
只要数据库不死，就是说，对用户来说，部分的请求都是可以被处理的。
只要有部分的请求可以被处理，就意味着你的系统没死，对用户来说，可能就是点击几次刷不出来页面，但是多点几次，就可以刷出来一次。

2.缓存穿透

缓存和数据库中都没有的数据，而用户（黑客）不断发起请求。缓存查询一个没有的key，同时数据库也没有，如果黑客大量的使用这种方式，那么就会导致DB宕机。
或者大量请求查询一个刚刚失效的key，导致DB压力倍增，可能导致宕机，但实际上，查询的都是相同的数据。

解决方案：
(1)在接口层增加校验，比如用户鉴权，参数做校验，不合法的校验直接return。
(2)使用一个默认值来防止，例如，当访问一个不存在的key，然后再去访问数据库，还是没有，那么就在缓存里放一个占位符，下次来的时候，检查这个占位符，如果发生时返回占位符，就不去数据库查询了，防止DB宕机。从数据库中只要没查到，就写一个空值到缓存里去，比如set -999 UNKNOWN。然后设置一个过期时间，这样的话，下次有相同的key来访问的时候，在缓存失效之前，都可以直接从缓存中取数据。
(3)使用布隆过滤器（使用类似位图的数据结构，快速判断某个数据是否在大的数据集合中存在，仅占用少量内存。因为hash可能重复，所以布隆过滤器可以判断某个数据一定不存在，但是无法判断一定存在）。高效判断Key是否在数据库中存在，不存在return就好了，存在就去查DB刷新KV再return。
就是引入了k(k>1)个相互独立的哈希函数，保证在给定的空间、误判率下，完成元素判重的过程。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。Bloom-Filter算法的核心思想就是利用多个不同的Hash函数来解决“冲突”。Hash存在一个冲突（碰撞）的问题，用同一个Hash得到的两个URL的值有可能相同。为了减少冲突，我们可以多引入几个Hash，如果通过其中的一个Hash值我们得出某元素不在集合中，那么该元素肯定不在集合中。只有在所有的Hash函数告诉我们该元素在集合中时，才能确定该元素存在于集合中。这便是Bloom-Filter的基本思想。Bloom-Filter一般用于在大数据量的集合中判定某元素是否存在。

3.缓存击穿

缓存击穿跟缓存雪崩有点像，但是又有一点不一样。缓存击穿不同的是缓存击穿是指一个Key非常热点，在不停地扛着大量的请求，大并发集中对这一个点进行访问，当这个Key在失效的瞬间，持续的大并发直接落到了数据库上，就在这个Key的点上击穿了缓存。

解决方案：
若缓存的数据是基本不会发生更新的，则可尝试将该热点数据设置为永不过期。
若缓存的数据更新不频繁，且缓存刷新的整个流程耗时较少的情况下，则可以采用基于redis、zookeeper等分布式中间件的分布式互斥锁，或者本地互斥锁以保证仅少量的请求能请求数据库并重新构建缓存，其余线程则在锁释放后能访问到新缓存。
若缓存的数据更新频繁或者缓存刷新的流程耗时较长的情况下，可以利用定时线程在缓存过期前主动的重新构建缓存或者延后缓存的过期时间，以保证所有的请求能一直访问到对应的缓存。

4.缓存并发竞争
多个客户端写一个key，如果顺序错了，数据就不对了。但是顺序我们无法控制。
解决：使用分布式锁，例如zk，同时加入数据的时间戳。同一时刻，只有抢到锁的客户端才能写入，同时，写入时，比较当前数据的时间戳和缓存中数据的时间戳。

5.缓存预热

缓存预热就是系统上线后，将相关的缓存数据直接加载到缓存系统。这样就可以避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题，用户直接查询事先被预热的缓存数据。

解决思路：
直接写个缓存刷新页面，上线时手工操作下。
数据量不大，可以在项目启动的时候自动进行加载。
定时刷新缓存。

6.缓存和数据库双写不一致
连续写数据库和缓存，但是操作期间，出现并发了，数据不一致了。

更新缓存和数据库有以下几种顺序：

(1)先更新数据库，再更新缓存。
当有2个请求同时更新数据，那么如果不使用分布式锁，将无法控制最后缓存的值到底是多少。也就是并发写的时候有问题。

(2)先删缓存，再更新数据库。
这么做的问题：如果在删除缓存后，有客户端读数据，将可能读到旧数据，并有可能设置到缓存中，导致缓存中的数据一直是老数据。
有2种解决方案：
使用“双删”，即删更删，最后一步的删除作为异步操作，就是防止有客户端读取的时候设置了旧值。
使用队列，当这个key不存在时，将其放入队列，串行执行，必须等到更新数据库完毕才能读取数据。总的来讲，比较麻烦。

(3)先更新数据库，再删除缓存。
这个实际是常用的方案，但是有很多人不知道，这里介绍一下，这个叫Cache Aside Pattern，老外发明的。如果先更新数据库，再删除缓存，那么就会出现更新数据库之前有瞬间数据不是很及时。
同时，如果在更新之前，缓存刚好失效了，读客户端有可能读到旧值，然后在写客户端删除结束后再次设置了旧值，非常巧合的情况。
有2个前提条件：缓存在写之前的时候失效，同时，在写客户度删除操作结束后，放置旧数据——也就是读比写慢，甚至有的写操作还会锁表。

示例代码：

    public static String getData(String key) throws InterruptedException {
            //从Redis查询数据
            String result = getDataByKV(key);
            //参数校验
            if (StringUtils.isBlank(result)) {
                try {
                    //获得锁
                    if (reenLock.tryLock()) {
                        //去数据库查询
                        result = getDataByDB(key);
                        //校验
                        if (StringUtils.isNotBlank(result)) {
                            //插进缓存
                            setDataToKV(key, result);
                        }
                    } else {
                        //睡一会再拿
                        Thread.sleep(100L);
                        result = getData(key);
                    }
                } finally {
                    //释放锁
                    reenLock.unlock();
                }
            }
            return result;
     }

7.主从复制

主从复制结合哨兵模式能解决单点故障问题，提高可用性。从节点仅提供读操作，主节点提供写操作。对于读多写少的状况，可给主节点配置多个从节点，从而提高响应效率。

(1)复制过程：
从节点执行slaveof masterIP masterPort，让一个服务器成为另一个服务器的从服务器。一个从服务器只能有一个主服务器，并且不支持主主复制。
从节点中的定时任务发现主节点信息，建立和主节点的Socket连接。
从节点发送Ping信号，主节点返回Pong，两边能互相通信。
连接建立后，主节点将所有数据发送给从节点（数据同步）。
主节点把当前的数据同步给从节点后，便完成了复制的建立过程。接下来，主节点就会持续的把写命令发送给从节点，保证主从数据一致性。

(2)数据同步大概过程：
主服务器创建快照文件，发送给从服务器，并在发送期间使用缓冲区记录执行的写命令。快照文件发送完毕之后，开始向从服务器发送存储在缓冲区中的写命令。
从服务器丢弃所有旧数据，载入主服务器发来的快照文件，之后从服务器开始接受主服务器发来的写命令；主服务器每执行一次写命令，就向从服务器发送相同的写命令。

(3)数据同步详细过程：
Redis 2.8之前使用sync runId offset同步命令（仅支持全量复制），Redis 2.8之后使用psync runId offset命令（支持全量和部分复制）。
当从节点发送psync runId offset命令，主节点有三种响应：FULLRESYNC：第一次连接，进行全量复制。CONTINUE：进行部分复制。ERR：不支持psync命令，进行全量复制
几个概念：
runId：每个Redis节点启动都会生成唯一的uuid，每次Redis重启后，runId都会发生变化。
offset：主节点和从节点都各自维护自己的主从复制偏移量offset，当主节点有写入命令时，offset=offset+命令的字节长度。从节点在收到主节点发送的命令后，也会增加自己的offset，并把自己的offset发送给主节点。这样主节点同时保存自己的offset和从节点的offset，通过对比offset来判断主从节点数据是否一致。
repl_backlog_size：保存在主节点上的一个固定长度的先进先出队列，默认大小是1MB。
主节点发送数据给从节点过程中，主节点还会进行一些写操作，这时候的数据存储在复制缓冲区中。从节点同步主节点数据完成后，主节点将缓冲区的数据继续发送给从节点，用于部分复制。主节点响应写命令时，不但会把命令发送给从节点，还会写入复制积压缓冲区，用于复制命令丢失的数据补救。

(4)全量复制：
从节点发送psync ? -1 命令（因为第一次发送，不知道主节点的runId，所以为?，因为是第一次复制，所以offset=-1）。
主节点发现从节点是第一次复制，返回FULLRESYNC runId offset，runId是主节点的runId，offset是主节点目前的offset。
从节点接收主节点信息后，保存到info中。
主节点在发送FULLRESYNC后，启动bgsave命令，生成RDB文件（数据持久化）。
主节点发送RDB文件给从节点。到从节点加载数据完成这段期间主节点的写命令放入缓冲区。
从节点清理自己的数据库数据。
从节点加载RDB文件，将数据保存到自己的数据库中。如果从节点开启了AOF，从节点会异步重写AOF文件。

(5)部分复制：
部分复制主要是针对全量复制的过高开销做出的一种优化措施，使用psync runId offset命令实现。当从节点正在复制主节点时，如果出现网络闪断或者命令丢失等异常情况时，从节点会向主节点要求补发丢失的命令数据，主节点的复制积压缓冲区将这部分数据直接发送给从节点。这样就可以保持主从节点复制的一致性。补发的这部分数据一般远远小于全量数据。
主从连接中断期间主节点依然响应命令，但因复制连接中断命令无法发送给从节点，不过主节点内的复制积压缓冲区依然可以保存最近一段时间的写命令数据。
当主从连接恢复后，由于从节点之前保存了自身已复制的偏移量和主节点的运行ID。因此会把它们当做psync参数发送给主节点，要求进行部分复制。
主节点接收到psync命令后首先核对参数runId是否与自身一致，如果一致，说明之前复制的是当前主节点。之后根据参数offset在复制积压缓冲区中查找，如果offset之后的数据存在，则对从节点发送+COUTINUE 命令，表示可以进行部分复制。因为缓冲区大小固定，若发生缓冲溢出，则进行全量复制。
主节点根据偏移量把复制积压缓冲区里的数据发送给从节点，保证主从复制进入正常状态。

(6)主从链：
随着负载不断上升，主服务器可能无法很快地更新所有从服务器，或者重新连接和重新同步从服务器将导致系统超载。为了解决这个问题，可以创建一个中间层来分担主服务器的复制工作。中间层的服务器是最上层服务器的从服务器，又是最下层服务器的主服务器。

8.哨兵模式（Sentinel）

哨兵可以监听集群中的服务器，并在主服务器进入下线状态时，自动从从服务器中选举出新的主服务器。哨兵最小配置是一主一从（正常三个）。Sentinel系统可以用来管理多个Redis服务器。

主要功能：
监控：不断检查主服务器和从服务器是否正常运行。
通知：当被监控的某个Redis服务器出现问题，Sentinel通过API脚本向管理员或者其他应用程序发出通知。
自动故障转移：当主节点不能正常工作时，Sentinel 会开始一次自动的故障转移操作，它会将与失效主节点是主从关系的其中一个从节点升级为新的主节点，并且将其他的从节点指向新的主节点，这样人工干预就可以免了。
配置提供者：在Sentinel模式下，客户端应用在初始化时连接的是Sentinel节点集合，从中获取Redis主节点的信息。

工作原理：
每个Sentinel节点都需要定期执行以下任务：每个Sentinel以每秒一次的频率，向它所知的主服务器、从服务器以及其他的Sentinel实例发送一个PING命令。
如果一个实例距离最后一次有效回复PING命令的时间超过down-after-milliseconds所指定的值，那么这个实例会被Sentinel标记为主观下线。
如果一个主服务器被标记为主观下线，那么正在监视这个服务器的所有Sentinel节点，要以每秒一次的频率确认主服务器的确进入了主观下线状态。
如果一个主服务器被标记为主观下线，并且有足够数量的Sentinel（至少要达到配置文件指定的数量）在指定的时间范围内同意这一判断，那么这个主服务器被标记为客观下线。
一般情况下，每个Sentinel会以每10秒一次的频率向它已知的所有主服务器和从服务器发送INFO命令。当一个主服务器被标记为客观下线时，Sentinel向下线主服务器的所有从服务器发送INFO命令的频率，会从10秒一次改为每秒一次。
Sentinel和其他Sentinel协商客观下线的主节点的状态，如果处于SDOWN状态，则投票自动选出新的主节点，将剩余从节点指向新的主节点进行数据复制。
当没有足够数量的Sentinel同意主服务器下线时，主服务器的客观下线状态就会被移除。当主服务器重新向 Sentinel 的 PING 命令返回有效回复时，主服务器的主观下线状态就会被移除。

正常状态 --> 标记为主观下线 --> 标记被客观下线 --> 标记为主观下线 --> 正常状态

9.分片

分片是将数据划分为多个部分的方法，可以将数据存储到多台机器里面，这种方法在解决某些问题时可以获得线性级别的性能提升。
根据执行分片的位置，可以分为三种分片方式：
客户端分片：客户端使用一致性哈希等算法决定键应当分布到哪个节点。
代理分片：将客户端请求发送到代理上，由代理转发请求到正确的节点上。
服务器分片：Redis Cluster。

10.同时有多个子系统去set一个key。这个时候要注意什么呢？
   
不推荐使用redis的事务机制。因为我们的生产环境，基本都是redis集群环境，做了数据分片操作。你一个事务中有涉及到多个key操作的时候，这多个key不一定都存储在同一个redis-server上。因此，redis的事务机制，十分鸡肋。
如果对这个key操作，不要求顺序：准备一个分布式锁，大家去抢锁，抢到锁就做set操作即可。
如果对这个key操作，要求顺序：分布式锁+时间戳。假设这会系统B先抢到锁，将key1设置为{valueB 3:05}。接下来系统A抢到锁，发现自己的valueA的时间戳早于缓存中的时间戳，那就不做set操作了。以此类推。
利用队列，将set方法变成串行访问也可以redis遇到高并发，如果保证读写key的一致性
对redis的操作都是具有原子性的,是线程安全的操作,你不用考虑并发问题,redis内部已经帮你处理好并发的问题了。

11.Redis集群方案应该怎么做？

(1)twemproxy，大概概念是它类似于一个代理方式，使用时在本需要连接redis的地方改为连接twemproxy， 它会以一个代理的身份接收请求并使用一致性hash 算法，将请求转接到具体redis，将结果再返回twemproxy。缺点：twemproxy 自身单端口实例的压力，使用一致性hash后，对redis节点数量改变时候的计算值的改变，数据无法自动移动到新的节点。
(2)codis，目前用的最多的集群方案，基本和 twemproxy 一致的效果，但它支持在 节点数量改变情况下，旧节点数据可恢复到新hash节点。
(3)redis cluster3.0 自带的集群，特点在于他的分布式算法不是一致性hash，而是hash槽的概念，以及自身支持节点设置从节点。

12.Redis主从集群切换数据丢失问题如何应对

丢失原因
(1)异步复制丢失：
对于Redis主节点与从节点之间的数据复制，是异步复制的，当客户端发送写请求给master节点的时候，客户端会返回OK，然后同步到各个slave节点中。如果此时master还没来得及同步给slave节点时发生宕机，那么master内存中的数据会丢失。
要是master中开启持久化设置数据可不可以保证不丢失呢？答案是否定的。在master发生宕机后，sentinel集群检测到master发生故障，重新选举新的master，如果旧的master在故障恢复后重启，那么此时它需要同步新master的数据，此时新的master的数据是空的（假设这段时间中没有数据写入）。那么旧master中的数据就会被刷新掉，此时数据还是会丢失。

(2)集群产生脑裂：
首先我们需要理解集群的脑裂现象，这就好比一个人有两个大脑，那么到底受谁来控制呢？在分布式集群中，分布式协作框架zookeeper很好的解决了这个问题，通过控制半数以上的机器来解决。
那么在Redis中，集群脑裂产生数据丢失的现象是怎么样的呢？假设我们有一个redis集群，正常情况下client会向master发送请求，然后同步到salve，sentinel集群监控着集群，在集群发生故障时进行自动故障转移。
此时，由于某种原因，比如网络原因，集群出现了分区，master与slave节点之间断开了联系，sentinel监控到一段时间没有联系认为master故障，然后重新选举，将slave切换为新的master。
但是master可能并没有发生故障，只是网络产生分区，此时client任然在旧的master上写数据，而新的master中没有数据，如果不及时发现问题进行处理可能旧的master中堆积大量数据。在发现问题之后，旧的master降为slave同步新的master数据，那么之前的数据被刷新掉，大量数据丢失。

如何保证尽量少的数据丢失？
在redis的配置文件中有两个参数我们可以如下设置。min-slaves-to-write 默认情况下是0，min-slaves-max-lag默认情况下是10。

    min-slaves-to-write 1
    min-slaves-max-lag 10

以上面配置为例，这两个参数表示至少有1个salve的与master的同步复制延迟不能超过10s，一旦所有的slave复制和同步的延迟达到了10s，那么此时master就不会接受任何请求。
我们可以减小min-slaves-max-lag参数的值，这样就可以避免在发生故障时大量的数据丢失，一旦发现延迟超过了该值就不会往master中写入数据。
那么对于client，我们可以采取降级措施，将数据暂时写入本地缓存和磁盘中，在一段时间后重新写入master来保证数据不丢失；也可以将数据写入kafka消息队列，隔一段时间去消费kafka中的数据。
通过上面两个参数的设置我们尽可能的减少数据的丢失，具体的值还需要在特定的环境下进行测试设置。

N.参考

(1)[Redis集群方案](https://blog.csdn.net/weixin_49724150/article/details/121659693)

# Redis性能优化

1.redis服务节点受到外部关联影响

redis服务所在服务器，物理机的资源竞争及网络状况等。同一台服务器上的服务必然面对着服务资源的竞争，CPU，内存，固存等。

(1)CPU资源竞争

redis属于CPU密集型服务，对CPU资源依赖尤为紧密，当所在服务器存在其它CPU密集型应用时，必然会影响redis的服务能力，尤其是在其它服务对CPU资源消耗不稳定的情况下。因此，在实际规划redis这种基础性数据服务时应该注意一下几点：
一般不要和其它类型的服务进行混部。
同类型的redis服务，也应该针对所服务的不同上层应用进行资源隔离。
说到CPU关联性，可能有人会问是否应该对redis服务进行CPU绑定，以降低由CPU上下文切换带来的性能消耗及关联影响？
简单来说，是可以的，这种优化可以针对任何CPU亲和性要求比较高的服务，但是在此处，有一点我们也应该特别注意：就是redis持久化时会fork出子进程进行AOF/RDB持久化任务。对于开启了持久化配置的redis服务（一般情况下都会开启），假如我们做了CPU亲和性处理，那么redis fork出的子进程则会和父进程共享同一个CPU资源，我们知道，redis持久化进程是一个非常耗资源的过程，这种自竞争必然会引发redis服务的极大不稳定。

(2)内存不在内存了

内存稳定性是redis提供稳定，低延迟服务的最基本的要求。然而，我们也知道操作系统有一个swap的东西，也就将内存交换到硬盘。假如发生了redis内存被交换到硬盘的情景发生，那么必然，redis服务能力会骤然下降。
swap发现及避免：
(a)info memory：关于redis内存分析，内存优化 中我们也讲过，swap这种情景，此时，查看redis的内存信息，可以观察到碎片率会小于1。这也可以作为监控redis服务稳定性的一个指标。
(b)通过redis进程查看：首先通过info server获取进程id，查看redis进程swap情况：cat /proc/$PID/smaps。确定交换量都为0KB或者4KB。
(c)redis服务maxmemory配置：对redis服务必要的内存上限配置，这是内存隔离的一种必要。需要确定的是所有redis实例的分配内存总额小于总的可用物理内存。
(d)系统优化：另外，在最初的基础服务操作系统安装部署时，也需要做一些必要的前置优化，如关闭swap或配置系统尽量避免使用。

(3)网络问题

网络问题，是一个普遍的影响因素。
(a)网络资源耗尽：简单来说，就是带宽不够了，整个属于基础资源架构的问题了，对网络资源的预估不足，跨机房，异地部署等都会成为诱因。
(b)连接数用完了：一个客户端连接对应着一个TCP连接，一个TCP连接在LINUX系统内对应着一个文件句柄，系统级别连接句柄用完了，也就无法再进行连接了。查看当前系统限制：ulimit -n。设置：ulimit -n {num}
(c)端口TCP backlog队列满了：linux系统对于每个端口使用backlog保存每一个TCP连接。redis配置：tcp_backlog 默认511。高并发情境下，可以适当调整此配置，但需要注意的是，同时要调整系统相关设置。系统修改命令：echo {num}>/proc/sys/net/core/somaxconn。查看因为队列溢出导致的连接绝句：netstat -s | grep overflowed。
(d)网络延迟网络质量问题，可以使用redis-cli进行网络状况的测试：延迟测试：redis-cli -h {host} -p {port} --latency。采样延迟测试：redis-cli -h {host} -p {port} --latency-history 默认15s一次。图形线上测试结果：redis-cli -h {host} -p {port} --latency-dist
(e)网卡软中断：单个网卡队列只能使用单个CPU资源问题。

2.redis服务使用问题

(1)慢查询

如果查询总是慢查询，那么必然你的使用存在不合理。
(a)key规划是否合理：太长或太短都是不建议的，key需要设置的简短而有意义。
(b)值类型选择是否合理。hash还是string，set还是zset，避免大对象存储。线上可以通过scan命令进行大对象发现治理。
(c)是否能够批查询：get还是mget；是否应该使用pipeline。
(d)禁止线上大数据量操作

(2)redis服务运行状况

查看redis服务运行状况：redis-cli -h {host} -p {port} --stat
keys：当前key总数；mem：内存使用；clients：当前连接client数；blocked：阻塞数；requests：累计请求数；connections：累计连接数

(3)持久化操作影响
(a)fork子进程影响：redis进行持久化操作需要fork出子进程。fork子进程本身如果时间过长，则会产生一定的影响。查看命令最近一次fork耗时：info stats，显示lastest_fork_usec：765，单位微妙，确保不要超过1s。
(b)AOF刷盘阻塞：AOF持久化开启，后台每秒进行AOF文件刷盘操作，系统fsync操作将AOF文件同步到硬盘，如果主线程发现距离上一次成功fsync超过2s，则会阻塞后台线程等待fsync完成以保障数据安全性。
(c)THP问题：透明大页问题，linux系统的写时复制机制会使得每次写操作引起的页复制由4KB提升至2M从而导致写慢查询。如果慢查询堆积必然导致后续连接问题。

3.遍历大数据量

事故产生：因为我们的用户token缓存是采用了【user_token:userid】格式的key，保存用户的token的值。我们运维为了帮助开发小伙伴们查一下线上现在有多少登录用户。直接用了keys user_token*方式进行查询，事故就此发生了。导致redis不可用，假死。
分析原因：我们线上的登录用户有几百万，数据量比较多；keys算法是遍历算法，复杂度是O(n)，也就是数据越多，时间复杂度越高。数据量达到几百万，keys这个指令就会导致Redis服务卡顿，因为 Redis 是单线程程序，顺序执行所有指令，其它指令必须等到当前的 keys 指令执行完了才可以继续。
解决方案：

采用redis的命令scan。复杂度虽然也是 O(n)，但是它是通过游标分步进行的，不会阻塞线程。
提供count参数，不是结果数量，是redis单次遍历字典槽位数量(约等于)。同keys一样，它也提供模式匹配功能。服务器不需要为游标保存状态，游标的唯一状态就是scan返回给客户端的游标整数;
返回的结果可能会有重复，需要客户端去重复，这点非常重要。单次返回的结果是空的并不意味着遍历结束，而要看返回的游标值是否为零。
SCAN命令是增量的循环，每次调用只会返回一小部分的元素。所以不会让redis假死。SCAN命令返回的是一个游标，从0开始遍历，到0结束遍历。

    // scan 游标 MATCH <返回和给定模式相匹配的元素> count 每次迭代遍历的元素数量
    scan cursor [MATCH pattern] [COUNT count]
    scan 0 match user_token* count 5
    
4.Redis主从结构持久化的性能问题

(1)Master最好不要做任何持久化工作，如RDB内存快照和AOF日志文件。
(2)如果数据比较重要，某个Slave开启AOF备份数据，策略设置为每秒同步一次。
(3)为了主从复制的速度和连接的稳定性，Master和Slave最好在同一个局域网内。
(4)尽量避免在压力很大的主库上增加从库。
(5)主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <- Slave1 <- Slave2 <-Slave3。
    
N.参考

(1)[【95期】面试官：你遇到 Redis 线上连接超时一般如何处理？](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247484462&idx=1&sn=915ad7fba59fcc7f375a19208d71c9db&chksm=e80db258df7a3b4ef42b6d612233de96fa5b76c7b7e988cbdf5659ffc35e3fa7db7da2682163&scene=21#wechat_redirect)

# 参考

1.[Redis中文官网](http://www.redis.cn/)

2.[菜鸟Redis教程](https://www.runoob.com/redis/redis-tutorial.html)