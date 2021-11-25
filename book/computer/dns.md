# DNS

DNS 的全称是 Domain Name System 或者 Domain Name Service，它主要的作用就是将人们所熟悉的网址 (域名) “翻译”成电脑可以理解的 IP 地址，这个过程叫做 DNS 域名解析。

## 浏览器中从输出URL到返回页面的过程

1. 进行DNS域名解析，浏览器向DNS服务器查找输入URL对应的IP地址
2. 浏览器根据IP地址与目标web服务器在80端口上建立TCP连接
3. 浏览器向web服务器地址，发送HTTP请求
4. web服务器处理请求，并发送处理结果
5. 浏览器得到响应，关闭TCP连接
6. 浏览器解析HTML，渲染DOM

# DNS解析

在浏览器地址栏输入地址后，浏览器这个进程去调操作系统某个库里的`gethostbyname`函数(例如，Linux GNU glibc标准库的`gethostbyname`函数)，然后呢这个函数通过网卡给DNS服务器发UDP请求，接收结果，然后将结果给返回给浏览器。

#### 应用程序

```
gethostbyname('www.alvinhtml.com');
```

#### Socket
```
gethostbyname {
  // 生成发送给DNS服务器的查询消息
  // 向DNS服务器发送查询消息
  // 接收DNS服务器返回的响应消息
  // 从响应消息中取出IP地址，存在内存地址中
}
```

#### 操作系统内部协议栈
```
发送UDP消息 {
  // 发送操作
}
接收UDP消息 {
  // 接收操作
}
```

大致的流程如上，但是细节上可能有些差异：

1. 我们在用chrome浏览器的时候，其实会先去浏览器的dns缓存里头查询，dns缓存中没有，再去调用gethostbyname函数
2. gethostbyname函数在试图进行DNS解析之前首先检查域名是否在本地 Hosts 里，如果没找到再去DNS服务器上查


## 区域传送

DNS的规范规定了2种类型的DNS服务器，一个叫主DNS服务器，一个叫辅助DNS服务器。在一个区中主DNS服务器从自己本机的数据文件中读取该区的DNS数据信息，而辅助DNS服务器则从区的主DNS服务器中读取该区的DNS数据信息。当一个辅助DNS服务器启动时，它需要与主DNS服务器通信，并加载数据信息，这就叫做区传送（zone transfer）。 这种情况下，使用TCP协议。

### 为什么域名解析用UDP协议?

因为UDP快啊！UDP的DNS协议只要一个请求、一个应答就好了。而使用基于TCP的DNS协议要三次握手、发送数据以及应答、四次挥手。但是UDP协议传输内容不能超过512字节。不过客户端向DNS服务器查询域名，一般返回的内容都不超过512字节，用UDP传输即可。

### 为什么区域传送用TCP协议？

因为TCP协议可靠性好啊！你要从主DNS上复制内容啊，你用不可靠的UDP？ 因为TCP协议传输的内容大啊，你用最大只能传512字节的UDP协议？万一同步的数据大于512字节，你怎么办？

## DNS解析域名的原理

在介绍DNS解析域名原理之前，先介绍linux下一个dig命令，以显示解析域名的过程。 为了便于说明，我们来dig一下天猫的过程

```bash
dig www.tmall.com
```

结果如下：

```
; <<>> DiG 9.10.6 <<>> www.tmall.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 39495
;; flags: qr rd ra; QUERY: 1, ANSWER: 7, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.tmall.com.			IN	A

;; ANSWER SECTION:
www.tmall.com.		200	IN	CNAME	www.tmall.com.danuoyi.tbcache.com.
www.tmall.com.danuoyi.tbcache.com. 32 IN A	101.28.132.240
www.tmall.com.danuoyi.tbcache.com. 32 IN A	101.28.132.241
www.tmall.com.danuoyi.tbcache.com. 32 IN A	27.221.93.101
www.tmall.com.danuoyi.tbcache.com. 32 IN A	27.221.93.102
www.tmall.com.danuoyi.tbcache.com. 32 IN A	119.249.48.238
www.tmall.com.danuoyi.tbcache.com. 32 IN A	119.249.48.239

;; Query time: 20 msec
;; SERVER: 192.168.51.1#53(192.168.51.1)
;; WHEN: Wed Aug 28 16:54:37 CST 2019
;; MSG SIZE  rcvd: 182
```

