---
layout: post
title: "大数据系列 -- Spark"
description: 大数据系列 -- Spark
modified: 2022-01-01
category: BigData
tags: [BigData]
---

# 一、安装

    docker run -d --name=spark_single --privileged hdfs_proto /usr/sbin/init
    docker cp E:\software\bigdata\spark-3.1.2-bin-without-hadoop.tgz spark_single:/
    docker exec -it spark_single bash
    tar -zxf spark-3.1.2-bin-without-hadoop.tgz
    mv spark-3.1.2-bin-without-hadoop /usr/local/spark
    chown -R hadoop /usr/local/spark

    # 配置环境变量，退出docker容器并重新进入才生效！！
    echo "export SPARK_HOME=/usr/local/spark" >> /etc/bashrc
    echo "export PATH=$PATH:$SPARK_HOME/bin" >> /etc/bashrc
    echo "export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/pyspark.zip:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip" >> /etc/bashrc

    cd /usr/local/spark/conf
    cp spark-env.sh.template spark-env.sh
    vim spark-env.sh
    # 添加如下
    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath)
    
    cd ~
    spark-shell
    :quit

    # 安装python3，否则pyspark无法运行，root用户
    yum -y install python36

    docker stop spark_single
    docker commit spark_single spark_proto

    docker run -d --name=spark_single2 -p 50022:22 -p 57077:7077 -p 58080:8080 --privileged spark_proto /usr/sbin/init

# 二、基础

1.执行过程

编写Spark应用与之前实现在Hadoop上的其他数据流语言类似。代码写入一个惰性求值的驱动程序（driver program）中，通过一个动作（action），驱动代码被分发到集群上，由各个RDD分区上的worker来执行。
然后结果会被发送回驱动程序进行聚合或编译。本质上，驱动程序创建一个或多个RDD，调用操作来转换RDD，然后调用动作处理被转换后的RDD。
定义一个或多个RDD，可以通过获取存储在磁盘上的数据（HDFS，Cassandra，HBase，Local Disk），并行化内存中的某些集合，转换（transform）一个已存在的RDD，或者，缓存或保存。
通过传递一个闭包（函数）给RDD上的每个元素来调用RDD上的操作。Spark提供了除了Map和Reduce的80多种高级操作。
使用结果RDD的动作（action）（如count、collect、save等）。动作将会启动集群上的计算。
当Spark在一个worker上运行闭包时，闭包中用到的所有变量都会被拷贝到节点上，但是由闭包的局部作用域来维护。
Spark提供了两种类型的共享变量，这些变量可以按照限定的方式被所有worker访问。广播变量会被分发给所有worker，但是是只读的。累加器这种变量，worker可以使用关联操作来“加”，通常用作计数器。

2.RDD操作

