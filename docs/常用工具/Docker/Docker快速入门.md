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

```

- 创建容器

```shell

```

- 访问测试







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
```













# 六：Dockerfile







# 七：Docker数据卷







# 八：Docker网络







# 九：Docker资源隔离







# 十：Docker运维

### 10.1 Docker私服





### 10.2 Docker可视化工具





