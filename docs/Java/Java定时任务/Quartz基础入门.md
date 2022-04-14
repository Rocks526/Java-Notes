# 一：Quartz快速入门

### 1.1 Quartz介绍

Quartz是OpenSymphony开源组织在Job scheduling领域又一个开源项目，是一个传统的企业级定时任务框架，可以用来构建运行上百个甚至上万个定时任务的复杂场景。

### 1.2 Quartz的特点

- Quartz源码由Java编写，实现了一个简单又强大的任务调度机制
- Quartz支持持久性作业，即可以实现对一个任务多个调度的状态保留
- Quartz可以单独使用，也可以与Servlet/Spring/SpringBoot集成，SpringBoot提供了Quartz的starter
- Quartz支持Cron/定时间隔等多个定时任务
- Quartz支持集群，集群模式会将所有任务信息存储到db，各个服务从db获取任务信息，相互负载

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

Quartz提供了一系列的监听器，监听Schedule，Job，Trigger等相关的事件。

### 4.1 TriggerListener

监听Trigger相关的事件，进行相关处理，需要实现TriggerListener接口或者继承JobListenerSupport抽象类，主要提供以下方法：

-  getName：返回TriggerListener实例名称
-  triggerFired：Trigger触发时调用此方法
-  triggerMisfired：Trigger触发器错过触发时调用此方法
-  triggerComplete：Trigger触发完成时调用
-  vetoJobExecution：Trigger触发时执行，可以控制是否否决这次执行

```java
package com.rocks.quartz.listener;

import org.apache.commons.lang3.RandomUtils;
import org.quartz.JobExecutionContext;
import org.quartz.Trigger;
import org.quartz.TriggerListener;

/**
 * Trigger监听器
 * @author lizhaoxuan
 */
public class MyTriggerListener implements TriggerListener {

    @Override
    public String getName() {
        return "myTriggerListener";
    }

    @Override
    public void triggerFired(Trigger trigger, JobExecutionContext context) {
        System.out.println("Trigger[" + trigger.getKey().getName() + "]触发了!");
    }

    @Override
    public boolean vetoJobExecution(Trigger trigger, JobExecutionContext context) {
        boolean isMisfire = RandomUtils.nextBoolean();
        System.out.println("Trigger[" + trigger.getKey().getName() + "]是否否决:" + isMisfire);
        return isMisfire;
    }

    @Override
    public void triggerMisfired(Trigger trigger) {
        System.out.println("Trigger[" + trigger.getKey().getName() + "]错过触发!");
    }

    @Override
    public void triggerComplete(Trigger trigger, JobExecutionContext context, Trigger.CompletedExecutionInstruction triggerInstructionCode) {
        System.out.println("Trigger[" + trigger.getKey().getName() + "]执行完成!");
    }
}
```

### 4.2 JobListener

监听Job相关的事件，进行相关处理，需要实现JobListener接口或者继承JobListenerSupport抽象类，主要提供以下方法：

-  getName：返回JobListener实例名称
-  jobToBeExecuted：Job将要执行时触发
-  jobWasExecuted：Job执行完成后触发
-  jobExecutionVetoed：Job被否决时触发

```java
package com.rocks.quartz.listener;

import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.quartz.JobListener;

/**
 * Job监听器
 * @author lizhaoxuan
 */
public class MyJobListener implements JobListener {

    /**
     * 此JobListener名称
     * @return
     */
    @Override
    public String getName() {
        return "myJobListener";
    }

    /**
     * Job将要执行
     * @param context
     */
    @Override
    public void jobToBeExecuted(JobExecutionContext context) {
        System.out.println("Job[" + context.getJobDetail().getKey().getName() + "]将要触发!");
    }

    /**
     * Job被否决时触发
     * @param context
     */
    @Override
    public void jobExecutionVetoed(JobExecutionContext context) {
        System.out.println("Job[" + context.getJobDetail().getKey().getName() + "]被否决了!");
    }

    /**
     * Job执行之后
     * @param context
     * @param jobException
     */
    @Override
    public void jobWasExecuted(JobExecutionContext context, JobExecutionException jobException) {
        System.out.println("Job[" + context.getJobDetail().getKey().getName() + "]触发完成!");
    }
}
```

### 4.3 SchedulerListener

监听Schedule容器和调度时运行信息，需要实现ScheduleListener接口或者继承ScheduleListener：

