---
title: TSDB
date: 2019-11-26 18:58:53
tags:
---

## 时序业务有哪些特点
- 持续产生海量数据，没有波峰波谷
- 数据都是插入操作，基本没有更新删除操作
- 近期数据关注度更高，未来会更关注流式处理这个环节，时间久远的数据极少被访问，甚至可以丢弃
- 数据存在多个维度的标签，往往需要多维度联合查询以及统计查询

## TSDB关注的核心技术点
- 高吞吐量写入能力
    - 系统具有水平扩展性
    - 单机LSM体系结构
- 数据分级存储/TTL
- 高压缩率
- 多维度查询能力
- 高效聚合能力
- 未来技术点

## 时序数据库产品
- OpenTSDB
- Druid
- InfluxDB
- Beringei

## 时序数据存储模型设计
一个时序数据点（point）由datasource(tags) + metric + timestamp这三部分唯一确定。
### KV数据库
- OpenTSDB
    - HBase
        - RowKey: metric + timestamp + datasource(tags)
        - 缺点：
            - 存在很多无用的字段: 一个KeyValue中只有rowkey是有用的，其他字段诸如columnfamily、column、timestamp以及keytype从理论上来讲都没有任何实际意义，但在HBase的存储体系里都必须存在，因而耗费了很大的存储成本。
            - 数据源和采集指标冗余: KeyValue中rowkey等于metric+timestamp+datasource，试想同一个数据源的同一个采集指标，随着时间的流逝不断吐出采集数据，这些数据理论上共用同一个数据源(datasource)和采集指标(metric)
            - 无法有效的压缩: HBase没有数据类型的概念，没有schema的概念，不能针对特定数据类型进行特定编码，只能选择通用的编码
            - 不能完全保证多维查询能力: HBase本身没有schema，目前没有实现倒排索引机制，所有查询必须指定metric、timestamp以及完整的tags或者前缀tags进行查询，对于后缀维度查询也勉为其难。
    - OpenTSDB优化：
        - timestamp并不是想象中细粒度到秒级或毫秒级，而是精确到小时级别，然后将小时中每一秒设置到列上。这样一行就会有3600列，每一列表示一小时的一秒。这样设置据说可以有效的取出一小时整的数据。
        - 所有metrics以及所有标签信息（tags）都使用了全局编码将标签值编码成更短的bit，减少rowkey的存储数据量。但是全局编码字典需要存储在内存中，在海量标签值情况下，字典所需内存会很大。
- Kudu
### Druid
    - 没有主键，每一列独立存储，采用编码字典对标签值进行局部编码，可以有效的减少多内存的压力。
    - 可以根据每列的类型设置对应的压缩策略，压缩效果好。
    - 支持多维度查询: 为dataSource的每列分别设置了Bitmap索引。但是在指定数据源范围内查询并没有OpenTSDB高效，Druid先根据标签走Bitmap索引，再最后使用与操作找到满足条件的行号，这个过程需要一定的开销。
### InfluxDB
只存储时序数据，因此在数据模型的存储上可以针对时序数据做非常多的优化工作。InfluxDB时序数据模型设计很好地将时间序列按照数据源以及metric挑选了出来，解决了维度列值冗余存储，时间列不能有效压缩的问题。
- seriesKey: measurement+datasource(tags)。measurement像是表的概念。内存中实际上就是一个Map：<SeriesKey+fieldKey, List<Timestamp|Value>>，Map中一个SeriesKey+fieldKey对应一个List，List中存储时间线数据。数据进来之后根据measurement+datasource(tags)拼成SeriesKey，加上fieldKey，再将Timestamp|Value组合值写入时间线数据List中。
    - 好处:
        - 同一数据源的tags不再冗余存储。
        - 时间序列和value可以在同一个Block内分开独立存储，独立存储就可以对时间列以及数值列分别进行压缩。
        - 对于给定数据源以及时间范围的数据查找，可以非常高效的进行查找。
        - InfluxDB内部实现了倒排索引机制，即实现了tag到SeriesKey的映射关系，可以实现实现多维查找。
- 系统架构设计
    - DataBase
    - Retention Policy (RP)
    - Shard Group
    - Shard
- InfluxDB中数据的组织形式: 先按照RP划分，不同过期时间的数据划分到不同的RP，同一个RP下的数据再按照时间Range分区形成ShardGroup，同一个ShardGroup中的数据再按照Series进行Hash分区，将数据划分成更小粒度的管理单元。Shard是InfluxDB中实际工作者，是InfluxDB的存储引擎
- TSMFile: 为了保证时序数据写入的高效，InfluxDB采用LSM结构，数据先写入内存以及WAL，当内存容量达到一定阈值之后flush成文件，文件数超过一定阈值执行合并，和其它LSM系统大同小异。InfluxDB针对时序数据做了针对性的存储改进，成为TSM(Time-Structured Merge Tree)。每隔一段时间，InfluxDB内存中的时序数据就会执行flush操作将数据写入到文件。TSM由Series Data Section以及Series Index Section两个部分组成。
    - Series Data Block: 由四部分构成
        - Type: seriesKey对应的时间序列的数据类型
        - Length: 用于读取Timestamps区域数据，解析Block
        - Timestamps: 时间值存储在一起形成的数据集，间隔固定，压缩率高
        - Values: 数据类型相同，易压缩
    - Series Index Block: 由Index Block Meta以及一系列Index Entry构成
        - index Block Meta最核心的字段是Key
        - Index Entry表示一个索引字段，指向对应的Series Data Block
            - Offset表示该Data Block在文件中的偏移量
            - Size表示指向的Data Block大小
            - Min Time和Max Time表示指向的Data Block中时序数据集合的最小时间以及最大时间
- 时序数据读取
    - 首先根据Key找到对应的SeriesIndex Block，因为Key是有序的，所以可以使用二分查找来具体实现
    - 找到SeriesIndex Block之后再根据查找的时间范围，使用[MinTime, MaxTime]索引定位到可能的Series Data Block列表
    - 将满足条件的Series Data Block加载到内存中解压进一步使用二分查找算法查找即可找到
### Beringei
InfluxDB没有很好的解决写入缓存压缩的问题：InfluxDB在写入内存的时候并没有压缩，而是在数据写入文件的时候进行对应压缩。
Beringei的时序数据模型设计与InfluxDB基本一致，也是提出类似于SeriesKey的概念。
和InfluxDB的区别: 
- 文件组织形式不同。Beringei的文件存储形式按照时间窗口组织，比如最近5分钟的数据全部写入同一个文件，这个文件分为很多block，每个block中的所有时序数据共用一个SeriesKey。Beringei文件没有索引，InfluxDB有索引。
- Beringei目前没有倒排索引机制，因此对于多维查询并不高效。