# 一：Prometheus介绍

### 1.1 监控系统介绍

监控对于一个系统的重要性不言而喻，可以应用于以下这些方面：

- 观察系统运行状态
- 问题排查
- 及时告警
- 系统调优

监控系统的指标又是监控系统的关键，一般包括以下指标：

- 系统层监控：服务器CPU、内存、负载等硬件资源
- 中间件监控：应用系统中所使用的各种中间件监控，包括kafka、pg、redis等
- 应用层监控：应用系统本身的监控，包括jvm监控、资源监控等
- 业务层监控：针对业务指标的监控，例如新增用户数、用户登录数、PV、UV等
- 端监控：ios、android、h5等各个客户端的监控，包括接口响应速度、页面加载时间等指标

监控系统常用组件：

- Zabbix：老牌监控系统，偏向于硬件监控
- Prometheus：新一代监控系统
- Grafana：可视化仪表盘
- Indfluxdb：时序数据库
- SkyWalking：分布式链路追踪

### 1.2 Prometheus介绍

Prometheus是由SoundCloud开发的开源监控报警系统和时序列数据库(TSDB)，Prometheus使用Go语言开发，是Google BorgMon监控系统的开源版本。

2016年由Google发起Linux基金会旗下的原生云基金会(Cloud Native Computing Foundation)将Prometheus纳入其下第二大开源项目，Prometheus目前在开源社区相当活跃。

Prometheus和Heapster(Heapster是K8S的一个子项目，用于获取集群的性能数据)相比功能更完善、更全面，Prometheus性能也足够支撑上万台规模的集群。

> 官网：https://prometheus.io/

### 1.3 Prometheus特点

- 多维度数据模型
- 灵活的查询语言
- 不依赖分布式存储，单个服务器节点是自主的
- 通过基于HTTP的pull方式采集时序数据
- 可以通过中间网关进行时序列数据推送
- 通过服务发现或者静态配置来发现目标服务对象
- 支持多种多样的图表和界面展示，比如Grafana等

### 1.4 Prometheus原理

