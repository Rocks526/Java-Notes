# 一：Jdbc介绍

### 1.1 Jdbc简介

JDBC（Java Database Connectivity）是Java语言中提供的访问关系型数据的接口。

在Java编写的应用中，使用JDBC API可以执行SQL语句、检索SQL执行结果以及将数据更改写回到底层数据源。JDBC API也可以用于分布式、异构的环境中与多个数据源交互。

JDBC API基于X/Open SQL CLI，是ODBC的基础。JDBC提供了一种自然的、易于使用的Java语言与数据库交互的接口。

自1997年1月Java语言引入JDBC规范后，JDBC API被广泛接受，并且广大数据库厂商开始提供JDBC驱动的实现。JDBC API为Java程序提供了访问一个或多个数据源的方法。在大多数情况下，数据源是关系型数据库，它的数据是通过SQL语句来访问的。当然，使用JDBC访问其他数据源（例如文件系统或者面向对象系统等）也是有可能的，只要该数据源提供JDBC规范驱动程序即可。

### 1.2 Jdbc的优点

在开发者和的数据库之间加一层代理，统一数据库的访问范式，不同的数据库实现交给数据库厂商，减轻开发者负担。

### 1.3 Jdbc快速入门

#### 1.3.1 代码示例

```java
package com.lizhaoxuan.jdbc;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

/**
 * JDBC示例
 * @author lizhaoxuan
 * @date 2021/8/11
 */
@Slf4j
public class QuickStart {

    @SneakyThrows
    public static void main(String[] args) {
        // 加载驱动 Jdbc3之后通过SPI机制加载类，可无须手动加载
        Class.forName("org.postgresql.Driver");
        // 获取连接器
        Connection connection = DriverManager.getConnection("jdbc:postgresql://127.0.0.1:5432/ngsoc", "postgres", "aaa");
        // 获取SQL执行器
        Statement statement = connection.createStatement();
        // 执行SQL 获取结果集
        ResultSet resultSet = statement.executeQuery("select * from work_order_field");
        // 解析结果集
        while (resultSet.next()){
            log.info("ID={},字段名字={},字段意义={},字段类型={}!",resultSet.getLong("id"),resultSet.getString("field"),resultSet.getString("label"),resultSet.getString("field_type"));
            log.info("ID={},字段名字={},字段意义={},字段类型={}!",resultSet.getLong(1),resultSet.getString(2),resultSet.getString(3),resultSet.getString(4));
        }
        // 关闭连接
        resultSet.close();
        statement.close();
        connection.close();
    }

}
```

#### 1.3.2 加载驱动

在Jdbc3之前，需要显式的加载各个厂商的Driver实现类，此类实现java.sql.Driver接口，定义了连接数据库服务端、获取版本的方法，同时在静态代码块里将自身注册给DriverManager。

> 在Jdbc3之后，Java程序会通过SPI机制自动加载classpath下的所有驱动类，自动完成注册。

#### 1.3.3 建立数据库连接

JDBC API中定义了Connection接口，用来表示与底层数据源的连接。JDBC应用程序可以使用以下两种方式获取Connection对象：

- DriverManager：这是一个在JDBC 1.0规范中就已经存在、完全由JDBC API实现的驱动管理类，提供了一系列重载的getConnection()方法

- DataSource：这个接口是在JDBC 2.0规范可选包中引入的API。它比DriverManager更受欢迎，因为它提供了更多底层数据源相关的细节，而且对应用来说，不需要关注JDBC驱动的实现。

> 相比DriverManager获取连接需要指定URL，DataSource不需要硬编码指定，切换数据源时，修改DataSource配置文件即可。
>
> 需要注意的是，JDBC API中只提供了DataSource接口，没有提供DataSource的具体实现，DataSource具体的实现由JDBC驱动程序提供。另外，目前一些主流的数据库连接池（例如DBCP、C3P0、Druid等）也提供了DataSource接口的具体实现。

