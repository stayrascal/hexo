---
title: Machine-Learning
date: 2018-11-03 11:35:33
tags:
---

再炫目的技术归根结底都是基本模型与方法再具体领域问题上的组合，而理解这些基本模型与方法才是掌握机器学习，也是掌握任何一门学问的要义所在。

### 机器学习概观
概率在机器学习中扮演者核心角色，而频率学派和贝叶斯学派对概率迥异的解读方式也将机器学习一份为二，发展出两套完全不同的理论体系。
机器学习领域的元老Tom.Mitchell这样定义机器学习：机器学习是一门研究通过计算的手段利用经验来改善系统自身性能的学科。现在大多数经验都以数据的形式出现，所以机器学习的任务就变成了基于已知数据构造概率模型，反过来再运用概率模型对未知的数据进行预测和分析。

#### 频率视角下的机器学习
- 频率率学派口中的概率表示的是事件发生频率的极限值，只有在无限次独立重复试验之下才有绝对的精确意义。
- 在频率学派眼中，当重复试验的次数趋近于无穷大时，事件发生的频率会收敛到真实的概率之上，这种观点背后暗含了一个前提，那就是概率是一个确定的值，并不会受单次观察结果的影响。
- 频率统计理论的核心在于认定待估计的参数是固定不变的常量，讨论参数的概率分布式没有意义的；而用来估计参数的数据是随机的变量，每个数据都是参数支配下一次独立重复试验的结果，由于参数本身是确定的，那频率的波动就并非来源于参数本身的不确定性，而是由有限次观察造成的干扰而导致的。
- 统计学的核心任务之一就是根据从总体中抽取出的样本，也就是数据来估计未知的参数。参数的最优估计可以通过样本数据的分布，也就是采样分布来求解。由于频率统计将数据看做随机变量，所以计算采样分布是没有问题的。确定采样分布之后，参数估计可以等效成一个最优化的问题，而频率统计最常用的最优化方法，就是最大似然估计，它的目的是让似然概率最大化，也就是在固定参数的前提之下，数据出现的条件概率最大化。这是评率学派估计参数的基本出发点：一组数据之所以能够在单次试验中出现，是因为它出现的可能性最大。而参数估计的过程就是赋予观测数据最大似然概率的过程。(就是对全部数据采样之后得到一部分数据，然后根据这部分数据估计出一个最优的参数，就是模型训练的过程)
- 频率主义解决统计问题的基本思路就是：参数是确定，数据是随机的，利用随机的数据推断确定的参数，得到的结果也是随机的。
- 将频率主义“参数确定，数据随机”的思路应用在机器学习当中，得到的就是统计机器学习。统计机器学习的做法是通过对给定的指标(比如似然函数或者均方误差)进行最优化，来估计模型中参数的取值，估计时并不考虑参数的不确定性，也就是不考虑未知参数的先验分布。和参数相关的信息全部来源于数据，输出的则是未知参数唯一的估计结果，这就是统计机器学习的核心特征。
- 但是由于噪声和干扰的影响，我们观测到的数据并不是未知参数的准确反映，因此如何衡量估计结果的精确程度就成为统计机器学习中的一个关键问题。损失函数直接定义了模型性能的度量方式，其数学期望被称为风险，风险最小化就是参数估计得依据和准则。估计参数时需要计算风险，计算风险时需要在数据的概率分布上对损失函数进行积分，可表示数据的分布又需要依赖未知参数的精确取值，这就带来了一个难题，风险函数是没办法精确求解的。为了解决这个问题，统计机器学习引入了经验风险，用训练数据的经验分布替换原始表达式中数据的真实分布，借此将风险函数转化成了可计算的数值，比如分类问题的误分类率，回归问题的均方误差。而我们所谓的最优模型也就是使经验风险最小化的那个模型。（就是因为我们是采样的数据，并不能代表整体数据，而且可能存在一些误差或者干扰，所以并不能确定每次采样数据所估计出来的参数哪个更优，因为我们不知道真实的参数值，所以采用第二次采样估计参数时，使用第一次参数估计值作为经验值代替参数真实值进行误差计算）

