---
title: Spark 性能优化
date: 2020-01-12 10:26:22
tags: https://tech.meituan.com/2016/05/12/spark-tuning-pro.html
---

Spark 性能优化可以分为四个部分

- 开发调优
  - 避免创建重复的 RDD
  - 尽可能复用同一个 RDD
  - 对多次使用的 RDD 进行持久化
  - 尽量避免使用 shuffle 类算子，使用 map 类非 shuffle 算子
  - 使用 map-side 预聚合的 shuffle 操作
  - 使用高性能的算子
    - 使用 reduceByKey，aggregateByKey 替代 groupByKey
    - 使用 mapPartitions 替代 map
    - 使用 foreachPartitions 替代 foreach
    - 使用 fillter 之后进行 coalesce 操作
    - 使用 repartitionAndSortWithinPartitions 替代 repartition 与 sort 类操作
  - 广播大变量
  - 使用 Kryo 优化序列化性能
  - 优化数据结构
- 资源调优
- 数据倾斜调优
  - 使用 Hive ETL 预处理数据
  - 过滤少数导致倾斜的 key
  - 提高 shuffle 操作的并行度
  - 两阶段聚合(局部聚合 + 全局聚合)
  - 将 reduce join 转为 map join
  - 采样倾斜 key 并分拆分 join 操作
  - 使用随机前缀和扩容 RDD 进行 join
  - 多种方案组合使用
- Shuffle 调优

开发调优和资源调优是所有 Spark 作业都需要注意和遵循的一些基本原则，是高性能 Spark 作业的基础

## 开发调优

Spark 性能优化的第一步，就是要在开发 Spark 作业的过程中注意和应用一些性能优化的基本原则。Spark 的基本开发原则：RDD lineage 设计、算子的合理使用、特殊操作的优化等。

### 1. 避免创建重复的 RDD

对于同一份数据，只应该创建一个 RDD，不能创建多个 RDD 来代表同一份数据。

### 2. 尽可能复用同一个 RDD

对不同的数据执行算子操作时还要尽可能地复用一个 RDD，这种多个 RDD 的数据有重叠或者包含的情况，应该尽量复用一个 RDD，这样可以尽可能地减少 RDD 的数量，从而尽可能减少算子执行的次数

### 3. 对多次使用的 RDD 进行持久化

针对不同情况有不同的持久化级别：

- MEMORY_ONLY：使用未序列化的 Java 对象格式，将数据保存在内存中。如果内存不够存放所有的数据，则数据可能就不会进行持久化。这是默认的持久化策略，使用 cache()方法时，实际就是使用的这种持久化策略。
- MEMORY_AND_DISK：使用未序列化的 Java 对象格式，优先尝试将数据保存在内存中。如果内存不够存放所有的数据，会将数据写入磁盘文件中。
- MEMORY_ONLY_SER：基本含义同 MEMORY_ONLY。唯一的区别是，会将 RDD 中的数据进行序列化，这种方式更加节省内存，从而可以避免持久化的数据占用过多内存导致频繁 GC。
- MEMORY_AND_DISK_SER：基本含义同 MEMORY_AND_DISK。唯一的区别是，会将 RDD 中的数据进行序列化。
- DISK_ONLY：使用未序列化的 Java 对象格式，将数据全部写入磁盘文件中
- MEMORY_ONLY_2/MEMORY_AND_DISK_2：对于上述任意一种持久化策略，如果加上后缀\_2，代表的是将每个持久化的数据，都复制一份副本，并将副本保存到其他节点上。这种基于副本的持久化机制主要用于进行容错。
  如何选取最优化策略：
