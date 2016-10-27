---
title: 概率论基础公式
date: 2016-10-27 23:46:24
tags:
    - math
    - probability
---
#### 组合

$$
C_n^N = \binom{N}{n} = \frac {N!} {n!(N - n)!}
$$

#### 排列

$$
P_n^N = n! \binom{N}{n} = \frac {N!} {(N - n)!}
$$

<!--more-->

#### 事件的补

$$
P(A) = 1 - P(A^c)
$$

#### 加法公式

$$
P(A \cup B) = P(A) + P(B) - P(A \cap B)
$$

#### 互斥事件的加法公式

$$
P(A \cup B) = P(A) + P(B)
$$

#### 条件概率

$$
P(A|B) = \frac {P(A \cap B)} {P(B)}
$$

#### 独立事件

$$
P(A|B) = P(A)
$$

#### 乘法公式

$$
P(A \cap B) = P(B)P(A|B)
$$

#### 独立事件的乘法公式

$$
P(A \cap B) = P(A)P(B)
$$

#### 贝叶斯定理

对于 $n$ 个互斥时间 $A_1, A_2, \cdots, A_n$

$$
P(A_i | B) = \frac {P(A_i)P(B)} {P(A_1)P(B|A_1) + P(A_2)P(B|A_2) + \cdots + P(A_n)P(B|A_n)}
$$
