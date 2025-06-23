# OS 第七次作业

9.4  A certain computer provides its users with a virtual-memory space of $2^{32}$ bytes. The computer has $2^{18}$ bytes of physical memory. The virtual memory is implemented by paging, and the page size is 4,096 bytes. A user process generates the virtual address 11123456H. Explain how the system establishes the corresponding physical location. Distinguish between software and hardware operations.

虚拟地址为 11123456H，由于页面大小为 $4096=2^{12}$字节，可以将此地址划分为页号和页内偏移两部分：

后 3 位（二进制的后 12 位）456H 为页内偏移，前 5 位（二进制的前 20 位）11123H 为页号

由于计算机的主存有 $2^{18}$字节（256KB），因此主存总共包含 $2^{18}/2^{12}=2^6=64$ 帧

查询页表中页号为 11123H 的表项对应的内存块号，如果不存在表项，终止当前用户进程；如果存在，则取得块号后，计算内存块号+页内偏移，得到该地址对应的物理地址。

分隔页号和页内偏移、查询内存块号和计算组合地址通常是硬件完成的；操作系统负责处理用户进程访问内存的请求，并告诉硬件需要转换地址。

9.10  Consider a demand-paging system with the following time-measured utilizations:

| CPU utilization   | 20%   |
| ----------------- | ----- |
| Paging disk       | 97.7% |
| Other I/O devices | 5%    |

For each of the following, say whether it will (or is likely to) improve CPU utilization. Explain your answers.

从各部分的利用率来看，CPU利用率较低，而分页磁盘利用率接近饱和。这表明系统可能正处于抖动状态。当前的内存容量不足以容纳所有正在运行的程序，导致频繁地在内存和磁盘之间进行页面交换，从而使得CPU大部分时间都在等待页面调入，进而导致CPU利用率下降。

a. Install a faster CPU.

否。提升CPU的速度大概率无法提高CPU利用率，因为当前系统的瓶颈在于内存不足，而不是CPU性能不足。现有的CPU性能已经足够应对程序的处理需求。

b. Install a bigger paging disk.

否。扩大磁盘分页区的容量不太可能提升CPU利用率，因为这无法解决内存不足导致的频繁内存与磁盘交换的根本问题。

c. Increase the degree of multiprogramming.

否。增加多道程序的数量不仅不能提高CPU利用率，反而可能使CPU利用率进一步下降。因为当内存中加载更多程序后，抖动现象会更加严重，程序在页面交换上耗费的时间也会更多，从而导致CPU利用率持续降低。

d. Decrease the degree of multiprogramming.

是。降低多道程序的数量可能会提升CPU利用率。当减少内存中运行的程序数量后，抖动现象会得到缓解，进程在页面交换上耗费的时间减少，从而能够更多地利用CPU进行实际的计算任务。

e. Install more main memory.

是。扩大主存容量可以提高CPU利用率。当主存容量增加后，能够容纳更多的程序和页面，从而减少因内存不足导致的抖动现象。这样一来，进程在CPU上执行的时间就会增加，CPU利用率也会随之提升。这是本质的解决方案。

f. Install a faster hard dist or multiple controllers with mutiple hard disks.

是。安装更快的磁盘可以提高CPU利用率。因为更快的磁盘能够加速主存和磁盘之间的页面交换速度，从而减少进程等待页面调入的时间，使得CPU可以更快地获取到需要的数据并继续执行，进而提高CPU利用率。

g. Add prepaging to the page-fetch algorithms.

是。增加预取操作可以提高CPU利用率。预取操作可以在进程需要页面之前提前将页面从磁盘加载到主存中，从而减少进程在请求页面时的等待时间。这样，CPU能够更快地获取到所需的页面并继续执行，减少了因页面调入延迟而导致的CPU空闲时间，进而提高CPU利用率。

h. Increase the page size.

不一定。增加页面大小可能增加 CPU 利用率，也可能减少 CPU 利用率。如果大部分请求空间局部性明显，那么页面大小增加后，每页包含需要的地址的概率更大，因此请求调页次数更少。但如果大部分请求空间局部性不明显，那么每次 I/O 的开销会更大，CPU的利用率会进一步降低。

  