- 优先选用 MEMORY_ONLY，性能最高，但是要求内存必须足够大，但是在实际的生产环境中，使用这种策略的场景很有限，比如数据达到几十亿维度的时候，使用这种持久化级别，会导致 JVM 的 OOM 内存溢出异常
- 如果使用 MEMORY_ONLY 级别时发生了内存溢出，那么建议尝试使用 MEMORY_ONLY_SER 级别，降低内存占用，当时多出了序列化与反序列化的性能开销，因为后续算子都是基于纯内存进行操作，总体性能还是比较高的，如果当 RDD 的数据量过多的话，还是可能会导致 OOM 内存溢出的异常
- 如果纯内存的级别都无法使用，那么建议使用 MEMORY_AND_DISK_SER 策略，而不是 MEMORY_AND_DISK 策略，如果走到这一步，说明数据量很大，内存没法放得下，序列化后的数据可以减少内存和磁盘的空间开销
- 通常不建议使用 DISK_ONLY 和后缀为\_2 的级别，完全基于磁盘文件进行数据的读写，会导致性能急剧降低，有时还不如重新计算一次所有 RDD，后缀为\_2 的级别，必须将所有数据都复制一份副本，并发送到其他节点上，数据复制以及网络传输会导致较大的性能开销，除非是要求作业的高可用性，否则不建议使用。

### 4. 尽量避免使用 shuffle 类算子

Spark 作业运行过程中，最消耗性能的地方就是 shuffle 过程。shuffle 过程中，各个节点上的相同 key 都会先写入本地磁盘文件中，然后其他节点需要通过网络传输拉取各个节点上的磁盘文件中的相同 key。而且相同 key 都拉取到同一个节点进行聚合操作时，还有可能会因为一个节点上处理的 key 过多，导致内存不够存放，进而溢写到磁盘文件中。因此在 shuffle 过程中，可能会发生大量的磁盘文件读写的 IO 操作，以及数据的网络传输操作。磁盘 IO 和网络数据传输也是 shuffle 性能较差的主要原因。

因此在我们的开发过程中，能避免则尽可能避免使用 reduceByKey、join、distinct、repartition 等会进行 shuffle 的算子，尽量使用 map 类的非 shuffle 算子。比如将 join 操作，转换成 Broadcast + map 的操作。

### 5. 使用 map-side 预聚合的 shuffle 操作

如果因为业务需要，一定要使用 shuffle 操作，无法用 map 类的算子来替代，那么尽量使用可以 map-side 预聚合的算子。类似于 MapReduce 中的本地 combiner，在每个节点本地对相同的 key 进行一次聚合操作。通常来说，在可能的情况下，建议使用 reduceByKey 或者 aggregateByKey 算子来替代掉 groupByKey 算子。因为 reduceByKey 和 aggregateByKey 算子都会使用用户自定义的函数对每个节点本地的相同 key 进行预聚合。而 groupByKey 算子是不会进行预聚合的，全量的数据会在集群的各个节点之间分发和传输，性能相对来说比较差。

### 6. 使用高性能的算子

除了 shuffle 相关的算子有优化原则之外，其他的算子也都有着相应的优化原则。

- 使用 reduceByKey/aggregateByKey 替代 groupByKey
- 使用 mapPartitions 替代普通 map：mapPartitions 类的算子，一次函数调用会处理一个 partition 所有的数据，而不是一次函数调用处理一条，性能相对来说会高一些，但有时候使用 mapPartitions 会出现 OOM（内存溢出）的问题，单次处理一个 partition 的数据，如果内存不够，就可能出现 OOM 异常，所以使用这类操作时要慎重！
- 使用 foreachPartitions 替代 foreach
- 使用 filter 之后进行 coalesce 操作：通常对一个 RDD 执行 filter 算子过滤掉 RDD 中较多数据后(比如 30%以上的数据)，建议使用 coalesce 算子，手动减少 RDD 的 partition 数量，将 RDD 中的数据压缩到更少的 partition 中去。
- 使用 repartitionAndSortWithinPartitions 替代 repartition 与 sort 类操作：如果需要在 repartition 重分区之后，还要进行排序，官方直接建议直接使用 repartitionAndSortWithinPartitions 算子。

### 7. 广播大变量

有时在开发过程中，会遇到需要在算子函数中使用外部变量的场景（尤其是大变量，比如 100M 以上的大集合），那么此时就应该使用 Spark 的广播（Broadcast）功能来提升性能。
在算子函数中使用到外部变量时，默认情况下，Spark 会将该变量复制多个副本，通过网络传输到 task 中，此时每个 task 都有一个变量副本，使用 Spark 的广播功能，对该变量进行广播。广播后的变量，会保证每个 Executor 的内存中，只驻留一份变量副本，而 Executor 中的 task 执行时共享该 Executor 中的那份变量副本。

