---
title: 统计 - 基础公式
date: 2016-10-27 22:30:26
tags:
    - math
    - statistics
---
## 统计

#### 总体方差

$$
\sigma^2 = \frac{\sum{(x_i - \mu)^2}}{N}
$$

#### 样本方差

$$
s^2 = \frac{\sum{(x_i - \overline{x})^2}}{n - 1}
$$

<!--more-->

> *可以证明，如果样本方差的离差平方和除以 $n-1$，而不是 $n$ 时，所得到的样本方差是总体方差的无偏估计。*

#### 总体/样本标准差

{% math %}
\begin{aligned}
\sigma & = \sqrt{\sigma^2} \\
s & = \sqrt{s^2}
\end{aligned}
{% endmath %}

#### 标准差系数

$$
(\frac{标准差}{平均数} \times 100) \%
$$

#### 样本偏度

$$
偏度 = \frac{n}{(n-1)(n-2)} \sum{(\frac{x_i - \overline{x}}{s})^3}
$$

> *当数据的偏度是正值时，通常平均数比中位数大；当数据的偏度是负值时，通常平均数比中位数小。*

#### $z$-分数

$$
z_i = \frac{x_i - \overline{x}}{s}
$$

#### 切比雪夫定理

与平均数的距离在 $z$ 个标准差之内的数据项所占比例至少为 $(1 - 1/z^2)$，其中 $z$ 是大于 1 的任意实数（不一定是整数）。

#### 经验法则

对于具有钟形分布的数据：

* 大约 68% 的数据值与平均数的距离在 1 个标准差之内。
* 大约 95% 的数据值与平均数的距离在 2 个标准差之内。
* 几乎所有的数据值与平均数的距离在 3 个标准差之内。

#### 五数概括法

1. 最小值
1. 第一四分位数（$Q_1$）
1. 中位数（$Q_2$）
1. 第三四分位数（$Q_3$）
1. 最大值

#### 总体协方差

$$
\sigma_{xy} = \frac {\sum{(x_i - \mu_x)(y_i - \mu_y)}} {N}
$$

#### 样本协方差

$$
s_{xy} = \frac {\sum{(x_i - \overline{x})(y_i - \overline{y})}} {n - 1}
$$

#### 皮尔逊积矩相关系数（总体/样本）

{% math %}
\begin{aligned}
\rho_{xy} & = \frac{\sigma_{xy}}{\sigma_x \sigma_y} \\
r_{xy} & = \frac{s_{xy}}{s_x s_y}
\end{aligned}
{% endmath %}
