---
title: CPU and Cache
date: 2017-05-23 16:56:56
tags:
 - Hardware
 - OS
---
### Linux perf
软件性能诊断工具
Use PMU, tracepoint and special counter in kernel

### Terms

1. 硬件并行设计

CPU 流水线 | processor pipeline
超标量 Superscalar
乱序执行 reordering ?
Based：相邻指令不依赖

<!-- more -->

2. 分支预测 branch predict

预测最可能的下条指令
对switch case效果不佳

3. 硬件中加入 PMU

performance monitor unit
允许软件对某种硬件事件设置 counter， 超过预设值时中断

3. Trackpoint

散落在内核中的hook, 可被 trace/debug 工具使用，如 perf

### Usages
> perf list
list all monitor event

Three types:
1. Hardware event, generated by PMU, eg. cache miss
1. Software event, generated by kernel, eg. context switch, tick
1. Tracepoint event, triggered by static tracepoint in kernel, eg. counter for slab 分配器

> perf stat
provide overview for program running detail

CPU bound 型 与 IO bound 型，调优不同

### CPU
#### 1. CPU affinity
CPU亲缘性 (soft and hard)
Cause: most popular architecture for server is SMP
and every CPU has its own cache (L1, L2)
TO: avoid L1/L2 cache invalid(失效) caused by context switch

#### 2. Cache barrier
A memory barrier, is a type of barrier instruction, which causes a CPU or _Compiler_ to enforce an ordering constraint on memory operations issued before and after the barrier instruction.
一旦内存数据被推送到缓存，就会有消息协议来确保所有的缓存会对所有的共享数据同步并保持一致。这个使内存数据对CPU核可见的技术被称为内存屏障或内存栅栏
volatile类型 写入之后插入store屏障，读取之前插入 load 屏障
[Why Memory Barrier？](https://sstompkins.wordpress.com/2011/04/12/why-memory-barrier%EF%BC%9F/)

### Cache
#### 1. Cache coherency 缓存一致性
> 基本定律：在任意时刻，任意级别缓存中的缓存段的内容，等同于它对应的内存中的内容。
write through 直写： 写入下级缓存
write back 回写：仅修改本级缓存中数据，并标记为缓存段(Line)为脏段，触发回写
优点：回写效率更高，过滤对同一地址的反复写操作
> 回写定律：当所有的脏段被回写后，任意级别缓存中的缓存段的内容，等同于它对应的内存中的内容。

Ref: 缓存关联性（cache associativity），缓存组（cache sets），使用分配写（write-allocate）还是非分配写（上面我描述的直写是和分配写相结合的，而回写是和非分配写相结合的），非对齐的访问（unaligned access），基于虚拟地址的缓存。。。

#### 2. 缓存一致性协议  Coherency protocols
- 解决多核心的多组缓存同步问题
- 窥探协议 snooping
- use by most device
- 基于目录的协议 directory-based

#### 3. Buffer vs. Cache
Buffer：一般用于写操作，可以称之为写缓冲
Cache：一般用于读操作，可以称之为读缓存

#### 4. Web 缓存体系
![Web缓存知识体系](http://mmbiz.qpic.cn/mmbiz/yNKv1P4Q9eVhezt0HiaXDfWR8ZZictibjtW0q3HHLdWQFEcPJcvbP0GXwrp1rgibUNEQcuRDCqjKECGprGNHv5CHeg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)
1.1.2 基于目录的协议 directory-based

### Barrier, Visibility and Mutex
#### Terms
- 可见性：
共享变量值，一个线程修改，其他能看见
- 共享变量：
一个变量在多个线程的工作内存中都有副本

#### JMM（Java Memory Model）Java内存模型
线程对共享变量的操作只能在工作内存中进行，不能直接读取主存
![JMM 内存模型](https://segmentfault.com/img/bVrbuK)
可见性的实现：synchronize and volatile
synchronized 两点要求：
1. unlock之前，共享变量必须刷新至主存
2. 加锁时重新加载主存中的共享变量

##### Workflow of lock and unlock：
1. 获得互斥锁
2. 清空工作内存
3. 从主存拷贝变量的最新副本到工作内存
4. 执行代码
5. 更新后的变量刷新到主存
6. 释放互斥锁
Q: 未更改的变量是否一同刷新？

##### 重排序
代码书写顺序与实际执行顺序不同，可能的优化：
1. 编译器优化重排序（编译器优化）
2. 指令级并行重排序（处理器优化）
3. 内存系统重排序（处理器优化）
可能会导致：
某些变量的操作会在后面的变量之前执行，比如终止条件后获取不到最终的结果值

解决：
可用 volatile，即 as-if-serial，程序执行的结果与代码顺序执行的结果一致

#### Mutex 互斥机制
1. Synchronize
when to use:
writing a variable might be read by other threads;
reading a variable might be written by other threads;
both reader and writer must use same monitor lock

用途：
Visibility and mutex

#### volatile and visibility
volatile 只能保证共享变量的可见性和读写操作的原子性？
volatile 原理：
volatile 类型的变量，转化为汇编后，增加 lock add1.. 指令, this will cause:
1. write present processor cache line back to main memory
2. write back action invalidates caches in other processors

安全地使用 volatile 变量：
1. 对变量的操作不依赖当前值
> 不满足：count++；count=count+1;
   满足：Boolean变量，温度值等。

2. 变量没有包含在其他变量的不变式中
>  不满足：不变式，low<up.

#### Atomic 原子性
##### CPU原子性实现：
1. 总线锁定
eg. a thread execute _i++_ on CPU1, will lock bus - communication between memory and other CPU, to ensure other tasks not changing the value of i in memory,
but other instructions will not execute during the lock.
2. 缓存锁定
only lock the address of variable in memory, and only allows CPU1 to write back new value, and invalidate the cache in other CPUs

##### Java原子性实现
使用CAS循环

Reference:
TO Read
[互斥锁和内存可见性](http://blog.csdn.net/gqtcgq/article/details/52330065)
[CON02-C. Do not use volatile as a synchronization primitive](https://www.securecoding.cert.org/confluence/display/c/CON02-C.+Do+not+use+volatile+as+a+synchronization+primitive)
[Memory Barriers: a Hardware View for Software Hackers](http://www.rdrop.com/users/paulmck/scalability/paper/whymb.2009.04.05a.pdf)