Transformation：转换的作用是从现有数据集创建新数据集。转换是惰性的，因为它们仅在动作需要将结果返回到驱动程序时才计算。

    map(func) - 它返回一个新的分布式数据集， 该数据集是通过函数func传递源的每个元素而形成的。
    filter(func) - 它返回一个新数据集， 该数据集是通过选择函数func返回true的源元素而形成的。
    flatMap(func) - 这里，每个输入项可以映射到零个或多个输出项， 因此函数func应该返回序列而不是单个项。
    mapPartitions(func) - 它类似于map，但是在RDD的每个分区(块)上单独运行， 因此当在类型T的RDD上运行时， func必须是Iterator <T> => Iterator <U>类型。
    mapPartitionsWithIndex(func) - 它类似于mapPartitions，它为func提供了一个表示分区索引的整数值，因此当在类型T的RDD上运行时，func必须是类型(Int，Iterator <T>)=> Iterator <U>。
    sample(withReplacement, fraction, seed) - 它使用给定的随机数生成器种子对数据的分数部分进行采样，有或没有替换。
    union(otherDataset) - 它返回一个新数据集，其中包含源数据集和参数中元素的并集。
    intersection(otherDataset) - 它返回一个新的RDD，其中包含源数据集和参数中的元素的交集。
    distinct([numPartitions])) - 它返回一个新数据集，其中包含源数据集的不同元素。
    groupByKey([numPartitions]) - 它接收键值对(K，V)作为输入，基于键对值进行分组，并生成(K，Iterable)对的数据集作为输出。
    reduceByKey(func, [numPartitions]) - 当调用(K，V)对的数据集时，返回(K，V)对的数据集，其中使用给定的reduce函数func聚合每个键的值，该函数必须是类型(V，V)=>V。
    aggregateByKey(zeroValue)(seqOp, combOp, [numPartitions]) - 当调用(K，V)对的数据集时，返回(K，U)对的数据集，其中使用给定的组合函数和中性“零”值聚合每个键的值。
    sortByKey([ascending], [numPartitions]) - 它接收键值对(K，V)作为输入，按升序或降序对元素进行排序，并按顺序生成数据集。
    join(otherDataset, [numPartitions])-当调用类型(K，V)和(K，W)的数据集时，返回(K，(V，W))对的数据集以及每个键的所有元素对。通过leftOuterJoin，rightOuterJoin和fullOuterJoin支持外连接。
    cogroup(otherDataset, [numPartitions])-当调用类型(K，V)和(K，W)的数据集时，返回(K，(Iterable，Iterable))元组的数据集。此操作也称为groupWith。
    cartesian(otherDataset)-生成两个数据集的笛卡尔积，并返回所有可能的对组合。
    pipe(command, [envVars])-通过shell命令管道RDD的每个分区，例如， 一个Perl或bash脚本。
    coalesce(numPartitions)-它将RDD中的分区数减少到numPartitions。
    repartition(numPartitions) -它随机重新调整RDD中的数据，以创建更多或更少的分区，并在它们之间进行平衡。
    repartitionAndSortWithinPartitions(partitioner) - 它根据给定的分区器对RDD进行重新分区，并在每个生成的分区中键对记录进行排序。

Action：动作的作用是在对数据集运行计算后将值返回给驱动程序。

    reduce(func)它使用函数func(它接受两个参数并返回一个)来聚合数据集的元素。该函数应该是可交换的和关联的，以便可以并行正确计算。
    collect()它将数据集的所有元素作为数组返回到驱动程序中。在过滤器或其他返回足够小的数据子集的操作之后，这通常很有用。
    count()它返回数据集中的元素数。
    first()它返回数据集的第一个元素(类似于take(1))。
    take(n)它返回一个包含数据集的前n个元素的数组。
    takeSample(withReplacement, num, [seed])它返回一个数组，其中包含数据集的num个元素的随机样本，有或没有替换，可选地预先指定随机数生成器种子。
    takeOrdered(n, [ordering])它使用自然顺序或自定义比较器返回RDD的前n个元素。
    saveAsTextFile(path)它用于将数据集的元素作为文本文件(或文本文件集)写入本地文件系统，HDFS或任何其他Hadoop支持的文件系统的给定目录中。
    saveAsSequenceFile(path)它用于在本地文件系统，HDFS或任何其他Hadoop支持的文件系统中的给定路径中将数据集的元素编写为Hadoop SequenceFile。
    saveAsObjectFile(path)它用于使用Java序列化以简单格式编写数据集的元素，然后可以使用SparkContext.objectFile()加载。
    countByKey()它仅适用于类型(K，V)的RDD。因此，它返回(K，Int)对的散列映射与每个键的计数。
    foreach(func)它在数据集的每个元素上运行函数func以获得副作用，例如更新累加器或与外部存储系统交互。

3.RDD持久化

在Spark中，RDD采用惰性求值的机制，每次遇到行动操作，都会从头开始执行计算。如果整个Spark程序中只有一次行动操作，这当然不会有什么问题。
但是，在一些情形下，我们需要多次调用不同的行动操作，这就意味着，每次调用行动操作，都会触发一次从头开始的计算。这对于迭代计算而言，代价是很大的，迭代计算经常需要多次重复使用同一组数据。
可以通过持久化（缓存）机制避免这种重复计算的开销。可以使用persist()方法对一个RDD标记为持久化，之所以说“标记为持久化”，是因为出现persist()语句的地方，并不会马上计算生成RDD并把它持久化，而是要等到遇到第一个行动操作触发真正计算以后，才会把计算结果进行持久化，持久化后的RDD将会被保留在计算节点的内存中被后面的行动操作重复使用。

