# 线程池的使用

在上一节介绍了如何创建正确的线程池，这一节开始介绍如何使用线程池。

之前介绍了 ThreadPoolExecutor 的 `void execute(Runnable command)` 方法，利用这个方法虽然可以提交任务，但是却没有办法获取任务的执行结果（execute() 方法没有返回值）。而很多场景下，我们又都是需要获取任务的执行结果的。那 ThreadPoolExecutor 是否提供了相关功能呢？

# 如何获取任务执行结果

Java 通过 ThreadPoolExecutor 提供的 3 个 submit() 方法和 1 个 FutureTask 工具类来支持获得任务执行结果的需求。下面先来介绍这 3 个 submit() 方法，这 3 个方法的方法签名如下：

```java
// 提交 Runnable 任务
Future<?> 
  submit(Runnable task);
// 提交 Callable 任务
<T> Future<T> 
  submit(Callable<T> task);
// 提交 Runnable 任务及结果引用  
<T> Future<T> 
  submit(Runnable task, T result);
```

这三个方法的返回值都是 Future 接口，Future 接口有 5 个方法，我都列在下面了，它们分别是**取消任务的方法 cancel()、判断任务是否已取消的方法 isCancelled()、判断任务是否已结束的方法 isDone()以及2 个获得任务执行结果的 get() 和 get(timeout, unit)**，其中最后一个 get(timeout, unit) 支持超时机制。

通过 Future 接口的这 5 个方法，提交的任务不但能够获取任务执行结果，还可以取消任务。不过需要注意的是：这两个 get() 方法都是阻塞式的，如果被调用的时候，任务还没有执行完，那么调用 get() 方法的线程会阻塞，直到任务执行完才会被唤醒。

```java
// 取消任务
boolean cancel(
  boolean mayInterruptIfRunning);
// 判断任务是否已取消  
boolean isCancelled();
// 判断任务是否已结束
boolean isDone();
// 获得任务执行结果
get();
// 获得任务执行结果，支持超时
get(long timeout, TimeUnit unit);
```

这 3 个 submit() 方法之间的区别在于方法参数不同，下面简要介绍一下：

1. 提交 Runnable 任务 `submit(Runnable task)` ：这个方法的参数是一个 Runnable 接口，Runnable 接口的 run() 方法是没有返回值的，所以 `submit(Runnable task)` 这个方法返回的 Future 仅可以用来断言任务已经结束了，类似于 Thread.join()。
2. 提交 Callable 任务 `submit(Callable<T> task)`：这个方法的参数是一个 Callable 接口，它只有一个 call() 方法，并且这个方法是有返回值的，所以这个方法返回的 Future 对象可以通过调用其 get() 方法来获取任务的执行结果。
3. 提交 Runnable 任务及结果引用 `submit(Runnable task, T result)`：这个方法很有意思，假设这个方法返回的 Future 对象是 f，f.get() 的返回值就是传给 submit() 方法的参数 result。

下面这段示例代码展示了第三个方法的经典用法。需要你注意的是 Runnable 接口的实现类 Task 声明了一个有参构造函数 `Task(Result r)` ，创建 Task 对象的时候传入了 result 对象，这样就能在类 Task 的 run() 方法中对 result 进行各种操作了。result 相当于主线程和子线程之间的桥梁，通过它主子线程可以共享数据。

```java
ExecutorService executor 
  = Executors.newFixedThreadPool(1);
// 创建 Result 对象 r
Result r = new Result();
r.setAAA(a);
// 提交任务
Future<Result> future = 
  executor.submit(new Task(r), r);  
Result fr = future.get();
// 下面等式成立
fr === r;
fr.getAAA() === a;
fr.getXXX() === x
 
class Task implements Runnable{
  Result r;
  // 通过构造函数传入 result
  Task(Result r){
    this.r = r;
  }
  void run() {
    // 可以操作 result
    a = r.getAAA();
    r.setXXX(x);
  }
}
```

下面介绍 FutureTask 工具类。前面提到的 Future 是一个接口，而 FutureTask 是一个实实在在的工具类，这个工具类有两个构造函数，它们的参数和前面介绍的 submit() 方法类似。

```java
FutureTask(Callable<V> callable);
FutureTask(Runnable runnable, V result);
```

FutureTask 实现了 Runnable 和 Future 接口，由于实现了 Runnable 接口，所以可以将 FutureTask 对象作为任务提交给 ThreadPoolExecutor 去执行，也可以直接被 Thread 执行；又因为实现了 Future 接口，所以也能用来获得任务的执行结果。下面的示例代码是将 FutureTask 对象提交给 ThreadPoolExecutor 去执行。

