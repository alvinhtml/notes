# V2Ray 完全使用教程

V2Ray一个优秀开源网络代理工具，可帮你畅爽体验互联网，目前已经全平台支持 Windows、Mac、Android、IOS、Linux 等操作系统的使用。相对起 Shadowsocks 来说属于后起之秀，在混淆能力、兼容性、速度上有着独到的优点。在目前 Shadowsocks、ShadowsocksR 停止更新的情况下，多掌握一个活跃项目工具的使用是完全有必要的。

## 前言

本文最后一次更新时间为：2020-2-25

本文起初于2016年，时至如今收到很多读者的反馈，经过不断的完善字数也从一两千到现在的一万三千字！这种完善也产生了副产品，许多章节并不是每个人都需要的，即使经过结构化的梳理对他们来说仍会产生的干扰和混淆。

于是我重新写了一篇教程，不做过多的描述，仅写最基础的使用和操作。我知道你不关心它是什么和为什么，只关心怎么做才能正常的访问 Google，那么请移步看我新写 面向新手的V2Ray搭建指南 这篇对新手更友好的教程。

我将尽量避免这两篇文章的重复，所以那篇文章只会讲述最基本的安装和使用，这一篇文章才涉及到配置讲解，所以那篇文章未涉及到的内容，你可以综合两篇文章一起看。

## V2Ray（Project V）介绍

Project V 包含一系列工具，帮助你打造专属的定制网络体系。

### v2ray.com

和Shadowsocks一样，V2Ray的运行需要服务端和客户端的相互支持，也就是说除了你正在使用的设备，你还需要配置一个境外远程服务器。这个过程并不是很简单，需要付出你的一点点努力才能完成，所以我在这里分享出我的使用经验，与君共勉。

### 和Shadowsocks有什么区别

V2Ray 更像是一个集成工具，它集合了多种多样的协议和功能，对个人用户而言像是一个工具箱，可以使用各种各样的工具组合。对开发者而言像是一个脚手架，可以在此基础上开发扩充自己需要的功能而节省开发时间。

总而言之大体上的道理是相同的，你若是需要个性化的功能，那么需要更复杂的配置，若是需要最基本、普遍的功能，只需要在已有基础上稍作修改即可。

## 所需工具：

### 境外VPS

任何境外 VPS 都可以，一般而言香港、台湾、新加坡、韩国、日本等亚洲机房速度（延迟小）最快，但价格贵并且由于用的人多经常会被重点关照。无论如何，如果预算充足并追求速度可以选择这些机房，但需要提前了解测试线路是否是直连中国，一些线路可能会绕美国。

美国VPS价格低廉宽带足，其中的洛杉矶(Los Angeles)、西雅图(Seattle)两个机房对中国物理距离最近，这两个机房为首选。

由于用洛杉矶和西雅图机房的人太多，这两个机房的许多 IP 已经被 BAN 了，或者速度十分缓慢，当你这两地的机房却无法连接或速度缓慢，请考虑连接其他机房，如纽约(New York)、迈阿密(Miami)。

退一步说话，就目前的网络优化技术下（推荐BBR），延迟对实际感觉到的网速而言存在感并不够强烈，所以更值得注意掉包率。最差的情况下（网络优化前），延迟不高过350，掉包率不超过15%，这样就已经能满足大多数人的网络体验了。

所以选购VPS时，大多数人并不需要太过追求于日本、香港线路，追求各种CN2优化的极致速度，大多数VPS实际上就已经能满足需求了，前提是开启BBR优化，这一点很重要。

## 耐心 && 加油

若有 Linux 系统（如 Ubuntu） 使用经验，那么这个过程对你来说是轻而易举。即使你没有太多电脑基础也没有关系，跟着本文进行操作，遇到不懂的问题善于使用搜索，或者在本文下方的评论区留言求助，相信机智的你可以轻松搞定。

### 写给新手

一些新手遇到了问题往往不知道如何解决，并且求助于我并解决问题后，发现很多问题所在都是配置不按照教程走（或许是从别处看的有失误的配置）。本文在下方手动安装环节展示了完整的服务端配置，并且在下方 3-3Linux 章节（客户端配置均通用，Windows用户可直接复制粘贴）中展示了完整的客户端配置。

💡当你无法理解配置的含义时，完全跟着教程的配置是最保险的选择，成功的运行是一个大前提。当你已经掌握用法并且想要更深一步的折腾时，可以跟着官方教程一步一步摸索。

另外由于 V2Ray 的更新，个别配置可能会产生变动或者客户端与服务端的不兼容，所以为了避免不必要的麻烦，请总是使用 V2Ray 最新版本的客户端和服务端。

