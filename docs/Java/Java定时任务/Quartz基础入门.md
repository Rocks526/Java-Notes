# 一：Quartz快速入门

### 1.1 Quartz介绍

Quartz是OpenSymphony开源组织在Job scheduling领域又一个开源项目，是一个传统的企业级定时任务框架，可以用来构建运行上百个甚至上万个定时任务的复杂场景。

### 1.2 Quartz的特点

- Quartz源码由Java编写，实现了一个简单又强大的任务调度机制
- Quartz支持持久性作业，即可以实现对一个任务多个调度的状态保留
- Quartz可以单独使用，也可以与Servlet/Spring/SpringBoot集成，SpringBoot提供了Quartz的starter
- Quartz支持Cron/定时间隔等多个定时任务
- Quartz支持集群，集群模式会将所有任务信息存储到db，各个服务从db获取任务信息，相互负载

> 相比Elastic-job的分布式，Quartz的集群模式不支持单个任务的负载，所有的定时任务以单个任务为单位分配到每个节点上。

### 1.3 Quartz的核心概念

- Job：表示一个具体任务的抽象，需要实现一个execute接口，描述要执行的任务内容
- JobDetail：具体要调度的任务实例，除了包含Job之外，还有一些附带的属性/分组等信息
- Tigger：触发器，用来控制JobDetail被调度的时间/策略等
- Scheduler：调度器，接收JobDetail和Tigger两个参数，实现根据Tigger的策略调度JobDetail，是一个容器，可以接收很多组JobDeatil和Tigger
- JobBuilder：用于定义/构建 JobDetail 实例，用于定义作业的实例
- TriggerBuilder：用于定义/构建触发器实例。
- DateBuilder：用于方便的构造触发器触发时间。
- Key：将 Job 和 Trigger 注册到 Scheduler 时，可以为它们设置 key，配置其身份属性。 Job 和 Trigger 的 key（JobKey 和 TriggerKey）可以用于将 Job 和 Trigger 放到不同的分组（group）里，然后基于分组进行操作。同一个分组下的 Job 或 Trigger 的名称必须唯一

> Scheduler 的生命期，从 SchedulerFactory 创建它时开始，到 Scheduler 调用shutdown() 方法时结束；Scheduler 被创建后，可以增加、删除和列举 Job 和 Trigger，以及执行其它与调度相关的操作（如暂停 Trigger）。但是，Scheduler 只有在调用 start() 方法后，才会真正地触发 trigger（即执行 job）

### 1.4 Quartz快速入门

- 创建Maven项目，引入Quartz依赖

```xml
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.3.2</version>
        </dependency>
```


- 编写任务Job的实现类

```java
package com.rocks.quartz.job;

import org.apache.commons.lang3.time.DateFormatUtils;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import java.util.Date;

/**
 * Job实现类 
 * @author lizhaoxuan
 */
public class SimpleJob implements Job {

    private final String DATE_FORMAT_PATTERN = "yyyy-MM-dd HH:mm:ss";
    
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        // 任务执行
        System.out.println("Job executed, Time: " + DateFormatUtils.format(new Date(),DATE_FORMAT_PATTERN));
    }
}
```

- 编写主程序

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;


/**
 * Quartz快速入门
 * @author lizhaoxuan
 */
public class Demo1 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

# 二：Job

### 2.1 Job与JobDetail

在上面介绍过，Job是任务的抽象接口，我们需要自定义子类实现Job接口，重写execute方法，表明任务的执行逻辑。

JobDetail实例是通过JobBuilder类创建的，包含一个任务实例的属性信息。

在主程序中，我们传给scheduler一个JobDetail实例，因为我们在创建JobDetail时，将要执行的job的类名传给了JobDetail，所以scheduler就知道了要执行何种类型的job；每次当scheduler执行job时，在调用其execute(…)方法之前会`创建该类的一个新的实例，执行完毕，对该实例的引用就被丢弃了，实例会被垃圾回收`；这种执行策略带来的一个后果是，`job必须有一个无参的构造函数（当使用默认的JobFactory时）`；另一个后果是，在job类中，不应该定义有状态的数据属性，因为在job的多次执行中，这些属性的值不会保留。

