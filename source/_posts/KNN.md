---
title: KNN
date: 2016-04-27 11:14:57
tags: machine learning
description: K-近邻算法
---

### k-近邻算法
- 优点：进度高、对异常值不敏感、无数据输入假定
- 缺点：计算复杂度高、空间复杂度高
- 适用数据范围：数值型和标称型

### 一般流程
* 收集数据：可以使用任何方法
* 准备数据：距离计算所需要的数值，最好是结构化的数据格式
* 分析数据：可是使用任何方法
* 训练算法：此步骤不适用于k－近邻算法
* 测试算法：计算错误率
* 使用算法：首先需要输入样本数据和结构化的输出结果，然后运行k－近邻算法判定输入数据分别属于哪个分类，最后应用对计算出的分类执行后续的处理


### 从文本文件中解析数据
* 计算已知类别数据集中的点与当前点之间的距离
* 按照距离按递增次序依次排序
* 选取与当前点距离最小的k个点
* 返回前k个点出现频率最高的类别作为当前点的预测分类。


### Algorithm
一般根据特性的不同，采用不同的度量方式，我们一般通过距离来表示个体之间差异的大小。而距离又分很多种，最基础的就是闵可夫斯基距离，而我们经常听说的欧几里得距离、曼哈顿距离和切别学夫距离分别是其对应的特俗情况，除此之外还有马斯距离(等高线)、汉明距离（两个字符串其中一个变为另外一个所需要作的最小替换次数）、编辑距离（两个字符串之间由一个转成另一个最小编辑次数）、DTW距离(Dynamic Time Warp)等，在本例中，我们利用最简单的欧式距离计算待分类的向量与训练样本集的距离。

	# inX: 用于分类的输入向量
	# dataSet: 训练样本集
	# labels: 标签向量
	# k: 最邻近的数目
	def classify(inX, dataSet, labels, k):
    	# 获取矩阵的行数
    	dataSetSize = dataSet.shape[0]
    	# tile(inX, (dataSetSize, 1)) 将inX这个行向量复制为与dataSet同维度的矩阵
    	diffMat = tile(inX, (dataSetSize, 1)) - dataSet
    	# 将矩阵里每一个元素乘以本身
    	sqDiffMat = diffMat ** 2
    	# 把矩阵的每一行相加，得到一个列向量
    	sqDistances = sqDiffMat.sum(axis=1)
    	# 将列向量的每一个元素开平方
    	distances = sqDistances ** 0.5
    	# 把列向量按照升序的形式进行排序，并以排序前各元素对应的索引的形式返回
    	sortedDistIndicies = distances.argsort()
    	return getBestLabel(k, labels, sortedDistIndicies)
    	

	def getBestLabel(k, labels, sortedDistIndicies):
		classCount = {}
		for i in range(k):
			# 获取对应元素中的标记
			voteIlabel = labels[sortedDistIndicies[i]]
			# 查询classCount中是否存在这个标记，如未存在，把该标记数的数目设为1，否则加1
			classCount[voteIlabel] = classCount.get(voteIlabel, 0) + 1

		# 对classCount这个dict根据value( key=operator.itemgetter(1))降序（默认升序，因为reverse=True，所以降序）
		sortedClassCount = sorted(classCount.items(), key=operator.itemgetter(1), reverse=True)
		return sortedClassCount[0][0]
	
### 测试算法
根据提供的数据datingTestSet.txt（一个包含4列的测试数据，前三列表示三类特征，最后一列表示标签），计算算法的出错率。通过计算，算法的出错率为24%，这是因为在计算距离时，使用欧式距离公式，不同维度的特征值可能会对其它特征值产生影响，我们可以通过归一化数据矩阵来消除这种影响，算法优化后，算法的出错率降低到了5%，有很大的提升。

	def datingClassTest(hoRatio=0.1, k=3):
	    # 将文件转换为矩阵
	    datingDataMat, datingLabels = fileToMatrix('datingTestSet.txt')
	    # 归一化矩阵
	    normMat, ranges, minVals = autoNorm(datingDataMat)
	    # 获取矩阵的行数
	    m = normMat.shape[0]
	    # 测试算法的数据条数
	    numTestVecs = int(m * hoRatio)
	    errorCount = 0
	    for i in range(numTestVecs):
	        classifierResult = classify(normMat[i, :], normMat[numTestVecs:m, :], datingLabels[numTestVecs:m], k)
	        print("the classifier came back with: %d, the real answer is: %d" % (classifierResult, datingLabels[i]))
	        if classifierResult != datingLabels[i]:
	            errorCount += 1
	    print("the total error rate is %f" % (errorCount / float(numTestVecs)))
	    return errorCount, numTestVecs

	def fileToMatrix(fileName):
	    # 打开文件
	    fr = open(fileName)
	    # 获取内容行数列表
	    arrayOlines = fr.readlines()
	    # 行数数目
	    numberOfLines = len(arrayOlines)
	    # 建立一个与内容同行3列的零矩阵
	    returnMat = zeros((numberOfLines, 3))
	    classLabelVector = []
	    index = 0
	    # 遍历每行内容
	    for line in arrayOlines:
	        # 去除第i行内容的回车符
	        line = line.strip()
	        # 根据Tab符号划分成对应的数组
	        listFromLine = line.split('\t')
	        # 将划分后的数组的前三个元素赋值给零矩阵的第i行
	        returnMat[index, :] = listFromLine[0:3]
	        # 将划分后的数组的最后一个元素添加到Label数组中
	        classLabelVector.append(int(listFromLine[-1]))
	        index += 1
	        # 返回生成的矩阵和标签数组
	    return returnMat, classLabelVector
	    
