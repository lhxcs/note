# A Survey on Efficient Inference Large Language Models

!!! note "Reference"

    [A Survey on Efficient Inference for Large Language Models](https://arxiv.org/pdf/2404.14294)

## Preliminaries

大部分都是 Transformer 的知识。这里就写一些我不熟悉的。

- The inference process of LLMs can be divided into two stages
    - **Prefilling Stage**: The LLM calculates and stores the KV cache of the initial input tokens, and generates the first output token.
    - **Decoding Stage**: The LLM generates the output tokens one by one with the KV cache, and then updates it with the key (K) and value (V) pairs of the newly generated token.
- Efficiency indicators
    - latency
        - first token latency: latency to generate the first output token in the prefilling stage.
        - per-output token latency
        - generation latency: whole output token seqeuences
    - Memory
        - model size: the memory to store the model weights
        - KV cache size: memory to store KV cache
        - peak memory: maximum memory usage during the generation process(appro equal to the sum of model and KV size)
    - Throughput
        - token throughput: number of generated tokens per second
        - request throughput: the number of completed requests per second
- Efficiency Analysis
    - computational cost, memory access cost, memory usage
    - Model size
    - Attention Operation
    - Decoding Approach

## Data-Level Optimization

### Input Compression

- Prompt Pruning
    - core idea: remove unimportant tokes
- Prompt Summary

## System-Level Optimization

### Inference Engine


### Serving System

- Memory Management
    - **paged appraoch**
        - vLLM, 见论文阅读笔记
        - LightLLM uses a more fine-grained KV cache storage to cut down the waste happening with the irregular boundary. (treat the KV cache of a token as a unit, so always saturates the pre-allocated space)
        - paged storage leads to irregular memory access in the attention operator. 数据被分散存储在内存的不同物理位置，需要考虑映射关系。
        - vLLM 将 K cache 的头大小维度结构化为一个 16 字节的连续向量，优化内存访问模式
        - FlashInfer 涉及多种数据布局以及相应的内粗访问策略。
- Continuous Batching
    - The request lenghths in a batch can be different->low utilization
    - mitigate such periods of low utilization
    - continuous batching: batching new requests once some old requests are finished.
    - ORCA
    - split-and-fuse: batch together prefilling requests and decoding requests. (分割较长的预填充请求，然后将分割后的部分与多个短解码请求组合在一起进行批处理。)
- Scheduling Strategy
    - LLM service 中每个请求的处理时间可能不同，导致执行请求的顺序对吞吐量有显著影响。
    - Head-of-Line Blocking: 长请求被优先处理时，会发生队头阻塞，长请求迅速占用大量内存阻碍后续请求的处理。
    - FCFS： 按请求到达的顺序处理。
    - 优先解码请求。
    - 抢占式调度策略。
    - 多级反馈队列，优先处理剩余时间最短的请求。
    - 预测请求长度：skip-join
- Distributed Systems
    - LLM 服务通常在多个计算节点上部署，并行处理多个请求。
    - Prefilling 和 Decoding 的分离
    - ExeGPT: 不确定的延迟约束下，控制变量来最大化系统吞吐量
    - Llumnix: 运行时重新调度(负载均衡、内存碎片整理、优先级排序)
    - TBD