### 请求参数

第一段代表请求参数如下所示

```
;; QUESTION SECTION:
;www.tmall.com.			IN	A
```

DNS的查询参数一般有三个:

1. 域名 - 服务器、邮件服务器(邮件地址中 @ 后面的部分)的名称
2. Class - 在设置DNS方案时，互联网之外的网络也考虑到了，而Class就是用来识别网络的，不过现在只有互联网，所以它的值永远都是代表互联网的IN
3. 记录类型 - 标识域名对应何种类型的记录
  - A 表示域名对应的IP地址
  - MX 表示域名对应的是邮件服务器
  - PTR 表示根据IP地址反查域名
  - CNAME 表示查询域名相关别名。

### 域名的结构

`www.tmall.com`对应的真正的域名为`www.tmall.com.`。末尾的`.`称为根域名，因为每个域名都有根域名，因此我们通常省略。

1. 根域名的下一级，叫做`顶级域名`（top-level domain，缩写为TLD），比如`.com`、`.net`；
2. 再下一级叫做`次级域名`（second-level domain，缩写为SLD），比如`www.tmall.com`里面的`.tmall`，这一级域名是用户可以注册的；
3. 再下一级是`主机名`（host），比如`www.tmall.com`里面的www，又称为"三级域名"，这是用户在自己的域里面为服务器分配的名称，是用户可以任意分配的。

### 分级查询

1. 先在本机的DNS里头查，如果有就直接返回了
2. 本机DNS里头发现没有，就去根服务器里查。根服务器发现这个域名是属于`com`域，因此根域DNS服务器会返回它所管理的`com`域中的DNS 服务器的IP地址，意思是“虽然我不知道你要查的那个域名的地址，但你可以去com域问问看”
3. 本机的DNS接到又会向`com`域的DNS服务器发送查询消息。`com` 域中也没有`www.tmall.com`这个域名的信息，和刚才一样，com域服务器会返回它下面的`tmall.com`域的DNS服务器的IP地址。

以此类推，只要重复前面的步骤，就可以顺藤摸瓜找到目标DNS服务器

```bash
dig +trace www.tmall.com
```

### 响应体

第二段内容，也就是响应体的部分

```
;; ANSWER SECTION:
www.tmall.com.		200	IN	CNAME	www.tmall.com.danuoyi.tbcache.com.
www.tmall.com.danuoyi.tbcache.com. 32 IN A	101.28.132.240
www.tmall.com.danuoyi.tbcache.com. 32 IN A	101.28.132.241
www.tmall.com.danuoyi.tbcache.com. 32 IN A	27.221.93.101
www.tmall.com.danuoyi.tbcache.com. 32 IN A	27.221.93.102
www.tmall.com.danuoyi.tbcache.com. 32 IN A	119.249.48.238
www.tmall.com.danuoyi.tbcache.com. 32 IN A	119.249.48.239
```

第一行就是说`www.tmall.com`有一个别名是`www.tmall.com.danuoyi.tbcache.com`。后面几行就是这个`www.tmall.com.danuoyi.tbcache.com`地址的真实IP。

### 使用A记录和CNAME进行域名解析的区别

A记录就是把一个域名解析到一个IP地址（Address，特制数字IP地址），而CNAME记录就是把域名解析到另外一个域名。其功能是差不多，CNAME将几个主机名指向一个别名，其实跟指向IP地址是一样的，因为这个别名也要做一个A记录的。但是使用CNAME记录可以很方便地变更IP地址。如果一台服务器有100个网站，他们都做了别名，该台服务器变更IP时，只需要变更别名的A记录就可以了。
