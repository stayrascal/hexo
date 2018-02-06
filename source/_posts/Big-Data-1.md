
title: Big-Data
date: 2018-02-04 20:48:16
tags:
---
# 什么事大数据

大数据三个字很简短，但背后的故事很长，从任何一个维度谈大数据都可以写成一本书，下面主要中概念、使用者、大数据分析、大数据技术三个维度简要介绍一下我理解的大数据。

## 概念
首先作为一个名词，大数据是记录集。百科解释大数据是无法在一定时间范围内用常规软件工具进行捕捉、管理和处理的数据集合，是需要新处理模式才能具有更强的决策力、洞察发现力和流程优化能力来适应海量、高增长率和多样化的信息资产。

从特征上看，大数据的特征经历了3V、4V到5V的演变
- 2001年，高德纳分析员道格莱尼在一片研究报告中指出数据增长有三个方向的挑战和机遇，为3V：Volume, Velocity, Variety.
- 在莱尼的理论基础上，IBM提出了大数据的4V特征：Volume, Velocity, Variety, Veracity.
- 国际数据公司IDC也定义了一套4V特征: Volume, Velocity, Variety, Value.
- 阿姆斯特丹大学的 Yuri Demchenko 等人提出了大数据体系架构框架的 5V 特征: Volume, Velocity, Variety, Value, Veracity.
- Volume(数据量大): 一般指TB、PB级的数据量
- Variety(多向性): 数据来自多种数据源，数据种类和格式日渐丰富，包括了非结构化、半结构化、结构化等数据范畴
- Velocity(速度快): 数据处理的速度也非常快，即使在数据量非常庞大的情况下，也能够做到数据的实时处理，以及批处理、多进程、数据流等
- Value(价值高): 主要体现的是大数据分析应用的目的意义所在。 通过深入的大数据分析挖掘，可以为各方各面的经营决策提供有效支持，创造巨大的经济及社会价值
- Veracity(真实性):随着社交数据，企业内容、交易与应用数据等新数据源的兴起，传统数据源的局限性被打破，企业愈发需要有效的信息之力确保其真实性及安全性

## 使用者

对于投资者和创业者而言，大数据是个热门的融资标签，是资本泡沫的催化剂。如今任何一家移动互联网公司都把自己标榜为大数据公司或者就是一家数据公司，但是大多数中国的互联网公司都是流量驱动的企业，实质上仅仅只是个数据采集公司，他们缺乏足够的人才和技术来充分挖掘数据中国的有效信息。

对于大多数互联网公司而言，大数据其实就只是招一堆数据工程师把一堆乱七八糟的数据扔到HDFS上然后进行计算，通过不同的计算工具把异构的数据和系统跑起来，然后再配个数据科学家搞搞数据挖掘或者机器学习，在支撑其业务系统的同时解决就业问题。

但是对于消费者来说，大数据是一把双刃剑。在享受更加服务化、个性化、便捷化的同时暴露我们越来越多的隐私。

从理论上讲，大数据公司通过搜集用户行为可以更好的了解消费者的需求，增强用户体验，但是实践上，所谓的智能推荐还停留在很初级的阶段，因此会有人在淘宝上搜索某商品之后在微博上不停的看到相应的广告，对于微博这样的公司，还意味着它会倾向于通过直接或者间接的暴露你的隐私来获取商业利益。据说，评价一家国内公司的大数据能力是跟查水表的频繁程度正相关的。

很多时候作为消费者的我们并没有感受到太多的被侵犯的原因并不是我们的隐私数据还没有被泄漏，而是目前还没有一个系统成型的分析技术能把这些泄露的我们认为微不足道的信息串联起来，下面分享一段成熟化的虚构的案例

