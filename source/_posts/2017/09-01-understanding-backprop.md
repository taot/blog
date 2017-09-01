---
title: 理解 Back-propagation
date: 2017-09-01 15:44:27
tags:
    - deeplearning
---

Back-propagation is a key procedure in deep learning. It's used to calculate the gradients in the layers, which are then used in optimization processes (gradient descent and other derived algorithms). So it's important to get a solid understanding on how back-propagation works. It really took me a long time to grasp the idea and finally derive the procedure.

What I'm writing here is mostly inspired by [Chapter 2 of the online book Neural Network and Deep Learning](http://neuralnetworksanddeeplearning.com/chap2.html). Honestly, I just read through Chapter 2, tried to understand it and then re-write the deriviation.

And my next step is to code back-propagation by hand, so that I could be more confident about it.

<!--more-->

# Symbols used in this post


$a^l_j$: Activation of neuron $j$ in layer $l$

$w^l_{jk}$: Weight of the connection between neuron $k$ in layer $l-1$ and neuron $j$ in layer $l$

$b^l_j$: Bias of neuron $j$ in layer $l$

$\sigma$: Activation function

Then we have the following form according to neural network definition:

$$
a^l_j = \sigma(\sum_k w^l_jk a^{l-1}_k + b^l_j)
$$

To make our deriviation easier, we define an intermediate variable $z^l_j$

$$
a^l_j = \sigma(\sum_k w^l_jk a^{l-1}_k + b^l_j)
z^l_j = \sum_k w^l_{jk} a^{l-1}_k + b^l_j
$$
