---
title: 深度学习编程风格 (翻译)
date: 2017-09-19 14:23:30
tags:
    - mxnet
    - deeplearning
---

翻译自 https://mxnet.incubator.apache.org/architecture/program_model.html

不论最终我们多么关心性能，首先我们要做的是让代码跑起来，然后才能开始关心如何优化它。要写出整洁易读的深度学习代码有挑战性，任何人首先要关心的就是编程的语法。麻烦的是，有那么多深度学习库，每种都有它们自己的编程风格。

这篇文章中，我们的讨论集中在两个最重要的上层 (high-level) 的设计方案：

  1. 对数学计算，是支持符号还是命令式。
  1. 是适用更大更抽象的运算来构建神经网络，还是用更原子性的操作。

我们的讨论将集中在编程模型本身。当编程风格对性能有影响时，我们会指出来，但是不会深入讨论实现上的细节。

<!--more-->

## 符号式编程和命令式编程

如果你是一个 Python 或 C++ 程序员，你对命令式编程应该很熟悉。命令式程序在你运行代码的时候执行计算。你写的大多数 Python 程序都是命令式的，比如以下 NumPy 代码：

```python
import numpy as np
a = np.ones(10)
b = np.ones(10) * 2
c = b * a
d = c + 1
```

当程序执行 c = b * a 时，它就在执行数值计算。

而符号式编程就不一样了。在符号式的程序中，我们首先定义一个（有可能很复杂的）函数。当定义这个函数时，实际并没有执行数值计算。我们用占位符代替实际的数值来定义这个函数。然后我们编译它，并且以实际的输入来运行它。在以下例子中，我们用符号式编程来重写上面的命令式程序：

```python
A = Variable('A')
B = Variable('B')
C = B * A
D = C + Constant(1)
# compiles the function
f = compile(D)
d = f(A=np.ones(10), B=np.ones(10)*2)
```

你可以看到，在符号式的程序中，当 C = B * A 这句被执行时，并没有进行计算，而是生成了一个计算图 (computation graph)，也叫做符号图 (symbolic graph) 来表示这个计算。下图展示了一个用来计算 D 的计算图。

![computation graph](comp_graph.png)

多数符号式的程序有显示或者隐式的编译的过程。它把计算图转换成可以调用的函数。在上面的例子中，数值计算只在最后一行代码中发生。符号式程序最明显的特征就是它分离了计算图的构建和执行。对于神经网络，我们一般把整个模型定义为一个计算图。

在流行的深度学习框架中，Torch, Chainer 和 Minerva 使用命令式风格。符号式的深度学习框架包括 Theano, CGT 和 TensorFlow。我们也可以把基于配置文件的库，如 CXXNet 和 Caffe 也看成符号式，这时我们将配置文件看成是符号图的定义。

现在你了解了这两种编程模型的不同点，让我们比较一下两者各自的优势。

### 命令式编程更加灵活

当你在 Python 中使用命令式的库时，你就是在写 Python 程序。几乎任何你用 Python 凭直觉可就完成的事，你都可以调用命令式深度学习库中相应的函数来加速。另一方面，当你在写符号式程序是，你无法使用 Python 中有些你熟悉的用法，比如迭代。考虑如下命令式程序，想想你该如何把它翻译成符号式程序：

```python
a = 2
b = a + 1
d = np.zeros(10)
for i in range(d):
    d += np.zeros(10)
```

如果符号式 API 中不支持 for 循环，那么这段代码就没那么容易翻译成符号式的了。你需要使用符号式 API 的 DSL (domain-specific language) 来写程序。深度学习库中的符号式 API 是功能强大的 DSL，可以用来生成神经网络的计算图L。

直觉上，你可能会说命令式编程比符号式更加自然。使用命令式编程能更容易地使用语言的原生特性，例如，可以很方便地打印出计算过程的中间值，或是在计算流程中的任何地方使用原生的控制流和循环。

### 符号式程序效率更高

