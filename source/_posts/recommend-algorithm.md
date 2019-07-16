---
title: recommend-algorithm
date: 2018-01-21 23:44:59
tags:
---


## 推荐系统场景需求

### 推荐系统与人工智能
> 复杂的推荐系统是人工智能的一个领域。

> 人工智能是一个偏业务的定义，多维度多学科交叉的概念，其核心的三要素：算法、计算能力、数据。围绕大量的基础数据，对基础数据进行特征处理，然后构建有用的的业务算法模型，然后基于分布式的基础架构计算能力，将算法模型用于实际的生产环境，以机器替代人工的行业，以提升效果与效率，达到机器智能化的目标。

> 传统的门户网站或者其它领域的推荐场景，差不多都是基于编辑或者运营手动进行配置推送，随着对数据、对算法模型的进一步应用，才有算法机器替代人工进行推荐，达到千人千面、个性化推荐的效果。追求其本质，也是算法模型+计算过程+基础数据的流程，最终达到了机器自动化、智能化的效果，所以广义的角度来说，复杂一些的推荐系统也能纳入人工智能的范畴。

### 推荐与检索
> 搜索与推荐都是信息获取的一种机制，核心区别在于主动与被动。

> 搜索引擎是典型的主动触发的形态，用户已经有明确的信息获取意图，渴望得到自身既定的目标信息，然后通过搜索规则进行最终信息的获取。

> 推荐系统是一种系统主动的行为，对用户来说是一个被动的行为，被动的接受系统推送过来的信息。推荐系统核心解决的还是用户额外信息获取的问题，从而提升用户进一步转化率，延长在网页的停留时间。

> 推荐要解决的就是从海量的冗余信息，以及用户在目的不算很明确的情况，进一步帮助其确定目标，主动筛选信息的过程。

> 关于信息的获取，还是一种常见的形态，就是结构化导航。如电商平台、门户网站的机构化频道信息，它是通过把信息进行结构化，构建脉络结构，帮助用户获取想要的信息。

### 推荐系统的场景

- 视频推荐
- 音乐推荐
- 小说推荐
- 电商平台推荐

### 推荐系统的一些坑
> 推荐系统是一把双刃剑，推荐的不好只会让用户徒增烦恼，推荐的不好指推荐的是否准确，推荐的时间是否恰当，推荐的场景是否正确等

> 好的推荐系统一般情况下会很依赖用户的行为数据，因为一般只有从用户行为中才能一窥用户的一些偏好所在，但实际情况是，用户的行为数据并不是很容易获取的，当用户的行为数据不够的时候，基于用户行为的分析结论就是一个伪命题，甚至会把我们带像错误的方向。

> 用户的偏好一定是会随时间偏移进行转变的，所以用户行为的有效性又会是一个问题。

> 如果用户是一个新用户的话，新用户没有轨迹信息。

> 实际影响用户选择的因素太多，我们容易陷入主观臆断的误区，综合性考虑是一个完善推荐系统必须要考虑的地方。

> 产品的逻辑有时候比底层算法更有效，典型的如阅文的例子，“喜欢这本书的人也喜欢”就是一种策略，也是一种推荐解释，可解释行会提升推荐的可信度，诸如一些其它的交互方式、产品形态都是对推荐转化有影响的。


## 推荐系统的基础知识

### 推荐系统概述
> 推荐算法或者推荐机制并不严格等同推荐系统，推荐系统是一个相对复杂的业务系统，涉及到数据的处理、架构的构成、推荐的逻辑机制、反馈数据的回收、效果的跟踪、AB测试等等。

> 一个完整的推荐系统，一定不会单纯的依赖某一个推荐算法。一些我们耳熟能详的推荐算法其实只是解决某种特定情况下的推荐机制问题，而很多时候整个系统是复合了多种算法结果，然后综合呈现出一种结果。

> 各种理论逻辑、算法机制是构建推荐系统的核心支撑。

### 推荐算法概述

#### 基于内容属性相似的推荐
> 从原始数据依赖的层面来说，常见的有基于内容属性的推荐机制，这种推荐只是单纯的依赖物品之间的属性相似来构建推荐系统关系，比较容易理解，在一定时间内还是有一定效果，但实际上很多时候存在这几种情况，会导致推荐失效。
- 如果用户浏览的物品本身就不是用户所期望的，甚至是一个非优质信息，即当前主体不可控，再基于当前物品推荐就是个伪命题
- 即使当前物品是用户的目标，但再推类似主体会造成信息冗余，即当前主体信息已经解决了用户的问题。
> 所以由于用户行为的不可控，基于内容性相似的推荐，风险还是挺高的，导致这种原始直接的机制并不会得到广泛的推广

#### 基于用户画像的推荐
> 基于物品本身属性的推荐只是跟物品主体有关，与用户无关，谈不上个性化

> 基于用户画像(用户标签)的推荐，更大程度上依赖用户的画像属性来推荐，体现了用户偏好信息，根据偏好信息来选择候选集。在大规模数据集情况下，很多实际的产生过程中喜欢使用这种机制。

> 用户画像依赖用户累积的行为数据，我们通过行为数据生成用户的兴趣标签。

> 然而，在实际的场景中，并不是所有用户的行为都足够用来表示其兴趣偏好，即我们会高估用户的行为集合，从而产生有偏差的画像属性。同时，用户的兴趣爱好是会随时间迁移而改变的，把握用户的兴趣程度及其变化并不是一个容易的事情，更何况用户实际的选择还会受很多因素影响。比如我当前查找的信息并不是我以前掌握的信息，就意味着这些信息偏好在我的历史轨迹中体现不出来，单纯的通过我的兴趣去推荐就显得不靠谱了。但是不管怎么说，根据用户的偏好来做推荐，大方向肯定是没有问题的。

#### 基于协同过滤的推荐
> 协同过滤不会去研究物品的本身属性，甚至没空去构建用户的画像标签，它严重依靠用户的行为以及周边用户的协同行为。比如为一个用户推荐信息，那么只需要参考其周边用户在看什么信息，就给他推荐什么信息。

> 协同过滤的重点在于如何限定周边这个范围，比如典型的基于用户的协同过滤推荐就是根据两个用户的行为，去构建相关关系，从而判断用户之间的相似度，把相似用户的行为推荐给当前用户，基于物品的协同逻辑是以物品为维度，以用户的购买或者观看记录为向量，则可以构建物品的相似度量，针对每一个待推荐项，用户的历史轨迹就是其向量构成，就可以判断该用户的轨迹历史与当前的待选物品的向量相关度，从而判断是否要推荐。

> 和基于用户画像的推荐相比，这种推荐有一定几率可以发现新物品，并不严格依赖用户的兴趣。比如假设几个信息的递进层级关系是ABC，对于一个用户来说，他掌握的是A，则意味着他的兴趣偏好大多偏向于A，如果根据兴趣标签，很难推荐这种递进相关的信息，但是如果其它用户的学习轨迹是A->B->C，这意味着ABC三者之间本身就有前后潜在的逻辑关系存在，基于协同，即可为该用户在掌握A的基础上，推荐BC的内容。

> 除了基于物品、用户的协同行为的推荐，还有其它诸如基于模型的协同，典型如最近邻模型、基于矩阵分解、以及基于图关系模型的构建的推荐机制

#### 其它
> 实际操作中，并不会严格的依赖于这个种条条框框，只要合理就行，比如可以把推荐问题转化为分类问题。或者一些类似“喜欢这本书的人还喜欢”这种典型的“啤酒与尿布”货架思维，关联销售，也是可以作为一种推荐机制存在的。

> 微信典型的熟人社交模型会研究用户的朋友圈关系，比如你对哪类朋友点赞、互动行为越多，它可能会推荐你欣赏的朋友偏好内容给你

#### 相似度量方式

