# Just-In-Time Checkpointing: Low Cost Error Recovery from Deep Learning Training Failures

## Overview of Key Mechanisms

### Domain-aware Device API Interception

当发生错误时，当前作业通常会崩溃或挂起，作业启动器通常会终止所有工作进程。文章作者开发了一种库，该库能够透明地拦截用户或框架代码做出的 CUDA 和 NCCL(英伟达集合通信库) API 调用。


### Recovery

JIT 的核心目标是在发生故障时，尽量减少需要重新执行的工作量，理想情况下只需要执行一个 minibatch 的工作。

## User-Level JIT Checkpointing

training jobs 可以通过少量修改启用 JIT checkpointing, 用户需要初始化作者提供的库，提供一个 `save_checkpoint` 函数用来保存检查点。

实现 JIT checkpointing 存在两个问题：

- 需要及时检测到故障
- 即使发生故障后，也需要能够访问到一致且未损坏的 GPU 状态

为了解决这些挑战，作者利用 DNN 大规模训练中数据并行副本的状态冗余，即利用健康的并行数据副本来访问和 checkpoint GPU 状态。但这需要分布式作业的每个等级工作进程首先能够检测到其它任何数据并行副本中的故障。作者利用所有数据并行等级定期通过集体通信操作进行同步，如果任何等级失败，其它等级的通信操作将挂起。所以基本的 idea 就是利用挂起的进程来检查每个 GPU 的相关状态，然后使用所有这些状态来进行恢复。

