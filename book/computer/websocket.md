# websocket 协议解析

## 概念与背景

WebSocket 协议在2008年诞生，2011年成为国际标准。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

其他特点包括：

> 1. 建立在 TCP 协议之上，服务器端的实现比较容易。
> 1. 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
> 1. 数据格式比较轻量，性能开销小，通信高效。
> 1. 可以发送文本，也可以发送二进制数据。
> 1. 没有同源限制，客户端可以与任意服务器通信。
> 1. 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。


## 连接时握手
`websocket` 的握手实际上就是给服务器发送一个GET请求，里面带上指定的header即可。

request例子如下
```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

其中比较特殊的是Upgrade，Connection，和Sec开头的几个字段，那么如果请求握手的话，

```
Upgrade: websocket
Connection: Upgrade
```

是固定的要填写的两个键值对。

Sec-WebSocket-Key是一个16位的随机值，经过base64编码后生成，给服务器进行UUID连接再编码后由客户端检查用。

Sec-WebSocket-Version是使用的版本号。

Sec-WebSocket-Protocol是选用的子协议，此字段为可选字段，由服务器选择一个子协议与客户端通信，子协议是由websocket承载的协议。

response例子如下

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
我们可以看到这是一个状态码为101的响应，响应的头内容基本和request可以对应，Sec-WebSocket-Accept是服务端利用Key和UUID拼接后再进行base64编码产生的一个值，由客户端进行验证。
这样，我们的连接时握手就完成了。
```

## 数据帧

### 基础帧协议

因为一些安全的原因，从客户端发送到服务端的帧全部要与掩码进行异或运算过才有效，而服务端发送到客户端的帧不需要进行异或运算。

我们来看下官方的一幅帧结构定义图

![官方 websocket 数据帧结构图](https://segmentfault.com/img/bVxZFC?w=554&h=293)

逐一解释

名称|长度|注释
-|-|-
FIN|1bit|标明这一帧是否是整个消息体的最后一帧
RSV1 RSV2 RSV3|1bit|保留位，必须为0，如果不为0，则标记为连接失败
opcode|4bit|操作位，定义这一帧的类型
Mask|1bit|标明承载的内容是否需要用掩码进行异或
Masking-key|0 or 4bytes|掩码异或运算用的key
Payload length|7bit or 7 +16bit or 7 + 64bit|承载体的长度（后续会解释为什么会有3种长度）

如果从结构角度讲，那么 `websocket` 帧结构就这么简单。

### 操作码 (opcode)

在 `websocket` 中，我们定义了几种操作类型，也就是表明了数据包的行为，数据包大体可分为两种，一种是字符数据包 (string)，一种是字节数据包 (byte) 不同的数据包使用不同的opcode来传输，opcode定义如下：


值|定义
-|-
%x0|标明这一个数据包是上一个数据包的延续，它是一个延长帧 (continuation frame)
%x1|标明这个数据包是一个字符帧 (text frame)
%x2|标明这个数据包是一个字节帧 (binary frame)
%x3-7|保留值，供未来的非控制帧使用
%x8|标明这个数据包是用来告诉对方，我方需要关闭连接
%x9|标明这个数据包是一个心跳请求 (ping)
%xA|标明这个数据包是一个心跳响应 (pong)
%xB-F|保留至，供未来的控制帧使用

### 关于掩码 (Mask)

如果是客户端发送到服务端的数据包，我们需要使用掩码对payload的每一个字节进行异或运算，生成masked payload 才能被服务器读取。
具体的运算其实很简单。

假设payload长度为pLen，mask-key长度为mLen，i作为payload的游标，j作为mask-key的游标，伪代码如下：

```
for (i = 0; i < pLen; i++){
    int j = i % mLen;
    maskedPayload[i] = payload[i] ^ maskKey[j];
}
```

### Payload长度
Payload Length位占用了可选的7bit或者7 + 16bit 或者 7 + 64bit，这里是什么意思呢？ MDN上有文章也是对websocket协议进行了很好的阐述，先贴原文：

[编写websocket服务器](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers)

引用其中关于Payload length定义的一段文字：

> ## 读解负载数据长度
> 读取负载数据，需要知道读到那里为止。因此获知负载数据长度很重要。这个过程稍微有点复杂，要以下这些步骤：

> 1. 读取9-15位 (包括9和15位本身)，并转换为无符号整数。如果值小于或等于125，这个值就是长度；如果是 126，请转到步骤 2。如果它是 127，请转到步骤 3。

> 1. 读取接下来的 16 位并转换为无符号整数，并作为长度。

> 1. 读取接下来的 64 位并转换为无符号整数，并作为长度。

当然我们这边所使用的都是`网络字节序`。

## 关闭连接时的握手
关闭连接的时候，只用发送一个opcode为`0x08`的帧，payload中前2个字节写入定义的code，后续写入关闭连接的reason，那么一个关闭流程就握手就开始，此处不再赘述。

## 客户端的简单示例

WebSocket 的[用法](http://www.ruanyifeng.com/blog/2017/05/websocket.html)相当简单。


下面是一个网页脚本的例子（点击这里看运行结果），基本上一眼就能明白。

```js
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) {
  console.log("Connection open ...");
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};
```

WebSocket 实例对象的所有属性和方法清单，参见[这里](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)。   
