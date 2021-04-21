---
layout: post
title: "后端开发面试题 -- 数据库篇"
description: 后端开发面试题 -- 数据库篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

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

8.分库分表后id主键如何处理

(1)自增id，往一个库的一个表里插入一条没什么业务含义的数据，然后获取一个数据库自增的一个id。拿到这个id之后再往对应的分库分表里去写入。不适合高并发场景。
(2)设置数据库sequence或者表的自增字段步长来进行水平伸缩。比如说，现在有8个服务节点，每个服务节点使用一个sequence功能来产生ID，每个sequence的起始ID不同，并且依次递增，步长都是8。
(3)snowflake算法:开源的分布式id生成算法，是把一个64 位的long型的id，1个bit是不用的，用其中的41bit作为毫秒数，用10bit作为工作机器id，12bit作为序列号。

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

13.MySQL全表扫描

全表扫描是数据库搜寻表的每一条记录的过程，直到所有符合给定条件的记录返回为止。通常在数据库中，对无索引的表进行查询一般称为全表扫描；然而有时候我们即便添加了索引，但当我们的SQL语句写的不合理的时候也会造成全表扫描。
当不规范的写法造成全表扫描时，会造成CPU和内存的额外消耗，甚至会导致服务器崩溃。

14.如何进行SQL优化，哪些SQL可能导致全表扫描 

(1)对查询进行优化，应尽量避免全表扫描，首先应考虑在where及order by涉及的列上建立索引。
避免使用null做为判断条件，如：select account from member where nickname is null。建议在设计字段时尽量将字段的默认值设为0，改为select account where nickname = 0; 

(2)左模糊查询Like %XXX%。 如：select account from member where nickname like ‘%XXX%’ 或者 select account from member where nickname like ‘%XXX’ 
建议使用select account from member where nickname like ‘XXX%’，如果必须要用到做查询，需要评估对当前表全表扫描造成的后果。

(3)使用or做为连接条件。如：select account from member where id = 1 or id = 2; 
建议使用union all,改为 select account from member where id = 1 union all select account from member where id = 2; 

(4)使用in和not in时， 如：select account from member where id in (1,2,3)。使用not in时，如select account where id not in (1,2,3)。
如果是连续数据，可以改为select account where id between 1 and 3;当数据较少时也可以参考union用法； 
或者：select account from member where id in (select accountid from department where id = 3 )，可以改为select account from member where id exsits (select accountid from department where id = 3)。
not in 可以对应 not exists; 

(5)使用!=或<>时，建议使用 <,<=,=,>,>=,between等； 

(6)不要在where子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将不能正确使用索引。对字段有操作时也会引起全表扫描， 如select account where salary * 0.8 = 1000 或者 select account where sustring(nickname,1,3) = 'aaa'; 

(7)使用count(*)时，如select count(*) from member。
建议使用select count(1) from member。

(8)使用参数做为查询条件时，如select account from member where nickname = @name；
由于SQL语句在编译执行时并不确定参数，这将无法通过索引进行数据查询，所以尽量避免。

(9)在使用索引字段作为条件时，如果该索引是复合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使用，并且应尽可能的让字段顺序与索引顺序相一致。

(10)并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能不会去利用索引，如一表中有字段sex，male、female几乎各一半，那么即使在sex上建了索引也对查询效率起不了作用。

(11)索引并不是越多越好，索引固然可以提高相应的select的效率，但同时也降低了insert及update的效率，因为insert或update时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有必要。

(12)应尽可能的避免更新clustered索引数据列，因为clustered索引数据列的顺序就是表记录的物理存储顺序，一旦该列值改变将导致整个表记录的顺序的调整，会耗费相当大的资源。若应用系统需要频繁更新clustered索引数据列，那么需要考虑是否应将该索引建为clustered索引。

(13)尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

(14)尽可能的使用varchar/nvarchar 代替 char/nchar ，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。

(15)任何地方都不要使用select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段。

