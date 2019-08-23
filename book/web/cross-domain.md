# 跨域

## JSONP

JSONP 的原理很简单，就是利用 <script> 标签没有跨域的限制的漏洞。当需要通讯时，通过 <script> 标签指向一个需要访问的地址，并提供一个回调函数来接收数据。

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

CORS需要浏览器和后端同时支持。IE 8 和 9 需要通过XDonmainRequest来实现。

浏览器会自动进行 CORS 通信，实现 CORS 通信的关键是后端，只要后端实现了 CORS ，就实现了跨域。
服务端设置 Access-C 动态容量 —Allow-Origin 就可以开启CORS。此属性表示哪些域名可以访问资源。

## document.domain

这种方式只能用于主域名相同的情况下，例如 a.alvinhtml.com 和 b.alvinhtml.com 适用于该方式。


只需要给页面添加 document.domain = 'alvinhtml.com'表示主域名都相同就可以实现跨域。

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
