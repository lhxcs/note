# Open-Sora: Democratizing Efficient Video Production for All

## Overview

- SORA 是在大规模的视频和图像数据上进行联合训练得到的生成模型。
    - 模型联合训练了不同持续时间、分辨率和纵横比的视频和图像上的文本条件扩散模型。
    - 利用了在视频和图像 latent space 上操作的 transformer 架构。
    - 生成一分钟的高清视频。
- 建立的一种通用的视觉模型方法(之前图像和视频模型使用不同的方法)


## Methods

### Spacetime Patches

训练模型之前，需要进行数据预处理。

与 LLM 类似， Sora 首先将所有输入数据统一化 (LLM 是文本，Sora 是视觉数据)，然后训练模型用于预测下一个词(LLM 是 token, Sora 是 patches)，并且使用原始数据而非修剪后的数据(不同分辨率，时长和长宽比)。

**具体过程**：

- 将原始视频数据转化为低维度潜空间特征，作者定义视频训练和推理的基本单元是 patches
    - patches 的实质是将完整的图片拆成 $N\times N$ 的小方格后，再将每个图像块转换成向量，可以得到包含每个小方格 Position 信息的 embedding。如下图所示，每个小方格就称一个 patch。

![](image/sora1.png)

- 但视频是由包含时间序列的多张图片构成的，处理时必须要考虑这些长时间范围 patches 序列的上下文关系。因此仅仅有 patch 图像块是不够的，作者将 patches 升级成包含时间信息的 spacetime patches.
- TODO(ViT, ViviT)


### Diffusion-Transformer

Sora 是一个 diffusion model, 能将有噪声的图像块，基于 prompt 还原出清晰的图像。

回顾一下 LLM 的文本生成任务，从输入一段 prompt 开始，模型会采用自回归的方式来预测接下来的每一个 token。

![](image/sora2.png)

如图所示，我们已知当前的 spacetime patches(可以类比 LLM 中的 prompt), sora 根据此推测下一个 spacetime patches, 通过自回归的方式预测出所有视频画面中的 spacetime patches, 然后组合在一起，便得到了整个视频画面的持续运动过程。

现在我们来看看 Sora 使用的 Diffusion-Transformer(DiT) 架构。DiT 是一个带有 Transformer Backbone 的扩散模型：

DiT=[VAE encoder + ViT + DDPM + VAE decoder], 其中 DDPM 中的卷积 U-Net 换成了 Transformer.