(16)避免频繁创建和删除临时表，以减少系统表资源的消耗。
临时表并不是不可使用，适当地使用它们可以使某些例程更有效，例如，当需要重复引用大型表或常用表中的某个数据集时。但是，对于一次性事件，最好使用导出表。
在新建临时表时，如果一次性插入数据量很大，那么可以使用select into代替create table，避免造成大量log，以提高速度；如果数据量不大，为了缓和系统表的资源，应先create table，然后insert。
如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，先truncate table ，然后drop table ，这样可以避免系统表的较长时间锁定。

(17)在所有的存储过程和触发器的开始处设置SET NOCOUNT ON ，在结束时设置SET NOCOUNT OFF 。无需在执行存储过程和触发器的每个语句后向客户端发送DONE_IN_PROC消息。

(18)尽量避免向客户端返回大数据量，若数据量过大，应该考虑相应需求是否合理。

(19)尽量避免大事务操作，提高系统并发能力。

15.MySQL的S锁和X锁的区别

MySQL的锁系统：shared lock和exclusive lock（共享锁和排他锁，也叫读锁和写锁，即read lock和write lock）。读锁是共享的，或者说是相互不阻塞的。写锁是排他的，一个写锁会阻塞其他的写锁和读锁。
共享锁【S锁】，又称读锁，若事务T对数据对象A加上S锁，则事务T可以读A但不能修改A，其他事务只能再对A加S锁，而不能加X锁，直到T释放A上的S锁。这保证了其他事务可以读A，但在T释放A上的S锁之前不能对A做任何修改。
排他锁【X锁】，又称写锁。若事务T对数据对象A加上X锁，事务T可以读A也可以修改A，其他事务不能再对A加任何锁，直到T释放A上的锁。这保证了其他事务在T释放A上的锁之前不能再读取和修改A。
   
16.锁的粒度和锁的策略
MySQL有三种锁的级别：页级、表级、行级。
MyISAM和MEMORY存储引擎采用的是表级锁（table-level locking）；BDB存储引擎采用的是页面锁（page-level locking），但也支持表级锁；InnoDB存储引擎既支持行级锁（row-level locking），也支持表级锁，但默认情况下是采用行级锁。
MySQL这3种锁的特性可大致归纳如下：
表级锁：开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高,并发度最低。
行级锁：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低,并发度也最高。
页面锁：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。

17.联合索引生效和失效的条件

联合索引又叫复合索引。两个或更多个列上的索引被称作复合索引。对于复合索引，Mysql从左到右的使用索引中的字段，一个查询可以只使用索引中的一部分，但只能是最左侧部分。例如索引是key index（a,b,c）。可以支持a | a,b| a,b,c 3种组合进行查找，但不支持b,c进行查找。当最左侧字段是常量引用时，索引就十分有效。
利用索引中的附加列，可以缩小搜索的范围，但使用一个具有两列的索引不同于使用两个单独的索引。复合索引的结构与电话簿类似，人名由姓和名构成，电话簿首先按姓氏对进行排序，然后按名字对有相同姓氏的人进行排序。如果您知道姓，电话簿将非常有用；如果您知道姓和名，电话簿则更为有用，但如果您只知道名不姓，电话簿将没有用处。
所以说创建复合索引时，应该仔细考虑列的顺序。对索引中的所有列执行搜索或仅对前几列执行搜索时，复合索引非常有用；仅对后面的任意列执行搜索时，复合索引则没有用处。如：建立 姓名、年龄、性别的复合索引。

    create table myTest(
         a int,
         b int,
         c int,
         KEY a(a,b,c)
    );