Spark通过在操作中将其持久保存在内存中，提供了一种处理数据集的便捷方式。在持久化RDD的同时，每个节点都存储它在内存中计算的任何分区。也可以在该数据集的其他任务中重用它们。
我们可以使用persist()或cache()方法来标记要保留的RDD。Spark的缓存是容错的。在任何情况下，如果RDD的分区丢失，它将使用最初创建它的转换自动重新计算。
存在可用于存储持久RDD的不同存储级别。通过将StorageLevel对象(Scala，Java，Python)传递给persist()来使用这些级别。但是，cache()方法用于默认存储级别，即StorageLevel.MEMORY_ONLY。

    MEMORY_ONLY 它将RDD存储为JVM中的反序列化Java对象。这是默认级别。如果RDD不适合内存，则每次需要时都不会缓存和重新计算某些分区。
    MEMORY_AND_DISK 它将RDD存储为JVM中的反序列化Java对象。如果RDD不适合内存，请存储不适合的分区到磁盘，并在需要时从那里读取它们。
    MEMORY_ONLY_SER 它将RDD存储为序列化Java对象(即每个分区一个字节的数组)。这通常比反序列化的对象更节省空间。
    MEMORY_AND_DISK_SER 它类似于MEMORY_ONLY_SER，但是将内存中不适合的分区溢出到磁盘而不是重新计算它们。
    DISK_ONLY 它仅将RDD分区存储在磁盘上。
    MEMORY_ONLY_2, MEMORY_AND_DISK_2 它与上面的级别相同，但复制两个群集节点上的每个分区。
    OFF_HEAP 它类似于MEMORY_ONLY_SER，但将数据存储在堆外内存中。必须启用堆外内存。

4.共享变量

在默认情况下，当Spark在集群的多个不同节点的多个任务上并行运行一个函数时，它会把函数中涉及到的每个变量，在每个任务上都生成一个副本。但是，有时候，需要在多个任务之间共享变量，或者在任务（Task）和任务控制节点（Driver Program）之间共享变量。
为了满足这种需求，Spark提供了两种类型的变量：广播变量（broadcast variables）和累加器（accumulators）。广播变量用来把变量在所有节点的内存之间进行共享。累加器则支持在所有不同节点之间进行累加计算（比如计数或者求和）。

(1)广播变量：

广播变量（broadcast variables）允许程序开发人员在每个机器上缓存一个只读的变量，而不是为机器上的每个任务都生成一个副本。
通过这种方式，就可以非常高效地给每个节点（机器）提供一个大的输入数据集的副本。Spark的“动作”操作会跨越多个阶段（stage），对于每个阶段内的所有任务所需要的公共数据，Spark都会自动进行广播。
通过广播方式进行传播的变量，会经过序列化，然后在被任务使用时再进行反序列化。
这就意味着，显式地创建广播变量只有在下面的情形中是有用的：当跨越多个阶段的那些任务需要相同的数据，或者当以反序列化方式对数据进行缓存是非常重要的。
可以通过调用SparkContext.broadcast(v)来从一个普通变量v中创建一个广播变量。这个广播变量就是对普通变量v的一个包装器，通过调用value方法就可以获得这个广播变量的值。

    broadcastVar = sc.broadcast([1, 2, 3])
    broadcastVar.value

(2)累加器：

累加器是仅仅被相关操作累加的变量，通常可以被用来实现计数器（counter）和求和（sum）。Spark原生地支持数值型（numeric）的累加器，程序开发人员可以编写对新类型的支持。如果创建累加器时指定了名字，则可以在Spark UI界面看到，这有利于理解每个执行阶段的进程。
一个数值型的累加器，可以通过调用SparkContext.accumulator()来创建。运行在集群中的任务，就可以使用add方法来把数值累加到累加器上，但是，这些任务只能做累加操作，不能读取累加器的值，只有任务控制节点（Driver Program）可以使用value方法来读取累加器的值。

    accum = sc.accumulator(0)
    sc.parallelize([1, 2, 3, 4]).foreach(lambda x : accum.add(x))
    accum.value

# 三、实例

