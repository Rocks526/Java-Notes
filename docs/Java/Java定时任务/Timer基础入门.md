# 一：Java定时任务介绍

### 1.1 定时任务简介

现代的应用程序早已不是以前的那些由简单的增删改查拼凑而成的程序了，高复杂性早已是标配，而任务的定时调度与执行也是对程序的基本要求了。

通过时间表达式来进行调度和执行的一类任务被称为`定时任务`。

很多业务需求的实现都离不开定时任务，例如备忘录提醒、闹钟等功能。

### 1.2 Java中定时任务的实现

定时任务的应用非常广泛，Java自然也有对定时任务的实现，常见的有以下几种：

- 通过Thread不断while true循环，然后sleep实现定时任务
- Jdk提供了Timer类实现定时任务
- Jdk8的current并发包提供了新的定时任务工具ScheduledExecutorService

Jdk自带的工具都存在一定的局限性，企业级应用里的定时任务更多是采用框架，常见的开源框架有：

- Quartz
- Elastic-job

# 二：Timer基础入门

### 2.1 通过Thread和while true实现定时任务

**特点**

- 实现简单，通过Thread和sleep即可实现
- 只能简单的周期性执行任务，对于一些延迟或者特定时间点执行的任务只能不断sleep然后检查时间，实现麻烦且时间不准确
- 最为重要的是此种方式对资源浪费极大，`一个线程只能启动一个定时任务`，当存在大量定时任务时，会占用大量系统资源

**示例代码**

```java
package com.rocks.timer;

import lombok.extern.slf4j.Slf4j;

/**
 * 通过Thread配合while true实现定时任务
 * @author lizhaoxuan
 */
@Slf4j
public class ThreadDemo {

    private static final long INTERVAL_TIME = 3000L;
    
    public static void main(String[] args) {

        new Thread(() -> {
            while (true){
                try {
                    // 任务逻辑
                    System.out.println("task running ...");
                    Thread.sleep(INTERVAL_TIME);
                } catch (Exception e) {
                    log.error("task running error,reason:{}",e);
                    e.printStackTrace();
                }
            }
        }).start();
    }

}
```

### 2.2 Timer实现定时任务

**基础使用**

Jdk提供的Timer使用非常简单，需要Timer和TimerTask两个对象：

- TimerTask是一个抽象类，继承自Runnable接口，自定义的定时任务都需要继承TimerTask类，实现run方法
- Timer是调度器，一个Timer可以指定多个TimerTask，Timer内部会有一个线程不断的检测当前时间是否有任务可以执行

Timer类提供了多个API，提供固定频率和定时开始的能力：

-  schedule(TimerTask task, long delay)：延迟delay毫秒后开始执行，只执行一次
-  schedule(TimerTask task, Date time)：指定开始时间执行，只执行一次
-  schedule(TimerTask task, long delay, long period)：延迟delay毫秒后开始执行，每period毫秒执行一次
-  schedule(TimerTask task, Date firstTime, long period)：指定开始时间执行，每period毫秒执行一次
-  scheduleAtFixedRate(TimerTask task, long delay, long period)：延迟delay毫秒后开始执行，每period毫秒执行一次，之前任务的延迟会导致后续任务追赶，不再等待固定间隔，目的是一段时间内执行足够次数的任务(如果时间来得及)
-  scheduleAtFixedRate(TimerTask task, Date firstTime,long period)：指定开始时间执行，每period毫秒执行一次，之前任务的延迟会导致后续任务追赶，不再等待固定间隔，目的是一段时间内执行足够次数的任务(如果时间来得及)
-  cancel()：终止timer，取消所有定时任务
-  purge()：清除timer里已经执行完成的任务

**代码示例**

