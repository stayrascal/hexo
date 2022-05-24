---
title: 数据科学家工程实践指北
date: 2020-04-04 11:55:29
tags:
---

## 前言

最近陆陆续续地看了一些数据分析师、数据科学家的 Homework，先不论在数据科学、数据分析方面的功力如何，只是从代码功底和工程实践角度来看，可以说是惨不忍睹，其中不乏在行业里浴血奋战多年的老兵。诚然，数据分析师、数据科学家的优势并不在基础代码、工程实践上，而且行业内大部分数据分析师、数据科学家大都是单兵作战，所以并不是很注重这方面，只要自己能够看懂自己写的程序，足矣。但对于在工程实践方面有卓越追求我司来讲，还是要求我们的数据分析师、数据科学家们在这个方面具备一些基本的功底。

所以以下分享一下我个人在数据工程、数据科学领的一些个人实践习惯，给刚接触这一领域的道友一些启示，由于工业界大部分数据科学类项目主要是基于 Python 技术栈，而我个人并没有实际参与过大型 Python 技术栈相关的项目，所以有些观点可能并不是特别适合，于是先抛转引玉，望各位道友补充指正。

此外我并不是要讨论代码上的艺术或者学术界的标准，我们的关注点主要还是在于如何构建数据科学方面通俗易懂、高正确性、易重现的代码。私以为要达到这个目的的影响因素有很多，所以在这里我们主要聊两个话题：

- 如何良好的建项目工程结构
- 如何优雅的写数据工程代码

## 为什么需要构建良好的代码工程结构

首先来聊聊为什么代码工程结构如此重要，私以为有一个通俗易懂的代码工程结构是一个良好的软件工程实践的前提。由于动态语言的灵活性，运行方式也十分灵活，不需要服从什么规范，这就导致使用的方式千奇百怪，可能大部分看过 Python 作业的同事深有体会。所以构建一个统一的通俗易懂的代码工程结构对于理解或者开发来讲，会是一个很好的开始。

在数据科学领域也是一样，当我们从事数据分析的时候，我们大部分情况下都在思考怎么呈现分析结论报表，怎么产生商业洞察，或者如何实现数据可视化。这些是数据分析过程中的主要工作，而且对最终结果有决定性的影响，所以我们往往容易忽略生成这些产出物代码的质量，我们总是说先撸出一版结果再看看。这些华丽的产出物都是通过一行一行的代码累积而成的，如果底层代码混杂，在需要修改产出物内容或者重现分析场景时，会成为一个噩梦。我曾接手过客户方数据科学家遗留的代码，当我尝试从这些遗留的代码中去重现它昔日的辉煌时，那暴躁的情绪让我丧失了一个温文尔雅的初级咨询师形象，默默的留下了不懂为什么要酱紫的眼泪，所以尽管分析思维、分析过程非常重要，但是代码质量亦不可或缺，我不想在未来某一天冷不丁的来了一个喷嚏。。。

因为数据分析的过程中充满了不确定性，我们可能会尝试不同的策略试验，或者快速测试某个方法，但这些尝试都可能没法达到我们预期的效果，所以我们不得不尝试其它方法，在得到最终结果前，我们可能会做很多的无用功，当然资深数据分析师一般都有一套自己特别的方法论和分析体系，来提高分析的效率。虽然有些分析在最终的结果体现上无用，但这些都是数据分析过程的一部分，不可或缺。尽管在这个过程中，我们会尽量考虑代码的复用性，但一旦启动，大部分人就不会仔细考虑代码或项目布局的结构，因此一个分析过程最好从干净简洁、逻辑分明的结构开始，并始终坚持下去。大家遵从这样一个标准去实践是项目成功的关键元素之一。

如果我们以这样一个标准来要求自己，首先其他人会感激我们。一个清晰标准化的项目结构对于新朋友来说，可以在不用深入研究大量文档的前提条件下快速的了解分析的整个流程，这也意味着他们不需要在阅读全部代码之后才知道自己要修改或者关注哪一部分内容。好的项目结构可以为我们的代码提供上下文，其它人会因此非常感谢我们，因为：

