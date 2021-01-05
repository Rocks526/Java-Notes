# Docker部署Redis

- 拉取Redis镜像

> docker pull redis

- 创建Redis容器并映射6379端口

> docker run -di --name=myredis -p 6379:6379 redis