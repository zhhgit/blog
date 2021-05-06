---
layout: post
title: "后端开发面试题 -- Redis篇"
description: 后端开发面试题 -- Redis篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

# 一、概念

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

5.常见缓存问题

(1)缓存雪崩
通常会使用缓存用于缓冲对DB的冲击，如果缓存宕机或者同一时间大面积失效，瞬间Redis跟没有一样，所有请求将直接打在DB，造成DB宕机——从而导致整个系统宕机。
解决方案：
(a)对缓存做高可用，防止缓存宕机。
(b)使用断路器，如果缓存宕机，为了防止系统全部宕机，限制部分流量进入DB，保证部分可用，其余的请求返回断路器的默认值。
(c)在批量往Redis存数据的时候，把每个Key的失效时间都加个随机值就好了，这样可以保证数据不会再同一时间大面积失效。或者设置热点数据永不过期，有更新操作就更新缓存就好了（不要设置过期时间），电商首页的数据也可以用这个操作，保险。

(2)缓存穿透
缓存和数据库中都没有的数据，而用户（黑客）不断发起请求。缓存查询一个没有的key，同时数据库也没有，如果黑客大量的使用这种方式，那么就会导致DB宕机。或者大量请求查询一个刚刚失效的key，导致DB压力倍增，可能导致宕机，但实际上，查询的都是相同的数据。
解决方案：
(a)在接口层增加校验，比如用户鉴权，参数做校验，不合法的校验直接return。
(b)使用一个默认值来防止，例如，当访问一个不存在的key，然后再去访问数据库，还是没有，那么就在缓存里放一个占位符，下次来的时候，检查这个占位符，如果发生时返回占位符，就不去数据库查询了，防止DB宕机。
(c)使用布隆过滤器（使用类似位图的数据结构，快速判断某个数据是否在大的数据集合中存在，仅占用少量内存。因为hash可能重复，所以布隆过滤器可以判断某个数据一定不存在，但是无法判断一定存在）。
高效判断Key是否在数据库中存在，不存在return就好了，存在就去查DB刷新KV再return。

(3)缓存击穿
缓存击穿跟缓存雪崩有点像，但是又有一点不一样。缓存击穿不同的是缓存击穿是指一个Key非常热点，在不停地扛着大量的请求，大并发集中对这一个点进行访问，当这个Key在失效的瞬间，持续的大并发直接落到了数据库上，就在这个Key的点上击穿了缓存。
解决方案：
设置热点数据永不过期，或者加上互斥锁。

(4)缓存并发竞争
多个客户端写一个key，如果顺序错了，数据就不对了。但是顺序我们无法控制。
解决：使用分布式锁，例如zk，同时加入数据的时间戳。同一时刻，只有抢到锁的客户端才能写入，同时，写入时，比较当前数据的时间戳和缓存中数据的时间戳。

(5)缓存和数据库双写不一致
连续写数据库和缓存，但是操作期间，出现并发了，数据不一致了。

(a)先更新数据库，再更新缓存。当有2个请求同时更新数据，那么如果不使用分布式锁，将无法控制最后缓存的值到底是多少。也就是并发写的时候有问题。
(b)先删缓存，再更新数据库。这么做的问题：如果在删除缓存后，有客户端读数据，将可能读到旧数据，并有可能设置到缓存中，导致缓存中的数据一直是老数据。
有2种解决方案：使用“双删”，即删更删，最后一步的删除作为异步操作，就是防止有客户端读取的时候设置了旧值。或使用队列，当这个key不存在时，将其放入队列，串行执行，必须等到更新数据库完毕才能读取数据。总的来讲，比较麻烦。
(c)先更新数据库，再删除缓存。这个实际是常用的方案，但是有很多人不知道，这里介绍一下，这个叫Cache Aside Pattern，老外发明的。如果先更新数据库，再删除缓存，那么就会出现更新数据库之前有瞬间数据不是很及时。
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

# 二、技术细节

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

