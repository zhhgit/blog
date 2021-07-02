---
layout: post
title: "后端开发面试题 -- Elasticsearch篇"
description: 后端开发面试题 -- Elasticsearch篇
modified: 2021-01-01
category: Interview
tags: [Interview]
---

1.为什么不用关系型数据库

如果用像MySQL这样的RDBMS来存储古诗的话，我们应该会去使用这样的SQL去查询，这种我们称为顺序扫描法，需要遍历所有的记录进行匹配。
不但效率低，而且不符合我们搜索时的期望，比如我们在搜索“ABCD"这样的关键词时，通常还希望看到"A","AB","CD",“ABC”的搜索结果。

    select name from poems where content like "%前%";

2.搜索引擎原理

(1)内容爬取，停顿词过滤，比如一些无用的像"的"，“了”之类的语气词/连接词。
(2)内容分词，提取关键词。
(3)根据关键词建立倒排索引。
(4)用户输入关键词进行搜索。

3.倒排索引

term：关键词。
postings list：所有包含特定term文档的id的集合。再说id的范围，在存储数据的时候，在每一个shard里面，ES会将数据存入不同的segment，这是一个比shard 更小的分片单位，这些segment会定期合并。在每一个segment里面都会保存最多2^31个文档，每个文档被分配一个唯一的id，从0到(2^31)-1。

在搜索引擎中，每个文档都有一个对应的文档ID，文档内容被表示为一系列关键词的集合。例如，文档1经过分词，提取了20个关键词，每个关键词都会记录它在文档中出现的次数和出现位置。
那么，倒排索引就是关键词到文档ID的映射，每个关键词都对应着一系列的文件，这些文件中都出现了关键词。例如：

    DocId	Doc
    1	谷歌地图之父跳槽 Facebook
    2	谷歌地图之父加盟 Facebook
    3	谷歌地图创始人拉斯离开谷歌加盟 Facebook
    4	谷歌地图之父跳槽 Facebook 与 Wave 项目取消有关
    5	谷歌地图之父拉斯加盟社交网站 Facebook

对文档进行分词之后，得到以下倒排索引。

    WordId	Word	DocIds
    1	谷歌	1, 2, 3, 4, 5
    2	地图	1, 2, 3, 4, 5
    3	之父	1, 2, 4, 5
    4	跳槽	1, 4
    5	Facebook	1, 2, 3, 4, 5
    6	加盟	2, 3, 5
    7	创始人	3
    8	拉斯	3, 5
    9	离开	3
    10	与	4

另外，实用的倒排索引还可以记录更多的信息，比如文档频率信息，表示在文档集合中有多少个文档包含某个单词。
有了倒排索引，搜索引擎可以很方便地响应用户的查询。比如用户输入查询 Facebook ，搜索系统查找倒排索引，从中读出包含这个单词的文档，这些文档就是提供给用户的搜索结果。
要注意倒排索引的两个重要细节：(1)倒排索引中的所有词项对应一个或多个文档；(2)倒排索引中的词项根据字典顺序升序排列。

4.es写数据过程

客户端选择一个node发送请求过去，这个node就是coordinating node（协调节点）。
coordinating node对document进行路由，确定在那个shard，将请求转发给对应的node（有primary shard）。
实际的node上的primary shard处理请求，然后将数据同步到replica node。
coordinating node如果发现primary node和所有replica node都写完，就返回响应结果给客户端。
写请求是写入primary shard，然后同步给所有的replica shard。

5.es读数据过程

可以通过doc id 来查询，会根据doc id进行hash，判断出来当时把doc id分配到了哪个shard上面去，从那个shard去查询。
客户端发送请求到任意一个node，成为coordinate node。
coordinate node对doc id进行哈希路由，将请求转发到对应的node，此时会使用round-robin随机轮询算法，在primary shard以及其所有replica中随机选择一个，让读请求负载均衡。
接收请求的node返回document给coordinate node 。
coordinate node返回document给客户端。
读请求可以从primary shard或replica shard读取，采用的是随机轮询算法。

6.es搜索数据过程

