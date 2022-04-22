# 一：Jdk命令行工具介绍

### 1.1 Jdk命令行工具介绍

JDK 自带了很多命令行甚至是图形界面工具，帮助我们查看 JVM 的一些信息。这些工具都在Jdk的bin目录下，如下所示：

![image-20220421230321526](http://rocks526.top/lzx/image-20220421230321526.png)

所有工具的使用手册可以参考官网的文档：https://docs.oracle.com/javase/8/docs/technotes/tools/。

此处只介绍一些常用的监控工具：

| 工具   | 类型                                                         |
| ------ | ------------------------------------------------------------ |
| jps    | JVM进程状态工具，列出系统上的JVM进程                         |
| jinfo  | JVM信息查看工具，查看各种JVM配置                             |
| jstat  | JVM统计监控工具，附加到一个JVM进程上收集和记录JVM各种性能指标数据 |
| jstack | JVM栈查看工具，可以打印JVM进程的线程栈和锁情况               |
| jcmd   | JVM命令行调试工具，用于向JVM发送调试命令                     |
| jmap   | JVM堆内存分析工具，可以打印JVM进程对象直方图、类加载统计、以及做堆转储操作 |
| jhat   | JVM堆快照分析工具，内置HTTP服务器可以Web分析                 |

### 1.2 测试程序

为了方便测试这些工具，写一段测试代码：启动 10 个死循环的线程，每个线程分配一个 10MB 左右的字符串，然后休眠 3-10 秒。

```java
package com.example.webservice;

import org.apache.commons.lang3.RandomUtils;

import java.util.Random;
import java.util.UUID;
import java.util.concurrent.TimeUnit;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class JvmDemo {

    public static void main(String[] args) throws InterruptedException {
        IntStream.rangeClosed(1, 10).mapToObj(i -> new Thread(() -> {
            while (true) {
                //每一个线程都是一个死循环，休眠3-10秒，打印10M数据
                String payload = IntStream.rangeClosed(1, 10000000)
                        .mapToObj(j -> "a")
                        .collect(Collectors.joining("")) + UUID.randomUUID();
                try {
                    TimeUnit.SECONDS.sleep(RandomUtils.nextInt(3,10));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(payload.length());
            }
        })).forEach(Thread::start);

        TimeUnit.HOURS.sleep(1);
    }

}
```

修改 pom.xml，配置 spring-boot-maven-plugin 插件打包的 Java 程序的 main 方法类：

```xml
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.example.webservice.JvmDemo</mainClass>
                </configuration>
            </plugin>
```

启动jar包：

```shell
java -Xms1g -Xmx1g -jar jvm-0.0.1-SNAPSHOT.jar
```

### 1.3 常见错误

Jdk命令行工具在连接Jvm进程时，有可能出现如下错误：

![image-20220422101523168](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20220422101523168.png)

这个错误一般是因为连接到的进程非Java，可能是进程号写错了，或者是Linux内核的`kernel.yama.ptrace_scope`禁止在运行时连接进程，可以通过`sysctl -n kernel.yama.ptrace_scope`查看，0代表允许，1代表禁止。

# 二：jps命令

使用 jps 命令可以得到 Java 进程列表，会出进程ID和主类名称，有时候比使用 ps 来的方便：

![image-20220421231401279](http://rocks526.top/lzx/image-20220421231401279.png)

添加`-l参数`，可查看完整类名：

![image-20220422150815525](http://rocks526.top/lzx/image-20220422150815525.png)

# 三：jinfo命令

### 3.1 jinfo命令介绍

jinfo命令一般用来查看JVM进程的运行时参数信息，用于验证设置的参数是否生效。

### 3.2 使用 jinfo 命令打印 JVM 的各种参数

```shell
[root@10.48.124.162 ~]# jinfo 28363
Attaching to process ID 28363, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.181-b13
Java System Properties:

java.runtime.name = Java(TM) SE Runtime Environment
java.vm.version = 25.181-b13
sun.boot.library.path = /usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/amd64
java.protocol.handler.pkgs = org.springframework.boot.loader
java.vendor.url = http://java.oracle.com/
java.vm.vendor = Oracle Corporation
path.separator = :
file.encoding.pkg = sun.io
java.vm.name = Java HotSpot(TM) 64-Bit Server VM
sun.os.patch.level = unknown
sun.java.launcher = SUN_STANDARD
user.country = US
user.dir = /opt/lzx
java.vm.specification.name = Java Virtual Machine Specification
java.runtime.version = 1.8.0_181-b13
java.awt.graphicsenv = sun.awt.X11GraphicsEnvironment
os.arch = amd64
java.endorsed.dirs = /usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/endorsed
java.io.tmpdir = /tmp
line.separator = 

java.vm.specification.vendor = Oracle Corporation
os.name = Linux
sun.jnu.encoding = UTF-8
java.library.path = /usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
java.specification.name = Java Platform API Specification
java.class.version = 52.0
sun.management.compiler = HotSpot 64-Bit Tiered Compilers
os.version = 3.10.0-693.el7.x86_64
user.home = /root
user.timezone = Asia/Shanghai
java.awt.printerjob = sun.print.PSPrinterJob
file.encoding = UTF-8
java.specification.version = 1.8
user.name = root
java.class.path = jvm-0.0.1-SNAPSHOT.jar
java.vm.specification.version = 1.8
sun.arch.data.model = 64
sun.java.command = jvm-0.0.1-SNAPSHOT.jar
java.home = /usr/bin/hadoop/software/jdk1.8.0_181/jre
user.language = en
java.specification.vendor = Oracle Corporation
awt.toolkit = sun.awt.X11.XToolkit
java.vm.info = mixed mode
java.version = 1.8.0_181
java.ext.dirs = /usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/ext:/usr/java/packages/lib/ext
sun.boot.class.path = /usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/resources.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/rt.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/sunrsasign.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/jsse.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/jce.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/charsets.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/lib/jfr.jar:/usr/bin/hadoop/software/jdk1.8.0_181/jre/classes
java.vendor = Oracle Corporation
file.separator = /
java.vendor.url.bug = http://bugreport.sun.com/bugreport/
sun.io.unicode.encoding = UnicodeLittle
sun.cpu.endian = little
sun.cpu.isalist = 

VM Flags:
Non-default VM flags: -XX:CICompilerCount=15 -XX:InitialHeapSize=1073741824 -XX:MaxHeapSize=1073741824 -XX:MaxNewSize=357564416 -XX:MinHeapDeltaBytes=524288 -XX:NewSize=357564416 -XX:OldSize=716177408 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseFastUnorderedTimeStamps -XX:+UseParallelGC 
Command line:  -Xms1g -Xmx1g

```

### 3.3 使用 -flag 参数，查看某个具体的参数

![image-20220422151659518](http://rocks526.top/lzx/image-20220422151659518.png)

可以看到，命令行定义的-Xms和-Xmx已经生效了。

### 3.4 使用 -flags参数，查看JVM默认参数和命令行参数

![image-20220422151825487](http://rocks526.top/lzx/image-20220422151825487.png)

### 3.5 程序获取运行参数

除了jinfo命令外，程序里也可以通过API获取JVM参数，如下所示：

```java
public static void main(String[] args) throws InterruptedException {

        System.out.println("VM options");
        System.out.println(ManagementFactory.getRuntimeMXBean().getInputArguments().stream().collect(Collectors.joining(System.lineSeparator())));
        System.out.println("Program arguments");
        System.out.println(Arrays.stream(args).collect(Collectors.joining(System.lineSeparator())));


        IntStream.rangeClosed(1, 10).mapToObj(i -> new Thread(() -> {
            while (true) {
                //每一个线程都是一个死循环，休眠3-10秒，打印10M数据
                String payload = IntStream.rangeClosed(1, 10000000)
                        .mapToObj(j -> "a")
                        .collect(Collectors.joining("")) + UUID.randomUUID();
                try {
                    TimeUnit.SECONDS.sleep(RandomUtils.nextInt(3,10));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(payload.length());
            }
        })).forEach(Thread::start);

        TimeUnit.HOURS.sleep(1);
    }
```

![image-20220422100801253](http://rocks526.top/lzx/image-20220422100801253.png)

# 四：jstat命令

### 4.1 jstat命令介绍

jstat命令可以查看JVM的一些统计信息，主要包括以下三类：

- 类加载信息
- 垃圾回收信息
- JIT编译信息

jstat命令整体语法如下：

```shell
jstat -options vmid [interval] [count]
```

vmid是Jvm的ID，即进程ID；

interval是输出的周期，可选参数，jstat命令支持周期输出；

count是输出的次数，可选参数；

options是jstat要查看的类型，支持以下可选项：

- class：查看类加载、卸载、耗时信息
- compiler：查看JIT编译的统计信息
- `gc：查看堆内存和GC的统计信息`
- gccapacity：查看堆区和元数据区内存统计信息
- gcnew：查看年轻代GC统计信息，可理解为gc参数的阉割版
- gcnewcapacity：查看年轻代内存统计信息，可理解为gccapacity的阉割版
- gcold：查看老年代GC统计信息，可理解为gc参数的阉割版
- gcoldcapacity：查看老年代内存统计信息，可理解为gccapacity的阉割版
- gcmetacapacity：查看元数据空间内存统计信息，可理解为gccapacity的阉割版
- gcutil：查看垃圾回收的统计信息，显示的是各区的使用百分比

注：上述参数中，最常用也是最有用的一个选项是`gc`选项，可以用来统计年轻代、老年代内存的增长速度，估算GC频率，也能查看GC次数和总耗时，估算每次GC耗时。

### 4.2 查看类加载信息

- 查看当前类加载状态

![image-20220422152708983](http://rocks526.top/lzx/image-20220422152708983.png)

Loaded：当前加载的类的个数，此处是959个；

Bytes：当前加载的类的字节数，此处是1868KB，即1.8M；

UnLoaded：当前卸载的类的个数，此处是4个；

Bytes：当前卸载的类的字节数，此处是3.3KB；

Time：类加载和卸载耗费的时间，此处是0.3ms；

- 周期输出

![image-20220422153648133](http://rocks526.top/lzx/image-20220422153648133.png)

### 4.3 JIT编译信息

- 查看JIT编译信息

![image-20220422154501402](http://rocks526.top/lzx/image-20220422154501402.png)

 Compiled：执行的编译任务数，此处是295345个；

 Failed：执行失败的编译任务数，此处是223个；

 Invalid：无效的编译任务数，此处是0个；

 Time：执行编译任务所花费的时间，此处是17s；

 FailedType：上次编译失败的编译类型；

 FailedMethod：上次编译失败的类名和方法；

- 周期输出

![image-20220422154949123](http://rocks526.top/lzx/image-20220422154949123.png)

### 4.4 查看垃圾回收信息

- gc参数

![image-20220422160001637](http://rocks526.top/lzx/image-20220422160001637.png)

注：C是capacity，代表总容量；U是used，代表已被使用的；单位都是KB；

S0C、S1C、S0U、S1U：Survivor0和Survivor1的总容量及当前使用量

EC、EU：Eden区的总容量和使用量；

OC、OU：Old区的总容量和使用量；

MC、MU：元空间的总容量和使用量；

CCSC、CCSU：压缩类的总空间和使用量；

YGC、YGCT：YoungGC的总次数和总耗时；

FGC、FGCT：FullGC的总次数和总耗时；

GCT：GC总耗时；

注：其他选项不做演示，使用较少，详细信息参考官网文档。

# 五：jstack命令

### 5.1 jstack命令介绍

`jstack` 命令是JDK工具之一，使用该命令可以打印Jvm的线程栈信息，语法如下：

```shell
jstack -options vmid
```

options支持以下选项：

- F：当进程无响应时，可以通过-F强制打印线程栈
- l：长列表模式. 额外打印关于锁的信息
- m ：混合模式，可以打印 Java 栈和本地方法栈

### 5.2 jstack基础使用

`jstack 进程ID`可标准打印线程栈信息：

```shell
[root@10.48.124.162 lzx]# jstack 12722
2022-04-22 22:08:47
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.181-b13 mixed mode):

"Attach Listener" #31 daemon prio=9 os_prio=0 tid=0x00007f449c001000 nid=0x3d3a waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Thread-9" #30 prio=5 os_prio=0 tid=0x00007f453c5c8800 nid=0x31f6 waiting on condition [0x00007f44a08bb000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
	at java.lang.Thread.sleep(Native Method)
	at java.lang.Thread.sleep(Thread.java:340)
	at java.util.concurrent.TimeUnit.sleep(TimeUnit.java:386)
	at com.example.webservice.JvmDemo.lambda$null$1(JvmDemo.java:29)
	at com.example.webservice.JvmDemo$$Lambda$18/661672156.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)
..........................................
```

- 开头的是线程的名字，如上面"Thread-9"
- `#30` 表示当前线程ID，从 `main`线程开始，`JVM` 根据线程创建的顺序为线程编号
- `prio` 是`priority`优先级的缩写，表名了当前线程的优先级，取值范围为[1-10]，默认为 5；在虚拟机进行线程调度的时候会参考该优先级为线程分配计算资源，`这个数值越低越有优先获取到计算资源`，一般不设置直接使用默认的优先级
- `os_prio`为线程对应系统的优先级
- `nid` 本地线程编号`NativeID`的缩写，对应JVM虚拟机中线程映射在操作系统中的线程编号；可以使用 top 查看进程对应的线程情况进行相关映射

### 5.3 jstack锁阻塞分析

- 测试并发包的Lock锁

```java
package com.example.webservice;

import java.util.concurrent.locks.ReentrantLock;

public class JvmDemo2 {

    private static ReentrantLock lock = new ReentrantLock();

    public static void main(String[] args) {
        final Thread task1 = new Thread(new Task(), "JvmDemo-1");
        final Thread task2 = new Thread(new Task(), "JvmDemo-2");
        task1.start();
        task2.start();
    }
    private static class Task implements Runnable {
        @Override
        public void run() {
            lock.lock();
            int i = 0;
            while (true) {
                i++;
            }
        }
    }

}
```

`jstack -l`打印堆栈信息如下：

```shell
[root@10.48.124.162 lzx]# jstack -l 31318
2022-04-22 22:25:10
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.181-b13 mixed mode):

"Attach Listener" #24 daemon prio=9 os_prio=0 tid=0x00007f850c001000 nid=0x8901 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"DestroyJavaVM" #23 prio=5 os_prio=0 tid=0x00007f85bc009800 nid=0x7a5a waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"JvmDemo-2" #22 prio=5 os_prio=0 tid=0x00007f85bc5b2000 nid=0x7b6c waiting on condition [0x00007f8544ccb000]
   java.lang.Thread.State: WAITING (parking)
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  <0x00000000ec5e1ae8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:836)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(AbstractQueuedSynchronizer.java:870)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire(AbstractQueuedSynchronizer.java:1199)
	at java.util.concurrent.locks.ReentrantLock$NonfairSync.lock(ReentrantLock.java:209)
	at java.util.concurrent.locks.ReentrantLock.lock(ReentrantLock.java:285)
	at com.example.webservice.JvmDemo2$Task.run(JvmDemo2.java:18)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- None

"JvmDemo-1" #21 prio=5 os_prio=0 tid=0x00007f85bc5b0000 nid=0x7b6b runnable [0x00007f8544dcc000]
   java.lang.Thread.State: RUNNABLE
	at com.example.webservice.JvmDemo2$Task.run(JvmDemo2.java:21)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- <0x00000000ec5e1ae8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)

```

当一个线程正在等在其他线程释放该锁，状态为WAITING，线程堆栈会打印一个－`parking to wait for  <0x00000000ec5e1ae8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)`。

- 测试synchronized锁

```java
package com.example.webservice;

public class JvmDemo3 {

    private static Object lock = new Object();

    public static void main(String[] args) {
        final Thread task1 = new Thread(() -> {
            synchronized (lock){
                int i = 0;
                while (true){
                    i++;
                }
            }
        }, "JvmDemo-1");
        final Thread task2 = new Thread(() -> {
            synchronized (lock){
                int i = 0;
                while (true){
                    i++;
                }
            }
        }, "JvmDemo-2");
        task1.start();
        task2.start();
    }

}
```

`jstack -k`打印线程栈信息如下：

```shell
[root@10.48.124.162 lzx]# jstack -l 22982
2022-04-22 22:34:00
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.181-b13 mixed mode):

"Attach Listener" #24 daemon prio=9 os_prio=0 tid=0x00007f78a8001000 nid=0x5f85 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"DestroyJavaVM" #23 prio=5 os_prio=0 tid=0x00007f795c009800 nid=0x59c7 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"JvmDemo-2" #22 prio=5 os_prio=0 tid=0x00007f795c591000 nid=0x5a42 waiting for monitor entry [0x00007f78e8fce000]
   java.lang.Thread.State: BLOCKED (on object monitor)
	at com.example.webservice.JvmDemo3.lambda$main$1(JvmDemo3.java:18)
	- waiting to lock <0x00000000ecb00498> (a java.lang.Object)
	at com.example.webservice.JvmDemo3$$Lambda$13/94438417.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- None

"JvmDemo-1" #21 prio=5 os_prio=0 tid=0x00007f795c590000 nid=0x5a41 runnable [0x00007f78e90cf000]
   java.lang.Thread.State: RUNNABLE
	at com.example.webservice.JvmDemo3.lambda$main$0(JvmDemo3.java:12)
	- locked <0x00000000ecb00498> (a java.lang.Object)
	at com.example.webservice.JvmDemo3$$Lambda$12/1104106489.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- None

```

当一个线程正在等在其他线程释放synchronized锁，状态为BLOCKED，线程堆栈会打印一个－`waiting to lock <0x00000000ecb00498> (a java.lang.Object)`。

- 测试并发包的Lock锁导致的死锁

```java
package com.example.webservice;

import java.util.concurrent.locks.ReentrantLock;

public class JvmDemo4 {

    private static ReentrantLock lock = new ReentrantLock();
    private static ReentrantLock lock2 = new ReentrantLock();

    public static void main(String[] args) {
        final Thread task1 = new Thread(() -> {
           lock.lock();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            lock2.lock();
        } ,"JvmDemo-1");
        final Thread task2 = new Thread(() -> {
            lock2.lock();
            lock.lock();
        }, "JvmDemo-2");
        task1.start();
        task2.start();
    }

}
```

`jstack -l`打印线程栈信息如下：

```shell
[root@10.48.124.162 lzx]# jstack -l 5902
2022-04-22 22:40:21
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.181-b13 mixed mode):

"Attach Listener" #24 daemon prio=9 os_prio=0 tid=0x00007fa840001000 nid=0x18ef waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"DestroyJavaVM" #23 prio=5 os_prio=0 tid=0x00007fa8e0009800 nid=0x170f waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"JvmDemo-2" #22 prio=5 os_prio=0 tid=0x00007fa8e05a8800 nid=0x174c waiting on condition [0x00007fa844bc5000]
   java.lang.Thread.State: WAITING (parking)
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  <0x00000000ecb01588> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:836)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(AbstractQueuedSynchronizer.java:870)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire(AbstractQueuedSynchronizer.java:1199)
	at java.util.concurrent.locks.ReentrantLock$NonfairSync.lock(ReentrantLock.java:209)
	at java.util.concurrent.locks.ReentrantLock.lock(ReentrantLock.java:285)
	at com.example.webservice.JvmDemo4.lambda$main$1(JvmDemo4.java:22)
	at com.example.webservice.JvmDemo4$$Lambda$13/94438417.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- <0x00000000ecb015b8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)

"JvmDemo-1" #21 prio=5 os_prio=0 tid=0x00007fa8e05a7000 nid=0x174b waiting on condition [0x00007fa844cc6000]
   java.lang.Thread.State: WAITING (parking)
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  <0x00000000ecb015b8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:836)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(AbstractQueuedSynchronizer.java:870)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire(AbstractQueuedSynchronizer.java:1199)
	at java.util.concurrent.locks.ReentrantLock$NonfairSync.lock(ReentrantLock.java:209)
	at java.util.concurrent.locks.ReentrantLock.lock(ReentrantLock.java:285)
	at com.example.webservice.JvmDemo4.lambda$main$0(JvmDemo4.java:18)
	at com.example.webservice.JvmDemo4$$Lambda$12/1104106489.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- <0x00000000ecb01588> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)

...............................

Found one Java-level deadlock:
=============================
"JvmDemo-2":
  waiting for ownable synchronizer 0x00000000ecb01588, (a java.util.concurrent.locks.ReentrantLock$NonfairSync),
  which is held by "JvmDemo-1"
"JvmDemo-1":
  waiting for ownable synchronizer 0x00000000ecb015b8, (a java.util.concurrent.locks.ReentrantLock$NonfairSync),
  which is held by "JvmDemo-2"

Java stack information for the threads listed above:
===================================================
"JvmDemo-2":
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  <0x00000000ecb01588> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:836)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(AbstractQueuedSynchronizer.java:870)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire(AbstractQueuedSynchronizer.java:1199)
	at java.util.concurrent.locks.ReentrantLock$NonfairSync.lock(ReentrantLock.java:209)
	at java.util.concurrent.locks.ReentrantLock.lock(ReentrantLock.java:285)
	at com.example.webservice.JvmDemo4.lambda$main$1(JvmDemo4.java:22)
	at com.example.webservice.JvmDemo4$$Lambda$13/94438417.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)
"JvmDemo-1":
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  <0x00000000ecb015b8> (a java.util.concurrent.locks.ReentrantLock$NonfairSync)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:175)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.parkAndCheckInterrupt(AbstractQueuedSynchronizer.java:836)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquireQueued(AbstractQueuedSynchronizer.java:870)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.acquire(AbstractQueuedSynchronizer.java:1199)
	at java.util.concurrent.locks.ReentrantLock$NonfairSync.lock(ReentrantLock.java:209)
	at java.util.concurrent.locks.ReentrantLock.lock(ReentrantLock.java:285)
	at com.example.webservice.JvmDemo4.lambda$main$0(JvmDemo4.java:18)
	at com.example.webservice.JvmDemo4$$Lambda$12/1104106489.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.
```

两个线程状态都是WAITING，分别阻塞于`parking to wait for  <0x00000000ecb015b8>`和`parking to wait for  <0x00000000ecb01588>`，并且Jvm已经自动检测到这个死锁，在jstack末尾打印了这两个线程信息。

- 测试synchronized锁导致的死锁

```java
package com.example.webservice;

public class JvmDemo5 {

    private static Object lock = new Object();
    private static Object lock2 = new Object();

    public static void main(String[] args) {
        final Thread task1 = new Thread(() -> {
            synchronized (lock){
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (lock2){
                    int i = 0;
                    while (true){
                        i++;
                    }
                }
            }
        }, "JvmDemo-1");
        final Thread task2 = new Thread(() -> {
            synchronized (lock2){
                synchronized (lock){
                    int i = 0;
                    while (true){
                        i++;
                    }
                }
            }
        }, "JvmDemo-2");
        task1.start();
        task2.start();
    }

}
```

`jstack -l`打印线程栈信息如下：

```shell
[root@ngsoc162 lzx]# jstack -l 1381
2022-04-22 23:00:34
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.181-b13 mixed mode):

"Attach Listener" #24 daemon prio=9 os_prio=0 tid=0x00007f9e80001000 nid=0x7fd waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"DestroyJavaVM" #23 prio=5 os_prio=0 tid=0x00007f9f1c009800 nid=0x566 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"JvmDemo-2" #22 prio=5 os_prio=0 tid=0x00007f9f1c587800 nid=0x59e waiting for monitor entry [0x00007f9e9a5e4000]
   java.lang.Thread.State: BLOCKED (on object monitor)
	at com.example.webservice.JvmDemo5.lambda$main$1(JvmDemo5.java:27)
	- waiting to lock <0x00000000ecb013a0> (a java.lang.Object)
	- locked <0x00000000ecb013b0> (a java.lang.Object)
	at com.example.webservice.JvmDemo5$$Lambda$13/94438417.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- None

"JvmDemo-1" #21 prio=5 os_prio=0 tid=0x00007f9f1c586800 nid=0x59d waiting for monitor entry [0x00007f9e9a6e5000]
   java.lang.Thread.State: BLOCKED (on object monitor)
	at com.example.webservice.JvmDemo5.lambda$main$0(JvmDemo5.java:17)
	- waiting to lock <0x00000000ecb013b0> (a java.lang.Object)
	- locked <0x00000000ecb013a0> (a java.lang.Object)
	at com.example.webservice.JvmDemo5$$Lambda$12/1104106489.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
	- None
..............................................

Found one Java-level deadlock:
=============================
"JvmDemo-2":
  waiting to lock monitor 0x00007f9e88004e28 (object 0x00000000ecb013a0, a java.lang.Object),
  which is held by "JvmDemo-1"
"JvmDemo-1":
  waiting to lock monitor 0x00007f9e88006218 (object 0x00000000ecb013b0, a java.lang.Object),
  which is held by "JvmDemo-2"

Java stack information for the threads listed above:
===================================================
"JvmDemo-2":
	at com.example.webservice.JvmDemo5.lambda$main$1(JvmDemo5.java:27)
	- waiting to lock <0x00000000ecb013a0> (a java.lang.Object)
	- locked <0x00000000ecb013b0> (a java.lang.Object)
	at com.example.webservice.JvmDemo5$$Lambda$13/94438417.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)
"JvmDemo-1":
	at com.example.webservice.JvmDemo5.lambda$main$0(JvmDemo5.java:17)
	- waiting to lock <0x00000000ecb013b0> (a java.lang.Object)
	- locked <0x00000000ecb013a0> (a java.lang.Object)
	at com.example.webservice.JvmDemo5$$Lambda$12/1104106489.run(Unknown Source)
	at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.
```

两个线程状态都是BLOCKED，分别阻塞于`waiting to lock <0x00000000ecb013a0>`和`waiting to lock <0x00000000ecb013b0>`，并且Jvm已经自动检测到这个死锁，在jstack末尾打印了这两个线程信息。

### 5.4 fastthread

jstack导出线程栈信息后，除了人工检查处理外，还可以通过一些工具进行分析，例如fastthread在线分析工具，可以根据线程栈信息生成一份报告。

fastthread：https://fastthread.io/。

# 六：jmap命令

### 6.1 jmap命令介绍

jmap命令是JVM堆内存分析的工具，可以打印JVM进程对象直方图、类加载统计、以及做堆转储操作，语法如下：

```shell
jmap -options vmid
```

vmid代表进程ID；

options参数支持以下选项：

- heap：打印堆的摘要信息，包括GC算法，堆的配置信息和使用信息；
- histo：按照类进行归纳，该命令会统计Java进程内每个类的对象个数之和，和该类所有对象的占用的空间之和，以及类名称；

`注：该命令在线上执行的时候要做好评估，否则会导致线上机器宕机`；jmap -histo:live 这个命令执行，JVM会先触发gc，然后再统计信息。

- clstats：打印类加载器信息；
- heap：导出堆内存快照；
- F：当进程无响应时，可以通过-F强制执行，live参数不会生效；

### 6.2 查看堆统计信息

`jmap -heap 进程ID`查看堆内存情况如下：

```shell
[root@10.48.124.162 logs]# jmap -heap 16967
Attaching to process ID 16967, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.181-b13

using thread-local object allocation.
Parallel GC with 28 thread(s)

Heap Configuration:		# 堆配置
   MinHeapFreeRatio         = 0	# 堆使用率小于此值时进行收缩，由于配置了-Xms=-Xmx，因此是0，表示永不收缩
   MaxHeapFreeRatio         = 100	# 堆使用率大于此值时进行扩容，由于配置了-Xms=-Xmx，因此是100，表示永不扩容
   MaxHeapSize              = 1073741824 (1024.0MB)	# 堆的最大容量
   NewSize                  = 357564416 (341.0MB)	# 新生代大小
   MaxNewSize               = 357564416 (341.0MB)	# 新生代最大容量
   OldSize                  = 716177408 (683.0MB)	# 老年代大小
   NewRatio                 = 2	# E、S0、S1的比例为8:1:1
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)	# 元空间大小
   CompressedClassSpaceSize = 1073741824 (1024.0MB)	# 指针压缩大小
   MaxMetaspaceSize         = 17592186044415 MB	# 最大元空间大小
   G1HeapRegionSize         = 0 (0.0MB)	# G1的每块分区大小，此时没有使用G1所以是0

Heap Usage:		# 堆使用情况
PS Young Generation		# 年轻代
Eden Space:		# Eden区
   capacity = 119537664 (114.0MB)		# 容量
   used     = 61222536 (58.38636016845703MB)	# 已使用的内存
   free     = 58315128 (55.61363983154297MB)	# 剩余内存
   51.21610541092722% used		# 使用率
From Space:		# S0区
   capacity = 119013376 (113.5MB)	# 容量
   used     = 40098336 (38.240753173828125MB)	# 已使用内存
   free     = 78915040 (75.25924682617188MB)	# 剩余内存
   33.692293545223016% used		# 使用率
To Space:		# S1区
   capacity = 119013376 (113.5MB)	# 容量
   used     = 0 (0.0MB)		# 使用率
   free     = 119013376 (113.5MB)	# 剩余内存
   0.0% used		# 使用率
PS Old Generation	# 老年代
   capacity = 716177408 (683.0MB)	# 容量
   used     = 535845448 (511.0220413208008MB)	# 已使用的内存
   free     = 180331960 (171.97795867919922MB)	# 剩余内存
   74.8202110279357% used	# 使用率

1737 interned Strings occupying 131384 bytes.	# 字符串个数和占用内存大小，130KB
```

### 6.3 查看堆里对象统计信息

`jmap -histo 进程ID`查看堆里对象个数和占用内存大小信息：

```shell
[root@10.48.124.162 logs]# jmap -histo 16967

 num     #instances         #bytes  class name
----------------------------------------------
   1:          4276      362769192  [C
   2:           779       23065744  [I
   3:           805         224696  [B
   4:          1079         123144  java.lang.Class
   5:          4248         101952  java.lang.String
   6:          1843          58976  java.util.concurrent.ConcurrentHashMap$Node
   7:          1102          55352  [Ljava.lang.Object;
   8:           548          17536  java.util.HashMap$Node
   9:            17          13776  [Ljava.util.concurrent.ConcurrentHashMap$Node;
  10:           233          13048  java.lang.invoke.MemberName
  11:           341          10912  sun.misc.FDBigInteger
  12:            29          10904  java.lang.Thread
  13:           228           9120  java.lang.ref.SoftReference
  14:           226           9040  java.util.LinkedHashMap$Entry
  15:            90           8808  [Ljava.util.HashMap$Node;
  16:           104           8760  [Ljava.lang.String;
  17:           247           7904  java.util.Hashtable$Entry
  18:           243           7776  java.lang.invoke.MethodType$ConcurrentWeakInternSet$WeakEntry
...........................................
```

- instances：对象个数
- bytes : 占用字节数
- class name : 对象所属的类

### 6.4 查看类加载器信息

` jmap -clstats 进程ID`查看类加载器信息：

```shell
[root@10.48.124.162 logs]# jmap -clstats 16967 
Attaching to process ID 16967, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.181-b13
finding class loader instances ..done.
computing per loader stat ..done.
please wait.. computing liveness.liveness analysis may be inaccurate ...
# 类加载器	加载的类个数  字节数	父类加载器	是否存活    类型
class_loader	classes	bytes	parent_loader	alive?	type

<bootstrap>	805	1614676	  null  	live	<internal>
0x00000000c00357c8	40	74155	0x00000000c0035828	dead	sun/misc/Launcher$AppClassLoader@0x000000010000f8d0
0x00000000c0035828	0	0	  null  	dead	sun/misc/Launcher$ExtClassLoader@0x000000010000fc78
0x00000000c003b8e8	3	7130	0x00000000c00357c8	dead	org/springframework/boot/loader/LaunchedURLClassLoader@0x0000000100060828

# 统计，总共4个加载器，加载了848个类，总大小1.6M，目前存活一个类加载器，死亡三个
total = 4	848	1695961	    N/A    	alive=1, dead=3	    N/A  
```

### 6.5 导出堆内存快照

`jmap -dump:format=b,file=./ 进程ID`可导出堆内存快照：

```shell
[root@10.48.124.162 lzx]# jmap -dump:format=b,file=/opt/lzx/jvm-2022-04-22.hprof 16967 
Dumping heap to /opt/lzx/jvm-2022-04-22.hprof ...
Heap dump file created
[root@10.48.124.162 lzx]# ll
total 577308
-rw-r--r-- 1 root root  70796171 Apr 21 22:14 apache-jmeter-5.4.3.tgz
-rw-r--r-- 1 root root  21238680 Apr 22 10:00 jvm-0.0.1-SNAPSHOT.jar
-rw------- 1 root root 499110425 Apr 22 18:01 jvm-2022-04-22.hprof		# 生成的堆内存快照，需要通过jhat或者MAT工具进行分析
```

> 除了jmap手动导出堆内存快照之外，还可以通过以下参数自动导出：
>
> - -XX：+HeapDumpOnOutOfMemoryError：系统OOM时自动导出堆内存快照
> - -XX：HeapDumpPath=./logs/java_heapdump_20220421185626.hprof：导出堆内存快照的文件存放位置

# 七：jhat命令

### 7.1 jhat命令介绍

jhat(JVM Heap Analysis Tool)是Jdk提供的用来分析堆内存快照的工具。

jhat内置了一个微型的HTTP/HTML服务器，生成dump文件的分析结果后，用户可以在浏览器中查看分析结果（分析虚拟机转储快照信息）。

语法如下：

```shell
jhat -options hprof文件
```

- stack：关闭或打开对象分配调用栈跟踪，默认true
- refs ：关闭｜打开对象引用跟踪，默认true
- port ：设置 HTTP Server的端口号，默认7000
- exclude ： 执行对象查询时需要排除的数据成员
- baseline ：指定一个基准堆转储
- debug ：设置debug级别

### 7.2 分析堆内存快照

`jhat jvm-2022-04-22.hprof `可分析堆内存快照，内置HTTP服务器分析：

```shell
[root@10.48.124.162 lzx]# jhat jvm-2022-04-22.hprof 
Reading from jvm-2022-04-22.hprof...
Dump file created Fri Apr 22 18:01:23 CST 2022
Snapshot read, resolving...
Resolving 21688 objects...
Chasing references, expect 4 dots....
Eliminating duplicate references....
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.

```

![image-20220422215030344](http://rocks526.top/lzx/image-20220422215030344.png)

![image-20220422215240718](http://rocks526.top/lzx/image-20220422215240718.png)