### 8. 使用 Kryo 优化序列化性能

在 Spark 中，有三个地方涉及到序列化：

- 在算子函数中使用到外部变量时，该变量会被序列化后进行网络传输
- 将自定义的类型作为 RDD 的泛型类型时，要求自定义的类必须实现 Serializable 接口
- 使用可序列化的持久化策略时，Spark 会将 RDD 中的每个 partition 都序列化成一个大的字节数组
  Spark 默认使用的是 Java 的序列化机制，同时支持使用 Kryo 序列化库，官方介绍性能高 10 倍左右，Spark 之所以默认没有使用 Kryo 作为序列化类库，是因为 Kryo 要求最好要注册所有需要进行序列化的自定义类型，因此对于开发者来说，这种方式比较麻烦。

### 9. 优化数据结构

Java 中，有三种类型比较耗费内存：

- 对象，每个 Java 对象都有对象头、引用等额外的信息，因此比较占用内存空间
- 字符串，每个字符串内部都有一个字符数组以及长度等额外信息
- 集合类型，集合类型内部通常会使用一些内部类来封装集合元素，比如 Map.Entry
  Spark 官方建议，在 Spark 编码实现中，特别是对于算子函数中的代码，尽量不要使用上述三种数据结构，尽量使用字符串替代对象，使用原始类型（比如 Int、Long）替代字符串，使用数组替代集合类型，这样尽可能地减少内存占用，从而降低 GC 频率，提升性能。
  但实际情况下，需要考虑到代码的可维护性，在保证代码可维护性的的前提条件下，使用占内存较少的数据结构

## 资源调优

在开发完 Spark 作业之后，就该为作业配置合适的资源了。用 spark-submit 提交命令时设置参数，资源参数设置的不合理，可能会导致没有充分利用集群资源，作业运行会极其缓慢；或者设置的资源过大，队列没有足够的资源来提供，进而导致各种异常。

- num-executors：
  - 说明：设置 Spark 作业总共要用多少个 Executor 进程来执行。这个参数非常之重要，如果不设置的话，默认只会给你启动少量的 Executor 进程，此时你的 Spark 作业的运行速度是非常慢的。
  - 建议：每个 Spark 作业的运行一般设置 50~100 个左右的 Executor 进程比较合适，设置太少或太多的 Executor 进程都不好。设置的太少，无法充分利用集群资源；设置的太多的话，大部分队列可能无法给予充分的资源。
- executor-memory：
  - 说明：该参数用于设置每个 Executor 进程的内存。
  - 建议：每个 Executor 进程的内存设置 4G~8G 较为合适。但是这只是一个参考值，具体的设置还是得根据不同部门的资源队列来定。可以看看自己团队的资源队列的最大内存限制是多少，num-executors 乘以 executor-memory，是不能超过队列的最大内存量的。此外，如果你是跟团队里其他人共享这个资源队列，那么申请的内存量最好不要超过资源队列最大总内存的 1/3~1/2，避免你自己的 Spark 作业占用了队列所有的资源，导致别的同学的作业无法运行。
- executor-cores：
  - 说明：该参数用于设置每个 Executor 进程的 CPU core 数量。这个参数决定了每个 Executor 进程并行执行 task 线程的能力。
  - 建议：Executor 的 CPU core 数量设置为 2~4 个较为合适。同样得根据不同部门的资源队列来定，可以看看自己的资源队列的最大 CPU core 限制是多少，再依据设置的 Executor 数量，来决定每个 Executor 进程可以分配到几个 CPU core。同样建议，如果是跟他人共享这个队列，那么 num-executors \* executor-cores 不要超过队列总 CPU core 的 1/3~1/2 左右比较合适，也是避免影响其他同学的作业运行。
- driver-memory:
  - 说明：该参数用于设置 Driver 进程的内存。
  - 建议：Driver 的内存通常来说不设置，或者设置 1G 左右应该就够了。唯一需要注意的一点是，如果需要使用 collect 算子将 RDD 的数据全部拉取到 Driver 上进行处理，那么必须确保 Driver 的内存足够大，否则会出现 OOM 内存溢出的问题。
