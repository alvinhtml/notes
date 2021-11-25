# AJAX

Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

AJAX 不是新的编程语言，而是一种使用现有标准的新方法。

AJAX 是与服务器交换数据并更新部分网页的艺术，通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

## 开始使用 AJAX

使用AJAX 需要创建 XMLHttpRequest 对象，所有现代浏览器（IE7+、Firefox、Chrome、Safari 以及 Opera）均内建 XMLHttpRequest 对象。

创建 XMLHttpRequest 对象的语法：

```js
const xhr = new XMLHttpRequest();
```

老版本的 Internet Explorer （IE5 和 IE6）使用 ActiveX 对象：

```js
const xhr = new ActiveXObject("Microsoft.XMLHTTP");
```

如需将请求发送到服务器，我们使用 XMLHttpRequest 对象的 open() 和 send() 方法：

```js
xhr.open("GET", "test1.txt", true);
xhr.send();
```


方法|描述
-|-
open(method, url, async) | 规定请求的类型、URL 以及是否异步处理请求。<br>async：true（异步）或 false（同步）
send(string) | 将请求发送到服务器。string：仅用于 POST 请求

如果需要像 HTML 表单那样 POST 数据，请使用 `setRequestHeader()` 来添加 HTTP 头。然后在 `send()` 方法中规定您希望发送的数据：

```js
xhr.open("POST", "ajax_test.asp",true);
xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
xhr.send("fname=Bill&lname=Gates");
```

使用 onreadystatechange 处理结果

```js
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4) {
    if (xhr.status === 200) {
      // success
    } else {
      // throw error
    }
  }
}
```


## XMLHttpRequest 对象

### 构造函数

```
XMLHttpRequest()
```

该构造函数用于初始化一个 XMLHttpRequest 实例对象。在调用下列任何其他方法之前，必须先调用该构造函数，或通过其他方式，得到一个实例对象。

### 属性

```
XMLHttpRequest.onreadystatechange
```

当 readyState 属性发生变化时，调用的 EventHandler




```
XMLHttpRequest.readyState 只读
```

返回 一个无符号短整型（unsigned short）数字，代表请求的状态码。

```
XMLHttpRequest.response 只读
```

返回一个 ArrayBuffer、Blob、Document，或 DOMString，具体是哪种类型取决于 XMLHttpRequest.responseType 的值。其中包含整个响应实体（response entity body）。

```
XMLHttpRequest.responseText 只读
```

返回一个 DOMString，该 DOMString 包含对请求的响应，如果请求未成功或尚未发送，则返回 null。

```
XMLHttpRequest.responseType
```

一个用于定义响应类型的枚举值（enumerated value）。

```
XMLHttpRequest.responseURL 只读
```

返回经过序列化（serialized）的响应 URL，如果该 URL 为空，则返回空字符串。

```
XMLHttpRequest.responseXML 只读
```

返回一个 Document，其中包含该请求的响应，如果请求未成功、尚未发送或时不能被解析为 XML 或 HTML，则返回 null。

```
XMLHttpRequest.status 只读
```

返回一个无符号短整型（unsigned short）数字，代表请求的响应状态。

```
XMLHttpRequest.statusText 只读
```

返回一个 DOMString，其中包含 HTTP 服务器返回的响应状态。与 XMLHTTPRequest.status 不同的是，它包含完整的响应状态文本（例如，"200 OK"）。

```
XMLHttpRequest.timeout
```

一个无符号长整型（unsigned long）数字，表示该请求的最大请求时间（毫秒），若超出该时间，请求会自动终止。

```
XMLHttpRequestEventTarget.ontimeout
```

当请求超时调用的 EventHandler。

```
XMLHttpRequest.upload 只读
```

XMLHttpRequestUpload，代表上传进度。

```
XMLHttpRequest.withCredentials
```

一个布尔值，用来指定跨域 Access-Control 请求是否应当带有授权信息，如 cookie 或授权 header 头。

### 事件处理器

作为 `XMLHttpRequest` 实例的属性之一，所有浏览器都支持 `onreadystatechange`

后来，许多浏览器实现了一些额外的事件（`onload`、`onerror`、`onprogress` 等）。

更多现代浏览器，包括 Firefox，除了可以设置 on* 属性外，也提供标准的监听器 addEventListener() API 来监听XMLHttpRequest 事件。

### 方法

```
XMLHttpRequest.abort()
```

如果请求已被发出，则立刻中止请求。

```
XMLHttpRequest.getAllResponseHeaders()
```

以字符串的形式返回所有用 CRLF 分隔的响应头，如果没有收到响应，则返回 null。

```
XMLHttpRequestgetResponseHeader(name)
```

返回包含指定响应头的字符串，如果响应尚未收到或响应中不存在该报头，则返回 null。

```
XMLHttpRequest.open()
```

