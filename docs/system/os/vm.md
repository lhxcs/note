# 内存设计

## 内存基础设计

### 内存保护

每一个进程在内存中都应当有一块连续的内存空间，而单个进程应当只能访问自己的内存空间，这就是内存保护的基本要求。

我们引入 base 和 limit 两个寄存器来实现框定进程的内存空间。当前进程的内存空间始于 base 寄存器中存储的地址，终于 base+limit 对应的地址。这两个寄存器只能由内核通过特定的特权指令来修改。内存管理单元 （MMU）会在每次访问内存时检查访问的地址是否在 base 和 limit 寄存器所定义的范围内，如果不在，会产生一个异常终端程序的执行。

### 地址绑定

静态代码程序转化成动态的进程的步骤：

- compile time: compiler 将代码中的 symbol 转为 relocatable addresses
- loda time: relocatable addresses 转化为 absolute addresses
- execution time: 如果进程在 execution time 时，允许被移动，那么可能从 relocatable addresses 转为 absolute addresses 这一步就需要延迟到 execution time 来执行(?)

内存分为三种：

- 符号地址(symbolic addresses)
- 可重定位地址(relocatable addresses)
- 绝对地址(absolute addresses)

### 动态装载

如果一个例程还没有被调用，那么它会以可重定位装载格式存储在磁盘上，当它被调用时，就动态被装在到内存中。



## 分页技术

想要解决的问题是：系统必须使用连续内存的限制。需要使用连续内存是需要一种逻辑上的连续，因此在物理地址和虚拟地址的语境下，我们只需要保证虚拟地址是连续的即可。

### 基本设计

过于稀碎的物理地址会导致内存访问缓慢！

- 帧 & 页
    - 我们将物理内存划分为固定大小的块，称为帧(frames)，每个帧对应虚拟地址中等大的一块页 (pages)
    - 用帧来作为连续的虚拟地址的物理基础，用虚拟的页号来支持连续虚拟地址。
    - 帧与页的对应关系是通过页表来实现的。
- 每个进程都应当有自己的页表，我们称页表是 per-process data structures.