- 欧几里得距离(Euclidean Distance): 衡量多维空间空间中两点之间的绝对距离，要求维度的统一
- 明可夫斯基距离(Minkowski Distance): 对多个距离度量公式的概括性表达，当p=2时，其实就是欧式距离
- 曼哈顿距离(Manhattan Distance): 曼哈顿距离来源于城市区块距离，是将多个维度上的距离进行求和后的结果，即当明氏距离中p=1时得到的距离度量
- 向量空间余弦相似度(Cosine Similarity): 余弦相似度用向量空间中的两个向量夹角的余弦值作为衡量两个个体间差异的大小，相比距离度量，余弦相似度更加注重两个向量在方向上的差异，而非距离或者长度上
- 皮尔森相关系数(Person Correlation Coefficient): 即相关分析中的相关系数r，分别对X和Y基于自身总体标准化后计算空间向量的余弦夹角。基于内容的推荐有一点需要注意的是，对于物品自身属性，如果属性值过少，我们需要适当进行扩大维度，如果维度过多，则需要进行降维度。 降维的方法有维度聚类，主体抽取，升维的方法有维度矩阵化，将设物品X有A和B两个维度属性，通过生成A*B矩阵的方式，把维度扩充到A*B个维度

### 冷启动问题的解决
> 冷启动是指在推荐系统初期，没有任何用户与物品的交集信息，即无用户的行为轨迹，无法用过类似协同或者用户偏好等方式进行推荐，我们把这种状态叫做冷启动状态。

> 在这种情况下，我们需要尽快的累积起第一批用户行为轨迹，我们可以通过基于内容的推荐，做一些其它类似的操作，快速有效的进行物品推荐。一段时间后，累积到一定的用户行为时，整个系统就能正常的使用协同过滤等方式进行推荐了

> 针对新加入的用户，或者新加入的物品，同样是处于冷启动状态，也需要对这种物品护着用户做特殊的处理

> 除了基于内容属性的推荐，还有一些其它的策略用于弥补这种行为数据不足的情况，如典型的热度模型(推荐热点)， 还可以根据一些统计学上的结论，进行基于统计分析结论的推荐。还可以通过其它渠道收集用户的数据，比如注册时填写的个人资料，这些都可以作为推荐的原始依赖数据。

### 马太效应
> 马太效益或者长尾效应，即热着愈热，比如在实际的购买场景中，由于推荐的次数越多，部分优质的商品购买或者点击的次数就越多，形成的用户购买轨迹就越多，所以得到的推荐机会就越多，进而产生的推荐也越多，变得越热。随着时间的推移，这样得到推荐的商品就会集中在少数部分商品中，而大部分长尾商品是沉寂的，一个推荐系统如果长时间处于长尾效应中，会造成推荐疲劳，其推荐效果就会减弱。

> 所以好的推荐系统要考虑到适当的挖掘长尾商品，通过真的个性化，把适当的长尾商品送到真正需要他们的人手里。在实际的操作过程中，在保证点击率的情况下，可以适当的进行热度降权，从而让一些中下层的商品得到更多的曝光机会。

> 另外一个场景会形成马太效应的是热度模型，即热度榜单长时间高居榜首，会获取更多的点击，从而其热度越高，但是我们的信息需要保持新鲜度，不然点击率迟早会下降。

> 所以，使用一些机制让处于头部的商品或者信息降权，时间衰减是一个比较通用的做法，随着时间的迁移，其整体热度会不断的下降，下降的方式速率取决于模型的设计

### AB测试
> 推荐系统的效果，核心的考核标准就是点击率，点击的越多说明推荐的越准确，用户停留的时间也会越长。同时一个不好的推荐系统有时候反而会形成方向作用，所以一个推荐系统的迭代更新至关重要。离线的效果评估一定要做，当离线实现的效果要优于线上效果，才能进行迭代。

> AB测试机制，就是将流量分为AB两类，A流量走原始的久模型，B流量走新模型，同步测试同步对比效果。打造一个好的AB测试系统，流量是需要可控的，其次模型的迭代上线是需要高度灵活的，最后肯定需要有完整的数据回收、数据分析对比机制存在


# 基于内容属性的推荐策略算法

## 最简单的推荐机制 - 基于主体属性相似推荐

### 选择相似计算的过程
> 夹角余弦可以有效规避个体相同认知中不同程度的差异表现，更注重维度之间的差异，而不注重数值上的差异，而欧式距离则是对个体异常数值会比较敏感。所以我们需要区分异常样本时，使用距离计算会更恰当，比如在计算电商领域中高价值与低价值用户的区分，我们的核心是想把他们的差异性拉大，才能体现出对比，这个时候使用余弦就是不合理的。

### 解决相似计算中，计算矩阵过大的问题
> 假如有1万个物品，对每个物品来说需要计算与其它物品计算与其的相似度或者相关度，然后再排序，这样会进行1亿次计算，显然是不合理的，这个过程必须被优化，核心思想就是初筛，把没多大关系的直接去掉。常见的筛选方式是寻找核心关键影响因素，保证关键因素的相关性。在实际的生产操作过程中，很多时候会通过关键属性是否基本匹配作为判断依据，或者直接通过搜索构建进行检索初筛，这样已经能把大部分相关度低的候选集过滤掉，降低计算复杂度。

### 如何权衡多个因子的影响权重
> 基于属性计算相似，从整体上来看，其实一般主体都不止一个属性，所以需要分辨哪些因素是重要因素。最简单并且实际上比较有效的一种方式是专家评判法，即通过权威经验来划定影响因子的权重，还有就是通过标注的样本进行方向拟合每种因素的占比权重。除此之外，学术上还有主成分分析法，层次分析法，熵权法。最终确定好影响因素，在上线之后，依然需要逐步进行权重影响调整，可以通过结果的样本数据，进行LR的回归拟合，寻找最合适的权重配比。

## 最简单的推荐策略算法实践

### 数据集
- movies.csv  (movieId,title,genre) 电影数据集
- ratings.csv (userId,movieId,rating,timestamp) 用户对电影的打分数据
- tags.csv    (userId,movieId,tag,timestamp) 电影标签数据

### 推荐机制逻辑
- 电影的类别：电影类别信息以一对多的关系存储于电影数据集中，需要进行切割
- 电影的播放年份：电影的年份其实有种潜在的关联关系，这些数据隐藏在电影的名字中，需正则过滤出来，可以通过差距来计算维度的相关
- 电影的标签：即用户对电影打的标签信息，需经过清洗、规整以及处理后变成电影的属性，标签是多对多的关系，可以通过计算欧式或者余弦计算相似度
- 电影的名称：从某种程度上来说，名称可以体现相关性，经过分词去除停词后以词维度进行余弦计算
- 候选集电影的评分：一个电影对应多个评分，需要进行归一化计算，简单的做法是计算整体评分的平均值

### 代码逻辑
> **Spark2.0之后，不再构建sparkcontext了，创建一个复合多功能的SparkSession替代，可以从HDFS读取文件，也可以从Hive中获取DataFrame**

```
val HIVE_METASTORE_URIS = "thrift://localhost:9083"
val spark = SparkSession.builder()
      .appName("Base content Recommend")
      .config("hive.metastore.uris", HIVE_METASTORE_URIS)
      .enableHiveSupport()
      .getOrCreate()
```

> **先将数据load到Hive中，然后spark直接从Hive中读取DataFrame/RDD**

```
create external table if not exists mite8.mite_movies(movieId INT, title STRING, genres STRING) comment 'tags' row format delimited fields terminated by ',' stored as textfile location '/tmp/movies' tblproperties("skip.header.line.count"="1");

create external table if not exists mite8.mite_ratings(userId INT, movieId INT, rating FLOAT, `timestamp` INT) comment 'ratings' row format delimited fields terminated by ',' stored as textfile location '/tmp/ratings' tblproperties("skip.header.line.count"="1");

create external table if not exists mite8.mite_tags(userId INT, movieId INT, tag STRING, `timestamp` INT) comment 'tags' row format delimited fields terminated by ',' stored as textfile location '/tmp/tags' tblproperties("skip.header.line.count"="1");
```