```
某披萨店的电话铃响了，客服人员拿起电话。 客服：您好，请问有什么需要我为您服务？ 顾客：你好，我想要一份……
客服：先生，烦请先把您的会员卡号告诉我。
顾客：342623***。
客服：陈先生，您好！您是住在安澜路一号12楼1205室，您家电话是6333***，您公司电话是2888***，您的手机是1390553****。请问您想用哪一个电话付费？ 顾客：你为什么知道我所有的电话号码？
客服：陈先生，因为我们联机到CRM系统。
顾客：我想要一个海鲜比萨……
客服：陈先生，海鲜比萨不适合您。
顾客：为什么？
客服：根据您的医疗记录，你的血压和胆固醇都偏高。
顾客：那你们有什么可以推荐的？
客服：您可以试试我们的低脂健康比萨。
顾客：你怎么知道我会喜欢吃这种的？ 客服：您上星期一在国家图书馆借了一本《低脂健康食谱》。 顾客：好。那我要一个家庭特大号比萨，要付多少钱？
客服：99元，这个足够您一家六口吃了。但您母亲应该少吃，她上个月刚刚做了心脏搭桥手术，还处在恢复期。 顾客：那可以刷卡吗？ 客服：陈先生，对不起。请您付现款，因为您的信用卡已经刷爆了，您现在还欠银行4807元，而且还不包括房贷利息。 顾客：那我先去附近的提款机提款。 客服：陈先生，根据您的记录，您已经超过今日提款限额。 顾客：算了，你们直接把比萨送我家吧，家里有现金。你们多久会送到？ 客服：大约30分钟。如果您不想等，可以自己骑车来。
顾客：为什么？
客服：根据我们CRM全球定位系统的车辆行驶自动跟踪系统记录。您登记有一辆车号为SB-748的摩托车，而目前您正在铁山路右侧骑着这辆摩托车。
顾客：当即晕倒……
```

```
某个周末，你来到了某个商场，在一个咖啡厅里面点了一杯咖啡，然后开始用智能手机上网。咖啡厅提供了免费 Wi-Fi 网络，由于法规要求需要你提供手机号进行实名认证，你毫不犹豫地输入了手机号。于是免费 Wi-Fi 的服务商知道了你的信息：你的手机号和智能手机的 MAC。然后你开始刷微博，由于微博的 API 通常不使用加密信道，于是 Wi-Fi 热点通过偷窥 HTTP 请求获得了你的微博账号。通过你的微博，Wi-Fi 服务商有可能了解你的性别年龄工作等信息。此外通过该热点请求的很多元信息都会被服务商保留，虽然它们未必知道怎么挖掘这些元信息，但是它们会尽量将你的身份和这些信息关联在一起并长期保留。喝完咖啡，你开始逛街，这时候你的手机会开始扫描热点，商场可以通过 Wi-Fi 探针追踪你的位置。如果商场使用的 Wi-Fi 服务商和咖啡厅是同一家，或者与服务商建立了数据交换的协议，那么商场有可能实名地追踪你的轨迹。商场的 Wi-Fi 服务商同样会非常有耐心地存储你的信息，以备不时之需。在逛街的过程中，你打开了一些购物 App 用于比价，顺便拍了一些照片发给好友。其中一些 App 会把你的 MAC 地址和通过 Wi-Fi 完成的定位信息也发送出去。如果存在一个完备的数据交易网络，任何对你感兴趣的人都有可能获得以下信息：你的电话号码、手机的 MAC、微博账号，何时出现在这个商场，在商场停留了多久，其间使用了哪些 App，在咖啡厅访问了哪些网站。而这一切都离不开 Wi-Fi 和 MAC。如果更极端一点，你使用了专车软件来这个商场，并且你经常来这家商场，那么你很可能已经在商场的常客数据库里了，你的家庭住址也不再是个秘密。
```

上面的例子都是虚构的，因为利益冲突无关公司之间很难达成信任，很少进行实质性的数据交换。

作为消费者，应该人人学点大数据知识，拾起大数据之盾，防大数据之矛。


