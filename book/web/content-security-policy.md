# Content-Security-Policy

## CSP 是什么

CSP 指的是内容安全策略，为了缓解很大一部分潜在的跨站脚本问题，浏览器的扩展程序系统引入了内容安全策略（CSP）的一般概念。这将引入一些相当严格的策略，会使扩展程序在默认情况下更加安全，开发者可以创建并强制应用一些规则，管理网站允许加载的内容。简单来说，就是我们能够规定，我们的网站只接受我们指定的请求资源。

## CSP 的意义

防 XSS 等攻击的利器。CSP 的实质就是白名单制度，开发者明确告诉客户端，哪些外部资源可以加载和执行，等同于提供白名单。它的实现和执行全部由浏览器完成，开发者只需提供配置。CSP 大大增强了网页的安全性。攻击者即使发现了漏洞，也没法注入脚本，除非还控制了一台列入了白名单的可信主机。

## CSP 的分类

- Content-Security-Policy
  - 配置好并启用后，不符合 CSP 的外部资源就会被阻止加载。
- Content-Security-Policy-Report-Only
  - 表示不执行限制选项，只是记录违反限制的行为。它必须与 report-uri 选项配合使用。

## CSP 的使用

在 HTTP Header 上使用（首选）

```
"Content-Security-Policy:" 策略
"Content-Security-Policy-Report-Only:" 策略
```

在 HTML 上使用

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src * 'unsafe-eval' 'unsafe-inline'; connect-src *;"
/>
<meta http-equiv="content-security-policy-report-only" content="策略" />
```

Meta 标签与 HTTP 头只是行式不同而作用是一致的，如果 HTTP 头与 Meta 定义同时存在，则优先采用 HTTP 中的定义。

如果用户浏览器已经为当前文档执行了一个 CSP 的策略，则会跳过 Meta 的定义。如果 META 标签缺少 content 属性也同样会跳过。

## 常用的 CSP 指令

| 指令        | 指令和指令值示例         | 指令说明                                                                                                                                                             |
| ----------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| default-src | ‘self’ cdn.guangzhul.com | 默认加载策略                                                                                                                                                         |
| script-src  | ‘self’ js.guangzhul.com  | 对 JavaScript 的加载策略。                                                                                                                                           |
| style-src   | ‘self’ css.guangzhul.com | 对样式的加载策略。                                                                                                                                                   |
| img-src     | ‘self’ img.guangzhul.com | 对图片的加载策略。                                                                                                                                                   |
| connect-src | ‘self’                   | 对 Ajax、WebSocket 等请求的加载策略。不允许的情况下，浏览器会模拟一个状态为 400 的响应。                                                                             |
| font-src    | font.cdn.guangzhul.com   | 针对 WebFont 的加载策略。                                                                                                                                            |
| object-src  | ‘self’                   | 针对 、 或 等标签引入的 flash 等插件的加载策略。                                                                                                                     |
| media-src   | media.cdn.guangzhul.com  | 针对媒体引入的 HTML 多媒体的加载策略。                                                                                                                               |
| frame-src   | ‘self’                   | 针对 frame 的加载策略。                                                                                                                                              |
| report-uri  | /report-uri              | 告诉浏览器如果请求的资源不被策略允许时，往哪个地址提交日志信息。 特别的：如果想让浏览器只汇报日志，不阻止任何内容，可以改用 Content-Security-Policy-Report-Only 头。 |

## 其他的 CSP 指令

| 指令            | 指令说明                                                      |
| --------------- | ------------------------------------------------------------- |
| sandbox         | 设置沙盒环境                                                  |
| child-src       | 主要防御 \<frame\>,\<iframe\>                                 |
| form-action     | 主要防御 \<form\>                                             |
| frame-ancestors | 主要防御 \<frame\>,\<iframe\>,\<object\>,\<embed\>,\<applet\> |
| plugin-types    | 主要防御 \<object\>,\<embed\>,\<applet\>                      |

## CSP 指令值

| 指令值            | 指令和指令值示例           | 指令值说明                                                                             |
| ----------------- | -------------------------- | -------------------------------------------------------------------------------------- |
| \*                | img-src \*                 | 允许任何内容。                                                                         |
| ‘none’            | img-src ‘none’             | 不允许任何内容。                                                                       |
| ‘self’            | img-src ‘self’             | 允许来自相同来源的内容（相同的协议、域名和端口）。                                     |
| data:             | img-src data:              | 允许 data: 协议（如 base64 编码的图片）。                                              |
| www.guangzhul.com | img-src img.guangzhul.com  | 允许加载指定域名的资源。                                                               |
| \*.guangzhul.com  | img-src \*.guangzhul.com   | 允许加载 guangzhul.com 任何子域的资源。                                                |
| ‘unsafe-inline’   | script-src ‘unsafe-inline’ | 允许加载 inline 资源（例如常见的 style 属性，onclick，inline js 和 inline css 等等）。 |
| ‘unsafe-eval’     | script-src ‘unsafe-eval’   | 允许加载动态 js 代码，例如 eval()。                                                    |

## Content Security Policy 入门教程

[Content Security Policy 入门教程](https://www.ruanyifeng.com/blog/2016/09/csp.html)

## 示例

```bash


> GET / HTTP/1.1
> Host: www.oa.com
>
< HTTP/1.1 200 OK
< Content-Type: text/html
# www.oa.com 回应 Content-Security-Policy 头表示允许执行 www.huafoun.com 下的 script 和 Ajax
< Content-Security-Policy: script-src www.huafoun.com; content-src www.huafoun.com;
<
<!DOCTYPE html><html><head><script src="http://www.huafoun.com/aaa.js" /> ...




# huafoun.com 回应 Access-Control-Allow-Origin 头表示允许 oa.com 跨域访问自己的资源
> GET / HTTP/1.1
> Host: www.huafoun.com
>
< HTTP/1.1 200 OK
< Content-Type: application/json;
< Access-Control-Allow-Origin: *.oa.com


```
