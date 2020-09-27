# Shell 学习笔记

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务，它类似于 DOS 下的 cmd.exe。

Shell 的作用是解释执行用户的命令，用户输入一条命令，Shell就解释执行一条，这种方式称为交互式（Interactive）。

Shell 既是一种命令语言，又是一种程序设计语言。作为命令语言，它交互式解释和执行用户输入的命令或者自动地解释和执行预先设定好的一连串的命令；作为程序设计语言，它定义了各种变量和参数，并提供了许多在高级语言中才具有的控制结构，包括循环和分支。


## Shell 脚本
如果你有很多 Shell 脚本要执行，一条一条敲肯定太麻烦，幸好 Shell 提供了另一种执行命令的方式，用户事先写一个 Shell 脚本（Script），通常以 `.sh` 结尾，其中有很多条命令，让 Shell 一次把这些命令执行完，而不必一条一条地敲命令，这种方式称为批处理（Batch）。

Shell 编程跟 java、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Linux 的 Shell 应用程序种类众多，常见的有：

- Bourne Shell（/usr/bin/sh或/bin/sh）
- Bourne Again Shell（/bin/bash）
- C Shell（/usr/bin/csh）
- K Shell（/usr/bin/ksh）
- Shell for Root（/sbin/sh）


## 编写 Shell 脚本
编写一个简单的脚本 `hello.sh`：
```bash
#! /bin/sh
echo "Hello World !"
```

Shell 脚本中用 `#` 表示注释，相当于 C 语言的 // 注释。

但如果 # 位于第一行开头，并且是 `#!`（称为 Shebang ）则例外，它表示该脚本使用后面指定的解释器 `/bin/sh` 解释执行。

运行 Shell 脚本有两种方法：
1. 作为可执行程序
2. 作为解释器参数

### 作为可执行程序

将 `hello.sh` 保存，并 cd 到相应目录：
```bash
chmod +x ./hello.sh  #使脚本具有执行权限
./hello.sh  #执行脚本
```

注意，一定要写成 ./hello.sh，而不是 hello.sh，运行其它二进制的程序也一样，直接写 hello.sh，linux 系统会去 PATH 里寻找有没有叫 hello.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 hello.sh 是会找不到命令的，要用 ./hello.sh 告诉系统说，就在当前目录找。


### 作为解释器参数

这种运行方式是，直接运行解释器，其参数就是 shell 脚本的文件名，如：
```bash
/bin/sh test.sh
/bin/php test.php
```
这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

## Shell 变量

### 定义变量

定义变量时，变量名不加美元符号（$，PHP语言中变量需要），如：
```bash
your_name="alvin"
```
注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样。

除了显式地直接赋值，还可以用语句给变量赋值，如：
```bash
for file in `ls /etc`
do
done
# or
for file in $(ls /etc)
do
done
```
### 使用变量
使用一个定义过的变量，只要在变量名前面加美元符号即可，如：
```bash
your_name="alvin"
echo $your_name
echo ${your_name}

# 使用 readonly 命令可以将变量定义为只读变量
my_name="alvin"
readonly my_name

# 使用 unset 命令可以删除变量。
unset your_name
```

### 变量类型

运行shell时，会同时存在三种变量：

1. **局部变量** 局部变量在脚本或命令中定义，仅在当前 Shell 实例中有效，其他 Shell 启动的程序不能访问局部变量。

2. **环境变量** 所有的程序，包括 Shell 启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候Shell 脚本也可以定义环境变量。

3. **Shell 变量** Shell 变量是由 Shell 程序设置的特殊变量。Shell 变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

### Shell 字符串
```bash
str='this is a string'
str2="Hello, I know you are \"$your_name\"! \n"
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"

# 获取字符串长度
string="abcd"
echo ${#string} #输出 4

# 提取子字符串
# 以下实例从字符串第 2 个字符开始截取 4 个字符：
string="runoob is a great site"
echo ${string:1:4} # 输出 unoo

# 查找子字符串
# 查找字符 i 或 o 的位置(哪个字母先出现就计算哪个)：
string="runoob is a great site"
echo `expr index "$string" io`  # 输出 4
```
- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；

- 双引号里可以有变量，可以出现转义字符

### Shell 数组

bash 支持一维数组（不支持多维数组），并且没有限定数组的大小。在 Shell 中，用括号来表示数组，数组元素用 "空格" 符号分割开。定义数组的一般形式为:

```bash
array_name=(value0 value1 value2 value3)
# or
array_name=(
value0
value1
value2
value3
)
# or
array_name[0]=value0
array_name[1]=value1
array_name[n]=valuen

# 读取数组 ${数组名[下标]}
valuen=${array_name[n]}

# 使用 @ 符号可以获取数组中的所有元素
echo ${array_name[@]}

# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}
```

### 多行注释

多行注释还可以使用以下格式：
```bash
:<<EOF
注释内容...
注释内容...
注释内容...
EOF

# EOF 也可以使用其他符号:
:<<'
注释内容...
注释内容...
注释内容...
'
:<<!
注释内容...
注释内容...
注释内容...
!
```