```java
package com.rocks.quartz.listener;

import org.quartz.*;

/**
 * SchedulerListener监听器
 * @author lizhaoxuan
 */
public class MyScheduleListener implements SchedulerListener {

    /**
     * job被触发
     * @param trigger
     */
    @Override
    public void jobScheduled(Trigger trigger) {
        System.out.println("jobScheduled");
    }

    /**
     * job没有被触发
     * @param triggerKey
     */
    @Override
    public void jobUnscheduled(TriggerKey triggerKey) {
        System.out.println("jobUnscheduled");
    }

    /**
     * 监听器销毁
     * @param trigger
     */
    @Override
    public void triggerFinalized(Trigger trigger) {
        System.out.println("triggerFinalized");
    }

    /**
     * 监听器暂停
     * @param triggerKey
     */
    @Override
    public void triggerPaused(TriggerKey triggerKey) {
        System.out.println("triggerPaused");
    }

    @Override
    public void triggersPaused(String triggerGroup) {
        System.out.println("triggersPaused");
    }

    /**
     * 监听器恢复
     * @param triggerKey
     */
    @Override
    public void triggerResumed(TriggerKey triggerKey) {
        System.out.println("triggerResumed");
    }

    @Override
    public void triggersResumed(String triggerGroup) {
        System.out.println("triggersResumed");
    }

    /**
     * 添加job
     * @param jobDetail
     */
    @Override
    public void jobAdded(JobDetail jobDetail) {
        System.out.println("jobAdded");
    }

    /**
     * 删除job
     * @param jobKey
     */
    @Override
    public void jobDeleted(JobKey jobKey) {
        System.out.println("jobDeleted");
    }

    /**
     * job停止
     * @param jobKey
     */
    @Override
    public void jobPaused(JobKey jobKey) {
        System.out.println("jobPaused");
    }

    @Override
    public void jobsPaused(String jobGroup) {
        System.out.println("jobsPaused");
    }

    /**
     * job恢复
     * @param jobKey
     */
    @Override
    public void jobResumed(JobKey jobKey) {
        System.out.println("jobResumed");
    }

    @Override
    public void jobsResumed(String jobGroup) {
        System.out.println("jobsResumed");
    }

    /**
     * 调度时出现异常
     * @param msg
     * @param cause
     */
    @Override
    public void schedulerError(String msg, SchedulerException cause) {
        System.out.println("schedulerError");
    }

    @Override
    public void schedulerInStandbyMode() {
        System.out.println("schedulerInStandbyMode");
    }

    /**
     * 调度开始
     */
    @Override
    public void schedulerStarted() {
        System.out.println("schedulerStarted");
    }

    /**
     * 调度正在开始
     */
    @Override
    public void schedulerStarting() {
        System.out.println("schedulerStarting");
    }

    /**
     * 调度停止
     */
    @Override
    public void schedulerShutdown() {
        System.out.println("schedulerShutdown");
    }

    /**
     * 调度正在停止
     */
    @Override
    public void schedulerShuttingdown() {
        System.out.println("schedulerShuttingdown");
    }

    /**
     * 数据被清除
     */
    @Override
    public void schedulingDataCleared() {
        System.out.println("schedulingDataCleared");
    }
}
```

### 4.4 监听器执行流程

1. Trigger判断是否错过触发：triggerFired方法
2. Trigger触发：triggerFired方法
3. Trigger是否否决这次执行：vetoJobExecution方法，如果否决，后续不再触发
4. Job即将触发：jobToBeExecuted方法
5. Job判断此次触发是否被否决：jobExecutionVetoed方法
6. Job触发完成：jobWasExecuted方法

```java
package com.rocks.quartz;

import com.rocks.quartz.job.SimpleJob;
import com.rocks.quartz.listener.MyJobListener;
import com.rocks.quartz.listener.MyScheduleListener;
import com.rocks.quartz.listener.MyTriggerListener;
import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;


/**
 * listener代码示例
 * @author lizhaoxuan
 */
public class Demo13 {

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
                .withIdentity("myJob1", "jobGroup")
                .build();

        // 3.创建触发器 绑定Calendar
        CronTrigger trigger = TriggerBuilder.newTrigger()
                .withIdentity("trigger1","triggerGroup")
                .startNow()
                // 设置Cron触发器
                .withSchedule(CronScheduleBuilder.cronSchedule("0/5 * * * * ?"))
                .build();

        // 4.注册监听器 第二个参数可以传入要监听哪些Job/Trigger 入参是JobKey/TriggerKey
        // 第二个参数允许传入一个matchList match除了KeyMatch 还有OrMatch AndMatch等
        scheduler.getListenerManager().addJobListener(new MyJobListener(), key -> "jobGroup".equals(key.getGroup()));
        scheduler.getListenerManager().addTriggerListener(new MyTriggerListener(), key -> "triggerGroup".equals(key.getGroup()));
        scheduler.getListenerManager().addSchedulerListener(new MyScheduleListener());

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail,trigger);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();
    }

}
```

