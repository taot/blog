---
title: 抽样和抽样分布
date: 2016-11-08 21:20:03
tags:
    - math
    - statistics
---
#### $\overline{x}$ 的数学期望

$$
E(\overline{x}) = \mu
$$

> 式中，$E(\overline{x})$ 为 $\overline{x}$ 的数学期望；$\mu$ 为总体均值。

#### $\overline{x}$ 的标准差

有限总体

$$
\sigma_{\overline{x}} = \sqrt {\frac {N - n} {N - 1}} (\frac {\sigma} {\sqrt{n}})
$$

> $\sqrt {\frac {N - n} {N - 1}}$ 为 **有限总体修正系数**（finite population correction factor）

<!--more-->

无限总体

$$
\sigma_{\overline{x}} = \frac {\sigma} {\sqrt{n}}
$$

$$
\sigma_{\overline{x}}
$$

> 为强调 $\sigma_{\overline{x}}$ 与 $\sigma$ 的不同，我们称 $\overline{x}$ 的标准差为标准误差（standard error）。

#### 中心极限定理

从总体中抽取容量为 $n$ 的简单随机样本，当样本容量很大时，样本均值 $\overline{x}$ 的抽样分布近似服从正态概率分布。

在一般的统计实践中，对于大多数应用，假定当样本容量大于或等于 30 时，$\overline{x}$ 的抽样分布可用正态分布近似。

#### $\overline{p}$ 的抽样分布

样本比率 $\overline{p}$ 是总体比率 $p$ 的点估计。计算公式为：

$$
\overline{p} = \frac{x}{n}
$$

#### $\overline{p}$ 的数学期望

$$
E(\overline{p}) = p
$$

式中，$E(\overline{p})$ 为 $\overline{p}$ 的数学期望；$p$ 为总体比率。

#### $\overline{p}$ 的标准差

有限总体

$$
\sigma_{\overline{p}} = \sqrt{\frac{N-n}{N-1}} \sqrt{\frac{p(1-p)}{n}}
$$

无限总体

$$
\sigma_{\overline{p}} = \sqrt{\frac{p(1-p)}{n}}
$$

当 $np \geq 5$ 并且 $n(1-p) \geq 5$ 时，$\overline{p}$ 的抽样分布可以用正态分布近似。

#### 无偏性

样本统计量 $\hat{\theta}$ 是总体参数 $\theta$ 的无偏估计量，如果

$$
E(\hat{\theta}) = \theta
$$

式中，$E(\hat{\theta})$ 代表样本统计量 $\hat{\theta}$ 的数学期望。

在样本方差和样本标准差公式时，分母是 $n-1$ 而不是 $n$，正式为了使样本方差是总体方差的无偏估计量。

#### 有效性

如果一个样本给出了同一个总体参数的两个不同的无偏点估计量，我们称有较小标准误差的点估计量比其他点估计量更**相对有效**（relative efficiency）。

#### 一致性

如果随着样本容量的增大，点估计量的值与总体参数越来越接近，换句话说，大样本情形下比小样本情形下更易于得到一个好的点估计。