- spark.default.parallelism：
  - 说明：该参数用于设置每个 stage 的默认 task 数量。这个参数极为重要，如果不设置可能会直接影响你的 Spark 作业性能。
  - 建议：Spark 作业的默认 task 数量为 500~1000 个较为合适。如果不设置，Spark 自己根据底层 HDFS 的 block 数量来设置 task 的数量，默认是一个 HDFS block 对应一个 task。通常 Spark 默认设置的数量是偏少的，如果 task 数量偏少的话，就会导致你前面设置好的 Executor 的参数都前功尽弃。Spark 官网建议，设置该参数为 num-executors \* executor-cores 的 2~3 倍较为合适。
- spark.storage.memoryFraction：
  - 说明：该参数用于设置 RDD 持久化数据在 Executor 内存中能占的比例，默认是 0.6。
  - 建议：
    - 如果 Spark 作业中，有较多的 RDD 持久化操作，该参数的值可以适当提高一些，保证持久化的数据能够容纳在内存中。
    - 如果 Spark 作业中的 shuffle 类操作比较多，而持久化操作比较少，那么这个参数的值适当降低一些比较合适。
    - 如果发现作业由于频繁的 gc 导致运行缓慢，意味着 task 执行用户代码的内存不够用，那么同样建议调低这个参数的值。
- spark.shuffle.memoryFraction：
  - 说明：该参数用于设置 shuffle 过程中一个 task 拉取到上个 stage 的 task 的输出后，进行聚合操作时能够使用的 Executor 内存的比例，默认是 0.2。Executor 默认只有 20%的内存用来进行该操作。shuffle 操作在进行聚合时，如果发现使用的内存超出了这个 20%的限制，那么多余的数据就会溢写到磁盘文件中去，此时就会极大地降低性能。
  - 建议： 
    - 如果 Spark 作业中的 RDD 持久化操作较少，shuffle 操作较多时，建议降低持久化操作(storage)的内存占比，提高 shuffle 操作的内存占比比例，避免 shuffle 过程中数据过多时内存不够用，必须溢写到磁盘上，降低了性能。 
    - 如果发现作业由于频繁的 gc 导致运行缓慢，意味着 task 执行用户代码的内存不够用，那么同样建议调低这个参数的值。
  
  样例：`./bin/spark-submit --master yarn-cluster --num-executors 100 --executor-memory 6G --executor-cores 4 --driver-memory 1G --conf spark.default.parallelism=100 --conf spark.storage.memoryFraction=0.5 --conf spark.shuffle.memoryFraction=0.3`

## 数据倾斜调优

### 1. 数据倾斜发生时的现象

- 绝大多数 task 执行得都非常快，但个别 task 执行极慢。比如，总共有 1000 个 task，997 个 task 都在 1 分钟之内执行完了，但是剩余两三个 task 却要一两个小时。
- 原本能够正常执行的 Spark 作业，某天突然报出 OOM（内存溢出）异常，观察异常栈，是我们写的业务代码造成的。

### 2. 数据倾斜发生的原理

在进行 shuffle 的时候，必须将各个节点上相同的 key 拉取到某个节点上的一个 task 来进行处理，比如按照 key 进行聚合或 join 等操作。此时如果某个 key 对应的数据量特别大的话，就会发生数据倾斜。出现数据倾斜的时候，Spark 作业看起来会运行得非常缓慢，甚至可能因为某个 task 处理的数据量过大导致内存溢出。

### 3. 如何定位导致数据倾斜的代码

数据倾斜只会发生在 shuffle 过程中。常用的并且可能会触发 shuffle 操作的算子有：`distinct、groupByKey、reduceByKey、aggregateByKey、join、cogroup、repartition`等。

#### 3.1 某个 task 执行特别慢的情况

- 找数据倾斜发生在第几个 stage 中
  - yarn-client 模式提交中，可以从本地 log 找到当前运行到第几个 stage
  - yarn-cluster 模式提交，可以通过 Spark Web UI 查看当前运行到第几个 stage，以及各 task 分配的数据量
- 知道数据倾斜发生在哪一个 stage 之后，然后根据 stage 划分原理，推算数据倾斜的那个 stage 对应代码中的那一部分

#### 3.2 某个 task 莫名其妙内存溢出的情况

