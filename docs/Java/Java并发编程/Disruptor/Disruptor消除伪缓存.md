

![image-20210111163906865](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111163906865.png)

![image-20210111165426243](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111165426243.png)

![image-20210111170000629](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111170000629.png)

> LockSupport的阻塞和唤醒是基于线程的，并且不注重先后顺序。

![image-20210111171249993](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111171249993.png)

线程池线程数量：

- CPU密集型：CPU*2或者CPU+1
- IO密集型：CPU核数/(1-阻塞系数)

> 阻塞系数一般0.8-0.9

![image-20210111172519609](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111172519609.png)

![image-20210111172553532](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111172553532.png)

![image-20210111172735365](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111172735365.png)

![image-20210111172809257](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111172809257.png)

![image-20210111191554978](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111191554978.png)

![image-20210111191627498](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111191627498.png)

![image-20210111191645330](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111191645330.png)

```
Sequence放到Sequencer中，Sequencer放到RingBuffer中
```

![image-20210111193803437](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111193803437.png)











# 高性能

### RingBuffer

![image-20210111193910189](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111193910189.png)

- 环型缓冲区，无扩容
- 内存预加载，预生成所有对象，运行时无new对象和gc开销

> 运行时，当环用完一轮后，第二轮使用获取前，会将属性清空。
>
> 只有disruptor使用完毕，这些对象才会释放，避免了运行时gc影响。
>
> 也是一种空间换时间的思路。

### 内核-单线程写

![image-20210111195417923](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210111195417923.png)

> 多线程存在竞争，而且需要加锁/上下文切换消耗，性能反倒降低。
>
> Redis中，当单线程无法满足需求时，部署多个实例即可。
>
> Netty整体也是Reactor设计模式，串行执行，当单线程满足不了时，创建多个Reactor组去实现。
>
> 这种思想的核心在于，某块逻辑处理，单线程串行执行，单线程肯定比多线程速度要快，因此极限性能采用单线程，如果不满足需求，可以创建多个实例，每个实例管理自己的数据，实现多线程。

### 内存屏障

![image-20210112092636107](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112092636107.png)

![image-20210112092740375](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112092740375.png)

> 通过内存屏障实现无锁也能保证可见性/重排序。

### 消除伪共享

![image-20210112093050233](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112093050233.png)

![image-20210112093139454](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112093139454.png)

![image-20210112093216206](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112093216206.png)

![image-20210112093805399](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112093805399.png)

> JUC包下也有这种填充缓存行消除伪共享的机制

### 序号栅栏机制

![image-20210112093933939](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112093933939.png)

![image-20210112094005024](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112094005024.png)

![image-20210112094029203](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112094029203.png)

> 生产者共用一个，消费者分别持有

![image-20210112094230313](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112094230313.png)

![image-20210112095543491](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112095543491.png)

通过自旋避免CAS操作，避免加锁

### 等待策略

![image-20210112102344188](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112102344188.png)

> Yield ==》 多线程自旋

# EventProcess

![image-20210112172610393](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210112172610393.png)

# Netty百万长连

![image-20210114114249500](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210114114249500.png)

![image-20210114114706950](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210114114706950.png)

![image-20210114114922476](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210114114922476.png)