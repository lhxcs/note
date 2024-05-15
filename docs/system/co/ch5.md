# Exploiting Memory Hierarchy

## Memory Technologies

回顾计逻的知识:

- SRAM(static random access memory)
    - 数据被存在反向器上
    - very fast but takes up more space than DRAM
- DRAM
    - value is stored as a charge on capacitor(must be refreshed)
    - very small but slower than SRAM
- Flash Storage (Nonvolatile semiconductor storage)
- Disk Storage

## Memory Hierarchy Introduction

- **Temporal locality**: Items accessed recently are likely to be accessed agian soon.(instructions in a loop, induction variables)
- **Spatial locality**: Items near those accessed recently are likely to be accessed soon.(sequential instruction access, array data)

### Taking Advantage of Locality

- Store everything on disk
- Copy recently accessed (and nearby) items from disk to smaller DRAM memory (Main memory)
- Copy more recently accessed (and nearby) items from DRAM to smaller SRAM memory (Cache memory attached to CPU, 其实 cache 是在 CPU 内部的)

![](image/5.1.png)

容量不断变小，速度不断变快，单位成本越来越高。

![](image/5.2.png)

- Block: 从底下一级存储器往上一级存储器搬数据的最小单位。
- Hit: 如果我访问的数据在上一级存储器中，则称为命中。
    - Hit ratio: hits/accesses
    - Hit time: the time to access the upper level of the memory hierachy, which includes the time needed to determine whether the access is a hit or a miss.
- Miss: accessed data is absent.
    - Time taken: miss penalty (如果 miss 了，就需要从 Lower level 搬一个 block 上来，所需的时间就是 miss penalty,需要注意的是，如果 upper level 满了，还需要替换掉一个 block)
    - Miss ratio: misses/accesses = 1 - hit ratio

![](image/5.3.png)

接下来讨论两个概念：

- The basics of Cache: SRAM and DRAM, 解决速度的问题
- Vitural Memory: DRAM and DISK, 解决容量的问题

## The basics of Cache

对于内存中的每个位置，cahce 里有唯一的位置分配，这意味着 lots of items at the lower level share locations in the upper level.

因此我们要解决两个问题：

- How do we know if a data item is in the cache?
- If it is, how do we find it?

### Direct Mapped Cache

![](image/5.4.png)

将内存地址对 cache 中的 block 数取模，得到对应 cache 中的位置。

我们首先要知道 cache 中某一块是否为空，因此设置一个 valid bit， 0 代表空。
我们要怎么知道 cache 中某一块是内存中的哪个 block？

- Store block address as well as the data(onlu need the high-order bits), called the tag.

由于取模的特性，内存地址的低位信息已经由 cache 中的 Block location 表示，因此我们存储 data 时只需要存地址的高位信息即可。

![](image/5.5.png)

内存中的地址都是以字节为单位的，而 block address 都是以 block 为单位的。由于一个 block 总是 2 的若干次方个 word 那么大， 而每个 word 是 4 Byte，因此每个 block 的 byte 数也是 2 的若干次方。因此，我们只需要去掉 byte address 的后几位，就可以获得它的 block address 了。这样相邻的 2 的若干次方个 byte 就会聚合成一个 block 了，因为它们的 byte address 的前若干位，即 block address，是相同的。

因此，我们将 byte address 分为 2 个部分：block address 和 byte offset，即所在 block 的编号以及在 block 中的偏移量 (in byte)；而 block address 又分为了两个部分，即 tag 和 index。

![](image/5.6.png)

