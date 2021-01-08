# CompletionService介绍

在介绍CompletionService之前，先看一个场景：现在某个程序要从多个电商平台上查询商品的价格，然后将价格保存到数据库。

上面的场景如果采用“ThreadPoolExecutor+Future”的方案，示例代码如下：

```java
// 创建线程池
ExecutorService executor =
  Executors.newFixedThreadPool(3);
// 异步向电商 S1 询价
Future<Integer> f1 = 
  executor.submit(
    ()->getPriceByS1());
// 异步向电商 S2 询价
Future<Integer> f2 = 
  executor.submit(
    ()->getPriceByS2());
// 异步向电商 S3 询价
Future<Integer> f3 = 
  executor.submit(
    ()->getPriceByS3());
    
// 获取电商 S1 报价并保存
r=f1.get();
executor.execute(()->save(r));
  
// 获取电商 S2 报价并保存
r=f2.get();
executor.execute(()->save(r));
  
// 获取电商 S3 报价并保存  
r=f3.get();
executor.execute(()->save(r));
```

上面的这个方案存在一个问题：如果获取电商 S1 报价的耗时很长，那么即便获取电商 S2 报价的耗时很短，也无法让保存 S2 报价的操作先执行，因为这个主线程都阻塞在了 `f1.get()` 操作上。

这个问题的解决方案也很简单，增加一个阻塞队列，获取到 S1、S2、S3 的报价都进入阻塞队列，然后在主线程中消费阻塞队列，这样就能保证先获取到的报价先保存到数据库了。示例代码如下：

```java
// 创建阻塞队列
BlockingQueue<Integer> bq =
  new LinkedBlockingQueue<>();
// 电商 S1 报价异步进入阻塞队列  
executor.execute(()->
  bq.put(f1.get()));
// 电商 S2 报价异步进入阻塞队列  
executor.execute(()->
  bq.put(f2.get()));
// 电商 S3 报价异步进入阻塞队列  
executor.execute(()->
  bq.put(f3.get()));
// 异步保存所有报价  
for (int i=0; i<3; i++) {
  Integer r = bq.take();
  executor.execute(()->save(r));
}  
```

在生产环境不建议这么使用，原因很简单，就是Java提供了类似的工具给我们，就是CompletionService。

> 其实询价这个场景，可以将数据库保存的动作放到任务内部去做，此处只是为了说明CompletionService的使用场景。

# CompletionService的使用

CompletionService 的实现原理也是内部维护了一个阻塞队列，当任务执行结束就把任务的执行结果加入到阻塞队列中，不同的是 CompletionService 是把任务执行结果的 Future 对象加入到阻塞队列中，而上面的示例代码是把任务最终的执行结果放入了阻塞队列中。

### **创建 CompletionService** 

CompletionService 接口的实现类是 ExecutorCompletionService，这个实现类的构造方法有两个，分别是：

1. `ExecutorCompletionService(Executor executor)`
2. `ExecutorCompletionService(Executor executor, BlockingQueue<Future<V>> completionQueue)`

这两个构造方法都需要传入一个线程池，如果不指定 completionQueue，那么默认会使用无界的 LinkedBlockingQueue。任务执行结果的 Future 对象就是加入到 completionQueue 中。

下面的示例代码完整地展示了如何利用 CompletionService 来实现高性能的询价系统。其中，没有指定 completionQueue，因此默认使用无界的 LinkedBlockingQueue。之后通过 CompletionService 接口提供的 submit() 方法提交了三个询价操作，这三个询价操作将会被 CompletionService 异步执行。最后，我们通过 CompletionService 接口提供的 take() 方法获取一个 Future 对象（前面我们提到过，加入到阻塞队列中的是任务执行结果的 Future 对象），调用 Future 对象的 get() 方法就能返回询价操作的执行结果了。

```java
// 创建线程池
ExecutorService executor = 
  Executors.newFixedThreadPool(3);
// 创建 CompletionService
CompletionService<Integer> cs = new 
  ExecutorCompletionService<>(executor);
// 异步向电商 S1 询价
cs.submit(()->getPriceByS1());
// 异步向电商 S2 询价
cs.submit(()->getPriceByS2());
// 异步向电商 S3 询价
cs.submit(()->getPriceByS3());
// 将询价结果异步保存到数据库
for (int i=0; i<3; i++) {
  Integer r = cs.take().get();
  executor.execute(()->save(r));
}
```

