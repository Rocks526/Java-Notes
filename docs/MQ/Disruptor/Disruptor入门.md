# Disruptor介绍

> GitHub官网：https://github.com/LMAX-Exchange/disruptor

### Disruptor背景

Disruptor是英国外汇交易公司LMAX开发的一个高性能队列，研发的初衷是解决内存队列的延迟问题（在性能测试中发现竟然与I/O操作处于同样的数量级）。基于Disruptor开发的系统单线程能支撑每秒600万订单，2010年在QCon演讲后，获得了业界关注。2011年，企业应用软件专家Martin Fowler专门撰写长文介绍。同年它还获得了Oracle官方的Duke大奖。

目前，包括Apache Storm、Camel、Log4j 2在内的很多知名项目都应用了Disruptor以获取高性能。

### Disruptor功能

从功能上来看，Disruptor 是实现了"队列"的功能，而且是一个有界队列。那么它的应用场景自然就是“生产者-消费者”模型的应用场合。

可以拿 JDK 的 BlockingQueue 做一个简单对比，我们知道 BlockingQueue 是一个 FIFO 队列，生产者(Producer)往队列里发布(publish)一项事件(或称之为"消息"也可以)时，消费者(Consumer)能获得通知；如果没有事件时，消费者被堵塞，直到生产者发布了新的事件。

这些都是 Disruptor 能做到的，与之不同的是，Disruptor 能做更多：

- 同一个"事件"可以有多个消费者，消费者之间既可以并行处理，也可以相互依赖形成处理的先后次序(形成一个依赖图)；
- 预分配用于存储事件内容的内存空间；
- 针对极高的性能目标而实现的极度优化和无锁的设计；

以上的描述虽然简单地指出了 Disruptor 是什么，但对于它"能做什么"还不是那么直截了当。一般性地来说，当你需要在两个独立的处理过程(两个线程)之间交换数据时，就可以使用 Disruptor 。当然使用队列（如上面提到的 BlockingQueue）也可以，只不过 Disruptor 做得更好。

### Disruptor应用

除了以上说的作为队列使用外，Disruptor一个很重要的用途是互联网核心链路应用场景。

在互联网中，对一些应用的核心链路常见的实现方式有两种：

- 传统的完全解耦的方式：此方式就是所有业务全部拆开，例如滴滴的网约车、顺风车都拆成不同的业务线，所有代码都解耦，但对应的，代码也没办法复用。
- 模板模式：对核心链路的整体流程做抽象，划分不同的阶段，具体业务可以采取不同的实现，可以做到尽可能的代码复用，但存在的问题是当业务存在重大变更时，可能会划分出不同的阶段，会影响所有的代码。

针对第二种方案的缺点，解决方式有两种：

- 领域模型的高度抽象：即对模板的所有阶段划分高度抽象，保证以后的业务变更也不会修改已有的阶段，但此种方式难以实现
- 利用一些框架完成各种流程/阶段的自动组装：例如有限状态机Spring-StateMachine和Disruptor

# Disruptor 的核心概念

先从了解 Disruptor 的核心概念开始，来了解它是如何运作的。下面介绍的概念模型，既是领域对象，也是映射到代码实现上的核心对象。

- Ring Buffer

如其名，环形的缓冲区。曾经 RingBuffer 是 Disruptor 中的最主要的对象，但从3.0版本开始，其职责被简化为仅仅负责对通过 Disruptor 进行交换的数据（事件）进行存储和更新。在一些更高级的应用场景中，Ring Buffer 可以由用户的自定义实现来完全替代。

- Sequence 

通过顺序递增的序号来编号管理通过其进行交换的数据（事件），对数据(事件)的处理过程总是沿着序号逐个递增处理。一个 Sequence 用于跟踪标识某个特定的事件处理者( RingBuffer/Consumer )的处理进度。虽然一个 AtomicLong 也可以用于标识进度，但定义 Sequence 来负责该问题还有另一个目的，那就是防止不同的 Sequence 之间的CPU缓存伪共享(Flase Sharing)问题。

- Sequencer 

Sequencer 是 Disruptor 的真正核心。此接口有两个实现类 SingleProducerSequencer、MultiProducerSequencer ，它们定义在生产者和消费者之间快速、正确地传递数据的并发算法。