#### 贝叶斯视角下的机器学习
- 频率主义把概率定义为无限次独立重复试验下事件发生频率的极限值，对于一些只会发生一次或者很少次的事件(比如飞机事故，一锤子买卖等不包含随机变量的事件)，评率主义的观点就不适用了。为了解决这个问题，贝叶斯学派给出了一种更加通用的概率定义：概率表示的是客观上事件的可信程度，也可以说成是主观上主体对事件的信任程度，它是简历在对事件的已有知识基础上的。
- 除了对概率的置信度解释之外，贝叶斯派中的另一个核心内容是贝叶斯定理，用来解决逆向概率问题。假定数据由一个生成模型给出，前向概率是在已知生成过程的前提条件下来计算数据的概率分布和数字特征，逆向概率则是已知数据的前提下反过来计算生成过程的未知特性。
- 将贝叶斯定理应用到统计推断中，就是贝叶斯主义的统计学。评率统计理论的核心在于认定待估计的参数是固定不变的常量，而用来估计得数据是随机的变量。贝叶斯统计则恰恰相反：它将待估计得参数视为随机变量，用来估计得数据是确定的常数，讨论观测数据的概率分布才是没有意义的。贝叶斯统计的任务就是根据这些确定的观测数据反过来推断未知参数的概率分布。
- 相对于频率主义的最大似然估计，贝叶斯主义在参数估计中倾向于使用后验概率最大化，使用最大后验概率估计，最大后验概率推断即使结合参数自身的分布特性，找到最可能产生观测数据的那个参数的过程。
- 贝叶斯的定理表示，后验概率正比于先验概率和似然概率的乘积，这意味着后验概率实质上就是用先验概率对似然概率做了个加权处理
- 先验信息在贝叶斯统计中占据着相当重要的地位，先验信息是在使用数据之前关于分析对象的已有知识，可当这种已有知识并不存在时，就不能对先验做出合理的建模。事实上，指定先验分布的必要性正是贝叶斯学派被频率学派诟病之处，因为先验分布不可避免地会受到主观因素的影响，这与统计学立足客观的出发点背道而驰。其实即使包含某些主观判断，先验信息也是贝叶斯主义中不可或缺的核心要素
- 当已有的知识实在不足以形成先验信息时，贝叶斯主义的处理方式是引入无信息先验，认为未知参数取均匀分布，由于这时的先验参数是个常数，这个先验概率也被称为平坦先验(flat prior)。在平坦先验之下，最大后验概率和最大似然估计等效
- 将贝叶斯定理应用到机器学习之中，完成模型预测和选择的任务，就是贝叶斯视角下的机器学习。由于贝叶斯定理大量涉及各种显式变量与隐式变量的依赖关系，通常用概率图模型来直观地描述。贝叶斯主义将未知参数视为随机变量，参数在学习之前的不确定性由先验概率描述，这中间不确定性的消除就是机器学习的作用
- 与频率主义不同的是，贝叶斯学习的输出不是简单的最优估计值，而是关于参数的概率分布，从而给出了跟加完整的信息。在预测问题时，贝叶斯学习给出的也不仅仅是一个可能性最大的结果，而是将所有结果以及概率以概率分布的形式完整地呈现出来
- 贝叶斯方法有两个缺点：
    - 对未知变量的积分运算会导致极高的计算复杂度
    - 先验分布的设定包含一定的主观性，因而一直不招老派统计学家待见


### 机器学习的使用
- 机器学习能解决的问题必然包含某些显式或者隐式的模式，没有模式的问题就不能通过机器学习解决。完全随机的问题是不可能被求解，也不可能被学习的。（问题不能是完全随机的，需要具备一定的模式）
- 一个具有解析解的问题是完全不需要机器学习的。即使问题本身没有解析解，要是能够通过数值计算的方法解决，而不实际明显的优化过程的话，也无需机器学习的使用。（问题本身不能通过纯计算的方法解决）
- 用机器学习解决问题需要一个条件，就是大量的可用数据。（有大量的数据可用）