```java
// 创建 FutureTask
FutureTask<Integer> futureTask
  = new FutureTask<>(()-> 1+2);
// 创建线程池
ExecutorService es = 
  Executors.newCachedThreadPool();
// 提交 FutureTask 
es.submit(futureTask);
// 获取计算结果
Integer result = futureTask.get();
```

# Future实践

以泡茶为例，整体流程如下：

![image-20210108101047759](http://rocks526.top/lzx/image-20210108101047759.png)

下面用程序来模拟一下这个最优工序。前面曾经提到，并发编程可以总结为三个核心问题：分工、同步和互斥。

编写并发程序，首先要做的就是分工，所谓分工指的是如何高效地拆解任务并分配给线程。对于烧水泡茶这个程序，一种最优的分工方案可以是下图所示的这样：用两个线程 T1 和 T2 来完成烧水泡茶程序，T1 负责洗水壶、烧开水、泡茶这三道工序，T2 负责洗茶壶、洗茶杯、拿茶叶三道工序，其中 T1 在执行泡茶这道工序时需要等待 T2 完成拿茶叶的工序。对于 T1 的这个等待动作，可以通过多种方案解决，例如 Thread.join()、CountDownLatch，甚至阻塞队列，不过此处采用 Future 特性来实现。

示例代码如下所示：首先，我们创建了两个 FutureTask——ft1 和 ft2，ft1 完成洗水壶、烧开水、泡茶的任务，ft2 完成洗茶壶、洗茶杯、拿茶叶的任务；这里需要注意的是 ft1 这个任务在执行泡茶任务前，需要等待 ft2 把茶叶拿来，所以 ft1 内部需要引用 ft2，并在执行泡茶之前，调用 ft2 的 get() 方法实现等待。

```java
// 创建任务 T2 的 FutureTask
FutureTask<String> ft2
  = new FutureTask<>(new T2Task());
// 创建任务 T1 的 FutureTask
FutureTask<String> ft1
  = new FutureTask<>(new T1Task(ft2));
// 线程 T1 执行任务 ft1
Thread T1 = new Thread(ft1);
T1.start();
// 线程 T2 执行任务 ft2
Thread T2 = new Thread(ft2);
T2.start();
// 等待线程 T1 执行结果
System.out.println(ft1.get());
 
// T1Task 需要执行的任务：
// 洗水壶、烧开水、泡茶
class T1Task implements Callable<String>{
  FutureTask<String> ft2;
  // T1 任务需要 T2 任务的 FutureTask
  T1Task(FutureTask<String> ft2){
    this.ft2 = ft2;
  }
  @Override
  String call() throws Exception {
    System.out.println("T1: 洗水壶...");
    TimeUnit.SECONDS.sleep(1);
    
    System.out.println("T1: 烧开水...");
    TimeUnit.SECONDS.sleep(15);
    // 获取 T2 线程的茶叶  
    String tf = ft2.get();
    System.out.println("T1: 拿到茶叶:"+tf);
 
    System.out.println("T1: 泡茶...");
    return " 上茶:" + tf;
  }
}
// T2Task 需要执行的任务:
// 洗茶壶、洗茶杯、拿茶叶
class T2Task implements Callable<String> {
  @Override
  String call() throws Exception {
    System.out.println("T2: 洗茶壶...");
    TimeUnit.SECONDS.sleep(1);
 
    System.out.println("T2: 洗茶杯...");
    TimeUnit.SECONDS.sleep(2);
 
    System.out.println("T2: 拿茶叶...");
    TimeUnit.SECONDS.sleep(1);
    return " 龙井 ";
  }
}
// 一次执行结果：
T1: 洗水壶...
T2: 洗茶壶...
T1: 烧开水...
T2: 洗茶杯...
T2: 拿茶叶...
T1: 拿到茶叶: 龙井
T1: 泡茶...
上茶: 龙井
```

# 总结

- 线程池提交任务，有Runnable和Callable两种方式，Runnable无返回值，Callable有返回值
- 提交Callable任务时，通过返回的Future判断任务执行情况并获取返回值
- 为了方便使用，Java提供了FutureTask这个工具类，他既实现了Runable接口，又实现了Future接口，因此既可以作为任务提交给线程池，又可以作为任务的返回值
- 在设计多线程程序时，首先要切分任务，当任务之间存在依赖关系时，可以通过Future来解决。

