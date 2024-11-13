# Virtual Memory

## Introduction

代码需要在内存里执行，但是整个程序几乎不会同时被使用。比如我们的代码在 3 页里，最开始执行时只有第一页在内存里，后面的页需要在需要的时候才会被加载到内存里。即我们可以把还没用到的 code 和 data 延迟加载到内存里，用到时再加载。

Consider ability to execute partially-loaded program:

- program no longer constrained by limits of physical memory.
- programs could be larger than physical memory

需要注意的是虚拟地址只是范围，并不能真正的存储数据，数据只能存在物理空间里。

## Demand Paging

按需换页，只把被需要的页载入内存。（当我们需要读写这个页的时候，说明我们需要这个页，这个时候再把这个页调入内存。）

- if page is invalid (error) -> abort the operation.
- if page is valid but not in memory -> bring it to memory.
    - memory heres means physical memory!
    - this is called **page fault**

### What causes page fault?

![](image/7.1.png)

以 C 语言中的 malloc 为例，malloc 会调用 `brk()`. 增长堆的大小。
VMA 是 Virtual Memory Area，`brk()` 只是增大了 VMA 的大小（修改 vm_end），但是并没有真正的分配内存，只有当我们真正访问这个地址的时候，会触发 page fault，然后找一个空闲帧真正分配内存，并做了映射。

user space 的访存，MMU 引起的 page fault.

![](image/7.2.png)

### Who handles page fault?

有两种情况：

- 地址本身超过了 `vma` 的范围，或者落在 heap 里但权限不对，这种情况操作系统会杀死进程。
- 落在 heap 里，权限正确，这个时候 os 就分配一个空闲帧，然后把这个页映射到这个帧上。

![](image/7.3.png)

每个 fault 过来先查是否在 `vm_start`, `vm_end` 之间，以及查权限。如果落在 `vm_area_struct` 中间，那么就是 segmentation fault. 为了判断地址是否落在 vma 里，linux 使用红黑树来加速查找。 

![](image/7.4.png)

首先 MMU 找 page table 发现 invalid, 之后引发 trap, OS 在 physical memory 中找到一个空闲的帧，把需要的内容从磁盘搬过来，然后修改 Page table, 最后重新开始 instruction, MMU 重新开始找 page table.

![](image/7.5.png)

- swapper
    - lazy swapper: never swaps a page in memory unless it will be needed.
    - pre-paging: pre-page all or some of pages a process will need, before they are referenced.
        - it can reduce the number of page faults during execution.
        - if pre-pages are unused, I/O and memory was wasted.

在被需求之前页不会被载入内存，只有在内存被需求后才会被载入内存，我们称之为纯按需换页(pure demand paging)

- get free frame
    - most operating systems maintain a **free-frame list**: a pool of free frames for satisfying such requests.

![](image/7.6.png)

![](image/7.7.png)

![](image/7.8.png)

读硬盘的时候很慢，所以换进程，把 CPU 给别人，context_switch

![](image/7.9.png)

### Demanding Paging Optimizations

![](image/7.10.png)

### Copy-on-Write

allows parent and child processes to initially share the same pages in memory.

提升 fork 效率，最开始页都是共享的，只有当父进程或子进程修改了页的内容时，才会真正为修改的页分配内存。

![](image/7.11.png)