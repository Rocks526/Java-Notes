- [网络编程模型](#mx)
  - [同步/异步/阻塞/非阻塞](#tb&yb)
  - [五种IO模型](#mx)
  - [Reactor](#reactor)
  - [Proactor](#proactor)
- [Java中的IO](#io)
  - [BIO](#bio)
  - [NIO](#nio)
  - [AIO](#aio)
- [聊天室Demo](#lts)

# <a id="mx">网络编程模型</a>

### <a id="tb&yb">同步&异步||阻塞&非阻塞</a>

一般来说，同步状态是当前线程发起请求调用后，在被调用的线程处理消息过程中，当前线程必须等被调用的线程处理完才能返回结果；如果被调用的线程还未处理完，则当前线程是不能返回结果的，当前线程必须主动等待所需的结果。异步状态是当前线程发起请求调用后，被调用的线程直接返回，但是并没有返回给当前线程对应的结果，而是等被调用的线程处理完消息后，通过状态、通知或者回调函数来通知调用线程，当前线程处于被动接收结果的状态。**从上述说明不难发现，同步和异步最主要的区别在于任务或接口调用完成时消息通知的方式。**

一般来说，阻塞状态是被调用的线程在返回处理结果前，当前线程会被挂起，不释放CPU执行权，当前线程也不能做其他事情，只能等待，直到被调用的线程返回处理结果后，才能接着向下执行。非阻塞状态是当前线程在没有获取被调用的线程的处理结果前，不是一直等待，而是继续向下执行。如果此时是同步状态，则当前线程可以通过轮询的方式检查被调用线程的处理结果是否返回；如果此时是异步状态，则只有在被调用的线程处理后才会通知当前线程回调。**从上述说明不难发现，阻塞和非阻塞最主要的区别在于当前线程发起任务或接口调用后是否能继续执行。	**

同步和异步、阻塞和非阻塞还可以两两组合，从而产生4种组合，即同步阻塞、同步非阻塞、异步阻塞和异步非阻塞。前端人员熟悉的Node.js、运维人员和前后端人员都熟悉的Nginx就是异步非阻塞的典型代表。

![image-20200923173812841](http://rocks526.top/lzx/image-20200923173812841.png)

> 假设现在在电商平台购物，此时会有如下几种选择：（1）如果付款后，什么事情也不做，仅是坐等快递小哥来送货上门——这是同步阻塞。（2）如果付款后，什么事情也不做，仅是坐等快递小哥来送货上门；快递小哥到楼下之后会打电话和笔者确认是否在家——这是异步阻塞。（3）如果付款后，就去锻炼身体了，在休息的间隙，不时地看看App中的送货状态——这就是同步非阻塞。（4）如果付款后，就去锻炼身体了；在快递小哥到楼下之后会打电话和笔者确认是否在家——这就是异步非阻塞。

**同步异步是通信机制的区别。**

**阻塞和非阻塞是等待返回结果之前，调用方处于什么样的状态。**

### <a id="mx">五种IO模型</a>

> https://segmentfault.com/a/1190000003063859#item-3-13
>
> 以一个网络IO为例，对于一个网络IO (例如read)，它会涉及到两个系统对象：一个是调用这个IO的进程，另一个就是系统内核。当一个read操作发生时，它会经历两个阶段：
>
> **阶段1：**等待数据准备 (Waiting for the data to be ready)
>
> **阶段2：** 将数据从内核拷贝到进程中 (Copying the data from the kernel to the process)
>
> 当进程请求 I/O 操作的时候，它执行一个系统调用将控制权移交给内核。Java等高级语言针对IO封装的read，write等方法要做的无非就是建立和执行适当的系统调用。当内核以这种方式被调用，它随即采取任何必要步骤，找到进程所需数据，并把数据传送到用户空间内的指定缓冲区。内核试图对数据进行高速缓存或预读取，因此进程所需数据可能已经在内核空间里了。如果是这样，该数据只需简单地拷贝出来即可。如果数据不在内核空间，则进程被挂起，内核着手把数据读进内存。

#### 阻塞式IO

![image-20201107151546439](http://rocks526.top/lzx/image-20201107151546439.png)

当用户进程调用了recvfrom这个系统调用，kernel就开始了IO的第一个阶段：准备数据。对于network io来说，很多时候数据在一开始还没有到达（比如，还没有收到一个完整的UDP包），这个时候kernel就要等待足够的数据到来。而在用户进程这边，整 个进程会被阻塞。当kernel一直等到数据准备好了，它就会将数据从kernel中拷贝到用户内存，然后kernel返回结果，用户进程才解除 block的状态，重新运行起来。

> 内核等待数据的到来，将数据从内核拷贝到应用程序两阶段都是阻塞的。

#### 非阻塞式IO

![image-20201107151635913](http://rocks526.top/lzx/image-20201107151635913.png)

当用户进程发出read操作时，如果kernel中的数据还没有准备好，那么它并不会block用户进程，而是立刻返回一个error。 从用户进程角度讲 ，它发起一个read操作后，并不需要等待，而是马上就得到了一个结果。用户进程判断结果是一个error时，它就知道数据还没有准备好，于是它可以再次 发送read操作。一旦kernel中的数据准备好了，并且又再次收到了用户进程的system call，那么它马上就将数据拷贝到了用户内存，然后返回。

> 系统调用不再阻塞，如果内核数据没有到来，客户端会得到响应，可以根据自己的逻辑处理别的事情，过段事件再来调用。
>
> 第一阶段不再阻塞，第二阶段数据从内核拷贝到应用中，还是阻塞的。

#### 多路复用IO

> https://www.bilibili.com/video/BV1qJ411w7du

![image-20201107152025219](http://rocks526.top/lzx/image-20201107152025219.png)

当用户进程调用了`select`，那么整个进程会被block，而同时，kernel会“监视”所有select负责的socket，当任何一个 socket中的数据准备好了，select就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程。

多路复用IO有时候也被称为事件驱动IO。底层通过操作系统提供的select，poll，epoll等函数实现。IO复用同非阻塞IO本质一样，不过利用了新的select系统调用，由内核来负责本来是请求进程该做的轮询操作。看似比非阻塞IO还多了一个系统调用开销，不过因为可以支持多路IO，才算提高了效率。它的基本原理就是select /epoll这个function会不断的轮询所负责的所有socket，当某个socket有数据到达了，就通知用户进程。

这个图和blocking IO的图其实并没有太大的不同，事实上，还更差一些。因为这里需要使用两个system call (select 和 recvfrom)，而blocking IO只调用了一个system call (recvfrom)。但是，用select的优势在于它可以同时处理多个connection。

> 如果处理的连接数不是很高的话，使用 select/epoll的web server不一定比使用multi-threading + blocking IO的web server性能更好，可能延迟还更大。
>
> select/epoll的优势并不是对于单个连接能处理得更快，而是在于能处理更多的连接。
>
> 在IO multiplexing Model中，实际中，对于每一个socket，一般都设置成为non-blocking，但是，如上图所示，整个用户的process其实是一直被 block的。只不过process是被select这个函数block，而不是被socket IO给block。

> 多路复用出现场景：针对服务端处理多个客户端连接的场景，当使用多线程时，如果客户端非常多，会导致线程上下文切换很频繁，性能消耗大，而且线程会占用很多服务端资源。因此需要采用单线程，通过DMA实现CPU处理某个客户端请求时，其他客户端数据不会丢失。

1. select函数

```c
int select(int maxfd,fd_set *readset,fd_set *writeset,fd_set *exceptset,const struct timeval *timeout)
```

select函数如上所示，需要五个参数：第一个参数是最大fd(文件描述符)，第二个参数是让内核监听可读事件的文件描述符集合，第三个参数是让内核监听可写事件的文件描述符集合，第四个参数是让内核监听异常事件的文件描述符集合，第五个参数是select函数的超时时间。

![image-20201108152521645](http://rocks526.top/lzx/image-20201108152521645.png)

- select的整体执行过程如上(已可读事件为例)：

第一步：构造需要内核监听的可读或可写事件的文件描述符集合，并记录所有文件描述符中最大的文件描述符ID

第二步：构造一个位图，遍历之前构造的需要监听的可读或可写文件描述符集合，将需要监听可读或可写的文件描述符ID在位图对应位置置为1

第三步：将构造好的位图和最大文件描述符ID传给select函数，开始select函数调用

第四步：select函数将用户进程传递的位图拷贝到内核空间，CPU不断轮询检测位图前max位(最大文件描述符ID)，检测值为1的文件描述符是否准备好数据，如果没有准备好则阻塞，不断轮询检测，如果准备好，则将对应位添加标识(例如置为2)，解除阻塞返回用户进程

第五步：用户进程拿到内核返回的位图，识别出数据已经准备好的文件描述符ID，做出对应的处理，然后再次遍历需要检测的文件描述符集合，构建新的位图传入select函数(由于select函数会修改位图，因此每次需要构建新的位图传入)

- 优点

在非阻塞IO中，数据没有准备好的话，函数调用会立刻返回，如果想实现类似效果，需要在应用层写while循环处理，每次判断都需要从用户态转入内核态，select函数的优点在于将while循环放到内核执行，只需要一次用户态转换内核态，提升效率

- 缺点

位图的默认大小1024，虽然可以调整大小，但有上限；

每次select函数都会修改位图，标识有数据到达的位，导致每次select调用都需要构建新的位图；

位图从用户态拷贝到内核态需要一定的开销；

当select函数返回时，只能确定位图中存在数据被置位，但不确定哪些被置位，需要遍历O(n)开销；

2. poll函数

![image-20201125164641785](http://rocks526.top/lzx/image-20201125164641785.png)

poll函数的整体思路和select一样，区别在于函数传给内核的并不是位图，而是一个pollfd的数组，pollfd的属性分别是文件描述符id，想要监听的事件和触发的事件。内核接收到数据后，修改对应pollfd的revents，标识该文件描述符的事件已到达。

- poll  VS  select

poll传入内核的不是位图，而是pollfd这个结构体的数组，解决了select位图1024的大小限制；

poll由于每次置位的是pollfd的revents，文件描述符fd是不变的，因此省去了select每次构造位图的开销，poll针对pollfd的revents属性的恢复可以放在获取触发事件的for循环里执行。

和select相同，poll仍存在将数组从用户空间传递给内核空间的开销，而且poll函数返回后，需要O(n)遍历一次来检测哪些文件描述符的数据已准备就绪。

3. epoll函数

![image-20201125165545969](http://rocks526.top/lzx/image-20201125165545969.png)

epoll的整体流程和上面两个类似，区别在于select利用位图描述要监听的文件描述符id和监听的事件，poll用pollfd数组描述，而epoll函数定义了新的结构体epfd，通过链表或红黑树存储里面的文件描述符id和想要监听的事件events。(与poll不同，epoll没有revents)

与select和poll不同，epoll不需要将epfd从用户空间传入内核空间，而是用户空间和内核空间共享这块数据，因此解决了从用户空间到内核空间的开销。

同时，epoll函数在检测到数据准备完成时，会将epfd重排，将数据准备完成的文件描述符排到前面，并返回数据准备完成的文件描述符的个数，因此解决了调用方需要遍历一遍O(n)才能获取准备好数据的文件描述符id集合的开销。

> epoll的两种触发模式：
> 		LT:水平触发
> 			当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，进程可以不立即处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式，并且同时支持 Blocking 和 No-Blocking。
> 		ET:边缘触发
> 			和 LT 模式不同的是，通知之后进程必须立即处理事件。
> 			下次再调用 epoll_wait() 时不会再得到事件到达的通知。很大程度上减少了 epoll 事件被重复触发的次数，
> 			因此效率要比 LT 模式高。只支持 No-Blocking，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。

#### 信号驱动IO

![image-20201107152608677](http://rocks526.top/lzx/image-20201107152608677.png)

首先开启套接口信号驱动IO功能，通过系统调用sigation执行一个信号处理函数（此信号调用直接返回，进程继续工作）。当数据准备就绪时，生成一个signal信号，通知应用程序来取数据。

> 此种IO模型的本质还是同步非阻塞IO，优点在于，不需要进程不断的进行系统调用，检查数据是否准备好，而是当数据准备好之后，系统会向进程发送信号，进程再发起系统调用完成数据的拷贝，第二步的数据拷贝依旧是阻塞的。

#### 异步IO

![image-20201107152838314](http://rocks526.top/lzx/image-20201107152838314.png)

告知内核启动某个操作，并让内核在整个操作完成之后（**将数据从内核复制到进程自己的缓冲区**）进行通知。

用户进程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从kernel的角度，当它受到一个asynchronous read之后，首先它会立刻返回，所以不会对用户进程产生任何block。然后，kernel会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，kernel会给用户进程发送一个signal，告诉它read操作完成了。 在这整个过程中，进程完全没有被block。

和信号驱动模型的主要区别是：信号驱动IO由内核通知进程数据到达了，让进程发起一个系统调用，将数据从内核拷贝到进程，拷贝的整个过程是阻塞的，所以是同步的。异步IO模型由内核通知进程IO何时已经完成，进程不需要再发起一次系统调用，所以是异步的。

> 在应用层，实现异步调用，一般都是通过发送系统调用时就带上回调函数实现。

#### 总结

![image-20201107153333007](http://rocks526.top/lzx/image-20201107153333007.png)

### <a id="reactor">Reactor</a>





### <a id="proactor">Proactor</a>



# <a id="io">Java中的IO</a>

- Java中的基础IO

Java中的基础IO分为字符流和字节流两大类：

![image-20201102174333487](http://rocks526.top/lzx/image-20201102174333487.png)

![image-20201102175524339](http://rocks526.top/lzx/image-20201102175524339.png)

> https://www.cnblogs.com/ylspace/p/8128112.html
>
> https://blog.csdn.net/qq_42191317/article/details/95937492

---------------------------------------------

- BIO：同步阻塞IO，JDK1.4之前
- NIO：多路复用IO，JDK1.4之后
- AIO：异步IO，JDK1.7之后

### <a id="bio">BIO</a>

同步阻塞IO，Java中早期唯一的IO，调用方法会陷入阻塞状态，同步返回结果。

Socket编程：

- 服务端bind自己的IP和端口
- accept阻塞等待客户端连接
- 客户端connect服务端
- 服务端的accept方法解除阻塞，与客户端通信，同步方式
- 此时新的客户端无法连接服务端，只有等待服务端再次accept才能连接服务端
- 优化：通过多线程优化，服务端通过主线程监听客户端连接，通过子线程处理客户端交互
- 不足：当客户端连接非常多时，服务端需要的线程数就非常大，不能无限制创建线程，因此服务端的并发量取决于线程池中的线程数量

----------------------

**单线程BIO模型**

Server代码：

```java
package demo.bio;

import lombok.extern.slf4j.Slf4j;

import java.io.*;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * Server 单线程 同时只能处理一个客户端请求
 */
@Slf4j
public class SocketServer {

    private static final String ServerIP = "127.0.0.1";

    private static final Integer ServerPort = 8888;

    private static String ServerStopCommand = "SHUTDOWN";

    private static String ClientStopCommand = "QUIT";

    public static void main(String[] args) {

        ServerSocket serverSocket = null;
        Socket clientSocket = null;
        try {
//            serverSocket = new ServerSocket(8888);
            serverSocket = new ServerSocket();
            serverSocket.bind(new InetSocketAddress(ServerIP,ServerPort));
            log.info("Server [{}:{}] init success...",ServerIP,ServerPort);
            Boolean serverRunningFlag = true;
            Boolean clientRunningFlag = true;
            //进入循环 一直接收客户端请求 直到客户端发送ServerStopCommand
            while (serverRunningFlag){
                clientSocket = serverSocket.accept();
                //连接到一个客户端 处理与该客户端的交互 直到该客户端发送ClientStopCommand
                log.info("Client [{}:{}] connected Server...",clientSocket.getInetAddress(),clientSocket.getPort());
                while (clientRunningFlag){
                    try{
                        BufferedReader reader = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(clientSocket.getOutputStream()));
                        String msg = reader.readLine();
                        log.info("Client [{}] sendMsg:{}",clientSocket.getInetAddress() + ":" +clientSocket.getPort(),msg);
                        writer.write(msg + "[Server]" + "\n");
                        writer.flush();
                        if (ServerStopCommand.equals(msg)){
                            serverRunningFlag = false;
                            break;
                        }
                        if (ClientStopCommand.equals(msg)){
                            clientRunningFlag = false;
                            break;
                        }
                    }catch (Exception e){
                        clientRunningFlag = false;
                        break;
                    }
                }
                log.info("Client [{}:{}] disconnected Server...",clientSocket.getInetAddress(),clientSocket.getPort());
                clientRunningFlag = true;
            }
            log.info("Server [{}:{}] is closed...",ServerIP,ServerPort);
        } catch (IOException e) {
            log.error("Server [{}] running error,Reason:{}",ServerIP+":"+ServerPort,e.getMessage());
            e.printStackTrace();
        }finally {
            if (clientSocket != null){
                try {
                    clientSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (serverSocket != null){
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

}
```

**Client代码**

```java
package demo.bio;

import lombok.extern.slf4j.Slf4j;

import java.io.*;
import java.net.InetSocketAddress;
import java.net.Socket;

/**
 * Client
 */
@Slf4j
public class SocketClient {

    private static final String ServerIP = "127.0.0.1";

    private static final Integer ServerPort = 8888;

    private static String ServerStopCommand = "SHUTDOWN";

    private static String ClientStopCommand = "QUIT";


    public static void main(String[] args) {

        Socket socket = null;
        try {
            socket = new Socket();
            socket.connect(new InetSocketAddress(ServerIP,ServerPort));
            log.info("Client [{}:{}] init success...",socket.getLocalAddress(),socket.getLocalPort());
            while (true){
                BufferedReader consoleReader = new BufferedReader(new InputStreamReader(System.in));
                BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                String msg = consoleReader.readLine();
                bufferedWriter.write(msg + "\n");
                bufferedWriter.flush();
                System.out.println(bufferedReader.readLine());
                if (msg.equals(ClientStopCommand) || msg.equals(ServerStopCommand)){
                    break;
                }
            }
        } catch (IOException e) {
            log.error("Client [{}] running error,Reason:{}",socket.getInetAddress()+":"+socket.getPort(),e.getMessage());
            e.printStackTrace();
        }finally {
            try {
                if (socket != null){
                    socket.close();
                }
                log.info("Client [{}:{}] closed success...",socket.getInetAddress(),socket.getPort());
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }

}
```

- 存在问题

![image-20201009162305442](http://rocks526.top/lzx/image-20201009162305442.png)

启动一个Server，两个Client，同时只有一个Client连接到Server，另一个Client会一直阻塞，直到第一个Client断开连接，Server重新accept。

---------------------

**多线程BIO模型**

Server代码：

```java
package demo.bio;

import lombok.extern.slf4j.Slf4j;

import java.io.*;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * Server 多线程 主线程接收客户端请求 子线程处理客户端逻辑
 *          有多少客户端连接即创建多少个线程 服务端资源消耗很大
 */
@Slf4j
public class SocketServer2 {

    private static final String ServerIP = "127.0.0.1";

    private static final Integer ServerPort = 8888;

    private static String ServerStopCommand = "SHUTDOWN";

    private static String ClientStopCommand = "QUIT";

    private static Boolean serverRunningFlag = true;

    public static void main(String[] args) {
        SocketServer2 socketServer2 = new SocketServer2();
        socketServer2.start();
    }


    public void start(){
        ServerSocket serverSocket = null;
        Socket clientSocket = null;
        try {
//            serverSocket = new ServerSocket(8888);
            serverSocket = new ServerSocket();
            serverSocket.bind(new InetSocketAddress(ServerIP,ServerPort));
            log.info("Server [{}:{}] init success...",ServerIP,ServerPort);
            //进入循环 一直接收客户端请求 直到客户端发送ServerStopCommand
            while (serverRunningFlag){
                clientSocket = serverSocket.accept();
                //连接到一个客户端 处理与该客户端的交互 直到该客户端发送ClientStopCommand
                log.info("Client [{}:{}] connected Server...",clientSocket.getInetAddress(),clientSocket.getPort());
                new Thread(new ClientHandler(clientSocket)).start();
            }
            log.info("Server [{}:{}] is closed...",ServerIP,ServerPort);
        } catch (IOException e) {
            log.error("Server [{}] running error,Reason:{}",ServerIP+":"+ServerPort,e.getMessage());
            e.printStackTrace();
        }finally {
            if (clientSocket != null){
                try {
                    clientSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (serverSocket != null){
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }


    //客户端请求处理器
    class ClientHandler implements Runnable{

        private Socket clientSocket = null;

        private Boolean clientRunningFlag = true;

        public ClientHandler(Socket client){
            this.clientSocket = client;
        }

        public void run() {
            while (clientRunningFlag){
                try{
                    BufferedReader reader = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                    BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(clientSocket.getOutputStream()));
                    String msg = reader.readLine();
                    log.info("Client [{}] sendMsg:{}",clientSocket.getInetAddress() + ":" +clientSocket.getPort(),msg);
                    writer.write(msg + "[Server]" + "\n");
                    writer.flush();
                    if (ServerStopCommand.equals(msg)){
                        serverRunningFlag = false;
                        break;
                    }
                    if (ClientStopCommand.equals(msg)){
                        clientRunningFlag = false;
                        break;
                    }
                }catch (Exception e){
                    clientRunningFlag = false;
                    break;
                }
            }
            log.info("Client [{}:{}] disconnected Server...",clientSocket.getInetAddress(),clientSocket.getPort());
        }
    }

}
```

> Client代码与之前保持一致。

- 存在问题

Server会对每个Client创建一个子线程去处理，同时有多少个Client连接，Server就会创建多少个线程。

Server创建大量线程会占用Server很多资源，导致Server能力下降。

![image-20201009165025205](http://rocks526.top/lzx/image-20201009165025205.png)

------------------

**线程池BIO模型**

Server代码：

```java
package demo.bio;

import lombok.extern.slf4j.Slf4j;

import java.io.*;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Server 线程池 为了解决每个客户端一个线程 线程开销太大的问题 服务端采用线程池
 *          
 */
@Slf4j
public class SocketServer3 {

    private static final String ServerIP = "127.0.0.1";

    private static final Integer ServerPort = 8888;

    private static String ServerStopCommand = "SHUTDOWN";

    private static String ClientStopCommand = "QUIT";

    private static Boolean serverRunningFlag = true;

    private static ExecutorService executorService = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        SocketServer3 socketServer3 = new SocketServer3();
        socketServer3.start();
    }


    public void start(){
        ServerSocket serverSocket = null;
        Socket clientSocket = null;
        try {
//            serverSocket = new ServerSocket(8888);
            serverSocket = new ServerSocket();
            serverSocket.bind(new InetSocketAddress(ServerIP,ServerPort));
            log.info("Server [{}:{}] init success...",ServerIP,ServerPort);
            //进入循环 一直接收客户端请求 直到客户端发送ServerStopCommand
            while (serverRunningFlag){
                clientSocket = serverSocket.accept();
                //连接到一个客户端 处理与该客户端的交互 直到该客户端发送ClientStopCommand
                log.info("Client [{}:{}] connected Server...",clientSocket.getInetAddress(),clientSocket.getPort());
                executorService.execute(new ClientHandler(clientSocket));
            }
            log.info("Server [{}:{}] is closed...",ServerIP,ServerPort);
        } catch (IOException e) {
            log.error("Server [{}] running error,Reason:{}",ServerIP+":"+ServerPort,e.getMessage());
            e.printStackTrace();
        }finally {
            if (clientSocket != null){
                try {
                    clientSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (serverSocket != null){
                try {
                    serverSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }


    //客户端请求处理器
    class ClientHandler implements Runnable{

        private Socket clientSocket = null;

        private Boolean clientRunningFlag = true;

        public ClientHandler(Socket client){
            this.clientSocket = client;
        }

        public void run() {
            while (clientRunningFlag){
                try{
                    BufferedReader reader = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                    BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(clientSocket.getOutputStream()));
                    String msg = reader.readLine();
                    log.info("Client [{}] sendMsg:{}",clientSocket.getInetAddress() + ":" +clientSocket.getPort(),msg);
                    writer.write(msg + "[Server]" + "\n");
                    writer.flush();
                    if (ServerStopCommand.equals(msg)){
                        serverRunningFlag = false;
                        break;
                    }
                    if (ClientStopCommand.equals(msg)){
                        clientRunningFlag = false;
                        break;
                    }
                }catch (Exception e){
                    clientRunningFlag = false;
                    break;
                }
            }
            log.info("Client [{}:{}] disconnected Server...",clientSocket.getInetAddress(),clientSocket.getPort());
        }
    }

}
```

通过线程池解决了之前随着客户端并发量增大导致的服务端无限创建线程的问题，但此时服务端的并发能力受限于线程池的数量。

### <a id="nio">NIO</a>

> BIO编程中存在两个地方的阻塞：
>
> - ServerSocket.accept()
> - InputStream.read()和OutputStream.write()
>
> ServerSocket.accept()的阻塞可以通过子线程实现非阻塞，主线程不断accept，将连接交给子线程执行即可。
>
> Stream的阻塞会导致一个线程只能处理一个客户端连接，即便该客户没有输入，线程依旧会阻塞，浪费线程资源。
>
> 为了解决这个问题，Jdk1.4之后出现了NIO，引入了Channel作为数据交换的组件，Channel可以设置阻塞或非阻塞。通过Channel替代Stream，服务端可以在一个线程里处理多个客户端连接，程序并发量不再受限于线程数量。
>
> 与Stream的输入输出不同，Channel是没有方向的，数据的交互依靠Buffer缓冲区完成，可以通过设置一个标志位来实现读取和写入的反转。
>
> 引入Channel可以实现单线程处理多个客户端IO，通过Selector类实现对批量Channel的管理，只需要将Channel注册到Selector上即可，调用Selector的select方法，会一直阻塞，直到有事件被触发。
>
> 该方法底层根据平台不同采用不同的实现，如select/poll/epoll等。

- Channel特点
  - 可选阻塞或非阻塞
  - 无方向
  - transferTo函数：两个通道之间直接传输文件
- 常见Channel
  - FileChannel
  - ServerSocketChannel
  - SocketChannel
- Channel状态
    - CONNECT
    - ACCEPT
    - READ
    - WRITE
- Channel操作

```java
//服务端SocketChannel创建
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
//服务端绑定端口
serverSocketChannel.bind(new InetSocketAddress(8888));
//服务端监听客户端连接 建立SocketChannel
SocketChannel socketChannel = serverSocketChannel.accept();
//客户端连接远程主机
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1",8000));
```


---------------------------------

- Buffer特点
  - 一块内存区域
  - 用来进行Channel数据操作
- Buffer属性
  - capacity：最大可写入的位置
  - position：当前位置
  - limit：最大可读取位置，刚开始等于capacity
  - mark：标记
  - allocate函数：初始化，分配固定长度的缓冲区
  - flip函数：模式翻转，从写到读，具体逻辑是将position归0，limit设置为之前的position位置
  - clear函数：模式翻转，从读到写，具体逻辑是将position归0，limit设置为capacity，所有数据不管是否读取完毕，全部清除
  - compact函数：模式翻转，从读到写，具体逻辑是将position到limit之间的未读数据拷贝到缓冲区的头部部分，然后将position设置到写入位置的下一个位置，limit设置为capacity，只会清除已读取的数据，未读数据下次可以接着读
  - hasRemaining函数：返回buffer是否还有未读数据

- Buffer操作

```java
        //初始化
        ByteBuffer byteBuffer = ByteBuffer.allocate(10);
```

![image-20201014170024252](http://rocks526.top/lzx/image-20201014170024252.png)

```java
        //写入abc三个字符 UTF-8编码
        byteBuffer.put("abc".getBytes(Charset.forName("UTF-8")));
```

![image-20201014170706440](http://rocks526.top/lzx/image-20201014170706440.png)

```java
        //从写模式切换为读模式
        byteBuffer.flip();
```

![image-20201014170929943](http://rocks526.top/lzx/image-20201014170929943.png)

```java
        //从ByteBuffer中读出一个字节  (第一个)
        System.out.println(byteBuffer.get());
```

![image-20201014171003026](http://rocks526.top/lzx/image-20201014171003026.png)

```java
        //调用mark方法记录当前position位置
        byteBuffer.mark();
```

![image-20201014171025057](http://rocks526.top/lzx/image-20201014171025057.png)

```java
        //调用get读取下一个字节 (第二个)
        System.out.println(byteBuffer.get());
        //调用reset将position重置到之前的mark 即第一个
        byteBuffer.reset();
```

![image-20201014171050160](http://rocks526.top/lzx/image-20201014171050160.png)

```java
        //调用clear将所有数据清空 重新回到写模式
        byteBuffer.clear();
```

![image-20201014171131836](http://rocks526.top/lzx/image-20201014171131836.png)

> JDK提供很多Buffer的实现，基本类型都有对应的Buffer缓冲区，使用最多的是ByteBuffer

-------------------------------

- Selector介绍
  - 选择器，多路复用器，NIO的核心，底层通过select,poll,epoll实现select方法
  - 监视注册在Selector上的多个Channel

- Selector操作

```java
        //创建Selector
        Selector selector = Selector.open();
        //将Channel注册到Selector上，监听就读事件
        serverSocketChannel.register(selector, SelectionKey.OP_READ);
        //阻塞等待selector上的所有channel事件就绪 返回就绪的Channel个数
        int selectedNumber = selector.select();
        //获取发生就绪事件的Channel集合
        Set<SelectionKey> selectionKeys = selector.selectedKeys();
```

- NIO编程步骤
  - 创建Selector
  - 创建ServerSocketChannel并绑定监听端口
  - 将ServerSocketChannel设置非阻塞模式
  - 将ServerSocketChannel注册到Selector上并监听连接事件
  - 循环调用Selector的select方法，检测事件就绪情况
  - 调用Selector的selectedKeys方法获取就绪的Channel集合
  - 判断就绪事件是何种类型，调用对应的处理方法
  - 根据业务决定是否继续注册监听事件，重复执行第三步

- Jdk中的NIO存在的问题
  - NIO类库繁琐，API复杂
  - 可靠性问题
    - 客户端的断连和重连
    - 网络闪断
    - 半包读写
    - 失败缓存
    - 网络阻塞
    - 异常码流
  - 空轮询
    - selector的select在linux下的epoll会出现空轮训，Jdk6修复，实际Jdk8仍然存在，概率低

-------------------------------

- NIO服务端代码

```java
package demo.nio;


import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.nio.charset.Charset;
import java.util.Set;

/**
 * 服务端
 */
@Slf4j
public class ServerSocket {


    public static void main(String[] args) throws IOException {

        ServerSocket serverSocket = new ServerSocket();
        serverSocket.start();
    }

    private void start() throws IOException {
        //创建Selector
        Selector selector = Selector.open();
        //创建ServerSocketChannel
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        //绑定端口
        serverSocketChannel.bind(new InetSocketAddress(Constant.ServerPort));
        //设置非阻塞
        serverSocketChannel.configureBlocking(false);
        //将Channel注册到Selector上，监听客户端连接事件
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        while (true){
            //阻塞等待selector上的所有channel事件就绪 返回就绪的Channel个数
            Integer selectedNumber = selector.select();

            //正常来说 只有事件触发才会返回 Jdk存在bug 可能空轮训
            if (selectedNumber.equals(0)){
                log.info("Server Selector returned by : {}",selectedNumber);
            }

            //获取发生就绪事件的Channel集合
            Set<SelectionKey> selectionKeys = selector.selectedKeys();

            for (SelectionKey selectionKey : selectionKeys){

                //客户端连接事件
                if (selectionKey.isAcceptable()){
                    ClientConnectedHandler(selectionKey,selector);
                }

                //客户端发送消息事件
                if (selectionKey.isReadable()){
                    ClientSendMsgHandler(selectionKey,selector);
                }

                //移除本次的响应事件 => 也可以不移除 留给下次处理
                selectionKeys.remove(selectionKey);
            }

        }
    }

    private void ClientSendMsgHandler(SelectionKey selectionKey, Selector selector) throws IOException {
        //获取客户端连接
        SocketChannel channel = (SocketChannel) selectionKey.channel();
        //读取客户端发送的消息 向客户端发送响应 原样回复 加Server后缀
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        StringBuilder msg = new StringBuilder();
        while (channel.read(byteBuffer) > 0){
            //切换为读
            byteBuffer.flip();
            //数据拼接
            msg.append(Charset.forName("UTF-8").decode(byteBuffer));
        }
        msg.append("[from Server]");
        channel.write(Charset.forName("UTF-8").encode(msg.toString()));
        //注册可读事件
        channel.register(selector,SelectionKey.OP_READ);
    }


    private void ClientConnectedHandler(SelectionKey selectionKey, Selector selector) throws IOException {
        //获取客户端连接 设置非阻塞
        ServerSocketChannel serverSocketChannel = (ServerSocketChannel) selectionKey.channel();
        SocketChannel channel = serverSocketChannel.accept();
        channel.configureBlocking(false);
        //向客户端发送响应
        String msg = "Server[" + Constant.ServerPort + "] connected success...";
        channel.write(Charset.forName("UTF-8").encode(msg));
        //注册可读事件
        channel.register(selector,SelectionKey.OP_READ);
    }

}
```

- NIO客户端代码

```java
package demo.nio;

import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.nio.charset.Charset;
import java.util.Set;

/**
 * 客户端
 */
@Slf4j
public class ClientSocket {

    private Boolean clientRunningFlag = true;

    private void start() throws IOException {
        //创建SocketChannel
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress(Constant.ServerIP,Constant.ServerPort));
        //设置非阻塞
        socketChannel.configureBlocking(false);
        //监听服务端响应事件
        Selector selector = Selector.open();
        socketChannel.register(selector, SelectionKey.OP_READ);
        while (clientRunningFlag){

            //阻塞等待响应
            Integer selectedNum = selector.select();

            if (selectedNum.equals(0)){
                log.info("Client Selector returned by : {}",selectedNum);
            }

            //获取响应事件
            Set<SelectionKey> selectionKeys = selector.selectedKeys();

            for (SelectionKey selectionKey : selectionKeys){

                //可读事件
                if (selectionKey.isReadable()){
                    ReciveServerMsgHandler(selectionKey,selector);
                }

                //删除处理过的事件
                selectionKeys.remove(selectionKey);

            }

            //向服务器发送数据
            BufferedReader consoleReader = new BufferedReader(new InputStreamReader(System.in));
            String msg = consoleReader.readLine();
            socketChannel.write(Charset.forName("UTF-8").encode(msg));

        }

    }

    private void ReciveServerMsgHandler(SelectionKey selectionKey, Selector selector) throws IOException {
        SocketChannel channel = (SocketChannel) selectionKey.channel();
        //读取服务端响应
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        StringBuilder msg = new StringBuilder();
        while (channel.read(byteBuffer) > 0){
            //切换为读
            byteBuffer.flip();
            //数据拼接
            msg.append(Charset.forName("UTF-8").decode(byteBuffer));
        }
        System.out.println(msg);
        if (Constant.ClientStopCommand.equals(msg.toString())){
            clientRunningFlag = false;
        }
        //注册可读事件
        channel.register(selector,SelectionKey.OP_READ);
    }

    public static void main(String[] args) throws IOException {

        ClientSocket clientSocket = new ClientSocket();
        clientSocket.start();

    }


}
```

### <a id="aio">AIO</a>

> 在Jdk1.4引入NIO之后，在Jdk又引入了AIO，进一步提升IO性能。
>
> 由于异步依赖操作系统层面的实现，因此不同的操作系统直接性能差异很大。
>
> https://blog.csdn.net/weixin_43430036/article/details/83747398

AIO主要通过以下两个类：

-  AsynchronousSocketChannel  ==>  客户端
-  AsynchronousServerSocketChannel  ==>  服务端

和之前的API类似，客户端connect，服务端accept，还有其他的read/write操作，这些操作全部都是异步的。

AIO通过以下两种方式实现异步:

- Future ==>  代表对异步操作结果的抽象，里面提供检查异步是否完成，是否异常，获取结果等方法
- CompletionHandler  ==>  回调函数，里面包括异步执行成功和执行失败之后的处理方法

操作系统回调时，CompletionHandler怎么执行？

- AsynchronousServerSocketChannel其实会绑定一个AsynchronousChannelGroup，类似于一个线程池，如果初始化时不指定，会使用一个默认的AsyncChannelGroup

----------------------------------

**Server**

```java
package demo.aio;

import com.sun.media.jfxmediaimpl.HostUtils;
import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousServerSocketChannel;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.channels.CompletionHandler;
import java.util.HashMap;

@Slf4j
public class AsyncServer {

    public static void main(String[] args) {
        AsyncServer asyncServer = new AsyncServer();
        asyncServer.start();
    }

    // Server
    private AsynchronousServerSocketChannel serverSocketChannel;

    private void start(){
        try {
            // 使用默认的ChannelGroup
            serverSocketChannel = AsynchronousServerSocketChannel.open();
            // 绑定IP 端口
            serverSocketChannel.bind(new InetSocketAddress(Constant.ServerIP, Constant.ServerPort));

            while (true){
                // 开始客户端监听  两个参数分别是回调函数和传递给回调函数的数据attachment
                serverSocketChannel.accept(null , new AcceptHandler());
                // 等待系统输入  ==>  阻塞线程
                int read = System.in.read();
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    // 客户端连接回调函数  泛型两个参数分别为 accept函数返回值类型和attachment类型
    private class AcceptHandler implements CompletionHandler<AsynchronousSocketChannel,Object> {

        @Override
        public void completed(AsynchronousSocketChannel clientChannel, Object attachment) {
            // 继续等待客户端连接
            if (serverSocketChannel.isOpen()){
                serverSocketChannel.accept(null, this);
            }
            // 读取客户端数据
            if (clientChannel != null && clientChannel.isOpen()){
                ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                HashMap<String, Object> info = new HashMap<>();
                info.put("type", "read");
                info.put("byteBuffer", byteBuffer);
                clientChannel.read(byteBuffer, info, new ClientHandler(clientChannel));
            }
        }

        @Override
        public void failed(Throwable exc, Object attachment) {
            log.error("接收客户端连接失败,Reason:{}", exc.getMessage());
        }


        // 客户端读写操作回调函数
        private class ClientHandler implements CompletionHandler<Integer, HashMap<String, Object>> {

            private AsynchronousSocketChannel clientChannel;

            public ClientHandler(AsynchronousSocketChannel clientChannel){
                this.clientChannel = clientChannel;
            }

            @Override
            public void completed(Integer result, HashMap<String, Object> attachment) {
                ByteBuffer buffer = (ByteBuffer) attachment.get("byteBuffer");
                String type = (String) attachment.get("type");
                // 读取或写入的长度
                if (result != null){
                    if (result < 0){
                        // 客户端异常
                        log.error("客户端异常:客户端" + type + "长度小于0!");
                    }else {
                        if ("read".equals(type)){
                            System.out.println("客户端发送数据:" + new String(buffer.array(), 0, result));
                            buffer.flip();
                            attachment.put("type","write");
                            clientChannel.write(buffer, attachment, this);
                            buffer.clear();
                        }else if ("write".equals(type)){
                            ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                            attachment.put("type","read");
                            attachment.put("byteBuffer",byteBuffer);
                            clientChannel.read(byteBuffer, attachment, this);
                        }else {
                            System.out.println("未知客户端操作....");
                        }
                    }
                }
            }

            @Override
            public void failed(Throwable exc, HashMap<String, Object> attachment) {
                String type = (String) attachment.get("type");
                if ("read".equals(type)){
                    log.error("读取客户端发送数据失败,Reason:{}", exc.getMessage());
                }else if ("write".equals(type)){
                    log.error("往客户端写入数据失败,Reason:{}", exc.getMessage());
                }else {
                    log.error("未知客户端操作失败失败,Reason:{}", exc.getMessage());
                }
            }
        }
    }
}
```

**Clinet**

```java
package demo.aio;

import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.BufferedInputStream;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.charset.Charset;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

@Slf4j
public class AsyncClient {

    public static void main(String[] args) throws InterruptedException, ExecutionException, IOException {
        new AsyncClient().start();

    }

    private AsynchronousSocketChannel clientChannel;

    private void start() throws IOException, ExecutionException, InterruptedException {
        clientChannel = AsynchronousSocketChannel.open();
        Future<Void> future = clientChannel.connect(new InetSocketAddress(Constant.ServerIP, Constant.ServerPort));
        // 阻塞等待返回结果
        future.get();
        // 接收用户输入
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        while (true) {
            String readLine = bufferedReader.readLine();

            // 发送服务端
            Future<Integer> write = clientChannel.write(Charset.forName("UTF-8").encode(readLine));
            write.get();

            // 获取服务端响应
            ByteBuffer buffer = ByteBuffer.allocate(1024);
//            buffer.flip();
            Future<Integer> read = clientChannel.read(buffer);
            Integer size = read.get();
            byte[] array = buffer.array();
            System.out.println(new String(array, 0, size));
        }
    }

}
```

# <a id="lts">聊天室实战</a>

### 项目需求

实现一个简单的多人聊天室

- 客户端
  - 接收用户键盘输入，将消息发送到服务端
  - 接收服务端转发来的别的用户发送的消息
- 服务端
  - 接收客户端连接，维持客户端状态
  - 接收客户端发送来的数据，将数据转发给其他客户端
- 实现思路
  - 服务端accept客户端请求，交给线程池处理
  - 客户端主线程进入循环接收服务端发送的数据，子线程接收用户输入

### BIO

- 服务端

```java
package chat.bio;

import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.BufferedWriter;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.io.Writer;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 多人聊天室服务端
 *      1. 主线程接收客户端请求 保存客户端连接
 *      2. 子线程处理客户端逻辑 将消息给其他客户端转发
 */
@Slf4j
public class ChatServer {


    //客户端连接
    private Map<Integer, Writer> clients = new ConcurrentHashMap<Integer, Writer>();

    //线程池
    private ExecutorService executorService = Executors.newCachedThreadPool();

    //服务端Socket
    private ServerSocket serverSocket = null;

    //启动程序
    private void start() {

        //创建服务端Socket
        try {
            serverSocket = new ServerSocket(Constant.ServerPort);
            log.info("Server [{}:{}] init success...",serverSocket.getInetAddress(),serverSocket.getLocalPort());
            while (true){
                //监听客户端连接
                Socket client = serverSocket.accept();
//                log.info("Client [{}:{}] connected Server...",client.getInetAddress(),client.getPort());
                executorService.execute(new ClientSocketHandler(client,this));
            }
        } catch (IOException e) {
            log.error("Server[{}] running error , Reason:{}",Constant.ServerPort,e.getMessage());
            e.printStackTrace();
        }finally {
            close();
        }
    }

    //关闭连接
    private void close() {
        if (serverSocket != null){
            try {
                serverSocket.close();
                log.info("Server[{}] closed ... ", Constant.ServerPort);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    //添加客户端连接
    public void addClientSocket(Socket client) throws IOException {
        if (client != null){
            Integer port = client.getPort();
            BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(client.getOutputStream()));
            clients.put(port, bufferedWriter);
            log.info("Client[{}] connected Server...",port);
        }
    }

    //移除客户端连接
    public void removeClientSocket(Socket client) throws IOException {
        if (client != null && clients.containsKey(client.getPort())){
            clients.get(client.getPort()).close();
            clients.remove(client.getPort());
            log.info("Client[{}] disConnected Server...",client.getPort());
        }
    }

    //转发客户端请求
    public void forwardMsg(Socket client,String msg) throws IOException {
        if (client != null){
            for (Integer port : clients.keySet()){
                if (!port.equals(client.getPort())){
                    Writer writer = clients.get(port);
                    writer.write(msg + "\n");
                    writer.flush();
                }
            }
        }
    }

    public static void main(String[] args) {
        ChatServer chatServer = new ChatServer();
        chatServer.start();
    }

}
```

```java
package chat.bio;

import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.Socket;

/**
 * 客户端连接处理器
 */
@Slf4j
public class ClientSocketHandler implements Runnable {

    private Socket clientSocket;

    private ChatServer chatServer;

    private Boolean clientRunningFlag = true;

    public ClientSocketHandler(Socket client, ChatServer chatServer) {
        this.clientSocket = client;
        this.chatServer = chatServer;
    }

    public void run() {
        try {
            //存储新上线的用户
            chatServer.addClientSocket(clientSocket);
            while (clientRunningFlag){
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                String msg = bufferedReader.readLine();
                String msgToSend = "Client[" + clientSocket.getPort() + "] sendMsg:" + msg;
                log.info(msgToSend);
                chatServer.forwardMsg(clientSocket,msgToSend);
                //检查用户是否退出
                if (Constant.ClientStopCommand.equals(msg)){
                    clientRunningFlag = false;
                }
            }
        } catch (IOException e) {
            log.error("Client[{}] running error, Reason:{}",clientSocket.getLocalPort(),e.getMessage());
            e.printStackTrace();
        } finally {
            try {
                chatServer.removeClientSocket(clientSocket);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }


}
```

- 客户端

```java
package chat.bio;

import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.*;
import java.net.InetSocketAddress;
import java.net.Socket;

/**
 * 多人聊天室客户端
 *      1. 主线程不断监听服务端转发来的消息
 *      2. 子线程接收用户输入
 */
@Slf4j
public class ChatClient {

    private Socket clientSocket = null;

    private BufferedReader reader = null;

    private BufferedWriter writer = null;

    private Boolean ClientRunningFlag = true;

    //向服务端发送消息
    public void sendMsgToServer(String msg) throws IOException {
        if (!clientSocket.isOutputShutdown()){
            writer.write(msg + "\n");
            writer.flush();
        }
    }

    //接收服务端的消息
    public String revicedMsgFromServer() throws IOException {
        String msg = null;
        if (!clientSocket.isInputShutdown()){
            msg = reader.readLine();
        }
        return msg;
    }

    //客户端连接关闭
    private void close() {

        if (clientSocket != null){
            try {
                clientSocket.close();
                log.info("Client closed ...");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    //更新ClientRunningFlag
    public void updateFlag(){
        ClientRunningFlag = false;
    }

    //启动程序
    public void start(){
        try {
            //创建客户端连接
            clientSocket = new Socket();
            clientSocket.connect(new InetSocketAddress(Constant.ServerIP,Constant.ServerPort));
            log.info("Server [{}:{}] init success...",clientSocket.getLocalAddress(),clientSocket.getLocalPort());

            //创建IO
            writer = new BufferedWriter(new OutputStreamWriter(clientSocket.getOutputStream()));
            reader = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));

            //处理用户输入
            new Thread(new UserInputHandler(this)).start();

            //读取服务端转发信息
            while (ClientRunningFlag){
                String msg = revicedMsgFromServer();
                log.info(msg);
            }

        } catch (IOException e) {
            log.error("Client Running error, Reason:{}",e.getMessage());
            e.printStackTrace();
        } finally {
          close();
        }
    }

    public static void main(String[] args) {
        ChatClient chatClient = new ChatClient();
        chatClient.start();
    }

}
```

```java
package chat.bio;

import util.Constant;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class UserInputHandler implements Runnable {

    private ChatClient chatClient;

    private BufferedReader reader;

    public UserInputHandler(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    public void run() {
        //接收用户输入 发送服务端 如果用户输入结束标识 则更新ClientRunningFlag
        reader = new BufferedReader(new InputStreamReader(System.in));
        while (true){
            try {
                String msg = reader.readLine();
                chatClient.sendMsgToServer(msg);
                if (Constant.ClientStopCommand.equals(msg)){
                    chatClient.updateFlag();
                    break;
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### NIO

- 服务端

```java
package chat.nio;

import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.nio.charset.Charset;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;

/**
 * NIO聊天室服务端  单线程
 *      1.监听客户端连接，将客户端连接保存，注册可读事件
 *      2.监听客户端发送消息，将消息转发给其他客户端
 */
@Slf4j
public class ChatServer {

    public static void main(String[] args) throws IOException {

        ChatServer chatServer = new ChatServer();
        chatServer.start();

    }

    private ServerSocketChannel serverSocketChannel = null;

    private Selector selector = null;

    private ByteBuffer readBuffer = ByteBuffer.allocate(1024);

    private void start() throws IOException {
        //获取一个Selector
        selector = Selector.open();
        //创建服务端Channel
        serverSocketChannel = ServerSocketChannel.open();
        //绑定IP端口
        serverSocketChannel.bind(new InetSocketAddress(Constant.ServerIP,Constant.ServerPort));
        //设置非阻塞
        serverSocketChannel.configureBlocking(false);
        //注册连接事件
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        //进入循环
        while(true){
            try {
                //等待事件响应
                Integer selectedNumber = selector.select();

                //正常来说 只有事件触发才会返回 Jdk存在bug 可能空轮训
                if (selectedNumber.equals(0)){
                    log.info("Server Selector returned by : {}",selectedNumber);
                }

                //获取发生就绪事件的Channel集合
                Set<SelectionKey> selectionKeys = selector.selectedKeys();

                for (SelectionKey selectionKey : selectionKeys) {

                    //移除本次的响应事件 => 也可以不移除 留给下次处理
                    selectionKeys.remove(selectionKey);

                    //客户端连接事件
                    if (selectionKey.isAcceptable()) {
                        ClientConnectedHandler(selectionKey, selector);
                    }

                    //客户端发送消息事件
                    if (selectionKey.isReadable()) {
                        ClientSendMsgHandler(selectionKey, selector);
                    }

                }
            }catch (Exception e) {
                log.error("Server Running error, Reason:{}",e.getMessage());
                e.printStackTrace();
            }finally {
//                close();
            }
        }
    }


    private void close()  {
        try{
            if (selector != null){
                selector.close();
            }
            if (serverSocketChannel != null){
                serverSocketChannel.close();
            }
            if (readBuffer != null){
                readBuffer.clear();
            }
        }catch (Exception e){
            log.error("Server Close error, Reason:{}",e.getMessage());
        }
    }

    private void ClientSendMsgHandler(SelectionKey selectionKey, Selector selector) throws IOException {
        //将消息转发给其他客户端
        SocketChannel socketChannel = (SocketChannel)selectionKey.channel();
        readBuffer.clear();
        StringBuilder msg = new StringBuilder();
        try{
            while (socketChannel.read(readBuffer) > 0);
        }catch (Exception e){
            //客户端强行关闭会导致selector不断返回读就绪事件
            socketChannel.close();
        }
        readBuffer.flip();
        msg.append(Charset.forName("UTF-8").decode(readBuffer));
        Boolean isClientStop = msg.toString().equals(Constant.ClientStopCommand) || msg.toString().equals("");
        String endPrefix = " [From Client " + socketChannel.socket().getPort() + "]";
        msg.append(endPrefix);
        for (SelectionKey selectionKeyItem : selector.keys()){
            if (selectionKeyItem.channel() instanceof ServerSocketChannel){
                continue;
            }
            SocketChannel otherSocketChannel = (SocketChannel)selectionKeyItem.channel();
            if (selectionKeyItem.isValid() && !otherSocketChannel.equals(socketChannel)){
                otherSocketChannel.write(Charset.forName("UTF-8").encode(msg.toString()));
            }
            //如果客户端结束 消除此客户端selector
            if (isClientStop){
                selectionKey.cancel();
                selector.wakeup();
            }
        }

        //重新注册可读事件
        socketChannel.register(selector, SelectionKey.OP_READ);
    }


    private void ClientConnectedHandler(SelectionKey selectionKey, Selector selector) throws IOException {
        //将客户端连接加入map
        ServerSocketChannel serverSocketChannel = (ServerSocketChannel)selectionKey.channel();
        SocketChannel socketChannel = serverSocketChannel.accept();
        //返回客户端连接成功响应
        socketChannel.write(Charset.forName("UTF-8").encode("Connected Server[" + Constant.ServerPort + "] Success.."));
        //注册可读事件
        socketChannel.configureBlocking(false);
        socketChannel.register(selector,SelectionKey.OP_READ);
    }

}
```

- 客户端

```java
package chat.nio;

import chat.bio.UserInputHandler;
import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.nio.charset.Charset;
import java.util.Set;

/**
 * 多人聊天室客户端
 *      1.接收用户输入，发送到服务端(由于用户输入是阻塞式的 因此采用子线程处理)
 *      2.接收服务端响应，打印信息
 */
@Slf4j
public class ChatClinet {

    private SocketChannel socketChannel = null;

    private Selector selector = null;

    private Boolean isClientRunning = Boolean.TRUE;

    private ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

    public static void main(String[] args) {

        ChatClinet chatClinet = new ChatClinet();
        chatClinet.start();
    }

    private void start(){

        try {
            selector = Selector.open();
            socketChannel = SocketChannel.open();
            socketChannel.configureBlocking(false);
            socketChannel.connect(new InetSocketAddress(Constant.ServerIP,Constant.ServerPort));
            socketChannel.register(selector, SelectionKey.OP_CONNECT);

            while (isClientRunning){

                Integer selectedNum = selector.select();

                //正常来说 只有事件触发才会返回 Jdk存在bug 可能空轮训
                if (selectedNum.equals(0)){
                    log.info("Server Selector returned by : {}",selectedNum);
                }

                Set<SelectionKey> selectionKeys = selector.selectedKeys();

                for (SelectionKey selectionKey : selectionKeys){

                    if (selectionKey.isConnectable()){
                        ConnectedServerHandler(selector,selectionKey);
                    }

                    if (selectionKey.isReadable()){
                        RecivedMsgFromServerHandler(selector,selectionKey);
                    }

                    selectionKeys.remove(selectionKey);

                }

            }
        }catch (Exception e){
            log.error("Client Running error,Reason:{}", e.getMessage());
        }finally {
            close();
        }
    }

    private void ConnectedServerHandler(Selector selector,SelectionKey selectionKey) throws IOException {
        SocketChannel socketChannel = (SocketChannel)selectionKey.channel();
        if (socketChannel.isConnectionPending()){
            socketChannel.finishConnect();
            new Thread(new NioUserInputHandler(this)).start();
        }
        socketChannel.register(selector, SelectionKey.OP_READ);
    }


    private void RecivedMsgFromServerHandler(Selector selector, SelectionKey selectionKey) throws IOException {
        SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
        byteBuffer.clear();
        while (socketChannel.read(byteBuffer) > 0);
        byteBuffer.flip();
        System.out.println(Charset.forName("UTF-8").decode(byteBuffer));
        socketChannel.register(selector, SelectionKey.OP_READ);
    }

    private void close() {
        try {
            if (selector != null){
                selector.close();
            }
            if (socketChannel != null){
                socketChannel.close();
            }
        }catch (Exception e){
            log.error("Client Close error, Reason:{}",e.getMessage());
        }
    }

    public void ClientExit(){
        this.isClientRunning = false;
        selector.wakeup();
    }

    public void sendMsgToServer(String msg) throws IOException {
        socketChannel.write(Charset.forName("UTF-8").encode(msg));
    }

}
```

```java
package chat.nio;

import lombok.SneakyThrows;
import util.Constant;

import java.io.BufferedReader;
import java.io.InputStreamReader;

public class NioUserInputHandler implements Runnable {

    private ChatClinet chatClinet;

    public NioUserInputHandler(ChatClinet chatClinet) {
        this.chatClinet = chatClinet;
    }

    @SneakyThrows
    @Override
    public void run() {

        while (true){
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
            String userInputMsg = bufferedReader.readLine();
            this.chatClinet.sendMsgToServer(userInputMsg);
            if (Constant.ClientStopCommand.equals(userInputMsg)){
                this.chatClinet.ClientExit();
                break;
            }
        }

    }
}
```

### AIO

- 服务端

```java
package chat.aio;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousServerSocketChannel;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.channels.CompletionHandler;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

@Slf4j
public class AsyncServer {

    public static void main(String[] args) {
        AsyncServer asyncServer = new AsyncServer();
        asyncServer.start();
    }

    // Server
    private AsynchronousServerSocketChannel serverSocketChannel;

    private List<ClientHandler> clients;

    public AsyncServer(){
        clients = new ArrayList<>();
    }

    private void start(){
        try {
            // 使用默认的ChannelGroup
            serverSocketChannel = AsynchronousServerSocketChannel.open();
            // 绑定IP 端口
            serverSocketChannel.bind(new InetSocketAddress(Constant.ServerIP, Constant.ServerPort));

            log.info("服务端启动成功...端口:[{}]", Constant.ServerPort);

            while (true){
                // 开始客户端监听  两个参数分别是回调函数和传递给回调函数的数据attachment
                serverSocketChannel.accept(null , new AcceptHandler());
                // 等待系统输入  ==>  阻塞线程
                int read = System.in.read();
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    // 客户端连接回调函数  泛型两个参数分别为 accept函数返回值类型和attachment类型
    private class AcceptHandler implements CompletionHandler<AsynchronousSocketChannel,Object> {

        @SneakyThrows
        @Override
        public void completed(AsynchronousSocketChannel clientChannel, Object attachment) {

            log.info("客户端[{}]连接成功!", clientChannel.getRemoteAddress());

            // 继续等待客户端连接
            if (serverSocketChannel.isOpen()){
                serverSocketChannel.accept(null, this);
            }
            // 读取客户端数据
            if (clientChannel != null && clientChannel.isOpen()){
                ByteBuffer byteBuffer = ByteBuffer.allocate(10240);
                HashMap<String, Object> info = new HashMap<>();
                info.put("type", "read");
                info.put("byteBuffer", byteBuffer);
                ClientHandler clientHandler = new ClientHandler(clientChannel);
                // 添加客户端到clients
                clients.add(clientHandler);
                clientChannel.read(byteBuffer, info, clientHandler);
            }
        }

        @Override
        public void failed(Throwable exc, Object attachment) {
            log.error("接收客户端连接失败,Reason:{}", exc.getMessage());
        }

    }

    // 客户端读写操作回调函数
    private class ClientHandler implements CompletionHandler<Integer, HashMap<String, Object>> {

        private AsynchronousSocketChannel clientChannel;

        public ClientHandler(AsynchronousSocketChannel clientChannel){
            this.clientChannel = clientChannel;
        }

        public void sendMsg(String msg, HashMap<String, Object> attachment) throws ExecutionException, InterruptedException {
//            clientChannel.write(Charset.forName("UTF-8").encode(msg), attachment, this);
            // 异步回调式会导致java.nio.channels.ReadPendingException
            Future<Integer> future = clientChannel.write(Charset.forName("UTF-8").encode(msg));
            future.get();
        }

        @SneakyThrows
        @Override
        public void completed(Integer result, HashMap<String, Object> attachment) {
            ByteBuffer buffer = (ByteBuffer) attachment.get("byteBuffer");
            String type = (String) attachment.get("type");
            // 读取或写入的长度
            if (result != null){
                if (result < 0){
                    // 客户端异常
                    log.error("客户端异常:客户端" + type + "长度小于0!");
                }else {
                    if ("read".equals(type)){
                        String msg = new String(buffer.array(), 0, result);
                        log.info("客户端[{}]发送数据:{}",clientChannel.getRemoteAddress(), msg);
                        buffer.flip();
                        attachment.put("type","write");
                        attachment.put("is_forward",true);
                        // 数据转发给其他客户端
                        for (ClientHandler clientHandler : clients){
                            if (clientHandler == this){
                                continue;
                            }
                            clientHandler.sendMsg("客户端[" + clientChannel.getRemoteAddress() + "]发送消息:[" + msg + "]!", attachment);
                        }
                        buffer.clear();
                    }else if ("write".equals(type)){
                        ByteBuffer byteBuffer = ByteBuffer.allocate(10240);
                        attachment.put("type","read");
                        attachment.put("byteBuffer",byteBuffer);
                        clientChannel.read(byteBuffer, attachment, this);
                    }else {
                        log.info("未知客户端操作....");
                    }
                }
            }
        }

        @Override
        public void failed(Throwable exc, HashMap<String, Object> attachment) {
            String type = (String) attachment.get("type");
            clients.remove(this);
            if ("read".equals(type)){
                log.error("读取客户端发送数据失败,Reason:{}", exc.getMessage());
            }else if ("write".equals(type)){
                log.error("往客户端写入数据失败,Reason:{}", exc.getMessage());
            }else {
                log.error("未知客户端操作失败失败,Reason:{}", exc.getMessage());
            }
        }
    }
}
```

- 客户端

```java
package chat.aio;

import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import util.Constant;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.charset.Charset;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

@Slf4j
public class AsyncClient {

    public static void main(String[] args) throws InterruptedException, ExecutionException, IOException {
        new AsyncClient().start();

    }

    private AsynchronousSocketChannel clientChannel;

    private void start() throws IOException, ExecutionException, InterruptedException {
        clientChannel = AsynchronousSocketChannel.open();
        Future<Void> future = clientChannel.connect(new InetSocketAddress(Constant.ServerIP, Constant.ServerPort));
        // 阻塞等待返回结果
        future.get();
        // 接收用户输入
        new Thread(new Runnable() {
            @SneakyThrows
            @Override
            public void run() {
                while (true){
                    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
                    String readLine = bufferedReader.readLine();
                    // 发送服务端
                    Future<Integer> write = clientChannel.write(Charset.forName("UTF-8").encode(readLine));
                    write.get();
                }
            }
        }).start();
        while (true) {
            // 获取服务端响应
            ByteBuffer buffer = ByteBuffer.allocate(1024);
//            buffer.flip();
            Future<Integer> read = clientChannel.read(buffer);
            Integer size = read.get();
            byte[] array = buffer.array();
            System.out.println(new String(array, 0, size));
        }
    }

}
```

### 性能测试

























































































