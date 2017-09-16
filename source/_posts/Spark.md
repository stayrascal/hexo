---
title: Spark
date: 2017-09-15 00:08:59
tags:
---

#### Spark 特点
- 运行速度快: Spark拥有DAG执行引擎，支持在内存中对数据进行迭代计算, 速度是Hadoop MapReduce的10倍以上。
- 易用性好: Spark不仅支持Scala编写应用程序，而且支持Java和Python等语言进行编写，特别是Scala是一种高效、可拓展的语言，能够用简洁的代码处理较为复杂的处理工作。
- 通用性强: Spark生态圈即BDAS包含了Spark Core、Spark SQL、Spark Streaming、MLLib和GraphX等组件, 能够无缝的集成并提供一站式解决平台。
    * Spark Core提供内存计算框架
    * SparkStreaming的实时处理应用
    * Spark SQL的即席查询
    * MLlib或MLbase的机器学习
    * GraphX的图处理
- GraphX的图处理: Spark具有很强的适应性，能够读取HDFS、Cassandra、HBase、S3和Techyon为持久层读写原生数据，能够以Mesos、YARN和自身携带的Standalone作为资源管理器调度job，来完成Spark应用程序的计算。

#### Spark VS Hadoop
- Spark是在借鉴了MapReduce之上发展而来的，继承了其分布式并行计算的优点并改进了MapReduce明显的缺陷
    * Spark支持DAG图的分布式并行计算的编程框架，把中间数据放到内存中，减少了迭代过程中数据的落地，迭代运算效率高。MapReduce中计算结果需要落地，保存到磁盘上。
    * Spark容错性高：Spark引进了弹性分布式数据集RDD (Resilient Distributed Dataset) 的抽象，如果数据集一部分丢失，则可以基于数据衍生过程对它们进行重建，RDD计算时可以通过CheckPoint来实现容错。
    * Spark更加通用：Spark提供的数据集操作类型有很多种，大致分为：Transformations和Actions两大类，而Hadoop只提供了Map和Reduce两种操作
        - Transformations
            * Map
            * Filter
            * FlatMap
            * Sample
            * GroupByKey
            * ReduceByKey
            * Union
            * Join
            * Cogroup
            * MapValues
            * Sort
            * PartionBy
        - Actions
            * Collect
            * Reduce
            * Lookup
            * Save

#### Spark 使用场景
* 大数据的使用场景
    - 复杂的批量处理（Batch Data Processing），偏重点在于处理海量数据的能力，至于处理速度可忍受，通常的时间可能是在数十分钟到数小时。（可以用Hadoop的MapReduce来进行批量海量数据处理）
    - 基于历史数据的交互式查询（Interactive Query），通常的时间在数十秒到数十分钟之间 （可以Impala进行交互式查询）
    - 基于实时数据流的数据处理（Streaming Data Processing），通常在数百毫秒到数秒之间 （可以用Storm分布式处理框架处理实时流式数据）
* Spark的使用场景
    - Spark是基于内存的迭代计算框架，适用于需要多次操作特定数据集的应用场合。需要反复操作的次数越多，所需读取的数据量越大，受益越大，数据量小但是计算密集度较大的场合，受益就相对较小
    - 由于RDD的特性，Spark不适用那种异步细粒度更新状态的应用，例如web服务的存储或者是增量的web爬虫和索引。就是对于那种增量修改的应用模型不适合
    - 数据量不是特别大，但是要求实时统计分析需求

#### Spark 运行模式
- 本地模式(Local): 常用于本地开发测试，本地还分为local单线程和local-cluster多线程;
- 集群模式(Standlone): 典型的Mater/slave模式，不过也能看出Master是有单点故障的；Spark支持 ZooKeeper来实现HA
- 集群模式(On yarn): 运行在yarn资源管理器框架之上，由yarn负责资源管理，Spark负责任务调度和计算
- 集群模式(On mesos): 运行在mesos资源管理器框架之上，由mesos负责资源管理，Spark负责任务调度和计算
- 集群模式(On cloud): 比如AWS的EC2，使用这个模式能很方便的访问Amazon的S3;Spark支持多种分布式存储系统：HDFS和S3

