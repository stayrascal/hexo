---
title: Spring Data JPA Tutorial
date: 2016-04-25 10:35:08
tags: Spring Data JPA
description: Spring Data JPA (待完成)
---

使用Java Persistence API创建仓库是一个很笨重的过程，不仅需要花费大量的时间而且还产生了大量的重复代码。我们可以通过如下几个步骤消除重复的代码：

* 创建一个抽象的基本仓库，提供了基本的CRUD操作
* 创建具体的仓库去继承上述基本仓库

但是还有一个问题是我们仍然需要编写一些关于数据库查询的方法并调用它们，更糟糕的是，我们不得不做同样的事情当我们创建一个新的数据库查询的时候。这简直就是在浪费时间，幸好Spring Data JPA可以解决这个问题。