JDBC API中定义了两个DataSource接口比较重要的扩展，用于支撑企业级应用。这两个接口分别为：

- ConnectionPoolDataSource：支持缓存和复用Connection对象，这样能够在很大程度上提升应用性能和伸缩性。

- XADataSource：该实例返回的Connection对象能够支持分布式事务。

#### 1.3.4 获取SQL执行器

JDBC API提供了访问SQL：2003规范中常用的实现特性，因为不同的JDBC厂商对这些特性的支持程度各不相同，所以JDBC API中提供了一个DatabaseMetadata接口，应用程序可以使用DatabaseMetadata的实例来确定目前使用的数据源是否支持某一特性。

```java
DatabaseMetaData metaData = connection.getMetaData();
```

获取到JDBC中的Connection对象之后，我们可以通过Connection对象设置事务属性，并且可以通过Connection接口中提供的方法创建Statement、PreparedStatement或者CallableStatement对象。

Statement接口可以理解为JDBC API中提供的SQL语句的执行器，有以下常用方法：

- executeQuery()：执行查询操作，返回ResultSet

- executeUpdate()：执行更新操作，返回影响行数

- executeBatch()：批量操作，返回每个SQL的影响行数

- execute()：当不知道操作类型时使用此方法，通过bool返回值确定操作类型，然后通过getResultSet()方法来获取查询结果集，或者通过getUpdateCount()方法来获取更新操作影响的行数

#### 1.3.5 解析结果集

JDBC API中提供了ResultSet接口，该接口的实现类封装SQL查询的结果，我们可以对ResultSet对象进行遍历，然后通过ResultSet提供的一系列getXXX()方法（例如getString）获取查询结果集。

> ResultSet对象的getMetaData()方法获取结果集元数据信息。该方法返回一个ResultSetMetaData对象，我们可以通过ResultSetMetaData对象获取结果集中所有的字段名称、字段数量、字段数据类型等信息。

# 二：Jdbc核心对象介绍

### 2.1 Jdbc API 

JDBC API由`java.sql`和`javax.sql`两个包构成，主要类如下：

#### 2.1.1 java.sql包

```java
// 数据类型
java.sql.Array
java.sql.Blob
java.sql.Clob
java.sql.NClob
java.sql.Struct
java.sql.Date
java.sql.Time
java.sql.Timestamp
java.sql.SQLXML
java.sql.Ref
java.sql.RowId
java.sql.SQLInput
java.sql.SQLOutput
java.sql.SQLData
// 枚举类
java.sql.SQLType
java.sql.JDBCType
java.sql.Types
java.sql.RowIdLifetime
java.sql.PseudoColumnUsage
java.sql.ClientInfoStatus
// API相关
java.sql.Wrapper
java.sql.Connection
java.sql.Statement
java.sql.CallableStatement
java.sql.PreparedStatement
java.sql.DatabaseMetaData
java.sql.ParameterMetaData
java.sql.ResultSet
java.sql.ResultSetMetaData
// 驱动相关
java.sql.Driver
java.sql.DriverAction
java.sql.DriverManager
java.sql.DriverPropertyInfo
java.sql.SQLPermission
java.sql.Savepoint
// 异常
java.sql.BatchUpdateException
java.sql.DataTruncation
java.sql.SQLClientInfoException
java.sql.SQLDataException
java.sql.SQLException
java.sql.SQLFeatureNotSupportedException
java.sql.SQLIntegrityConstraintViolationException
java.sql.SQLInvalidAuthorizationSpecException
java.sql.SQLNonTransientConnectionException
java.sql.SQLNonTransientException
java.sql.SQLRecoverableException
java.sql.SQLTimeoutException
java.sql.SQLTransientException
```

API相关的接口都继承了java.sql.Wrapper接口。许多JDBC驱动程序提供超越传统JDBC的扩展，为了符合JDBC API规范，驱动厂商可能会在原始类型的基础上进行包装，`Wrapper接口为使用JDBC的应用程序提供访问原始类型的功能`，从而使用JDBC驱动中一些非标准的特性。

