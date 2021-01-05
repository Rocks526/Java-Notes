# Docker部署Nginx

- 拉取Nginx镜像

  > Docker pull Nginx:1.8.0

- 创建Nginx容器并映射80端口

  > docker run -di --name=mynginx -p 80:80 nginx:1.8.0