---
title: emr
date: 2019-07-22 22:54:46
tags:
---
# Amazon EMR

### 存储
- HDFS
- EMRFS
- Local File System

### 集群资源管理
- YARN

### 数据处理框架
- 批处理
- 交互式处理
- 内存中处理
- 流式处理

### 应用程序和项目
- Hadoop
- Hive
- Pig
- Spark Streaming

### Amazon EMR 可以接受的输入类型
- 集群的默认输入格式是文本文件，每行都使用换行符 (\n) 分隔，这是最常用的输入格式。
- 如果输入的数据格式不是默认的文本文件，可以使用 Hadoop 接口 InputFormat 指定其他输入类型。
- Hive可以使用串行器/解串器 (SerDe) 将给定格式的数据读入 HDFS。

### 数据导入
- 可以先上传到S3，然后加载到集群
- 可以利用Hadoop分布式缓存功能将文件从分布式文件系统传输到本地文件系统
    - 分布式缓存是一种Hadoop功能，可以在映射或归约任务需要访问通用数据时提高效率。文件缓存的默认大小是10GB
    - 支持的文件类型：zip, tgz, tar.gz, tar, jar
    - 缓存位置：仅当使用Streaming时，才可以使用分布式缓存，数据会存在从属节点或主节点上，只有当个文件的时候，数据会存在/archive目录下面
    -
- Hive可以在 DynamoDB 和 Amazon EMR 集群之间导入和导出数据
- AWS Direct Connect 服务适用于大量的本地数据处理
- 使用 AWS Import/Export 上传大量数据

### Amazon S3存储桶与EMR集群最佳实践
- 启用版本控制：可确保即使数据被意外删除或覆盖，也可恢复数据。
- 考虑生命周期管理：可以创建控制对象的存储类和生命周期的规则。
- 清除失败的分段上传和版本标记：失败的分段上传会占用存储桶并会产生存储费用，建议上传启动日期三天后删除仍未完成的分段上传
- 性能最佳实践：
    - 如果Amazon S3存储桶中的日常工作负载超出每秒100个PUT/LIST/DELETE请求，或超出每秒300个GET请求时才考虑一下情况
    - 为对象合理选择键名称可缩短访问Amazon S3索引的延迟，从而确保实现更高的性能。这样还可确保可扩展性(与每秒发送的请求数无关)
    - 如果工作负载中包含大量的GET请求，建议使用Amazon CloudFront内容传输服务。
    - 向键名称添加十六进制哈希前缀
    - 反转键名称字符串

### 如何处理压缩文件
- Hadoop会检查文件扩展名以检测压缩文件。Hadoop支持的压缩类型是：gzip、bzip2 和 LZO。不需要执行任何额外操作来解压缩使用这些压缩类型的文件，Hadoop 会执行这些操作。

### 压缩集群的输出
- 输出数据压缩：TextOutputFormat -> gzip, SequenceFiles -> SequenceFile. mapred.output.compress=true
- 中间数据压缩: mapred.compress.map.output=true


### 使用自定义AMI最佳实践和注意事项


### 计划和配置联网
- EC2-Classic：实例会运行在一个可与其他客户共享的扁平化网络中。EC2-Classic 仅适用于某些区域内的特定账户
- EC2-VPC：
    * 处理敏感数据
    * 访问内部网络上的资源