### 频率学派的统计学习-统计机器学习
统计机器学习的核心是数据，它即从数据中来，利用不用的模型去拟合数据背后的规律；也到数据中去，用拟合出的规律去推断和预测未知的结果。几乎所有的其他模型都是从不同角度对线性回归模型做出的拓展和修正
### 贝叶斯学派的符号学习-概率图模型
和基于数据的统计学习相比，基于关系的图模型更多的代表了因果推理的发展方向。贝叶斯主义也需要计算待学习对象的概率分布，但它利用的不是海量的具体数据，而是变量之间的相关关系、每个变量的先验分布和大量复杂的积分技巧。




# 机器学习方法

https://blog.csdn.net/SEUer_jeff/article/details/65629877

正则化算法（Regularization Algorithms）

它是另一种方法（通常是回归方法）的拓展，这种方法会基于模型复杂性对其进行惩罚，它喜欢相对简单能够更好的泛化的模型。
例子：
  ● 岭回归（Ridge Regression）：岭回归是一种专用于共线性数据分析的有偏估计回归方法，实质上是一种改良的最小二乘估计法，通过放弃最小二乘法的无偏性，以损失部分信息、降低精度为代价，获得回归系数更为符合实际、更可靠的回归方法，对病态数据的耐受性远远强于最小二乘法。在平方误差的基础上增加正则项，通过确定λ的值可以在方差和偏差之间达到平衡，随λ的增大，方差减小，偏差增大

  ● 最小绝对收缩与选择算子（LASSO）：为了提高最小二乘估计的两个技术标准，子集选择法和岭回归都有缺陷。子集选择法可以得出一个可以解释的模型，但是给出的模型过于多变，而回归过程本身是离散的——因变量既不能被保留，也不能从模型中剔除。数据中的小变动会影响由子集选择法得出的不同模型而且还会降低模型的预测精度。岭回归是一个连续的过程，由于其不断收缩系数，因此较平稳。然而，他并没有将任何系数收缩为0，因而这个方法不能给出一个简单的可解释的模型。在此，我们提出一个新的方法，成为LASSO，就是“绝对收缩和选择算子”。它使一些系数收缩并将其他的设为0，因此就是说它尝试保留了子集选择法和岭回归的好的性质。

  ● GLASSO
  ● 弹性网络（Elastic Net）：ElasticNet是Lasso和Ridge回归技术的混合体。它使用L1来训练并且L2优先作为正则化矩阵。当有多个相关的特征时，ElasticNet是很有用的。Lasso 会随机挑选他们其中的一个，而ElasticNet则会选择两个。

  ● 最小角回归（Least-Angle Regression）：最小角回归和模型选择比较像，是一个逐步的过程，每一步都选择一个相关性最大的特征，总的运算步数只和特征的数目有关，和训练集的大小无关。每次都选择和期望输出相关性最大的特征。
优点：
  ● 其惩罚会减少过拟合
  ● 总会有解决方法
缺点：
  ● 惩罚会造成欠拟合
  ● 很难校准


集成算法（Ensemble algorithms）

集成方法是由多个较弱的模型集成模型组，其中的模型可以单独进行训练，并且它们的预测能以某种方式结合起来去做出一个总体预测。
该算法主要的问题是要找出哪些较弱的模型可以结合起来，以及结合的方法。这是一个非常强大的技术集，因此广受欢迎。
  ● Boosting
  ● Bootstrapped Aggregation（Bagging）
  ● AdaBoost
  ● 层叠泛化（Stacked Generalization）（blending）
  ● 梯度推进机（Gradient Boosting Machines，GBM）
  ● 梯度提升回归树（Gradient Boosted Regression Trees，GBRT）
  ● 随机森林（Random Forest）
优点：
  ● 当先最先进的预测几乎都使用了算法集成。它比使用单个模型预测出来的结果要精确的多
缺点：
  ● 需要大量的维护工作


决策树算法（Decision Tree Algorithm）

