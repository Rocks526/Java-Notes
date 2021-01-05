# Docker部署Mysql

- 拉取Mysql镜像

> docker pull mysql

- 创建MySQL容器并映射3306端口，设置MySQL默认密码

> docker run -di --name=docker_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql