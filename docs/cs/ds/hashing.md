# Hashing

## General Idea

哈希表，又称散列表，通过建立键`key`与值`value`之间的映射，实现高效的元素查询。

我们向哈希表输入一个键`key`,则可以在$O(1)$时间内获取对应的值`value`。

哈希表中进行增删改查的时间复杂度都是$O(1)$。

![](image/7.1.png)

- 哈希函数的作用是将一个较大的输入空间映射到一个较小的输出空间。在哈希表中，输入空间是所有 `key` ，输出空间是所有桶（数组索引）。即输入`key`我们可以通过哈希函数得到该`key`对应的键值对在数组中的存储位置。
- 负载因子：哈希表元素数量除以桶数量。用于衡量哈希冲突的严重程度，**也常作为哈希表扩容的触发条件**。

**collision**: A collision occurs when we hash two nonidentical identifiers into the same bucket.

**overflow**: An overflow occurs when we hash a new identifier into a full bucket.



## Hash Function

### Properties of $f$:

- must be easy to compute and minimizes the number of collisions.
- should be unbiased. That is, for any $x$ and any $i$, we have the Probability $(f(x)=i)=\frac{1}{b}$. Such kind of a hash function is called a uniform hash function.

### 哈希算法的设计

- 加法哈希：对输入的每个字符的ASCII码进行相加，将得到的总和作为哈希值。
- 乘法哈希：利用乘法的不相关性，每轮乘以一个常数，将各个字符的 ASCII 码累积到哈希值中。
- 异或哈希：将输入数据的每个元素通过异或操作累积到一个哈希值中。

**使用大质数作为模数，可以最大化地保证哈希值的均匀分布**。因为质数不与其他数字存在公约数，可以减少因取模操作而产生的周期性模式，从而避免哈希冲突。