1.在容器中运行代码，新建~/test1.py

    from pyspark import SparkContext
    sc = SparkContext( 'local', 'test')
    logFile = "file:///usr/local/spark/README.md"
    logData = sc.textFile(logFile, 2).cache()
    numAs = logData.filter(lambda line: 'a' in line).count()
    numBs = logData.filter(lambda line: 'b' in line).count()
    print('Lines with a: %s, Lines with b: %s' % (numAs, numBs))

两种方式运行。

方式一：

    # hadoop用户执行
    spark-submit ~/test1.py 2>&1 | grep "Lines with a"

方式二：

    # root用户配置，否则可能出现“ModuleNotFoundError: No module named 'pyspark'”
    echo "export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/pyspark.zip:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip" >> /etc/bashrc

    # hadoop用户执行
    python ~/test1.py

2.词频统计，新建~/test2.py

    from pyspark import SparkContext
    sc = SparkContext( 'local', 'test')
    logFile = "file:///home/hadoop/wordcount/data.txt"
    logData = sc.textFile(logFile, 2).cache()
    wordCount = logData.flatMap(lambda line: line.split(" ")).map(lambda word: (word,1)).reduceByKey(lambda a, b : a + b)
    print(wordCount.collect())

3.PyCharm远程调试，配置SSH Interpreter。

    from pyspark import SparkContext
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test') #或者sc = SparkContext( 'local', 'test')，6504c1d2a2ca为容器ID
    logFile = "file:///home/hadoop/hello.txt"
    logData = sc.textFile(logFile, 2).cache()
    wordCount = logData.flatMap(lambda line: line.split(" ")).map(lambda word: (word,1)).reduceByKey(lambda a, b : a + b)
    print(wordCount.collect())

    // 注意是启动spark的命令的是
    cd /usr/local/spark/sbin
    sh ./start-all.sh

    // 并不是直接start-all.sh，这样会执行Hadoop的启动。注意区分！

    // 重要！！run configuration的环境变量中添加配置：PYTHONUNBUFFERED=1;SPARK_HOME=/usr/local/spark;PYTHONPATH=/usr/local/spark/python:/usr/local/spark/python/lib/pyspark.zip:/usr/local/spark/python/lib/py4j-0.10.9-src.zip

4.Spark访问HBase