### CompletionService 接口说明

下面详细地介绍一下 CompletionService 接口提供的方法，CompletionService 接口提供的方法有 5 个，这 5 个方法的方法签名如下所示：

```java
Future<V> submit(Callable<V> task);
Future<V> submit(Runnable task, V result);
Future<V> take() 
  throws InterruptedException;
Future<V> poll();
Future<V> poll(long timeout, TimeUnit unit) 
  throws InterruptedException;
```

其中，submit() 相关的方法有两个。一个方法参数是`Callable<V> task`，另外一个方法有两个参数，分别是`Runnable task`和`V result`，这个方法类似于 ThreadPoolExecutor 的 `<T> Future<T> submit(Runnable task, T result)` 。

CompletionService 接口其余的 3 个方法，都是和阻塞队列相关的，take()、poll() 都是从阻塞队列中获取并移除一个元素；它们的区别在于如果阻塞队列是空的，那么调用 take() 方法的线程会被阻塞，而 poll() 方法会返回 null 值。 `poll(long timeout, TimeUnit unit)` 方法支持以超时的方式获取并移除阻塞队列头部的一个元素，如果等待了 timeout unit 时间，阻塞队列还是空的，那么该方法会返回 null 值。

# CompletionService实践

Dubbo 中有一种叫做**Forking 的集群模式**，这种集群模式下，支持**并行地调用多个查询服务，只要有一个成功返回结果，整个服务就可以返回了**。

假设需要提供一个地址转坐标的服务，为了保证该服务的高可用和性能，你可以并行地调用 3 个地图服务商的 API，然后只要有 1 个正确返回了结果 r，那么地址转坐标这个服务就可以直接返回 r 了。这种集群模式可以容忍 2 个地图服务商服务异常，但缺点是消耗的资源偏多。

利用 CompletionService 可以快速实现 Forking 这种集群模式，代码如下所示：

首先我们创建了一个线程池 executor 、一个 CompletionService 对象 cs 和一个`Future<Integer>`类型的列表 futures，每次通过调用 CompletionService 的 submit() 方法提交一个异步任务，会返回一个 Future 对象，我们把这些 Future 对象保存在列表 futures 中。通过调用 `cs.take().get()`，我们能够拿到最快返回的任务执行结果，只要我们拿到一个正确返回的结果，就可以取消所有任务并且返回最终结果了。

```java
// 创建线程池
ExecutorService executor =
  Executors.newFixedThreadPool(3);
// 创建 CompletionService
CompletionService<Integer> cs =
  new ExecutorCompletionService<>(executor);
// 用于保存 Future 对象
List<Future<Integer>> futures =
  new ArrayList<>(3);
// 提交异步任务，并保存 future 到 futures 
futures.add(
  cs.submit(()->geocoderByS1()));
futures.add(
  cs.submit(()->geocoderByS2()));
futures.add(
  cs.submit(()->geocoderByS3()));
// 获取最快返回的任务执行结果
Integer r = 0;
try {
  // 只要有一个成功返回，则 break
  for (int i = 0; i < 3; ++i) {
    r = cs.take().get();
    // 简单地通过判空来检查是否成功返回
    if (r != null) {
      break;
    }
  }
} finally {
  // 取消所有任务
  for(Future<Integer> f : futures)
    f.cancel(true);
}
// 返回结果
return r;
```

# 总结

当需要批量提交异步任务的时候建议你使用 CompletionService。CompletionService 将线程池 Executor 和阻塞队列 BlockingQueue 的功能融合在了一起，能够让批量异步任务的管理更简单。除此之外，CompletionService 能够让异步任务的执行结果有序化，先执行完的先进入阻塞队列，利用这个特性，你可以轻松实现后续处理的有序性，避免无谓的等待，同时还可以快速实现诸如 Forking Cluster 这样的需求。

- CompletionService适合需要批量提交异步任务的场景
- CompletionService 可以让异步任务的执行结果有序化
- 通过CompletionService 可以快速实现Forking Cluster这样的需求



