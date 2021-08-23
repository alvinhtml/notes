# HTML / CSS

## meta

meta 元素定义的元数据的类型包括以下几种：

- 如果设置了 name 属性，meta 元素提供的是文档级别（document-level）的元数据，应用于整个页面。
- 如果设置了 http-equiv 属性，meta 元素则是编译指令，提供的信息与类似命名的HTTP头部相同。
- 如果设置了 charset 属性，meta 元素是一个字符集声明，告诉文档使用哪种字符编码。
- 如果设置了 itemprop 属性，meta 元素提供用户定义的元数据。


### viewport

viewport 是用户网页的可视区域。

viewport 翻译为中文可以叫做"视区"。

手机浏览器是把页面放在一个虚拟的"窗口"（viewport）中，通常这个虚拟的"窗口"（viewport）比屏幕宽，这样就不用把每个网页挤到很小的窗口中（这样会破坏没有针对手机浏览器优化的网页的布局），用户可以通过平移和缩放来看网页的不同部分。


```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

- width：控制 viewport 的大小，可以指定的一个值，如 600，或者特殊的值，如 device-width 为设备的宽度（单位为缩放为 100% 时的 CSS 的像素）。
- height：和 width 相对应，指定高度。
- initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。
- maximum-scale：允许用户缩放到的最大比例。
- minimum-scale：允许用户缩放到的最小比例。
- user-scalable：用户是否可以手动缩放。

### apple-mobile-web-app-capable

WebApp全屏模式：伪装app，离线应用。

```html
<meta name="apple-mobile-web-app-capable" content="yes" /> <!-- 启用 WebApp 全屏模式 -->
```

### apple-mobile-web-app-status-bar-style

隐藏状态栏/设置状态栏颜色：只有在开启WebApp全屏模式时才生效。content的值为default | black | black-translucent 。

```html
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
```
### apple-mobile-web-app-title

添加到主屏后的标题

```html
<meta name="apple-mobile-web-app-title" content="标题">
```

### format-detection

忽略数字自动识别为电话号码

```html
<meta name="format-detection" content="telephone=no" />
```

忽略识别邮箱

```html
<meta name="format-detection" content="email=no" />
```

### apple-itunes-app

添加智能 App 广告条 Smart App Banner：告诉浏览器这个网站对应的app，并在页面上显示下载banner(如下图)。参考文档

```html
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
```

### 申明编码

```html
<meta charset='utf-8'>
```


### Expires(期限)

说明：可以用于设定网页的到期时间。一旦网页过期，必须到服务器上重新传输。

```html
＜meta http-equiv="expires" content="Wed, 20 Jun 2007 22:33:00 GMT" /＞  
```

注意：必须使用GMT的时间格式。

### Pragma(cache模式)

说明：是用于设定禁止浏览器从本地机的缓存中调阅页面内容，设定后一旦离开网页就无法从Cache中再调出

```html
＜meta http-equiv="Pragma" content="no-cache"＞  
```

注意：这样设定，访问者将无法脱机浏览。

### Refresh(刷新)

说明：自动刷新并指向新页面。

```html
＜meta http-equiv="Refresh" content="2；URL=http://www.net.cn/"＞  
```

注意：其中的2是指停留2秒钟后自动刷新到URL网址。

### Set-Cookie(cookie设定)

说明：如果网页过期，那么存盘的cookie将被删除。

```html
＜meta http-equiv="Set-Cookie" content="cookievalue=xxx;expires=Wednesday, 20-Jun-2007 22:33:00 GMT； path=/"＞  
```

注意：必须使用GMT的时间格式。

### Window-target(显示窗口的设定)

说明：强制页面在当前窗口以独立页面显示。

```html
＜meta http-equiv="Window-target" content="_top"＞  
```

注意：用来防止别人在框架里调用自己的页面。

### content-Type(显示字符集的设定)

说明：设定页面使用的字符集。


```html
＜meta http-equiv="content-Type" content="text/html; charset=gb2312"＞
```

### Pics-label(网页等级评定)

```html
<meta http-equiv="Pics-label" contect="">
```

说明：在IE的internet选项中有一项内容设置，可以防止浏览一些受限制的网站，而网站的限制级别就是通过meta属性来设置的。

### Page_Enter、Page_Exit

设定进入页面时的特殊效果

```html
<meta http-equiv="Page-Enter"    contect="revealTrans(duration=1.0,transtion=    12)">    
```

设定离开页面时的特殊效果

```html
<meta http-equiv="Page-Exit"    contect="revealTrans(duration=1.0,transtion=    12)">    
```

Duration 的值为网页动态过渡的时间，单位为秒。
Transition 是过渡方式，它的值为0到23，分别对应24种过渡方式。如下表：

- 0    盒状收缩
- 1    盒状放射  
- 2    圆形收缩
- 3    圆形放射  
- 4    由下往上
- 5    由上往下  
- 6    从左至右
- 7    从右至左  
- 8    垂直百叶窗
- 9    水平百叶窗  
- 10   水平格状百叶窗
- 11   垂直格状百叶窗  
- 12   随意溶解
- 13   从左右两端向中间展开  
- 14   从中间向左右两端展开
- 15   从上下两端向中间展开  
- 16   从中间向上下两端展开
- 17   从右上角向左下角展开  
- 18   从右下角向左上角展开
- 19   从左上角向右下角展开  
- 20   从左下角向右上角展开
- 21   水平线状展开  
- 22   垂直线状展开
- 23   随机产生一种过渡方式  

### 清除缓存（再访问这个网站要重新下载！）

```html
<meta http-equiv="cache-control" content="no-cache">  
```

### 设定网页的到期时间

```html
<meta http-equiv="expires" content="0">  
```

### 关键字,给搜索引擎用的

```html
<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">  
```

### 页面描述

```html
<meta http-equiv="description" content="This is my page">
```