2,键相关操作

    del key
    // 返回序列号之后的值
    dump key
    // 判断是否存在key，若key存在返回1，否则返回0。
    exists key
    // 设置过期时间，设置成功返回1，否则返回0。TIME_IN_UNIX_TIMESTAMP例如1293840000，TIME_IN_MILLISECONDS_IN_UNIX_TIMESTAMP例如1555555555005
    expire key seconds
    pexpire key milliseconds
    expireat key TIME_IN_UNIX_TIMESTAMP
    pexpireat key TIME_IN_MILLISECONDS_IN_UNIX_TIMESTAMP
    // 查找所有符合给定模式的key，获取所有的key可用使用keys *
    keys pattern
    // 将当前数据库的key移动到给定的数据库db当中。MOVE song 1 将song移动到数据库1
    move key DESTINATION_DATABASE
    // 移除给定key的过期时间，使得key永不过期。
    persist key
    // 返回key的剩余过期时间
    ttl key
    pttl key
    // 当前数据库中随机返回一个key 
    randomkey
    // 修改key的名称，当oldkey newkey相同，或者当oldkey不存在时，返回一个错误。当newkey已经存在时将覆盖旧值。
    rename oldkey newkey
    // 在新的key不存在时修改key的名称
    renamenx oldkey newkey
    // 返回所储存的值的类型
    type key
    // SCAN命令用于迭代数据库中的数据库键。是一个基于游标的迭代器，每次被调用之后，都会向用户返回一个新的游标，用户在下次迭代时需要使用这个新游标作为SCAN命令的游标参数，以此来延续之前的迭代过程。
    // SCAN 返回一个包含两个元素的数组， 第一个元素是用于进行下一次迭代的新游标， 而第二个元素则是一个数组， 这个数组中包含了所有被迭代的元素。如果新游标返回0表示迭代已结束。
    // 例如：scan 0 match key1111* count 20
    scan cursor [MATCH pattern] [COUNT count]
    
3.数据结构

Redis内部使用一个redisObject对象来表示所有的key和value。
redisObject最主要的信息：type表示一个value对象具体是何种数据类型，encoding是不同数据类型在Redis内部的存储方式，数据指针ptr，虚拟内存vm。
比如：type=string表示value存储的是一个字符串，那么encoding 可以是raw或者int。

(1)string，最大512M，是二进制安全的，即可以包含任何数据（比如图片或者序列化的对象）。value不仅可以是字符串，也可以是数字。

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

(2)hash，是一个string类型的field和value的映射表，特别适合用于存储对象。每个hash可以存储2^32 -1键值对（40多亿）

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

(3)list，字符串列表，按照插入顺序排序，列表最多可存储2^32 - 1元素。其实现基于双向链表，即可以支持正向、反向查找和遍历，更方便操作，不过带来了额外的内存开销。

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

(4)set，是string类型的无序集合，是去重的。集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。集合中最大的成员数为2^32 - 1。

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

(5)zset(sorted set)，zset和set一样也是string类型元素的集合，且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数score。redis正是通过分数来为集合中的成员进行从小到大的排序。
其实现使用HashMap和跳跃表（skipList）来保证数据的存储和有序，HashMap里放的是成员到Score的映射。而跳跃表里存放的是所有的成员，排序依据是HashMap里存的Score，使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。

    zadd key score member
    zincrby key increment member
    zrem key member
    zscore key member
    // 默认是闭区间，即min <= score <= max，开区间可以zrangebyscore key (min (max，正负无穷为+inf，-inf，withscores表示显示整个有序集及成员的score值
    zrangebyscore key min max [withscores]

(6)bitmap：更细化的一种操作，以bit为单位。

(7)hyperloglog：基于概率的数据结构。

4.Redis持久化

持久化策略有两种：RDB（快照）与AOF（写文件）

