---
title: MXNet 系统架构（翻译）
date: 2017-09-10 22:27:31
tags:
    - mxnet
    - deeplearning
---

翻译自 https://mxnet.incubator.apache.org/architecture/overview.html

![mxnet architecture overview](overview.png)

这张图展示了 MXNet 的主要模块，以及他们之间的交互。这些模块是：

  * Runtime Dependency Engine: 根据读写的依赖关系，安排并执行各种操作。

  * Storage Allocator: 高效地分配回收主机（CPU）和设备（GPU）的内存块。

  * Resource Manager: 管理全局资源，比如随机数生成器和临时空间。

  * NDArray: 动态、异步的 n-维数组，为 MXNet 提供灵活的命令式编程。

  * Symbolic Execution: 静态符号图执行器，提供高效的符号图执行和优化。

  * Operator: 定义静态的前向和梯度计算（backprop）操作。

  * SimpleOp: 以统一的方式扩展 NDArray 操作和符号操作的操作符。

  * Symbol Construction: 符号构建，提供构建计算图（神经网络配置）的方式。

  * KVStore: 键值存储接口，提供高效的参数同步。

  * Data Loading (IO): 高效的数据加载和修改。

# MXNet 系统模块

## Execution Engine (执行引擎)

你不仅可以使用 MXNet 引擎进行深度学习，还可以使用它来解决任何领域相关的问题。MXNet 被设计成可以解决一个通用问题：根据依赖关系来执行一系列的函数。有依赖关系的任意两个函数应该按照顺序执行。为提升性能，没有依赖关系的函数可以被并行执行。更详细的讨论，请见 [notes on the dependency engine](https://mxnet.incubator.apache.org/architecture/note_engine.html)。

### Interface (接口)

这个 API 是执行引擎的核心接口：

``` cpp
virtual void PushSync(Fn exec_fun, Context exec_ctx,
                      std::vector<VarHandle> const& const_vars,
                      std::vector<VarHandle> const& mutate_vars) = 0;
```

这个 API 允许你将一个函数（exec_fun）和它的执行上下文信息（context）以及依赖关系一起推送给执行引擎。exec_ctx 是 exec_fun 的执行上下文，const_vars 是函数读取的变量，mutate_vars 是函数将要修改的变量。执行引擎提供以下保证：

> 任意两个函数，如果它们会修改同一个变量，则它们的执行顺序与它们被推送到引擎的顺序是一致的。

### Function (函数)

引擎的函数的类型是：

```cpp
using Fn = std::function<void(RunContext)>;
```

RunContext 包含了运行时的信息，这些信息由引擎来决定。

```cpp
struct RunContext {
    // stream pointer which could be safely cast to
    // cudaStream_t* type
    void *stream
};
```

另外，你也可以使用 mxnet::engine::DAGEngine::Fn, 它有相同的类型定义。

所有的函数都在引擎的内部线程中执行。在这个模型下，将会阻塞的函数（通常处理磁盘或网络之类的 I/O 任务）发送给引擎通常不是个好主意，因为它会占用执行线程，并且降低吞吐量。我们为阻塞的情形提供了另一个异步的函数：

```cpp
using Callback = std::function<void()>;
using AsyncFn = std::function<void(RunContext, Callback)>;
```

在 AsyncFn 中，你可以在退出函数之前把需要阻塞的部分传到你自己的线程。引擎在 Callback 退出之前都会认为函数没有执行完成。

### Context (上下文)

你可以指定函数执行的上下文。这通常包括函数应该执行在 CPU 还是 GPU 上，如果是 GPU，还可以指定使用哪个 GPU。Context 和 RunContext 不同。Context 包含设备类型 (GPU/CPU) 和设备 id，而 RunContext 包含只有运行时才能决定的信息，比如函数应该在哪个流上被执行。

### VarHandle

VarHandle 是用来指定函数的依赖关系的。MXNet 引擎在设计上，和其他模块是松耦合的，因此 VarHandle 就像一个引擎提供的记号，你可以用它来标记函数能使用或修改的外部资源。VarHandle 是很轻量的，所以创建、删除或复制都只有很小的开销。在将函数推送给引擎时，你需要在 const_vars 向量中指定函数将要使用（只读）的变量，在 mutate_vars 向量中指定函数将要修改的变量。引擎使用以下规则来解析函数之间的依赖关系：

> 如果两个函数修改至少一个共同的变量，那么它们的执行顺序和它们被推送的顺序是一致的。

举个例子，如果 Fn1 和 Fn2 都修改 V2，并且 Fn2 是在 Fn1 之后被推送，则执行引擎保证 Fn2 在 Fn1 之后执行。如果 Fn1 和 Fn2 都使用（只读）V2，则它们的执行顺序是随机的。

这样的设计允许引擎按照尽量少分配内存的方式来安排函数的调用。例如，DNN 的权重更新函数可以使用 += 操作在原地更新权重，而不是每次都生成新的权重数组。

你可以使用 NewVar() 来创建变量，用 PushDelete() 来删除变量。

### Push and Wait (推送和等待)

所有的 Push API 都是异步的。Push API 调用会立即返回，不管 Fn 是否执行完成。这允许引擎在用户线程推送函数的时候并行开始执行计算。Push API 不是线程安全的，在一个时刻，只有一个线程可以调用 Push API。

如果你想要等待某个特定的 Fn 执行完成，你可以在传入一个 Callback，并且在你的 Fn 结束的时候调用它。

如果你想等待所有涉及到某个变量的所有 Fn 都结束，可以使用 WaitForVar(var) API。

如果你想等待所有推送的 Fn 都结束，使用 WaitForAll() API。

### Save Object Creation Cost ()

在某些情况下，你需要在较长的时间区间内推送多个函数到引擎。如果这些函数的计算开销比较小，复制匿名函数和创建变量的开销则变得相对高了。这种情况下，我们提供了 API 来提前创建 OprHandle：

```cpp
virtual OprHandle NewOperator(AsyncFn fn,
                              std::vector<VarHandle> const& const_vars,
                              std::vector<VarHandle> const& mutate_vars) = 0;
```

你可以连续推送 OprHandle 而不用重复创建它：

```cpp
virtual void Push(OprHandle op, Context exec_ctx) = 0;
```

要删除它，调用 DeleteOperator(OprHandle op) API。要保证在调用这个 API 之前，操作已经完成。

### API Reference

略。