![img](http://rocks526.top/lzx/16027237814690.jpg)

从上图可以看到，整个 Prometheus 可以分为四大部分，分别是：

- `Prometheus 服务器`

Prometheus Server 是 Prometheus组件中的核心部分，负责实现对监控数据的获取，存储以及查询。

- `NodeExporter 采集数据源`

业务数据源通过 Pull/Push 两种方式推送数据到 Prometheus Server。

- `AlertManager 报警管理器`

Prometheus 通过配置报警规则，如果符合报警规则，那么就将报警推送到 AlertManager，由其进行报警处理。

- `可视化监控界面`

Prometheus 收集到数据之后，由 WebUI 界面进行可视化图标展示。

目前我们可以通过自定义的 API 客户端进行调用数据展示，也可以直接使用 Grafana 解决方案来展示。

> 简单地说，Prometheus 的实现架构也并不复杂。**其实就是收集数据、处理数据、可视化展示，再进行数据分析进行报警处理。** 但其珍贵之处在于提供了一整套可行的解决方案，并且形成了一整个生态，能够极大地降低我们的研发成本。

# 二：Prometheus Server 安装部署

### 2.1 Prometheus Server 安装

- 下载Prometheus

Prometheus 服务端负责数据的收集，因此我们应该首先安装并运行 Prometheus Server，下载地址：https://prometheus.io/download/。

解压后，Prometheus 的目录结构如下所示：

![image-20210915141345721](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210915141345721.png)

其中 data 目录是数据的存储路径，也可以通过运行时的 `--storage.tsdb.path="data/"` 命令另行指定。Prometheus.yml 是 Prometheus的配置文件，prometheus 是运行的命令。

启动prometheus服务，其会默认加载当前路径下的prometheus.yaml文件，也可以手动指定配置文件地址：

```shell
./prometheus --config.file=prometheus.yml
```

启动后，输出日志如下，：

```shell
level=info ts=2021-09-15T06:15:14.588Z caller=head.go:506 component=tsdb msg="Replaying WAL, this may take a while"
level=info ts=2021-09-15T06:15:14.589Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=0 maxSegment=1
level=info ts=2021-09-15T06:15:14.590Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=1 maxSegment=1
level=info ts=2021-09-15T06:15:14.591Z caller=head.go:583 component=tsdb msg="WAL replay completed" checkpoint_replay_duration=29.065µs wal_replay_duration=2.96001ms total_replay_duration=3.010731ms
level=info ts=2021-09-15T06:15:14.593Z caller=main.go:849 fs_type=EXT4_SUPER_MAGIC
level=info ts=2021-09-15T06:15:14.593Z caller=main.go:852 msg="TSDB started"
level=info ts=2021-09-15T06:15:14.593Z caller=main.go:979 msg="Loading configuration file" filename=prometheus.yml
level=info ts=2021-09-15T06:15:14.594Z caller=main.go:1016 msg="Completed loading of configuration file" filename=prometheus.yml totalDuration=871.496µs db_storage=1.836µs remote_storage=7.303µs web_handler=765ns query_engine=1.383µs scrape=365.62µs scrape_sd=54.767µs notify=40.353µs notify_sd=15.272µs rules=5.092µs
level=info ts=2021-09-15T06:15:14.594Z caller=main.go:794 msg="Server is ready to receive web requests."
```

prometheus的默认web端口为9090，可以看到如下页面：

![image-20210915151847184](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210915151847184.png)

### 2.2 Prometheus Server 配置

- prometheus配置文件：https://prometheus.io/docs/prometheus/latest/configuration/configuration/

```yml
# 全局配置
global:
  scrape_interval: 15s # 设置数据抓取频率为15s，默认1min，job级别的配置会覆盖全局配置
  evaluation_interval: 15s # 设置监控规则计算频率为15s，默认1min
  scrape_timeout: 15s # 数据抓取超时时间，默认10s

# 告警配置
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# 规则配置，根据全局配置的规则检测间隔，抓取数据进行检测
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# 数据抓取配置 ==>  此处配置的是抓取prometheus server自身的指标
scrape_configs:
  # 作业名称
  - job_name: "prometheus"
    #metrics_path ==> 抓取指标的url，默认/metrics
    #scheme ==> 抓取指标的协议，默认http
    static_configs:
        # 采集点
      - targets: ["127.0.0.1:9090"]
```

- 配置文件检查

```shell
./promtool check config prometheus.yml
```

- 启动时的扩展参数

```shell
./prometheus --config.file="prometheus.yml" --web.listen-address="0.0.0.0:9090"
# --config.file="/usr/local/prometheus/prometheus.yml"  #指定配置文件路径
# --web.listen-address="0.0.0.0:9090"  #指定服务端口
# --storage.tsdb.path="/data/prometheus"  #指定数据存储路径
# --storage.tsdb.retention.time=15d  #数据保留时间
# --collector.systemd #开启服务状态监控，开启后在WEB上可以看到多出相关监控项
# --collector.systemd.unit-whitelist=(sshd|nginx).service  #具体要监控的服务名
# --web.enable-lifecycle  #开启热加载配置
```

- 配置文件热加载

```shell
--web.enable-lifecycle后，通过`curl -X POST http://localhost:9090/-/reload`接口可以实现配置文件的热加载.
```

# 三：Prometheus 整合 Grafana

Prometheus UI 提供了快速验证 PromQL 以及临时可视化支持的能力，但其可视化能力却比较弱。一般情况下，我们都用 Grafana 来实现对 Prometheus 的可视化实现。

### 4.1 Grafana介绍

Grafana 是一个用来展示各种各样数据的开源软件，提供大量的仪表盘组件，也支持各种数据源。我们只需要在 Grafana 上配置一个 Prometheus 的数据源，接着我们就可以配置各种图表，Grafana 就会自动去 Prometheus 拉取数据进行展示。

官网：https://grafana.com/

### 4.2 Grafana安装

- 下载安装包

```shell
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-8.1.3.linux-amd64.tar.gz
tar -zxvf grafana-enterprise-8.1.3.linux-amd64.tar.gz
```

- 启动

```shell
cd grafana-8.1.3/bin/
# 启动web，默认端口3000
./grafana-server web
# 访问web页面
http://10.48.124.162:3000/
```

![image-20210916114333649](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916114333649.png)

Granafa的初始账号密码为：admin/admin，登录后如下所示：

![image-20210916114419615](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916114419615.png)

### 4.3 Grafana配置数据源

- 打开数据源配置

![image-20210916114559520](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916114559520.png)

- 打开后如下所示：

![image-20210916114630717](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916114630717.png)

- 添加一个数据源，选择Prometheus

![image-20210916114846365](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916114846365.png)

- 保存后会自动测试数据源的连通性

![image-20210916114908224](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916114908224.png)

### 4.4 Grafana配置面板

在 Grafana 中有 `Dashboard` 和 `Panel` 的概念，Dashboard 可以理解成 `看板` ，而 Panel 可以理解成 `图表` ，一个看板中包含了无数个图表。例如下图就是一个看板（Dashboard）：

![img](https://www-shuyi-me.oss-cn-shenzhen.aliyuncs.com/blog/16027618541693.jpg)

里面一个个小的图表，就是一个个小的图表（Panel）。

点击「+号」-> 「Dashboard」就可以添加一个大面板。

![image-20210916144505286](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916144505286.png)

- 添加一个CPU监控的图表

![image-20210916191436637](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916191436637.png)

### 4.5 Grafana模板中心

对于线上监控来讲，如果每个面板都需要自己从零开始，那么就太累了。事实上，我们用到的许多监控信息都是类似的。因此 [Grafana官网 - Dashboards 模块](https://grafana.com/grafana/dashboards) 提供了下载 Dashboard 模板的功能。

![image-20210916191817834](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916191817834.png)

Dashboards 里有许多各种类型的 Dashboard 面板，例如 JVM 监控、MySQL 数据库监控等。只需找到合适自己的监控面板，之后根据 ID 添加即可。

例如这个硬件资源监控的模板，包含了各种常见的资源监控，例如：CPU、内存等。

![image-20210916192437577](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916192437577.png)

只需要复制它的 ID 并使用 Grafana 的 import 功能导入即可，如下图所示：

![image-20210916192145827](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916192145827.png)

![image-20210916192505146](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916192505146.png)

- 如果内网不支持导入，可以手动下载模板的json文件进行上传导入，最终效果图如下所示：

![image-20210916192712372](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916192712372.png)



# 四：Prometheus Exporter安装部署

### 3.1 Node Exporter 安装

NodeExporter 是 Prometheus 提供的一个可以采集到主机信息的应用程序，它能采集到机器的 CPU、内存、磁盘等信息。

- 下载NodeExporter 

下载地址：https://prometheus.io/download/，NodeExporter 只支持Mac和Linux。

- 解压&运行

```shell
tar -zxvf node_exporter-1.2.2.linux-amd64.tar.gz
./node_exporter --web.listen-address 10.48.124.162:9091
```

- 启动后输出日志：

```shell
level=info ts=2021-09-15T08:19:53.113Z caller=node_exporter.go:115 collector=uname
level=info ts=2021-09-15T08:19:53.113Z caller=node_exporter.go:115 collector=vmstat
level=info ts=2021-09-15T08:19:53.113Z caller=node_exporter.go:115 collector=xfs
level=info ts=2021-09-15T08:19:53.113Z caller=node_exporter.go:115 collector=zfs
level=info ts=2021-09-15T08:19:53.113Z caller=node_exporter.go:199 msg="Listening on" address=10.48.124.162:9091
level=info ts=2021-09-15T08:19:53.113Z caller=tls_config.go:191 msg="TLS is disabled." http2=false
```

- 查看监控指标

![image-20210915162348811](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210915162348811.png)

![image-20210915162533451](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210915162533451.png)

每一个监控指标之前都会有一段类似于如下形式的信息：

```shell
# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 3.44556542e+06
node_cpu_seconds_total{cpu="0",mode="iowait"} 44569.94
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0.06
node_cpu_seconds_total{cpu="0",mode="softirq"} 126565.8
```

其中 HELP 用于解释当前指标的含义，TYPE 则说明当前指标的数据类型。

- 配置Prometheus Server 监控数据源

```yml
scrape_configs:
  # 作业名称 此处配置的是抓取prometheus server自身的指标
  - job_name: "prometheus"
    #metrics_path ==> 抓取指标的url，默认/metrics
    #scheme ==> 抓取指标的协议，默认http
    static_configs:
        # 采集点
      - targets: ["127.0.0.1:9090"]
  - job_name: "node-162"
    static_configs:
      - targets: ["127.0.0.1:9091"]
```

- 查询监控数据

配置完采集数据源数据后，通过 Prometheus UI 可以查询 Prometheus 收集到的数据，而 Prometheus 定义了 PromQL 语言来作为查询监控数据的语言，和 SQL 类似。输入up命令，可以查看正在运行的采集器：

![image-20210915172305648](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210915172305648.png)

可以看到 `Element` 处有几条记录，其中 instance 值为采集器的实例名称，后面的value 是 1，这代表对应应用是存活状态。

![image-20210915173134344](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210915173134344.png)

### 3.2 Redis Exporter 安装

- 下载Redis Exporter

下载地址：https://github.com/oliver006/redis_exporter/releases

- 解压 & 安装

```shell
tar -zxvf redis_exporter-v1.27.0.linux-amd64.tar.gz
cd redis_exporter-v1.27.0.linux-amd64
# 查看帮助文档
./redis_exporter --help  
# 启动redis exporter 
./redis_exporter -redis.addr 10.48.124.162:6380 -redis.password dfe69c50663c425334d06f940ded0e31 &
# 查看端口监控，默认redis exporter 的端口为9121
netstat -tunlp | grep 9121
```

- 查看redis监控指标

![image-20210916101525003](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916101525003.png)

![image-20210916101709425](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916101709425.png)

- 配置Prometheus Server 监控数据源

```yml
# 数据抓取配置
scrape_configs:
  # 作业名称 此处配置的是抓取prometheus server自身的指标
  - job_name: "prometheus"
    #metrics_path ==> 抓取指标的url，默认/metrics
    #scheme ==> 抓取指标的协议，默认http
    static_configs:
        # 采集点
      - targets: ["10.48.124.162:9090"]
  - job_name: "node-162"
    static_configs:
      - targets: ["10.48.124.162:9091"]
  - job_name: "redis"
    static_configs:
      - targets: ["10.48.124.162:9121"]
```

![image-20210916102437638](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916102437638.png)

![image-20210916102808155](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210916102808155.png)

### 3.3 Nginx Exporter 安装

用Prometheus进行nginx的监控可以自动的对相关server_name和upstream进行监控，也可以自定义Prometheus的数据标签，实现对不同机房和不同项目的nginx进行监控。

监控Nginx主要用到以下三个模块：

- nginx-module-vts：Nginx virtual host traffic status module，Nginx的监控模块，能够提供JSON格式的数据产出。
- nginx-vts-exporter：Simple server that scrapes Nginx vts stats and exports them via HTTP for Prometheus consumption。主要用于收集Nginx的监控数据，并给Prometheus提供监控接口，默认端口号9913。
- Prometheus：监控Nginx-vts-exporter提供的Nginx数据，并存储在时序数据库中，可以使用PromQL对时序数据进行查询和聚合。

`安装Nginx和nginx-module-vts模块`：

```shell
# 安装依赖
yum -y install pcre-devel
yum -y install openssl openssl-devel
# 下载模块上传环境
unzip /opt/qax/ngsoc/lzx/nginx-module-vts.zip  -o
# 编译
./configure --with-http_ssl_module --prefix=/opt/qax/ngsoc/lzx/nginx/ --with-http_stub_status_module --with-threads --with-file-aio --add-module=/opt/qax/ngsoc/lzx/nginx-module-vts-master
# 安装
make && make install
# 如果已有Nginx，将新安装的nginx命令覆盖原有nginx命令即可
```

`修改Nginx配置文件，打开状态监控`：

```conf
http {
    ...
    # 打开Nginx监控
    vhost_traffic_status_zone;
    # 多个Server区分计算
    vhost_traffic_status_filter_by_host on;
    ...
    server {
        ...   
        location ^~ /status {
               vhost_traffic_status_display;
               vhost_traffic_status_display_format html;
        }
        
        server {
        ...
        # 不想统计的server可以禁用
        vhost_traffic_status off;
        ...
        }
}


```

`访问/status页面，查看监控指标`：

![image-20210917171604570](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210917171604570.png)

`安装Nginx Exporter`：https://github.com/hnlq715/nginx-vts-exporter

```shell
# 新版本的exporter只提供源码包，编译需要go环境，可以安装旧版本的包，提供linux-cmd64.tar.gz
# 上传tar包 & 解压
tar -zxvf nginx-vts-exporter-0.10.0.linux-amd64.tar.gz
# 启动采集器
cd nginx-vts-exporter-0.10.7
nohup ./nginx-vts-exporter -nginx.scrape_timeout 10 -nginx.scrape_uri https://10.48.124.162/status/format/json &
```

`访问web页面-http://ip:9913/，查看监控指标`：

![image-20210917173508548](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210917173508548.png)

`设置Prometheus Server抓取监控数据`：

```yml
# 数据抓取配置
scrape_configs:
  # 作业名称 此处配置的是抓取prometheus server自身的指标
  - job_name: "prometheus"
    #metrics_path ==> 抓取指标的url，默认/metrics
    #scheme ==> 抓取指标的协议，默认http
    static_configs:
      - targets: ["10.48.124.162:9090"]
  - job_name: "node-162"
    static_configs:
      - targets: ["10.48.124.162:9091"]
  - job_name: "redis"
    static_configs:
      - targets: ["10.48.124.162:9121"]
  - job_name: "nginx"
    static_configs:
      - targets: ["10.48.124.162:9913"]
```

`查看Prometheus Server的job状态`：

![image-20210917173743394](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210917173743394.png)

`导入Nginx的Grafana模板`：

![image-20210917191703752](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20210917191703752.png)

### 3.4 Postgresql Exporter 安装

Postgresql Exporter是 Prometheus 社区提供的一个可以采集到Postgresql数据库信息的监控程序，它能采集到Postgresql的 配置、内存、QPS等信息。

- 下载Postgresql Exporter

下载地址：https://github.com/wrouesnel/postgres_exporter/releases，支持Windows、Linux等

- 安装Postgresql Exporter

```shell
# 下载
wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.10.1/postgres_exporter-0.10.1.linux-amd64.tar.gz
# 解压
tar -zxvf postgres_exporter-0.10.1.linux-amd64.tar.gz
cd postgres_exporter-0.10.1.linux-amd64
# 启动

```