- 他们可以更轻松的在这个分析工作上与我们协作
- 他们可以从我们的分析中了解相关流程和领域的信息
- 他们会对我们分析得出的结论充满信心

比如在使用 Java 进行 Web 开发的时候，我们参考 Maven 默认的项目结构标准风格，以及按 MVC 模式或者按领域来规划我们的代码层级时，这对从未见过特定项目的人来说，去找他们想要了解的部分会非常容易。另外一个很好的例子是类 Unix 系统的文件系统层次结构标准，比如/etc 目录与/tmp 目录一样具有特定的用途，每个操作系统提供方都统一遵守这样的约定，这使得 RedHat 用户和 Ubuntu 用户在使用对方系统时都大致知道应该在哪里查找某些类型的文件。同理，我们希望我们的同事打开我们的数据科学项目时，也应该是这样。

其次，我们也会感激自己。你是否曾遇到过与我类似的场景，当我们尝试去重现几个月前甚至几年前所做的分析时，当看着 xxx_features.py.old, xxx_features_workding.py, xxx_features01.py 这些文件时，小朋友，你是否有很多问号，为什么别人。。。。另外还有一些灵魂问题，直击人心，比如：

- 当你做一些特征工程时，想加一些 field 的时候，到底应该是从头开始还是在某一个 notebook 的结果上做操作？
- 当你想执行一段绘图代码的时候，所依赖的数据是中间过程数据还是“干净”数据？
- 当需要绘一个地理图时，图上的地理位置元信息因该从哪里下载？

这些问题都十分痛苦，也是项目结构混乱的症状，良好的项目结构以及一些工程实践可以让你快速的找回当时做分析时的状态，比如职责分离，将分析抽象为 DAG，版本控制等。

## 什么是一个数据科学项目应有的项目结构

那么一个好的项目结构应该是怎么样的呢？就我经验而言，cookiecutter 提供了一个不错的模板，我们可以通过`cookiecutter https://github.com/drivendata/cookiecutter-data-science`快速新建一个数据科学项目工程，当然需要先提前通过`pip install cookiecutter`把这个工具安装好。新建的项目结构大致如下图所示，不同的项目对不需要的部分可以进行删减。

```
├── LICENSE
├── Makefile           <- 封装了一些基本命令，比如`make data`或者`make train`
├── README.md          <- 项目介绍已经开发指导
├── data
│   ├── external       <- 外部第三方数据
│   ├── interim        <- 已经被装换过的中间数据
│   ├── processed      <- 数据处理完毕，可以直接用于数据建模的数据
│   └── raw            <- 原始数据，不可变
│
├── docs               <- 一个默认的Sphinx项目，具体内容可以参考sphinx-doc.org
│
├── models             <- 训练好并序列化之后的模型
│
├── notebooks          <- Jupyter notebooks. 通常以顺序编号和简单描述命名，以`-`进行分隔，比如`1.0-jqp-initial-data-exploration`
│
├── references         <- 数据字典，手册和所有其他说明性材料
│
├── reports            <- 生成的分析报告，主要以HTML、PDF、LaTeX格式为主
│   └── figures        <- 保存在报告里面嵌入的图表信息
│
├── requirements.txt   <- 包含项目所依赖的第三发依赖包，用于重现构建整个分析环境，需要通过`pip freeze > requirements.txt`命令生成
│
├── setup.py           <- 保证当前项目可以通过`pip install -e`的方式被当成第三方进行安装
├── src                <- 源代码目录
│   ├── __init__.py    <- Makes src a Python module
│   │
│   ├── data           <- 存放在下载或者生成数据的脚本
│   │   └── make_dataset.py
│   │
│   ├── features       <- 存放将原始数据转换为直接可供模型训练数据的数据处理脚本
│   │   └── build_features.py
│   │
│   ├── models         <- 存放训练已经预测模型的脚本
│   │   ├── predict_model.py
│   │   └── train_model.py
│   │
│   └── visualization  <- 存放探索性可视化的脚本
│       └── visualize.py
│
└── tox.ini            <- 存放项目的一些基本设置，比如静态代码检查等，详情可以参考tox.testrun.org
```