```java
package com.rocks.timer;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.RandomUtils;
import org.apache.commons.lang3.time.DateUtils;
import java.util.Date;
import java.util.Timer;
import java.util.TimerTask;

/**
 * Timer实现定时任务代码示例
 * @author lizhaoxuan
 */
@Slf4j
public class TimerDemo {

    private static final long delayTime = 3000L;

    private static final long intervalTime = 2000L;

    private static final long taskRunningTime = 3000L;

    public static void main(String[] args) {

        // 创建一个调度器 可以设置名字和是否设置为后台线程
        Timer timer = new Timer("Rocks",false);

        demo1(timer);
        demo2(timer);
        demo3(timer);
        demo4(timer);
        demo5(timer);
        demo6(timer);

    }


    /**
     * 延迟指定时间后周期执行
     *      如果任务执行时间小于周期间隔,则每个周期间隔执行一次
     *      如果任务执行时间大于周期间隔,会阻塞后续任务,当上个任务执行完后再开始执行
     *      但此方法会存在任务追赶,如果之前有任务被延迟执行了,当任务完成后,后续任务会追赶
     *          例如: 当前面有任务延迟后,下一个任务执行时间小于周期 schedule方法会周期执行 而fixedRate会追赶任务 任务完成后会立即执行 不等周期
     *      fixedRate的目的是在一段时间内执行指定次数
     * @param timer
     */
    @SneakyThrows
    private static void demo6(Timer timer) {
        // 延迟一段时间后开始周期性执行 之前的任务不会阻塞之后任务开始时间
        timer.scheduleAtFixedRate(new TimerTask() {
            @SneakyThrows
            @Override
            public void run() {
                long l = RandomUtils.nextLong(1000, taskRunningTime);
                log.info("task running,time:{},sleep:{}",new Date(),l);
                Thread.sleep(l);
            }
        },delayTime,intervalTime);
    }

    /**
     * 指定开始时间周期执行
     *      如果任务执行时间小于周期间隔,则每个周期间隔执行一次
     *      如果任务执行时间大于周期间隔,会阻塞后续任务,当上个任务执行完后再开始执行
     *      但此方法会存在任务追赶,如果之前有任务被延迟执行了,当任务完成后,后续任务会追赶
     *          例如: 当前面有任务延迟后,下一个任务执行时间小于周期 schedule方法会周期执行 而fixedRate会追赶任务 任务完成后会立即执行 不等周期
     *      fixedRate的目的是在一段时间内执行指定次数
     * @param timer
     */
    private static void demo5(Timer timer) {
        // 指定时间开始固定频率执行 之前的任务不会阻塞之后任务开始时间
        timer.scheduleAtFixedRate(new TimerTask() {
            @SneakyThrows
            @Override
            public void run() {
                long l = RandomUtils.nextLong(1000, taskRunningTime);
                log.info("task running,time:{},sleep:{}",new Date(),l);
                Thread.sleep(l);
            }
        },DateUtils.addSeconds(new Date(),5),intervalTime);
    }

    /**
     * 指定开始时间周期执行
     *      如果任务执行时间小于周期间隔,则每个周期间隔执行一次
     *      如果任务执行时间大于周期间隔,会阻塞后续任务,当上个任务执行完后再开始执行
     * @param timer
     */
    private static void demo4(Timer timer) {
        // 指定时间开始周期执行
        timer.schedule(new TimerTask() {
            @SneakyThrows
            @Override
            public void run() {
                long l = RandomUtils.nextLong(1000, taskRunningTime);
                log.info("task running,time:{},sleep:{}",new Date(),l);
                Thread.sleep(l);
            }
            // 延迟5秒后 每2s执行一次
        },DateUtils.addSeconds(new Date(),5),intervalTime);
    }

    private static void demo3(Timer timer) {
        // 指定时间开始执行一次
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                log.info("task running!");
            }
            // 延迟五秒后执行
        },DateUtils.addSeconds(new Date(),5));
    }

    /**
     * 延迟一定时间后周期执行
     *      如果任务执行时间小于周期间隔,则每个周期间隔执行一次
     *      如果任务执行时间大于周期间隔,会阻塞后续任务,当上个任务执行完后再开始执行
     * @param timer
     */
    private static void demo2(Timer timer) {
        // 延迟指定时间后周期执行
        timer.schedule(new TimerTask() {
            @SneakyThrows
            @Override
            public void run() {
                long l = RandomUtils.nextLong(1000, taskRunningTime);
                log.info("task running,time:{},sleep:{}",new Date(),l);
                Thread.sleep(l);
            }
        },delayTime,intervalTime);
    }

    private static void demo1(Timer timer) {
        // 延迟指定时间后执行一次
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                log.info("task running!");
            }
        }, delayTime);
    }

}
```

**Timer存在的问题**

