- [Disruptor的串行操作](#cxcz)
- [Disruptor的并行操作](#bxcz)
- [Disruptor的菱形操作](#lxcz)
- [Disruptor的多边形操作](#dbxcz)

-----------------------

> 之前提到过，在Disruptor中，对一个Event事件允许有多个Handler处理器，多个Handler处理器之间可以进行串行操作，也可以进行并行操作。

在正式开始测试之前，先准备一下数据：

假设存在电商用户下单的场景，整个订单的生成逻辑需要以下五步：

1. 填充订单基本信息(耗时1s)
2. 计算订单金额(耗时1s)
3. 填充订单用户信息(耗时1s)
4. 填充订单商家信息(耗时2s)
5. 生成订单并打印(无耗时)

订单的ID在发布事件时通过随机数生成。

相关基础类如下：

- Order事件对象

```java
package com.rocks.high.chain;

import lombok.Data;
import java.io.Serializable;

/**
 * Event事件
 */
@Data
public class Order implements Serializable {

    private String Id;

    private String orderName;

    private String createTime;

    private Double totalPrice;

    private String userInfo;

    private String businessInfo;

}
```

- 事件发布对象

```java
package com.rocks.high.chain;

import com.lmax.disruptor.EventTranslator;
import com.lmax.disruptor.dsl.Disruptor;

import java.util.Random;
import java.util.UUID;
import java.util.concurrent.CountDownLatch;

public class OrderPublisher implements Runnable {

    private static final int PUBLISH_COUNT = 1;

    private CountDownLatch countDownLatch;

    private Disruptor<Order> disruptor;

    public OrderPublisher(CountDownLatch countDownLatch, Disruptor<Order> disruptor) {
        this.countDownLatch = countDownLatch;
        this.disruptor = disruptor;
    }

    @Override
    public void run() {
        OrderEventTranslator eventTranslator = new OrderEventTranslator();
        for(int i =0; i < PUBLISH_COUNT; i ++){
            disruptor.publishEvent(eventTranslator);
        }
        countDownLatch.countDown();
    }
}

class OrderEventTranslator implements EventTranslator<Order> {

    private Random random = new Random();

    public void translateTo(Order event, long sequence) {
        this.generateTrade(event);
    }

    private void generateTrade(Order event) {
        event.setId(UUID.randomUUID().toString());
    }

}
```

- 五个处理器

```java
package com.rocks.high.chain;

import com.lmax.disruptor.EventHandler;

import java.util.ArrayList;
import java.util.Date;
import java.util.Random;

// Handler1 设置订单基本信息 通过set模拟数据
public class Handler1 implements EventHandler<Order> {

    private ArrayList<String> orderNames = new ArrayList<>();

    private Random random = new Random();

    public Handler1(){
        orderNames.add("服装订单");
        orderNames.add("玩具订单");
        orderNames.add("食品订单");
        orderNames.add("游戏用品订单");
        orderNames.add("宠物用品订单");
        orderNames.add("电子产品订单");
        orderNames.add("日常用品订单");
        orderNames.add("水果外卖订单");
    }

    @Override
    public void onEvent(Order event, long sequence, boolean endOfBatch) throws Exception {
        event.setOrderName(orderNames.get(random.nextInt(orderNames.size())));
        event.setCreateTime(new Date().toString());
        Thread.sleep(1000);
    }
}
```

```java
package com.rocks.high.chain;

import com.lmax.disruptor.EventHandler;

import java.util.ArrayList;
import java.util.Date;
import java.util.Random;

// Handler2 计算订单的总金额 随机值模拟
public class Handler2 implements EventHandler<Order> {

    private Random random = new Random();

    @Override
    public void onEvent(Order event, long sequence, boolean endOfBatch) throws Exception {
        event.setTotalPrice(random.nextDouble()*999);
        Thread.sleep(1000);
    }
}
```

```java
package com.rocks.high.chain;

import com.lmax.disruptor.EventHandler;

import java.util.ArrayList;
import java.util.Date;
import java.util.Random;

// Handler3 填充订单的用户信息 模拟数据
public class Handler3 implements EventHandler<Order> {

    private ArrayList<String> users = new ArrayList<>();

    private Random random = new Random();

    public Handler3(){
        users.add("Rocks");
        users.add("Lucy");
        users.add("Jack");
        users.add("Tim");
        users.add("Time");
        users.add("Tack");
        users.add("Deni");
        users.add("Taro");
    }

    @Override
    public void onEvent(Order event, long sequence, boolean endOfBatch) throws Exception {
        event.setUserInfo("User is " + users.get(random.nextInt(users.size())));
        Thread.sleep(1000);
    }
}
```

```java
package com.rocks.high.chain;

import com.lmax.disruptor.EventHandler;

import java.util.ArrayList;
import java.util.Date;
import java.util.Random;

// Handler4 填充商家信息 模拟数据
public class Handler4 implements EventHandler<Order> {

    private ArrayList<String> business = new ArrayList<>();

    private Random random = new Random();

    public Handler4(){
        business.add("淘宝商家");
        business.add("天猫商家");
        business.add("京东商家");
        business.add("拼多多商家");
        business.add("当当商家");
        business.add("小红书商家");
        business.add("唯品会商家");
    }

    @Override
    public void onEvent(Order event, long sequence, boolean endOfBatch) throws Exception {
        event.setBusinessInfo("商家为: " + business.get(random.nextInt(business.size())));
        Thread.sleep(2000);
    }
}
```

```java
package com.rocks.high.chain;

import com.lmax.disruptor.EventHandler;

import java.util.ArrayList;
import java.util.Date;
import java.util.Random;

// Handler5 打印订单信息 ==> 模拟生成订单
public class Handler5 implements EventHandler<Order> {

    @Override
    public void onEvent(Order event, long sequence, boolean endOfBatch) throws Exception {
        System.out.println("订单信息为:" + event);
    }
}
```

- 主函数

```java
package com.rocks.high.chain;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 串并行操作测试
 */
public class Main {

    public static void main(String[] args) throws InterruptedException {

        // 发布者线程池
        ExecutorService executorService = Executors.newFixedThreadPool(8);
        // Disruptor的消费者线程池 单消费者模式 一个handler需要绑定一个线程 如果线程数少于handler数会出错
        ExecutorService threadPool = Executors.newFixedThreadPool(8);
        // 计数器 等待发布者发送全部消息
        CountDownLatch countDownLatch = new CountDownLatch(1);

        // 1.创建Disruptor
        Disruptor<Order> disruptor = new Disruptor<Order>(
                new EventFactory<Order>() {
                    @Override
                    public Order newInstance() {
                        return new Order();
                    }
                },
                1024*1024,
                threadPool,
                ProducerType.SINGLE,
                new BlockingWaitStrategy()
        );

        // 2.设置handler处理器
        Handler1 h1 = new Handler1();
        Handler2 h2 = new Handler2();
        Handler3 h3 = new Handler3();
        Handler4 h4 = new Handler4();
        Handler5 h5 = new Handler5();

        // 3.启动Disruptor
        disruptor.start();

        // 计数器
        long begin = System.currentTimeMillis();
        // 发布事件
        executorService.submit(new OrderPublisher(countDownLatch,disruptor));

        countDownLatch.await();

        disruptor.shutdown();
        threadPool.shutdown();
        executorService.shutdown();

        System.err.println("总耗时: " + (System.currentTimeMillis() - begin));
    }

}
```

> 不同的调用方式，都在disruptor设置handler时实现。

# <a id="cxcz">Disruptor的串行操作</a>

![image-20210111153140360](http://rocks526.top/lzx/image-20210111153140360.png)

串行操作即事件发布之后，五个handler依次处理，总耗时为5S+，具体代码如下：

```java
package com.rocks.high.chain;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 串并行操作测试
 */
public class Main {

    public static void main(String[] args) throws InterruptedException {

        // 发布者线程池
        ExecutorService executorService = Executors.newFixedThreadPool(8);
        // Disruptor的消费者线程池 单消费者模式 一个handler需要绑定一个线程 如果线程数少于handler数会出错
        ExecutorService threadPool = Executors.newFixedThreadPool(8);
        // 计数器 等待发布者发送全部消息
        CountDownLatch countDownLatch = new CountDownLatch(1);

        // 1.创建Disruptor
        Disruptor<Order> disruptor = new Disruptor<Order>(
                new EventFactory<Order>() {
                    @Override
                    public Order newInstance() {
                        return new Order();
                    }
                },
                1024*1024,
                threadPool,
                ProducerType.SINGLE,
                new BlockingWaitStrategy()
        );

        // 2.设置handler处理器
        Handler1 h1 = new Handler1();
        Handler2 h2 = new Handler2();
        Handler3 h3 = new Handler3();
        Handler4 h4 = new Handler4();
        Handler5 h5 = new Handler5();

        // 2.1 所有操作串行 需要链式调用 每个handlerEventsWith会返回一个处理器组
        disruptor
                .handleEventsWith(h1)
                .handleEventsWith(h2)
                .handleEventsWith(h3)
                .handleEventsWith(h4)
                .handleEventsWith(h5);

        // 3.启动Disruptor
        disruptor.start();

        // 计数器
        long begin = System.currentTimeMillis();
        // 发布事件
        executorService.submit(new OrderPublisher(countDownLatch,disruptor));

        countDownLatch.await();

        disruptor.shutdown();
        threadPool.shutdown();
        executorService.shutdown();

        System.err.println("总耗时: " + (System.currentTimeMillis() - begin));
    }

}
```

![image-20210111153331113](http://rocks526.top/lzx/image-20210111153331113.png)

五个handler一个会sleep五秒，多出来的600毫秒是最后打印，还有线程阻塞唤醒消耗的时间。

# <a id="bxcz">Disruptor的并行操作</a>

![image-20210111153622405](http://rocks526.top/lzx/image-20210111153622405.png)

并行操作即事件发布之后，五个handler同时处理，总耗时为handler处理中耗时最长的一个(handler4的sleep两秒)，总耗时应该为2S+，具体代码如下：

> 在使用单消费者模式时，disruptor的线程池的线程数量必须大于并行的handler数量，否则会出问题。

```java
package com.rocks.high.chain;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 串并行操作测试
 */
public class Main {

    public static void main(String[] args) throws InterruptedException {

        // 发布者线程池
        ExecutorService executorService = Executors.newFixedThreadPool(8);
        // Disruptor的消费者线程池 单消费者模式 一个handler需要绑定一个线程 如果线程数少于handler数会出错
        ExecutorService threadPool = Executors.newFixedThreadPool(8);
        // 计数器 等待发布者发送全部消息
        CountDownLatch countDownLatch = new CountDownLatch(1);

        // 1.创建Disruptor
        Disruptor<Order> disruptor = new Disruptor<Order>(
                new EventFactory<Order>() {
                    @Override
                    public Order newInstance() {
                        return new Order();
                    }
                },
                1024*1024,
                threadPool,
                ProducerType.SINGLE,
                new BlockingWaitStrategy()
        );

        // 2.设置handler处理器
        Handler1 h1 = new Handler1();
        Handler2 h2 = new Handler2();
        Handler3 h3 = new Handler3();
        Handler4 h4 = new Handler4();
        Handler5 h5 = new Handler5();

        // 2.2 所有操作并行 生成多个处理器组即可 可以一次传递多个handler 也可以调用多次handleEventsWith方法
        disruptor.handleEventsWith(h1,h2,h3,h4,h5);

        // 3.启动Disruptor
        disruptor.start();

        // 计数器
        long begin = System.currentTimeMillis();
        // 发布事件
        executorService.submit(new OrderPublisher(countDownLatch,disruptor));

        countDownLatch.await();

        disruptor.shutdown();
        threadPool.shutdown();
        executorService.shutdown();

        System.err.println("总耗时: " + (System.currentTimeMillis() - begin));
    }

}

```

![image-20210111153958166](http://rocks526.top/lzx/image-20210111153958166.png)

 由于多线程并行，部分信息设置成功，部分信息设置失败。

# <a id="lxcz">Disruptor的菱形操作</a>

![image-20210111154404380](http://rocks526.top/lzx/image-20210111154404380.png)

在上一个所有handler并行时，由于最后打印订单依赖于前面的执行结果，因此导致打印订单错误，因此需要等前面的handler执行完最后再执行打印订单操作。总体耗时为handler1的1S，handler4的2S，总计3S+。代码实现如下：

```java
package com.rocks.high.chain;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 串并行操作测试
 */
public class Main {

    public static void main(String[] args) throws InterruptedException {

        // 发布者线程池
        ExecutorService executorService = Executors.newFixedThreadPool(8);
        // Disruptor的消费者线程池 单消费者模式 一个handler需要绑定一个线程 如果线程数少于handler数会出错
        ExecutorService threadPool = Executors.newFixedThreadPool(8);
        // 计数器 等待发布者发送全部消息
        CountDownLatch countDownLatch = new CountDownLatch(1);

        // 1.创建Disruptor
        Disruptor<Order> disruptor = new Disruptor<Order>(
                new EventFactory<Order>() {
                    @Override
                    public Order newInstance() {
                        return new Order();
                    }
                },
                1024*1024,
                threadPool,
                ProducerType.SINGLE,
                new BlockingWaitStrategy()
        );

        // 2.设置handler处理器
        Handler1 h1 = new Handler1();
        Handler2 h2 = new Handler2();
        Handler3 h3 = new Handler3();
        Handler4 h4 = new Handler4();
        Handler5 h5 = new Handler5();

        // 2.3 菱形操作 实现方式有以下两种
        disruptor.handleEventsWith(h1).handleEventsWith(h2,h3,h4).handleEventsWith(h5);
//        disruptor.handleEventsWith(h1).then(h2,h3,h4).then(h5);


        // 3.启动Disruptor
        disruptor.start();

        // 计数器
        long begin = System.currentTimeMillis();
        // 发布事件
        executorService.submit(new OrderPublisher(countDownLatch,disruptor));

        countDownLatch.await();

        disruptor.shutdown();
        threadPool.shutdown();
        executorService.shutdown();

        System.err.println("总耗时: " + (System.currentTimeMillis() - begin));
    }

}
```

![image-20210111154821530](http://rocks526.top/lzx/image-20210111154821530.png)

耗时3S5，订单所有信息填充完成。

# <a id="dbxcz">Disruptor的多边形操作</a>

在上一个菱形计算的示例中，订单用户信息填充和订单商家信息填充是可以并行的，假设他们之间存在依赖关系，填充商家订单之前需要先填充用户订单，计算金额之前需要填充订单基本信息，则形成以下依赖关系图：

![image-20210111155233967](http://rocks526.top/lzx/image-20210111155233967.png)

Disruptor实现上面这种多边形流程图，预计耗时3S+，实现代码如下：

```java
package com.rocks.high.chain;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 串并行操作测试
 */
public class Main {

    public static void main(String[] args) throws InterruptedException {

        // 发布者线程池
        ExecutorService executorService = Executors.newFixedThreadPool(8);
        // Disruptor的消费者线程池 单消费者模式 一个handler需要绑定一个线程 如果线程数少于handler数会出错
        ExecutorService threadPool = Executors.newFixedThreadPool(8);
        // 计数器 等待发布者发送全部消息
        CountDownLatch countDownLatch = new CountDownLatch(1);

        // 1.创建Disruptor
        Disruptor<Order> disruptor = new Disruptor<Order>(
                new EventFactory<Order>() {
                    @Override
                    public Order newInstance() {
                        return new Order();
                    }
                },
                1024*1024,
                threadPool,
                ProducerType.SINGLE,
                new BlockingWaitStrategy()
        );

        // 2.设置handler处理器
        Handler1 h1 = new Handler1();
        Handler2 h2 = new Handler2();
        Handler3 h3 = new Handler3();
        Handler4 h4 = new Handler4();
        Handler5 h5 = new Handler5();

        // 2.4 多边形操作
        disruptor.handleEventsWith(h1,h3);
        disruptor.after(h1).handleEventsWith(h2);
        disruptor.after(h3).handleEventsWith(h4);
        disruptor.after(h2,h4).handleEventsWith(h5);


        // 3.启动Disruptor
        disruptor.start();

        // 计数器
        long begin = System.currentTimeMillis();
        // 发布事件
        executorService.submit(new OrderPublisher(countDownLatch,disruptor));

        countDownLatch.await();

        disruptor.shutdown();
        threadPool.shutdown();
        executorService.shutdown();

        System.err.println("总耗时: " + (System.currentTimeMillis() - begin));
    }

}
```

![image-20210111155540863](http://rocks526.top/lzx/image-20210111155540863.png)

订单信息填充完成，耗时3.8S。

# 总结

> 通过以上示例可以看出，Disruptor可以很容易的实现多任务并行计算，实现任务之间的依赖关系，并且语义性很强。
>
> Main函数的所有代码如下：

```java
package com.rocks.high.chain;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 串并行操作测试
 */
public class Main {

    public static void main(String[] args) throws InterruptedException {

        // 发布者线程池
        ExecutorService executorService = Executors.newFixedThreadPool(8);
        // Disruptor的消费者线程池 单消费者模式 一个handler需要绑定一个线程 如果线程数少于handler数会出错
        ExecutorService threadPool = Executors.newFixedThreadPool(8);
        // 计数器 等待发布者发送全部消息
        CountDownLatch countDownLatch = new CountDownLatch(1);

        // 1.创建Disruptor
        Disruptor<Order> disruptor = new Disruptor<Order>(
                new EventFactory<Order>() {
                    @Override
                    public Order newInstance() {
                        return new Order();
                    }
                },
                1024*1024,
                threadPool,
                ProducerType.SINGLE,
                new BlockingWaitStrategy()
        );

        // 2.设置handler处理器
        Handler1 h1 = new Handler1();
        Handler2 h2 = new Handler2();
        Handler3 h3 = new Handler3();
        Handler4 h4 = new Handler4();
        Handler5 h5 = new Handler5();


        /**
         * // 2.1 所有操作串行 需要链式调用 每个handlerEventsWith会返回一个处理器组
        disruptor
                .handleEventsWith(h1)
                .handleEventsWith(h2)
                .handleEventsWith(h3)
                .handleEventsWith(h4)
                .handleEventsWith(h5);
         **/

        /**
        // 2.2 所有操作并行 生成多个处理器组即可 可以一次传递多个handler 也可以调用多次handleEventsWith方法
        disruptor.handleEventsWith(h1,h2,h3,h4,h5);
         **/

        /**
        // 2.3 菱形操作 实现方式有以下两种
        disruptor.handleEventsWith(h1).handleEventsWith(h2,h3,h4).handleEventsWith(h5);
        disruptor.handleEventsWith(h1).then(h2,h3,h4).then(h5);
        **/

        // 2.4 多边形操作
        disruptor.handleEventsWith(h1,h3);
        disruptor.after(h1).handleEventsWith(h2);
        disruptor.after(h3).handleEventsWith(h4);
        disruptor.after(h2,h4).handleEventsWith(h5);


        // 3.启动Disruptor
        disruptor.start();

        // 计数器
        long begin = System.currentTimeMillis();
        // 发布事件
        executorService.submit(new OrderPublisher(countDownLatch,disruptor));

        countDownLatch.await();

        disruptor.shutdown();
        threadPool.shutdown();
        executorService.shutdown();

        System.err.println("总耗时: " + (System.currentTimeMillis() - begin));
    }

}
```