那么如何给job实例增加属性或配置呢？如何在job的多次执行中，跟踪job的状态呢？

答案就是：`JobDataMap`，JobDetail对象属性的一部分。

- JobDetail实例化验证代码

```java
package com.rocks.quartz.job;

import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

/**
 * JobDetail实例化验证
 * @author lizhaoxuan
 */
public class MyJob1 implements Job {

    public MyJob1(){
        System.out.println("new MyJob1...");
    }

    private int i = 0;

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        i++;
        System.out.println("i: " + i);
    }
}
```

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob1;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * JobDetail实例化验证
 * @author lizhaoxuan
 */
public class Demo2 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob1.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

- JobDetail需要无参构造函数验证（默认JobFactory）

```java
package com.rocks.quartz.job;

import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

import java.util.Date;

/**
 * JobDetail无参构造函数验证
 * @author lizhaoxuan
 */
public class MyJob3 implements Job {

    public MyJob3(String msg){
        System.out.println("msg....");
    }

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println("job execute, Time: " + new Date());
    }
}
```

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob1;
import com.rocks.quartz.job.MyJob3;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * JobDetail实例化无参构造函数验证
 * @author lizhaoxuan
 */
public class Demo3 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob3.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

> MyJob3被调度时，抛出异常信息：SchedulerException: Problem instantiating class 'com.rocks.quartz.job.MyJob3'

### 2.2 JobDataMap传递参数

JobDataMap中可以包含不限量的数据对象，在job实例执行的时候，可以使用其中的数据；JobDataMap是Java Map接口的一个实现，额外增加了一些便于存取基本类型的数据的方法。

将job加入到scheduler之前，在构建JobDetail时，可以将数据放入JobDataMap。

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob4;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * 通过JobDataMap传递数据
 * @author lizhaoxuan 
 */
public class Demo4 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob4.class)
                // 通过JobDataMap传递数据
                .usingJobData("msg","send msg by jobDataMap!")
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // trigger也有jobDataMap
                .usingJobData("msg","send msg by trigger jobDataMap!")
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }
    
}
```

JobDataMap数据的获取可以通过`任务执行上下文context`和`get/set方法`获取：

```java
package com.rocks.quartz.job;

import lombok.Getter;
import lombok.Setter;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

/**
 * JobDataMap传递数据
 * @author lizhaoxuan
 */
public class MyJob4 implements Job {

    /**
     * 通过get/set方法获取jobDataMap里的数据
     * 在实例化时,会自动调用set map的key必须和属性名称一致
     */
    @Getter@Setter
    private String msg;

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println("msg: " + msg);
    }
}
```

```java
package com.rocks.quartz.job;

import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

/**
 * 通过JobDataMap获取数据
 * @author lizhaoxuan
 */
public class MyJob5 implements Job {

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        // 可以获取job和trigger的map
        Object msg = jobExecutionContext.get("msg");
        String msg1 = jobExecutionContext.getMergedJobDataMap().getString("msg");
        // 获取job的map
        String msg2 = jobExecutionContext.getJobDetail().getJobDataMap().getString("msg");
        String msg3 = jobExecutionContext.getTrigger().getJobDataMap().getString("msg");
        System.out.println(msg1);
        System.out.println(msg1);
        System.out.println(msg2);
        System.out.println(msg3);
    }
}
```

### 2.3 Job状态的持久化

之前在上一节介绍了可以通过JobDataMap传递数据，但JobDataMap的数据其实不会持久化，每次任务执行，都会实例化新的JobDetail并设置当时的那些属性。

如下代码，count的值一直为1：

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob5;
import com.rocks.quartz.job.MyJob6;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * Job持久化测试
 * @author lizhaoxuan
 */
public class Demo5 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob6.class)
                // 通过JobDataMap传递数据
                .usingJobData("count",1)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

```java
package com.rocks.quartz.job;

import lombok.Getter;
import lombok.Setter;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