## Shell 传递参数

我们可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：$n。n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推

```bash
#!/bin/bash
echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```


参数处理|说明
-|:-
$#|传递到脚本的参数个数
$\*|以一个单字符串显示所有向脚本传递的参数
$$|脚本运行的当前进程ID号
$!|后台运行的最后一个进程的ID号
$@|与$\*相同，但是使用时加引号，并在引号中返回每个参数
$\-|显示Shell使用的当前选项，与set命令功能相同。
$?|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。

### 默认参数(变量默认值)

if 繁琐方式

```sh
if [ ! $1 ]; then
    $1='default'
fi
```

变量为 null, 取默认值

```sh
${vari-defaultValue}
```

变量为 null 或 空字符串

```sh
${vari:-defaultValue}
```

- :? 变量为null 或 空字符串时报错并退出 `${name:?yjx}`
- :+ 变量不为空时使用默认值 `${name:+yjx}`


## Shell 基本运算符

Shell 和其他编程语言一样，支持多种运算符，原生 bash 不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。expr 是一款表达式计算工具，使用它能完成表达式的求值操作。

- 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
- 完整的表达式要被 &#96;&#96; 包含，注意这个字符不是常用的单引号，在 Esc 键下边。


### 算术运算符

```bash
#!/bin/bash
a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi
if [ $a != $b ]
then
   echo "a 不等于 b"
fi
```
- 条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]。
- 乘号 (\*) 前边必须加反斜杠 (\\) 才能实现乘法运算；
- 在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "\*" 不需要转义符号 "\\" 。


### 关系运算符

关系运算符只支持数字，不支持字符串，除非字符串的值是数字。下表列出了常用的关系运算符，假定变量 a 为 10，变量 b 为 20：

运算符|说明|举例
-|-|-
-eq|检测两个数是否相等，相等返回 true。|[ $a -eq $b ] 返回 false。
-ne|检测两个数是否不相等，不相等返回 true。|[ $a -ne $b ] 返回 true。
-gt|检测左边的数是否大于右边的，如果是，则返回 true。|[ $a -gt $b ] 返回 false。
-lt|检测左边的数是否小于右边的，如果是，则返回 true。|[ $a -lt $b ] 返回 true。
-ge|检测左边的数是否大于等于右边的，如果是，则返回 true。|[ $a -ge $b ] 返回 false。
-le|检测左边的数是否小于等于右边的，如果是，则返回 true。|[ $a -le $b ] 返回 true。

### 布尔运算符

运算符|说明|举例
-|-|-
!|非运算，表达式为 true 则返回 false，否则返回 true。|[ ! false ] 返回 true。
-o|或运算，有一个表达式为 true 则返回 true。|[ $a -lt 20 -o $b -gt 100 ] 返回 true。
-a|与运算，两个表达式都为 true 才返回 true。|[ $a -lt 20 -a $b -gt 100 ] 返回 false。

### 逻辑运算符

运算符|说明|举例
-|-|-
&&|逻辑的 AND|[[ $a -lt 100 && $b -gt 100 ]] 返回 false
&#124;&#124;|逻辑的 OR|[[ $a -lt 100 &#124;&#124; $b -gt 100 ]] 返回 true

## 字符串运算符
下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：

运算符|说明|举例
-|-|-
=|检测两个字符串是否相等，相等返回 true。|[ $a = $b ] 返回 false。
!=|检测两个字符串是否相等，不相等返回 true。|[ $a != $b ] 返回 true。
-z|检测字符串长度是否为0，为0返回 true。|[ -z $a ] 返回 false。
-n|检测字符串长度是否为0，不为0返回 true。|[ -n "$a" ] 返回 true。
$|检测字符串是否为空，不为空返回 true。|[ $a ] 返回 true。

### 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

操作符|说明|举例
-|-|-
-b file|检测文件是否是块设备文件，如果是，则返回 true。|[ -b $file ] 返回 false。
-c file|检测文件是否是字符设备文件，如果是，则返回 true。|[ -c $file ] 返回 false。
-d file|检测文件是否是目录，如果是，则返回 true。|[ -d $file ] 返回 false。
-f file|检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。|[ -f $file ] 返回 true。
-g file|检测文件是否设置了 SGID 位，如果是，则返回 true。|[ -g $file ] 返回 false。
-k file|检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。|[ -k $file ] 返回 false。
-p file|检测文件是否是有名管道，如果是，则返回 true。|[ -p $file ] 返回 false。
-u file|检测文件是否设置了 SUID 位，如果是，则返回 true。|[ -u $file ] 返回 false。
-r file|检测文件是否可读，如果是，则返回 true。|[ -r $file ] 返回 true。
-w file|检测文件是否可写，如果是，则返回 true。|[ -w $file ] 返回 true。
-x file|检测文件是否可执行，如果是，则返回 true。|[ -x $file ] 返回 true。
-s file|检测文件是否为空（文件大小是否大于0），不为空返回 true。|[ -s $file ] 返回 true。
-e file|检测文件（包括目录）是否存在，如果是，则返回 true。|[ -e $file ] 返回 true。
