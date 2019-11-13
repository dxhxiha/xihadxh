docker



拓扑

![26bd9ab01ef3dd3b9cab4e96b51f79f](C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\WeChat Files\26bd9ab01ef3dd3b9cab4e96b51f79f.jpg)

![cac80a89c82ab7d428f292434de208e](C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\WeChat Files\cac80a89c82ab7d428f292434de208e.jpg)





docker的安装与部署

安装docker是最少centos7.4版本以上

使用centos7.8

mirrors.aliyun.com



下载的镜像一般在

quay.io     hub.docker.com



centos 7.7下载地址

 https://mirrors.aliyun.com/centos/7.7.1908/isos/x86_64/CentOS-7-x86_64-DVD-1908.iso 



vim /etc/yum.repos.d/yum.repo

![1573434689472](C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Roaming\Typora\typora-user-images\1573434689472.png)



 yum -y install docker-ce



## 做加速器

### （个人旧版）是在阿里云上做的

echo '{"registry-mirrors": ["https://gm9yu60g.mirror.aliyuncs.com"]}' >> /etc/docker/daemon.json



### 在官网上做

（）注册----  找火箭头

daocloud.io     

 `curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io` 



然后重启docker服务 

systemctl restart docker



docker  search  搜索镜像

docker  rmi     删除镜像

docker   pull    下载镜像

docker   save  -o  自命名   镜像名称       镜像的导出

docker   load  <   自命名     镜像的导入

docker  tag    镜像名称    改名镜像

docker  run  -it   centos:latest   /bin/bash

docker exec -it   镜像id

yum  provides  ifconfig  

yum   -y install   net-tools



查看正在运行的容器

docker ps 

查看所有的容器

docker ps  -a





vim  /usr/bin/docker-enter

```
#!/bin/sh
if [ -e $(dirname "$0")/nsenter ]; then

  # with boot2docker, nsenter is not in the PATH but it is in the same folder

  NSENTER=$(dirname "$0")/nsenter
else
  NSENTER=nsenter
fi
if [ -z "$1" ]; then
  echo "Usage: `basename "$0"` CONTAINER [COMMAND [ARG]...]"
  echo ""
  echo "Enters the Docker CONTAINER and executes the specified COMMAND."
  echo "If COMMAND is not specified, runs an interactive shell in CONTAINER."
else
  PID=$(docker inspect --format "{{.State.Pid}}" "$1")
  if [ -z "$PID" ]; then
    exit 1
  fi
  shift
  OPTS="--target $PID --mount --uts --ipc --net --pid --"
  if [ -z "$1" ]; then
    # No command given.
    # Use su to clear all host environment variables except for TERM,
    # initialize the environment variables HOME, SHELL, USER, LOGNAME, PATH,
    # and start a login shell.
    "$NSENTER" $OPTS su - root
  else
    # Use env to clear all host environment variables.
    "$NSENTER" $OPTS env --ignore-environment -- "$@"
  fi
fi
```

chmod  +x /usr/bin/docker-enter



直接docker-enter   加容器ID即可



把运行的容器打包成镜像

docker commit   web  centos-nginx:v1.0.0



把运行的容器打包成模板



docker  export web  > newcentos.tar

导入镜像

cat  newcentos.tar  |  docker  import  - newcentos.tar 







# 实验



轮训调度nginx

![img](file:///C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\ksohtml12536\wps1.png)

 

在已安装的docker

 

 

实验环境：拖入需要的nginx软件包

一台主机（已安装docker）

 

创建容器：docker run -itd --name web1 --restart always centos:latest /bin/bash

进入容器：docker -it exec web1 /bin/bash

 

安装依赖环境

yum -y install iproute openssh-clients zlib pcre pcre-devel gcc gcc-c++  net-tools make openssl openssl-devel tar vim 

 

cd /

tar -zxvf nginx-1.6.0.tar.gz 

cd nginx-1.6.0

useradd -M -s /sbin/nologin nginx

./configure  --prefix=/usr/local/nginx --user=nginx --group=nginx && make && make install

ln -s /usr/local/nginx/sbin/* /usr/local/sbin/

 

编辑

vim /usr/local/nginx/html/index.html  里面内容替换为

hello world!!!!!!!

 

访问

![img](file:///C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\ksohtml12536\wps2.jpg) 

 

exit

 

把运行的容器打包成镜像

docker commit  web1  centos-nginx:v1.0 

 

创建容器

docker run -itd --name web2 centos-nginx:v1.0 /bin/bash

 

 

进入容器

docker exec -it web2 /bin/bash

 

nginx

 

vim /usr/local/nginx/html/index.html  替换

welcome to hourse!!

 

访问

![img](file:///C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\ksohtml12536\wps3.jpg) 

 

 Exit

 

创建容器

docker run -itd --name web3 centos-nginx:v1.0 /bin/bash

 

 

进入容器

docker exec -it web3 /bin/bash

 

nginx

 

vim /usr/local/nginx/html/index.html  替换

xixixixi  hahahaha

 

访问

![img](file:///C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\ksohtml12536\wps4.jpg) 

 

 exit

 

 yum -y install  pcre pcre-devel openssl openssl-devel zlib 

 tar -zxvf nginx-1.6.0.tar.gz

 cd nginx-1.6.0/

 useradd -M  -s /sbin/nologin nginx

 ./configure --prefix=/usr/local/nginx --user=nginx --group=nginx && make && make install 

 

ln -s /usr/local/nginx/sbin/* /usr/local/sbin/

 

vim /usr/local/nginx/conf/nginx.conf

![img](file:///C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\ksohtml12536\wps5.jpg) 

 

 

启动服务：  nginx

 

访问主机

![img](file:///C:\Users\Administrator.DESKTOP-Q44R3VB\AppData\Local\Temp\ksohtml12536\wps6.jpg) 

 

 

 





























