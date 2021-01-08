# Jdk1.0

> 版本代号为 Oak(橡树)， 1996-01-23 发布

- 【新增】JVM；
- 【新增】Applet: 已被淘汰；
- 【新增】AWT(Abstract Window ToolKit): 已被淘汰；
- 【新增】File 和 IO(InputStream/OutputStream) 相关操作 API；

# Jdk1.1

> 无版本代号, 1997-02-19 发布, major.minor 版本为 45

- 【新增】JDBC(Java Database Connectivity)， 注意不是 Connection；
- 【新增】内部类；
- 【新增】Java Bean；
- 【新增】RMI(Remote Method Invocation)；
- 【新增】反射(仅用于内省)；
- 【提升】IO 引入 Reader/Writer 及其子类；

# Jdk1.2

> 版本代号为 Playground(操场)， 1998-12-08 发布, major.minor 版本为 46

- 【新增】集合框架；
- 【新增】对字符串常量做内存映射；
- 【新增】JIT(Just In Time) 编译器；
- 【新增】对打包的 Java 文件进行数字签名；
- 【新增】控制授权访问系统资源的策略工具；
- 【新增】JFC(Java Foundation Classes)， 包括 Swing 1.0， 拖放和 Java2D 类库，很少使用， 主要的使用场景是用在后端生成图片的业务场景， 如二维码生成；
- 【新增】Java 插件；
- 【新增】strictfp 关键字；
- 【提升】在 JDBC 中引入可滚动结果集， BLOB， CLOB， 批量更新和用户自定义类型；
- 【提升】在 Applet 中添加声音支持， 已被淘汰；

# Jdk1.3

> 版本代号为 Kestrel(红隼)， 2000-05-08 发布, major.minor 版本为 47

- 【新增】Java Sound API， 已被淘汰；
- 【新增】jar文件索引；
- 【新增】JVM 配备 HotSpot JVM；
- 【新增】代理类；
- 【新增】Java 命名与目录接口；
- 【新增】Java 平台调试体系；

# Jdk1.4

> 版本代号为 Merlin(隼)， 2004-02-06 发布(首次在 JCP 下发行), major.minor 版本为 48

- 【新增】XML处理;
- 【新增】Java打印服务;
- 【新增】Logging API;
- 【新增】Java Web Start;
- 【新增】断言;
- 【新增】引入Preferences API;
- 【新增】链式异常处理;
- 【新增】支持IPV6;
- 【新增】正则表达式;
- 【新增】Image I/O API；
- 【新增】NIO API；
- 【新增】集成 JCE、JSSE、JAAS；
- 【提升】引入JDBC 3.0 API;

# Jdk5

> 版本代号为 Tiger(老虎)， 2004-09-30 发布, major.minor 版本为 49。从 JDK5 开始， JDK 的版本不再以 1.x 的方式来命名了， 而是直接用 x 来命名。

- 【新增】泛型
- 【新增】增强 for 循环， 可以使用迭代方式；
- 【新增】自动装箱与自动拆箱；
- 【新增】类型安全的枚举；
- 【新增】支持可变参数；
- 【新增】静态导入；
- 【新增】注解： 动态注解、元数据；
- 【新增】Instrumentation；
- 【新增】内省(Introspector)
- 【新增】JUC 包
- 【新增】Scanner 类

# Jdk6

> 版本代号为 Mustang(野马)， 2006-12-11 发布, major.minor 版本为 50

- 【新增】Web 服务元数据；
- 【新增】脚本语言支持；
- 【新增】JTable 的排序和过滤；
- 【新增】轻量级的 Http Server；
- 【新增】插入式注解处理 API(Pluggable Annotation Processing API)；
- 【新增】支持嵌入式数据库 Derby；
- 【新增】Console API；
- 【新增】Compile API；
- 【新增】StAX(Streaming API for XML) 处理 XML；
- 【提升】引入 JAXB2 来处理对象和 XML 之间的映射；
- 【提升】AWT 中新增了两个类 Desktop 和 SystemTray， 极不常用；
- 【提升】Common Annotations；
- 【提升】JAX-WS2.0；
- 【提升】JDBC4.0；
- 【提升】引入新的 GC 算法；

# Jdk7

> 版本代号为 Dolphin(海豚)， 2011-07-28 发布, major.minor 版本为 51

- 【新增】Fork-Join并行计算框架
- 【新增】数值可加下划线（eg:int one_million=123_1）
- 【提升】创建泛型对象时应用类型推导
- 【提升】自动资源管理（try-with-resources）；
- 【提升】异常捕获的处理方式（通过 | 捕获多个异常），
- 【提升】 Java NIO2 API(working with path 和 file change notification)；
- 【提升】switch 的分支条件支持字符串；
- 【提升】集合中新增 TransferQueue 接口， 是 BlockingQueue 的改进版，实现类为 LinkedTransferQueue；
- 【提升】JDBC4.1: try-with-resources 和 RowSet1.1
- 【提升】网络、Swing、XML 处理、国际化等 API 的提升
- 【提升】JVM方面， 支持非 Java 语言， Garbage-First-Collector 和提升了 Java HotSpot 虚拟机的性能；

