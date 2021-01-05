# Docker网络介绍

> Docker安装好之后，会有一个默认的docker0网络，容器之间就是通过这个网络进行通信。

![image-20210105105228007](http://rocks526.top/lzx/image-20210105105228007.png)

在平时部署项目时，大量的互联网应用服务包括多个服务组件，这往往需要多个容器之间通过网络通信进行相互配合。

Docker目前提供了映射容器端口到宿主主机和容器互联机制来为容器提供网络服务。

# 容器端口映射方式

- 拉取nginx镜像

```shell
docker image pull nginx
```

- 启动Nginx，通过-p参数指定容器内端口和容器外端口的映射

```shell
docker run -di --name=nginx-1 -p 80:80 nginx
```

- 端口连通性测试

```shell
curl localhost:80
```

![image-20210105105654859](http://rocks526.top/lzx/image-20210105105654859.png)

- 通过-P指定容器端口映射到宿主机随机端口

```shell
docker run -di --name=nginx-2 -P nginx
```

- 查看宿主机映射出来的端口

```shell
docker ps
```

- 端口连通性测试

```shell
curl localhost:49153
```

![image-20210105105916191](http://rocks526.top/lzx/image-20210105105916191.png)

- 测试两个容器的网络联通性

```shell
# 查看两个容器的ip  分别是172.17.0.3和172.17.0.4
docker inspect nginx-1
docker inspect nginx-2
# 安装ping相关命令
apt-get update
apt install iputils-ping 
# 测试网络连通性  58d13b693f93是nginx-1的容器ID  172.17.0.4是nginx-2的容器IP
docker exec -it 58d13b693f93 ping 172.17.0.4   # 可以连通
docker exec -it 58d13b693f93 ping nginx-2   # 无法连通
```

![image-20210105111201236](http://rocks526.top/lzx/image-20210105111201236.png)

- 此时查看宿主机网络

```shell
ifconfig 
# 宿主机网络多了几个veth网卡
# Docker是通过docker0和veth网卡实现容器之间的互通的 并且每个容器没有配置hosts文件，只能根据ip连接
```

![image-20210105111512188](http://rocks526.top/lzx/image-20210105111512188.png)

# 容器互联机制

- 清除所有容器

```shell
docker rm -f $(docker ps -aq)
```

- ​	创建容器

```shell
# 创建nginx-1 nginx-2 nginx-2连通nginx-1
docker run -di --name=nginx-1 nginx
docker run -di --name-nginx-2 --link=nginx-1 nginx
```

- 测试连通性

```shell
# nginx2可以通过容器名连接nginx1
docker exec -it nginx-1 ping nginx-2
docker exec -it nginx-2 ping nginx-1
```

![image-20210105114037963](http://rocks526.top/lzx/image-20210105114037963.png)

# 自定义Docker网络

> 查看Docker所有网络：docker network ls
>
> bridge：桥接模式(默认)
>
> none：不配置网络
>
> host：和宿主机共享网络

![image-20210105104840122](http://rocks526.top/lzx/image-20210105104840122.png)

> docker network常用命令：docker network --help

![image-20210105104923318](http://rocks526.top/lzx/image-20210105104923318.png)

> 创建一个Docker网络

```shell
# driver网络模式  subnet子网  gateway网关 mynet是自定义的网络名
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
```

![image-20210105115434170](http://rocks526.top/lzx/image-20210105115434170.png)

> 使用自定义的网络创建容器

```shell
# 使用自定义网络
docker run -di --network=mynet --name=nginx-1 nginx
# 使用原网络
docker run -di --network=bridge --name=nginx-2 nginx
# 查看ip  nginx-1是192.168.0.2  nginx-2是172.17.0.2
docker inspect nginx-1
docker inspect nginx-2
```

![image-20210105115840170](http://rocks526.top/lzx/image-20210105115840170.png)

> 测试连通性

```shell
# 由于nginx-1和nginx-2使用的是不同网络 无法连通
docker exec -it nginx-1 ping 172.17.0.2
```

![image-20210105120124931](http://rocks526.top/lzx/image-20210105120124931.png)

> 通过--link连通不同网络的容器

```shell
# 通过link连接不同网络的容器，会直接报错
docker run -di --name=nginx-3 --link=nginx-1 nginx
```

![image-20210105120251153](http://rocks526.top/lzx/image-20210105120251153.png)

> 将容器加入某个网络中

```shell
# 将nginx-2加入mynet网络
docker network connect mynet nginx-2
# 查看nginx-2容器的网络信息 发现多了一块网卡
docker inspect nginx-2
```

![image-20210105140701997](http://rocks526.top/lzx/image-20210105140701997.png)

![image-20210105140755385](http://rocks526.top/lzx/image-20210105140755385.png)

> 测试两个容器的连通性

```shell
# 两个容器连通 并且可以通过容器名访问
docker exec -it nginx-2 ping nginx-1
docker exec -it nginx-1 ping nginx-2
```

![image-20210105141211254](http://rocks526.top/lzx/image-20210105141211254.png)

> 容器退出一个网络或者删除一个网络参考docker network --help命令

# Docker网络总结

- Docker安装好后自带docker0网络，用来完成容器和宿主机之间和容器和容器之间的网络连通性
- 创建容器时，默认都会使用docker0端口，因此所有容器都处于一个子网内，相互可以连通，如果想要集群处于不同的网络中，需要自定义网络
- 通过-p参数可以将容器端口映射到制定宿主机端口，-P将容器端口与宿主机随机端口连通
- 虽然创建容器时默认使用docker0端口，所有容器处于一个网络可以相互连通，但只能通过ip方式访问，无法通过容器名访问
- 通过--link方式可以将一个容器与另一个容器连通，同时会配置hosts文件，可以通过容器名访问
- 自定义的网络支持通过容器名进行访问

