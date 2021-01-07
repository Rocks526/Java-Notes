# CountDownLatch 和 CyclicBarrier 介绍

CountDownLatch 和 CyclicBarrier 都是JUC包里提供的并发工具，这两个工具都可以提供类似于计数器的功能，CountDownLatch 不可复用，CyclicBarrier 可复用，并提供回调函数。

假设有以下的一个对账系统，处理逻辑是首先查询订单，然后查询派送单，之后对比订单和派送单，将差异写入差异库。

![image-20210107153432520](http://rocks526.top/lzx/image-20210107153432520.png)

核心代码如下，就是在一个单线程里面循环查询订单、派送单，然后执行对账，最后将写入差异库：

```java
while(存在未对账订单){
  // 查询未对账订单
  pos = getPOrders();
  // 查询派送单
  dos = getDOrders();
  // 执行对账操作
  diff = check(pos, dos);
  // 差异写入差异库
  save(diff);
} 
```

在上面的代码中，整个执行流程都是串行的，需要进行优化，做并行处理：

```java
while(存在未对账订单){
  // 查询未对账订单
  Thread T1 = new Thread(()->{
    pos = getPOrders();
  });
  T1.start();
  // 查询派送单
  Thread T2 = new Thread(()->{
    dos = getDOrders();
  });
  T2.start();
  // 等待 T1、T2 结束
  T1.join();
  T2.join();
  // 执行对账操作
  diff = check(pos, dos);
  // 差异写入差异库
  save(diff);
} 
```

优化完后，两个查询任务并行处理，之后汇总对比，但存在一点不足：while 循环里面每次都会创建新的线程，而创建线程可是个耗时的操作。因此通过线程池优化如下：

```java
// 创建 2 个线程的线程池
Executor executor = 
  Executors.newFixedThreadPool(2);
while(存在未对账订单){
  // 查询未对账订单
  executor.execute(()-> {
    pos = getPOrders();
  });
  // 查询派送单
  executor.execute(()-> {
    dos = getDOrders();
  });
  
  /* ？？如何实现等待？？*/
  
  // 执行对账操作
  diff = check(pos, dos);
  // 差异写入差异库
  save(diff);
}   
```

上面的代码就是用线程池优化后的：首先创建了一个固定大小为 2 的线程池，之后在 while 循环里重复利用。但是有个问题，那就是主线程如何知道 getPOrders() 和 getDOrders() 这两个操作什么时候执行完。前面主线程通过调用线程 T1 和 T2 的 join() 方法来等待线程 T1 和 T2 退出，但是在线程池的方案里，线程根本就不会退出，所以 join() 方法已经失效了。

解决这个问题最直接的办法是弄一个计数器，初始值设置成 2，当执行完`pos = getPOrders();`这个操作之后将计数器减 1，执行完`dos = getDOrders();`之后也将计数器减 1，在主线程里，等待计数器等于 0；当计数器等于 0 时，说明这两个查询操作执行完了。等待计数器等于 0 其实就是一个条件变量，用管程实现起来也很简单。

不过并不需要你在实际项目中去实现上面的方案，因为 Java 并发包里已经提供了实现类似功能的工具类：**CountDownLatch**。

# 用 CountDownLatch 实现线程等待

下面的代码示例中，在 while 循环里面，首先创建了一个 CountDownLatch，计数器的初始值等于 2，之后在`pos = getPOrders();`和`dos = getDOrders();`两条语句的后面对计数器执行减 1 操作，这个对计数器减 1 的操作是通过调用 `latch.countDown();` 来实现的。在主线程中，通过调用 `latch.await()` 来实现对计数器等于 0 的等待。

```java
// 创建 2 个线程的线程池
Executor executor = 
  Executors.newFixedThreadPool(2);
while(存在未对账订单){
  // 计数器初始化为 2
  CountDownLatch latch = 
    new CountDownLatch(2);
  // 查询未对账订单
  executor.execute(()-> {
    pos = getPOrders();
    latch.countDown();
  });
  // 查询派送单
  executor.execute(()-> {
    dos = getDOrders();
    latch.countDown();
  });
  
  // 等待两个查询操作结束
  latch.await();
  
  // 执行对账操作
  diff = check(pos, dos);
  // 差异写入差异库
  save(diff);
}
```

在上面的例子中，通过CountDownLatch 实现了线程的等待，完成了两个查询任务的并行处理，但这两个查询操作和对账操作 check()、save() 之间还是串行的。很显然，这两个查询操作和对账操作也是可以并行的，也就是说，在执行对账操作的时候，可以同时去执行下一轮的查询操作。

两次查询操作能够和对账操作并行，对账操作还依赖查询操作的结果，这明显有点生产者 - 消费者的模型，两次查询操作是生产者，对账操作是消费者。既然是生产者 - 消费者模型，那就需要有个队列，来保存生产者生产的数据，而消费者则从这个队列消费数据。

不过针对对账这个项目，需要设计两个队列，并且两个队列的元素之间还有对应关系。订单查询操作将订单查询结果插入订单队列，派送单查询操作将派送单插入派送单队列，这两个队列的元素之间是有一一对应的关系的。两个队列的好处是，对账操作可以每次从订单队列出一个元素，从派送单队列出一个元素，然后对这两个元素执行对账操作，这样数据一定不会乱掉。

下面再来看如何用双队列来实现完全的并行。一个最直接的想法是：一个线程 T1 执行订单的查询工作，一个线程 T2 执行派送单的查询工作，当线程 T1 和 T2 都各自生产完 1 条数据的时候，通知线程 T3 执行对账操作。

这个想法虽看上去简单，但其实还隐藏着一个条件，那就是线程 T1 和线程 T2 的工作要步调一致，不能一个跑得太快，一个跑得太慢，只有这样才能做到各自生产完 1 条数据的时候，通知线程 T3。

上面提到的方案有两个难点：一个是线程 T1 和 T2 要做到步调一致，另一个是要能够通知到线程 T3。

依然可以利用一个计数器来解决这两个难点，计数器初始化为 2，线程 T1 和 T2 生产完一条数据都将计数器减 1，如果计数器大于 0 则线程 T1 或者 T2 等待。如果计数器等于 0，则通知线程 T3，并唤醒等待的线程 T1 或者 T2，与此同时，将计数器重置为 2，这样线程 T1 和线程 T2 生产下一条数据的时候就可以继续使用这个计数器了。

同样，还是建议不要在实际项目中这么做，因为 Java 并发包里也已经提供了相关的工具类：**CyclicBarrier**。

# 用 CyclicBarrier 实现线程同步

在下面的代码中，我们首先创建了一个计数器初始值为 2 的 CyclicBarrier，你需要注意的是创建 CyclicBarrier 的时候，我们还传入了一个回调函数，当计数器减到 0 的时候，会调用这个回调函数。

线程 T1 负责查询订单，当查出一条时，调用 `barrier.await()` 来将计数器减 1，同时等待计数器变成 0；线程 T2 负责查询派送单，当查出一条时，也调用 `barrier.await()` 来将计数器减 1，同时等待计数器变成 0；当 T1 和 T2 都调用 `barrier.await()` 的时候，计数器会减到 0，此时 T1 和 T2 就可以执行下一条语句了，同时会调用 barrier 的回调函数来执行对账操作。

非常值得一提的是，CyclicBarrier 的计数器有自动重置的功能，当减到 0 的时候，会自动重置你设置的初始值。

```java
// 订单队列
Vector<P> pos;
// 派送单队列
Vector<D> dos;
// 执行回调的线程池 
Executor executor = 
  Executors.newFixedThreadPool(1);
final CyclicBarrier barrier =
  new CyclicBarrier(2, ()->{
    executor.execute(()->check());
  });
  
void check(){
  P p = pos.remove(0);
  D d = dos.remove(0);
  // 执行对账操作
  diff = check(p, d);
  // 差异写入差异库
  save(diff);
}
  
void checkAll(){
  // 循环查询订单库
  Thread T1 = new Thread(()->{
    while(存在未对账订单){
      // 查询订单库
      pos.add(getPOrders());
      // 等待
      barrier.await();
    }
  });
  T1.start();  
  // 循环查询运单库
  Thread T2 = new Thread(()->{
    while(存在未对账订单){
      // 查询运单库
      dos.add(getDOrders());
      // 等待
      barrier.await();
    }
  });
  T2.start();
}
```

> CyclicBarrier 的回调函数是由最后一个触发barrier.await()的线程调用的，因此如果回调函数里不做异步处理，其实最终执行对账操作还是同步的，只有回调函数执行完才会开始第二个回合。
>
> 其次回调函数里之所以采用一个单线程的线程池，主要是因为从两个队列获取元素的操作没做同步处理，多线程可能出现数据乱序的问题。

# 总结

- **CountDownLatch 主要用来解决一个线程等待多个线程的场景**，可以类比旅游团团长要等待所有的游客到齐才能去下一个景点
- **CyclicBarrier 是一组线程之间互相等待**，更像是几个驴友之间不离不弃
- CountDownLatch 的计数器是不能循环利用的，也就是说一旦计数器减到 0，再有线程调用 await()，该线程会直接通过
- **CyclicBarrier 的计数器是可以循环利用的**，而且具备自动重置的功能，一旦计数器减到 0 会自动重置到你设置的初始值
- CyclicBarrier 还可以设置回调函数，回调由最后一个触发barrier.await()的线程进行调用，只有回调函数执行完才会开始第二个回合

