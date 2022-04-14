# 一：Java并发编程模型

### 1.1 并发编程介绍

随着分时操作系统的出现，并发编程开始出现在人们的视野里。早期的单核CPU，并发编程主要为了避免阻塞IO对程序的影响，随着硬件的发展，多核CPU将并发编程推向高峰，并发编程可以有效利用多核，大幅度提升程序性能。

并发编程特点：

- 单核CPU，`并发编程可以有效提升CPU利用率`
- 多核CPU，`并发编程可以通过将任务拆解分治计算，大幅度提升程序性能`
- 并发编程难度较高，很容易出现一些违反直觉的Bug

并发程序的编写可以类比现实世界，接到需求后，团队如何快速完成提测上线？

答案是将任务进行拆解，尽可能地让多人进行并行处理，最后完成代码合并提测上线。针对有相互依赖的任务，需要对应的人员进行沟通排期，代码合并时需要有先后顺序，后者解决冲突，避免代码覆盖。这些流程对应到编程世界如下：

- `分工`：将任务进行拆解，让多个线程同时处理
- `协作`：针对相互依赖的任务，需要线程之间进行沟通协作
- `互斥`：代码的汇总合并，多个线程针对临界资源的访问需要保证互斥

Java的整个并发工具包也是按照这三个方面来组织的，在正式介绍这些工具之前，需要先了解一下线程安全相关的理论基础。

### 1.2 Java的并发编程模型

随着多核CPU的普及，主流编程语言都提供了并发编程的API，包括Java。主流的并发方案有：

- 多进程
- 多线程
- 协程
- ......

`在Java中，并发编程主要通过多线程实现。`

### 1.3 Java的线程设计

线程是操作系统上的概念，各个编程语言都会线程进行了封装。在Java中，线程对应的抽象为Thread类，`一个操作系统内核线程，对应多个Java应用线程。`

- 线程的创建和启动

```java
package com.rocks.test.lang;

/**
 * 线程测试
 */
public class ThreadTest {

    // 自定义线程实现方式一
    public static class MyThread extends Thread {
        @Override
        public void run() {
            System.out.println("MyThread running!");
        }
    }

    // 自定义线程实现方式二
    public static class MyRunnable implements Runnable {
        @Override
        public void run() {
            System.out.println("MyRunnable running!");
        }
    }

    // 创建 && 启动线程
    public static void main(String[] args) {
        new MyThread().start();
        new Thread(new MyRunnable()).start();
    }

}
```

- 线程的常用API

```java
// 将当前线程挂起，让出CPU调度 （CPU可忽略这个信号）    
public static native void yield();

// 让线程让出cpu使用权，进入休眠，沉睡指定时间。
// 可以通过中断异常唤醒当前线程，抛出异常后会自动清除中断标识。
// 沉睡时不会放弃对锁的持有。
public static native void sleep(long millis) throws InterruptedException;
    
// 启动线程，一个线程只能启动一次。
public synchronized void start()

// 销毁线程，已标记为废弃方法，可能导致持有的锁、资源无法释放
public final void stop()
public void destroy()
public final void suspend()
public final void resume()

// 中断线程 （给线程发送中断标识），可以实现优雅退出
public void interrupt()

// 判断线程是否被中断
public static boolean interrupted()
    
// 线程是否存活
public final native boolean isAlive()
    
// 主线程阻塞，等待子线程执行完，用于线程间的同步
public final synchronized void join(long millis)
```

### 1.4 Java线程的生命周期

线程也有"生老病死"，专业名词是`生命周期`。线程的生命周期通用的描述主要是以下五个形态：

