---
title: 连续型概率分布
date: 2016-11-06 11:06:09
tags:
    - math
    - probability
---
#### 均匀概率分布

{% math %}
\begin{aligned}
f(x) & = \frac {1} {b - a}, a \leq x \leq b \\
f(x) & = 0, 其他
\end{aligned}
{% endmath %}

#### 连续型均匀概率分布的数学期望和方差

{% math %}
\begin{aligned}
E(x) & = \frac {a + b} {2} \\
Var(x) & = \frac {(b - a)^2} {12}
\end{aligned}
{% endmath %}

<!--more-->

#### 正态概率密度函数

$$
f(x) = \frac{1}{\sqrt{2\pi}\sigma} e^{-(x-\mu)^2 / 2\sigma^2}
$$

> 式中，$\mu$ 为均值；$\sigma$ 为标准差；$\pi$ 为 3.14159；$e$ 为 2.71828

#### 标准正态概率分布

$$
f(z) = \frac{1}{\sqrt{2\pi}} e^{-z^2/2}
$$

#### 转换为标准正态随机变量

$$
z = \frac{x - \mu}{\sigma}
$$

#### 二项概率的正态近似

二项分布中随机变量是 $n$ 次试验中成功的次数，在 $n$ 次试验中有 $x$ 次成功的概率是我们关心的概率问题。在 $np \geq 5$ 和 $n(1-p) \geq 5$ 的情况下，正态分布式对二项概率分布的一个简便易行的近似。当使用正态分布近似二项分布时，正态曲线中取 $\mu = np$ 和 $\sigma = \sqrt{np(1-p)}$。

#### 指数概率分布

排队论中，指数分布常用语描述服务时间。指数分布的性质之一是，分布的均值和标准差相等。

$$
f(x) = \frac{1}{\mu} e^{-x/\mu}, x \geq 0
$$

> 式中，$\mu$ 为期望值或均值

#### 指数分布：累积概率

$$
P(x \leq x_0) = 1 - e^{-x_0/\mu}
$$

#### 泊松分布与指数分布的关系

连续型指数概率分布与离散型泊松分布时相互联系的，泊松分布描述了每一区间中事件发生的次数，指数分布描述了事件发生的时间间隔长度。