# PWA 基本介绍

PWA（Progressive Web App）是一种理念，使用多种技术来增强 web app 的功能，可以让网站的体验变得更好，能够模拟一些原生功能，比如通知推送。在移动端利用标准化框架，让网页应用呈现和原生应用相似的体验。

## PWA 特点

1. 可靠——即时加载，即使在不确定的网络条件下也不会受到影响。

当用户从主屏幕启动时，service work 可以立即加载渐进式 Web 应用程序，完全不受网络环境的影响。service work 就像一个客户端代理，它控制缓存以及如何响应资源请求逻辑，通过预缓存关键资源，可以消除对网络的依赖，确保为用户提供即时可靠的体验。

2. 快速

据统计，如果站点加载时间超过 3s，53% 的用户会放弃等待。页面展现之后，用户期望有平滑的体验，过渡动画和快速响应。

3. 沉浸式体验—— 感觉就像设备上的原生应用程序，具有沉浸式的用户体验。

渐进式 Web 应用程序可以安装并在用户的主屏幕上，无需从应用程序商店下载安装。他们提供了一个沉浸式的全屏幕体验，甚至可以重新与用户接触的 Web 推送通知。

## PWA 技术点

### web app manifest

web 程序应用清单 [manifest_mdn](https://developer.mozilla.org/zh-CN/docs/Web/Manifest)

Web 应用程序清单在一个 JSON 文本文件中提供有关应用程序的信息（如名称，作者，图标和描述）。manifest 的目的是将 Web 应用程序安装到设备的主屏幕，为用户提供更快的访问和更丰富的体验。

manifest.json 常见配置:

- `name`：指定应用名称
- `short_name`：应用短名称，主屏显示
- `start_url`：用户启动程序加载的 url
- `icons`：各个平台 icon
- `background_color`：用户指定的背景颜色
- `theme_color`：主题色
- `display`： app 显示方式

manifest 范例:

```html
<link rel="manifest" href="/manifest.json" />
```

```json
{
  "name": "HackerWeb",
  "short_name": "HackerWeb",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#fff",
  "description": "A simply readable Hacker News app.",
  "icons": [
    {
      "src": "images/touch/homescreen48.png",
      "sizes": "48x48",
      "type": "image/png"
    },
    {
      "src": "images/touch/homescreen72.png",
      "sizes": "72x72",
      "type": "image/png"
    }
  ],
  "related_applications": [
    {
      "platform": "web"
    },
    {
      "platform": "play",
      "url": "https://play.google.com/store/apps/details?id=cheeaun.hackerweb"
    }
  ]
}
```

### service worker

[Worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 接口是 Web Workers API 的一部分，指的是一种可由脚本创建的后台任务，任务执行中可以向其创建者收发信息。要创建一个 Worker ，只须调用 Worker(URL) 构造函数，函数参数 `URL` 为指定的脚本。

下面的代码通过构造函数 Worker() 创建了一个 Worker 对象。

```js
var myWorker = new Worker('worker.js')
var first = document.querySelector('#number1')
var second = document.querySelector('#number2')

first.onchange = function () {
  myWorker.postMessage([first.value, second.value])
  console.log('Message posted to worker')
}
```

#### service worker 特点

- 一旦 install, 就永久存储，除非 unregister
- 用到的时候唤醒，不用的时候自动休眠
- 可编程式拦截请求，缓存文件
- 必须 https 请求（localhost）

#### 注册步骤

- window.onload 后注册
- navigator 对象内置 serviceWorker
- 注意可能低版本兼容性问题 if()
- 注册 service worker, 返回的是一个 promise 对象

```js
window.addEventListener('load', () => {
  if ('serviceWorker' in navigator) {
    // Register a service worker hosted at the root of the
    // site using the default scope.
    navigator.serviceWorker.register('/sw.js').then(
      function (registration) {
        console.log('Service worker registration succeeded:', registration)
      },
      /*catch*/ function (error) {
        console.log('Service worker registration failed:', error)
      }
    )
  } else {
    console.log('Service workers are not supported.')
  }
})
```

#### 生命周期

- install 会在 service worker 注册成功后触发，主要用于缓存资源
- activate 事件会在 service worker 激活后触发，一般用于删除旧的资源
- fetch 发送请求的时候触发

```js
self.addEventListener('install', (event) => {
  console.log('install', event)
  // service worker 跳过等待   直接进入activate
  event.waitUntil(self.skipWaiting())
})

self.addEventListener('activate', (event) => {
  console.log('activate', event)

  // 表示service worker激活后，立即获取控制器
  event.waitUntil(self.clients.claim())
})

self.addEventListener('fetch', (event) => {
  console.log('fetch', event)
})
```

#### cache storage

[CacheStorage](https://developer.mozilla.org/zh-CN/docs/Web/API/CacheStorage) 接口表示 Cache 对象的存储。它提供了一个 ServiceWorker 、其它类型 worker 或者 window 范围内可以访问到的所有命名 cache 的主目录（它并不是一定要和 service workers 一起使用，即使它是在 service workers 规范中定义的），并维护一份字符串名称到相应 Cache 对象的映射。

CacheStorage 同样暴露了 CacheStorage.open() 和 CacheStorage.match()方法。使用 CacheStorage.open() 获取 Cache 实例。使用 CacheStorage.match() 检查给定的 Request 是否是 CacheStorage 对象跟踪的任何 Cache 对象中的键。

### notification

[Notifications API](https://developer.mozilla.org/zh-CN/docs/Web/API/Notification) 的通知接口用于向用户配置和显示桌面通知。

示例：

假定有如下 HTML：

```html
<button onclick="notifyMe()">Notify me!</button>
```

接下来发送一条通知：

```js
function notifyMe() {
  // 先检查浏览器是否支持
  if (!('Notification' in window)) {
    alert('This browser does not support desktop notification')
  }

  // 检查用户是否同意接受通知
  else if (Notification.permission === 'granted') {
    // If it's okay let's create a notification
    var notification = new Notification('Hi there!')
  }

  // 否则我们需要向用户获取权限
  else if (Notification.permission !== 'denied') {
    Notification.requestPermission().then(function (permission) {
      // 如果用户接受权限，我们就可以发起一条消息
      if (permission === 'granted') {
        var notification = new Notification('Hi there!')
      }
    })
  }

  // 最后，如果执行到这里，说明用户已经拒绝对相关通知进行授权
  // 出于尊重，我们不应该再打扰他们了
}
```
