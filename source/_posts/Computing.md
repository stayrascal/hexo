---
title: Computing
date: 2019-07-22 22:50:25
tags:
---

大规模数据处理技术如果从MapReduce论文算起，已经前后跨越了十六年。我们先沿着时间线看一下大规模数据处理的重要技术和它们产生的年代。后面从MapReduce到Spark、Flink的演进特性来看大规模数据处理计算引擎应该具备什么样的能力。
<kbd>![Compution Tool](https://raw.githubusercontent.com/stayrascal/continuous-intelligence/master/ci-workshop-demo/docs/images/computing.png)</kbd>



如果参考人类发展史的划分，我们大概可以把大规模数据处理技术的演进划分为五个阶段：石器时代，青铜时代，铁器时代、蒸汽时代、电气时代。每个时代的演进都具有显著的意义。(*<small>当然我们这里只讨论大数据处理中偏MapReduce派系分支的计算引擎处理</small>*)

## 大规模数据处理的技术的五个时代和四代引擎
- 石器时代
> 随着互联网的蓬勃发展，数据量在呈指数型增长。早在2003年之前，各大厂商都已经严重面临大规模数据的处理难题，比如Google就已经面对大于600亿的搜索量。如何从海量的原始数据中挖掘出有效的信息，如何保证数据挖掘程序的容错性一直困扰着Google的工程师们。
> 这个时候大规模数据处理技术还处于仿徨阶段，每个公司甚至个人都有一套独立的数据处理工具，层次不齐，纷繁复杂，但是还没有提炼抽象出一个系统的办法。
- 青铜时代
> 直到2003年，MapReduce问世，开启了数据处理的青铜时代(*真正意义上的第一代大数据计算引擎*)。Jeff Dean和Sanjay Ghemawat发表革命性的论文[MapReduce:Simplified Data Processing on Large Clusters](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)，从纷繁复杂的业务逻辑中抽象出Map和Reduce这样足够通用的编程模型。利用这个简单的编程模型编写分布式程序，跑在那些廉价的机器上。在随后的十年中，MapReduce在Google内部广泛使用，不断优化，投入了大量的人力物力将这套系统推向了前所未有的高度。
- 铁器时代
> MapReduce虽好，但是对于上层应用来说，在一些复杂的应用场景下，编写MapReduce程序会变得越来越复杂，工程师们想法设法的拆分算法，实现多个Job的串联，开发维护工作量大。这种弊端催生了DAG(有向无环图)框架的诞生，支持DAG的框架被划分为第二代计算引擎，如[Tez](https://hortonworks.com/blog/expressing-data-processing-in-apache-tez/)以及Ooize，此时计算引擎处理的大多数都还是批处理任务。
- 蒸汽机时代
> 时代的步伐并不会满足于计算引擎的一点点进步，计算时间太长的问题一直被诟病。直到基于分布式内存的Spark作为第三代计算引擎出现，才逐渐取代了MapReduce的龙头地位(*<small>2014年之后，国际各大互联网厂商几乎都没有人再直接写MapReduce程序了</small>*)，并且逐步向流处理开始发力。第三代计算引擎的出现，促进了上层应用快速发展，如各种迭代计算以及流计算和SQL等。
- 电气时代
> 随着时代的前行，上层应用越来越注重实时流处理技术，以及批流一体化的计算引擎框架。这时批流一体化的新贵Flink应运而生；同时Spark也在不断弥补自己在实时流处理上的短板，增加新特性；而Google也在不断发力，推出Apache Beam。师夷长技以制夷，各大计算引擎相互参考，不断优化着自己的短板，推出新的特性，争当第四代计算引擎扛旗手。

如果重登历史的巨轮，一路走马观花般地来看看大数据处理计算引擎的每一个转折点，我们不难发现每一次的升级都是在业务和技术开发的双重推动下稳步前行，每一次升级都是对数据模型抽象能力的提升。那么，我们不禁思考，下一代(第四代)计算引擎应该具备什么样的能力？回答这个问题前，我们先来看看每一代计算引擎都有什么问题？

## 历代计算引擎的问题和创新点
### MapReduce
首先从第一代计算引擎开始。MapReduce虽好，但饮者不会贪杯。不可否认，作为划时代的一笔，MapReduce解决了当时大规模数据处理的大部分问题。但是其由于某些问题，依旧被开发者诟病：
<small>

> - 开发复杂度高，只提供了Map和Reduce两个操作
- MapReduce 模型的抽象层次低，大量的底层逻辑都需要开发者手工完成。开发者在使用MapReduce的时候，需要严格的遵循Map和Reduce步骤，当构造更为复杂的处理架构时，往往需要协调多个Map和多个Reduce任务。很多现实的处理场景并不适合用这个模型来描述。
- 高昂的维护成本
    - 程序每一步的MapReduce都可能出错，为了处理这些异常，很多人开始设计自己的协调系统和容错系统，比如做一个状态机来协调多个MapReduce任务，这样大大增加了整个系统的复杂度。（*<small>如果你Google一下“MapReduce orchestration”关键字，你会发现很多书整篇都在讲如何协调MapReduce任务</small>*）
- 配置太复杂，错误的配置最终导致数据
    - 实际的开发过程中，不是每个人都对MapReduce细微的配置信息了如指掌，对一个没有用过MapReduce的新手来说是很难发挥好MapReduce的性能的。(*<small>Google有500多页的关于MapReduce的性能调优的说明手册，可见其复杂度之高</small>*)
    - 调优的过程也比较漫长。Google曾在2007年到2012年间做过一个对于1PB数据的大规模排序，来测试MapReduce的性能。花了五年时间从12小时优化到0.5个小时，包括缓冲大小，分片多少，预抓取策略，缓存大小等等。
- 计算效率低
    - 每一个Job的计算结果都会存储在HDFS文件系统中，每一步的计算都需要进行硬盘的读写和写入，大大增加了系统的的延迟。所以MapReduce对于迭代算法的处理性能很差，而且很耗资源。
- MapReduce只支持数据批处理，应对流处理的时候不再得心应手
    - 真正的业务系统，批处理和流处理常常是混合共生，或者频繁切换的。
</small>
MapReduce作为分布式数据处理的开山鼻祖，虽然有很多缺陷，但是它的思想不仅没有过时，而且还影响了新的数据处理系统的设计，如Spark，Storm，Presto，Impala等。
传统的MapReduce任务之所以运行速度慢，主要是由于需要将计算的中间结果落地到硬盘这种稳定的存储介质中来防止运行结果丢失，每一次获取中间结果，都需要进行一次硬盘的读取，反复读写操作以及潜在的数据复制和序列化操作大大的提高了计算的延迟。

### Tez
作为第二代计算引擎的代表Tez是一个运行在Yarn上支持DAG(有向无环图)作业的计算框架，是对MapReduce数据处理的归纳。它把Map/Reduce过程拆分成若干个子过程，同时可以把多个Map/Reduce任务组合成一个较大的DAG任务，减少了Map/Reduce之间的文件存储。同时合理组合其子过程，也可以减少任务的运行时间。**Tez的问世解决了MapReduce开发复杂度高，计算效率低的问题。**

但Tez并不直接面向最终用户，事实上它允许开发者为最终用户构建性能更快、扩展性更好的应用程序。但Tez的目的主要是帮助Hadoop处理很多需要近实时查询、机器学习的用例场景。

### Spark
作为第三代计算引擎的Spark**几乎解决了上面列出的MapReduce面临的所有问题**。
<small>
> 相较与MapReduce的数据模型，Spark提出了另外一种基于分布式内存的基本数据抽象模型RDD（弹性分布式数据集：表示已被分区、不可变的，并能够被并行操作的数据集合），在各方面都有很大的提升，同时保证了系统的稳定性、错误恢复和可拓展性，并且尽可能的减少磁盘I/O操作。
>
   - 首先，它尽可能的把数据存在内存中，大大的提高了数据处理的效率；
   - 其次，它是分区存储，天然支持并行处理
   - 它存储了每一步骤计算结果之间的依赖关系，大大提升了数据容错性和错误恢复的正确率，是Spark更加可靠
</small>

与编写MapReduce程序一样，针对RDD的编程还是比较偏向于底层，然而我们并不是总需要在RDD的层次进行编程，同样针对RDD的编程需要开发人员手动进行性能调优。幸运的是Spark生态系统提供了一个高级结构化数据抽象模型和功能模块，**使开发变得简单**。比如Spark SQL，DataFrame，DataSet。
<small>
> 从发展历史角度来看，RDD API是在Spark设计之初就有的，是整个Spark框架的基石。为了方便熟悉数据库和SQL的开发人员使用，在RDD的基础上，Spark创建了DataFrame API，开发人员可以方便的对数据的列进行操作。在Spark 1.6中，引入了DataSet，它在DataFrame的基础上添加了对数据每一列的类型的限制。在Spark2.0中，DataFrame和DataSet被统一。DataFrame作为DataSet[Row]存在。
</small>

DataSet和DataFrame都是基于RDD的，都拥有RDD的基本特性，但是性能要比RDD更好。是因为Spark程序运行时，Spark SQL的查询优化器会对语句进行分析，生成优化过的RDD在底层执行。**基于DataFrame和DataSet API开发的程序会被自动优化，使得开发人员不需要操作底层的RDD API来进行手动优化，大大提升开发效率。**(*<small>但是 RDD API 对于非结构化的数据处理有独特的优势，比如文本流数据，而且方便底层操作。所以在开发中，还是要根据实际情况来选择使用哪种API</small>*)

无论是DataFrame API还是DataSet API，都是基于批处理模式对静态数据进行处理的。当需要对数据进行流处理操作时，Spark生态圈引入了Spark Streaming。
<small>
> Spark Streaming提供了一个对流数据的抽象DStream(由很多个序列化的RDD构成)。DStream可以来自Kafka、Flume或者HDFS的流数据生成，也可以由别的DStream经过各种转换操作得来。由于Spark Streaming的底层是基于RDD实现的，所以RDD的优良特性它都有：
>
   - 数据容错性，如果RDD某些分区丢失了，可以通过依赖信息重新计算恢复
   - 运行速度，DSteam可以通过持久化方法将数据流放在内存中，在遇到需要多次迭代计算的程序中，速度优势十分明显
   - 作为Spark生态的一部分，可以和Spark核心引擎、Spark SQL、MLib无缝衔接
</small>

但是Spark Streaming由于不支持太小的批处理时间间隔而带来的秒级别计算延迟，在实时性要求比较高的场景下被诟病。所以引申出了Structured Streaming。
<small>
> 
- Spark Streaming提供的DStream API与RDD API比较类似，相对比较低level，前面已经提到RDD API开发的程序需要开发者自己构造RDD的DAG执行图，依赖开发者自己去优化。既然Spark已经提供了DataFrame，DataSet这种高级API，可以自动优化Spark程序了，那么能否将他们应用在流处理中呢？答案是肯定的。2016年，Spark在2.0版本中推出了结构化流数据处理模块Structured Streaming。Structured Streaming是基于Spark SQL引擎实现的，依赖Spark Streaming，在开发者眼里，流数据和静态数据是没有区别的，开发者可以像处理批处理静态数据那样去处理流数据。随着流数据的持续输入，借助于Spark SQL的优化引擎，Spark SQL引擎会帮助我们持续地处理新数据，更新计算结果。所以Structured Streaming的应用程序性能很好。(*<small>现在Spark社区几乎停止了关于Spark Streaming的更新，将重心投放到Structured Streaming上了</small>*)。
- 实时性，其次Spark Streaming是准实时的，它能做到最小延迟在一秒左右，虽然Structured Streaming的思想类似，但是相比Spark Streaming来说，它能做到更小的时间间隔，*最小延迟在100毫秒左右*，在Spark2.3版本中。*Structured Streaming引入了连续处理模式，可以做到真正的毫秒级延迟*，拓展了Structured Streaming的应用广度。
- 对事件时间的支持，Spark Streaming把数据接收到的时间切分成一个个RDD进行批处理，所以它很难基于数据本省的产生时间来进行处理。而Structured Streaming对基于事件时间的处理有很好的支持。
- Structured Streaming还有其它优点，比如它有*更好的容错性，保证了端到端的exactly once语义等*
</small>

无论是Spark Streaming还是Structured Streaming，Spark流处理的实时性还是不够，所以还是无法用在一些对实时性要求很高的流处理场景中，这是因为Spark的流处理是基于微批处理的思想，它把流处理看做批处理的一种特殊形式，每次收到一个时间间隔的数据才会去处理，所以天生很难在实时性上有所提升，虽然Spark 2.3提出了连续处理模型，但是支持功能有限，还有很长的路要走。

### Flink
想要在流处理的实时性上提升，就不能继续用微批处理的模式，要做到每当有一条数据输入就立即处理，不做等待。Flink就是这方面的翘楚，它采用基于操作符的连续流模型，可以做到**微秒级别的延迟**。
<small>
> Flink中最核心的数据结构Stream，它代表一个运行在多个分区上的并行流，与Spark的RDD不同的是，Stream代表一个数据流而不是静态数据的集合，它包含的数据是随着时间增长变化的。
> Flink提供两个核心API：用于做批处理的DataSet API和和流处理的Data Stream API。
</small>

Flink和Spark都有很多相同点：
- 都基于内存计算
- 都有**统一的批处理和流处理API**，都支持类似SQL的编程接口
- 都支持很多相同的转换操作，编程都是类似于Scala Collection API的函数式编程模式
- 都有**完善的错误恢复机制**
- 都支持Exactly once的语义一致性

当然也有很多不同点：
- 从**流处理**的角度来讲，Spark是基于微批处理，把流数据看成一个个小的批处理数据块分别处理，延迟性只能做到秒级。Flink基于每个时间处理，每当有新的数据输入都会立即处理，是真正的流式计算，支持毫秒级计算。基于同样的原因，Spark只能支持基于时间的窗口操作(处理时间或者事件时间)，而Flink支持的窗口操作非常灵活，不仅支持时间窗口，还支持基于数据本身的窗口，开发者可以自定义想要的窗口操作
- 从**SQL功能**的角度来讲，Spark和Flink分别提供SparkSQL和Table API提供的SQL交互支持。相比而言，Spark SQL支持更好，相应的优化、拓展和性能更好，Flink在这方面还有很大提升空间
- 从**机器学习**迭代计算的角度来讲，Spark对机器学习的支持很好，可以在内存中缓存中间计算结果加速机器学习算法的运行。但是大部分机器学习算法其实是一个有环的数据流，然而Spark是用无环图来标示的。Flink支持在运行时间总的有环数据流，从而可以更有效的对机器学习算法进行运行
- 从**生态系统**角度来讲，Spark的社区更加活跃，Spark有着Apache旗下最多的开源贡献者，有很多不同的库用在不同场景。Flink比较新，社区活跃度不如Spark活跃，各种库的功能不如Spark全面。但是Flink在国内背后有阿里、华为、美团的加持，相信这块这后面会不断完善。

### Beam
在Spark和Flink分别发力争当下一代计算引擎的领头羊时，来自大洋彼岸的Google发出了自己的声音。2016年，Google联合Talend、Cloudera等大数据公司，基于Dataflow Model思想开发出一套SDK，Apache Beam(**B**atch + Str**eam**ing)，其含义就是统一了批处理和流处理的一个框架。

**Beam提供了一套统一的API来处理这两种数据处理模式**，开发者只需要专注于在数据处理的算法上，不用花时间去对两种数据处理模式的差异进行维护。它将工程师写的算法逻辑和底层运行的环境分隔开，即使用Beam提供的API写好数据处理逻辑后，这个逻辑可以不做任何修改，直接放到任何支持Beam API的底层系统上运行，如Google Cloud Dataflow，Flink，Spark等。在Beam上，这些底层运行的系统被称为Runner，Beam提供了Java、Python、Golang的SDK，支持多语言编写程序。（*<small>熟悉深度学习的朋友可以把Beam理解为Keras，它编写的程序可以运行在TensorFlow、Theano、CNTK这些backends上，或者把Beam理解成SQL，它编写的程序就是Query，这个Query可以放在任何数据库系统上运行，比如Mysql或者Oracle上</small>*）
<small>
> Apache Beam和其它开源项目不太一样，*它不是一个数据处理平台，本身无法对数据进行处理**。**Beam所提供的是一个统一的编程思想*，通过这个统一的借口编写符合各自需求的处理逻辑，这些处理逻辑被转换为底层引擎相应的API去运行(是有一定的性能损耗的)。Beam的编程模型会涉及到4个概念：窗口、水印、触发器和累加模式
>
   - 窗口(Window)：窗口将无边界的数据更具时间实践分成了一个个有限的数据集
   - 水印(Watermark):用来表示与数据事件时间相关联的输入完整性的概念。在数据处理中，水印是用来测量数据进度的。对于事件时间为X的水印表示数据处理逻辑已经得到了所有事件时间小于X的无边界数据
   - 触发器(Triggers):表示具体在什么时候，数据处理逻辑会真正的触发窗口中的数据倍计算。
   - 累加模式(Accumulation)：如果我们在同一个窗口中得到多个运算结果，如何处理这些运行结果，是丢弃、追加，还是直接覆盖
</small>

## 大规模数据处理计算引擎该有的样子
> <small>世界上最好的样子，莫过于我喜欢的样子，你都有。 <small>---徐志摩没有说过</small></small>

历史的篇章就翻到这里。让我们回到最初的问题：新一代的计算引擎应该具备什么样的能力？其实从Spark的发展旅程和Flink、Beam的特性，不难发现一些基本的雏形，下面站在工程师的角度来简单归纳一下：

- **编程简单，易维护**：你需要提供一种让多步骤数据处理变得易于维护抽象技术
    - 作为工程师的我们或许可以用有向无环图（DAG）来抽象表达。因为有向图能为多个步骤的数据处理依赖关系，建立很好的模型。
    - 支持SQL语言。现在机器学习都能SQL化了，Kakfa也能SQL化了，能一行SQL解决的问题，为什么还要去写多行代码？
    - 支持多语言的客户端
- **自优化**：你已经是一个成熟的计算引擎了，我不想要复杂的配置，你需要能自动进行性能优化
- **业务逻辑描述和计算分离**：你可以把数据处理的描述语言，与背后的运行引擎解耦合开来
    - 在使用有向无环图进行数据抽象描述之后，数据处理描述语言部分已经可以和运算引擎部分分离了。有向图可以作为数据处理描述语言和运算引擎的前后端分离协议。
    - 业务团队可以使用不用语言的客户端进行数据描述，计算引擎由C++实现，由数据底层团队维护并高度优化
    - 数据描述可以在本地写，计算引擎在云端执行
- **批流一体化**：你要提供统一批处理和流处理的编程模型，我不想再写两套代码了
    - 批处理处理的是有界离散的数据，比如处理一个文本文件；流处理处理的是无界连续的数据，比如每时每刻的支付宝交易数据。
    - 真正的业务系统，批处理和流处理常常是混合共生，或者频繁切换的
    - 不论是批处理还是流处理的，都用统一的数据结构表示。编程的API也需要统一。不论业务需求怎么样，开发者只需要学习一套API
- **自监控**：你要在架构层面提供异常处理和数据监控的能力，对数据处理的每一步提供自动的监控平台
    - 做过大规模数据处理系统的人都很明白：一个复杂的数据处理系统中，难的不是开发系统，而是异常处理。
    - 下一代大规模数据处理框架要有一套基本的数据监控能力，对于数据处理的每一步提供自动的监控平台



## 题外话

##### 题外话1: Spark SQL
<small>
> - 在那个MapReduce横行的时代，大部分开发人员最熟悉的还是传统的关系型数据库，MapReduce的开发对于他们来讲使用难度较大，所以Hive就应运而生了，Hive提供类似SQL的编程接口，HQL语句经过语法解析、逻辑计划、物理计划转换成MapReduce程序运行，使开发人员很容易对HDFS上存储的数据进行查询和分析。
> - Spark刚问世的时候，也开发了一个Shark来支持SQL语言查询，它的本质是Hive，修改了Hive的内存管理模块，大幅度优化了运行速度，是Hive的10-100倍。但是Shark对于Hive的依赖严重影响了Spark的发展。Spark想要的不只是一个SQL，而是想要定义一个统一的技术栈和完整的生态，眼睛里面揉不得沙子的Spark不可能允许有这样的外在依赖，所以就将Shark交给Hive进行管理，转而开发SparkSQL
> - 摒弃了Shark将SQL语句转化为Spark RDD的执行引擎，就想脱缰的野马一样跑的“飞快”。Spark SLQ不仅将关系型数据库的处理模式和Spark的函数式编程相结合，还兼容了Hive、RDD、JSON、CSV等多种数据格式
</small>

##### 题外话2：流处理计算的技术选型
<small>
> 看起来Flink在数据处理这块的能力完爆Spark，但是*在下列场景下，Spark更加适合，Spark可以一站式解决这些问题，无需其他的数据处理平台*：
> - 数据量非常大而且逻辑复杂的批数据处理，并且对计算效率有较高要求(比如个性化推荐、广告精准营销)
> - 基于历史数据的交互式查询，要求相应较快
> - 基于实时数据量的数据处理，延迟性要求在数百毫秒到数秒之间
</small>

##### 题外话3：流处理计算编程模型 WWWH
<small>
> - *What result are calculated?**: 我们要计算什么？比如经典例子WordCount就是要计算每个词出现的次数
> - *Where in event time are calculated?*: 计算什么时间范围的数据？一般通过窗口来表示，比如计算的一点两点的访问量
> - *When in processing are result materialized?*: 何时将计算结果输出？可以通过水印和触发器配合触发计算。可根据时间信号或者窗口元素计数来触发，比如定义好固定窗口为1个小时，元素个数为10，从凌晨0:00开始，每当1:00,2:00或者窗口内个数超过10时，就会触发计算将结果输出
> - *How do refinement of result related?*: 后续数据处理结果如何影响之前的处理结果？这个可以通过累加模式解决(丢弃，累积)
</small>

##### 题外话4：Apache Beam
<small>
> Apache Beam最早来自于Google内部产生的FlumeJava。在Google内部，基于前面提到的关于MapReduce的各种问题，Google的工程师们开始考虑如何解决那些问题，FlumeJava在这样的背景下诞生了，并且在2010的时候公开了其论文[FlumeJava: Easy, Efficient Data-Parallel Pipelines](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/35650.pdf).

> FlumeJava的思想是将所有的数据都抽象为一个叫PCollection的数据结构，在Map和Reduce的思想上，抽象出了4个原始操作：parallelDo, groupByKey, combineValues和flatten，这4种原始操作能表达任意Map或者Reduce逻辑，同时它采用了一种Deferred Evalution的技术(遍历静态代码，构建一个可执行的有向无环图，有了图之后就可以很好的优化代码)来优化开发写的代码。

> 但是FlumeJava有一个弊端，就是只能支持批处理的任务，对于流处理的框架却是来自另外一个项目[MillWheel: Fault-Tolerant Stream Processing at Internet Scale](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41378.pdf)。Google的工程师能回头一看，优秀，但是貌似我们可以再优秀一点，于是集合多个框架(包括MapReduce)的Dataflow Model诞生了[The Dataflow Model: A Practical Approach to Balancing Correctness, Latency, and Cost in Massive-Scale, Unbounded, Out-of-Order Data Processing](https://www.vldb.org/pvldb/vol8/p1792-Akidau.pdf)。

> 但是Dataflow Model的程序需要运行在Google的云平台上，如何才能在其它的平台商跑起来呢，所以为了解决这个问题，才有了Apache Beam的诞生
</small>

##### 题外话5: 个人解读一下现在计算引擎发展的趋势
<small>
> 首先统一计算平台和批流一体化已经是主要趋势了。

> 对于Spark来说，它是很优秀的一个引擎，依靠强大的社区、生态圈、all in one的组件栈，占据大部分的市场。从Spark2.3 Structured streaming出来后的批流api的统一，以及目前在不断的优化continues Mode，可以看出Spark在逐渐弥补自己实时流处理计算的短板。此外Spark还引入第四种调度策略Kubernetes clusters集成如火如荼的云平台设施；引入并且不断改善pandas的性能来提升PySpark让很多重度依赖pandas的数据分析师得心应手；以及对深度学习的支持。可以说Spark的棋盘铺的很大，想法设法的讨开发者的喜爱。所以这也是我不太认可现在大部分道友认为Flink已经是下一代计算引擎的领头羊的这个观点，凭借Spark这些中盘的布局，在收官阶段未必没有和Flink的一拼之势。

> 而Flink，的确因为阿里的运营，在国内火了。但也展现了它的独有优势，更加贴近dataflow model的思想。同时，基于社区以及阿里、华为小伙伴的努力，flink的table/sql 的api也得到的很大的增强，提供了批流统一的api。虽然底层需要分化为dataset和datastream以及runtime层的batchTask和StreamTask，但是现在也在rethink the stack，这个点在2019 SF的大会也几乎吸引了所有人。但就现状而言，flink的确有着理念上的优势（流是批的超集），同时也有迅猛上升的趋势。同时Flink也有自己的机器学习库(FlinkML)和图分析库(Gelly)，以及强大的复杂时间处理库(CEP)。总之Spark有的，他全都要，Flink想要的，可不仅仅只是一个流计算而已。

> 再来看看Beam，不可否认Beam的新颖性和所站的高度，凭借Google的背书，或许以后的应用面特别广，但在国内要大面积的接受采纳接受的话，大概还需要四五年的样子。而且Beam只是对批流处理进行了抽象一体化，计算还是要依赖其它计算引擎，目前对SQL，Machine Learning的支持也不是很完善(**<small><small>但我觉得Google要想要支持也是很容易，特别和其它计算框架如TensorFlow等结合起来，这画面太美不敢看</small></small>**)。但Beam提出的抽象模式很值得我们借鉴，不仅仅只是应用在数据处理方面。
</small>