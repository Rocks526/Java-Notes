- [Docker介绍](#js)
- [Docker安装](#az)
- [Docker常用命令](#cyml)
- [Docker应用部署](#yybs)
- [Docker备份与迁移](#bf)
- [Docker私有仓库](#syck)
- [Dockerfile](#dockerfile)
- [Docker Compose](#compose)
- [Docker Swarm](#swarm)

- [K8s](#k8s)

------

# <a id="js">Docker介绍</a>

### 什么是Docker

Docker 是一个开源项目，诞生于 2013 年初，最初是 dotCloud 公司内部的一个业余项目。它基于 Google 公司推出的 Go 语言实现。 项目后来加入了 Linux 基金会，遵从了 Apache 2.0 协议，项目代码在 [GitHub](https://github.com/docker/docker) 上进行维护。

![image-20200827154711068](.\images\image-20200827154711068.png)

Docker 自开源后受到广泛的关注和讨论，以至于 dotCloud 公司后来都改名为 Docker Inc。Redhat 已经在其 RHEL6.5 中集中支持 Docker；Google 也在其 PaaS 产品中广泛应用。

Docker 项目的目标是实现轻量级的操作系统虚拟化解决方案。 Docker 的基础是 Linux 容器（LXC）等技术。

在 LXC 的基础上 Docker 进行了进一步的封装，让用户不需要去关心容器的管理，使得操作更为简便。用户操作 Docker 的容器就像操作一个快速轻量级的虚拟机一样简单。

### Docker特点

- 上手快

​	用户只需要几分钟，就可以把自己的程序“Docker化”。Docker依赖于“写时复制”（copy-on-write）模型，使修改应用程序也非常迅速，可以说达到“随心所致，代码即改”的境界。	

         随后，就可以创建容器来运行应用程序了。大多数Docker容器只需要不到1秒中即可启动。由于去除了管理程序的开销，Docker容器拥有很高的性能，同时同一台宿主机中也可以运行更多的容器，使用户尽可能的充分利用系统资源。

- 职责的逻辑分类

​	使用Docker，开发人员只需要关心容器中运行的应用程序，而运维人员只需要关心如何管理容器。Docker设计的目的就是要加强开发人员写代码的开发环境与应用程序要部署的生产环境一致性。从而降低那种“开发时一切正常，肯定是运维的问题（测试环境都是正常的，上线后出了问题就归结为肯定是运维的问题）”

- 快速高效的开发生命周期

​	Docker的目标之一就是缩短代码从开发、测试到部署、上线运行的周期，让你的应用程序具备可移植性，易于构建，并易于协作。（通俗一点说，Docker就像一个盒子，里面可以装很多物件，如果需要这些物件的可以直接将该大盒子拿走，而不需要从该盒子中一件件的取。）

- 鼓励使用面向服务的架构

​	Docker还鼓励面向服务的体系结构和微服务架构。Docker推荐单个容器只运行一个应用程序或进程，这样就形成了一个分布式的应用程序模型，在这种模型下，应用程序或者服务都可以表示为一系列内部互联的容器，从而使分布式部署应用程序，扩展或调试应用程序都变得非常简单，同时也提高了程序的内省性。（当然，可以在一个容器中运行多个应用程序）

### Docker与虚拟机的区别

- 不做硬件的隔离，更加轻量级
- 可以将应用环境一块复制打包，还原相同环境

### Docker组件

> Docker是一个CS架构程序，服务端有一个Deamon线程，客户端可以通过命令行和本地服务端或远程服务端通信

- 镜像

镜像是构建Docker的基石。用户基于镜像来运行自己的容器。

可以将镜像当作容器的“源代码”。镜像体积很小，非常“便携”，易于分享、存储和更新。

- 容器

类似于一个虚拟机，提供与本机隔离的运行环境

- 注册中心

Docker用Registry来保存用户构建的镜像。Registry分为公共和私有两种。

Docker公司运营公共的Registry叫做Docker Hub。用户可以在Docker Hub注册账号，分享并保存自己的镜像

私有的注册中心类似于GitLab或者自己的Maven私服

> 国内Docker仓库：https://docker.mirrors.ustc.edu.cn

# <a id="az">Docker安装</a>

### Window安装Docker

- 下载地址：https://www.docker.com/
- 下载Windows的Docker安装程序安装即可

> Windows提供了Docker的可视化管理面板，第一次需要构建一个容器才能顺利进入，可以通过引导或者cmd构建
>
> 在Settings里可以设置Docker占用内存，DockerHub等

### Linux安装Docker

- 安装Docker需要的依赖包

> sudo yum install -y yum-utils device-mapper-persistent-data lvm2

- 设置yum的源

> sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

- 安装Docker

> sudo yum install docker-ce

- 检查Docker版本

> docker -v

# <a id="cyml">Docker常用命令</a>

### 镜像常用命令

- 查看镜像

docker images

> 返回结果解析：
>
> REPOSITORY：镜像名称
>
> TAG：镜像标签
>
> IMAGE ID：镜像ID
>
> CREATED：镜像的创建日期（不是获取该镜像的日期）
>
> SIZE：镜像大小

- 搜索镜像

docker search 镜像名称

> 返回结果解析：
>
> NAME：仓库名称/镜像名称
>
> DESCRIPTION：镜像描述
>
> STARS：用户评价，反应一个镜像的受欢迎程度
>
> OFFICIAL：是否官方
>
> AUTOMATED：自动构建，表示该镜像由Docker Hub自动构建流程创建的

- 拉取镜像

docker pull 镜像名称:镜像标签

例如：docker pull centos:7

- 删除镜像

docker rmi 镜像ID或镜像名称:镜像标签

例如：docker rmi centos:7

> 删除所有镜像：docker rmi \`docker images -q`
>
> docker images -q命令是返回所有镜像ID
>
> -f参数强制删除

### 容器常用命令

- 查看容器
  - 查看正在运行的容器：docker ps
  - 查看所有容器：docker ps -a
  - 查看最后一次运行的容器：docker ps -l
  - 查看停止的容器：docker ps -f status=exited
- 创建和启动容器
  - docker run
  - 常用参数
    - -i：运行容器
    - -t：交互式进入容器
    - -d：后台启动容器
    - --name：为创建的容器命名
    - -v：目录映射，可以做多个目录映射
    - -p：端口映射
  - 交互式进入容器后，exit退出容器
  - 进入后台运行的容器：docker exec -it 容器ID或容器名称:容器标签 /bin/bash
- 停止容器

docker stop 容器名称:容器标签

- 启动容器

docker start 容器名称:容器标签

- 文件拷贝

docker cp 宿主文件 容器目录

- 查看容器信息
  - docker inspect 容器ID/容器名称
  - 通过--format参数可以筛选容器信息
    - 例如：docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器名称（容器ID）输出容器IP信息
- 删除容器

docker rmi 容器ID/容器名

# <a id="yybs">Docker常用应用部署</a>

- Docker部署Nginx

  - 拉取Nginx镜像

    > Docker pull Nginx:1.8.0

  - 创建Nginx容器并映射80端口

    > docker run -di --name=mynginx -p 80:80 nginx:1.8.0

- Docker部署Mysql

  - 拉取Mysql镜像

  > docker pull mysql

  - 创建MySQL容器并映射3306端口，设置MySQL默认密码

  > docker run -di --name=docker_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql

- Docker部署Redis

  - 拉取Redis镜像

  > docker pull redis

  - 创建Redis容器并映射6379端口

  > docker run -di --name=myredis -p 6379:6379 redis

- Docker部署Flask

  - 拉取CentOS镜像

  > Docker pull centos:7

  - 运行CentOS容器并映射88端口

  > docker run -di -p 88:80 --name=flask-demo centos:7 

  - 安装Python3环境和相关依赖

  > yum install python3
  >
  > pip3 install flask
  >
  > pip3 install gunicorn

  - 运行flask程序

  > python3 flask_demo.py --host 0.0.0.0 --port 80
  >
  > gunicorn -w 4 -d -b 0.0.0.0:80 flask_demo:app

- Docker部署SpringBoot

  - 拉取jdk镜像

  > docker pull openjdk:8

  - 运行容器并开放8080端口

  > docker run -di --name=springboot-docker -p 8080:8080 openjdk:8

  - 将springboot项目打出jar包，复制到容器里

  > docker cp .\springboot-docker-0.0.1-SNAPSHOT.jar springboot-docker:/home/

  - 进入容器，启动springboot项目

  > docker exec -it springboot-docker /bin/bash
  >
  > cd /home
  >
  > java -jar springboot-docker-0.0.1-SNAPSHOT.jar

# <a id="bf">Docker备份与迁移</a>

- 容器导出镜像

docker commit 容器名字 导出的镜像名

> 例如：docker commit flask_demo flask_demo
>
> 容器不需要启动也可以导出镜像

- 镜像备份

docker save -o 保存的文件名 镜像名

> 例如：docker flask_demo_iamge.tar flask_demo

- 镜像恢复

docker load -i 镜像文件的名字

> 例如：docker load flask_demo_image.tar

# <a id="syck">Docker私有仓库</a>

- 搭建私有仓库

  - 拉取仓库的镜像

    >  docker pull registry

  - 启动私有仓库容器

    > docker run -di --name=rocks_registry -p 5000:5000 registry

  - 宿主浏览器访问http://localhost:5000/v2/_catalog

    > 返回JSON内容是仓库里的镜像

![image-20200827161644649](.\images\image-20200827161644649.png)

- 添加远程私有仓库授信
  - Linux修改/etc/docker/deamon.json文件，加入：{"insecure-registries":["IP:5000"]}
  - Windows直接在设置中修改即可，加入：{"insecure-registries":["IP:5000"]}
  - 修改完成后重启Docker即可从远程私服拉镜像

- 上传镜像到远程私有仓库

  - 给镜像打标签，标记为私有仓库镜像

    > docker tag redis 10.91.130.221:5000/redis:v1

  - 启动远程私服容器

    > docker start rocks_registry

  - 上传镜像到远程的私服

    > docker push 10.91.130.221:5000/redis:v1

- 从远程私有仓库拉取镜像

docker pull 10.91.130.221:5000/redis:v1

# <a id="dockerfile">Dockerfile</a>









# <a id="compose">Docker Compose</a>





# <a id="swarm">Docker Swarm</a>





# <a id="k8s">K8s</a>

