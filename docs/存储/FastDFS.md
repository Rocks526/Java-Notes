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
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.43.tar.gz

tar -xzvf libfastcommon-1.0.43.tar.gz 

cd libfastcommon-1.0.43/

./make.sh

./make.sh install
```

- 安装fastdfs

```shell
wget https://github.com/happyfish100/fastdfs/archive/V6.06.tar.gz

tar -xzvf fastdfs-6.06.tar.gz 

cd fastdfs-6.06

./make.sh

./make.sh install
```

- 配置Tracker Server

```shell
cd conf

vim tracker.conf 

base_path = /root/fastdfs/tracker

mkdir /root/fastdfs/tracker -p
```

- 配置Storage Server

```shell
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
/usr/bin/fdfs_monitor /root/fastdfs/fastdfs-6.06/conf/storage.conf 
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
>
> 安装过程中，尽量使用绝对路径，其次记得开放Tracker的22122端口，如果安装出现问题，可以打开日志查看错误信息

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

- Tracker Server配置文件

```properties
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
# 接收线程数
accept_threads = 1
# Work进程数 通常设置为CPU数
work_threads = 4 
#最小网络缓冲区大小
min_buff_size = 8KB
#最大网络缓冲区大小
max_buff_size = 128KB
# 上传文件的选组方式 0轮询 1指定组 2平衡负载，选择剩余空间最大的组
store_lookup = 2
# 当store_lookup为1时 此参数指定上传的组名
store_group = group1
# 组内Storage选择策略 0轮询 1根据IP排序选最小 2根据优先级 值越小优先级越高
store_server = 0
# 上传路径的选择策略(Storage Server可以有多个存储路径)，0轮询 2选择剩余空间最大的
store_path = 0
# 下载服务器的选择方式 0轮询 1文件源服务器
download_Server = 0
# 保留空间值 当剩余空间不足时 文件不会上传该服务器 可指定大小 也可指定百分比
reserved_storage_space = 20% 
# 日志级别 emerg alert crit error warn notice info debug
log_level = info  
# 指定操作系统运行该程序的用户组
run_by_group = 
# 指定操作系统运行该程序的用户
run_by_user = 
# 可以连接到tracker Server的ip范围 可设定多个值
allow_hosts = *
# 日志缓存同步磁盘间隔 默认单位10s
sync_log_buff_interval = 1
# 检测 storage Server 存活的时间隔，单位为秒 
# 如果tracker Server在一个check_active_interval内还没有收到storage Server的一次心跳，就认为该storage Server已经下线。所以本参数值必须大于storage Server配置的心跳时间间隔，一般为2-3倍
check_active_interval = 120
# 设定线程栈的大小。 线程栈越大，一个线程占用的系统资源就越多。
# 如果要启动更多的线程,可以适当降低本参数值。如64KB，默认值526KB，tracker Server线程栈不应小于64KB 
thread_stack_size = 256KB
# 这个参数控制当storage Server IP地址改变时，集群是否自动调整。注：只有在storage Server进程重启时才完成自动调整
storage_ip_changed_auto_adjust = true
# 存储同步文件的最大延迟时间 默认86400秒 即一天
storage_sync_file_max_delay = 86400
# 存储同步文件的最长时间 默认300s
storage_sync_file_max_time = 300
# 是否开启合并存储
use_trunk_file = false
# 最小槽大小 <=4KB
slot_min_size = 256
# 最大槽大小 当上传文件小于此值时 将其存到块中 否则尽心分块
slot_max_size = 1MB
# 块中对齐空间
trunk_alloc_alignment_size = 256
# 合并连续块中的剩余空间
trunk_free_space_merge = true
# 删除未使用的块文件
delete_unused_trunk_files = false
# 块文件大小 >=4MB
trunk_file_size = 64MB
# 是否提前创建块文件
trunk_create_file_advance = false
# 创建块文件的时基
trunk_create_file_time_base = 02:00
# 创建块文件的时间间隔 默认1天
trunk_create_file_interval = 86400
# 创建块文件阈值  当空闲块文件小于此值时开始创建
trunk_create_file_space_threshold = 20G
# 检查块空闲空间时发现占用 则忽略
trunk_init_check_occupying = false
# if ignore storage_trunk.dat, reload from trunk binlog
trunk_init_reload_from_binlog = false
# 压缩binlog间隔
trunk_compress_binlog_min_interval = 86400
# 压缩的时基
trunk_compress_binlog_time_base = 03:00
# binlog文件最大备份数
trunk_binlog_max_backups = 7
# 使用Storage Server的配置ID而不是IP，当配置双IP时必须使用
use_storage_id = false
# 指定Storage Server的ID的配置文件
storage_ids_filename = storage_ids.conf
# storage_ids.conf配置文件中ID类型，ID或IP
id_type_in_filename = id
# 存储从文件使用符号链接
store_slave_file_use_link = false
# 每天清除错误日志
rotate_error_log = false
# 错误日志清除时间
error_log_rotate_time = 00:00
# 使用gzip压缩错误日志
compress_old_error_log = false
# 压缩七天之前的错误日志
compress_error_log_days_before = 7
# 超过指定大小删除错误日志
rotate_error_log_size = 0
# 日志文件保留天数
log_file_keep_days = 0
# 是否使用连接池
use_connection_pool = true
# 连接池的连接最大空闲时间
connection_pool_max_idle_time = 3600
# Tracker Server的Http端口
http.server_port = 8080
# 检查Storage Server的Http是否存活间隔
http.check_alive_interval = 30
# 检查Storage Server是否存活的方式，可选tcp或http
http.check_alive_type = tcp
# 检查心跳的url
http.check_alive_uri = /status.html
```