# 五：SpringBoot整合Quartz





# 六：Quartz集群

### 6.1 Job Stores

JobStore负责跟踪Quartz的所有调度信息：jobs，triggers，日历等。JobStore有多种存储方式：

- RAMJobStore

RAMJobStore是默认的存储方式，基于内存存储，性能最高，但程序重启后运行时信息会丢失。

> Quartz的集群模式每个节点都是无状态的，需要借助数据库存储运行时信息，因此此方式无法用于集群。

```properties
org.quartz.jobStore.class=org.quartz.simpl.RAMJobStore
```

- JDBC JobStore

JDBC JobStore是基于数据库的存储方式，支持常见的Oracle、PostgreSQL、MySQL等。

基于数据库的方式可用于集群环境，使用此方式需要使用Quartz预定义好的表，可以修改表前缀。

使用JDBC方式还需要注意数据库的事务，支持JobStoreTX和JobStoreCMT两种方式，TX方式由Quartz自己管理事务，CMT方式将Quartz的事务交给其他应用来管理，例如Spring。CMT方式可以将添加Job，Trigger等操作和其他业务操作放到一个事务里。

```properties
# 使用JDBC TX方式存储
org.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
# 数据库方言
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
# 数据库表前缀
org.quartz.jobStore.tablePrefix = QRTZ_
```

> 一般应用程序都需要配置数据库，没必要在给Quartz单独配置一份，一般采用代码配置：在代码里获取dataSource，set给SchedulerFactory。
>
> Quartz需要用的数据库建表SQL在quartz-core的resources目录下，下面附带上mysql的建表语句。
>
> 具体表结构及如何存储运行参考原理篇。

