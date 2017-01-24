---
title: decision-making tree
date: 2016-04-27 16:39:01
tags: machine learning
description: 决策树
---

###  决策树
- 优点：计算复杂度不高，输出结果易于理解，对中间值的缺失不敏感，可以处理不相关特征数据
- 缺点：可能会产生过度匹配问题
- 适用数据字典：数值型和标称型
- 相关算法：ID3、C4.5、CART

### 一般流程
* 收集数据：可以使用任何方法
* 准备数据：树构造算法只适用于标称型数据，因此数值型数据必须离散化
* 分析数据：可以使用任何方法，构造树完成后，应该检查图形是否符合预期
* 训练算法：构造的数据结构
* 测试算法：使用经验树计算错误率
* 使用算法：此步骤可以适用于任何监督学习算法，而使用决策树可以更好地理解数据的内在含义。

### 相关名词
- 信息增益(information gain)：划分数据前后信息发生的变化
- 香农熵(熵entropy)：集合信息的度量方式，熵定义为信息的期望值，符号Xi的信息定义为l(Xi)=-log2P(Xi),P(Xi)表示选取该分类的概率
- 基尼不纯度(Gini impurity)：从一个数据集中随机选取子项，度量其被错误分类到其它分组里面的概率

### Algorithm

#### 计算香农熵
熵越高，表明混合的数据越多

	# 计算香农熵
	def calcShannonEnt(dataSet):
	    # 计算数据集中实例的总数
	    numEntries = len(dataSet)
	    # 创建一个数据字典，用它的键值记录最后一列的值
	    labelCounts = {}
	    for featVec in dataSet:
	        currentLabel = featVec[-1]
	        if currentLabel not in labelCounts.keys():
	            labelCounts[currentLabel] = 0
	        labelCounts[currentLabel] += 1
	
	    shannonEnt = 0.0
	    for key in labelCounts:
	        # 计算类别出现的概率
	        prob = float(labelCounts[key]) / numEntries
	        # 计算香农熵
	        shannonEnt -= prob * log(prob, 2)
	    return shannonEnt
	    
#### 按照获取最大信息增益的方法划分数据
对每个特征划分数据集的结果计算一次信息熵，判断哪个特征划分数据集时最好的划分方式。

按照给定的特征划分数据：

	# dataSet 待划分的数据集
	# axis    划分数据集的特征
	# value   特征的返回值
	def splitDataSet(dataSet, axis, value):
	    # 创建新的list对象
	    retDataSet = []
	    for featVec in dataSet:
	        if featVec[axis] == value:
	            reducedFeatVec = featVec[:axis]
	            reducedFeatVec.extend(featVec[axis + 1:])
	            retDataSet.append(reducedFeatVec)
	    return retDataSet
	    
遍历整个数据集，循环计算香农熵和splitDataSet()函数，找到最好的特征划分方式。

	def chooseBestFeatureToSplit(dataSet):
	    # 获取当前数据集特征属性个数
	    numFeatures = len(dataSet[0]) - 1
	    # 计算原始香农熵
	    baseEntropy = calcShannonEnt(dataSet)
	    # 原始信息增益值
	    bestInfoGain = 0.0
	    bestFeature = -1
	    for i in range(numFeatures):
	        # 创建唯一的分类标签列表(将数据集中所有第i个特征或者所有可能存在的值写入这个新list中)
	        featList = [example[i] for example in dataSet]
	        uniqueVals = set(featList)
	        newEntropy = 0.0
	
	        # 遍历当前特征中的所有唯一属性值
	        for value in uniqueVals:
	            # 对每个特征划分一次数据集
	            subDataSet = splitDataSet(dataSet, i, value)
	            prob = len(subDataSet) / float(len(dataSet))
	            # 计算新熵值
	            newEntropy += prob * calcShannonEnt(subDataSet)
	        infoGain = baseEntropy - newEntropy
	        if infoGain > bestInfoGain:
	            bestInfoGain = infoGain
	            bestFeature = i
	    return bestFeature
	    
#### 采用递归的原则处理数据集
递归结束条件：程序遍历完所有划分数据集的属性，或者每个分支的所有实例都具有相同的分类。如果所有实例具有相同的分类，则得到一个叶子节点或者终止块。

	# dataSet 数据集
	# labels  标签列表:包含了数据集中所有特征的标签
	def createTree(dataSet, labels):
	    # 创建一个包含了数据集所有类标签的列表变量
	    classList = [example[-1] for example in dataSet]
	
	    # 第一个停止条件：所有的类标签完全相同
	    if classList.count(classList[0]) == len(classList):
	        return classList[0]
	
	    # 第二个停止条件：使用完所有特征，仍然不能将数据集划分成仅包含唯一类别的分组，挑选出现次数最多的类别作为返回指
	    if len(dataSet[0]) == 1:
	        return majorityCnt(classList)
	
	    bestFeat = chooseBestFeatureToSplit(dataSet)
	    bestFeatLabel = labels[bestFeat]
	
	    # 创建树
	    myTree = {bestFeatLabel: {}}
	    del (labels[bestFeat])
	    featValues = [example[bestFeat] for example in dataSet]
	    uniqueVals = set(featValues)
	    for value in uniqueVals:
	        # 复制类标签
	        subLabels = labels[:]
	        subDataSet = splitDataSet(dataSet, bestFeat, value)
	        myTree[bestFeatLabel][value] = createTree(subDataSet, subLabels)
	    return myTree

