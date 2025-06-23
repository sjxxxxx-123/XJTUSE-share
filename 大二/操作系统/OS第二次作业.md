# 第二次作业

**1.画出进程的5状态转换图，并说明转换原因。**

![image-20250308114203657](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250308114203657.png)

新建->就绪：进程被长调度程序选中，从磁盘中的作业队列加载到内存中的就绪队列。

就绪->运行：短调度程序从就绪队列中选择进程，为其分配 CPU 时间片，进程开始运行。

运行->阻塞：进程在运行过程中执行了阻塞操作，例如发起 I/O 请求，导致其进入阻塞状态。

运行->就绪：进程因以下原因之一从运行状态变为就绪状态：用完分配的时间片，或者在抢占式调度中，有优先级更高的进程到来。

阻塞->就绪：进程等待的 I/O 操作或其他阻塞事件完成，被唤醒并进入就绪队列。

运行->终止：进程完成其执行任务，正常结束运行。

**2.Describe the differences among short-term, medium-term, and long-term scheduling.**

三种调度的区别在于，它们让进程转变的状态不同，目的也不同。

短程调度：从就绪队列中选择一个进程，分配给 CPU 运行，主要关注 CPU 的高效利用和进程的公平性。这会让进程从就绪状态转变为运行状态。

中程调度：在进程因等待某些资源（如 I/O 操作）而被阻塞时，将其从内存中的阻塞队列调出到磁盘的休眠队列，以释放内存资源；或者在合适的时候，将休眠队列中的进程重新调入内存，使其进入就绪队列。进程从原先的状态变为挂起状态。

长程调度：从磁盘上的作业队列中选择一个或多个作业，将其调入内存，使其进入就绪队列，主要关注系统的整体性能、吞吐量和公平性。这会让进程从新建状态转化为就绪状态。

短调度发生的频率最高，中调度发生的频率次之，长调度发生的频率最低。

**3.Describe the actions taken by a kernel to context-switch between processes.**

在上下文切换过程中，内核会执行以下操作：

**保存旧进程状态**：将旧进程的进程控制块（PCB）、寄存器、栈以及进程地址空间等数据保存起来。

**读取新进程状态**：读取新进程的PCB、寄存器、栈、地址空间等数据，并将这些数据加载到CPU中。

上下文切换的过程主要由内核控制，硬件在此过程中起到辅助作用，例如保存和恢复寄存器等操作可能涉及硬件支持，但整体切换流程是由内核主导完成的。

**4.采用下述程序，确定A、B、C、D四行中pid和pid1的值。**

（假设父进程和子进程的pid分别为2600和2603）

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
 
int main()
{
  pid_t pid,pid1;
 
  pid=fork();
 
  if (pid<0)
  {
    fprintf(stderr,"fork fail");
    return 1;
  }
  else if (pid==0)
  {
      pid1=getpid();
      printf("child:pid=%d",pid);      //A
      printf("child:pid1=%d",pid1);     //B
   }
   else
   {
      pid1=getpid();
      printf("parent:pid=%d",pid);     //C
      printf("parent:pid1=%d",pid1);   //D
      wait(NULL);
   }
  return 0;
}
```

**子进程的输出：**

**A：`pid = 0`**
在子进程中，`fork()` 的返回值为 **0**，表示这是子进程。因此，`pid` 的值为 **0**。

**B：`pid1 = 2603`**
子进程调用 `getpid()` 获取自己的进程ID，其值为 **2603**。因此，`pid1` 的值为 **2603**。

**父进程的输出：**

**C：`pid = 2603`**
在父进程中，`fork()` 的返回值是子进程的PID，即 **2603**。因此，`pid` 的值为 **2603**。

**D：`pid1 = 2600`**
父进程调用 `getpid()` 获取自己的进程ID，其值为 **2600**。因此，`pid1` 的值为 **2600**。

**5.课本P104页的3.10题**

使用如图 3-33 所示的程序，请解释一下 X 和 Y 的输出是什么

```c
#include <sys/types.h>
#include <stdio.h>
#include <unistd.h>


#define SIZE 5

int nums[SIZE] = {0, 1, 2, 3, 4};

int main(){
    int i;
    pid_t pid;
    
    pid = fork();
    if (pid == 0){
        for (int i=0;i<SIZE;i++){
            nums[i] *= -i;
            printf("CHILD: %d ", nums[i]); /* LINE X */
        }
    }
    else if (pid > 0){
        wait(NULL);
        for (int i=0;i<SIZE;i++){
            printf("PARENT: %d ", nums[i]); /* LINE Y */
        }
    }
    
    return 0;
}
```

X 行产生的输出如下：CHILD: 0 CHILD: -1 CHILD: -4 CHILD: -9 CHILD: -16

Y 行产生的输出如下：PARENT: 0 PARENT: 1 PARENT: 2 PARENT: 3 PARENT: 4

X 行的输出是子进程对数组操作后的结果，而 Y 行的输出是父进程中未被修改的原始数组。这是因为 `fork()` 使父子进程的数据空间相互独立，子进程的修改不会影响父进程的数据。
