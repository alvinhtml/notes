# EventSource

EventSource 是实现服务端推送的一个网络事件接口。EventSource 实例会与服务端创建一个持久化的连接，服务端以 `text/event-stream` 格式发送事件， 并且会一直保持开启直到被要求关闭。一旦连接开启，来自服务端传入的消息会以事件的形式分发至你代码中。如果接收消息中有一个事件字段，触发的事件与事件字段的值相同。如果没有事件字段存在，则将触发通用事件。

## 什么是服务端推送

服务端推送，指的是由服务器主动的向客户端发送消息。在应用层的 HTTP 协议实现中，“请求-响应” 是一个往返，它的起点来自客户端，因此在应用层之上无法实现简易的服务端推功能。当前解决服务端推送的方案有这几个：

- 客户端长轮询
- websocket 双向连接
- iframe 永久帧
- EventSource

长轮训虽然可以避免短轮训造成的服务端过载，但在服务端返回数据后仍需要客户端主动发起下一个长轮训请求，等待服务端响应，这样仍需要底层的连接建立而且服务端处理逻辑需要相应处理，不符合逻辑上的流程简单的服务端推送；

websocket 连接相对而言功能最强大，但是它对服务器的版本有要求，在可以使用 websocket 协议的服务器上尽量采用此种方式；

iframe 永久帧则是在在页面嵌入一个专用来接受数据的 iframe 页面，该页面由服务器输出相关信息，如服务器不停的向 iframe 中写入类似的 script 标签和数据，实现另一种形式的服务端推送。不过永久帧的技术会导致主页面的加载条始终处于 “loading” 状态，体验很差。

HTML5 规范中提供了服务端事件 EventSource，浏览器在实现了该规范的前提下创建一个 EventSource 连接后，便可收到服务端的发送的消息，这些消息需要遵循一定的格式，对于前端开发人员而言，只需在浏览器中侦听对应的事件皆可。

相比较上文中提到的 3 中实现方式，EventSource 流的实现方式对客户端开发人员而言非常简单，兼容性上出了IE系的浏览器（IE、Edge）外其他都良好；对于服务端，它可以兼容老的浏览器，无需 upgrade 为其他协议，在简单的服务端推送的场景下可以满足需求。在浏览器与服务端需要强交互的场景下，websocket 仍是不二的选择。

## EventSource 浏览器端

要在浏览器端创建一个 EventSource 链接，需要 new 一个 EventSource 对象，并且传入一个服务端的接口 URI 作为参数。

```js
const evtSource = new EventSource('http://localhost:8081/es');
```


其中，`http://localhost:8081/es` 为服务端吐出数据的接口。目前，EventSource 在大多数浏览器端不支持跨域，因此它不是一种跨域的解决方案。

默认 EventSource 对象通过侦听 `message` 事件获取服务端传来的消息，`open` 事件则在 http 连接建立后触发，`error` 事件会在通信错误（连接中断、服务端返回数据失败）的情况下触发。同时，EventSource 规范允许服务端指定自定义事件，客户端侦听该事件即可。

```js
evtSource.addEventListener('message', function(event) {
  console.log(event.data);
});
evtSource.addEventListener('error', function(event) {
  console.log(event);
})
```

## EventSource Type

EventSource 的 type 代表事件的类型，不同的 type 需要通过 addEventListener 侦听

```js
// EventSource type 为  action
evtSource.addEventListener('action', function(e) {
  console.log(e.data);
});
```

## 服务端

事件流的对应 MIME 格式为 text/event-stream，而且其基于 HTTP 长连接。针对 HTTP1.1 规范默认采用长连接，针对 HTTP1.0 的服务器需要特殊设置。

服务端返回数据需要特殊的格式，它分为四种消息类型：

- event - event 指定自定义消息的名称，如 `event: customMessage\n`
- data - data 指定具体的消息体，可以是对象或者字符串，如 `data: JSON.stringify(jsonObj)\n\n`，在消息体后面有两个换行符 `\n`，代表当前消息体发送完毕，一个换行符标识当前消息并未结束，浏览器需要等待后面数据的到来后再触发事件；
- id - 当前消息的标识符，可以不设置。一旦设置则在浏览器端的 EventSource 对象中就会有体现(假设服务端返回`id: 369\n`)，`eventSource.lastEventId == 369`。
- retry - 设置当前 http 连接失败后，重新连接的间隔。EventSource 规范规定，客户端在 http 连接失败后默认进行重新连接，重连间隔为3s，通过设置 retry 字段可指定重连间隔;

每个字段都有名称，紧接着有个 `:`。当出现一个没有名称的字段而只有 `:` 时，这就会被服务端理解为注释，并不会被发送至浏览器端，如 `:commision`。

由于 EventSource 是基于 HTTP 连接之上的，因此在一段没有数据的时期会出现超时问题。服务器默认 HTTP 超时时间为 2 分钟，在 node 端可以通过 `response.connection.setTimeou(0)` 设置为默认的 2min 超时， 因此需要服务端做心跳保活，否则客户端在连接超时的情况下出现 `net::ERR_INCOMPLETE_CHUNKED_ENCODING` 错误。通过阅读相关规范，发现注释行可以用来防止连接超时,服务器可以定期发送一条消息注释行, 以保持连接不断。

## 参考

- [MDN EventSource](https://developer.mozilla.org/zh-CN/docs/Web/API/EventSource)

## Keywords

`EventSource` `服务端推送`