初始化一个请求。该方法只能在 JavaScript 代码中使用，若要在 native code 中初始化请求，请使用 openRequest()。

```
XMLHttpRequest.overrideMimeType()
```

覆写由服务器返回的 MIME 类型。

```
xhrReq.open(method, url, async, user, password);
```

发送请求。如果请求是异步的（默认），那么该方法将在请求发送后立即返回。

```
XMLHttpRequest.setRequestHeader()
```

设置 HTTP 请求头的值。必须在 open() 之后、send() 之前调用 setRequestHeader() 方法。


### 监测进度

XMLHttpRequest 提供了各种在请求被处理期间发生的事件以供监听。这包括定期进度通知、 错误通知，等等。

- progress 检索的数据量发生了变化。
- load 传输完成，所有数据保存在 response 中。

```js
var oReq = new XMLHttpRequest();

oReq.addEventListener("progress", updateProgress);
oReq.addEventListener("load" , transferComplete);
oReq.addEventListener("error", transferFailed  );
oReq.addEventListener("abort", transferCanceled);

oReq.open();

// ...

// 服务端到客户端的传输进程（下载）
function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total * 100;
    // ...
  } else {
    // 总大小未知时不能计算进程信息
  }
}

function transferComplete(evt) {
  console.log("The transfer is complete.");
}

function transferFailed(evt) {
  console.log("An error occurred while transferring the file.");
}

function transferCanceled(evt) {
  console.log("The transfer has been canceled by the user.");
}
```




## Fetch API

Fetch API 是 XMLHttpRequest 的现代替代方法，用于从服务器检索资源。

与XMLHttpRequest不同，它具有更强大的功能集和更有意义的名称。

由于其语法和结构，Fetch 更灵活且易于使用的。

### 语法

```
Promise<Response> fetch(input[, init]);
```

**input**

定义要获取的资源。这可能是：

  1. 一个 USVString 字符串，包含要获取资源的 URL。一些浏览器会接受 blob: 和 data: 作为 schemes.
  2. 一个 Request 对象。

**init (可选)**

一个配置项对象，包括所有对请求的设置。可选的参数有：

- `method`: 请求使用的方法，如 GET、POST。
- `headers`: 请求的头信息，形式为 `Headers` 的对象或包含 `ByteString` 值的对象字面量。
- `body`: 请求的 body 信息：可能是一个 `Blob`、`BufferSource`、`FormData`、`URLSearchParams` 或者 `USVString` 对象。*注意 GET 或 HEAD 方法的请求不能包含 body 信息*。
- `mode`: 请求的模式，如 `cors`、 `no-cors` 或者 `same-origin`。
- `credentials`: 请求的 `credentials`，如 `omit`、`same-origin` 或者 `include`。为了在当前域名内自动发送 cookie ， 必须提供这个选项。
- `cache`:  请求的 cache 模式: `default`、 `no-store`、 `reload` 、 `no-cache` 、 `force-cache` 或者 `only-if-cached` 。
- `redirect`: 可用的 redirect 模式: follow (自动重定向), error (如果产生重定向将自动终止并且抛出一个错误）, 或者 manual (手动处理重定向). 在Chrome中默认使用follow（Chrome 47之前的默认值是manual）。
- `referrer`: 一个 USVString 可以是 no-referrer、client或一个 URL。默认是 client。
- `referrerPolicy`: 指定了HTTP 头部 referer 字段的值。可能为以下值之一： `no-referrer`、 `no-referrer-when-downgrade`、 `origin`、 `origin-when-cross-origin`、 `unsafe-url` 。
- `integrity`: 包括请求的  subresource integrity 值 （ 例如： sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=）。

## Request()

这是一个实验中的功能

此功能某些浏览器尚在开发中，请参考浏览器兼容性表格以得到在不同浏览器中适合使用的前缀。由于该功能对应的标准文档可能被重新修订，所以在未来版本的浏览器中该功能的语法和行为可能随之改变。

Request() 构造器创建一个新的Request 对象。

语法

```
var myRequest = new Request(input[, init]);
```

fetch() 方法的参数与 Request() 构造器是一样的。


## 常用AJAX库

### SuperAgent

SuperAgent是一个轻量级和渐进式AJAX库，更侧重于可读性和灵活性。


### Axios

Axios是一个基于XMLHttpRequest构建的JavaScript库，用于进行AJAX调用。它允许您从浏览器和服务器发出HTTP请求。此外，它还支持ES6原生的Promise API。 Axios的其他突出特点包括：

- 拦截请求和响应。
- 使用promise来转换请求和响应数据。
- 自动转换JSON数据。
- 取消实时请求。

### Request 简化的 HTTP 客户端

Request 是进行 HTTP 调用的最简单的方法之一。结构和语法与在 Node.js 中处理请求的方式非常相似。目前，该项目在 GitHub 上有 18K 星。