如我们所见，命令式编程更加灵活，而且和语言本身的控制流程更加匹配。那么我们为什么还需要符号式编程呢？主要的原因是效率，包括内存上的和速度上的。让我们再看一下之前的小例子。

```python
import numpy as np
a = np.ones(10)
b = np.ones(10) * 2
c = b * a
d = c + 1
...
```

![computation graph](comp_graph.png)

假设数组中的每个元素占 8 字节内存，要在 Python 中运行这个程序，需要多少内存？

命令式程序中，我们需要在每一行中分配内存。这样我们分配了 4 个大小为 10 的数组，所以我们需要 4 * 10 * 8 = 320 个字节。另一方面，如果我们构建一个计算图，并且事先知道我们需要的最终结果只有 d，那么我们可以重用那些分配给中间结果的内存。例如，通过在原地进行计算，我们可以重用 b 的空间来存储 c。并且我们可以重用 c 的空间来存储 d。最终我们可以把内存使用量减半，只需要 2 * 10 * 8 = 160 字节。

符号式程序受到更多限制。当我们编译 D 时，我们告诉系统只有 d 的值是需要的。计算的中间结果，在这里是 c，对我们是不可见的。

在符号式程序可以安全地重用内存来进行原地的计算，这对我们很有利。但是另一方面，如果我们之后需要访问 c，就不行了。所以命令式程序能更好地满足所有可能的需求。如果我们在 Python 命令行中执行命令式的程序，那么我们可以查看任何的中间变量。

符号式程序也可以做另外一种优化，叫做运算折叠（operation folding）。回到我们的小例子，其中的乘法操作和加法操作可以被合并到一个操作，如下图所示。如果我们用 GPU 来计算，则仅需要一个 GPU 核心，而不是两个。实际上，在优化过的库中，如 CXXNet 和 Caffe，我们就是这样手动来优化运算的。运算折叠能够优化计算效率。

![computation graph folding](comp_graph_fold.png)

注意，在命令式程序中，你不能做运算折叠，因为中间值可能以后会被用到。在符号图中，运算折叠是可行的，因为你有整个计算图，和清晰的定义来说明哪些中间结果是需要的，哪些是不需要的。

### 案例研究：Backprop 和自动微分

这一节中，我们在自动微分和反向传播中这比较两种编程模型。微分在深度学习中非常重要，因为它是我们训练模型的机制。在任何深度学习模型中，我们都要定义一个损失函数（loss function）。损失函数告诉我们当前模型的输出和我们想要的输出的差距有多大。我们把训练集中的数据（输入和期望的输出）送入神经网络进行训练，并且更新网络参数，来最小化损失函数。为了决定要向哪个方向更新参数，我们需要求出损失函数相对于各个参数的导数。

过去，当人们定义一个新的模型时，他们需要手动写出导数的函数。虽然这是个直接的计算，但是对于复杂的模型，这需要消耗大量时间并且是个烦人的工作。所有现代的深度学习框架都提供自动微分的功能，大大简化了工程师和研究人员的工作。

命令式编程和符号式编程都可以做自动微分，让我们来看一下它们分别是怎么做的。

让我们从命令式程序开始。如下 Python 代码给我们之前的小例子做了个自动微分：

```python
class array(object) :
    """Simple Array object that support autodiff."""
    def __init__(self, value, name=None):
        self.value = value
        if name:
            self.grad = lambda g : {name : g}

    def __add__(self, other):
        assert isinstance(other, int)
        ret = array(self.value + other)
        ret.grad = lambda g : self.grad(g)
        return ret

    def __mul__(self, other):
        assert isinstance(other, array)
        ret = array(self.value * other.value)
        def grad(g):
            x = self.grad(g * other.value)
            x.update(other.grad(g * self.value))
            return x
        ret.grad = grad
        return ret

# some examples
a = array(1, 'a')
b = array(2, 'b')
c = b * a
d = c + 1
print d.value
print d.grad(1)
# Results
# 3
# {'a': 2, 'b': 1}
```

在这段代码中，每个 array 对象包含一个 grad 函数（实际上是闭包）。当然你执行 d.grad 时，它递归地调用了 grad 函数，把梯度反向传播，并返回每个输入的梯度。