下面着重介绍一些基于这个工程结构我认为对我们有帮助的一些实践。

### 数据不可变

当我们获取到原始数据的时候，**不要**直接在原始数据上进行操作修改，特别是手动的情况，更**不要**覆盖你的原始数据，因为修改操作可能会导致一些原始信息的丢失，尽管可能我们拿到的数据质量不佳，那也不要直接在原始数据集上进行操作。在数据工程中，无论是数据接入还是构建数据仓库，我们都会加一个原始数据层来保存原始数据，后续的数据操作会直接读取原始数据。我们需要把数据看作一种不可变的资源，我们只需要在分析过程中将原始数据放到数据处理的 Pipeline 中，通过对 Data Pipeline 的拆分，将中间产物保存到不同的数据目录下，这样当我们想有一些新的想法时，没必要重头运行我们的程序，同时我们尽量把我们的代码放到`src`目录下，这样如果其它人想重现整个数据分析结果时，可以通过`src`下的代码和`data/raw`的数据重现。
另外，如果数据是不可变的，那么我们就不再需要像管理代码版本一样管理数据。一般情况下我们依赖的原始数据都比较大，用类似 Git 这样的版本管理工具不太方便，而且我们的代码产库往往都有文件大小的限制，比如当上传超过 50MB 的文件到 GitHub 时，会收到警告，同时 GitHub 会拒绝超过 100M 的文件。当然，这里有一些其它的选择可以让我们存储或者同步大量的数据，比如使用`s3cmd`工具同步 AWS S3 的数据，[Git Large File Storage](https://git-lfs.github.com/)， [GitAnnex](https://git-annex.branchable.com/) 和 [dat](http://dat-data.com/)也是不错的选择。当前`data-science`这个模板默认会让你填一个 S3 Bucket 地址，然后使用 AWS CLI 来同步数据，如果不符合你的场景，可以选择其它方案进行替代。

### Notebooks 只适合探索性分析和快速试错

市面上一些 notebook 工具可以让我们快速开启数据科学旅程，比如[Jupyter notebook](http://jupyter.org/)，[Beaker notebook](http://beakernotebook.com/)，[Zeppline](http://zeppelin-project.org/)，大部分人刚接触数据科学相关的学习时，可能最开始接触的就是这个工具，凭借交互式的编程方式备受广大开发者青睐。这些工具对于探索性分析来讲非常高效，但是当我们想要去重现分析结果，或者给定不同数据再次运行 notebook 里面的逻辑时，就不那么高效了。所以我们主要将 notebook 用于探索性分析或者生成分析结果，比如建立`notebooks/explorator`和`notebooks/reports`这样的目录保存对应的内容，但是当我们准备工程化数据处理和模型构建过程的时候，还是需要在`src`下通过特定的代码实现。简单来讲，我们可以先通过 notebook 先打个草稿，减少整个项目或者解决方案的不确定性，然后再以工程化的方式将 notebook 中的逻辑转换为适合于大数据量下高质量、易维护的工程化代码。另外一个 notebook 不适合于工程化的原因是当我们使用版本控制工具去管理这些 notebooks 时候，他们主要以 json 文件的方式呈现，我们没法一目了然的看到对应的变化内容，所以我们建议不要直接在 Jupyter notebooks 上与他人一起协作。此外有两个比较好的关于 notebook 的实践我们可以采纳：

- notebook 文件的命名，命名是一个比较头疼的问题，经常看到为以 bak、copy、01、02 之类的后缀命名方式，并不能知道其中的差别是什么，我们希望 notebook 的命名能简单的描述分析的内容和分析者，通过这样的格式`<step>-<user>-<description>.ipynb`进行命名，比如`0.3-tony-visualize-distributions.ipynb`。
- 尽量最小化 notebook 中的处理逻辑，当我们有多个 notebook 时，经常会有非常多的重复代码，比如数据处理，可视化等，我们可以将多个 notebooks 公用的任务抽离出来，放源码目录下，如果是一个数据获取任务，那么将数据获取的逻辑放到`src/data/make_dataset.py`中，如果是其它可以公用的处理逻辑，那么请放在`src`源码目录下，然后在 notebook 里面引用，这样更加方便处理逻辑的测试以及修改。

### 以 DAG 的方式构建分析的代码

通常在分析过程中，我们需要运行一些长时间运行的任务，比如数据预处理或者模型训练，特别是在数据量大的场景下，这些任务的运行时间更长。如果这些任务都已经被运行了，并且已经将输出结果保存到数据目录下，比如`data/interim`，那么下一次重新运行任务的时候，其实我们并不需要每次都重头运行，可以直接读取上一次的部分结果。所以我们建议将整个任务尽量简小化，分成多段，以 DAG 的方式将多个任务串联起来。当然拆分的任务并不是越多越好，这里需要根据具体情况去权衡，分成的子任务越多，就意味着会有更多的序列化、磁盘 IO 读写的操作，这部分在大数据量下其实也是蛮耗时间的，甚至比重新计算更耗时，这样就得不付出了。所以建议在生产环境尽量不要这么做，在开发环境可以多多尝试，因为数据处理过程是一个看不着的过程，数据处理的逻辑除了可以用单元测试来保证以外，很大程度上还是要依赖端到端的前后比对结果，开发阶段变化较多，如果保存一些中间产物的话也能够帮助快速定位问题。当任务拆解完成之后，我们可以使用`make`来管理不同任务之间的依赖，特别是运行一个耗时很长的任务时。也有一些其它工具可以用来管理 DAGs，比如 [Airflow](http://pythonhosted.org/airflow/cli.html)，[Paver](http://paver.github.io/paver/#)，[Luigi](http://luigi.readthedocs.org/en/stable/index.html)，[Snakemake](https://bitbucket.org/snakemake/snakemake/wiki/Home)， [Ruffus](http://www.ruffus.org.uk/)， [Joblib](https://pythonhosted.org/joblib/memory.html) 等。

### 用虚拟环境构建运行项目

Python 的版本以及依赖包的管理是一个很麻烦的事情。当我们尝试去重现一个分析结果或者有新的伙伴加入到项目中时，往往第一件事情就是重现运行环境，我们需要保证工作在一起的人使用相同版本的工具，依赖包。一个有效的方式就是使用[virtualenv](https://virtualenv.pypa.io/en/latest/)来创建虚拟环境，然后用[virtualenvwrapper](https://virtualenvwrapper.readthedocs.org/en/latest/)来管理多个虚拟环境，并将项目用到的所有第三方依赖包全部显示在`requirements.txt`中，这样其它小伙伴就能轻松愉快的和你构建一模一样的运行环境了。

需要注意一点的时候，每一次有新增或者删除第三方依赖包时，需要运行`pip freeze > requirements.txt`这个命令刷新 requirements.txt 的内容，这个操作可以加入到 githook 脚本中，避免忘记新增带来的“环境”问题。除此之外，我个人习惯于使用 conda + pipenv 的方式达到上述的目的，pipenv 是最近几年才开始流行的 Python 依赖包版本控制工具，有很多的优秀的特性，比如在安装依赖包的时候，可以显式的将依赖包的版本加入到 PipeFile 中(和 npm 的 package.json, Maven 的 pom.xml 或者 Gradle 的 build.gradle 类似)，不再需要手动的将依赖包添加到`requrements.txt`中，但是有一个相对致命的问题就是初次安装第三依赖包的时候耗时太长，而且一般在工程实践中，我们会将应用程序构建为 Docker 镜像，在构建的时候需要先安装 pipenv 才行，不像 pip 原生就被 Python 支持。

### 不要使用版本控制工具保存密钥和敏感配置信息

在分析过程中，我们或多或少会遇到需要读取 DB 或者公有云服务的情况，不要图一时方便，将这些密钥信息硬编码在分析的代码之中。我们提倡的方式是从系统环境变量里面读取这些内容，除此之外也可以创建一个特殊的文件存储密钥和配置信息，比如可以在项目根目录下创建一个.env 文件，但是不要将该文件提交到代码仓库中，然后使用`python-dotenv`加载这个文件中的配置信息到系统环境变量中。如果是用 Kubernetes 进行编排运行的话，可以从 ConfigMap 中读取这部分信息。这部分内容可以参考其他工程项目的最佳实践。

## 如何优雅的写数据工程代码

接下来咱们再聊聊如果优雅的写数据工程代码。

特征工程在数据分析、数据工程、机器学习中占了大部分工作，往往变化最多的也是这部分的内容，关于这部分的代码，我们主要关注两个方面：可读性和性能。大部分情况下，我们会使用`Pandas + sklearn`或者`Spark`进行数据特征处理，他们在 API 上有一定的相似性，所以下面以`Pandas + sklearn`为例。

### 代码可读性

很多情况下，我们会根据数据特征衍生出其它的特征，比如以时间日期为例，在时序性或者周期性分析过程中，我们往往会把单个时间特征衍生为年、月、日、时、第几周、星期几、季节、上下午、月初、月末、是否周末、是否节假日等衍生特征。但在进行特征衍生的时候，一些人会直接在唯一的一个 Python 文件里面，过程式把处理逻辑一个一个的列出来，而且混杂其它处理逻辑，导致整个 Python 文件过大，理解比较耗时，而且也不容易测试。有一些工程经验的人会写一个方法把部分逻辑封装起来，可读性和测试性提高了不少。`sklearn`和`Spark`其实封装了不少特征处理器，在进行特征处理的时候我们可以把这些处理器串行的放入 Pipeline 中，可以通过组合的方式选择性的将要处理的内容串联在一起，不论是可读性还是可测性，会有大大的提升，针对一些框架没有提供的特征处理器，我们可以自己定制化，比如针对上述时间特征处理，我们可以这样实现：

```
from sklearn.base import TransformerMixin
class CustomTimeTransformer(TransformerMixin):
    def __init__(self, time_col='timestamp', period_dict=None, season_dict=None):
        self.time_col = time_col
        self.period_dict = period_dict
        self.season_dict = season_dict

    def transform(self, df):
        data = df.copy()
        data[self.time_col] = data[self.time_col].apply(lambda x: pd.Timestamp(x))
        data['year'] = data[self.time_col].apply(lambda x: x.year)
        data['mth'] = data[self.time_col].apply(lambda x: x.month)
        data['day'] = data[self.time_col].apply(lambda x: x.day)
        data['hour'] = data[self.time_col].apply(lambda x: x.hour)
        data['minute'] = data[self.time_col].apply(lambda x: x.minute)
        data['second'] = data[self.time_col].apply(lambda x: x.second)
        data['minute_of_day'] = data[self.time_col].apply(lambda x: x.minute + x.hour * 60)
        data['week'] = data[self.time_col].apply(lambda x: x.dayofweek)
        data['day_of_year'] = data[self.time_col].apply(lambda x: x.dayofyear)
        data['week_of_year'] = data[self.time_col].apply(lambda x: x.week)
        if self.period_dict:
            data['period'] = data['hour'].map(period_dict)
        if self.season_dict:
            data['season'] = data['mth'].map(season_dict)
        return data

    def fit(self, *_):
        return self

class CustomTimeBooleanTransformer(TransformerMixin):
    def __init__(self, time_col='timestamp', public_vacations=[]):
        self.time_col = time_col
        self.public_vacations = public_vacations

    def transform(self, df):
        data = df.copy()
        data[self.time_col] = data[self.time_col].apply(lambda x: pd.Timestamp(x))
        data['is_leap_year'] = data[self.time_col].apply(lambda x: x.is_leap_year)
        data['is_mth_start'] = data[self.time_col].apply(lambda x: x.is_month_start)
        data['is_mth_end'] = data[self.time_col].apply(lambda x: x.is_month_end)
        data['is_quarter_start'] = data[self.time_col].apply(lambda x: x.is_quarter_start)
        data['is_quarter_end'] = data[self.time_col].apply(lambda x: x.is_quarter_end)
        data['is_year_start'] = data[self.time_col].apply(lambda x: x.is_year_start)
        data['is_year_end'] = data[self.time_col].apply(lambda x: x.is_year_end)
        data['is_weekend'] = data[self.time_col].apply(lambda x: True if x.dayofweek in [5, 6] else False)

        data['is_working'] = False
        data['tmp_hour'] = data[self.time_col].apply(lambda x: x.hour)
        data.loc[((data['tmp_hour'] >= 8) & (data['tmp_hour'] < 22)), 'is_working'] = True
        data.drop(['tmp_hour'], axis=1, inplace=True)

        if self.public_vacations:
            data['is_public_holiday'] = data[self.time_col].apply(
                lambda x: True if x.strftime('%Y%m%d') in self.public_vacations else False)
        return data

    def fit(self, *_):
        return self

class CustomTimeSeriesTransformer(TransformerMixin):
    def __init__(self, cols=None):
        self.cols = cols

    def transform(self, df):
        data = df.copy()
        for col in self.cols:
            # 时间序列历史特征
            for i in range(1, 4):
                data["{}-{}day".format(col, i)] = data[col].shift(1)

            # 趋势特征
            data[col + '_last_3_day_mean'] = (data[col].shift(1) + data[col].shift(2) + data[col].shift(3)) / 3
            # 窗口差异值特征
            data[col + '_last_2_mins'] = data[col].shift(1) - data[col].shift(2)
            # 自相关特征¶
            data[col + '_lag_1_corr'] = data[col].autocorr(1)
            data[col + '_lag_2_corr'] = data[col].autocorr(2)
        return data

    def fit(self, *_):
        return self
```

在使用的时候，只需要进行组合即可，每个 Transformer 可以由各自的单元测试来验证，并且涉及到多个数据处理任务时，可以并行开发。

```
ctf = CustomTimeTransformer(period_dict=period_dict, season_dict=season_dict)
ctbt = CustomTimeBooleanTransformer(public_vacations=public_vacation_list)
model = Pipeline([('ctf', ctf), ('ctbt', ctbt)])
model.fit_transform(df)
```

其它数据补全、空间特征、文本特征处理也可以参考类似操作。同时我们还可以把这些 Transformer 与机器学习模型结合一起放到 Data Pipeline 中运行。

### 数据处理性能

数据处理过程中，除了代码可读性以外，另外一个需要注意的是处理性能。`Pandas`相对`Spark`来讲比较轻量级，一般也是单机运行，适合处理百兆以内的数据，`Spark`凭借分布式计算的能力可以处理大量数据，二者都有对应的调优技巧，这里就不对`Spark`的调优进行展开，只是简单列一些调优点，主要介绍我们在使用`Pandas`的时候可以有哪些性能提升的技巧。

首先， Pandas 的[官方文档](https://pandas.pydata.org/pandas-docs/stable/user_guide/enhancingperf.html)上已经列了一些简单的技巧，比如使用`Cython`, `ndarray`, `Numba`, 添加类型等，除此之外我们可以聊一些其它的技巧。

#### 使用 hdf、feather、h5py 格式文件加快数据读取

很多简单的分析都是直接从 CSV 文件中直接读取数据，当数据量比较大时，读取数据比较缓慢，这个时候我们可以在第一次读取数据后，将数据以 hdf 的形式保存，后续重新运行这个任务时，直接从 hdf 文件读取，加快读取速度。下面是一个单机性能对比，可以看到当数据量达到成千上万行时(当然也和列数有关)，hdf 的优势随着数据量的增加会越来越明显。

![](https://raw.githubusercontent.com/stayrascal/images/master/pandas/hdf.png)

#### 使用 itertuples、iterrows 代替朴素 for 循环

很多时候，我们需要遍历 Dataframe 的内容，做一些复杂逻辑处理，最简单的处理方式是通过 for 循环获得每一行，然后进行处理，然而这种方式最耗时，特别是数据量比较大的时候。替代方式是使用 itertuples 和 iterrows，itertuples 的性能会比 iterrows 好很多，但是它会丢掉元信息，也就是只能通过索引号没法通过列名来获取对应的值，可读性上不是那么友好，需要均衡。

![](https://raw.githubusercontent.com/stayrascal/images/master/pandas/iteruples.png)

#### 使用 apply 方法替代 iterrows

尽管 iterrows 的性能效果已经在朴素 for 循环上有很大的提升，但直接使用 Dataframe 提供的 apply 方法性能提升会更大。有的朋友会问，那所有的都直接使用 apply 方法好了嘛，不急，存在即合理，apply 方法有一个缺陷就是只能传一个参数，也就是 Dataframe 里面的每一行，在一些涉及到外部变量的场景下就没法适用了，当然可以通过一些骚操作比如把外部参数作为一列加入到 Dataframe 里面去，带来的问题是数据重复内存增加。

![](https://raw.githubusercontent.com/stayrascal/images/master/pandas/apply.png)

#### 使用 apply、transform、agg 函数式尽量适用内置函数

我们在使用 Dataframe 提供的 apply、transform、agg 这些 API 时，最好使用一些简单的函数甚至只使用内置函数，Pandas 本身对这些场景有优化，如果是一些复杂逻辑的话，还是不要放到这些 API 里了。

![](https://raw.githubusercontent.com/stayrascal/images/master/pandas/transform.png)

#### 类型参数优化

大多数情况，当我们读取数据的时候，并不会指定数据的类型，Pandas 会默认使用对象或者高精度的数据类型加载数据，比如 float64、object 等。在我们对精度要求并没有那么高时，我们可以一开始的时候就指定加载的数据类型，或者手动降低数据的精度，比如使用 float32 替换 float64 时，内存使用会减低一半。对于一些类型特征，Pandas 默认会用 object 数据类型，我们可以使用 category 进行替换，替换后内存使用减少了 17 倍。省下来的数据意味着我们可以加载更多的内容或者加快计算的速度了。

![](https://raw.githubusercontent.com/stayrascal/images/master/pandas/data-type.png)

#### 并行计算库 Modin、Ray、Dask、Swifer

如果上述操作试完之后，还是觉得计算缓慢，可以尝试一下这些并行计算库。他们在 Pandas 的基础上进行了优化，让你使用起来和使用 Pandas 没有任何区别，但是他们只支持部分的 API，比如并不支持读取 Excel 格式的数据，但遇到他们不支持的 API 时，框架会自动降级使用 Pandas 原生的 API 进行操作，所以也不用担心兼容问题。此外需要注意一点的是，小数据量场景下就不要用这些工具了，这种场景下他们的性能还不如原生 API。

![](https://raw.githubusercontent.com/stayrascal/images/master/pandas/dask.png)

除了这些并行库以外，也可以用使用多线程、多进程的方式实现数据并行处理，但是需要手动维护对应的池子。

#### Spark 基本调优技巧

如果是使用 Spark 进行数据调优的话，这里也有一些对应的调优技巧：

- 尽可能复用同一个 RDD，避免创建重复的 RDD
- 对多次使用的 RDD 进行持久化
- 尽量避免使用 shuffle 类算子，或者使用 map-side 预聚合 shuffle 操作
- 使用高性能的算子
  - 使用 reduceByKey、aggregateByKey 替换 groupByKey
  - 使用 mapPartitions 替换普通 map
  - 使用 foreachPartitions 替代 foreach
  - 提前 filter，然后再做 coalesce 操作
  - 使用 repartitionAndSortWithinPartitions 替代 repartition 和 sort 类操作
- 广播大变量，避免 shuffle
- 使用 Kryo 优化序列性能

## 写在最后

关于数据科学家工程实践方面的经验就介绍到此，也欢迎大家多多补充。其实数据科学工程实践和其它业务开发实践并没有什么很大的区别，严格上说，数据科学只是软件开发中的一环，目前在工程实践这块也没有一个成熟的指导文档，还处于一个摸索的过程，所以很大程度上需要像其它领域借鉴学习。

**阅读参考**

- Cookiecutter Data Science: https://drivendata.github.io/cookiecutter-data-science/
- Pandas Enhancing Performance: https://pandas.pydata.org/pandas-docs/stable/user_guide/enhancingperf.html
- Parallel Python: https://towardsdatascience.com/10x-faster-parallel-python-without-python-multiprocessing-e5017c93cce1
- Dask Best Practices: https://docs.dask.org/en/latest/best-practices.html
- Spark 性能优化: https://tech.meituan.com/2016/05/12/spark-tuning-pro.html
