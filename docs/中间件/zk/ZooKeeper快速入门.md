# 一：ZooKeeper介绍







# 二：ZooKeeper一致性算法







# 三：ZooKeeper安装部署

### 3.1 单机版

- 下载安装包

```shell
下载地址：https://zookeeper.apache.org/releases.html
```

- 上传安装包到环境

```shell
# 创建文件夹
mkdir zk
# 进入文件夹
cd zk
# 解压
tar -zxvf apache-zookeeper-3.8.0-bin.tar.gz
```

- 修改配置文件

```shell
# 进入配置文件目录
cd apache-zookeeper-3.8.0-bin/conf/
# 复制配置文件
cp zoo_sample.cfg zoo.cfg
# 修改配置文件
vim zoo.cfg
# 修改如下配置
dataDir=/opt/lzx/zk/data
clientPort=2080
admin.serverPort=30080
# 创建数据存储目录
mkdir -p /opt/lzx/zk/data
```

- 启动

```shell
# 启动
apache-zookeeper-3.8.0-bin/bin/zkServer.sh start
# 其他命令
apache-zookeeper-3.8.0-bin/bin/zkServer.sh start     # 启动
apache-zookeeper-3.8.0-bin/bin/zkServer.sh stop	   # 停止
apache-zookeeper-3.8.0-bin/bin/zkServer.sh status    # 状态
apache-zookeeper-3.8.0-bin/bin/zkServer.sh restart   # 重启
```

![image-20220321102244301](http://rocks526.top/lzx/image-20220321102244301.png)

### 3.2 集群版

- 三个节点全部安装好zk

```shell
# 创建文件夹
mkdir zk
# 进入文件夹
cd zk
# 解压
tar -zxvf apache-zookeeper-3.8.0-bin.tar.gz
```

- 修改配置文件

```shell
dataDir=/opt/lzx/zk/data
clientPort=2080
admin.serverPort=30080

server.1=10.48.124.162:2888:2999
server.2=10.48.124.163:2888:2999
server.3=10.48.124.164:2888:2999
```

- 启动三个节点

```shell
apache-zookeeper-3.8.0-bin/bin/zkServer.sh start
```

- 查看节点状态

```shell
apache-zookeeper-3.8.0-bin/bin/zkServer.sh status
```

![image-20220322141828880](http://rocks526.top/lzx/image-20220322141828880.png)

![image-20220322141911565](http://rocks526.top/lzx/image-20220322141911565.png)

# 四：ZooKeeper快速使用

### 4.1 启动命令行客户端

zkCli.sh是zk自带的一个客户端命令行，启动如下：

![image-20220321113735956](http://rocks526.top/lzx/image-20220321113735956.png)

### 4.2 创建

使用`create`命令可以创建一个ZooKeeper节点，语法如下：

```shell
create [-s] [-e] path data acl
```

其中，`-s和-e分别指定节点特性：顺序或临时节点。`默认情况下，创建的是持久节点。data和acl可以忽略，则值为null，权限不做任何控制。

![image-20220321142915024](http://rocks526.top/lzx/image-20220321142915024.png)

### 4.3 读取

- ls

用`ls`查看某个节点下的所有一级子节点，语法如下：

```zookeeper
ls path [watch]
```

![image-20220321142926988](http://rocks526.top/lzx/image-20220321142926988.png)

- get

用`get`查看某个节点的值，语法如下：

```shell
get path [watch]
```

![image-20220321143121800](http://rocks526.top/lzx/image-20220321143121800.png)

- stat

用`stat`查看某个节点的状态信息，语法如下：

```shell
stat path
```

![image-20220321143249303](http://rocks526.top/lzx/image-20220321143249303.png)

cZxid：创建该节点的事物ID

ctime：创建该节点的时间

mZxid：更新该节点的事物ID

mtime：更新该节点的时间

pZxid：操作当前节点的子节点列表的事物ID(这种操作包含增加子节点，删除子节点)

cversion：当前节点的子节点版本号

dataVersion：当前节点的数据版本号

aclVersion：当前节点的acl权限版本号

ephemeralowner：当前节点的如果是临时节点，该属性是临时节点的事物ID

dataLength：当前节点的d的数据长度

numchildren：当前节点的子节点个数

### 4.4 更新

使用`set`命令，可以更新指定节点的数据内容，用法如下：

```shell
set path data [version]
```

其中，data用于指定要更新的值，version表明要基于哪个版本更新。

![image-20220321143958129](http://rocks526.top/lzx/image-20220321143958129.png)

### 4.5 删除

- delete

使用`delete`命令，可以删除ZooKeeper上的指定节点，用法如下：

```shell
delete path [version]
```

![image-20220321144133474](http://rocks526.top/lzx/image-20220321144133474.png)

当存在子节点时不可直接删除，需要先删除掉子节点，当删除不存在的节点的也会提示无法删除。