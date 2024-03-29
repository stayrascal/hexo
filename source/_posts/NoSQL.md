---
title: NoSQL
date: 2019-08-02 22:13:04
tags:
---
## MongoDB、ElasticSearch、Redis、HBase这四种热门数据库的优缺点及应用场景

copy from https://zhuanlan.zhihu.com/p/37964096

### MongoDB
优缺点：
- MongoDB最大的特点是表结构灵活可变，字段类型可以随时修改。
- MongoDB不需要定义表结构这个特点给表结构的修改带来了极大的方便，但是也给多表查询、复杂事务等高级操作带来了阻碍。

如果你的数据的逻辑结构非常复杂，经常需要进行复杂的多表查询或者事务操作，那显然还是MySQL这类关系型数据库更合适。
MongoDB很适合那些表结构经常改变，数据的逻辑结构没又没那么复杂不需要多表查询操作，数据量又比较大的应用场景。

### Redis
优点：
- Redis的最大特点当然就是key-value存储所带来的简单和高性能了。
- Redis会把所有数据加载到内存中的，Redis能得到远高于MongoDB这类常规数据库的读写性能。

缺点：
- Redis的key-valule存储带来了性能这个优势，但是也给复杂查询带来了很多局限。
- Redis无法提供常规数据库所具备的多列查询、区段查询等复杂查询功能。
- 由于Redis需要把数据存在内存中，这也大大限制了Redis可存储的数据量，这也决定了Redis难以用在数据规模很大的应用场景中。

Redis牺牲了常规数据库中的数据表、复杂查询等功能，换来了很大的性能提升，特别适合那些对读写性能要求极高，且数据表结构简单（key-value、list、set之类）、查询条件也同样简单的应用场景。如果你的数据表结构还挺复杂，你还经常需要做一些复杂查询操作，那你最好还是老老实实用MongoDB或者SQL吧。

### ElasticSearch
优点：
- ES的特点，正如其名，那就是搜索。支持全文搜索。
- 除了搜索之外，ES还会自动的替你对所有字段建立索引，以实现高性能的复杂聚合查询，因此只要是存入ES的数据，无论再复杂的聚合查询也可以得到不错的性能，而且你再也不用为如何建立各种复杂索引而头痛了。

缺点：
- ES也有很多的短处，最明显的就是字段类型无法修改、写入性能较低和高硬件资源消耗。
- ES在数据结构灵活度上高于MySQL但远不如MongoDB
- 自动建立索引使得ES的写入性能也收到了影响，要明显低于MongoDB。
- 对于同样的数据ES占用的存储空间也要明显大于MongoDB

ES的全文搜索特性使它成为构建搜索引擎的利器。除此之外，ES很好的支持了复杂聚合查询这一特点还使得ES非常适合拿来作数据分析使用。ES的高成本和低写入性能这些缺点也注定了它不适合用在那些数据价值不高、对写入性能有要求、数据量大而成本受限的场景中。

### HBase
优点：
- HBase也继承了Hadoop项目的最大优点，那就是对海量数据的支持，以及极强的横向（存储容量）扩展能力。

- HBase的列式存储特性带来了海量数据规模的支持和极强的扩展能力，但是也给数据的读取带来很大的局限。
- 所有的查询都必须要依赖RowKey，这就使得很多复杂查询难以进行

HBase的列式存储特点带来了对海量数据的容纳能力，因此非常适合数据量极大，查询条件简单，列与列之间联系不大的轻查询应用场景。HBase不适合数据结构复杂，且需要复杂查询的应用场景。

### 总结
- 如果你对数据的读写要求极高，并且你的数据规模不大，也不需要长期存储，选redis；
- 如果你的数据规模较大，对数据的读性能要求很高，数据表的结构需要经常变，有时还需要做一些聚合查询，选MongoDB；
- 如果你需要构造一个搜索引擎或者你想搞一个看着高大上的数据可视化平台，并且你的数据有一定的分析价值或者你的老板是土豪，选ElasticSearch；
- 如果你需要存储海量数据，连你自己都不知道你的数据规模将来会增长多么大，那么选HBase。
