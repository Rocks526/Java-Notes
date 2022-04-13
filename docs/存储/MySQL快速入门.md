![Mysql知识体系 (1)](http://rocks526.top/lzx/Mysql知识体系 (1).png)

# 一：Mysql介绍

### 1.1 Mysql介绍

MySQL是一款开源的、高性能的`关系型数据库管理系统`，是Web领域最流行的数据库系统。

注：早期版本的Mysql虽然性能不如商业的Oracle、复杂函数触发器等进阶功能也支持的较差，但胜在`开源`、`入门简单`，且`性能对比SqlServer等并不弱`，因此特别适合互联网初期的快速迭代式发展，成为了`当时市场的选择`，伴随着互联网和Web的发展而水涨船高，成为数据库领域的Top之一。

### 1.2 Mysql发展历程

- MySQL的历史可以追溯到1979年，一个名为Monty Widenius的程序员在为TcX的小公司打工，并且用BASIC设计了一个报表工具，使其可以在4MHz主频和16KB内存的计算机上运行。当时，这只是一个很底层的且仅面向报表的存储引擎，名叫Unireg。
- 1990年，TcX公司的客户中开始有人要求为他的API提供SQL支持。Monty直接借助于mSQL的代码，将它集成到自己的存储引擎中。令人失望的是，效果并不太令人满意，因此决心自己重写一个SQL支持。
- `1996年，MySQL 1.0发布`，但只面向一小拨人，相当于内部发布。
- 到了`1996年10月，MySQL 3.11.1发布`(MySQL没有2.x版本)，最开始只提供Solaris下的二进制版本。一个月后，Linux版本出现了，在接下来的两年里，MySQL被移植到各个平台。
- 1999～2000年，MySQL AB公司在瑞典成立。Monty雇了几个人与Sleepycat合作，`开发出了Berkeley DB引擎，由于BDB支持事务处理`，因此MySQL从此开始支持事务处理。
- `2000，MySQL不仅公布自己的源代码`，并采用GPL(GNU General Public License)许可协议，正式进入开源世界。同年4月，MySQL对旧的存储引擎ISAM进行了整理，将其命名为MyISAM。
- `2001年，集成Heikki Tuuri的存储引擎InnoDB`，这个引擎不仅能`支持事务处理，并且支持行级锁`。后来该引擎被证明是最为成功的MySQL事务存储引擎。MySQL与InnoDB的正式结合版本是4.0。
- `2003年12月，MySQL 5.0`版本发布，提供了`视图、存储过程`等功能。
- `2008年1月，MySQL AB公司被Sun公司以10亿美金收购`，MySQL数据库进入Sun时代。在Sun 时代，Sun公司对其进行了大量的推广、优化、Bug修复等工作。
- `2008年11月，MySQL 5.1发布`，它提供了`分区、事件管理，以及基于行的复制和基于磁盘的NDB集群系统`，同时修复了大量的Bug。
- `2009年4月，Oracle公司以74亿美元收购Sun公司`，自此MySQL数据库进入Oracle时代，而其第三方的存储引擎InnoDB早在2005年就被Oracle公司收购。
- `2010年12月，MySQL 5.5发布`，其主要新特性包括`半同步的复制及对SIGNAL/RESIGNAL的异常处理功能的支持`，最重要的是`InnoDB存储引擎终于变为当前MySQL的默认存储引擎`。MySQL 5.5不是时隔两年后的一次简单的版本更新，而是加强了MySQL各个方面在企业级的特性。Oracle公司同时也承诺 MySQL 5.5和未来版本仍是采用GPL授权的开源产品。

### 1.3 Mysql的安装

- 检查是否有mysql的yum源

```shell
yum repolist all | grep mysql
```

- 卸载mysql

```shell
#卸载mysql
yum remove -y mysql mysql-libs mysql-common 
#删除mysql下的数据文件
rm -rf /var/lib/mysql 
#删除mysql配置文件
rm /etc/my.cnf 
#删除组件
yum remove -y mysql-community-release-el6-5.noarch 
```

- 安装mysql

```shell
#执行安装文件
yum install mysql-community-server
```

- 启动mysql

```shell
systemctl start mysqld
```

- 设置密码/修改密码

```shell
/usr/bin/mysqladmin -u root password 'admin'
#没有密码 有原来的密码则加
/usr/bin/mysqladmin -u root -p 'admin123' password 'admin'
```

- 登录mysql

```shell
# -u：指定数据库用户名
# -p：指定数据库密码，记住-u和登录密码之间没有空格
mysql -uroot -proot
```

- 修改配置文件

```shell
vim /etc/my.cnf
################################ 
[mysqld]
# MySQL设置大小写不敏感：默认：区分表名的大小写，不区分列名的大小写
# 0：大小写敏感 1：大小写不敏感
lower_case_table_names=1
# 默认字符集
character-set-server=utf8
```

- mysql远程授权

```mysql
-- 授予root用户对所有数据库对象的全部操作权限：
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
-- 刷新权限
FLUSH PRIVILEGES;
```

### 1.4 Mysql的逻辑架构

和其它数据库相比，Mysql有点与众不同，它的架构可以在多种不同场景中应用并发挥良好作用。主要体现在存储引擎的架构上，`插件式的存储引擎架构将查询处理和其它的系统任务以及数据的存储提取相分离`。这种架构可以根据业务的需求和实际需要选择合适的存储引擎。Mysql的逻辑架构主要分为三层，具体如下所示：

![2019110142053365.png](http://rocks526.top/lzx/2019110142053365.png)

- 连接层

最上层是一些客户端和连接服务，包含本地Socket通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信。主要完成一些类似于`连接处理`、`授权认证`、及相关的`安全方案`。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的`操作权限`。

- 业务层/Server层/核心层

Mysql的核心层，`查询解析、查询优化、缓存、函数、视图`等功能都是此层提供，还定义了`存储引擎层的统一接口`。

| 组件                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| Connection Pool                  | 连接池，用于为接入的客户端连接分配线程                       |
| Management Serveices & Utilities | 系统管理和控制工具，主要用于备份、恢复等系统功能             |
| SQL Interface                    | SQL接口，用于接受用户的SQL命令                               |
| Parser                           | 解析器，用于解析SQL命令，转换成语法树，校验语法结构          |
| Optimizer                        | 查询优化器，用于对不合理的SQL进行优化                        |
| Cache和Buffer                    | 查询缓存，用于缓存之前的查询结果，由一系列小缓存组成，8.0版本之后移除 |

- 引擎层(Pluggable Storage Engines)

存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信。`Mysql的存储引擎是插件化设计，支持插拔`，不同的存储引擎具有的功能不同，这样可以根据自己的实际需要进行选取。

注：存储引擎是以表为单位的，每个表可指定不同的存储引擎。

- 存储层

数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

### 1.5 SQL执行流程

- 简化执行流程

![image-20220405162933043](http://rocks526.top/lzx/image-20220405162933043.png)

- 详细执行流程

<img src="http://rocks526.top/lzx/image-20220405162949977.png" alt="image-20220405162949977" style="zoom:150%;" />

- 总结

1. 客户端建立Socket连接，Mysql连接器接入连接并进行用户认证和校验等，成功后交给连接池处理
2. 客户端发送的SQL交给命令分发器来处理，如果存在缓存，则直接查询缓存并返回
3. 命令分发器根据配置文件选择是否记录审计日志
4. 命令分发器将SQL发给命令解析器，校验SQL语法标准，生成语法树
5. 命令解析器根据SQL类型，将SQL交给不同的组件处理，例如查询SQL，会交给查询优化器处理，对SQL进行优化生成执行计划
6. 优化、处理完后的SQL交给访问控制模块，进行权限的校验
7. 最后调用存储引擎的接口，执行SQL操作，提取返回的数据

### 1.5 Mysql的物理结构

#### 1.5.1 Mysql的物理结构介绍

- MySQL是通过文件系统对数据和索引进行存储的
- MySQL从物理结构上可以分为`日志文件`和`数据索引`文件
- MySQL在Linux中的数据索引文件和日志文件都在`/var/lib/mysql`目录下
- `日志文件采用顺序IO方式存储`、`数据文件采用随机IO方式存储`

注：对于SATA磁盘，顺序IO的性能可达到随机IO的几十倍甚至百倍以上，即便是大幅度提供随机IO性能的SSD磁盘，顺序IO的性能也是随机IO的数倍，但顺序IO只能`追加式写入`，因此一般适合存储日志，关系型数据库一般都采用`WAL(Write Ahead Log)机制`。

#### 1.5.2 日志文件

此处只对Mysql中常见的日志文件进行简单介绍，在介绍InnoDB引擎和Mysql主从复制时再详细介绍相关的日志文件。

- 错误日志（error log）

默认是开启的，且从5.5.7以后无法关闭错误日志，`错误日志记录了运行过程中遇到的所有严重的错误信息，以及MySQL每次启动和关闭的详细信息`。

- 二进制日志（bin log）

二进制日志会记录所有数据的变化，`记录的是会引起数据变化的所有SQL（DDL及DML）语句`，所有语句以事件的形式保存，描述了数据的变更顺序，binlog还包括了每个更新语句的执行时间信息。`如果是DDL语句，则直接记录到binlog日志，而DML语句，必须通过事务提交才能记录到binlog日志中`。

生产中都是开启binlog日志，主要用于`数据备份、恢复、主从、异构数据源更新同步`等。

注：DDL语句直接记录binlog日志，如果后面rollback，从库执行也会rollback，但DML语句无法rollback，因此只有commit才会记录。

- 通用查询日志（general query log）

通用查询日志有时候也称为审计日志，会记录数据库执行的所有SQL，生产环境不建议开启，会对性能产生影响，在某些情况下可用于排查问题。

- 慢查询日志（slow query log）

用于记录执行较慢的SQL，经常用于SQL优化、性能调优。默认是关闭状态，可通过以下配置开启：

```properties
#开启慢查询日志
slow_query_log=ON
#慢查询的阈值，秒
long_query_time=3
#日志记录文件如果没有给出file_name值， 默认为主机名，后缀为-slow.log。如果给出了文件名，但不是绝对路径名，文件则写入数据目录。
slow_query_log_file=file_name
```

- 重做日志（redo log）

InnoDB引擎特有日志，主要用来配合undo log实现事务的持久性、原子性、一致性。

- 回滚日志（undo log）

InnoDB引擎特有日志，主要用来配合redo log实现事务的持久性、原子性、一致性。

- 中继日志（relay log）

主要用于Mysql主从模式，从节点从主节点抓取bin log后进行数据同步，通过relay log记录当前的执行位置。

- 日志配置

<img src="http://rocks526.top/lzx/image-20220405171745244.png" alt="image-20220405171745244" style="zoom:80%;" />

- 查看日志开启情况

```mysql
show variables like 'log_%';
```

#### 1.5.3 数据文件

- 查看数据文件存储目录

```mysql
show variables like '%datadir%';
```

数据文件的存储格式和存储引擎相关，不同存储引擎采用不同的存储格式，对上层提供相关的数据存储和提取的API。

- MyIsam数据文件
  - .frm文件：主要存放与表相关的结构信息，每张表对应一个frm文件
  - .myd文件：主要用来存储表数据信息 ，每张表对应一个myd文件
  - .myi文件：主要用来存储表索引信息，每张表对应一个myi文件

- InnoDB数据文件
  - ibdata文件：主要用来存储表的元信息，所有表共同使用一个或者多个ibdata文件
  - .frm文件：主要用来存储表结构信息，一张表对应一个frm文件
  - .ibd：主要用来存储表数据和索引信息，一张表对应一个ibd文件

注：`MyIsam采用非聚集索引，InnoDB采用聚集索引`，因此MyIsam的表数据和索引存储分离，分别是myd和myi文件，而InnoDB的表数据就存在主键索引上，因此是一个文件，即ibd文件。

### 1.6 Mysql的数据类型

MySQL支持多种类型，大致可以分为三类：`数值`、`日期/时间`和`字符串(字符)`类型。

- 数值类型

MySQL支持所有标准SQL数值数据类型，包括严格数值数据类型(INTEGER、SMALLINT、DECIMAL 和 NUMERIC)，以及近似数值数据类型(FLOAT、REAL 和 DOUBLE PRECISION)。作为SQL标准的扩展，MySQL也支持整数类型TINYINT、MEDIUMINT和BIGINT。

| 类型             | 大小                                     | 范围（有符号）                                               | 范围（无符号）                                               | 用途            |
| :--------------- | :--------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :-------------- |
| `TINYINT`        | 1 Bytes                                  | (-128，127)                                                  | (0，255)                                                     | 小整数值        |
| SMALLINT         | 2 Bytes                                  | (-32 768，32 767)                                            | (0，65 535)                                                  | 大整数值        |
| MEDIUMINT        | 3 Bytes                                  | (-8 388 608，8 388 607)                                      | (0，16 777 215)                                              | 大整数值        |
| `INT或INTEGER`   | 4 Bytes                                  | (-2 147 483 648，2 147 483 647)                              | (0，4 294 967 295)                                           | 大整数值        |
| `BIGINT`         | 8 Bytes                                  | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)      | (0，18 446 744 073 709 551 615)                              | 极大整数值      |
| FLOAT            | 4 Bytes                                  | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  | 单精度 浮点数值 |
| `DOUBLE`         | 8 Bytes                                  | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| `DECIMAL（M,D）` | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值                                               | 依赖于M和D的值                                               | 小数值          |

- 日期和时间类型

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。

| 类型       | 大小 ( bytes) | 范围                                                         | 格式                | 用途                     |
| :--------- | :------------ | :----------------------------------------------------------- | :------------------ | :----------------------- |
| `DATE`     | 3             | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 日期值                   |
| TIME       | 3             | '-838:59:59'/'838:59:59'                                     | HH:MM:SS            | 时间值或持续时间         |
| YEAR       | 1             | 1901/2155                                                    | YYYY                | 年份值                   |
| `DATETIME` | 8             | 1000-01-01 00:00:00/9999-12-31 23:59:59                      | YYYY-MM-DD HH:MM:SS | 混合日期和时间值         |
| TIMESTAMP  | 4             | 1970-01-01 00:00:00/2038结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYYMMDD HHMMSS     | 混合日期和时间值，时间戳 |

- 字符串类型

字符串类型指CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM和SET。

| 类型           | 大小                  | 用途                            |
| :------------- | :-------------------- | :------------------------------ |
| CHAR（M）      | 0-255 bytes           | 定长字符串，M为0-255的数        |
| `VARCHAR（M）` | 0-65535 bytes         | 变长字符串，M为0-65535的数      |
| TINYBLOB       | 0-255 bytes           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT       | 0-255 bytes           | 短文本字符串                    |
| `BLOB`         | 0-65 535 bytes        | `二进制`形式的长文本数据        |
| `TEXT`         | 0-65 535 bytes        | 长文本数据                      |
| MEDIUMBLOB     | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT     | 0-16 777 215 bytes    | 中等长度文本数据                |
| LONGBLOB       | 0-4 294 967 295 bytes | 二进制形式的极大文本数据        |
| LONGTEXT       | 0-4 294 967 295 bytes | 极大文本数据                    |

注：在使用varchar时，根据适用场景选择合适的长度还是很重要的，虽然`空间占用之和存储的数据大小有关`，但`长度越长，排序时会消耗更多的内存`，这是因为 ORDER BY  采用 fixed_length 计算列长度(memory引擎也一样)。

注：在开发中，金钱相关的数据类型一般有两种选择：

- 使用 int 或者 bigint 类型，如果需要存储到分的维度，需要 *100 进行放大
- 使用 decimal 类型，避免精度丢失。如果使用 Java 语言时，需要使用 BigDecimal 进行对应

# 二：Mysql的存储引擎

### 2.1 存储引擎介绍

MySQL中的存储引擎是一个非常有特色的设计，`支持插拔式`。Mysql提供了大量的存储类型，支持不同的能力，`针对不同表可根据适用场景设置不同的存储引擎`。存储引擎说白了就是如何存储数据、如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法。

### 2.2 常用存储引擎

#### 2.2.1 InnoDB

InnoDB是一个健壮的`事务型`存储引擎，并且提供`行级锁`、`MVCC`等用于应对并发、提升性能的功能，还有`崩溃恢复`等生产常用功能，InnoDB也是5.4版本之后默认的存储引擎。一般来说，如果需要事务支持，并且有较高的并发读取频率，InnoDB是不错的选择。

- 更新密集的表：InnoDB存储引擎特别适合处理多重并发的更新请求
- 事务：InnoDB存储引擎是支持事务的标准MySQL存储引擎
- 自动灾难恢复：与其它存储引擎不同，InnoDB表能够自动从灾难中恢复
- 外键约束。MySQL支持外键的存储引擎只有InnoDB。

#### 2.2.2 MyISAM

MyISAM表是`独立于操作系统`的，这说明可以轻松地将其从Windows服务器移植到Linux服务器。`MyISAM表无法处理事务`，这就意味着有事务处理需求的表，不能使用MyISAM存储引擎。

- 选择密集型的表：MyISAM存储引擎在筛选大量数据时非常迅速，这是它最突出的优点
- 插入密集型的表：MyISAM的并发插入特性允许同时选择和插入数据

例如：MyISAM存储引擎很适合管理邮件或Web服务器日志数据。

#### 2.2.3 MRG_MYISAM

`MRG_MyISAM存储引擎是一组MyISAM表的组合`，老版本叫 MERGE 其实是一回事儿，这些MyISAM表结构必须完全相同，尽管其使用不如其它引擎突出，但是在某些情况下非常有用。说白了，Merge表就是几个相同MyISAM表的聚合器；Merge表中并没有数据，对Merge类型的表可以进行查询、更新、删除操作，这些操作实际上是对内部的MyISAM表进行操作。

Merge存储引擎的使用场景。对于服务器日志这种信息，一般常用的存储策略是将数据分成很多表，每个名称与特定的时间端相关。例如：可以用12个相同的表来存储服务器日志数据，每个表用对应各个月份的名字来命名。当有必要基于所有12个日志表的数据来生成报表，这意味着需要编写并更新多表查询，以反映这些表中的信息。与其编写这些可能出现错误的查询，不如将这些表合并起来使用一条查询，之后再删除Merge表，而不影响原来的数据，`删除Merge表只是删除Merge表的定义，对内部的表没有任何影响`。

- ENGINE=MERGE，指明使用MERGE引擎
- UNION=(t1, t2)，指明了MERGE表中挂接了些哪表，可以通过alter table的方式修改UNION的值，以实现增删MERGE表子表的功能

- INSERT_METHOD=LAST，INSERT_METHOD指明插入方式，取值可以是：0 不允许插入；FIRST 插入到UNION中的第一个表； LAST 插入到UNION中的最后一个表。
- `MERGE表及构成MERGE数据表结构的各成员数据表必须具有完全一样的结构`。每一个成员数据表的数据列必须按照同样的顺序定义同样的名字和类型，`索引也必须按照同样的顺序和同样的方式定义`。

```mysql
alter table tb_merge engine=merge union(tb_log1) insert_method=last;
```

#### 2.2.4 MEMORY

使用MySQL Memory存储引擎的出发点是速度。为得到最快的响应时间，`采用的存储介质是系统内存`。虽然在内存中存储表数据确实会提供很高的性能，但当`mysqld守护进程崩溃时，所有的Memory数据都会丢失`。`它要求存储在Memory数据表里的数据使用的是长度不变的格式`，这意味着不能使用BLOB和TEXT这样的长度可变的数据类型，`VARCHAR是一种长度可变的类型，但因为它在MySQL内部当做长度固定不变的CHAR类型`，所以可以使用。

一般在以下几种情况下使用Memory存储引擎：

- 目标数据较小，而且被非常频繁地访问。在内存中存放数据，所以会造成内存的使用，可以通过参数max_heap_table_size控制Memory表的大小，设置此参数，就可以限制Memory表的最大大小。
- 如果数据是临时的，而且要求必须立即可用，那么就可以存放在内存表中。
- 存储在Memory表中的数据如果突然丢失，不会对应用服务产生实质的负面影响。
- Memory同时支持散列索引和B树索引。

注：`B树索引的优于散列索引的是，可以使用部分查询和通配查询，也可以使用<、>和>=等操作符方便数据挖掘`。散列索引进行“相等比较”非常快，但是对“范围比较”的速度就慢多了，`因此散列索引值适合使用在=和<>的操作符中，不适合在<或>操作符中，也同样不适合用在order by子句中`。

#### 2.2.5 CSV

CSV 存储引擎是基于 CSV 格式文件存储数据。

- CSV 存储引擎因为自身文件格式的原因，`所有列必须强制指定 NOT NULL `。
- CSV 引擎也`不支持索引，不支持分区`。
- CSV 存储引擎也会包含一个存储表结构的 .frm 文件，还会创建一个 .csv 存储数据的文件，还会创建一个同名的元信息文件，该文件的扩展名为 .CSM ，用来保存表的状态及表中保存的数据量。
- 每个数据行占用一个文本行。

因为 csv 文件本身就可以被Office等软件直接编辑，当需要这种场景时可以考虑CSV引擎。如果出现csv文件中的内容损坏了的情况，可以使用CHECK TABLE 或者 REPAIR TABLE 命令检查和修复。

#### 2.2.6 ARCHIVE

Archive是归档的意思，在归档之后很多的高级功能就不再支持了，`仅仅支持最基本的插入和查询两种功能`。在MySQL 5.5版以前，Archive是不支持索引，但是在MySQL 5.5以后的版本中就开始支持索引了。`Archive拥有很好的压缩机制，它使用zlib压缩库`，在记录被请求时会实时压缩，所以它`经常被用来当做仓库使用`。

#### 2.2.7 BLACKHOLE

黑洞存储引擎，所有插入的数据并不会保存，BLACKHOLE 引擎表永远保持为空，写入的任何数据都会消失，

#### 2.2.8 PERFORMANCE_SCHEMA

主要用于收集数据库服务器性能参数。MySQL用户是不能创建存储引擎为PERFORMANCE_SCHEMA的表，一般用于记录binlog做复制的中继。

#### 2.2.9 FEDERATED

主要用于访问其它远程MySQL服务器一个代理，它通过创建一个到远程MySQL服务器的客户端连接，并将查询传输到远程服务器执行，而后完成数据存取；在MariaDB的上实现是FederatedX。

#### 2.2.10 其他

除了以上搜索引擎，还有一部分社区维护的更小众的搜索引擎：OQGraph、SphinxSE、TokuDB、Cassandra、CONNECT、SQUENCE。

#### 2.2.11 总结

| 存储引擎   | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| `MyISAM`   | 高速引擎，拥有`较高的插入，查询速度`，但`不支持事务`、`不支持行锁`、`支持3种不同的存储格式`。包括静态型、动态型和压缩型。 |
| `InnoDB`   | 5.5版本后MySQL的`默认存储引擎`，`支持事务和行级锁定`，`事务处理`、`回滚`、`崩溃修复能力`和`多版本并发控制的事务安全`，比 MyISAM处理速度稍慢、`支持外键`（FOREIGN KEY） |
| ISAM       | MyISAM的前身，MySQL5.0以后不再默认安装                       |
| MRG_MyISAM | 将多个表联合成一个表使用，在超大规模数据存储时很有用         |
| `Memory`   | `内存存储`引擎，`拥有极高的插入，更新和查询效率`。但是会占用和数据量成正比的内存空间。只在内存上保存数据，意味着数据`可能会丢失` |
| CSV        | CSV 存储引擎是基于 CSV 格式文件存储数据(应用于跨平台的数据交换) |
| Archive    | 将数据压缩后进行存储，非常适合存储大量的独立的，作为历史记 录的数据，但是只能进行插入和查询操作 |

### 2.3 存储引擎对比

不同存储引起都有各自的特点，为适应不同的需求，需要选择不同的存储引擎，所以首先考虑这些存储引擎各自的功能和兼容。

- 常用存储引擎的功能支持

| 特性                                                   | InnoDB | MyISAM | MEMORY | ARCHIVE |
| ------------------------------------------------------ | ------ | ------ | ------ | ------- |
| 存储限制(Storage limits)                               | 64TB   | No     | YES    | No      |
| 支持事物(Transactions)                                 | Yes    | No     | No     | No      |
| 锁机制(Locking granularity)                            | 行锁   | 表锁   | 表锁   | 行锁    |
| B树索引(B-tree indexes)                                | Yes    | Yes    | Yes    | No      |
| T树索引(T-tree indexes)                                | No     | No     | No     | No      |
| 哈希索引(Hash indexes)                                 | Yes    | No     | Yes    | No      |
| 全文索引(Full-text indexes)                            | Yes    | Yes    | No     | No      |
| 集群索引(Clustered indexes)                            | Yes    | No     | No     | No      |
| 数据缓存(Data caches)                                  | Yes    | No     | N/A    | No      |
| 索引缓存(Index caches)                                 | Yes    | Yes    | N/A    | No      |
| 数据可压缩(Compressed data)                            | Yes    | Yes    | No     | Yes     |
| 加密传输(Encrypted data[1])                            | Yes    | Yes    | Yes    | Yes     |
| 集群数据库支持(Cluster databases support)              | No     | No     | No     | No      |
| 复制支持(Replication support[2])                       | Yes    | No     | No     | Yes     |
| 外键支持(Foreign key support)                          | Yes    | No     | No     | No      |
| 存储空间消耗(Storage Cost)                             | 高     | 低     | N/A    | 非常低  |
| 内存消耗(Memory Cost)                                  | 高     | 低     | N/A    | 低      |
| 数据字典更新(Update statistics for data dictionary)    | Yes    | Yes    | Yes    | Yes     |
| 备份/时间点恢复(backup/point-in-time recovery[3])      | Yes    | Yes    | Yes    | Yes     |
| 多版本并发控制(Multi-Version Concurrency Control/MVCC) | Yes    | No     | No     | No      |
| 批量数据写入效率(Bulk insert speed)                    | 慢     | 快     | 快     | 非常快  |
| 地理信息数据类型(Geospatial datatype support)          | Yes    | Yes    | No     | Yes     |
| 地理信息索引(Geospatial indexing support[4])           | Yes    | Yes    | No     | Yes     |

- 常用存储引擎对比

|           | Innodb                                       | Myisam                                                  |
| --------- | -------------------------------------------- | ------------------------------------------------------- |
| 存储文件  | .frm 表定义文件<br />.ibd 数据文件和索引文件 | .frm 表定义文件 <br />.myd 数据文件 <br />.myi 索引文件 |
| 锁        | 表锁、行锁、MVCC                             | 表锁                                                    |
| 事务      | 支持                                         | 不支持                                                  |
| 适用场景  | 读、写                                       | 读多                                                    |
| count查询 | 扫表                                         | 专门存储的地方 （加where也扫表）                        |
| 索引结构  | B+ Tree                                      | B+ Tree                                                 |
| 外键      | 支持                                         | 不支持                                                  |
| 全文检索  | 不支持                                       | 支持                                                    |

### 2.4 如何选择存储引擎

选择存储引擎主要是根据适用场景来，选择需要具备的对应特性的存储引擎：

- 是否需要支持事务 ==> Innodb
- 对索引和缓存的支持 ==> Innodb、Myisam
- 是否需要使用热备 ==> Innodb
- 崩溃恢复，能否接受崩溃 ==> Innodb
- 存储的限制 ==> Myisam
- 是否需要外键支持 ==> Innodb

- 是否需要很快的读写速度 ==> MEMORY

一般情况下，建议选择InnoDB引擎。

注：由于`外键的性能较差`，现在开发中外键已经使用较少，数据的安全放到应用层通过业务逻辑保障。

### 2.5 储存引擎的使用

#### 2.5.1 查看存储引擎

使用`“SHOW VARIABLES LIKE '%storage_engine%';”` 命令在mysql系统变量搜索默认设置的存储引擎，输入语句如下：

![image-20220405214430521](http://rocks526.top/lzx/image-20220405214430521.png)

使用`“SHOW ENGINES;”`命令显示安装以后可用的所有的支持的存储引擎和默认引擎，后面带上 \G 可以列表输出结果。

![image-20220405214500546](http://rocks526.top/lzx/image-20220405214500546.png)

#### 2.5.2 设置存储引擎

Mysql默认存储引擎是InnoDB，但也可以在`my.cnf` 配置文件中设置你需要的存储引擎，这个参数放在 [mysqld] 这个字段下面的 default_storage_engine 参数值，例如下面配置的片段：

```properties
[mysqld]
default_storage_engine=CSV
```

在创建表的时候，对表设置存储引擎，例如：

```mysql
CREATE TABLE `user` (
  `id`     int(100) unsigned NOT NULL AUTO_INCREMENT,
  `name`   varchar(32) NOT NULL DEFAULT '' COMMENT '姓名',
  `mobile` varchar(20) NOT NULL DEFAULT '' COMMENT '手机',
  PRIMARY KEY (`id`)
)ENGINE=InnoDB;
```

# 三：Mysql索引

### 2.1 索引的介绍

- 索引的介绍

官方介绍索引是`帮助MySQL高效获取数据的数据结构`。更通俗的说，数据库索引好比是一本书前面的目录，能加快数据库的查询速度。

- 索引的优势

1. 可以提高数据`检索`的效率，降低数据库的IO成本
2. 通过索引列对数据进行`排序`，降低数据排序的成本，降低了CPU的消耗
3. Mysql5.6版本之后支持`索引下推`，可以有效减少回表次数和返回Server的数据量

- 索引的劣势

1. 索引会占据磁盘空间
2. 索引虽然会提高查询效率，但是会降低写入的效率，如新增和更新表数据都需要更新对应的索引信息

### 2.2 索引的常见数据模型

索引的出现是为了提高查询效率，但是实现索引的方式却有很多种，所以这里也就引入了索引模型的概念。可以用于提高读写效率的数据结构很多，这里先介绍最常见的三种，它们分别是`哈希表`、`有序数组`和`搜索树`。

介绍数据结构之前，先假设存在一个需求：现在维护着一个身份证信息和姓名的表，需要根据身份证号查找对应的名字。

- 哈希表

哈希表是一种以键 - 值（key-value）存储数据的结构，只要输入待查找的值即 key， 就可以找到其对应的值即 Value。哈希的思路很简单，把值放在数组里，用一个哈希函数把 key 换算成一个确定的位置，然后把 value 放在数组的这个位置。多个 key 值经过哈希函数的换算，可能会出现同一个值的情况，处理这种情况的一种方法是拉出一个链表。针对上述需求，哈希表存储结构如下：

![image-20220407152155503](http://rocks526.top/lzx/image-20220407152155503.png)

需要注意的是，图中四个 ID_card_n 的值并不是递增的，这样做的好处是增加新的 User 时速度会很快，只需要往后追加。但缺点是，因为不是有序的，所以哈希索引做区间查询的速度是很慢的。当遇到区间查询时，需要做全表扫描。

所以，`哈希表这种结构适用于只有等值查询的场景`，比如 Memcached 及其他一些 NoSQL 引擎。

- 有序数组

还是上面那个例子，有序数组存储结构如下所示：

![image-20220407152402837](http://rocks526.top/lzx/image-20220407152402837.png)

由于数组是按照身份证号递增的顺序保存的，因此根据身份证查询名称时，采用二分查找即可，时间复杂度O(N)。同时，这个索引结构能很好的支持范围查询，首先二分查找左区间的值，再顺序遍历直至遇到第一个大于的值即可，时间复杂度O(N)。

如果仅仅看查询效率，有序数组就是最好的数据结构。但是，在需要更新数据的时候就比较麻烦，往中间插入一个记录就必须得挪动后面所有的记录，成本太高。

所以，`有序数组索引只适用于静态存储引擎`，比如要保存的是 2017 年某个城市的所有人口信息，这类不会再修改的数据。

- 搜索树

二叉搜索树也是一个经典的数据结构，还是上面的需求，二叉搜索树的示意图如下：

![image-20220407152831528](http://rocks526.top/lzx/image-20220407152831528.png)

二叉搜索树的特点是：每个节点的左儿子小于父节点，父节点又小于右儿子。因此根据身份证查询名称时，不断往下搜索即可，时间复杂度O(log(N))。

当然为了维持 O(log(N)) 的查询复杂度，就需要保持这棵树是平衡二叉树。为了做这个保证，更新的时间复杂度也是 O(log(N))。

树可以有二叉，也可以有多叉。多叉树就是每个节点有多个儿子，儿子之间的大小保证从左到右递增。二叉树是搜索效率最高的，但是`实际上大多数的数据库存储却并不使用二叉树。 其原因是，索引不止存在内存中，还要写到磁盘上`。可以想象一下一棵 100 万节点的平衡二叉树，树高 20。一次查询可能需要访问 20 个数据块。在机械硬盘时代，从磁盘随机读一个数据块需要 10 ms 左右的寻址时间。也就是说，对于一个 100 万行的表，如果使用二叉树来存储，单独访问一个行可能需要 200 ms 的时间。

为了让一个查询尽量少地读磁盘，就必须让查询过程访问尽量少的数据块。那么，我们就不应该使用二叉树，而是要使用"N叉"树。这里，"N叉"树中的"N"取决于数据块的大小。以 InnoDB 的一个整数字段索引为例，`这个 N 差不多是 1200`。这棵树高是 4 的时候，就可以存 1200 的 3 次方个值，这已经 17 亿了。考虑到树根的数据块总是在内存中的，一个 10 亿行的表上一个整数字段的索引，查找一个值最多只需要访问 3 次磁盘。其实，树的第二层也有很大概率在内存中，那么访问磁盘的平均次数就更少了。

`N叉树由于在读写上的性能优点，以及适配磁盘的访问模式`，已经被广泛应用在数据库引擎中了。

### 2.3 Mysql的索引分类（存储方式）

索引有很多种类型，可以为不同的场景提供更好的性能。在Mysql中，索引是在存储引擎层中实现的，所以没有统一的标准，不同的存储引擎支持不同的索引类型，甚至同一种索引类型，不同的存储引擎底层实现也不一致。下面介绍下常用的索引类型：

#### 2.3.1 B-Tree索引

- B-Tree介绍

B-树索引又称为 BTREE 索引，目前大部分的索引都是采用 B-树索引来存储的。B-树索引底层存储结构其实就是上面提到的N叉树，为了降低树的高度，减少磁盘IO次数，将整个树压扁，每个节点挂的不是一个数据，而是一个数据页，查找时先通过树定位到具体的数据页，再通过二分查找目标数据。

注：B-Tree底层分为B树和B+树实现，B+树相比B树，`所有数据存储在叶子节点，并且不同数据页之前通过指针关联`，范围查询会非常快。

- 应用场景

1. 等值匹配
2. 范围查询
3. 前缀查询
4. ORDER BY排序

注：当添加B树索引时，索引字段整体有序，因此不需要再把所有数据抓取到Server层的内存进行排序。

#### 2.3.2 哈希索引

- 哈希索引介绍

哈希索引的底层数据结构就是上面介绍的哈希表，MySQL 目前仅有 MEMORY 存储引擎和 HEAP 存储引擎支持这类索引。哈希索引的最大特点是访问速度快，但也存在下面的一些缺点：

1. MySQL 需要读取表中索引列的值来参与散列计算，散列计算是一个比较耗时的操作
2. HASH 索引只支持等值比较
3. HASH无序，HASH索引无法用来排序
4. HASH 索引不支持键的部分匹配，因为在计算 HASH 值的时候是通过整个索引值来计算的

- 哈希索引适用场景

1. 只做等值匹配、且存在较多更新的表
2. Mysql的Server里，Buffer Pool就是Hash结构缓存

#### 2.3.3 空间数据索引（R-Tree）

MyISAM引擎支持空间数据索引，可以用于作地理数据存储。和B-Tree索引不同，这类索引无需前缀查询，空间索引会从所有维度来索引数据。查询时，可以有效地使用任意维度来组合查询。

注：Mysql的GIS支持并不完善，因此使用的很少，开源关系型数据库里，对GIS支持较好的是PostgreSql的PostGIS。

#### 2.3.4 全文索引

全文索引是一种特殊类型的索引，它查找的是文本中的关键字，而不是做全值比较。全文索引底层采用倒排索引结构，与其他索引存在较大区别。

注：在相同的列上，可以同时创建全文索引和B-Tree索引，当SQL使用MATCH AGAINST匹配时使用全文索引，反之使用B-Tree索引。

注：全文检索的需求，建议交给Elastic search这种专业的中间件去做，MySQL的全文索引性能较差，且只有MyISAM引擎支持，因此使用不多。

#### 2.3.5 其他索引

还有很多第三方的存储引擎使用不同的数据结构来存储索引，例如TokuDB使用分形树，这是一种新开发的数据结构，既具备很多B+树的优点，又避免了一些缺点。只是目前还不是主流，此处只介绍一些主流的索引存储结构。

### 2.4 Mysql的索引分类（逻辑结构）

上面介绍的MySQL索引的分类是根据底层物理结构划分，下面介绍的索引分类将是根据特性划分，例如是否允许空值、是否唯一等。

#### 2.4.1 聚簇索引 && 非聚簇索引

聚簇索引是InnoDB引入的概念，指的是数据存储在主键索引上，辅助索引存的是数据是主键。

非聚簇索引，就是索引和数据分离存储的模式，主键索引和辅助索引上存储的都是数据的存放地址。

- 非聚簇索引示例图（MyISAM）

主键索引示意图如下：

![image-20220408160615413](http://rocks526.top/lzx/image-20220408160615413.png)

辅助索引/二级索引示意图如下：

![image-20220408160749160](http://rocks526.top/lzx/image-20220408160749160.png)

- 聚簇索引示意图（InnoDB）

主键索引示意图：

![image-20220408160947925](http://rocks526.top/lzx/image-20220408160947925.png)

辅助索引/二级索引示意图如下：

![image-20220408161211689](http://rocks526.top/lzx/image-20220408161211689.png)

- 总结

1. 非聚簇索引每次查询都需要两次交互，首先查询索引获取数据的存放地址，再去读数据
2. 聚簇索引根据主键查询，只需要查询一次，走二级索引时，需要判断该二级索引存放的数据是否满足要查询的字段，不满足时根据主键ID去主键索引再次查询，这个再次查询的操作叫做回表，可以通过组合索引/覆盖索引减少回表操作。
3. 当InnoDB的表没有设置主键时，InnoDB会自动选择唯一键作为主键，当没有唯一键值，InnoDB会自动生成一个伪列作为主键，即InnoDB必须存在主键
4. 针对InnoDB的主键，尽量使用自增ID，不要使用UUID，因为`自增主键可以保证追加插入，会尽可能较少索引的页分裂`，而且辅助索引存储的数据都是主键，也`会减少空间占用`，针对分布式场景可以通过`雪花算法生成自增ID`。

#### 2.4.2 普通索引 && 唯一索引 && 主键索引

这三种索引和底层存储结构无关，是Mysql业务层实现的约束，每种索引的约束如下：

- 普通索引：无任何约束
- 唯一索引：建立此索引的列，值必须唯一，例如身份证信息
- 主键索引：唯一索引 + NOT NULL，即列值不可为空，且必须唯一

#### 2.4.3 组合索引 && 覆盖索引

- 组合索引介绍

组合索引顾名思义，就是将多个列组合在一起作为一个值来建立索引，如下图的（id，name，age）的组合索引：

![image-20220408164006494](http://rocks526.top/lzx/image-20220408164006494.png)

- 组合索引的优点

1. 相比每个字段建立一个索引，建立`组合索引更省空间`
2. Mysql在查询优化器选择索引时，只能选择一个索引，当存在查询条件包含name、age、id的SQL时，使用`组合索引会比单索引效率高`
3. 在InnoDB引擎中，辅助索引在查询数据时，如果无法覆盖查询项会导致回表操作，而组合索引存储数据更多，`更容易覆盖查询项，减少回表操作`
4. 组合索引在使用时`需要满足最左前缀匹配原则`，即查询时使用索引必须和组合索引顺序一致（考虑底层B+树存储结构即可理解）

注：SQL的where条件顺序不一定要和索引顺序一致，因为查询优化器会进行优化，选择合适的顺序。

注：组合索引在遇到范围匹配时，会导致后续的索引字段不再生效（考虑底层B+树存储结构即可理解）。

#### 2.4.4 索引下推

索引下推(Index Condition Pushdown，简称ICP)，是MySQL5.6版本的新特性，它能减少回表查询次数，提高查询效率。

- 示例

假设现在存在user表，采用InnoDB引擎，包含id、name、age、ismale等字段，id为主键，且存在一个name和age的组合索引。

现在存在如下的查询SQL：`select * from user where name like '张%' and age = 10;`

- Mysql 5.6之前

在未使用索引下推之前，使用(name、age)联合索引的时候，只能匹配到以张开头的条件，不会进行age条件校验，然后将满足name的行进行回表并返回给Server层，Server层再内存里对其他的检索条件进行过滤。

![image-20220411114602233](http://rocks526.top/lzx/image-20220411114602233.png)

如上图所示，未使用索引下推，将name满足的四条数据都会回表并交给Server层。

注：由于age字段InnoDB不关注，因此没有画出。

- 索引下推

在使用索引下推之后，会判断多个过滤条件，尽可能的减少回表和返回Server层的数据量。

![image-20220411114746530](http://rocks526.top/lzx/image-20220411114746530.png)

如上图所示，使用索引下推后，将name和age都满足的条件再进行回表。

- 适用场景

1. 只能用于`InnoDB`和 `MyISAM`存储引擎及其分区表
2. 对存储引擎来说，索引下推只适用于二级索引（也叫辅助索引）
3. 引用了子查询的条件不能下推
4. 引用了存储函数的条件不能下推，因为存储引擎无法调用存储函数

- 相关配置

索引条件下推默认是开启的，可以使用系统参数`optimizer_switch`来控制器是否开启。

![image-20220411115452876](http://rocks526.top/lzx/image-20220411115452876.png)

开启、关闭ICP：

```mysql
set ="index_condition_pushdown=off";
set ="index_condition_pushdown=on";
```

### 2.4 索引的选择策略

- 主键自动建立唯一索引
- 频繁作为查询条件的字段应该创建索引 where
- 多表关联查询中，关联字段应该创建索引 on 两边都要创建索引
- 查询中排序的字段，应该创建索引 B + tree 有顺序
- 统计或者分组字段，应该创建索引
- 主键索引尽量采用自增INT，避免页分裂影响插入性能和二级索引存储空间的消耗
- 尽量采用组合索引，实现覆盖索引，减少回表等操作
- 索引的列值分化程度要足够高，至少通过索引可以减少搜索的数据量为之前的三分之一，例如性别字段就不适合作为索引

### 2.5 索引的使用

- 创建索引

```mysql
-- 单列索引/普通索引
CREATE INDEX index_name ON table(column(length));
CREATE INDEX book_name_index ON book(name);

-- 同上
ALTER TABLE table_name ADD INDEX index_name (column(length)) ;
ALTER TABLE book ADD INDEX book_name_index_2 (name) ;

-- 单列索引，唯一索引
CREATE UNIQUE INDEX index_name ON table(column(length)) ;
CREATE UNIQUE INDEX book_name_union_index ON book(name);

-- 同上
alter table table_name add unique index index_name(column);
ALTER TABLE book ADD unique INDEX book_name_union_index_2 (name) ;

-- 全文索引
CREATE FULLTEXT INDEX index_name ON table(column(length)) ;
CREATE FULLTEXT INDEX book_name_full_index ON book(name);

-- 同上
alter table table_name add fulltext index index_name(column)
ALTER TABLE book ADD fulltext INDEX book_name_full_index_2 (name) ;

-- 组合索引
ALTER TABLE table_name ADD INDEX index_name (column1,column2) ;
ALTER TABLE book ADD INDEX index_name_publisher (name,publisher) ;
```

- 删除索引

```mysql
DROP INDEX index_name ON table;
DROP INDEX book_name_full_index_2 ON book;
```

- 查看索引

```mysql
SHOW INDEX FROM table_name;
SHOW INDEX FROM book;
```

![image-20220411141656120](http://rocks526.top/lzx/image-20220411141656120.png)

### 2.6 查看执行计划

- 官方文档

https://dev.mysql.com/doc/refman/5.7/en/explain-output.html

- 初始化表结构

```mysql
-- ----------------------------
-- 图书表
-- ----------------------------
DROP TABLE IF EXISTS `book`;
CREATE TABLE `book`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `name` varchar(64)  NOT NULL COMMENT '名称',
  `publisher` varchar(255)  NOT NULL COMMENT '出版社',
  `publish_time` date NOT NULL COMMENT '出版时间',
  `price` double NOT NULL COMMENT '价格',
  `isbn` varchar(64)  NOT NULL COMMENT '图书编号',
  `introduce` TEXT(65535)  NULL DEFAULT NULL COMMENT '图书介绍',
  `author` bigint NOT NULL COMMENT '作者',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;
CREATE INDEX book_name_index ON book(name);
CREATE INDEX book_author_index ON book(author);
ALTER TABLE book ADD INDEX index_name_publisher (name,publisher) ;

-- ----------------------------
-- 图书作者表
-- ----------------------------
DROP TABLE IF EXISTS `book_author`;
CREATE TABLE `book_author`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `name` varchar(64)  NOT NULL COMMENT '名称',
  `sex` int NOT NULL COMMENT '性别',
  `country` varchar(64)  NULL DEFAULT NULL COMMENT '国家',
  `birthday` date NOT NULL COMMENT '生日',
  `tel` varchar(64)  NOT NULL COMMENT '手机号',
  `introduce` TEXT(65535)  NULL DEFAULT NULL COMMENT '介绍',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间',
  `num` bigint NOT NULL COMMENT '测试数字'
) ENGINE = InnoDB;
CREATE INDEX book_author_num_index ON book_author(num);

-- ----------------------------
-- 图书评论表
-- ----------------------------
DROP TABLE IF EXISTS `book_comment`;
CREATE TABLE `book_comment`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `score` int NOT NULL COMMENT '评分',
  `content` TEXT(65535)  NULL DEFAULT NULL COMMENT '评价内容',
  `user` varchar(64) NULL DEFAULT NULL COMMENT '评论人',
  `create_time` datetime NOT NULL COMMENT '评论时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书和评论关联表
-- ----------------------------
DROP TABLE IF EXISTS `book_comment_relation`;
CREATE TABLE `book_comment_relation`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `book_id` bigint NOT NULL COMMENT '图书ID',
  `book_comment` bigint NOT NULL COMMENT '图书评论ID'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书标签表
-- ----------------------------
DROP TABLE IF EXISTS `book_tag`;
CREATE TABLE `book_tag`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `name` varchar(64)  NOT NULL COMMENT '标签名称',
  `sort` int NOT NULL COMMENT '标签排序',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '更新时间'
) ENGINE = InnoDB;

-- ----------------------------
-- 图书标签关联表
-- ----------------------------
DROP TABLE IF EXISTS `book_tag_relation`;
CREATE TABLE `book_tag_relation`  (
  `id` bigint PRIMARY KEY AUTO_INCREMENT COMMENT '主键',
  `book_id` bigint NOT NULL COMMENT '图书ID',
  `tag_id` bigint NOT NULL COMMENT '标签ID'
) ENGINE = InnoDB;

```

一个查询的SQL在Mysql内部的执行逻辑，可以通过查看查询计划获取一部分，该执行计划不一定完全正确但是可以参考。

- 查看SQL的查询计划

```mysql
-- 语法
explain + sql语句;
-- 示例
explain select * from book;
```

![image-20220411142255490](http://rocks526.top/lzx/image-20220411142255490.png)

查询计划返回的结果很多，下面会详细介绍，其中比较重要的是select_type、type、extra几列。

- id

对于复杂查询，每个子查询都会生成一个id字段，用于标识此子查询在整个SQL中的执行顺序，有如下三种情况：

1. id相同：`执行顺序由上到下`
2. id不同：`id越大，优先级越高`
3. id相同的不同的同时存在，应用如上两条规则

```mysql
explain select * from book where author in (select id from book_author where country  = '美国');
```

![image-20220411143152832](http://rocks526.top/lzx/image-20220411143152832.png)

可以看到，底层查询逻辑是先查询`select id from book_author where country  = '美国'`，再查询`select * from book where author in()`。

- select_type

|    select_type     |                        说明                         | 示例语句                                                     |
| :----------------: | :-------------------------------------------------: | ------------------------------------------------------------ |
|       SIMPLE       |                      简单查询                       | select * from book                                           |
|      PRIMARY       |       最外层查询，针对union或子查询的外层查询       | select * from book where author in (select id from book_author where country  = '美国') |
|      SUBQUERY      |     映射为子查询，除了from字句中包含的子查询外      | explain select id,name,(select name from book_author where author = id) as book_author from book; |
| DEPENDENT SUBQUERY |    表示这个subquery的查询要受到外部表查询的影响     | explain select id,name,(select name from book_author where author = id) as book_author from book; |
|       UNION        |      union联合的两个查询，第二个表开始为union       | select * from book where id = 1 union select * from book where id = 2 union select * from book where id > 2; |
|    UNION RESULT    | 合并上面几个union的结果，由于不需要查询，因此id为空 | select * from book where id = 1 union select * from book where id = 2 union select * from book where id > 2; |

- table

当前查询正在访问的表名。

- partitions

当前查询匹配的分区信息。

- type

针对单表的访问方法，例如使用主键索引、唯一索引、组合索引、全表扫描等。性能从好到差，分别为：

```mysql
system > const > eq_ref > ref > fulltext > ref_or_null > unique_subquery > index_subquery > range > index_merge > index > ALL
```

除了all之外，其他的type都可以使用到索引，除了index_merge之外，其他的type只可以用到一个索引，优化器会选用最优索引。

出现比较多的是`system>const>eq_ref>ref>range>index>ALL`，一般来说，得保证查询至少达到 `range` 级别，最好能达到 `ref`。

|      type       |                             说明                             | 查询示例                                                     |
| :-------------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
|     system      | 当表中只有`一条记录`并且该表使用的存储引擎的统计数据是精确的，比如MyISAM、Memory，那么对该表的访问方法就是 system，在InnoDB是不会出现system的 |                                                              |
|      const      | 当`根据主键或者唯一二级索引列与常数进行等值匹配`时，对单表的访问方法就是 const，因为只匹配一行数据，所以很快 | select * from book where id = 1;                             |
|     eq_ref      | 在连接查询时，如果`被驱动表是通过主键或者唯一二级索引列等值匹配的方式进行访问的`(如果该主键或者唯一二级索引是联合索引的话，所有的索引列都必须进行等值比较)，则对该被驱动表的访问方法就是 eq_ref | explain select * from book b left join book_author a on b.author = a.id; |
|       ref       | 针对`非唯一性索引`，`使用等值（=）查询非主键`，或者是`使用了最左前缀规则索引的查询` | explain select * from book where author = 1;<br />explain select * from book where name = '高性能Mysql' and publisher like '电子工业出版社%'; |
|    fulltext     |                        `全文索引检索`                        |                                                              |
|   ref_or_null   |     与ref方法类似，只是增加了null值的比较，实际用的不多      | explain select * from book where name = '高性能Mysql' or name is NULL; |
| unique_subquery | unique _subquery 是针对在一些包含`IN子查询的查询语句`中，如果查询优化器决定将IN子查询转换为EXISTS子查询，而且`子查询可以使用到主键进行等值匹配的话`，那么该子查询执行计划的 type 列的值就是 unique_subquery | explain select * from book where author in (select id from book_author where book.author = book_author.sex) or name = 'Java'; |
| index_subquery  | index_subquery 与 unique_subquery 类似，`只不过访问⼦查询中的表时使⽤的是普通的索引` | explain select * from book where author in (select num from book_author where book.author = book_author.sex) or name = 'Java'; |
|      range      | `索引范围扫描，常见于使用>,<,is null,between ,in ,like等运算符的查询中` | explain select * from book where id > 3;<br />explain select * from book where name like 'J%'; |
|   index_merge   | 表示查询使用了两个以上的索引，最后取交集或者并集，常见and ，or的条件使用了不同的索引，官方 排序这个在ref_or_null之后，但是实际上由于要读取所个索引，性能可能大部分时间都不如range | explain select * from book where name = '高性能Mysql' or author = 1; |
|      index      | 当可以使用索引，但需要扫描全部的索引记录时，该表的访问方法就是 index，常见于没有过滤条件的查询索引列 | explain select name from book;<br />explain select name,publisher from book; |
|       ALL       |                           全表扫描                           | explain select name,introduce from book;                     |

注：当查询计划的type为ALL，与预期结果不符时，`可能是表内数据太少导致的，如果查询出来的条数多余表的二分之一，可能不走索引，直接全表扫描`。

- possible_keys

表示在查询时，可以使用到的索引有哪些，如果为NULL，则没有可用的索引。

- key

表示在查询时，查询优化器最终选择使用的索引。

- key_len

表示在查询时，选择的索引的长度，如果是单列索引，那就整个索引长度算进去，如果是多列索引，那么查询不一定都能使用到所有的列，具体使用到了多少个列的索引，这里就会计算进去，没有使用到的列，这里不会计算进去。

注：这个值一般在使用组合索引时很有用，`可以根据长度判断组合索引使用了哪几列`。

注：key_len只计算where条件用到的索引长度，而排序和分组就算用到了索引，也不会计算到 key_len中

- ref

如果是使用的常数等值查询，这里会显示const。

如果是连接查询，被驱动表的执行计划这里会显示驱动表的关联字段。

如果是条件使用了表达式或者函数，或者条件列发生了内部隐式转换，这里可能显示为func。

- rows

这里是执行计划中估算的扫描行数，不是精确值（InnoDB不是精确的值，MyISAM是精确的值，主要原 因是InnoDB里面使用了MVCC并发机制）。

- filtered

查询优化器预测有多少百分比的记录将会满足其余的搜索条件，在join时，表示驱动表对被驱动表需要链接的查询百分比。

- extra

这个列包含不适合在其他列中显示单十分重要的额外的信息，这个列可以显示的信息非常多，有几十种，常用的有：

| 可选项                                | 说明                                                         | 示例                                                         |
| ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Impossible where                      | WHERE 子句永远为 FALSE 时将会提示该额外信息                  | explain select * from book where 1 = 2;                      |
| using filesort                        | 排序时无法使用到索引时，就会出现这个，说明MySQL会使用一个外部的索引排序，而不是按照索引顺序进行读取，MySQL中无法利用索引完成的排序操作称为"文件排序" | explain select * from book order by create_time;             |
| Using index                           | 查询时不需要回表查询，直接通过索引就可以获取查询的数据       | explain select name,publisher from book;                     |
| using where                           | 表示存储引擎返回的记录并不是所有的都满足查询条件，需要在server层进行过滤 | explain select * from book where create_time > '2022-01-01'; |
| Using index condition                 | 使用了索引下推优化，将查询条件下发给存储引擎进行过滤         | explain select * from book where name > 'A' and name like '电子%'; |
| Not exists                            | 使用左（外）连接时，如果 WHERE 子句中包含要求被驱动表的某个列等于 NULL 值的搜索条件，而且那个列又是不允许存储 NULL 值的 | explain select * from book b left join book_author a on b.author = a.id where a.name is null; |
| Using temporary                       | MySQL 可能会借助临时表来完成一些功能，比如去重、排序之类的，如果不能有效利用索引来完成查询，MySQL 很有可能寻求通过建立内部的临时表来执行查询。如果查询中使用到了内部的临时表，在执行计划的 Extra 列将会显示 Using temporary 提示 | explain select DISTINCT b.name from book b left join book_author a on b.author = a.id; |
| Using join buffer (Block Nested Loop) | 联表查询时没有使用到索引，通过Block Nested-Loop Join算法在内存中逐行扫描对比 | explain select * from book b left join book_author a on b.author = a.id where a.name is null; |

### 2.7 索引失效分析

- 优先全值匹配

遇到组合索引时，多个字段尽量都采用等值匹配，尽可能多的使用索引。

例如组合索引(a,b,c)：

1. select * from table where a='xx' and b = 'xxx' and c = 'xxx' ： 可以使用索引的三个字段
2. select * from table where a='xx' and b = 'xxx' ： 可以使用索引的两个字段

- 最佳左前缀匹配

对于组合索引，要满足最左前缀原则。

例如组合索引(a,b,c)：

1. select * from table where a='xx' and b = 'xxx' and c = 'xxx' ： 可以使用索引的三个字段
2. select * from table where a='xx' and c = 'xxx' and b = 'xxx' ： 可以使用索引的三个字段（查询优化器改写后SQL同上）
3. select * from table where a='xx' and c = 'xxx' ：只能使用索引的一个字段，由于b断掉了
4. select * from table where b='xx' and c = 'xxx' ：无法使用索引，想要使用索引，必须包含a字段的查询条件

- 范围条件右边的列失效

对于组合索引，遇到范围匹配后，后面的字段会失效：

例如组合索引(a,b,c)：

1. select * from table where a='xx' and b = 'xxx' and c = 'xxx' ： 可以使用索引的三个字段
2. select * from table where a='xx' and b = 'xxx' and c > 'xxx' ： 可以使用索引的三个字段
3. select * from table where a='xx' and b > 'xxx' and c = 'xxx' ： 只能使用索引的两个字段，c字段无法使用
4. select * from table where a>'xx' and b = 'xxx' and c = 'xxx' ： 只用使用索引的一个字段，b、c都无法使用

- 尽量使用覆盖索引

对于组合索引，查询结果的列尽可能都包含在索引数据里，可以不需要回表操作。

例如组合索引(a,b,c)：

1. select * from table where a='xx' and b = 'xxx' and c = 'xxx' ： 由于查询所有字段，而组合索引只包含a、b、c和主键id的值，因此需要回表
2. select id from table where a='xx' and b = 'xxx' and c = 'xxx' ： 由于索引包含id的值，因此不需要回表
3. select a,b,c,id from table where a='xx' and b = 'xxx' and c = 'xxx' ： 由于索引包含a,b,c,id的值，因此不需要回表

- 索引字段上不要使用不等

在索引上使用>、<、!=、<>时，会导致索引失效，转为全表扫描。

注：主键索引使用时，会降为range，二级索引会改为全表扫描。

例如主键索引(id)，二级索引(a)：

1. select * from table where id = xxx ： 使用主键索引，等值匹配，不需要回表
2. select * from table where a = xxx ： 使用二级索引，等值匹配，需要回表
3. select * from table where id != xxx ： 使用主键索引，范围查询，不需要回表
4. select * from table where a != xxx ： 无法使用索引，全表扫描，不需要回表

- 不要在索引上做计算

在索引字段上不要进行这些操作：计算、函数、自动/手动类型转换，不然会导致索引失效而转向全表扫描。

例如主键索引(id)，二级索引(a)：

1. select * from table where id = xxx ： 使用主键索引
2. select * from table where a = xxx ： 使用二级索引
3. select * from table where id + 1= 3 ： 无法使用索引，全表扫描
4. select * from table where a * 2 = 6 ： 无法使用索引，全表扫描

- 主键索引上不可判断NULL

在主键索引上使用is null或not is null查询条件时，会导致无法使用索引，非主键索引可以使用。

例如主键索引(id)，二级索引(a)：

1. select * from table where id = xxx ： 使用主键索引
2. select * from table where id not is null ： 全表扫描
3. select * from table where a not is null ： 可以使用二级索引a

- 索引字段like匹配不能通配符开头

like匹配查询时，如果以通配符开头，无法使用索引。

例如主键索引(id)，二级索引(a)：

1. select * from table where a like 'xxxx%' ： 可以使用二级索引a
2. select * from table where a like '%xxxx%' ： 无法使用索引，全表扫描

注：对于'%xxxx%'此类查询，可以通过覆盖索引优化，覆盖索引可以使用。

- 索引字段不要使用or

索引字段使用 or 时，会导致索引失效而转向全表扫描。

例如组合索引(a,b)：

1. select * from table where a ='xxxx' and b = 'xxx'： 可以使用组合索引a、b
2. select * from table where a ='xxxx' or b = 'xxx'： 无法使用索引，全表扫描

- 字段类型不一致

当索引字段类型和等值匹配的字段类型不一致时，会发生隐式转换，可能导致索引失效。

例如主键索引（id），二级索引（a）是字符串类型：

1. select * from table where a = 'xxx'： 可以使用索引a
2. select * from table where a = xxx： 无法使用索引a，会全表扫描

- 总结

```properties
全值匹配我最爱，最左前缀要遵守；
带头大哥不能死，中间兄弟不能断；
索引列上少计算，范围之后全失效；
LIKE百分写最右，覆盖索引不写星；
不等空值还有or，索引失效要少用；
```

# 三：Mysql锁

### 3.1 Mysql锁介绍







### 3.2 Mysql两阶段锁



### 3.3 Mysql全局锁







### 3.4 Mysql表级锁



### 3.5 Mysql元数据锁



### 3.6 Mysql行级锁



### 3.7 Mysql间隙锁



### 3.8 Mysql死锁









# 五：Mysql事务

### 4.1 事务介绍

- 事务介绍

事务是数据库中一种常用的逻辑处理单元，它由一些相关性很强的SQL语句组成。将一些SQL语句写成事务之后，这些SQL语句要么全部执行成功，要么全部执行失败，不会因为MySQL数据库崩溃等意外情况而导致事务的部分SQL命令执行成功而部分SQL命令执行失败的情景。

- 事务的使用场景

事务在一些敏感事项的操作中非常有用。例如，银行账户的汇款场景中，假设A给B汇款500元，那么将A账户的余额减少500元和将B账户的余额增加500元这两个操作就可以并且应该写成一个事务，来保证这两个操作要么全部完成，要么全部不完成，防止出现某条SQL语句执行成功而另一条执行失败的情景。

- 事务的特性

`事务必须满足ACID4个特性，或者说实现了ACID四个特性，就算实现了事务`：

1. 原子性（Autmic）：所谓原子性，就是指事务里的SQL命令要么全部完成，要么全部不完成，不允许事务中的SQL命令部分执行的情景
2. 一致性（Consistency）：所谓一致性，就是指事务必须使得数据库从一个状态转变到另一个状态，不允许存在中间状态（对比分布式一致性的级别，很多都是对外实现一致性，系统内部存在不一致，而事务就是要求系统内部也是一致性状态）
3. 隔离性（Isolation）：所谓隔离性，是指当有多个事务并发执行的时候，事务之间的执行应该相互隔离，不能相互干扰。（隔离性根据适用场景和实现层面，分为四个级别，不同的级别定义了多个并发事务之间的可见性问题）
4. 持久性（Durability）：所谓持久性，是指一个事务在提交后，对数据库中数据的改变是永久性的，即便发生故障也不应该对事务操作的数据有影响

注：此处只介绍事务的概念和使用，事务的底层实现原理在InnoDB引擎处详解。

### 4.2 事务的使用

- 自动事务

MySQL对于事务支持手动控制和自动控制两种方式，默认是开启自动提交的，即针对每个SQL都会当做一个独立事务进行提交，可以通过设置autocommit参数，禁止事务自动提交功能。

```mysql
-- 查看是否开启自动提交，1开启，0禁止
select @@autocommit;
-- 禁止自动提交
set autocommit = 0;
-- 开启自动提交
set autocommit = 1;
```

- 手动事务

关闭事务自动提交后，所有事务都依赖手动提交和回滚。

```mysql
-- 开启事务
start transaction;  -- 等效 begin;
-- 执行SQL
delete from book where author = 1;
delete from book_author where id = 1;
-- 提交/回滚事务
commit;
rollback;
```

注：在MySQL中的事务是由存储引擎实现的，而且支持事务的存储引擎不多，主要使用的是InnoDB存储引擎，对于其他引擎，事务相关的语句不会报错，但rollback不会生效。

### 4.3 事务的隔离级别

#### 4.3.1 并发事务存在的问题

上面提到了事务的隔离性，之所以需要保证隔离性，支持多个隔离级别，是因为在事务的并发操作中可能会出现一些问题：

- 丢失更新：两个事务针对同一数据都发生修改操作时，先操作的会存在丢失更新问题

| 事务A                                        | 事务B                                        |
| -------------------------------------------- | -------------------------------------------- |
| begin;                                       |                                              |
|                                              | begin;                                       |
| update book set name = 'book1' where id = 1; |                                              |
|                                              | update book set name = 'book2' where id = 2; |
| commit;                                      |                                              |
|                                              | commit;                                      |

事务A的更新会丢失，最终的结果是事务B的结果。

- 脏读：一个事务读取到另一个事务未提交的数据，当另一个事务回滚，则读取到了脏数据

| 事务A                                        | 事务B                            |
| -------------------------------------------- | -------------------------------- |
| begin;                                       |                                  |
|                                              | begin;                           |
| update book set name = 'book1' where id = 1; |                                  |
|                                              | select * from book where id = 1; |
| rollback;                                    |                                  |
|                                              | commit;                          |

事务B读取到的结果是事务A刚刚更新的'book1'，由于事务A后面又rollback了，相当于读取到了脏数据。

- 不可重复读：一个事务因读取到另一个事务已提交的update或者delete数据，导致对同一条记录读取两次的结果不一致

| 事务A                                        | 事务B                            |
| -------------------------------------------- | -------------------------------- |
| begin;                                       |                                  |
|                                              | begin;                           |
|                                              | select * from book where id = 1; |
| update book set name = 'book1' where id = 1; |                                  |
| commit;                                      |                                  |
|                                              | select * from book where id = 1; |
|                                              | commit;                          |

事务B第二次读取时，由于事务A已经提交了，因此读取到了事务A提交的数据，导致事务B两次同样的SQL查询结果不一致。

- 幻读：一个事务因读取到另一个事务已提交的insert数据，导致对同一张表读取两次以上的结果不一致

| 事务A                                 | 事务B                            |
| ------------------------------------- | -------------------------------- |
| begin;                                |                                  |
|                                       | begin;                           |
|                                       | select * from book where id > 1; |
| insert into book values (2, 'book2'); |                                  |
| commit;                               |                                  |
|                                       | select * from book where id > 1; |
|                                       | commit;                          |

事务B第二次读取时，由于事务A已经提交了，因此读取到了事务A新插入的数据，导致事务B两次同样的SQL查询结果不一致。

注：上面这些问题，在有些场景下算问题，而有些场景下不算问题，依据具体的场景而定。

#### 4.3.2 事务的隔离级别

为了解决上述的并发性问题，SQL92标准给不同的场景提供了不同的隔离级别，可以解决不同的问题：

- Read uncommitted (读未提交)：最低级别，任何情况都无法保证
- Read committed (RC，读已提交)：可避免脏读的发生
- Repeatable read (RR，可重复读)：可避免脏读、不可重复读的发生
- Serializable (串行化)：可避免脏读、不可重复读、幻读的发生

注：以上只是SQL92的标准，但在Mysql中，`InnoDB引擎其实可以通过Next-Key锁解决幻读的问题`。

# 六：InnoDB引擎

### 5.1 InnoDB介绍



### 5.2 InnoDB内存结构



### 5.3 InnoDB磁盘文件



### 5.4 InnoDB的日志文件



### 5.6 InnoDB的MVCC机制



### 5.7 InnoDB事务的实现













# 七：Mysql性能优化

### 6.1 Mysql性能分析思路

Mysql的性能优化可以分为两个方向：

- 应用层关注的延时数据：即SQL执行时间，可以通过慢查询日志、应用层监控收集，相关工具分析优化
- Mysql整体负载数据：实现Mysql和机器硬件的监控，参考负载、tps、io等相关数据，判断是否正常，异常情况进行检测分析

注：此处重点介绍延时数据的优化，Mysql整体的监控和优化参考后续章节。

### 6.2 服务器优化

#### 6.2.1 硬件优化

部署mysql等关系型数据库时，尽量选择较高的配置和固态硬盘，例如8C+16G+500G的SSD。虽然mysql软件层面已经尽可能的减少写盘的随机io操作，但有些无可避免，选择固态盘会比机械盘的随机IO性能高出很多。

注：数据库软件尽量独立部署，避免其他应用与mysql竞争磁盘io资源，导致mysql性能下降。

#### 6.2.2 参数优化

不同的硬件配置、场景、并发对Mysql的参数要求不一致，因此`一般都是根据经验给出一些基础配置，再不断进行压测观察，进行调整`。

Mysql和性能相关的重点参数有以下几类：

- 请求连接相关：max_connections、back_log、wait_timeout和interative_timeout
- 缓存区相关：key_buffer_size、query_cache_size、max_connect_errors、sort_buffer_size、max_allowed_packet、join_buffer_size、thread_cache_size
- InnoDB相关配置：innodb_buffer_pool_size、innodb_flush_log_at_trx_commit、innodb_thread_concurrency、innodb_log_buffer_size、innodb_log_file_size、innodb_log_files_in_group、read_buffer_size、read_rnd_buffer_size、bulk_insert_buffer_size、binary_log

-----------------

https://www.jb51.net/article/144039.htm#_label2

### 6.3 表结构设计优化

#### 6.3.1 范式介绍

设计数据库时，需要遵循的一些规范，`要遵循后边的范式要求，必须先遵循前边的所有范式要求`。`各种范式呈递次规范，越高的范式数据库冗余越小，扩展性越强`。目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF）。在项目开发里，一般遵循前三范式，简称3NF。

注：并非所有情况都需要遵循3NF，范式的出现是为了减少数据库的冗余，提升扩展性，即`会将关联性不强的数据拆分为一个个小表`，业务层通过联结表进行查询，而联结操作是比较慢的，有时候为了提升性能，会`违反3NF，保留一些冗余数据实现性能和业务上的需求`。

#### 6.3.2 第一范式（1NF）

- 定义

第一范式（1NF）：要求数据库表的每一列都是不可分割的原子数据项。

- 示例

![在这里插入图片描述](http://rocks526.top/lzx/ca5c503b7ea44f38926e71af044487b5.png)

上面的表结构设计中将系名和系主任合并为系一个字段，不符合1NF要求。

注：表结构的设计还是要考虑实际场景，如果实际场景这两个字段都是一起出现，也可以作为一个字段存在。

![在这里插入图片描述](http://rocks526.top/lzx/f38cc334c7b84c27987ddc12376e38f6.png)

将系名和系主任两个字段拆分后，即符合1NF规范，其中学号、姓名、系名、系主任、课程名称、分数就是第一范式中的原子数据项。

- 缺点

1. 存在非常严重的数据冗余：姓名、系名、系主任字段
2. 数据添加存在问题：添加新开设的计算机系、系名，数据不合法（没有对应的学生和课程信息，无法添加）
3. 数据删除存在问题：如果学生毕业了，删除杨过同学的数据，系名和系主任都会被删除

#### 6.3.3 第二范式（2NF）

- 定义

第二范式（2NF）：在1NF的基础上，非码属性必须完全依赖于码（在1NF基础上消除非主属性对主属性的部分函数依赖）。

函数依赖：如果通过A属性(属性组)的值，可以确定唯一B属性的值,则称B依赖于A，即：A–>B；例如：学号–>姓名 ；（学号、课程名称）–>分数

完全函数依赖：如果A是一个属性组，则B属性值得确定需要依赖于A属性组中所有的属性值，即A–>B；例如：（学号、课程名称）–>分数

部分函数依赖：如果A是一个属性组，则B属性值得确定只需要依赖于A属性组中某一些值即可，即A–>B；例如：（学号，课程名称） –> 姓名

传递函数依赖：如果通过A属性(属性组)的值，可以确定唯一B属性的值，再通过B属性（属性组）的值可以确定唯一C属性的值，则称 C 传递函数依赖于A，即A–>B, B – >C；例如：学号–>系名，系名–>系主任

码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性(属性组)为该表的码；例如：表中原本设计的码为：（学号，课程名称），但其实不满足2NF要求

主属性：码属性组中的所有属性

非主属性：除了码属性组的属性

注：说人话，码对应表的主键，`2NF的目的就是需要确保数据库表中的每一列都和主键直接相关`，而不能只与主键的某一部分相关（主要针对联合主键而言）或者间接相关。

- 示例

![在这里插入图片描述](http://rocks526.top/lzx/c58e497667a1493aba775827c2420bb1.png)

如上图所示，这张表原本的设计初衷是记录所有学生的课程及分数信息，因此（学号，课程名称）作为码，即主属性，其他字段为非主属性，由于姓名、系名、系主任这三个字段都只依赖于学号，和课程无关，因此属于部分依赖，不符合2NF规范，需要进行拆分。

![在这里插入图片描述](http://rocks526.top/lzx/ac5af2f55fe84d6aa3bcdfc47d178d8c.png)![在这里插入图片描述](http://rocks526.top/lzx/29faa18623f24045a36adecb9bcb4207.png)

- 特点

1. 一张表只描述一件事情
2. 表中的每一列都完全依赖于主键

- 缺点

1. 第二范式解决了第一范式的数据冗余的缺点；
2. 数据添加存在问题：添加新开设的计算机系、系名，数据不合法；
3. 数据删除存在问题：如果学生毕业了，删除杨过同学的数据，系名和系主任都会被删除；

#### 6.3.4 第三范式（3NF）

- 定义

第三范式（3NF）：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递依赖）。

- 示例

在上面的表结构中，选课表中不存在传递依赖；学生表中系主任依赖于系名，系名依赖于学号，因此存在传递依赖，需要消除；因此，创建三个表：选课表、学生表、系表分别如下：

![在这里插入图片描述](http://rocks526.top/lzx/e44578d032304e82976304dffef3b6a4.png)

- 特点

1. 表中每一列都直接依赖于主键
2. 每张表只描述一件事情

- 缺点

1. 数据冗余的缺点被2NF解决
2. 由于关联性不强（非直接依赖）的数据被拆分不同的表，因此新增系和删除学生都没不存在问题

#### 6.3.5 反范式

上图中的3NF看似很完美，但结合实际场景，会存在以下问题：

- 3NF的性能问题

在选课表中，一般业务场景都会查询出学生和名称和对应的课程、分数信息等，3NF的设计由于学生的名称被拆分到学生表，导致每次都需要联表查询，数据库的性能压力会比较大。

为了提高查询性能，一般建议学生姓名信息给课程表里冗余一份，这样查询课程信息这个高频的操作即可单表完成，且学生姓名信息一般很少变动，维护成本也比较低。

- 3NF的业务问题

当系主任发生变化时，学生由于与系主任通过系名关联，会导致每次查询到变更后的系主任信息，如果学生当时已经毕业，相当于他大学时的系主任是A，后来系主任变更为B，会导致查询该学生时发现其当时的系主任也是B，这是业务所不允许的。

注：3NF由于所有信息都通过关联实现，因此没办法存储历史快照记录，上面的需求其实是想要当时的快照信息。针对此类需求，有时候需要冗余部分字段，例如上面的需求就需要将系主任名称冗余一份给学生表。

#### 6.3.6 其他优化

在实际项目开发中，还有一些其他的常见优化手段：

- 对于字段太多的大表，考虑拆表（比如一个表有100多个字段） 
- 对于表中经常不被使用的字段或者存储数据比较多的字段，考虑拆表，比如商品表中的商品介绍字段等。
- 每张表建议都要有一个主键（主键索引），而且主键类型最好是int/bigint类型，建议自增主键

### 6.4 SQL语句优化

- 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引
- 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描（可设置默认为0解决NULL值）
- 应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描
- 应尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描（可通过union解决or拼接）
- in 和 not in 也要慎用，否则会导致全表扫描（连续的值，尽量用between）
- 尽量避免通配符开头的模糊匹配，会导致全表扫描
- 应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描

```mysql
select * from t where num/2=100 ==> select * from t where num=100*2
```

- 应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描
- 不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引
- 在使用索引字段作为条件时，如果该索引是复合索引，那么必须满足左前缀匹配
- 很多时候用 exists 代替 in 是一个好的选择

```mysql
select num from a where num in(select num from b) ==>  select num from a where exists(select 1 from b where num=a.num)
```

- 并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能不会去利用索引
- 索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率

一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有必要。

- 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销

因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了

- 尽可能的使用 varchar 代替 char ，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些
- 任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段
- 避免频繁创建和删除临时表，以减少系统表资源的消耗
- 针对count查询，可以创建一个字段较小的索引，会大大加快查询速度（主键索引存储了数据，导致每页数据较少，会有大量磁盘IO）

### 6.5 New-SQL

上面的优化方向都是基于`单机高性能的方向`，当应用程序的并发和性能要求特别高时，例如淘宝、微信这种应用程序，单机远远无法满足需求，因此出现了各种各样的扩展方案，根据发展历程，主要分为以下这些：

- 传统关系型数据库（SQL）：Mysql、Postgresql、Oracle等，支持ACID特性，适合OLTP场景，但扩展性较差
- 非关系型数据库（NoSQL）：Redis、ES、MogoDB等，这种数据库基于CAP、Base等分布式理论，放弃了对事务ACID的完整支持，从而获得了横向扩展的能力，天然支持分片、副本，可以平滑横向扩容，一般适用于高并发、高性能的非业务数据或者不是很重要的业务数据

注：虽然NoSQL提供了高并发、高性能、高扩展的特性，但不支持事务完整的ACID特性，就决定了NoSQL没办法替代SQL，只能作为SQL的补充扩展存在。

- 传统关系型数据库集群：为了性能，传统数据库大都提供了集群方案，受限于CAP定理和ACID特性，集群方案大多采用主从模型，只能实现高可能和读写分离
- 传统关系型数据库的分库分表方案：虽然集群方案可以实现读写分离，从库能负担读请求压力，提升读的qps，但所有的写请求还是集中于主节点，无法扩展，为了应对写请求的压力，出现了分库分表方案，即在应用层通过规则将写请求路由到多个Mysql集群里，每个集群分担一部分写压力，存储一部分数据

- 新型关系型数据库（New-SQL）：受益于分库分表的思想和分布式共识算法的成熟，逐渐出现了新型关系型数据库，不但支持事务，而且可以和NoSQL一样支持横向扩展，并且大多兼容传统关系型数据库的协议，可以平滑切换，未来会逐渐成为主流。例如：TiDB、OceanBase等

注：新型关系型数据库其实就是参考了NoSQL和分库分表的实现，将分库分表原本的业务侵入逻辑迁移到Server端实现，在Server端加了个Proxy层，而且结合分布式事务，解决分库分表遗留的一些问题。

# 八：Mysql集群

### 7.1 Mysql主从复制



### 7.2 Mysql读写分离









# 九：Mysql监控与基准测试





