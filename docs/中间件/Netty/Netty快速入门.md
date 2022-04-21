# 一：Netty介绍

### 1.1 Netty介绍

Netty是jboss提供的一个Java开源框架，Netty提供`异步的、事件驱动`的网络应用程序框架和工具，用以快速开发`高性能、高可用`的网络服务器和客户端程序。也就是说`Netty是一个基于NIO的编程框架`，使用Netty可以快速的开发出一个网络应用。

由于Java自带的NIO API使用起来非常复杂，并且还可能出现 Epoll Bug，这使得我们使用原生的NIO来进行网络编程存在很大的难度且非常耗时，因此出现了Netty，Netty良好的设计可以使开发人员快速高效的进行网络应用开发。

注：学习Netty之前，建议先看看Java的NIO编程，熟悉Java的BIO、NIO、AIO编程和场景的线程模型后再学习Netty。

### 1.2 Netty架构设计

如下图所示：

Netty的核心是支持零拷贝的ByteBuf缓冲对象、通用通信api和可扩展的事件模型；它支持多种传输服务并且支持HTTP、Protobuf、二进制、文本、WebSocket 等一系列常见协议，也支持自定义协议。

![img](http://rocks526.top/lzx/1277268-20190219150626351-482632846.png)

Netty的模型是基于Reactor多线程模型，其中MainReactor用于接收客户端请求并转发给SubReactor，SubReactor负责通道的读写请求，非 IO 请求（具体逻辑处理）的任务则会直接写入队列，等待 Worker Threads 进行处理。

![img](http://rocks526.top/lzx/1277268-20190219152254900-1708549562.png)

### 1.3 Netty的核心概念

- Bootstrap、ServerBootstrap

Bootstrap的意思是引导，其主要作用是配置整个Netty程序，将各个组件整合起来；

Bootstrap用于连接远程主机；它有一个EventLoopGroup ；

ServerBootstrap是服务器端的引导类，ServerBootstrap用于监听本地端口，有两个EventLoopGroup。

- EventLoop

EventLoop代表事件循环，内部维护了一个线程和任务队列，支持异步提交执行任务。

- EventLoopGroup

EventLoopGroup 主要是管理EventLoop的生命周期，可以将其看作是一个线程池，其内部维护了一组EventLoop，每个EventLoop对应处理多个Channel，而一个Channel只能对应一个EventLoop。

- ChannelPipeLine

ChannelPipeLine是一个包含ChannelHandler的集合，采用了责任链模式，用来设置ChannelHandler的执行顺序。

- Channel

Channel代表一个实体（如一个硬件设备、一个文件、一个网络套接字等）的开放连接，可以进行读操作、写操作。

- Futrue、ChannelFuture

Future是异步操作结果的抽象接口，提供了另一种在操作完成时通知应用程序的方式。

这个对象可以看作是一个异步操作结果的占位符；它将在未来的某个时刻完成，并提供对其结果的访问。Netty的大部分API都会返回一个ChannelFuture。Future上面可以注册一个监听器，当对应的事件发生后会触发该监听器。

- ChannelInitializer

ChannelInitializer是一个特殊的ChannelInboundHandler，当Channel注册到EventLoop上面时，对Channel进行初始化。

- ChannelHandler

Channel的处理器，用于对连接的客户端进行读写处理或者业务处理，ChannelHandler是一个父接口，ChannelnboundHandler和ChannelOutboundHandler都继承了该接口，它们分别用来处理读和写。

- ChannelHandlerContext

ChannelHandler上下文容器，允许与其关联的ChannelHandler、ChannlePipeline和其它ChannelHandler来进行交互，也可以对其所属的ChannelPipeline进行动态修改。

注：对这些概念不理解也没有关系，Netty的上手门槛很低，后面通过一个IM系统进行快速入门。

#  二：IM系统介绍

### 2.1 单聊流程

![image-20220419150021582](http://rocks526.top/lzx/image-20220419150021582.png)

1. A和B登录服务器，与服务器建立连接，服务器存储A和B的标识和TCP连接的映射关系
2. A给B发送消息，将携带B标识的消息发送到服务器，服务器获取B标识，将消息转发给B
3. 任意一方发送消息给对方，如果对方不在线，则需要将消息缓存，在对方上线后再发送

### 2.2 单聊的指令

![image-20220419150312786](http://rocks526.top/lzx/image-20220419150312786.png)

单聊指令如下：

- 登录请求
- 登录响应
- 客户端发送消息
- 服务端发送消息
- 登出请求
- 登出响应

### 2.3 群聊流程

群聊指一个组内多个用户之间的聊天，一个用户发到群组的消息会被组内任意一个成员收到。

![image-20220419150606408](http://rocks526.top/lzx/image-20220419150606408.png)

1. A、B、C依次登录，服务端保存用户标识和TCP连接
2. A发起群聊，将A、B、C的标识发到服务端，服务端建立群聊，生成ID和群用户的映射
3. 群聊里任一方发起聊天的时候，将群ID发送到服务端，服务端根据群ID取出所有用户标识，依次发给所有用户

### 2.4 群聊指令

![image-20220419150802736](http://rocks526.top/lzx/image-20220419150802736.png)

群聊需要支持的指令如下：

- 创建群聊请求
- 群聊创建成功通知
- 加入群聊请求
- 群聊加入通知
- 发送群聊消息
- 接收群聊消息
- 退出群聊请求
- 退出群聊通知









