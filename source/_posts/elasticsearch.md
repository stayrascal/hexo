---
title: elasticsearch
date: 2019-07-22 22:56:02
tags:
---


## 唯品会实时平台的核心业务
    - 实时推荐：作为电商的重点业务，包含多个实时特征
    - 大促看板：包含各种维度的统计指标（例如：各种维度的订单、UV、转化率、漏斗等），供领导层、运营、产品决策使用
    - 实时数据清洗：从用户埋点收集来数据，进行实时清洗和关联，为下游的各个业务提供更好的数据
    - 互联网金融
    - 安全风控
    - 与友商比价
    - 内部服务的监控系统：Logview、Mercury、Titan
    - 实时数据同步系统：VDRC



# ElasticSearch

## Kibana short cut
    - cmd + / : 查看API帮助文档
    -

### ELK with docker
    - https://github.com/deviantony/docker-elk

### 基本API
    - https://www.elastic.co/guide/en/elasticsearch/reference/7.2/docs.html
    - Nodes: https://www.elastic.co/guide/en/elasticsearch/reference/7.2/cat-nodes.html
        - GET /_nodes/es7_01,es7_02
        - GET /_cat/nodes?v
        - GET /_cat/nodes?v&h=id,ip,port,v,m
    - Cluster: https://www.elastic.co/guide/en/elasticsearch/reference/7.2/cluster.html
        - GET _cluster/health
        - GET _cluster/health?level=shards
        - GET /_cluster/health/kibana_sample_data_ecommerce,kibana_sample_data_flights
        - GET /_cluster/health/kibana_sample_data_flights?level=shards
        - GET /_cluster/state
        - GET /_cluster/settings
        - GET /_cluster/settings?include_defaults=true
    - Shards: https://www.elastic.co/guide/en/elasticsearch/reference/7.2/cat-shards.html
        - GET _cat/shards
        - GET _cat/shards?h=index,shard,prirep,state,unassigned.reason
    - Indices
        - 创建Index: PUT my_index
        - 查看所有Index: _cat/indices
    - Document
        - Create Document:
            - PUT my_index/_doc/1?op_type=create {"user": mike, "comment": "test"}
            - PUT my_index/_create/1 {"user": mike, "comment": "test"}
            - POST my_index/_doc  {"user": mike, "comment": "test"}
        - Update Document: 增量修改
            - POST my_index/_update/1  {"doc":{"user": mike, "comment": "test"}}
        - Read Document:
            - GET my_index/_doc/1
        - Delete Document:
            - DELETE my_index/_doc/1
    - 批量操作
        - 批量查询:
            - GET /_mget {"doc": [{"index": "index1", "_type":"_doc", "_id": "1"}, {"index": "index2", "_type":"_doc", "_id": "1"}]}
            - GET /test/_mget {"doc": [{"_type":"_doc", "_id": "1"}, {"_type":"_doc", "_id": "2"}]}
            - GET /test/_doc/_mget {"doc": [{"_id": "1"}, {"_id": "2"}]}
            - GET /test/_doc/_mget {"ids": ["1", "2"]}
            - POST /index/_msearch {} {"query" : {"match_all" : {}},"size":1}
        - 批量操作：(批量操作的数据量不应过大，建议在5-15MB，默认不能超过100M，建议1000-5000个文档)
            - POST _bulk
    - Search API
        - URL 查询
            - curl -XGET "http://es:9200/index/_search?q=field:value"
            - curl -XGET "http://es:9200/index/_search?q=value&df=field&sort=field:desc&siez=10&timeout=1s"
            - curl -XGET "http://es:9200/index/_search" -H 'Content-Type:application/json' -d '{ "query": { "match_all": {} } }'


## 运维

### ES如何做异地两中心之间的集群间同步
    - CCR
    - 采用active-active模式运行，程序同时进行多写，读取时使用GTM实现

### ES安装环境
    - 推荐使用SSD
    - JVM的最大Xmx不要超过30G
    - 主分片单个存储不要超过30G
    - 副本分片至少设置为1

### ES的JVM垃圾回收算法建议
    - CMS

### 单索引数据量达到多少后需要考虑分片
    - 分片大小控制在30G到100G之间比较好



## ES相对MySQL性能分析
- 写性能较慢：数据写入 -> ESBuffer -> translog(默认落盘) -> 生成倒排索引 -> 写入磁盘
    - 罗盘多次：ES先将写入的数据通过translog落盘，保证数据不丢失，然后更具translog的内容周期性的将索引和Doc写入磁盘，完成存储。
    - 倒排索引分片合并消耗IO：ES内部优化会将索引内的不同倒排索引分片合并，需要消耗一定的IO
    - translog可以不落盘
- 占用存储空间小：存储大小 = 数据 + 索引
    - 数据存储大小接近：Doc的存储在磁盘的大小和MySQL数据存磁盘的大小接近
    - 索引大小差异大：倒排索引不存储重复维度，Doc Value列式存储压缩比例大；MySQL为了CRUD效率的均衡使用B/B+数，压缩比例有限
    - ES在分析场景可以不存数据
- 查询聚合速度快：查询速度 = 过滤速度 + 聚合速度 + 排序速度
    - 过滤：通过倒排索引表过滤文档，倒排索引表会被缓存到内存，复杂度为O(1)，MySQL复杂度为O(logN)。
    - 聚合: ES使用倒排索引，时间复杂度O(logN)，MySQL的GroupBy为O(n)
    - 排序：Doc Value压缩比高，从磁盘读取速度快，同时Doc都是已经倒排好的，索引在获取TopN数据的时候非常高效，MySQL默认未排序，需要单独优化。

## 主流数据分析OLAP引擎比较
- 大数据OLAP：Apache Kylin
    - Hadoop之上的SQL查询接口及多维分析(OLAP)能力以支持超大规模数据。
- 轻量级OLAP引擎：
    - Druid(用于大数据实时查询和分析的高容错、高性能开源分布式系统)
        - 为分析而生-Druid是为OLAP工作流的探索性分析而构建，支持各种过滤、聚合和查询
        - 快速的交互式查询-低延迟数据摄取架构允许在他们创建后毫秒内科被查询到
        - 高可用性-Druid的数据在系统更新是依然可用，规模的扩大和缩小都不会造成数据丢失
        - 可拓展-Druid已实现每天能处理数十亿事件和TB级数据
    - ES：以更轻量的方式实现上述功能