```sql
#
# Quartz seems to work best with the driver mm.mysql-2.0.7-bin.jar
#
# PLEASE consider using mysql with innodb tables to avoid locking issues
#
# In your Quartz properties file, you'll need to set
# org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
#

DROP TABLE IF EXISTS QRTZ_FIRED_TRIGGERS;
DROP TABLE IF EXISTS QRTZ_PAUSED_TRIGGER_GRPS;
DROP TABLE IF EXISTS QRTZ_SCHEDULER_STATE;
DROP TABLE IF EXISTS QRTZ_LOCKS;
DROP TABLE IF EXISTS QRTZ_SIMPLE_TRIGGERS;
DROP TABLE IF EXISTS QRTZ_SIMPROP_TRIGGERS;
DROP TABLE IF EXISTS QRTZ_CRON_TRIGGERS;
DROP TABLE IF EXISTS QRTZ_BLOB_TRIGGERS;
DROP TABLE IF EXISTS QRTZ_TRIGGERS;
DROP TABLE IF EXISTS QRTZ_JOB_DETAILS;
DROP TABLE IF EXISTS QRTZ_CALENDARS;


CREATE TABLE QRTZ_JOB_DETAILS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    JOB_NAME  VARCHAR(200) NOT NULL,
    JOB_GROUP VARCHAR(200) NOT NULL,
    DESCRIPTION VARCHAR(250) NULL,
    JOB_CLASS_NAME   VARCHAR(250) NOT NULL,
    IS_DURABLE VARCHAR(1) NOT NULL,
    IS_NONCONCURRENT VARCHAR(1) NOT NULL,
    IS_UPDATE_DATA VARCHAR(1) NOT NULL,
    REQUESTS_RECOVERY VARCHAR(1) NOT NULL,
    JOB_DATA BLOB NULL,
    PRIMARY KEY (SCHED_NAME,JOB_NAME,JOB_GROUP)
);

CREATE TABLE QRTZ_TRIGGERS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    TRIGGER_NAME VARCHAR(200) NOT NULL,
    TRIGGER_GROUP VARCHAR(200) NOT NULL,
    JOB_NAME  VARCHAR(200) NOT NULL,
    JOB_GROUP VARCHAR(200) NOT NULL,
    DESCRIPTION VARCHAR(250) NULL,
    NEXT_FIRE_TIME BIGINT(13) NULL,
    PREV_FIRE_TIME BIGINT(13) NULL,
    PRIORITY INTEGER NULL,
    TRIGGER_STATE VARCHAR(16) NOT NULL,
    TRIGGER_TYPE VARCHAR(8) NOT NULL,
    START_TIME BIGINT(13) NOT NULL,
    END_TIME BIGINT(13) NULL,
    CALENDAR_NAME VARCHAR(200) NULL,
    MISFIRE_INSTR SMALLINT(2) NULL,
    JOB_DATA BLOB NULL,
    PRIMARY KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP),
    FOREIGN KEY (SCHED_NAME,JOB_NAME,JOB_GROUP)
        REFERENCES QRTZ_JOB_DETAILS(SCHED_NAME,JOB_NAME,JOB_GROUP)
);

CREATE TABLE QRTZ_SIMPLE_TRIGGERS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    TRIGGER_NAME VARCHAR(200) NOT NULL,
    TRIGGER_GROUP VARCHAR(200) NOT NULL,
    REPEAT_COUNT BIGINT(7) NOT NULL,
    REPEAT_INTERVAL BIGINT(12) NOT NULL,
    TIMES_TRIGGERED BIGINT(10) NOT NULL,
    PRIMARY KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP),
    FOREIGN KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
        REFERENCES QRTZ_TRIGGERS(SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
);

CREATE TABLE QRTZ_CRON_TRIGGERS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    TRIGGER_NAME VARCHAR(200) NOT NULL,
    TRIGGER_GROUP VARCHAR(200) NOT NULL,
    CRON_EXPRESSION VARCHAR(200) NOT NULL,
    TIME_ZONE_ID VARCHAR(80),
    PRIMARY KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP),
    FOREIGN KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
        REFERENCES QRTZ_TRIGGERS(SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
);

CREATE TABLE QRTZ_SIMPROP_TRIGGERS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    TRIGGER_NAME VARCHAR(200) NOT NULL,
    TRIGGER_GROUP VARCHAR(200) NOT NULL,
    STR_PROP_1 VARCHAR(512) NULL,
    STR_PROP_2 VARCHAR(512) NULL,
    STR_PROP_3 VARCHAR(512) NULL,
    INT_PROP_1 INT NULL,
    INT_PROP_2 INT NULL,
    LONG_PROP_1 BIGINT NULL,
    LONG_PROP_2 BIGINT NULL,
    DEC_PROP_1 NUMERIC(13,4) NULL,
    DEC_PROP_2 NUMERIC(13,4) NULL,
    BOOL_PROP_1 VARCHAR(1) NULL,
    BOOL_PROP_2 VARCHAR(1) NULL,
    PRIMARY KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP),
    FOREIGN KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
    REFERENCES QRTZ_TRIGGERS(SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
);

CREATE TABLE QRTZ_BLOB_TRIGGERS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    TRIGGER_NAME VARCHAR(200) NOT NULL,
    TRIGGER_GROUP VARCHAR(200) NOT NULL,
    BLOB_DATA BLOB NULL,
    PRIMARY KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP),
    FOREIGN KEY (SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
        REFERENCES QRTZ_TRIGGERS(SCHED_NAME,TRIGGER_NAME,TRIGGER_GROUP)
);

CREATE TABLE QRTZ_CALENDARS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    CALENDAR_NAME  VARCHAR(200) NOT NULL,
    CALENDAR BLOB NOT NULL,
    PRIMARY KEY (SCHED_NAME,CALENDAR_NAME)
);

CREATE TABLE QRTZ_PAUSED_TRIGGER_GRPS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    TRIGGER_GROUP  VARCHAR(200) NOT NULL,
    PRIMARY KEY (SCHED_NAME,TRIGGER_GROUP)
);

CREATE TABLE QRTZ_FIRED_TRIGGERS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    ENTRY_ID VARCHAR(95) NOT NULL,
    TRIGGER_NAME VARCHAR(200) NOT NULL,
    TRIGGER_GROUP VARCHAR(200) NOT NULL,
    INSTANCE_NAME VARCHAR(200) NOT NULL,
    FIRED_TIME BIGINT(13) NOT NULL,
    SCHED_TIME BIGINT(13) NOT NULL,
    PRIORITY INTEGER NOT NULL,
    STATE VARCHAR(16) NOT NULL,
    JOB_NAME VARCHAR(200) NULL,
    JOB_GROUP VARCHAR(200) NULL,
    IS_NONCONCURRENT VARCHAR(1) NULL,
    REQUESTS_RECOVERY VARCHAR(1) NULL,
    PRIMARY KEY (SCHED_NAME,ENTRY_ID)
);

CREATE TABLE QRTZ_SCHEDULER_STATE
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    INSTANCE_NAME VARCHAR(200) NOT NULL,
    LAST_CHECKIN_TIME BIGINT(13) NOT NULL,
    CHECKIN_INTERVAL BIGINT(13) NOT NULL,
    PRIMARY KEY (SCHED_NAME,INSTANCE_NAME)
);

CREATE TABLE QRTZ_LOCKS
  (
    SCHED_NAME VARCHAR(120) NOT NULL,
    LOCK_NAME  VARCHAR(40) NOT NULL,
    PRIMARY KEY (SCHED_NAME,LOCK_NAME)
);


commit;
```

