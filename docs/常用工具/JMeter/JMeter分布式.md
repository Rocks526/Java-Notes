# 一：JMeter分布式

### 1.1 为什么需要分布式JMeter

之前在介绍JMeter监听器时，提到过`图形化的监听器会严重影响JMeter的性能，导致客户端压力不够而压测结果不准`，那么是否使用命令行的方式就可以解决这个问题？

答案是否定的，对于一般系统、要求1k并发以内、QPS在1W以内单机JMeter压测足以，但大于并发量较高的大型系统，要求十几万并发的压测，单机JMeter就无能为力，这时候就需要分布式JMeter。

> 注：单机JMeter按照经验值，一般1K个线程/并发就会达到上限，再多就要考虑分布式压测。

### 1.2 分布式JMeter架构介绍

在分布式 JMeter 架构下，JMeter 使用的是 Master 和 Slave。

- Master

Master 负责远程控制 Slave（负载机），分布式通常有多个 JMeter 节点，其中一个节点承担 Master 的作用；Master 通过发送信号控制节点机的启动和停止，并进行收集节点机的数据等操作。

- Slave

Slave 一般也叫负载机，主要是发起线程来访问 target 服务器；一般在 Slave 节点机上先启动代理 jar 包，控制机远程连接，负载机运行脚本后对 Master 回传数据；流程示意图如下：

![图片3.png](http://rocks526.top/lzx/CgqCHl_2gXGAX4J2AAB_Vvcgr6E022.png)

JMeter 的 Master 和 Slave 配置也比较简单，将 JMeter 的 bin 目录下的 jmeter.properties 文件配置：IP 和 Port 是 Slave 机的 IP 以及默认的 1099 端口；如下所示：

```properties
remote_hosts=ip:1099,ip:1099
```

Slave 启动 jar 包之后，默认会启动 1099 端口；Master 配置完成启动后便可以建立和 Slave 连接，从而进行控制和收集等操作。

一般来说，`JMeter 分布式压测都是作为缓减客户端瓶颈的重要方式`。

# 二：分布式压测实战