这看起来可能有点复杂，让我们考虑符号式编程下的梯度计算，如以下代码所示：

```python
A = Variable('A')
B = Variable('B')
C = B * A
D = C + Constant(1)
# get gradient node.
gA, gB = D.grad(wrt=[A, B])
# compiles the gradient function.
f = compile([gA, gB])
grad_a, grad_b = f(A=np.ones(10), B=np.ones(10)*2)
```

D 的梯度函数生成了一个反向计算图，并且返回梯度节点，gA 和 gB，对应于下图中的红色节点。

![computation graph backward](./comp_graph_backward.png)

前面命令式程序和符号式程序做的事情实际上是一样的。它将反向计算图隐式地保存在闭包中。当你调用 d.grad，你就从 d(D) 开始，反向遍历这个图，计算出梯度，并把它们收集起啦。

符号式和命令式的编程中，梯度的计算都遵循同样的模式，那么它们的不同点是什么？回想命令式编程的需求：准备好应对所有可能的需求。如果你创造一个支持自动微分的库，你必须在计算过程中保留梯度的闭包。这意味着所有历史变量都不能被垃圾回收，因为他们都在函数闭包中被变量 d 引用。

如果你只需要计算 d 的值而不需要梯度值，怎么办？在符号式程序中，你可以使用 f = compiled([D])。这也设定了计算的边界，告诉系统你只想计算前向路径。系统就可以释放之前计算所占用的内存空间，并且在输入和输出之间共享内存。

想象你在跑一个 n 层的深度神经网络。如果你只跑前向计算，而不需要反向传播，你只需要为中间层分配两份临时空间，而不是 n 份。然而，因为命令式编程需要应对所有可能的需求，它就需要保存中间结果，需要 n 份临时空间。

可以看到，你能做的优化取决于你受到了多少限制。符号式编程要求你在编译计算图时清晰地指定这些限制。而命令式编程必须应对更多的需求。在这方面，符号式编程有很自然的优势，因为它知道你想要什么不想要什么。

你可以按某些方法修改你的命令式程序来加入类似的限制。例如，一个解决方法是，引入一个上下文变量。你可以引入一个无梯度上下文变量 (no-gradient context variable) 来禁用梯度计算。

```python
with context.NoGradient():
    a = array(1, 'a')
    b = array(2, 'b')
    c = b * a
    d = c + 1
```

然而，这个例子仍然需要准备应对所有需求，意味着你不能在前向路径做原地计算来重用内存（这是个减少 GPU 内存使用的常用的技巧）。我们讨论的技巧显式地生成了一个反向路径。有些库，比如 Caffe 和 CXXNet，会在同一个图上隐式地执行反向计算。我们讨论的方法对它们也适用。

多数基于配置文件的库，例如 CXXNet 和 Caffe，它们被设计为满足一到两个通用需求：获得每一层的激活值，或者获得所有权重的梯度值。这些库都有同样的问题：在相同的数据结构的基础上，想要支持越多的通用操作，能做的优化（内存空间共享）就越少。

如你所见，多数情况下，限制和灵活性的权衡都是这样的。

### 模型检查点 (Model Checkpoint)

能够保存一个模型，之后再加载回来，这是很重要的。有很多不同的方法来保存你的工作。通常，要保存一个神经网络，你需要保存两样东西：网络结构配置，和神经网络的权重。

能够保存网络配置是符号式程序的一个优点。因为符号创建的过程不执行计算，你可以直接将计算图序列化，之后再加载。这样在不需要引入额外的层的情况下，就可以解决保存配置的问题。

```python
A = Variable('A')
B = Variable('B')
C = B * A
D = C + Constant(1)
D.save('mygraph')
...
D2 = load('mygraph')
f = compile([D2])
# more operations
...
```

因为命令式程序中，描述计算过程就是执行它，你需要保存代码本身作为配置，或在命令式语言之上再构建一个配置层。

### 参数更新 (Parameter Updates)

