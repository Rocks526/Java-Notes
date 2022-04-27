# 一：JMeter脚本

### 1.1 JMeter脚本构建

JMeter提供了GUI页面，脚本构建较为简单，但为了尽可能避免压测脚本的性能，提升扩展性，需要遵循以下准则：

- 提供公共部分，增加脚本扩展性：例如请求协议，IP，端口，Cookie等
- 尽量较少结果树的使用：结果树插件比较消耗性能，在正式压测中应当禁止使用
- 尽可能让脚本精简：过于复杂的逻辑会影响压力发起的效率
- 尽量少使用JMeter周边插件：例如不要使用JMeter监控服务器资源，会影响发压效率
- 尽可能的还原真实业务：比如读写流量2:8等等

### 1.2 JMeter脚本执行

在构建好脚本后，下面就是脚本的执行；JMeter的脚本可以通过GUI执行，但并非推荐模式；JMeter启动的提示信息如下：

![Drawing 1.png](https://s0.lgstatic.com/i/image/M00/8D/6D/CgqCHl_-p-SAVYK0AAGf14hnG0w748.png)

提示信息里说明：`GUI的在用脚本的构建和测试，真正的压测要采用命令行的方式`，因为GUI的压测方式会消耗较多的客户端性能，在压测过程中容易因为客户端问题导致内存溢出。

那么命令行如何执行JMeter脚本？

JMeter命令行执行语法如下：

```shell
jmeter -n -t [jmx file] -l [results file] -e -o [Path to web report folder]
```

- -n 表示在非 GUI 模式下运行 JMeter
- -t 表示要运行的 JMeter 测试脚本文件，一般是 jmx 结尾的文件
- -l 表示记录结果的文件，默认以 jtl 结尾
- -e 表示测试完成后生成测试报表
- -o 表示指定的生成结果文件夹位置

通过命令行执行脚本，输出如下图所示：

![Drawing 2.png](https://s0.lgstatic.com/i/image/M00/8D/6D/CgqCHl_-p_GATU1yAACmJ7JsOJs941.png)

从图中可以看到，命令行的方式直接产生了总的 TPS、报错和一些时间层级的指标。命令行的执行方式规避了图形化界面存在的性能问题，但这样的结果输出方式存在 2 个问题：

- 看不到实时的接口返回报错的具体信息
- 看不到混合场景下的每个接口的实时处理能力

### 1.3 实时接口报错信息获取

压测过程中，实时的接口报错信息可以通过两方面去获取：

- 服务端的日志
- JMeter的运行日志jmeter.log

jmeter的运行日志如下所示：

![Drawing 3.png](https://s0.lgstatic.com/i/image/M00/8D/6D/CgqCHl_-p_yAeoEUAACugfrmrq0387.png)

可以看到，jmeter.log里默认只能显示报错率，但看不到具体的报错内容，具体报错内容一般通过 `beanshell` 进行记录，把判定为报错的内容增加到 log 里；示例代码如下所示：

```java
String response = prev.getResponseDataAsString();
//获取接口响应信息
String code = prev.getResponseCode();
//获取接口响应状态码
if (code.equals("200")){//根据返回状态码判断
	// log.info("Respnse is " + response);
    //打印正确的返回信息，建议调试使用避免无谓的性能消耗
}else {
	log.error("Error Response is"+response);
    //打印错误的返回信息
	}
```

这样就会自动在 jmeter.log 中打印出具体的报错信息。

### 1.4 每个接口的实时处理能力统计

如果想实时且直观地看到每个接口的处理能力，一般有以下两种方式：

- JMeter+InfluxDB+Grafana：将JMeter采集到的数据借助第三方工具进行存储和分析
- JMeter二次开发：JMeter提供了非常多的扩展，可以很轻易的实现二次开发

一般没有特殊需求，建议采用JMeter+InfluxDB+Grafana的方式，示意图如下：

![Lark20210113-183606.png](https://s0.lgstatic.com/i/image/M00/8D/73/CgqCHl_-zTuAMf2DAABpdYPtYUw826.png)

下面对 InfluxDB 和 Grafana 做一个简单的介绍、安装。

# 二：JMeter+InfluxDB+Grafana

### 2.1 InfluxDB 介绍及安装

- 介绍

InfluxDB 是 Go 语言编写的`时间序列数据库`，用于`处理海量写入与负载查询`。涉及大量时间戳数据的任何用例（包括 DevOps 监控、应用程序指标等）。InfluxDB 最大的特点在于可以按照时间序列面对海量数据时候的高性能读写能力，非常适合在性能测试场景下用作数据存储。

- 安装

```shell
# 下载安装包
[root@10.48.124.162 influxdb-1.7.8-1]# wget https://dl.influxdata.com/influxdb/releases/influxdb-1.7.8_linux_amd64.tar.gz
# 解压
[root@10.48.124.162 influxdb-1.7.8-1]# tar -zxvf influxdb-1.7.8_linux_amd64.tar.gz
# 创建数据、元数据、wal日志存放目录
[root@10.48.124.162 influxdb-1.7.8-1]# cd influxdb-1.7.8-1
[root@10.48.124.162 influxdb-1.7.8-1]# mkdir data
[root@10.48.124.162 influxdb-1.7.8-1]# mkdir meta
[root@10.48.124.162 influxdb-1.7.8-1]# mkdir wal-log
# 查看原来的配置文件
[root@10.48.124.162 influxdb-1.7.8-1]# egrep -v "^#|^$|#" etc/influxdb/influxdb.conf
[meta]
  dir = "/var/lib/influxdb/meta"
[data]
  dir = "/var/lib/influxdb/data"
  wal-dir = "/var/lib/influxdb/wal"
  series-id-set-cache-size = 100
# 修改配置文件，修改后如下
[root@10.48.124.162 influxdb-1.7.8-1]# egrep -v "^#|^$|#" etc/influxdb/influxdb.conf
[meta]
  dir = "/opt/lzx/influxdb-1.7.8-1/meta"
[data]
  dir = "/opt/lzx/influxdb-1.7.8-1/data"
  wal-dir = "/opt/lzx/influxdb-1.7.8-1/wal-log"
  series-id-set-cache-size = 100
  # 启动
 [root@10.48.124.162 influxdb-1.7.8-1]# usr/bin/influxd -config ./etc/influxdb/influxdb.conf
 # 客户端连接
 [root@10.48.124.162 influxdb-1.7.8-1]# usr/bin/influx
```

- 基本操作

```shell
# 显示数据库
show databases;
# 创建数据库：
create database jmeter;
# 使用数据库
use jmeter;
# 查看库下面的表
show measurements;
```

### 2.2 Grafana 介绍及安装

- 介绍

`Grafana 是一个跨平台的开源的度量分析和可视化工具`，纯 JavaScript 开发的前端工具，通过访问库（如 InfluxDB），展示自定义报表、显示图表等。大多时候用在时序数据的监控上。Grafana 功能强大、UI 灵活，并且提供了丰富的插件。

- 安装

```shell
# 下载安装包
https://grafana.com/get/?plcmt=top-nav&cta=downloads
# 解压
[root@10.48.124.162 lzx]# tar -zxvf grafana-6.0.1.linux-amd64.tar.gz 
# 启动
[root@10.48.124.162 lzx]# cd grafana-6.0.1/bin
[root@10.48.124.162 lzx]# ./grafana-server 
```

默认用户名密码为：admin/admin；

### 2.3 JMeter+InfluxDB+Grafana集成

- JMeter添加InfluxDB插件

![image-20220426160639194](http://rocks526.top/lzx/image-20220426160639194.png)

![image-20220426161052726](http://rocks526.top/lzx/image-20220426161052726.png)

- 启动压测脚本，此时数据已经记录到InfluxDB数据库里

![image-20220426161334988](http://rocks526.top/lzx/image-20220426161334988.png)

- Grafana数据源配置

![image-20220426171912064](http://rocks526.top/lzx/image-20220426171912064.png)

- 模板导入：https://grafana.com/grafana/dashboards/5496

![image-20220426172657496](http://rocks526.top/lzx/image-20220426172657496.png)

- 展示

![image-20220426172814282](http://rocks526.top/lzx/image-20220426172814282.png)

# 三：JMeter压测示例

- 执行脚本开始压测

```shell
[root@ngsoc163 apache-jmeter-5.4.3]# bin/jmeter -n -t repository/portal.jmx -l result/portal-test.jtl -e -o result/portal-test/
Creating summariser <summary>
Created the tree successfully using repository/portal.jmx
Starting standalone test @ Wed Apr 27 10:21:00 CST 2022 (1651026060059)
Waiting for possible Shutdown/StopTestNow/HeapDump/ThreadDump message on port 4445
summary +      1 in 00:00:01 =    0.9/s Avg:   221 Min:   221 Max:   221 Err:     0 (0.00%) Active: 86 Started: 86 Finished: 0
summary +  52136 in 00:00:28 = 1852.2/s Avg:    54 Min:     8 Max:   857 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary =  52137 in 00:00:29 = 1783.0/s Avg:    54 Min:     8 Max:   857 Err:     0 (0.00%)
summary +  57721 in 00:00:30 = 1924.0/s Avg:    51 Min:    10 Max:   222 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 109858 in 00:00:59 = 1854.4/s Avg:    52 Min:     8 Max:   857 Err:     0 (0.00%)
summary +  57679 in 00:00:30 = 1922.6/s Avg:    51 Min:    14 Max:   212 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 167537 in 00:01:29 = 1877.4/s Avg:    52 Min:     8 Max:   857 Err:     0 (0.00%)
summary +  58706 in 00:00:30 = 1956.9/s Avg:    50 Min:     9 Max:   177 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 226243 in 00:01:59 = 1897.4/s Avg:    51 Min:     8 Max:   857 Err:     0 (0.00%)
summary +  56895 in 00:00:30 = 1896.5/s Avg:    52 Min:    14 Max:   607 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 283138 in 00:02:29 = 1897.2/s Avg:    51 Min:     8 Max:   857 Err:     0 (0.00%)
summary +  58144 in 00:00:30 = 1938.1/s Avg:    51 Min:     9 Max:   178 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 341282 in 00:02:59 = 1904.0/s Avg:    51 Min:     8 Max:   857 Err:     0 (0.00%)
summary +  53595 in 00:00:30 = 1786.5/s Avg:    55 Min:    11 Max:  1440 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 394877 in 00:03:29 = 1887.2/s Avg:    52 Min:     8 Max:  1440 Err:     0 (0.00%)
summary +  57725 in 00:00:30 = 1924.2/s Avg:    51 Min:     5 Max:  1008 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 452602 in 00:03:59 = 1891.8/s Avg:    52 Min:     5 Max:  1440 Err:     0 (0.00%)
summary +  55859 in 00:00:30 = 1862.0/s Avg:    53 Min:     6 Max:   210 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 508461 in 00:04:29 = 1888.5/s Avg:    52 Min:     5 Max:  1440 Err:     0 (0.00%)
summary +  59332 in 00:00:30 = 1977.7/s Avg:    50 Min:    19 Max:   152 Err:     0 (0.00%) Active: 100 Started: 100 Finished: 0
summary = 567793 in 00:04:59 = 1897.4/s Avg:    52 Min:     5 Max:  1440 Err:     0 (0.00%)
summary +   3421 in 00:00:02 = 1674.5/s Avg:    41 Min:     4 Max:   118 Err:     0 (0.00%) Active: 0 Started: 100 Finished: 100
summary = 571214 in 00:05:01 = 1895.9/s Avg:    51 Min:     4 Max:  1440 Err:     0 (0.00%)
Tidying up ...    @ Wed Apr 27 10:26:02 CST 2022 (1651026362044)
... end of run
```

- JMeter监控如下

![image-20220427103316694](http://rocks526.top/lzx/image-20220427103316694.png)

- JMeter压测结果

```shell
[root@ngsoc163 apache-jmeter-5.4.3]# cd result/
[root@ngsoc163 result]# ll
drwxr-xr-x 4 root root     4096 Apr 27 10:26 portal-test	# 压测结果
-rw-r--r-- 1 root root 90791640 Apr 27 10:26 portal-test.jtl	# JMeter运行日志
```

打开压测结果，如下所示：

![image-20220427104030564](http://rocks526.top/lzx/image-20220427104030564.png)

![image-20220427104419655](http://rocks526.top/lzx/image-20220427104419655.png)

重点关注并发量、TP95、TP99、QPS等指标。