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

> **获取数据与上一节类似**

> **先进行movie候选集的处理，包括Tag预处理，合并，以及类目年份的提取**


### 注意事项

**用户行为没有想象中靠谱**
> 用户的行为数据有时候并不是其兴趣特点所表现，比如如果系统把一些信息故意放在很显眼的位置，对于一般用户来说，不点也得点了，就会造成用户数据其实不那么靠谱。
> 如果用户产生了行为数据，但是行为数据不足够多，这个时候这些行为数据是有置信度的考量的，行为数据不够产生的描述可能会形成偏差，如果根据偏差去做推荐的话，结果可能会很离谱。

***用户兴趣实效性问题*







