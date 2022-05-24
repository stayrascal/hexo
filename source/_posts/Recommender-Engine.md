---
title: Recommender-Engine
date: 2018-05-23 22:54:46
tags:
---

### 相似度本质
1. 推荐系统中，推荐算法分为两个门派，机器学习派和相似度派。相似度门派，有这么一个潜在假设：如果两个物体很相似，也就是距离很近，那么这两个物体就很容易产生一样的动作
2. 近邻推荐的核心就是相似度计算方法的选择，由于近邻推荐并没有采用最优化思路，所以效果通常取决于矩阵的量化方式和相似度的选择
3. 相似度，与之配套的还有另一个概念就是距离，两者都是用来量化两个物体在高维空间中的亲疏程度的，它们是硬币的两面
4. 在近邻推荐中，最常用的相似度是余弦相似度。除此之外还有欧氏距离、皮尔逊相关度、自适应的余弦相似度、局部敏感哈希等
- 欧式距离
    * 欧式距离度量的是空间中两个点的绝对差异，适用于分析用户能力模型之间的差异，比如消费能力、贡献内容的能力等
- 余弦相似度
    * 余弦相似度在度量文本相似度、用户相似度、物品相似度的时候都较为常用。余弦相似度的特点是它与向量的长度无关，背后潜藏着这样一种思想：两个向量，只要方向一致，无论程度强弱，都可以视为“相似”。
    * 余弦相似度对绝对值大小不敏感这件事，在某些应用上仍然有些问题。比如用户A对两部电影评分分别是1分和2分，用户B对同样这两部电影评分是4分和5分。用余弦相似度计算出来，两个用户的相似度达到0.98。这和实际直觉不符，用户A明显不喜欢这两部电影。针对这个问题，对余弦相似度有个改进，改进的算法叫做调整的余弦相似度，就是先计算向量每个维度上的均值，然后每个向量在各个维度上都减去均值后，再计算余弦相似度。
- 皮尔逊相关度
    * 皮尔逊相关度，实际上也是一种余弦相似度，不过先对向量做了中心化，向量 p 和 q 各自减去向量的均值后，再计算余弦相似度
    * 皮尔逊相关度其实度量的是两个随机变量是不是在同增同减，两个变量的变化趋势是否一致，所以不适合用作计算布尔值向量之间相关度
- 杰卡德（Jaccard）相似度
    * 杰卡德相似度，是两个集合的交集元素个数在并集中所占的比例。由于集合非常适用于布尔向量表示，所以杰卡德相似度简直就是为布尔值向量私人定做的。
    * 余弦相似度适用于评分数据，杰卡德相似度适合用于隐式反馈数据。例如，使用用户的收藏行为
5. 按照向量维度取值是否是布尔值来看，杰卡德相似度就只适合布尔值向量，余弦相似度弹性略大，适合两种向量。欧式距离度量的是绝对差异，余弦相似度度量的是方向差异，但是调整的余弦相似度则可以避免这个弱点
6. 改进的余弦相似度是对列向量中心化，皮尔逊相关度是对行向量中心化
7. 如何做社交网络好友相似的度量
    - 相似度派
        * 统计帖子发布数量，月均发帖数量，平均帖子字数，头像，一些标签数据，例如是否大V，是否营销号，是否网红，职业等标签数据，还可以统计发文Top关键词向量及词频
        * 标签数据可计算杰卡的相似度，Top关键词可计算余弦相似度，发布量，字数等可计算欧氏距离，然后再融合这几种相似度得到总和相似度
    - 机器学习派
        * 将社交网络看成图，用node2vec方法，定义网络：用户为点，用户之间的沟通次数/点赞次数等互动行为定义为边的权重
        * 根据边的权重进行随机游走，生成序列
        * 用word2vec生成点的向量值
        * 选择相似度公式计算

### 推荐系统中的经典问题-评分预测
1. 评分预测问题只是很典型，其实并不大众，毕竟在实际的应用中，评分数据很难收集到，属于典型的精英问题；与之相对的另一类问题行为预测，才是平民级推荐问题，处处可见