### 优化算法

由于在计算距离的时候采用的是欧式距离，针对不同维度的特征值差异可能导致某个维度对另外一个维度有较大影响。如计算（0，20000，1.1）与（76， 320000，0.1）的距离时，第二个特征值会起主导作用，计算结果几乎忽略了第三个特征值的作用，而实际上，这三个特征值时同等重要的。所以我们需要对数据集进行归一化处理。

	def autoNorm(dataSet):
	    # 返回矩阵中每列中最小元素组成的行向量
	    minVals = dataSet.min(0) 
	    # 返回矩阵中没列中最大元素组成的行向量
	    maxVals = dataSet.max(0) 
	    # 返回两个向量之差
	    ranges = maxVals - minVals
	
	    # 建立与dataSet同维度的零矩阵
	    normDataSet = zeros(shape(dataSet))
	    # 矩阵的行数
	    m = dataSet.shape[0]
	
	    # 原矩阵减去由最小值向量复制生成的同维度矩阵
	    normDataSet = dataSet - tile(minVals, (m, 1))
	
	    # 处理后的矩阵与最大最小差量复制而成的同维度矩阵的特征值相除
	    normDateSet = normDataSet / tile(ranges, (m, 1))  # eigenvalue division
	    return normDataSet, ranges, minVals
	    
	 
### 使用算法

#### 约会网站预测

	def classifyPerson():
	    resultList = ['not at all', 'in small doses', 'in large doses']
	    percentTats = float(input("percentage of time spent playing video games?"))
	    ffMiles = float(input("frequent flier miles earned per year?"))
	    iceCream = float(input("liters of ice cream consumed per year?"))
	    # 文件转换为矩阵
	    datingDataMat, datingLabels = fileToMatrix('datingTestSet.txt')
	    # 归一化矩阵
	    normMat, ranges, minVals = autoNorm(datingDataMat)
	    # 测试数据
	    inArr = array([ffMiles, percentTats, iceCream])
	    classifierResult = classify((inArr - minVals) / ranges, normMat, datingLabels, 3)
	    print("You will probably like this person: ", resultList[classifierResult - 1])
	    
	    
#### 手写识别系统
将实际图像以二进制（32x32）的形式存储在一个txt文件中，先编写一个函数img2vector，将图像转化为向量：

	def imgToVector(fileName):
	    # 建立1024列行向量
	    returnVect = zeros((1, 1024))
	    # 打开32x32像素的文件
	    fr = open(fileName)
	    for i in range(32):
	        # 读取第i行
	        lineStr = fr.readline()
	        for j in range(32):
	            # 将第i行的第j个元素负责给行向量
	            returnVect[0, 32 * i + j] = int(lineStr[j])
	    return returnVect
	    
再根据训练数据使用算法识别测试数据：
	
	def handwritingClassTest():
    trainingMat, hwLabels = getTrainingMatAndLabel()

    testFileNameList = listdir('testDigits')
    errorCount = 0.0
    mTest = len(testFileNameList)
    for i in range(mTest):
        fileNameStr = testFileNameList[i]
        fileStr = fileNameStr.split('.')[0]
        classNumStr = int(fileStr.split('_')[0])
        vectorUnderTest = imgToVector('testDigits/%s' % fileNameStr)

        classifierResult = classify(vectorUnderTest, trainingMat, hwLabels, 3)
        # print("the classifier came back with: %d, the real answer is: %d" % (classifierResult, classNumStr))
        if classifierResult != classNumStr:
            errorCount += 1.0
    print("\nthe total number of errors is: %d" % errorCount)
    print("\nthe total error rate is: %f" % (errorCount / float(mTest)))


	def getTrainingMatAndLabel():
	    hwLabels = []
	    # 获取trainingDigits文件夹下面的文件列表
	    trainFileNameList = listdir('trainingDigits')
	    m = len(trainFileNameList)
	    trainingMat = zeros((m, 1024))
	    for i in range(m):
	        # 获取第i个文件
	        fileNameStr = trainFileNameList[i]
	        # 获取文件名
	        fileStr = fileNameStr.split('.')[0]
	        # 获取数字（Label）
	        classNumStr = int(fileStr.split('_')[0])
	        hwLabels.append(classNumStr)
	        # 将第i个文件的内容添加到矩阵中
	        trainingMat[i, :] = imgToVector('trainingDigits/%s' % fileNameStr)
	    return trainingMat, hwLabels
	    