es 最强大的是做全文检索，就是比如你有三条数据：

    java真好玩儿啊
    java好难学啊
    j2ee特别牛
    
你根据java关键词来搜索，将包含java的document给搜索出来。es就会给你返回：java真好玩儿啊，java好难学啊。
客户端发送请求到一个coordinate node。
协调节点将搜索请求转发到所有的shard对应的primary shard或replica shard，都可以。
query phase：每个shard将自己的搜索结果（其实就是一些doc id）返回给协调节点，由协调节点进行数据的合并、排序、分页等操作，产出最终结果。
fetch phase：接着由协调节点根据doc id去各个节点上拉取实际的document数据，最终返回给客户端。

搜索时，Lucene会搜索所有的segment然后将每个segment的搜索结果返回，最后合并呈现给客户。Lucene的一些特性使得这个过程非常重要：
Segments是不可变的（immutable）。
Delete? 当删除发生时，Lucene做的只是将其标志位置为删除，但是文件还是会在它原来的地方，不会发生改变。
Update? 所以对于更新来说，本质上它做的工作是：先删除，然后重新索引（Re-index）。
随处可见的压缩，Lucene非常擅长压缩数据，基本上所有教科书上的压缩方式，都能在Lucene中找到。
缓存所有的所有，Lucene也会将所有的信息做缓存，这大大提高了它的查询效率。

ElasticSearch从Shard中搜索的过程与Lucene Segment中搜索的过程类似。
与在Lucene Segment中搜索不同的是，Shard可能是分布在不同Node上的，所以在搜索与返回结果时，所有的信息都会通过网络传输。1次搜索查找2个shard ＝ 2次分别搜索shard

7.写数据底层原理

数据先写入内存buffer，在buffer里的时候数据是搜索不到的；如果buffer快满了，或者到一定时间(1秒)，就会将内存buffer数据refresh到一个新的segment file中，但是此时数据不是直接进入segment file磁盘文件，而是先进入os cache。数据写入segment file之后，同时就建立好了倒排索引。
操作系统里面，磁盘文件其实都有一个东西，叫做os cache ，即操作系统缓存，就是说数据写入磁盘文件之前，会先进入os cache ，先进入操作系统级别的一个内存缓存中去。到了os cache 数据就能被搜索到（所以我们才说es从写入到能被搜索到，中间有1s的延迟）。
如果buffer里面此时没有数据，那当然不会执行refresh操作，如果buffer里面有数据，默认1秒钟执行一次refresh操作，刷入一个新的segment file中。每秒钟会产生一个新的磁盘文件segment file，这个segment file中就存储最近1秒内buffer中写入的数据。
为什么叫es是准实时的？NRT (near real-time)。默认是每隔1秒 refresh一次的，所以es是准实时的，因为写入的数据1秒之后才能被看到。可以通过es的restful api或者java api ，手动执行一次refresh 操作，就是手动将buffer中的数据刷入os cache 中，让数据立马就可以被搜索到。

同时将数据写入translog日志文件。translog大到一定程度，或者默认每隔30mins，会触发commit操作，将缓冲区的数据都flush到segment file磁盘文件中。
commit操作发生第一步，就是将buffer中现有数据refresh到os cache中去，清空buffer。然后，将一个commit point写入磁盘文件，里面标识着这个commit point对应的所有segment file，同时强行将os cache中目前所有的数据都fsync到磁盘文件中去。最后清空现有translog日志文件，重启一个translog，此时commit操作完成。
这个commit操作叫做flush 。默认30 分钟自动执行一次flush ，但如果translog过大，也会触发flush。flush操作就对应着commit的全过程，我们可以通过es api，手动执行flush 操作，手动将os cache中的数据fsync强刷到磁盘上去。
translog日志文件的作用是什么？你执行commit操作之前，数据要么是停留在buffer中，要么是停留在os cache中，无论是buffer还是os cache都是内存，一旦这台机器死了，内存中的数据就全丢了。所以需要将数据对应的操作写入一个专门的日志文件translog中，一旦此时机器宕机，再次重启的时候，es会自动读取translog日志文件中的数据，恢复到内存buffer和os cache中去。
translog其实也是先写入os cache 的，默认每隔5s刷一次到磁盘中去，所以默认情况下，可能有5秒的数据会仅仅停留在buffer或者translog文件的os cache中，如果此时机器挂了，会丢失5秒钟的数据。但是这样性能比较好，最多丢5秒的数据。也可以将translog设置成每次写操作必须是直接fsync到磁盘，但是性能会差很多。
如果面试官没有问你es丢数据的问题，可能会丢失数据的。有5秒的数据，停留在 buffer、translog os cache、segment file os cache 中，而不在磁盘上，此时如果宕机，会导致5秒的数据丢失。