### 6.2 Quartz集群

Quartz的集群支持负载均衡和故障转移，各个节点在启动之后会检查配置文件是否开启集群模式，如果开启了集群模式，会启动一个子线程定时向数据库同步自己的状态，同时从数据库捞取执行失败或者超时任务进行执行。

> 具体的执行原理参考Quartz原理篇。

### 6.3 Quartz集群配置

- 引入Maven依赖

```xml
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.3.2</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
```

- 创建配置文件

```properties
#==============================================================
#Configure Main Scheduler Properties
#==============================================================
# quartz集群(实例)名称
org.quartz.scheduler.instanceName=myScheduler
# 节点ID 自动生成
org.quartz.scheduler.instanceId=AUTO


#==============================================================
#Configure JobStore
#==============================================================
# 是否集群模式
org.quartz.jobStore.isClustered=true
# Job存储方式
org.quartz.jobStore.class=org.quartz.impl.jdbcjobstore.JobStoreTX
# 数据库方言 mysql
org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.StdJDBCDelegate
# 数据库表前缀
org.quartz.jobStore.tablePrefix=QRTZ_
# 节点和数据库检查心跳间隔
org.quartz.jobStore.clusterCheckinInterval=10000
# dataSource名称
org.quartz.jobStore.dataSource=myDS
# 任务错过阈值
org.quartz.jobStore.misfireThreshold=60

#==============================================================
#Configure DataSource
#==============================================================
# 数据库驱动
org.quartz.dataSource.myDS.driver=com.mysql.jdbc.Driver
# 数据库URL
org.quartz.dataSource.myDS.URL=jdbc:mysql://localhost:3306/quartz-learn?useUnicode=true&amp;characterEncoding=UTF-8
# 用户名
org.quartz.dataSource.myDS.user=root
# 密码
org.quartz.dataSource.myDS.password=123456
# 最大连接数
org.quartz.dataSource.myDS.maxConnections=30

#==============================================================
#Configure ThreadPool
#==============================================================
org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 5
org.quartz.threadPool.threadPriority = 5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread = true
```

- 编写Job实例

```java
package com.rocks.quartz;

import org.apache.commons.lang3.time.DateFormatUtils;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import java.io.Serializable;
import java.util.Date;

/**
 * Job实现类
 * @author lizhaoxuan
 */
public class SimpleJob implements Job, Serializable {

    private final String DATE_FORMAT_PATTERN = "yyyy-MM-dd HH:mm:ss";

    @Override
    public void execute(JobExecutionContext jobExecutionContext)  {
        // 任务执行
        System.out.println("Job:[" + jobExecutionContext.getJobDetail().getKey().getName() + "] executed, Time: " + DateFormatUtils.format(new Date(),DATE_FORMAT_PATTERN));
    }
}
```

- 启动两个节点实例，一个不提交任务，一个提交三个任务

```java
package com.rocks.quartz;

import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * Quartz集群示例代码
 * 节点实例2 启动三个任务 由于集群会自动负载 会任务给节点1
 * @author lizhaoxuan
 */
public class ClusterDemo2 {

    @SneakyThrows
    public static void main(String[] args) {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.创建JobDetail
        JobDetail jobDetail2 = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob1", "MyGroup1")
                .build();
        JobDetail jobDetail3 = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob2", "MyGroup1")
                .build();
        JobDetail jobDetail4 = JobBuilder
                // 指定任务
                .newJob(SimpleJob.class)
                // 指定任务实例名称和组
                .withIdentity("myJob3", "MyGroup1")
                .build();

        // 3.创建触发器
        SimpleTrigger trigger = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startAt(DateBuilder.futureDate(1, DateBuilder.IntervalUnit.SECOND))
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();
        SimpleTrigger trigger2 = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startAt(DateBuilder.futureDate(2, DateBuilder.IntervalUnit.SECOND))
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();
        SimpleTrigger trigger3 = TriggerBuilder.newTrigger()
                // 被调度后立即开始执行
                .startAt(DateBuilder.futureDate(3, DateBuilder.IntervalUnit.SECOND))
                // 设置触发器类型
                .withSchedule(SimpleScheduleBuilder.simpleSchedule()
                        // 5s执行一次
                        .withIntervalInSeconds(5)
                        // 永久执行
                        .repeatForever())
                .build();

        // 4.将Job和Trigger注册给Scheduler, 任务开始调度
        scheduler.scheduleJob(jobDetail2,trigger);
        scheduler.scheduleJob(jobDetail3,trigger2);
        scheduler.scheduleJob(jobDetail4,trigger3);

        // 5.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();

    }

}
```

