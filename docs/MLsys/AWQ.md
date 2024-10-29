# AWQ: Activation-Aware Weight Quantization for on-device LLM Compression and Acceleration

## Abstract

在边缘设备上部署 LLM 收到 model size 和有限硬件资源的挑战。 AWQ 是一种针对 LLM 低比特权重量化的硬件友好方法。

AWQ 发现并非所有的权重对 LLM 的性能都同等重要，保护 1% 的 salient weight 就可以大幅度减少量化误差。并且 AWQ 通过激活分布而非权重本身来识别 salient 的权重 channel.

## Method

### Improving LLM Quantization by Preserving 1% Salient Weights

作者观察到 LLM 中的权重并非同等重要，我们在量化过程中可以保持这些关键权重，从而可以不用进行 training or regression 来减少性能损失。

通常用权重的大小或 L2 范数来判断权重的重要性。但这样不会显著提高量化性能。有趣的是，基于 activation 的大小来选择权重可以显著提高性能。作者假设具有较大输入特征的权重更重要，保留相应的权重可以保留这些特征。

Limitations: despite keeping 0.1% of weights in FP16 can improve the quantized performance without a noticeble increase in model size, such a **mixed-precision** data type will make the system implementation difficult. We need to come up with a method to protect the important weights without actually keeping them as FP16.

现代硬件架构中，整数乘法器和浮点乘法器是两个独立的运算单元，进行混合精度乘法时，如果整型权重矩阵混入了少量浮点型，硬件就会将所有权重数据转换成浮点型。

### Protecting Sailent Weights by Activation-aware Scaling

Propose an alternative method to reduce the quantization error of the salient weight by per-channel scaling, which does not suffer from hardware inefficiency issue.

- Analyzing the quantization error
    - 