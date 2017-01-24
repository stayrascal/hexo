---
title: bayes
date: 2016-04-28 10:59:51
tags: machine learning
description: 朴素贝叶斯
---

### 朴素贝叶斯
- 优点：在数据较少的情况下仍然有效，可以处理多类别问题
- 缺点：对于输入数据的准备方式较为敏感
- 适用数据类型：标称型数据

### 相关知识点
- 贝叶斯决策理论：选择具有最高概率的决策
- 条件概率：在某条件已经发生的情况下，计算另外一条件发生的概率 P(a|b) = P(a and b)/P(b)
- 贝叶斯准则：P(c|x)P(x)=P(x|c)P(c)

### 原理
- 数据点(x, y)是属于c1类别还是属于c2类别主要通过比较P(c1|x, y)、P(c2|x, y)来决定
- 从文本中获取特征，需要先拆分文本，这里的特征时来自文本的词条(token)，一个词条时字符的人一组合，然后将每一个文本片段表示为一个词条向量，其中值为1表示词条出现在文档中，0表示未出现


### 朴素贝叶斯的一般过程
- 收集数据：可以使用任何方法，这是使用RSS源
- 准备数据：需要数值型或者布尔型数据
- 分析数据：有大量特征时，绘制特征作用不大，此时使用直方图效果更好
- 训练算法：计算不同的独立特征的条件概率
- 测试算法：计算错误率
- 使用算法：一个常见的朴素贝叶斯应用时文档分类。可以在任意的分类场景中使用朴素贝叶斯分类器，不一定非要时文本

#### 收集数据
把文本看成单词向量

	def loadDataSet():
	    # 进行词条切分后的文档集合
	    postingList = [['my', 'dog', 'has', 'flea', 'problems', 'help', 'please'],
	                   ['maybe', 'not', 'take', 'him', 'to', 'dog', 'park', 'stupid'],
	                   ['my', 'dalmation', 'is', 'so', 'cute', 'I', 'love', 'him'],
	                   ['stop', 'posting', 'stupid', 'worthless', 'garbage'],
	                   ['mr', 'licks', 'ate', 'my', 'steak', 'how', 'to', 'stop', 'him'],
	                   ['quit', 'buying', 'worthless', 'dog', 'food', 'stupid']]
	    classVec = [0, 1, 0, 1, 0, 1]  # 1代表侮辱性文字，0代表正常言论
	    return postingList, classVec
	    
#### 准备数据
词集模型：将每个词的出现与否作为一个特征，创建一个包含所有文档中出现的不重复词的列表

	def createVocabList(dataSet):
	    # 创建一个空集
	    vocabSet = set([])
	    for document in dataSet:
	        # 创建两个集合的并集
	        vocabSet = vocabSet | set(document)
	    return list(vocabSet)
	    
将文本单词向量转化为0和1类型的数值向量，分别代表对应单词在总单词列表中是否出现

	# vocabList 词汇表
	# inputSet 输入文档
	def setOfWords2Vec(vocabList, inputSet):
	    # 创建一个其中所含元素都为0的向量
	    returnVec = [0] * len(vocabList)
	    for word in inputSet:
	        if word in vocabList:
	            returnVec[vocabList.index(word)] = 1
	        else:
	            print("the word: %s is not in my Vocabulary!" % word)
	    return returnVec

词袋模型：针对一个词在文档中不止出现一次。
为了适应词袋模型，需要对函数setOfWords2Vec()稍加修改

	# vocabList 词汇表
	# inputSet 输入文档
	def bagOfWords2VecMN(vocabList, inputSet):
	    # 创建一个其中所含元素都为0的向量
	    returnVec = [0] * len(vocabList)
	    for word in inputSet:
	        if word in vocabList:
	            returnVec[vocabList.index(word)] += 1
	    return returnVec
	    
#### 训练算法
从词向量计算计算概率,计算文档中各单词在各类别中的概率(通过某单词出现的次数除以对应类别所有单词总数)

	# trainMatrix 文档矩阵
	# trainCategory 每篇文档类别所构成的向量
	def trainNBO(trainMatrix, trainCategory):
	    numTrainDocs = len(trainMatrix)
	    numWords = len(trainMatrix[0])
	    pAbusive = sum(trainCategory) / float(numTrainDocs)
	    # 初始化分子变量
	    p0Num = zeros(numWords)
	    p1Num = zeros(numWords)
	    # 初始化分母变量
	    p0Denom = 0.0
	    p1Denom = 0.0
	    for i in range(numTrainDocs):
	    	 ＃根据文档类别统计各单词在每个类别中出现的次数以及该类别总次数
	        if trainCategory[i] == 1:
	            p1Num += trainMatrix[i]
	            p1Denom += sum(trainMatrix[i])
	        else:
	            p0Num += trainMatrix[i]
	            p0Denom += sum(trainMatrix[i])
	    p1Vect = p1Num / p1Denom
	    p0Vect = p0Num / p0Denom
	    return p0Vect, p1Vect, pAbusive
	    