### 矩阵分解
0. 目录
    - 在Netflix Prize中大放异彩的推荐算法
        * 为什么要矩阵分解
        * 基础的SVD算法
        * 增加偏置信息
        * 增加历史行为
        * 考虑时间因素
    - Facebook怎么为十亿人互相推荐好友的
        * 回顾矩阵分解
        * 交替最小二乘
        * 隐式反馈
        * 推荐计算
    - 如果关注排序效果，使用怎样的模型
        * 矩阵分解的不足
        * 贝叶斯个性化推荐
            - AUC
            - 构建样本
            - 目标函数
            - 训练方法
1. 近邻模型的问题：
    * 物品之间存在相关性，信息量并不随着向量维度增加而线性增加；
    * 矩阵元素稀疏，计算结果不稳定，增减一个向量维度，导致近邻结果差异很大的情况存在。
2. 基础的SVD算法
3. 在SVD的算法基础上，增加偏置信息：
    * 实际情况：有一些用户会给出偏高的评分，比如标准宽松的用户；有一些物品也会收到偏高的评分，比如一些目标观众为铁粉的电影，甚至有可能整个平台的全局评分就偏高。
    * 一个用户给一个物品的评分会由四部分相加：全局平均分、物品的评分偏置、用户评分的偏置、用户和物品之间的兴趣偏好
    * 和基本的 SVD 相比，要想学习两个参数：用户偏置和物品偏置
4. 在SVD的算法基础上，增加偏置信息，再增加历史行为
    * 实际情况：有的用户评分比较少。事实上这很常见，相比沉默的大多数，主动点评电影或者美食的用户是少数。换句话说，显式反馈比隐式反馈少，那么能不能利用隐式反馈来弥补这一点
    * 在 SVD 中结合用户的隐式反馈行为和属性，这套模型叫做 SVD++
    * 隐式反馈加入：除了假设评分矩阵中的物品有一个隐因子向量外，用户有过行为的物品集合也都有一个隐因子向量，维度是一样的。把用户操作过的物品隐因子向量加起来，用来表达用户的兴趣偏好。
    * 类似的，用户属性，全都转换成 0-1 型的特征后，对每一个特征也假设都存在一个同样维度的隐因子向量，一个用户的所有属性对应的隐因子向量相加，也代表了他的一些偏好
    * 学习算法依然不变，只是要学习的参数多了两个向量：x和y。一个是隐式反馈的物品向量，另一个用户属性的向量
5. 在SVD中考虑时间因素的几种做法
    * 对评分按照时间加权，让久远的评分更趋近平均值
    * 对评分时间划分区间，不同的时间区间内分别学习出隐因子向量，使用时按照区间使用对应的隐因子向量来计算；
    * 对特殊的期间，如节日、周末等训练对应的隐因子向量
6. 交替最小二乘原理(ALS)
    * 找到两个矩阵P和Q，让它们相乘后约等于原矩阵R
    * 好处
        - 在交替的其中一步，也就是假设已知其中一个矩阵求解另一个时，要优化的参数是很容易并行化的
        - 在不那么稀疏的数据集合上，交替最小二乘通常比随机梯度下降要更快地得到结果
    * 对隐式反馈的矩阵分解，需要将交替最小二乘做一些改进，改进后的算法叫做加权交替最小二乘：Weighted-ALS
    * 加权交替最小二乘这样对待隐式反馈：
        - 如果用户对物品无隐式反馈则认为评分是0
        - 如果用户对物品有至少一次隐式反馈则认为评分是1，次数作为该评分的置信度
    * 负类别样本采样
        - 按照物品的热门程度采样：一个越热门的物品，用户越可能知道它的存在。那这种情况下，用户还没对它有反馈就表明：这很可能就是真正的负样本
    * 分解后的推荐计算
        - 利用一些专门设计的数据结构存储所有物品的隐因子向量，从而实现通过一个用户向量可以返回最相似的K个物品
        - 就是拿着物品的隐因子向量先做聚类，海量的物品会减少为少量的聚类。然后再逐一计算用户和每个聚类中心的推荐分数，给用户推荐物品就变成了给用户推荐物品聚类