9.13  A page-replacement algorithm should minimize the number of page faults. We can achieve this minimization by distributing heavily used pages evenly over all of memory, rather than having them compete for a small number of page frames. We can associate with each page frame a counter of the number of pages associated with that frame. Then, to replace a page, we can search for the page frame with the smallest counter.

a. Define a page-replacement algorithm using this basic idea. Specifically address these problems:

1. What the initial value of the counters is

   计数器的初始值应当为 0

2. When counters are increased

   当一个新页面被加载到某个帧时，该帧的计数器需要加1。

3. When counters are decreased

   当发生缺页且已经与此帧关联的某页再也不会被使用后，该帧的计数器需要减1。

4. How the page to be replaced is seleced

   选择计数器最少的帧，将其存储的页面替换；如果有多个页面的计数器都是最少的，就替换第一个位置的页面。如果一个页面被重新加载到同一个帧中，计数器的值不应发生变化。

b. How many page faults occur for your algorithm for the following reference string, with four page frames?

​        1,2,3,4,5,3,4,1,6,7,8,7,8,9,7,8,9,5,4,5,4,2.

页面分配如下：

| 输入 | 1    | 2    | 3    | 4        | 5    | 3    | 4        | 1        | 6        | 7        | 8    | 7    | 8        | 9    | 7    | 8    | 9        | 5    | 4    | 5    | 4        | 2    |
| ---- | ---- | ---- | ---- | -------- | ---- | ---- | -------- | -------- | -------- | -------- | ---- | ---- | -------- | ---- | ---- | ---- | -------- | ---- | ---- | ---- | -------- | ---- |
| 1    | 1    | 1    | 1    | <u>1</u> | 5    | 5    | 5        | 5        | <u>5</u> | 7        | 7    | 7    | 7        | 7    | 7    | 7    | 7        | 7    | 7    | 7    | <u>7</u> | 2    |
| 2    |      | 2    | 2    | 2        | 2    | 2    | <u>2</u> | 1        | 1        | 1        | 1    | 1    | <u>1</u> | 9    | 9    | 9    | 9        | 9    | 9    | 9    | 9        | 9    |
| 3    |      |      | 3    | 3        | 3    | 3    | 3        | <u>3</u> | 6        | <u>6</u> | 8    | 8    | 8        | 8    | 8    | 8    | <u>8</u> | 5    | 5    | 5    | 5        | 5    |
| 4    |      |      |      | 4        | 4    | 4    | 4        | 4        | 4        | 4        | 4    | 4    | 4        | 4    | 4    | 4    | 4        | 4    | 4    | 4    | 4        | 4    |

<u>下划线</u>代表被换出的页面

共出现 12 次页面错误

c. What is the minimum number of page faults for an optimal page-replacement strategy for the reference string in part b with four page frames?

最优算法是OPT，因此其替换过程如下：

| 输入 | 1    | 2    | 3    | 4        | 5    | 3    | 4    | 1        | 6        | 7        | 8    | 7    | 8        | 9    | 7    | 8    | 9    | 5        | 4    | 5    | 4        | 2    |
| ---- | ---- | ---- | ---- | -------- | ---- | ---- | ---- | -------- | -------- | -------- | ---- | ---- | -------- | ---- | ---- | ---- | ---- | -------- | ---- | ---- | -------- | ---- |
| 1    | 1    | 1    | 1    | 1        | 1    | 1    | 1    | <u>1</u> | 6        | <u>6</u> | 8    | 8    | 8        | 8    | 8    | 8    | 8    | <u>8</u> | 4    | 4    | 4        | 4    |
| 2    |      | 2    | 2    | <u>2</u> | 5    | 5    | 5    | 5        | 5        | 5        | 5    | 5    | 5        | 5    | 5    | 5    | 5    | 5        | 5    | 5    | 5        | 5    |
| 3    |      |      | 3    | 3        | 3    | 3    | 3    | 3        | <u>3</u> | 7        | 7    | 7    | 7        | 7    | 7    | 7    | 7    | 7        | 7    | 7    | <u>7</u> | 2    |
| 4    |      |      |      | 4        | 4    | 4    | 4    | 4        | 4        | 4        | 4    | 4    | <u>4</u> | 9    | 9    | 9    | 9    | 9        | 9    | 9    | 9        | 9    |

一共出现过 11 次页面错误。