以下示例：

    select * from myTest  where a=3 and b=5 and c=4; --abc三个索引都在where条件里面用到了，而且都发挥了作用
    select * from myTest  where  c=4 and b=6 and a=3; --where里面的条件顺序在查询之前会被mysql自动优化，效果跟上一句一样
    select * from myTest  where a=3 and c=7;  --a用到索引，b没有用，所以c是没有用到索引效果的
    select * from myTest  where a=3 and b>7 and c=3;  --a用到了，b也用到了，c没有用到，这个地方b是范围值，也算断点，只不过自身用到了索引
    select * from myTest  where b=3 and c=4;   ---因为a索引没有使用，所以这里bc都没有用上索引效果
    select * from myTest  where a>4 and b=7 and c=9;  --a用到了 b没有使用，c没有使用
    select * from myTest  where a=3 order by b;  --a用到了索引，b在结果排序中也用到了索引的效果，a下面任意一段的b是排好序的
    select * from myTest  where a=3 order by c;  --a用到了索引，但是这个地方c没有发挥排序效果，因为中间断点了，使用 explain 可以看到 filesort
    select * from mytable where b=3 order by a;  --b没有用到索引，排序中a也没有发挥索引效果

索引失效的条件：

不在索引列上做任何操作（计算、函数、（自动or手动）类型转换），会导致索引失效而转向全表扫描。
存储引擎不能使用索引范围条件的右边的列。
尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少select *。即如果select的列都在索引列中，就算是覆盖索引，like '%abc'也能使用索引。
mysql在使用不等于（！=或者<>）的时候无法使用索引会导致全表扫描。
is null,is not null也无法使用索引。
like以通配符开头like '%abc'，mysql索引失效会变成全表扫描的操作。
字符串不加单引号索引失效，SELECT * from staffs where name=2000;  -- 未使用索引，因为mysql会在底层对其进行隐式的类型转换。

18.join操作如何优化提升性能

数据规模较大，可以通过增加索引来优化join语句的执行速度，可以通过冗余信息来减少join的次数。尽量减少表连接的次数，一个SQL语句表连接的次数不要超过5次。
逐条比较两个表的语句是比较慢的，因此我们可以把两个表中数据依次读进一个内存块中, 以MySQL的InnoDB引擎为例，使用以下语句我们必然可以查到相关的内存区域show variables like '%buffer%'，join_buffer_size的大小将会影响我们join语句的执行性能。
大部分数据库中的数据最终要保存到硬盘上,并且以文件的形式进行存储。以MySQL的InnoDB引擎为例，InnoDB以页(page)为基本的IO单位，每个页的大小为16KB，InnoDB会为每个表创建用于存储数据的.ibd文件，这意味着我们有多少表要连接就需要读多少个文件，虽然可以利用索引，但还是免不了频繁的移动硬盘的磁头，就是说频繁的移动磁头会影响性能。
Join算法：有索引的情况下直接读取两个表的索引树进行比较，利用索引来提升性能。无索引的话嵌套循环，在扫描过程中，数据库会选择一个表把它要返回以及需要进行和其他表进行比较的数据放进join_buffer。
Nested Loop Join，嵌套循环，每次只读取表中的一行数据，也就是说如果outerTable有10万行数据, innerTable有100行数据，需要读取10000000次(假设这两个表的文件没有被操作系统给缓存到内存, 我们称之为冷数据表)，当然现在没啥数据库引擎使用这种算法，太慢了。
Block nested loop，Block 块，也就是说每次都会取一块数据到内存以减少I/O的开销，当没有索引可以使用的时候，MySQL InnoDB就会使用这种算法，当无法使用索引执行join操作的时候，InnoDB会自动使用Block nested loop算法

19.处理慢查询

以下语句返回的结果是实时变化的，是对mysql链接执行的现场快照，所以用来处理突发事件非常有用。它可以查看当前mysql的一些运行情况，是否有压力，都在执行什么sql，语句耗时几何，有没有慢sql在执行等等。

    -- 显示数据库线程
    show full processlist;
    show full processlist\G;

