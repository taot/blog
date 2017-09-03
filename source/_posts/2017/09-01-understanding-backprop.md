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

We have the following formula according to the definition of neural network:

{% math %}
a^l_j = \sigma(\sum_k w^l_{jk} a^{l-1}_k + b^l_j)
{% endmath %}

And to make our deriviation easier, define an intermediate variable $z^l_j$ to be the sum of weights and bias:

{% math %}
\begin{aligned}
z^l_j = \sum_k w^l_{jk} a^{l-1}_k + b^l_j
\end{aligned}
{% endmath %}

so

$$
a^l_j = \sigma z^l_j
$$

And we use minimum squared error as the loss function (the loss function needs to satisfy two assumptions, which we just ignore here.):

$$
C = \dfrac{1}{2} \lVert \mathbf{y} - a^L(\mathbf{x}) \rVert^2
$$

Then we define the error of a layer, and it's kind of the combination of how the weights and biases should change in a training pass. And the error is used in equations BP3 and BP4.

$$
\sigma^l_j = \dfrac{\partial C}{\partial z^l_j}
$$

# Back-propagation equations

Here I just write down the four equations for backprob here, and later on I will present the proof of the formula.

{% math %}
\delta^L_j = \dfrac{\partial C}{\partial a^L_j} \sigma'(z^L_j) \label{BP1}\tag{BP1}
{% endmath %}

{% math %}
\mathbf{\delta}^L = \bigtriangledown_a C \cdot \sigma'(\mathbf{z}^L) \label{BP1a}\tag{BP1a}
{% endmath %}

{% math %}
\delta^l_j = \dfrac{\partial C}{\partial z^l_j} = (\sum_k \delta^{l+1}_k w^{l+1}_{kj}) \cdot \sigma' (z^l_j) \tag{BP2}
{% endmath %}

{% math %}
\mathbf{\delta}^l = [ (\mathbf{W}^{l+1})^T \mathbf{\delta}^{l+1} ] \odot \sigma' (\mathbf{z}^l) \tag{BP2a}
{% endmath %}

{% math %}
\dfrac{\partial C}{\partial b^l_j} = \dfrac{\partial C}{\partial z^l_j} = \delta^L_j \tag{BP3}
{% endmath %}

{% math %}
\dfrac{\partial C}{\partial w^l_{jk}} = a^{l-1}_k \delta^l_j \tag{BP4}
{% endmath %}

# Deriviation of the equations

## BP1 and BP1a

Equations BP1 and BP1a calculate the error of the output layer. By using chain-rule of partial deriviation, we get:

{% math %}
\delta^L_j = \sum_k \dfrac{\partial C}{\partial a^L_k} \dfrac{\partial a^L_k}{\partial z^L_j} = \dfrac{\partial C}{\partial a^L_j} \sigma' (z^L_j)
{% endmath %}

BP1a is just the matrix form of BP1, but it gives us a more gloabal view:

{% math %}
\mathbf{\delta}^L = \dfrac{\partial C}{\partial \mathbf{z}} = \bigtriangledown_\mathbf{a} C \cdot \sigma' (\mathbf{z}^L)
{% endmath %}

## BP2 and BP2a

Then the error for hidden layers. Here we calculate the partial deriviation of layer $l$ against that of layer $l+1$, since we need to back-propagate the error from higher layer to lower layer.

{% math %}
\delta^l_j = \dfrac{\partial C}{\partial z^l_j} = \sum_k \dfrac{\partial C}{\partial z^{l+1}_k} \cdot \dfrac{\partial z^{l+1}_k}{\partial z^l_j} = \sum_k \delta^{l+1}_k \cdot \dfrac{\partial z^{l+1}_k}{\partial z^l_j}
{% endmath %}

because

{% math %}
z^{l+1}_k = \sum_m w^{l+1}_{km} \cdot \sigma (z^l_m) + b^{l+1}_k
{% endmath %}

and it's partial deriviation:

{% math %}
\dfrac{\partial z^{l+1}_k}{\partial z^l_j} = w^{l+1}_{kj} \cdot \sigma' (z^l_j)
{% endmath %}

so we get:

{% math %}
\delta^l_j = \dfrac{\partial C}{\partial z^l_j} = (\sum_k \delta^{l+1}_k w^{l+1}_{kj}) \cdot \sigma' (z^l_j)
{% endmath %}

Write it in matrix form:

{% math %}
\mathbf{\delta}^l = [ (\mathbf{W}^{l+1})^T \mathbf{\delta}^{l+1} ] \odot \sigma' (\mathbf{z}^l)
{% endmath %}

## BP3

{% math %}
\dfrac{\partial C}{\partial b^l_j} = \sum_k \dfrac{\partial C}{\partial z^l_k} \cdot \dfrac{\partial z^l_k}{\partial b^l_j} = \dfrac{\partial C}{\partial z^l_j} = \sigma^l_j
{% endmath %}

It's straight forward because

{% math %}
z^l_m = \sum_n w^l_{mn} a^{l-1}_n + b^l_m
{% endmath %}

And the part $\dfrac{\partial z^l_k}{\partial b^l_j} = \dfrac{\partial C}{\partial z^l_j}$ is 1 only when $k = j$, otherwise it's 0.

## BP4

Similarly,

{% math %}
\dfrac{\partial C}{\partial w^l_{jk}} = \sum_m \dfrac{\partial C}{\partial z^l_m} \cdot \dfrac{\partial z^l_m}{\partial w^l_{jk}}
{% endmath %}

because

{% math %}
z^l_m = \sum_n w^l_{mn} a^{l-1}_n + b^l_m
{% endmath %}

and when and only when m = j, n = k, the derivative is not 0, so here we get BP4:

{% math %}
\dfrac{\partial C}{\partial w^l_{jk}} = \dfrac{\partial C}{\partial z^l_j} \cdot a^{l-1}_k = \sigma^l_j a^{l-1}_k
{% endmath %}