#### 我可以使用其他系统吗？比如 Centos

是的你可以使用，但是许多新手并不清楚 Centos 默认开启防火墙，这意味着即使你安装好 V2Ray 并运行后仍然无法进行连接，因为防火墙阻断了他们之间的连接。

当你并不熟悉 Centos 时，我建议关闭系统防火墙，因为当你不熟悉系统操作时，防火墙给你带来的负担远大于好处。如果你确定要关闭防火墙，那么运行下面两个命令即可：

```
#停止firewall
systemctl stop firewalld

#禁止firewall开机启动
systemctl disable firewalld
```

## 服务端搭建过程

开始讲解在VPS上部署V2Ray的过程。大多数情况下推荐使用自动安装脚本，当你无法使用脚本安装成功后，可以跟随 2-2部分内容手动安装。

要进行下面的搭建过程当然需要先 ssh 连接我们预先购买的 VPS，如果你当前使用的是 Win10 系统，不妨使用自带的 ssh 客户端用于连接远程主机。使用方法参考：使用Win10内置SSH客户端

### 自动安装脚本（推荐）

V2Ray 官方维护并提供了适用于大多数主流系统的自动安装脚本，只需一行命令即可完成安装，当你想要更新V2Ray 的时候同样只需要运行下面一行命令。（谨慎使用第三方的安装脚本）
```bash
bash <(curl -L -s https://install.direct/go.sh)
# 上列命令会自动安装 V2Ray，然后执行下面命令运行
systemctl start v2ray
```

安装完成后会新增下列文件：

```
/usr/bin/v2ray/v2ctl：V2Ray 工具，用于给程序自身调用
/usr/bin/v2ray/v2ray：V2Ray 核心程序
/etc/v2ray/config.json：配置文件
/usr/bin/v2ray/geoip.dat：IP 数据文件，V2Ray 路由功能时有用，下同
/usr/bin/v2ray/geosite.dat：域名数据文件
```

脚本安装完成无误后，可以进行如下操作：

1. 编辑 vim /etc/v2ray/config.json 文件来配置你需要的代理方式；
1. 运行 systemctl start v2ray 来启动 V2Ray 进程；
1. 之后可以使用 systemctl start|restart|stop|status v2ray 控制 V2Ray 的运行。

以上命令均需要 root 权限。对于第一步编辑配置文件，可以改动端口为三位数或更小的数字（如443端口）对速度也许有所改善，这是一种玄学 : ) 。

当你使用自动脚本安装结束后，就可以说服务端的部署已经完成了，自动脚本不仅安装了 V2Ray，还在配置中随机生成了一个 5 位数端口以及 UUID 供我们直接使用，所以我们无需进一步配置服务端，我们已经获得三个必要的信息了：IP、端口（Port）、id（UUID）。

若需要支持 Shadowsocks，则需要进一步配置，可以参考下面章节 2-2手动安装 中的部分。V2Ray 服务端支持同时配置多种协议，你可以在一个端口配置Vmess协议，一个端口配置 Shadowsocks 协议。配置 Shadowsocks 协议后，你也可以使用其他 SS 客户端而非一定要使用 V2Ray 的客户端。

### 手动安装

手动安装和自动安装达到的效果基本一致，但是许多脚本自动完成的地方却需要我们自己操作，不建议自己手动安装，仅仅在自动安装失败后再尝试。

#### 下载与安装

首先 SSH 连接上自己的 VPS，然后到 Releases 找到适合自己 VPS 系统的版本，在下载链接上右键复制链接。


大多数情况下，VPS的CPU都是Inter架构，而系统位数则已经在购买VPS时就已经选择了，所以下列查询架构和运行模式的部分只在不确定的时候再进行。

那么如何判断 CPU 型号以及运行模式（32 还是 64）呢？可以通过命令很方便的查询，比如来查询 CPU 运行模式：

```bash
getconf LONG_BIT
# 32
```
返回的 32 表明运行模式为 32 位，查询 CPU 型号也只需要一行命令：

```bash
lscpu | grep "Model name"
# Model name: 2 Intel(R) Xeon(R) CPU E5620 @ 2.40GHz
```

下列命令是下载 64 位版本的 V2Ray 主要过程，随后进行详细说明（这里的配置文件“vpoint_vmess_freedom.json”对应自动安装脚本中的 /etc/v2ray/config.json，二者完全相同）：