字段意义：   
Id：链接mysql 服务器线程的唯一标识，可以通过kill来终止此线程的链接。
User：当前线程链接数据库的用户。
Host：显示这个语句是从哪个ip 的哪个端口上发出的。可用来追踪出问题语句的用户。
DB: 线程链接的数据库，如果没有则为null。
Command: 显示当前连接的执行的命令，一般就是休眠或空闲（sleep），查询（query），连接（connect）。
Time: 线程处在当前状态的时间，单位是秒。
State：显示使用当前连接的sql语句的状态，很重要的列，后续会有所有的状态的描述，请注意，state只是语句执行中的某一个状态，一个 sql语句，已查询为例，可能需要经过copying to tmp table，Sorting result，Sending data等状态才可以完成。
Info: 线程执行的sql语句，如果没有语句执行则为null。这个语句可以使客户端发来的执行语句也可以是内部执行的语句。

当发现一些执行时间很长的sql时，就需要多注意一下了，必要时kill掉，先解决问题。

    -- 杀线程
    kill XXXXX
    
    -- 批量结束时间超过3分钟的线程
    select concat('kill ', id, ';')
    from information_schema.processlist
    where command != 'Sleep'
    and time > 3*60
    order by time desc;

当MySQL在进行一些alter table等DDL操作时，如果该表上有未提交的事务则会出现Waiting for table metadata lock，而一旦出现metadata lock，该表上的后续操作都会被阻塞。
一般只要kill掉这些线程，DDL操作就不会Waiting for table metadata lock。

    -- 从information_schema.innodb_trx 表中查看当前未提交的事务
    select trx_state, trx_started, trx_mysql_thread_id, trx_query from information_schema.innodb_trx;

字段意义：
trx_state: 事务状态，一般为RUNNING。
trx_started: 事务执行的起始时间，若时间较长，则要分析该事务是否合理。
trx_mysql_thread_id: MySQL的线程ID，用于kill。
trx_query: 事务中的sql。

调整锁超时阈值，lock_wait_timeout表示获取metadata lock的超时（单位为秒），允许的值范围为1到31536000（1年）。默认值为31536000。

    set session lock_wait_timeout = 1800;
    set global lock_wait_timeout = 1800;
    
20.主键ID

无特殊需求下Innodb建议使用与业务无关的自增ID作为主键。InnoDB引擎使用聚集索引，数据记录本身被存于主索引（一颗B+Tree）的叶子节点上。
这就要求同一个叶子节点内（大小为一个内存页或磁盘页）的各条数据记录按主键顺序存放，因此每当有一条新的记录插入时，MySQL会根据其主键将其插入适当的节点和位置，如果页面达到装载因子（InnoDB默认为15/16），则开辟一个新的页（节点）。
如果表使用自增主键，那么每次插入新的记录，记录就会顺序添加到当前索引节点的后续位置，当一页写满，就会自动开辟一个新的页。这样就会形成一个紧凑的索引结构，近似顺序填满。由于每次插入时也不需要移动已有数据，因此效率很高，也不会增加很多开销在维护索引上。
如果使用非自增主键（如果身份证号或学号等），由于每次插入主键的值近似于随机，因此每次新纪录都要被插到现有索引页得中间某个位置，此时MySQL不得不为了将新记录插到合适位置而移动数据，甚至目标页面可能已经被回写到磁盘上而从缓存中清掉，此时又要从磁盘上读回来，这增加了很多开销，同时频繁的移动、分页操作造成了大量的碎片，得到了不够紧凑的索引结构，后续不得不通过OPTIMIZE TABLE来重建表并优化填充页面。
mysql在频繁的更新、删除操作，会产生碎片。而含碎片比较大的表，查询效率会降低。此时需对表进行优化，这样才会使查询变得更有效率。

21.事务

(1)数据库事务ACID原则