```
val movieAvgRate = spark.sql("select movieId, round(avg(rating)) as avg_rate from mite8.mite_ratings group by movieId").rdd
      .filter(_.get(0) != null).map { f => (f.get(0).toString.toInt, f.get(1).toString.toDouble) }

val moviesData = spark.sql("select movieId, title, genres from mite8.mite_movies").rdd

val tagsData = spark.sql("select movieId, tag from mite8.mite_tags").rdd
```

> **对tags标签进行处理，包括分词，去除停用词**

```
val tagsStandardize = tagsData.filter(_.get(0) != null).map { row =>
  val movieId = row.get(0).toString.toInt
  val tag = if (row.get(1).toString.split(" ").length <= 3) row.get(1).toString else HanLP.extractKeyword(row.get(1).toString, 20).toArray.toSet.mkString(" ")
  (movieId, tag)
}
```

> **统计tag频度，取前10个作为电影的tag属性**

```
val movieTag = tagsStandardize.reduceByKey(_ + _).groupBy(k => k._1._1).map { f =>(f._1, f._2.map { ff => (ff._1, ff._2)}.toList.sortBy(_._2).reverse.take(10).toMap)
}
```

> **处理年龄、年份、名称**

```
val moviesGenresTitleYear = moviesData.filter(_.get(0) != null).map { f =>
  val movieId = f.get(0).toString.toInt
  val title = f.get(1).toString
  val genres = f.get(2).toString.split("|").toList.take(10)
  val titleWords = HanLP.extractKeyword(title.toString, 10)
  val year = MovieYearRegex.movieYearReg(title.toString)
  (movieId, (genres, titleWords, year))
}
```

> **提取年份的正则如下：**

```
object MovieYearRegex {
  val moduleType = ".*\\(([1-9][0-9][0-9][0-9])\\).*"

  def movieYearReg(str: String): Int = {
    var retYear = 1994
    val pattern = Pattern.compile(moduleType)
    val matcher = pattern.matcher(str)
    while (matcher.find()) {
      retYear = matcher.group(1).toInt
    }
    retYear
  }
}
```

> **通过join进行数据合并，生成一个以movieId为核心的属性集合**

```
val movieContent = movieTag.join(movieAvgRate).join(moviesGenresTitleYear).map(
  // movieTag: (movieId, Map((movieId, tag), count))
  // movieAvgRate: (movieId, avgRate)
  // moviesGenresTitleYear: (movieId, (genres, titleList, year))
  // (movie, tagList, titleList, year, genreList, rate)
  f => (f._1, f._2._1._1, f._2._2._2, f._2._2._3, f._2._2._1, f._2._1._2)
)
```

> **使用余弦相似度计算内容的相似性，排序之后取前20作为推荐列表(只推荐优质电影)**


```
val movieContentTmp = movieContent.filter(f => f._6.asInstanceOf[java.math.BigDecimal].doubleValue() > 3.5).collect()

val movieContentBase = movieContent.map {
  f =>
    val currentMovieId = f._1
    val currentTagList = f._2
    val currentTitleWorldList = f._3
    val currentYear = f._4
    val currentGenreList = f._5
    val currentRate = f._6.asInstanceOf[java.math.BigDecimal].doubleValue()
    val recommendMovies = movieContentTmp.map {
      ff =>
        val tagSimi = getCosTags(currentTagList, ff._2)
        val titleSimi = getCosList(currentTitleWorldList, ff._3)
        val genreSimi = getCosList(currentGenreList, ff._5)
        val yearSimi = getYearSimi(currentYear, ff._4)
        val rateSimi = getRateSimi(ff._6.asInstanceOf[java.math.BigDecimal].doubleValue())
        val score = 0.4 * genreSimi + 0.25 * tagSimi + 0.1 * yearSimi + 0.05 * titleSimi + 0.2 * rateSimi
        (ff._1, score)
    }.toList.sortBy(k => k._2).reverse.take(20)
    (currentMovieId, recommendMovies)
}.flatMap(f => f._2.map(k => (f._1, k._1, k._2))).map(f => Row(f._1, f._2, f._3))
```

> **相似度计算函数**

```
def getCosTags(tags1: Map[(Int, String), Int], tags2: Map[(Int, String), Int]): Double = {
    var xySum: Double = 0
    var aSquareSum: Double = 0
    var bSquareSum: Double = 0

    val tagsA = tags1.keys.toList
    val tagsB = tags2.keys.toList
    tagsA.union(tagsB).foreach {
      f => {
        if (tagsA.contains(f)) aSquareSum += tags1(f) * tags1(f)
        if (tagsB.contains(f)) bSquareSum += tags2(f) * tags2(f)
        if (tagsA.contains(f) && tagsB.contains(f)) xySum += tags1(f) * tags2(f)
      }
    }

    if (aSquareSum != 0 && bSquareSum != 0) {
      xySum / (Math.sqrt(aSquareSum) * Math.sqrt(bSquareSum))
    } else {
      0
    }
}

def getCosList(tags1: java.util.List[String], tags2: java.util.List[String]): Double = {
    var xySum: Double = 0
    var aSquareSum: Double = 0
    var bSquareSum: Double = 0

    tags1.union(tags2).foreach {
      f => {
        if (tags1.contains(f)) aSquareSum += 1
        if (tags2.contains(f)) bSquareSum += 1
        if (tags1.contains(f) && tags2.contains(f)) xySum += 1
      }
    }

    if (aSquareSum != 0 && bSquareSum != 0) {
      xySum / (Math.sqrt(aSquareSum) * Math.sqrt(bSquareSum))
    } else {
      0
    }
}

def getYearSimi(year1: Int, year2: Int): Double = {
    val count = Math.abs(year1 - year2)
    if (count > 10) 0 else (1 - count) / 10
  }

  def getRateSimi(rate: Double): Double = {
    if (rate >= 5) 1 else rate / 5
}
```

> **将结果存入Hive**

```
val schemaString2 = "movieId recommendMovieId score"
val schemaContentBase = StructType(schemaString2.split(" ")
  .map(fieldName => StructField(fieldName, if (fieldName.equals("score")) DoubleType else StringType, nullable = true)))
val movieContentBaseDataFrame = spark.createDataFrame(movieContentBase, schemaContentBase)

val userTagTmpTableName = "mite_content_base_tmp"
val userTagTableName = "mite8.mite_content_base_reco"
movieContentBaseDataFrame.createOrReplaceTempView(userTagTmpTableName)

spark.sql("insert into table " + userTagTableName + " select * from " + userTagTmpTableName)

spark.stop()
```

# 基于用户画像的个性推荐

## 个性化与用户画像
> 个性化一定与人相关，每个人可能都有自己的个性，一个好的推荐系统推荐的信息需要满足用户的个性，才具有足够的智能

