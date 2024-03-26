# 学习 Linux

## Linux 概念

Linux 是一种操作系统 （OS），它是负责计算机软件和硬件之间通信的软件。从技术上讲，操作系统中实际上称为“Linux”的部分是内核，它是负责管理计算机核心处理单元（CPU）以及内存和外围设备（如显示器，键盘，打印机等）的软件。最初的 Linux 内核是开源的，这意味着用户可以更改和重新分发其源代码。但并非随后的每个 Linux 变体都走上了开源道路。

Linux 有不同的变体，称为项目或发行版。一些流行的 Linux 发行版包括 Debian、Fedora、Ubuntu、Red Hat Enterprise Linux、Oracle Enterprise Linux 和 SUSE Linux Enterprise。企业发行版专为企业、商业或学术用途而设计，为付费客户提供定期软件更新和专门的技术支持。

每个发行版通常都带有 Linux 内核;软件包管理系统，是用于管理软件的安装、卸载、升级和配置的软件;以及促进用户与软件之间交互的不同软件，例如图形用户界面 （GUI）、编辑和访问代码的方法，以及连接输入、输出和存储设备的方法。

## Linux 常用命令

### 查看 Linux 命令帮助信息的要点

- 查看 Shell 内部命令的帮助信息 - 使用 `help`
- 查看命令的简要说明 - 使用 `whatis`
- 查看命令的详细说明 - 使用 `info`
- 查看命令的位置 - 使用 `which`
- 定位指令的二进制程序、源代码文件和 man 手册页等相关文件的路径 - 使用 `whereis`
- 查看命令的帮助手册（包含说明、用法等信息） - 使用 `man`
- 只记得部分命令关键字 - 使用 `man -k`

> 注：推荐一些 Linux 命令中文手册：
>
> - Linux 命令大全
> - [linux-command](https://github.com/jaywcjlove/linux-command)

#### help

help 命令用于查看 Shell 内部命令的帮助信息。而对于外部命令的帮助信息只能使用 man 或者 info 命令查看。

参考：http://man.linuxde.net/help

#### whatis

whatis 用于查询一个命令执行什么功能。

参考：http://man.linuxde.net/whatis

示例：

```sh
# 查看 man 命令的简要说明

$ whatis man

# 查看以 loca 开拓的命令的简要说明

$ whatis -w "loca\*"
```

#### info

info 是 Linux 下 info 格式的帮助指令。

参考：http://man.linuxde.net/info

示例：

```sh
# 查看 man 命令的详细说明

$ info man
```

#### which

which 命令用于查找并显示给定命令的绝对路径，环境变量 PATH 中保存了查找命令时需要遍历的目录。which 指令会在环境变量$PATH 设置的目录里查找符合条件的文件。也就是说，使用 which 命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。

参考：http://man.linuxde.net/which

示例：

```sh
which pwd # 查找命令的路径
```

说明：which 是根据使用者所配置的 PATH 变量内的目录去搜寻可运行档的！所以，不同的 PATH 配置内容所找到的命令当然不一样的！

```sh
[root@localhost ~]# which cd
cd: shell built-in command
```

cd 这个常用的命令竟然找不到啊！为什么呢？这是因为 cd 是 bash 内建的命令！但是 which 默认是找 PATH 内所规范的目录，所以当然一定找不到的！

#### whereis

whereis 命令用来定位指令的二进制程序、源代码文件和 man 手册页等相关文件的路径。

whereis 命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man 说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。

参考：http://man.linuxde.net/whereis

示例：

```sh
whereis git # 将相关的文件都查找出来
```

#### man

man 命令是 Linux 下的帮助指令，通过 man 指令可以查看 Linux 中的指令帮助、配置文件帮助和编程帮助等信息。

参考：http://man.linuxde.net/man

示例：

```sh
$ man date # 查看 date 命令的帮助手册
$ man 3 printf # 查看 printf 命令的帮助手册中的第 3 类
$ man -k keyword # 根据命令中部分关键字来查询命令
```

##### man 要点

在 man 的帮助手册中，可以使用 page up 和 page down 来上下翻页。

man 的帮助手册中，将帮助文档分为了 9 个类别，对于有的关键字可能存在多个类别中， 我们就需要指定特定的类别来查看；（一般我们查询 bash 命令，归类在 1 类中）。

man 页面的分类(常用的是分类 1 和分类 3)：

- 可执行程序或 shell 命令
- 系统调用(内核提供的函数)
- 库调用(程序库中的函数)
- 特殊文件(通常位于 /dev)
- 文件格式和规范，如 /etc/passwd
- 游戏
- 杂项(包括宏包和规范，如 man(7)，groff(7))
- 系统管理命令(通常只针对 root 用户)
- 内核例程 [非标准]

前面说到使用 whatis 会显示命令所在的具体的文档类别，我们学习如何使用它

```sh
$ whatis printf
printf (1) - format and print data
printf (1p) - write formatted output
printf (3) - formatted output conversion
printf (3p) - print formatted output
printf [builtins](1) - bash built-in commands, see bash(1)
```

我们看到 printf 在分类 1 和分类 3 中都有；分类 1 中的页面是命令操作及可执行文件的帮助；而 3 是常用函数库说明；如果我们想看的是 C 语言中 printf 的用法，可以指定查看分类 3 的帮助：

```sh
$ man 3 printf
```
