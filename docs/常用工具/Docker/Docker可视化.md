# 可视化管理工具

> 当部署Docker容器较多时，需要一个可视化的管理工具，了解拉取下来的所有镜像和启动的容器。常用的可视化工具有两个：Portainer和Rancher

### Portainer

- 安装

```shell
 docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

安装完成后，Portainer会在8088端口启动一个可视化管理界面，第一次访问需要设置密码，之后即可实现Docker可视化管理。

![image-20210102020033461](http://rocks526.top/lzx/image-20210102020033461.png)

![image-20210102020049406](http://rocks526.top/lzx/image-20210102020049406.png)

### Rancher

