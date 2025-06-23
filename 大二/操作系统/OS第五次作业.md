# OS 第五次作业

7.1  Consider the traffic deadlock depicted in Figure 7.10.

   a. Show that the four necessary conditions for deadlock indeed hold in this example.

   b. State a simple rule for avoiding deadlocks in this system.

a.

互斥

在路口处，车辆的通行是相互排斥的。也就是说，在同一时刻，十字路口只能允许一辆车通过，其他车需要等待。

占有并保持

当出现堵车情况时，车辆在通过路口的过程中，可能会停留在路口位置，这就使得其他车辆无法正常通过路口。

非抢占

如果一辆车正在十字路口通行，其他车辆无法强制让这辆车退出路口，自己抢先通过。

循环等待

车辆在四个十字路口的通行形成了一个循环的等待关系。每辆车都在等待其他车辆先通过路口，从而构成了一个等待环路。

b.

打破循环等待条件：在四个十字路口安装红绿灯，红绿灯的设置规则是每次只允许车辆在横向或纵向方向上同时通行。即使某一方向的直行信号变为红灯，另一方向的直行车辆也必须等待该方向的车辆全部驶离路口后，才能进入路口。

7.11  Consider the following snapshot of a system:

|      | *Allocation*   A B C D | *Max*   A B C D | *Available*   A B C D |
| ---- | ---------------------- | --------------- | --------------------- |
| P0   | 0012                   | 0012            | 1520                  |
| P1   | 1000                   | 1750            |                       |
| P2   | 1354                   | 2356            |                       |
| P3   | 0632                   | 0652            |                       |
| P4   | 0014                   | 0656            |                       |

Answer the following questions using the banker’s algorithm:

 a. What is the content of the matrix Need?

由
$$
Need=Max-Allocation
$$
可得需求矩阵的内容为：

|      | *Need*    A B C D |
| :--: | :---------------: |
|  P0  |       0000        |
|  P1  |       0750        |
|  P2  |       1002        |
|  P3  |       0020        |
|  P4  |       0642        |

 b. Is the system in a safe state?

当前系统是安全的，推导过程如下：

- P0 不需要任何额外资源，因此可以直接结束并释放持有的资源。P0 完成后剩余可用资源如下：

  |                | Need ABCD | Available ABCD |
  | :------------: | :-------: | :------------: |
  | P0（finished） |           |      1532      |
  |       P1       |   0750    |                |
  |       P2       |   1002    |                |
  |       P3       |   0020    |                |
  |       P4       |   0642    |                |

- 此时，剩余资源足够满足 P2 的所有需求，因此 P2 可以完成。P2 释放资源后剩余资源如下：

  |                | Need ABCD | Available ABCD |
  | :------------: | :-------: | :------------: |
  | P0（finished） |           |      2886      |
  |       P1       |   0750    |                |
  | P2（finished） |           |                |
  |       P3       |   0020    |                |
  |       P4       |   0642    |                |

- 此时，剩余资源足够满足 P3的所有需求，因此 P3 可以完成。P3 释放资源后剩余资源如下：

  |                | Need ABCD | Available ABCD |
  | :------------: | :-------: | :------------: |
  | P0（finished） |           |   2 14 11 8    |
  |       P1       |   0750    |                |
  | P2（finished） |           |                |
  | P3（finished） |           |                |
  |       P4       |   0642    |                |

- 此时，剩余资源足够满足 P4 的所有需求，因此 P4 可以完成。P4 释放资源后剩余资源如下：

  |                | Need ABCD | Available ABCD |
  | :------------: | :-------: | :------------: |
  | P0（finished） |           |   2 14 12 12   |
  |       P1       |   0750    |                |
  | P2（finished） |           |                |
  | P3（finished） |           |                |
  | P4（finished） |           |                |

- 此时，剩余资源足够满足 P1 的所有需求，因此 P1 可以完成。

  综上，当前存在安全序列 (P0, P2, P3, P4, P1)，因此当前系统是安全的

 c. If a request from process P1 arrives for (0,4,2,0), can the request be granted immediately?

假设系统接受了此请求，则剩余资源如下：

|      | *Allocation* A B C D | *Max*   A B C D | *Need*  ABCD | *Available*   A B C D |
| :--: | :------------------: | :-------------: | :----------: | :-------------------: |
|  P0  |         0012         |      0012       |     0000     |         1100          |
|  P1  |         1420         |      1750       |     0330     |                       |
|  P2  |         1354         |      2356       |     1002     |                       |
|  P3  |         0632         |      0652       |     0020     |                       |
|  P4  |         0014         |      0656       |     0642     |                       |

此时，P0 进程可以完成，完成后资源剩余如下：

|                | *Allocation*   A B C D | *Max*   A B C D | *Need*  ABCD | *Available*  A B C D |
| :------------: | :--------------------: | :-------------: | :----------: | :------------------: |
| P0（finished） |                        |                 |              |         1112         |
|       P1       |          1420          |      1750       |     0330     |                      |
|       P2       |          1354          |      2356       |     1002     |                      |
|       P3       |          0632          |      0652       |     0020     |                      |
|       P4       |          0014          |      0656       |     0642     |                      |

此时，P2 进程可以完成，完成后剩余资源如下：

|                | *Allocation* A B C D | *Max*   A B C D | *Need*  ABCD | *Available*   A B C D |
| :------------: | :------------------: | :-------------: | :----------: | :-------------------: |
| P0（finished） |                      |                 |              |         2466          |
|       P1       |         1420         |      1750       |     0330     |                       |
| P2（finished） |                      |                 |              |                       |
|       P3       |         0632         |      0652       |     0020     |                       |
|       P4       |         0014         |      0656       |     0642     |                       |

此时，进程 P1可以完成，完成后剩余资源如下：

|                | *Allocation* A B C D | *Max*   A B C D | *Need*  ABCD | *Available*   A B C D |
| :------------: | :------------------: | :-------------: | :----------: | :-------------------: |
| P0（finished） |                      |                 |              |         3886          |
| P1（finished） |                      |                 |              |                       |
| P2（finished） |                      |                 |              |                       |
|       P3       |         0632         |      0652       |     0020     |                       |
|       P4       |         0014         |      0656       |     0642     |                       |

进程 P3可以完成，完成后剩余资源如下：

|                | *Allocation* A B C D | *Max*   A B C D | *Need*  ABCD | *Available*   A B C D |
| :------------: | :------------------: | :-------------: | :----------: | :-------------------: |
| P0（finished） |                      |                 |              |       3 14 11 8       |
| P1（finished） |                      |                 |              |                       |
| P2（finished） |                      |                 |              |                       |
| P3（finished） |                      |                 |              |                       |
|       P4       |         0014         |      0656       |     0642     |                       |

最后，进程 P4 可以完成：

|                | *Allocation* A B C D | *Max*   A B C D | *Need*  ABCD | *Available*   A B C D |
| :------------: | :------------------: | :-------------: | :----------: | :-------------------: |
| P0（finished） |                      |                 |              |      3 14 12 12       |
| P1（finished） |                      |                 |              |                       |
| P2（finished） |                      |                 |              |                       |
| P3（finished） |                      |                 |              |                       |
|       P4       |                      |                 |              |                       |

因此，存在安全序列（P0，P2，P1，P3，P4），因此系统处于安全状态，从而可以批准 P1 的资源需求。