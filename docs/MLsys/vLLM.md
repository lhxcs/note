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