/**
 * Job状态持久化测试
 * @author lizhaoxuan
 */
public class MyJob6 implements Job {

    @Setter@Getter
    private Integer count;

    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println("count: " + count);
        // 更新count
        jobExecutionContext.getJobDetail().getJobDataMap().put("count",count+1);
    }
}
```

对于需要保留状态的场景，例如通过count记录任务执行次数，该如何实现？

答案是通过quartz提供的`@PersistJobDataAfterExecution`注解，使用此注解后，quartz会将JobDataMap的数据保存下来，支持RAM、JDBC等方式。

> Quartz默认使用RAM方式持久化Job信息，需要注意的是JobDataMap的value值，如果是对象，默认采用Java的序列化方式，需要注意反序列化版本号问题。
>
> 为了避免反序列化问题，Quartz提供`org.quartz.jobStore.useProperties = true`配置使得持久化以String形式存储。

```java
package com.rocks.quartz.job;

import lombok.Getter;
import lombok.Setter;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.quartz.PersistJobDataAfterExecution;

/**
 * 实现Job持久化 保留状态
 * @author lizhaoxuan
 */
@PersistJobDataAfterExecution
public class MyJob7 implements Job {
    
    @Getter@Setter
    private Integer count;
    
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println("count: " +count);
        // 更新
        jobExecutionContext.getJobDetail().getJobDataMap().put("count",count+1);
    }
}
```

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob7;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * Job持久化测试
 * @author lizhaoxuan
 */
public class Demo5 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob7.class)
                // 通过JobDataMap传递数据
                .usingJobData("count",1)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

### 2.4 Job任务并发控制

在Quartz中，默认没有对任务做并发控制，因此当使用持久化Job保留状态时，`如果任务执行时间大于间隔时间`，可能导致多个线程共享JobDataMap的数据，可能出现一些意想不到的后果。

并发Job示例：

- 场景：执行间隔5S，任务执行7S，每次count++，Scheduler线程数大于并发任务数
- 结果：每5S执行一次任务，第二秒由于第一个任务没执行完，导致count为1，后续同样由于之前的任务没执行完，导致count++丢失

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob8;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * 并发Job示例
 * @author lizhaoxuan
 */
public class Demo6 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob8.class)
                .usingJobData("count",1)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

```java
package com.rocks.quartz.job;

import lombok.Getter;
import lombok.Setter;
import lombok.SneakyThrows;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.quartz.PersistJobDataAfterExecution;

import java.util.Date;

/**
 * 并发Job示例
 * @author lizhaoxuan
 */
@PersistJobDataAfterExecution
public class MyJob8 implements Job {

    @Getter@Setter
    private Integer count;

    @SneakyThrows
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println(Thread.currentThread() + ", count: " + count + " ,executed! Time: " + new Date());
        Thread.sleep(7000);
        jobExecutionContext.getJobDetail().getJobDataMap().put("count",count+1);
    }
}
```

Quartz提供了`@DisallowConcurrentExecution`注解来解决这个问题，此注解可以禁止任务并发运行，当触发器触发时，如果之前的任务没有执行完，会导致任务延期，同时只能有一个任务触发。代码示例如下：

> 任务延期之后的触发逻辑参考Trigger章节的misfire机制。

```java
package com.rocks.quartz.job;

import lombok.Getter;
import lombok.Setter;
import lombok.SneakyThrows;
import org.quartz.*;

import java.util.Date;

/**
 * 并发Job示例
 * @author lizhaoxuan
 */
@PersistJobDataAfterExecution
@DisallowConcurrentExecution
public class MyJob8 implements Job {

    @Getter@Setter
    private Integer count;