#### Spark Core
- 提供了有向无环图（DAG）的分布式并行计算框架，并提供Cache机制来支持多次迭代计算或者数据共享，大大减少迭代计算之间读取数据局的开销，这对于需要进行多次迭代的数据挖掘和分析性能有很大提升
- 在Spark中引入了RDD (Resilient Distributed Dataset) 的抽象，它是分布在一组节点中的只读对象集合，这些集合是弹性的，如果数据集一部分丢失，则可以根据“血统”对它们进行重建，保证了数据的高容错性；
- 移动计算而非移动数据，RDD Partition可以就近读取分布式文件系统中的数据块到各个节点内存中进行计算
- 使用多线程池模型来减少task启动开稍
- 采用容错的、高可伸缩性的akka作为通讯框架

#### SparkStreaming
> SparkStreaming是一个对实时数据流进行高通量、容错处理的流式处理系统，可以对多种数据源（如Kdfka、Flume、Twitter、Zero和TCP 套接字）进行类似Map、Reduce和Join等复杂操作，并将结果保存到外部文件系统、数据库或应用到实时仪表盘。

- 计算流程：Spark Streaming是将流式计算分解成一系列短小的批处理作业。这里的批处理引擎是Spark Core，也就是把Spark Streaming的输入数据按照batch size（如1秒）分成一段一段的数据（Discretized Stream），每一段数据都转换成Spark中的RDD（Resilient Distributed Dataset），然后将Spark Streaming中对DStream的Transformation操作变为针对Spark中对RDD的Transformation操作，将RDD经过操作变成中间结果保存在内存中。整个流式计算根据业务的需求可以对中间的结果进行叠加或者存储到外部设备。
- 容错性：对于流式计算来说，容错性至关重要。首先我们要明确一下Spark中RDD的容错机制。每一个RDD都是一个不可变的分布式可重算的数据集，其记录着确定性的操作继承关系（lineage），所以只要输入数据是可容错的，那么任意一个RDD的分区（Partition）出错或不可用，都是可以利用原始输入数据通过转换操作而重新算出的。  
- 实时性：对于实时性的讨论，会牵涉到流式处理框架的应用场景。Spark Streaming将流式计算分解成多个Spark Job，对于每一段数据的处理都会经过Spark DAG图分解以及Spark的任务集的调度过程。对于目前版本的Spark Streaming而言，其最小的Batch Size的选取在0.5~2秒钟之间（Storm目前最小的延迟是100ms左右），所以Spark Streaming能够满足除对实时性要求非常高（如高频实时交易）之外的所有流式准实时计算场景。
- 扩展性与吞吐量：Spark目前在EC2上已能够线性扩展到100个节点（每个节点4Core），可以以数秒的延迟处理6GB/s的数据量（60M records/s），其吞吐量也比流行的Storm高2～5倍，图4是Berkeley利用WordCount和Grep两个用例所做的测试，在Grep这个测试中，Spark Streaming中的每个节点的吞吐量是670k records/s，而Storm是115k records/s。

#### Spark SQL
- 引入了新的RDD类型SchemaRDD，可以象传统数据库定义表一样来定义SchemaRDD，SchemaRDD由定义了列数据类型的行对象构成。SchemaRDD可以从RDD转换过来，也可以从Parquet文件读入，也可以使用HiveQL从Hive中获取。
- 内嵌了Catalyst查询优化框架，在把SQL解析成逻辑执行计划之后，利用Catalyst包里的一些类和接口，执行了一些简单的执行计划优化，最后变成RDD的计算
- 在应用程序中可以混合使用不同来源的数据，如可以将来自HiveQL的数据和来自SQL的数据进行Join操作。
- Spark SQL 优化
    * 内存列存储（In-Memory Columnar Storage） sparkSQL的表数据在内存中存储不是采用原生态的JVM对象存储方式，而是采用内存列存储；
    * 字节码生成技术（Bytecode Generation） Spark1.1.0在Catalyst模块的expressions增加了codegen模块，使用动态字节码生成技术，对匹配的表达式采用特定的代码动态编译。另外对SQL表达式都作了CG优化， CG优化的实现主要还是依靠Scala2.10的运行时放射机制（runtime reflection）；
    * Scala代码优化 SparkSQL在使用Scala编写代码的时候，尽量避免低效的、容易GC的代码；尽管增加了编写代码的难度，但对于用户来说接口统一。

