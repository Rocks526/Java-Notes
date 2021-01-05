# 搭建Docker私有仓库

Docker类似于Git，除了Docker Hub之外，也可以搭建远程私服。

### 搭建私有仓库

- 拉取仓库镜像

```shell
docker pull registry
```

- 启动私有仓库容器

```shell
docker run -di --name=rocks_registry -p 80:5000 registry
```

- 宿主浏览器访问http://localhost:80/v2/_catalog，返回JSON内容是仓库里的镜像

  ![image-20210105144231263](http://rocks526.top/lzx/image-20210105144231263.png)

![image-20200907173602378](http://rocks526.top/lzx/image-20200907173602378.png)

### 添加远程私有仓库授信

- Linux修改/etc/docker/daemon.json文件，加入：{"insecure-registries":["172.19.197.200:80"]}

> 注意！！是daemon而不是deamon
>
> 如果没有daemon.json文件则新建并写入

![image-20210105142601426](http://rocks526.top/lzx/image-20210105142601426.png)

![image-20210105145020103](http://rocks526.top/lzx/image-20210105145020103.png)

- Windows直接在设置中修改即可，加入：{"insecure-registries":["172.19.197.200:80"]}
- 修改完成后重启Docker即可从远程私服拉镜像

> docker启动命令,docker重启命令,docker关闭命令
>
> 启动    sudo systemctl start docker
> 守护进程重启  sudo systemctl daemon-reload
> 重启docker服务  sudo systemctl restart  docker
> 重启docker服务  sudo service docker restart
> 关闭docker  sudo service docker stop  
> 关闭docker  sudo systemctl stop docker

### 上传镜像到远程私有仓库

- 给镜像打标签，标记为私有仓库镜像

```shell
docker tag nginx 172.19.197.200:80/nginx:v1
```

![image-20210105144543995](http://rocks526.top/lzx/image-20210105144543995.png)

- 启动远程私服容器

```shell
docker start rocks_registry
```

- 上传镜像到远程的私服

```shell
docker push 172.19.197.200:80/nginx:v1 
```

![image-20210105145543419](http://rocks526.top/lzx/image-20210105145543419.png)

### 从远程私有仓库拉取镜像

- 删除本地镜像，然后从远程拉取

```shell
docker rmi -f 172.19.197.200:80/nginx:v1
docker pull 172.19.197.200:80/nginx:v1
```

![image-20210105145912375](http://rocks526.top/lzx/image-20210105145912375.png)