    @SneakyThrows
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println(Thread.currentThread() + ", count: " + count + " ,executed! Time: " + new Date());
        Thread.sleep(7000);
        jobExecutionContext.getJobDetail().getJobDataMap().put("count",count+1);
    }
}
```

### 2.5 Job其他特性

通过JobDetail对象，可以给job实例配置的其它属性有：

- Durability：如果一个job是非持久的，当没有活跃的trigger与之关联的时候，会被自动地从scheduler中删除。也就是说，非持久的job的生命期是由trigger的存在与否决定的；
- RequestsRecovery：如果一个job是可恢复的，并且在其执行的时候，scheduler发生硬关闭（hard shutdown)（比如运行的进程崩溃了，或者关机了），则当scheduler重新启动的时候，该job会被重新执行。此时，该job的JobExecutionContext.isRecovering() 返回true。

# 三：Trigger

### 3.1 Trigger基本属性

与Job一样，trigger也很容易使用，但是还有一些扩展选项需要理解，以便更好地使用quartz。

Trigger也有很多类型，可以根据实际需要来选择，最常用的有两种：`SimpleTrigger`和`CronTrigger`。

虽然不同的Trigger有不同的特性，但Trigger也有一些公用属性：

- TriggerKey：标识Trigger的唯一性

- startTime：设置trigger第一次开始触发的时间（此时间之后根据trigger的规则进行触发）
- endTime：设置Trigger失效的时间（此时间之后trigger不再触发）
- priority：优先级，当任务并发数大于Scheduler线程池数量时，根据优先级排序执行，默认为5
- misfire：错过触发，设置当任务由于超时或其他并发任务导致错过时的处理方案，不同的触发器有不同的处理方案

```java
package com.rocks.quartz;

import com.rocks.quartz.job.MyJob8;
import org.apache.commons.lang3.time.DateUtils;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

import java.util.Date;

/**
 * Trigger公共属性示例
 * @author lizhaoxuan
 */
public class Demo7 {

    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(MyJob8.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 开始时间
                .startAt(new Date())
                // 停止时间
                .endAt(DateUtils.addDays(new Date(),7))
                // triggerKey
                .withIdentity(new TriggerKey("trigger1","trigger"))
                // 设置优先级
                .withPriority(10)
                // 设置Simple触发器
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever()
                        // 设置misfire策略
                        .withMisfireHandlingInstructionFireNow())
                .build();

        // 3.创建触发器
        CronTrigger trigger2 = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 开始时间
                .startAt(new Date())
                // 停止时间
                .endAt(DateUtils.addDays(new Date(),7))
                // triggerKey
                .withIdentity(new TriggerKey("trigger2","trigger"))
                // 设置优先级
                .withPriority(-1)
                // 设置Simple触发器
                .withSchedule(CronScheduleBuilder
                        // cron表达式
                        .cronSchedule("* * * * * *")
                        // misfire机制
                        .withMisfireHandlingInstructionDoNothing())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);
        scheduler.scheduleJob(jobDetail,trigger2);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

### 3.2 Calendar机制

Trigger除了以上基本属性之外，还有一个`Calendar(日历)`属性，此属性可以将一些特殊日期排除在触发时间之外，例如将一些节日排除在触发时间之外等。

Quartz常见的Calendar有以下：

- BaseCalendar：基类，可以继承此类实现自定义Calendar

- AnnualCalendar：排除一年中的某一天

- CronCalendar：使用表达式排除某些时间段不执行
- DailyCalendar：排除一天中的某些时间段不执行
- HolidayCalendar：内置了一些节假日常量，可以用来排除某些节假日不执行
- MonthlyCalendar：排除一个月中的某天
- WeeklyCalendar：排除一星期中的某天

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;
import org.quartz.impl.calendar.*;

import java.util.GregorianCalendar;


/**
 * Calendar示例
 * @author lizhaoxuan
 */
public class Demo8 {