java.sql.Wrapper接口提供了两个方法：

- unwrap()：用于返回未经过包装的JDBC驱动原始类型实例，我们可以通过该实例调用JDBC驱动中提供的非标准的方法。

- isWrapperFor()：用于判断当前实例是否是JDBC驱动中某一类型的包装类型。

```java
Statement stm = conn.getStatement();
Class clazz = Class.forName("oracle.jdbc.OracleStatement");
if (stm.isWrapperFor(clazz)){
    OracleStatement os = stmt.unwrap(clazz);
    // 使用OracleStatement的特性
    // ..................
}
```

#### 2.1.2 javax.sql包

javax.sql包中的类和接口最早是由JDBC 2.0版本的可选包提供的，主要包括下面几个类和接口：

```java
// 数据源
javax.sql.DataSource
javax.sql.CommonDataSource
// 连接池相关
javax.sql.ConnectionPoolDataSource
javax.sql.PooledConnection
javax.sql.ConnectionEvent
javax.sql.ConnectionEventListener
javax.sql.StatementEvent
javax.sql.StatementEventListener
// ResultSet扩展
javax.sql.RowSet
javax.sql.RowSetEvent
javax.sql.RowSetInternal
javax.sql.RowSetListener
javax.sql.RowSetMetaData
javax.sql.RowSetReader
javax.sql.RowSetWriter
// 分布式扩展
javax.sql.XAConnection
javax.sql.XADataSource
```

相比java.sql.DriverManager获取驱动需要硬编码URL，DataSource更加灵活。开发人员可以选择通过JNDI注册这个数据源对象，然后在程序中使用一个逻辑名称来引用它，JNDI会自动根据我们给出的名称找到与这个名称绑定的DataSource对象。

使用DataSource接口的第二个优势体现在连接池和分布式事务上。

javax.sql包下还提供了一个PooledConnection接口。PooledConnection和Connection的不同之处在于，它提供了连接池管理的句柄，可以用于返回连接池而不是释放连接。

> 应用程序开发人员一般不会直接使用PooledConnection接口，而是通过一个管理连接池的中间层基础设施使用。
>
> 连接池实现模块可以调用PooledConnection对象的addConnectionEventListener()将自己注册成为一个PooledConnection对象的监听者，当数据库连接需要重用或关闭的时候会产生一个ConnectionEvent对象。

javax.sql包中还包含XADataSource、XAResource和XAConnection接口，这些接口提供了分布式事务的支持，具体由JDBC驱动来实现。

> XAConnection接口继承了PooledConnection接口，因此它具有所有PooledConnection的特性。

javax.sql包中还提供了一个RowSet接口，该接口继承自java.sql包下的ResultSet接口。RowSet用于为数据源和应用程序在内容中建立一个映射。

RowSet对象可以建立一个与数据源的连接并在其整个生命周期中维持该连接，在这种情况下，该对象被称为连接的RowSet。RowSet对象还可以建立一个与数据源的连接，从其获取数据，然后关闭它，这种RowSet被称为非连接RowSet。非连接Rowset可以在断开时更改其数据，然后将这些更改写回底层数据源，不过它必须重新建立连接才能完成此操作。

> 相较于java.sql.ResultSet而言，RowSet的离线操作能够有效地利用计算机越来越充足的内存减轻数据库服务器的负担。由于数据操作都是在内存中进行的，然后批量提交到数据源，因此灵活性和性能都有了很大的提高。
>
> RowSet默认是一个可滚动、可更新、可序列化的结果集，而且它作为一个JavaBean组件，可以方便地在网络间传输，用于两端的数据同步。

### 2.2 Connection

#### 2.2.1 Connection定义

一个Connection对象表示通过JDBC驱动与数据源建立的连接，这里的数据源可以是关系型数据库管理系统（DBMS）、文件系统或者其他通过JDBC驱动访问的数据。

我们可以通过两种方式获取JDBC中的Connection对象：