决策树学习使用一个决策树作为一个预测模型，它将对一个 item（表征在分支上）观察所得映射成关于该 item 的目标值的结论（表征在叶子中）。
树模型中的目标是可变的，可以采一组有限值，被称为分类树；在这些树结构中，叶子表示类标签，分支表示表征这些类标签的连接的特征。
例子：
  ● 分类和回归树（Classification and Regression Tree，CART）使用基尼系数来选择划分属性
  ● Iterative Dichotomiser 3（ID3）:使用信息增益来进行决策树的划分属性选择。ID3是非递增算法，单变量决策树，只考虑属性变量为离散型
  ● C4.5 和 C5.0（一种强大方法的两个不同版本）：信息增益准则对可取值数目较多的属性有偏好，为了减少这种偏好可能带来的不利影响，C4.5使用增益率来选择最优划分属性。增益率准则对可取值数目较少的属性有所偏好，所以C4.5算法并不是选择增益率最大的候选划分属性，而是使用了一个启发式：先从候选划分属性中找出信息增益高于平均水平的属性，再从中选择增益率最高的。但是C4.5需要多次顺序扫描和排序，运行效率比较低，所以C5.0就出现了，C5.0执行效率和内存使用改进，适用于大数据集。C5.0是一种多叉树（即如果根节点或中间节点存在连续型的自变量，则该变量会一分为二的展开两个分支；如果根节点或中间节点存在离散的自变量，则该变量会根据离散变量的水平数分开多个分支），就会导致某个变量一旦被使用，后面的节点将不会再启用该变量，C5.0算法通过构造多个C4.5算法，是一种boosting算法，运算速度快，运行内存小，可以人为的加入客观规则，可以处理较大的数据集
优点：
  ● 容易解释
  ● 非参数型
缺点：
  ● 趋向过拟合
  ● 可能或陷于局部最小值中
  ● 没有在线学习

回归（Regression）算法

回归是用于估计两种变量之间关系的统计过程。当用于分析因变量和一个 多个自变量之间的关系时，该算法能提供很多建模和分析多个变量的技巧。具体一点说，回归分析可以帮助我们理解当任意一个自变量变化，另一个自变量不变时，因变量变化的典型值。最常见的是，回归分析能在给定自变量的条件下估计出因变量的条件期望。
回归算法是统计学中的主要算法，它已被纳入统计机器学习。
例子：
  ● 普通最小二乘回归（Ordinary Least Squares Regression，OLSR）
  ● 线性回归（Linear Regression）
  ● 逻辑回归（Logistic Regression）
  ● 逐步回归（Stepwise Regression）
  ● 多元自适应回归样条（Multivariate Adaptive Regression Splines，MARS）
  ● 本地散点平滑估计（Locally Estimated Scatterplot Smoothing，LOESS）
优点：
  ● 直接、快速
  ● 知名度高
缺点：
  ● 要求严格的假设
  ● 需要处理异常值


人工神经网络

人工神经网络是受生物神经网络启发而构建的算法模型。
它是一种模式匹配，常被用于回归和分类问题，但拥有庞大的子域，由数百种算法和各类问题的变体组成。
例子：
  ● 感知器
  ● 反向传播
  ● Hopfield 网络
  ● 径向基函数网络（Radial Basis Function Network，RBFN）：单隐层前馈神经网络，使用径向基函数作为隐层神经元激活函数，常用的径向函数如高斯径向基函数。足够多隐层神经元的RBF网络能以任意精度逼近任意连续函数。通常采用两步过程来训练RBF网络，第一步确定神经元中心Ci常用的方式包括随机采样、聚类等，第二步通过BP算法来确定参数w和β
优点：
  ● 在语音、语义、视觉、各类游戏（如围棋）的任务中表现极好。
  ● 算法可以快速调整，适应新的问题。
缺点：
  ● 需要大量数据进行训练
  ● 训练要求很高的硬件配置
  ● 模型处于「黑箱状态」，难以理解内部机制
  ● 元参数（Metaparameter）与网络拓扑选择困难。


深度学习（Deep Learning）

深度学习是人工神经网络的最新分支，它受益于当代硬件的快速发展。
众多研究者目前的方向主要集中于构建更大、更复杂的神经网络，目前有许多方法正在聚焦半监督学习问题，其中用于训练的大数据集只包含很少的标记。
例子：
  ● 深玻耳兹曼机（Deep Boltzmann Machine，DBM）
  ● Deep Belief Networks（DBN）
  ● 卷积神经网络（CNN）
  ● Stacked Auto-Encoders
优点/缺点：见神经网络


支持向量机（Support Vector Machines）