#### MLBase/MLlib
MLBase是Spark生态圈的一部分专注于机器学习，让机器学习的门槛更低，让一些可能并不了解机器学习的用户也能方便地使用MLbase。MLBase分为四部分：MLlib、MLI、ML Optimizer和MLRuntime。
- ML Optimizer会选择它认为最适合的已经在内部实现好了的机器学习算法和相关参数，来处理用户输入的数据，并返回模型或别的帮助分析的结果；
- MLI 是一个进行特征抽取和高级ML编程抽象的算法实现的API或平台；
- MLlib是Spark实现一些常见的机器学习算法和实用程序，包括分类、回归、聚类、协同过滤、降维以及底层优化，该算法可以进行可扩充； MLRuntime 基于Spark计算框架，将Spark的分布式计算应用到机器学习领域。
- MLBase的核心是他的优化器，把声明式的Task转化成复杂的学习计划，产出最优的模型和计算结果
    * MLBase是分布式的，Weka是一个单机的系统；
    * MLBase是自动化的，Weka和Mahout都需要使用者具备机器学习技能，来选择自己想要的算法和参数来做处理；
    * MLBase提供了不同抽象程度的接口，让算法可以扩充
    * MLBase基于Spark这个平台

#### GraphX
GraphX是Spark中用于图(e.g., Web-Graphs and Social Networks)和图并行计算(e.g., PageRank and Collaborative Filtering)的API,可以认为是GraphLab(C++)和Pregel(C++)在Spark(Scala)上的重写及优化，跟其他分布式图计算框架相比，GraphX最大的贡献是，在Spark之上提供一栈式数据解决方案，可以方便且高效地完成图计算的一整套流水作业。
GraphX的核心抽象是Resilient Distributed Property Graph，一种点和边都带属性的有向多重图。它扩展了Spark RDD的抽象，有Table和Graph两种视图，而只需要一份物理存储。两种视图都有自己独有的操作符，从而获得了灵活操作和执行效率。如同Spark，GraphX的代码非常简洁。GraphX的核心代码只有3千多行，而在此之上实现的Pregel模型，只要短短的20多行。
GraphX的底层设计有以下几个关键点：
- 对Graph视图的所有操作，最终都会转换成其关联的Table视图的RDD操作来完成。Graph最终具备了RDD的3个关键特性：Immutable、Distributed和Fault-Tolerant。其中最关键的是Immutable（不变性）。逻辑上，所有图的转换和操作都产生了一个新图；物理上，GraphX会有一定程度的不变顶点和边的复用优化，对用户透明。
- 两种视图底层共用的物理数据，由RDD[Vertex-Partition]和RDD[EdgePartition]这两个RDD组成。点和边实际都不是以表Collection[tuple]的形式存储的，而是由VertexPartition/EdgePartition在内部存储一个带索引结构的分片数据块，以加速不同视图下的遍历速度。不变的索引结构在RDD转换过程中是共用的，降低了计算和存储开销。
- 图的分布式存储采用点分割模式，而且使用partitionBy方法，由用户指定不同的划分策略（PartitionStrategy）。划分策略会将边分配到各个EdgePartition，顶点Master分配到各个VertexPartition，EdgePartition也会缓存本地边关联点的Ghost副本。划分策略的不同会影响到所需要缓存的Ghost副本数量，以及每个EdgePartition分配的边的均衡程度，需要根据图的结构特征选取最佳策略。目前有EdgePartition2d、EdgePartition1d、RandomVertexCut和CanonicalRandomVertexCut这四种策略。在淘宝大部分场景下，EdgePartition2d效果最好。

#### Tachyon
Tachyon是一个高容错的分布式文件系统，允许文件以内存的速度在集群框架中进行可靠的共享，就像Spark和 MapReduce那样。通过利用信息继承，内存侵入，Tachyon获得了高性能。Tachyon工作集文件缓存在内存中，并且让不同的 Jobs/Queries以及框架都能内存的速度来访问缓存文件”。因此，Tachyon可以减少那些需要经常使用的数据集通过访问磁盘来获得的次数。Tachyon兼容Hadoop，现有的Spark和MR程序不需要任何修改而运行。

refer：http://www.cnblogs.com/shishanyuan/p/4700615.html



