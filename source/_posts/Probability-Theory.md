---
title: Probability Theory
date: 2018-04-02 09:08:14
tags:
---

## 概率和统计
概率和统计看似两个相近的概念，其实研究的问题刚好相反。
- 概率是已知模型和参数，推数据。
- 统计时已知数据，推模型和参数

## 最大似然估计和最大后验概率估计
最大似然估计（Maximum likelihood estimation, 简称MLE）和最大后验概率估计（Maximum a posteriori estimation, 简称MAP）是很常用的两种参数估计方法。
对于这个函数：P(x|θ)。输入有两个：x表示某一个具体的数据；θ表示模型的参数。
- 如果θ是已知确定的，x是变量，这个函数叫做概率函数(probability function)，它描述对于不同的样本点x，其出现概率是多少。
- 如果x是已知确定的，θ是变量，这个函数叫做似然函数(likelihood function), 它描述对于不同的模型参数，出现x这个样本点的概率是多少。

似然函数只是一个关于θ的函数，最大似然估计是求参数θ, 使似然函数P(x0|θ)最大。
最大后验概率估计则是想求θ使P(x0|θ)P(θ)最大，根据贝叶斯全概率公式，其实是在最大化P(θ|x0)=P(x0|θ)P(θ)/P(x0)，P(x0)是一个定值


## 离散随机变量分布
- (0-1)分布: P{X=k}=p^k * (1-p)^k, k=0,1 (0<p<1)
- 伯努利实验指实验E只有两个可能结果：A和~A
- 二项分布b(n,p): P{X=k}=Cnk * p^k * (1-p)^k, k=0,1 (0<p<1)
- 泊松分布π(λ): P{X=k}=λ^k * e^{-k} / k! (当n趋近于无穷大时，二项分布为泊松分布)


## 随机变量的分布函数
对于非离散型随机变量X，由于其可能的值不能一一列举出来，因而不能像离散型随机变量那样用分布律描述它。针对这种变量，我们往往是去研究其落在某个区间(x1, x2]的概率: P{x1<X<=x2}=P{X<=x2} - P{X<=x1}，即X的分布函数F(x)=P{X<=x}，分布函数F(x)在x处的函数值就表示x落在区间(-∞,x)上的概率


## 连续型随机变量及其概率密度
- 均匀分布X~U(a, b) => 概率密度: f(x) = 1 / (b - a) when a<x<b, otherwise f(x) = 0
- 指数分布X~Θ       => 概率密度: f(x) = 1 / Θ * e^{-x/Θ} when x > 0, otherwise f(x) = 0
- 正态分布X~(µ,σ^2) => 概率密度: f(x) = 1 / √2πσ e^{-(x-µ)^2 / 2σ^2}
    * 1σ: 68.26%
    * 2σ: 95.44%
    * 3σ: 99.74%
- χ^2分布χ^2~χ^2(n)=> 概率密度: f(y) = 1/2^{n/2}Γ(n/2) * y^{n/2-1}e^{-y/2} when y > 0, otherwise f(y) = 0
    * n = 1时，y = x^2, x为标准正态分布
- t分布(学生氏分布): X~N(0,1), Y~χ^2(n)，且X，Y相互独立，t=X/√Y/n
- F分布: U~χ^2(n1), V~χ^2(n2), U,V相互独立，F=U/n1/ V/n2