给定一组训练事例，其中每个事例都属于两个类别中的一个，支持向量机（SVM）训练算法可以在被输入新的事例后将其分类到两个类别中的一个，使自身成为非概率二进制线性分类器。
SVM 模型将训练事例表示为空间中的点，它们被映射到一幅图中，由一条明确的、尽可能宽的间隔分开以区分两个类别。
随后，新的示例会被映射到同一空间中，并基于它们落在间隔的哪一侧来预测它属于的类别。
优点：
  ● 在非线性可分问题上表现优秀
缺点：
  ● 非常难以训练
  ● 很难解释


降维算法（Dimensionality Reduction Algorithms）

和集簇方法类似，降维追求并利用数据的内在结构，目的在于使用较少的信息总结或描述数据。
这一算法可用于可视化高维数据或简化接下来可用于监督学习中的数据。许多这样的方法可针对分类和回归的使用进行调整。
例子：
  ● 主成分分析（Principal Component Analysis (PCA)）
  ● 主成分回归（Principal Component Regression (PCR)）
  ● 偏最小二乘回归（Partial Least Squares Regression (PLSR)）
  ● Sammon 映射（Sammon Mapping）
  ● 多维尺度变换（Multidimensional Scaling (MDS)）
  ● 投影寻踪（Projection Pursuit）
  ● 线性判别分析（Linear Discriminant Analysis (LDA)）
  ● 混合判别分析（Mixture Discriminant Analysis (MDA)）
  ● 二次判别分析（Quadratic Discriminant Analysis (QDA)）
  ● 灵活判别分析（Flexible Discriminant Analysis (FDA)）
优点：
  ● 可处理大规模数据集
  ● 无需在数据上进行假设
缺点：
  ● 难以搞定非线性数据
  ● 难以理解结果的意义



聚类算法（Clustering Algorithms）

聚类算法是指对一组目标进行分类，属于同一组（亦即一个类，cluster）的目标被划分在一组中，与其他组目标相比，同一组目标更加彼此相似（在某种意义上）。
例子：
  ● K-均值（k-Means）
  ● k-Medians 算法
  ● Expectation Maximi 封层 ation (EM)
  ● 最大期望算法（EM）
  ● 分层集群（Hierarchical Clstering）
优点：
  ● 让数据变得有意义
缺点：
  ● 结果难以解读，针对不寻常的数据组，结果可能无用。


基于实例的算法（Instance-based Algorithms）

基于实例的算法（有时也称为基于记忆的学习）是这样学 习算法，不是明确归纳，而是将新的问题例子与训练过程中见过的例子进行对比，这些见过的例子就在存储器中。
之所以叫基于实例的算法是因为它直接从训练实例中建构出假设。这意味这，假设的复杂度能随着数据的增长而变化：最糟的情况是，假设是一个训练项目列表，分类一个单独新实例计算复杂度为 O（n）
例子：
  ● K 最近邻（k-Nearest Neighbor (kNN)）
  ● 学习向量量化（Learning Vector Quantization (LVQ)）
  ● 自组织映射（Self-Organizing Map (SOM)）
  ● 局部加权学习（Locally Weighted Learning (LWL)）
优点：
  ● 算法简单、结果易于解读
缺点：
  ● 内存使用非常高
  ● 计算成本高
  ● 不可能用于高维特征空间


贝叶斯算法（Bayesian Algorithms）

贝叶斯方法是指明确应用了贝叶斯定理来解决如分类和回归等问题的方法。
例子：
  ● 朴素贝叶斯（Naive Bayes）
  ● 高斯朴素贝叶斯（Gaussian Naive Bayes）
  ● 多项式朴素贝叶斯（Multinomial Naive Bayes）
  ● 平均一致依赖估计器（Averaged One-Dependence Estimators (AODE)）
  ● 贝叶斯信念网络（Bayesian Belief Network (BBN)）
  ● 贝叶斯网络（Bayesian Network (BN)）
优点：
  ● 快速、易于训练、给出了它们所需的资源能带来良好的表现
缺点：
  ● 如果输入变量是相关的，则会出现问题


关联规则学习算法（Association Rule Learning Algorithms）