- 通过JDBC API中提供的DriverManager类获取。

- 通过DataSource接口的实现类获取。

> 使用DataSource的具体实现获取Connection对象是比较推荐的一种方式，因为它增强了应用程序的可移植性，使代码维护更加容易，并且使应用程序能够透明地使用连接池和处理分布式事务。几乎在所有的Java EE项目中都是使用DataSource的具体实现来维护应用程序和数据库连接的。目前使用比较广泛的数据库连接池C3P0、DBCP、Druid等都是javax.sql.DataSource接口的具体实现。

#### 2.2.2 Jdbc驱动类型

- JDBC-ODBC Bridge Driver：JDBC-ODBC的桥接驱动，利用现成的ODBC架构将JDBC调用转换为ODBC调用，避免了当时JDBC无驱动可用的窘境。
- Native API Driver：这类驱动程序会直接调用数据库提供的原生链接库或客户端，因为没有中间过程，访问速度通常表现良好，但是驱动程序与数据库和平台绑定无法达到JDBC跨平台的基本目的。

- JDBC-Net Driver：这类驱动程序会将JDBC调用转换为独立于数据库的协议，然后通过特定的中间组件或服务器转换为数据库通信协议，主要目的是获得更好的架构灵活性。
- Native Protocol Driver：这是最常见的驱动程序类型，开发中使用的驱动包基本都属于此类，通常由数据库厂商直接提供，例如mysql-connector-java，驱动程序把JDBC调用转换为数据库特定的网络通信协议，使用网络通信，驱动程序可以纯Java实现，支持跨平台部署，性能也较好。

#### 2.2.3 java.sql.Driver接口

所有的JDBC驱动都必须实现Driver接口，而且实现类必须包含一个静态初始化代码块。

类的静态初始化代码块会在类初始化时调用，驱动实现类需要在静态初始化代码块中向DriverManager注册自己的一个实例。

```javaa
            Driver registeredDriver = new Driver();
            DriverManager.registerDriver(registeredDriver);
            Driver.registeredDriver = registeredDriver;
```

> 为了确保驱动程序可以使用这种机制加载，Driver实现类需要提供一个无参数的构造方法。
>
> JDBC 4.0以上的版本对DriverManager类的getConnection()方法做了增强，可以通过Java的SPI机制加载驱动。
>
> 符合JDBC 4.0以上版本的驱动程序的JAR包中必须存在一个META-INF/services/java.sql.Driver文件，在java.sql.Driver文件中必须指定Driver接口的实现类。

DriverManager类与注册的驱动程序进行交互时会调用Driver接口中提供的方法。

Driver接口中提供了一个acceptsURL()方法，DriverManager类可以通过Driver实现类的acceptsURL()来判断一个给定的URL是否能与数据库成功建立连接。当我们试图使用DriverManager与数据库建立连接时，会调用Driver接口中提供的connect()方法。

#### 2.2.4 java.sql.DriverAction接口

Driver实现类在被加载时会调用DriverManager类的registerDriver()方法注册驱动，我们也可以在应用程序中显式地调用

DriverManager类的deregisterDriver()方法来解除注册。

JDBC驱动可以通过实现DriverAction接口来监听DriverManager类的deregisterDriver()方法的调用。

#### 2.2.5 java.sql.DriverManager类

DriverManager类通过Driver接口为JDBC客户端管理一组可用的驱动实现，当客户端通过DriverManager类和数据库建立连接时，

DriverManager类会根据getConnection()方法参数中的URL找到对应的驱动实现类，然后使用具体的驱动实现连接到对应的数据库。

DriverManager类提供了两个关键的静态方法：

- registerDriver()：该方法用于将驱动的实现类注册到DriverManager类中
- getConnection()：这个方法是提供给JDBC客户端调用的，可以接收一个JDBC URL作为参数，DriverManager类会对所有注册驱动进行遍历，调用Driver实现的connect()方法找到能够识别JDBC URL的驱动实现后，会与数据库建立连接，然后返回Connection对象

