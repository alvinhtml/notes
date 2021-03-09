# Go 语言

## 环境安装

### 下载安装

下载([Go 安装包下载](https://studygolang.com/dl))可执行文件版，直接点击下一步安装即可，默认会将go安装到/usr/local/go目录下。

安装完成后，输入 `go version` 命令，查看安装的Go版本。

```bash
go version
```

### 配置 GOPATH

GOPATH目录下一共包含了三个子目录，分别是：

- bin：存储所编译生成的二进制文件。
- pkg：存储预编译的目标文件，以加快程序的后续编译速度。
- src：存储所有.go文件或源代码。在编写 Go 应用程序，程序包和库时，一般会以 `$GOPATH/src/github.com/foo/bar` 的路径进行存放。


编辑 `~/.bash_profile`（在终端中运行 vi ~/.bash_profile 即可）来添加下面这行代码（如果你找不到 .bash_profile，那就自己创建一个）。

```bash
export GOPATH=/Users/alvin/coder/go
```

保存然后退出你的编辑器。然后在终端中运行下面命令

```bash
source ~/.bash_profile
```

提示：$HOME 是每个电脑下的用户主目录，每个电脑可能不同，可以在终端运行 echo $HOME 获取

GOROOT 也就是 Go 开发包的安装目录默认是在 /usr/local/go，如果没有，可以在 bash_profile 文件中设置。

```bash
export GOROOT=/usr/local/go
```

然后保存并退出编辑器，运行 `source ~/.bash_profile` 命令即可。

使用 GOPATH 模式下，我们需要将应用代码存放在固定的 `$GOPATH/src` 目录下，并且如果执行 `go get` 来拉取外部依赖会自动下载并安装到$GOPATH目录下。

```
GOPATH 模式下没有版本控制的概念，具有致命的缺陷，至少会造成以下问题:

1. 在执行go get的时候，你无法传达任何的版本信息的期望，也就是说你也无法知道自己当前更新的是哪一个版本，也无法通过指定来拉取自己所期望的具体版本。
2. 在运行 Go 应用程序的时候，你无法保证其它人与你所期望依赖的第三方库是相同的版本，也就是说在项目依赖库的管理上，你无法保证所有人的依赖版本都一致。
3. 你没办法处理 v1、v2、v3 等等不同版本的引用问题，因为 GOPATH 模式下的导入路径都是一样的，都是github.com/foo/bar。
```

## Go Modules

在 Go modules 中，我们能够使用如下命令进行操作：

命令|作用
-|-
go mod init	| 生成 go.mod 文件
go mod download	| 下载 go.mod 文件中指明的所有依赖
go mod tidy	| 整理现有的依赖
go mod graph	| 查看现有的依赖结构
go mod edit	| 编辑 go.mod 文件
go mod vendor	| 导出项目所有的依赖到vendor目录
go mod verify	| 校验一个模块是否被篡改过
go mod why	| 查看为什么需要依赖某模块

### 所提供的环境变量

在 Go modules 中有如下常用环境变量，我们可以通过 go env 命令来进行查看，如下：

```
$ go env
GO111MODULE="auto"
GOPROXY="https://proxy.golang.org,direct"
GONOPROXY=""
GOSUMDB="sum.golang.org"
GONOSUMDB=""
GOPRIVATE=""
```
#### GO111MODULE

Go语言提供了 GO111MODULE 这个环境变量来作为 Go modules 的开关，其允许设置以下参数：

- auto：只要项目包含了 go.mod 文件的话启用 Go modules，目前在 Go1.11 至 Go1.14 中仍然是默认值。
- on：启用 Go modules，推荐设置，将会是未来版本中的默认值。
- off：禁用 Go modules，不推荐设置。

#### GOPROXY

这个环境变量主要是用于设置 Go 模块代理（Go module proxy），其作用是用于使 Go 在后续拉取模块版本时能够脱离传统的 VCS 方式，直接通过镜像站点来快速拉取。

GOPROXY 的默认值是：`https://proxy.golang.org,direct`，这有一个很严重的问题，就是 proxy.golang.org 在国内是无法访问的，因此这会直接卡住你的第一步，所以你必须在开启 Go modules 的时，同时设置国内的 Go 模块代理，执行如下命令：

```bash
$ go env -w GOPROXY=https://goproxy.cn,direct
```

GOPROXY 的值是一个以英文逗号 “,” 分割的 Go 模块代理列表，允许设置多个模块代理，假设你不想使用，也可以将其设置为 “off” ，这将会禁止 Go 在后续操作中使用任何 Go 模块代理。

```
direct是什么?

实际上 “direct” 是一个特殊指示符，用于指示 Go 回源到模块版本的源地址去抓取（比如 GitHub 等），场景如下：当值列表中上一个 Go 模块代理返回 404 或 410 错误时，Go 自动尝试列表中的下一个，遇见 “direct” 时回源，也就是回到源地址去抓取，而遇见 EOF 时终止并抛出类似 “invalid version: unknown revision...” 的错误。
```

#### GOSUMDB

它的值是一个 Go checksum database，用于在拉取模块版本时（无论是从源站拉取还是通过 Go module proxy 拉取）保证拉取到的模块版本数据未经过篡改，若发现不一致，也就是可能存在篡改，将会立即中止。

GOSUMDB 的默认值为：`sum.golang.org`，在国内也是无法访问的，但是 GOSUMDB 可以被 Go 模块代理所代理（详见：Proxying a Checksum Database）。

因此我们可以通过设置 `GOPROXY` 来解决，而先前我们所设置的模块代理 `goproxy.cn` 就能支持代理 `sum.golang.org`，所以这一个问题在设置 `GOPROXY` 后，你可以不需要过度关心。

#### GONOPROXY/GONOSUMDB/GOPRIVATE

这三个环境变量都是用在当前项目依赖了私有模块，例如像是你公司的私有 git 仓库，又或是 github 中的私有库，都是属于私有模块，都是要进行设置的，否则会拉取失败。

更细致来讲，就是依赖了由 GOPROXY 指定的 Go 模块代理或由 GOSUMDB 指定 Go checksum database 都无法访问到的模块时的场景。

而一般建议直接设置 GOPRIVATE，它的值将作为 GONOPROXY 和 GONOSUMDB 的默认值，所以建议的最佳姿势是直接使用 GOPRIVATE。

并且它们的值都是一个以英文逗号 “,” 分割的模块路径前缀，也就是可以设置多个，例如：

```bash
$ go env -w GOPRIVATE="git.example.com,github.com/eddycjy/mquote"
```

设置后，前缀为 `git.xxx.com` 和 `github.com/eddycjy/mquote` 的模块都会被认为是私有模块。

如果不想每次都重新设置，我们也可以利用通配符，例如：

```bash
$ go env -w GOPRIVATE="*.example.com"
```

这样子设置的话，所有模块路径为 example.com 的子域名（例如：git.example.com）都将不经过 Go module proxy 和 Go checksum database，需要注意的是不包括 example.com 本身。

### 初始化项目

在完成 Go modules 的开启后，我们需要创建一个示例项目来进行演示，执行如下命令：

```bash
cd /Users/alvin/coder/go/src/alvinhtml.com/module-repo
go mod init alvinhtml.com/module-repo

go: creating new go.mod: module alvinhtml.com/module-repo
```

在执行 go mod init 命令时，我们指定了模块导入路径为 `alvinhtml.com/module-repo`。接下来我们在该项目根目录下创建 main.go 文件，如下：

```go
package main


import (
    "fmt"
    "github.com/gin-gonic/gin"
)


func main() {
	fmt.Println(mquote.GetHello())
}
```

然后在项目根目录执行 `go get github.com/gin-gonic/gin` 命令，如下：

```bash
go get github.com/gin-gonic/gin
# go: github.com/gin-gonic/gin upgrade => v1.6.3
# go: downloading gopkg.in/yaml.v2 v2.2.8
# go: downloading github.com/golang/protobuf v1.3.3
# go: downloading github.com/go-playground/validator/v10 v10.2.0
# go: downloading golang.org/x/sys v0.0.0-20200116001909-b77594299b42
# go: downloading github.com/json-iterator/go v1.1.9
# go: downloading github.com/modern-go/reflect2 v0.0.0-20180701023420-4b7aa43c6742
# go: downloading github.com/modern-go/concurrent v0.0.0-20180228061459-e0a39a4cb421
```

#### 查看 go.mod 文件

在初始化项目时，会生成一个 `go.mod` 文件，是启用了 Go modules 项目所必须的最重要的标识，同时也是 GO111MODULE 值为 `auto` 时的识别标识，它描述了当前项目（也就是当前模块）的元信息，每一行都以一个动词开头。

在我们刚刚进行了初始化和简单拉取后，我们再次查看 `go.mod` 文件，基本内容如下：

```
module alvinhtml.com/module-repo

go 1.15

require github.com/gin-gonic/gin v1.6.3 // indirect
```

为了更进一步的讲解，我们模拟引用如下：

```
module github.com/eddycjy/module-repo


go 1.13


require (
    example.com/apple v0.1.2
    example.com/banana v1.2.3
    example.com/banana/v2 v2.3.4
    example.com/pear // indirect
    example.com/strawberry // incompatible
)


exclude example.com/banana v1.2.4
replace example.com/apple v0.1.2 => example.com/fried v0.1.0
replace example.com/banana => example.com/fish
```

module：用于定义当前项目的模块路径。

go：用于标识当前模块的 Go 语言版本，值为初始化模块时的版本，目前来看还只是个标识作用。

require：用于设置一个特定的模块版本。

exclude：用于从使用中排除一个特定的模块版本。

replace：用于将一个模块版本替换为另外一个模块版本。

另外你会发现 example.com/pear 的后面会有一个 indirect 标识，indirect 标识表示该模块为间接依赖，也就是在当前应用程序中的 import 语句中，并没有发现这个模块的明确引用，有可能是你先手动 go get 拉取下来的，也有可能是你所依赖的模块所依赖的，情况有好几种。


#### 查看 go.sum 文件
在第一次拉取模块依赖后，会发现多出了一个 go.sum 文件，其详细罗列了当前项目直接或间接依赖的所有模块版本，并写明了那些模块版本的 SHA-256 哈希值以备 Go 在今后的操作中保证项目所依赖的那些模块版本不会被篡改。

我们可以看到一个模块路径可能有如下两种：

```
github.com/eddycjy/mquote v0.0.1 h1:4QHXKo7J8a6J/k8UA6CiHhswJQs0sm2foAQQUq8GFHM=
github.com/eddycjy/mquote v0.0.1/go.mod h1:ZtlkDs7Mriynl7wsDQ4cU23okEtVYqHwl7F1eDh4qPg=
```

h1 hash 是 Go modules 将目标模块版本的 zip 文件开包后，针对所有包内文件依次进行 hash，然后再把它们的 hash 结果按照固定格式和算法组成总的 hash 值。

而 h1 hash 和 go.mod hash 两者，要不就是同时存在，要不就是只存在 go.mod hash。那什么情况下会不存在 h1 hash 呢，就是当 Go 认为肯定用不到某个模块版本的时候就会省略它的 h1 hash，就会出现不存在 h1 hash，只存在 go.mod hash 的情况。

#### 查看全局缓存

我们刚刚成功的将 `github.com/gin-gonic/gin` 模块拉取了下来，其拉取的结果缓存在  `$GOPATH/pkg/mod` 和 `$GOPATH/pkg/sumdb ` 目录下，而在mod目录下会以 github.com/foo/bar 的格式进行存放，如下：

```
mod
├── cache
├── github.com
├── golang.org
├── google.golang.org
├── gopkg.in
...
```

需要注意的是同一个模块版本的数据只缓存一份，所有其它模块共享使用。如果你希望清理所有已缓存的模块版本数据，可以执行 `go clean -modcache` 命令。

### Go Modules 下的 go get 行为

在拉取项目依赖时，你会发现拉取的过程总共分为了三大步，分别是 finding（发现）、downloading（下载）以及 extracting（提取）， 并且在拉取信息上一共分为了三段内容：

```
v0.0.0-20200116001909-b77594299b42
```

- v0.0.0 版本信息, v0.0.0 表示没有发布过任何 tag
- 20200116001909 所拉取版本的 commit 时间, 以UTC时区为准
- b77594299b42 所拉取版本的 commit 哈希

#### go get 的拉取行为

刚刚我们用 `go get` 命令拉取了新的依赖，那么 `go get` 又提供了哪些功能呢，常用的拉取命令如下：

命令|作用
-|-
go get	| 拉取依赖，会进行指定性拉取（更新），并不会更新所依赖的其它模块。
go get -u	| 更新现有的依赖，会强制更新它所依赖的其它全部模块，不包括自身。
go get -u -t ./...	| 更新所有直接依赖和间接依赖的模块版本，包括单元测试中用到的。

那么我想选择具体版本应当如何执行呢，如下：

```bash
go get golang.org/x/text@latest	# 拉取最新的版本，若存在tag，则优先使用。
go get golang.org/x/text@master	# 拉取 master 分支的最新 commit。
go get golang.org/x/text@v0.3.2	# 拉取 tag 为 v0.3.2 的 commit。
go get golang.org/x/text@342b2e	# 拉取 hash 为 342b231 的 commit，最终会被转换为 v0.3.2。
```



## 项目目录结构

```
.
├── bin
├── pkg
└── src
    ├── alvinhtml.com
    │   └── hello-world
    │       └── main.go
    └── github.com
```

## go build

`go build` 表示将源代码编译成可执行文件。

在 `hello-world` 目录下执行：

```bash
go build
```

或者在其他目录执行以下命令：

```
go build alvinhtml.com/hello-world
```

go 编译器会去 GOPATH 的 src 目录下查找你要编译的 alvinhtml.com/hello-world 项目

编译得到的可执行文件会保存在执行编译命令的当前目录下，如果是 windows 平台会在当前目录下找到 hello-world.exe 可执行文件。

我们还可以使用 `-o` 参数来指定编译后得到的可执行文件的名字。

```
go build -o hello-world.exe
```

## 变量

标准声明

```
var 变量名 变量类型
var 变量名 类型 = 表达式
```

批量声明

```go
var (
    a string
    b int
    c bool
    d float32
)

var name, age = "Q1mi", 20
```

- 类型推导
  - `var name = "Q1mi"`。
- 短变量声明
  - 在函数内部，可以使用更简略的 `:=` 方式声明并初始化变量。例如：`m := 200`
- 匿名变量（哑元变量）
  - 匿名变量用一个下划线 `_` 表示，例如：`x, _ := foo()`
  - 匿名变量不占用命名空间，不会分配内存，所以匿名变量之间不存在重复声明。
- 注意事项：
  - 函数外的每个语句都必须以关键字开始（var、const、func等）
  - `:=` 不能使用在函数外。
  - `_` 多用于占位，表示忽略值。

## 常量

相对于变量，常量是恒定不变的值，多用于定义程序运行期间不会改变的那些值。 常量的声明和变量声明非常类似，只是把 `var` 换成了 `const`，常量在定义的时候必须赋值。

```go
const pi = 3.1415

const (
    a = 3.1415
    b = 2.7182
)
```

### iota

`iota` 是 `go` 语言的常量计数器，只能在常量的表达式中使用。

`iota` 在 `const` 关键字出现时将被重置为 `0`。const 中每新增一行常量声明将使 `iota` 计数一次。 使用 `iota` 能简化定义，在定义枚举时很有用。

```go
const (
    n1 = iota //0
    n2        //1
    _
    n4        //3
)

const (
    _  = iota
    KB = 1 << (10 * iota)
    MB = 1 << (10 * iota)
    GB = 1 << (10 * iota)
    TB = 1 << (10 * iota)
    PB = 1 << (10 * iota)
)
```

## 基本数据类型

### 整型

类型|描述
-|-
uint8|无符号 8位整型 (0 到 255)
uint16|无符号 16位整型 (0 到 65535)
uint32|无符号 32位整型 (0 到 4294967295)
uint64|无符号 64位整型 (0 到 18446744073709551615)
int8|有符号 8位整型 (-128 到 127)
int16|有符号 16位整型 (-32768 到 32767)
int32|有符号 32位整型 (-2147483648 到 2147483647)
int64|有符号 64位整型 (-9223372036854775808 到 9223372036854775807)

### 特殊整型

类型|描述
-|-
uint|32位操作系统上就是uint32，64位操作系统上就是uint64
int|32位操作系统上就是int32，64位操作系统上就是int64
uintptr|无符号整型，用于存放一个指针

### 浮点型

类型|描述
-|-
float32|最大范围约为 `3.4e38`，可以使用常量定义：`math.MaxFloat32`
float64|最大范围约为 `1.8e308`，可以使用常量定义：`math.MaxFloat64`

### 数字字面量语法（Number literals syntax）

### 布尔值
  - true
  - false

### 字符串

Go 语言中的字符串以原生数据类型出现，使用字符串就像使用其他原生数据类型（int、bool、float32、float64 等）一样。 Go 语言里的字符串的内部实现使用 UTF-8编码。 字符串的值为双引号 (") 中的内容，可以在Go语言的源码中直接添加非 ASCII 码字符，例如：

```go
s1 := "hello"
s2 := "你好"
```
- 字符串转义符
  - `\r` 回车符（返回行首）
  - `\n` 换行符（直接跳到下一行的同列位置）
  - `\t` 制表符
  - `\'` 单引号
  - `\"` 双引号
  - `\\` 反斜杠
- 多行字符串
  - Go 语言中要定义一个多行字符串时，就必须使用反引号: \`hello\`

### 字符串的常用操作
方法|介绍
-|-
len(str)|求长度
+或fmt.Sprintf|拼接字符串
strings.Split|分割
strings.contains|判断是否包含
strings.HasPrefix,strings.HasSuffix|前缀/后缀判断
strings.Index(),strings.LastIndex()|子串出现的位置
strings.Join(a[]string, sep string)|join操作



## HTTP

理解HTTP相关的网络应用，主要关注两个地方-客户端(client)和服务端(server)

两者的交互主要是client的request以及server的response,主要就在于如何接受client的request并向client返回response

接收request的过程中，最重要的莫过于路由（router），即实现一个Multiplexer器。Go中既可以使用内置的mutilplexer --- DefautServeMux，也可以自定义。Multiplexer路由的目的就是为了找到处理器函数（handler），后者将对request进行处理，同时构建response

流程为：

```
Clinet -> Requests ->  [Multiplexer(router) -> handler  -> Response -> Clinet
```

理解go中的http服务，最重要就是要理解Multiplexer和handler，Golang中的Multiplexer基于ServeMux结构，同时也实现了Handler接口。下面对几个重要概念说明：

- hander函数： 具有func(w http.ResponseWriter, r \*http.Requests)签名的函数
- handler处理器(函数): 经过HandlerFunc结构包装的handler函数，它实现了ServeHTTP接口方法的函数。调用handler处理器的ServeHTTP方法时，即调用handler函数本身。