- Sequence Barrier

用于保持对RingBuffer的 main published Sequence 和Consumer依赖的其它Consumer的 Sequence 的引用。 Sequence Barrier 还定义了决定 Consumer 是否还有可处理的事件的逻辑。

- Wait Strategy

定义 Consumer 如何进行等待下一个事件的策略。 （注：Disruptor 定义了多种不同的策略，针对不同的场景，提供了不一样的性能表现）主要策略有：

BlockingWaitStrategy：最低效的策略，但其对CPU的消耗最小并且在各种不同部署环境中能提供更加一致的性能表现；

SleepingWaitStrategy：性能表现跟 BlockingWaitStrategy 差不多，对 CPU 的消耗也类似，但其对生产者线程的影响最小，适合用于异步日志类似的场景；

YieldingWaitStrategy：性能是最好的，适合用于低延迟的系统。在要求极高性能且事件处理线数小于 CPU 逻辑核心数的场景中，推荐使用此策略；例如，CPU开启超线程的特性。

- Event

在 Disruptor 的语义中，生产者和消费者之间进行交换的数据被称为事件(Event)。它不是一个被 Disruptor 定义的特定类型，而是由 Disruptor 的使用者定义并指定。

- EventProcessor

EventProcessor 持有特定消费者(Consumer)的 Sequence，并提供用于调用事件处理实现的事件循环(Event Loop)。

- EventHandler

Disruptor 定义的事件处理接口，由用户实现，用于处理事件，是 Consumer 的真正实现。

- Producer

即生产者，只是泛指调用 Disruptor 发布事件的用户代码，Disruptor 没有定义特定接口或类型。

