# 第六次作业

8.1  Explain the difference between internal and external fragmentation.

当为进程分配内存时，分配的内存块大小往往大于进程实际所需的内存量，导致分配给进程的内存块中有一部分未被进程使用，这部分未被利用的内存空间就是内碎片。

在内存中存在一些未被分配的内存空间，但由于这些空间的大小过小，无法满足任何一个进程对内存的需求，这些无法被有效利用的小块内存空间就是外碎片。

内碎片是由于静态分区或分页分配方式导致的：分配给进程的内存块是固定大小的，而进程的实际需求可能小于分配的内存，因此会产生未被利用的剩余空间。

外碎片是由于动态分区分配方式引起的：内存被划分为大小不等的区域，随着进程的分配和释放，会留下许多大小不一的空闲区域。这些空闲区域可能因为太小而无法满足后续进程对连续内存的需求。

8.3  Given five memory partitions of 100 KB, 500 KB, 200 KB, 300 KB, and 600 KB (in order), how would each of the first-fit, best-fit, and worst-fit algorithms place processes of 212 KB, 417 KB, 112 KB, and 426 KB (in order)? Which algorithm makes the most efficient use of memory?

**首次适应算法**：每次都从低地址开始查找，找到第一个能满足大小的空闲分区。

第一次分配后，分配结果如下

| 内存大小 | 需要分配内存大小 | 剩余内存 |
| -------- | ---------------- | -------- |
| 100KB    |                  |          |
| 500KB    | 212KB            | 288KB    |
| 200KB    |                  |          |
| 300KB    |                  |          |
| 600KB    |                  |          |

之后，将 500KB 这块的剩余空间也记录下来，继续分配内存，最后得到的结果为：

| 内存大小 | 需要分配内存大小 | 剩余内存 |
| -------- | ---------------- | -------- |
| 100KB    |                  |          |
| 288KB    | 112KB            | 176KB    |
| 200KB    |                  |          |
| 300KB    |                  |          |
| 600KB    | 417KB            | 183KB    |
| 无法分配 | 426KB            |          |

外碎片大小为 $100+176+200+300+183=959KB$，剩余一个 $426KB$ 的进程由于内存空间不足，无法为其分配内存

**最佳适应算法**：选择全部空闲空间中，满足作业要求且大小最小的空闲分区

| 内存大小 | 需要分配内存大小 | 剩余内存 |
| -------- | ---------------- | -------- |
| 100KB    |                  |          |
| 500KB    | 417KB            | 83KB     |
| 200KB    | 112KB            | 88KB     |
| 300KB    | 212KB            | 88KB     |
| 600KB    | 426KB            | 174KB    |

外碎片大小为$ 83+88+88+174+100=533KB$

**最差匹配算法**：选择能够存放下所需内存的最大内存块

第一次分配（分配 212KB 大小的空间）时，分配结果如下

| 内存大小 | 需要分配内存大小 | 剩余内存 |
| -------- | ---------------- | -------- |
| 100KB    |                  |          |
| 500KB    |                  |          |
| 200KB    |                  |          |
| 300KB    |                  |          |
| 600KB    | 212KB            | 388KB    |

之后，将 600KB 这块的剩余空间（388KB）也记录下来继续分配，接着分配内存，最后得到的结果为：

| 内存大小 | 需要分配内存大小 | 剩余内存 |
| -------- | ---------------- | -------- |
| 100KB    |                  |          |
| 500KB    | 417KB            | 83KB     |
| 200KB    |                  |          |
| 300KB    |                  |          |
| 388KB    | 112KB            | 276KB    |
| 无法分配 | 426KB            |          |

外碎片大小为 $83+276+600=959KB$，且由于内存空间不足，需要 $426KB $的进程无法获得内存。

综上：最佳匹配算法对内存的利用率是最高的。

8.9 . Consider a paging system with the page table stored in memory.

   a. If a memory reference takes 200 nanoseconds, how long does a paged memory reference take?

   b. If we add TLBs, and 75 percent of all page-table reference are found in the TLBs, what is the effective memory reference time?(Assume that finding a page-table entry in the TLBs takes zero time, if the entry is there)

 a. 由于一次内存访问需要 200ns，且页表位于内存中，则总共访问路径为访问页表+访问实际内容，共计访问内存两次，因此需要 400ns。

 b. 访问时间 

= **$访问 TLB 命中概率 \times(访问 TLB 时间+访问内存时间)+TLB 未命中概率\times（访问 TLB 时间+2\times访问内存时间）$**

=$75\%\times (0 + 200) + 25\% \times (0+2\times 200) $

= $250ns$

8.12  Consider the following segment table:

| Segment | Base | Length |
| ------- | ---- | ------ |
| 0       | 219  | 600    |
| 1       | 2300 | 14     |
| 2       | 90   | 100    |
| 3       | 1327 | 580    |
| 4       | 1952 | 96     |

   What are the physical addresses for the following logical addresses?

​    a. 0,430

​    b. 1,10

​    c. 2,500

​    d. 3,400

​    e. 4,112

物理内存地址 = 段号对应起始地址 + 段内偏移

通过此公式，可以得到：

a. 430<600，地址合法，物理地址为 219 + 430 = 649

b. 10<14，地址合法，物理地址为 2300+10 = 2310

c. 500>100,此地址无效

d. 400<580，地址合法，物理地址为 1327 + 400 = 1727

e. 112>96,此地址无效