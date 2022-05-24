---
title: Pandas 优化
date: 2020-02-01 11:03:12
tags:
---

# Pandas 性能优化方法

## 1. 数据读取与存取

尽可能的避免读取原始 csv，使用 hdf、feather 或 h5py 格式文件加快文件读取

## 2. itertuples 与 iterrows

itertuples 和 iterrows 都能实现按行进行迭代的操作，但在任何情况下 itertuples 都比 iterrows 快很多倍。

## 3. apply、transform 和 agg 时尽量使用内置函数

```
test_df.groupby(['a', 'b'])['c'].transform(lambda x: x.count())
=>
test_df.groupby(['a', 'b'])['c'].transform('count')
test_df.groupby(['a', 'b'])['c'].agg('count')
```

## 4. 第三方库并行库

- modin：对读取和常见的操作进行并行；
- swifter：对 apply 函数进行并行操作；在某些操作中，Modin 要快得多，通常是读取数据并查找值。其他操作，如执行统计计算，在 pandas 中要快得多。

## 5. 代码优化思路

在优化的过程中可以按照自己需求进行优化代码，写代码尽量避免循环，尽量写能够向量化计算的代码，尽量写多核计算的代码。

# Reference links

- https://pandas.pydata.org/pandas-docs/stable/user_guide/enhancingperf.html
