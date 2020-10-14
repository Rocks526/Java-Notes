Redis基础](#rm)
- [Redis介绍](#rm-js)
- [Redis安装](#rm-az)
- [Redis数据类型](#rm-sjlx)
- [Redis事务](#rm-sw)
- [Redis生存时间](#rm-scsj)
- [Redis排序](#rm-px)
- [Redis发布订阅](#rm-fbdy)
- [Redis管道](#rm-gd)
- [Redis脚本](#rm-jb)
- [Redis协议](#rm-xy)
- [Redis持久化](#rm-cjh)
- [Redis安全](#rm-aq)
- [Redis集群](#rm-jq)
- [Redis架构设计](#rm-jgsj)
- [Redis高性能](#rm-gxn)
- [Redis优化](#rm-yh)
- [Redis可视化工具](#rm-kshgj)

- [Redis客户端](#khd)
  - [redis-java-client](#redis-java-client)
  - [Jedis](#jedis)
  - [Redisson](#redisson)
  - [Spring Data Redis](#springdataredis)
- [Redis应用](#yy)
  - [计数器](#jsq)
  - [排行榜](#phb)
  - [布隆过滤器](#blglq)
  - [Session共享](#session)
  - [MQ](#mq)
  - [缓存](#hc)
  - [分布式锁](#fbss)
  - [注册中心](#zczx)
- [Redis版本](#bb)

# <a id="rm">Redis基础</a>

### <a id="rm-js">Redis介绍</a>

- Redis简介

Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker. It supports data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes with radius queries and streams. Redis has built-in replication, Lua scripting, LRU eviction, transactions and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster.

> Redis是一个C语言编写的，基于内存的高性能开源数据库，可应用于缓存，MQ等。
>
> Redis支持丰富的数据结构，包括字符串，列表，哈希表，集合，有序集合，bitmaps，hyperloglogs，GEO等。
>
> Redis支持复制，Lua脚本，LRU，事务，持久化等机制，同时提供Redis Sentinel和Redis Cluster两种集群方案。

Redis官网：https://redis.io/

- Redis历史

2008年，意大利的一家创业公司Merzia推出了一款基于MySQL的网站实时统计系统LLOOGG，然而没过多久该公司的创始人Salvatore Sanfilippo便开始对MySQL的性能感到失望，于是他决定亲自为LLOOGG量身定做一个数据库，并于2009年开发完成，这个数据库就是Redis。不过SalvatoreSanfilippo并不满足只将Redis用于LLOOGG这一款产品，而是希望让更多的人使用它，于是在同一年Salvatore Sanfilippo将Redis开源发布，并开始和Redis的另一名主要的代码贡献者Pieter Noordhuis一起继续着Redis的开发，~~直到今天~~。

> Redis是REmote DIctionary Server（远程字典服务器）的缩写，它以字典结构存储数据，并允许其他应用通过TCP协议读写字典中的内容。

- Redis特点
  - 丰富的数据类型
  - 基于内存存储，性能高
  - 提供AOF和RDB两种持久化方案
  - 功能丰富，提供了TTL，阻塞队列，除了数据库，还适合做缓存，MQ
  - 简单稳定，~~整个项目代码三万行~~，语法简单
  - 高可用，分布式支持好

### <a id="rm-az">Redis安装</a>

> 安装Redis是开始Redis学习之旅的第一步。在安装Redis前需要了解Redis的版本规则以选择最适合自己的版本，Redis约定次版本号（即第一个小数点后的数字）为偶数的版本是稳定版（如2.4版、2.6版），奇数版本是非稳定版（如2.5版、2.7版），推荐使用稳定版本进行开发和在生产环境使用。

- Linux安装Redis

1. 下载Redis源码包

> wget http://download.redis.io/releases/redis-6.0.8.tar.gz

2. 解压

> tar -zxvf redis-6.0.8.tar.gz 

3. 编译源代码

> make
>
> Redis是C语言写的，需要C语言运行环境，如果没有，需要安装gcc
>
> yum install gcc-c++

4. 安装Redis

> make install PREFIX=/home/rocks/redis/redis608

5. 启动Redis

> cd /home/rocks/redis/redis680/bin
>
> ./redis-server

![image-20200915174004274](http://rocks526.top/lzx/image-20200915174004274.png)

6.  此方式是交互式启动Redis，通过另开一个linux窗口启动redis客户端

> cd /home/rocks/redis/redis680/bin
>
> ./redis-cli

![image-20200915174106010](http://rocks526.top/lzx/image-20200915174106010.png)

7. 后台启动Redis

> 将redis配置文件拷贝到redis的bin目录下
>
> cp /home/rocks/redis/redis-6.0.8/redis.conf /home/rocks/redis/redis608/bin/
>
> 修改配置文件
>
> daemonize yes  开启后台守护线程
>
> bind 0.0.0.0  允许外网访问 默认只能本地访问
>
> 修改完成后启动Redis
>
> ./redis-server redis.conf

8. Redis设置密码

> 登入redis客户端
>
> ./redis-cli
>
> 查询密码
>
> config get requirepass
>
> 设置密码为123456
>
> config set requirepass 123456

9. 停止Redis

> ./redis-cli shutdown
>
> kill redis的pid
>
> 以上两种方式会安全关闭Redis，会等同步完的数据全部写入磁盘
>
> kill -9 redis的pid可能导致数据丢失

![image-20200915175503460](http://rocks526.top/lzx/image-20200915175503460.png)

- 安装过程出现的问题

> make[1]: *** [server.o] Error 1 make[1]: Leaving directory `/home/rocks/redis/redis-6.0.8/src
>
> 需要cd 到redis的src目录下进行make
>
> error: ‘struct redisServer’ has no member named ‘server_cpulist’
>
> 需要gcc环境版本问题导致的，重新安装更新gcc环境
>
> 1、安装gcc套装：
>
> yum install cpp 
>
> yum install binutils 
>
> yum install glibc 
>
> yum install glibc-kernheaders 
>
> yum install glibc-common 
>
> yum install glibc-devel 
>
> yum install gcc 
>
> yum install make 
>
> 2、升级gcc 
>
> yum -y install centos-release-scl 
>
> yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils 
>
> scl enable devtoolset-9 bash 
>
> 3、执行完上述命令后再次make即可。

- Redis官方不支持在Windows平台安装，可以通过虚拟机或者Docker实现Windows安装Redis

1. 在docker官方仓库查找redis镜像

> docker search redis

![image-20200915180215598](http://rocks526.top/lzx/image-20200915180215598.png)

2. 拉取redis-6.0.8的镜像

> docker pull redis:6.0.8

3. 利用redis镜像构建容器，运行redis，映射6379端口

> docker run -di -p 6379:6379 --name=redis6.0.8 redis:6.0.8

- 检查Redis版本号

> ./redis-server -v

- Redis自带的工具

![image-20200928152047750](http://rocks526.top/lzx/image-20200928152047750.png)

|                 |                                      |                      |
| :-------------: | :----------------------------------: | :------------------: |
|     文件名      |                 描述                 |         备注         |
|  redis-server   |             redis服务端              |                      |
| redis-sentinel  |            Redis Sentinel            | redis-server的软连接 |
|    redis-cli    |           Redis命令行工具            |                      |
| redis-check-rdb |          Redis RDB检查工具           |                      |
| redis-check-aof | Redis Append Only Files(AOF)检查工具 |                      |
| redis-benchmark |        Redis基准/性能测试工具        |                      |

- Redis状态监控命令——info

进入redis客户端，info命令可以查看整个Redis的状态，包含以下几部分：

|  段落名称   |            描述            |
| :---------: | :------------------------: |
|   server    | 关于Redis服务器的基本信息  |
|   Clients   |   客户端连接的状态和指标   |
|   Memory    |     大致的内存消耗指标     |
| Persistemce | 数据持久化相关的状态和指标 |
|    Stats    |        总体统计数据        |
| Replication |  主从复制相关的状态和指标  |
|     CPU     |        CPU使用情况         |
|   Cluster   |    Redis Cluster的状态     |
|  Keyspace   |    数据库相关的统计数据    |

也可以指定查询某个部分的信息，如info server => 查询server的信息

Redis监控相关参考文档：https://www.cnblogs.com/liujunwei/p/11816899.html

- Redis配置

Redis支持三种配置方式：

第一种是启动时通过参数进行配置，如./redis-server --port 6379

第二种是修改配置文件，启动时指定配置文件，如./redis-server /usr/local/redis.conf

第三种是通过Config Set命令动态修改配置，可以修改部分配置，如：config set loglevel warning修改日志等级

![image-20200915193701111](http://rocks526.top/lzx/image-20200915193701111.png)

- Redis的多数据库

> Redis是一个字典结构的存储服务器，而实际上一个Redis实例提供了多个用来存储数据的字典，客户端可以指定将数据存储在哪个字典中。这与我们熟知的在一个关系数据库实例中可以创建多个数据库类似，所以可以将其中的每个字典都理解成一个独立的数据库。
>
> 每个数据库对外都是以一个从0开始的递增数字命名，Redis默认支持16个数据库，可以通过配置参数databases来修改这一数字。客户端与Redis建立连接后会自动选择0号数据库，不过可以随时使用SELECT命令更换数据库。
>
> 然而这些以数字命名的数据库又与我们理解的数据库有所区别。首先Redis不支持自定义数据库的名字，每个数据库都以编号命名，开发者必须自己记录哪些数据库存储了哪些数据。另外Redis也不支持为每个数据库设置不同的访问密码，所以一个客户端要么可以访问全部数据库，要么连一个数据库也没有权限访问。最重要的一点是多个数据库之间并不是完全隔离的，比如FLUSHALL命令可以清空一个Redis实例中所有数据库中的数据。综上所述，这些数据库更像是一种命名空间，而不适宜存储不同应用程序的数据。比如可以使用0号数据库存储某个应用生产环境中的数据，使用1号数据库存储测试环境中的数据，但不适宜使用0号数据库存储A应用的数据而使用1号数据库存储B应用的数据，不同的应用应该使用不同的Redis实例存储数据。由于Redis非常轻量级，一个空Redis实例占用的内存只有1MB左右，所以不用担心多个Redis实例会额外占用很多内存。

### <a id="rm-sjlx">Redis的数据类型</a>

> Redis官网数据类型参考文档：https://redis.io/topics/data-types-intro

#### Redis基本数据类型

- 字符类型（String）：最基本的数据类型，二进制安全，一个字符类型的key默认存储的最大容量是512M
- 散列类型（Hash）：String元素组成的字典，适合存储对象。
- 列表类型（List）：列表，按照String元素插入的顺序排序，常用的操作是向列表两端添加元素，或者获得列表的某一个片段。列表内部使用双向链表实现，所以添加删除元素快，查询慢。
- 集合类型（Set）：String元素组成的无序集合，不允许重复。集合底层采用散列表实现，所以加入删除元素和判断某个元素是否存在等速度非常快，并且集合支持并集，交集，差集运算。
- 有序集合（Sorted Set）：底层使用散列表和跳跃表，在集合的基础上为集合中的每个元素关联上一个分数，使得我们不但可以完成插入、删除和判断元素是否存在等集合类型支持的操作，还能够获得分数最高（最低）的前N个元素，获得指定分数范围内的元素等操作。集合中的元素不可重复，但分数可以相同。

#### Redis高级数据类型

- HyperLogLog

基于HyperLogLog算法：以极小的空间完成独立数量的统计，底层通过字符串实现。存在部分精度误差，0.81%。

比Set更节省空间，相比位图，位图需要提前置入所有用户量，当用户量比较小时，位图并不适合。

场景：需要统计网页每天的用户访问量的数据，同一个用户一天之内的多次访问请求只能计数一次，要求每一个网页请求都需要带上用户的 ID，无论是登陆用户还是未登陆用户都需要一个唯一 ID 来标识。

- Geo

地理位置信息定位：存储经纬度信息，计算两地距离，范围计算等。

底层采用有序集合实现。

- Bitmaps

Redis提供的Bitmaps这个“数据结构”可以实现对位的操作。Bitmaps本身不是一种数据结构，实际上就是字符串，但是它可以对字符串的位进行操作。

可以把Bitmaps想象成一个以位为单位数组，数组中的每个单元只能存0或者1，数组的下标在bitmaps中叫做偏移量。单个bitmaps的最大长度是512MB，即2^32个比特位。

> 应用：
>
> - 统计N天内访问网站的用户，连续N天访问网站的用户
>
> 将每个用户的id对应bitmaps上的一个下标，通过对活跃用户对应的位进行置位，就能用一个value记录所有的活跃用户信息。
>
> 我们将每天访问网站的用户存储在一个bitmaps中，通过日期生成键，例如：unique:users:2017-07-11,这样我们就可以统计每天的用户访问情况，同时通过bitmaps的与或运算等功能统计出一个时间段内的活跃用户及连续N天访问网站用户等相关需求。
>
> 当记录的活跃用户数占总数的比例高的时候，bitmaps相比集合类型要节省非常多的内存，但是如果每天的活跃用户数很少，则bitmaps并不试用。下面以1亿用户，每天分别有5千万和10万来具体分析：
>
> ![image-20200918174946637](http://rocks526.top/lzx/image-20200918174946637.png)

- Stream

 Stream是Redis 5.0引入的一种新数据类型，允许消费者等待生产者发送的新数据，还引入了Kakfa类似的消费者组概念。在某些特定场景可以使用redis的stream代替kafka等消息队列，减少系统复杂性，增强系统的稳定性。

相比Redis的list类型，提供了更加强大的API。

> redis5引入了消费者组的概念，一个stream的数据每一个消费者组都发一份，消费者组里面的消费者竞争同一份数据，亦即在同一个消费者组内，一个消息是不可能发给多个消费者的。

#### Redis命令

- 通用命令

keys *：查询所有key 支持通配符

dbsize：查询现在Redis一共有多少个key

exists key：判断key是否存在

del key [key...]：删除key、批量删除key

expire key seconds：给key设置过期时间

ttl key：查询key过期时间

type key：查询key类型

object key：查询key底层编码

> Redis的所有命令都是原子性的。
>
> Redis的所有命令大小写不敏感。

- 字符串

添加键：set key value 

批量添加：mset key1 value1 key2 value2 .....

获取键的值：get key

数字递增：incr key  => 当key不存在时默认值为0  当递增的key不是数字时报错

添加键：setnx key value  =>  如果key不存在则设置该key并返回1，如果该key存在则返回0

> 数字递增：incrby key number  给key增加number大小
>
> 数字减少：decr key 
>
> 数字减少：decrby key number  给key减小number大小
>
> 数字递增：incrfloat key number  给key增加浮点数
>
> 向尾部追加值：append key value
>
> 获取字符串长度：strlen key  =>  key不存在则返回0
>

![image-20200928145622728](http://rocks526.top/lzx/image-20200928145622728.png)

- 散列类型

添加键：hset key field value 

批量添加：hmset key1 field1 value1 key2 field2 value2 .....

获取键的值：hget key field

判断字段是否存在：hexists key field

当字段不存在时赋值：hsetnx key field value

数字递增：hincrby key field

删除字段：hdel key field [field2.....]

> 获取所有字段名：hkeys key
>
> 获取所有字段值：hvals key
>
> 获取字段数量：hlen key

![image-20200928145641529](http://rocks526.top/lzx/image-20200928145641529.png)

- 列表类型

向列表左边/右边添加元素：lpush/rpush key value [value2......]

向列表左边/右边弹出元素：lpop/rpop key

阻塞弹出元素：blpop key timeout  =>  timeout是超时时间

获取元素个数：llen key

获取列表片段：lrange key start stop  =>  索引从0开始 -1代表最后一个

删除列表指定值：lrem key count value  =>  删除前count个值为value的元素，返回删除的个数  count大于0从左边开始删  count小于0从右边开始删  count等于0则删除全部等于的元素

> 获取指定索引的元素：lindex key index
>
> 设置指定索引的元素：lset key index value
>
> 只保留列表指定片段：ltrim key start end
>
> 向列表中插入元素：linsert key before|after pivot value  =>  在pivot元素的前面|后面插入value  从左往右查找pivot  返回值是插入后列表元素的个数
>
> 将列表的一个元素转移到另一个列表：rpoplpush source destinnation  =>  从source列表右边弹出一个元素插入destinnation列表的左边

![image-20200928145700304](http://rocks526.top/lzx/image-20200928145700304.png)![image-20200928145709153](http://rocks526.top/lzx/image-20200928145709153.png)

- 集合类型

增加元素：sadd key member  =>  如果元素已存在，则忽略存在的值，并返回成功加入的元素的个数

删除元素：srem key value1 value2....  =>  删除集合中的value1和value2  可以删除多个  返回值是删除成功的个数

获得集合中所有元素：smembers key

判断元素是否在集合中：sismember key member

集合间的运算，可以计算多个集合的：

- sdiff key[key....]  差集
- sinter key[key.....] 交集
- sunion key[key......]  并集

获得集合中的元素个数：scard key

> 集合间运算，将运算结果存储到新的key里：
>
> - sdiffstore destkey key[key....]  差集
> - sinterstore destkey key[key.....] 交集
> - sunionstore destkey key[key......]  并集
>
> 随机获取集合中的元素：srandmember key [count]  =>  随机获取集合中的count个元素
>
> 随机中集合中弹出一个元素：spop key

![image-20200928145728362](http://rocks526.top/lzx/image-20200928145728362.png)

- 有序集合类型

添加元素：zadd key score member

获得元素分数：zscore key member

获得排名在某个范围内的元素列表：zrange key start stop [withscores]

获得指定分数范围的元素列表：zrangebyscore key min max [withscores] [limit it offset count]  =>  limit限制分页

增加某个元素的分数：zincrby key score member  =>  给key的member元素增加score分

> 获取集合中元素的数量：zcard key
>
> 获取指定分数范围的元素个数：zcount key min max
>
> 删除一个或多个元素：zrem key member [member2 ........]
>
> 按照排名范围元素：zremrangebyrank key start stop
>
> 按照分数范围删除元素：zremrangebyscore key min max
>
> 获得某个元素的排名：zrank key member   =>  分数从小到大   zrevrank key member  =>  分数从大到小
>
> 弹出集合中分数最小的元素：zpop min  =>  Redis5.0之后新增
>
> 弹出集合中分数最大的元素：zpop max  =>  Redis5.0之后新增

![image-20200928145740301](http://rocks526.top/lzx/image-20200928145740301.png)![image-20200928145747866](http://rocks526.top/lzx/image-20200928145747866.png)

- HyperLogLog

添加元素：pfadd key element [element .....]

获取去重后的总数：pfcount key

合并两个HyperLogLog：pfmerge key1 key2

![image-20200928145806366](http://rocks526.top/lzx/image-20200928145806366.png)

- Bitmaps

设置某个key的指定偏移量的位值：setbit key offset value

获取某个key的指定偏移量的位值：getbit key offset value

获取指定范围位值为1的个数(单位为字节，如果不指定则默认全部范围)：bitcount  key  [start] [end]

位图间运算：bitop op destkey key [key.....]  =>  op代表操作类型，可以是and交集，or并集，not非，xor异或运算，操作完成后将结果保存到destkey

计算位图指定范围内第一个偏移量等于目标值的位置：bitpos key target [start] [end]

![image-20200928145814437](http://rocks526.top/lzx/image-20200928145814437.png)

- Geo

添加元素：geoadd key longitude latitude member  =>  给key添加成员member 设置经纬度信息

> geoadd city:locations 116.28 39.55 beijing

获取元素的地理位置信息：geopos key member [member ........]

获取两个地理位置的距离：geodist key member1 member2 [unit]  =>  unit是距离的单位，m米，km千米，mi英里，ft尺

> geodist city:locations shijiazhuang beijing km

![image-20200928145825598](http://rocks526.top/lzx/image-20200928145825598.png)

- Stream

添加元素：xadd key id field1 value1 [field2 value2......]  =>  给名称为key的stream流添加一条偏偏移量为id的记录，该记录可以包含多个kv键值对

> id可以自己输入，也可以输入*让Redis自动生成，该ID就是消息的偏移量，需要满足单调递增。Redis生成的ID由两部分构成，当前时间的时间戳和用于同一时间的递增序列号。
>
> xadd workorders * workorder_id 136 workorder_name demo
>
> 往workorders的stream里新增一条记录，id自动生成，包含workorder的id和name两个kv键值对
>
> ID之所以要单调递增，保持唯一是因为Redis是支持范围查询的。

查看队列长度：xlen key

获取范围数据：xrange key id1 id2  [count num]  ==>  获取大于id1小于id2的num条记录

> id1和id2可以用`-`和`+`，表示最小值和最大值。

获取范围数据：xrevrange key id1 id2  [count num]  ==>  发现查询，降序排列，获取小于id1大于id2的num条记录

获取数据：xread [count count] [block timeout] streams key1 key2 id1  ==>  非阻塞(阻塞)从键名为key1和key2的streams里读取id大于id1的count条记录

> id为$表示此事stream里最大的ID

**消费者组：**

创建消费者组：xgroup create key groupName id1  ==>  创建一个名字为groupName的消费者组去消费key里的id大于id1的数据

从消费者组读取数据：xreadgroup group groupName consumerName count num streams key id1 ==>  让消费者consumerName从消费者组mygroup的名字为key的streams里获取num条id大于id1的记录

> 此时id可以设置为`>`。
>
> 该符号只有在消费者组命令xreadgroup中有效，意思为stream中最新且没有被其他消费者处理的ID

发送ACK：xack key groupName id1  ==>  往名称为groupName的消费者组发消息，键名为key的stream里的id为id1的记录已经被消费

![image-20200928144038930](http://rocks526.top/lzx/image-20200928144038930.png)

![image-20200928144050643](http://rocks526.top/lzx/image-20200928144050643.png)

#### Redis底层数据结构

- SDS

> Redis中并没有直接使用C语言中的字符串，而是定义了一种简单动态字符串（simple dynamic string）作为Redis的默认字符串实现，简称SDS。
>
> 在Redis中，C语言的字符串只会用于一些无需对字符串修改的地方，如日志打印等。
>
> 而Redis默认的字符串实现是SDS，如set命令中的key底层即是一个SDS，而value如果是一个字符串类型，则底层也是SDS，如果value是列表，则列表里的每个元素底层都是SDS。
>
> 除了set命令外，SDS还被用作缓冲区：AOF模块中的AOF缓冲区，客户端状态中的输入缓冲区等都是SDS实现的。

`SDS定义`

SDS的定义在Redis源码的src目录下的sds.h和sds.c文件中，定义如下：

```c
struct sdshdr {
    //字符串长度
    unsigned int len;
    //剩余空间
    unsigned int free;
    //字节数组存储数据
    char buf[];
};
```

SDS的结构由三个属性组成：

- len属性是SDS所存储字符串的长度
- free属性是剩余空间大小
- buf属性是一个char数组，用于存储数据

![image-20200920130308735](http://rocks526.top/lzx/image-20200920130308735.png)

如上图所示即为一个SDS，该SDS的len属性为5，代表目前存储的字符串长度是5，free属性为5，代表该SDS还有5个空余长度，为了兼容C函数，字符串末尾保留\0字符。

`SDS优点`

- 常数时间复杂度获取字符串长度

原始C字符串通过char数组保存数据，每次获取字符串长度都需要遍历整个数组，时间复杂度O(N)，而SDS通过len属性维护字符串长度，获取字符串长度只需要查询len属性即可，时间复杂度O(1)

Redis通过SDS将字符串长度计算的时间复杂度从O(N)降低为O(1)，确保获取字符串长度的工作不会成为Redis的瓶颈。例如我们即使对一个特别长的字符串键反复执行STRLEN命令时，也不会对系统造成影响。

> 这其实是一种分摊的思想，Jdk封装的工具类基本都内置了len属性，每次修改字符串时同时更新len属性，将原本计算字符串长度的O(N)时间复杂度分摊到每次字符串更新上。

- 杜绝缓冲区溢出

在C语言原生的字符串中，当需要修改字符串且修改后的长度大于修改前的长度时，在修改之前需要先对原数组申请空间扩容，否则可能导致数组溢出，内容写入到相邻的下一个数组中从而改变下一个字符串的值。

在SDS中，SDS屏蔽了用户对数组空间的分配，SDS在增长之前会根据free属性自动检测是否足够修改之后的字符串所需空间，如果足够则直接修改，并更新修改之后的len和free属性，如果当前剩余空间不够，SDS会根据空间分配策略自动进行扩容，无需用户关心。

- 减少修改字符串时带来的内存重新分配次数

在C语言中，修改字符串时，无论是增加长度还是减少长度，都会需要对应的申请新的内存空间或者释放多余的内存空间的操作，这些统称为内存重新分配操作，这些操作不但繁琐，需要开发者自己实现，而且频繁的内存分配也会影响性能。

在SDS中，修改字符串时，增加长度时，SDS会根据空间分配策略自动分配，而且会多余分配一些空间，可能下次扩容时上次分配的多余空间足够使用则不用再次分配空间，减少长度时同理，当减少长度时可能并不会立刻回收空间，而是将多余的空间留下，修改free值，用于下次使用。

SDS的空间分配策略：

空间预分配：用于优化SDS的扩展，当SDS长度增加之后，如果修改后的SDS长度将小于1MB，那么分配和len长度相等的多余空间，反之分配1MB的多余空间。

惰性空间释放：用于优化SDS的字符串缩短操作：当SDS的长度缩短之后，并不会立即释放对应的空间，而是修改free属性，将多余空间保留用以下次扩展。

> SDS采用的也是一种空间换时间的思路，无论是扩展之后分配多余空间从而降低下次扩展时需要再次内存分配的概率，还是缩容之后并不立即回收空间而是留给下次扩容，这两种操作都会导致空闲空间增大，内存占用提升，而Redis又了很多数据压缩策略来控制内存。

- 二进制安全

在C语言原生字符串中，字符必须符合某种编码（例如ASCII），并且字符串里不能包含空格字符，否则会被认为是结束标志，因此C字符串只能存储一些文本数据，而不能存储视频，音频压缩文件这样的二进制数据。

在SDS中，由于SDS是通过len属性计算长度的，/0只是为了兼容C字符串，因此即便数据中含有/0也能准确的计算出数据的长度，因此Redis可以存储二进制数据，例如图片音频等。

`SDS总结`

Redis中的SDS和Java里的String很相似，都是对原生String进行的一层封装，屏蔽了底层一些操作的细节，提供了自动扩容等机制，方便开发者使用，同时通过空间换时间的方式优化字符串的性能。对于大多数场景，SDS都优于C字符串，而对于不涉及字符串更新和长度统计的场景，则C字符串更节省空间。

----------------

- 链表

> 由于C语言中没有内置的链表数据结构，因此Redis自己构造了链表的实现。
>
> 链表在Redis中应用十分广泛，比如列表的底层实现之一就是链表。除了列表之外，发布订阅模型，慢查询，监视器等功能也都使用到了链表，Redis服务器本身还用链表保存多个客户端的状态信息。

`链表定义`

每一个链表节点使用一个adlist.h/listNode结构来表示，代码如下所示：

```c
typedef struct listNode {
    //前指针
    struct listNode *prev;
    //后指针
    struct listNode *next;
    //值
    void *value;
} listNode;
```

虽然使用listNode节点相互连接就可以构成链表，但为了操作方便，Redis封装了链表的数据结构adlist.h/list，代码如下所示：

```c
typedef struct list {
    //头指针
    listNode *head;
    //尾指针
    listNode *tail;
    //节点拷贝函数
    void *(*dup)(void *ptr);
    //节点删除函数
    void (*free)(void *ptr);
    //节点对比函数
    int (*match)(void *ptr, void *key);
    unsigned long len;
} list;
```

此数据结构为链表提供了表头，表尾，长度计数器等属性，还有链表拷贝，删除，对比的函数。

一个完整的链表示例如下所示：

![image-20200920124849496](http://rocks526.top/lzx/image-20200920124849496.png)

`链表优点`

- 双向：此链表是双向链表，包含前后指针，获取前后一个节点的时间复杂度都是O(1)
- 无环：此链表表头的前节点和表尾的后节点都是NULL，可以方便遍历链表
- 带表头和表尾：获取表头节点和表尾节点时间复杂度都是O(1)，获取中间节点，先根据链表长度计算离头近还是离尾近，再从头或尾开始遍历，最多时间复杂度O(N/2)

-----------------

- 字典

> 由于C语言中没有内置的字典数据结构，因此Redis自己构造了字典的实现。
>
> 字典在Redis中的应用相当广泛，比如Redis的数据库就是使用字典来作为底层实现的，对数据库的增、删、查、改操作也是构建在对字典的操作之上的。
>
> 除了用来表示数据库之外，字典还是哈希键的底层实现之一，当一个哈希键包含的键值对比较多，又或者键值对中的元素都是比较长的字符串时，Redis就会使用字典作为哈希键的底层实现。

`字典定义`

Redis的字典使用哈希表作为底层实现，一个哈希表里面可以有多个哈希表节点，而每个哈希表节点就保存了字典中的一个键值对。

Redis字典所使用的底层数据结构都在dict.h定义，哈希表节点使用dictEntry结构表示，每个dictEntry结构都保存着一个键值对：

```c
typedef struct dictEntry {
    //键
    void *key;
    //值
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    //后指针
    struct dictEntry *next;
} dictEntry;
```

key属性保存着键值对中的键，而v属性则保存着键值对中的值，其中键值对的值可以是一个指针，或者是一个uint64_t整数，又或者是一个int64_t整数。next属性是指向另一个哈希表节点的指针，这个指针可以将多个哈希值相同的键值对连接在一次，以此来解决键冲突（collision）的问题。

Redis字典所使用的哈希表由dict.h/dictht结构定义，具体代码如下：

```c
typedef struct dictht {
    //元素存储的列表
    dictEntry **table;
    //哈希表大小
    unsigned long size;
    //哈希表大小掩码 用于计算key的索引 总是等于size-1
    unsigned long sizemask;
    //元素个数
    unsigned long used;
} dictht;
```

table属性是一个数组，数组中的每个元素都是一个指向dict.h/dictEntry结构的指针，每个dictEntry结构保存着一个键值对。size属性记录了哈希表的大小，也即是table数组的大小，而used属性则记录了哈希表目前已有节点（键值对）的数量。sizemask属性的值总是等于size-1，这个属性和哈希值一起决定一个键应该被放到table数组的哪个索引上面。

Redis中的字典由dict.h/dict结构表示，具体代码如下：

```c
typedef struct dict {
    //字典类型
    dictType *type;
    //字典类型对应的可选函数
    void *privdata;
    //数据存储
    dictht ht[2];
    //rehash进度  不在rehash时为-1
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
} dict;
```

type属性和privdata属性是针对不同类型的键值对，为创建多态字典而设置的：Redis会为用途不同的字典设置不同的类型特定函数。

ht属性是一个包含两个项的数组，数组中的每个项都是一个dictht哈希表，一般情况下，字典只使用ht[0]哈希表，ht[1]哈希表只会在对ht[0]哈希表进行rehash时使用。除了ht[1]之外，另一个和rehash有关的属性就是rehashidx，它记录了rehash目前的进度，如果目前没有在进行rehash，那么它的值为-1。

一个正常情况下的字典示例如下所示：

![image-20200920131717304](http://rocks526.top/lzx/image-20200920131717304.png)

添加键：当要将一个新的键值对添加到字典里面时，程序需要先根据键值对的键计算出哈希值和索引值，然后再根据索引值，将包含新键值对的哈希表节点放到哈希表数组的指定索引上面。

> Redis的哈希算法有不同的实现，Redis2.9采用MurmurHash算法，通过将计算得到的hash值 & sizemask得到索引值，此处采用和Jdk的HashMap类型的机制，通过&操作替换取余操作来提升性能，要求size必须是2的n次方。

冲突解决：遇到哈希冲突时，采用拉链法解决冲突，将冲突的key拼接在之前key后面，形成一个单向链表。

Rehash：Redis中的Rehash操作类似于HashMap中的自动扩容机制，随着操作的不断执行，哈希表保存的键值对会逐渐地增多或者减少，为了让哈希表的负载因子（load factor）维持在一个合理的范围之内，当哈希表保存的键值对数量太多或者太少时，程序需要对哈希表的大小进行相应的扩展或者收缩。具体步骤如下：

1. 为字典的ht[1]哈希表分配空间，这个哈希表的空间大小取决于要执行的操作，以及ht[0]当前包含的键值对数量（也即是ht[0].used属性的值）：
   1. 如果执行的是扩展操作，那么ht[1]的大小为第一个大于等于ht[0].used*2的2的n次方幂
   2. 如果执行的是收缩操作，那么ht[1]的大小为第一个大于等于ht[0].used的2的n次方幂

2. 将保存在ht[0]中的所有键值对rehash到ht[1]上面：rehash指的是重新计算键的哈希值和索引值，然后将键值对放置到ht[1]哈希表的指定位置上。
3. 当ht[0]包含的所有键值对都迁移到了ht[1]之后（ht[0]变为空表），释放ht[0]，将ht[1]设置为ht[0]，并在ht[1]新创建一个空白哈希表，为下一次rehash做准备。

> 当以下条件中的任意一个被满足时，程序会自动开始对哈希表执行扩展操作：
>
> 1）服务器目前没有在执行BGSAVE命令或者BGREWRITEAOF命令，并且哈希表的负载因子大于等于1。
>
> 2）服务器目前正在执行BGSAVE命令或者BGREWRITEAOF命令，并且哈希表的负载因子大于等于5。
>
> 另一方面，当哈希表的负载因子小于0.1时，程序自动开始对哈希表执行收缩操作。
>
> 根据BGSAVE命令或BGREWRITEAOF命令是否正在执行，服务器执行扩展操作所需的负载因子并不相同，这是因为在执行BGSAVE命令或BGREWRITEAOF命令的过程中，Redis需要创建当前服务器进程的子进程，而大多数操作系统都采用写时复制（copy-on-write）技术来优化子进程的使用效率，所以在子进程存在期间，服务器会提高执行扩展操作所需的负载因子，从而尽可能地避免在子进程存在期间进行哈希表扩展操作，这可以避免不必要的内存写入操作，最大限度地节约内存。

渐进式Hash：扩展或收缩哈希表需要将ht[0]里面的所有键值对rehash到ht[1]里面，但是，这个rehash动作并不是一次性、集中式地完成的，而是分多次、渐进式地完成的。这么做的原因是，如果字典存在大量key，上百万甚至千万，那么要一次性将这些键值对全部rehash到ht[1]的话，庞大的计算量可能会导致服务器在一段时间内停止服务。因此，为了避免rehash对服务器性能造成影响，服务器不是一次性将ht[0]里面的所有键值对全部rehash到ht[1]，而是分多次、渐进式地将ht[0]里面的键值对慢慢地rehash到ht[1]。

渐进式Hash的具体步骤如下：

1. 为ht[1]分配空间，让字典同时持有ht[0]和ht[1]两个哈希表。
2. 在字典中维持一个索引计数器变量rehashidx，并将它的值设置为0，表示rehash工作正式开始。
3. 在rehash进行期间，每次对字典执行添加、删除、查找或者更新操作时，程序除了执行指定的操作以外，还会顺带将ht[0]哈希表在rehashidx索引上的所有键值对rehash到ht[1]，当rehash工作完成之后，程序将rehashidx属性的值增一。
4. 随着字典操作的不断执行，最终在某个时间点上，ht[0]的所有键值对都会被rehash至ht[1]，这时程序将rehashidx属性的值设为-1，表示rehash操作已完成。

> 渐进式rehash的好处在于它采取分而治之的方式，将rehash键值对所需的计算工作均摊到对字典的每个添加、删除、查找和更新操作上，从而避免了集中式rehash而带来的庞大计算量。
>
> 因为在进行渐进式rehash的过程中，字典会同时使用ht[0]和ht[1]两个哈希表，所以在渐进式rehash进行期间，字典的删除（delete）、查找（find）、更新（update）等操作会在两个哈希表上进行。例如，要在字典里面查找一个键的话，程序会先在ht[0]里面进行查找，如果没找到的话，就会继续到ht[1]里面进行查找。
>
> 另外，在渐进式rehash执行期间，新添加到字典的键值对一律会被保存到ht[1]里面，而ht[0]则不再进行任何添加操作，这一措施保证了ht[0]包含的键值对数量会只减不增，并随着rehash操作的执行而最终变成空表。

`字典优点`

- Redis中的字典使用哈希表作为底层实现，每个字典带有两个哈希表，一个平时使用，另一个仅在进行rehash时使用。
- 哈希表使用链地址法来解决键冲突，被分配到同一个索引上的多个键值对会连接成一个单向链表。
- 在对哈希表进行扩展或者收缩操作时，程序需要将现有哈希表包含的所有键值对rehash到新哈希表里面，并且这个rehash过程并不是一次性地完成的，而是渐进式地完成的。
- 字典的负载因子是1，BGSAVE时为5

---------------

- 跳跃表

> 跳跃表（skiplist）是一种有序数据结构，它通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的。
>
> 跳跃表支持平均O（logN）、最坏O（N）复杂度的节点查找，还可以通过顺序性操作来批量处理节点。
>
> 在大部分情况下，跳跃表的效率可以和平衡树相媲美，并且因为跳跃表的实现比平衡树要来得更为简单，所以有不少程序都使用跳跃表来代替平衡树。
>
> Redis使用跳跃表作为有序集合键的底层实现之一，除此之外，跳表还在集群节点中用作内部数据结构。

`跳表定义`

Redis的跳跃表由redis.h/zskiplistNode和redis.h/zskiplist两个结构定义，其中zskiplistNode结构用于表示跳跃表节点，具体代码如下：

``` c
typedef struct zskiplistNode {
    //值
    robj *obj;
    //分数
    double score;
    //后指针
    struct zskiplistNode *backward;
    //跳表的层
    struct zskiplistLevel {
        //前指针
        struct zskiplistNode *forward;
        //跨度
        unsigned int span;
    } level[];
} zskiplistNode;
```

> 层（level）：节点中用L1、L2、L3等字样标记节点的各个层，L1代表第一层，L2代表第二层，以此类推。每个层都带有两个属性：前进指针和跨度。前进指针用于访问位于表尾方向的其他节点，而跨度则记录了前进指针所指向节点和当前节点的距离。在上面的图片中，连线上带有数字的箭头就代表前进指针，而那个数字就是跨度。当程序从表头向表尾进行遍历时，访问会沿着层的前进指针进行。
>
> 后退（backward）指针：节点中用BW字样标记节点的后退指针，它指向位于当前节点的前一个节点。后退指针在程序从表尾向表头遍历时使用。
>
> 分值（score）：各个节点中的1.0、2.0和3.0是节点所保存的分值。在跳跃表中，节点按各自所保存的分值从小到大排列。
>
> 成员对象（obj）：各个节点中的o1、o2和o3是节点所保存的成员对象。

zskiplist结构则用于保存跳跃表节点的相关信息，比如节点的数量，以及指向表头节点和表尾节点的指针等等。具体代码如下：

```c
typedef struct zskiplist {
    //头 尾指针
    struct zskiplistNode *header, *tail;
    //记录跳跃表的长度，也即是，跳跃表目前包含节点的数量（表头节点不计算在内）
    unsigned long length;
    //记录目前跳跃表内，层数最大的那个节点的层数（表头节点的层数不计算在内）
    int level;
} zskiplist;
```

一个正常的跳表如下图所示：

![image-20200920134333248](http://rocks526.top/lzx/image-20200920134333248.png)

> 注意表头节点和其他节点的构造是一样的：表头节点也有后退指针、分值和成员对象，不过表头节点的这些属性都不会被用到，所以图中省略了这些部分，只显示了表头节点的各个层。

`跳表特点`

- 跳跃表是有序集合的底层实现之一。
- 每个跳跃表节点的层高都是1至32之间的随机数。
- 在同一个跳跃表中，多个节点可以包含相同的分值，但每个节点的成员对象必须是唯一的。
- 跳跃表中的节点按照分值大小进行排序，当分值相同时，节点按照成员对象的大小进行排序。

-------------------

- 整数集合

> 整数集合（intset）是集合键的底层实现之一，当一个集合只包含整数值元素，并且这个集合的元素数量不多时，Redis就会使用整数集合作为集合键的底层实现。

`整数集合定义`

整数集合（intset）是Redis用于保存整数值的集合抽象数据结构，它可以保存类型为int16_t、int32_t或者int64_t的整数值，并且保证集合中不会出现重复元素。

每个intset.h/intset结构表示一个整数集合：

```c
typedef struct intset {
    //编码方式
    uint32_t encoding;
    //集合包含元素个数
    uint32_t length;
    //元素列表
    int8_t contents[];
} intset;
```

contents数组是整数集合的底层实现：整数集合的每个元素都是contents数组的一个数组项（item），各个项在数组中按值的大小从小到大有序地排列，并且数组中不包含任何重复项。

length属性记录了整数集合包含的元素数量，也即是contents数组的长度。虽然intset结构将contents属性声明为int8_t类型的数组，但实际上contents数组并不保存任何int8_t类型的值，contents数组的真正类型取决于encoding属性的值。

一个INT16的整数集合如下所示：

![image-20200920135709119](http://rocks526.top/lzx/image-20200920135709119.png)

整数集合升级：每当我们要将一个新元素添加到整数集合里面，并且新元素的类型比整数集合现有所有元素的类型都要长时，整数集合需要先进行升级（upgrade），然后才能将新元素添加到整数集合里面。

升级整数集合并添加新元素共分为三步进行：

1. 根据新元素的类型，扩展整数集合底层数组的空间大小，并为新元素分配空间。
2. 将底层数组现有的所有元素都转换成与新元素相同的类型，并将类型转换后的元素放置到正确的位上，而且在放置元素的过程中，需要继续维持底层数组的有序性质不变。
3. 将新元素添加到底层数组里面。

> 因为每次向整数集合添加新元素都可能会引起升级，而每次升级都需要对底层数组中已有的所有元素进行类型转换，所以向整数集合添加新元素的时间复杂度为O（N）。

升级的好处：整数集合的升级策略有两个好处，一个是提升整数集合的灵活性，另一个是尽可能地节约内存。

> 因为C语言是静态类型语言，为了避免类型错误，我们通常不会将两种不同类型的值放在同一个数据结构里面。例如，我们一般只使用int16_t类型的数组来保存int16_t类型的值，只使用int32_t类型的数组来保存int32_t类型的值，诸如此类。但是，因为整数集合可以通过自动升级底层数组来适应新元素，所以我们可以随意地将int16_t、int32_t或者int64_t类型的整数添加到集合中，而不必担心出现类型错误，这种做法非常灵活。
>
> 当然，要让一个数组可以同时保存int16_t、int32_t、int64_t三种类型的值，最简单的做法就是直接使用int64_t类型的数组作为整数集合的底层实现。不过这样一来，即使添加到整数集合里面的都是int16_t类型或者int32_t类型的值，数组都需要使用int64_t类型的空间去保存它们，从而出现浪费内存的情况。
>
> 整数集合不支持降级操作，一旦对数组进行了升级，编码就会一直保持升级后的状态。

`整数集合优点`

- 时间换空间的思路，对于每次需要的时候再进行升级，可以节省内存开销。
- 整数集合只支持升级操作，不支持降级操作。

------------------------------

- 压缩列表

> 压缩列表（ziplist）是列表键和哈希键的底层实现之一。当一个列表键只包含少量列表项，并且每个列表项要么就是小整数值，要么就是长度比较短的字符串，那么Redis就会使用压缩列表来做列表键的底层实现。

`压缩列表定义`

压缩列表是Redis为了节约内存而开发的，是由一系列特殊编码的连续内存块组成的顺序型（sequential）数据结构。

一个压缩列表可以包含任意多个节点（entry），每个节点可以保存一个字节数组或者一个整数值。

如下图展示了压缩列表的各个组成部分：

![image-20200920140412516](http://rocks526.top/lzx/image-20200920140412516.png)

zlbytes用于记录整个压缩列表的字节数，在对压缩列表进行内存重分配或就算zlend位置时使用。

zltail属性用于记录压缩列表的尾结点距离压缩列表的起始位置距离，通过这个值无需遍历整个列表即可获得尾结点的地址。

zllen属性记录了压缩列表包含的节点数。

entry属性是压缩列表保存的各个节点。

zlend属性是压缩列表的默认标志。

如下图展示了压缩列表的节点的组成部分：

![image-20200920141047090](http://rocks526.top/lzx/image-20200920141047090.png)

每个压缩列表节点都由previous_entry_length、encode、content三个部分组成。

previous_entry_length：节点的previous_entry_length属性以字节为单位，记录了压缩列表中前一个节点的长度。

encode：记录了节点的content属性所保存数据的类型以及长度

content：负责保存节点的值，节点值可以是一个字节数组或者整数，值的类型和长度由节点的encoding属性决定。

连锁更新问题：压缩列表为了节约内存，会根据存储数据的长度选择不同的字节数，当某个压缩列表的表头数据增加后，导致该节点的字节数变长需要空间扩容，之后接连导致后续的所有节点扩容。

> 除了添加新节点可能会引发连锁更新之外，删除节点也可能会引发连锁更新。
>
> 因为连锁更新在最坏情况下需要对压缩列表执行N次空间重分配操作，而每次空间重分配的最坏复杂度为O（N），所以连锁更新的最坏复杂度为O（N 2）。
>
> 要注意的是，尽管连锁更新的复杂度较高，但它真正造成性能问题的几率是很低的。

`压缩列表优点`

- 压缩列表是一种为节约内存而开发的顺序型数据结构。
- 压缩列表被用作列表键和哈希键的底层实现之一。
- 压缩列表可以包含多个节点，每个节点可以保存一个字节数组或者整数值。
- 添加新节点到压缩列表，或者从压缩列表中删除节点，可能会引发连锁更新操作，但这种操作出现的几率并不高。

--------------------------

- Redis对象体系结构

> 在之前介绍了Redis用到的所有主要数据结构，比如简单动态字符串（SDS）、双端链表、字典、压缩列表、整数集合等等。但Redis并没有直接使用这些数据结构来实现键值对数据库，而是基于这些数据结构创建了一个对象系统，这个系统包含字符串对象、列表对象、哈希对象、集合对象和有序集合对象这五种类型的对象，每种对象都用到了至少一种我们前面所介绍的数据结构。
>
> 通过这五种不同类型的对象，Redis可以在执行命令之前，根据对象的类型来判断一个对象是否可以执行给定的命令。使用对象的另一个好处是，我们可以针对不同的使用场景，为对象设置多种不同的数据结构实现，从而优化对象在不同场景下的使用效率。
>
> 除此之外，Redis的对象系统还实现了基于引用计数技术的内存回收机制，当程序不再使用某个对象的时候，这个对象所占用的内存就会被自动释放；另外，Redis还通过引用计数技术实现了对象共享机制，这一机制可以在适当的条件下，通过让多个数据库键共享同一个对象来节约内存。
>
> 最后，Redis的对象带有访问时间记录信息，该信息可以用于计算数据库键的空转时长，在服务器启用了maxmemory功能的情况下，空转时长较大的那些键可能会优先被服务器删除。

Redis中的每个对象都由一个redisObject结构表示，代码如下：

```c
typedef struct redisObject {
    //类型
    unsigned type:4;
    //编码
    unsigned encoding:4;
    //指定底层实现的数据结构
    void *ptr;
    //...........
}robj;
```

对象的type属性记录对象的类型，可以是REDIS_STRING,REDIS_LIST,REDIS_HASH,REDIS_SET,REDIS_ZSET五个常量中的一个，对于Redis来说，key总是String类型，value可以是以上五个类型中的一个。

> Redis的type命令其实就是返回该键对应的值对象的type属性，type命令输出如下：

| 对象         | 对象的type属性值 | type命令输出 |
| ------------ | ---------------- | ------------ |
| 字符串对象   | REDIS_STRING     | "string"     |
| 哈希对象     | REDIS_HASH       | "hash"       |
| 列表对象     | REDIS_LIST       | "list"       |
| 集合对象     | REDIS_SET        | "set"        |
| 有序集合对象 | REDIS_ZSET       | "zset"       |

对象的ptr指针指向对象的底层实现数据结构，而这些数据结构由对象的encoding属性决定。encoding属性记录了对象所使用的编码，也即是说这个对象使用了什么数据结构作为对象的底层实现，这个属性的值可以是下表列出的常量的其中一个。

| 类型         | 编码                      | 对象                                           |
| ------------ | ------------------------- | ---------------------------------------------- |
| REDIS_STRING | REDIS_ENCODING_INT        | 使用整数值实现的字符串对象                     |
| REDIS_STRING | REDIS_STRING_EMBSTR       | 使用embstr编码的简单动态字符串实现的字符串对象 |
| REDIS_STRING | REDIS_ENCODING_RAW        | 使用简单动态字符串实现的字符串对象             |
| REDIS_LIST   | REDIS_ENCODING_ZIPLIST    | 使用压缩列表实现的列表对象                     |
| REDIS_LIST   | REDIS_ENCODING_LINKEDLIST | 使用双链表实现的列表对象                       |
| REDIS_HASH   | REDIS_ENCODING_ZIPLIST    | 使用压缩列表实现的哈希对象                     |
| REDIS_HASH   | REDIS_ENCODING_HT         | 使用字典实现的集合对象                         |
| REDIS_SET    | REDIS_ENCODING_INTSET     | 使用整数集合实现的集合对象                     |
| REDIS_SET    | REDIS_ENCODING_HT         | 使用字典实现的集合对象                         |
| REDIS_ZSET   | REDIS_ENCODING_ZIPLIST    | 使用压缩列表实现的有序集合对象                 |
| REDIS_ZSET   | REDIS_ENCODING_SKIPLIST   | 使用跳表实现的有序集合对象                     |

> 使用OBJECT ENCODING命令可以查看一个数据库键的值对象的编码

通过encoding属性来设定对象所使用的编码，而不是为特定类型的对象关联一种固定的编码，极大地提升了Redis的灵活性和效率，因为Redis可以根据不同的使用场景来为一个对象设置不同的编码，从而优化对象在某一场景下的效率。

> - 字符串对象
>
> 可以用long类型表示的整数用int类型存，小于32字节的字符串用embstr存，大于32字节的字符串用sds存储。
>
> 浮点数会转化为字符串存储。embstr比sds少一次内存重分配开销且可以更好的利用缓存。
>
> - 列表对象
>
> 列表中每个元素小于64字节且列表元素少于512个时，用压缩列表存储，节省内存开销并且利用缓存。
>
> 列表中有元素大于64字节或列表元素多于512个时，用双链表存储。
>
> 具体大小可以通过配置文件修改。
>
> - 哈希对象
>
> 哈希表中每个元素小于64字节且哈希表元素少于512个时，用压缩列表存储，节省内存开销并且利用缓存。
>
> 哈希表中有元素大于64字节或哈希表元素多于512个时，用字典存储。
>
> - 集合对象
>
> 集合中每个元素都是整数且集合元素少于512个时，用整数集合存储，节省内存开销。
>
> 集合中有元素不是整数或集合元素多于512个时，用字典存储，字典的值全部置为Null。
>
> - 有序集合对象
>
> 集合中每个元素都小于64字节且集合元素少于128个时，用压缩列表存储，节省内存开销。
>
> 集合中有元素大于64字节或集合元素多于128个时，用跳表存储。
>
> 有序集合用跳表存储时，实际底层使用了跳表加字典，跳表实现范围查找，字典实现成员和分数映射。

- 命令解析

Redis中有一些命令是所有类型的通用命令，有些是特定类型的命令，针对不同的对象编码，底层命令的执行逻辑也不同，Redis针对这些实现了命令的多态。命令解析过程如下：

1. 检查该命令是否是通用命令，如果是直接执行。
2. 根据key对应value对象的type字段确定对象类型，判断是否可执行该命令，如果不能执行，给客户端返回错误提示。
3. 根据key对应value对象的encoding字段确定对象底层编码，调用对应的命令实现。

- 内存回收

因为C语言并不具备自动内存回收功能，所以Redis在自己的对象系统中构建了一个引用计数（reference counting）技术实现的内存回收机制，通过这一机制，程序可以通过跟踪对象的引用计数信息，在适当的时候自动释放对象并进行内存回收。

每个对象的引用计数信息由redisObject结构的refcount属性记录：

```c
typedef struct redisObject {
    //引用计数
    int refcount;
    //...........
}robj;
```

对象的引用计数信息会随着对象的使用状态而不断变化，创建一个新的对象时，该值会被置为1，被一个新的程序引用时，该值加一；不再引用时，该值减一；当该值变为0时，所占用内存被清除。

除了用于实现引用计数内存回收机制之外，对象的引用计数属性还带有对象共享的作用。

当两个key的value一样时，不会创建两个value对象，还是创建一个value对象，两个key都指向该对象，该对象的引用为2。此共享方式可以大量节约内存。

> 通过OBJECT REFCOUNT key可以查看该key对应的value对象的引用数。
>
> 需要注意的是，Redis只对整数进行共享，字符串和哈希之类的对象不会共享，因为验证对象是否相等的速度太慢，对于整数类型是O(1)，而字符串和哈希表需要O(n)，对时间开销太大。

- 空转时间

除了前面介绍过的type、encoding、ptr和refcount四个属性之外，redisObject结构包含的最后一个属性为lru属性，该属性记录了对象最后一次被命令程序访问的时间：

```c
typedef struct redisObject {
    //空转时间
    unsigned lru:22;
    //...........
}robj;
```

OBJECT IDLETIME命令可以打印出给定键的空转时长，这一空转时长就是通过将当前时间减去键的值对象的lru时间计算得出的。

> OBJECT IDLETIME命令的实现是特殊的，这个命令在访问键的值对象时，不会修改值对象的lru属性。

除了可以被OBJECT IDLETIME命令打印出来之外，键的空转时长还有另外一项作用：如果服务器打开了maxmemory选项，并且服务器用于回收内存的算法为volatile-lru或者allkeys-lru，那么当服务器占用的内存数超过了maxmemory选项所设置的上限值时，空转时长较高的那部分键会优先被服务器释放，从而回收内存。

### <a id="rm-sw">Redis事务</a>

- Redis事务介绍

Redis中的事务（transaction）是一组命令的集合。事务同命令一样都是Redis的最小执行单位，一个事务中的命令要么都执行，要么都不执行。

> 事务具备ACID特性：
>
> - 原子性（Atomicity）：事务被视为不可分割的最小单元，事务的所有操作要么全部提交成功，要么全部失败回滚。
> - 一致性（Consistency）：数据库在事务执行前后都保持一致性状态。在一致性状态下，所有事务对一个数据的读取结果都是相同的。
> -  隔离性（Isolation）：一个事务所做的修改在最终提交以前，对其它事务是不可见的。
> -  持久性（Durability）：一旦事务提交，则其所做的修改将会永远保存到数据库中。即使系统发生崩溃，事务执行的结果也不能丢失。使用重做日志来保证持久性。
>
> MySQL等数据库一般通过redolog和undolog完成事务的回滚，而Redis为了性能考虑，不提供事务回滚机制。
>
> 由于Redis是单线程的，在此事务执行期间，无法执行其他命令，因此满足一致性和隔离性，而Redis提供AOF的RDB持久化方案，满足持久性。

- Redis事务使用

正确事务使用示例：

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name soc
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> exec
1) OK
2) "soc"
127.0.0.1:6379> get name
"soc"
```

> 使用MULTI指令开启一个事务，之后执行的所有操作都会进入队列存储，当执行EXEC指令时，Redis会将队列中的所有操作拿出来依次执行，如果没有错误，会返回每一步的执行结果。

语法错误使用示例：

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name tianyan
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> hget name
(error) ERR wrong number of arguments for 'hget' command
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get name
"soc"
```

> 当事务中某个指令存在语法错误时，如此示例中的hget命令，在该命令入队时，Redis会检测到异常，当exec执行事务时，Redis会抛出异常，所有命令都不执行，即便正确的命令。

运行错误使用示例：

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name tianyan
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> sadd name 2
QUEUED
127.0.0.1:6379> exec
1) OK
2) "tianyan"
3) (error) WRONGTYPE Operation against a key holding the wrong kind of value
127.0.0.1:6379> get name
"tianyan"
```

> 当事务中某个指令存在运行错误时，如此示例中的sadd命令，name键是String类型，而sadd是对集合的操作，但此命令入队时，Redis无法判断name的类型，因此不会报错，当exec执行事务之后，发现此命令执行出错，但之前的命令已经执行，无法回滚。

放弃事务示例：

```shell
127.0.0.1:6379> get name
"tianyan"
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name soc
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> discard
OK
127.0.0.1:6379> exec
(error) ERR EXEC without MULTI
127.0.0.1:6379> get name
"tianyan"
```

Watch监听机制：WATCH命令可以监控一个或多个键，一旦其中有一个键被修改（或删除），之后的事务就不会执行。监控一直持续到EXEC命令。

> WATCH 命令可以为 Redis 事务提供 check-and-set （CAS）行为。是一种非常强大的乐观锁。

```shell
127.0.0.1:6379> get name
"soc"
127.0.0.1:6379> watch name
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name tianqing
QUEUED
127.0.0.1:6379> exec
1) OK
127.0.0.1:6379> get name
"tianqing"
---------------------------------------------------------------------
127.0.0.1:6379> watch name
OK
127.0.0.1:6379> set name soc
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> 
127.0.0.1:6379> set name soc2
QUEUED
127.0.0.1:6379> exec
(nil)
127.0.0.1:6379> get name
"soc"
```

> 通过UnWatch命令可以取消对键的监控

- Redis事务原理

**事务的实现**

Redis服务端会维护所有连接的客户端状态，每个Redis客户端都有自己的事务状态，这个事务状态保存在客户端状态的mstate属性里面：

```c
typedef struct redisClient{
    //.......
    //事务状态
    multiState mstate;
    //.....
}redisClient;
```

事务状态包含一个事务队列，以及一个已入队命令的计数器（也可以说是事务队列的长度）：

```c
typedef struct multiState{
    //事务队列
    multiCmd *commands;
    //以入队命令数
    int count;
}multiState;
```

> 事务队列是一个multiCmd类型的数组，数组中的每个multiCmd结构都保存了一个已入队命令的相关信息，包括指向命令实现函数的指针、命令的参数，以及参数的数量。
>
> 事务队列以先进先出（FIFO）的方式保存入队的命令，较先入队的命令会被放到数组的前面，而较后入队的命令则会被放到数组的后面。

一个事务从开始到结束通常会经历以下三个阶段：事务开始，事务入队，事务执行。

1. 事务开始，客户端发送MULTI命令，服务端会将客户端从非事务状态切换至事务状态，这一切换是通过在客户端状态的flags属性中打开REDIS_MULTI标识来完成的
2. 当一个客户端处于非事务状态时，这个客户端发送的命令会立即被服务器执行；当一个客户端处于事务状态时，这个客户端发送的命令会被放入事务队列中，返回客户端QUEUE响应。
3. 直到客户端发送的命令为EXEC、DISCARD、WATCH、MULTI四个命令的其中一个，那么服务器立即执行这个命令。

![image-20201012155817014](http://rocks526.top/lzx/image-20201012155817014.png)

**Watch命令实现**

每个Redis数据库都保存着一个watched_keys字典，这个字典的键是某个被WATCH命令监视的数据库键，而字典的值则是一个链表，链表中记录了所有监视相应数据库键的客户端：

```c
typedef struct redisDb{
    //.......
    //正在被WATCH监视的键
    dict *watched_keys;
    //.....
}redisDb;
```

通过watched_keys字典，服务器可以清楚地知道哪些数据库键正在被监视，以及哪些客户端正在监视这些数据库键。

> 客户端执行WATCH命令，即服务端将该客户端加入redisDb中，如果key存在，则链表加入客户端，如果key不存在，则加入kv

所有对数据库进行修改的命令，比如SET、LPUSH、SADD、ZREM、DEL、FLUSHDB等等，在执行之后都会调用multi.c/touchWatchKey函数对watched_keys字典进行检查，查看是否有客户端正在监视刚刚被命令修改过的数据库键，如果有的话，那么touchWatchKey函数会将监视被修改键的客户端的REDIS_DIRTY_CAS标识打开，表示该客户端的事务安全性已经被破坏。

当服务器接收到一个客户端发来的EXEC命令时，服务器会根据这个客户端是否打开了REDIS_DIRTY_CAS标识来决定是否执行事务：如果已经打开，说明键被修改，事务失败；反之执行事务。

### <a id="rm-scsj">Redis生存时间</a>

- Redis生存时间介绍

> 在实际的开发中经常会遇到一些有时效的数据，比如限时优惠活动、缓存或验证码等，过了一定的时间就需要删除这些数据。在关系数据库中一般需要额外的一个字段记录到期时间，然后定期检测删除过期数据。而在Redis中可以使用EXPIRE命令设置一个键的生存时间，到时间后Redis会自动删除它。

- Redis生存时间使用

> EXPIRE key seconds：其中seconds参数表示键的生存时间，单位是秒
>
> ttl key：返回key的过期时间，当键不存在或者没有为键设置生存时间时返回-1(高版本Redis，键不存在返回-2，没有生命周期返回-1)
>
> persist：清楚key的生存时间，清楚成功返回1，否则返回0（键不存在或者没有生存时间）

```shell
127.0.0.1:6379> get name
"tianqing"
127.0.0.1:6379> expire name 10
(integer) 1
127.0.0.1:6379> get name
"tianqing"
127.0.0.1:6379> ttl name
(integer) -1
127.0.0.1:6379> get name
(nil)
--------------------------------------------------
127.0.0.1:6379> set name soc
OK
127.0.0.1:6379> get name
"soc"
127.0.0.1:6379> expire name 1000
(integer) 1
127.0.0.1:6379> ttl name
(integer) 998
127.0.0.1:6379> persist name
(integer) 1
127.0.0.1:6379> ttl name
(integer) -1
----------------------------------------------------
127.0.0.1:6379> expire name 10
(integer) 1
127.0.0.1:6379> ttl name
(integer) 7
127.0.0.1:6379> ttl name
(integer) 2
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> persist name
(integer) 0
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379> ttl name
(integer) -2
-----------------------------------------------------------
127.0.0.1:6379> set name soc
OK
127.0.0.1:6379> get name
"soc"
127.0.0.1:6379> expire name 1000
(integer) 1
127.0.0.1:6379> ttl name
(integer) 997
127.0.0.1:6379> set name tianyan
OK
127.0.0.1:6379> get name
"tianyan"
127.0.0.1:6379> ttl name
(integer) -1
-----------------------------------------------------------
127.0.0.1:6379> sadd ids 1 2
(integer) 2
127.0.0.1:6379> smembers ids
1) "1"
2) "2"
127.0.0.1:6379> expire ids 1000
(integer) 1
127.0.0.1:6379> ttl ids
(integer) 997
127.0.0.1:6379> sadd ids 8
(integer) 1
127.0.0.1:6379> smembers ids
1) "1"
2) "2"
3) "8"
127.0.0.1:6379> ttl ids
(integer) 979
```

> expire返回1代表设置成功，返回0代表键不存在或者设置失败。
>
> 当键已经过期时，persist会失败。
>
> 对一个有过期时间的key进行set操作，过期时间会被清除，其他只对键值进行操作的命令（如INCR、LPUSH、HSET、ZREM）均不会影响键的生存时间。

EXPIRE命令的seconds参数必须是整数，所以最小单位是1秒。如果想要更精确的控制键的生存时间应该使用PEXPIRE命令，PEXPIRE命令与EXPIRE的唯一区别是前者的时间单位是毫秒，即PEXPIRE key 1000与EXPIRE key 1等价。对应地可以用PTTL命令以毫秒为单位返回键的剩余时间。

> 如果使用WATCH命令监测了一个拥有生存时间的键，该键时间到期自动删除并不会被WATCH命令认为该键被改变。

- Redis生存时间原理





- Redis内存淘汰策略

> 当Redis内存到达配置文件的maxmemory参数时，Redis会自动启动内存淘汰策略，根据maxmemory-policy参数指定的策略来删除不需要的键，直到Redis占用的内存小于指定内存。
>
> maxmemory-policy支持的规则如下所示：
>
> - volatile-lru：针对设置生存时间的键进行LRU删除
> - allkeys-lru：针对所有键进行LRU删除
> - volatile-random：针对设置过期时间的键随机删除
> - allkeys-random：针对所有键随机删除
> - volatile-ttl：删除生存时间最短的键
> - noeviction：不删除键，返回客户端内存超出异常
> - volatile-lfu：针对设置生存时间的键进行LFU删除，访问频率最低 （Redis4.0之后新增）
> - allkeys-lfu：针对所有键进行LFU删除（Redis4.0之后新增）

### <a id="rm-px">Redis排序</a>

> Redis中的SORT命令可以对列表类型、集合类型和有序集合类型键进行排序，并且可以完成与关系数据库中的连接查询相类似的任务。

```shell
127.0.0.1:6379> lpush ids 1 2 88 32 9 6
(integer) 6
127.0.0.1:6379> sort ids
1) "1"
2) "2"
3) "6"
4) "9"
5) "32"
6) "88"
127.0.0.1:6379> zadd game 30 zhangsan 49 wangwu 88 lisi
(integer) 3
    127.0.0.1:6379> sort game ALPHA
1) "lisi"
2) "wangwu"
3) "zhangsan"
```

> Sort可以对列表，集合，有序集合进行排序，当对有序集合使用时，会忽略分数，Sort排序时加上ALPHA，可以根据字典顺序进行排序。不加ALPHA参数，默认会转换为浮点型进行比较。

```shell
127.0.0.1:6379> sort ids desc limit 0 3
1) "88"
2) "32"
3) "9"
```

> 通过DESC或者ASC控制降序和升序，LIMIT控制分页。

```shell
127.0.0.1:6379> lpush ids4 1 2 3
(integer) 3
127.0.0.1:6379> set itemscore:1 20
OK
127.0.0.1:6379> set itemscore:2 30
OK
127.0.0.1:6379> set itemscore:3 -10
OK
127.0.0.1:6379> sort ids4
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> sort ids4 by itemscore:* DESC
1) "2"
2) "1"
3) "3"
------------------------------------------------------------
127.0.0.1:6379> sort ids4 by demo
1) "3"
2) "2"
3) "1"
```

> 通过BY参数可以实现类似于SQL里多表联查的功能，当不使用BY参数时，ids4排序是1 -> 2 -> 3，当使用BY参数将ids4的元素值给itemscore时，是根据itemscore:1,itemsocre:2,itemscore:3的值进行排序，因此顺序是2 -> 1 -> 3。
>
> 也可以将itemscore这个字符串换成某个Hash的某个属性。
>
> 当BY后面跟的是常量时，此时SORT的结果与LRANGE的结果相同，没有执行排序操作。
>
> 在不需要排序但需要借助SORT命令获得与元素相关联的数据时，常量键名是很有用的。

```shell
127.0.0.1:6379> sort ids4 by itemscore:* DESC get itemscore:*
1) "30"
2) "20"
3) "-10"
-----------------------------------------------------------------------
127.0.0.1:6379> sort ids4 by itemscore:* DESC get itemscore:* get #
1) "30"
2) "2"
3) "20"
4) "1"
5) "-10"
6) "3"
```

> GET参数不影响排序，它的作用是使SORT命令的返回结果不再是元素自身的值，而是GET参数中指定的键值。GET参数的规则和BY参数一样，GET参数也支持字符串类型和散列类型的键，并使用“＊”作为占位符。
>
> 在一个SORT命令中可以有多个GET参数（而BY参数只能有一个）
>
> get #可以获取元素本身的值

```shell
127.0.0.1:6379> sort ids4 by itemscore:* DESC get itemscore:* get # store result
(integer) 6
127.0.0.1:6379> lrange result 0 -1
1) "30"
2) "2"
3) "20"
4) "1"
5) "-10"
6) "3"
```

> 默认情况下SORT会直接返回排序结果，如果希望将排序结果保存到某个键，可以使用STORE参数。
>
> 排序结果会保存成列表的类型，如果该键已经存在，改键会被覆盖。
>
> SORT命令的时间复杂度是O(n+mlogm)，其中n表示要排序的列表（集合或有序集合）中的元素个数，m表示要返回的元素个数。当n较大的时候SORT命令的性能相对较低，并且Redis在排序前会建立一个长度为n[插图]的容器来存储待排序的元素，虽然是一个临时的过程，但如果同时进行较多的大数据量排序操作则会严重影响性能。

### <a id="rm-fbdy">Redis发布订阅</a>

> “发布/订阅”模式中包含两种角色，分别是发布者和订阅者。订阅者可以订阅一个或若干个频道（channel），而发布者可以向指定的频道发送消息，所有订阅此频道的订阅者都会收到此消息。

- 发布消息：PUBLISH channel message，返回值是收到这条命令的订阅者数量
- 订阅频道：SUBSCRIBE channel [channel …] 

- 取消订阅：UNSUBSCRIBE [channel [channel …]]，如果不指定频道会取消所有频道

> 执行SUBSCRIBE命令后客户端会进入订阅状态，处于此状态下客户端不能使用除SUBSCRIBE/UNSUBSCRIBE/PSUBSCRIBE/PUNSUBSCRIBE这4个属于“发布/订阅”模式的命令之外的命令。
>
> 除了可以使用SUBSCRIBE命令订阅指定名称的频道外，还可以使用PSUBSCRIBE命令订阅指定的规则。规则支持glob风格通配符格式。
>
> 使用PSUBSCRIBE命令可以重复订阅一个频道，如某客户端执行了PSUBSCRIBE channel.2 channel.2，这时向channel.2发布消息后该客户端会收到两条消息，而同时PUBLISH命令返回的值也是2而不是1。
>
> PUNSUBSCRIBE和UNSUBSCRIBE互不影响，无法退订对方订阅的频道。
>
> PUBSUB命令是Redis 2.8新增加的命令之一，客户端可以通过这个命令来查看频道或者模式的相关信息，比如某个频道目前有多少订阅者，又或者某个模式目前有多少订阅者，诸如此类。

```shell
127.0.0.1:6379> subscribe workorders assets
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "workorders"
3) (integer) 1
1) "subscribe"
2) "assets"
3) (integer) 2
1) "message"
2) "workorders"
3) "new-msg"
1) "message"
2) "assets"
3) "new-msg2"
--------------------------------------------------------------
127.0.0.1:6379> subscribe workorders
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "workorders"
3) (integer) 1
1) "message"
2) "workorders"
3) "new-msg"
---------------------------------------------------------------
127.0.0.1:6379> publish workorders new-msg
(integer) 2
127.0.0.1:6379> publish assets new-msg2
(integer) 1
127.0.0.1:6379> 
```

- 发布订阅原理

当一个客户端执行SUBSCRIBE命令订阅某个或某些频道的时候，这个客户端与被订阅频道之间就建立起了一种订阅关系。

Redis将所有频道的订阅关系都保存在服务器状态的pubsub_channels字典里面，这个字典的键是某个被订阅的频道，而键的值则是一个链表，链表里面记录了所有订阅这个频道的客户端：

```c
struct redisServer{
    //......
    //保存所有频道的订阅
    dict *pubsub_channels;
    //.........
}
```

> 订阅频道即将客户端加入链表的尾部，取消订阅则从链表中删除客户端。

服务器将所有频道的订阅关系都保存在服务器状态的pubsub_channels属性里面，与此类似，服务器也将所有模式的订阅关系都保存在服务器状态的pubsub_patterns属性里面：

```c
struct redisServer{
    //......
    //保存所有模式订阅
    list *pubsub_patterns;
    //.........
}
```

pubsub_patterns属性是一个链表，链表中的每个节点都包含着一个pubsub Pattern结构，这个结构的pattern属性记录了被订阅的模式，而client属性则记录了订阅模式的客户端：

```c
typedef struct pubsubPattern{
    //订阅模式的客户端
    redisClient *client;
    //被订阅的模式
	robj *patterns;
}pubsubPattern;
```

> 客户端订阅即创建一个pubsubPattern结构，加入链表中，退订即删除对应的结构

当客户端往指定频道PUBLISH消息时，会执行以下两步：

1. 将消息message发送给channel频道的所有订阅者。
2. 如果有一个或多个模式pattern与频道channel相匹配，那么将消息message发送给pattern模式的订阅者。

### <a id="rm-gd">Redis管道</a>

Redis底层采用了TCP协议，因此每次客户端和服务器通信都需要三次握手，而对于Redis来说，可能网络IO的消耗比Redis本身执行操作的消耗更大，因此RESP协议底层提供了管道的支持。

Redis的管道和HTTP的管道类似，都是一种批量操作的机制，通过一次网络IO将多个命令发往服务端，服务端再返回批量结果。

> 具体管道的实现需要客户端完成，redis-cli客户端不支持管道功能。
>
> 需要注意的一点是pipeline发送的多条命令不具备原子性，有可能在一个pipeline的多条命令之间插入其他客户端的命令。
>
> Redis管道的官方文档：https://redis.io/topics/pipelining

### <a id="rm-jb">Redis脚本</a>

Redis在2.6版推出了脚本功能，允许开发者使用Lua语言编写脚本传到Redis中执行。在Lua脚本中可以调用大部分的Redis命令。

- 脚本优点

  - 减少网络开销，和管道类似，减少网络IO
  - 原子操作，不需要Redis事务不断Watch实现CAS，Redis执行Lua脚本是原子性
  - 复用，客户端发送的脚本会永久存储在Redis中，这就意味着其他客户端（可以是其他语言开发的项目）可以复用这一脚本而不需要使用代码完成同样的逻辑。

- 脚本的使用
  - eval 脚本名 [key值] , [args]
  - evalsha 脚本名 [key值] , [args]

Redis提供了EVAL命令可以使开发者像调用其他Redis内置命令一样调用脚本。

EVAL命令的格式是：EVAL脚本内容key参数的数量[key …][arg …]。可以通过key和arg这两类参数向脚本传递数据，它们的值可以在脚本中分别使用KEYS和ARGV两个表类型的全局变量访问。

考虑到在脚本比较长的情况下，如果每次调用脚本都需要将整个脚本传给Redis会占用较多的带宽。为了解决这个问题，Redis提供了EVALSHA命令允许开发者通过脚本内容的SHA1摘要来执行脚本，该命令的用法和EVAL一样，只不过是将脚本内容替换成脚本内容的SHA1摘要。

Redis在执行EVAL命令时会计算脚本的SHA1摘要并记录在脚本缓存中，执行EVALSHA命令时Redis会根据提供的摘要从脚本缓存中查找对应的脚本内容，如果找到了则执行脚本，否则会返回错误：“NOSCRIPT No matching script. Please useEVAL.”

![image-20200924130023713](http://rocks526.top/lzx/image-20200924130023713.png)

![image-20200924130047764](http://rocks526.top/lzx/image-20200924130047764.png)

> Lua脚本教程：https://www.runoob.com/lua/lua-tutorial.html
>
> https://www.cnblogs.com/kaituorensheng/p/11098194.html

### <a id="rm-xy">Redis协议</a>

- RESP协议介绍

Redis客户端和Redis服务端通信采用的协议叫做RESP(REdis Serialization Protocol)。RESP是专为Redis设计的协议，底层基于TCP，但该协议也可用于其他C/S程序。RESP协议设计初衷有以下三点：

1. 易于实现
2. 快速解析
3. 人类可读

RESP协议的通信模型在大部分情况下是请求-响应模型，在管道模式下使用批处理，在发布订阅模式下改用推送协议。

> Redis官方介绍，RESP协议不但易于阅读，易于实现，而且性能可以和二进制协议比肩。
>
> RESP协议的命令有一个记录长度的前缀，因此无需像JSON一样全部扫描，而且每个命令有/r/n结尾标志。同时RESP协议没有HTTP协议的缓冲控制、校验和之类的冗余字段，因此性能很高。
>
> 目前最新的Redis6.0.8版本采用的是RESP3协议。
>
> RESP协议的官方文档：https://redis.io/topics/protocol

- RESP协议标准

RESP协议的整体结构比较简单，针对不同的命令或回复，有不同的前缀，每个命令通过\r\n结尾，同时每个命令的长度会被一个标志位记录，因此是二进制安全的。

当RESP协议用作请求-响应模型时，Redis客户端将命令作为RESP的多行字符串类型发送到Redis服务器，Redis服务器根据命令实现以RESP类型之一进行回复。RESP类型包括以下几种：

1. 错误回复

错误回复（error reply）以-开头，并在后面跟上错误信息，最后以\r\n结尾:

> -ERR unknown command 'ERRORCOMMAND'\r\n  ==>  ERRORCOMMAND

2. 状态回复

状态回复（status reply）以+开头，并在后面跟上状态信息，最后以\r\n结尾：

> +OK\r\n  ==>  OK

3. 整数回复

整数回复（integer reply）以:开头，并在后面跟上数字，最后以\r\n结尾：

> :3\r\n  ==>  3

4. 字符串回复

字符串回复（bulk reply）以$开头，并在后面跟上字符串的长度，并以\r\n分隔，接着是字符串的内容和\r\n：

> $4\r\ndemo\r\n  ==>  demo

5. 多行字符串回复

多行字符串回复（multi-bulk reply）以＊开头，并在后面跟上字符串回复的组数，并以\r\n分隔。接着后面跟的就是字符串回复的具体内容了：

> *3\r\n$1\r\n3\r\n$1\r\n$1\r\n1\r\n  ==>  3 2 1

- RESP协议示例

> 测试RESP协议可以使用redis-java-client客户端来测试，此客户端可以通过输入和输出流作为入参传给Redis实例，因此可以替换其中一个流来将内容输出到文件

测试客户端往服务端发送命令生成的RESP协议内容：

```java
    public static void main(String[] args) throws IOException {
        //------------- 查看客户端发送请求的RESP格式   -----------------
        FileOutputStream fileOutputStream = new FileOutputStream(new File("respSend.txt"));
        Redis redis = new Redis(null,fileOutputStream);
        //String类型
        redis.call("set", "myName", "DEMO");
        redis.call("---------------------------");
        //list类型
        redis.call("lpush", "Names", "TIMA","TEST","TEST2");
        redis.call("---------------------------");
    }
// 生成文件内容如下,由于将输出内容写入了文本，因此自动将\r\n转换为换行符了
*3
$3
set
$6
myName
$4
DEMO
*1
$27
---------------------------
*5
$5
lpush
$5
Names
$4
TIMA
$4
TEST
$5
TEST2
*1
$27
---------------------------
```

测试服务端响应的RESP协议内容：

```java
    public static void main(String[] args) throws IOException {
        //------------- 查看服务端响应的RESP格式   -----------------
        FileOutputStream fileOutputStream = new FileOutputStream(new File("respRecv.txt"));
        Socket redisSocket = new Socket("127.0.0.1", 6379);
        Redis redis = new Redis(redisSocket.getInputStream(),redisSocket.getOutputStream());
        //String类型
        redis.call("set", "myName", "ZHANGSAN");
        //list类型
        redis.call("lpush", "Names", "TEST","DEMO","TEST2");
        //Pipeline
        redis.pipeline().call("set","version","1.5.1")
                .call("rpush","dependences","redis","springcloud","kafka")
                .call("get","version")
                .call("get","dependences")
                .call("lrange","dependences","0","10");
        readFromRedisAndWriteFile(redis.reader.input,fileOutputStream);
    }

    public static void readFromRedisAndWriteFile(InputStream inputStream,OutputStream outputStream) throws IOException {
        int msg = inputStream.read();
        while (msg != -1){
            outputStream.write(msg);
            msg = inputStream.read();
        }
    }
// 生成文件内容如下,由于将输出内容写入了文本，因此自动将\r\n转换为换行符了
+OK
:9
+OK
:9
$7
1.5.1
-WRONGTYPE Operation against a key holding the wrong kind of value
*9
$5
redis
$11
springcloud
$5
kafka
$5
redis
$11
springcloud
$5
kafka
$5
redis
$11
springcloud
$5
kafka
```

### <a id="rm-cjh">Redis持久化</a>

> Redis的强劲性能很大程度上是由于其将所有数据都存储在了内存中，为了使Redis在重启之后仍能保证数据不丢失，需要将数据从内存中以某种形式同步到硬盘中，这一过程就是持久化。
>
> Redis有两种持久化方案，AOF和RDB，可以单独使用其中一种或者两种结合使用，默认采用RDB方式。
>
> RDB是采用快照的方式，类似于MySQL的dump，AOF是采用写日志的方式，类似于MySQL的binlog

#### RDB方式

- RDB介绍

RDB方式的持久化是通过快照（snapshotting）完成的，当符合一定条件时Redis会自动将内存中的所有数据进行快照并存储在硬盘上。

进行快照的条件可以由用户在配置文件中自定义，由两个参数构成：时间和改动的键的个数。当在指定的时间内被更改的键的个数大于指定的数值时就会进行快照。

RDB是Redis默认采用的持久化方式，在配置文件中已经预置了3个条件：

``` shell
save 900 1    ----------    十五分钟有一个键被改变
save 300 10   ----------    五分钟有十个键被改变
save 60 10000  ----------    一分钟有一万个键被改变
```

save参数指定了快照条件，可以存在多个条件，条件之间是“或”的关系。如果想要禁用自动快照，只需要将所有的save参数删除即可。

- RDB相关配置

> save 900 1   ==>  save触发条件
>
> dbfilename   ==>  保存的快照文件名字，默认rdb.dump
>
> dir   ==>  保存的快照文件存储目录
>
> stop-writes-on-bgsave-error  ==>   BGSAVE出现异常时是否停止Redis写入 建议开启
>
> rdbcompression  ==>   生成的快照文件是否压缩 建议开启
>
> rdbchecksum   ==>   加载rdb文件时是否计算校验和 建议开启

- RDB原理

当Redis的快照满足条件之后，会自动执行BGSAVE命令实现快照备份。BGSAVE命令执行过程如下：

1. redis使用fork函数复制一份当前进程的副本(子进程)

2. 父进程继续接收并处理客户端发来的命令，而子进程开始将内存中的数据写入硬盘中的临时文件

3. 当子进程写入完所有数据后会用该临时文件替换旧的RDB文件，至此，一次快照操作完成。 

> 在执行fork的时候操作系统（类Unix操作系统）会使用写时复制（copy-on-write）策略，即fork函数发生的一刻父子进程共享同一内存数据，当父进程要更改其中某片数据时（如执行一个写命令），操作系统会将该片数据复制一份以保证子进程的数据不受影响，所以新的RDB文件存储的是执行fork一刻的内存数据。
>
> redis在进行快照的过程中不会修改RDB文件，只有快照结束后才会将旧的文件替换成新的，也就是说任何时候RDB文件都是完整的。 这就使得我们可以通过定时备份RDB文件来实现redis数据库的备份， RDB文件是经过压缩的二进制文件，占用的空间会小于内存中的数据，更加利于传输。

除了BGSAVE命令之外，Redis还有一个快照备份的命令：SAVE。此命令不同与BGSAVE的fork子进程去备份，此命令会使用主进程备份，备份期间会阻塞客户端请求。

除了满足配置文件的save条件会进行BGSAVE之外，还可以手动调用SAVE或者BGSAVE方法进行备份，除此以外，当主从节点全量复制时，也会触发BGSAVE命令，即便配置文件将所有的save配置删除。还有在Redis重启reload的时候和Redis关闭发送shutdown命令时也会触发BGSAVE。

- RDB特点
  - 使用RDB方式实现持久化，一旦Redis异常退出，就会丢失最后一次快照以后更改的所有数据。
  - RDB父进程在保存RDB文件时唯一要做的就是fork出一个子进程，然后这个子进程就会处理接下来的所有保存工作，父进程无序执行任何磁盘I/O操作。同时这个也是一个缺点，如果数据集比较大的时候，fork可以能比较耗时，造成服务器在一段时间内停止处理客户端的请求；
  - RDB方式在Redis重启时会自动加载之前备份的rdb文件恢复Redis数据，加载速度相比较AOF会更快。

#### AOF方式

> RDB方式在生成快照时需要扫描整个数据库所有键，O(N)操作，且BGSAVE时采用fork子进程进行写时复制机制，会加大内存消耗，最为重要的是RDB方式会丢失最后一次快照之后的数据。为了解决RDB方式的缺陷，引入了AOF方式。

- AOF介绍

AOF方式是通过备份Redis执行的每条写入/更新命令实现数据库备份的。开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件。在启动时Redis会逐个执行AOF文件中的命令来将硬盘中的数据载入到内存中，载入的速度相较RDB会慢一些。

默认情况下Redis没有开启AOF。

- AOF原理

Redis的AOF方式会将所有客户端发来的写入/更新命令保存进aof文件，该文件是一个文本文件，写入的内容是客户端发来命令的RESP协议内容。

AOF方式会存在随着时间增加，aof文件越来越大的问题，如对于一个频繁修改的key，其实之前的所有修改操作都是过期的，只有最后一次修改是有用的，而AOF方式会将所有修改记录都保存下来，因此Redis提供了AOF重写机制来解决这个问题。

Redis在AOF文件大小越来越大时，会自动在后台调用bgrewriteaof命令实现aof文件重写：新的aof文件不是基于旧的aof文件进行删除冗余命令，而是根据现在的数据库环境还原需要执行的最小命令集。整个重写操作是绝对安全的，因为 Redis 在创建新 AOF 文件的过程中，会继续将命令追加到现有的 AOF 文件里面，即使重写过程中发生停机，现有的 AOF 文件也不会丢失。 而一旦新 AOF 文件创建完毕，Redis 就会从旧 AOF 文件切换到新 AOF 文件，并开始对新 AOF 文件进行追加操作。

**日志重写原理：**

- 调用fork()，创建一个子进程
- 子进程把新的AOF文件写到一个临时文件里，不依赖原来的AOF文件
- 主进程持续将新的变动同时写到一个缓冲区和原来的AOF里
- 主进程获取子进程重写AOF的完成信号，往新AOF同步缓冲区里的增量变动
- 使用新的AOF文件替换掉旧的AOF文件

> 当满足配置文件里定义的对于AOF文件重写阈值时，Redis会自动进行AOF重写，或者客户端发送bgrewriteaof命令也可以手动让AOF文件重写。

- AOF存在问题

redis每次更改数据的时候， AOF机制都会将命令记录到AOF文件，但是实际上由于操作系统的缓存机制，数据并没有实时写入到硬盘，而是进入硬盘缓存。在默认情况下系统每30秒会执行一次同步操作，以便将硬盘缓存中的内容真正地写入硬盘，在这30秒的过程中如果系统异常退出则会导致硬盘缓存中的数据丢失。一般来讲启用AOF持久化的应用都无法容忍这样的损失，这就需要Redis在写入AOF文件后主动要求系统将缓存内容同步到硬盘中。在Redis中我们可以通过appendfsync参数设置同步的时机。

- AOF配置

> appendonly  ==>  是否开启AOF持久化方式 ，默认no ，设置为yes打开AOF
>
> appendfilename  ==>  AOF文件名字
>
> dir  ==>   AOF文件目录
>
> auto-aof-rewrite-min-size  ==>  AOF重写开启的最小文件大小，大于此值的AOF文件才会被重写
>
> auto-aof-rewerite-percentage  ==>  AOF文件增长率，当AOF文件多出上次重写的这个百分比后再次进行重写
>
> appendfsync  ==>  操作系统刷盘策略，可选always，no，everysec三种，always代表每次执行写入命令都让操作系统刷盘一次，no代表不管操作系统的刷盘机制，由操作系统自行决定，everysec代表让操作系统每秒钟刷盘一次。默认采用everysec
>
> no-appendfsync-on-rewrite：在AOF重写时不主动刷盘

- AOF文件修复

服务器可能在程序正在对 AOF 文件进行写入时停机， 如果停机造成了 AOF 文件出错（corrupt）， 那么 Redis 在重启时会拒绝载入这个 AOF 文件， 从而确保数据的一致性不会被破坏。

如：set key value命令写入时刚写入key停机，value没有写入

当发生这种情况时， 可以用以下方法来修复出错的 AOF 文件：

- 为现有的 AOF 文件创建一个备份。
- 使用 Redis 附带的 redis-check-aof 程序，对原来的 AOF 文件进行修复。

**redis-check-aof --fix**

重启 Redis 服务器，等待服务器载入修复后的 AOF 文件，并进行数据恢复。

#### RDB和AOF对比

| 命令         | RDB        | AOF                    |
| ------------ | ---------- | ---------------------- |
| 启动优先级   | 低         | 高                     |
| 占用磁盘大小 | 小         | 大                     |
| 恢复速度     | 快         | 慢                     |
| 数据安全性   | 丢数据     | 根据策略决定，相对安全 |
| 轻重         | 重量级操作 | 轻量级操作             |

> 建议关闭RDB备份方式，采用主从架构时可以从节点开启RDB，减少对性能的影响。
>
> 建议开启AOF备份方式，设置每秒刷盘一次。

#### RDB和AOF混合模式

为了优化AOF在启动时加载AOF文件慢的问题和AOF重写时CPU消耗大的问题，Redis4.0之后提供了混合模式：

通过配置文件的aof-use-rdb-preamble参数设置为true开启混合模式，在混合模式下，aof重写时子进程不会再计算当前环境需要的最小命令子集，而是将当前环境的数据以RDB的方式写入aof文件，缓冲区的增量命令以aof方式写入，最后将新的aof文件覆盖旧的aof文件。

在Redis重新启动时，会优先加载AOF文件，如果存在RDB格式，则先加载RDB，然后加载AOF。

#### 常见问题

- fork操作

执行fork复制子进程时是同步操作，会短暂阻塞，当要复制的内存特别大时，可能阻塞时间较长

> 可以优先使用物理机或者高效支持fork操作的虚拟化技术，设置redis最大内存大小，可以多分片
>
> 降低AOF重写频率，减少fork频率

- CPU开销

RDB文件和AOF文件重写都属于CPU密集型开销，应尽量避开，同时Redis与CPU密集型应用分离部署。

- AOF追加阻塞

当设置AOF刷盘每秒一次时，整体执行流程如下：

1. 主线程将命令写入缓冲区
2. 同步线程负责将命令刷盘
3. 主线程检查上次刷盘时间，小于2s时则跳过，进行后续操作，如果超过2s没有刷盘，主线程则会阻塞，等待刷盘

### <a id="rm-aq">Redis安全</a>

> Redis的理念是简介至上，对于安全相关并没有提供很多的保护措施，主要取决于应用环境，应该处于安全的内网环境。
>
> 在Redis6.0之前，Redis只提供默认用户名default，密码可以通过配置文件或者config set命令设置。Redis提供了命令重命名的功能，可以将某些高危操作重命名。
>
> 在Redis6.0之后，Redis对安全方面进行增强，新增了SSL。同时新增了ACL（Access Control List）机制。

#### 绑定指定IP

Redis默认是允许任何IP地址的客户端访问的，可以通过redis.conf配置文件里的bind参数绑定指定的IP访问。

#### Redis设置密码

- 方式一

Redis配置文件里有一个requirepass参数，可以通过该参数设置Redis密码。

- 方式二

Redis启动后，连入Redis客户端，通过config get requirepass命令查看当前Redis的密码，通过config set requirepass password设置密码

- 认证

Redis设置密码后，客户端登录Redis需要认证，redis-cli客户端通过auth命令认证，其他客户端类似

![image-20200928155419042](http://rocks526.top/lzx/image-20200928155419042.png)

![image-20200928155620451](http://rocks526.top/lzx/image-20200928155620451.png)

> 以Redis的性能，每秒30W的QPS，Redis密码很容易暴力破解，因此Redis密码的安全程度并不是很高，主要还是需要Redis处于内网，一个安全的环境。

#### 命令重命名

Redis中存在很多高危命令，主要命令如下：

- flushdb,清空数据库

- flushall,清空所有记录，数据库

- config,客户端连接后可配置服务器

- keys,客户端连接后可查看所有存在的键

对于这些高危命令，Redis提供重命名的功能，可以将这些命令重命名为别的命令，如果想禁用，重命名为空串即可。

通过redis.conf配置文件的SECURITY参数设置：

![image-20200928160231112](http://rocks526.top/lzx/image-20200928160231112.png)

![image-20200928160617260](http://rocks526.top/lzx/image-20200928160617260.png)

#### ACL

>  在Redis6之前的版本，我们只能使用requirepass参数给default用户配置登录密码，同一个redis集群的所有开发都共享default用户，难免会出现误操作把别人的key删掉或者数据泄露的情况，那之前我们也可以使用rename command的方式给一些危险函数重命名或禁用，但是这样也防止不了自己的key被其他人访问。
>
> 因此**Redis6**版本推出了**ACL(Access Control List)访问控制权限**的功能，基于此功能，我们可以设置多个用户，并且给每个用户单独设置命令权限和数据权限。 为了保证**向下兼容**，Redis6保留了default用户和使用requirepass的方式给default用户设置密码，默认情况下default用户拥有Redis最大权限，我们使用redis-cli连接时如果没有指定用户名，用户也是默认default。
>
> 我们可以在配置文件中或者命令行中设置ACL，如果使用配置config文件的话需要重启服务，使用配置aclfile文件或者命令行授权的话无需重启Redis服务但需要及时将权限持久化到磁盘，否则下次重启的时候无法恢复该权限。 
>
> Redis官网文档：https://redis.io/topics/acl
>
> ACL介绍：https://blog.csdn.net/wsdc0521/article/details/106765856

- 配置文件方式

配置ACL的方式有两种，一种是在config文件中直接配置，另一种是在外部aclfile中配置。配置的命令是一样的，但是两种方式只能选择其中一种。

**在redis.conf配置文件里设置用户**

![image-20200928164222672](http://rocks526.top/lzx/image-20200928164222672.png)

![image-20200928164205772](http://rocks526.top/lzx/image-20200928164205772.png)

> 此条配置的意思是新增一条用户，用户名user1，密码为password1，只能访问以jobs:开头的键，只能执行list和connection相关的命令

**在acl文件中设置（推荐，支持热加载）**

![image-20200928165433630](http://rocks526.top/lzx/image-20200928165433630.png)

![image-20200928165718766](http://rocks526.top/lzx/image-20200928165718766.png)

![image-20200928165815403](http://rocks526.top/lzx/image-20200928165815403.png)

> 当acl文件里不配置default用户时，该用户为nopass，即无密码，当用户状态为off时，无法登陆

- 命令行方式

ACL LIST : 获取所有用户信息，密码是SHA256加密的密文

ACL SETUSER:创建或编辑用户

ACL GETUSER：获取某个用户信息

ACL DELUSER：删除某个用户

ACL USERS：返回所有用户名

ACL WHOAML：获取当前用户

ACL CAT：查看命令类型

ACL SAVE：将当前的用户信息保存到acl文件里

ACL LOAD：热加载acl文件里的用户

ACL LOG：查看ACL日志

- ACL规则

**开启或禁用用户**

- `on`：启用用户：可以以该用户身份进行认证。
- `off`：禁用用户：不再可以使用此用户进行身份验证，但是已经通过身份验证的连接仍然可以使用。

**允许或禁止的调用命令**

- `+<command>`：将命令添加到用户可以调用的命令列表中。
- `-<command>`：将命令从用户可以调用的命令列表中移除。
- `+@<category>`：允许用户调用 <category> 类别中的所有命令，有效类别为@admin，@set，@sortedset等，可通过调用ACL CAT命令查看完整列表。特殊类别@all表示所有命令，包括当前和未来版本中存在的所有命令。
- `-@<category>`：禁止用户调用<category> 类别中的所有命令。
- `+<command>|subcommand`：允许使用已禁用命令的特定子命令。
- `allcommands`：+@all的别名。包括当前存在的命令以及将来通过模块加载的所有命令。
- `nocommands`：-@all的别名，禁止调用所有命令。

**允许或禁止访问某些key**

-  `~<pattern>`：添加可以在命令中提及的键模式。例如`~*和* allkeys `允许所有键。
- \* `resetkeys`：使用当前模式覆盖所有允许的模式。如： ~foo:* ~bar:* resetkeys ~objects:* ，客户端只能访问匹配 object:* 模式的 KEY。

**为用户配置密码**

- `><password>`：将此密码添加到用户的有效密码列表中。例如，`>mypass`将“mypass”添加到有效密码列表中。该命令会清除用户的*nopass*标记。每个用户可以有任意数量的有效密码。
- `<<password>`：从有效密码列表中删除此密码。若该用户的有效密码列表中没有此密码则会返回错误信息。
- `#<hash>`：将此SHA-256哈希值添加到用户的有效密码列表中。该哈希值将与为ACL用户输入的密码的哈希值进行比较。允许用户将哈希存储在users.acl文件中，而不是存储明文密码。仅接受SHA-256哈希值，因为密码哈希必须为64个字符且小写的十六进制字符。
- `!<hash>`：从有效密码列表中删除该哈希值。当不知道哈希值对应的明文是什么时很有用。
- `nopass`：移除该用户已设置的所有密码，并将该用户标记为nopass无密码状态：任何密码都可以登录。*resetpass*命令可以清除nopass这种状态。
- `resetpass`：情况该用户的所有密码列表。而且移除*nopass*状态。*resetpass*之后用户没有关联的密码同时也无法使用无密码登录，因此*resetpass*之后必须添加密码或改为*nopass*状态才能正常登录。
- `reset`：重置用户状态为初始状态。执行以下操作resetpass，resetkeys，off，-@all。

### <a id="rm-jq">Redis集群</a>

#### Redis主从

- 单机Redis存在的问题
  - 单点故障
  - 内存容量瓶颈
  - QPS瓶颈
- Redis主从模型介绍

![image-20200930103623700](http://rocks526.top/lzx/image-20200930103623700.png)

> Redis支持多个主从模型，从节点也可以是某几个节点的主节点。
>
> 一个主节点可以有多个从节点，而多个从节点只能有一个主节点。
>
> Redis为了解决单点故障和瓶颈问题，Redis提供了主从模型来搭建Redis集群，可以实现读写分离，主从备份。默认主节点负责写入，从节点负责读请求，从节点通过异步复制从主节点拉取最新数据。

- 实现方式

主节点无需修改任何配置，从节点通过slaveof参数配置主节点的IP和端口，实现数据同步

> Redis5.0之后，slave改名为replicas，配置文件的slaveof参数改为replicaof
>
> - 单机模拟Redis集群，需要把端口改掉
> - 如果主节点开启了auth认证，从节点需要配置主节点的用户名和密码信息
> - 除了配置文件配置slaveof参数外，也可以在从节点动态执行slaveof命令连接某个主节点

单机模拟Redis主从模型，Redis主节点6379，从节点6380和6381，未设置auth信息。

`主节点`信息如下所示：

![image-20200930110033073](http://rocks526.top/lzx/image-20200930110033073.png)

![image-20200930110159606](http://rocks526.top/lzx/image-20200930110159606.png)

`从节点`信息如下所示：

![image-20200930110442046](http://rocks526.top/lzx/image-20200930110442046.png)

`测试主从同步`：

![image-20200930110612940](http://rocks526.top/lzx/image-20200930110612940.png)

![image-20200930110557773](http://rocks526.top/lzx/image-20200930110557773.png)

- 主从复制原理

Redis主从复制的整体流程分为三步：

1. 复制初始化阶段：从库向主库建立socket连接，发送ping心跳，检测连通性，如果主库设置密码则还需要发送auth命令
2. 数据同步阶段：此阶段是从库在第一次连接主库或者重启，主从切换之后连接主库，实现数据同步的阶段。
3. 命令传播阶段：完成从库和主库的初始数据同步之后，主库和从库之间会维持着心跳检测，从库每秒向主节点发送REPLCONF ACK命令，该命令会携带自己同步数据的偏移量offset，主库会每隔一段时间（默认10秒，通过repl-ping-slave-period参数指定）根据从库的偏移量向从库同步最新的增量数据。

Redis2.8和Redis4.0分别对数据同步阶段做了优化，在Redis2.8之前，从库和主库的同步是发送SYNC命令，全量同步的方式，在Redis2.8之后，同步是通过PSYNC命令携带主库的run_id和offset实现增量同步，Redis4.0之后，对PSYNC命令进行优化，主要是在从库重启或故障转移时也能实现增量同步。

在Redis2.8之前，主从之间的复制都是**全量复制**，主要流程如下：

![image-20200930112627617](http://rocks526.top/lzx/image-20200930112627617.png)

1. 从节点向主节点发送PING心跳检测
2. 主节点回复PONG
3. 从节点发送SYNC命令请求同步数据
4. 主节点后台BGSAVE保存快照，同时将这段时间客户端发来的写入和更新请求的命令写入缓冲区
5. 主节点完成后将生成的rdb文件和缓冲区一块发送给slave
6. slave清空本地的旧数据，加载rdb文件，执行缓冲区命令

全量复制存在的问题：

1. 主服务器需要执行BGSAVE命令来生成RDB文件，这个生成操作会耗费主服务器大量的CPU、内存和磁盘I/O资源
2. RDB文件传输网络延时大，主从延时大
3. 从节点每次都要清空旧数据，加载rdb文件和增量命令，执行速度慢
4. 当网络波动，主节点执行耗时命令，从节点重启，主从故障时，会频繁执行全量同步，导致主节点频繁BGSAVE

为了解决全量复制存在的问题，Redis在2.8之后推出了增量复制模式——PSYNC命令，主要流程如下：

![image-20200930150034666](http://rocks526.top/lzx/image-20200930150034666.png)

1. 当从库第一次启动或主从库网络波动断连后，从库向主库发送PSYNC命令，携带自己保存的主库的runid和之前同步到offset，如果第一次连接主库，则runid为`？`，offset为-1
2. 主库接到PSYNC命令，获取runid和offset，根据runid和offset判断是进行全量同步还是增量同步
3. 如果从库发送的runid和主库的runid相同，则说明之前从库同步过数据，则需要同步offset之后的新数据，判断当前replication backlog buffer（复制积压缓冲区）是否存在该offset之后的数据，如果存在则将新的数据增量发送给从库，如果不存在，则断连时间太长，执行全量复制。

> runid是Redis启动时会为每一个Redis实例分配一个40位的唯一ID，用于标识每个实例。
>
> replication backlog buffer(复制积压缓冲区)是一个FIFO的队列，里面存放offset和对应的命令，保存最新执行的一些命令。
>
> PSYNC的增量复制就是通过主节点的复制积压缓冲区保存最近命令，从节点offset确定从库同步到的数据，然后进行增量复制。
>
> 当主从第一次连接，从库不知道主库的runid，会发送一个PSYNC ? -1 ，之后主库将全量复制的数据给从库，同时将自己的runid给从库，从库会保存runid，之后PSYNC会携带该runid。

PYNC存在的问题：

1. runid是唯一的，当主库重启或主从故障自动切换时runid会改变，从库全部需要全量同步，主库会大量BGSAVE
2. 当从库重启，之前保存的runid和offset会丢失，会进行全量同步

在Redis4.0版本，对PSYNC进行了优化，解决了从库重启和主从切换时全量同步的问题，新增了以下参数：

`master_replid`: 复制id1(后文简称：replid1)，一个长度为41个字节(40个随机串+’0’)的字符串，每个redis实例都有，和runid没有直接关联，但和runid生成规则相同。当实例变为从实例后，自己的replid1会被主实例的replid1覆盖。

`master_replid2`：复制id2(后文简称:replid2),默认初始化为全0，用于存储上次主实例的replid1。

当从节点重启时，通过shutdown save会调用rdbSaveInfoAuxFields函数，把当前实例的repl-id和repl-offset保存到RDB文件中,当前的RDB存储的数据内容和复制信息是一致性的可通过redis-check-rdb命令查看。

从节点重启后，加载RDB文件，会专门处理文件中辅助字段(AUX fields）信息，把其中repl_id和repl_offset加载到实例中，分别赋给master_replid和master_repl_offset两个变量值，此时从库即实现重启也拥有之前的同步信息。之后和主库同步，会携带之前的同步信息，主库进行判断是全量同步还是增量同步。

> 特别注意当从库开启了AOF持久化，redis加载顺序发生变化优先加载AOF文件，但是由于aof文件中没有复制信息，所以导致重启后从实例依旧使用全量复制！

psync2除了解决redis重启使用部分同步外，还为解决在主库故障时候从库切换为主库时候使用部分同步机制。redis从库默认开启复制积压缓冲区功能，以便从库故障切换变化master后，其他落后该从库可以从缓冲区中获取缺少的命令。该过程的实现通过两组replid、offset替换原来的master runid和offset变量实现：

`第一组：master_replid和master_repl_offset`：如果redis是主实例，则表示为自己的replid和复制偏移量； 如果redis是从实例，则表示为自己主实例的replid1和同步主实例的复制偏移量。

`第二组：master_replid2和second_repl_offset`：无论主从，都表示自己上次主实例repid1和复制偏移量。

主从故障时，即便自动故障转移，master_replid1变化了，但之前的master_replid1会保留在master_replid2里，从库依旧可以根据master_replid2判断之前同步过。

![image-20201014100431567](http://rocks526.top/lzx/image-20201014100431567.png)

> 参考文档：https://www.cnblogs.com/wdliu/p/9407179.html
>
> 复制积压缓冲区默认1MB，通过配置文件修改，推荐10MB，根据2\*QPS*断线重连时间计算
>
> 在Redis2.8之后，除了新增PSYNC之外，还新增了主从之间的心跳检测，从节点会每秒向主节点发送REPLCONF ACK进行心跳检测，同时携带自己的offset
>
> 通过心跳检测，主节点可以检测从节点的最新同步进度，检测是否命令传播时发生丢失，如果发生丢失，进行补发操作。
>
> 同时min-slaves-to-write功能也是通过心跳检测实现的。

- 相关配置

> replicaof  masterip  masterport  ==>  主节点IP和端口
>
> masterauth master-password  ==>  主节点密码
>
> masteruser  username  ==>  主节点用户名 （Redis6.0之后）
>
> replica-serve-stale-data yes  ==>  主从同步时对客户端的反应 设置为yes 则即便同步还未完成 副本也会响应客户端请求 此时可能副本和主节点数据不一致  设置no则同步完成之前 副本不可用
>
> replica-read-only yes  ==>  副本是否只可读
>
> repl-diskless-sync no   ==>  是否开启无盘复制   （Redis6.0之后）
>
> repl-diskless-sync-delay 5   ==>   开启无盘复制时，设置的延时（Redis6.0之后）
>
> repl-ping-replica-period 10   ==>   命令传播阶段，主从库之间的延时
>
> repl-backlog-size 1mb  ==>   复制积压缓冲区大小  最好设置10MB
>
> min-slaves-to-write 3
> min-slaves-max-lag 10
> #设置某个时间断内，如果从库数量小于该某个值则不允许主机进行写操作，以上参数表示10秒内如果主库的从节点小于3个，则主库不接受写请求，min-slaves-to-write 0代表关闭此功能。

- 主从模式存在的问题

1. 高可用问题

现在的主从模型，虽然实现了持久化，读写分离，避免单点故障，但当主节点挂掉时，没办法实现故障自动转移，需要人工干预。

> Redis可以通过slaveof命令让从库监听另一个主库，通过slaveof no one将自己设置为主库，虽然这些命令可以实现主从切换，但Redis没办法自动故障转移，需要写脚本不断监测Redis状态，当Redis挂了的时候实现故障转移。

2. 读写分离问题

由于Redis主从模型不是数据强一致性模型，Redis只要求主从最终数据一致性，主从之前可能出现数据不一致的情况，用户可能从节点读到部分旧的数据。

由于Redis的内存空间管理策略，对过期key主要通过定期扫描和懒加载策略删除，因此可能存在从库读取到过期key的问题，此问题在Redis3.2版本修复，通过从节点维护一个逻辑时钟，判断该key是否过期，如果过期，则不再返回，等待主节点发送DEL命令再进行删除。每次主从同步时会核对该逻辑时钟的时间。

> 虽然每次Redis主从同步都会核对从节点的逻辑时钟，但不同的节点仍可能存在偏差，因此只能保证一定程度的准确性。

#### Redis哨兵

> 为了解决Redis主从存在的无法故障转移的问题，Redis推出了Redis Sentinel(哨兵)。Redis Sentinel通过引入一组Sentinel节点监听Redis主节点，实现故障自动转移，Redis高可用。

Sentinel是Redis的高可用性解决方案：由一个或多个Sentinel实例组成的Sentinel系统可以监视任意多个主服务器，以及这些主服务器属下的所有从服务器，并在被监视的主服务器进入下线状态时，自动将下线主服务器属下的某个从服务器升级为新的主服务器，然后由新的主服务器代替已下线的主服务器继续处理命令请求。

![image-20201014141707412](http://rocks526.top/lzx/image-20201014141707412.png)

Redis故障转移流程：

1. 启动并初始化多个Redis Sentinel节点，对每个Redis节点创建命令和订阅两个连接

2. 信息同步阶段，通过命令连接Sentinel可以感知所有配置的主节点信息和对应的从节点信息，通过订阅连接，Sentinel可以自动感知到其他的Sentinel，实现Sentinel之间的信息同步。

   >  以上两步即完成整个哨兵系统的初始化工作，之后可以实现故障的自动转移

3. Sentinel检测Redis主节点主观下线

4. 多个Sentinel检测Redis主节点主观下线，彼此通信，确定Redis主节点客观下线

5. 多个Sentinel通过Raft协议选主

6. Sentinel主节点从Redis从节点中选举出新的Master

7. 通知新的Master成为主节点，通知其他Redis节点Slave新的主节点

8. 监听老的Master状态，等其复活后设置为Slave

> Redis哨兵的故障切换整体流程如上，以下介绍每一步的详细操作和原理。

- 启动初始化Sentinel

Redis可以通过以下两个命令启动Sentinel节点，实现效果完全一致：

```c
redis-sentinel sentinel.conf
redis-server sentinel.conf --sentinel
```

Redis Sentinel启动时会执行以下步骤：

1. 初始化服务器

   > Sentinel本质上只是一个运行在特殊模式下的Redis服务器，所以启动Sentinel的第一步，就是初始化一个普通的Redis服务器，不过，因为Sentinel执行的工作和普通Redis服务器执行的工作不同，所以Sentinel的初始化过程和普通Redis服务器的初始化过程并不完全相同。例如Sentinel不会加载AOF，RDB功能，Sentinel具体会加载的功能如下表所示：
   >
   > ![image-20201014142813903](http://rocks526.top/lzx/image-20201014142813903.png)

2. 将普通Redis服务器使用的代码替换成Sentinel专用代码

   > 启动Sentinel的第二个步骤就是将一部分普通Redis服务器使用的代码替换成Sentinel专用代码。
   >
   > 例如将Redis默认端口号6379改为Sentinel的默认端口号26379，除此以外，还有Sentinel不会载入GET/SET/HGET等命令，会载入一个Sentinel专门定义的命令集，客户端只能对Sentinel执行PING、SENTINEL、INFO、SUBSCRIBE、UNSUBSCRIBE、PSUBSCRIBE和PUNSUBSCRIBE这七个命令。

3. 初始化Sentinel状态

   > 在应用了Sentinel的专用代码之后，接下来，服务器会初始化一个sentinel.c/sentinelState结构，该结构保存了服务器中所有和Sentinel功能有关的状态，例如纪元，主节点列表等。

4. 根据给定的配置文件，初始化Sentinel的监视主服务器列表

   > 记录Sentinel相关状态的结构创建好之后，就是读取配置文件里的主节点信息，更新Sentinel记录的主节点列表信息。

5. 创建连向主服务器的网络连接

   > 初始化Sentinel的最后一步是创建连向被监视主服务器的网络连接，Sentinel将成为主服务器的客户端，它可以向主服务器发送命令，并从命令回复中获取相关的信息。
   >
   > 对于每个被Sentinel监视的主服务器来说，Sentinel会创建两个连向主服务器的异步网络连接：
   >
   > - 一个是命令连接，这个连接专门用于向主服务器发送命令，并接收命令回复。
   > - 另一个是订阅连接，这个连接专门用于订阅主服务器的__sentinel__:hello频道。
   >
   > 命令连接主要用来连接各个Redis节点，发送INFO命令获取节点的主从信息
   >
   > 订阅连接主要用来各个Sentinel节点之间做信息交互，因为所有Sentinel节点都会订阅相同的频道，因此所有Sentinel节点会通过这个订阅连接实现信息的同步。

- 信息同步阶段，感知所有的Redis主从节点信息，Sentinel节点信息

1. 获取Redis主节点信息

> Sentinel默认会以每十秒一次的频率，通过命令连接向被监视的主服务器发送INFO命令，并通过分析INFO命令的回复来获取主服务器的当前信息。
>
> 通过分析主服务器返回的INFO命令回复，Sentinel可以获取以下两方面的信息：
>
> - 一方面是关于主服务器本身的信息，包括run_id域记录的服务器运行ID，以及role域记录的服务器角色；
> - 另一方面是关于主服务器属下所有从服务器的信息，每个从服务器都由一个"slave"字符串开头的行记录，每行的ip=域记录了从服务器的IP地址，而port=域则记录了从服务器的端口号。根据这些IP地址和端口号，Sentinel无须用户提供从服务器的地址信息，就可以自动发现从服务器。
>
> 根据run_id域和role域记录的信息，Sentinel将对主服务器的实例结构进行更新，同时更新主服务器实例结构中的从节点信息。

2. 获取Redis从节点信息

> 当Sentinel发现主服务器有新的从服务器出现时，Sentinel除了会为这个新的从服务器创建相应的实例结构之外，Sentinel还会创建连接到从服务器的命令连接和订阅连接。
>
> 在创建命令连接之后，Sentinel在默认情况下，会以每十秒一次的频率通过命令连接向从服务器发送INFO命令，之后根据响应结果，提取出以下信息：
>
> - 从服务器的运行ID run_id
> - 从服务器的角色role（根据这个可以检测是否存在某个从节点又是其他节点的主节点情况）
> - 主服务器的IP地址master_host，以及主服务器的端口号master_port
> - 主从服务器的连接状态master_link_status
> - 从服务器的优先级slave_priority
> - 从服务器的复制偏移量slave_repl_offset
>
> 获取这些信息之后，Sentinel将对主服务器的实例结构中的从节点信息进行更新。

3. 获取其他Sentinel信息，实现Sentinel之间的信息同步

> 在默认情况下，Sentinel会以每两秒一次的频率，通过命令连接向所有被监视的主服务器和从服务器的`__sentinel__:hello`频道发送Sentinel自身和它所监视的主或从节点服务器信息。
>
> 由于在初始化阶段，Sentinel对所有的主节点和从节点都建立了命令连接和订阅连接，其中订阅连接就是订阅了`__sentinel__:hello`这个频道，因此所有Sentinel都会收到每个Sentinel发布的消息，包括他自身的消息。
>
> 当Sentinel接收到发布订阅的消息后，会提取出Sentinel和监视的Redis节点信息，通过对比Sentinel的runid，如果是自己本身的消息则丢弃，反之更新sentinel.c/sentinelState结构里的sentinels字典，该字典里记录了监视该Redis主节点的所有Sentinel节点，如此即可实现Sentinel之间的自动发现。
>
> 当Sentinel通过频道信息发现一个新的Sentinel时，它不仅会为新Sentinel在sentinels字典中创建相应的实例结构，还会创建一个连向新Sentinel的命令连接，而新Sentinel也同样会创建连向这个Sentinel的命令连接，最终监视同一主服务器的多个Sentinel将形成相互连接的网络。
>
> 使用命令连接相连的各个Sentinel可以通过向其他Sentinel发送命令请求来进行信息交换，最终完成故障转移时的Reidis节点客观下线和Sentinel选主。
>
> Sentinel在连接主服务器或者从服务器时，会同时创建命令连接和订阅连接，但是在连接其他Sentinel时，却只会创建命令连接，而不创建订阅连接。这是因为Sentinel需要通过接收主服务器或者从服务器发来的频道信息来发现未知的新Sentinel，所以才需要建立订阅连接，而相互已知的Sentinel只要使用命令连接来进行通信就足够了。

- Sentinel检测节点主观下线

在默认情况下，Sentinel会以每秒一次的频率向所有与它创建了命令连接的实例（包括主服务器、从服务器、其他Sentinel在内）发送PING命令，并通过实例返回的PING命令回复来判断实例是否在线。

Sentinel配置文件中的down-after-milliseconds选项指定了Sentinel判断实例进入主观下线所需的时间长度：如果一个实例在down-after-milliseconds毫秒内，连续向Sentinel返回无效回复，那么Sentinel会修改这个实例所对应的实例结构，在结构的flags属性中打开SRI_S_DOWN标识，以此来表示这个实例已经进入主观下线状态。

> 需要注意每个Sentinel启动时都可以指定配置文件，因此不同的Sentinel可能down-after-milliseconds参数不一致，导致不同的Sentinel判定的Redis节点是否下线也不一致。
>
> Sentinel判断某个节点主观下线，可能是该节点挂掉了，也可能是由于网络波动或其他原因导致的这两个节点不同，甚至可能是Sentinel节点自己本身挂掉了，因此主管下线不能判定该Redis节点挂掉，需要其他Sentinel对该节点进行确定，是否客观下线。

- Sentinel检测节点客观下线

当Sentinel将一个主服务器判断为主观下线之后，为了确认这个主服务器是否真的下线了，它会向同样监视这一主服务器的其他Sentinel进行询问，看它们是否也认为主服务器已经进入了下线状态（可以是主观下线或者客观下线）。当Sentinel从其他Sentinel那里接收到足够数量的已下线判断之后，Sentinel就会将从服务器判定为客观下线，并对主服务器执行故障转移操作。

> Sentinel之间会发送SENTINEL is-master-down-by-address命令，携带sentinel和要判断的节点ip端口信息，Sentinel会回复自身对该节点是否下线的判断
>
> 需要注意的是，Sentinel判断节点是否客观下线，是根据配置文件的里quorum参数确定的，只要大于等于quorum个Sentinel确定该节点主观下线，就判断该节点客观下线，不同的Sentinel可能有不同的quorum值。

- 多个Sentinel通过Raft协议选主

当一个主服务器被判断为客观下线时，监视这个下线主服务器的各个Sentinel会进行协商，选举出一个领头Sentinel，并由领头Sentinel对下线主服务器执行故障转移操作。

Raft选举流程很简单，具体流程如下：

1. 所有判断监视节点下线的Sentinel都会参与选举，向其他Sentinel发送SENTINEL is-master-down-by-address命令，要求其他节点选举自己为主节点
2. Raft采取先到先得的方案，Sentinel节点接到选举命令之后，如果自己当前还没有确定主节点，则将该命令里要求的Sentinel节点设置为主节点，之后的选举命令不再理会。
3. 如果有某个Sentinel被半数以上的Sentinel设置成了主节点Sentinel，那么这个Sentinel成为主节点
4. 如果在给定时限内，没有一个Sentinel被选举为领头Sentinel，那么各个Sentinel将在一段时间之后再次进行选举，直到选出领头Sentinel为止

- Sentinel主节点从Redis从节点中选举出新的Master

Sentinel主节点选举出来之后，开始进行故障自动转移，从所有Redis从节点列表中挑选一个升为主节点，挑选规则如下：

1. 删除列表中所有处于下线或者断线状态的从服务器，这可以保证列表中剩余的从服务器都是正常在线的。
2. 删除列表中所有最近五秒内没有回复过领头Sentinel的INFO命令的从服务器，这可以保证列表中剩余的从服务器都是最近成功进行过通信的。
3. 删除所有与已下线主服务器连接断开超过down-after-milliseconds*10毫秒的从服务器，即确保新的主节点没有和之前的主节点断连太久，丢失数据不多
4. Sentinel将根据从服务器的优先级，对列表中剩余的从服务器进行排序，并选出其中优先级最高的从服务器
5. 如果有多个优先级最高的从节点，则选举复制偏移量offset最大的节点
6. 如果有多个复制偏移量最大的节点，则根据从节点的runid选举，选举最小的

- 通知新的Master成为主节点，通知其他Redis节点Slave新的主节点

选举出新的Redis主节点之后，Sentinel会向新的主节点发送SLAVEOF no one命令，让其成为一个主节点，之后Sentinel会以每秒一次的频率（平时是每十秒一次），向被升级的从服务器发送INFO命令，并观察命令回复中的角色（role）信息，当被升级服务器的role从原来的slave变为master时，Sentinel就知道被选中的从服务器已经顺利升级为主服务器了。

当新的主节点升级成功之后，Sentinel向之前其他的从节点发送SLAVEOF命令，让其他从节点将主节点切换为新的主节点的IP和端口，实现主节点的切换。

- 监听老的Master状态，等其复活后设置为Slave

实现故障转移的最后一步是监听之前老的Master节点状态，在其重新上线后切换为Slave节点，避免脑裂问题。

- 总结

Redis哨兵的故障自动转移是通过加了一堆Sentinel节点监控Redis主节点状态实现的。

Sentinel通过命令连接感知所有的主从节点，通过订阅连接实现Sentinel之间的自动发现。

哨兵感知主节点下线分为主观下线和客观下线两种，主观下线只需要超过配置文件的最大超时时间即可，客观下线需要超过配置文件指定的N个Sentinel节点同时认为才可以。

确定节点下线之后，根据Raft算法选举出一个Sentinel的Leader，由Leader选举新的Redis主节点。

选举完成之后，SentinelLeader向之前的所有从节点发送命令，切换主从，并且监听之前主节点的状态，在其上线后将其改成从节点。

**集群中存在的三个定时任务 ：**

1. 每10秒每个Sentinel会对master和slave执行INFO命令，更新主从关系，发现新节点
2. 每2秒每个Sentinel通过master节点的`__sentinel__:hello`频道实现sentinel之间的信息同步
3. 每秒Sentinel对所有Redis节点发送PING，检测Redis节点是否下线

> Redis哨兵只是实现了服务端的高可用，客户端如果还是采取直连主节点的策略，主从切换后依旧不可用，因此还需要客户端配置，支持Redis哨兵。客户端方案：
>
> - 指定哨兵集群的IP和地址，客户端直连哨兵
> - 哨兵集群里维护最新的master信息，客户端从哨兵里获取
> - 当Redis节点信息变更时，哨兵通知客户端

#### Redis集群











#### Codis









#### Twemproxy













### <a id="rm-jgsj">Redis架构设计</a>









### <a id="rm-gxm">Redis高性能</a>









### <a id="rm-yh">Redis优化</a>

- Redis慢查询

Redis命令的执行包括四步：

客户端发送命令 => 服务端接收命令，放入待执行命令队列 => 服务端执行命令 => 返回客户端响应

这四步的每一步都可能导致客户端超时，如客户端和服务端交互的网络IO波动，如Redis服务端的任务队列很长，或者Redis命令执行很慢。Redis中的慢查询指的是执行时间超过配置文件设置的阈值的命令。

Redis慢查询相关配置：

slowlog-max-len：慢查询的阈值，超过此值则视为慢查询，默认为10ms，通常设置1ms

slowlog-log-slower-than：记录慢查询的队列长度，通常设置1000左右。

> Redis针对慢查询是记录在一个慢查询队列里，此队列先进先出，里面记录慢查询的命令和执行的时间等信息，每个记录给个顺序递增的ID，从1开始，此队列保存在内存中。

Redis针对慢查询提供了一个监控功能，Redis慢查询相关命令如下：

slowlog get [n]：获取慢查询队列。

slowlog len：获取慢查询队列长度。

slowlog reset：清空慢查询队列。



### <a id="rm-kshgj">Redis可视化工具</a>

















![image-20200916175728176](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916175728176.png)



Redis底层用了epoll，同时在代码层面将epoll内部的通知封装成事件。

![image-20200916194126323](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916194126323.png)







































![image-20200916200539999](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916200539999.png)





![image-20200916201125892](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201125892.png)

![image-20200916201233572](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201233572.png)

![image-20200916201433868](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201433868.png)

![image-20200916201457679](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201457679.png)

![image-20200916201634668](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201634668.png)

![image-20200916201657689](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201657689.png)

![image-20200916201750140](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201750140.png)

![image-20200916201913190](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200916201913190.png)

















![image-20200923114327234](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114327234.png)

![image-20200923114517508](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114517508.png)

![image-20200923114531111](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114531111.png)

![image-20200923114555846](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114555846.png)

![image-20200923114623953](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114623953.png)

![image-20200923114740276](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114740276.png)

![image-20200923114940217](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923114940217.png)

![image-20200923115132769](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923115132769.png)

![image-20200923115224659](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923115224659.png)

![image-20200923115708384](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923115708384.png)

- 范围分片
  - 业务层分片
  - 连续范围
- Hash分片
  - 不可连续获取

![image-20200923115952366](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923115952366.png)

![image-20200923120213729](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923120213729.png)

![image-20200923140829060](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923140829060.png)

![image-20200923141139523](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923141139523.png)

![image-20200923141219248](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923141219248.png)

- 客户端缓存
  - 放弃caching slot
  - 使用key names机制
- ACL
  - 对不同命令做出权限控制
  - 可以查询所有违反ACL机制的客户端操作
- PSYNC2
  - 改良Redis复制协议，可以实现部分同步

![image-20200923141855398](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923141855398.png)

![image-20200923141918404](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923141918404.png)

![image-20200923142017444](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200923142017444.png)















# <a id="khd">Redis客户端</a>

> Redis的客户端众多，常见的Java，C，C++，Go，Python，Rust等语言都支持。
>
> Redis支持的客户端具体列表如下：https://redis.io/clients
>
> Java常见客户端为Jedis，Redisson，Lettuce，Spring Data Redis底层是基于Jedis或者Lettuce。
>
> Jedis：对Redis支持比较好，实现所有的Redis命令，支持连接池，哨兵，集群等。底层基于Socket同步阻塞IO。
>
> Redisson：提供很多分布式相关操作服务，例如，分布式锁，分布式集合，可通过Redis支持延迟队列。底层基于Netty异步调用。
>
> Lettuce：主要在一些分布式缓存框架上使用比较多，底层基于Netty，异步调用。

### <a id="redis-java-client">redis-java-client</a>

> redis-java-client是一个非常简单的Java客户端，零依赖，只有两百行代码。
>
> 对于Redis高级功能没有适配，pipeline也是半成品，没有连接池，更像是RESP协议的一个实现。
>
> Github地址：https://github.com/drm/java-redis-client?_ga=2.83788629.2142369040.1600420179-1815258866.1596435142

打开项目之后，整个项目只有一个Redis类，类结构如下所示：

![image-20200921153149959](http://rocks526.top/lzx/image-20200921153149959.png)

- Encoder：编码器，负责将用户输入的命令转换成RESP协议
- Parser：解码器，负责将Redis的响应从RESP解析出内容
- writer和reader：输入输出流
- Redis接收一个Socket或者输入输出流作为入参

使用示例如下所示：

```java
/**
 * 测试Redis基本操作
 */
public class Demo1 {

    public static void main(String[] args) throws IOException {

        //String类型
        Redis redis = new Redis(new Socket("127.0.0.1",6379));
        byte[] recvMsg = redis.call("set", "name", "soc");
        byte[] recvMsg2 = redis.call("get", "name");
        System.out.println(new String(recvMsg));
        System.out.println(new String(recvMsg2));
        System.out.println("------------------------------------");
        //list类型
        Long num = redis.call("lpush", "ids", "1","2","5","29");
        List<byte[]> ids = redis.call("lrange", "ids","0","2");
        System.out.println(num);
        ids.forEach(id -> System.out.println(new String(id)));
        System.out.println("------------------------------------");
        //中文测试
        byte[] recvMsg3 = redis.call("set", "name", "大数据");
        byte[] recvMsg4 = redis.call("get", "name");
        System.out.println(new String(recvMsg3));
        System.out.println(new String(recvMsg4));
    }

}
```

```java
/**
 * Pipeline  伪管道
 */
public class Demo3 {

    public static void main(String[] args) throws IOException {

        Redis redis = new Redis(new Socket("127.0.0.1",6379));
        final Redis.Pipeline pipeline = redis.pipeline();
        pipeline.call("set","test1","test1");
        pipeline.call("set","test2","test2");
        final List<Object> read = pipeline.read();
        System.out.println(read);
    }

}
```

> 此客户端只做了RESP协议的轻量级实现，无连接池，异常，返回值处理等机制，不适合作为Redis的客户端使用，但适合用来测试RESP协议。
>
> 需要注意返回的响应是byte数组，里面是ASCII。

### <a id="jedis">Jedis</a>

> Jedis是一个轻量级且易于使用的Redis的Java客户端，对Redis功能支持比较全面，包括事务，管道，哨兵，集群等。
>
> Jedis的Github地址：https://github.com/xetorthio/jedis









### <a id="redisson">Redisson</a>





### <a id="springdataredis">Spring Data Redis</a>











# <a id="bb">Redis版本</a>

> Redis借鉴了Linux操作系统对于版本号的命名规则：版本号第二位如果是奇数，则为非稳定版本（例如2.7、2.9、3.1），如果是偶数，则为稳定版本（例如2.6、2.8、3.0、3.2）。当前奇数版本就是下一个稳定版本的开发版本，例如2.9版本是3.0版本的开发版本。所以我们在生产环境通常选取偶数版本的Redis，如果对于某些新的特性想提前了解和使用，可以选择最新的奇数版本。

### Redis2.6

Redis2.6在2012年正式发布，经历了17个版本，到2.6.17版本，相比于Redis2.4，主要特性如下：

- 服务端支持Lua脚本。
- 去掉虚拟内存相关功能。
- 放开对客户端连接数的硬编码限制。
- 键的过期时间支持毫秒。
- 从节点提供只读功能。
- 两个新的位图命令：bitcount和bitop。
- 增强了redis-benchmark的功能：支持定制化的压测，CSV输出等功能。
- 基于浮点数自增命令：incrbyfloat和hincrbyfloat。
- redis-cli可以使用--eval参数实现Lua脚本执行。
- shutdown命令增强。
- info可以按照section输出，并且添加了一些统计项。
- 重构了大量的核心代码，所有集群相关的代码都去掉了，cluster功能将会是3.0版本最大的亮点。
- sort命令优化。

### Redis2.8

Redis2.8在2013年11月22日正式发布，经历了24个版本，到2.8.24版本，相比于Redis2.6，主要特性如下：

- 添加部分主从复制的功能，在一定程度上降低了由于网络问题，造成频繁全量复制生成RDB对系统造成的压力。
- 尝试性地支持IPv6。
- 可以通过config set命令设置maxclients。
- 可以用bind命令绑定多个IP地址。
- Redis设置了明显的进程名，方便使用ps命令查看系统进程。
- config rewrite命令可以将config set持久化到Redis配置文件中。
- 发布订阅添加了pubsub命令。
- Redis Sentinel第二版，相比于Redis2.6的Redis Sentinel，此版本已经变成生产可用。

### Redis3.0

Redis3.0在2015年4月1日正式发布，相比于Redis2.8主要特性如下：

注意Redis3.0最大的改动就是添加Redis的分布式实现Redis Cluster，填补了
Redis官方没有分布式实现的空白。

- Redis Cluster：Redis的官方分布式实现。
- 全新的embedded string对象编码结果，优化小对象内存访问，在特定
  的工作负载下速度大幅提升。
- lru算法大幅提升。
- migrate连接缓存，大幅提升键迁移的速度。
- migrate命令两个新的参数copy和replace。
- 新的client pause命令，在指定时间内停止处理客户端请求。
- bitcount命令性能提升。
- config set设置maxmemory时候可以设置不同的单位（之前只能是字
  节），例如config set maxmemory1gb。
- Redis日志小做调整：日志中会反应当前实例的角色（master或者
  slave）。
- incr命令性能提升。

### Redis3.2

Redis3.2在2016年5月6日正式发布，相比于Redis3.0主要特征如下：

- 添加GEO相关功能。
- SDS在速度和节省空间上都做了优化。
- 支持用upstart或者systemd管理Redis进程。
- 新的List编码类型：quicklist。
- 从节点读取过期数据保证一致性。
- 添加了hstrlen命令。
- 增强了debug命令，支持了更多的参数。
- Lua脚本功能增强。
- 添加了Lua Debugger。
- config set支持更多的配置参数。
- 优化了Redis崩溃后的相关报告。
- 新的RDB格式，但是仍然兼容旧的RDB。
- 加速RDB的加载速度。
- spop命令支持个数参数。
- cluster nodes命令得到加速。
- Jemalloc更新到4.0.3版本。

### Redis4.0

可能出乎很多人的意料，Redis3.2之后的版本是4.0，而不是3.4、3.6、3.8。

一般这种重大版本号的升级也意味着软件或者工具本身发生了重大变革，2017年7月Redis发布了4.0-GA，下面列出Redis4.0的新特性：

- 提供了模块系统，方便第三方开发者拓展Redis的功能，更多模块详见：http://redismodules.com
- PSYNC2.0：优化了之前版本中，主从节点切换必然引起全量复制的问题。
- 提供了新的缓存剔除算法：LFU（Last Frequently Used），并对已有算法进行了优化。
- 提供了非阻塞del和flushall/flushdb功能，有效解决删除bigkey可能造成的Redis阻塞。
- 提供了RDB-AOF混合持久化格式，充分利用了AOF和RDB各自优势。
- 提供memory命令，实现对内存更为全面的监控统计。
- 提供了交互数据库功能，实现Redis内部数据库之间的数据置换。
- Redis Cluster兼容NAT和Docker。

### Redis5.0

2018年10月发布Redis 5.0.0，引入Streams结构。

- 新的Stream数据类型，可以直接作为消息队列。

- 新的Redis模块API：Timers and Cluster API。

- RDB存储LFU和LRU信息。

- 集群管理器从Ruby（redis-trib.rb）移植到C代码。可以在redis-cli中。查看`redis-cli —cluster help`了解更多信息。

- 新sorted set命令：ZPOPMIN / MAX。
- 新增LOLWUT命令。
- 新增Client Unblock和Client ID。
- Redis 5.0引入动态哈希，以平衡CPU的使用率和相应性能，可以通过配置文件进行配置。Redis 5.0默认使用动态哈希。

- 增强HyperLogLog实现。
- Redis主从复制中的从节点不再成为slave，改称Replicas。

- 更好的内存统计报告。

- 许多带有子命令的命令现在都有一个HELP子命令。

- 客户经常连接和断开连接时性能更好。
- Redis核心代码进行了部分重构和优化。

- Jemalloc升级到5.1版

### Redis6.0

2020年4月发布Redis 6.0.0-GA。

1. 众多新模块（modules）API
2. 引入SSL机制
3. 引入ACL权限控制机制
4. 新的RESP3协议
5. 更快的RDB加载，预计优化20-30%。
6. 多线程IO
7. 客户端缓存
8. 无盘复制副本（Diskless replication on replicas）
9. Redis-benchmark 的集群支持和 redis-cli 优化

# Redis应用

### Reids缓存

![image-20201013174153314](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013174153314.png)

![image-20201013191824008](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013191824008.png)

![image-20201013192012082](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013192012082.png)

![image-20201013192200621](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013192200621.png)

![image-20201013192545532](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013192545532.png)

![image-20201013192648512](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013192648512.png)

![image-20201013192828560](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013192828560.png)

![image-20201013193006127](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013193006127.png)

![image-20201013193146580](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013193146580.png)

> 部分核心属性比较好，属性太多序列化对CPU开销也很大

![image-20201013193327105](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013193327105.png)

> 大量请求穿透缓存层，请求存储层，导致存储层压力大
>
> 可能造成的原因：
>
> - 业务代码自身问题(拿不到结果或拿到结果写入缓存失败)
> - 恶意攻击/爬虫等(故意访问不存在的数据，会大量穿透缓存)
>
> 如果检查这些问题：
>
> - 业务的响应时间，如果缓存被穿透，响应时间势必升高
> - 相关指标：总调用数，缓存层命中数，存储层命中数，一些缓存中间件提供
>
> 解决方案：
>
> - 缓存空对象:当存储层拿不到结果时，原本逻辑会返回前端null，redis不修改，现在返回将null写入redis中，这种方式可以在之后将请求拦截在缓存层，缺点是redis会缓存更多的键，并且缓存和存储层可能数据不一致。
> - 布隆过滤器拦截

![image-20201013194418105](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013194418105.png)

> 优化方案：
>
> - 缓存高可用(哨兵，集群，VIP主从漂移)
> - 依赖隔离组件进行限流降级(线程池/信号量)
> - 压力测试，提前演练

![image-20201013194845581](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013194845581.png)

![image-20201013195210526](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013195210526.png)

![image-20201013195453741](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013195453741.png)

> 当机器越多，批量操作可能越慢。
>
> 优化IO方案：
>
> - 命令本身优化：慢查询，bigkey等
> - 减少网络IO次数
> - 降低接入成本：客户端长连接/连接池/NIO等
>
> 批量操作优化：
>
> - 串行mget
> - 串行IO
> - 并行IO
> - hash_tag

![image-20201013195851017](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013195851017.png)

![image-20201013195920503](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013195920503.png)

![image-20201013195933798](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013195933798.png)

![image-20201013195948554](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013195948554.png)

![image-20201013200001901](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20201013200001901.png)