- [Jvm概述](#jvm-gs)
- [Jvm内存管理](#jvm-ncgl)
  - [内存区域](#jvm-nc)
  - [垃圾回收器](#jvm-gc)
  - [Jvm监控工具](#jvm-jk)
- [Jvm底层实现](#jvm-dcsx)
  - [类文件结构](#jvm-class)
  - [类加载机制](#jvm-classloader)
  - [字节码执行引擎](#jvm-engine)
- [Jvm特性](#jvm-tx)

----------------------

# <a id="jvm-gs">Jvm概述</a>







# <a id="jvm-ncgl">Jvm内存管理</a>





### <a id="jvm-nc">内存区域</a>



### <a id="jvm-gc">垃圾回收器</a>





### <a id="jvm-jk">Jvm监控工具</a>

#### Jdk自带工具

> 在Jdk包里，除了JRE之外，bin目录下带了很多Jvm的监控，分析工具，以Jdk1.8为例，包括以下工具：

![image-20201203105111175](http://rocks526.top/lzx/image-20201203105111175.png)

- 基础工具：用于支持基本的程序创建和运行

appletviewer：在不使用Web浏览器的情况下运行和调试Applet，Jdk11中被移除

extcheck：检查JAR冲突的工具，从Jdk9中被移除

jar：创建和管理JAR文件

java：Java解释器，用于运行class文件或JAR文件

javac：java编译器

javadoc：Java的API文档生成工具

javah：C语言头文件和Stub函数生成器，用于编写JNI方法

javap：Java字节码分析工具

jlink：将Module和它的依赖打包成一个运行时镜像文件

jdb：基于JPDA协议的调试器，以类似于GDB的方式调试Java代码

jdeps：Java类依赖性分析器

jdeprscan：用于搜索JAR包中使用了"deprecated"的类，从Jdk9开始提供

- 安全相关：用于程序签名，设置安全测试等

keytool：管理密匙库和证书。

jarsigner：生成并验证JAR签名

policytool：管理策略文件的GUI工具，用于管理用户策略文件(.java.policy)，在Jdk10中被移除

- 国际化：用于创建本地语言文件

native2ascii：本地编码到ASCII编码的转换器

- 远程方法调用：用于跨Web或网络的服务交互

rmic：Java RMI编译器，为使用JRMP和IIOP协议的远程对象生成Stub，Skeleton和Tie类，也用于生成OMG IDL

rmiregistry：远程对象注册表服务，用于在当前主机的指定端口上创建并启动一个远程对象注册表

rmid：启动激活系统守护进程，允许在虚拟机中注册或激活对象

serialver：生成并返回指定类的序列化版本ID

- 部署工具：用于程序打包、发布和部署

javapackager：打包、签名Java和JavaFX应用程序，Jdk11中被移除

pack200：使用Java Gzip压缩器将JAR文件转换成Pack200文件。压缩的压缩文件是高度压缩的JAR，可以直接部署，节省带宽并减少时间。

unpack200：将pack200生成的打包文件解压提取成JAR文件

- Java Web Start

javaws：启动Java Web Start并设置各种选项的工具

- 性能监控和故障处理：用于监控分析Java虚拟机运行信息，排查问题

jps：JVM Process Status Tool，显示指定系统内所有的HotSpot虚拟机进程

jstat：JVM Statistics Monitoring Tool，用于收集HotSpot虚拟机各方面运行数据

jstatd：JVM Statistics Monitoring Tool Deamon，jstat的守护进程，启动一个RMI服务器程序，用于监视测试HotSpot虚拟机的创建和停止，并提供界面允许远程监控。在Jdk9中集成到JHSDB中。

jinfo：显示虚拟机配置信息，包括启动时设置的参数，系统环境参数等，可以在运行时动态修改部分参数。

jmap：生成虚拟机的内存转储快照信息。

jhat：用于分析jmap导出的内存快照信息，会生成一个Web界面显示分析结果，Jdk9之后被JHSDB代替。

jstack：显示虚拟机的线程快照问题，在Jdk9中被集成到JHSDB。

jhsdb：一个基于Serviceability Agent的HotSpot进程调试器，从Jdk9开始提供

jsadebugd：适用于Java的可维护性代理调试守护程序，主要用于附加到指定的Java进程、核心文件，充当一个调试器

jcmd：虚拟机诊断命令工具，将诊断命令请求发送到正在运行的Java虚拟机

jconsole：用于监控虚拟机的使用JMX规范的图形化工具，可以监控本地和远程的Jvm。

jmc：包含用于监控和管理Java应用程序的工具。

jvisualvm：一种图形化工具，可在Java虚拟机中运行时提供有关于基于Java技术的应用程序的详细信息。包括内存，CPU分析，堆转储分析，内存泄漏检测，MBean访问和垃圾收集等功能。Jdk6开始提供，Jdk9之后不再打入Jdk包，但可免费下载。

- WebService工具：与CORBA一起在JDK 11中被移除

schemagen：用于XML绑定的Schema生成器，生成XML schema文件。

wsgent：XML Web Service2.0的Java API，生成用于JAX-WS Web Service的JAX-WS便携式产物。

wsimport：XML Web Service2.0的Java API，用于根据服务端发布的WSDL文件生成客户端。

xjc：主要用于根据XML schema文件生成对应的Java类。

- REPL和脚本工具

jshell：Java的Shell交互工具。

jjs：对Nashorn引擎的调用入口。Nashorn是基于Java实现的一个轻量级高性能的JS运行环境。

jrunscript：Java命令行脚本外壳工具，主要用于解释执行JS，Groovy，Ruby等脚本语言。

#### 常用工具

##### Jvm参数

> https://blog.csdn.net/wang379275614/article/details/78471604
>
> https://www.cnblogs.com/xuwenjin/p/13092857.html

Jvm的参数类型主要分三大类：标准参数，X参数和XX参数。

- 标准参数：在Jdk版本中基本不变的参数，比较稳定

-help：查看帮助信息

-server  -client：选择Jvm按照Server/Client模式运行，默认是Server

-version  -showversion：显示Java版本号

-cp -classpath：设置Java的类搜索目录

- X参数：非标准化参数，不同版本的Jdk可能不一致，但变化较少

-Xint：解释执行

-Xcomp：第一次使用就编译成本地代码

-Xmixed：混合模式，Jvm自行决定是否编译(默认mixed模式)

- XX参数：非标准化参数，相对不稳定，一般用于Jvm调优和debug

-XX:[+-]\<name\>：表示启动或禁用name属性

例如-XX:+UseConcMarkSweepGC表示启用CMS垃圾收集器，例如-XX:-UseG1GC表示禁用G1垃圾收集器。

-XX:\<name\>=\<value\>表示name的属性的值为value

例如-XX:MaxGCPauseMillis=500表示设置GC最大的停顿时间是500毫秒

- 常用Jvm参数

**-Xms：** JVM启动时申请的初始Heap值，默认为操作系统物理内存的1/64但小于1G。默认当空余堆内存大于70%时，JVM会减小heap的大小到-Xms指定的大小，可通过-XX:MaxHeapFreeRation=来指定这个比列。**Server端JVM最好将-Xms和-Xmx设为相同值，避免每次垃圾回收完成后JVM重新分配内存**。

**-Xmx：** JVM可申请的最大Heap值，默认值为物理内存的1/4但小于1G，默认当空余堆内存小于40%时，JVM会增大Heap到-Xmx指定的大小，可通过-XX:MinHeapFreeRation=来指定这个比列。最佳设值应该视物理内存大小及计算机内其他内存开销而定。

**-Xmn：**Java Heap Young区大小。持久代一般固定大小为64m，所以增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，Sun官方推荐配置为整个堆的3/8。

**-XX:PretenureSizeThreshold：**大对象的阈值，大于此值的对象直接分配到老年代。

**-Xss：**Java每个线程的Stack大小。JDK5.0以后每个线程堆栈大小为1M，以前每个线程堆栈大小为256K。根据应用的线程所需内存大小进行调整。**在相同物理内存下，减小这个值能生成更多的线程。**但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在3000~5000左右。

**-XX:PermSize：**永久代（方法区）的初始内存大小。

**-XX:MaxPermSize：**永久代（方法区）的最大内存大小。

**-XX:MetaspaceSize：**元空间初始化大小。

**-XX:MaxMetaspaceSize：**元空间最大大小。(Jdk8之后，永久代改名元空间)

**-XX:+UseCompressedClassesPointers：**开启类引用压缩。

**-XX:+UseCompressedOops：**开启对象引用压缩。

**-XX:CompressedClassSpaceSize：**存放以上两个指针的地方。

**-XX:InitialCodeCacheSize：**调整JIT的编译缓存大小，一般不同调整。

**-XX:ReservedCodeCacheSize：**调整JIT的编译缓存大小，一般不同调整。

**-XX:-OmitStackTraceInFastThrow：**JIT编译优化，会将频繁出现的异常不打印堆栈信息，此参数可禁止这个优化。

**-XX:+HeapDumpOnOutOfMemoryError：**OOM时自动备份堆快照。

**-XX:HeapDumpPath：**堆快照的备份路径。

**-XX:OnOutOfMemoryError：**参数值是一个shell脚本，当内存溢出时，可以调用此shell脚本，脚本里可以优雅关闭或重启。

**-XX:-UseLargePages：**禁止大内存页。

>  -XX:LargePageSizeInBytes：如果开启大内存页，可以通过此参数指定大小。

**-Xloggc：**GC输出日志路径。

**-verbose:gc**：GC时将GC信息输出。

**-XX:+PrintGCDetails：**输出GC的详细信息。

**-XX:+PrintGCDateStamps：**输出GC的日期信息，默认输出格式是距离启动多长时间。

**-XX:+PrintGCTimeStamps：**输出GC的时间信息。

**-XX:+UseGCLogFileRotation：**开启GC日志文件轮转，即当日志文件大小到达指定大小时，开始写入下一个文件。

**-XX:NumberOfGCLogFiles=10：**一共可以写入10个文件。

**-XX:GCLogFileSize=100M：**每个文件最大100M。

> 需要注意，如果所有文件全部存满时，会删除旧的日志文件，写入新的gc日志。

**-XX:UseSerialGC：**年轻代使用Serial垃圾收集器。

**-XX:UseParallelGC：**年轻代使用Parallel Scavenge垃圾收集器。

**-XX:+UseParNewGC：**年轻代使用ParNew垃圾收集器。

**-XX:+UseParallelOldGC：**老年代使用Parallel Old收集器。

**-XX:+UseConcMarkSweepGC：**老年代使用CMS收集器，青年代默认使用ParNew。

**-XX:+UseCMSCompactAtFullCollection：**CMS收集器的参数，在Full GC的时候，对老年代进行压缩整理。

> 因为CMS是不会移动内存的，因此非常容易产生内存碎片。因此增加这个参数就可以在FullGC后对内存进行压缩整理，消除内存碎片。当然这个操作也有一定缺点，就是会增加CPU开销与GC时间，所以可以通过-XX:CMSFullGCsBeforeCompaction这个参数来控制多少次Full GC以后进行一次碎片整理。

**-XX:+CMSInitiatingOccupancyFraction：**CMS收集器的参数，代表老年代使用空间达到80%后，就进行Full GC，默认92%

**-XX:+PrintFlagsInitial** ==>  查看参数的初始值(可能后来被修改)

**-XX:+PrintFlagsFinal**  ==>  查看参数的最终值

**-XX:+UnlockExperimentalVMOptions**  ==>  解锁实验参数

> Jvm有的参数不能直接通过jinfo赋值，需要先解锁

**-XX:+UnlockDiagnosticVMOptions**  ==>  解锁诊断参数

**-XX:+PrintCommandLineFlags**  ==>  打印命令行参数

----------------------

##### jps：查看Java进程的ID

可以列出正在运行的虚拟机进程，并显示虚拟机执行主类（Main Class，main()函数所在的类）名称以及这些进程的本地虚拟机唯一ID。

![image-20201203185257000](http://rocks526.top/lzx/image-20201203185257000.png)

> jps -l -q -m -v
>
> -l代表输出主类的全名，如果是JAR包，输出JAR包全路径
>
> -v输出JVM启动时参数
>
> -m输出启动时传递给main函数的参数
>
> -q只输出进程ID，忽略名字

##### jinfo：查看Jvm运行时的参数信息，可以修改部分参数

jinfo -flag 属性 进程ID  ==>  查询该进程对应的该属性的值

![image-20201203185426205](http://rocks526.top/lzx/image-20201203185426205.png)

jinfo -flags 进程ID  ==>  查询该进程启动时修改的值

##### jstat：用于监视虚拟机各种运行状态信息







##### jmap







##### jstack















