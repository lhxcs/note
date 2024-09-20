# Efficient Memory Management for Large Language Model Serving withe PagedAttention


## Background

在这一节简单介绍 LLM 的背景。

### Transformer-Based LLMs

**autoregressive decomposition**(条件概率的乘积)
$$
P(x) = P(x_1)\cdot P(x_2|x_1)\cdots P(x_n|x_1,\cdots,x_{n-1})
$$
其余见 Transformer 的笔记。

### LLM Service & Autoregressive Generation

A request to an LLM provides a list of *input prompt tokens* $(x_1,\cdots,x_n)$ and the LLM service generates a list of output tokens $(x_{n+1},\cdots,x_{n+T})$.

需要注意的是，LLM 只能一个个生成新的 token, 并且生成的新 token 只取决于序列中之前的 token , 特别是之前 Token 的 key vector 和 value vector。因此在生成的过程中，所有 token 的这两个向量都被存储起来，称作 KV cache。每个 token 的 KV cache 只取决于它前面的 token，这意味着输入序列中不同位置的 Token 具有不同的 KV cache。

因此，我们可以总结出 LLM 的生成过程，大致分为两个步骤:

1. **The prompt phase**: LLM 接受整个 prompt 输入 $(x_1,\cdots, x_n)$ 并计算第一个新 token 的概率 $P(x_{n+1}|x_1,\cdots,x_n)$，同时生成 key vectors $k_1,\cdots,k_n$ 和 value vectors $v_1,\cdots,v_n$。这个过程可以并行计算(因为输入序列已知)。
2. **The autoregressive generation phase**: 这个阶段不断生成剩下的 token。在第 $t$ 轮循环中，模型只接受 $x_{n+t}$ 作为新输入，并计算 $P(x_{n+t+1}|x_1,\cdots,x_{n+t})$ 以及 key/value vectors。需要注意的是第 $1$ 到第 $n+t-1$ 个 token 的 key/value vector 已经被 cache 了，因此只要计算 $n+t$ position 的向量即可。整个过程不能并行，因为数据是相互依赖的。**所以这个阶段是最主要的瓶颈**。(underutilizes GPU computation and memory-bound)

### Batching Techniques for LLMs

为了提升 serving 的效率，我们可以把多个请求 Batch 起来，即不用对每次输入都加载一遍模型参数，而是用于多次输入(其实就类似于模型训练的 batch)。

但是 LLM 的 batching 有两个问题:

1. 不同请求的到达时间不同。naive batching 策略要么让最早到达的请求等待，要么让之后到达的请求等前面的请求处理完毕。两种方法都会造成延时。
2. 请求的输入输出长度可能相差很大。我们的 batching 会填充短的请求，使得所有长度相等，这样就浪费了 GPU 的计算和内存。

为了解决这个问题，可以使用一些高细粒度(fine-grained)的 batching 技术。(cellular batching 或者 iteration-level scheduling)。在每轮迭代结束之后，完成的请求被移除 batch 并添加新的请求。这样子就不需要进行 input/ouutput 的填充了。


## Memory Challenges in LLM Serving

尽管我们有细粒度的 batching 技术，LLM serving 仍然受到 GPU 内存的限制 (memory-bound)，具体来说有以下几个问题:

- **Large KV cache**
    - 随着请求数量增多，KV Cache 的大小也快速增大。
    - 13B 的 OPT 模型中，每个 Token 需要 $2\times 5120\times 40\times 2$ 的空间，其中 2(key and value vectors), 5120(隐藏层维度), 40(层数),2(fp16 字节数)，而 OPT 模型最长可以生成 2048 个 Token 的序列，KV cache 需要 1.6GB 的内存。所以即使 GPU 所有的内存空间都分配给 KV cache, 一次也只能处理十几个请求。
    - 低效的内存管理会减小批次大小
    - GPU 计算增长速度快于内存容量的增长速度，导致内存是最主要的瓶颈。
- **Complex decoding algorithms**
    - 不同的应用场景和解码算法会有不同的 KV cache 共享效率。
- **Scheduling for unknown input & output lengths**
    - 输入和输出长度不是确定的，需要内存管理算法来处理这些情况。
    - 输出长度会不断变长，这时候 KV cache 所需要的 memory 大小也会增大，这时如果出现内存不足的情况，就需要系统进行调度。

### Memory Management in Exsiting Systems

现在的 LLM 将单个请求的 KV cache 存储为一个连续的张量，但由于 LLM 的输出长度是不可预测的，系统在内存管理的时候只能静态预分配一块足够大的内存(该请求的最大可能序列长度)。

![](image/vllm1.png)


假设有两个请求 $A,B$, A 最大长度为 2047，B 为 512.那么系统的分配结果如上图所示。

- reserved slots：用来放接下来要输出的 token
- Interanal fragmentation: 预分配的空间大于实际需要的空间造成的内存未利用
- external fragmentation: 两个请求的分配导致中间一段内存不可用。

internal/external fragmentation 不会再被使用，是纯粹的内存浪费(pure memory waste), reserved slots 最终会被使用，但是可能这段空间会被保留很长一段时间，但其实可以被其它的请求使用。这是非常低效的。