RDB：
快照形式是直接把内存中的数据保存到磁盘的一个二进制文件dump.rdb中，定时保存。Redis默认是RDB的持久化方式。
Redis会fork一个子进程，子进程将数据写到磁盘上一个临时RDB文件中。当子进程完成写临时文件后，将原来的RDB替换掉。
可以将快照复制到其它服务器从而创建具有相同数据的服务器副本。如果系统发生故障，将会丢失最后一次创建快照之后的数据。如果数据量很大，保存快照的时间会很长。
数据库备份和灾难恢复：定时生成RDB快照非常便于进行数据库备份，并且RDB恢复数据集的速度也要比AOF恢复的速度快。

AOF：
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

5.Redis实现分布式锁

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

也可以考虑基于Redission框架实现。
redisson所有指令都通过lua脚本执行，redis支持lua脚本原子性执行。
redisson设置一个key的默认过期时间为30s,如果某个客户端持有一个锁超过了30s怎么办？
redisson中有一个watchdog的概念，翻译过来就是看门狗，它会在你获取锁之后，每隔10秒帮你把key的超时时间设为30s。这样的话，就算一直持有锁也不会出现key过期了，其他线程获取到锁的问题了。redisson的“看门狗”逻辑保证了没有死锁发生。如果机器宕机了，看门狗也就没了。此时就不会延长key的过期时间，到了30s之后就会自动过期了，其他线程可以获取到锁。

    Config config = new Config();
    config.useClusterServers()
    .addNodeAddress("redis://192.168.31.101:7001")
    .addNodeAddress("redis://192.168.31.101:7002")
    .addNodeAddress("redis://192.168.31.101:7003")
    .addNodeAddress("redis://192.168.31.102:7001")
    .addNodeAddress("redis://192.168.31.102:7002")
    .addNodeAddress("redis://192.168.31.102:7003");
    
    RedissonClient redisson = Redisson.create(config);
    RLock lock = redisson.getLock("anyLock");
    lock.lock();
    lock.unlock();

6.Redis的过期策略

(1)定期删除策略：Redis会将每个设置了过期时间的key放入到一个独立的字典中，默认每100ms进行一次过期扫描：随机抽取20个key，删除这20个key中过期的key，如果过期的key比例超过1/4，就重复步骤，继续删除。
不能全部扫描，因为Redis是单线程，全部扫描岂不是卡死了。而且为了防止每次扫描过期key比例都超过1/4，导致不停循环卡死线程，Redis为每次扫描添加了上限时间，默认是25ms。

(2)从库的过期策略：
从库不会进行过期扫描，从库对过期的处理是被动的。主库在key到期时，会在AOF文件里增加一条del指令，同步到所有的从库，从库通过执行这条del指令来删除过期的key。
因为指令同步是异步进行的，所以主库过期的key的del指令没有及时同步到从库的话，会出现主从数据的不一致，主库没有的数据在从库里还存在。

(3)懒惰删除策略：
删除指令del会直接释放对象的内存，大部分情况下，这个指令非常快，没有明显延迟。不过如果删除的key是一个非常大的对象，比如一个包含了千万元素的hash，又或者在使用FLUSHDB和FLUSHALL删除包含大量键的数据库时，那么删除操作就会导致单线程卡顿。
redis 4.0引入了lazyfree的机制，它可以将删除键或数据库的操作放在后台线程里执行，从而尽可能地避免服务器阻塞。
指令为：unlink key/flushall async

(4)内存淘汰机制：
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
    
Redis使用的是近似LRU算法，它跟常规的LRU算法还不太一样。近似LRU算法通过随机采样法淘汰数据，每次随机出5（默认）个key，从里面淘汰掉最近最少使用的key。可以通过maxmemory-samples参数修改采样数量。maxmenory-samples配置的越大，淘汰的结果越接近于严格的LRU算法。
Redis为了实现近似LRU算法，给每个key增加了一个额外增加了一个24bit的字段，用来存储该key最后一次被访问的时间。

    maxmemory-samples 10
    
