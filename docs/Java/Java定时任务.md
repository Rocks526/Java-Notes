# 一：Java定时任务基础篇

[Jdk的Timer基础入门](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Timer基础入门.md)

[Cron表达式](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Cron表达式.md)

[Quartz基础入门](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Quartz基础入门.md)

[Elastic-job基础入门](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Elastic-job基础入门.md)

# 二：Java定时任务原理篇

[定时任务基本原理](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/定时任务基本原理.md)

[Timer源码分析](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Timer源码分析.md)

[ScheduledExecutorService源码分析](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/ScheduledExecutorService源码分析.md)

[Quartz源码分析](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Quartz源码分析.md)

[Elastic-job源码分析](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Quartz源码分析.md)

# 三：Java定时任务实战篇

[Quartz任务监控](https://github.com/Rocks526/Java-Notes/blob/master/docs/Java/Java定时任务/Quartz任务监控.md)



















![image-20210322155647956](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210322155647956.png)

### 小顶堆实现方案存在的问题

- 每次取出，都需要进行下沉操作，即便logN，但当堆特别大时，性能会降低
- 定时任务只关心最近的任务，也就是堆顶元素，其他元素不关心，也就是说，再取出之后，只要下沉一次即可满足需求，但小顶堆为了维持数据结构，会一直下沉，造成性能浪费

> 因此小顶堆比较适合少量的定时任务存储，当有几万几十万定时任务时，定时任务的执行又很频繁，会导致不断的进行取出，堆下沉操作。

### 时间轮原理

- 简易时间轮

和钟表一样，每个小时划分为一片时间轮，每个位置存储一个链表，将该时间段内的所有定时任务连接起来。

当时间往后走时，只需要检查当前时间片的定时任务即可，而且执行完之后，不需要进行下沉操作维护数据结构。

存在的问题：

时钟只有12刻度，1和13是同一个时间片，人类可以区分，程序无法区分，改成24个时间片可以解决这个问题。

定时任务可能不是一天，一个是2号的3点，一个3号的3点，如何存储？如果再加一个天级别的时间轮，那还有月和年，程序太过复杂。

- round世间轮

采用12刻度，如果13点执行，在1点的位置上添加任务，并给任务添加一个round值，记为1，当时间达到1点的时间片时，round值减1，然后将所有round值为0的任务进行执行。第二天，下一个月的任务同理，设置更大的round值即可。

存在的问题：

可能很久以后才只能的任务，但每轮(12h)都需要更新round，导致大量的遍历操作。

- 分层时间轮（目前普遍采用的方案，包括linux的cron，Quartz，Elastic Job）

以两层时间轮为例，一层天轮，一层月轮，天轮存储24个时间片，代表一天的24时，月轮存储30个时间片，代表一个月，当第二天到达时，先将月轮对应的那一天的定时任务全部取出放到天轮里，然后天轮执行。

如果任务超出月轮限制(下个月或者下一年)，可以再加一层时间轮，或者添加round值。

### Timer存在问题

- schedule：丢任务
- AtFiexedRate：提前执行，执行时间乱掉
- 任务出现异常，导致整个Timer终止

> 原因：单线程执行所有任务，任务阻塞会对别的任务有影响
>
> 解决方案：在TimerTask的run方法里启动子线程执行任务，实现真正的多线程执行
>
> 使用场景：即便解决时间不准确的问题，Timer支持的定时任务也都是指定时间或者固定频率的定时任务，不支持每个月几号执行这种场景，Timer只适合简单定时任务场景

![image-20210322171112634](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210322171112634.png)

### ScheduledExcutorService

Jdk提供的定时任务线程池，解决Timer单线程调用，任务阻塞的问题，实际上就是在TimerTask里启动子线程执行。

![image-20210322171848636](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210322171848636.png)

- 任务出现异常，任务会被丢弃

![image-20210322172105183](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210322172105183.png)