多数符号式程序是数据流（计算）图。数据流图描述计算。但是如何用图来描述参数的更新，这无法在图中清晰地表达。这是因为参数的更新涉及到变化，这并不是一个数据流的概念。多数符号式编程引入了特殊的更新指令，来更新程序的持久化状态。

通常用命令式的方式来写参数的更新，会更简单一些，特别是你需要多个互相相关的更新。在符号式程序中，更新语句在你调用它的时候执行。所以多数符号式深度学习库使用命令式的方式来执行更新操作，而使用符号式的方式来的进行计算。

### 两种编程模式时间没有严格的边界

在比较两种编程模式时，我们的某些论点并不总是成立的，例如，你可以将命令式程序写成很像传统的符号式程序，或者反过来讲符号式程序写成命令式的样子。然而，这是两种很有用的抽象，特别是为了理解各个的深度学习库之间的不同。我们可以认为，在两种编程风格之间没有清晰的界限。例如，你可以做一个即时编译器（just-in-time compiler, JIT）来编译 Python 程序，这样可以有某些符号式程序中全局信息的优点。

## 大运算和小运算 (Big vs. Small Operations)

设计一个深度学习库时，另一个重要的需要考虑的设计是要支持什么运算。通常来说，多数深度学习库会支持两大类运算：

  * 大运算（Big operations）- 神经网络中层的计算（例如，FullyConnected 和 BatchNormalize）
  * 小运算（Small operations）- 数学函数，例如矩阵乘法和元素加法。

CXXNet 和 Caffe 这样的库支持层级（layer-level）的运算。Theano 和 Minerva 支持细粒度的运算。

### 小运算更加灵活

很自然地，我们可以用小的运算来组成大的运算。比如，sigmoid 单元可以用除法，加法和幂运算来组成：

```python
sigmoid(x) = 1.0 / (1.0 + exp(-x))
```

使用更小的运算来作为基础组件，你可以表达任何你想表达的运算。如果你更熟悉 CXXNet 或 Caffe 风格的层，注意这些运算和层没有什么不同，只是它们更小。

```python
SigmoidLayer(x) = EWiseDivisionLayer(1.0, AddScalarLayer(ExpLayer(-x), 1.0))
```

这个表达式由三个层组成，每个都定义它的了前向和后向（梯度）函数。使用更小的运算，你可以很快地构建你新定义的层，因为你只需要把这些小运算组合起来。

### 大运算效率更高

用小运算组成 sigmoid 层需要三层，而不是一层。

```python
SigmoidLayer(x) = EWiseDivisionLayer(1.0, AddScalarLayer(ExpLayer(-x), 1.0))
```

这带来了计算内存更多的开销（可以优化，但是有些代价）。

CXXNet 和 Caffe 这类的库用了另一种方式。为了直接支持粗粒度的运算，像 BatchNormalization 和 SigmoidLayer，这些计算是直接用 CUDA 实现的。这使得它们的效率更高。

### 编译和优化

小运算能被优化吗？当然可以。让我们看一下编译引擎的优化部分。两种类型的优化可以在计算图上进行：

  * 内存优化，重用中间计算的内存。
  * 运算合并，匹配子图的模式，比如 sigmoid，然后把它们合并到更大的运算中。

内存优化不仅能用在小运算组成的计算图中，也能被用于大运算组成的计算图。然而，使用大运算的库如 CXXNet 和 Caffe 不一定会优化内存，因为你很难找到编译阶段。然而，它们有类似编译的阶段，将各层的定义翻译成固定的前向传播和后向传播的执行计划。

对于由小运算构成的计算图，这些优化对性能非常重要。因为运算很小，很多子图模式可以被匹配。而且，因为最终生成的运算可能无法枚举，需要重新编译计算核，而基于大运算的库则只需要固定数量的预编译的计算核。这就给支持小运算的符号式的库，带来了更多的编译上的开销。需要编译期优化，这一点也给仅支持小运算的库，带来了工程上更多的开销。

基于大运算的库实际上在 “作弊”，因为它要求你来提供约束，需要你来进行子图的匹配。这把编译的开销转嫁给了人类的大脑，不过通常没什么问题。