通过日志的异常栈定位代码中的哪一行发生了内存溢出，然后再那行代码附近找找相应的 shuffle 算子。但是不能单纯靠偶然的内存溢出判定发生了数据倾斜，一些代码的 Bug，偶然出现的异常数据，也可能会导致数据倾斜，最佳的方式还是通过 Spark Web UI 查看报错的 stage 的各个 task 的运行时间以及分配的数据量，才能确定是否由于数据倾斜才导致了内存溢出

### 4. 查看导致数据倾斜的 key 的数据分布情况

知道数据倾斜发生在哪里之后，需要分析一下执行那个 shuffle 操作并导致数据倾斜的 RDD，查看其中 key 的分布情况。

- 如果是 Spark SQL 中的 group by、join 语句导致的数据倾斜，那么就查询一下 SQL 中使用的表的 key 分布情况。
- 如果是对 Spark RDD 执行 shuffle 算子导致的数据倾斜，那么可以在 Spark 作业中加入查看 key 分布的代码，比如 RDD.countByKey()。然后对统计出来的各个 key 出现的次数，collect/take 到客户端打印一下，就可以看到 key 的分布情况。

### 5. 数据倾斜的解决方案

#### 5.1 使用 Hive ETL 预处理数据

- 适用场景：导致数据倾斜的是 Hive 表，该 Hive 表中的数据本身很不均匀，业务场景需要频繁使用 Spark 对 Hive 表执行某个分析操作。
- 实现思路：评估是否可以通过 Hive 来进行数据预处理，即预先对数据按照 key 进行聚合
- 实现原理：从数据源解决了数据倾斜，避免 Spark 中执行 shuffle 算子，但是指标不治本，只是把数据倾斜的发生提前到了 Hive ETL 中
- 优点：实现简单，效果还不错，完全规避掉了数据倾斜，Spark 作业的性能大幅度提升
- 缺点：治标不治本，Hive ETL 中还是会发生数据倾斜
- 实践经验：试用于一些 Java 系统和 Spark 结合的项目中，Java 代码频繁调用 Spark 作业的场景，对 Spark 作业的执行性能要求很高，比较适用这种方案，将数据倾斜提前到上游的 Hive ETL，每天执行一次，之后 Java 调用 Spark 作业时，执行速度为很快，能够提供给更好的用户体验。

#### 5.2 过滤少数导致倾斜的 key

- 适用场景：如果发现导致倾斜的 key 就少数几个，而且对计算本身影响并不大
- 实现思路：先判断哪些 key 的数据量特别多，如果对结果不重要，直接过滤掉。如果需要每次作业执行时，动态判定哪些 key 的数据量最多然后再进行过滤，那么可以使用 sample 算子对 RDD 进行采样，然后计算出每个 key 的数量，取数据量最多的 key 过滤掉即可。
  - 在 Spark SQL 中可以使用 where 子句过滤掉这些 key
  - 在 Spark Core 中对 RDD 执行 filter 算子过滤掉这些 key
- 实现原理：将导致数据倾斜的 key 过滤到之后，这些 key 就不会参与计算了
- 优点：实现简单，效果也很好
- 缺点：适用场景不多，大多数情况下，导致倾斜的 key 还是很多的
- 实践经验：每次执行前先进行采样，计算出样本中数据量最大的几个 key 之后，直接在程序中将那些 key 给过滤掉。

#### 5.3 提高 shuffle 操作的并行度

- 适用场景：如果必须要对数据倾斜迎难而上，这种方式最简单
- 实现思路：在对 RDD 执行 shuffle 算子时，显式制定并发度，比如 reduceByKey(1000)，对于 Spark SQL 中的 shuffle 类语句，比如 group by、join 等，需要设置一个参数，即 spark.sql.shuffle.partitions，该参数代表了 shuffle read task 的并行度，该值默认是 200。
- 实现原理：增加 shuffle read task 的数量，可以让原本分配给一个 task 的多个 key 分配给多个 task，从而让每个 task 处理比原来更少的数据。
- 优点：实现简单，可以有效缓解和减轻数据倾斜的影响
- 缺点：只是缓解了数据倾斜，没有彻底根除问题，效果有限
- 实践经验：该方案通常无法彻底解决数据倾斜，因为如果出现一些极端情况，比如某个 key 对应的数据量有 100 万，那么无论你的 task 数量增加到多少，这个对应着 100 万数据的 key 肯定还是会分配到一个 task 中去处理，因此注定还是会发生数据倾斜的。这种方案只能说是在发现数据倾斜时尝试使用的第一种手段，尝试去用嘴简单的方法缓解数据倾斜而已，或者是和其他方案结合起来使用