7. 矩阵分解的不足
    * 矩阵分解本质上是在预测用户对一个物品的偏好程度，得到这样的矩阵分解结果后，常常在实际使用时，又是用这个预测结果来排序。训练的时候以最小化预测误差，实际上只是想得到一个好一点的排序。
    * point-wise：针对单个用户对单个物品的偏好程度进行预测，得到结果后再排序的问题
    * pair-wise：直接预测物品两两之间相对顺序的问题
    * 矩阵分解都属于point-wise模型，只能收集到正样本，没有负样本，于是认为缺失值就是负样本，再以预测误差为评判标准去使劲逼近这些样本。逼近正样本没问题，但是同时逼近的负样本只是缺失值而已，还不知道真正呈现在用户面前，到底是不喜欢还是喜欢不确定
    * 人在面对选择时，总是倾向矮子中选高个子，而不是真的在意身高到底是不是180，因此，更直接的推荐模型应该是：能够较好地为用户排列出更好的物品相对顺序，而非更精确的评分
8. 贝叶斯个性化排序
    * 相对排序使用的指标比较好的是AUC，Area Under Curve，意思是ROC曲线下的面积
    * AUC 这个值在数学上等价于：模型把关心的那一类样本排在其他样本前面的概率。最大是1，完美结果，而 0.5就是随机排列，0就是完美地全部排错
    * AUC计算步骤
        - 用模型给样本计算推荐分数，比如样本都是用户和物品这样一对一对的，同时还包含了有无反馈的标识；
        - 得到打过分的样本，每条样本保留两个信息，第一个是分数，第二个是 0 或者 1，1 表示用户消费过，是正样本，0 表示没有，是负样本
        - 按照分数对样本重新排序，降序排列
        - 给每一个样本赋一个排序值，第一位r1=n，第二位r2=n-1，样本分数一样排序值调整为他们的平均值
        - 计算AUC：∑[(r - M * (M - 1) / 2)] / (M * N)，M为正样本个数，N为其它样本个数
    * BPR
        - 一个样本构造方法
            * 矩阵分解处理的是用户、物品、反馈这样的三元组形式
            * BPR关心物品间对于用户的相对排序，处理的是用户、物品1、物品2、两个物品的相对顺序四元组形式
        - 一个模型目标函数
        - 一个模型学习框架

### 模型融合
> 推荐系统在技术实现上一般划分为三个阶段：挖掘、召回、排序。
    - 挖掘的工作就是庖丁解牛般地对用户和物品做非常深入的结构化分析，各个角度各个层面的特征都被呈现出来，并且建好索引，供召回阶段使用，大部分挖掘工作都是离线进行的。
    - 召回就是在向用户计算推荐结果时，从全量的物品中筛选出一部分比较靠谱的，降低计算压力
    - 排序(融合)就是针对召回的排序结果进行排序
> 在推荐系统的模型融合阶段，就要以产品目标为导向。举个简单的例子，信息流推荐，如果以提高 CTR 为目标，则融合模型就要把预估 CTR 作为本职工作。
    - 逻辑回归
    - GBDT
    - FM



## 推荐系统和搜索引擎
- 相同点
    * 帮助用户快速发现有用信息的工具
- 不同点
    * 推荐引擎需要用户主动提供准确的关键词来寻找信息
    * 推荐系统不需要用户提供明确的需求，而是通过分析用户的历史行为给用户的兴趣建模
- 关系
    * 搜索引擎满足了用户有明确目的时的主动查找需求
    * 推荐系统能够在用户没有明确目的的时候帮助他们发现感兴趣的新内容

## 推荐系统的工作原理(以看电影为例)
- 社会化推荐：向朋友咨询，即让好友给自己推荐
- 基于内容的推荐：打开搜索引擎，输入自己喜欢的演员名，然后看看返回的结果里面有哪些电影是自己没有看过的
- 基于流行度的推荐：查看排行榜。
- 基于协同过滤的推荐：找到和自己历史兴趣相似的一群用户，看看他们最近在看什么电影