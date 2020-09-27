# Docker 学习笔记

## 登录 docker login

```bash
docker login quay.io/alvinhtml/test
# 输入 username
# 输入 password
```

## docker images

```bash
docker images
```

## docker pull

```bash
docker pull springcloud/eureka
```


## docker tag

```bash
docker tag springcloud/eureka:latest 192.168.14.3:443/library/eureka:latest
````

## docker push

```bash
docker push 192.168.14.3:443/library/eureka
```


## docker ps 列出容器

语法：
```bash
docker ps [OPTIONS]
```
OPTIONS说明：

参数|说明
-:|-
-a |显示所有的容器，包括未运行的。
-f |根据条件过滤显示的内容。
--format |指定返回值的模板文件。
-l |显示最近创建的容器。
-n |列出最近创建的n个容器。
--no-trunc |不截断输出。
-q |静默模式，只显示容器编号。
-s |显示总的文件大小。



## Docker run 命令

创建一个新的容器并运行一个命令，语法：

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

OPTIONS说明：

参数|说明
-:|-
-a stdin|指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
-d|后台运行容器，并返回容器ID；
-i|以交互模式运行容器，通常与 -t 同时使用；
-p|端口映射，格式为：主机(宿主)端口:容器端口
-t|为容器重新分配一个伪输入终端，通常与 -i 同时使用；
--name="nginx-lb"|为容器指定一个名称；
--dns 8.8.8.8|指定容器使用的DNS服务器，默认和宿主一致；
--dns-search example.com|指定容器DNS搜索域名，默认和宿主一致；
-h "mars"|指定容器的hostname；
-e username="ritchie"|设置环境变量；
--env-file=[]|从指定文件读入环境变量；
--cpuset="0-2" or --cpuset="0,1,2"|绑定容器到指定CPU运行；
-m :设置容器使用内存最大值；
--net="bridge"|指定容器的网络连接类型，支持 bridge/host/none/container|四种类型；
--link=[]|添加链接到另一个容器；
--expose=[]|开放一个端口或一组端口；


### 运行一个容器
docker run --name=node8 -v $PWD:/app -p 7070:7070 -w /app -it node:8 bash
docker run --name=rancher-node8 -w /app -v $PWD:/app -p 8000:8000 -it bash


### 启动一个 Docker 容器

```bash
docker start containername
```

### 运行 docker 下的 bash

```bash
docker exec -it containername bash
```

### 退出 docker 下的 bash

```bash
exit;
```

## docker build

```
docker build .
```

```bash
docker build -f Dockerfile.debug .
```

## Dokcerfile

```yml
FROM node:8

WORKDIR /app

RUN \
  git clone https://github.com/facebook/watchman.git &&\
  cd watchman &&\
  git checkout v4.9.0 &&\
  ./autogen.sh &&\
  ./configure &&\
  make &&\
  make install
```

## 批量删除容器

删除 docker container 和 docker image 的关键都是找到准确的 ID。所以，如果能批量提供 ID 号给删除命令，就能实现批量删除了。

### 批量获取容器 ID 和镜像 ID

获取容器 ID：

```bash
docker container ls -a -q
2de6da0c4bd4
13a281a930ae
e9a95cf3b698
122f540ed44b
0913aa6263b1
0422216a248b
491a5136409c
c6c6373a3e50
d048cd4c405b
```

获取镜像 ID ：

```bash
docker image ls -a -q
d499bdb8fd09
6fb74f33a5e1
4c0a26e51da8
2b37f252629b
2817cb463960
eb516548c180
a89b45f36d5e
d531d047a4e2
643c21638c1c
```

### 批量停止和删除容器

批量停止容器 ：

```bash
docker stop $(docker container ls -a -q)
```

批量删除容器 ：

```bash
docker rm $(docker container ls -a -q)
```
批量删除镜像 ：

```bash
docker rmi $(docker image ls -a -q)
```

如果只想批量删除一部分容器或者镜像呢？这个需求也很容易实现，想象如下的简单场景：将要删除的容器名写到一个文档，然后根据提供的名字，再结合 grep 、awk 等命令，就可以轻松地获取准确的 ID
