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

-  reduce(func)

作用：通过func函数聚集RDD中的所有元素，先聚合分区内数据，再聚合分区间数据。

案例：创建一个RDD，将所有元素聚合得到结果。

![image-20201208194526381](http://rocks526.top/lzx/image-20201208194526381.png)

- collect()

作用：在驱动程序中，以数组的形式返回数据集的所有元素。

案例：创建一个RDD，并将RDD内容收集到Driver端打印

![image-20201208194607729](http://rocks526.top/lzx/image-20201208194607729.png)

- count()

作用：返回RDD中元素的个数

案例：创建一个RDD，统计该RDD的条数

![image-20201208194705130](http://rocks526.top/lzx/image-20201208194705130.png)

- first()

作用：返回RDD中的第一个元素

案例：创建一个RDD，返回该RDD中的第一个元素

![image-20201208194747278](http://rocks526.top/lzx/image-20201208194747278.png)

- take(n)

作用：返回一个由RDD的前n个元素组成的数组

案例：创建一个RDD，统计该RDD的条数

![image-20201208194834934](http://rocks526.top/lzx/image-20201208194834934.png)

- takeOrdered(n)

作用：返回该RDD排序后的前n个元素组成的数组

案例：创建一个RDD，统计该RDD的条数

![image-20201208195454257](http://rocks526.top/lzx/image-20201208195454257.png)

- aggregate

参数：(zeroValue: U)(seqOp: (U, T) ⇒ U, combOp: (U, U) ⇒ U)

作用：aggregate函数将每个分区里面的元素通过seqOp和初始值进行聚合，然后用combine函数将每个分区的结果和初始值(zeroValue)进行combine操作。这个函数最终返回的类型不需要和RDD中元素类型一致。

案例：创建一个RDD，将所有元素相加得到结果

![image-20201208195749638](http://rocks526.top/lzx/image-20201208195749638.png)

- fold(num)(func)

作用：折叠操作，aggregate的简化操作，seqop和combop一样。

案例：创建一个RDD，将所有元素相加得到结果

![image-20201208200004870](http://rocks526.top/lzx/image-20201208200004870.png)

- saveAsTextFile(path)

作用：将数据集的元素以textfile的形式保存到HDFS文件系统或者其他支持的文件系统，对于每个元素，Spark将会调用toString方法，将它装换为文件中的文本。

![image-20201208201628391](http://rocks526.top/lzx/image-20201208201628391.png)

- saveAsSequenceFile(path) 

作用：将数据集中的元素以Hadoop sequencefile的格式保存到指定的目录下，可以使HDFS或者其他Hadoop支持的文件系统。

- saveAsObjectFile(path) 

作用：用于将RDD中的元素序列化成对象，存储到文件中。

> 使用Java自带的序列化机制。

- countByKey()

作用：针对(K,V)类型的RDD，返回一个(K,Int)的map，表示每一个key对应的元素个数

案例：创建一个PairRDD，统计每种key的个数

![image-20201208202345879](http://rocks526.top/lzx/image-20201208202345879.png)

- foreach(func)

作用：在数据集的每一个元素上，运行函数func.

案例：创建一个RDD，对每个元素进行打印

![image-20201208202743955](http://rocks526.top/lzx/image-20201208202743955.png)

### RDD中的函数传递

在实际开发中我们往往需要自己定义一些对于RDD的操作，那么此时需要注意的是，初始化工作是在Driver端进行的，而实际运行程序是在Executor端进行的，这就涉及到了跨进程通信，是需要进行RDD序列化。

对于方法的传递，需要将class继承Serializable，对于类属性的传递，需要类继承Serializable，方法里将类属性赋值给临时变量进行操作。

```scala
import org.apache.spark.rdd.RDD

// 自定义RDD处理方法
// 继承Serializable 实现序列化
class Search(query: String) extends Serializable {
    
    //过滤出包含字符串的数据
    def isMatch(s: String): Boolean = {
        s.contains(query)
    }
    
    //过滤出包含字符串的RDD
    def getMatch1 (rdd: RDD[String]): RDD[String] = {
        rdd.filter(isMatch)
    }
    
    //过滤出包含字符串的RDD
    def getMatch2(rdd: RDD[String]): RDD[String] = {
        // 类的属性需要通过赋值给临时变量完成传递
        val query_word = this.query
        rdd.filter(x => x.contains(query_word))
    }
    
}
```

```scala
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object Demo1 {
    def main(args: Array[String]): Unit = {
        
        //1.初始化配置信息及SparkContext
        val sparkConf: SparkConf = new SparkConf().setAppName("WordCount").setMaster("local[*]")
        val sc = new SparkContext(sparkConf)
        
        //2.创建一个RDD
        val rdd: RDD[String] = sc.parallelize(Array("hadoop", "spark", "hive", "hbase"))
        
        //3.创建一个Search对象
        val search = new Search("h")
        
        //4.运用第一个过滤函数并打印结果
        val match1: RDD[String] = search.getMatch1(rdd)
        match1.collect().foreach(println)
    
        //5.运用第一个过滤函数并打印结果
        val match2: RDD[String] = search.getMatch2(rdd)
        match2.collect().foreach(println)
    }
    
}
```

### RDD依赖关系

RDD只支持粗粒度转换，即在大量记录上执行的单个操作。将创建RDD的一系列Lineage（血统）记录下来，以便恢复丢失的分区。RDD的Lineage会记录RDD的元数据信息和转换行为，当该RDD的部分分区数据丢失时，它可以根据这些信息来重新运算和恢复丢失的数据分区。

![image-20201209094130763](http://rocks526.top/lzx/image-20201209094130763.png)

> 通过toDebugString和dependencies可以查看RDD的依赖关系

**RDD的依赖关系分为两种：窄依赖和宽依赖。**

窄依赖是指每一个父RDD的Partition最多被子RDD的一个Partition使用。

![image-20201209094313826](http://rocks526.top/lzx/image-20201209094313826.png)

宽依赖指的是多个子RDD的Partition会依赖同一个父RDD的Partition，会引起shuffle。

![image-20201209094326875](http://rocks526.top/lzx/image-20201209094326875.png)

> 宽依赖并不是特指，Spark源码没有宽依赖这个类，只有窄依赖的类，窄依赖之外的全部是宽依赖。
>
> 之所以分窄依赖和宽依赖，主要是因为宽依赖的一个分区会根据某种条件分为多个子分区，而这种操作必须是分区已知才能执行，因此窄依赖之间没有依赖关系，可以并行处理，宽依赖必须之前的RDD已知，需要串行处理，因此一个宽依赖也对应一个Stage。

- DAG

DAG(Directed Acyclic Graph)叫做有向无环图，原始的RDD通过一系列的转换就就形成了DAG，根据RDD之间的依赖关系的不同将DAG划分成不同的Stage，对于窄依赖，partition的转换处理在Stage中完成计算。对于宽依赖，由于有Shuffle的存在，只能在parent RDD处理完成后，才能开始接下来的计算，因此**宽依赖是划分Stage的依据**。

![image-20201209094657015](http://rocks526.top/lzx/image-20201209094657015.png)

> Stage的划分是从后往前推的，遇到宽依赖/Shuffle进行Stage的隔断，因此Stage的数量等于宽依赖/Shuffle数量+1.

- Spark的任务划分

RDD任务的划分为：Application、Job、Stage和Task。

1）Application：初始化一个SparkContext即生成一个Application，即我们提交的jar里面的主类

2）Job：一个Action算子就会生成一个Job

3）Stage：根据RDD之间的依赖关系的不同将Job划分成不同的Stage，遇到一个宽依赖则划分一个Stage。

4）Task：Stage是一个TaskSet，将Stage划分的结果发送到不同的Executor执行即为一个Task。

> 为了并行度更高，一个分区会产生一个Task，但是没有依赖关系的分区才会并行执行，因此Task数量等于Stage最后一步的RDD的分区数。
>
> Application->Job->Stage-> Task每一层都是1对n的关系。
>
> 在Spark的UI，4040端口可以看到任务的划分/执行情况和DAG。

### RDD缓存

RDD通过persist方法或cache方法可以将前面的计算结果缓存，默认情况下 persist() 会把数据以序列化的形式缓存在 JVM 的堆空间中。 

但是并不是这两个方法被调用时立即缓存，而是触发后面的action时，该RDD将会被缓存在计算节点的内存中，并供后面重用。

> cache最终也是调用了persist方法，默认的存储级别都是仅在内存存储一份，Spark的存储级别还有好多种，存储级别在StorageLevel类中定义的。

![image-20201209095330873](http://rocks526.top/lzx/image-20201209095330873.png)

![image-20201209095344029](http://rocks526.top/lzx/image-20201209095344029.png)

> 在存储级别的末尾加上“_2”都是把持久化数据存为两份。
>
> OFF_HEAP是堆外内存。

缓存有可能丢失，或者存储存储于内存的数据由于内存不足而被删除，RDD的缓存容错机制保证了即使缓存丢失也能保证计算的正确执行。通过基于RDD的一系列转换，丢失的数据会被重算，由于RDD的各个Partition是相对独立的，因此只需要计算丢失的部分即可，并不需要重算全部Partition。

![image-20201209102956912](http://rocks526.top/lzx/image-20201209102956912.png)

- 总结

RDD的缓存机制主要是为了优化热点RDD的频繁计算问题，通过缓存机制可以让热点RDD只需要一次计算，后续使用缓存内容。

由于缓存可能不可靠，存在丢失问题，因此RDD缓存后血缘关系也不会删除，当缓存丢失时，会自动计算还原RDD。

### RDD的CheckPoint

Spark中对于数据的保存除了持久化操作之外，还提供了一种检查点的机制，检查点（本质是通过将RDD写入Disk做检查点）是为了通过lineage做容错的辅助，lineage过长会造成容错成本过高，这样就不如在中间阶段做检查点容错，如果之后有节点出现问题而丢失分区，从做检查点的RDD开始重做Lineage，就会减少开销。检查点通过将数据写入到HDFS文件系统实现了RDD的检查点功能。

为当前RDD设置检查点。该函数将会创建一个二进制的文件，并存储到checkpoint目录中，该目录是用SparkContext.setCheckpointDir()设置的。在checkpoint的过程中，该RDD的所有依赖于父RDD中的信息将全部被移除。对RDD进行checkpoint操作并不会马上被执行，必须执行Action操作才能触发。

![image-20201209103555660](http://rocks526.top/lzx/image-20201209103555660.png)

- 总结

检查点机制主要是为了解决血缘关系太长，一步运算出错需要全部重算的问题。通过检查点可以将血缘斩断，之后计算复用检查点的内容。

检查点除了存储hdfs之外，还可以存储文件系统，推荐hdfs，因为检查点会将血缘关系斩断，需要确保检查点存储可靠，而hdfs有备份系统。

### 键值对RDD的数据分区器

Spark目前支持Hash分区和Range分区，用户也可以自定义分区。

Hash分区为当前的默认分区，Spark中分区器直接决定了RDD中分区的个数、RDD中每条数据经过Shuffle过程属于哪个分区和Reduce的个数。

> 只有Key-Value类型的RDD才有分区器的，非Key-Value类型的RDD分区器的值是None
> 每个RDD的分区ID范围：0~numPartitions-1，决定这个值是属于那个分区的。

#### 获取RDD分区

可以通过使用RDD的partitioner 属性来获取 RDD 的分区方式。它会返回一个 scala.Option 对象， 通过get方法获取其中的值。

![image-20201208183358286](http://rocks526.top/lzx/image-20201208183358286.png)

#### Hash分区器

HashPartitioner分区的原理：对于给定的key，计算其hashCode，并除以分区的个数取余，如果余数小于0，则用余数+分区的个数（否则加0），最后返回的值就是这个key所属的分区ID。

![image-20201208183908241](http://rocks526.top/lzx/image-20201208183908241.png)

#### Range分区器

HashPartitioner分区弊端：可能导致每个分区中数据量的不均匀，极端情况下会导致某些分区拥有RDD的全部数据。

RangePartitioner作用：将一定范围内的数映射到某一个分区内，尽量保证每个分区中数据量的均匀，而且分区与分区之间是有序的，一个分区中的元素肯定都是比另一个分区内的元素小或者大，但是分区内的元素是不能保证顺序的。简单的说就是将一定范围内的数映射到某一个分区内。

实现过程为：

第一步：先从整个RDD中抽取出样本数据，将样本数据排序，计算出每个分区的最大key值，形成一个Array[KEY]类型的数组变量rangeBounds；

第二步：判断key在rangeBounds中所处的范围，给出该key值在下一个RDD中的分区id下标；该分区器要求RDD中的KEY类型必须是可以排序的

#### 自定义分区

要实现自定义的分区器，你需要继承 org.apache.spark.Partitioner 类并实现下面三个方法：

- numPartitions: Int:返回创建出来的分区数。

- getPartition(key: Any): Int:返回给定键的分区编号(0到numPartitions-1)。 

- equals():Java 判断相等性的标准方法。这个方法的实现非常重要，Spark 需要用这个方法来检查你的分区器对象是否和其他分区器实例相同，这样 Spark 才可以判断两个 RDD 的分区方式是否相同。

案例：将相同后缀的数据写入相同的文件，通过将相同后缀的数据分区到相同的分区并保存输出来实现。

![image-20201208184453302](http://rocks526.top/lzx/image-20201208184453302.png)

> 使用自定义的 Partitioner 是很容易的:只要把它传给 partitionBy() 方法即可。
>
> Spark 中有许多依赖于数据混洗的方法，比如 join() 和 groupByKey()，它们也可以接收一个可选的 Partitioner 对象来控制输出数据的分区方式。

### 数据的读取和保存









### 累加器









# <a id="spark-sql">Spark SQL</a>









# <a id="spark-streaming">Spark Streaming</a>

### Spark Streaming概述

Spark Streaming 是 Spark Core API 的扩展，它支持弹性的，高吞吐的，容错的实时数据流的处理。

Spark Streaming支持的数据输入源很多，例如：Kafka、Flume、Twitter、ZeroMQ和简单的TCP套接字等等。

数据输入后可以用Spark的高度抽象原语如：map、reduce、join、window等进行运算。

而结果也能保存在很多地方，如HDFS，数据库等。

![image-20201209104209093](http://rocks526.top/lzx/image-20201209104209093.png)

在内部，它工作原理如上，Spark Streaming 接收实时输入数据流并将数据切分成多个 batch（批）数据，然后由 Spark 引擎处理它们以生成最终的 stream of results in batches（分批流结果）。

![image-20201209104242109](http://rocks526.top/lzx/image-20201209104242109.png)

Spark Streaming 提供了一个名为 *discretized stream* 或 *DStream* 的高级抽象，它代表一个连续的数据流。DStream 可以从数据源的输入数据流创建，例如 Kafka，Flume 以及 Kinesis，或者在其他 DStream 上进行高层次的操作以创建。在内部，一个 DStream 是通过一系列的 RDDs 来表示。

### Word Count入门案例

- 引入Maven依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>2.1.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming_2.11</artifactId>
            <version>2.1.1</version>
        </dependency>
    </dependencies>
```

- 编写代码

```scala
import org.apache.spark.SparkConf
import org.apache.spark.streaming.{Seconds, StreamingContext}

// WordCount案例
object Demo1 {

    def main(args: Array[String]): Unit = {
        // 设置app配置
        val conf = new SparkConf().setMaster("local[*]").setAppName("SparkStreaming-WordCount")
        // 初始化StreamingContext 设置时间间隔20s
        val sc = new StreamingContext(conf, Seconds(20))
        // 通过读取Socket创建DStream 读取到的数据是行
        val lines = sc.socketTextStream("localhost", 9999)
        // 行切分单词 转换元组
        val words = lines.flatMap(_.split(" ")).map((_,1))
        // 单词统计
        val res = words.reduceByKey(_+_)
        // 打印结果
        res.print()
    
        // 启动SparkStreaming
        sc.start()
        sc.awaitTermination()
    }

}
```

- 往9999端口发送数据

> 在Linux下，可以通过netcat工具向9999端口不断的发送数据，由于我的Spark跑在docker内，端口不同，因此写一个ServerSocket接收9999连接，读取用户输入数据发送给spark。

```java
import java.io.*;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.net.Socket;

// Socket Server
public class SocketServer {

    public static void main(String[] args) throws IOException {

        ServerSocket serverSocket = new ServerSocket();
        serverSocket.bind(new InetSocketAddress("localhost", 9999));
        System.out.println("Server start!");
        Socket client = serverSocket.accept();
        System.out.println("Client Connected!");
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(client.getOutputStream()));
        while (true){
            String line = bufferedReader.readLine();
            // 注意加上\n 客户端是readLine 按行读取 如果没有\n 会一直阻塞
            bufferedWriter.write(line + "\n");
            bufferedWriter.flush();
        }
    }

}
```

- 统计示例

![image-20201209114035816](http://rocks526.top/lzx/image-20201209114035816.png)

> 通过拷贝spark的conf目录下的log4j文件到idea目录下，修改日志级别可以减少多余信息输入。

### Dstream的创建

Spark Streaming原生支持一些不同的数据源。一些“核心”数据源已经被打包到Spark Streaming 的 Maven 依赖中，而其他的一些则可以通过 spark-streaming-kafka 等附加工件获取。

每个接收器都以 Spark 执行器程序中一个长期运行的任务的形式运行，因此会占据分配给应用的 CPU 核心。此外，我们还需要有可用的 CPU 核心来处理数据。这意味着如果要运行多个接收器，就必须至少有和接收器数目相同的核心数，还要加上用来完成计算所需要的核心数。例如，如果我们想要在流计算应用中运行 10 个接收器，那么至少需要为应用分配 11 个 CPU 核心。所以如果在本地模式运行，不能使用local[1]。

#### 文件数据源





#### RDD队列





#### 自定义数据源





#### Kafka数据源









### Dstream的转换





### Dstream的输出