Spark运行在172.17.0.3，HBase运行在172.17.0.2。并且两个容器中都要start-dfs.sh启动Hadoop。
参照[Spark2.1.0+入门：读写HBase数据(Python版)](http://dblab.xmu.edu.cn/blog/1715-2/)，将172.17.0.2中相关jar包拷贝到172.17.0.3中。
    
    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath):/usr/local/spark/jars/hbase/*

test_hbase.py：

    from pyspark import SparkContext

    # http://dblab.xmu.edu.cn/blog/1715-2/
    # spark运行在172.0.0.3，hbase运行在172.0.0.2。
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test')

    host = '172.17.0.2'
    table = 'student'
    # 从HBase自带的ZK获取HBase相关信息
    conf = {"hbase.zookeeper.quorum": host, "hbase.mapreduce.inputtable": table}

    # 用于类型转换
    keyConv = "org.apache.spark.examples.pythonconverters.ImmutableBytesWritableToStringConverter"
    valueConv = "org.apache.spark.examples.pythonconverters.HBaseResultToStringConverter"

    hbase_rdd = sc.newAPIHadoopRDD("org.apache.hadoop.hbase.mapreduce.TableInputFormat",
                                   "org.apache.hadoop.hbase.io.ImmutableBytesWritable",
                                   "org.apache.hadoop.hbase.client.Result",
                                   keyConverter=keyConv,
                                   valueConverter=valueConv,
                                   conf=conf)
    count = hbase_rdd.count()
    hbase_rdd.cache()
    output = hbase_rdd.collect()
    for (k, v) in output:
        print(k, v)

注意在172.17.0.3的/etc/hosts文件中加上：

    172.17.0.2      1e79fcc0828a

# 四、Spark SQL

1.基础

    from pyspark import SparkContext
    from pyspark.sql.session import SparkSession
    
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test')
    spark=SparkSession(sc)
    df = spark.read.json("file:///home/hadoop/people.json")
    df.show()
    
    # 打印模式信息
    df.printSchema()
    # 选择多列
    df.select(df.name, df.age + 1).show()
    # 条件过滤
    df.filter(df.age > 20).show()
    # 分组聚合
    df.groupBy("age").count().show()
    # 排序
    df.sort(df.age.desc()).show()
    # 多列排序
    df.sort(df.age.desc(), df.name.asc()).show()
    # 对列进行重命名
    df.select(df.name.alias("username"), df.age).show()

2.访问MySQL，注意先拷贝mysql-connector-java-8.0.27.jar到/usr/local/spark/jars/

    from pyspark import SparkContext
    from pyspark.sql.session import SparkSession

    sc = SparkContext('spark://6504c1d2a2ca:7077', 'test')
    spark = SparkSession(sc)

    prop = {'user': 'root',
            'password': '123456',
            'driver': 'com.mysql.cj.jdbc.Driver'}
    url = 'jdbc:mysql://172.17.0.3:3306/spark?useSSL=false'
    # 读取表
    df = spark.read.jdbc(url=url, table='student', properties=prop)
    df.show()
    df.printSchema()

# 五、Spark Streaming

1.基础

Spark Streaming最主要的抽象是DStream（Discretized Stream，离散化数据流），表示连续不断的数据流。
在内部实现上，Spark Streaming的输入数据按照时间片（如1秒）分成一段一段的DStream，每一段数据转换为Spark中的RDD，并且对DStream的操作都最终转变为对相应的RDD的操作。
例如进行单词统计时，每个时间片的数据（存储句子的RDD）经flatMap操作，生成了存储单词的RDD。
整个流式计算可根据业务的需求对这些中间的结果进一步处理，或者存储到外部设备中。
Spark Streaming和Storm最大的区别在于，Spark Streaming无法实现毫秒级的流计算，而Storm可以实现毫秒级响应。
Spark Streaming无法实现毫秒级的流计算，是因为其将流数据按批处理窗口大小（通常在0.5~2秒之间）分解为一系列批处理作业，在这个过程中，会产生多个Spark 作业，且每一段数据的处理都会经过Spark DAG图分解、任务调度过程，因此，无法实现毫秒级响应。
Spark Streaming难以满足对实时性要求非常高（如高频实时交易）的场景，但足以胜任其他流式准实时计算场景。相比之下，Storm处理的单位为Tuple，只需要极小的延迟。

2.DStream转换操作包括无状态转换和有状态转换。
无状态转换：每个批次的处理不依赖于之前批次的数据。例如文件流实例。
有状态转换：当前批次的处理需要使用之前批次的数据或者中间结果。有状态转换包括基于滑动窗口的转换和追踪状态变化的转换(updateStateByKey)。

3.文件流实例

    from operator import add
    from pyspark import SparkContext
    from pyspark.streaming import StreamingContext
    
    sc = SparkContext('spark://793c4b434a2c:7077', 'test')
    ssc = StreamingContext(sc, 1)
    lines = ssc.textFileStream('file:///home/hadoop/spark/streaming')
    words = lines.flatMap(lambda line: line.split(' '))
    wordCounts = words.map(lambda x : (x,1)).reduceByKey(add)
    wordCounts.pprint()
    # 开始自动进入循环监听状态，监听新放入文件夹中的文件。
    ssc.start()
    ssc.awaitTermination()

4.套接字实例，并updateStateByKey

    import sys
    from pyspark import SparkContext
    from pyspark.streaming import StreamingContext
    
    if __name__ == "__main__":
        if len(sys.argv) != 3:
            exit(-1)
    
        sc = SparkContext('spark://793c4b434a2c:7077', 'test')
        ssc = StreamingContext(sc, 1)
        ssc.checkpoint("file:///home/hadoop/spark/streaming/log/")
    
        # RDD with initial state (key, value) pairs
        initialStateRDD = sc.parallelize([(u'hello', 1), (u'world', 1)])
    
        def updateFunc(new_values, last_sum):
            return sum(new_values) + (last_sum or 0)
    
        lines = ssc.socketTextStream(sys.argv[1], int(sys.argv[2]))
        running_counts = lines.flatMap(lambda line: line.split(" ")) \
            .map(lambda word: (word, 1)) \
            .updateStateByKey(updateFunc, initialRDD=initialStateRDD)
    
        running_counts.pprint()
    
        ssc.start()
        ssc.awaitTermination()

开一个新窗口，使用如下命令，在这个窗口中手动输入一些单词

    nc -lk 9999

# 六、MLlib

1.基于大数据的机器学习

传统的机器学习算法，由于技术和单机存储的限制，只能在少量数据上使用。即以前的统计/机器学习依赖于数据抽样。但实际过程中样本往往很难做好随机，导致学习的模型不是很准确，在测试数据上的效果也可能不太好。
随着 HDFS(Hadoop Distributed File System) 等分布式文件系统出现，存储海量数据已经成为可能。在全量数据上进行机器学习也成为了可能，这顺便也解决了统计随机性的问题。然而，由于MapReduce自身的限制，使得使用MapReduce来实现分布式机器学习算法非常耗时和消耗磁盘IO。
因为通常情况下机器学习算法参数学习的过程都是迭代计算的，即本次计算的结果要作为下一次迭代的输入，这个过程中，如果使用MapReduce，我们只能把中间结果存储磁盘，然后在下一次计算的时候从新读取，这对于迭代 频发的算法显然是致命的性能瓶颈。

在大数据上进行机器学习，需要处理全量数据并进行大量的迭代计算，这要求机器学习平台具备强大的处理能力。Spark立足于内存计算，天然的适应于迭代式计算。即便如此，对于普通开发者来说，实现一个分布式机器学习算法仍然是一件极具挑战的事情。
幸运的是，Spark提供了一个基于海量数据的机器学习库，它提供了常用机器学习算法的分布式实现，开发者只需要有Spark基础并且了解机器学习算法的原理，以及方法相关参数的含义，就可以轻松的通过调用相应的 API 来实现基于海量数据的机器学习过程。
其次，Spark-Shell的即席查询也是一个关键。算法工程师可以边写代码边运行，边看结果。spark提供的各种高效的工具正使得机器学习过程更加直观便捷。比如通过sample函数，可以非常方便的进行抽样。
当然，Spark发展到后面，拥有了实时批计算，批处理，算法库，SQL、流计算等模块等，基本可以看做是全平台的系统。把机器学习作为一个模块加入到Spark中，也是大势所趋。

2.MLlib

MLlib是Spark的机器学习（Machine Learning）库，旨在简化机器学习的工程实践工作，并方便扩展到更大规模。MLlib由一些通用的学习算法和工具组成，包括分类、回归、聚类、协同过滤、降维等，同时还包括底层的优化原语和高层的管道API。具体来说，其主要包括以下几方面的内容：

    算法工具：常用的学习算法，如分类、回归、聚类和协同过滤；
    特征化工具：特征提取、转化、降维，和选择工具；
    管道(Pipeline)：用于构建、评估和调整机器学习管道的工具;
    持久性：保存和加载算法，模型和管道;
    实用工具：线性代数，统计，数据处理等工具。

Spark 机器学习库从 1.2 版本以后被分为两个包：

    spark.mllib包含基于RDD的原始算法API。Spark MLlib 历史比较长，在1.0 以前的版本即已经包含了，提供的算法实现都是基于原始的RDD。
    spark.ml则提供了基于DataFrames高层次的API，可以用来构建机器学习工作流（PipeLine）。ML Pipeline 弥补了原始 MLlib 库的不足，向用户提供了一个基于DataFrame的机器学习工作流式API套件。

3.几个重要概念：

DataFrame：使用SparkSQL中的DataFrame作为数据集，它可以容纳各种数据类型。较之RDD，包含了schema信息，更类似传统数据库中的二维表格。它被MLPipeline用来存储源数据。例如，DataFrame中的列可以是存储的文本，特征向量，真实标签和预测的标签等。
Transformer：翻译成转换器，是一种可以将一个DataFrame转换为另一个DataFrame的算法。比如一个模型就是一个Transformer。它可以把一个不包含预测标签的测试数据集DataFrame打上标签，转化成另一个包含预测标签的DataFrame。技术上，Transformer实现了一个方法transform（），它通过附加一个或多个列将一个DataFrame转换为另一个DataFrame。
Estimator：翻译成估计器或评估器，它是学习算法或在训练数据上的训练方法的概念抽象。在Pipeline里通常是被用来操作DataFrame数据并生产一个Transformer。从技术上讲，Estimator实现了一个方法fit（），它接受一个DataFrame并产生一个转换器。如一个随机森林算法就是一个Estimator，它可以调用fit（），通过训练特征数据而得到一个随机森林模型。
Parameter：Parameter被用来设置Transformer或者Estimator的参数。现在，所有转换器和估计器可共享用于指定参数的公共API。ParamMap是一组（参数，值）对。
PipeLine：翻译为工作流或者管道。工作流将多个工作流阶段（转换器和估计器）连接在一起，形成机器学习的工作流，并获得结果输出。

4.工作流实例，查找出所有包含”spark”的句子

    from pyspark import SparkContext
    from pyspark.sql.session import SparkSession
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    
    
    sc = SparkContext( 'spark://6504c1d2a2ca:7077', 'test')
    spark = SparkSession(sc)
    
    # Prepare training documents from a list of (id, text, label) tuples.
    # 我们的目的是查找出所有包含”spark”的句子，即将包含”spark”的句子的标签设为1，没有”spark”的句子的标签设为0。
    training = spark.createDataFrame([
        (0, "a b c d e spark", 1.0),
        (1, "b d", 0.0),
        (2, "spark f g h", 1.0),
        (3, "hadoop mapreduce", 0.0)
    ], ["id", "text", "label"])
    
    # 定义Pipeline中的各个工作流阶段PipelineStage，包括转换器和评估器，具体的，包含tokenizer, hashingTF和lr三个步骤。
    #  Tokenizer.transform（）方法将原始文本文档拆分为单词，向DataFrame添加一个带有单词的新列。
    #  HashingTF.transform（）方法将字列转换为特征向量，向这些向量添加一个新列到DataFrame。
    #  现在，由于LogisticRegression是一个Estimator，Pipeline首先调用LogisticRegression.fit（）产生一个LogisticRegressionModel。 如果流水线有更多的阶段，则在将DataFrame传递到下一个阶段之前，将在DataFrame上调用LogisticRegressionModel的transform（）方法。
    
    tokenizer = Tokenizer(inputCol="text", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.001)
    # 有了这些处理特定问题的转换器和评估器，接下来就可以按照具体的处理逻辑有序的组织PipelineStages 并创建一个Pipeline。
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    # 现在构建的Pipeline本质上是一个Estimator，在它的fit（）方法运行之后，它将产生一个PipelineModel，它是一个Transformer。
    model = pipeline.fit(training)
    
    # 我们可以看到，model的类型是一个PipelineModel，这个管道模型将在测试数据的时候使用。所以接下来，我们先构建测试数据。
    test = spark.createDataFrame([
        (4, "spark i j k"),
        (5, "l m n"),
        (6, "spark hadoop spark"),
        (7, "apache hadoop")
    ], ["id", "text"])
    
    # 然后，我们调用我们训练好的PipelineModel的transform（）方法，让测试数据按顺序通过拟合的工作流，生成我们所需要的预测结果。
    prediction = model.transform(test)
    selected = prediction.select("id", "text", "probability", "prediction")
    
    for row in selected.collect():
        rid, text, prob, prediction = row
        print("(%d, %s) --> prob=%s, prediction=%f" % (rid, text, str(prob), prediction))

5.

# 七、参考

1.[易百Spark教程](https://www.yiibai.com/spark)

2.[w3cschool Spark编程指南](https://www.w3cschool.cn/spark/)

3.[w3cschool spark sql](https://www.w3cschool.cn/spark_sql/)

4.[RDD Programming Guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html)

5.[Spark编程指南简体中文版](https://endymecy.gitbooks.io/spark-programming-guide-zh-cn/content/)

6.[子雨大数据之Spark入门教程(Python版)](http://dblab.xmu.edu.cn/blog/1709-2/)

7.[pyspark对Mysql数据库进行读写](https://zhuanlan.zhihu.com/p/136777424)