#### 5.4 两阶段聚合(局部聚合 + 全局聚合)

- 适用场景：对 RDD 执行 reduceByKey 等聚合类 shuffle 算子或者在 Spark SQL 中使用 group by 语句进行分组聚合时，比较适用这种方案。
- 实现思路：
  - 第一次是局部聚合，先给每个 key 都打上一个随机数，比如 10 以内的随机数，此时原先一样的 key 就变成不一样的了
  - 接着对打上随机数后的数据，执行 reduceByKey 等聚合操作，进行局部聚合，那么局部聚合结果
  - 然后将各个 key 的前缀给去掉，再次进行全局聚合操作
- 实现原理：将原本相同的 key 通过附加随机前缀的方式，变成多个不同的 key，就可以让原本被一个 task 处理的数据分散到多个 task 上去做局部聚合，进而解决单个 task 处理数据量过多的问题。接着去除掉随机前缀，再次进行全局聚合，就可以得到最终的结果。
- 优点：对于聚合类的 shuffle 操作导致的数据倾斜，效果是非常不错的。通常都可以解决掉数据倾斜，或者至少是大幅度缓解数据倾斜，将 Spark 作业的性能提升数倍以上。
- 缺点：仅仅适用于聚合类的 shuffle 操作，适用范围相对较窄。如果是 join 类的 shuffle 操作，还得用其他的解决方案

#### 5.5 将 reduce join 转为 map join

- 适用场景：在对 RDD 使用 join 类操作，或者是在 Spark SQL 中使用 join 语句时，而且 join 操作中的一个 RDD 或表的数据量比较小（比如几百 M 或者一两 G），比较适用此方案。
- 实现思路：不使用 join 算子进行连接操作，而使用 Broadcast 变量与 map 类算子实现 join 操作，进而完全规避掉 shuffle 类的操作，彻底避免数据倾斜的发生和出现。
  - 将较小 RDD 中的数据直接通过 collect 算子拉取到 Driver 端的内存中来，然后对其创建一个 Broadcast 变量
  - 接着对另外一个 RDD 执行 map 类算子，在算子函数内，从 Broadcast 变量中获取较小 RDD 的全量数据
- 实现原理：普通的 join 是会走 shuffle 过程的，而一旦 shuffle，就相当于会将相同 key 的数据拉取到一个 shuffle read task 中再进行 join，此时就是 reduce join。但是如果一个 RDD 是比较小的，则可以采用广播小 RDD 全量数据+map 算子来实现与 join 同样的效果，也就是 map join，此时就不会发生 shuffle 操作，也就不会发生数据倾斜。
- 优点：对 join 操作导致的数据倾斜，效果非常好，因为根本就不会发生 shuffle，也就根本不会发生数据倾斜。
- 缺点：适用场景比较少，这种方案只适合于一个大表和一个小表的情况

#### 5.6 采样倾斜 key 并分拆分 join 操作

- 适用场景：两个 RDD/Hive 表进行 join 的时候，如果数据量都比较大，那么此时可以看一下两个 RDD/Hive 表中的 key 分布情况。如果出现数据倾斜，是因为其中某一个 RDD/Hive 表中的少数几个 key 的数据量过大，而另一个 RDD/Hive 表中的所有 key 都分布比较均匀，那么采用这个解决方案是比较合适的。
- 实现思路：
  - 对包含少数几个数据量过大的 key 的那个 RDD，通过 sample 算子采样出一份样本来，然后统计一下每个 key 的数量，计算出来数据量最大的是哪几个 key。
  - 然后将这几个 key 对应的数据从原来的 RDD 中拆分出来，形成一个单独的 RDD，并给每个 key 都打上 n 以内的随机数作为前缀，而不会导致倾斜的大部分 key 形成另外一个 RDD。
  - 接着将需要 join 的另一个 RDD，也过滤出来那几个倾斜 key 对应的数据并形成一个单独的 RDD，将每条数据膨胀成 n 条数据，这 n 条数据都按顺序附加一个 0~n 的前缀，不会导致倾斜的大部分 key 也形成另外一个 RDD。
  - 再将附加了随机前缀的独立 RDD 与另一个膨胀 n 倍的独立 RDD 进行 join，此时就可以将原先相同的 key 打散成 n 份，分散到多个 task 中去进行 join 了。
  - 而另外两个普通的 RDD 就照常 join 即可。
  - 最后将两次 join 的结果使用 union 算子合并起来即可，就是最终的 join 结果。
