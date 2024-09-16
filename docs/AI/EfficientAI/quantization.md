# Quantization

## Numeric Data Types

- Motivation: less bit-width means less energy.

- Integer
    - Unsigned integer: n-bit Range: $[0,2^n-1]$
    - Signed Integer
        - Sign-Magnitude Representation: n-bit range $[-2^{n-1}-1,2^{n-1}-1]$
        - Two's Complement Representation: n-bit range $[-2^{n-1},2^{n-1}-1]$
- Fixed-Point Number
- Floating-Point Number
    - IEEE FP32, FP64, FP16
    - Google's BFloat16: 8-bit exponent, 7-bit mantissa
        - 用精度换动态范围
    - Nvidia FP8(E4M3)
        - 没有 inf, $S.1111.111_2$ 表示 NaN
        - 最大的规格化值为 $S.1111.110_2 = 448$ 
    - Nvidia FP8(E5M2)
        - 有 inf ($S.11111.00_2$) 和 NaN($S.11111.XX_2$)
        - 最大的规格化值为 $S.11110.11_2=57344$
        - 有更大的动态范围(exp)，用于反向传播时的梯度计算(模型在训练过程中追求更高的动态范围，推理过程中追求更高的精度)
- INT4 & FP4

![](image/15.png)

## Neural Network Quantization

**Quantization is the process of constraining an input from a continuous or otherwise large set of values to a discrete set.**

![](image/16.png)

### K-Means-based Weight Quantization

- 使用 K-means 算法对权重进行分类，接近的权重着相同的颜色，将同类的数统一替换为与之相近的浮点数
- 有一个 lookup table(codebook)， 权重就是调色板里的索引。因此只需要存浮点调色板和整数索引，不用完整地将浮点权重存下来。

!!! Example

    ![](image/17.png)

    我们使用 2-bit 量化，即得到 4 个不同的值，经过 K-Means 算法我们得到 4 个中心点，然后将权重量化为这 4 个中心点的索引。右侧是量化后的权重。

- 量化后的权重也可以进行微调。梯度也可以进行相应的量化。
    - 按权重分类结果对梯度进行分组，每一组求和或者average, 得到改组的梯度
    - 将 centroids 的值进行更新

![](image/18.png)

- 卷积层小于 4—bit 编码后准确率大幅度下降，全连接层小于 2-bit 编码后准确率大幅下降。
- 这种量化方法只节省了存储空间，没有减少计算量，因为所有的计算和内存访问依然是浮点数。

### Linear Quantization

- 用仿射变换将整数映射到实数。
    - ![](image/19.png)
    - 使用 $r=S(q-Z)$ 将整数 $q$ 映射到实数 $r$。其中 $Z$ 是 zero-point(int),$S$ 是 scale(fp)
        - zero-point: 为了让 $r=0.0$ 这个数能被准确映射到一个整数 $Z$ 上。
        - 确定 $S,Z$: 当我们量化的位数确定后，使用二进制补码表示法，$q_{max}$ 和 $q_{min}$ 就是 $2^{N-1}-1$ 和 $-2^{N-1}$。这时候如果知道了 $r_{max},r_{min}$ 就可以求出 $S,Z$.
        - ![](image/20.png)
- 线性量化下的矩阵乘法 TODO