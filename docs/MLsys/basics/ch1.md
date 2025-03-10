# DNN Architectures

!!! note

    https://mlsysbook.ai/contents/core/dnn_architectures/dnn_architectures.html#sec-deep-learning-primer-resource



## Background: Deep Learning

放一个 deep learning 任务的 general formula:

$$
\theta ^{t+1} = f(\theta ^{t},\nabla_{\mathcal{L}}(\theta^{t}, D^{(t)}))
$$

**Three most important components**

- Data: images, text, audio, tables, etc.
- Model: CNNs, RNNs, Transformers, MoEs, etc.
- Compute: CPU, GPU/TPU/LPU, M1/M2/M3/M4, FPGA, etc.

### CNN

The design of CNN is inherently good at finding local spatial relationships in images as a result of their focus on small patches of an image. 

!!! note "several intersting applications"

    - Classification
    - Retriebal: retrieving images similar to a query image.
    - Detection
    - Segementation
    - Self-driving
    - Synthesis: as in synthesizing and generating visual data.

The magic of CNNs is that if we stack their convolution layers many times, we start to learn more complex features.

- At the very lowest level, we learn edge and color.
- As we stack, we begin to get higher-level features such as texture, shapes, and eventually high-level semantic representations.


!!! note "top 3 models for CNNs"

    1. AlexNet: breakthrough model for CNNs.
    2. ResNet: introducing skip connections, which address the vanishing gradient problem.
    3. U-Net: backbone for stable diffusion. 

**Important components for CNNs**

- Convolutional operations. Especially, `Conv2d` with a $3\times 3$ filter.
- Matrix multiplication. Included because skip connections.
- Softmax: applied at the output layer to predict the label with probabilities.
- Element-wise operations: ReLU, addition, subtraction, pooling, batch normalization.

### RNN


## 

### MLPs: Dense Pattern Processing

```python
def mlp_layer_matrix(X, W, b):
    # X: input matrix (batch_size * num_inputs)
    # W: weight matrix (num_inputs * num_outputs)
    # b: bias vector
    H = activation(matmul(X, W) + b)
    return H
```

```python
def mlp_layer_compute(X, W, b):
    for batch in range(batch_size):
        for out in range(num_outputs):
            Z[batch, out] = b[out]
            for in_ in range(num_inputs):
                Z[batch, out] += X[batch, in_] * W[in_, out]
    H = activation(Z)
    return H
```

!!! note

    When analyzing how computational patterns impact computer systems, we typically examine three fundamental dimensions

    - memory requirements
    - computation needs
    - data movement

- Memory Requirements
    - store and access weights, inputs, and intermediate results.
- Computation Needs
    - The core computation revolves around multiply-accumulate operations arranged in nested loops.
    - The dense matrix multiplication pattern can be parallelized across multiple processing units, with each handling different subsets of neurons.
- Data Movement
    - The all-to-all connectivity pattern in MLPs creates significant data movement requirements.


### CNNs: Spatial Pattern Processing

Task requirements:

- the ability to detect local patterns.
- the ability to recognize these patterns regardless of their position.

CNNs: each output connects only to a small spatially contiguous region of the input. 
