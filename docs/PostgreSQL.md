- [PostgreSQL介绍](#js)
- [PostgreSQL安装](#az)
- [PostgreSQL VS MySQL](#qb)
- [SpringBoot操作PostgreSQL](#sb)



# <a id="js">PostgreSQL介绍</a>

PostgreSQL是一个开源的对象-关系型数据库，具有以下特点：

- 运行平台广：支持众多Linux发行版本和Windows，Unix系统
- 编程接口丰富：支持Java，C，C++，Go，Python，Ruby等
- 支持广泛的数据类型：数组，json，jsonb以及几何类型，还可以使用SQL命令CREATE TYPE创建自定义类型
- 支持大部分SQL标准，支持复杂SQL查询，SQL子查询，Windows Function，统计函数，主键，外键，触发器，视图，物化视图等，可以使用C，Java，Python等多种语言编写存储过程
- 支持并行计算和基于MVVC的多版本并发控制，支持同步，半同步，异步的流复制
- 支持表继承

目前PostgreSQL最新版本为12.2，官网为https://www.postgresql.org/

# <a id="az">PostgreSQL安装</a>

**Windows安装PostgreSQL**

- 下载PostgreSQL的安装程序，推荐exe程序

下载地址：https://www.postgresql.org/download/windows/

- 安装提示选择安装目录，相关工具，密码等信息即可

- 安装完成后，在导航栏搜索PgAdmin即可打开Pg的可视化管理工具

****

**Linux安装PostgreSQL**

- 进入PostgreSQL的下载页面，找到对应系统的rpm包，安装rpm包

> yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

- 安装完pg的rpm后，通过yum的install命令安装pg

> yum install postgresql12
>
> yum install postgresql-12-server
>
> 安装完成后，pg可执行文件位于/usr/pgsql-12/bin目录，并且会自动创建一个postgres账户，它的home目录在/var/lib/pgsql

- 初始化并启动pg

> /usr/pgsql-12/bin/postgresql-12-setup initdb
>
> systemctl enable postgresql-12
>
> systemctl start postgresql-12

- 检测pg状态，显示绿色即pg正常服务

> service postgresql-12 status

- 修改postgres用户名密码

> sudo passwd postgres

- 修改配置文件，允许远程访问

> pg的默认配置文件目录在/var/lib/pgsql/12/data/下
>
>  cd /var/lib/pgsql/12/data/
>
> vim postgresql.conf
>
>  listen_addresses = '\*' 
>
>  password_encryption = md5

# <a id="qb">PostgreSQL VS MySQL</a>









# <a id="sb">SpringBoot操作PostgreSQL</a>