```java
package com.rocks.quartz;

import lombok.SneakyThrows;
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;

/**
 * Quartz集群示例代码
 * 节点实例1 不提交任务 启动后从节点2分摊任务过来
 * @author lizhaoxuan
 */
public class ClusterDemo {

    @SneakyThrows
    public static void main(String[] args) {

        // 1.创建scheduler && 启动
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        scheduler.start();

        // 2.休眠一段时间后停止scheduler
        Thread.sleep(10*60*1000);
        scheduler.shutdown();

    }

}
```

# 七：Quartz配置文件

> Quartz的github官网有个config说明文件，详细说明quartz相关配置。

### 7.1 Quartz配置文件介绍

StdSchedulerFactory默认使用资源路径下的`quartz.properties`作为配置文件，如果没有该文件，则使用默认配置。

Quartz的配置主要分为以下几类：

- 主配置（配置主调度器设置，事务处理）
- ThreadPool的配置（调整作业执行的资源）
- 监听器的配置（您的应用程序可以接收预定事件的通知）
- 插件配置（为您的调度程序添加功能）
- RMI服务器和客户端的配置（从远程进程使用Quartz实例）
- RAMJobStore的配置（存储作业和触发器）
- JDBC-JobStoreTX的配置（通过JDBC在数据库中存储作业和触发器）
- JDBC-JobStoreCMT（具有JTA容器管理事务的JDBC）的配置
- DataSources的配置（供JDBC-JobStores使用）
- 数据库集群的配置（使用JDBC-JobStore实现故障切换和负载平衡）
- TerracottaJobStore的配置（无数据库的集群）

### 7.2 基础配置

| 属性作用                                                     | 属性名称                                                     | 默认                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------ |
| `quartz程序实例名称，集群模式下所有节点必须一样`             | org.quartz.scheduler.instanceName                            | 'QuartzScheduler'                          |
| `quartz节点ID，集群模式下必须唯一，可选AUTO`                 | org.quartz.scheduler.instanceId                              | 'NON_CLUSTERED'                            |
| 节点ID设置为AUTO时的生成策略，默认根据主机名和时间戳生成实例ID | org.quartz.scheduler.instanceIdGenerator.class               | org.quartz.simpl.SimpleInstanceIdGenerator |
| `quartz的调度线程名称`                                       | org.quartz.scheduler.threadName                              | instanceName + '_QuartzSchedulerThread'    |
| `是否设置调度线程为后台线程`                                 | org.quartz.scheduler.makeSchedulerThreadDaemon               | false                                      |
| 用于指定Quartz产生的线程是否会继承初始化线程                 | org.quartz.scheduler.threadsInheritContextClassLoaderOfInitializer | false                                      |
| 在调度程序处于空闲状态时，调度程序将在重新查询可用触发器之前等待的时间量 | org.quartz.scheduler.idleWaitTime                            | 30000                                      |
| 当调查器检测到JobStore中的连接丢失（例如数据库）时，调度程序将在重试之间等待的时间量 | org.quartz.scheduler.dbFailureRetryInterval                  | 15000                                      |
| 类加器辅助，当找不到类时的处理方案                           | org.quartz.scheduler.classLoadHelper.class                   | org.quartz.simpl.CascadingClassLoadHelper  |
| `生产Job的工厂`                                              | org.quartz.scheduler.jobFactory.class                        | org.quartz.simpl.PropertySettingJobFactory |
| 调度上下文中预置KV                                           | org.quartz.context.key.SOME_KEY                              | none                                       |
| 应设置为Quartz可以找到Application Server的UserTransaction管理器的JNDI URL | org.quartz.scheduler.userTransactionURL                      | 'java:comp/UserTransaction'                |
| 如果您希望Quartz在您的工作调用执行之前启动UserTransaction，应设置为“true” | org.quartz.scheduler.wrapJobExecutionInUserTransaction       | false                                      |
| 是否跳过运行快速Web请求以确定是否有可更新的Quartz版本可供下载。如果检查运行，并且找到更新，则会在Quartz的日志中报告它 | org.quartz.scheduler.skipUpdateCheck                         | false                                      |
| 允许调度程序节点一次获取（用于触发）的触发器的最大数量。默认值为1.数字越大，触发效率越高（在需要很多触发器的情况下需要同时触发） - 但是以群集节点之间可能的不平衡负载为代价 | org.quartz.scheduler.batchTriggerAcquisitionMaxCount         | 1                                          |
|                                                              | org.quartz.scheduler.batchTriggerAcquisitionFireAheadTimeWindow | 0                                          |

