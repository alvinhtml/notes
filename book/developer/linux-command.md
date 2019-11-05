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

## sysctl 内核与模块管理

sysctl命令被用于在内核运行时动态地修改内核的运行参数

```
sysctl(选项)(参数)

选项：
-n：打印值时不打印关键字；
-e：忽略未知关键字错误；
-N：仅打印名称；
-w：当改变sysctl设置时使用此项；
-p：从配置文件“/etc/sysctl.conf”加载内核参数设置；
-a：打印当前所有可用的内核参数变量和值；
-A：以表格方式打印当前所有可用的内核参数变量和值。

参数：
变量=值：设置内核参数对应的变量值。
```

## telnet

```
telnet [-8acdEfFKLrx][-b<主机别名>][-e<脱离字符>][-k<域名>][-l<用户名称>][-n<记录文件>][-S<服务类型>][-X<认证形态>][主机名称或IP地址<通信端口>]
```

```
-8 允许使用8位字符资料，包括输入与输出。
-a 尝试自动登入远端系统。
-b<主机别名> 使用别名指定远端主机名称。
-c 不读取用户专属目录里的.telnetrc文件。
-d 启动排错模式。
-e<脱离字符> 设置脱离字符。
-E 滤除脱离字符。
-f 此参数的效果和指定"-F"参数相同。
-F 使用Kerberos V5认证时，加上此参数可把本地主机的认证数据上传到远端主机。
-k<域名> 使用Kerberos认证时，加上此参数让远端主机采用指定的领域名，而非该主机的域名。
-K 不自动登入远端主机。
-l<用户名称> 指定要登入远端主机的用户名称。
-L 允许输出8位字符资料。
-n<记录文件> 指定文件记录相关信息。
-r 使用类似rlogin指令的用户界面。
-S<服务类型> 设置telnet连线所需的IP TOS信息。
-x 假设主机有支持数据加密的功能，就使用它。
-X<认证形态> 关闭指定的认证形态。
```

```bash
telnet 65.49.195.225 8358
```

## nc

```
nc [-hlnruz][-g<网关...>][-G<指向器数目>][-i<延迟秒数>][-o<输出文件>][-p<通信端口>][-s<来源位址>][-v...][-w<超时秒数>][主机名称][通信端口...]
```

```bash
nc -v -w2 65.49.195.225 8350-8359
```


## md5

```bash
md5 ./filename.txt
```
