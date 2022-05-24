---
title: Pandas
date: 2017-03-21 22:56:45
tags: Python
---


## API
- link: http://pandas.pydata.org/pandas-docs/stable/10min.html#min

- df.drop([0,1,3]): 删除第1，2，4行数据
- df.drop(['column1', 'column2'], axis=1): 删除列
- df.dropna(): 删除任何含有缺失值的行， 参数how='all'表明值删除所有行都为缺失值的观测
- df.fillna(0): 用0填补所有缺失值
- df.fillna(method='ffill'): 用前一个观察值填充
- df.fillna(method='bfill'): 用后一个观察值填充
- df.fillna({'column1':value1, 'column2':value2}): 使用常量填充不同的列
- df.head(): See the top rows of the frame
- df.tail(3): Sees the bottom rows of the frame
- df.describe(): 现实统计信息
- df.T: 转置矩阵
- df.sort_index(axis=1, ascending=False): 根据axis排序
- df.sort_values(by='column'): 根据values排序
- df.copy(): 复制
- df.isin([]): 过滤

### Missing Data
- df.dropna(how='any'): 删除存在nan的行或列
- df.fillna(value=5): 用指定值填充nan
- pd.isnull(df):  得到boolean矩阵

### Operations
- df.mean()[df.mean(1)]: 求平均值
- df.apply(function): 改变值
- pd.value_counts(): 统计次数
- df.concat([one, two]): 合并并自动对齐两个数据框的变量
- pd.merge(left, right, on='column'):横向合并
- df.append(s, ignore_index=True):添加新的一行
- df.groupby(['column1', 'column2'])


### 小技巧
- df[numerical_features].hist(bins=30, figsize=(10, 7))
- 
    ```
    fig, ax = plt.subplots(1, 3, figsize=(14, 4))
    df[df.Churn == "No"][numerical_features].hist(bins=30, color="blue", alpha=0.5, ax=ax)
    df[df.Churn == "Yes"][numerical_features].hist(bins=30, color="red", alpha=0.5, ax=ax)
    ```
- 