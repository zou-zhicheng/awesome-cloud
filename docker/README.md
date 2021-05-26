# 书籍: 每天5分钟玩转docker容器技术

## 简介

[《每天5分钟玩转Docker容器技术》(CloudMan)【摘要 书评 试读】- 京东图书 (jd.com)](https://item.jd.com/12200103.html)

## 第1章: 鸟瞰容器生态系统

安装

[Install Docker Engine on Ubuntu | Docker Documentation](https://docs.docker.com/engine/install/ubuntu/)

测试

```shell
james@james-ubuntu20:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete
Digest: sha256:5122f6204b6a3596e048758cabba3c46b1c937a46b5be6225b835d091b90e46c
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```



```shell
james@james-ubuntu20:~$ sudo docker run -d -p 80:80 httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
69692152171a: Pull complete
7284b4e0cc7b: Pull complete
3678b2d55ccd: Pull complete
ab492cf0b2a4: Pull complete
991f7f97a9d8: Pull complete
Digest: sha256:e4c2b93c04762468a6cce6d507d94def02ef4dc285278d0d926e09827f4857db
Status: Downloaded newer image for httpd:latest
f6d0d9ae2dc71afc376320778cf52382a02e232d22ca93c4e19b4975fff95a51

# 访问
james@james-ubuntu20:~$ curl http://localhost:80
<html><body><h1>It works!</h1></body></html>
james@james-ubuntu20:~$ curl http://10.20.21.253:80
<html><body><h1>It works!</h1></body></html>
```

## 第2章: 容器核心知识概述

## 第3章: Docker镜像

```shell
# 默认在当前目录查找Dockerfile文件
docker build -t ubuntu-with-vim .
```

查看镜像分层

```shell
docker history ubuntu
```

常用指令

- FROM

- MAINTAINER

- COPY

  - COPY src dst
  - COPY["src", "dst"]

- ADD

  如果文件是归档文件， 会自动解压到dst

- ENV

- EXPOSE

- VOLUMN

- WORKDIR

- RUN

- CMD

- ENTRYPOINT

docker tag

docker push

docker rmi

只能删除host上的镜像

docker search

## 第4章: Docker容器

```shell
docker run ubuntu pwd
docker ps
docker ps -a

# 让容器长期运行
docker run ubuntu /bin/bash -c "while true; do sleep; done"
# -d
docker run --name "myhttpd" -d httpd
docker stop myhttpd

# 进入容器
docker attach [docker_id]
docker exec -it [docker_id] bash

# 查看启动命令输出
docker logs -f [docker_id]

docker stop [docker]
docker kill [docker]
docker start 
docker restart

# 出现错误自动重启
docker run -d --restart=always httpd

docker pause xxx
docker unpause xxx
# 删除容器
docker rm [container]
# 删除镜像
docker rmi [image]

# 内存限额
docker run -m 200M --memory-swap=300M ubuntu --vm 1 --vm-bytes 280M

# cpu限额
# -c --cpu-shares
# 设置容器使用cpu的权重, 默认1024
# top 查看cpu使用情况
docker run --name "dockerA" -c 1024 ubuntu
docker run --name "dockerB" -c 512 ubuntu

# BlockIO带宽限额
# 通过设置 权重 / 限制bps / 限制iops 控制容器读写磁盘的带宽
# blkio默认500
docker run --name "dockerA" --blkio-weight 600 ubuntu
docker run --name "dockerB" --blkio-weight 300 ubuntu
# --device-read-bps
# --device-write-bps
# --device-read-iops
# --device-write-iops
docker run -it --device-write-bps /dev/sda:30M ubuntu
# 系统中通过dd进行测试
```

### cgroup

### namespace

- mount
- uts
- ipc
- pid
- network
- username

## 第5章: docker网络

```shell
# 查看网络
james@james-ubuntu20:~$ sudo docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
a21694510504   bridge    bridge    local
e4e9f610f0f0   host      host      local
98342a511f2c   none      null      local

# none网络
james@james-ubuntu20:~$ sudo docker run -it --network=none busybox
/ # ifconfig
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

# host网络
# 与host网络完全一致
james@james-ubuntu20:~$ sudo docker run -it --network=host busybox

# bridge网络
james@james-ubuntu20:~$ brctl show
bridge name	bridge id		STP enabled	interfaces
docker0		8000.024290c8335d	no
# 运行容器
james@james-ubuntu20:~$ sudo docker run -d ubuntu
# 再次查看
james@james-ubuntu20:~$ brctl show
bridge name	bridge id		STP enabled	interfaces
docker0		8000.024290c8335d	no		vethaabaf54
# 查看docker
james@james-ubuntu20:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS     NAMES
80d20bffcb92   httpd     "httpd-foreground"   55 seconds ago   Up 54 seconds   80/tcp    admiring_dirac
# 进入docker查看
```

### user-defined网络

## 第6章: Docker存储

### storage driver



### volume container

## 第9章: 容器监控

### sysdig

```shell
# 启动
docker container run -it --rm --name sysdig --privileged=true --volume=/host/var/run/docker.sock:/var/run/docker.sock --volume=/dev:/host/dev --volume=/proc:/host/proc:ro --volume=/boot:/host/boot:ro --volume=/lib/modules:/host/lib/modules:ro --volume=/usr:/host/usr:ro sysdig/sysdig

# 进入容器
docker container exec -it sysdig bash
# 执行csysdig命令
csysdig
```

### weavescope

### cAdvisor

```shell
docker run --volume=/:/rootfs:ro --volume=/var/run:/var/run:rw --volume=/sys:/sys:ro --volume=/var/lib/docker:/var/lib/docker:ro --publish=8080:8080 --detach=true --name=cadvisor google/cadvisor:latest
```



