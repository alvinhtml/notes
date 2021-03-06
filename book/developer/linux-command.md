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

# 查看当前安装的版本及位置
ls -l `which node`
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

## grep

```
grep [选项] 搜索内容 文件名
```

选项说明


```
-A n：n为数字，列出符合条件的行，并列出后续的n行。
-B n：n为数字，列出符合条件的行，并列出前面的n行。
-c：统计找到的符合条件的字符串的次数。
-i：忽略大小写。
-n：输出行号。
-v：反向査找，也就是查询没有关键字的一行。
--color=auto：搜索出的关键字用颜色显示。
```


## lsof

查看端口被哪个进程占用

```bash
lsof -i:8081
```

## echo

将 `123` 追加到 `a.txt`

```bash
echo '123' >> a.txt
# or
cat circleci-rsa.pub >> authorized_keys
```

## cat

合并多个文件到一个文件

```bash
cat b1.sql b2.sql b3.sql > b_all.sql

# 或者

cat *.sql > merge.sql
```


## Linux 查看内存使用情况

### top: 用于实时显示 process 的动态
### free: 查看系统内存使用情况
### cat /proc/meminfo: 查看RAM使用情况最简单的方法是通过/proc/meminfo

这个动态更新的虚拟文件实际上是许多其他内存相关工具(如：free / ps / top)等的组合显示。

`/proc/meminfo` 列出了所有你想了解的内存的使用情况。

进程的内存使用信息也可以通过 `/proc/<pid>/statm` 和 `/proc/<pid>/status` 来查看。

## tree

```sh
tree -a  #显示所有
tree -d  #仅显示目录

tree -L n  #n代表数字..表示要显示几层

tree -f  #显示完整路径

tree -P .DS_Store/ -a # / 表示不显示
```

- \-a 显示所有文件和目录。
- \-A 使用ASNI绘图字符显示树状图而非以ASCII字符组合。
- \-C 在文件和目录清单加上色彩，便于区分各种类型。
- \-d 显示目录名称而非内容。
- \-D 列出文件或目录的更改时间。
- \-f 在每个文件或目录之前，显示完整的相对路径名称。
- \-F 在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"\*","/","=","@","|"号。
- \-g 列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码。
- \-i 不以阶梯状列出文件或目录名称。
- \-L level 限制目录显示层级。
- \-l 如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录。
- \-n 不在文件和目录清单加上色彩。
- \-N 直接列出文件和目录名称，包括控制字符。
- \-p 列出权限标示。
- \-P<范本样式> 只显示符合范本样式的文件或目录名称。
- \-q 用"?"号取代控制字符，列出文件和目录名称。
- \-s 列出文件或目录大小。
- \-t 用文件和目录的更改时间排序。
- \-u 列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码。
- \-x 将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该子目录予以排除在寻找范围外。

## compgen

查看可用命令列表

```bash
compgen -c
```

## poweroff

强制关机

## history

```bash
history

# 执行一条历史命令
!112
```

## fdisk

```bash
# 显示磁盘分区
fdisk -l
```

## tail

```
tail [参数] [文件]

参数：

-f 循环读取
-q 不显示处理信息
-v 显示详细的处理信息
-c<数目> 显示的字节数
-n<行数> 显示文件的尾部 n 行内容
--pid=PID 与-f合用,表示在进程ID,PID死掉之后结束
-q, --quiet, --silent 从不输出给出文件名的首部
-s, --sleep-interval=S 与-f合用,表示在每次反复的间隔休眠S秒
```

```
tail -100 | grep error
```

## cat /etc/shells

```sh
# 查看系统有几个shell
cat /etc/shells

# 使用下面命令设置默认shell
chsh -s /bin/zsh
```

## 自定义shell
