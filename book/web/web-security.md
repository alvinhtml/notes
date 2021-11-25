# 前端安全

在移动互联网时代，前端人员除了传统的 XSS、CSRF 等安全问题之外，又时常遭遇网络劫持、非法调用 Hybrid API 等新型安全问题。当然，浏览器自身也在不断在进化和发展，不断引入 CSP、Same-Site Cookies 等新技术来增强安全性，但是仍存在很多潜在的威胁。

## XSS

### 什么是 XSS

Cross-Site Scripting（跨站脚本攻击）简称 XSS，是一种代码注入攻击。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等，进而危害数据安全。

### XSS 分类

根据攻击的来源，XSS 攻击可分为存储型、反射型和 DOM 型三种。

- 存储型：攻击者将恶意代码提交到目标网站的数据库中
- 反射型：攻击者构造出特殊的 URL，其中包含恶意代码
- DOM型：攻击者构造出特殊的 URL，其中包含恶意代码，前端 JavaScript 取出 URL 中的恶意代码并执行

DOM 型 XSS 跟前两种 XSS 的区别：DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。


### XSS 注入的方法

XSS 攻击主要是通过用户输入向代码中注入脚本。

- 在 HTML 中内嵌的文本中，恶意内容以 script 标签形成注入。
- 在内联的 JavaScript 中，拼接的数据突破了原本的限制（字符串，变量，方法名等）。
- 在标签属性中，恶意内容包含引号，从而突破属性值的限制，注入其他属性或者标签。
- 在标签的 href、src 等属性中，包含 javascript: 等可执行代码。
- 在 onload、onerror、onclick 等事件中，注入不受控制代码。

### 预防 XSS 攻击

1. 输入过滤
2. 转义 HTML
3. 预防 DOM 型 XSS 攻击，在使用 .innerHTML、.outerHTML、document.write() 时要特别小心，尽量使用 .textContent、.setAttribute() 等
4. 设置 Content Security Policy

严格的 CSP 在 XSS 的防范中可以起到以下的作用：

- 禁止加载外域代码，防止复杂的攻击逻辑。
- 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
- 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
- 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
- 合理使用上报可以及时发现 XSS，利于尽快修复问题。

## CSRF

### 什么是CSRF

CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

一个典型的CSRF攻击有着如下的流程：

1. 受害者登录 a.com，并保留了登录凭证（Cookie）。
2. 攻击者引诱受害者访问了 b.com。
3. b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带 a.com 的 Cookie。
4. a.com 接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。
5. a.com 以受害者的名义执行了 act=xx。
6. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让 a.com 执行了自己定义的操作。

### 几种常见的攻击类型

攻击者先在 b.com 设下了三种类型的陷阱，并引诱受害者访问 b.com。

```html
<!-- GET类型的CSRF -->
<img src="![](https://www.a.com?act=xxx)" />

<!-- POST类型的CSRF -->
<form action="https://www.a.com" method=POST>
    <input type="hidden" name="act" value="xxx" />
</form>
<script> document.forms[0].submit(); </script>

<!-- 链接类型的CSRF -->
<a href="https://www.a.com?act=xxx" taget="_blank">重磅消息！<a/>
```

### CSRF的特点

1. 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生。
2. 攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；而不是直接窃取数据。
3. 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”。
4. 跨站请求可以用各种方式：图片URL、超链接、CORS、Form提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。

CSRF通常是跨域的，因为外域通常更容易被攻击者掌控。但是如果本域下有容易被利用的功能，比如可以 **发图和链接的论坛和评论区**，攻击可以直接在本域下进行，而且这种攻击更加危险。

### 防护策略

1. CSRF Token
2. 同源检测
  - 既然 CSRF 大多来自第三方网站，那么我们就直接禁止外域（或者不受信任的域名）对我们发起请求
  - 使用 Origin Header 确定来源域名
  - 使用 Referer Header 确定来源域名
3. 双重Cookie验证
  1. 在用户访问网站页面时，向请求域名注入一个Cookie，内容为随机字符串（例如csrfcookie=v8g9e4ksfhw）。
  2. 在前端向后端发起请求时，取出Cookie，并添加到URL的参数中（接上例POST a.com?csrfcookie=v8g9e4ksfhw）。
  3. 后端接口验证Cookie中的字段与URL参数中的字段是否一致，不一致则拒绝。
4. Samesite Cookie属性

### Samesite Cookie

为了从源头上解决CSRF攻击，Google 起草了一份草案来改进 HTTP 协议，那就是为 Set-Cookie 响应头新增 Samesite 属性，它用来标明这个 Cookie 是个同站 Cookie，同站 Cookie 只能作为第一方 Cookie，不能作为第三方Cookie，Samesite 有两个属性值，分别是 Strict 和 Lax:


1. 严格模式 Samesite=Strict 表明这个 Cookie 在任何情况下都不可能作为第三方 Cookie
  - 浏览器在任何跨域请求中都不会携带Cookie，新标签重新打开也不携带，所以说CSRF攻击基本没有机会。
  - 跳转子域名或者是新标签重新打开新标签，都需要重新登录，用户体验不好。
2. 宽松模式 Samesite=Lax 假如这个请求改变了当前页面或者打开了新页面, 且同时是个GET请求，则这个Cookie可以作为第三方Cookie.
  - 通过页面跳转过来的时候可以使用Cookie，可以保障外域连接打开页面时用户的登录状态。但相应的，其安全性也比较低。

```
Set-Cookie: foo=1; Samesite=Strict
Set-Cookie: bar=2; Samesite=Lax
Set-Cookie: baz=3
```

#### SamesiteCookie 缺陷

1. 兼容性不是很好，现阶段除了从新版Chrome和Firefox支持以外，Safari以及iOS Safari都还不支持。
2. SamesiteCookie 在子域 me.a.com 下不能使用 a.com 下种植的 SamesiteCookie。多个子域名不能共享 SamesiteCookie 在主域名存储用户登录信息，每个子域名都需要用户重新登录一次。


### 防止网站被利用

- 严格管理所有的上传接口，防止任何预期之外的上传内容（例如HTML）。
- 添加 `Header X-Content-Type-Options: nosniff` 防止黑客上传HTML内容的资源（例如图片）被解析为网页。
- 对于用户上传的图片，进行转存或者校验。不要直接使用用户填写的图片链接。
- 当前用户打开其他用户填写的链接时，需告知风险（这也是很多论坛不允许直接在内容中发布外域链接的原因之一，不仅仅是为了用户留存，也有安全考虑）。

### 个人用户CSRF安全的建议

经常上网的个人用户，可以采用以下方法来保护自己：

- 使用网页版邮件的浏览邮件或者新闻也会带来额外的风险，因为查看邮件或者新闻消息有可能导致恶意代码的攻击。
- 尽量不要打开可疑的链接，一定要打开时，使用不常用的浏览器或者 Chrome 无痕浏览模式。
