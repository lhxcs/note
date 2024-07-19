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

#### 为什么能 work

#### technical advantage