关联规则学习方法能够提取出对数据中的变量之间的关系的最佳解释。比如说一家超市的销售数据中存在规则 {洋葱，土豆}=> {汉堡}，那说明当一位客户同时购买了洋葱和土豆的时候，他很有可能还会购买汉堡肉。
概念：
  ● 频繁项集：某一个项集的支持度大于最小支持度阈值
  ● 超集：{a,b,c} 是 {a,b}的超集，{a,b} 是 {a,b,c}的真子集
  ● 最大频繁项集：频繁项集L的所有超集都是非频繁项集
  ● 闭合频繁项集：项集X的直接超集的支持度都不等于它本身的支持度，并且项集本身是频繁的。
例子：
  ● Apriori 算法（Apriori algorithm）：逐层搜索的迭代算法，缺点：每生成下一层候选集的时候，空间上组合量级大，时间每一次都要扫描一下数据库进行减枝。IO次数多，效率低下
  ● Eclat 算法（Eclat algorithm）
  ● FP-growth
      ○ FP-tree构建：通过两次数据扫描，将原始数据中的事务压缩到一个FP-tree前缀树
          ■ 第一遍扫描数据，找出频繁1项集L，按降序排序
          ■ 第二篇扫描数据：
              ● 对每个transction，过滤不频繁集合，剩下的频繁项集按L顺序排序
              ● 把每个transactio的频繁1项集插入到FP-tree中，相同的前缀路径可以共用
              ● 同时增加一个header table，把FP-tree相同item连接起来
          ■ 频繁项挖掘
              ● 从header table最下面item开始，构造每个item的条件模式基CPB
              ● 构造条件FP-tree (Conditional FP-tree)
      ○ 递归挖掘FP-tree：通过FP-tree找出每个item的条件模式基、条件FP-tree，递归的挖掘条件FP-tree得到所有的频繁项集
      ○ 缺点：FP-tree结构动态维护复杂，在挖掘过程中需要递归地创建大量的条件模式树，将消耗大量的时间和空间，时空效率不高
  ● FPMAX：
      ○ 基于FP-tree，是目前较为高效与稳定的最大频繁项目集挖掘算法之一。
      ○ 通过递归的构造条件FP-tree直接在树上得到频繁集，避免了候选项集的产生，并使用MFI-tree存储已经挖掘到的最大频繁项集以及实现超集检测。后面提出的FPMAX*改进算法通过引进预计数以及构造条件MFI-tree策略更进一步地提升了FPMAX在稀疏数据库中的效率
      ○ 但是对于稠密数据库中的挖掘会产生大量的冗余递归过程，导致额外的条件FP-tree构造开销，在支持度较低时，FPMAX会因为用于超集检测的全局MFI-tree较为庞大而导致超集检测的性能下降。改进的方式有FPMAX-reduce，通过采用基于事务共同前后缀的前瞻剪枝策略来减小挖掘过程中的冗余过程，当递归过程中产生的新条件FP-tree规模较小时，FPMAX-reduce通过构造条件MFI-tree来减少后续超集检测遍历的开销
评价：FP-growth算法应用在稀疏数据库上能取得不错的性能，但是对于稠密数据库以及较低支持度是，频繁模式的数量会以指数形式增加。减少频繁模式中冗余的最主要方法是挖掘最大频繁项目集MFI，因为最大频繁项目集包含了所有的频繁项目集，常用的算法有FP-max，FPMAX*，AFOPT-tree等
  ● FP-max通过递归构造模式树的方法产生最大频繁项集，需要大量的条件模式树，造成一定的时空资源浪费
  ● FPMAX*基于FP-max提出的一种改进方法，通过引入预计数枝术和构造条件提升了算法在稀疏数据库中的性能，但是在稠密数据库会有一定的限制，并且也未能减少潜在的冗余递归的次数
  ● AFOPT-Tree通过对不同数据项集的对比实验，在超集检测优化和总体运行效率方面有优越性，但是在数据预处理阶段使用了基于基于三角矩阵的数据预处理技术，对算法的性能造成了一定的影响
  ● IFP-tree采用动态节点插入技术构建FP-tree，通过置换节点的形式降低频繁模式树的宽度，从而达到压缩树空间的目的，同时避免产生新的分支，有效的避免了节点的冗余遍历。
  ● FPMAX-reduce采用基于事务共同前后缀的前瞻减枝策略来减少挖掘过程中的冗余过程
  ● FPMFI算法采用有效的投影机制，缩减了超集检测的时间开销，同时删除子树中与最大频繁项集无关的冗余信息，压缩了FP子树的规模

