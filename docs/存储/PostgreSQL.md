# <a id="js">一：PostgreSQL基础</a>

### 1.1 PostgreSQL介绍

PostgreSQL是一个开源的对象-关系型数据库，具有以下特点：

- 运行平台广：支持众多Linux发行版本和Windows，Unix系统
- 编程接口丰富：支持Java，C，C++，Go，Python，Ruby等
- 支持广泛的数据类型：数组，json，jsonb以及几何类型，还可以使用SQL命令CREATE TYPE创建自定义类型
- 支持大部分SQL标准：支持复杂SQL查询、SQL子查询、Windows Function、统计函数、主键、外键、触发器、视图、物化视图等，可以使用C，Java，Python等多种语言编写存储过程
- 支持并行计算和基于MVVC的多版本并发控制：支持同步、半同步、异步的流复制，支持逻辑复制和订阅，Hot Standby，支持多种数据源的外部表（Foreign data wrappers）
- 支持表继承等特性

目前PostgreSQL最新版本为12.2，官网为https://www.postgresql.org/

### 1.2 PostgreSQL安装

**Windows安装PostgreSQL**

- 下载PostgreSQL的安装程序，推荐exe程序

下载地址：https://www.postgresql.org/download/windows/

- 安装提示选择安装目录，相关工具，密码等信息即可

- 安装完成后，在导航栏搜索PgAdmin即可打开Pg的可视化管理工具

****

**Linux安装PostgreSQL**

- 进入PostgreSQL的下载页面，找到对应系统的rpm包，安装rpm包

```shell
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

- 安装完pg的rpm后，通过yum的install命令安装pg

```shell
yum install postgresql12 -y
yum install postgresql-12-server -y
# 安装完成后，pg可执行文件位于/usr/pgsql-12/bin目录，并且会自动创建一个postgres账户，它的home目录在/var/lib/pgsql
```

- 初始化并启动pg

```shell
/usr/pgsql-12/bin/postgresql-12-setup initdb
systemctl enable postgresql-12
systemctl start postgresql-12
```

- 检测pg状态，显示绿色即pg正常服务

```shell
service postgresql-12 status
```

- 修改postgres用户名密码

```shell
sudo passwd postgres
```

- 修改配置文件，允许远程访问

```shell
# pg的默认配置文件目录在/var/lib/pgsql/12/data/下
cd /var/lib/pgsql/12/data/
vim postgresql.conf
# 添加如下配置
listen_addresses = '\*' 
password_encryption = md5

vim /var/lib/pgsql/12/data/pg_hba.conf
# 添加如下配置
host    all             all             0.0.0.0/0               md5

# 重启服务器
systemctl restart postgresql-12
```

- 创建用户名和数据库

```shell
# 切换用户
su postgres
# 登录pg
/usr/pgsql-12/bin/psql
# 创建用户
create user lizhaoxuan with password '123';
# 创建数据库
create database test with encoding='utf8' owner='lizhaoxuan';
```

- 添加环境变量

```shell
vim /home/postgres/.bash_profile
# 添加如下内容
PG_BIN=/usr/pgsql-12/bin
export PG_BIN
# 立即生效
source /home/postgres/.bash_profile
```

### 1.3 PstgreSQL目录结构

- 安装目录：/usr/pgsql-12
  - bin：二进制可执行文件目录
  - doc：说明文档
  - lib：动态库目录
  - share：目录下放有文档和配置模板文件

- bin常用脚本
  - 





# <a id="qb">PostgreSQL VS MySQL</a>









# <a id="sb">SpringBoot操作PostgreSQL</a>







# <a id = "py">Python操作PostgreSQL</a>


- 安装Python操作pg的包：psycopg2

> pip install psycopg2

- 操作示例

```python
import psycopg2

# 创建连接对象
conn = psycopg2.connect(database="postgres", user="postgres", password="123456", host="localhost", port="5432")
# 获取游标
cur = conn.cursor()
# 执行SQL
cur.execute("select * from user_info")
# 获取结果 返回元组
res = cur.fetchall()
print(res)
# 提交事务
conn.commit()
# 关闭连接
cur.close()
conn.close()

[(1, 'Jack', '123456', '17629154002'), (2, 'Jack2', '123456', '17629154003'), (3, 'Jack3', '123456', '17629154004'), (4, 'Jack4', '123456', '17629154005'), (5, 'Jack5', '123456', '17629154006'), (6, 'unknow', '123456', '17629154008')]
```