8.删除/更新数据底层原理
如果是删除操作，commit的时候会生成一个.del文件，里面将某个doc标识为deleted状态，那么搜索的时候根据.del文件就知道这个doc是否被删除了。
如果是更新操作，就是将原来的 doc 标识为 deleted 状态，然后新写入一条数据。
buffer每refresh 一次，就会产生一个segment file ，所以默认情况下是1秒钟一个segment file，这样下来segment file会越来越多，此时会定期执行merge。每次merge 的时候，会将多个segment file合并成一个，同时这里会将标识为deleted的doc给物理删除掉，然后将新的segment file写入磁盘，这里会写一个commit point ，标识所有新的segment file，然后打开segment file供搜索使用，同时删除旧的segment file。

9.底层lucene

简单来说，lucene就是一个jar包，里面包含了封装好的各种建立倒排索引的算法代码。我们用Java开发的时候，引入lucene jar，然后基于lucene的api去开发就可以了。
通过lucene，我们可以将已有的数据建立索引，lucene会在本地磁盘上面，给我们组织索引的数据结构。

10.Segment内部数据结构

一个ElasticSearch的Shard本质上是一个Lucene Index。在Lucene索引里面有很多小的segment，我们可以把它们看成Lucene内部的mini-index。Segment内部数据结构：

    Inverted Index
    Stored Fields
    Document Values
    Cache

(1)Inverted Index：

主要包括两部分：一个有序的数据字典Dictionary（包括单词Term和它出现的频率）。与单词Term对应的Postings（即存在这个单词的文件）。当我们搜索的时候，首先将搜索的内容分解，然后在字典里找到对应Term，从而查找到与搜索相关的文件内容。
自动补全（AutoCompletion-Prefix）：如果想要查找以字母“c”开头的字母，可以简单的通过二分查找（Binary Search）在Inverted Index表中找到例如“choice”、“coming”这样的词（Term）。
昂贵的查找：如果想要查找所有包含“our”字母的单词，那么系统会扫描整个Inverted Index，这是非常昂贵的。
问题的转化：在此种情况下，如果想要做优化，那么我们面对的问题是如何生成合适的Term。对于以上诸如此类的问题，我们可能会有几种可行的解决方案：

    *suffix ->xiffus* 如果我们想以后缀作为搜索条件，可以为Term做反向处理。
    (60.6384, 6.5017) -> u4u8gyykk 对于GEO位置信息，可以将它转换为GEO Hash。
    123 -> {1-hundreds, 12-tens, 123} 对于简单的数字，可以为它生成多重形式的Term。

(2)Stored Field：

当我们想要查找包含某个特定标题内容的文件时，Inverted Index就不能很好的解决这个问题，所以Lucene提供了另外一种数据结构Stored Fields来解决这个问题。
本质上，Stored Fields是一个简单的键值对key-value。默认情况下，ElasticSearch会存储整个文件的JSON source。

(3)Document Values：

即使这样，我们发现以上结构仍然无法解决诸如：排序、聚合、facet，因为我们可能会要读取大量不需要的信息。
所以，另一种数据结构解决了此种问题：Document Values。这种结构本质上就是一个列式的存储，它高度优化了具有相同类型的数据的存储结构。
为了提高效率，ElasticSearch可以将索引下某一个Document Value全部读取到内存中进行操作，这大大提升访问速度，但是也同时会消耗掉大量的内存空间。