#### 2.2.6 javax.sql.DataSource接口

javax.sql.DataSource接口最早是由JDBC 2.0版本扩展包提供的，它是比较推荐的获取数据源连接的一种方式，使用DataSource对象可以提高应用程序的可移植性。在应用程序中，可以通过逻辑名称来获取DataSource对象，而不用为特定的驱动指定特定的信息。我们可以使用JNDI（Java Naming and Directory Interface）把一个逻辑名称和数据源对象建立映射关系。

DataSource接口其他能力：

- 通过连接池提高系统性能和伸缩性。
- 通过XADataSource接口支持分布式事务。

#### 2.2.7 关闭Connection对象

当我们使用完Connection对象后，需要显式地关闭该对象。Connection接口中提供了以下方法：

- close()：调用该方法后，由该Connection对象创建的所有Statement对象都会被关闭。一旦Connection对象关闭后，调用Connection的常用方法都将会抛出SQLException异常。
- isClosed()：Connection接口中提供的isClosed()方法用于判断应用中是否调用了close()方法关闭该Connection对象
- isValid()：Connection接口提供的isValid()方法用于判断连接是否有效。当该方法返回false时，调用除了close()、isClosed()、isValid()以外的其他方法将会抛出SQLException异常。

> 有些JDBC驱动实现厂商对isClosed()方法做了增强，可以用它判断数据库连接是否有效。
>
> 但是为了程序的可移植性，需要判断连接是否有效时还是建议使用isValid()方法。

### 2.3 Statement

Statement接口中定义了执行SQL语句的方法，这些方法不支持参数输入，PreparedStatement接口中增加了设置SQL参数的方法，CallableStatement接口继承自PreparedStatement，在此基础上增加了调用存储过程以及检索存储过程调用结果的方法。













### 2.4 ResultSet



### 2.5 DatabaseMetaData



### 2.6 JDBC事务







# 三：Jdbc响应式编程









# 四：SPI机制







# 五：JNDI机制





 









### 江苏问题整理

#### 需求侧

- 需求输入端太多，不同警员不同诉求，在尚未完全确定之前就要求研发上线，导致需求频繁变更
- 客户需求奇特，很多和SOC已有功能冲突，例如客户要求的数据权限和SOC的分权分域等，导致改造难度大
- 客户需求太零碎，例如UI样式、下拉框输入框、页面关联跳转等，不排除有实用价值的需求，但SOC已有自己标准，改造费时费力，也未必满足客户心意

#### 研发侧

- 前期缺乏架构设计，包括组织、警员等和SOC冲突很大，代码一堆屎山
- 需求频繁变更，代码也就随之拆东墙补西墙，大量屎山
- 每次上线前还在不断换包，上线新功能，导致上线时bug不可控
- 定制代码太多且101未封版，common，user，gateway，cooperation，asset，而且jar包和war混合，导致维护很麻烦

#### 测试侧

- 开发和测试并行，导致测试提出的bug很大一部分都是换包等导致，降低效率和研发的信任度
- 测试缺少计划、范围、用例，无法系统性测试，每次都是新功能测试，缺少对历史功能的回归测试，导致出现一些新功能引发的bug

### 建议

- 对警员、组织、权限重新进行架构设计，与soc融合
- common包、user等一些可以合入主线的代码最好合入，或者督促主线优化性能，减少我们维护的工作量
- 在可能的情况下，和客户各方确定好需求再输入，反复返工很影响效率
- 在可能的情况下，去掉一些鸡毛蒜皮的非功能性需求，这些东西非标，无法合入主线，改造费时费力，也很难达到客户的心理预期
- 研发和测试分离，以3-5天为单位提交一批实现的需求，测试需要给出详细的测试用例，除了新需求的测试之外，每轮都需要回归测试，上线正式环境之前，至少经过一轮回归测试（对应的给客户承诺的上线时间就不能以天为单位排期，需要以3-5天为单位排期）