![img](http://rocks526.top/lzx/211551023129327.png)

# Disruptor入门案例

Disruptor 的 API 十分简单，主要有以下几个步骤：

1. 定义事件

> 事件(Event)就是通过 Disruptor 进行交换的数据类型。

```java
package com.rocks.quickstart;

import lombok.Data;
import lombok.experimental.Accessors;

import java.io.Serializable;

/**
 * Event事件对象
 */
@Data
@Accessors(chain = true)
public class Order implements Serializable {

    private String Id;

    private String orderName;

    private String orderContent;

    private Double orderPrice;


}
```

2. 定义事件工厂

> 事件工厂(Event Factory)定义了如何实例化前面第1步中定义的事件(Event)，需要实现接口 com.lmax.disruptor.EventFactory\<T\>。
> Disruptor 通过 EventFactory 在 RingBuffer 中预创建 Event 的实例。
> 一个 Event 实例实际上被用作一个"数据槽"，发布者发布前，先从 RingBuffer 获得一个 Event 的实例，然后往 Event 实例中填充数据，之后再发布到 RingBuffer 中，之后由 Consumer 获得该 Event 实例并从中读取数据。

```java
package com.rocks.quickstart;

import com.lmax.disruptor.EventFactory;

import java.util.UUID;

/**
 * EventFactory 事件工厂 用于提前创建Event对象放于环形缓冲区
 */
public class OrderEventFactory implements EventFactory<Order> {
    // 定义如何创建Event对象的方法
    @Override
    public Order newInstance() {
        // 给订单一个UUID
        return new Order().setId(UUID.randomUUID().toString());
    }
}
```

3. 定义事件处理的具体实现

> 通过实现接口 com.lmax.disruptor.EventHandler\<T\> 定义事件处理的具体实现，可以实现多个Handler，一个事件可以多次处理。
>
> 事件处理器可以通过实现EventHandler和WorkerHandler接口两种方式实现，EventHandler接口附带了一些序列号信息，当使用多消费者模型时，必须通过WorkerHandler实现。

```java
package com.rocks.quickstart;

import com.lmax.disruptor.EventHandler;

/**
 * 事件处理逻辑
 */
public class OrderHandler implements EventHandler<Order> {
    /**
     *
     * @param event      发布的事件
     * @param sequence   事件的序号
     * @param endOfBatch 是否是批次的最后一个事件
     * @throws Exception
     */
    @Override
    public void onEvent(Order event, long sequence, boolean endOfBatch) throws Exception {
        System.out.println("序列号:" + sequence + ", 订单内容:" + event);
    }
}
```

4. 定义用于事件处理的线程池

> Disruptor 通过 java.util.concurrent.ExecutorService 提供的线程来触发 Consumer 的事件处理。

```java
ExecutorService executor = Executors.newCachedThreadPool();
```

5. 指定等待策略

```java
WaitStrategy BLOCKING_WAIT = new BlockingWaitStrategy();
WaitStrategy SLEEPING_WAIT = new SleepingWaitStrategy();
WaitStrategy YIELDING_WAIT = new YieldingWaitStrategy();
```

6. 启动 Disruptor

```java
ExecutorService executorService = Executors.newFixedThreadPool(1);
        OrderEventFactory orderEventFactory = new OrderEventFactory();
        int ringBufferSize = 1024*1024;

        // 创建Disruptor
        Disruptor<Order> orderDisruptor = new Disruptor<Order>(
                // 创建事件的工厂
                orderEventFactory,
                // 环形缓冲区大小 要求2的N次方
                ringBufferSize,
                // 用来调用Consumer的线程池
                executorService,
                // 单生产者模式
                ProducerType.SINGLE,
                // 等待策略
                new BlockingWaitStrategy()

        );

        // 设置事件处理器
        orderDisruptor.handleEventsWith(new OrderHandler());

        // 启动Disruptor
        orderDisruptor.start();
```

7. 发布事件

Disruptor 的事件发布过程：
　　第一步：先从 RingBuffer 获取下一个可以写入的事件的序号；
　　第二步：获取对应的事件对象，将数据写入事件对象；
　　第三部：将事件提交到 RingBuffer;
事件只有在提交之后才会通知 EventProcessor 进行处理；

> 事件的发布有两种方式：
>
> 第一种是通过disruptor获取RingBuffer，通过RingBuffer获取下一个序列对象，然后填充属性进行提交，需要注意一定要finally确保提交。
>
> 为了简化操作，Disruptor提供了第二种方式：通过实现EventTranslator接口来保证提交。

```java
        // 发布事件
        RingBuffer<Order> ringBuffer = orderDisruptor.getRingBuffer();

        Random random = new Random();
        for (int i=0;i<100;i++){
            // 获取下一个序列号
            long nextSeq = ringBuffer.next();
            try{
                // 根据序列号获取Event
                Order order = ringBuffer.get(nextSeq);
                // 设置属性
                order.setOrderName("订单" + i);
                order.setOrderPrice(random.nextDouble()*100);
            }finally {
                // 发布事件
                ringBuffer.publish(nextSeq);
            }
        }
```

8. 关闭 Disruptor

```java
disruptor.shutdown();//关闭 disruptor，方法会堵塞，直至所有的事件都得到处理；
executor.shutdown();//关闭 disruptor 使用的线程池；如果需要的话，必须手动关闭， disruptor 在 shutdown 时不会自动关闭；
```

- 整体代码如下所示：

```java
package com.rocks.quickstart;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.RingBuffer;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

import java.util.Random;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Disruptor快速入门
 */
public class Main {

    public static void main(String[] args) {

        ExecutorService executorService = Executors.newFixedThreadPool(1);
        OrderEventFactory orderEventFactory = new OrderEventFactory();
        int ringBufferSize = 1024*1024;

        // 创建Disruptor
        Disruptor<Order> orderDisruptor = new Disruptor<Order>(
                // 创建事件的工厂
                orderEventFactory,
                // 环形缓冲区大小 要求2的N次方
                ringBufferSize,
                // 用来调用Consumer的线程池
                executorService,
                // 单生产者模式
                ProducerType.SINGLE,
                // 等待策略
                new BlockingWaitStrategy()

        );

        // 设置事件处理器
        orderDisruptor.handleEventsWith(new OrderHandler());

        // 启动Disruptor
        orderDisruptor.start();

        // 发布事件
        RingBuffer<Order> ringBuffer = orderDisruptor.getRingBuffer();

        Random random = new Random();
        for (int i=0;i<100;i++){
            // 获取下一个序列号
            long nextSeq = ringBuffer.next();
            try{
                // 根据序列号获取Event
                Order order = ringBuffer.get(nextSeq);
                // 设置属性
                order.setOrderName("订单" + i);
                order.setOrderPrice(random.nextDouble()*100);
            }finally {
                // 发布事件
                ringBuffer.publish(nextSeq);
            }
        }

        // 关闭Disruptor
        orderDisruptor.shutdown();
        executorService.shutdown();
    }

}
```

# Disruptor与BlockingQueue性能对比测试

- 基础类

```java
package com.rocks.performance;

import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

import java.io.Serializable;

/**
 * 测试数据
 */
@lombok.Data
@AllArgsConstructor
@NoArgsConstructor
public class Data implements Serializable {

	private static final long serialVersionUID = 2035546038986494352L;
	private Long id ;
	private String name;

}
```

```java
package com.rocks.performance;

/**
 * 常量类
 */
public class Constants {

	public static final Integer EVENT_NUM_OHM = 100000000;

	public static final Integer EVENT_NUM_FM = 50000000;

	public static final Integer EVENT_NUM_OM = 10000000;
	
}
```

- ArrayBlockingQueue

```java
pacage com.rocks.performance;

import java.util.concurrent.ArrayBlockingQueue;

/**
 * ArrayBlockingQueueTest
 */
public class ArrayBlockingQueueTest {

    public static void main(String[] args) {

        // 创建ArrayBlockingQueue
        ArrayBlockingQueue<Data> blockingQueue = new ArrayBlockingQueue<>(Constants.EVENT_NUM_OHM);
        // 开始时间
        long startTime = System.currentTimeMillis();

        //向容器中添加元素
        new Thread(new Runnable() {

            public void run() {
                long i = 0;
                while (i < Constants.EVENT_NUM_OHM) {
                    Data data = new Data(i, "c" + i);
                    try {
                        blockingQueue.put(data);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    i++;
                }
            }
        }).start();

        // 消费元素
        new Thread(new Runnable() {
            public void run() {
                int k = 0;
                while (k < Constants.EVENT_NUM_OHM) {
                    try {
                        blockingQueue.take();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    k++;
                }
                long endTime = System.currentTimeMillis();
                System.out.println("ArrayBlockingQueue costTime = " + (endTime - startTime) + "ms");
            }
        }).start();


    }

}
```

- Disruptor

```java
package com.rocks.performance;

import java.util.concurrent.Executors;

import com.lmax.disruptor.BlockingWaitStrategy;
import com.lmax.disruptor.BusySpinWaitStrategy;
import com.lmax.disruptor.EventFactory;
import com.lmax.disruptor.RingBuffer;
import com.lmax.disruptor.YieldingWaitStrategy;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.dsl.ProducerType;

/**
 * Disruptor单线程测试
 */
public class DisruptorSingleTest {

    public static void main(String[] args) {
        int ringBufferSize = 65536;
        final Disruptor<Data> disruptor = new Disruptor<Data>(
                 new EventFactory<Data>() {
					public Data newInstance() {
						return new Data();
					}
				},
                ringBufferSize,
                Executors.newSingleThreadExecutor(),
                ProducerType.SINGLE, 
                //new BlockingWaitStrategy()
                new YieldingWaitStrategy()
        		);

        DataConsumer consumer = new DataConsumer();
        //消费数据
        disruptor.handleEventsWith(consumer);
        disruptor.start();

        // 发布事件
        new Thread(new Runnable() {
            public void run() {
                RingBuffer<Data> ringBuffer = disruptor.getRingBuffer();
                for (long i = 0; i < Constants.EVENT_NUM_OHM; i++) {
                    long seq = ringBuffer.next();
                    Data data = ringBuffer.get(seq);
                    data.setId(i);
                    data.setName("c" + i);
                    ringBuffer.publish(seq);
                }
            }
        }).start();
    }
}
```

```java
package com.rocks.performance;

import com.lmax.disruptor.EventHandler;

public class DataConsumer implements EventHandler<Data> {

    private long startTime;
    private int i;

    public DataConsumer() {
        this.startTime = System.currentTimeMillis();
    }

    public void onEvent(Data data, long seq, boolean bool)
            throws Exception {
        i++;
        if (i == Constants.EVENT_NUM_OHM) {
            long endTime = System.currentTimeMillis();
            System.out.println("Disruptor costTime = " + (endTime - startTime) + "ms");
        }
    }

}
```

- 测试结果

测试一亿数据，ArrayBlockingQueue耗时34226ms，Disruptor采用单生产者单消费者，单线程耗时14231ms。