图模型（Graphical Models）

图模型或概率图模型（PGM/probabilistic graphical model）是一种概率模型，一个图（graph）可以通过其表示随机变量之间的条件依赖结构（conditional dependence structure）。
例子：
  ● 贝叶斯网络（Bayesian network）
  ● 马尔可夫随机域（Markov random field）
  ● 链图（Chain Graphs）
  ● 祖先图（Ancestral graph）
优点：
  ● 模型清晰，能被直观地理解
缺点：
  ● 确定其依赖的拓扑很困难，有时候也很模糊


# 问题分析

## 算法
1. 现有算法是否能正常运行，运行时间，运行环境配置情况(内存，处理器数量)
2. 现有算法是否能并行，以及是否要求并行
3. 现有算法的输入输出格式
4. 现有算法运行流程，上下文
5. 现有算法是否依赖第三方算法库，Java中是否能找到对应的算法库
6. 现有算法逻辑是否正确
7. 算法模型、输入是否需要永久存储，存放位置
8. 算法模型运行过程中若出现中断，对应的处理方式(从头训练，从失败处继续运行)
9. 算法模型运行过程中，是否需要监控

## 数据集成

从现有的资料了解到，数据来源有两部分：
- OWS API提供
- 用户上传Excel(主要是来自EMS的告警数据)

基于现有假设对应的需求问题：
- 数据源为OWS提供结构化得数据， API已经具备
- Excel导入的也是20个字段的结构化数据，需要做格式化检查
  1. 格式化检查的规则是否已有，还是需要我们自己制定？上传的数据不符合规则时如何处理？
  2. 上传之后的Excel数据存储在什么地方？
  3. 上传的数据和从OWS获取的数据的关系，是否在模型训练之前合并在一起？
- 清洗过程每次加载2000W数据，清洗的策略已知，不保存清洗的结果
  1. 2000W数据有多大，需要占用多少空间？现有清洗耗时多长？
  2. 是否只清洗来源于OWS的数据 ？
  3. 从OWS获取数据时是一次性获取还是批量获取？是否会对其他数据请求者性能造成影响？
  4. 清洗策略实现的难易程度？
  5. 建议保存清洗结果
    - 数据准备和模型训练分离，开发时两者可独立进行
    - 出现问题时，中间产物能帮助定位问题，重新运行时可能只需要运行一个就行，节省时间
- 可从OWS获取拓扑文件
  1. 拓扑文件由谁上传？我们是否需要提供上传功能？
  2. 拓扑文件在整个算法中的作用 ？
  3. 解析拓扑文件的算法，以及输入输出格式 ？
  4. 解析结果的存储以及如何运用到算法中？
  5. 算法运行过程中，拓扑文件发成变化如何处理？
- 现有算法模型已经完成
  1. 现有算法是否能正常运行，运行时间，运行环境配置情况(内存，处理器数量)
  2. 现有算法是否能并行，以及是否要求并行
  3. 现有算法的输入输出格式
  4. 现有算法运行流程，上下文，为什么选择对应的算法？是否可选择其它算法？
  5. 现有算法是否依赖第三方算法库，Java中是否能找到对应的算法库
  6. 现有算法逻辑是否正确
  7. 算法模型、输出是否需要永久存储，存放位置
  8. 算法模型运行过程中若出现中断，对应的处理方式(从头训练，从失败处继续运行)
  9. 算法模型运行过程中，是否需要监控？(运行状态，进度)
  10. 算法输出如何反应到前端用户
- 训练时，数据加载要求并行
  1. 现有的算法数据加载，模型训练是否并行 ？
  2. 目前算法运行的环境信息 ？
- 需要有可以查看训练任务的列表
  1. 是否同一时刻同一类型的算法只能运行一个，是否需要排队
  2. 是否需要权限控制
