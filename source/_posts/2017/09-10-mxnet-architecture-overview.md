---
title: MXNet 系统架构（翻译）
date: 2017-09-10 22:27:31
tags:
    - mxnet
    - deeplearning
---

翻译自 https://mxnet.incubator.apache.org/architecture/overview.html

<!--more-->

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

## Operators (操作符) in MXNet

在 MXNet 中，操作符是一个类，这个类包括了实际的计算逻辑和一些能够帮助系统进行优化的辅助信息，像原地更新和自动微分之类的。要更好地理解这篇文档剩余的部分，我们建议你熟悉一下 mshadow 库，因为所有的操作符运算都是运行在系统运行时提供的类似张量（tensor-like）的数据结构 mshadow::TBlob 上。

MXNet 的操作符接口允许你：

  * 通过指定原地更新来减少内存分配。

  * 对 Python 接口隐藏一些内部参数。

  * 定义输入张量和输出张量之间的关系，允许系统为你检查它们的形状（shape）。

  * 为进行计算（例如调用 cudnn 操作）向系统请求额外的临时控件。

### Operator Interface (操作接口)

Forward 是核心操作接口：

```cpp
virtual void Forward(const OpContext &ctx,
                     const std::vector<TBlob> &in_data,
                     const std::vector<OpReqType> &req,
                     const std::vector<TBlob> &out_data,
                     const std::vector<TBlob> &aux_states) = 0;
```

OpContext 结构：

```cpp
struct OpContext {
    int is_train;
    RunContext run_ctx;
    std::vector<Resource> requested;
}
```

它描述了此操作符是否在训练阶段或测试阶段，它应该运行在哪个设备上（run_ctx），以及已经请求了的资源（将在之后的章节谈到）。

  * in_data 和 out_data 分别代表输入和输出张量。系统已经分配好所有张量使用的空间。。

  * req 表示计算结果应如何写入 out_data。换句话说，req.size() == out_data.size()，并且 req[i] 对应于  out_data[i] 的写入类型。

  * OpReqType 的定义为：
  ```cpp
  enum OpReqType {
      kNullOp,
      kWriteTo,
      kWriteInplace,
      kAddTo
  };
  ```

  通常，所有 out_data 的类型应该为 kWriteTo，表明所提供的 out_data 张量就是无数据的内存块，操作符应当直接向它里面写入数据。然而在某些情况下，例如在计算梯度张量时，我们最好可以对结果进行累加，而不是直接覆盖张量原有数据，这样我们就不用每次分配额外的内存。在这种情况下，相对应的 req 类型应被设置为 kAddTo，表示应当使用 += 操作。

  * aux_states 被设计为辅助计算的张量，当前没有用到。

除了 Foward 操作，你可以选择实现 Backward 接口：

```cpp
virtual void Backward(const OpContext &ctx,
                      const std::vector<TBlob> &out_grad,
                      const std::vector<TBlob> &in_data,
                      const std::vector<TBlob> &out_data,
                      const std::vector<OpReqType> &req,
                      const std::vector<TBlob> &in_grad,
                      const std::vector<TBlob> &aux_states);
```

这个接口遵循和 Forward 接口相同的设计原则，不同之处在于它使用了 out_grad, in_data 和 out_data，并且输出 in_grad 作为结果。这里的命名策略与 Torch 类似，可以总结为下图：

[input/output semantics figure]

某些操作可能不需要所有参数：out_grad, in_data, 和 out_data。你可以用 OperatorProperty 类的 DeclareBackwardDependency 接口来指定它们的依赖关系。

### Operator Property (操作的属性)

卷积可以有多种实现方式，你可能想在各种方式之间切换以实现最佳性能。因此，我们把操作的语义接口从他的实现接口（Operator 类）中剥离出来，放到 OperatorProperty 类。OperatorProperty 的接口包含：

  * InferShape

  ```cpp
  virtual bool InferShape(std::vector<TShape> *in_shape,
                          std::vector<TShape> *out_shape,
                          std::vector<TShape> *aux_shape) const = 0;
  ```

  这个接口有两个目的：

    * 告诉系统每个输入张量和输出张量的形状，以便在调用 Forward 和 Backward 之前分配空间。

    * 在执行之前检查参数的大小，保证没有明显的错误。in_shape 中指定的形状是被系统设置的（从前一个操作的 out_shape 中得到）。当获得的信息不足以推断形状时，InferShape 返回 false，并且当参数形状不一致时报错。

    * 请求资源: 像 cudnnConvolutionForward 之类的操作计算时需要一个工作区（workspace）。如果系统能够管理工作区，就可以进行优化，比如重用空间等。为此，MXNet 定义了两个接口：

    ```cpp
    virtual std::vector<ResourceRequest> ForwardResource(const std::vector<TShape> &in_shape) const;
    virtual std::vector<ResourceRequest> BackwardResource(const std::vector<TShape> &in_shape) const;
    ```

    ResourceRequest 结构（在 resource.h 中）目前仅包括一个类型标志：

    ```cpp
    struct ResourceRequest {
        enum Type {
            kRandom,  // get a mshadow::Random object
            kTempSpace,  // request temporary space
        };
        Type type;
    };
    ```

    如果 ForwardResource 和 BackwardResource 返回非空的数组，系统就会通过 Operator 类的 Forward 和 Backward 接口的 ctx 参数，来提供相应的资源。要访问这些资源，就使用：

    ```cpp
    auto tmp_space_res = ctx.requested[kTempSpace].get_space(some_shape, some_stream);
    auto rand_res = ctx.requested[kRandom].get_random(some_stream);
    ```
