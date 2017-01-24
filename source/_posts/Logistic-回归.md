---
title: Logistic 回归
date: 2016-04-28 22:50:17
tags: machine learning
description: 根据现有数据对分类边界线建立回归公式，以此进行分类。“回归”一词源于最佳拟合，表示要找到最佳拟合参数集。训练分类器时的做法就是寻找最佳拟合参数，使用的是最优化解法。
---
## Logistic 回归
- 优点：计算代价不高，易于理解和实现
- 缺点：容易欠拟合，分类精度不高
- 适用数据类型：数值型和标称型数据

### Loggistic回归的一般流程
- 收集数据：采用任意方法收集数据
- 准备数据：由于需要进行距离计算，因此要求数据类型为数值型。另外，结构化数据格式则最佳。
- 分析数据：采用任意方法对数据进行分析
- 训练算法：大部分时间将用于训练，训练的目的是为了找到最佳的分类回归系数
- 测试算法：一旦训练步骤完成，分类将会很快
- 使用算法：首先需要输入一些数据，并将其转换成对应的结构化数值，然后基于训练好的回归系数就可以对这些数值进行简单的回归计算，判定它们属于哪个类别，最后就可以在输出的类别上做一些其他分析工作。

### 相关知识
- 海维塞德阶跃函数(Heavuside step function)：又称单位阶跃函数，只输入0或1
- Sigmoid函数：f(z) = 1/(1+e^(-z)),z=0时，f＝0.5，随着z的增大，f逼近与1，随着z的减小，f逼近与0， 为了实现Logistic回归，将每个特征乘以一个回归系数，然后把所有结果值相加，带入Sigmoid函数中，得到的值大于0.5的数据分入1类，小于0.5的数据归入0类。
- 梯度上升法：找到某函数的最大值，最好的方法是沿着函数的梯度方向寻找，即分别在每个点对应的每个方向上求偏导

### Algorithm
####  准备数据
	def loadDataSet():
	    dataMat = []
	    labelMat = []
	    fr = open('testSet.txt')
	    for line in fr.readlines():
	        lineArr = line.strip().split()
	        dataMat.append([1.0, float(lineArr[0]), float(lineArr[1])])
	        labelMat.append(int(lineArr[2]))
	    return dataMat, labelMat
#### 分析数据
Logistic回归梯度上升优化算法
	def sigmoid(inX):
    	return 1.0 / (1 + exp(-inX))


	# dataMatIn 2维Numpy数组(100x3)
	# classLabels 类别标签(vector 1x100)
	def gradAscent(dataMatIn, classLabels):
	    # 转换维Numpy矩阵数据类型
	    dataMatrix = mat(dataMatIn)
	    # 将行向量转换维列向量
	    labelMat = mat(classLabels).transpose()
	
	    # 矩阵大小
	    m, n = shape(dataMatrix)
	    # 移动步长
	    alpha = 0.001
	    # 迭代次数
	    maxCycles = 500
	    weights = ones((n, 1))
	    for k in range(maxCycles):
	        h = sigmoid(dataMatrix * weights)
	        # 计算误差
	        error = labelMat - h
	        weights = weights + alpha * dataMatrix.transpose() * error
	    return weights
	
	
	def plotBestFit(weights):
	    import matplotlib.pylab as plt
	    dataMat, labelMat = loadDataSet()
	    dataArr = array(dataMat)
	    n = shape(dataArr)[0]
	
	    xcord1 = []
	    ycord1 = []
	    xcord2 = []
	    ycord2 = []
	
	    for i in range(n):
	        if int(labelMat[i]) == 1:
	            xcord1.append(dataArr[i, 1])
	            ycord1.append(dataArr[i, 2])
	        else:
	            xcord2.append(dataArr[i, 1])
	            ycord2.append(dataArr[i, 2])
	
	    fig = plt.figure()
	    ax = fig.add_subplot(111)
	    ax.scatter(xcord1, ycord1, s=30, c='red', marker='s')
	    ax.scatter(xcord2, ycord2, s=30, c='green')
	
	    x = arange(-3.0, 3.0, 0.1)
	    # 0 = w0x0 + w1x1+ w2x2
	    y = (-weights[0] - weights[1] * x) / weights[2]
	    ax.plot(x, y)
	    plt.xlabel('X1')
	    plt.ylabel('X2')
	    plt.show()
	   
从图像显示的分类结果来看，值分错了四个点，但是尽管例子简单且数据量很小，这个方法却需要大量的计算。

