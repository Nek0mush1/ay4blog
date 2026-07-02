---
title: "02 逻辑回归：用 sigmoid 把线性输出转成分类概率"
description: ""
date: "2026-06-30"
draft: false
showHeroImage: false
tags: ["机器学习"]
categories: []
series: ["机器学习笔记"]
comments: true
sidebar:
  enable: true
  toc: true
  relatedPosts: true
---

# 02 逻辑回归：用 sigmoid 把线性输出转成分类概率

这份笔记根据 `logicRegression` 文件夹里的截图整理，主要讲两件事：逻辑回归怎样用 sigmoid 函数输出概率，以及训练逻辑回归时为什么使用 Log Loss 和正则化。

## 1. 逻辑回归输出的是什么

很多问题需要模型输出一个概率。比如垃圾邮件预测模型输入一封邮件后，输出 `0.932`，可以直接理解为这封邮件有 `93.2%` 的概率是垃圾邮件。

逻辑回归的输出有两种常见用法：

1. 直接把输出当作概率使用。
2. 把概率再转换成二分类结果，比如 `True` 和 `False`，或者 `Spam` 和 `Not Spam`。

截图里的内容主要关注第一种用法，也就是如何得到一个介于 0 和 1 之间的概率值。

## 2. sigmoid 函数

逻辑回归需要保证输出像概率一样落在 0 到 1 之间。为此，它会使用 logistic function，其中最常见的是 sigmoid 函数：

<div class="math-display">
  <span>f(x) = </span>
  <span class="fraction">
    <span class="fraction-top">1</span>
    <span class="fraction-bottom">1 + e<sup>-x</sup></span>
  </span>
</div>

这里的符号含义是：

- `f(x)` 是 sigmoid 函数的输出。
- `e` 是欧拉数，约等于 `2.71828`。
- `x` 是 sigmoid 函数的输入。

![sigmoid 函数曲线](/postImages/01_sigmoid_curve.png)

图 1：sigmoid 函数图像。随着 `x` 不断减小并趋于负无穷，曲线接近 0；随着 `x` 不断增大并趋于正无穷，曲线接近 1。

这条曲线的形状说明了一个关键点：输入可以是任意实数，但输出会被压到 0 和 1 之间。输入越大，输出越接近 1；输入越小，输出越接近 0。

## 3. 从线性输出到概率

逻辑回归先计算一个线性部分：

<div class="math-display">
  z = b + w<sub>1</sub>x<sub>1</sub> + w<sub>2</sub>x<sub>2</sub> + ... + w<sub>N</sub>x<sub>N</sub>
</div>

这里：

- `z` 是线性方程的输出，也叫 log odds。
- `b` 是偏置项。
- `w` 是模型通过训练学到的权重。
- `x` 是某个样本的特征值。

得到 `z` 之后，再把它输入 sigmoid 函数：

<div class="math-display">
  <span>y' = </span>
  <span class="fraction">
    <span class="fraction-top">1</span>
    <span class="fraction-bottom">1 + e<sup>-z</sup></span>
  </span>
</div>

这里：

- `y'` 是逻辑回归模型的输出。
- `e` 是欧拉数，约等于 `2.71828`。
- `z` 是前面线性方程算出来的结果。

![线性输出转换为 sigmoid 输出](/postImages/02_linear_to_sigmoid.png)

图 2：左图是线性函数 `z=2x+5`，右图是同一批点经过 sigmoid 函数转换后的结果。线性函数的输出可以很大或很小，但 sigmoid 会把它们映射到 0 和 1 之间。

截图里给了一个例子：左图中黄色方块对应的 `z` 值是 `-10`，经过 sigmoid 后，右图中的 `y'` 约为 `0.00004`。这说明当 `z` 很小的时候，模型输出会非常接近 0。

## 4. 为什么不用平方损失

线性回归常用 squared loss，也叫 `L2` loss。它适合线性模型，因为线性模型输出的变化率是恒定的。例如：

<div class="math-display">
  y' = b + 3x<sub>1</sub>
</div>

当输入特征每增加 1，预测值就增加 3。

逻辑回归不一样。它经过 sigmoid 函数后，输出变化率不是恒定的。`z` 靠近 0 时，`z` 的小幅变化会带来比较明显的概率变化；当 `z` 已经很大或很小时，输出会贴近 1 或 0，此时变化会变得很小。

截图中的表格展示了当输入从 5 增加到 10 时，sigmoid 输出越来越接近 1：

| input | logistic output | required digits of precision |
|---:|---:|---:|
| 5 | 0.993 | 3 |
| 6 | 0.997 | 3 |
| 7 | 0.999 | 3 |
| 8 | 0.9997 | 4 |
| 9 | 0.9999 | 4 |
| 10 | 0.99998 | 5 |

如果用平方损失来计算 sigmoid 输出的误差，当输出越来越接近 0 或 1 时，就需要更多精度来跟踪这些很小的差别。这也是逻辑回归不用平方损失的原因之一。

## 5. Log Loss

逻辑回归使用 Log Loss 作为损失函数。它关注的不是预测值和真实值之间的普通距离，而是通过对数形式衡量预测错误的代价。

截图中的 Log Loss 公式是：

<div class="math-display">
  <span>Log Loss = -</span>
  <span class="fraction">
    <span class="fraction-top">1</span>
    <span class="fraction-bottom">N</span>
  </span>
  <span> sum from i = 1 to N [y<sub>i</sub> log(y'<sub>i</sub>) + (1 - y<sub>i</sub>) log(1 - y'<sub>i</sub>)]</span>
</div>

符号含义如下：

- `N` 是数据集中有标签样本的数量。
- `i` 是样本索引，例如 `(x_3,y_3)` 表示第 3 个样本。
- `y_i` 是第 `i` 个样本的真实标签。在逻辑回归中，`y_i` 只能是 0 或 1。
- `y'_i` 是模型对第 `i` 个样本的预测结果，取值在 0 到 1 之间。
- `x_i` 是第 `i` 个样本的特征集合。

这个公式可以分两种情况理解：

当真实标签 `y_i=1` 时，公式中主要看 `log(y'_i)`。如果模型把正样本预测成接近 1，损失较小；如果预测成接近 0，损失会很大。

当真实标签 `y_i=0` 时，公式中主要看 `log(1-y'_i)`。如果模型把负样本预测成接近 0，损失较小；如果预测成接近 1，损失会很大。

所以 Log Loss 会惩罚“很自信但预测错”的情况。

## 6. 正则化

逻辑回归训练过程和线性回归相似，但截图中强调了两个区别：

1. 逻辑回归使用 Log Loss，而不是 squared loss。
2. 逻辑回归需要正则化来防止过拟合。

正则化的作用是在训练时惩罚模型复杂度。截图里提到，如果没有正则化，并且模型有大量特征，逻辑回归的渐近特性可能会持续把损失推向 0。这样模型容易把训练数据拟合得过细。

截图中列了两种降低模型复杂度的策略：

- `L2` regularization。
- Early stopping，也就是限制训练步数，在损失仍然下降时提前停止训练。

## 7. 复习要点

逻辑回归不是直接输出类别，而是先输出一个概率。它先用线性方程算出 `z`，再用 sigmoid 函数把 `z` 映射到 0 和 1 之间。

sigmoid 曲线不是线性的。`z` 靠近 0 时，输出变化比较明显；`z` 很大或很小时，输出会接近 1 或 0。

训练逻辑回归时，损失函数使用 Log Loss。这个损失函数会根据真实标签是 0 还是 1，分别惩罚错误的概率预测。

正则化用来限制模型复杂度。常见做法包括 `L2` regularization 和 early stopping。