### 7.3 ThreadPool设置

| 属性作用                                                     | 属性名称                                                     | 默认                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------ |
| `是要使用的ThreadPool实现的名称，Quartz附带的线程池是“org.quartz.simpl.SimpleThreadPool”` | org.quartz.threadPool.class                                  | null                     |
| `调度线程池的线程数`                                         | org.quartz.threadPool.threadCount                            | -1                       |
| 默认线程优先级                                               | org.quartz.threadPool.threadPriority                         | Thread.NORM_PRIORITY (5) |
| `设置为后台线程`                                             | org.quartz.threadPool.makeThreadsDaemons                     | false                    |
|                                                              | org.quartz.threadPool.threadsInheritGroupOfInitializingThread | true                     |
|                                                              | org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread | false                    |
| `在工作池中的线程名称的前缀`                                 | org.quartz.threadPool.threadNamePrefix                       | [Scheduler Name]_Worker  |

### 7.4 全局监听器

> 监听器可以在程序里创建，被job/trigger引用，也可以设置全局监听器，监听所有job/trigger等。
>
> 全局监听器必须有无参的构造函数/set方法等。

| 属性作用          | 属性名称                                  | 默认 |
| ----------------- | ----------------------------------------- | ---- |
| trigger监听器类名 | org.quartz.triggerListener.NAME.class     |      |
| 属性名属性        | org.quartz.triggerListener.NAME.propName  |      |
| 属性名属性        | org.quartz.triggerListener.NAME.prop2Name |      |
| job监听器类名     | org.quartz.jobListener.NAME.class         |      |
| job监听器属性     | org.quartz.jobListener.NAME.propName      |      |
| job监听器属性     | org.quartz.jobListener.NAME.prop2Name     |      |

### 7.5 插件配置

> 像通过配置文件配置插件的Listeners一样，包括给出一个名称，然后指定类名称以及要在实例上设置的任何其他属性。
>
> 该类必须有一个no-arg构造函数，并且属性被反射设置。只支持原始数据类型值

| 属性作用   | 属性名称                         | 默认 |
| ---------- | -------------------------------- | ---- |
| 插件类名   | org.quartz.plugin.NAME.class     |      |
| 属性名属性 | org.quartz.plugin.NAME.propName  |      |
| 属性名属性 | org.quartz.plugin.NAME.prop2Name |      |

> Quartz附带了几个插件，可以在org.quartz.plugins包（和子包）中找到。

- Quartz自带的Jvm挂钩插件

```properties
org.quartz.plugin.shutdownhook.class =  org.quartz.plugins.management.ShutdownHookPlugin
org.quartz.plugin.shutdownhook.cleanShutdown = true
```

### 7.6 RAMJobStore配置

> RAMJobStore是将调度信息（jobs，Triggers和日历）存储于内存。

```properties
org.quartz.jobStore.class = org.quartz.simpl.RAMJobStore
# 判断misfire的阈值 即超过60s认为错过
org.quartz.jobStore.misfireThreshold = 60000
```

### 7.7 Jdbc-jobStoreTX配置

> JDBCJobStore用于在关系数据库中存储调度信息（jobs，Triggers和日历）。

```properties
# 使用JDBC存储调度信息
org.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
```

