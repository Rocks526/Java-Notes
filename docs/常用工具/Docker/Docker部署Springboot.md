# Docker部署SpringBoot

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