- 实现原理：对于 join 导致的数据倾斜，如果只是某几个 key 导致了倾斜，可以将少数几个 key 分拆成独立 RDD，并附加随机前缀打散成 n 份去进行 join，此时这几个 key 对应的数据就不会集中在少数几个 task 上，而是分散到多个 task 进行 join 了。
- 优点：对于 join 导致的数据倾斜，如果只是某几个 key 导致了倾斜，采用该方式可以用最有效的方式打散 key 进行 join。而且只需要针对少数倾斜 key 对应的数据进行扩容 n 倍，不需要对全量数据进行扩容。避免了占用过多内存。
- 缺点：如果导致倾斜的 key 特别多的话，比如成千上万个 key 都导致数据倾斜，那么这种方式也不适合。

#### 5.7 使用随机前缀和扩容 RDD 进行 join

- 适用场景：如果在进行 join 操作时，RDD 中有大量的 key 导致数据倾斜，那么进行分拆 key 也没什么意义，此时就只能使用最后一种方案来解决问题了。
- 实现思路：
  - 首先查看 RDD/Hive 表中的数据分布情况，找到那个造成数据倾斜的 RDD/Hive 表，比如有多个 key 都对应了超过 1 万条数据。
  - 然后将该 RDD 的每条数据都打上一个 n 以内的随机前缀。
  - 同时对另外一个正常的 RDD 进行扩容，将每条数据都扩容成 n 条数据，扩容出来的每条数据都依次打上一个 0~n 的前缀。
  - 最后将两个处理后的 RDD 进行 join 即可。
- 实现原理：将原先一样的 key 通过附加随机前缀变成不一样的 key，然后就可以将这些处理后的“不同 key”分散到多个 task 中去处理，而不是让一个 task 处理大量的相同 key。
- 优点：对 join 类型的数据倾斜基本都可以处理，而且效果也相对比较显著，性能提升效果非常不错。
- 缺点：该方案更多的是缓解数据倾斜，而不是彻底避免数据倾斜。而且需要对整个 RDD 进行扩容，对内存资源要求很高。

#### 5.8 多种方案组合使用

在实践中发现，很多情况下，如果只是处理较为简单的数据倾斜场景，那么使用上述方案中的某一种基本就可以解决。但是如果要处理一个较为复杂的数据倾斜场景，那么可能需要将多种方案组合起来使用。比如说，我们针对出现了多个数据倾斜环节的 Spark 作业，可以先运用解决方案一和二，预处理一部分数据，并过滤一部分数据来缓解；其次可以对某些 shuffle 操作提升并行度，优化其性能；最后还可以针对不同的聚合或 join 操作，选择一种方案来优化其性能。

## Shuffle 调优

大多数 Spark 作业的性能主要就是消耗在了 shuffle 环节，因为该环节包含了大量的磁盘 IO、序列化、网络数据传输等操作。如果要让作业的性能更上一层楼，就有必要对 shuffle 过程进行调优。但影响一个 Spark 作业性能的因素，主要还是代码开发、资源参数以及数据倾斜，shuffle 调优只能在整个 Spark 的性能调优中占到一小部分而已。
Shffule 过程汇总常用的参数：

- spark.shuffle.file.buffer
  - 默认值：32K
  - 说明：该参数用于设置 shuffle write task 的 BufferedOutputStream 的 buffer 缓冲大小。将数据写到磁盘文件之前，会先写入 buffer 缓冲中，待缓冲写满之后，才会溢写到磁盘。
  - 建议：如果作业可用的内存资源较为充足的话，可以适当增加这个参数的大小（比如 64k），从而减少 shuffle write 过程中溢写磁盘文件的次数，也就可以减少磁盘 IO 次数，进而提升性能。