# Jdk8

> 版本代号为 Spider(蜘蛛)， 2014-03-18 发布, major.minor 版本为 52

- 【新增】Lambda 表达式；
- 【新增】Stream函数式操作流元素集合；
- 【新增】新的日期和时间 API；
- 【新增】函数式接口；
- 【新增】接口的默认方法，又称为扩展方法；
- 【新增】方法与构造函数的引用；
- 【新增】Optional API；
- 【新增】并行操作；
- 【新增】新工具，如 Nashorn 引擎 jjs、类依赖分析器 jdeps；
- 【提升】支持多重注解，并新增了部分注解；
- 【提升】JVM的permGen空间移除，被Metaspace元空间取代

# Jdk9

> 无版本代号， 2017-09-21 发布。

- 【新增】模块化，进而使得 JDK 目录结构发生变化；
- 【新增】交互式编程环境 REPL(JShell)；
- 【新增】轻量级 JSON API；
- 【新增】响应式流 (Reactive Streams) API
- 【新增】HTTP 2.0 客户端；
- 【新增】多版本兼容 jar 包；
- 【新增】货币相关的 API； //
- 【新增】代码分段缓存； //
- 【新增】智能 Java 编译，第二阶段； //
- 【提升】集合： 提供集合工厂方法；
- 【提升】接口： 私有接口方法；
- 【提升】String： 底层存储结构更换；
- 【提升】锁争用机制；
- 【提升】简化进程 API；
- 【提升】Javadoc 的提升

# Jdk10

> 无版本代号， 2018-03-20 发布。

- 【新增】局部变量类型推断；
- 【新增】统一的垃圾回收接口；
- 【新增】并行Full GC的垃圾回收器 G1；
- 【新增】应用程序类数据共享；
- 【新增】线程-局部管控；
- 【新增】基于 Java 的 实验性 JIT 编译器
- 【提升】基于时间的版本发布模式
- 【提升】备用存储装置上的堆分配
- 【提升】根证书认证
- 【提升】额外的 Unicode 语言标签扩展
- 【提升】整合 JDK 代码仓库；
- 【删除】移除 Native-Header 自动生成工具；

# Jdk11

> 无版本代号， 2018-09-25 发布。

- 【新增】可伸缩低延迟垃圾收集器(ZGC， 实验)；
- 【新增】基于嵌套的访问控制；
- 【新增】低开销垃圾回收器(Epsilon)；
- 【新增】低开销的 Heap Profiling；
- 【提升】标准 HTTP Client 升级；
- 【提升】启动单个源代码文件的方法；
- 【提升】Lambda 参数的局部变量语法；
- 【提升】支持 TLS 1.3 协议；
- 【提升】飞行记录器，之前只有商业版中提供；
- 【提升】动态类文件常量；
- 【提升】新增加密算法(ChaCha20 和 Poly1305)；
- 【废弃】废弃 Nashorn JavaScript 引擎、 Pack200 工具类和 API
- 【移除】移除 Java EE 和 CORBA 模块；

# Jdk12

> 无版本代号， 2019-03-19 发布。

- 【新增】低停顿垃圾收集算法(Shenandoah， 实验)；
- 【新增】微基准套件；
- 【新增】JVM 常量 API；
- 【提升】Switch 表达式(预览)；
- 【提升】使用默认类数据共享（CDS）存档；
- 【提升】AArch64 的实现(一个端口)；
- 【提升】G1 优化，终止混合集合，能自动返回堆内存；

# Jdk13

> 无版本代号， 2019-09-17 发布。

- 【提升】动态程序类数据共享；
- 【提升】ZGC： 释放未使用的内存；
- 【提升】Socket API
- 【提升】Switch 表达式(预览)；
- 【提升】文本块(预览)；

# Jdk14

> 无版本代号， 2020-03-17 发布。

- 305: instanceof的模式匹配 (预览)
- 343: 打包工具 (Incubator)
- 345: G1的NUMA内存分配优化
- 349: JFR事件流
- 352: 非原子性的字节缓冲区映射
- 358: 友好的空指针异常
- 359: Records (预览)
- 361: Switch表达式 (标准)
- 362: 弃用Solaris和SPARC端口
- 363: 移除CMS（Concurrent Mark Sweep）垃圾收集器
- 364: macOS系统上的ZGC
- 365: Windows系统上的ZGC
- 366: 弃用ParallelScavenge + SerialOld GC组合
- 367: 移除Pack200 Tools和API
- 368: 文本块 (第二个预览版)
- 370: 外部存储器API (Incubator)



