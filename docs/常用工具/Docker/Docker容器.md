# Docker容器介绍

容器是Docker的另一个核心概念。简单地说，容器是镜像的一个运行实例，所不同的是，它带有额外的可写文件层。

如果认为虚拟机是模拟运行的一整套操作系统（提供了运行态环境和其他系统环境）和跑在上面的应用。那么Docker容器就是独立运行的一个或一组应用，以及它们的必需运行环境。

# 容器常用命令

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
  - docker attach 容器id 也可以进入容器，会使用之前启动的窗口
- 停止容器

docker stop 容器名称:容器标签

- 退出容器
  - exit：直接退出并停止容器
  - ctrl + P + Q：容器不停止退出

- 启动容器

docker start 容器名称:容器标签

- 文件拷贝

docker cp 宿主文件 容器目录

- 查看容器信息
  - docker inspect 容器ID/容器名称
  - 通过--format参数可以筛选容器信息
    - 例如：docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器名称（容器ID）输出容器IP信息
- 删除容器

docker rm 容器ID/容器名

docker rm -f $(docker ps -aq)  删除所有容器

- 查看容器内进程信息

docker top 容器id

- 查看容器日志信息

docker logs -tf -detail 10 容器ID：查看10行详细信息