- Jdk的Timer底层基于小顶堆实现，不适合大量任务的场景(无法解决)
- Jdk的Timer提交的任务如果出现异常，会导致整个Timer终止(任务内try/catch可以解决)
- Jdk的Timer调度程序是单线程负责，当存在某些任务很耗时时，可能会阻塞调度，影响后续任务和其他任务的触发时间(在TimerTask里通过子线程异步执行可解决，但很耗费线程资源)

- Jdk的Timer只支持简单的定时任务，如固定频率或指定时间，不支持cron表达式，对于每个月1号执行这种任务实现很繁琐

**Timer并发任务阻塞代码示例**

```java
package com.rocks.timer;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import java.util.Date;
import java.util.Timer;
import java.util.TimerTask;

/**
 * Timer并发任务问题
 * @author lizhaoxuan
 */
@Slf4j
public class TimerCurrentDemo {

    public static void main(String[] args) {
        
        // 由于Timer是单线程调度 当某个任务执行超时后 会导致别的任务也延迟执行 
        // 如果任务逻辑放到子线程异步处理 又会很耗费线程资源
        Timer timer = new Timer();
        timer.schedule(new TimerTask() {
            @SneakyThrows
            @Override
            public void run() {
                log.info("task1 run,time:{}",new Date());
                Thread.sleep(3000);
            }
        },0,2000);
        // 任务1的延迟会导致任务2也每3s执行一次
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                log.info("task2 run,time:{}",new Date());
            }
        },0,2000);
    }

}
```

**适用场景**

- Jdk的Timer只适合一些简单的少量定时任务场景

### 2.3 ScheduledExecutorService实现定时任务

**基础使用**

- 提供的API和其他线程池类似，多个两个schedule和scheduledFiexRate方法
- schedule和scheduledFiexRate方法的作用和Timer的类似
- 当使用多个线程时，只要阻塞任务的数量小于线程的数量，阻塞任务不会对其他任务造成影响，主要是解决Timer并发任务的问题

**代码示例**

```java
package com.rocks.timer;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.RandomUtils;

import java.util.Date;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

/**
 * ScheduledExecutorService代码示例
 * @author lizhaoxuan
 */
@Slf4j
public class ScheduledExecutorDemo {

    private static final long delayTime = 3000L;

    private static final long intervalTime = 2000L;

    private static final long taskRunningTime = 3000L;

    public static void main(String[] args) {
        // 创建线程池调度器
        ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(Runtime.getRuntime().availableProcessors());
//         ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
        // 提交任务 可以提交runnable callable
//        demo1(scheduledExecutorService);
//        demo2(scheduledExecutorService);
        demo3(scheduledExecutorService);
    }

    /**
     * 并发任务测试
     *      多线程: task1每两秒执行一次 task2的阻塞不会影响到其他任务
     *      单线程: 和Timer一样存在并发问题, 某个任务的阻塞会影响到其他并发任务
     * @param scheduledExecutorService
     */
    private static void demo3(ScheduledExecutorService scheduledExecutorService) {
        scheduledExecutorService.scheduleAtFixedRate(() -> log.info("task1 running!"),delayTime, intervalTime, TimeUnit.MILLISECONDS);
        scheduledExecutorService.scheduleAtFixedRate(() -> {
            try {
                log.info("task2 running!");
                Thread.sleep(taskRunningTime);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },delayTime,intervalTime,TimeUnit.MILLISECONDS);
    }

    /**
     * 延迟一定时间后周期执行 效果和Timer的fixedRate一致
     * @param scheduledExecutorService
     */
    private static void demo2(ScheduledExecutorService scheduledExecutorService) {
        scheduledExecutorService.scheduleAtFixedRate(() -> {
            long l = RandomUtils.nextLong(1000, taskRunningTime);
            log.info("task running,time:{},sleep:{}",new Date(),l);
            try {
                Thread.sleep(l);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },delayTime,intervalTime,TimeUnit.MILLISECONDS);
    }

    /**
     * 延迟指定时间后执行一次
     * @param scheduledExecutorService
     */
    private static void demo1(ScheduledExecutorService scheduledExecutorService) {
        scheduledExecutorService.schedule(() -> log.info("task running!"),delayTime, TimeUnit.MILLISECONDS);
    }

}
```