```bash
# 创建一个目录并进入
mkdir v2ray;cd v2ray
# 下载压缩包
wget https://github.com/v2ray/v2ray-core/releases/download/v4.16.0/v2ray-linux-64.zip
# 解压压缩包
unzip v2ray*.zip
# 复制安装包中的配置文件到 /etc/v2ray 目录中
cp vpoint_vmess_freedom.json /etc/v2ray/config.json
# 创建一个目录用来储存日志文件
mkdir /var/log/v2ray/
# 运行，默认在 /etc/v2ray/config.json 寻找配置文件
./v2ray
```
不出意外就能看到下面的界面：

为了方便接下来的使用，我们还是按照文件的存放约定把配置文件放到 etc 路径下（这里忽略官方的示例配置文件，使用文章下方的配置,这里仅仅创建一个文件），可执行文件文件放到 /usr/bin 路径下：

```bash
# 创建目录
sudo mkdir /etc/v2ray /usr/bin/v2ray /var/log/v2ray
# 赋予可执行权限
sudo chmod +x v2ray v2ctl
# 移动文件
sudo mv v2ray v2ctl geoip.dat geosite.dat -t /usr/bin/v2ray
# 仅创建配置文件的空文件
touch /etc/v2ray/config.json
# 用于程序开机启动并且崩溃时自动重启程序
sudo mv systemd/v2ray.service /etc/systemd/system/
sudo systemctl enable v2ray
```

OK，经过上列的操作，我们的 V2Ray 和用脚本自动安装的达到的效果已经基本一致了，注意此时的最后一条命令是让 V2Ray 永久运行并且开机启动，但是执行此命令时并未让它启动（还没有配置 config.json 当然不会启动）。

我们需要接着下面的步骤来修改我们的配置文件，每次配置改动都需要重启程序使其生效，执行命令：

```bash
sudo systemctl restart v2ray
```

## 配置 V2Ray

通过下方命令编辑配置文件：

```bash
vim /etc/v2ray/config.json
```

服务端配置如下：

```js
{
  "inbounds": [
    {
      "port": 443, // Vmess 协议服务器监听端口
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b528e293-5979-408f-98de-a031564d1d6d" // id(UUID) 需要修改
          }
        ]
      }
    },
    {
      "port": 444, // SS 协议服务端监听端口
      "protocol": "shadowsocks",
      "settings": {
        "method": "aes-128-gcm", // 加密方式
        "password": "lanfenger8" //密码
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

以上配置了两种协议，Vmess 在 443 端口，Shadowsocks 在 444 端口，也就是说你可以一个客户端使用 Vmess 协议，另一个客户端使用 Shadowsocks，当然你闲置一个不用也无所谓。

对于上述配置而言，最少只需要修改 Vmess 的 UUID 和 Shadowsocks 的密码就可以直接使用了。

### Vmess

换掉 ID 可以使用 [Online UUID Generator](https://www.uuidgenerator.net/) 这个网站生成，注意服务端的 ID 需要和客户端保持一致。

```
Your Version 4 UUID:
b528e293-5979-408f-98de-a031564d1d6d
```


当然还可以修改端口，如下图所示：


### Shadowsocks

Shadowsocks 的端口配置支持1000-1010这种形式的连续多端口配置。

```js
  "inbounds": [
    {
      "protocol": "shadowsocks",
      "port": 444, // 监听 444 端口
      "settings": {
        "method": "aes-256-cfb",  // 加密方式
        "password": "lanfenger8"    // 密码，必须和客户端相同
      }
    }
  ],
```

V2Ray的json配置文件支持 `//`、`/* */`形式的注释，所以不需要删除注解也可以运行，当你的文本编辑器支持 json 的语法检查时可能会对注释报错，不用理会，V2Ray会正确的处理它。

目前 V2Ray 支持的加密方式很多，以下仅推荐两种：

- aes-256-gcm：PC端推荐，安全
- chacha20-ietf：移动端推荐，更省电，更快速，良好的加密性


### 永久运行

如果一致跟着教程走，我们已经配置了 Systemd，成功配置完成后我们需要使用它来管理 V2Ray 的运行，需要掌握的命令就以下几条：

#### 启用和禁用

注意启用和禁用并不会直接影响当下 V2Ray 的运行，启用状态时 V2Ray 崩溃会自动重新运行，开机会自动运行 V2Ray。

```bash
sudo systemctl enable v2ray
sudo systemctl disable v2ray
```

#### 启动、停止、重启V2RAY

```bash
sudo systemctl start v2ray
sudo systemctl stop v2ray
sudo systemctl restart v2ray
```

#### 查看状态

```bash
sudo systemctl status v2ray
```

## 原文链接

- [https://yuan.ga/v2ray-complete-tutorial/](https://yuan.ga/v2ray-complete-tutorial/)

- [https://zshttp.com/1310.html](https://zshttp.com/1310.html)

- [CentOS 7 开启BBR加速](https://www.noobyy.com/863.html)
