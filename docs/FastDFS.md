- [FastDFS介绍](#js)
- [FastDFS原理](#yl)
- [FastDFS安装](#az)
- [FastDFS-Nginx](#ng)



# <a id="js">FastDFS介绍</a>





# <a id="yl">FastDFS原理</a>







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

# <a id="ng">FastDFS-Nginx</a>

