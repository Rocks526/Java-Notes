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