![image-20220208175140713](http://rocks526.top/lzx/image-20220208175140713.png)

- 初始状态：线程已经被创建，但还不能被分配CPU。（编程语言独有的，在操作系统层面线程还没有创建）
- 可运行状态：线程已经被创建，等待CPU分配。
- 运行状态：线程获取到CPU使用权，开始运行。
- 休眠状态：程序遇到阻塞式API时进入休眠状态，例如等待网络IO、磁盘IO或其条件变量等。
- 终止状态：线程执行完，或出现异常则进入终止态，线程的生命周期结束。

在Java中，对线程的生命周期有一些调整，一共分为六种状态：

![image-20220208175628326](http://rocks526.top/lzx/image-20220208175628326.png)

- NEW：初始化状态
- RUNNABLE：可运行、运行状态
- BLOCKED：重量级阻塞状态（获取synchronized锁，无法中断）
- WAITING：轻量级堵塞状态（可中断）
- TIMED_WAITING：存在超时时间的轻量级阻塞状态（可中断）
- TERMINATED：终止状态

> 由于Jvm无法干涉CPU分配，可运行状态与运行状态对于Jvm来说无差别，因此Jvm将此两种状态合并。
>
> Jvm将睡眠状态进行细化，分为BLOCKED、WAITINT、TIMED_WAITING，用于区分synchronized锁和主动wait，还有是否超时。

Java线程各种状态之前的转换方式如下：

![image-20220208180109691](http://rocks526.top/lzx/image-20220208180109691.png)



### 1.5 应用程序需要多少个线程

之前提过多线程可以有效提过CPU的利用率，减少阻塞式IO带来的性能问题，那么应用程序创建多少个线程合适？

判断应用需要多少个线程，需要区分应用是CPU密集型还是IO密集型，如果是CPU密集型，设置CPU核数个线程即可，有效利用多核CPU。如果是IO密集型，则需要计算IO阻塞时间和CPU计算时间的比例，将IO阻塞的时间全部交给其他线程去使用CPU即可实现硬件效率最大化。

- CPU密集型：设置CPU核数+1 （避免内存页失效等情况导致的线程阻塞）
- IO密集型：CPU 核数 * [ 1 +（I/O 耗时 / CPU 耗时）]
- 经验值：一般的web程序，都是IO阻塞偏多，经验值可以设置为 2 * CPU 的核数 + 1
- 压测优化：根据上面的方式预估线程数后，线上持续观察CPU和IO的利用率，进行优化调整

### 1.6 线程安全

在Jvm中，每个方法的调用都是通过虚拟机栈实现的，`虚拟机栈是每个线程独有的`，因此方法内的局部变量不会被并发访问，也就不存在线程安全问题，这种实现并发安全的方式也称作`线程封闭`。

只有在类级别的共享数据或者方法传入的参数，`数据会被多个线程并发访问`，才可能存在线程安全问题。

# 二：并发编程Bug的根源

这些年硬件在不断发展，CPU、IO、内存等都在不断迭代，但在这个发展的过程中，有一个核心矛盾一直存在，`就是这三者之间的速度差异。`CPU与内存、内存与IO设备之间的访问速度差距在几个数量级，根据木桶效应，我们的应用程序访问这三者时，程序的性能取决于最慢的操作——IO设备，因此单方面提升CPU的性能是无效的。

为了合理利用 CPU 的高性能，平衡这三者的速度差异，计算机体系机构、操作系统、编译程序都做出了贡献，主要体现为：

- CPU 增加了缓存，以均衡与内存的速度差异
- 操作系统增加了进程、线程，以分时复用 CPU，进而均衡 CPU 与 I/O 设备的速度差异
- 编译程序优化指令执行次序，使得缓存能够得到更加合理地利用

这些机制默默的优化着程序的性能，也给并发编程带来了很多诡异的Bug，主要是以下三方面：`可见性，原子性，有序性。`

### 2.1 缓存导致的可见性问题

- 问题描述

在单核时代，所有的线程都是在一颗 CPU 上执行，CPU 缓存与内存的数据一致性容易解决。因为所有线程都是操作同一个 CPU 的缓存，一个线程对缓存的写，对另外一个线程来说一定是可见的。

`一个线程对共享变量的修改，另外一个线程能够立刻看到，我们称为可见性。`

多核时代，每颗 CPU 都有自己的缓存，当多个线程在不同的 CPU 上执行时，`这些线程操作的是不同的 CPU 缓存，因此就可能读取到旧的数据进而导致逻辑错误`，例如经典的多线程计算1到10000之和的问题。

- 问题示例

```java
package com.rocks.test.current;

/**
 * 并发问题复现
 * @author lizhaoxuan
 * @date 2022/02/09
 */
public class CurrentBug {

    public static class Counter {
        // 初始值
        private long count = 0;

        // 添加10000
        private void add10K(){
            int index = 0;
            while (index < 10000){
                count ++;
                index ++;
            }
        }
    }

    public static long calc() throws InterruptedException {
        Counter counter = new Counter();
        // 创建两个线程各执行add
        Thread t1 = new Thread(counter::add10K);
        Thread t2 = new Thread(counter::add10K);
        t1.start();
        t2.start();
        // 等待执行结束
        t1.join();
        t2.join();
        return counter.count;
    }

    public static void main(String[] args) throws InterruptedException {
        // 可见性问题示例
        System.out.println(calc());
    }


}
```

由于CPU缓存的存在，可能出现以下的执行顺序：

1. t1从内存读取count值到CPU-1缓存
2. t1执行count++操作（此时的count值还没有写入内存）
3. t2从内存读取count值到CPU-2缓存
4. t2执行count++操作（此时的count值还没有写入内存）
5. t1和t2将count值写入内存（实际上count值只增加了1）

由于可见性问题，最终导致这个程序的计算结果可能是10000到20000之间的任意一个值。

- 解决方案

为了解决这个问题，`操作系统提供MESI协议来实现内存和CPU缓存的一致性`，各种编程语言则在这个基础之上进行封装提供对应的解决方案。

### 2.2 线程切换带来的原子性问题

- 问题描述

由于IO设备的速度太慢，操作系统为了提升CPU的利用率发明了多进程和多线程，将CPU的使用权划分为一个一个时间片，`当程序阻塞在IO时，则进行上下文切换，让出CPU的使用权从而提升利用率。`

乍看起来好像没什么问题，其实问题出在高级编程语言和CPU指令的级别上，`操作系统的上下文切换是针对CPU指令级别，而现在的高级编程语言一般一条命令对应多条CPU指令`，因此可能出现某条语句执行一半的时候发现线程切换进而导致的Bug。

- 问题示例

例如高级语言里的count++操作，对应到CPU指令层面，其实包含三条命令：

1. 首先，需要把变量 count 从内存加载到 CPU 的寄存器
2. 在寄存器中执行 +1 操作
3. 最后，将结果写入内存（缓存机制导致可能写入的是 CPU 缓存而不是内存）

操作系统做任务切换，可以发生在任何一条CPU 指令执行完，对于上面的三条指令来说，我们假设 count=0，如果线程 A 在 指令 1 执行完后做线程切换，线程 A 和线程 B 按照下图的序列执行，那么我们会发现两个线程都执行了 count+=1 的操作，但是得到的结果不是我们期望的 2，而是 1。

![image-20220209165908220](http://rocks526.top/lzx/image-20220209165908220.png)

在上面的计算累加的示例里，将count值改成`volatile修饰，可以解决可见性问题`，但由于原子性问题的存在，计算的结果依旧不是20000，而是10000-20000之间的任意一个值。

```java
package com.rocks.test.current;

/**
 * 并发问题复现
 * @author lizhaoxuan
 * @date 2022/02/09
 */
public class CurrentBug {

    public static class Counter {
        // 初始值
        private volatile long count = 0;

        // 添加10000
        private void add10K(){
            int index = 0;
            while (index < 10000){
                count ++;
                index ++;
            }
        }
    }

    public static long calc() throws InterruptedException {
        Counter counter = new Counter();
        // 创建两个线程各执行add
        Thread t1 = new Thread(counter::add10K);
        Thread t2 = new Thread(counter::add10K);
        t1.start();
        t2.start();
        // 等待执行结束
        t1.join();
        t2.join();
        return counter.count;
    }

    public static void main(String[] args) throws InterruptedException {
        System.out.println(calc());
    }


}
```

- 解决方案

在单核服务器上，`禁止CPU中断从而禁止线程切换即可解决原子性问题`，但在多核服务器上，即便禁止CPU中断，多个核心也可能存在多个线程同时访问某个临界资源，`要解决原子性需要通过锁实现临界资源的互斥访问`，保证线程安全。

由于锁的本质是将多个线程对临界资源的访问串行化，解决原子性问题的同时也降低了性能，因此`操作系统提供了CAS指令，编程语言借助CAS指令可以实现无锁化线程安全。`

### 2.3 编译器优化带来的有序性问题

- 问题描述

顾名思义，`有序性指的是程序按照代码的先后顺序执行。`编译器为了优化性能，有时候会改变程序中语句的先后顺序，例如程序中：“a=6；b=7；”编译器优化后可能变成“b=7； a=6；”，在这个例子中，编译器调整了语句的顺序，但是不影响程序的最终结果。不过有时候编译器及解释器的优化可能导致意想不到的Bug。

- 问题示例

```java
    public static class Singleton {
        static Singleton instance;

        static Singleton getInstance(){
            if (instance == null){
                synchronized (Singleton.class){
                    if (instance == null){
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }

    }
```

上面的代码是一个典型的单例创建，双重检查+锁，看起来没有问题，其实极端情况下可能出现空指针的问题。问题就出在编译器的重排序上，我们以为的 new 操作应该是：

1. 分配一块内存 M
2. 在内存 M 上初始化 Singleton 对象
3. 然后 M 的地址赋值给 instance 变量

但是实际上优化后的执行路径却是这样的：

1. 分配一块内存 M
2. 将 M 的地址赋值给 instance 变量
3. 最后在内存 M 上初始化 Singleton 对象

优化后，假设线程T1执行完1、2步之后发生线程切换，线程T2进入第一个if判断，此时instance变量已经有了指向地址，因此不再加锁初始化，直接返回instance变量，而由于instance变量还没有初始化，程序直接使用就会出现空指针异常。

- 解决方案

针对重排序问题，解决方案其实很简单，只要禁止编译器重排序即可，为了做到性能最优，`可以告诉编译器哪些地方不允许重排序，做到按需禁用，在Java里面，提供这个功能的就是volatile关键字。`

因此，在上面的例子中，给instance变量添加volatile修饰即可解决这个问题，volatile可以禁止编译器重排序。

# 三：Java提供的解决方案

### 3.1 Java内存模型

上面提到的三个并发编程的Bug属于编程领域的通用问题，





### 3.2 互斥锁



### 3.3 锁的优化



### 3.4 面向对象思想与并发编程的结合

