### 表达式模板和静态类型语言

你总是需要小运算，并且把它们组合起来。某些库，比如 Caffe，使用手写的计算核来构造更大的操作。或者，你需要使用 Python 来组合小运算。

你还有第三种选择，表达式模板。简单地说，在编译时，使用模板编程来从表达式树生成通用的计算核。其中的细节可以看 [Expression Template Tutorial](https://github.com/dmlc/mshadow/blob/master/guide/exp-template/README.md)。CXXNet 的很多地方使用了表达式模板，使得你可以创建性能相当于手写计算核的操作，而且代码更短且更易读。

使用表达式模板和使用 Python 计算核生成的不同点在于，C++ 的表达式模板的生成是在编译期对已经存在的类型做的，所以运行期没有更多的开销。原则上，其他静态类型的支持模板的语言也可以实现，但是我们还没有在 C++ 之外的语言中见到。

支持表达式模板的库通过允许 C++ 用户用小运算组成高性能的大运算，在 Python 运算和手写计算核之间建立了一个中间地带。这是个值得考虑的选项。

## 混合使用各种方式

我们已经比较了不同的编程模式，应该选哪个呢？在我们深入讨论之前，需要强调，我们的比较不一定对你想解决的问题有大的影响。

记住[阿姆达尔定律](https://en.wikipedia.org/wiki/Amdahl%27s_law)：如果你优化的部分不是系统中的性能关键点，那么你无法获得很大的性能提升。

通常，你需要在效率，灵活性和工程复杂度上做取舍。你需要根据你想解决的问题来选择合适的编程模式。例如，命令式编程更适于更新参数，而符号式编程更适于计算梯度。

我们主张混合使用各种方式，有时我们需要灵活性的地方并不是性能的关键点。这种情况下，舍弃效率而支持更灵活的接口是可以接受的。在机器学习中，组合多种方式通常比只使用一种要来得好。

如果你正确地组合不同的编程模式，你能得到比仅用一种方式更好的结果。在这一节中，我们讨论如何做到这一点。

### 符号式和命令式编程

要结合符号式和命令式编程，有两种方式：

  * 在符号式程序中，使用命令式编程作为回调函数（callback）
  * 将符号式程序作为命令式程序的一部分

我们已经看到，通常应该用命令式来更新参数，用符号式来计算梯度。

符号式的库已经混合了命令式程序，因为 Python 本身就是命令式的。例如，以下代码混合了符号式和 NumPy （命令式的）。

```python
A = Variable('A')
B = Variable('B')
C = B * A
D = C + Constant(1)
# compiles the function
f = compile(D)
d = f(A=np.ones(10), B=np.ones(10)*2)
d = d + 1.0
```

符号计算图被编译成函数，这个函数可以被命令式地执行。其内部对于用户来说是个黑盒。这和我们通常做的，写个 C++ 程序然后用 Python 来调用是一样的。

因为参数是保存在 GPU 内存中，我们可能不想在命令式的部分中使用 NumPy。支持一个兼容 GPU 的命令式的库来和符号式函数进行交互，或者在符号式编程中提供有限的一些更新操作，可能是更好的选择。

### 小运算和大运算

有理由相信，混合小运算和大运算是个更好的选择。考虑那些需要改变损失函数或者添加一些自定义层的应用场景。通常你可以使用大运算来构建网络，而使用小运算来创建新的组件。

记住阿姆达尔定律。通常这些新的组件不是性能上的瓶颈。因为性能关键点已经在大运算中被优化了，放弃优化这些额外的小运算不是什么大问题，或者可以做些有限的内存优化，而不是直接组合成一个大运算来运行它。

### 做出你自己的选择

在这篇文章中，我们比较了开发深度学习环境的多种方式。比较了各种方式的可用性和效率，还有很多需要权衡的地方（比如命令式和函数式编程并不是非黑即白）。你可以选择你自己的方式，或者将各种方式组合起来创建更有趣和更智能的深度学习库。