## 大数据分析
大数据已经不是简单的数据大的事实了，而最重要的现实是对大数据的分析，只有通过分析才能获取到很多智能的深入的，有价值的东西。
- 可视化分析：可视化分析能够直观的呈现大数据特点，同时能够非常容易的被读者所接受。
- 数据挖掘算法：大数据分析的理论核心是数据挖掘算法，各种数据挖掘算法基于不同的数据类型和格式才能更加科学的呈现出数据本身具备的特点。
- 预测性分析能力：从大数据中挖掘出特点，通过科学的建立模型，然后通过模型带入新的数据，从而预测未来的数据
- 数据质量和数据管理： 高质量的数据和有效的数据管理，无论是在学术研究还是在商业应用领域，都能够保证分析结果的真实和价值


## 大数据技术
大数据是一门技术，特别是数据处理的过程。

### 采集
> 大数据的采集是指利用多个数据库来接收发自客户端(Web、App或者传感器)的数据，并且用户可以通过这些数据库信息来进行简单的查询和处理工作。
> 在大数据的采集过程中，主要的特点和挑战是并发数高，因为同时可能会有成千上万的用户进行访问和操作，并发的访问量在峰值达到上百万，所以需要在采集端部署大量数据库才能支撑，并且如何在这些数据库之间进行负载均衡和分片的确是需要深入的思考和设计

### 导入、预处理
> 虽然采集端本身会有很多数据库，但是如果要对这些海量数据进行有效的分析，还需要将这些来自终端的数据导入到一个集中的大型分布式数据库，或者分布式存储集群，并且可以在导入基础上做一些简单的清洗和预处理工作。
> 常见的导入流程是使用ETL工具将分布的、异构数据源中的数据如关系数据、平面数据文件等抽取到临时的中间层(HDFS)后进行清洗、转换、集成，最后加载到数据仓库或者数据集市中，成为联机分析处理、数据挖掘的基础
> 导入与预处理过程的特点和挑战主要是导入的数量大，每秒钟的导入量经常会达到百兆，甚至前兆级别，同时还需要考虑数据的容错性等相关因素

### 统计分析
> 统计与分析主要利用分布式数据库，或者分布式计算集群来对存储于其内的海量数据进行普通的分析和分类汇总等，以及满足大多数常见的分析需求。在这方面，一些实时性需求会用到Storm、Spark Stream结合Kafka流处理技术，而一些批处理或者半结构化数据的需求可以使用Hadoop、Spark、Spring Batch等技术
> 统计与分析这部分的主要特点和挑战是分析涉及的数据量大，其对系统资源，特别是I/O会有极大的占用

### 挖掘
> 和统计、分析不用的是，数据挖掘一般没有什么预先设定好的主题，主要是在现有数据进行基于各种算法的计算，从而起到预测的效果，从而实现一些高级别数据分析的需求。比如用于聚类的Kmeans、用于统计学习的SVM和用于分类的NaiveBayes等
> 该过程的特点和挑战主要是用于数据挖掘的算法很复杂，并且计算涉及的数据量和计算量都很大，常用数据挖掘算法都以单线程为主


## 什么是数据平台
数据平台是一套全方位的正对数据采集、处理、分析、管理的方案，现在主流的数据平台都是基于CDH、hortorworks全家桶的基础上实现自定义的需求开发


## 为什么要建立数据平台
诚然，不建立数据平台依然能解决我们当前的问题，但不可否认，随着业务需求的不断完善，最终还是会在痛苦的挣扎过程中打造住一个集成的数据平台，既然后最后的结果就是这样，那为什么从一开始的时候就把地基打造好呢。

比如，可能我们最初的需求仅仅只是从一个数据源系统中批处理的导出转换一部分数据到另外一个数据库中，这样一个很小的需求还谈不上需要一个数据平台，甚至不需要Hadoop系列的组件，但随着数据量的增大，数据源系统的增多，我们不得不需要一个分布式的计算引擎和存储系统，同时实时性的统计需求出现的时候，我们又不得不重现纳入流处理工具，紧接者，随着数据量越来越大，使用的用户越来越多，数据安全管理，多用户权限隔离，元数据管理等需求的出现，我们不得不在原有的基础上添加这些功能，这个时候我们可能会苦恼最初的设计拓展性差，甚至推翻原来的设计。所以如果在最初的时候，搭建好数据平台，后续需求基于数据平台插件式的开发会顺畅多




