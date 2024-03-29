# 一：Docker介绍

### 1.1 Docker是什么

Docker 是一个开源项目，诞生于 2013 年初，最初是 dotCloud 公司内部的一个业余项目。它基于 Google 公司推出的 Go 语言实现。 项目后来加入了 Linux 基金会，遵从了 Apache 2.0 协议，项目代码在 [GitHub](https://github.com/docker/docker) 上进行维护。

![image-20200907173628246](http://rocks526.top/lzx/image-20200907173628246.png)

Docker 自开源后受到广泛的关注和讨论，以至于 dotCloud 公司后来都改名为 Docker Inc。Redhat 已经在其 RHEL6.5 中集中支持 Docker；Google 也在其 PaaS 产品中广泛应用。

Docker 项目的目标是实现轻量级的操作系统虚拟化解决方案。 Docker 的基础是 Linux 容器（LXC）等技术。

在 LXC 的基础上 Docker 进行了进一步的封装，让用户不需要去关心容器的管理，使得操作更为简便。用户操作 Docker 的容器就像操作一个快速轻量级的虚拟机一样简单。

### 1.2 Docker的特点

- 上手快

用户只需要几分钟，就可以把自己的程序“Docker化”。随后，就可以创建容器来运行应用程序了。大多数Docker容器只需要不到1秒中即可启动。由于去除了管理程序的开销，Docker容器拥有很高的性能，同时同一台宿主机中也可以运行更多的容器，使用户尽可能的充分利用系统资源。

Docker依赖于“写时复制”（copy-on-write）模型，使修改应用程序也非常迅速，可以说达到“随心所致，代码即改”的境界。	

- 职责的逻辑分类

使用Docker，开发人员只需要关心容器中运行的应用程序，而运维人员只需要关心如何管理容器。`Docker设计的目的就是要加强开发人员写代码的开发环境与应用程序要部署的生产环境一致性。`从而降低那种“开发时一切正常，肯定是运维的问题（测试环境都是正常的，上线后出了问题就归结为肯定是运维的问题）”

- 快速高效的开发生命周期

Docker的目标之一就是缩短代码从开发、测试到部署、上线运行的周期，让你的应用程序具备可移植性，易于构建，并易于协作。（通俗一点说，Docker就像一个盒子，里面可以装很多物件，如果需要这些物件的可以直接将该大盒子拿走，而不需要从该盒子中一件件的取。）

- 鼓励使用面向服务的架构

Docker还鼓励面向服务的体系结构和微服务架构。Docker推荐单个容器只运行一个应用程序或进程，这样就形成了一个分布式的应用程序模型，在这种模型下，应用程序或者服务都可以表示为一系列内部互联的容器，从而使分布式部署应用程序，扩展或调试应用程序都变得非常简单，同时也提高了程序的内省性。（当然，可以在一个容器中运行多个应用程序）

### 1.3 Docker和虚拟机的区别

作为一种轻量级的虚拟化方式，Docker在运行应用上跟传统的虚拟机方式相比具有显著优势：

- Docker容器很快，启动和停止可以在秒级实现，这相比传统的虚拟机方式要快得多。
- Docker容器对系统资源需求很少，一台主机上可以同时运行数千个Docker容器。
- Docker通过类似Git的操作来方便用户获取、分发和更新应用镜像，指令简明，学习成本较低。
- Docker通过Dockerfile配置文件来支持灵活的自动化创建和部署机制，提高工作效率。

Docker容器除了运行其中的应用之外，基本不消耗额外的系统资源，保证应用性能的同时，尽量减小系统开销。传统虚拟机方式运行N个不同的应用就要启动N个虚拟机（每个虚拟机需要单独分配独占的内存、磁盘等资源），而Docker只需要启动N个隔离的容器，并将应用放到容器内即可。

![image-20210105100349857](http://rocks526.top/lzx/image-20210105100349857.png)

### 1.4 Docker的核心概念

Docker是一个CS架构程序，服务端有一个Deamon线程，客户端可以通过命令行和本地服务端或远程服务端通信。

![img](http://rocks526.top/lzx/u=3857054039,2046077413&fm=26&gp=0.jpg)

- 镜像

镜像是构建Docker的基石。用户基于镜像来运行自己的容器。

可以将镜像当作容器的“源代码”。镜像体积很小，非常“便携”，易于分享、存储和更新。

- 容器

类似于一个虚拟机，提供与本机隔离的运行环境。

- 注册中心

Docker用Registry来保存用户构建的镜像。Registry分为公共和私有两种。

Docker公司运营公共的Registry叫做Docker Hub；用户可以在Docker Hub注册账号，分享并保存自己的镜像；私有的注册中心类似于GitLab或者自己的Maven私服。

注：国内Docker仓库https://docker.mirrors.ustc.edu.cn。

### 1.5 Docker的安装

#### 1.5.1 Windows安装Docker

- 下载地址：https://www.docker.com/
- 下载Windows的Docker安装程序安装即可

Windows提供了Docker的可视化管理面板，第一次需要构建一个容器才能顺利进入，可以通过引导或者cmd构建；在Settings里可以设置Docker占用内存，DockerHub等；

#### 1.5.2 Linux安装Docker

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
[root@VM-12-2-centos ~]# docker run hello-world 
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:10d7d58d5ebd2a652f4d93fdd86da8f265f5318c6a73cc5b6a9798ff6d2b2e67
Status: Downloaded newer image for hello-world:latest

Hello from Docker!		# 出现这句话代表Docker安装没有问题
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
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

# 二：Docker镜像

### 2.1 镜像简介

Docker的镜像类似于VMware的iso文件，里面包含了系统和应用相关的信息。

想要启动一个Docker容器，需要先从镜像仓库服务中拉取镜像。常见的镜像仓库服务是Docker Hub，但是也存在其他镜像仓库服务。拉取操作会将镜像下载到本地Docker主机，可以使用该镜像启动一个或者多个容器。

`镜像由多个层组成，每层叠加之后，从外部看来就如一个独立的对象`。镜像内部是一个精简的操作系统（OS），同时还包含应用运行所必须的文件和依赖包。因为容器的设计初衷就是`快速和小巧`，所以镜像通常都比较小。

### 2.2 镜像的分层存储

Docker镜像是一个特殊的文件系统，除了提供容器运行时所需的`程序、库、资源、配置`等文件外，还包含了一些为运行时准备的一些`配置参数`(如匿名卷、环境变量、用户等)。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

因为镜像包含系统完整的root文件系统，其体积往往是庞大的，因此在Docker设计时，就充分利用`联合文件（Union FS）技术`，将其设计为`分层存储的架构`。所以严格来说，镜像并非是像一个ISO那样的打包文件，镜像只是一个虚拟的概念，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说由多层系统联合组成。

镜像构建时会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层，比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。

分层存储的特征还使得`镜像的复用、定制变的更为容易`。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需要的内容，构建新的镜像。分层结构如下图所示：

![image-20201207115614514](http://rocks526.top/lzx/image-20201207115614514.png)

### 2.3 镜像的常用命令

- 查看镜像

```shell
[root@VM-12-2-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
```

REPOSITORY：镜像名称

TAG：镜像标签

IMAGE ID：镜像ID

CREATED：镜像的创建日期（不是获取该镜像的日期）

SIZE：镜像大小

- 搜索镜像

```shell
[root@VM-12-2-centos ~]# docker search mysql
NAME                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                            MySQL is a widely used, open-source relation…   12475     [OK]       
mariadb                          MariaDB Server is a high performing open sou…   4804      [OK]       
mysql/mysql-server               Optimized MySQL Server Docker images. Create…   923                  [OK]
percona                          Percona Server is a fork of the MySQL relati…   575       [OK]       
phpmyadmin                       phpMyAdmin - A web interface for MySQL and M…   514       [OK]       
```

NAME：仓库名称/镜像名称

DESCRIPTION：镜像描述

STARS：用户评价，反应一个镜像的受欢迎程度

OFFICIAL：是否官方

AUTOMATED：自动构建，表示该镜像由Docker Hub自动构建流程创建的

- 拉取镜像

```shell
# 语法 ==> docker pull 镜像名称:镜像标签              
[root@VM-12-2-centos ~]# docker pull centos:7
7: Pulling from library/centos
2d473b07cdd5: Pull complete 
Digest: sha256:c73f515d06b0fa07bb18d8202035e739a494ce760aa73129f60f4bf2bd22b407
Status: Downloaded newer image for centos:7
docker.io/library/centos:7
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
centos        7         eeb6ee3f44bd   7 months ago   204MB
```

- 删除镜像

```shell
# 语法 ==> docker rmi 镜像ID或镜像名称:镜像标签
[root@VM-12-2-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
centos        7         eeb6ee3f44bd   7 months ago   204MB
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker rmi centos:7
Untagged: centos:7
Untagged: centos@sha256:c73f515d06b0fa07bb18d8202035e739a494ce760aa73129f60f4bf2bd22b407
Deleted: sha256:eeb6ee3f44bd0b5103bb561b4c16bcb82328cfe5809ab675bb17ab3a16c517c9
Deleted: sha256:174f5685490326fc0a1c0f5570b8663732189b327007e47ff13d2ca59673db02
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
[root@VM-12-2-centos ~]# 
```

当镜像被某个容器使用时，无法删除，需要先删除容器或者通过-f参数强制删除。

```shell
[root@VM-12-2-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker rmi hello-world:latest
Error response from daemon: conflict: unable to remove repository reference "hello-world:latest" (must force) - container 15d0e5d059b0 is using its referenced image feb5d9fea6a5
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker rmi hello-world:latest -f
Untagged: hello-world:latest
Untagged: hello-world@sha256:10d7d58d5ebd2a652f4d93fdd86da8f265f5318c6a73cc5b6a9798ff6d2b2e67
Deleted: sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

通过 docker rmi \`docker images -aq` 命令可以删除所有镜像；

通过 docker images -q 命令可以返回所有镜像ID；

- 查看镜像详细信息

```shell
# 语法 ==> docker image inspect 镜像ID或镜像名称:镜像标签
[root@VM-12-2-centos ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
redis        latest    3c3da61c4be0   6 days ago   113MB
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker image inspect 3c3da61c4be0
[
    {
        "Id": "sha256:3c3da61c4be0fb9e93fc84fb702b6a1e01d8c43ffae4d2ea88192803c2b44191",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:b7fd1a2c89d09a836f659d72c52d27b9f71202c97014a47639f87c992e8c0f1b"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2022-04-20T13:29:53.41875044Z",
        "Container": "74d1d1144c27943f70a2f656edd944489d305515d02c0896f88e939d222849d6",
        "ContainerConfig": {
            "Hostname": "74d1d1144c27",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.14",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:fa6fb6c397c7a4acd229a5c8252793a25dc853df9a2e8af04ac9507bfb9d9d24",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "20.10.12",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.14",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:fa6fb6c397c7a4acd229a5c8252793a25dc853df9a2e8af04ac9507bfb9d9d24",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 112618313,
        "VirtualSize": 112618313,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/bcb140b24e48571d1716cf7455f74716aa9c2ef39329f1975433796988606291/diff:/var/lib/docker/overlay2/f810e7a00b5e9584e413a4aabf56e1ea82f54b09033883ce9ef5acd7507ea11c/diff:/var/lib/docker/overlay2/4d76a98e23eb15a6ea542d686d2bb272b90a95516b7090a7af7586cd0fbe5e02/diff:/var/lib/docker/overlay2/c92a7d00cac742b27296b558503dd88818a2ae27ca6aba2c29333e5a9a65e10e/diff:/var/lib/docker/overlay2/916cb6212e047d064f42a19f74a4bd220ef86cd5947c5fbf4eb8ab570ada01f1/diff",
                "MergedDir": "/var/lib/docker/overlay2/d5c519498896b16c6f1449679cf5b5e64a330144b46a947059d239b8e99bf9c0/merged",
                "UpperDir": "/var/lib/docker/overlay2/d5c519498896b16c6f1449679cf5b5e64a330144b46a947059d239b8e99bf9c0/diff",
                "WorkDir": "/var/lib/docker/overlay2/d5c519498896b16c6f1449679cf5b5e64a330144b46a947059d239b8e99bf9c0/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:9c1b6dd6c1e6be9fdd2b1987783824670d3b0dd7ae8ad6f57dc3cea5739ac71e",
                "sha256:d65e371da0453ddb98c6be22dcc90e7a6eb875e2132210657dde8322cb257dc6",
                "sha256:f0228b605b4af7181c89c268d333a0d41d815fe30dda1e7f481f02dee3642d27",
                "sha256:b959234ba79a363e2ebbdb652b33de94df87c24ba63d78c34b8c0823930cd94c",
                "sha256:cfcecd8b0c43b02f466e77a62b0e507df795d752f32fafb37c9a88e00e9d1934",
                "sha256:65d8db3af33580e3274c7dbea66651b51e2d74dce04c3a7ad8292b039275eed3"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

- 容器导出镜像

```shell
# 语法 ==> docker commit 容器名字 导出的镜像名
[root@VM-12-2-centos ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
c2898aef53b8   hello-world   "/hello"   5 seconds ago    Exited (0) 5 seconds ago              nervous_carver
0547bb703cf7   hello-world   "/hello"   6 minutes ago    Exited (0) 6 minutes ago              affectionate_noyce
15d0e5d059b0   hello-world   "/hello"   12 minutes ago   Exited (0) 12 minutes ago             funny_chatelet
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker commit nervous_carver hello-world-x
sha256:5aa5faee729609ce33055a4aef36bf9c4e9ceecdafa05d1bcec2ec8ef1ebf762
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
hello-world-x   latest    5aa5faee7296   7 seconds ago   13.3kB	# 新导出的镜像
redis           latest    3c3da61c4be0   6 days ago      113MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
```

注：容器不需要启动也可以导出镜像。

- 镜像备份

```shell
# 语法 ==> docker save -o 保存的文件名 镜像名
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
hello-world-x   latest    5aa5faee7296   52 seconds ago   13.3kB
redis           latest    3c3da61c4be0   6 days ago       113MB
hello-world     latest    feb5d9fea6a5   7 months ago     13.3kB
[root@VM-12-2-centos ~]# docker save -o hello-world-x.tar hello-world-x
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# ll
total 24
-rw------- 1 root root 23552 Apr 27 16:29 hello-world-x.tar
```

- 镜像恢复

```shell
# 语法 ==> docker load -i 镜像文件的名字
[root@VM-12-2-centos ~]# docker rmi hello-world-x
Untagged: hello-world-x:latest
Deleted: sha256:5aa5faee729609ce33055a4aef36bf9c4e9ceecdafa05d1bcec2ec8ef1ebf762
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
redis         latest    3c3da61c4be0   6 days ago     113MB
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# pwd
/root
[root@VM-12-2-centos ~]# docker load -i /root/hello-world-x.tar
Loaded image: hello-world-x:latest
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
hello-world-x   latest    5aa5faee7296   3 minutes ago   13.3kB
redis           latest    3c3da61c4be0   6 days ago      113MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
```

注：通过导出和导入的方式，可以将镜像拷贝入内网环境。

# 三：Docker容器

### 3.1 容器的介绍

容器是Docker的另一个核心概念。简单地说，`容器是镜像的一个运行实例，所不同的是，它带有额外的可写文件层`。

如果认为虚拟机是模拟运行的一整套操作系统（提供了运行态环境和其他系统环境）和跑在上面的应用。那么Docker容器就是独立运行的一个或一组应用，以及它们的必需运行环境。

### 3.2 容器的常用命令

- 查看容器

```shell
# 查看正在运行的容器：docker ps
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
01a409de6d51   redis     "docker-entrypoint.s…"   3 seconds ago   Up 2 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   myredis
# 查看所有容器：docker ps -a
[root@VM-12-2-centos ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                      PORTS                                       NAMES
01a409de6d51   redis         "docker-entrypoint.s…"   23 seconds ago   Up 22 seconds               0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   myredis
c2898aef53b8   hello-world   "/hello"                 12 minutes ago   Exited (0) 12 minutes ago                                               nervous_carver
0547bb703cf7   hello-world   "/hello"                 18 minutes ago   Exited (0) 18 minutes ago                                               affectionate_noyce
15d0e5d059b0   hello-world   "/hello"                 25 minutes ago   Exited (0) 25 minutes ago                                               funny_chatelet
# 查看最近运行的容器：docker ps -l
[root@VM-12-2-centos ~]# docker ps -l
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
01a409de6d51   redis     "docker-entrypoint.s…"   41 seconds ago   Up 40 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   myredis
# 查看停止的容器：docker ps -f status=exited
[root@VM-12-2-centos ~]# docker ps -f status=exited
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
c2898aef53b8   hello-world   "/hello"   13 minutes ago   Exited (0) 13 minutes ago             nervous_carver
0547bb703cf7   hello-world   "/hello"   19 minutes ago   Exited (0) 19 minutes ago             affectionate_noyce
15d0e5d059b0   hello-world   "/hello"   26 minutes ago   Exited (0) 26 minutes ago             funny_chatelet
```

- 创建容器

以交互方式运行容器： 

docker run -i -t --name 容器名称 repository:tag /bin/bash 

docker run -it --name 容器名称 imageID /bin/bash 

以守护进程方式运行容器： 

docker run -di --name 容器名称 repository:tag 

docker run -di --name 容器名称 imageID 

```shell
# 语法：docker run，常用参数：-i：运行容器；-t：交互式进入容器；-d：后台启动容器；--name：为创建的容器命名；-v：目录映射，可以做多个目录映射；-p：端口映射；
[root@VM-12-2-centos ~]# docker run -ti --name=x-redis -p 6379:6379 redis
1:C 27 Apr 2022 08:48:15.708 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 27 Apr 2022 08:48:15.708 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 27 Apr 2022 08:48:15.708 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 27 Apr 2022 08:48:15.708 * monotonic clock: POSIX clock_gettime
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 6.2.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                  
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 1
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           https://redis.io       
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

1:M 27 Apr 2022 08:48:15.709 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:M 27 Apr 2022 08:48:15.709 # Server initialized
1:M 27 Apr 2022 08:48:15.709 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1:M 27 Apr 2022 08:48:15.709 * Ready to accept connections
```

注意：通过 run 创建并进入容器之后，如果使用 exit 命令退出容器，则容器停止； 再次进入该容器，先使用 start 启动容器，再使用 exec/attach 命令进入容器。

- 进入正在运行的容器

docker exec -it 容器名称或者容器 ID /bin/bash 

docker attach 容器名称或者容器 ID

```shell
# 语法 ==> docker exec -it 容器ID或容器名称:容器标签/容器ID /bin/bash
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
57db961806e4   redis     "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   x-redis
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker exec -it 57db961806e4 /bin/bash
root@57db961806e4:/data# exit # 退出容器
```

注意：attach 进入容器之后，如果使用 exit 退出容器，则容器停止； exec 进入容器之后，使用 exit 退出容器，容器依然处于运行状态。

- 停止容器

```shell
# 语法 ==> docker stop 容器名称:容器标签/容器ID
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
45feacfe2f48   redis     "docker-entrypoint.s…"   40 seconds ago   Up 40 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   x-redis-2
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker stop 45feacfe2f48
45feacfe2f48
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

- 启动容器

```shell
# 语法 ==> docker start 容器名称:容器标签/容器ID
[root@VM-12-2-centos ~]# docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS                      PORTS     NAMES
45feacfe2f48   redis     "docker-entrypoint.s…"   About a minute ago   Exited (0) 32 seconds ago             x-redis-2
57db961806e4   redis     "docker-entrypoint.s…"   5 minutes ago        Exited (0) 2 minutes ago              x-redis
[root@VM-12-2-centos ~]# docker start 57db961806e4
57db961806e4
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
57db961806e4   redis     "docker-entrypoint.s…"   5 minutes ago   Up 2 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   x-redis
```

- 删除容器

```shell
# 语法：docker rm 容器ID/容器名
# 删除所有容器 ==> docker rm -f $(docker ps -aq)  
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
57db961806e4   redis     "docker-entrypoint.s…"   17 minutes ago   Up 12 minutes   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   x-redis
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker rm 57db961806e4		# 删除之前需要先停止容器
Error response from daemon: You cannot remove a running container 57db961806e42e1dd7ef495f80431b450f20d8483438c79aa5f8014f717d27c2. Stop the container before attempting removal or force remove
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker stop 57db961806e4
57db961806e4
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker rm 57db961806e4
57db961806e4
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

- 文件拷贝

```shell
# 语法：docker cp 源文件 目标文件
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
c550aebed004   redis     "docker-entrypoint.s…"   4 seconds ago   Up 4 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   x-redis-3
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker exec -it c550aebed004 /bin/bash
root@c550aebed004:/data# 
root@c550aebed004:/data# cd /opt/
root@c550aebed004:/opt# ls
root@c550aebed004:/opt# exit
exit
[root@VM-12-2-centos ~]# docker cp /root/hello-world-x.tar c550aebed004:/opt/
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker exec -it c550aebed004 /bin/bash
root@c550aebed004:/data# cd /opt/
root@c550aebed004:/opt# ls
hello-world-x.tar
root@c550aebed004:/opt# 
```

注意：源文件可以是宿主机器也可以是容器中的文件，同样，目标文件可以是容器也可以是宿主机器的文件。

- 查看容器信息

```shell
# 语法：docker inspect 容器ID/容器名称
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS       PORTS                                       NAMES
c550aebed004   redis     "docker-entrypoint.s…"   3 hours ago   Up 3 hours   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   x-redis-3
[root@VM-12-2-centos ~]# docker inspect c550aebed004
[
    {
        "Id": "c550aebed004534f53d5048f74edd2c61dab7a52b742510bbb4467cf3bb69b82",
        "Created": "2022-04-27T09:09:26.317650133Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "redis-server"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1985,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-04-27T09:09:26.61468093Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:3c3da61c4be0fb9e93fc84fb702b6a1e01d8c43ffae4d2ea88192803c2b44191",
        "ResolvConfPath": "/var/lib/docker/containers/c550aebed004534f53d5048f74edd2c61dab7a52b742510bbb4467cf3bb69b82/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/c550aebed004534f53d5048f74edd2c61dab7a52b742510bbb4467cf3bb69b82/hostname",
        "HostsPath": "/var/lib/docker/containers/c550aebed004534f53d5048f74edd2c61dab7a52b742510bbb4467cf3bb69b82/hosts",
        "LogPath": "/var/lib/docker/containers/c550aebed004534f53d5048f74edd2c61dab7a52b742510bbb4467cf3bb69b82/c550aebed004534f53d5048f74edd2c61dab7a52b742510bbb4467cf3bb69b82-json.log",
        "Name": "/x-redis-3",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": [
            "40634aa5af84a3d8617a624f72980dee2707f479ea687ef17368f0a53fba056c"
        ],
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {
                "6379/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "6379"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/8707aa4532c13e134d175c9816bd640c644f0e238928c37920cf163b6aea3749-init/diff:/var/lib/docker/overlay2/d5c519498896b16c6f1449679cf5b5e64a330144b46a947059d239b8e99bf9c0/diff:/var/lib/docker/overlay2/bcb140b24e48571d1716cf7455f74716aa9c2ef39329f1975433796988606291/diff:/var/lib/docker/overlay2/f810e7a00b5e9584e413a4aabf56e1ea82f54b09033883ce9ef5acd7507ea11c/diff:/var/lib/docker/overlay2/4d76a98e23eb15a6ea542d686d2bb272b90a95516b7090a7af7586cd0fbe5e02/diff:/var/lib/docker/overlay2/c92a7d00cac742b27296b558503dd88818a2ae27ca6aba2c29333e5a9a65e10e/diff:/var/lib/docker/overlay2/916cb6212e047d064f42a19f74a4bd220ef86cd5947c5fbf4eb8ab570ada01f1/diff",
                "MergedDir": "/var/lib/docker/overlay2/8707aa4532c13e134d175c9816bd640c644f0e238928c37920cf163b6aea3749/merged",
                "UpperDir": "/var/lib/docker/overlay2/8707aa4532c13e134d175c9816bd640c644f0e238928c37920cf163b6aea3749/diff",
                "WorkDir": "/var/lib/docker/overlay2/8707aa4532c13e134d175c9816bd640c644f0e238928c37920cf163b6aea3749/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [
            {
                "Type": "volume",
                "Name": "96d58fd0591708b7aef675cc3032daa52e1ba5fd00204f36488dc7a66b1cd477",
                "Source": "/var/lib/docker/volumes/96d58fd0591708b7aef675cc3032daa52e1ba5fd00204f36488dc7a66b1cd477/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
        "Config": {
            "Hostname": "c550aebed004",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": true,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.14",
                "REDIS_VERSION=6.2.6",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.6.tar.gz",
                "REDIS_DOWNLOAD_SHA=5b2b8b7a50111ef395bf1c1d5be11e6e167ac018125055daa8b5c2317ae131ab"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "redis",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "f32fdb3d6c1f3ff227ff0f22a86b61b662aa1d312c479365279e137c72f6a27f",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "6379/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "6379"
                    },
                    {
                        "HostIp": "::",
                        "HostPort": "6379"
                    }
                ]
            },
            "SandboxKey": "/var/run/docker/netns/f32fdb3d6c1f",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "4fdf75be67e617fcf5b17311ec8c9640a7a218d603f183d11cf65dc13afda0c9",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "a295b9f0cfc089b1a74911534e0a69f17bfece6e3db5ee7fe6b574632aecd29e",
                    "EndpointID": "4fdf75be67e617fcf5b17311ec8c9640a7a218d603f183d11cf65dc13afda0c9",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

# 通过--format参数筛选容器信息
[root@VM-12-2-centos ~]# docker inspect c550aebed004 --format='{{.State.Status}}'
running
```

- 查看容器内进程信息

```shell
# 语法 ==> docker top 容器id
[root@VM-12-2-centos ~]# docker top c550aebed004
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
polkitd             1985                1966                0                   17:09               ?                   00:00:09            redis-server *:6379
root                4391                1966                0                   17:25               pts/0               00:00:00            /bin/bash
```

- 查看容器日志信息

```shell
# 查看容器日志 ==> docker logs 容器ID
[root@VM-12-2-centos ~]# docker logs c550aebed004
1:C 27 Apr 2022 09:09:26.622 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 27 Apr 2022 09:09:26.622 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 27 Apr 2022 09:09:26.622 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 27 Apr 2022 09:09:26.622 * monotonic clock: POSIX clock_gettime
1:M 27 Apr 2022 09:09:26.622 * Running mode=standalone, port=6379.
1:M 27 Apr 2022 09:09:26.622 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:M 27 Apr 2022 09:09:26.622 # Server initialized
1:M 27 Apr 2022 09:09:26.622 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1:M 27 Apr 2022 09:09:26.623 * Ready to accept connections
# tailf查看实时日志
[root@VM-12-2-centos ~]# docker logs -tf c550aebed004
2022-04-27T09:09:26.623122894Z 1:C 27 Apr 2022 09:09:26.622 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
2022-04-27T09:09:26.624602657Z 1:C 27 Apr 2022 09:09:26.622 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=1, just started
2022-04-27T09:09:26.624610431Z 1:C 27 Apr 2022 09:09:26.622 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
2022-04-27T09:09:26.624614489Z 1:M 27 Apr 2022 09:09:26.622 * monotonic clock: POSIX clock_gettime
2022-04-27T09:09:26.624617274Z 1:M 27 Apr 2022 09:09:26.622 * Running mode=standalone, port=6379.
2022-04-27T09:09:26.624619949Z 1:M 27 Apr 2022 09:09:26.622 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
2022-04-27T09:09:26.624622734Z 1:M 27 Apr 2022 09:09:26.622 # Server initialized
2022-04-27T09:09:26.624625720Z 1:M 27 Apr 2022 09:09:26.622 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
2022-04-27T09:09:26.624628866Z 1:M 27 Apr 2022 09:09:26.623 * Ready to accept connections
```

- 查看容器状态

```shell
# 查看所有容器状态，每秒刷新一次，按C可现实Docker容器名称
[root@VM-12-2-centos ~]# docker stats

CONTAINER ID   NAME           CPU %     MEM USAGE / LIMIT   MEM %     NET I/O          BLOCK I/O         PIDS
c1c5465d6109   tomcat         0.05%     88.14MiB / 3.7GiB   2.33%     104kB / 105kB    2.46MB / 0B       28
886df6dbc34d   registry-web   0.00%     1.969MiB / 3.7GiB   0.05%     317kB / 4MB      0B / 41kB         3
43583ad57e36   registry       0.00%     15.45MiB / 3.7GiB   0.41%     532MB / 1.09MB   10.1MB / 70.9MB   7

CONTAINER ID   NAME           CPU %     MEM USAGE / LIMIT   MEM %     NET I/O          BLOCK I/O         PIDS
c1c5465d6109   tomcat         0.05%     88.14MiB / 3.7GiB   2.33%     104kB / 105kB    2.46MB / 0B       28
886df6dbc34d   registry-web   0.00%     1.969MiB / 3.7GiB   0.05%     317kB / 4MB      0B / 41kB         3
43583ad57e36   registry       0.00%     15.45MiB / 3.7GiB   0.41%     532MB / 1.09MB   10.1MB / 70.9MB   7
# 查看某个容器的状态，每秒刷新一次
[root@VM-12-2-centos ~]# docker stats c1c5465d6109

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O         BLOCK I/O     PIDS
c1c5465d6109   tomcat    0.00%     88.14MiB / 3.7GiB   2.33%     104kB / 105kB   2.46MB / 0B   28

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O         BLOCK I/O     PIDS
c1c5465d6109   tomcat    0.00%     88.14MiB / 3.7GiB   2.33%     104kB / 105kB   2.46MB / 0B   28
```

# 四：Docker应用

### 4.1 Docker部署Mysql

- 拉取镜像

```bash
[root@VM-12-2-centos ~]# docker pull mysql:5.7
..................................................................................................
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
hello-world-x   latest    5aa5faee7296   8 days ago      13.3kB
redis           latest    3c3da61c4be0   2 weeks ago     113MB
mysql           5.7       82d2d47667cf   2 weeks ago     450MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
redis           6.0.8     16ecd2772934   18 months ago   104MB
```

- 创建容器

```shell
[root@VM-12-2-centos ~]# docker run -di -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:5.7
0b780160d431e3bcb5950f791e73beaf72e2aaacf86d77d6a2ca42517fd1e0ad
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                                  NAMES
0b780160d431   mysql:5.7   "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   33060/tcp, 0.0.0.0:3310->3306/tcp, :::3310->3306/tcp   relaxed_khorana
69e5ce02db61   redis       "docker-entrypoint.s…"   8 days ago      Up 8 days      0.0.0.0:6380->6379/tcp, :::6380->6379/tcp              redis-server
```

- 命令行登录

```shell
[root@VM-12-2-centos ~]# docker exec -it 0b780160d431 /bin/bash
root@0b780160d431:/# 
root@0b780160d431:/# mysql
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
root@0b780160d431:/# mysql -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.37 MySQL Community Server (GPL)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show tables;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

### 4.2 Docker部署Redis

- 拉取镜像

```shell
[root@VM-12-2-centos ~]# docker pull redis:6.0.8
6.0.8: Pulling from library/redis
bb79b6b2107f: Pull complete 
1ed3521a5dcb: Pull complete 
5999b99cee8f: Pull complete 
3f806f5245c9: Pull complete 
f8a4497572b2: Pull complete 
eafe3b6b8d06: Pull complete 
Digest: sha256:21db12e5ab3cc343e9376d655e8eabbdbe5516801373e95a8a9e66010c5b8819
Status: Downloaded newer image for redis:6.0.8
docker.io/library/redis:6.0.8
```

- 创建容器

```shell
[root@VM-12-2-centos lzx]# docker run -di --name=redis-server -p 6380:6379 redis
69e5ce02db614427a8c47af05f9b05ee3e3e76efdf4b2f8a1db08f7c6bdf4373
[root@VM-12-2-centos lzx]# docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                                       NAMES
69e5ce02db61   redis     "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:6380->6379/tcp, :::6380->6379/tcp   redis-server
[root@VM-12-2-centos lzx]# netstat -tunlp | grep 6380
tcp        0      0 0.0.0.0:6380            0.0.0.0:*               LISTEN      29254/docker-proxy  
tcp6       0      0 :::6380                 :::*                    LISTEN      29260/docker-proxy
```

- 命令行登录

```shell
[root@VM-12-2-centos lighthouse]# docker exec -it 69e5ce02db61 redis-cli
127.0.0.1:6379> 
127.0.0.1:6379> get key
(nil)
127.0.0.1:6379> set key value1
OK
127.0.0.1:6379> get key
"value1"
127.0.0.1:6379> 
```

### 4.3 Docker部署Nginx

- 拉取镜像

```shell
[root@VM-12-2-centos ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
1fe172e4850f: Already exists 
35c195f487df: Pull complete 
213b9b16f495: Pull complete 
a8172d9e19b9: Pull complete 
f5eee2cb2150: Pull complete 
93e404ba8667: Pull complete 
Digest: sha256:859ab6768a6f26a79bc42b231664111317d095a4f04e4b6fe79ce37b3d199097
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
hello-world-x   latest    5aa5faee7296   9 days ago      13.3kB
redis           latest    3c3da61c4be0   2 weeks ago     113MB
nginx           latest    fa5269854a5e   2 weeks ago     142MB
mysql           5.7       82d2d47667cf   2 weeks ago     450MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
redis           6.0.8     16ecd2772934   18 months ago   104MB
```

- 创建容器

```shell
[root@VM-12-2-centos ~]# docker run -di --name=web-server -p 443:443 -p 80:80 nginx
ce5f7383ae2aa9e2dc989daafade85e001f16d9ed1e686c9a462533c9c8079bd
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                                                      NAMES
ce5f7383ae2a   nginx       "/docker-entrypoint.…"   2 seconds ago   Up 1 second    0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis       "docker-entrypoint.s…"   8 days ago      Up 8 days      0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
```

- 访问测试

![image-20220506160658578](http://rocks526.top/lzx/image-20220506160658578.png)

### 4.4 Docker部署Tomcat

- 拉取镜像

```shell
[root@VM-12-2-centos ~]# docker pull tomcat:9
9: Pulling from library/tomcat
67e8aa6c8bbc: Pull complete 
627e6c1e1055: Pull complete 
0670968926f6: Pull complete 
5a8b0e20be4b: Pull complete 
7a93fb438607: Pull complete 
400f1e54bef0: Pull complete 
f0b65b53f1a4: Pull complete 
dc9d1a029c69: Pull complete 
25dbf9415d2d: Pull complete 
28cdc7690cfc: Pull complete 
Digest: sha256:3593fef10852f51c1243c4e275ea6acba1441413271a04b6047da19259458b24
Status: Downloaded newer image for tomcat:9
docker.io/library/tomcat:9
[root@VM-12-2-centos ~]# docker images
REPOSITORY                   TAG       IMAGE ID       CREATED         SIZE
tomcat                       9         5e9a4d049871   2 days ago      680MB
my-jdk8-img                  latest    4eae70b95876   7 days ago      1.2GB
my-centos-img                latest    a2812fd8436b   8 days ago      435MB
hello-world-x                latest    5aa5faee7296   2 weeks ago     13.3kB
redis                        latest    3c3da61c4be0   3 weeks ago     113MB
nginx                        latest    fa5269854a5e   3 weeks ago     142MB
mysql                        5.7       82d2d47667cf   3 weeks ago     450MB
hello-world                  latest    feb5d9fea6a5   7 months ago    13.3kB
centos                       7         eeb6ee3f44bd   8 months ago    204MB
tomcat                       7         9dfd74e6bc2f   10 months ago   533MB
redis                        6.0.8     16ecd2772934   18 months ago   104MB
```

- 创建容器

```shell
[root@VM-12-2-centos ~]# docker run -di --name=tomcat -p 443:8080 tomcat:9
84e7a5b30a18747d64f6922470ccaa0a510b067624a068150ba96db1104c1a69
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED             STATUS          PORTS                                       NAMES
84e7a5b30a18   tomcat:9                   "catalina.sh run"        3 seconds ago       Up 3 seconds    0.0.0.0:443->8080/tcp, :::443->8080/tcp     tomcat
```

# 五：镜像制作

### 5.1 制作CentOS镜像

Centos官方的镜像为了瘦身，删除了很多不必要的软件，例如vim，net-tools，ssh等，因此制作一个包含基础工具的CentOS镜像。

- 拉取官方镜像

```shell
[root@VM-12-2-centos ~]# docker pull centos:7.4
Error response from daemon: manifest for centos:7.4 not found: manifest unknown: manifest unknown
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker pull centos:7
7: Pulling from library/centos
2d473b07cdd5: Pull complete 
Digest: sha256:c73f515d06b0fa07bb18d8202035e739a494ce760aa73129f60f4bf2bd22b407
Status: Downloaded newer image for centos:7
docker.io/library/centos:7
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
hello-world-x   latest    5aa5faee7296   9 days ago      13.3kB
redis           latest    3c3da61c4be0   2 weeks ago     113MB
nginx           latest    fa5269854a5e   2 weeks ago     142MB
mysql           5.7       82d2d47667cf   2 weeks ago     450MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
centos          7         eeb6ee3f44bd   7 months ago    204MB
redis           6.0.8     16ecd2772934   18 months ago   104MB
```

- 运行容器

```shell
[root@VM-12-2-centos ~]# docker run --name=my-centos -tdi --privileged=true -p 442:22 centos:7 /usr/sbin/init
8e06432c4f995485f97f57356462b9ba18c0a495a5e67d37f363e8adcbe2c1c8
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
8e06432c4f99   centos:7    "/bin/bash"              3 seconds ago    Up 2 seconds    0.0.0.0:422->22/tcp, :::422->22/tcp                                        beautiful_saha
ce5f7383ae2a   nginx       "/docker-entrypoint.…"   14 minutes ago   Up 14 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7   "docker-entrypoint.s…"   20 minutes ago   Up 20 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis       "docker-entrypoint.s…"   8 days ago       Up 8 days       0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
[root@VM-12-2-centos ~]# docker exec -it 8e06432c4f99 /bin/bash
[root@8e06432c4f99 /]# 
```

- 安装vim

```shell
[root@8e06432c4f99 /]# yum -y install vim
Loaded plugins: fastestmirror, ovl
Determining fastest mirrors
 * base: mirrors.nju.edu.cn
 * extras: ftp.sjtu.edu.cn
 * updates: ftp.sjtu.edu.cn

Installed:
  vim-enhanced.x86_64 2:7.4.629-8.el7_9                                                                                                                                    

Dependency Installed:
  gpm-libs.x86_64 0:1.20.7-6.el7                groff-base.x86_64 0:1.22.2-8.el7       perl.x86_64 4:5.16.3-299.el7_9           perl-Carp.noarch 0:1.26-244.el7          
  perl-Encode.x86_64 0:2.51-7.el7               perl-Exporter.noarch 0:5.68-3.el7      perl-File-Path.noarch 0:2.09-2.el7       perl-File-Temp.noarch 0:0.23.01-3.el7    
  perl-Filter.x86_64 0:1.49-3.el7               perl-Getopt-Long.noarch 0:2.40-3.el7   perl-HTTP-Tiny.noarch 0:0.033-3.el7      perl-PathTools.x86_64 0:3.40-5.el7       
  perl-Pod-Escapes.noarch 1:1.04-299.el7_9      perl-Pod-Perldoc.noarch 0:3.20-4.el7   perl-Pod-Simple.noarch 1:3.28-4.el7      perl-Pod-Usage.noarch 0:1.63-3.el7       
  perl-Scalar-List-Utils.x86_64 0:1.27-248.el7  perl-Socket.x86_64 0:2.010-5.el7       perl-Storable.x86_64 0:2.45-3.el7        perl-Text-ParseWords.noarch 0:3.29-4.el7 
  perl-Time-HiRes.x86_64 4:1.9725-3.el7         perl-Time-Local.noarch 0:1.2300-2.el7  perl-constant.noarch 0:1.27-2.el7        perl-libs.x86_64 4:5.16.3-299.el7_9      
  perl-macros.x86_64 4:5.16.3-299.el7_9         perl-parent.noarch 1:0.225-244.el7     perl-podlators.noarch 0:2.5.1-3.el7      perl-threads.x86_64 0:1.87-4.el7         
  perl-threads-shared.x86_64 0:1.43-6.el7       vim-common.x86_64 2:7.4.629-8.el7_9    vim-filesystem.x86_64 2:7.4.629-8.el7_9  which.x86_64 0:2.20-7.el7                

Complete!
```

- 安装net-tools

```shell
    [root@8e06432c4f99 /]# yum -y install net-tools
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
 * base: mirrors.nju.edu.cn
 * extras: ftp.sjtu.edu.cn
 * updates: ftp.sjtu.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package net-tools.x86_64 0:2.0-0.25.20131004git.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===========================================================================================================================================================================
 Package                                Arch                                Version                                                Repository                         Size
===========================================================================================================================================================================
Installing:
 net-tools                              x86_64                              2.0-0.25.20131004git.el7                               base                              306 k

Transaction Summary
===========================================================================================================================================================================
Install  1 Package

Total download size: 306 k
Installed size: 917 k
Downloading packages:
net-tools-2.0-0.25.20131004git.el7.x86_64.rpm                                                                                                       | 306 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : net-tools-2.0-0.25.20131004git.el7.x86_64                                                                                                               1/1 
  Verifying  : net-tools-2.0-0.25.20131004git.el7.x86_64                                                                                                               1/1 

Installed:
  net-tools.x86_64 0:2.0-0.25.20131004git.el7                                                                                                                              

Complete!
```

- 安装SSH

```shell
[root@8e06432c4f99 /]# yum -y install openssh-server
........................................................................
Installed:
  openssh-server.x86_64 0:7.4p1-22.el7_9                                                                                                                                   

Dependency Installed:
  fipscheck.x86_64 0:1.4.1-6.el7         fipscheck-lib.x86_64 0:1.4.1-6.el7         openssh.x86_64 0:7.4p1-22.el7_9         tcp_wrappers-libs.x86_64 0:7.6-77.el7        

Complete!
[root@8e06432c4f99 /]# yum -y install openssh-clients
........................................................................

Installed:
  openssh-clients.x86_64 0:7.4p1-22.el7_9                                                                                                                                  

Dependency Installed:
  libedit.x86_64 0:3.0-12.20121213cvs.el7                                                                                                                                  

Complete!
[root@8e06432c4f99 /]# vim /etc/ssh/sshd_config
PubkeyAuthentication yes #启用公钥私钥配对认证方式
AuthorizedKeysFile .ssh/authorized_keys #公钥文件路径（和上面生成的文件同）
PermitRootLogin yes #root能使用ssh登录

[root@8e06432c4f99 /]# yum install initscripts -y
........................................................................

Installed:
  initscripts.x86_64 0:9.49.53-1.el7_9.1                                                                                                                                   

Dependency Installed:
  iproute.x86_64 0:4.11.0-30.el7         iptables.x86_64 0:1.4.21-35.el7              libmnl.x86_64 0:1.0.3-7.el7      libnetfilter_conntrack.x86_64 0:1.0.6-1.el7_3     
  libnfnetlink.x86_64 0:1.0.1-4.el7      sysvinit-tools.x86_64 0:2.88-14.dsf.el7     

Complete!
[root@e9565293654e /]# service sshd restart
Redirecting to /bin/systemctl restart sshd.service
[root@e9565293654e /]# netstat -tunlp | grep 22
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      282/sshd            
tcp6       0      0 :::22                   :::*                    LISTEN      282/sshd 
```

- 测试SSH连接

```shell
# 如果不知道密码，可以先登录容器修改密码
[root@e9565293654e /]# passwd		
Changing password for user root.
New password: 
BAD PASSWORD: The password fails the dictionary check - it is based on a dictionary word
Retype new password: 
passwd: all authentication tokens updated successfully.
# 测试SSH连接
[root@VM-12-2-centos ~]# ssh -p 442 root@127.0.0.1
root@127.0.0.1's password: 
Last login: Fri May  6 08:52:01 2022 from gateway
[root@e9565293654e ~]# 
[root@e9565293654e ~]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.5  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:05  txqueuelen 0  (Ethernet)
        RX packets 4151  bytes 47039835 (44.8 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3205  bytes 232794 (227.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- 将容器打包保存为镜像

```shell
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
e9565293654e   centos:7    "/usr/sbin/init"         8 minutes ago    Up 8 minutes    0.0.0.0:442->22/tcp, :::442->22/tcp                                        my-centos
ce5f7383ae2a   nginx       "/docker-entrypoint.…"   49 minutes ago   Up 49 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7   "docker-entrypoint.s…"   54 minutes ago   Up 54 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis       "docker-entrypoint.s…"   8 days ago       Up 8 days       0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker commit my-centos my-centos-img
sha256:a2812fd8436b6777857d69bb74934c01b19a803d8d4ec9c31a967cf3881d2f8a
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
my-centos-img   latest    a2812fd8436b   3 seconds ago   435MB
hello-world-x   latest    5aa5faee7296   9 days ago      13.3kB
redis           latest    3c3da61c4be0   2 weeks ago     113MB
nginx           latest    fa5269854a5e   2 weeks ago     142MB
mysql           5.7       82d2d47667cf   2 weeks ago     450MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
centos          7         eeb6ee3f44bd   7 months ago    204MB
tomcat          7         9dfd74e6bc2f   10 months ago   533MB
redis           6.0.8     16ecd2772934   18 months ago   104MB
```

- 通过镜像一键启动容器

```shell
# 删除之前的容器
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
e9565293654e   centos:7    "/usr/sbin/init"         9 minutes ago    Up 9 minutes    0.0.0.0:442->22/tcp, :::442->22/tcp                                        my-centos
ce5f7383ae2a   nginx       "/docker-entrypoint.…"   50 minutes ago   Up 50 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7   "docker-entrypoint.s…"   56 minutes ago   Up 56 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis       "docker-entrypoint.s…"   8 days ago       Up 8 days       0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
[root@VM-12-2-centos ~]# docker stop e9565293654e
e9565293654e
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker rm e9565293654e
e9565293654e
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
ce5f7383ae2a   nginx       "/docker-entrypoint.…"   51 minutes ago   Up 51 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7   "docker-entrypoint.s…"   56 minutes ago   Up 56 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis       "docker-entrypoint.s…"   8 days ago       Up 8 days       0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
# 根据刚才的镜像一键创建容器
[root@VM-12-2-centos ~]# docker run --name=my-centos -tdi --privileged=true -p 442:22 my-centos-img
9bc7eadb3e124b66df34431fffb2a8e1e3c3c715257c8dc3ce003688483807ab
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
9bc7eadb3e12   my-centos-img   "/usr/sbin/init"         3 seconds ago    Up 2 seconds    0.0.0.0:442->22/tcp, :::442->22/tcp                                        my-centos
ce5f7383ae2a   nginx           "/docker-entrypoint.…"   52 minutes ago   Up 52 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7       "docker-entrypoint.s…"   57 minutes ago   Up 57 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis           "docker-entrypoint.s…"   8 days ago       Up 8 days       0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
# 测试SSH连接，密码为刚才容器里修改的密码
[root@VM-12-2-centos ~]# ssh -p 442 root@127.0.0.1
root@127.0.0.1's password: 
Last login: Fri May  6 08:52:33 2022 from gateway
[root@9bc7eadb3e12 ~]# 
[root@9bc7eadb3e12 ~]# exit
logout
Connection to 127.0.0.1 closed.
```

### 5.2 制作Jdk环境镜像

项目部署时，Jdk基本是必备的，因此基于刚才的CentOS镜像，再制作一个Java部署环境镜像。

- 基于刚才的CentOS镜像创建容器

```shell
[root@VM-12-2-centos ~]# docker run --name=my-centos -tdi --privileged=true -p 442:22 my-centos-img
9bc7eadb3e124b66df34431fffb2a8e1e3c3c715257c8dc3ce003688483807ab
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                                                                      NAMES
9bc7eadb3e12   my-centos-img   "/usr/sbin/init"         3 seconds ago    Up 2 seconds    0.0.0.0:442->22/tcp, :::442->22/tcp                                        my-centos
ce5f7383ae2a   nginx           "/docker-entrypoint.…"   52 minutes ago   Up 52 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7       "docker-entrypoint.s…"   57 minutes ago   Up 57 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis           "docker-entrypoint.s…"   8 days ago       Up 8 days       0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server

```

- 安装Jdk

```shell
[root@VM-12-2-centos lzx]# docker cp /home/lzx/jdk1.8.0_181.tar.gz 9bc7eadb3e12:/opt/
[root@VM-12-2-centos ~]# docker exec -it 9bc7eadb3e12 /bin/bash
[root@9bc7eadb3e12 /]# ls /opt/
jdk1.8.0_181.tar.gz
[root@9bc7eadb3e12 opt]# tar -xvf jdk1.8.0_181.tar.gz
..........................................
[root@9bc7eadb3e12 opt]# ls
jdk1.8.0_181  jdk1.8.0_181.tar.gz
```

- 声明环境变量

```shell
[root@9bc7eadb3e12 opt]# vim /etc/profile

export JAVA_HOME=/opt/jdk1.8.0_181
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$PATH:${JAVA_PATH}

[root@9bc7eadb3e12 opt]# 
[root@9bc7eadb3e12 opt]# 
[root@9bc7eadb3e12 opt]# source /etc/profile
[root@9bc7eadb3e12 opt]# 
[root@9bc7eadb3e12 opt]# java -version
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

- 将容器打包为新的镜像

```shell
[root@VM-12-2-centos lzx]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED       STATUS       PORTS                                                                      NAMES
9bc7eadb3e12   my-centos-img   "/usr/sbin/init"         4 hours ago   Up 4 hours   0.0.0.0:442->22/tcp, :::442->22/tcp                                        my-centos
ce5f7383ae2a   nginx           "/docker-entrypoint.…"   4 hours ago   Up 4 hours   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7       "docker-entrypoint.s…"   5 hours ago   Up 5 hours   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis           "docker-entrypoint.s…"   9 days ago    Up 9 days    0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# docker commit my-centos my-jdk8-img
sha256:4eae70b95876f8790b1a9ef220b33da5b75f323f6578a9372cb073c2b4a20327
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
my-jdk8-img     latest    4eae70b95876   5 seconds ago   1.2GB
my-centos-img   latest    a2812fd8436b   4 hours ago     435MB
hello-world-x   latest    5aa5faee7296   9 days ago      13.3kB
redis           latest    3c3da61c4be0   2 weeks ago     113MB
nginx           latest    fa5269854a5e   2 weeks ago     142MB
mysql           5.7       82d2d47667cf   2 weeks ago     450MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
centos          7         eeb6ee3f44bd   7 months ago    204MB
tomcat          7         9dfd74e6bc2f   10 months ago   533MB
redis           6.0.8     16ecd2772934   18 months ago   104MB
```

# 六：Dockerfile

### 6.1 Dockerfile介绍

Dockerfile是一个Docker镜像的描述文件，Dockerfile其内部包含了一条条的指令，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

简单来说，Dockerfile就是Docker容器的行为描述，用于`一键快速启动部署容器`。

Dockerfile结构大致分为四个部分：

- 基础镜像信息：FROM centos:7
- 维护者信息：
- 镜像操作指令：RUN yum insatll openssh-server -y
- 容器启动时执行指令：CMD ["/bin/bash"]

### 6.2 Dockerfile指令

- 常用指令

| 指令       | 说明                                                         | 语法                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| FROM       | 指定基础镜像                                                 | FROM centos:7                                                |
| MAINTAINER | 指定维护者信息，可以没有                                     | MAINTAINER 维护者信息                                        |
| RUN        | 编译镜像时运行的命令、脚本（docker build时运行）             | RUN <shell 命令> <br/>RUN ["<可执行文件或命令>","\<param1>","\<param2>",...] |
| ADD        | 编译镜像时，复制文件到容器里，针对tar压缩包会自动解压        | ADD [--chown=\<user>:\<group>] <源路径1>...  <目标路径>      |
| WORKDIR    | 设置RUN、CMD、COPY、ADD等命令执行的工作目录，类似于cd        | WORKDIR <工作目录路径>                                       |
| VOLUME     | 设置匿名数据卷，在启动容器时忘记挂载数据卷，会自动挂载到匿名卷 | VOLUME ["<路径1>", "<路径2>"...]                             |
| EXPOSE     | 设置容器守护端口，以方便使用者配置映射，在运行时使用随机端口映射时，会自动随机映射 EXPOSE 的端口 | EXPOSE <端口1> [<端口2>...]                                  |
| CMD        | 设置容器启动后执行的命令（docker run时运行）                 | CMD <shell 命令> <br/>CMD ["<可执行文件或命令>","\<param1>","\<param2>",...] |

- 其他指令

| 指令        | 说明                                                         | 语法                                                         |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| COPY        | 编译镜像时，复制文件到容器里                                 | COPY [--chown=\<user>:\<group>] <源路径1>...  <目标路径>     |
| ENV         | 设置容器的环境变量                                           | ENV \<key> \<value><br/>ENV \<key1>=\<value1> \<key2>=\<value2>... |
| ENTRYPOINT  | 类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序（存在多个 ENTRYPOINT 指令，仅最后一个生效） | ENTRYPOINT <shell 命令> <br/>ENTRYPOINT ["<可执行文件或命令>","\<param1>","\<param2>",...] |
| HEALTHCHECK | 设置容器健康检查的命令                                       | HEALTHCHECK [选项] CMD <命令>：设置检查容器健康状况的命令    |
| ARG         | 设置编译镜像时加入的参数（与ENV类似，但只在构建容器时有效），构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖 | ARG <参数名>[=<默认值>]                                      |

### 6.4 Dockerfile构建Jdk环境

在之前制作Jdk环境镜像时，是通过手动安装然后commit产生新的镜像实现的，下面通过Dockerfile实现一键安装：

- dockerfile如下：

```dockerfile
# 拉取基础镜像
FROM centos:7
# 声明维护者
MAINTAINER lizhaoxuan
# 安装基础工具包
RUN yum -y install vim net-tools openssh-server openssh-clients initscripts
# 修改ssh配置
RUN sed -i '$aPubkeyAuthentication yes\nPermitRootLogin yes' /etc/ssh/sshd_config
# 定义默认密码，可构建时修改
ARG pwd=root@1234
# 修改密码
RUN echo "root:"${pwd} | chpasswd
# 创建Jdk目录
RUN mkdir -p /home/java/
# 拷贝Jdk安装包
ADD jdk1.8.0_181.tar.gz /home/java/
# 添加环境变量
RUN sed -i '$aexport JAVA_HOME=/home/java/jdk1.8.0_181\nexport JRE_HOME=${JAVA_HOME}/jre\nexport CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH\nexport JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin\nexport PATH=$PATH:${JAVA_PATH}' /etc/profile
# 让环境变量生效
RUN source /etc/profile
# 容器启动后再启动ssh服务（容器启动时需要指定--privileged=true，-t，/usr/sbin/init）
CMD service sshd start
```

- 构建镜像

```shell
[root@VM-12-2-centos lzx]# docker build -f /home/lzx/jdk8-dockerfile -t jdk8-dockerfile-img:v1 --build-arg pwd=root@123 .
Sending build context to Docker daemon  767.1MB
Step 1/11 : FROM centos:7
 ---> eeb6ee3f44bd
Step 2/11 : MAINTAINER lizhaoxuan
 ---> Using cache
 ---> 175947f11654
Step 3/11 : RUN yum -y install vim net-tools openssh-server openssh-clients initscripts
 ---> Using cache
 ---> 21d1c4f0e040
Step 4/11 : RUN sed -i '$aPubkeyAuthentication yes\nPermitRootLogin yes' /etc/ssh/sshd_config
 ---> Using cache
 ---> 2ebd0492321f
Step 5/11 : ARG pwd=root@1234
 ---> Using cache
 ---> 5df2f96b3cc5
Step 6/11 : RUN echo "root:"${pwd} | chpasswd
 ---> Using cache
 ---> 215c88117fca
Step 7/11 : RUN mkdir -p /home/java/
 ---> Using cache
 ---> 5c1b2c809d82
Step 8/11 : ADD jdk1.8.0_181.tar.gz /home/java/
 ---> 58976b2bfe2c
Step 9/11 : RUN sed -i '$aexport JAVA_HOME=/home/java/jdk1.8.0_181\nexport JRE_HOME=${JAVA_HOME}/jre\nexport CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH\nexport JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin\nexport PATH=$PATH:${JAVA_PATH}' /etc/profile
 ---> Running in 84a065fc5867
Removing intermediate container 84a065fc5867
 ---> 385d2fa21d28
Step 10/11 : RUN source /etc/profile
 ---> Running in be100d0db181
Removing intermediate container be100d0db181
 ---> d2653899eb19
Step 11/11 : CMD service sshd start
 ---> Running in 96d36bfde0bf
Removing intermediate container 96d36bfde0bf
 ---> ee59d98fb817
Successfully built ee59d98fb817
Successfully tagged jdk8-dockerfile-img:v1
# 查看构建好的镜像
[root@VM-12-2-centos lzx]# docker images
REPOSITORY                   TAG       IMAGE ID       CREATED          SIZE
jdk8-dockerfile-img          v1        ee59d98fb817   15 seconds ago   817MB
# 启动容器
[root@VM-12-2-centos lzx]# docker run -tdi --name jdk8 --privileged=true -p 422:22 jdk8-dockerfile-img:v1 /usr/sbin/init
a81702744333152ccce2b8cbe665e24276b27dda25f777b4b4a90af3dd88b597
# ssh连接，测试Java环境
[root@VM-12-2-centos lzx]# ssh -p 422 root@127.0.0.1
The authenticity of host '[127.0.0.1]:422 ([127.0.0.1]:422)' can't be established.
ECDSA key fingerprint is SHA256:QmIdYZEey7OmgqH/DVCoOpcLgc4wQp5SOqPHOCw4Qjk.
ECDSA key fingerprint is MD5:be:c3:d4:93:c8:e6:4a:b8:f5:6e:d9:cb:d5:cb:2e:8e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[127.0.0.1]:422' (ECDSA) to the list of known hosts.
root@127.0.0.1's password: 
[root@a81702744333 ~]# 
[root@a81702744333 ~]# java -version
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
[root@a81702744333 ~]# 
```

# 七：Docker数据卷

### 7.1 Docker数据卷介绍

Docker容器在删除时，容器相关的Namespaces都会被删除，包括容器里的数据。为了实现数据的持久化和备份，Docker容器提供了数据卷技术：`通过目录的挂载，将容器内的目录挂载在宿主机上面`，数据卷技术有以下优点：

- 数据保存备份：删除容器，容器挂载到本地的目录和数据不会删除；

- 数据同步：修改挂载在本地的文件，数据会自动同步到容器内；

- 数据共享：多个容器可以将目录挂载到宿主机的同一个目录，容器内产生数据，数据就会同步到本地，然后传递到其他容器；

Docker的数据卷技术分为三类：

- Volume：普通数据卷，映射到宿主机的/var/lib/docekr/volumes目录下
- bind mounts：绑定数据卷，映射到宿主机指定路径下

### 7.2 Volume

- 创建数据卷

```shell
[root@VM-12-2-centos ~]# docker volume create  my-volume-for-nginx
my-volume-for-nginx
```

- 查看创建的数据卷

```shell
[root@VM-12-2-centos ~]# docker volume ls
DRIVER    VOLUME NAME
local     my-volume-for-nginx
```

- 查看数据卷详情

```shell
[root@VM-12-2-centos ~]# docker volume inspect my-volume-for-nginx
[
    {
        "CreatedAt": "2022-05-26T13:47:07+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-volume-for-nginx/_data",
        "Name": "my-volume-for-nginx",
        "Options": {},
        "Scope": "local"
    }
]
```

注：可以看到此数据卷保存在宿主机的/var/lib/docker/volumes/my-volume-for-nginx/_data目录。

- 容器挂载数据卷

```shell
# 启动容器，mount参数挂载数据卷，type是数据卷类型，source是数据卷名称，target是容器目录
[root@VM-12-2-centos ~]# docker run -di --name=web-server --mount type=volume,source=my-volume-for-nginx,target=/usr/share/nginx/html nginx 
b050d04c974008cf7bd4b8ae9a82b763fb7504d452b7d8c139dd70e6be05112c
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
b050d04c9740   nginx     "/docker-entrypoint.…"   3 seconds ago   Up 2 seconds   80/tcp    web-server
# 查看容器挂载的数据卷信息
[root@VM-12-2-centos ~]# docker inspect web-server | grep Mounts -A 10
            "Mounts": [
                {
                    "Type": "volume",
                    "Source": "my-volume-for-nginx",
                    "Target": "/usr/local/share/nginx/html"
                }
            ],
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
--
        "Mounts": [
            {
                "Type": "volume",
                "Name": "my-volume-for-nginx",
                "Source": "/var/lib/docker/volumes/my-volume-for-nginx/_data",  
                "Destination": "/usr/local/share/nginx/html",
                "Driver": "local",
                "Mode": "z",
                "RW": true,			# 容器对挂载目录的读写权限，可以通过readonly参数设置只读
                "Propagation": ""
            }
# 修改nginx主页
[root@VM-12-2-centos _data]# cd /var/lib/docker/volumes/my-volume-for-nginx/_data
[root@VM-12-2-centos _data]# ll
total 8
-rw-r--r-- 1 root root 497 Jan 25 23:03 50x.html
-rw-r--r-- 1 root root 615 Jan 25 23:03 index.html
[root@VM-12-2-centos _data]# echo "Hello world" > index.html
[root@VM-12-2-centos _data]# 
[root@VM-12-2-centos _data]# docker exec -it web-server curl 127.0.0.1:80
Hello world
# 删除容器，数据卷依旧存在
[root@VM-12-2-centos _data]# docker stop web-server
web-server
[root@VM-12-2-centos _data]# docker rm web-server
web-server
[root@VM-12-2-centos _data]# docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@VM-12-2-centos _data]# ll
total 8
-rw-r--r-- 1 root root 497 Jan 25 23:03 50x.html
-rw-r--r-- 1 root root  12 May 27 12:02 index.html
```

设置只读权限：--mount type=volume,source=my-volume-for-nginx,target=/usr/local/share/nginx/html,readonly

- 删除数据卷

```shell
[root@VM-12-2-centos lzx]# docker volume rm my-volume-for-nginx
my-volume-for-nginx
[root@VM-12-2-centos lzx]# docker volume ls
DRIVER    VOLUME NAME
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# ll /var/lib/docker/volumes/
total 32
brw------- 1 root root 253, 1 May 14 19:34 backingFsBlockDev
-rw------- 1 root root  65536 May 27 12:05 metadata.db
```

### 7.3 bind mounts

```shell
[root@VM-12-2-centos ~]# mkdir /opt/nginx
[root@VM-12-2-centos ~]# 
# 启动容器，并绑定数据卷
[root@VM-12-2-centos ~]# docker run -itd --name=web-server --mount type=bind,source=/opt/nginx,target=/usr/share/nginx/html nginx
dc7a192b71ccf9b5dfff8990f132387d2e1912ef288bb1cd264f485b5ae23906
[root@VM-12-2-centos ~]# 
# 查看绑定的数据卷信息
[root@VM-12-2-centos ~]# docker inspect web-server | grep Mounts -A 10
            "Mounts": [
                {
                    "Type": "bind",
                    "Source": "/opt/nginx",
                    "Target": "/usr/share/nginx/html"
                }
            ],
# 查看容器绑定目录，可以看到里面原本的index.html和50x.html文件没有了
[root@VM-12-2-centos ~]# docker exec -it web-server ls /usr/share/nginx/html
[root@VM-12-2-centos ~]# 
# 容器里添加文件
[root@VM-12-2-centos ~]#  docker exec -it web-server /bin/bash
root@dc7a192b71cc:/# echo "Hello,World" > /usr/share/nginx/html/index.html
root@dc7a192b71cc:/# curl 127.0.0.1:80
Hello,World
# 检查宿主机目录
[root@VM-12-2-centos ~]# ll /opt/nginx/
total 4
-rw-r--r-- 1 root root 12 May 27 13:44 index.html
[root@VM-12-2-centos ~]# cat /opt/nginx/index.html 
Hello,World
```

注：`Bind mounts挂载宿主机目录到一个容器中的非空目录，那么此容器中的非空目录中的文件会被隐藏`，容器访问这个目录时能够访问到的文件均来自于宿主机目录。这也是Bind mounts模式和Volumes模式最大的不同。

注：上面容器启动时的挂载参数，可以简化为`-v /opt/nginx:/usr/share/nginx/html`。

# 八：Docker网络

### 8.1 Docker网络介绍

大量的互联网应用服务包括多个服务组件，这往往需要多个容器之间通过网络通信进行相互配合。Docker默认的网络机制，会在`宿主机上初始化一个docker0网卡，针对宿主机和每个容器都分配不同的IP`，如下所示：

```shell
# 宿主机
[root@VM-12-2-centos sbin]# ifconfig
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255 # IP为172.17.0.1
        inet6 fe80::42:45ff:fe4d:5b36  prefixlen 64  scopeid 0x20<link>
        ether 02:42:45:4d:5b:36  txqueuelen 0  (Ethernet)
        RX packets 112817  bytes 17613325 (16.7 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 114124  bytes 865716227 (825.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.12.2  netmask 255.255.252.0  broadcast 10.0.15.255
        inet6 fe80::5054:ff:fe97:ef97  prefixlen 64  scopeid 0x20<link>
        ether 52:54:00:97:ef:97  txqueuelen 1000  (Ethernet)
        RX packets 11623953  bytes 3658366185 (3.4 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10628290  bytes 1585812773 (1.4 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 65518  bytes 534348741 (509.5 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 65518  bytes 534348741 (509.5 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  
# 启动jar包，用于后续端口测试
[root@VM-12-2-centos lzx]# nohup java -jar docker-1.0.0-SNAPSHOT.jar > docker-jar.log 2>&1 & 
[1] 20026
[root@VM-12-2-centos lzx]# tailf docker-jar.log 
2022-05-22 23:27:02.198  INFO 20026 --- [           main] com.lizhaoxuan.docker.DockerApplication  : Starting DockerApplication on VM-12-2-centos with PID 20026 (/home/lzx/docker-1.0.0-SNAPSHOT.jar started by root in /home/lzx)
2022-05-22 23:27:02.201  INFO 20026 --- [           main] com.lizhaoxuan.docker.DockerApplication  : No active profile set, falling back to default profiles: default
2022-05-22 23:27:03.406  INFO 20026 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8088 (http)
2022-05-22 23:27:03.419  INFO 20026 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-05-22 23:27:03.419  INFO 20026 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.41]
2022-05-22 23:27:03.485  INFO 20026 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-05-22 23:27:03.486  INFO 20026 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 1160 ms
2022-05-22 23:27:03.725  INFO 20026 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2022-05-22 23:27:03.960  INFO 20026 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8088 (http) with context path ''
2022-05-22 23:27:03.978  INFO 20026 --- [           main] com.lizhaoxuan.docker.DockerApplication  : Started DockerApplication in 2.42 seconds (JVM running for 2.943)
[root@VM-12-2-centos lzx]# curl 127.0.0.1:8088/
hello docker!
# 创建容器
[root@VM-12-2-centos lzx]# docker run -di --name=demo1 my-jdk8-img
22a8f7fe1922a420e4b37522b0b0da7fb1bde78fb07ce1e4340433b5f02f8e8d
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# docker exec -it demo1 ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255  # 容器IP为172.17.0.2
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 7  bytes 586 (586.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# 测试容器访问宿主机
[root@VM-12-2-centos lzx]# docker exec -it demo1 ping 172.17.0.1
PING 172.17.0.1 (172.17.0.1) 56(84) bytes of data.
64 bytes from 172.17.0.1: icmp_seq=1 ttl=64 time=0.073 ms
64 bytes from 172.17.0.1: icmp_seq=2 ttl=64 time=0.048 ms
64 bytes from 172.17.0.1: icmp_seq=3 ttl=64 time=0.059 ms
^C
--- 172.17.0.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.048/0.060/0.073/0.010 ms
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# docker exec -it demo1 curl 172.17.0.1:8088/
hello docker!
# 容器启动jar包，测试物理机访问容器
[root@VM-12-2-centos lzx]# docker cp docker-1.0.0-SNAPSHOT.jar demo1:/home/
[root@VM-12-2-centos lzx]# docker exec -it demo1  /bin/bash
[root@9d981484be1e /]# cd /home/
[root@9d981484be1e home]# source /etc/profile
[root@9d981484be1e home]# nohup java -jar docker-1.0.0-SNAPSHOT.jar > docker-jar.log 2>&1 & 
[1] 141
[root@9d981484be1e home]# tailf docker-jar.log 
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.7.RELEASE)

2022-05-24 08:57:24.378  INFO 141 --- [           main] com.lizhaoxuan.docker.DockerApplication  : Starting DockerApplication on 9d981484be1e with PID 141 (/home/docker-1.0.0-SNAPSHOT.jar started by root in /home)
2022-05-24 08:57:24.382  INFO 141 --- [           main] com.lizhaoxuan.docker.DockerApplication  : No active profile set, falling back to default profiles: default
2022-05-24 08:57:26.067  INFO 141 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8088 (http)
2022-05-24 08:57:26.111  INFO 141 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-05-24 08:57:26.111  INFO 141 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.41]
2022-05-24 08:57:26.246  INFO 141 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-05-24 08:57:26.246  INFO 141 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 1675 ms
2022-05-24 08:57:26.477  INFO 141 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2022-05-24 08:57:26.721  INFO 141 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8088 (http) with context path ''
2022-05-24 08:57:26.732  INFO 141 --- [           main] com.lizhaoxuan.docker.DockerApplication  : Started DockerApplication in 3.126 seconds (JVM running for 4.791)
# 物理机访问容器
[root@VM-12-2-centos lzx]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.043 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.044 ms
^C
--- 172.17.0.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.043/0.043/0.044/0.006 ms
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# curl http://172.17.0.2:8088/
hello docker!
[root@VM-12-2-centos lzx]# ping demo1  # 指定根据IP访问，无法根据容器名称访问
ping: demo1: Name or service not known
```

如上所示，Docker默认会为宿主机和所有容器分配一个IP，彼此之间可以通过IP相互访问，但无法通过容器名称访问。此种方式，最大的问题在于分配的IP不固定，`多个容器之间无法预先知道要访问的容器的IP`，例如SpringBoot服务要访问Redis容器，但无法预先知道Redis容器的IP，只能初始化完Redis容器后，手动修改SpringBoot容器里设置的Redis容器的IP，给大型系统的部署带来很大的麻烦。

因此Docker提供了`映射容器端口到宿主主机`和`容器互联机制`来为容器提供网络服务。

- 容器端口映射：将容器的内部端口映射到物理机的端口上
- 容器互联机制：多个容器之间可以通过容器名称直接访问

### 8.1 端口映射

Docker支持在启动容器时，通过`-p`或`-P`参数进行端口映射；`-p`指定映射的容器端口和宿主机端口，`-P`将容器指定端口映射到宿主机随机端口；通过多个`-p`参数可以配置映射多个端口，示例如下：

```shell
# 启动容器，配置端口映射
[root@VM-12-2-centos lzx]# docker run -di --name demo2 -p 8089:8088 my-jdk8-img
cec88e20993c55e258c4c9d56ef078af4d1ee00fef3ef20d95c32d0a6f167db9
# 容器里启动Jar包
[root@VM-12-2-centos lzx]# docker cp docker-1.0.0-SNAPSHOT.jar demo2:/home/
[root@VM-12-2-centos lzx]# docker exec -it demo2 /bin/bash
[root@cec88e20993c /]# cd home/
[root@cec88e20993c home]# source /etc/profile
[root@cec88e20993c home]# nohup java -jar docker-1.0.0-SNAPSHOT.jar > docker-jar.log 2>&1 & 
[1] 39
[root@cec88e20993c home]# tailf docker-jar.log 
2022-05-24 10:03:46.244  INFO 39 --- [           main] com.lizhaoxuan.docker.DockerApplication  : Starting DockerApplication on cec88e20993c with PID 39 (/home/docker-1.0.0-SNAPSHOT.jar started by root in /home)
2022-05-24 10:03:46.253  INFO 39 --- [           main] com.lizhaoxuan.docker.DockerApplication  : No active profile set, falling back to default profiles: default
2022-05-24 10:03:47.588  INFO 39 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8088 (http)
2022-05-24 10:03:47.600  INFO 39 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-05-24 10:03:47.601  INFO 39 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.41]
2022-05-24 10:03:47.679  INFO 39 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-05-24 10:03:47.679  INFO 39 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 1308 ms
2022-05-24 10:03:47.904  INFO 39 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2022-05-24 10:03:48.127  INFO 39 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8088 (http) with context path ''
2022-05-24 10:03:48.144  INFO 39 --- [           main] com.lizhaoxuan.docker.DockerApplication  : Started DockerApplication in 2.465 seconds (JVM running for 2.975)
# 测试容器映射到宿主机的端口
[root@VM-12-2-centos lzx]# curl http://127.0.0.1:8089/
hello docker!
# 启动容器，配置随机端口映射
[root@VM-12-2-centos lzx]# docker run -di --name demo3 -P my-jdk8-img
f9114cfd1ff5995d9563828be65faf4d6f3236f08e4b6dd19f856eb9fa873898
[root@VM-12-2-centos lzx]# 
# 由于my-jdk8-img镜像里只声明了22端口，因此容器的22端口随机映射到宿主机的49153
[root@VM-12-2-centos lzx]# docker ps   
CONTAINER ID   IMAGE         COMMAND            CREATED             STATUS             PORTS                                               NAMES
f9114cfd1ff5   my-jdk8-img   "/usr/sbin/init"   3 seconds ago       Up 2 seconds       0.0.0.0:49153->22/tcp, :::49153->22/tcp             demo3
cec88e20993c   my-jdk8-img   "/usr/sbin/init"   3 minutes ago       Up 3 minutes       22/tcp, 0.0.0.0:8089->8088/tcp, :::8089->8088/tcp   demo2
9d981484be1e   my-jdk8-img   "/usr/sbin/init"   About an hour ago   Up About an hour   22/tcp                                              demo1
# 测试随机映射端口
[root@VM-12-2-centos lzx]# ssh -p 49153 root@127.0.0.1
root@127.0.0.1's password: 
Last failed login: Tue May 24 10:09:29 UTC 2022 from gateway on ssh:notty
There was 1 failed login attempt since the last successful login.
Last login: Fri May  6 08:58:16 2022 from gateway
[root@336c6e540665 ~]# 
```

注：`-P`参数配置随机映射时，镜像里必须声明要暴露的端口。

### 8.2 容器互联

Docker支持在容器启动时，通过`--link`参数指定要连接的容器，多个容器之间可以通过容器名访问。

- 制作dockerfile，用于一键启动

```shell
# 创建文件
[root@VM-12-2-centos lzx]# vim springboot-dockerfile 
# 拉取基础镜像
FROM my-jdk8-img
# 声明维护者
MAINTAINER lizhaoxuan
# 拷贝jar包
COPY docker-1.0.0-SNAPSHOT.jar /home/
# 创建Jdk目录
RUN mkdir -p /home/java/
# 拷贝Jdk安装包
ADD jdk1.8.0_181.tar.gz /home/java/
# 配置环境变量
ENV JAVA_HOME=/home/java/jdk1.8.0_181
ENV JRE_HOME=${JAVA_HOME}/jre
ENV CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
ENV JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
ENV PATH=$PATH:${JAVA_PATH}
# 容器启动后再启动jar包
WORKDIR /home/
CMD java -jar docker-1.0.0-SNAPSHOT.jar

# 构建镜像
[root@VM-12-2-centos lzx]# docker build -f /home/lzx/springboot-dockerfile -t springboot-img:v1 .
Sending build context to Docker daemon  817.6MB
Step 1/9 : FROM my-jdk8-img
 ---> 4eae70b95876
Step 2/9 : MAINTAINER lizhaoxuan
 ---> Using cache
 ---> 7faca34bddfe
Step 3/9 : COPY docker-1.0.0-SNAPSHOT.jar /home/
 ---> d5128fdaf6a8
Step 4/9 : RUN mkdir -p /home/java/
 ---> Running in 0701438aae4a
Removing intermediate container 0701438aae4a
 ---> 6831a0f6a210
Step 5/9 : ADD jdk1.8.0_181.tar.gz /home/java/
 ---> 92d3138841cb
Step 6/9 : RUN sed -i '$aexport JAVA_HOME=/home/java/jdk1.8.0_181\nexport JRE_HOME=${JAVA_HOME}/jre\nexport CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH\nexport JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin\nexport PATH=$PATH:${JAVA_PATH}' /etc/profile
 ---> Running in 28fe38dba122
Removing intermediate container 28fe38dba122
 ---> 231d1eea1ae9
Step 7/9 : RUN source /etc/profile
 ---> Running in 46b2657b25d5
Removing intermediate container 46b2657b25d5
 ---> f7d506189ade
Step 8/9 : WORKDIR /home/
 ---> Running in 71a72cf1b2b4
Removing intermediate container 71a72cf1b2b4
 ---> 63ce49c2729a
Step 9/9 : CMD nohup java -jar docker-1.0.0-SNAPSHOT.jar > docker-jar.log 2>&1 &
 ---> Running in 9ef10c1b2d02
Removing intermediate container 9ef10c1b2d02
 ---> da6041381d45
Successfully built da6041381d45
Successfully tagged springboot-img:v1
```

- 启动容器

```shell
[root@VM-12-2-centos lzx]# docker run -di --name=demo1 springboot-img:v1
82fbcf207002923da0a27475bed5e6c5b9c5805166414a12f6cb9493d9516bab
[root@VM-12-2-centos lzx]# docker run -di --name=demo2 --link demo1 springboot-img:v1
f2c8092070dbfb73d02effc456cc4dada88fe2f357dc74cb00edaa96c01cbb91
[root@VM-12-2-centos lzx]# 
[root@VM-12-2-centos lzx]# docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED              STATUS              PORTS     NAMES
f2c8092070db   springboot-img:v1   "/bin/sh -c 'java -j…"   3 seconds ago        Up 2 seconds        22/tcp    demo2
82fbcf207002   springboot-img:v1   "/bin/sh -c 'java -j…"   About a minute ago   Up About a minute   22/tcp    demo1
# 连通性测试
[root@VM-12-2-centos lzx]# docker exec -it demo1 ping demo2
ping: demo2: Name or service not known
[root@VM-12-2-centos lzx]# docker exec -it demo2 ping demo1
PING demo1 (172.17.0.2) 56(84) bytes of data.
64 bytes from demo1 (172.17.0.2): icmp_seq=1 ttl=64 time=0.077 ms
64 bytes from demo1 (172.17.0.2): icmp_seq=2 ttl=64 time=0.049 ms
64 bytes from demo1 (172.17.0.2): icmp_seq=3 ttl=64 time=0.047 ms
^C
--- demo1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.047/0.057/0.077/0.016 ms
[root@VM-12-2-centos lzx]# docker exec -it demo2 curl http://demo1:8088/
hello docker!
[root@VM-12-2-centos lzx]# docker exec -it demo1 curl http://demo2:8088/
curl: (6) Could not resolve host: demo2; Unknown error
```

由此可见，`--link`参数是单向的，即当前启动的容器可以连接指向的容器，反过来不行。

### 8.3 宿主机共享网络

通过`端口映射`和`容器互联`机制，可以实现宿主机访问容器和容器之间相互访问。但还存在某种情况，需要容器访问宿主机，例如数据库部署在宿主机，而服务都通过容器启动的场景。

针对这种场景，需要先介绍下Docker的网络模式：

- host模式

Docker使用了Linux的Namespaces技术来进行资源隔离，如PID Namespace隔离进程，Mount Namespace隔离文件系统，Network Namespace隔离网络等。⼀个Network Namespace提供了⼀份独立的网络环境，包括网卡、路由、Iptable规则等都与其他的Network Namespace隔离。⼀个Docker容器⼀般会分配⼀个独立的Network Namespace，但如果启动容器的时候使用host模式，那么这个容器将不会获得⼀个独立的Network Namespace，而是和宿主机共用⼀个Network Namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。

Docker启动时通过`--net=host`指定此模式，可以看出，这个模式即可实现容器访问宿主机。

- container模式

这个模式指定创建的容器和已经存在的一个容器共享一个Network Namespace，而不是和宿主机共享。新创建的容器不回创建自己的网卡、配置自己的IP，而是和指定容器共享IP、端口范围等。同样，两个容器除了网络方面外，其他的如文件系统、进程列表是完全隔离的。

Docker启动时通过`--net=container`指定此模式。

- none模式

这种模式下，容器拥有自己的Network Namespace，但并不为容器配置网络，也就是说这个容器没有网卡、IP、路由等，适合隔离的网络环境，不与外部交互的进程。

Docker启动时通过`--net=none`指定此模式。

- bridge模式

此模式是Docker的默认模式，为每个容器分配Network Namespace，设置IP等，并将一个主机上的Docker容器连接到一个虚拟网桥上。

```shell
# host模式示例，容器访问宿主机的端口
# 修改之前的dockerfile文件，将容器的springboot端口设置为9091，避免和宿主机冲突
...........................................
WORKDIR /home/
CMD java -jar docker-1.0.0-SNAPSHOT.jar --server.port=9091
# 构建镜像
[root@VM-12-2-centos lzx]# docker build -f springboot-dockerfile -t springboot-img:v2 .
Sending build context to Docker daemon  817.6MB
Step 1/12 : FROM my-jdk8-img
 ---> 4eae70b95876
Step 2/12 : MAINTAINER lizhaoxuan
 ---> Using cache
 ---> 496f394030f8
Step 3/12 : COPY docker-1.0.0-SNAPSHOT.jar /home/
 ---> Using cache
 ---> 628f47da9b4b
Step 4/12 : RUN mkdir -p /home/java/
 ---> Using cache
 ---> d917db360c70
Step 5/12 : ADD jdk1.8.0_181.tar.gz /home/java/
 ---> Using cache
 ---> 45ceaf27f74b
Step 6/12 : ENV JAVA_HOME=/home/java/jdk1.8.0_181
 ---> Using cache
 ---> c2178bcef34a
Step 7/12 : ENV JRE_HOME=${JAVA_HOME}/jre
 ---> Using cache
 ---> 3ea094f59b10
Step 8/12 : ENV CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
 ---> Using cache
 ---> c4e23ac09b0c
Step 9/12 : ENV JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
 ---> Using cache
 ---> 685b64e11aa5
Step 10/12 : ENV PATH=$PATH:${JAVA_PATH}
 ---> Using cache
 ---> e6511fd0f1cc
Step 11/12 : WORKDIR /home/
 ---> Using cache
 ---> 7adbfb7b0688
Step 12/12 : CMD java -jar docker-1.0.0-SNAPSHOT.jar --server.port=9091
 ---> Running in 2a3213ef440f
Removing intermediate container 2a3213ef440f
 ---> 4354c697256d
Successfully built 4354c697256d
Successfully tagged springboot-img:v2
# 启动容器，配置host模式
[root@VM-12-2-centos lzx]# docker run -di --name=demo1 --net=host springboot-img:v2
2058aa722bd388d41e15fff47b2256d878ceec78e24b278b7c44289bfcf0d01e
[root@VM-12-2-centos lzx]# docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS        PORTS     NAMES
2058aa722bd3   springboot-img:v2   "/bin/sh -c 'java -j…"   2 seconds ago   Up 1 second             demo1
# 测试容器和宿主机相互访问
[root@VM-12-2-centos lzx]# netstat -tunlp | grep 9091
tcp6       0      0 :::9091                 :::*                    LISTEN      30830/java          
[root@VM-12-2-centos lzx]# netstat -tunlp | grep 8088
tcp6       0      0 :::8088                 :::*                    LISTEN      20026/java   
[root@VM-12-2-centos lzx]# ps -ef | grep docker
root     20026     1  0 May22 ?        00:02:48 java -jar docker-1.0.0-SNAPSHOT.jar
root     30830 30810 17 11:12 ?        00:00:05 java -jar docker-1.0.0-SNAPSHOT.jar --server.port=9091
[root@VM-12-2-centos lzx]# curl http://127.0.0.1:9091
hello docker!
[root@VM-12-2-centos lzx]# docker exec -it demo1 curl http://127.0.0.1:8088/
hello docker!
```

# 九：Docker资源隔离

### 9.1 Docker资源隔离介绍

Docker除了支持应用级别环境恢复外，还支持资源的限制与隔离。

Docker是通过内核的 `cgroups` 来做容器的资源限制，包括`CPU、内存、磁盘`三大方面。

注：cgroups是Linux的另外一个强大的内核工具，有了cgroups，不仅可以限制被namespace隔离起来的资源，还可以为资源设置权重、计算使用量、操控任务启停等。说白了就是：cgroups可以限制、记录任务组所使用的物理资源（包括CPU，Memory，IO等），是构建Docker等一系列虚拟化管理工具的基石。

### 9.2 内存限制

Docker 提供的内存限制功能有以下几点：

- 容器能使用的内存和交换分区大小
- 容器的核心内存大小
- 容器虚拟内存的交换行为
- 容器内存的软性限制
- 是否杀死占用过多内存的容器
- 容器被杀死的优先级

容器内存相关配置参数如下：

| 选项                 | 描述                                                       |
| -------------------- | ---------------------------------------------------------- |
| -m，--memory         | 内存限制，格式是数字加单位，单位可选b、k、m、g，最小为4M   |
| --memory-swap        | 内存+交换分区大小总限制，格式如上，必须大于-m参数          |
| --memory-reservation | 内存的软性限制，格式如上                                   |
| --oom-kill-disable   | 是否阻止OOM killer 杀死容器，默认不会                      |
| --oom-score-adj      | 容器被OOM killer 杀死的优先级，范围为[-1000,1000]，默认为0 |
| --memory-swappines   | 用于设置容器的虚拟内存控制行为，值为0-100之间              |
| --kernel-memory      | 核心内存限制，格式同上，最小为4M                           |

```shell
# 开启一个容器，不做内存限制
[root@VM-12-2-centos ~]# docker run -di --name centos1 centos:7
5a23dbbf3fbd545a032ea6932b274c35153a9066b0323dce20d7eaf4b0c889f1
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED         STATUS         PORTS     NAMES
5a23dbbf3fbd   centos:7   "/bin/bash"   5 seconds ago   Up 4 seconds             centos1
# 开启一个容器，内存限制为500M
[root@VM-12-2-centos ~]# docker run -di -m 500M --name centos2 centos:7
efd7239651f9db21cdebb0d839faf077a98bd8df49a2b70ae55b4e5216e40cfc
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED          STATUS          PORTS     NAMES
efd7239651f9   centos:7   "/bin/bash"   4 seconds ago    Up 4 seconds              centos2
5a23dbbf3fbd   centos:7   "/bin/bash"   24 seconds ago   Up 24 seconds             centos1
# 查看容器状态，不做限制时使用物理机所有内存
[root@VM-12-2-centos ~]# docker stats

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
efd7239651f9   centos2   0.00%     192KiB / 500MiB     0.04%     656B / 0B   0B / 0B     1
5a23dbbf3fbd   centos1   0.00%     188KiB / 3.7GiB     0.00%     656B / 0B   0B / 0B     1

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
efd7239651f9   centos2   0.00%     192KiB / 500MiB     0.04%     656B / 0B   0B / 0B     1
5a23dbbf3fbd   centos1   0.00%     188KiB / 3.7GiB     0.00%     656B / 0B   0B / 0B     1

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
efd7239651f9   centos2   0.00%     192KiB / 500MiB     0.04%     656B / 0B   0B / 0B     1
5a23dbbf3fbd   centos1   0.00%     188KiB / 3.7GiB     0.00%     656B / 0B   0B / 0B     1
```

### 9.3 CPU限制

Docker对容器CPU的限制有以下几点：

- 相对份额限制：多个容器竞争资源时根据比例获取
- 绝对限制：容器在固定周期内只能使用多久的CPU
- 绑定CPU核：给容器绑定某个或多个CPU核使用
- 混合限制：给容器绑定CPU核心，在基于绑定的核心进行相对份额限制和绝对限制

相关配置参数如下：

| 选项               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| --cpuset-cpus=""   | 允许使用的CPU集，值可以为0-3、0、1                           |
| --cpuset-mems=""   | 允许在上执行的内存节点（MEMs），只对NUMA系统有效             |
| --cpus=0.5         | 通过cpus设置一个小数，来表示要使用的CPU的最大限制，这个小数的区间为0.01~[最大核心数] |
| --cpu-quota=0      | 限制CPU CFS配额，必须不小于1ms，即>=1000                     |
| --cpu-period=0     | 限制CPU CFS的周期，范围从100ms-1s，即[1000,1000000]          |
| -c，--cpu-shares=0 | CPU共享权值，相对权重                                        |

```shell
# 开启一个容器，不做CPU限制
[root@VM-12-2-centos ~]# docker run -di --name centos1 centos:7
f60e692719e41c987d0a76c18e22d2b969964a0be683d291a1d45f2806281397
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED         STATUS         PORTS     NAMES
f60e692719e4   centos:7   "/bin/bash"   3 seconds ago   Up 2 seconds             centos1
# 开启一个容器，限制只能使用0.5核
[root@VM-12-2-centos ~]# docker run -di --cpus 0.5 --name centos2 centos:7
851955f711693e3795e22c423f8ea6306943dd023f6365ae379237a9305110d5
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED          STATUS          PORTS     NAMES
851955f71169   centos:7   "/bin/bash"   1 second ago     Up 1 second               centos2
f60e692719e4   centos:7   "/bin/bash"   34 seconds ago   Up 33 seconds             centos1
# 编写测试脚本如下
#!/bin/sh
count=4
for (( i=0; i<$count+1;i++ ))
do
	echo $i
	dd if=/dev/zero of=/dev/null &    
done
# 进入容器centos1，执行脚本
[root@VM-12-2-centos ~]# docker exec -it centos1 /bin/bash
[root@f60e692719e4 /]# 
[root@f60e692719e4 /]# vi cpu_test.sh
[root@f60e692719e4 /]# 
[root@f60e692719e4 /]# 
[root@f60e692719e4 /]# /bin/bash cpu_test.sh 
0
1
2
3
4
[root@f60e692719e4 /]# exit
# 查看容器CPU情况，物理机为2C4G，可以看到CPU已经全部耗尽
[root@VM-12-2-centos ~]# docker stats

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
851955f71169   centos2   0.00%     188KiB / 3.7GiB     0.00%     656B / 0B   0B / 0B     1
f60e692719e4   centos1   199.21%   708KiB / 3.7GiB     0.02%     656B / 0B   0B / 0B     6

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
851955f71169   centos2   0.00%     188KiB / 3.7GiB     0.00%     656B / 0B   0B / 0B     1
f60e692719e4   centos1   199.21%   708KiB / 3.7GiB     0.02%     656B / 0B   0B / 0B     6
# 停止容器centos1，开始测试centos2
[root@VM-12-2-centos ~]# docker stop f60e692719e4
f60e692719e4
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED         STATUS         PORTS     NAMES
851955f71169   centos:7   "/bin/bash"   3 minutes ago   Up 3 minutes             centos2
[root@VM-12-2-centos ~]# docker exec -it centos2 /bin/bash
[root@851955f71169 /]# vi cpu_test.sh
[root@851955f71169 /]# 
[root@851955f71169 /]# 
[root@851955f71169 /]# /bin/bash cpu_test.sh 
0
1
2
3
4
[root@851955f71169 /]# exit
exit
# 查看此时容器CPU使用和物理机CPU使用
[root@VM-12-2-centos ~]# docker stats

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
851955f71169   centos2   49.77%    712KiB / 3.7GiB     0.02%     656B / 0B   0B / 0B     6

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
851955f71169   centos2   49.77%    712KiB / 3.7GiB     0.02%     656B / 0B   0B / 0B     6

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
851955f71169   centos2   49.68%    712KiB / 3.7GiB     0.02%     656B / 0B   0B / 0B     6

[root@VM-12-2-centos ~]# top
top - 23:12:53 up 19 days, 13:20,  2 users,  load average: 0.86, 2.90, 3.32
Tasks: 100 total,   6 running,  94 sleeping,   0 stopped,   0 zombie
%Cpu(s):  6.5 us, 19.0 sy,  0.0 ni, 74.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3880180 total,   694392 free,   310196 used,  2875592 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  3271516 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                               
10839 root      20   0    4408    356    280 R  12.6  0.0   0:06.75 dd                                                                                                    
10840 root      20   0    4408    356    280 R  12.3  0.0   0:08.11 dd                                                                                                    
10838 root      20   0    4408    356    280 R   8.3  0.0   0:06.53 dd                                                                                                    
10841 root      20   0    4408    352    280 R   8.3  0.0   0:05.88 dd                                                                                                    
10842 root      20   0    4408    356    280 R   8.3  0.0   0:06.04 dd                                                                                                    
 1844 root      20   0 1029976  64572  14484 S   0.7  1.7 205:47.98 YDService                                                                                             
    9 root      20   0       0      0      0 S   0.3  0.0   7:52.04 rcu_sched                                                                                             
 1237 root      20   0   24576   6552   3660 S   0.3  0.2  34:03.10 tat_agent  
```

### 9.4 磁盘限制

容器对磁盘的限制主要支持以下参数：

| 选项                | 描述                                               |
| ------------------- | -------------------------------------------------- |
| --device-read-bps   | 限制容器对某个磁盘的读速度，单位可以是kb、mb或者gb |
| --device-read-iops  | 限制容器对某个磁盘每秒读的IO次数                   |
| --device-write-bps  | 限制容器对某个磁盘的写速度                         |
| --device-write-iops | 限制容器对某个磁盘每秒写的IO次数                   |
| --blkio-weight      | 设置各个容器之间的相对权重，默认为500              |

```shell
# 创建容器centos1，不做任何限制
[root@VM-12-2-centos ~]# docker run -di --name centos1 centos:7
3957e0e3968c6703b8a59d099707aaa9594b1275dde12438aacc42dfa0d7d6a9
# 创建容器centos2，限制/dev/vda的写入速度
[root@VM-12-2-centos ~]# docker run -di --name centos2 --device-write-bps /dev/vda:50MB centos:7
e87935e0be443348ca37e4df4de3eef46810c962674acaa5f0bcaaea99b12939
# 进入容器centos1，测试写入速度
[root@3957e0e3968c /]# time dd if=/dev/zero of=test.out bs=1M count=800 oflag=direct
800+0 records in
800+0 records out
838860800 bytes (839 MB) copied, 6.15525 s, 136 MB/s

real	0m6.157s
user	0m0.001s
sys	0m0.130s
# 进入容器centos2，测试写入速度
[root@ec084165fb0d /]# time dd if=/dev/zero of=test.out bs=1M count=800 oflag=direct
800+0 records in
800+0 records out
838860800 bytes (839 MB) copied, 15.9585 s, 52.6 MB/s

real	0m15.960s
user	0m0.000s
sys	0m0.137s
```

# 十：Docker运维

### 10.1 Docker私服

- Docker私服介绍

之前在拉取Docker镜像的时候，都是从Docker的官方仓库Docker Hub里拉取的，我们也可以将自己的镜像上传到官方仓库，但对于公司内部应用来说，需要搭建自己的私有化仓库，即Docker私服。两者之间的关系类似于GitHub和GitLab。

- Docker私服搭建

```shell
# 拉取私服的镜像
[root@VM-12-2-centos ~]# docker pull registry
Using default tag: latest
latest: Pulling from library/registry
df9b9388f04a: Pull complete 
52dc419b0ee2: Pull complete 
b6846b9db566: Pull complete 
b0a23bbf973d: Pull complete 
c50f110701a7: Pull complete 
Digest: sha256:dc3cdf6d35677b54288fe9f04c34f59e85463ea7510c2a9703195b63187a7487
Status: Downloaded newer image for registry:latest
docker.io/library/registry:latest
# 搭建私服
[root@VM-12-2-centos ~]# docker run -di --name=registry -p 5000:5000 registry
43583ad57e3652b12393f04cdb6071b0607ae7b1829801d990a20381a254022e
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS                                                                      NAMES
43583ad57e36   registry        "/entrypoint.sh /etc…"   3 seconds ago   Up 2 seconds   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp                                  registry
9bc7eadb3e12   my-centos-img   "/usr/sbin/init"         8 days ago      Up 8 days      0.0.0.0:442->22/tcp, :::442->22/tcp                                        my-centos
ce5f7383ae2a   nginx           "/docker-entrypoint.…"   8 days ago      Up 8 days      0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   web-server
f01f507bcef1   mysql:5.7       "docker-entrypoint.s…"   8 days ago      Up 8 days      0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp                       jolly_blackburn
69e5ce02db61   redis           "docker-entrypoint.s…"   2 weeks ago     Up 2 weeks     0.0.0.0:6380->6379/tcp, :::6380->6379/tcp                                  redis-server
[root@VM-12-2-centos ~]# 
# 私服测试，调用/v2/_catalog接口返回仓库所有镜像
[root@VM-12-2-centos ~]# curl http://127.0.0.1:5000/v2/_catalog
{"repositories":[]}
# Docker要求远程仓库都开启https访问，由于私服目前还未开启https，因此先配置docker信任此私服
[root@VM-12-2-centos ~]# vim /etc/docker/daemon.json
{"insecure-registries":["127.0.0.1:5000"]}
[root@VM-12-2-centos ~]# cat /etc/docker/daemon.json
{"insecure-registries":["127.0.0.1:5000"]}
# 重启Docker
[root@VM-12-2-centos ~]# systemctl restart  docker
```

- 上传镜像到Docker私服

```shell
# 启动私服：刚才重启Docker会关闭所有Docker容器
[root@VM-12-2-centos ~]# docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS                            PORTS     NAMES
43583ad57e36   registry        "/entrypoint.sh /etc…"   5 minutes ago   Exited (2) 2 minutes ago                    registry
9bc7eadb3e12   my-centos-img   "/usr/sbin/init"         8 days ago      Exited (137) About a minute ago             my-centos
e09d1c93c2d2   centos:7        "/bin/bash"              8 days ago      Created                                     optimistic_heisenberg
ce5f7383ae2a   nginx           "/docker-entrypoint.…"   8 days ago      Exited (0) 2 minutes ago                    web-server
f01f507bcef1   mysql:5.7       "docker-entrypoint.s…"   8 days ago      Exited (0) 2 minutes ago                    jolly_blackburn
69e5ce02db61   redis           "docker-entrypoint.s…"   2 weeks ago     Exited (0) 2 minutes ago                    redis-server
[root@VM-12-2-centos ~]# docker start registry
registry
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS         PORTS                                       NAMES
43583ad57e36   registry   "/entrypoint.sh /etc…"   6 minutes ago   Up 3 seconds   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   registry
# 上传镜像到私服
# 给镜像打标签，标记为私有仓库镜像
[root@VM-12-2-centos ~]# docker tag my-jdk8-img 127.0.0.1:5000/my-jdk8-img:v1
[root@VM-12-2-centos ~]# docker images
REPOSITORY                   TAG       IMAGE ID       CREATED         SIZE
127.0.0.1:5000/my-jdk8-img   v1        4eae70b95876   7 days ago      1.2GB
my-jdk8-img                  latest    4eae70b95876   7 days ago      1.2GB
my-centos-img                latest    a2812fd8436b   8 days ago      435MB
hello-world-x                latest    5aa5faee7296   2 weeks ago     13.3kB
redis                        latest    3c3da61c4be0   3 weeks ago     113MB
nginx                        latest    fa5269854a5e   3 weeks ago     142MB
mysql                        5.7       82d2d47667cf   3 weeks ago     450MB
registry                     latest    2e200967d166   5 weeks ago     24.2MB
hello-world                  latest    feb5d9fea6a5   7 months ago    13.3kB
centos                       7         eeb6ee3f44bd   8 months ago    204MB
tomcat                       7         9dfd74e6bc2f   10 months ago   533MB
redis                        6.0.8     16ecd2772934   18 months ago   104MB
# 上传镜像到远程的私服
[root@VM-12-2-centos ~]# docker push 127.0.0.1:5000/my-jdk8-img:v1
The push refers to repository [127.0.0.1:5000/my-jdk8-img]
ff0861fe9b39: Pushed 
3028613ebb16: Pushed 
174f56854903: Pushed 
v1: digest: sha256:f9e3abe5ddbd0fa793e0387d2f195669431f2ccdf69cefdd9f97d70e7738d68c size: 954
# 查看私服仓库所有镜像
[root@VM-12-2-centos ~]#  curl http://127.0.0.1:5000/v2/_catalog
{"repositories":["my-jdk8-img"]}
```

- 从Docker私服拉取镜像

```shell
# 删除本地镜像
[root@VM-12-2-centos ~]# docker rmi 127.0.0.1:5000/my-jdk8-img:v1
Untagged: 127.0.0.1:5000/my-jdk8-img:v1
Untagged: 127.0.0.1:5000/my-jdk8-img@sha256:f9e3abe5ddbd0fa793e0387d2f195669431f2ccdf69cefdd9f97d70e7738d68c
[root@VM-12-2-centos ~]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
tomcat          9         5e9a4d049871   2 days ago      680MB
my-jdk8-img     latest    4eae70b95876   7 days ago      1.2GB
my-centos-img   latest    a2812fd8436b   8 days ago      435MB
hello-world-x   latest    5aa5faee7296   2 weeks ago     13.3kB
redis           latest    3c3da61c4be0   3 weeks ago     113MB
nginx           latest    fa5269854a5e   3 weeks ago     142MB
mysql           5.7       82d2d47667cf   3 weeks ago     450MB
registry        latest    2e200967d166   5 weeks ago     24.2MB
hello-world     latest    feb5d9fea6a5   7 months ago    13.3kB
centos          7         eeb6ee3f44bd   8 months ago    204MB
tomcat          7         9dfd74e6bc2f   10 months ago   533MB
redis           6.0.8     16ecd2772934   18 months ago   104MB
# 私服拉取镜像
[root@VM-12-2-centos ~]# docker pull 127.0.0.1:5000/my-jdk8-img
Using default tag: latest
Error response from daemon: manifest for 127.0.0.1:5000/my-jdk8-img:latest not found: manifest unknown: manifest unknown
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker pull 127.0.0.1:5000/my-jdk8-img:v1
v1: Pulling from my-jdk8-img
Digest: sha256:f9e3abe5ddbd0fa793e0387d2f195669431f2ccdf69cefdd9f97d70e7738d68c
Status: Downloaded newer image for 127.0.0.1:5000/my-jdk8-img:v1
127.0.0.1:5000/my-jdk8-img:v1
[root@VM-12-2-centos ~]# docker images
REPOSITORY                   TAG       IMAGE ID       CREATED         SIZE
tomcat                       9         5e9a4d049871   2 days ago      680MB
127.0.0.1:5000/my-jdk8-img   v1        4eae70b95876   7 days ago      1.2GB
my-jdk8-img                  latest    4eae70b95876   7 days ago      1.2GB
my-centos-img                latest    a2812fd8436b   8 days ago      435MB
hello-world-x                latest    5aa5faee7296   2 weeks ago     13.3kB
redis                        latest    3c3da61c4be0   3 weeks ago     113MB
nginx                        latest    fa5269854a5e   3 weeks ago     142MB
mysql                        5.7       82d2d47667cf   3 weeks ago     450MB
registry                     latest    2e200967d166   5 weeks ago     24.2MB
hello-world                  latest    feb5d9fea6a5   7 months ago    13.3kB
centos                       7         eeb6ee3f44bd   8 months ago    204MB
tomcat                       7         9dfd74e6bc2f   10 months ago   533MB
redis                        6.0.8     16ecd2772934   18 months ago   104MB
```

- 私服可视化管理

```shell
# Docker提供了一个仓库可视化管理工具
# 拉取镜像
[root@VM-12-2-centos ~]# docker pull joxit/docker-registry-ui
Using default tag: latest
latest: Pulling from joxit/docker-registry-ui
df9b9388f04a: Already exists 
5867cba5fcbd: Pull complete 
4b639e65cb3b: Pull complete 
061ed9e2b976: Pull complete 
bc19f3e8eeb1: Pull complete 
4071be97c256: Pull complete 
4f4fb700ef54: Pull complete 
618e6efaf8a8: Pull complete 
5afc3e5df415: Pull complete 
a6368918034f: Pull complete 
1ebab13c87a5: Pull complete 
1b1a50956b80: Pull complete 
Digest: sha256:96720e723ab66a1121df9117746eca21a7c965e77d1e74bc1ec696dccc7091d4
Status: Downloaded newer image for joxit/docker-registry-ui:latest
docker.io/joxit/docker-registry-ui:latest
[root@VM-12-2-centos ~]# docker images
REPOSITORY                   TAG       IMAGE ID       CREATED         SIZE
tomcat                       9         5e9a4d049871   2 days ago      680MB
joxit/docker-registry-ui     latest    5c76608cdfec   7 days ago      27.2MB
my-jdk8-img                  latest    4eae70b95876   7 days ago      1.2GB
127.0.0.1:5000/my-jdk8-img   v1        4eae70b95876   7 days ago      1.2GB
my-centos-img                latest    a2812fd8436b   8 days ago      435MB
hello-world-x                latest    5aa5faee7296   2 weeks ago     13.3kB
redis                        latest    3c3da61c4be0   3 weeks ago     113MB
nginx                        latest    fa5269854a5e   3 weeks ago     142MB
mysql                        5.7       82d2d47667cf   3 weeks ago     450MB
registry                     latest    2e200967d166   5 weeks ago     24.2MB
hello-world                  latest    feb5d9fea6a5   7 months ago    13.3kB
centos                       7         eeb6ee3f44bd   8 months ago    204MB
tomcat                       7         9dfd74e6bc2f   10 months ago   533MB
redis                        6.0.8     16ecd2772934   18 months ago   104MB
# 启动UI服务
[root@VM-12-2-centos ~]# docker run -p 80:80 --name registry-ui --link registry -e REGISTRY_URL="http://127.0.0.1:5000" -e DELETE_IMAGES="true" -e REGISTRY_TITLE="Registry" -d joxit/docker-registry-ui
8dd39b4dd7af08e9d34f5da5e57686e0c4151809e66477715255648475810ab7
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                                       NAMES
8dd39b4dd7af   joxit/docker-registry-ui   "/docker-entrypoint.…"   2 seconds ago    Up 2 seconds    0.0.0.0:80->80/tcp, :::80->80/tcp           registry-ui
43583ad57e36   registry                   "/entrypoint.sh /etc…"   33 minutes ago   Up 27 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   registry
# 访问页面 ：http://124.222.146.210/
```

### 10.2 Docker可视化工具

当Docker容器和镜像比较多时，通过可视化工具进行管理会更为方便，Docker常用的可视化工具有：Portainer和Rancher。

#### 10.2.1 Portainer搭建

- 拉取镜像

```shell
[root@VM-12-2-centos ~]# docker pull portainer/portainer
Using default tag: latest
latest: Pulling from portainer/portainer
772227786281: Pull complete 
96fd13befc87: Pull complete 
8b2d9b141e4d: Pull complete 
Digest: sha256:25415d1143949e5dc0b03585365dc8bbe84f443ef116dc27719dc69f23ead35e
Status: Downloaded newer image for portainer/portainer:latest
docker.io/portainer/portainer:latest
```

- 创建容器

```shell
[root@VM-12-2-centos ~]#  docker run -d -p 443:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
3e23801787fd615a633ba67517ac870b16736805e0d8b65855563f09a61c7c00
[root@VM-12-2-centos ~]# 
[root@VM-12-2-centos ~]# docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                                       NAMES
3e23801787fd   portainer/portainer        "/portainer"             2 seconds ago    Up 1 second     0.0.0.0:443->9000/tcp, :::443->9000/tcp     wizardly_goldwasser
886df6dbc34d   joxit/docker-registry-ui   "/docker-entrypoint.…"   10 minutes ago   Up 10 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp           registry-web
43583ad57e36   registry                   "/entrypoint.sh /etc…"   49 minutes ago   Up 43 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   registry
```

- 访问Portainer可视化页面：http://124.222.146.230:443

安装完成后，Portainer会在443端口启动一个可视化管理界面，第一次访问需要设置密码，之后即可实现Docker可视化管理。

![image-20220514202052316](http://rocks526.top/lzx/image-20220514202052316.png)

Portainer可以管理多个Docker环境，可以选择本地或者远程的Docker环境进行连接，此处连接本地Docker，如下所示：

![image-20220514202519028](http://rocks526.top/lzx/image-20220514202519028.png)

每个Docker管理页面如下，可查看镜像、容器、网络、数据卷等信息，如下图所示：

![image-20220514202630058](http://rocks526.top/lzx/image-20220514202630058.png)