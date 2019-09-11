# Linux 常用命令学习

## rsync

rsync的目的是实现本地主机和远程主机上的文件同步(包括本地推到远程，远程拉到本地两种同步方式)，也可以实现本地不同路径下文件的同步，但不能实现远程路径1到远程路径2之间的同步(scp可以实现)。

rsync 具有如下的基本特性：

* 可以镜像保存整个目录树和文件系统
* 可以很容易做到保持原来文件的权限、时间、软硬链接等
* 无须特殊权限即可安装
* 优化的流程，文件传输效率高
* 可以使用 rsh、ssh 方式来传输文件，当然也可以通过直接的 socket 连接
* 支持匿名传输，以方便进行网站镜象

无论本地同步目录还是远程同步数据，首次运行时将会把全部文件拷贝一次，以后再运行时将只拷贝有变化的文件（对于新文件）或文件的变化部分（对于原有文件）。

本节重点介绍 rsync 客户命令的使用，有关 rsync 服务器的配置和使用请参见下节。

rsync 在首次复制时没有速度优势，速度不如 tar，因此当数据量很大时您可以考虑先使用 tar 进行首次复制，然后再使用 rsync 进行数据同步。

### rsync 用法

rsync 是一个功能非常强大的工具，其命令也有很多功能选项。
```bash
# 在本地同步, 同步目录需加 -r
rsync -r docker/a/ docker/b/

# 将本地 docker/a/ 目录拷贝到远程主机的 alvinhtml/ 下，以保证远程目录和本地 docker/a/ 保持同步           
rsync -r -e 'ssh -p 29771' docker/a/ root@65.49.195.225:/home/wwwroot/alvinhtml/

# 将远程主机的 docker/a/ 目录拷贝到本地 alvinhtml/ 下，以保证本地目录和远程 docker/a/ 保持同步    
rsync -r -e 'ssh -p 29771' root@65.49.195.225:/home/wwwroot/alvinhtml/  docker/a/

# 列出本地 docker/a/ 目录下的文件列表    
rsync docker/a/

# 列出远程主机上 /home/wwwroot/alvinhtml/ 目录下的文件列表                     
rsync -e 'ssh -p 29771' root@65.49.195.225:/home/wwwroot/alvinhtml/           
```




## yum

使用 `yum` 安装和更新软件包      
```bash
# 列出所有可更新的软件清单
yum check-update

# 安装所有更新软件
yum update

# 仅安装指定的软件
yum install <package_name>

# 仅更新指定的软件
yum update <package_name>        

# 用YUM删除软件包
yum remove <package_name>
```



## which

which 命令的原理：在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。

```bash
which npm
```

## env

显示当前环境变量



## iproute2

iproute2是linux下管理控制TCP/IP网络和流量控制的新一代工具包，旨在替代老派的工具链net-tools，即大家比较熟悉的ifconfig，arp，route，netstat等命令。net-tools通过procfs(/proc)和ioctl系统调用去访问和改变内核网络配置，而iproute2则通过netlink套接字接口与内核通讯。


### ip地址管理

#### 1.显示ip地址

```bash
ip a
ip address show
ip addr show dev eth0
ip a sh eth0
```

#### 2.增加删除地址

```bash
ip address add 192.0.2.1/24 dev eth0
ip addr del 192.0.2.2/24 dev eth0
```

#### 3.显示接口统计

```bash
ip -s link ls eth0
```

### 网卡和链路配置

#### 4.显示链路

```bash
ip link show
ip link sh eth0
```

#### 5.修改接口状态

```
ip link set eth0 up
ip link s gre01 down
```

### 路由表管理

#### 6.显示路由表

```bash
ip route
ip ro show dev gre01
```

#### 7.增加新路由

```bash
ip route add 10.2.2.128/27 dev gre01
```

#### 8.增加默认路由

```bash
ip route add default via 192.168.1.1
```


#### 9.修改默认路由

```bash
ip route chg default via 192.168.1.2
```


#### 10.删除默认路由

```bash
ip route del default
```
