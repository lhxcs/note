# PuzzleAvatar

!!! note

    以 [论文解析树](https://pengsida.notion.site/d192db870bc64436ae4a4a590b36772a) 的方式来阅读 Paper。

## Abstract

### Task

**Album2Human**: generate a faithful 3D avatar from a personal OOTD(outfit of the day) album.

### Technical Challenge for previous methods

- 之前的文生 3D 的工作能够生成名人或者动画角色，但是在 everyday people 上表现不好。
- reconstruction from OOTD: OOTD 的照片有多样的位姿、视角、遮挡等问题，i.e, casual photo collections. 但是之前的方法都需要 full-body images in controlled settings.

### Key insight/motivation

1. 一句话介绍 insight/motivation:  fine-tune a foundational VLM,encoding appearence, identity, garments etc. into separate learned tokens, from which assemble a 3D avatar. (circumvent estimating human body poses and cameras)

2. 一句话介绍 insight 的好处: 可以忽略人体和相机姿态的估计。


### Technical contributions

1. customize avatars by simply inter-changing tokens.
2. create **PuzzleIOI** dataset as a benchmark for Album2Human task.

## Introduction

> In all chaos there is a cosmos, in all disorder a secret order

### Task and application

- Task: Album2Human, reconstructing a 3D avatar from a personal photo album with a consistent outfit, hairstyle and accessories, but unconstrained human pose, camera settings, framing, lighting and background.
- Application: character editing, vitual try-on



### Technical challenge for previous methods

1. Varying body articulation
2. 之前的方法收集的 image 是在 laboratory settings 里得到的 (limited body poses, calibrated and synchronized cameras, simple backgrounds etc.)


### 介绍解决 challenge 的 our pipeline

#### Reconstruction as conditional generation

将不完整观察的重建问题视为一种图像修复任务。

- Album2Human 的任务中，OOTD中的对象可能只有有限的视角，或者条件不好(occlusion等问题)。
- 利用基础模型的先验知识来推断出未观察到的部分。

#### Pipeline

- Input: OOTD 照片集合
- 预处理：使用自动化工具识别和描述照片中的元素
- 资产分割：将图像分割成多个 assets，每个 asset 都有相应的分割掩码和学习标记。
- 微调：对 T2I 模型进行微调，以识别和生成与个人相关的 asset
- 生成：使用 SDS 技术，根据微调后的模型和文本描述生成 3D avatar

> We adpat existing T2I work to learn subject-specific priors from a personall OOTD image collection, by finetuning T2I models on such images to capture identity, pieces of clothing, accessories, and hairstyle into unique and inter-exchangeable tokens, and extracting 3D geometry and texture with SDS based techniques.


**Advantage**

1. 避免显式地建立每个像素到规范人体空间的对应关系。


open-vocabulary segmentor: 不限于识别训练数据中出现过的对象，能够泛化到新的，未知的类别。在这篇 paper 里，被用来自动从个人照片集中分割和识别不同的服装、发型、配饰等。

## Method

### Overview

- Input: image collection $\{\mathcal{I}_1,\mathcal{I}_2,\cdots \mathcal{I}_N\}$
- Output: a 3D avatar that captures the person's shape $\phi_g$ and appearance $\phi_c$.

Circumvent estimating human body poses and cameras, performing implicit human canonicalization via a foundation vision-language model.


### Pipeline Module 1: PuzzleBooth

#### Motivation



#### 做法

- Asset Creation
    - All images are segmented into multiple assets $V_k$, each of which is associated with a segmentation mask $\mathcal{M}_k$, a dedicated learnable token $[v_k]$, and its textual name $[c_k]$.
    - Obtain a coarse view direction $d$ for each image. Information s obtained by Grounded-SAM or GPT-4V.
- Two-Stage Personalization
    - Finetune the pretrained T2I dffusion model so that it adpats to the new assets.(对预训练模型进行微调，以适应特定任务或 New dataset)
        - 第一阶段：文本嵌入优化，即模型如何理解描述 asset 的文字。此时用高学习率(让模型快速学习并适应 new asset 的特征，即迅速收敛到好的解空间，捕捉关键特征)
        - 第二阶段：同时优化 text 和 visual 部分，即开始优化模型生成图像的能力。此时使用低学习率(微调模型，细化第一阶段学到的知识。)
    - Union Sampling Durng Training
        - 对于每张图片，随机选择图片中出现的一些 asset，将这些选中的资产联合起来训练模型。
        - 被选中的资产通过像素级联合操作形成一个掩码的集合，表示所有选中资产的可见区域 $\mathcal{M}_{\cup}=\cup_{i=1}^{j}\mathcal{M_i}$
        - 图像的联合表示：$\mathcal{I}_{\cup}=\mathcal{I}\odot \mathcal{M}_{\cup}$,即最终得到的联合图像相当于从原始图像中剪出了所有选中的 asset。
        - 构建联合文本 prompt: 该 propmpt 通过串联所选资产的描述来形成。
- Losses
    - Masked Diffusion Loss
        - 在分割掩码区域内确保模型生成的图像尽可能接近真实图像。
        - $\mathcal{L}_{rec}=\mathbb{E}_{z,\epsilon\sim\mathcal{N}(0,1),t}[\Vert[\epsilon-\epsilon_\theta(z_t,t,p_{\cup})]\odot \mathcal{M}_{\cup}\Vert_2^2]$
        - $\mathcal{M}_{\cup}$ is union mask, $\epsilon_\theta$ is the denoised output at diffusion step $t$ given the union prompt $p_{\cup}$.
    - Cross-Attention Loss
        - 确保新添加的 token 与它们各自目标 asset 之间的专一性关联。
        - $\mathcal{L}_{attn}=\mathbb{E}_{z,j,t}[\Vert\mathcal{CA}_{\theta}(v_j,z_t)-\mathcal{M}_j\Vert_2^2]$
        - 其中 $\mathcal{CA}_{\theta}$ 代表 $v_j$ 和 $z_t$ 某部分的相关性强度，该损失函数最小化交叉注意力和分割掩码 $M_j$ 差异，即新添加的标记 $v_j$ 只与目标资产 $j$ 相关联。
    - Prior Preservation Loss
        - 保持模型泛化能力
        - $\mathcal{L}=\mathbb{E}_{z^{pr},\epsilon\sim\mathcal{N}(0,1),t}[\Vert[\epsilon-\epsilon_{\theta}(z_t^{pr},t,p_{\cup}^{*})]\Vert_2^2]$
        - 其中 $p_{\cup}^*$ 是没有特殊标记的文本提示，即只包含通用概念，不包含微调过程中新添加的资产标记。

#### 为什么能 work

#### technical advantage


### Pipeline Module 2: PuzzleAvatar: Put Puzzle Pieces Together

#### Motivation


#### Method

!!! note

    图像数据的分布：图像在所有可能的图像空间中的出现概率。
    在生成模型中，我们的目标是学习输入图片的分布，这样我们就可以在这个分布中采样并生成新的图像。

- Score Distillation Sampling(SDS)
    - 最早在 dreamfusion 中提出，从 diffusion 模型中蒸馏知识。
    - 想法就是 dreamfusion 中优化的损失函数含有一个 UNet 的梯度，非常慢而且很难算，想法就是跳过这个梯度的计算。
    - 因此可以对 $L_{diff}$ 通过链式法则进行分解，将 UNet 的梯度分离出来，然后扔掉。
    - 最终表达式 : $\nabla_{\theta}\mathcal{L}_{SDS}=\mathbb{E}_{t, \epsilon}[w(t)(\hat{\epsilon}(z_t|y)-\epsilon)\dfrac{\partial x}{\partial \theta}]$, 其中三维场景辐射场的参数是 $\theta$。

- Noise-Free Distillation Sampling
    - NFDS 是 SDS 的改进版本，解决 SDS 颜色过饱和的问题(why)。
    - NFDS 将重建残差分为两个独立的残差项 $\delta_C,\delta_D$。
        - 其中 $\delta_C$ 比较了给定扩散输出的潜在表示 $z_t$ 下，考虑资产 $p$ 和不考虑时图像的重建结果 (为了能够使模型专注于生成与特定资产相关的图像内容)。$\delta_C(z_t,p,t)=\epsilon_{\theta}(z_t;p,t)-\epsilon_{\theta}(z_t;\emptyset,t)$
        - $\delta_D$ 根据 step $t$ 的不同，调整了重建过程中的图像对比度。(动态调整图像的对比度，实现更平滑的图像细节过渡和更自然的颜色分布)

- Representation and initialization
    - 3D Human avatar is parameterized with DMTet
    - geometry $\phi_g$ is initialized to an A-posed SMPL-X body.(3D模型的结构部分，如人物的身体形状)
- Optimization
    - use full-text prompts from $p^{all}$ as a guiding prompt.
    - 两阶段优化
        - 几何优化：用表面法线空间引导几何。并且加文本提示进行指示。
        - 外观优化

#### 为什么能 work

#### technical advantage

## Experiments

- 利用 4D Scanner (synced with IOI color cameras) 来捕捉真实的 3D 形状和外观作为 benchmark 来衡量重建误差

### PuzzleIOI Dataset

### 量化评估的方法

- quality of shape reconstruction
    - Chamfer 距离： $A$ 中每个点到 $B$ 中最近邻点的距离之和加上 $B$ 中每个点到 $A$ 中最近邻点的距离之和。
    - P2S 距离：单向点到表面的距离度量。
    - $L_2$ error for normal maps rendered for four views to capture local surface details.
- quality of appearance reconstruction
    - PSNR: PSNR 越高，表示重建图像的质量越接近原始图像
    - SSIM: 考虑亮度、对比度、结构(图像中纹理和细节)，结果在 $-1$ 到 $1$ 之间，越接近 $1$ 表示两幅图像越相似。
    - LPIPS：使用预训练的深度网络来提取图像特征，然后计算这些特征之间的距离，以评估图像之间的感知相似度。LPIPS的值越低表示两张图像越相似


### Benchmark


### Ablations

消融实验是通过移除或修改模型的某些部分来理解这些部分对最终性能的贡献。

- view prompt $[d]$ helps the reconstruction
- NSFD outperforms vanilla SDS
- syntheic human prior helps
- token $[v_i]$ encode the identity and features of assets, but more detailed prompts can introduce bias