> 要实现推荐个性化，需要对用户进行分析，分析用户的偏好，根据偏好来做推荐。分析用户的偏好核心还是用户画像的分析，然后基于用户画像属性进行推荐
![](https://mmbiz.qpic.cn/mmbiz_png/wvkocF2MXjVPzXic3UE2w5YC4B2NXuJk2oib6RsK3la9bvRaiab4u2dsjoWVONpJlicdsyGQUjeWDbczBVQDgUp1jg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

## 基于用户画像的个性化推荐策略
- 先根据行为数据，进行用户画像描述抽取
- 再结合用户画像数据为用户进行信息推荐

## 数据源
- 用户对电影的标签数据
- 用户对电影的评分数据

### 用户兴趣标签提取
- 用户对电影类目偏好数据，即用户对那些类目偏好
- 用户的偏好兴趣标签，即通过用户对电影的偏好标签打标行为，提取用户的兴趣标签
- 用户的偏好年份，通过打分数据，描述用户偏好那个年代的电影

#### 用户的偏好标签
> 我们拥有的是用户对电影的打标行为数据，实际上这是电影层级的标签，我们需要在这个基础上，为用户映射这些特征标签。所以我们需要对单个用户的所有打标签进行合并，然后如果用户对刚好打标的电影有评分的话，再带上评分权重，最终合并这些标签，形成基于频度、评分用户带权重的标签集，这就是用户的一些兴趣点

#### 用户对电影的类目偏好
> 通过评分表，把对应所有的电影都取出来，然后分析其类目，将评分作为对应类目的权重，然后将类目进行合并，最终求取用户的类目偏好合集

#### 用户的偏好年份
> 过程与取类目的过程类似，最终获取到年份偏好

### 电影数据的处理
> 再已经获取用户层级的画像属性信息之后，我们需要绘制候选集电影的属性，对应用户的三个属性，其中年份、类目直接存放与电影表中，唯一需要额外处理的是特征tag。由于不同人对不同电影进行Tag标记，而且在进行用户画像绘制的时候，是以人为维度的，现在需要以电影为维度，进行标签合并，最终形成电影维度的标签集

### 关联推荐计算
> 每个维度分别进行计算相似度或者相关度，然后不同维度进行合并归一计算最终用户与电影的相关度。最外层依然以权重模型去做，类目最重要，其次是Tag，最后才是年份属性，最终怎么调整还是需要根据实际反馈数据来做微调
![](https://mmbiz.qpic.cn/mmbiz_png/wvkocF2MXjVPzXic3UE2w5YC4B2NXuJk2p74XVDfXicDZgd18L8Jue3iblxfz0HBdlNseibXg18DLSysPRsgzWzm6A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 代码逻辑

#### **先进行movie候选集的处理，包括Tag预处理，合并，以及类目年份的获取**

> **获取数据与上一节类似**

> **获取每部电影的平均分数**

```
val movieAvgRate = spark.sql("select movieId, round(avg(rating)) as avg_rate from mite8.mite_ratings group by movieId").rdd
      .filter(_.get(0) != null)
      .map(f => (f.get(0).toString.toInt, f.get(1).toString.toDouble))
```

> **先进行tag候选集的处理**

```
val tagsData = spark.sql("select movieId, tag from mite8.mite_tags").rdd
val tagsStandardize = tagsData
  .filter(_.get(0) != null)
  .map { row =>
    val movieId = row.get(0).toString.toInt
    val tag = if (row.get(1).toString.split(" ").length <= 3) row.get(1).toString else HanLP.extractKeyword(row.get(1).toString, 20).toArray.toSet.mkString(" ")
    (movieId, tag)
  }
```
> **将预处理之后的movie-tag数据进行统计频度，直接作为tag权重,形成(movie,tagList(tag,score))数据形态**

```
// (movieId, Map(tag, count)
val movieTagList = tagsStandardize
  .map(item => ((item._1, item._2), 1))
  .reduceByKey(_ + _)
  .groupBy(_._1._1)
  .map { f =>
    (f._1,
      f._2
        .map(ff => (ff._1._2, ff._2))
        .toList
        .sortBy(_._2)
        .reverse
        .take(10)
        .toMap
    )
  }
```

> **抽取电影类别、年份属性**

```
val moviesData = spark.sql("select movieId, title, genres from mite8.mite_movies").rdd
val moviesGenresYear = moviesData
  .filter(_.get(0) != null)
  .map { f =>
    val movieId = f.get(0).toString.toInt
    val genres = f.get(2).toString
    val year = MovieYearRegex.movieYearReg(f.get(1).toString)
    (movieId, (genres, year))
  }
```

> **合并不同属性**

```
// movieId, tagList(Map(tag, count)), genre, year, rate
val movieContent = movieTagList
  .join(movieAvgRate)
  .join(moviesGenresYear)
  .filter(_._2._1._2 > 2.5) // rate > 2.5
  .sortBy(_._2._1._2, false)
  .map(f => (f._1, f._2._1._1, f._2._2._1, f._2._2._2, f._2._1._2)).collect()
```

#### **获取用户画像属性**

> **先通过rating评分表与tags表进行关联join，获取用户直接与tag的关联关系**

```
val tagSchema = "movieId tag"
val schema = StructType(tagSchema.split(" ").map(fieldName => StructField(fieldName, StringType, true)))

val tagsSimiDataFrame = spark.createDataFrame(tagsStandardize.map(item => Row(item._1, item._2)), schema)

val ratingData = spark.sql("select userId, movieId, rate from mite8.mite_ratings")

val tagRateDataFrame = ratingData
  .join(tagsSimiDataFrame, ratingData("movieId") === tagsSimiDataFrame("movieId"), "inner")
  .select("useId", "tag", "rate")
```

> **将(userId, tag, rate)中(userId, tag)相同的分数rate相加**

```
val userPortraitTag = tagRateDataFrame
  .groupBy("userId", "tag")
  .sum("rate").rdd
  .map {
    f => (f.get(0).toString.toInt, f.get(1).toString, f.get(2).asInstanceOf[java.math.BigDecimal].doubleValue())
  }
  .groupBy(_._1)
  .map { item =>
    val userId = item._1
    val tagList = item._2.toList.sortBy(_._3).reverse.map(k => (k._2, k._3)).take(20) // (tag, rate)
    (userId, tagList.toMap)
  }
```

> **处理Year属性**

```
// (userId, year, rate)
val userYear = ratingData.rdd
  .map(f => (f.get(1).toString.toInt, (f.get(0).toString.toInt, f.get(2).toString.toDouble))) // (movieId, (userId, rate))
  .join(moviesGenresYear) // (movieId, (genres, year))
  .map(f => (f._2._1._1, f._2._2._2, f._2._1._2))

val userPortraitYear = userYear
  .groupBy(_._1)
  .map { f =>
    val userId = f._1
    val yearList = f._2.map(ff => (ff._2, ff._3.asInstanceOf[java.math.BigDecimal].doubleValue())).toList.take(10)
    (userId, yearList)
  }
```

> **处理genre偏好**

```
// (userId, genre. rate)
val userGenre = ratingData.rdd
  .map(f => (f.get(1).toString.toInt, (f.get(0).toString.toInt, f.get(2).toString.toDouble))) // (movieId, (userId, rate))
  .join(moviesGenresYear) // (movieId, (genres, year))
  .map(f => (f._2._1._1, f._2._2._1, f._2._1._2))

val userPortraitGenre = userGenre
  .groupBy(_._1)
  .map { f =>
    val userId = f._1
    val genreList = f._2.map(ff => (ff._2, ff._3.asInstanceOf[java.math.BigDecimal].doubleValue())).toList.take(10)
    (userId, genreList)
  }
```

> **获取用户的观看列表,在获取推荐列表之后移除已观看的电影**

```
// ratingData: (userId, movieId, rate)
val userMovieGet = ratingData.rdd.map(x => (x.get(0).toString.toInt, x.get(1).toString.toInt)).groupByKey()
```

> **计算相似度**

```
val portraitBaseReData = userPortraitTag // (userId, tags:Map(tag, rate))
  .join(userPortraitYear) // (userId, yearList)
  .join(userPortraitGenre) // (userId, genreList)
  .join(userMovieGet) // (userId, movieIds)
  .map { f =>
  val userId = f._1
  val userTag = f._2._1._1._1 // Map(tag, rate)
val userYear = f._2._1._1._2 // List[(year, rate)]
val userGenre = f._2._1._2 // // List[(gnere, rate)]
val userMovieList = f._2._2.toList
  val movieRe = movieContent.map { ff =>
    val movieId = ff._1
    val movieTag = ff._2 // Map(tag, count)
  val movieGenre = ff._3
    val movieYear = ff._4
    val movieRate = ff._5
    val simiScore = getSimiScore(userTag, movieTag, userGenre, movieGenre, userYear, movieYear, movieRate)
    (movieId, simiScore)
  }.diff(userMovieList).sortBy(_._2).reverse.take(20)
  (userId, movieRe)
}.flatMap(f => f._2.map(ff => (f._1, ff._1, ff._2)))
```

> **其它相关函数**

```
def getCosList(tags1: java.util.List[String], tags2: java.util.List[String]): Double = {
    var xySum: Double = 0
    var aSquareSum: Double = 0
    var bSquareSum: Double = 0

    tags1.union(tags2).foreach {
      f => {
        if (tags1.contains(f)) aSquareSum += 1
        if (tags2.contains(f)) bSquareSum += 1
        if (tags1.contains(f) && tags2.contains(f)) xySum += 1
      }
    }

    if (aSquareSum != 0 && bSquareSum != 0) {
      xySum / (Math.sqrt(aSquareSum) * Math.sqrt(bSquareSum))
    } else {
      0
    }
}

def getYearSimi(year1: Int, year2: Int): Double = {
    val count = Math.abs(year1 - year2)
    if (count > 10) 0 else (1 - count) / 10
}

def getRateSimi(rate: Double): Double = {
    if (rate >= 5) 1 else rate / 5
}

def getCosTags(userTag: Map[String, Double], movieTag: Map[String, Int]): Double = {
    var scores: Double = 0
    val tags = movieTag.keys.toList.union(userTag.keys.toList)

    tags.foreach { tag =>
      scores += userTag.get(tag).getOrElse(0.0) * movieTag.get(tag).getOrElse(0)
    }
    scores / tags.length
}

def getGenre(userGenre: List[(String, Double)], movieGenre: String): Double = {
    userGenre.toMap.get(movieGenre).getOrElse(0)
}

def getYear(userYear: List[(Int, Double)], movieYear: Int): Double = {
    var scores: Double = 0
    val userYears = userYear.toMap.keys.toList
    userYears.foreach { year =>
      scores += getYearSimi(year, movieYear)
    }
    scores / userYears.length
}

def getSimiScore(userTag: Map[String, Double], // Map(tag, rate)
                   movieTag: Map[String, Int], // Map(tag, count)
                   userGenre: List[(String, Double)], // List[(genre, rate)]
                   movieGenre: String,
                   userYear: List[(Int, Double)], // List[(year, rate)]
                   movieYear: Int,
                   movieRate: Double): Double = {
    val tagSimi = getCosTags(userTag, movieTag)
    val genreSimi = getGenre(userGenre, movieGenre)
    val yearSimi = getYear(userYear, movieYear)
    val rateSimi = getRateSimi(movieRate)
    val score = 0.4 * genreSimi + 0.3 * tagSimi + 0.1 * yearSimi + 0.2 * rateSimi
    score
}
```

> **储存结果**

```
val schemaPortraitStr = "userId movieId score"
val schemaPortrait = StructType(schemaPortraitStr.split(" ").map(fieldName => StructField(fieldName, if (fieldName.equals("score")) DoubleType else StringType, true)))
val portraitBaseReDataFrame = spark.createDataFrame(portraitBaseReData
  .map(f => Row(f._1, f._2, f._3)), schemaPortrait)

val portraitBaseReTmpTableName = "mite_portrait_base_tmp"
val portraitBaseTableName = "mite8.mite_portrait_base_re"
portraitBaseReDataFrame.createOrReplaceTempView(portraitBaseReTmpTableName)
spark.sql("insert into table " + portraitBaseTableName + " select * from " + portraitBaseReTmpTableName)

spark.stop()
```

### 注意事项

**用户行为没有想象中靠谱**
> 用户的行为数据有时候并不是其兴趣特点所表现，比如如果系统把一些信息故意放在很显眼的位置，对于一般用户来说，不点也得点了，就会造成用户数据其实不那么靠谱。
> 如果用户产生了行为数据，但是行为数据不足够多，这个时候这些行为数据是有置信度的考量的，行为数据不够产生的描述可能会形成偏差，如果根据偏差去做推荐的话，结果可能会很离谱。

**用户兴趣实效性问题**
> 在上面的实验中，我们并没有对用户的行为数据做更多的过滤，而实际的操作中，用户的兴趣是有一定时效性的。用户的兴趣可能已经随时间偏移了，过去喜欢的东西已经不喜欢了。所以一般在实际操作过程中，一定要分辨用户的兴趣数据的有效性，一般情况下，我们会进行长期兴趣和短期兴趣的区分，人在一定时间内其兴趣是固定的，并且在一些很短暂的时间段内，其关注点事有一定意义的，这个时候其短期兴趣就生效了，所以，在实际操作的时候，长期兴趣、短期兴趣的具体的应用需要结合实际的场景区分，需要注意原始数据是否适合做兴趣描述的来源数据，是否已经失效

**冷启动问题**
> 搜有涉及到行为数据的推荐算法，都绕不开冷启动问题，即用户没有任何行为记录，没有记录就意味着无法分析画像。我们之前提到过一些冷启动的机制，比如基于内容的推荐，进行热点内容推荐，还比如根据整体数据做关联推荐，方式很多，效果不一，需要根据具体情况来看，实在不行就需要在用户注册的时候尽可能收集用户的静态数据，再根据用户的静态画像数据来推荐，总比乱推荐好

**匹配计算的问题**
> 在上面的实验中，并没有做过多匹配计算逻辑的讲解，只是简单的描述同维度的进行相似计算，然后上层做权重模型，这是一种很普通的匹配计算的过程，模型的效果在于外层权重的合理性。当有的时候没法让不同主题(用户&内容)形成一个维度矩阵的时候，这个时候就要有比较合理的映射机制，能让内容与用户的属性做关联计算

**基于用户画像的个性推荐的缺陷**
> 该推荐方式不会做用户兴趣的升级，而实际上一些知识本身就是具有一定的阶梯性的。比如我的兴趣是大数据方向的东西，于是系统天天给我推荐Spark、大数据各种技术框架等信息，在某段时间是合理的，当我对大数据领域已经熟知后，如果还天天给我推送大数据相关的信息就已经不是我想要的了。我实际上是需要寻求大数据关联的信息，甚至是升级的信息，比如基于大数据的机器学习、数据挖掘相关的东西，基于用户画像这个机制是无法做到这一层的


# 基于协同过滤的推荐算法

上面提到如果要达到推荐个性化目的，核心还是用户的行为数据，只有用户各自的行为数据才能反馈其与其他人不一样的特性，从而有针对性的进行推荐。基于协同过滤的推荐，或多或少能解决一点用户兴趣升级这类问题，能结合本身用户的行为，触达新的知识信息，并且这种递进式通过协同关系得到的，意味着大部分人的共同选择，所以还是具有一定合理性

## 协同原理
> 基于协同过滤的推荐是推荐系统中的经典推荐算法，协同过滤又分为基于用户的协同UserCF，基于物品的协同ItemCF，以及基于模型的协同ModelCF

**基于用户的协同过滤推荐**
> 基于用户的协同过滤，即希望通过用户之间的关系来达到推荐物品的目的，给某用户推荐物品，即转换为这个用户寻找他的相似用户，然后相似用户喜欢的物品，那么也可能是这个用户喜欢的物品

| 用户/午评 | 物品A | 物品B | 物品C | 物品D|
|:--------:|:----:|:-----:|:-----:|:-----:|
|用户A|Y|？|Y|？|
|用户B|-|Y|-|-|
|用户C|Y|-|Y|Y|
比如我们考虑是否向用户A推荐B或者D物品，我们先计算用户A与BC之间的余弦相似度，cosAB=0，cosAC=0.8199，我们发现用户A与用户C的相似度比较大，于是我们观察用户C喜欢了哪些物品，然后与当前用户去重，然后会发现会给用户A推荐物品D

**基于物品的协同推荐**
> 不用于基于用户的协同，这里计算的是物品之间的相似度，计算物品相似度的时候，与直接基于物品相似度推荐不同的是，使用的特征不是物品的本身属性，而依然是用户的行为

| 用户/午评 | 物品A | 物品B | 物品C |
|:--------:|:----:|:-----:|:-----:|
|用户A|Y|-|Y|
|用户B|Y|Y|Y|
|用户C|Y|？|？|
现在向用户C推荐一个物品，计算物品A与其它两种物品的相似度,cosAB=0.7, cosAC=1,或者AB共现1次，AC共现2次，通过类似这种方式，可以知道物品A与物品C在某种程度上是更相似的。这种类似共现类做计算的方式在大规模数据的情况下是很有效的一种方式，基于统计的方法在数据量足够的时候，更能体现问题的本质

**最近邻模型**
> 最近邻模型，即使用用户的偏好信息，计算单前被推荐用户与其它用户的距离，然后根据近邻进行单前用户对于物品的评分预测，典型的如K最近邻模型，假如我们使用皮尔森相关系数，计算当前用户与其它用户的相似度sim，然后在K个近邻中，通过这些相似用户，预测当前用户对于每一饿物品的评分，然后重新排序，最终推出M个评分最高的物品推荐出去，需要注意的是，基于近邻的协同推荐，比较依赖当前被推荐用户的历史数据，这样计算出来的相关度才更准确

**SVD矩阵分解**
> 对于一个user-prodcts-rating的评分数据集，我们把用户和物品的对应关系可以看作是一个矩阵X，然后矩阵X可以分解为X=A*B。 但是在这个数据集中，并不是每个用户都对每个产品进行过评分，所以存在着一些缺失值，为了满足这种分解，每个用户对应于物品都有评分，必定存在某组隐含的因子，使得用户对于物品的评分逼近真实值，而我们的目标就是通过分解矩阵得到这些隐性因子，并且通过这些因子来预测还未评分的物品。

> 有两种办法来学习隐性因子，一个是ALS交叉最小二乘法，另一个是随机梯度下降法，首先对ALS来说，首先随机化矩阵A，然后通过目标函数求B，然后对B进行归一化处理，反过来求A，不断迭代，直到A*B满足一定的收敛条件即停止。对于随机梯度下降来说，首先目标函数是凹函数或者是凸函数，通过调整因子矩阵使得我们的目标沿着凹函数的最小值，或者凸函数的最大值移动，最终到达移动阈值或者两个函数变化绝对值小雨阈值，停止因子矩阵的变化，得到的函数即为隐性因子

> 使用分解矩阵的方式进行协同推荐，可解释行比较差，但是使用RMSE作为评判标准，较容易评判，并且我们在使用这种方法的时候，要尽可能的让用户覆盖物品，即用户对物品的历史评分记录需要足够多，模型才能更准确

**社会网络模型**
> 社会网络模型是我们认为每个人之间都是有联系的，任何两个用户都可以通过某种或者多个物品的购买行为而联系起来，即如果一端的节点是被推荐用户，另一端是其它用户，他们之间通过若干个物品，最终联系到一起。基于社会网络模型，即研究用户对物品的评分行为，获取用户于用户之间的图关系，最终依据关系的距离，为用户推荐相关的物品，但是目前这种协同推荐使用的比较少

## 基于Spark的协同过滤实践
> 详情请查看[Zeppelin Notebook](https://zeppelin.tdp.domain/#/notebook/2D87AKWPW)

## 协同和基于用户兴趣的画像推荐区别
> 原则上说基于画像会将思维局限于画像兴趣的偏好内，但兴趣本身就会升级，通过历史单个用户的行为无法推测。而协同一方面考虑用户的历史行为，另一方面参考了用户周围协同的行为，而对大部分人来说，共有的行为轨迹其实很多时候能够一定程度上体现用户的自我认知，以及认知升级的过程，这意味着物品之间的关联性本身就通过共有的用户行为天然关联，而协同就是要挖掘这种潜在的关联性，这无关物品之间的属性差异。从维度上说，协同是容易产生惊喜推荐的一种机制


### 推荐系统要解决的到底是个什么样的问题？
推荐系统在本质上是一个信息检索的系统。它和搜索最大的区别是，搜索是主动式的，根据关键词和引擎参数、搜索引擎召回、机器学习排序，决定给你看到的是哪些内容。
而我们看到的推荐系统，在大多数情况下是没有主动输入的（有时会有一些简单的反馈动作），是被动出现的。
推荐系统是利用上下文，根据当前用户所处的环境，根据信息的特点来决定给你推荐什么内容和商品。

### 推荐系统的特征体系
- 用户特征: 包括但不限于用户姓名、性别、年龄、注册时间、收货地址、常用区域等用户特征
- 内容特征: 包括但不限于商品、内容的标题分词、内容的TF-IDF、内容来源、内容渠道、内容生产者等等
- 上下文特征: 代表用户当前时空状态，最近一段时间的行为抽象的特征，比如用户当前的地理位置、IP地址，最近浏览的内容、最近购买的商品
- 用户行为特征：
  * 显式反馈: 用户在网站上自然浏览或者使用网站以外，显式的提供反馈信息，如评分、评价
  * 隐式反馈: 用户在使用网站时产生的数据，隐式的反应了用户对物品的喜好，如购买，查看
  * 显式反馈能准确的反应用户对物品的真实喜好，但需要用户付出额外的代价，收集到的数据相对较少
  * 隐式的用户行为，通过一些分析和处理，也能反应用户的喜好，只是数据不是很精确，有些行为的分析存在较大的噪声

### 推荐系统分类
- 按数据：协同过滤、内容过滤、社会化过滤
- 按算法：基于领域、基于图、基于矩阵分解、概率模型

### 社会化推荐
- 优点
  - 好友推荐可以增加推荐的信任度
  - 社交网络可以解决冷启动问题
- 缺点
  - 很多时候并不一定能提高推荐算法的离线精度(准确率和召回率)

### 推荐系统界面
- 通过一定方式展示物品，主要包括物品的标题、缩略图和介绍等
- 很多推荐界面都提供了推荐理由，理由可以增加用户对推荐结果的信任度
- 推荐界面还需要提供一些按钮让用户对推荐结果进行反馈，这样才能让推荐算法不断改善用户的个性化推荐体验

### 数据收集和存储
按数据的规模和是否需要实施存取，不同的行为数据将被存在不同的媒介中
- 需要实时存取的数据存储在数据库和缓存中
- 大规模的非实时地存取数据存储在分布式文件系统中


### 推荐任务
- 新物品推荐
- 商业上需要宣传的物品推荐
- 给用户推荐不同种类的物品
- 不同的产品推荐不同新颖度的物品。如首页展示热门推荐结果、推荐系统页面展示长尾中的物品

### 推荐系统的点击率预测
- 用户u相关的特征，比如年龄、性别、活跃程度、之前有没有点击行为
- 物品i相关的特征，比如流行度，平均分，内容属性
- 物品i在推荐列表中的位置。用户的点击和用户界面的设计有很高的相关性，因此物品i在推荐列表中的位置对预测用户是否点击很重要
- 用户之前是否点击过和推荐物品i具有同样推荐解释的其他推荐结果
- 用户之前是否点击过和推荐物品i来自同样推荐引擎的其他推荐结果
如果以CTR为考核指标，把豆瓣电影单品页中「喜欢这部电影的人也喜欢」这部分换成推荐热门电影，这样这部分的点击率会提高很多。但如果考察用户点击的深度，即点击了推荐项之后又继续沿此路径发生了多少次点击，推荐系统给出的结果就要远超热门结果了。所以推荐系统从长期用户价值来看更有用处


社交网络研究中有两个最著名的问题。第一个是如何度量人的重要性，也就是社交网络顶点 的中心度(centrality)，第二个问题是如何度量社交网络中人和人之间的关系，也就是链接预测

推荐系统的任务就是联系用户和信息，一方面帮助用户发现对自己 有价值的信息，另一方面让信息能够展现在对它感兴趣的用户面前，从而实现信息消费者和信息 生产者的双赢

从物品的角度出发，推荐系统可以更好地发掘物品的长尾

推荐系统和搜索引擎对于用户来说是两个互补的工具。搜索引擎满足了用户有明确目的时的主动 查找需求，而推荐系统能够在用户没有明确目的的时候帮助他们发现感兴趣的新内容。

个性化广告投放和狭义个性化推荐的区别是，个性化推荐着重于帮助用户找到可能令他们感 兴趣的物品，而广告推荐着重于帮助广告找到可能对它们感兴趣的用户，即一个是以用户为核心， 而另一个以广告为核心。

好的推荐系统不仅仅能够准确预测用户的行为，而且能够扩展用户的视野，帮助用户
发现那些他们可能会感兴趣，但却不那么容易发现的东西。同时，推荐系统还要能够帮助商家将 那些被埋没在长尾中的好商品介绍给可能会对它们感兴趣的用户。

评测新颖度的最简单方法是利用推荐结果的平均流行度，因为越不热门的物品越 可能让用户觉得新颖。因此，如果推荐结果中物品的平均热门程度较低，那么推荐结果就可能有
比较高的新颖性。


一般认为，新用户倾向于浏览热门的物品，因为他 们对网站还不熟悉，只能点击首页的热门物品，而老用户会逐渐开始浏览冷门的物品。


随着负样本数目的增加，LFM的准确率和召回率有明显提高。不过当 ratio>10以后，准确率和召回率基本就比较稳定了。同时，随着负样本数目的增加，覆盖率不 断降低，而推荐结果的流行度不断增加，说明ratio参数控制了推荐算法发掘长尾的能力。

当数据集非常稀疏时，LFM的性能会明显下降，甚至不如UserCF和ItemCF的性能

LFM模型在实际使用中有一个困难，那就是它很难实现实时的推荐

推荐系统的目的是联系用户的兴趣和物品，这种联系需要依赖不同的媒介。
第一种方式是利用用户喜欢过的物品，给用户推荐与他喜欢过的物品相似的物品，这就是前面提到 的基于物品的算法。
第二种方式是利用和用户兴趣相似的其他用户，给用户推荐那些和他们兴趣 爱好相似的其他用户喜欢的物品，这是前面提到的基于用户的算法。除了这两种方法，
第三种重要的方式是通过一些特征(feature)联系用户和物品，给用户推荐那些具有用户喜欢的特征的物品。特征有不同的表现方式，如可以表现为物品的属性集合，也可以表现为隐语义向量


### 推荐阅读

Link: https://zhuanlan.zhihu.com/p/34026953
机器学习在热门微博推荐系统的应用 => https://blog.csdn.net/qq_40027052/article/details/79161696
Wide & Deep Learning for Recommender Systems => http://d0evi1.com/widedeep-recsys/
Youtube基于深度学习的推荐 => http://d0evi1.com/youtube-recommend2/
各大公司广泛使用的在线学习算法FTRL详解 => http://www.cnblogs.com/EE-NovRain/p/3810737.html
浅谈推荐系统基础 => https://www.jianshu.com/p/c8711ff27eb0
个性化推荐的十大挑战 => https://blog.csdn.net/SunnyYoona/article/details/28115699
《推荐系统实践》笔记 => http://www.yeolar.com/note/2013/10/06/recommend-system-2/,http://www.yeolar.com/note/2013/10/03/recommend-system/?spm=5176.100239.blogcont54403.9.wkvRES




### 信息发现
这是一个数据爆炸的时代，Web 2.0的发展已经是Web变成了数据分享的平台，如何让人们在海量的数据中找到需要的信息将变得越来越难。
如果用户对自己需求相对明确的话，使用搜索引擎可以很方便的通过一些列关键字或者搜索条件迅速的找到自己需要的信息，但是如果用户自己都不明确自己需求是什么，或者很难用简单的关键字描述自己的需求时，搜索引擎就有些显得无能为力了，这个时候推荐引擎就应运而生，推荐引擎的出现，用户获取信息的方式从简单的目标明确的数据搜索转换到更高级更符合人们使用习惯的信息发现


### 什么是协同过滤
协同过滤是利用集体智慧的一个典型方法。协同过滤一般是咋海量的用户中发掘出一小部分和你品味比较类似的用户，把这些用户成为邻居，然后根据他们喜欢的其它东西组成一个排序的目录推荐给你

### 用户的偏好信息
用户行为在个性化推荐系统中一般分两种——显性反馈行为(explicit feedback)和隐性反馈 行为(implicit feedback)。显性反馈行为包括用户明确表示对物品喜好的行为，主要方式就是评分和喜欢/不喜欢。隐性反馈行为指的是那些不能明确反应用户喜好 的行为。最具代表性的隐性反馈行为就是页面浏览行为相比显性反馈，隐性反馈虽然不明确，但数据量更大。在很多网站中，很多用户甚至只有隐性反馈数据，而没有显性反馈数据
* 显示数据
  - 评分 => 整数量化的偏好，可能的取值是 [0, n]；n 一般取值为5或者是10 => 通过用户对物品的评分，可以精确的得到用户的偏好
  - 投票 => 布尔量化的偏好，取值是0或1 => 通过用户对物品的投票，可以较精确的得到用户的偏好
  - 转发 => 布尔量化的偏好，取值是0或1 => 通过用户对物品的转发，可以精确的得到用户的偏好
  - 保存书签 => 布尔量化的偏好，取值是0或1 => 通过用户对物品的收藏，可以精确的得到用户的偏好
  - 标记标签 => 一些单词，需要对单词进行分析，得到偏好 => 通过分析用户的标签，可以得到用户对项目的理解，同时可以分析出用户的情感：喜欢还是讨厌
  - 评论 => 一段文字，需要进行文本分析，得到偏好 => 通过分析用户的评论，可以得到用户的情感：喜欢还是讨厌
* 隐式数据
  - 点击流(查看) => 一组用户的点击，用户对物品感兴趣，需要进行分析，得到偏好 => 用户的点击一定程度上反映了用户的注意力，所以它也可以从一定程度上反映用户的喜好
  - 页面停留时间 => 一组时间信息，噪音大，需要进行去噪，分析，得到偏好 => 用户的页面停留时间一定程度上反映了用户的注意力和喜好，但噪音偏大，不好利用
  - 购买 => 布尔量化的偏好，取值是 0 或 1 => 用户的购买是很明确的说明这个项目它感兴趣。
* 基于不同的行为数据，计算不同的用户、物品相似度，然后根据不同行为反应用户喜好的程度将它们进行加权，得到用户对于物品的总体喜好。
* 收集了用户行为数据，还需要对数据进行一定的预处理，最核心的工作是:
  - 减噪：用户行为数据在产生过程中可能存在大量的噪声和误操作行为，可以使用经典的数据挖掘算法过滤掉行为数据中的噪音
  - 归一化：在计算用户对物品的喜好程度时，可能需要对不同的行为数据加权，如果不同行为的数据取值相差很大，需要将各个行为的数据统一在一个相同的取值范围中，从而使得加权求和得到的总体喜好更加精确，这就需要进行归一化处理，最简单的做法就是将各类数据除以此类中的最大值，以保证归一化后的数据取值在[0,1]范围中

### 相似度计算
* 欧几里得距离: sim(x, y) = 1 / (1 + d(x, y))
* 皮尔逊相关系数: 皮尔逊相关系数一般用于计算两个定距变量间联系的紧密程度
* Cosine 相似度: Cosine 相似度被广泛应用于计算文档数据的相似度
* Tanimoto 系数: Tanimoto 系数也称为 Jaccard 系数，是 Cosine 相似度的扩展，也多用于计算文档数据的相似度

### 相似邻居的计算
* 固定数量的邻居：K-neighborhoods 或者 Fix-size neighborhoods
* 基于相似度门槛的邻居：Threshold-based neighborhoods

### UserCF vs ItemCF
* 计算复杂度：一个在线电子商务网站的用户数量往往大大超过物品的数量，同时物品的数据量相对稳定，所以计算物品相似度时计算量小，同时也不必频繁更新，这时ItemCF的性能和复杂度比UserCF更优，而新闻博客类的网站恰恰相反。
* 使用场景：非社交网络的网站中，ItemCF便于为推荐做出解释，比较适合；而在社交网络中，UserCF加上社会网络信息，可以增加用户对推荐解释的信服程度。
* 多样性：从单个用户的角度来讲UserCF的多样性比ItemCF好
* 覆盖率：ItemCF推荐有很好的新颖性，很擅长推荐长尾的物品，UserCF倾向推荐热门物品
UserCF的推荐结果着重于反映和用户兴趣相似的小群体的热点，而ItemCF的推荐结果着重于维系用户的历史兴趣。换句话说，UserCF的推荐更社会化，反映了用户所在的小型兴趣群体中物品的热门程度，而ItemCF的推荐更加个性化，反映了用户自己的兴趣传承。


### 不同的聚类问题
* 聚类结果是排他的还是可重叠的
* 基于层次还是基于划分
* 簇数目固定还是无限制的聚类
* 基于距离还是基于概率分布模型
* K均值聚类算法在当结果簇是密集的时候，而且簇与簇之间的区别比较明显时，效果比较好，同时处理大数据集时，算法是相对可伸缩的和高效的，但是需要新给定出K的个数，k的选择一般都是基于一些经验值和多次的实验结果。另外K均值对噪音和孤立点数据比较敏感，少量的这类数据就能对平均值造成极大的影响。
* Canopy聚类算法的基本原则是首先应用低成本的近似的距离计算方法高效的将数据分为多个组，称为一个Canopy，Canopy之间可以有重叠的部分，然后采用严格的距离计算方式准确的计算在同一Canopy中的点，将他们分配与最合适的簇中。Canopy聚类算法经常用于K均值聚类算法的预处理，用来找合适的K值和簇中心
* 模糊K均值聚类算法是K均值算法的拓展，原理和K均值一样，知识它的聚类结果运行存在对象属于多个簇，在计算的时候，不是将向量分配给距离最近的簇，而是计算向量和各个簇的相关性
* 狄利克雷聚类算法是一种基于概率分布模型的聚类算法，原理是先定义一个分布模型如正太分布，然后按照模型对数据进行分类，将不同的对象加入一个模型，模型会增长或者收缩，每一轮过后需要对模型的各个参数进行重新计算，同时估计对象属于这个模型的概率



### 数据清洗
- 检查数据的一致性
- 处理无效值和缺失值
- 去除脏数据，
- 处理成格式化数据

### 数据存储
- 用户行为日志和推荐日志由于随时间积累会越来越大，一般存储在分布式文件系统（HDFS），即Hive表中，当需要的时候可以下载到本地进行离线分析
- 物品信息一般存储在Hive表和HBase中，Hive可以方便离线分析时操作，但实时程序读取的时候Hive表的实时性较差，所以同时也会写一份放在HBase中供实时程序读取
- 各个程序模块生成的结果，有进程同步关系的程序一般会使用Redis作为缓冲存储，生产者会把信息写到redis中供消费者使用


### Context-aware RecSys
- Contextual Prefiltering: Give U x I x C x R, using context filter U x I x R at first, and then train the model with U x I X R, got model result: U x I -> R, give an user, return items
- Contextual Postfiltering: Give U x I x C x R, train the model with U x I x C x R, got model reuslt: U x I -> R, give an user, got item list, and then give context, return filtered items
- Contextual Modeling: Give U x I x C x R, train the model with U x I x C x R, got model reuslt: U x I x C -> R, give an user and context, return items



### 推荐系统设计
- 需求分析和用户调研
  * 用户
    - 新用户->兴趣未知，着重促销、多样性、新颖性
    - 老用户->兴趣已知，注重个性化
    - 主流用户，小众用户
  * 推荐什么：
    - 价格一致，用户经常购买的类别
      * 书、电影、音乐、文章->以用户对内容主题的兴趣为主
    - 价格不一致，用户经常购买的类别
      * 服饰、日用百货->视觉、品牌、价格、内容
    - 用户很少购买的类别
      * 房、车、装修材料、家具->专家推荐、互补推荐
    - 新品促销/库存清理->考虑整体系统的获利
  * 何时
    - Email VS 收集短信
    - 短期、长期、周期(节假日)
  * 何地
- 功能设计->具体实现哪些功能呢
  * 个性化首页
    - 个性化促销，关注信息推送
  * Item页面
    - 关联物品
    - 基于浏览历史的推荐
    - 基于购买历史的推荐
  * User页面
  * 购物车页面
  * Community页面
  * 其它：收集、email
- 界面设计
  * 如何将推荐结果呈现给用户
  * 如何收集用户信息和反馈数据
  * 目的
    - 提高用户满意度，达到推荐目的
    - 更多更好地收集高质量的用户反馈
    - 准确评测推荐算法效果
- 架构设计
  * 硬件资源的限制
  * 用户数、item数
  * 存储、接口
  * 实时响应的要求
- 算法设计
  * 优化准则
  * 数据预处理
  * 离线算法
  * 在线算法
  * 功能实现策略
  * 推荐解释
- 系统评测
  * 用户反馈
    - 点击率
    - 准换率
    - 单次使用时长
    - 重复使用率
  * A/B测试


一般情况下，一个推荐引擎模型的评估可以根据如下几个指标进行评价：用户满意度、准确度、覆盖率、多样性、新颖性、惊喜度、信任度、实时性、健壮性、商业目标。如果针对于招行电话呼出这个场景，能够选择或者实现起来比较容易的有：准确度(精准率、召回率)、覆盖率、多样性、商业目标四个维度进行评价。
1. 准确度这个就不多介绍了，就是看推荐的准不准，在数据充足的情况下可以离线评估或者在线评估。
2. 覆盖率：我理解的业务场景是电话呼出人员在向客户推荐产品的时候，其实是有多种产品可以选择推荐给用户的，那么招行到底是想不管推荐什么产品只要用户肯买就行了，还是说希望把那些卖的不好的产品也推销出去，从而达成交易(这个就是推荐引擎里面想要解决的“长尾”问题)。如果是第一种的话，一般把最热门卖的最好的产品推销出去，无论是谁，就可以达到一定的效果，如果是第二种的话，就需要保证模型的推荐结构需要具有一定的覆盖率，也就是说推荐的时候需要雨露均沾，所以是不是用这个指标是跟招行的业务需求目标相关的。另外这个指标可以离线计算得到
3. 多样性：这个体现在如果用户已经买过某个产品，或者贷过款了，还需不需要想这个用户推荐其它类型的产品，也是和业务挂钩的，也可以离线计算
4. 商业目标：除了上面提到的和业务场景挂钩的案例以外，可能还有其它的业务目标，比如就是想最大化净利润，那么这个指标可能就和不同产品的近利润占比和推荐的次数相关了，需要结合业务认为定义

然后关于数据收集这块，除了基本的用户属性画像和产品属性数据以外，更多的就是用户产生的行为数据了，一般又分为两种：显式的和隐式的，显式的意思是能够明确的得到用户对某个产品到底是like还是dislike，比如评论、评分等，隐式的话就是无法从表面看到到底是喜欢还是不喜欢，比如说购买行为，不能说购买了某个产品就表示对这个产品喜欢了，有可能买了就马上失望了，再也不买的。(但是对于贷款这个场景，暂时还没想好什么样的行为表示用户“喜欢”)，在电商场景中，隐式数据一般比较多。
但是如果是电话呼出这个场景，和电商场景有很大的不一样，最大的不同就是交互形式，在电商中，用户至少看到了推荐的产品，我们才能通过一系列的数据分析出用户到底喜不喜欢，而且还能给出对应的推荐理由让用户信服，但是在电话呼出这个场景中，这部分交互是缺失的，所以我建议我们是否能收集客户经理在向用户打电话过程中的具体非隐私数据，比如客户经理的性别，工作年龄，打电话过程中的套路(我猜应该是有几套沟通模板的，用的是那一套)，通话时间、是否在提到产品内容前结束通话(这种情况并不能表示用户对这个产品不感兴趣，而是可能对所有产品或者推荐方式不感兴趣)等信息，当然，这部分内容应该是整体方案肯定之后的内容