| 属性作用                                                     | 属性名称                                         | 默认                                                         |
| ------------------------------------------------------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| `数据库方言，可选      org.quartz.impl.jdbcjobstore.StdJDBCDelegate` | org.quartz.jobStore.driverDelegateClass          | null                                                         |
| `数据库dataSource名称`                                       | org.quartz.jobStore.dataSource                   | null                                                         |
| `数据库表前缀`                                               | org.quartz.jobStore.tablePrefix                  | "QRTZ_"                                                      |
| `如果为true，则JobDataMap存储只能使用String`                 | org.quartz.jobStore.useProperties                | false                                                        |
| `misfire阈值`                                                | org.quartz.jobStore.misfireThreshold             | 60000                                                        |
| `是否是集群模式`                                             | org.quartz.jobStore.isClustered                  | false                                                        |
| `集群节点之间心跳检测的频率`                                 | org.quartz.jobStore.clusterCheckinInterval       | 15000                                                        |
| 处理最大超时任务的时间，太长可能导致表锁定                   | org.quartz.jobStore.maxMisfiresToHandleAtATime   | 20                                                           |
| 设置JDBC是否自动提交                                         | org.quartz.jobStore.dontSetAutoCommitFalse       | false                                                        |
| 必须是在“LOCKS”表中选择一行并在该行上放置一个锁的SQL字符串   | org.quartz.jobStore.selectWithLockSQL            | "SELECT * FROM {0}LOCKS WHERE SCHED_NAME = {1} AND LOCK_NAME = ? FOR UPDATE" |
| 设置JDBC的隔离级别                                           | org.quartz.jobStore.txIsolationLevelSerializable | false                                                        |
| 加锁触发Trigger                                              | org.quartz.jobStore.acquireTriggersWithinLock    | false                                                        |
| 用于生成用于锁定作业存储数据控件的org.quartz.impl.jdbcjobstore.Semaphore实例的类名称 | org.quartz.jobStore.lockHandler.class            | null                                                         |
| 一个以管道分隔的属性列表（及其值），可以在初始化时间内传递给DriverDelegate。 | org.quartz.jobStore.driverDelegateInitString     | null                                                         |

### 7.8 DataSources配置

> 如果使用JDBC管理调度运行时信息，则需要配置DataSource信息。

| 属性作用                         | 属性名称                                                   | 默认         |
| -------------------------------- | ---------------------------------------------------------- | ------------ |
| `JDBC驱动类名`                   | org.quartz.dataSource.NAME.driver                          | null         |
| `JDBC连接的URL`                  | org.quartz.dataSource.NAME.URL                             | null         |
| `数据库用户名`                   | org.quartz.dataSource.NAME.user                            | ""           |
| `数据库密码`                     | org.quartz.dataSource.NAME.password                        | ""           |
| `连接池最大连接数`               | org.quartz.dataSource.NAME.maxConnections                  | 10           |
| 检测和替换失效连接               | org.quartz.dataSource.NAME.validationQuery                 | null         |
| 空闲连接有效性检查周期           | org.quartz.dataSource.NAME.idleConnectionValidationSeconds | 50           |
| 每次从连接池取出时是否校验有效性 | org.quartz.dataSource.NAME.validateOnCheckout              | false        |
| `空闲多久后删除连接`             | org.quartz.dataSource.NAME.discardIdleConnectionsSeconds   | 0 (disabled) |

### 7.9 Job集群配置

> Quartz的集群支持负载均衡和高可用，目前只能通过多个节点共享数据库实现。
>
> 将配置文件里的isCluster设置为true，并多个节点的instanceName一致，即可组成集群。

```properties
#============================================================================
# Configure Main Scheduler Properties  
#============================================================================
# 实例名称
org.quartz.scheduler.instanceName = MyClusteredScheduler
# 节点ID
org.quartz.scheduler.instanceId = AUTO

#============================================================================
# Configure ThreadPool  
#============================================================================

org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 25
org.quartz.threadPool.threadPriority = 5

#============================================================================
# Configure JobStore  
#============================================================================

org.quartz.jobStore.misfireThreshold = 60000

org.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.oracle.OracleDelegate
org.quartz.jobStore.useProperties = false
org.quartz.jobStore.dataSource = myDS
org.quartz.jobStore.tablePrefix = QRTZ_

# 集群模式
org.quartz.jobStore.isClustered = true
org.quartz.jobStore.clusterCheckinInterval = 20000

#============================================================================
# Configure Datasources  
#============================================================================

org.quartz.dataSource.myDS.driver = oracle.jdbc.driver.OracleDriver
org.quartz.dataSource.myDS.URL = jdbc:oracle:thin:@polarbear:1521:dev
org.quartz.dataSource.myDS.user = quartz
org.quartz.dataSource.myDS.password = quartz
org.quartz.dataSource.myDS.maxConnections = 5
org.quartz.dataSource.myDS.validationQuery=select 0 from dual
```