    @SneakyThrows
    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建Calendar
        // 排除一年中的某些天
        AnnualCalendar annualCalendar = new AnnualCalendar();
        // GregorianCalendar是用来生成Date的类 虽然设置了年分 但annualCalendar只会取月和日
        annualCalendar.setDayExcluded(new GregorianCalendar(2021, 4,8),true);
        // Cron表达式排除早晚 只在早8-晚5执行
        CronCalendar cronCalendar = new CronCalendar("* * 0-7,18-23 ? * *");
        // 排除一天中的某些时间不执行
        DailyCalendar dailyCalendar = new DailyCalendar("20:57:00", "20:59:00");
        dailyCalendar.setInvertTimeRange(true);
        // 排除某些节假日 用法和AnnualCalendar一致 但年份有效
        HolidayCalendar holidayCalendar = new HolidayCalendar();
        holidayCalendar.addExcludedDate(new GregorianCalendar(2021,4,8).getTime());
        // 排除每个月中的某些天
        MonthlyCalendar monthlyCalendar = new MonthlyCalendar();
        monthlyCalendar.setDayExcluded(1,true);
        monthlyCalendar.setDayExcluded(7,true);
        // 排除星期中的某一天
        WeeklyCalendar weeklyCalendar = new WeeklyCalendar();
        weeklyCalendar.setDayExcluded(java.util.Calendar.THURSDAY, true);

        // 4.将Calendar注册给Schedule  此处示例只注册一个
        scheduler.addCalendar("calendar1",monthlyCalendar,false,false);

        // 5.创建触发器 绑定Calendar
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startNow()
                // 设置Calendar
                .modifiedByCalendar("calendar1")
                // 设置Simple触发器
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever()
                        // 设置misfire策略
                        .withMisfireHandlingInstructionFireNow())
                .build();

        // 6.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 7.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }
}
```

### 3.3 SimpleTrigger

SimpleTrigger是简单触发器，允许在指定时间点执行，也可以以固定时间间隔触发指定次数。

- 主要属性：开始时间/结束时间/重复次数/重复间隔
- 重复次数：可选0/正整数/-1，-1为无限执行
- 重复间隔：0，并发执行，即上一个任务执行完立马开始下一次，或者long类型的毫秒时间间隔

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;
import org.quartz.impl.calendar.*;
import java.util.GregorianCalendar;


/**
 * SimpleTrigger示例
 * @author lizhaoxuan
 */
public class Demo9 {

    @SneakyThrows
    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器 绑定Calendar
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 5S后开始执行
                .startAt(DateBuilder.futureDate(5, DateBuilder.IntervalUnit.SECOND))
                // 10min后结束执行 (和重复次数双重计算 只要有一个满足就结束)
                .endAt(DateBuilder.futureDate(10, DateBuilder.IntervalUnit.MINUTE))
                // 设置Simple触发器
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever()
                        // 设置重复次数
//                        .withRepeatCount(10)
                        // 设置misfire策略
                        .withMisfireHandlingInstructionFireNow())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

- SimpleTrigger的misfire策略

Quartz在确定任务超时后会触发对应的misfire策略，而任务的超时阈值是由`org.quartz.jobStore.misfireThreshold`配置确定的，此值默认为60S，因此在测试misfire之前，需要先添加如下配置文件`quartz.properties`：

```properties
# quartz集群(实例)名称
org.quartz.scheduler.instanceName=myScheduler
# quartz调度程序线程池大小(最多可同时调度任务的数量)
org.quartz.threadPool.threadCount=3
# Job存储方式
org.quartz.jobStore.class=org.quartz.simpl.RAMJobStore
# 任务错过阈值
org.quartz.jobStore.misfireThreshold=1
```

SimpleTrigger支持以下misfire策略，定义在SimpleTrigger类里：

```java
// 定义在父类Trigger里，公共策略，错过后尽快下次触发，例如，每15秒钟触发一次的SimpleTrigger延迟了5分钟，它一旦有触发的机会就会触发20次。
int MISFIRE_INSTRUCTION_IGNORE_MISFIRE_POLICY = -1;
// 错过后立即触发 只适合单次执行的任务 对于重复任务 效果等同于3
int MISFIRE_INSTRUCTION_FIRE_NOW = 1;
// 错过后立即触发，保持执行总数不变，结束时间延迟
int MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_EXISTING_REPEAT_COUNT = 2;
// 错过后立即触发，根据startTime计算错过的次数，只执行剩余的次数
int MISFIRE_INSTRUCTION_RESCHEDULE_NOW_WITH_REMAINING_REPEAT_COUNT = 3;
// 错过后等待下次触发时间，根据startTime计算错过的次数，只执行剩余的次数
int MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_REMAINING_COUNT = 4;
// 错过后等待下次触发时间，保持执行总数不变，结束时间延迟
int MISFIRE_INSTRUCTION_RESCHEDULE_NEXT_WITH_EXISTING_COUNT = 5;
```

> 参考文档：https://www.cnblogs.com/yaopengfei/p/8549508.html

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;


/**
 * SimpleTrigger的misfire机制
 * @author lizhaoxuan
 */
public class Demo10 {

    @SneakyThrows
    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器 绑定Calendar
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                .startAt(DateBuilder.dateOf(14,0,0))
                // 设置Simple触发器
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .withRepeatCount(3)
                        // .withMisfireHandlingInstructionFireNow()
                        // .withMisfireHandlingInstructionIgnoreMisfires()
                        // .withMisfireHandlingInstructionNextWithExistingCount()
                        // .withMisfireHandlingInstructionNextWithRemainingCount()
                        // .withMisfireHandlingInstructionNowWithExistingCount()
                         .withMisfireHandlingInstructionNowWithRemainingCount()
                        )
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

### 3.4 CronTrigger

相比SimpleTrigger，CronTrigger功能更为强大，支持根据Cron表达式执行定时任务。

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;


/**
 * CronTrigger代码示例
 * @author lizhaoxuan
 */
public class Demo11 {

    @SneakyThrows
    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器 绑定Calendar
        CronTrigger trigger = TriggerBuilder.newTrigger()
                .startNow()
                // 设置Cron触发器
                .withSchedule(CronScheduleBuilder.cronSchedule("0/5 * * * * ?"))
                // 每天8点执行 会自动生成Cron表达式
                //.withSchedule(CronScheduleBuilder.dailyAtHourAndMinute(8,0))
                .build();


        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }
}
```