- Storage Server配置文件













# <a id="ng">Nginx-FastDFS</a>

FastDFS提供客户端和服务端，支持客户端将图片上传至某个服务器，但图片的访问需要使用客户端才可以。

在很多场景中，我们需要让上传至服务端的图片可以提供HTTP服务，不但可以通过FastDFS的客户端可以访问，也可以通过HTTP服务访问。

实现这个需求解决方案很多：

- 在服务器上安装Nginx/Apache等HTTP服务器，让服务器上的所有内容都可以向外提供接口访问，自然包含上传的图片
- 使用FastDFS自带的HTTP服务（早期FastDFS是有自带的http服务的，后来由于性能较弱，在V4.0.5版本之后取消了）
- 使用Nginx-FastDFS扩展模块

使用Nginx-FastDFS的优点：

- 如果开启文件合并，不使用FastDFS的nginx扩展模块，是无法访问到具体的文件的，因为文件合并之后，多个小文件都是存储在一个trunk文件中的，在存储目录下，是看不到具体的小文件的，只有FastDFS才可以解析出小文件
- 在文件未同步成功到副本之前，不使用FastDFS的nginx扩展模块，副本服务器是无法正常访问到指定的文件的，而使用了 FastDFS的nginx扩展模块之后，如果要访问的文件未同步成功，那么会解析出来该文件的源存储服务器ip，然后将该访问请求重定向或者代理到源存储服务器中进行访问。









# <a id="sb">SpringBoot-FastDFS</a>

- 安装FastDfs的官方Java客户端

由于FastDfs的官方Java客户端在Maven仓库没有对应依赖，因此需要自己去github下载安装到本地

FastDfs的官方Java客户端地址：https://github.com/happyfish100/fastdfs-client-java

下载之后打开项目，用Maven编译打包到本地仓库

![image-20200430115318338](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200430115318338.png)

- 创建SpringBoot项目，引入web和fastdfs依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.csource</groupId>
            <artifactId>fastdfs-client-java</artifactId>
            <version>1.29-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

- 添加FastDfs配置文件

```properties
# app
server.port=8080
# 存储
storage.fastdfs.tracker_server=106.13.191.813:22122
```

除了TrackerServer的IP之外，其他属性可选，参考https://github.com/happyfish100/fastdfs-client-java

- 创建service层接口


```java
package com.rocks.springboot.fastdfs.service;



/**
 * 文件存储服务接口
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 13:17
 */
public interface StorageService {


    /**
     * 上传文件
     *
     * @param data    文件的二进制内容
     * @param extName 扩展名
     * @return 上传成功后返回生成的文件URL；失败，返回null
     */
    String upload(byte[] data, String extName);

    /**
     * 删除文件
     *
     * @param filePath 被删除的文件路径
     * @return 删除成功后返回0，失败后返回错误代码
     */
    int delete(String filePath);

    /**
     * 下载文件
     *
     * @param filePath 要下载的文件路径
     * @return 删除成功后返回0，失败后返回错误代码
     */
    byte[] downLoad(String filePath);


}
```

- 注入FastDfs服务实现类

