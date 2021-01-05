# Docker部署Flask

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