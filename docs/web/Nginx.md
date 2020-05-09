- [Nginx简介](#js)
- [Nginx安装](#az)
- [Nginx原理](#yl)
- [Nginx核心配置](#pz)
- [Nginx实战](#sz)
- [OpenResty](#op)

# <a id="js">Nginx简介</a>

### Nginx介绍

Nginx是一个轻量级，高性能，基于HTTP的反向代理服务器/静态Web服务器。

Nginx最初是由俄罗斯人Igor Sysoev(伊戈尔·塞索耶夫)使用C语言开发的一款Web服务器，2004年10月发布第一个版本。

Nginx官网：http://nginx.org/

### Nginx特点

- 高并发

一个Nginx服务器在不做任何配置的情况下并发量可达到1000左右，在硬件允许的前提下，Nginx可以支持高达5-10万并发量。

>  Java的运行时容器Tomcat，不做任何配置的默认并发量是150。

- 低消耗

官网给出的测试结果，10000个非活跃连接，在Nginx中仅消耗2.5M内存。

- 热部署

可以在7*24小时不间断服务的前提下，进行Nginx版本的升级，Nginx配置文件的修改。

> 通过多个worker进程分批切换实现

- 高可用

Nginx服务器具有很多worker工作进程，当某个出问题时可以被其余worker进程替换继续工作。

- 高扩展

Nginx的模块化使得Nginx的扩展性非常强，Nginx有很多扩展模块，可以安装自己需要的模块，也可以自己编写扩展模块实现自己需要的功能。

> 扩展模块一般由C语言或者Lua脚本编写
>
> OpenResty就是一个基于Nginx并添加许多扩展模块的框架
>
> 官网：http://openresty.org/cn/

# <a id="az">Nginx安装</a>

- 下载Nginx安装包

> http://nginx.org/en/download.html，目前最新版本1.17.9

- 安装C/C++环境

> yum install gcc gcc-c++

- 安装依赖库

> yum install -y pcre-devel openssl-devel

- 解压压缩包

> tar -xzvf nginx-1.17.9.tar.gz 

- 进入解压后的目录，使用configure命令生成编译所需的Makefile文件

> cd nginx-1.17.9
>
> ./configure

- 编译并安装

> make && make install

- 启动Nginx

> Nginx的安装目录在/usr/local/nginx
>
> /usr/local/nginxs/sbin/nginx 启动Nginx

- 访问Nginx

> http://localhost:80/

![image-20200414162618495](C:\Users\lizhaoxuan\AppData\Roaming\Typora\typora-user-images\image-20200414162618495.png)

- 常用Nginx命令

> - nginx -h 查看Nginx命令选项
>
> - nginx -v 查看Nginx版本信息
>
> - nginx -t 测试Nginx配置文件是否正确，默认只测试conf/nginx.conf
>
> - nginx -T 测试Nginx配置文件是否正确，并显示配置文件信息
>
> - nginx -tq 测试Nginx配置文件是否正确，只显示错误信息
>
> - nginx -c 测试指定的Nginx配置文件
>
> - nginx -c configfile 启动Nginx，不带参数默认使用conf/nginx.conf配置文件
>
> - nginx -s stop 强制停止Nginx
>
> - nginx -s quit 优雅停止Nginx
>
> - nginx -s reload 平滑重启Nginx
>
> - nginx -s reopen 重新打开日志文件
>
> - nginx -p 指定Nginx配置文件存放路径

# <a id="yl">Nginx原理</a>













# <a id="pz">Nginx核心配置</a>









# <a id="sz">Nginx实战</a>







