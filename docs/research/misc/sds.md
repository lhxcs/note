# Score Distillation Sampling

## SDS

我们想要得到一个 3D 模型，这个模型在任何视角下渲染都能得到好的 2D image。形式化地描述，即我们有一个可微生成器 $g$，它能够把参数 $\theta$ 转化成图像 $x=g(\theta)$。对于 3D 来说， $\theta$ 是三维体素的参数, $g$ 是体渲染器。

对于首次提出 SDS loss 的工作 DreamFusion，任务是 text-to-3D, idea 就是优化 $x=g(\theta)$ 使得 $x$ 近似 diffusion model 采样得到的图片(用 diffusion 模型去噪的过程来监督一个 NeRF)。关键在于给出一个合理的损失函数。

原损失函数:

$$
\mathcal{L}_{Diff}(\phi, x)=\mathbb{E}_{t\sim\mathcal{U}(0,1),\epsilon\sim\mathcal{N}(0,I)}[\omega (t)\Vert \epsilon_{\phi}(\alpha_tx+\sigma_t\epsilon;t)-\epsilon\Vert_2^2]
$$

我们考虑 $\mathcal{L}_{Diff}$ 的梯度：

$$
\nabla_\theta\mathcal{L}_{Diff}(\phi, x=g(\theta))=\mathbb{E}_{t,\epsilon}[\omega (t)(\hat{\epsilon}_{\phi}(z_t;y,t)\epsilon)\dfrac{\partial\hat{\epsilon}_{\phi}(z_t;y,t)}{\partial z_t}\dfrac{\partial x}{\partial \theta}]
$$

即我们通过链式法则把损失函数的梯度拆解成噪声的对比项，UNet 的梯度以及 NeRF (in this case) 的梯度，但是 UNet 的梯度不好算，我们就直接把它扔掉，得到 SDS loss 的梯度表达式:

$$
\nabla_\theta\mathcal{L}_{SDS}(\phi, x=g(\theta))=\mathbb{E}_{t,\epsilon}[\omega (t)(\hat{\epsilon}_{\phi}(z_t;y,t)\epsilon)\dfrac{\partial x}{\partial \theta}]
$$

## NFDS

首先给出 diffusion model 在有条件 $y$(文本提示等) 下的一种噪声表达式形式：

$$
\epsilon_{\phi}^s(z_t;y,t)=\epsilon_{\phi}^s(z_t;y=\varnothing,t)+s(\epsilon_{\phi}^s(z_t;y,t)-\epsilon_{\phi}^s(z_t;y=\varnothing,t))
$$

其中 $y=\varnothing$ 代表无条件噪声。可以理解为我们通过控制 $s$ 引导原来的无条件模型向条件信息的方向便宜。其中 $s$ 和模型参数 $\phi$ 是要优化得到的。

### Score Decomposition

首先我们考虑 $\delta_C=\epsilon_{\phi}^s(z_t;y,t)-\epsilon_{\phi}^s(z_t;y=\varnothing,t)$。差异 $\delta_C$ 可以被视为指导生成图像朝向与条件 $y$ 对齐的方向。其中 $\epsilon_{\phi}^s(z_t;y,t)$ 理想情况下指向图像分布中概率密度中的局部最大值，以确保生成的图像在给定条件下是高概率的，即它们既真实又符合条件。

其次我们将无条件项 $\epsilon_{\phi}^s(z_t;y=\varnothing,t)$ 分解成两个组成部分: 域校正 $\delta_D$ 和去噪方向 $\delta_N$。

注意到在 SDS 中，$z_t$ 是通过对分布外的渲染图像 $x=g(\theta)$ 添加噪声得到的，这个图像不是从原始分布 $p_data$ 采样的。因此我们想将 $\epsilon_{\phi}^s(z_t;y=\varnothing,t)$ 分解成:

- 域矫正项 $\delta_D$：由渲染图像和真实图像分布之间的差异引起，与 $x(\theta)$ 的内容有关。
- 去噪方向 $\delta_N$: 指向更清晰图像的去噪方向，与 $x(\theta)$ 无关。

因此我们可以把 SDS 重写为：

$$
\nabla_\theta\mathcal{L}_{SDS}=\omega (t)(\epsilon_\phi^s(z_t;y,t)-\epsilon)\dfrac{\partial x}{\partial \theta}=\omega (t)(\delta_D+\delta_N+s\delta_C-\epsilon)\dfrac{\partial x}{\partial \theta}
$$

注意到 $\delta_N-\epsilon$ 计算了预测噪声和实际添加到图像的噪声之间的差异。在实际情况中，二者不完全相同，导致 $\delta_N-\epsilon$ 非零，带有噪声。这会对细节的生成质量造成影响。(较小的时间步用于形成图像的细节)

因此 NFDS 的 Loss 就基于此提出，我们希望理想情况下只有 $s\delta_C+\delta_D$ 两项用来指导参数的优化。由于 $\epsilon_{\phi}^s(z_t;y=\varnothing,t)=\delta_N+\delta_D$:

- 在较小的时间步 $t$ 下，$\delta_N$ 相对较小，$\epsilon_{\phi}^s(z_t;y=\varnothing,t)$ 由 $\delta_D$ 主导。
- 在较大的时间步 ($t\ge 200$),令 $\delta_D=\epsilon_{\phi}^s(z_t;y=\varnothing,t)-\epsilon_{\phi}(z_t;o^{neg},t)$，其中 $p^{neg}$ 是一些负面属性集合 (不真实，模糊，低质量等)


因此我们可以得到 NFDS 的表达式 :

$$
\nabla_{\theta}\mathcal{L}_{NFSD}=\omega (t)(\delta_D+s\delta_C)\dfrac{\partial x}{\partial \theta}
$$