LFU算法是Redis4.0里面新加的一种淘汰策略。它的全称是Least Frequently Used，它的核心思想是根据key的最近被访问的频率进行淘汰，很少被访问的优先被淘汰，被访问的多的则被留下来。
LFU算法能更好的表示一个key被访问的热度。假如你使用的是LRU算法，一个key很久没有被访问到，只刚刚是偶尔被访问了一次，那么它就被认为是热点数据，不会被淘汰，而有些key将来是很有可能被访问到的则被淘汰了。如果使用LFU算法则不会出现这种情况，因为使用一次并不会使一个key成为热点数据。
LFU一共有两种策略：

    volatile-lfu：在设置了过期时间的key中使用LFU算法淘汰key
    allkeys-lfu：在所有的key中使用LFU算法淘汰数据
    
7.事务与流水线

一个事务包含了多个命令，服务器在执行事务期间，不会改去执行其它客户端的命令请求。Redis最简单的事务实现方式是使用MULTI和EXEC命令将事务操作包围起来。

因为redis的客户端和服务器的连接时基于TCP的，默认每次连接都时只能执行一个命令。流水线则是允许利用一次连接来处理多条命令，从而可以节省一些tcp连接的开销。流水线和事务的差异在于流水线是为了节省通信的开销，但是并不会保证原子性。
pipeline只是把多个redis指令一起发出去，redis并没有保证这些指定的执行是原子的；multi相当于一个redis的transaction的，保证整个操作的原子性，避免由于中途出错而导致最后产生的数据不一致。

8.事件

Redis服务器是一个事件驱动程序。
(1)文件事件:服务器通过套接字与客户端或者其它服务器进行通信，文件事件就是对套接字操作的抽象。Redis基于Reactor模式开发了自己的网络事件处理器，使用I/O多路复用程序来同时监听多个套接字，并将到达的事件传送给文件事件分派器，分派器会根据套接字产生的事件类型调用相应的事件处理器。
(2)时间事件:服务器有一些操作需要在给定的时间点执行，时间事件是对这类定时操作的抽象。时间事件又分为：定时事件（是让一段程序在指定的时间之内执行一次），周期性事件（是让一段程序每隔指定时间就执行一次）。Redis将所有时间事件都放在一个无序链表中，通过遍历整个链表查找出已到达的时间事件，并调用相应的事件处理器。

9.主从复制

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

10.哨兵模式（Sentinel）

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

11.分片

分片是将数据划分为多个部分的方法，可以将数据存储到多台机器里面，这种方法在解决某些问题时可以获得线性级别的性能提升。
根据执行分片的位置，可以分为三种分片方式：
客户端分片：客户端使用一致性哈希等算法决定键应当分布到哪个节点。
代理分片：将客户端请求发送到代理上，由代理转发请求到正确的节点上。
服务器分片：Redis Cluster。

12.使用Redis的方式

UP：使用RedisConnector工具类，JedisBalancer线程池中获取连接，直接用Jedis。
RedisTemplate：opsForValue()、opsForHash()、opsForList()、opsForSet()、opsForZSet()
缓存注解：使用Spring Cache集成Redis（也就是注解的方式）。

13.缓存注解

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

14.关于Redis新版本开始引入多线程

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

# 三、参考

(1)[Redis中文官网](http://www.redis.cn/)

(2)[Redis持久化 - RDB和AOF](https://segmentfault.com/a/1190000016021217)

(3)[Redis分布式锁的正确实现方式（Java版）](https://blog.csdn.net/yb223731/article/details/90349502)

(4)[【99期】中高级开发面试必问的Redis，看这篇就够了！](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247484524&idx=1&sn=7ae13d7805f70592245464a7d9512e57&chksm=e80db21adf7a3b0c6b7c8e1367f4c9e772666e1e764a00ad2f4771c87520fdeff68da57f5f07&scene=21#wechat_redirect)

(5)[Redis分布式锁实例](https://blog.csdn.net/weixin_43841693/article/details/99677422)

(6)[菜鸟Redis教程](https://www.runoob.com/redis/redis-tutorial.html)

(7)[【244期】万字+图解 Redis，面试不用愁了！](https://mp.weixin.qq.com/s/qrX5WUC_oAes3JPgnP6bNQ)