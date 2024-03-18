# 摊还分析

在摊还分析中，我们求数据结构的一个操作序列中所执行的所有操作的平均时间，来评价操作的代价。这样我们就可以说明一个操作的平均代价很低，即使序列中某个单一操作的代价很高。

摊还分析不涉及概率，它可以保证最坏情况下每个操作的平均性能。

## 聚合分析

利用聚合分析，我们证明对所有 $n$ ,一个 $n$ 个操作的序列*最坏情况*下花费的总时间为$T(N)$。因此在最坏情况下，每个操作的平均代价(摊还代价)为 $\frac{T(n)}{n}$。

!!! note

    聚合分析得到的摊还代价适用于每个操作，核算法和势能法对不同类型的操作可能赋予不同的摊还代价。



> Suppose we perform a sequence of $n$ operations on a data structure in which the $i$ th operation costs $i$ if $i$ is an exact power of 2, and 1 otherwise. Use aggregate analysis to determine the amortized cost per operation.

??? Question "Answer"
    
    Suppose the cost of operation $i$ be $c(i)$. Then we have:

    $$\sum_{i=1}^n c(i) = \sum_{i=1}^{lgn} 2^i + \sum_{i\le n is\,not\,a\,power\,of\,2}1 \le 3n \in O(n).$$

## 核算法

用核算法进行摊还分析时，我们对不同操作赋予不同费用，赋予某些操作的飞跃可能多于或少于其实际代价。
我们将赋予一个操作的费用称为它的**摊还代价**。
当一个操作的摊还代价超出其实际代价时，我们将差额存入数据结构中的特定对象，存入的差额称为**信用**(credits)。对于后续操作中摊还代价小于实际代价的情况，信用可以用来支付差额。

我们应该确保操作序列的总摊还代价给出序列总真实代价的上界。用$c_i$表示第$i$个操作的真实代价，用$\hat{c_i}$表示摊还代价，则对任意$n$个操作的序列，要求：
$$
\sum_{i=1}^n\hat{c_i} \ge \sum_{i=1}^n c_i
$$

!!! note

    如果在某个步骤我们允许信用为负值，那么当时的总摊还代价就会低于总实际代价，对于到那个时刻终止的操作序列，总摊还代价就不是实际代价的上界了。

!!! Example "栈操作"

    除了PUSH, POP之外，定义操作MULTIPOP(S,k):
    ```
    MULTIPOP(S,k)
        while not STACK-EMPTY(S) and k>0:
            POP(S)
            k=k-1
    ```
    
    其中PUSH, POP的实际代价为 1， MULTPOP的实际代价为 min(k,s)。
    我们为这些操作赋予摊还代价：PUSH 2, POP 0, MULTIPOP 0。
    
    假定使用 1 美元来表示一个单位的代价，从空栈开始，用 1 美元支付压栈操作的实际代价，将剩余的 1 美元存作信用。
    对每个盘子存储的 1 美元，实际上是作为将来它被弹出栈时代价的预付费。


## 势能法

在势能法中，我们将信用表示为势能，并且将势能与整个数据结构关联起来。

我们对一个初始数据结构 $D_0$ 执行 $n$ 个操作。令 $c_i$ 为第 $i$ 个操作的实际代价，令 $D_i$ 为在数据结构 $D_{i-1}$ 上执行第 $i$ 个操作得到的结果数据结构。
势函数 $\Phi$ 将每个数据结构映射到一个实数 $\Phi(D_i)$ , 此值即为关联到数据结构 $D_i$ 的势。
第 $i$ 个操作的摊还代价 $\hat{c_i}$ 定义为：
$$
\hat{c_i} = c_i + \Phi(D_i) - \Phi(D_{i-1})
$$
即每个操作的摊还代价等于其实际代价加上此操作引起的势能变化。因此 $n$ 个操作的总摊还代价:
$$
\sum_{i=1}^{n}\hat{c_i} = \sum_{i=1}^n(c_i+\Phi(D_i)-\Phi(D_{i-1})) = \sum_{i=1}^nc_i + \Phi(D_n) - \Phi(D_0)
$$

如果能定义一个势函数 $\Phi$, 使得 $\Phi(D_n)\ge\Phi(D_0)$, 那么总摊还代价给出了实际代价的一个上界。
但是我们不知道总共要求执行多少个操作，因此我们要求对所有 $i$, $\Phi(D_i)\ge\Phi(D_0)$。

!!! Example "栈操作"

    定义势函数为栈中对象的数量。满足$\Phi(D_i)\ge\Phi(D_0)$。
    计算不同操作的摊还代价:

    - PUSH: 势差 $Phi(D_i) - \Phi(D_{i-1}) = 1$, 摊还代价 $\hat{c_i} = c_i + Phi(D_i) - \Phi(D_{i-1}) = 2$
    - MULTIPOP: 势差 $Phi(D_i) - \Phi(D_{i-1}) = -min(k,s)$, 摊还代价 $\hat{c_i} = c_i + Phi(D_i) - \Phi(D_{i-1}) = 0$
    - POP：同MULTIPOP，0
    因此$n$个操作总摊还代价为$O(n)$。