- spark.reducer.maxSizeInFight
  - 默认值：48M
  - 说明：该参数用于设置 shuffle read task 的 buffer 缓冲大小，而这个 buffer 缓冲决定了每次能够拉取多少数据。
  - 建议：如果作业可用的内存资源较为充足的话，可以适当增加这个参数的大小（比如 96m），从而减少拉取数据的次数，也就可以减少网络传输的次数，进而提升性能。
- spark.shuffle.io.maxRetries
  - 默认值：3
  - 说明：shuffle read task 从 shuffle write task 所在节点拉取属于自己的数据时，如果因为网络异常导致拉取失败，是会自动进行重试的。该参数就代表了可以重试的最大次数。如果在指定次数之内拉取还是没有成功，就可能会导致作业执行失败。
  - 建议：对于那些包含了特别耗时的 shuffle 操作的作业，建议增加重试最大次数（比如 60 次），以避免由于 JVM 的 full gc 或者网络不稳定等因素导致的数据拉取失败。在实践中发现，对于针对超大数据量（数十亿~上百亿）的 shuffle 过程，调节该参数可以大幅度提升稳定性。
- spark.shuffle.io.retryWait
  - 默认值：5s
  - 说明：代表了每次重试拉取数据的等待间隔，默认是 5s。
  - 建议：建议加大间隔时长（比如 60s），以增加 shuffle 操作的稳定性。
- spark.shuffle.memoryFraction
  - 默认值：0.2
  - 说明：该参数代表了 Executor 内存中，分配给 shuffle read task 进行聚合操作的内存比例，默认是 20%。
  - 建议：在资源参数调优中讲解过这个参数。如果内存充足，而且很少使用持久化操作，建议调高这个比例，给 shuffle read 的聚合操作更多内存，以避免由于内存不足导致聚合过程中频繁读写磁盘。
- spark.shuffle.manager
  - 默认值：sort
  - 说明：该参数用于设置 ShuffleManager 的类型。Spark 1.5 以后，有三个可选项：hash、sort 和 tungsten-sort。tungsten-sort 与 sort 类似，但是使用了 tungsten 计划中的堆外内存管理机制，内存使用效率更高。
  - 建议：由于 SortShuffleManager 默认会对数据进行排序，因此如果你的业务逻辑中需要该排序机制的话，则使用默认的 SortShuffleManager 就可以；而如果你的业务逻辑不需要对数据进行排序，那么建议参考后面的几个参数调优，通过 bypass 机制或优化的 HashShuffleManager 来避免排序操作，同时提供较好的磁盘读写性能。
- spark.shuffle.sort.bypassMErgeTreshold
  - 默认值：200
  - 说明：当 ShuffleManager 为 SortShuffleManager 时，如果 shuffle read task 的数量小于这个阈值（默认是 200），则 shuffle write 过程中不会进行排序操作，而是直接按照未经优化的 HashShuffleManager 的方式去写数据，但是最后会将每个 task 产生的所有临时磁盘文件都合并成一个文件，并会创建单独的索引文件。
  - 建议：当你使用 SortShuffleManager 时，如果的确不需要排序操作，那么建议将这个参数调大一些，大于 shuffle read task 的数量。那么此时就会自动启用 bypass 机制，map-side 就不会进行排序了，减少了排序的性能开销。但是这种方式下，依然会产生大量的磁盘文件，因此 shuffle write 性能有待提高。
- spark.shuffle.consolidateFiles
  - 默认值：false
  - 说明：如果使用 HashShuffleManager，该参数有效。如果设置为 true，那么就会开启 consolidate 机制，会大幅度合并 shuffle write 的输出文件，对于 shuffle read task 数量特别多的情况下，这种方法可以极大地减少磁盘 IO 开销，提升性能。
  - 建议：如果的确不需要 SortShuffleManager 的排序机制，那么除了使用 bypass 机制，还可以尝试将 spark.shffle.manager 参数手动指定为 hash，使用 HashShuffleManager，同时开启 consolidate 机制。在实践中尝试过，发现其性能比开启了 bypass 机制的 SortShuffleManager 要高出 10%~30%。
