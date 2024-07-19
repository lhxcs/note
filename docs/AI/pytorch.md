# Pytorch

## 预备知识

### 数据操作

#### tensor

- 张量表示一个由数值组成的数组，这个数组可能有多个维度。张量对应 vector.

- 我们可以用 `arange` 创建一个行向量 `x`。

```python
x = torch.arange(12) #x = tensor([0,1,2, ……， 11])
```

- 可以通过张量的 `shape` 属性来访问张量的形状

```python
x.shape #torch.Size([12])
```

- `reshape` 用于改变一个张量的形状而不改变元素数量和元素值
  - 不需要手动指定每个维度来改变形状。

```python
X = x.reshape(3,4)
X = x.reshape(-1,4)
X = x.reshape(3,,-1)

tensor([[ 0,  1,  2,  3],
        [ 4,  5,  6,  7],
        [ 8,  9, 10, 11]])
```

- 创建全零，全一

```python
torch.zeros((2,3,4))
torch.ones((2,3,4))
torch.randn(3,4)#形状为 （3，4）的tensor,每个元素都从均值为 0， 标准差为 1 的标准高斯分布随机采样
```

#### 运算符

直接看代码, 注意都是按元素运算

```python
x = torch.tensor([1.0, 2, 4, 8])
y = torch.tensor([2, 2, 2, 2])
x + y, x - y, x * y, x / y, x ** y # ** 是求幂运算
torch.exp(x)
```

- 可以把多个张量连结起来

```python
X = torch.arange(12, dtype = torch.float32).reshape((3, 4))
Y = torch.tensor([[2.0, 1, 4, 3], [1, 2, 3, 4], [4, 3, 2, 1]])
torch.cat((X,Y),dim = 0)

tensor([[ 0.,  1.,  2.,  3.],
        [ 4.,  5.,  6.,  7.],
        [ 8.,  9., 10., 11.],
        [ 2.,  1.,  4.,  3.],
        [ 1.,  2.,  3.,  4.],
        [ 4.,  3.,  2.,  1.]])

torch.cat((X, Y), dim = 1)
tensor([[ 0.,  1.,  2.,  3.,  2.,  1.,  4.,  3.],
         [ 4.,  5.,  6.,  7.,  1.,  2.,  3.,  4.]
         [ 8.,  9., 10., 11.,  4.,  3.,  2.,  1.]])
```

- 对张量中的所有元素求和，产生一个单元素张量

```python
X.sum()
```

#### 广播机制

某些情况下，即使形状不同，我们仍然可以通过调用广播机制来执行按元素操作：

- 通过适当复制元素来扩展一个或两个数组，以便在转换之后，两个张量具有相同的形状。
- 对生成的数组执行按元素操作。

```python
a = torch.arange(3).reshape((3,1))
b = torch.arange(2).reshape((1,2))
a + b # a 复制列，b 复制行
tensor([[0, 1],
        [1, 2],
        [2, 3]])
```

#### 索引和切片

```python
X[0:2, :] = 12 # 访问第 1，2行，:表示沿轴 1 的所有元素
```



### 线性代数

```python
import torch

x = torch.tensor(3.0)
y = torch.tensor(2.0)

# 一维张量表示向量
x.arange(4)
x.shape

# 降维
A_sum_axis0 = A.sum(axis=0)
A_sum_axis1 = A.sum(axis=1)

A.mean(), A.sum() / A.numel() //所有元素和的均值，二者结果相等

# 点积
y = torch.ones(4, dtype = torch.float32)
torch.dot(x,y)
torch.sum(x*y) // 按行元素乘法，然后求和表示两个向量点积

# 矩阵乘法
torch.mm(A,B)

# 范数
u = torch.tensor([3.0, -4.0])
torch.norm(u) # tensor(5.) L2范数

```



### 微积分

```python
def f(x):
    return 3 * x ** 2 - 4 * x

def numerical_lim(f, x, h) :
    return (f(x+h) - f(x)) / h
```



## 线性神经网络