原子性(Atomicity)：是指一个事务要么全部执行，要么不执行，也就是说一个事务不可能只执行了一半就停止了。
一致性(Consistency)： 一致性是指数据库的完整性约束没有被破坏，在事务执行前后都是合法的数据状态。这里的一致可以表示数据库自身的约束没有被破坏，比如某些字段的唯一性约束、字段长度约束等等；还可以表示各种实际场景下的业务约束，比如上面转账操作，一个账户减少的金额和另一个账户增加的金额一定是一样的。
独立性(Isolation）：事务的独立性也有称作隔离性，是指两个以上的事务不会出现交错执行的状态。因为这样可能会导致数据不一致。
持久性(Durability）：事务的持久性是指事务执行成功以后，该事务对数据库所作的更改便是持久的保存在数据库之中，不会无缘无故的回滚。

(2)事务的状态：
活动的（active） 当事务对应的数据库操作正在执行过程中，则该事务处于活动状态。
部分提交的（partially committed） 当事务中的最后一个操作执行完成，但还未将变更刷新到磁盘时，则该事务处于部分提交状态。
失败的（failed） 当事务处于活动或者部分提交状态时，由于某些错误导致事务无法继续执行，则事务处于失败状态。
中止的（aborted） 当事务处于失败状态，且回滚操作执行完毕，数据恢复到事务执行之前的状态时，则该事务处于中止状态。
提交的（committed） 当事务处于部分提交状态，并且将修改过的数据都同步到磁盘之后，此时该事务处于提交状态。

(3)事务隔离级别

查看事务隔离级别使用select @@tx_isolation
READ UNCOMMITTED：未提交读。存在脏读、不可重复读、幻读问题。
READ COMMITTED：已提交读。存在不可重复读、幻读问题。
REPEATABLE READ：可重复读。存在幻读问题。
SERIALIZABLE：串行化。无问题。

(4)事务并发执行遇到的问题

脏读（Dirty Read） 脏读是指一个事务读到了其它事务未提交的数据。
不可重复读（Non-Repeatable Read） 不可重复读指的是在一个事务执行过程中，读取到其它事务已提交的数据，导致两次读取的结果不一致。
幻读（Phantom） 幻读是指的是在一个事务执行过程中，读取到了其他事务新插入数据，导致两次读取的结果不一致。不可重复读和幻读的区别在于不可重复读是读到的是其他事务修改或者删除的数据，而幻读读到的是其它事务新插入的数据。

(5)MVCC

MVCC(Multi Version Concurrency Control)，中文名是多版本并发控制，简单来说就是通过维护数据历史版本，从而解决并发访问情况下的读一致性问题。

N.参考

(1)[Java面试题之数据库三范式是什么？](https://www.cnblogs.com/marsitman/p/10162231.html)

(2)[三张图搞透第一范式(1NF)、第二范式(2NF)和第三范式(3NF)的区别](https://blog.csdn.net/weixin_43971764/article/details/88677688)

(3)[真正理解Mysql的四种隔离级别](https://www.jianshu.com/p/8d735db9c2c0)

(4)[【178期】面试官：谈谈在做项目过程中，你是是如何进行SQL优化的](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247486029&idx=1&sn=a2ae60fb8a326fded471dfa8411d5d52&chksm=e80dbc3bdf7a352d5875b6f82668e5e3a9bb37cb72167a8fbbce70bd47d8bcd917fd28deb5f5&scene=21#wechat_redirect)

(5)[MySQL幻读的详解、实例及解决办法](https://segmentfault.com/a/1190000016566788?utm_source=tag-newest)

(6)[聊聊MVCC和Next-key Locks](https://juejin.im/post/6844903842505555981)

(7)[【57期】面试官问，MySQL建索引需要遵循哪些原则呢？](https://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247484196&idx=1&sn=d1a082c4eaa6ca9c35bfb220f2f9d0a0&chksm=e80db552df7a3c44fcca59444fc6246ab169d165634fbe3b2f9e2465093a96e662c01a62a91e&scene=21#wechat_redirect)

(8)[MySQL索引与查询优化](https://juejin.im/post/6844903818056974350)

(9)[最官方的mysql explain type字段解读](https://mengkang.net/1124.html)

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

3.流式查询

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