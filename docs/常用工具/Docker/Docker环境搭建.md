# Docker的核心概念

> Docker是一个CS架构程序，服务端有一个Deamon线程，客户端可以通过命令行和本地服务端或远程服务端通信。

![img](http://rocks526.top/lzx/u=3857054039,2046077413&fm=26&gp=0.jpg)

- 镜像

镜像是构建Docker的基石。用户基于镜像来运行自己的容器。

可以将镜像当作容器的“源代码”。镜像体积很小，非常“便携”，易于分享、存储和更新。

- 容器

类似于一个虚拟机，提供与本机隔离的运行环境。

- 注册中心

Docker用Registry来保存用户构建的镜像。Registry分为公共和私有两种。

Docker公司运营公共的Registry叫做Docker Hub。用户可以在Docker Hub注册账号，分享并保存自己的镜像。

私有的注册中心类似于GitLab或者自己的Maven私服

> 国内Docker仓库：https://docker.mirrors.ustc.edu.cn

# Docker安装

### Windows安装Docker

- 下载地址：https://www.docker.com/
- 下载Windows的Docker安装程序安装即可

> Windows提供了Docker的可视化管理面板，第一次需要构建一个容器才能顺利进入，可以通过引导或者cmd构建
>
> 在Settings里可以设置Docker占用内存，DockerHub等

### Linux安装Docker

- 安装Docker需要的依赖包

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

- 设置yum的源

```shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

- 安装Docker

```shell
sudo yum install docker-ce
```

- Linux启动Docker后台进程

```shell
systemctl start docker
```

- 检查Docker版本

```shell
docker -v
```

- 测试Docker-HelloWorld

```shell
# docker run hello-world 
Unable to find image 'hello-world:latest' locally
docker run hello-worldlatest: Pulling from library/hello-world
0e03bdcc26d7: Already exists
Digest: sha256:6a65f928fb91fcfbc963f7aa6d57c8eeb426ad9a20c7ee045538ef34847f44f1
Status: Downloaded newer image for hello-world:latest

Hello from Docker!             --> 出现这句话代表Docker安装没有问题
This message shows that your installation appears to be working correctly.
```

- 卸载Docker

```shell
#1. 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
#2. 删除资源 /var/lib/docker 是docker的默认工作路径！
rm -rf /var/lib/docker
```

- 常用Docker命令

```shell
Docker -v  # 查看Docker版本
Docker --help  # 查看帮助文档
Docker inspect  # 查看具体信息
```

