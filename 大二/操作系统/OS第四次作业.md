# 第四次作业

6.9  Show that, if the wait() and signal() semaphore operations are not executed atomically, then mutual exclusion may be violated.

wait 操作即为 P 操作，signal 操作即为 V 操作。如果两个操作不是原子的话，那么按照如下顺序执行时，可能出现问题：

假设初始时信号量**S**的值为1

1. **P1开始执行wait(S)**：

   检查S的值为1，但尚未将S减1。

2. **P2开始执行wait(S)**：

   同样检查S的值为1，也尚未将S减1。

3. **P1和P2同时将S减1**：

   S的值变为0，但此时P1和P2都认为自己已经成功获取了资源，双方都进入了临界区。

4. **P1和P2同时在临界区执行**：

   这导致了互斥被破坏，两个进程同时访问了共享资源，可能引发数据不一致或其他问题。

6.11  The Sleeping-Barber Problem. A barbershop consists of a waiting room with n chairs and abarber room with one barber chair. If there are no customers to be served, the barber goes to sleep. If a customer enters the barbershop and all chairs are occupied, then the customer leaves the shop. If the barber is busy but chairs are available, the customer sits in one of the free chairs. If the barber is asleep, the customer wakes up the barber. Write a program to coordinate the barber and the customers.

 设立四个信号量：customer=0，barber=1，mutex=1，设置共享变量 waiting = 0（表示当前等待人数），假设店里有 n 把椅子

Customer 执行的操作如下：

```
P(mutex)

if waiting > n 

V(mutex);     exit

else 

waiting +=1;    V(mutex)

V(customer)

P(barber)

理发
```

Barber 执行的操作如下：

```
while(1):
P(customer)

P(mutex)

waiting -= 1

V(mutex)

理发

V(barber)
```



1. 有一个系统，定义 P 、 V 操作如下：

```piantext
P(s):

s:=s-1;

if s<0 then

将本进程插入相应队列末尾等待；

V(s):

s:=s+1;

if s<=0 then

从相应等待队列队尾唤醒一个进程，将其插入就绪队列；
```

问题：

这样定义 P **、** V 操作是否有问题？

用这样的 P **、** V 操作实现 N 个进程竞争使用某一共享变量的互斥机制。

有问题。从队尾唤醒进程可能会导致不公平调度，因为后来到达的进程可能会比早期到达的进程更早被唤醒和服务。这种行为违背了先进先出（FIFO）的原则，可能导致某些进程长时间等待，从而引发饥饿问题。 

标准的 V 操作应该如下所示：

```
V(s):
    s := s + 1;
    if s <= 0 then
        从相应等待队列队首唤醒一个进程，将其插入就绪队列；
```

这样可以确保先进先出的调度原则，避免饥饿问题。

可以设置 N-1 个上述定义的信号量：$S_1, S_2, ..., S_{n-1}$，它们的初始值分别为 1，2，3，……，n-1。每个进程访问时，就由大到小依次对这些信号量做 P 操作；访问完成后，按编号从小到大对这 n-1 个信号量做 V 操作。

下面以 3 个进程互斥访问为例，说明此方法的正确性。假设三个进程为 P1, P2, P3

|  P1   |  P2   |  P3   |
| :---: | :---: | :---: |
| P(S1) | P(S2) |       |
| read  | P(S1) | P(S2) |
| read  |       |       |
| read  |       |       |
| V(S1) | read  |       |
|       | V(S1) |       |
|       | V(S2) | P(S1) |
|       |       | read  |
|       |       | V(S1) |
|       |       | V(S2) |

2.  第二类读者写者问题：写者优先

条件：

多个读者可以同时进行读

写者必须互斥（只允许一个写者写，也不能读者写者同时进行）

写者优先于读者（一旦有写者，则后续读者必须等待，唤醒时优先考虑写者）

```c
//初始化
mutex=1;//控制互斥访问数据区
Rmutex=1;//用于读者互斥访问Rcount
Wmutex=1;//用于写者互斥访问Wcount
readable=1;//用于表示当前是否有写者
Rcount=0,Wcount=0;//记录读者和写者的数量
//读者操作
reader
	P(readable);//检查是否有写者，没有才进入，反映了写者优先的原则
	P(Rmutex);//保护Rcount
	if(Rcount==0)
		P(mutex);//第一个读者，占用数据区
	Rcount++;
	V(Rmutex);//允许其他读者读
	V(readable);//释放readable
	read;
	P(Rmutex);//保护Rcount
	Rcount--;
	if(Rcount==0)
		V(mutex);//释放数据区
	V(Rmutex);//允许其他读者读 
//写者操作
writer
	P(Wmutex);//准备修改Wcount
	if(Wcount==0)
		P(readable);//第一个写者，组织后续读者进入
	Wcount++;//写者数+1
	V(Wmutex);//释放，允许其他写者修改Wcount
	P(mutex);//等当前的读者或写者完成时，占用数据区
	写操作；
	V(mutex);//写完，释放数据区
	p(Wmutex);//准备修改Wcount
	Wcount--;
	if(Wcount==0)
		V(readable==0);//若为最后一个写者，则允许读者进入
	V(Wmutex);
```

3. 把学生和监考老师都看作进程 **,**  学生有 N 人 **,**  教师 1 人 **.**  考场门口每次只能进出一个人 **,**  进考场原则是先来先进 **.**  当 N 个学生都进入考场后 **,**  教师才能发卷子 **.**  学生交卷后可以离开考场 **.**  教师要等收上来全部卷子并封装卷子后才能离开考场。

   问题 **:**

   问共需设置几个进程 ?

   试用 P **、** V 操作解决上述问题中的同步和互斥关系。
   
   共需要设置两类进程：学生进程和老师进程。
   
   设置信号量如下：
   
   **`mutex`**：用于控制考场门口的互斥访问，初始值为 1。
   
   **`students_in`**：用于记录进入考场的学生数量，初始值为 0。
   
   **`papers_collected`**：用于记录已收集的试卷数量，初始值为 0。
   
   **`can_start_exam`**：用于控制教师是否可以开始发卷子，初始值为 0。
   
   **`can_leave`**：用于控制教师是否可以离开考场，初始值为 0。

学生的操作如下：

```c
for (i = 1; i <= N; i++) {
    P(mutex); // 进入考场门口的互斥区
     学生进入考场
    V(mutex); // 离开考场门口的互斥区
    V(students_in); // 增加进入考场的学生计数
    P(can_start_exam); // 等待教师发卷子
     学生开始考试
     学生完成考试，交卷
    P(mutex); // 进入考场门口的互斥区
     学生离开考场
    V(mutex); // 离开考场门口的互斥区
    V(papers_collected); // 增加已收集的试卷数量
}
```

教师的操作如下：

```c
// 教师等待所有学生进入考场
for (i = 1; i <= N; i++) {
    P(students_in);
}
V(can_start_exam); // 允许学生开始考试
 教师等待所有试卷收集完成
for (i = 1; i <= N; i++) {
    P(papers_collected);
}
 教师封装试卷
V(can_leave); // 教师可以离开考场
```

