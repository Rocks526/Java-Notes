- [Spark基础](#spark-base)
- [Spark Core](#spark-core)
- [Spark SQL](#spark-sql)
- [Spark Streaming](#spark-streaming)

------------------------

# <a id="spark-base">Spark基础</a>

### Spark概述

Spark是一种**基于内存**快速、通用、可扩展的大数据分析引擎，2009年诞生于加州大学伯克利分校AMPLab，2010年开源，2013年6月成为Apache孵化项目，2014年2月成为Apache顶级项目。项目是用Scala进行编写。

目前，Spark生态系统已经发展成为一个包含多个子项目的集合，其中包含SparkSQL、Spark Streaming、GraphX、MLib、SparkR等子项目，Spark是基于内存计算的大数据并行计算框架。除了扩展了广泛使用的 MapReduce 计算模型，而且高效地支持更多计算模式，包括交互式查询和流处理。Spark 适用于各种各样原先需要多种不同的分布式平台的场景，包括批处理、迭代算法、交互式查询、流处理。通过在一个统一的框架下支持这些不同的计算，Spark 使我们可以简单而低耗地把各种处理流程整合在一起。

### Spark生态

![image-20201207112047418](http://rocks526.top/lzx/image-20201207112047418.png)

**Spark Core：** 实现了 Spark 的基本功能，包含任务调度、内存管理、错误恢复、与存储系统交互等模块。Spark Core 中还包含了对弹性分布式数据集(resilient distributed dataset，简称RDD)的 API 定义。 
**Spark SQL：** 是 Spark 用来操作结构化数据的程序包。通过 Spark SQL，我们可以使用 SQL 或者 Apache Hive 版本的 SQL 方言(HQL)来查询数据。Spark SQL 支持多种数据源，比如Hive 表、Parquet 以及 JSON 等。 
**Spark Streaming：** 是 Spark 提供的对实时数据进行流式计算的组件。提供了用来操作数据流的 API，并且与 Spark Core 中的 RDD API 高度对应。 
**Spark MLlib：** 提供常见的机器学习(ML)功能的程序库。包括分类、回归、聚类、协同过滤等，还提供了模型评估、数据导入等额外的支持功能。 
**集群管理器：** Spark 设计为可以高效地在一个计算节点到数千个计算节点之间伸缩计算。为了实现这样的要求，同时获得最大灵活性，Spark 支持在各种集群管理器(cluster manager)上运行，包括 Hadoop YARN、Apache Mesos，以及Spark 自带的一个简易调度器，叫作独立调度器。

Spark得到了众多大数据公司的支持，这些公司包括Hortonworks、IBM、Intel、Cloudera、MapR、Pivotal、百度、阿里、腾讯、京东、携程、优酷土豆。当前百度的Spark已应用于凤巢、大搜索、直达号、百度大数据等业务；阿里利用GraphX构建了大规模的图计算和图挖掘系统，实现了很多生产系统的推荐算法；腾讯Spark集群达到8000台的规模，是当前已知的世界上最大的Spark集群。

### Spark特点

- 快

与Hadoop的MapReduce相比，Spark基于内存的运算要快100倍以上，基于硬盘的运算也要快10倍以上。Spark实现了高效的DAG执行引擎，可以通过基于内存来高效处理数据流。计算的中间结果是存在于内存中的。

- 易用

Spark支持Java、Python和Scala的API，还支持超过80种高级算法，使用户可以快速构建不同的应用。而且Spark支持交互式的Python和Scala的shell，可以非常方便地在这些shell中使用Spark集群来验证解决问题的方法。

- 通用

Spark提供了统一的解决方案。Spark可以用于批处理、交互式查询（Spark SQL）、实时流处理（Spark Streaming）、机器学习（Spark MLlib）和图计算（GraphX）。这些不同类型的处理都可以在同一个应用中无缝使用。Spark统一的解决方案非常具有吸引力，毕竟任何公司都想用统一的平台去处理遇到的问题，减少开发和维护的人力成本和部署平台的物力成本。

- 兼容性

Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。这对于已经部署Hadoop集群的用户特别重要，因为不需要做任何数据迁移就可以使用Spark的强大处理能力。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。此外，Spark还提供了在EC2上部署Standalone的Spark集群的工具。

### Spark环境搭建

#### 相关参考资料

- Spark官网：http://spark.apache.org/
- Spark中文文档：http://spark.apachecn.org/

#### Spark主要角色

![image-20201207113357870](http://rocks526.top/lzx/image-20201207113357870.png)

从物理部署层面上来看，Spark主要分为两种类型的节点，Master节点和Worker节点，Master节点主要运行集群管理器的中心化部分，所承载的作用是分配Application到Worker节点，维护Worker节点，Driver，Application的状态。Worker节点负责具体的业务运行。

从Spark程序运行的层面来看，Spark主要分为驱动器节点和执行器节点。

- Driver(驱动器)

Spark的驱动器是执行开发程序中的main方法的进程。它负责开发人员编写的用来创建SparkContext、创建RDD，以及进行RDD的转化操作和行动操作代码的执行。如果你是用spark shell，那么当你启动Spark shell的时候，系统后台自启了一个Spark驱动器程序，就是在Spark shell中预加载的一个叫作sc的SparkContext对象。如果驱动器程序终止，那么Spark应用也就结束了，主要负责以下任务：

1. 把用户程序转为作业（JOB）

2. 跟踪Executor的运行状况

3. 为执行器节点调度任务

4. UI展示应用运行状况

- Exceutor(执行器)

Spark Executor是一个工作进程，负责在 Spark 作业中运行任务，任务间相互独立。Spark 应用启动时，Executor节点被同时启动，并且始终伴随着整个 Spark 应用的生命周期而存在。如果有Executor节点发生了故障或崩溃，Spark 应用也可以继续执行，会将出错节点上的任务调度到其他Executor节点上继续运行。主要负责：

1. 负责运行组成 Spark 应用的任务，并将结果返回给驱动器进程；

2. 通过自身的块管理器（Block Manager）为用户程序中要求缓存的RDD提供内存式存储。RDD是直接缓存在Executor进程内的，因此任务可以在运行时充分利用缓存数据加速运算。

#### Local模式

Local模式就是运行在一台计算机上，通常用于在本机上练手和测试。可以通过以下几种方式设置Master：

- local：所有计算都在一个线程当中，没有任何并行计算。
- local[K]：指定使用几个线程来进行计算，比如local[4]就是运行4个Worker线程。
- local[*]：按照机器CPU设置线程数。

**安装步骤**

1. 上传并解压spark安装包

```shell
tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz
```

![image-20201207165949479](http://rocks526.top/lzx/image-20201207165949479.png)

2. 通过Spark自带的脚本检测是否安装成功

```shell
cd spark-2.1.1-bin-hadoop2.7
bin/spark-submit --class org.apache.spark.examples.SparkPi --executor-memory 1G --total-executor-cores 2 ./examples/jars/spark-examples_2.11-2.1.1.jar 100
```

![image-20201207170628304](http://rocks526.top/lzx/image-20201207170628304.png)

> 如果出现java not set是因为Java环境没装。

spark-submit用法：

```shell
bin/spark-submit \
--class <main-class>    # 你的应用的启动类 (如 org.apache.spark.examples.SparkPi)
--master <master-url> \     # --master 指定Master的地址，默认为Local
--deploy-mode <deploy-mode> \   # 是否发布你的驱动到worker节点(cluster) 或者作为一个本地客户端 (client) (default: client)*
--conf <key>=<value> \  # 任意的Spark配置属性， 格式key=value. 如果值包含空格，可以加引号“key=value” 
... # other options 
<application-jar> \  #  打包好的应用jar,包含依赖. 这个URL在集群中全局可见。 比如hdfs:// 共享存储系统， 如果是 file:// path， 那么所有的节点的path都包含同样的jar
[application-arguments]   # 传给main()方法的参数
--executor-memory 1G  # 指定每个executor可用内存为1G
--total-executor-cores 2 # 指定每个executor使用的cup核数为2个
```

3. spark-shell测试词频统计

```shell
# 创建input目录
[root@4575886edb18 spark-2.1.1-bin-hadoop2.7]# mkdir input
[root@4575886edb18 spark-2.1.1-bin-hadoop2.7]# cd input/
# 创建多个txt文件 里面存放空格隔开的单词 用于等会的词频统计
```

![image-20201207193956469](http://rocks526.top/lzx/image-20201207193956469.png)

```shell
# 进入spark的shell交互环境
bin/spark-shell
```

![image-20201207194144117](http://rocks526.top/lzx/image-20201207194144117.png)

```shell
# 运行WorkCount程序
scala>sc.textFile("input").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
```

![image-20201207194528692](http://rocks526.top/lzx/image-20201207194528692.png)

textFile("input")：读取本地文件input文件夹数据；

flatMap(_.split(" "))：压平操作，按照空格分割符将文件读取的每行数据映射成一个个单词；

map((_,1))：对每一个元素操作，将单词映射为元组；

reduceByKey(\_+\_)：按照key将值进行聚合，相加；

collect：将数据收集到Driver端展示

#### Standalone模式

#### Yarn模式

#### Mesos模式

> Spark客户端直接连接Mesos；不需要额外构建Spark集群。国内应用比较少，更多的是运用yarn调度。

#### 模式对比

| 模式       | Spark安装机器数 | 需启动的进程   | 所属者 |
| ---------- | --------------- | -------------- | ------ |
| Local      | 1               | 无             | Spark  |
| Standalone | 3               | Master及Worker | Spark  |
| Yarn       | 1               | Yarn及HDFS     | Hadoop |

### submit包案例

Spark Shell仅在测试和验证我们的程序时使用的较多，在生产环境中，通常会在IDE中编制程序，然后打成jar包，然后提交到集群，最常用的是创建一个Maven项目，利用Maven来管理jar包的依赖。

- 创建Maven项目并引入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.11</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>
<build>
        <finalName>WordCount</finalName>
        <plugins>
<plugin>
                <groupId>net.alchim31.maven</groupId>
<artifactId>scala-maven-plugin</artifactId>
                <version>3.2.2</version>
                <executions>
                    <execution>
                       <goals>
                          <goal>compile</goal>
                          <goal>testCompile</goal>
                       </goals>
                    </execution>
                 </executions>
            </plugin>
        </plugins>
</build>
```

- 编写WordCount计算代码

```scala
import org.apache.spark.{SparkConf, SparkContext}

object WordCount {
    def main(args: Array[String]): Unit = {
        // 1.创建Job配置信息
        val conf = new SparkConf().setAppName("Spark-WordCount")
        // 2.获取Spark Context
        val sc = new SparkContext(conf)
        // 3.从文件读取数据 创建RDD
        val lines = sc.textFile(args(0))
        // 4.获取统计数据 ==> 保存到文件
        lines.flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_,1)
            .sortBy(_._2, false).saveAsTextFile(args(1))
        // 关闭连接
        sc.stop()
    }
}
```

- 引入打包插件

```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>WordCount</mainClass>
                        </manifest>
                    </archive>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
      </plugin>
```

- jar包提交spark运行

```shell
bin/spark-submit --class WordCount --master local[*] Spark-WordCount.jar input out
```

![image-20201208160142480](http://rocks526.top/lzx/image-20201208160142480.png)

- 本地调试

> 本地Spark程序调试需要使用local提交模式，即将本机当做运行环境，Master和Worker都为本机。运行时直接加断点调试即可。

```scala
        // 本机调试
        val conf = new SparkConf().setMaster("local[*]").setAppName("Spark-WordCount")
```

![image-20201208160905632](http://rocks526.top/lzx/image-20201208160905632.png)

> 出现这个问题的原因，并不是程序的错误，而是用到了hadoop相关的服务，本机没有装Hadoop依赖，安装hadoop依赖然后配置环境变量即可。
>
> Hadoop的安装包在windows下缺少winutils.exe和hadoop.dll ，需要额外下载：https://github.com/steveloughran/winutils
>
> 也可以不用下载hadoop，直接下载这个github项目，将bin的上一级目录配到环境变量里即可。

# <a id="spark-core">Spark Core</a>

### RDD概述

- 什么是RDD

RDD（Resilient Distributed Dataset）叫做分布式数据集，是Spark中最基本的数据抽象。代码中是一个抽象类，它代表一个**不可变**、**可分区**、**里面的元素可并行计算**的集合。

- RDD的属性

1)   一组分区（Partition），即数据集的基本组成单位;

2)   一个计算每个分区的函数;

3)   RDD之间的依赖关系;

4)   一个Partitioner，即RDD的分片函数;

5)   一个列表，存储存取每个Partition的优先位置（preferred location）。

- RDD的特点

RDD表示只读的分区的数据集，对RDD进行改动，只能通过RDD的转换操作，由一个RDD得到一个新的RDD，新的RDD包含了从其他RDD衍生所必需的信息。RDDs之间存在依赖，RDD的执行是按照血缘关系延时计算的。如果血缘关系较长，可以通过持久化RDD来切断血缘关系。

**RDD的分区特点**

RDD逻辑上是分区的，每个分区的数据是抽象存在的，计算的时候会通过一个compute函数得到每个分区的数据。如果RDD是通过已有的文件系统构建，则compute函数是读取指定文件系统中的数据，如果RDD是通过其他RDD转换而来，则compute函数是执行转换逻辑将其他RDD的数据进行转换。

**RDD的只读特点**

RDD是只读的，要想改变RDD中的数据，只能在现有的RDD基础上创建新的RDD。

由一个RDD转换到另一个RDD，可以通过丰富的操作算子实现，不再像MapReduce那样只能写map和reduce。

RDD的操作算子包括两类，一类叫做transformations(转化)，它是用来将RDD进行转化，构建RDD的血缘关系；另一类叫做actions(动作)，它是用来触发RDD的计算，得到RDD的相关计算结果或者将RDD保存的文件系统中。

**RDD的依赖**

RDDs通过操作算子进行转换，转换得到的新RDD包含了从其他RDDs衍生所必需的信息，RDDs之间维护着这种血缘关系，也称之为依赖。如下图所示，依赖包括两种，一种是窄依赖，RDDs之间分区是一一对应的，另一种是宽依赖，下游RDD的每个分区与上游RDD(也称之为父RDD)的每个分区都有关，是多对多的关系。

![image-20201208095716507](http://rocks526.top/lzx/image-20201208095716507.png)

**RDD的缓存**

如果在应用程序中多次使用同一个RDD，可以将该RDD缓存起来，该RDD只有在第一次计算的时候会根据血缘关系得到分区的数据，在后续其他地方用到该RDD的时候，会直接从缓存处取而不用再根据血缘关系计算，这样就加速后期的重用。

**RDD的CheckPoint**

虽然RDD的血缘关系天然地可以实现容错，当RDD的某个分区数据失败或丢失，可以通过血缘关系重建。但是对于长时间迭代型应用来说，随着迭代的进行，RDDs之间的血缘关系会越来越长，一旦在后续迭代过程中出错，则需要通过非常长的血缘关系去重建，势必影响性能。为此，RDD支持checkpoint将数据保存到持久化的存储中，这样就可以切断之前的血缘关系，因为checkpoint后的RDD不需要知道它的父RDDs了，它可以从checkpoint处拿到数据。

### RDD编程模型

在Spark中，RDD被表示为对象，通过对象上的方法调用来对RDD进行转换。经过一系列的transformations定义RDD之后，就可以调用actions触发RDD的计算，action可以是向应用程序返回结果(count, collect等)，或者是向存储系统保存数据(saveAsTextFile等)。在Spark中，只有遇到action，才会执行RDD的计算(即延迟计算)，这样在运行时可以通过管道的方式传输多个转换。

要使用Spark，开发者需要编写一个Driver程序，它被提交到集群以调度运行Worker，如下图所示。Driver中定义了一个或多个RDD，并调用RDD上的action，Worker则执行RDD分区计算任务。

![image-20201208103316084](http://rocks526.top/lzx/image-20201208103316084.png)

### RDD的创建

在Spark中创建RDD的创建方式可以分为三种：从集合中创建RDD；从外部存储创建RDD；从其他RDD转化。

**从集合中创建RDD**

从集合中创建RDD，Spark主要提供了两种函数：parallelize和makeRDD。

```scala
val rdd = sc.parallelize(Array(1,2,3,4,5,6,7,8))
```

![image-20201208103731299](http://rocks526.top/lzx/image-20201208103731299.png)

```scala
val rdd1 = sc.makeRDD(Array(1,2,3,4,5,6,7,8))
```

![image-20201208103800471](http://rocks526.top/lzx/image-20201208103800471.png)

> 两种方式差不多，makeRDD底层调用的也是parallelize，相比parallelize，makeRDD的语义性更强一些。

**从外部存储创建RDD**

包括本地的文件系统，还有所有Hadoop支持的数据集，比如HDFS、Cassandra、HBase等，在后续的数据导入与导出详细介绍。

### RDD的transformations(转化)

RDD整体上分为Value类型和Key-Value类型.

#### Value类型

- map(func)

作用：返回一个新的RDD，该RDD由每一个输入元素经过func函数转换后组成

示例：创建一个1-10数组的RDD，将所有元素*2形成新的RDD

```scala
source.map(_ * 2)
```

![image-20201208104801324](http://rocks526.top/lzx/image-20201208104801324.png)

- mapPartitions(func)

作用：类似于map，但独立的在RDD的每一个分区上运行，因此在类型为T的RDD上运行时，func的函数类型必须是Iterator[T] => Iterator[U]。

> 假设有N个元素，有M个分区，那么map的函数的将被调用N次,而mapPartitions被调用M次,一个函数一次处理所有分区。

示例：创建一个RDD，使每个元素*2组成新的RDD

```scala
# x是分区 通过map遍历分区每个元素
rdd.mapPartitions(x=>x.map(_*2))
```

![image-20201208105149784](http://rocks526.top/lzx/image-20201208105149784.png)

> map()：每次处理一条数据.
>
> mapPartition()：每次处理一个分区的数据，这个分区的数据处理完后，原RDD中分区的数据才能释放，可能导致OOM.
>
> 当内存空间较大的时候建议使用mapPartition()，以提高处理效率。

- mapPartitionsWithIndex(func)

作用：类似于mapPartitions，但func带有一个整数参数表示分区的索引值，因此在类型为T的RDD上运行时，func的函数类型必须是(Int, Interator[T]) => Iterator[U]

示例：创建一个RDD，使每个元素跟所在分区形成一个元组组成一个新的RDD

```scala
rdd.mapPartitionsWithIndex((index,items)=>(items.map((index,_)))).collect()
```

![image-20201208105533614](http://rocks526.top/lzx/image-20201208105533614.png)

- flatMap(func)

作用：类似于map，但是每一个输入元素可以被映射为0或多个输出元素（所以func应该返回一个序列，而不是单一元素）

示例：创建一个元素为1-5的RDD，运用flatMap创建一个新的RDD，新的RDD为原RDD的每个元素的序列

```scala
rdd.flatMap(1 to _).collect()
```

![image-20201208110018750](http://rocks526.top/lzx/image-20201208110018750.png)

- glom()

作用：将每一个分区形成一个数组，形成新的RDD类型时RDD[Array[T]]

案例：创建一个4个分区的RDD，并将每个分区的数据放到一个数组

![image-20201208110504630](http://rocks526.top/lzx/image-20201208110504630.png)

- groupBy(func)

作用：分组，按照传入函数的返回值进行分组。将相同的key对应的值放入一个迭代器。

案例：创建一个RDD，按照元素模以2的值进行分组。

![image-20201208110729172](http://rocks526.top/lzx/image-20201208110729172.png)

- filter(func)

作用：过滤，返回一个新的RDD，该RDD由经过func函数计算后返回值为true的输入元素组成。

案例：创建一个RDD，过滤大于10的元素

![image-20201208110902177](http://rocks526.top/lzx/image-20201208110902177.png)

- sample(withReplacement，fraction，seed)

作用：以指定的随机种子随机抽样出数量为fraction的数据。withReplacement表示是抽出的数据是否放回，true为有放回的抽样，false为无放回的抽样，seed用于指定随机数生成器种子。

案例：创建一个RDD（1-10），从中选择放回和不放回抽样

> 适用于部分数据量特别大，可以通过采样部分数据统计分布情况
>
> fraction和seed都和底层的随机算法有关系

![image-20201208112804968](http://rocks526.top/lzx/image-20201208112804968.png)

- distinct([num Tasks])

作用：对源RDD进行去重后返回一个新的RDD。默认情况下，只有8个并行任务来操作，但是可以传入一个可选的numTasks参数改变它。

案例：创建一个RDD，使用distinct()对其去重。

![image-20201208113447763](http://rocks526.top/lzx/image-20201208113447763.png)

- coalesce(numPartitions)

作用：缩减分区数，用于大数据集过滤后，提高小数据集的执行效率。

 案例：创建一个8个分区的RDD，对其缩减分区

![image-20201208114249114](http://rocks526.top/lzx/image-20201208114249114.png)

- repartition(numPartitions)

作用：根据分区数，重新计算元素所在分区

案例：创建一个8个分区的RDD，对其重新分区

![image-20201208115614320](http://rocks526.top/lzx/image-20201208115614320.png)

> repartition和coalesce的区别如下所示：
>
> coalesce会将分区合并，元素一块跟随原分区合并
>
> repartition会对分区元素进行重新计算所在分区
>
> coalesce重新分区，可以选择是否进行shuffle过程。由参数shuffle: Boolean = false/true决定。repartition实际上是调用的coalesce，默认是进行shuffle的。

![image-20201208115722411](http://rocks526.top/lzx/image-20201208115722411.png)

- sortBy(func,[ascending], [numTasks]) 

作用：使用func先对数据进行处理，按照处理后的数据比较结果排序，默认为正序。

> ascending是控制升序或降序，numTasks是计算的任务数

案例：创建一个RDD，按照不同的规则进行排序

![image-20201208120143031](http://rocks526.top/lzx/image-20201208120143031.png)

- pipe(command, [envVars])

作用：管道，针对每个分区，都执行一个shell脚本，返回输出的RDD。

> 注意：脚本需要放在Worker节点可以访问到的位置

#### 双Value类型

- union(otherDataset)

作用：对源RDD和参数RDD求并集后返回一个新的RDD

> 不会自动去重

案例：创建两个RDD，求并集

![image-20201208140712088](http://rocks526.top/lzx/image-20201208140712088.png)

- subtract (otherDataset)

作用：计算差的一种函数，去除两个RDD中相同的元素，不同的RDD将保留下来

案例：创建两个RDD，求第一个RDD与第二个RDD的差集

![image-20201208140851435](http://rocks526.top/lzx/image-20201208140851435.png)

- intersection(otherDataset)

作用：对源RDD和参数RDD求交集后返回一个新的RDD

案例：创建两个RDD，求两个RDD的交集

![image-20201208140957821](http://rocks526.top/lzx/image-20201208140957821.png)

- cartesian(otherDataset)

作用：笛卡尔积（尽量避免使用）

案例：创建两个RDD，计算两个RDD的笛卡尔积

![image-20201208141058229](http://rocks526.top/lzx/image-20201208141058229.png)

- zip(otherDataset)

作用：将两个RDD组合成Key/Value形式的RDD,这里默认两个RDD的partition数量以及元素数量都相同，否则会抛出异常。

案例：创建两个RDD，并将两个RDD组合到一起形成一个(k,v)RDD

![image-20201208141246920](http://rocks526.top/lzx/image-20201208141246920.png)

#### KV类型

- partitionBy(partition)

作用：对RDD进行分区操作，根据某种分区算法进行分区，如果前后分区个数不一样，会触发shuffle过程。

案例：创建一个4个分区的RDD，对其重新分区

![image-20201208142247086](http://rocks526.top/lzx/image-20201208142247086.png)

- groupByKey()

作用：groupByKey也是对每个key进行操作，但只生成一个sequence。

案例：创建一个RDD，将相同key对应值聚合到一个sequence中，并计算相同key对应值的相加结果。

![image-20201208142742677](http://rocks526.top/lzx/image-20201208142742677.png)

- reduceByKey(func, [numTasks]) 

作用：在一个(K,V)的RDD上调用，返回一个(K,V)的RDD，使用指定的reduce函数，将相同key的值聚合到一起，reduce任务的个数可以通过第二个可选的参数来设置。

案例：创建一个pairRDD，计算相同key对应值的相加结果

![image-20201208143029552](http://rocks526.top/lzx/image-20201208143029552.png)

> reduceByKey和groupByKey区别：
>
> reduceByKey：按照key进行聚合，在shuffle之前有combine（预聚合）操作，返回结果是RDD[k,v].
>
> groupByKey：按照key进行分组，直接进行shuffle。

- aggregateByKey

参数：(zeroValue:U,[partitioner: Partitioner]) (seqOp: (U, V) => U,combOp: (U, U) => U)

（1）zeroValue：给每一个分区中的每一个key一个初始值；

（2）seqOp：函数用于在每一个分区中用初始值逐步迭代value；

（3）combOp：函数用于合并每个分区中的结果。

作用：适合用于分区内和分区间不同计算方式的场景。首先计算每个分区根据key分组，value给初始值(zeroValue)，之后根据seqOp函数计算分区内的序列，输出kv结果，之后根据combOp函数计算分区间的kv，输出最终结果。

案例：创建一个pairRDD，取出每个分区相同key对应值的最大值，然后相加

![image-20201208150059569](http://rocks526.top/lzx/image-20201208150059569.png)

![image-20201208150530967](http://rocks526.top/lzx/image-20201208150530967.png)

- foldByKey

参数：(zeroValue: V)(func: (V, V) => V): RDD[(K, V)]

作用：aggregateByKey的简化操作，seqop和combop使用相同函数

案例：创建一个pairRDD，计算相同key对应值的相加结果

![image-20201208150823973](http://rocks526.top/lzx/image-20201208150823973.png)

-  combineByKey[C]

参数：(createCombiner: V => C, mergeValue: (C, V) => C, mergeCombiners: (C, C) => C)

（1）createCombiner: combineByKey() 会遍历分区中的所有元素，因此每个元素的键要么还没有遇到过，要么就和之前的某个元素的键相同。如果这是一个新的元素,combineByKey()会使用一个叫作createCombiner()的函数来创建那个键对应的累加器的初始值

（2）mergeValue: 如果这是一个在处理当前分区之前已经遇到的键，它会使用mergeValue()方法将该键的累加器对应的当前值与这个新的值进行合并

（3）mergeCombiners: 由于每个分区都是独立处理的， 因此对于同一个键可以有多个累加器。如果有两个或者更多的分区都有对应同一个键的累加器， 就需要使用用户提供的 mergeCombiners() 方法将各个分区的结果进行合并。

作用：对相同K，把V合并成一个集合。

案例：创建一个pairRDD，根据key计算每种key的均值。（先计算每个key出现的次数以及可以对应值的总和，再相除得到结果）

![image-20201208151340712](http://rocks526.top/lzx/image-20201208151340712.png)

![image-20201208152100320](http://rocks526.top/lzx/image-20201208152100320.png)

![image-20201208152315885](http://rocks526.top/lzx/image-20201208152315885.png)

- sortByKey([ascending], [numTasks]) 

作用：在一个(K,V)的RDD上调用，K必须实现Ordered接口，返回一个按照key进行排序的(K,V)的RDD

案例：创建一个pairRDD，按照key的正序和倒序进行排序

![image-20201208153318063](http://rocks526.top/lzx/image-20201208153318063.png)

- mapValues(func)

作用：针对于(K,V)形式的类型只对V进行操作。

案例：创建一个pairRDD，并将value添加字符串"|||"

![image-20201208153443954](http://rocks526.top/lzx/image-20201208153443954.png)

- join(otherDataset, [numTasks]) 

作用：在类型为(K,V)和(K,W)的RDD上调用，返回一个相同key对应的所有元素对在一起的(K,(V,W))的RDD

案例：创建两个pairRDD，并将key相同的数据聚合到一个元组。

![image-20201208153615319](http://rocks526.top/lzx/image-20201208153615319.png)

- cogroup(otherDataset, [numTasks]) 

作用：在类型为(K,V)和(K,W)的RDD上调用，返回一个(K,(Iterable、<V、>,Iterable\<W\>))类型的RDD

案例：创建两个pairRDD，并将key相同的数据聚合到一个迭代器。

![image-20201208153756550](http://rocks526.top/lzx/image-20201208153756550.png)

### RDD的actions(动作)











# <a id="spark-sql">Spark SQL</a>









# <a id="spark-streaming">Spark Streaming</a>





