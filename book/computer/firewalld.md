# iptables 与 firewalld

保障数据的安全性是继保障数据的可用性之后最为重要的一项工作。防火墙作为公网与内网之间的保护屏障，在保障数据的安全性方面起着至关重要的作用。

```
firewalld 与 iptables
iptables
firewall-cmd
firewall-config
TCP Wrappers
```

在生产环境公网条件下，黑客丛生、罪恶漫天，企业会在公网和内网之间砌一座保护墙，这个就叫做防火墙，有软件和硬件之分，其原理都是依据策略对穿越防火墙自身的流量过滤。

```
centos7 　　firewalld
centos6 　　iptables
```

iptables 与 firewalld 都不是真正的防火墙， 它们都只是用来定义防火墙策略的防火墙管理工具，是一种服务。

## 策略和规则链

防火墙会从上至下的顺序来读取配置的策略规则，在找到匹配项后就立即结束匹配工作 并去执行匹配项中定义的行为(即放行或阻止)。如果在读取完所有的策略规则之后没有匹配 项，就去执行默认的策略。一般而言，防火墙策略规则的设置有两种:一种是“通”(即放行)， 一种是“堵”(即阻止)。当防火墙的默认策略为拒绝时(堵)，就要设置允许规则(通)，否则 谁都进不来;如果防火墙的默认策略为允许时，就要设置拒绝规则，否则谁都能进来，防火墙 也就失去了防范的作用。

iptables 服务把用于处理或过滤流量的策略条目称之为规则，多条规则可以组成一个规则 链，而规则链则依据数据包处理位置的不同进行分类，具体如下:

在进行路由选择前处理数据包(PREROUTING);

- ➢ 处理流入的数据包(INPUT); 　　最长用的规则链
- ➢ 处理流出的数据包(OUTPUT);
- ➢ 处理转发的数据包(FORWARD);
- ➢ 在进行路由选择后处理数据包(POSTROUTING)。

## firewalld 与 iptables 的比较

1. firewalld可以动态修改单条规则，动态管理规则集，允许更新规则而不破坏现有会话和连接。而iptables，在修改了规则后必须得全部刷新才可以生效；
1. firewalld使用区域和服务而不是链式规则；
1. firewalld默认是拒绝的，需要设置以后才能放行。而iptables默认是允许的，需要拒绝的才去限制；
1. firewalld自身并不具备防火墙的功能，而是和iptables一样需要通过内核的netfilter来实现。也就是说，firewalld和iptables一样，它们的作用都用于维护规则，而真正使用规则干活的是内核的netfilter。只不过firewalld和iptables的结果以及使用方法不一样！
1. firewalld是iptables的一个封装，可以让你更容易地管理iptables规则。它并不是iptables的替代品，虽然iptables命令仍可用于firewalld，但建议firewalld时仅使用firewalld命令。

## firewalld

firewalld自身并不具备防火墙的功能，而是和iptables一样需要通过内核的netfilter来实现，也就是说firewalld和 iptables一样，他们的作用都是用于维护规则，而真正使用规则干活的是内核的netfilter，只不过firewalld和iptables的结构以及使用方法不一样罢了。

### 区域管理

通过将网络划分成不同的区域，制定出不同区域之间的访问控制策略来控制不同程序区域间传送的数据流。例如，互联网是不可信任的区域，而内部网络是高度信任的区域。网络安全模型可以在安装，初次启动和首次建立网络连接时选择初始化。该模型描述了主机所连接的整个网络环境的可信级别，并定义了新连接的处理方式。有如下几种不同的初始化区域：

- 阻塞区域（block）：任何传入的网络数据包都将被阻止。
- 工作区域（work）：相信网络上的其他计算机，不会损害你的计算机。
- 家庭区域（home）：相信网络上的其他计算机，不会损害你的计算机。
- 公共区域（public）：不相信网络上的任何计算机，只有选择接受传入的网络连接。
- 隔离区域（DMZ）：隔离区域也称为非军事区域，内外网络之间增加的一层网络，起到缓冲作用。对于隔离区域，只有选择接受传入的网络连接。
- 信任区域（trusted）：所有的网络连接都可以接受。
- 丢弃区域（drop）：任何传入的网络连接都被拒绝。
- 内部区域（internal）：信任网络上的其他计算机，不会损害你的计算机。只有选择接受传入的网络连接。
- 外部区域（external）：不相信网络上的其他计算机，不会损害你的计算机。只有选择接受传入的网络连接。

注：FirewallD的默认区域是public。

firewalld默认提供了九个zone配置文件：block.xml、dmz.xml、drop.xml、external.xml、 home.xml、internal.xml、public.xml、trusted.xml、work.xml，他们都保存在“/usr/lib /firewalld/zones/”目录下。

### 配置方法

firewalld的配置方法主要有三种：firewall-config、firewall-cmd和直接编辑xml文件，其中 firewall-config是图形化工具，firewall-cmd是命令行工具，而对于linux来说大家应该更习惯使用命令行方式的操作，所以 firewall-config我们就不给大家介绍了。

### firewalld 操作

```bash
# 启动
systemctl start  firewalld

# 查看状态 or
systemctl status firewalld

# 查看状态
firewall-cmd --state

# 禁用
systemctl disable firewalld

# 停止
systemctl stop firewalld

# 查询端口是否开放
firewall-cmd --query-port=8080/tcp

# 开放80端口
firewall-cmd --permanent --add-port=80/tcp

# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp

# 重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload
```

## iptables

iptables 是与最新的 3.5 版本 Linux 内核集成的 IP 信息包过滤系统。

```bash
# 查看防火墙状态
service iptables status

# 停止防火墙
service iptables stop  

# 启动防火墙
service iptables start  

# 重启防火墙
service iptables restart  

# 永久关闭防火墙
chkconfig iptables off  

# 永久关闭后重启
chkconfig iptables on
```