```java
package com.rocks.springboot.fastdfs.service.Impl;

import com.rocks.springboot.fastdfs.exception.FastDfsException;
import com.rocks.springboot.fastdfs.service.StorageService;
import lombok.extern.slf4j.Slf4j;
import org.csource.common.NameValuePair;
import org.csource.fastdfs.*;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.io.File;
import java.io.FileWriter;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;


/**
 * FastDfs文件存储服务
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 13:17
 */
@Slf4j
@Service
public class FastDfsServiceImpl implements StorageService, InitializingBean {


    private TrackerServer trackerServer = null;

    private TrackerClient trackerClient = null;

    private StorageServer storageServer = null;

    private StorageClient storageClient = null;

    @Value("${storage.fastdfs.tracker_server}")
    private String trackerServerIP;

    private SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Override
    public String upload(byte[] data, String extName) {
        try{
            Date date = new Date();
            String createTime = simpleDateFormat.format(date);
            //元数据
            NameValuePair[] meta_list = new NameValuePair[2];
            meta_list[0] = new NameValuePair("author", "Rocks526");
            meta_list[1] = new NameValuePair("createTime", createTime);
            String[] res = storageClient.upload_file(data, extName,null);
            StringBuilder filePath = new StringBuilder();
            filePath.append(res[0]).append("/").append(res[1]);
            return filePath.toString();
        }catch (Exception e){
            throw new FastDfsException("FastDfs文件上传失败！",e);
        }
    }


    @Override
    public int delete(String filePath) {
        try{
            int index = filePath.indexOf('/');
            String groupName = filePath.substring(0, index);
            String fileName = filePath.substring(index+1);
            return storageClient.delete_file(groupName,fileName);
        }catch (Exception e){
            throw new FastDfsException("FastDfs文件删除失败！",e);
        }
    }

    @Override
    public byte[] downLoad(String filePath) {
        try{
            int index = filePath.indexOf('/');
            String groupName = filePath.substring(0, index);
            String fileName = filePath.substring(index+1);
            byte[] bytes = storageClient.download_file(groupName, fileName);
            return bytes;
        }catch (Exception e){
            throw new FastDfsException("FastDfs文件下载失败！",e);
        }
    }


    //初始化
    @Override
    public void afterPropertiesSet() throws Exception {
        File confFile = File.createTempFile("FastDfs", ".conf");
        PrintWriter confWriter = new PrintWriter(new FileWriter(confFile));
        confWriter.println("tracker_server=" + trackerServerIP);
        confWriter.close();
        ClientGlobal.init(confFile.getAbsolutePath());
        confFile.delete();
        trackerClient = new TrackerClient();
        trackerServer = trackerClient.getTrackerServer();
        storageClient = new StorageClient1(trackerServer, storageServer);
        log.info("Init FastDFS Success! Tracker_server : {}", trackerServer);
    }

}
```

- 创建测试Controller

```java
package com.rocks.springboot.fastdfs.controller;

import com.rocks.springboot.fastdfs.service.StorageService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.imageio.stream.FileImageOutputStream;
import java.io.*;

/**
 * FastDfs文件存储服务测试
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 15:43
 */
@RestController
public class DemoController {


    @Autowired
    private StorageService storageService;



    @RequestMapping(value = "/upload",method = RequestMethod.POST)
    public String uploadFile(@RequestBody MultipartFile uploadFile) throws IOException {
        // 获取文件后缀
        String fileName = uploadFile.getOriginalFilename();
        String extName = fileName.substring(fileName.lastIndexOf(".") + 1);
        String filePath = storageService.upload(uploadFile.getBytes(),extName);
        return filePath;
    }

    @RequestMapping(value = "/delete",method = RequestMethod.DELETE)
    public String uploadFile(@RequestParam(value = "filePath",required = true) String filePath)  {
        int delete = storageService.delete(filePath);
        if ( delete == 0){
            return "success!";
        }
        return "failed!";
    }

    @RequestMapping(value = "/downLoad",method = RequestMethod.GET)
    public String downLoadFile(@RequestParam(value = "filePath",required = true) String filePath) throws IOException {

        String extName = filePath.substring(filePath.lastIndexOf(".") + 1);
        byte[] bytes = storageService.downLoad(filePath);
        String path = "C:\\Users\\Rocks526\\Desktop\\downLoad." + extName;
        FileImageOutputStream imageOutput = new FileImageOutputStream(new File(path));
        imageOutput.write(bytes, 0, bytes.length);
        imageOutput.close();
        System.out.println("Make Picture success,Please find image in " + path);
        return "success!";
    }


}
```

- 自定义FastDfs服务异常

```java
package com.rocks.springboot.fastdfs.exception;

import lombok.Data;

/**
 * FastDfs异常
 *
 * @author Rocks526
 * @version 1.0.0
 * @date 2020/4/30 15:26
 */
@Data
public class FastDfsException extends RuntimeException {

    private String msg;

    private Throwable cause;

    public FastDfsException(String msg) {
        super(msg);
        this.msg = msg;
        this.cause = null;
    }

    public FastDfsException(String msg,Throwable cause) {
        super(msg,cause);
        this.msg = msg;
        this.cause = cause;
    }

}
```

****

### 注意事项

- 开放FastDfs服务器的23000端口
- 上传文件时的元数据可以自定义，但需要注意数组长度，如果数组存在空的位置，上传文件时会抛出NPE
- 删除文件时，groupName和fileName都没有之前的"/"，如果出现"/"，删除会返回状态码22，多次删除可能抛出异常
- 使用Java操作FastDfs时，除了这个原生客户端之外，github上还有一个基于原生客户端封装的Java客户端，适合集成SpringBoot，地址：https://github.com/tobato/FastDFS_Client