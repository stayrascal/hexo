---
title: Support Vector Machines
date: 2016-04-29 14:11:21
tags: machine learning
description: 支持向量机制(SVM)是最好的现成的分类器，这里说的“现成”指的是分类器不加修改即可直接使用。同时，这意味着在数据上应用基本形式的SVM分类器就可以得到低错误率的结果。SVM能够对训练集之外的数据点做出最好的分类决策。
---

### 支持向量机制
- 优点：泛化错误率低，计算开销不大，结果易解释
- 缺点：对参数调节和核函数的选择敏感，原始分类器不加修改仅适用于处理二类问题
- 适用数据类型： 数值型和标称型数据

### 相关名词
- 线性可分数据(linearly separable)：可以直接通过一种对象将数据分开
- 分割超平面(separating hyperplane):如果数据集都在在二维平面上，分割超平面就只是一条曲线，如果数据集是三维的，分割超平面就是一个平面，如果数据集是1024维的，那么久需要一个1023维的对象来对数据进行分割
- 间隔(margin)：数据集中的点到分隔面的距离，我们希望间隔仅可能地大，这是因为如果我们犯错或者在有限数据上训练分类器的话，我们希望分类器仅可能健壮
- 支持向量(support vector)：离分隔平面最近的那些点。

#### 寻找最大间隔
分割超平面的形式可以写成WtX＋b，要计算点A到分隔超平面的距离，就必须给出点到分隔面的法线或垂线的长度，该值为|WtX+b|/||W||，这里的b类似Logistic回归中的截距w0

### SVM的一般流程
- 收集数据：可以使用任意方法
- 准备数据：需要数值型数据
- 分析数据：有助于可视化分隔超平面
- 训练算法：SVM的大部分时间都源自训练，该过程主要实现两个参数的调优
- 测试算法：十分简单的计算过程就可以实现
- 使用算法：几乎所有分类问题都可以使用SVM，值得一提的是，SVM本身是一个二类分类器，对多类问题引用SVM需要对代码做一步修改

### SMO高效优化算法(Sequential Minimal Optimization)
- SMO表示序列最小优化，将大优化问题分解为多个小优化问题来求解的
- SMO算法的目标是求出一系列alpha和b，然后计算权重向量w并得到分隔超平面
- 原理：每次循环中选择两个alpha进行优化处理，一旦找到一对合适的alpha，增大一个的同时减小另一个。“合适”指这两个alpha必须要在间隔边界之外，并且还没有进行过区间处理或者不在边界上

#### SMO的辅助函数

	def loadDataSet(fileName):
	    dataMat = []
	    labelMat = []
	    fr = open(fileName)
	    for line in fr.readlines():
	        lineArr = line.strip().split('\t')
	        dataMat.append([float(lineArr[0]), float(lineArr[1])])
	        labelMat.append(float(lineArr[2]))
	    return dataMat, labelMat


	# 随机选取一个小于size不等于inputNum的数
	def selectRand(inputNum, size):
	    result = inputNum
	    while result == inputNum:
	        result = int(random.uniform(0, size))
	    return result


	# 限制alpha的范围
	def clopAlpha(alpha, high, low):
	    if alpha > high:
	        alpha = high
	    if alpha < low:
	        alpha = low
	    return alpha

