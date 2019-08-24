# 跨域

## JSONP

JSONP 的原理很简单，就是利用 `<script>` 标签没有跨域的限制的漏洞。当需要通讯时，通过 `<script>` 标签指向一个需要访问的地址，并提供一个回调函数来接收数据。

JSONP使用简单并且兼容性不错，但是只限于get请求。

```html
<script src="http://www.alvinhtml.com/api?&callback=jsonp"></script>
<script>
  function jsonp(data) {
    console.log(data)
  }
</script>
```


## CORS

跨域资源共享(CORS) 是一种机制，它使用额外的 HTTP 头来告诉浏览器  让运行在一个 origin (domain) 上的Web应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

出于安全原因，浏览器限制从脚本内发起的跨源HTTP请求。 例如，XMLHttpRequest和Fetch API遵循同源策略。 这意味着使用这些API的Web应用程序只能从加载应用程序的同一个域请求HTTP资源，除非响应报文包含了正确CORS响应头。

跨域资源共享（ CORS ）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。现代浏览器支持在 API 容器中（例如 XMLHttpRequest 或 Fetch ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。

CORS需要浏览器和后端同时支持。IE 8 和 9 需要通过XDonmainRequest来实现。

浏览器会自动进行 CORS 通信，实现 CORS 通信的关键是后端，只要后端实现了 CORS ，就实现了跨域。
服务端设置 Access-C 动态容量 —Allow-Origin 就可以开启CORS。此属性表示哪些域名可以访问资源。

### 什么情况下需要 CORS ？节

跨域资源共享标准（ cross-origin sharing standard ）允许在下列场景中使用跨域 HTTP 请求：

```
- 前文提到的由 XMLHttpRequest 或 Fetch 发起的跨域 HTTP 请求。
- Web 字体 (CSS 中通过 @font-face 使用跨域字体资源), 因此，网站就可以发布 TrueType 字体资源，并只允许已授权网站进行跨站调用。
- WebGL 贴图
- 使用 drawImage 将 Images/video 画面绘制到 canvas
```

### CORS 实现

客户端和服务器之间使用 CORS 首部字段来处理跨域权限

![CORS](../assets/cors.png)

以下是示例是一个跨域请求的请求头和响应头：

```
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/access-control/simpleXSInvocation.html
Origin: http://foo.example


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2.0.61
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml

[XML Data]
```

使用 Origin 和 Access-Control-Allow-Origin 就能完成最简单的访问控制。本例中，服务端返回的 Access-Control-Allow-Origin: * 表明，该资源可以被任意外域访问。如果服务端仅允许来自 http://foo.example 的访问，该首部字段的内容如下：

```
Access-Control-Allow-Origin: http://foo.example
```
现在，除了 http://foo.example，其它外域均不能访问该资源（该策略由请求首部中的 ORIGIN 字段定义，见第10行）。Access-Control-Allow-Origin 应当为 * 或者包含由 Origin 首部字段所指明的域名。


### HTTP 响应首部字段节


#### Access-Control-Allow-Origin

响应首部中可以携带一个 Access-Control-Allow-Origin 字段，其语法如下:

```
Access-Control-Allow-Origin: <origin> | *
```

其中，origin 参数的值指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。

#### Access-Control-Expose-Headers

在跨域访问时，XMLHttpRequest对象的getResponseHeader()方法只能拿到一些最基本的响应头，Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头。

Access-Control-Expose-Headers 头让服务器把允许浏览器访问的头放入白名单，例如：

```
Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header
```

这样浏览器就能够通过getResponseHeader访问X-My-Custom-Header和 X-Another-Custom-Header 响应头了。

#### Access-Control-Max-Age

Access-Control-Max-Age 头指定了preflight请求的结果能够被缓存多久，请参考本文在前面提到的preflight例子。

```
Access-Control-Max-Age: <delta-seconds>
```

delta-seconds 参数表示preflight请求的结果在多少秒内有效。

#### Access-Control-Allow-Credentials

Access-Control-Allow-Credentials 头指定了当浏览器的credentials设置为true时是否允许浏览器读取response的内容。当用在对preflight预检测请求的响应中时，它指定了实际的请求是否可以使用credentials。请注意：简单 GET 请求不会被预检；如果对此类请求的响应中不包含该字段，这个响应将被忽略掉，并且浏览器也不会将相应内容返回给网页。

```
Access-Control-Allow-Credentials: true
```

#### Access-Control-Allow-Methods

Access-Control-Allow-Methods 首部字段用于预检请求的响应。其指明了实际请求所允许使用的 HTTP 方法。

```
Access-Control-Allow-Methods: <method>[, <method>]*
```

#### Access-Control-Allow-Headers

Access-Control-Allow-Headers 首部字段用于预检请求的响应。其指明了实际请求中允许携带的首部字段。

```
Access-Control-Allow-Headers: <field-name>[, <field-name>]*

```

### HTTP 请求首部字段节

#### Origin

Origin 首部字段表明预检请求或实际请求的源站。

```
Origin: <origin>
```

origin 参数的值为源站 URI。它不包含任何路径信息，只是服务器名称。

```
1. 有时候将该字段的值设置为空字符串是有用的，例如，当源站是一个 data URL 时。
2. 不管是否为跨域请求，ORIGIN 字段总是被发送。
```

#### Access-Control-Request-Method

Access-Control-Request-Method 首部字段用于预检请求。其作用是，将实际请求所使用的 HTTP 方法告诉服务器。

```
Access-Control-Request-Method: <method>
```

#### Access-Control-Request-Headers

Access-Control-Request-Headers 首部字段用于预检请求。其作用是，将实际请求所携带的首部字段告诉服务器。

```
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```

## document.domain

这种方式只能用于主域名相同的情况下，例如 www.alvinhtml.com 和 b.alvinhtml.com 适用于该方式。

只需要给页面添加 document.domain = 'alvinhtml.com' 表示主域名都相同就可以实现跨域。

domain 只能设置为主域名，不可以在b.alvinhtml.com中将domain设置为 c.alvinhtml.com。



## postMessage

```js
// 发送消息端
window.parent.postMessage('message','http://alvinhtml.com')

//接收消息端
const myMessage = new MessageChannel();

myMessage.addEventListener('message', event => {
  var origin = even.origin || event.originalEvent.origin
  if(origin === 'http://alvinhtml.com') {
    console.log('验证通过')
  }
})
```