#### 测试算法
利用贝叶斯分类器对文档进行分类时，要计算多个概率的乘积以获得文档属于某个类别的概率，即计算P(w0|1)P(w1|1)P(w2|1)...P(wn|1)。如果其中一个概率值为0，那么最后的乘积也为0。为了降低这种影响，可以将所有的词出现的初始化为1，并将分母化为2。即修改代码：

	# 初始化分子变量
    p0Num = ones(numWords)
    p1Num = ones(numWords)
    # 初始化分母变量
    p0Denom = 2.0
    p1Denom = 2.0
另外一个问题时下溢出，这是由于太多很小的数相乘造成的。一种解决办法是对乘积取自然对数。在代数中有ln(a*b) = ln(a) + ln(b)，采用自然对数进行处理不会有任何损失。即修改代码：
	
	p1Vect = log(p1Num / p1Denom)
    p0Vect = log(p0Num / p0Denom)
    
朴素贝叶斯分类函数

	# vec2Classify 要分类的向量
	def classifyNB(vec2Classify, p0Vec, p1Vec, pClass):
	    # 元素相乘
	    # log(P(w0|1)P(w1|1)....P(1)) = sum(log(P(wi|1))) + log(P(1))
	    p1 = sum(vec2Classify * p1Vec) + log(pClass)
	    p0 = sum(vec2Classify * p0Vec) + log(1.0 - pClass)
	    if p1 > p0:
	        return 1
	    else:
	        return 0
	
	
	def testingNB():
	    listOPosts, listClasses = loadDataSet()
	    myVocabList = createVocabList(listOPosts)
	    trainMat = []
	    for postinDoc in listOPosts:
	        trainMat.append(setOfWords2Vec(myVocabList, postinDoc))
	    p0V, p1V, pAb = trainNBO(trainMat, listClasses)
	
	    testEntry = ['love', 'my', 'dalmation']
	    thisDoc = array(setOfWords2Vec(myVocabList, testEntry))
	    print(testEntry, 'classified as: ', classifyNB(thisDoc, p0V, p1V, pAb))
	
	    testEntry = ['stupid', 'garbage']
	    thisDoc = array(setOfWords2Vec(myVocabList, testEntry))
	    print(testEntry, 'classified as: ', classifyNB(thisDoc, p0V, p1V, pAb))

#### 使用算法

##### 切分文本
	def textParse(bigString):
	    import re
	    listOfToken = re.split(r'\W+', bigString)
	    return [token.lower() for token in listOfToken if len(token) > 2]
	   
#### 使用朴素贝叶斯进行交叉验证
	def spamTest():
	    docList = []
	    classList = []
	    fullText = []
	
	    # 导入并解析文本文件
	    for i in range(1, 26):
	        wordList = textParse(open('email/ham/%d.txt' % i, encoding='latin1').read())
	        docList.append(wordList)
	        fullText.extend(wordList)
	        classList.append(0)
	
	        wordList = textParse(open('email/spam/%d.txt' % i, encoding='latin1').read())
	        docList.append(wordList)
	        fullText.extend(wordList)
	        classList.append(1)
	
	    vocabList = createVocabList(docList)
	
	    # 留存交叉验证(hold-out cross validation)
	
	    # 从两个文件中总共获取50封邮件
	    trainingSet = list(range(50))
	
	    # 从50封邮件里面随机选取10封邮件,将它们的索引号保存在testSet里面去,并从trainingSet里面删除对应索引
	    testSet = []
	    for i in range(10):
	        randIndex = int(random.uniform(0, len(trainingSet)))
	        testSet.append(trainingSet[randIndex])
	        del trainingSet[randIndex]
	
	    # 从docList里面选取trainingSet剩余索引对应的数据集作为训练集
	    trainMat = []
	    trainClass = []
	    for docIndex in trainingSet:
	        trainMat.append(setOfWords2Vec(vocabList, docList[docIndex]))
	        trainClass.append(classList[docIndex])
	
	    p0V, p1V, pSpam = trainNBO(array(trainMat), array(trainClass))
	
	    errorCount = 0
	    for docIndex in testSet:
	        wordVector = setOfWords2Vec(vocabList, docList[docIndex])
	        if classifyNB(array(wordVector), p0V, p1V, pSpam) != classList[docIndex]:
	            errorCount += 1
	    print('the error rate is: ', float(errorCount) / len(testSet))
	    
如何想要更好地估计错误率，需要将这个过程重复多次，然后求其平均