#### 训练算法
随机梯度上升算法，每次仅用一个样本点来更新回归系数，可以在新样本到来时对分类器进行增量式更新，避免梯度上升算法在每次更新回归系数时都需要遍历整个数据集。

	def autoGradAscent0(dataMatrix, classLabels):
	    m, n = shape(dataMatrix)
	    alpha = 0.01
	    weights = ones(n)
	    for i in range(n):
	        h = sigmoid(sum(dataMatrix[i] * weights))
	        error = classLabels[i] - h
	        weights += alpha * error * dataMatrix[i]
	    return weights
	
使用该算法分类结果不会像回归梯度算法那么完美，但评价一个优化算法优劣的可靠方法时看它是否收敛。
上述算法返回的回归系数在大的波动停止后，还有一些小的周期性波动，这是由于存在一些不能正确分类的样本点，在每次迭代时会引发系数的剧烈改变。
我们期望算法能避免来回波动，从而收敛到某个值，另外收敛速度也需要加快。
改进的随机梯度上升算法

	def stocGradAscent1(dataMatrix, classLabels, numIter=150):
	    m, n = shape(dataMatrix)
	    weights = ones(n)  # initialize to all ones
	    for j in range(numIter):
	        dataIndex = list(range(m))
	        for i in range(m):
	            # alpha 在每次迭代的时候都会调整，缓解数据波动或者高频振动
	            alpha = 4 / (1.0 + j + i) + 0.0001  # apha decreases with iteration, does not
	            # 通过随机选取样本来更新回归系数,这种做法将减少周期性波动
	            randIndex = int(random.uniform(0, len(dataIndex)))  # go to 0 because of the constant
	            h = sigmoid(sum(dataMatrix[randIndex] * weights))
	            error = classLabels[randIndex] - h
	            weights += alpha * error * dataMatrix[randIndex]
	            del dataIndex[randIndex]
	    return weights
	   
使用改进的随机梯度算法后，不会出现回归系数随着迭代次数的周期性波动，归功于样本随机选择机制，其次回归系数收敛得更快。

#### 使用算法
从疝气病症预测病马的死亡率
##### 处理数据中的缺失值
- 使用可用特征的均值来填补缺失值
- 使用特殊值来填补缺失值
- 忽略有缺失值的样本
- 使用相似样本的均值添补缺失值
- 使用另外的机器学习算法缺失值

##### 对数据进行预处理
- 所有缺失值必须用一个是数值来替换，因为使用Numpy数据类型步允许包含缺失值，所以选择实数0来替换所有的缺失值，回归系数的更新公式：weights = weights = alpha * error * dataMatrix[randIndex]，如果dataMatrix的某特征值为0，则特征值的系数不做更新
- sigmoid(0)=0.5,它对结果的预测不具有任何倾向性
- 如果在测试数据集中发现了一条数据的类别标签已经缺失，则将该条数据丢弃

##### 测试算法
	def classifyVector(inX, weights):
	    prob = sigmoid(sum(inX * weights))
	    if prob > 0.5:
	        return 1.0
	    else:
	        return 0.0
	
	
	def colicTest():
	    frTrain = open('horseColicTraining.txt')
	    frTest = open('horseColicTest.txt')
	    trainingSet = []
	    trainingLabels = []
	    for line in frTrain.readlines():
	        currLine = line.strip().split('\t')
	        lineArr = []
	        for i in range(21):
	            lineArr.append(float(currLine[i]))
	        trainingSet.append(lineArr)
	        trainingLabels.append(float(currLine[21]))
	    trainWeights = stocGradAscent1(array(trainingSet), trainingLabels, 1000)
	
	    errorCount = 0
	    numTestVec = 0.0
	    for line in frTest.readlines():
	        numTestVec += 1.0
	        currLine = line.strip().split('\t')
	        lineArr = []
	        for i in range(21):
	            lineArr.append(float(currLine[i]))
	        if int(classifyVector(array(lineArr), trainWeights)) != int(currLine[21]):
	            errorCount += 1
	    errorRate = (float(errorCount) / numTestVec)
	    print("the error rate of this test is: %f" % errorRate)
	    return errorRate
	
	
	def multiTest():
	    numTests = 10
	    errorSum = 0.0
	    for k in range(numTests):
	        errorSum += colicTest()
	    print("after %d iterations the average error rate is: %f" % (numTests, errorSum / float(numTests)))
	 
从结果可以看到，10次迭代以后