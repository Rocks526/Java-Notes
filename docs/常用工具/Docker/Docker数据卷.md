# Docker数据卷介绍

> 为了实现数据库的数据备份，多容器数据共享等需求，Docker提供文件共享机制。

卷技术：通过目录的**挂载**，将容器内的目录挂载在宿主机上面。

1. 通过挂载技术，我们修改挂载在本地的文件，数据会自动同步到容器内；

2. 删除容器，容器挂载到本地的目录和数据不会删除；

3. 容器之间存在数据共享的技术，多个容器可以将目录挂载到宿主机的同一个目录，容器内产生数据，数据就会同步到本地，然后传递到其他容器。

# Docker数据卷的使用方式

- 使用-v参数设置挂载

```shell
docker run -it -v 宿主机目录:容器内目录 镜像名
```

- Docker的挂载方式分类

Docker的数据卷挂载分为匿名挂载和具名挂载，一般都采用具名挂载。

> 匿名挂载，默认宿主机位置/var/lib/volumes

```shell
-v 容器内路径    # 没有指定宿主机路径，是匿名挂载
-v 卷名:容器内路径  # 具名挂载
-v 宿主机路径:容器内路径  # 指定路径挂载
```

> 通过ro，rw参数可改变docker容器对数据卷的读写权限
>
> ro  ==>  readonly   # 只读权限
> rw  ==>  readwriter  # 读写权限

```shell
docker run -d -p --name nginx01 -v jm-nginx:/etc/nginx:ro nginx   # ro设置容器只读权限，只能从宿主机修改
```

如果要授权一个容器访问另一个容器的Volume，我们可以使用-volumes-from参数来执行启动容器。

docker容器数据共享的本质是：共享卷双向拷贝，互相备份，数据共享。

>  子容器继承父容器，将父容器删除，数据也不会丢失。