- CronTrigger的misfire策略，内置在CronTrigger里：

```java
// 父类Tigger的公共策略，错过后尽快下次触发，例如，每15秒钟触发一次的SimpleTrigger延迟了5分钟，它一旦有触发的机会就会触发20次。
public static final int MISFIRE_INSTRUCTION_IGNORE_MISFIRE_POLICY = -1;
// 错过后任何事情都不做，等待下次触发
public static final int MISFIRE_INSTRUCTION_DO_NOTHING = 2;
// 错过后在当前时间立即触发一次 不修改调度时间 后续还是基于之前的时间开始
public static final int MISFIRE_INSTRUCTION_FIRE_ONCE_NOW = 1;
```

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;


/**
 * CronTrigger的misfire示例
 * @author lizhaoxuan
 */
public class Demo12 {

    @SneakyThrows
    public static void main(String[] args) throws SchedulerException, InterruptedException {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();

        // 3.创建触发器 绑定Calendar
        CronTrigger trigger = TriggerBuilder.newTrigger()
                .startNow()
                // 设置Cron触发器
                .withSchedule(CronScheduleBuilder.cronSchedule("0/5 * * * * ?")
                                //.withMisfireHandlingInstructionDoNothing()
                                .withMisfireHandlingInstructionFireAndProceed()
                                //.withMisfireHandlingInstructionIgnoreMisfires()
                                )
                .build();


        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

# 四：Listener

### 4.1JobListener



### 4.2 TriggerListener



### 4.3 SchedulerListener







# 五：SpringBoot整合Quartz







# 六：Quartz集群



![image-20210401164731623](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210401164731623.png)

![image-20210401164743739](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210401164743739.png)

![image-20210401165109450](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210401165109450.png)



![image-20210401165133390](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210401165133390.png)

![image-20210401165237522](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210401165237522.png)

![image-20210401170421054](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210401170421054.png)



![image-20210403170542728](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210403170542728.png)

![image-20210403171323622](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210403171323622.png)



# 七：Quartz配置文件

- 添加Quartz配置，resources目录下新增quartz.properties配置文件，配置如下：

```properties

```











