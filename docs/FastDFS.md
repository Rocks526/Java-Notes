- [FastDFS介绍](#js)
- [FastDFS架构](#yl)
- [FastDFS安装](#az)
- [FastDFS配置](#pz)
- [Nginx-FastDFS](#ng)
- [SpringBoot-FastDFS](#sb)



# <a id="js">FastDFS介绍</a>

- 一个使用C语言编写的开源的高性能的分布式文件系统
- 可以通过FastDFS实现对文件的管理，包括文件存储，文件同步，文件下载/上传等功能
- 适合存储中小文件（4KB - 500MB之间），HDFS适合存储大文件
- 可实现冗余备份，负载均衡，线性扩容等机制，并注重高性能，高可用指标

# <a id="yl">FastDFS架构</a>

![image-20200330132727979](F:\git-repository\Java-Notes\images\image-20200330132727979.png)

FastDFS系统有三个角色：跟踪服务器(Tracker Server)、存储服务器(Storage Server)和客户端(Client)

**Tracker Server**

- 主要负责调度工作，对存储服务器实现负载均衡
- 负责管理所有的Storage Server和group，每个Storage Server启动后会连接Tracker Server并汇报自身信息
- Tracker Server可以部署多台实现高可用，多台之间都是平等的，客户端采用轮询的方式访问各个Tracker Server

**Storage Server**

- 主要负责文件存储和备份
- 以group为单位，多个group相互独立，每个group内可配置多个Storage Server实现数据备份
- 客户端上传文件时可以指定上传到某个组，也可以通过Tracker Server调度
- 分组机制类似于Kafka的分区机制，当某个组访问压力大时，可以扩充组内Storage Server数量减小压力，当存储容量不足时，可以扩充新的分组

**Client**

- 和服务端交互实现文件上传下载等

****

**存储策略**

- Storage Server节点采用分组的方式组织存储
- 组内多个节点的数据一致，互为备份，多组间相互独立
- 当组内新增服务器时，会自动同步已有文件，同步完成后开始提供服务

**Storage Server状态**

Storage Server启动时会通过配置文件自动连接Tracker Server，定时向Tracker Server汇报自己的状态，包括磁盘剩余空间，文件同步状况，文件上传下载次数等信息。

Storage Server从启动到提供服务，有以下几种状态：

- FDFS_STORAGE_STATUS_INIT：初始化，尚未得到同步已有数据的源服务器
- FDFS_STORAGE_STATUS_WAIT_SYNC ：等待同步，已得到同步已有数据的源服务器 
- FDFS_STORAGE_STATUS_SYNCING：同步中 
- FDFS_STORAGE_STATUS_DELETED：已删除，该服务器从本组中移除
- FDFS_STORAGE_STATUS_OFFLINE：离线 
- FDFS_STORAGE_STATUS_ONLINE：在线，尚不能提供服务 
- FDFS_STORAGE_STATUS_ACTIVE：在线，可以提供服务

当Storage Server的状态为FDFS_STORAGE_STATUS_ONLINE时，当该Storage Server向Tracker Server发起一 次心跳时，Tracker Server会将其状态更改为FDFS_STORAGE_STATUS_ACTIVE

****

**文件上传流程分析**











# <a id="az">FastDFS安装</a>

- 安装gcc环境

```shell
yum install -y gcc-c++
```

- 安装libfastcommon核心包

```shell
https://github.com/happyfish100/libfastcommon/releases

tar -xzvf libfastcommon-1.0.43.tar.gz 

cd libfastcommon-1.0.43/

./make.sh

./make.sh install
```

- 安装fastdfs

```shell
tar -xzvf fastdfs-6.06.tar.gz 

./make.sh

./make.sh install
```

- 配置Tracker Server

```shell
cd /root/fastdfs/fastdfs-6.06/conf/

vim tracker.conf 

base_path = /root/fastdfs/tracker

mkdir /root/fastdfs/tracker -p
```

- 配置Storage Server

```shell
cd /root/fastdfs/fastdfs-6.06/conf/

vim storage.conf 

#指定storage的组名 
group_name=group1 
base_path=/root/fastdfs/storage
store_path0=/root/fastdfs/storage
#如果有多个挂载磁盘则定义多个store_path，如下 
#store_path_count = n
#store_path1=..... 
#store_path2=...... 
#配置tracker服务器IP和端口 
#如果有多个则配置多个tracker #tracker_Server=111.231.106.221:22122   
tracker_Server=106.13.146.213:22122

mkdir /root/fastdfs/storage -p
```

- 启动Tracker Server和Storage Server

```shell
/usr/bin/fdfs_trackerd /root/fastdfs/fastdfs-6.06/conf
/tracker.conf

/usr/bin/fdfs_storaged /root/fastdfs/fastdfs-6.06/conf
/storage.conf
```

- 查看集群状态

```shell
fdfs_monitor /root/fastdfs/fastdfs-6.06/conf/storage.conf 
```

FastDFS启动成功后，在/root/fastdfs/storage和/root/fastdfs/tracker目录下新增data和logs目录，用于文件存储和日志存储

> 设置Tracker和Storage开机自启动：
>
> - vim /etc/rc.d/rc.local
>
> - 添加/usr/bin/fdfs_trackerd /root/fastdfs/fastdfs-6.06/conf
>   /tracker.conf
> - 添加/usr/bin/fdfs_trackerd /root/fastdfs/fastdfs-6.06/conf
>   /storage.conf
>
> 关闭Tracker和Storeage命令：
>
> - killall fdfs_trackerd
> - killall fdfs_storaged
> - 如果没有killall命令则通过yum install psmisc -y安装

------

- FastDFS搭建成功后，可以通过fdfs_test命令测试

```shell
vim /root/fastdfs/fastdfs-6.06/conf/client.conf 

base_path = /root/fastdfs/client
tracker_server = 106.13.146.213:22122

mkdir /root/fastdfs/client
```

- 利用fdfs_test命令上传文件

```shell
[root@instance-cngw2m1f client]# /usr/bin/fdfs_test /root/fastdfs/fastdfs-6.06/conf/client.conf upload /root/user.txt
This is FastDFS client test program v6.06
......
group_name=group1, remote_filename=M00/00/00/ag2S1V58TK-AOs7iAAAAR7B1FE0023.txt
source ip address: 106.13.146.213
file timestamp=2020-03-26 14:33:19
file size=71
file crc32=2960462925
example file url: http://106.13.146.213/group1/M00/00/00/ag2S1V58TK-AOs7iAAAAR7B1FE0023.txt
storage_upload_slave_by_filename
group_name=group1, remote_filename=M00/00/00/ag2S1V58TK-AOs7iAAAAR7B1FE0023_big.txt
source ip address: 106.13.146.213
file timestamp=2020-03-26 14:33:19
file size=71
file crc32=2960462925
example file url: http://106.13.146.213/group1/M00/00/00/ag2S1V58TK-AOs7iAAAAR7B1FE0023_big.txt
```

> 由于fastdfs没有提供http服务，因此暂时无法访问，可以通过提示的文件存储路径在磁盘查找文件.
>
> 通过fastdfs-nginx模块可以借助Nginx实现http访问.
>
> fdfs_test --help可以查看fdfs_test的其他命令.



# <a id="pz">FastDFS配置</a>

- Tracker Server配置详解

```shell
# 此配置文件是否生效 false生效  true不生效
disable = false
# 绑定的IP地址，一般用于服务器有多个IP，绑定某个IP
bind_addr = 192.168.6.102 
# 端口号
port = 22122
# Socket连接超时时间 单位秒
connect_timeout = 5
# 网络超时时间 超过此时间还不收发数据则断开连接 单位秒
network_timeout = 60 
# 根目录地址
base_path = /home/fdfs/tracker 
# 最大连接数
max_connections = 1024
# Work进程数 通常设置为CPU数
work_threads = 4 
# 上传文件的选组方式 0轮询 1指定组 2平衡负载，选择剩余空间最大的组
store_lookup = 2
# 当store_lookup为1时 此参数指定上传的组名
store_group
# 组内Storage选择策略 0轮询 1根据IP排序选最小 2根据优先级 值越小优先级越高
store_server = 0
# 上传路径的选择策略(Storage Server可以有多个存储路径)，0轮询 1选择剩余空间最大的
store_path = 0
# 下载服务器的选择方式 0轮询 1选择IP小的 2优先级
download_Server = 0
# 保留空间值 当剩余空间不足时 文件不会上传该服务器 可指定大小 也可指定百分比
reserved_storage_space = 1G 
# 日志级别 emerg alert crit error warn notice info debug
log_level = info  
# 指定操作系统运行该程序的用户组
run_by_group = 
# 指定操作系统运行该程序的用户
/ run_by_user = 
# 可以连接到tracker Server的ip范围 可设定多个值
allow_hosts = *
# 检测 storage Server 存活的时间隔，单位为秒 
# 如果tracker Server在一个check_active_interval内还没有收到storage Server的一次心跳，就认为该storage Server已经下线。所以本参数值必须大于storage Server配置的心跳时间间隔，一般为2-3倍
check_active_interval = 120
# 如果tracker Server在一个check_active_interval内还没有收到storage Server的一次心跳， #      那边将认为该storage Server已经下线。所以本参数值必须大于storage Server配置的心跳时间间 隔。 #      通常配置为storage Server心跳时间间隔的2倍或3倍。 check_active_interval=120 thread_stack_size #func：设定线程栈的大小。 线程栈越大，一个线程占用的系统资源就越多。 #      如果要启动更多的线程（V1.x对应的参数为max_connections，V2.0为work_threads），可以适当 降低本参数值。 #valu：如64KB，默认值为64，tracker Server线程栈不应小于64KB thread_stack_size=64KB storage_ip_changed_auto_adjust #func：这个参数控制当storage Server IP地址改变时，集群是否自动调整。注：只有在storage Server进 程重启时才完成自动调整。 #valu：true或false storage_ip_changed_auto_adjust=true

```















# <a id="ng">FastDFS-Nginx</a>













# <a id="sb">SpringBoot-FastDFS</a>

