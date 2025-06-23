# OS 第八次作业

10.1  Consider a file system where a file can be deleted and its disk space reclaimed while links to that file still exist. What problems may occur if a new file is created in the same storage area or with the same absolute path name? How can these problems be avoided?

如果一个文件被删除且其存储空间被回收，但指向该文件路径的链接仍然存在，那么当创建一个与被删除文件同名的新文件时，原本指向被删除文件的链接会错误地指向新创建的文件。

解决方案：维护一个「指向文件的链接」的记录表。在删除文件时，根据该表的内容，同步删除所有指向该文件的链接，而不是保留它们。

11.1  Consider a file system that uses a modified contiguous-allocation scheme with support for extents. A file is a collection of extents, with each extent corresponding to a contiguous set of blocks. A key issue in such system is the degree of variability in the size of the extents. What are the advantages and disadvantages of the following schemes?

​    a. All extents are of the same size, and the size is predetermined.

​    b. Extents can be of any size and are allocated dynamically.

​    c. Extents can be of a few fixed sizes, and these sizes are predetermined.

a. 

优点

与静态分配类似，所有扩展块的大小一致，因此不会产生外碎片，分配过程简单。

缺点

容易出现内碎片，因为文件大小可能无法完美匹配扩展块的大小，导致部分空间浪费。此外，这种方案不够灵活，难以适应不同大小的文件需求。

b. 

优点

类似动态分配，每个扩展块的大小可以根据文件的实际需求动态调整，因此不会产生内碎片，空间利用率更高，分配方式更加灵活。

缺点

容易出现外碎片，因为不同大小的扩展块会导致磁盘空间的碎片化。此外，用于记录扩展块分配情况的数据结构会变得复杂，管理成本较高。

c. 

优点

由于扩展块有几种固定的大小可供选择，文件存储时可以选择最接近需求的最小块，从而减少内碎片的产生，相比方案a，内碎片的大小会更小。

缺点

块大小是固定的，仍然可能存在内碎片。灵活性和复杂性介于方案a和方案b之间，虽然比方案a更灵活，但不如方案b灵活；同时管理复杂度也介于两者之间。

11.2  What are the advantages of the variant of linked allocation that uses a FAT to chain together the blocks of a file?

FAT（文件分配表）的优点在于：通过顺序访问FAT表本身，无需直接访问磁盘中的文件数据，就可以快速获取文件某个块的地址，同时也能快速计算出文件所占用的块数量。

与隐式链接相比，FAT在计算文件大小和查找文件特定块地址时更具优势。由于FAT表通常驻留在内存中，因此在操作过程中无需频繁访问磁盘，从而大大减少了时间消耗。

11.3  Consider a system where free space is kept in a free-space list.

​    a. Consider a file system similar to the one used by UNIX with indexed allocation. How many disk I/O operations might be required to read the contents of a small local file at /a/b/c? Assume that none of the disk blocks is currently being cached.

​    b. Suggest a scheme to ensure that the pointer to the free space list is never lost as a result of memory failure.

a. 需要四次磁盘访问才能读取到文件：

第一次访问磁盘，读取根目录（`/`）文件，查找`/a`目录的物理位置。

第二次访问磁盘，读取`/a`目录下的目录文件，获取`/a`路径下子目录的物理地址。

第三次访问磁盘，读取`/b`目录下的目录文件，查找`/a/b`路径下文件的物理地址。

第四次访问磁盘，读取实际的文件`/a/b/c`。

b. 可以在磁盘中保留一份空闲空间链表的副本。在分配或回收空闲空间时，先更新磁盘中的副本，然后再修改内存中的链表。