有些时候数据集已经处理了所有属性，但是类标签依然不是唯一的，此时通常采用多数表决的方法决定该叶子节点的分类。

	# 多数表决
	def majorityCnt(classList):
	    classCount = {}
	    for vote in classList:
	        if vote not in classCount.keys():
	            classCount[vote] = 0
	        classCount[vote] += 1
	    sortedClassCount = sorted(classCount.items(), key=operator.itemgetter(1), reverse=True)
	    return sortedClassCount[0][0]
	    
#### 测试算法
	def classify(inputTree, featLabels, testVec):
	    firstStr = inputTree.keys()[0]
	    secondDict = inputTree[firstStr]
	
	    # 将标签字符串转换为索引
	    featIndex = featLabels.index(firstStr)
	    for key in secondDict.keys():
	        if testVec[featIndex] == key:
	            if type(secondDict[key]).__name__ == 'dict':
	                classLabel = classify(secondDict[key], featLabels, testVec)
	            else:
	                classLabel = secondDict[key]
	    return classLabel
	    

#### 使用算法
构造决策树很耗时，所以一般序列化对象，并保存在磁盘上，并在需要的时候读取出来。

	def storeTree(inputTree, filename):
	    import pickle
	    fw = open(filename, 'wb')
	    pickle.dump(inputTree, fw)
	    fw.close()


	def grabTree(filename):
	    import pickle
	    fr = open(filename, 'rb')
	    return pickle.load(fr)
	    
	    
#### 使用决策树预测隐形眼镜类型

	def main(filename):
	    fr = open(filename)
	    lenses = [inst.strip().split('\t') for inst in fr.readlines()]
	    lensesLabels = ['age', 'prescript', 'astigmatic', 'tearRate']
	    lensesTree = createTree(lenses, lensesLabels)
	    return lensesTree
	    
	    
#### 绘制图像

	import matplotlib.pyplot as plt
	
	# 定义文本框和箭头符号(定义树节点格式的常量)
	decisionNode = dict(boxstyle="sawtooth", fc="0.8")
	leafNode = dict(boxstyle="round4", fc="0.8")
	arrow_args = dict(arrowstyle="<-")
	
	
	# 绘制带箭头的注解
	def plotNode(nodeText, centerPt, parentPt, nodeType):
	    createPlot.ax1.annotate(nodeText, xy=parentPt, xycoords='axes fraction', xytext=centerPt,
	                            textcoords='axes fraction',
	                            va="center", ha="center", bbox=nodeType, arrowprops=arrow_args)
	
	
	# 获取叶节点的数目
	def getNumLeafs(myTree):
	    numLeafs = 0
	    firstStr = list(myTree)[0]
	    secondDict = myTree[firstStr]
	    for key in secondDict.keys():
	        if type(secondDict[key]).__name__ == 'dict':
	            numLeafs += getNumLeafs(secondDict[key])
	        else:
	            numLeafs += 1
	    return numLeafs
	
	
	def getTreeDepth(myTree):
	    maxPath = 0
	    firstStr = list(myTree)[0]
	    secondDict = myTree[firstStr]
	    for key in secondDict.keys():
	        if type(secondDict[key]).__name__ == 'dict':
	            thisDepth = 1 + getTreeDepth(secondDict[key])
	        else:
	            thisDepth = 1
	        if thisDepth > maxPath:
	            maxPath = thisDepth
	    return maxPath
	
	
	# 在父子节点间填充文本信息
	def plotMidText(centerPt, parentPt, txtString):
	    xMid = (parentPt[0] - centerPt[0]) / 2.0 + centerPt[0]
	    yMid = (parentPt[1] - centerPt[1]) / 2.0 + centerPt[1]
	    createPlot.ax1.text(xMid, yMid, txtString)
	
	
	def plotTree(myTree, parentPt, nodeText):
	    # 计算宽和高
	    numLeafs = getNumLeafs(myTree)
	    depth = getTreeDepth(myTree)
	
	    firstStr = list(myTree)[0]
	
	    # 通过计算树所有叶子节点数，划分图形的高度，从而计算当前节点的中心位置
	    centerPt = (plotTree.xOff + (1.0 + float(numLeafs)) / 2.0 / plotTree.totalW, plotTree.yOff)
	
	    # 计算父节点和子节点的中间位置，并在此处添加文本标签信息
	    plotMidText(centerPt, parentPt, nodeText)
	    plotNode(firstStr, centerPt, parentPt, decisionNode)
	    secondDict = myTree[firstStr]
	
	    # 按比例减少全局变量
	    plotTree.yOff -= 1.0 / plotTree.totalD
	    for key in secondDict.keys():
	        if type(secondDict[key]).__name__ == 'dict':
	            plotTree(secondDict[key], centerPt, str(key))
	        else:
	            plotTree.xOff += 1.0 / plotTree.totalW
	            plotNode(secondDict[key], (plotTree.xOff, plotTree.yOff), centerPt, leafNode)
	            plotMidText((plotTree.xOff, plotTree.yOff), centerPt, str(key))
	    plotTree.yOff += 1.0 / plotTree.totalD
	
	
	def createPlot(inTree):
	    fig = plt.figure(1, facecolor='white')
	    fig.clf()
	    axpros = dict(xticks=[], yticks=[])
	    createPlot.ax1 = plt.subplot(111, frameon=False, **axpros)
	
	    # 使用下列两个变量计算树节点的摆放位置，这样可以将树绘制在水平方向和垂直方向的中心位置
	    plotTree.totalW = float(getNumLeafs(inTree))  # 存储树的宽度
	    plotTree.totalD = float(getTreeDepth(inTree))  # 存储树的高度
	
	    # 追踪已经绘制的节点位置，以及放置下一个节点的恰当位置
	    plotTree.xOff = -0.5 / plotTree.totalW
	    plotTree.yOff = 1.0
	
	    plotTree(inTree, (0.5, 1.0), '')
	    plt.show()
