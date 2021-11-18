# JavaScript 常用工具函数

## 获取元素相对于窗口的位置
### 获取元素相对于窗口最左边的位置

```js
function offsetTop( elements ) {
    var top = elements.offsetTop;
    var parent = elements.offsetParent;
    while( parent != null ){
        top += parent.offsetTop;
        parent = parent.offsetParent;
    };
    return top;
};
```

### 获取元素相对于窗口最顶部的位置

```js
function offsetLeft( elements ) {
    var left = elements.offsetLeft;
    var parent = elements.offsetParent;
    while( parent != null ){
        left += parent.offsetLeft;
        parent = parent.offsetParent;
    };
    return left;
};
```

## IP地址与整数的相互转换
### IP转成整型

```js
function _ip2int(ip) {
    var num = 0;
    ip = ip.split(".");
    num = Number(ip[0]) * 256 * 256 * 256 + Number(ip[1]) * 256 * 256 + Number(ip[2]) * 256 + Number(ip[3]);
    num = num >>> 0;
    return num;
}
```

### 整型解析为IP地址

```js
function _int2iP(num) {
    var str;
    var tt = new Array();
    tt[0] = (num >>> 24) >>> 0;
    tt[1] = ((num << 8) >>> 24) >>> 0;
    tt[2] = (num << 16) >>> 24;
    tt[3] = (num << 24) >>> 24;
    str = String(tt[0]) + "." + String(tt[1]) + "." + String(tt[2]) + "." + String(tt[3]);
    return str;
}
```

## 字符串base64编码、解码

```js
var keyStr = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

//将Ansi编码的字符串进行Base64编码
function encode64(input) {
    var output = "";
    var chr1, chr2, chr3 = "";
    var enc1, enc2, enc3, enc4 = "";
    var i = 0;
    do {
        chr1 = input.charCodeAt(i++);
        chr2 = input.charCodeAt(i++);
        chr3 = input.charCodeAt(i++);
        enc1 = chr1 >> 2;
        enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);
        enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);
        enc4 = chr3 & 63;
        if (isNaN(chr2)) {
            enc3 = enc4 = 64;
        } else if (isNaN(chr3)) {
            enc4 = 64;
        }
        output = output + keyStr.charAt(enc1) + keyStr.charAt(enc2) + keyStr.charAt(enc3) + keyStr.charAt(enc4);
        chr1 = chr2 = chr3 = "";
        enc1 = enc2 = enc3 = enc4 = "";
    } while (i < input.length);
    return output;
}
//将Base64编码字符串转换成Ansi编码的字符串
function decode64(input) {
    var output = "";
    var chr1, chr2, chr3 = "";
    var enc1, enc2, enc3, enc4 = "";
    var i = 0;

    if (input.length % 4 != 0) {
        return "";
    }
    var base64test = /[^A-Za-z0-9\+\/\=]/g;
    if (base64test.exec(input)) {
        return "";
    }
    do {
        enc1 = keyStr.indexOf(input.charAt(i++));
        enc2 = keyStr.indexOf(input.charAt(i++));
        enc3 = keyStr.indexOf(input.charAt(i++));
        enc4 = keyStr.indexOf(input.charAt(i++));
        chr1 = (enc1 << 2) | (enc2 >> 4);
        chr2 = ((enc2 & 15) << 4) | (enc3 >> 2);
        chr3 = ((enc3 & 3) << 6) | enc4;


        output = output + String.fromCharCode(chr1);
        if (enc3 != 64) {
            output += String.fromCharCode(chr2);
        }
        if (enc4 != 64) {
            output += String.fromCharCode(chr3);
        }
        chr1 = chr2 = chr3 = "";
        enc1 = enc2 = enc3 = enc4 = "";
    } while (i < input.length);
    return output;
}


function utf16to8(str) {
    var out, i, len, c;

    out = "";
    len = str.length;
    for(i = 0; i < len; i++) {
        c = str.charCodeAt(i);
        if ((c >= 0x0001) && (c <= 0x007F)) {
            out += str.charAt(i);
        } else if (c > 0x07FF) {
            out += String.fromCharCode(0xE0 | ((c >> 12) & 0x0F));
            out += String.fromCharCode(0x80 | ((c >>  6) & 0x3F));
            out += String.fromCharCode(0x80 | ((c >>  0) & 0x3F));
        } else {
            out += String.fromCharCode(0xC0 | ((c >>  6) & 0x1F));
            out += String.fromCharCode(0x80 | ((c >>  0) & 0x3F));
        }
    }
    return out;
}


function utf8to16(str) {
    var out, i, len, c;
    var char2, char3;


    out = "";
    len = str.length;
    i = 0;
    while(i < len) {
        c = str.charCodeAt(i++);
        switch(c >> 4) {
            case 0: case 1: case 2: case 3: case 4: case 5: case 6: case 7:
            // 0xxxxxxx
            out += str.charAt(i-1);
            break;
            case 12: case 13:
            // 110x xxxx   10xx xxxx
            char2 = str.charCodeAt(i++);
            out += String.fromCharCode(((c & 0x1F) << 6) | (char2 & 0x3F));
            break;
            case 14:
            // 1110 xxxx  10xx xxxx  10xx xxxx
            char2 = str.charCodeAt(i++);
            char3 = str.charCodeAt(i++);
            out += String.fromCharCode(((c & 0x0F) << 12) |
            ((char2 & 0x3F) << 6) |
            ((char3 & 0x3F) << 0));
            break;
        }
    }
    return out;
}


// 测试代码 开始
var de = encode64(utf16to8('{"search":"中文转码","order":"base64","curpage":1}'));
document.writeln(de+"
");
var ee = utf8to16(decode64(de))
document.writeln(ee);
```

## JavaScript 获取包含中文的字串符实际长度
中文占三个字符长度

```js
String.prototype.getLength = function() {
    var realLen  = 0,
        charCode = -1;
    for (var i = this.length; i--;) {
        charCode = this.charCodeAt(i);
        if(charCode >= 0 && charCode <= 128){
            realLen += 1;
        }else{
            realLen += 3;
        }
    };
    return realLen;
}
```

## RGB与HEX互相转换函数
### rgb to hex
```js
function rgbToHex(r, g, b) {
  const hex = ((r << 16) | (g << 8) | b).toString(16);
  return `#${new Array(Math.abs(hex.length - 7)).join('0')}${hex}`;
}
```
### hex to rgb
```js
function hexToRgb(hex) {
  const rgb = [];
  for (let i = 1; i < 7; i += 2) {
    rgb.push(parseInt(`0x${hex.slice(i, i + 2)}`, 16));
  }
  return rgb;
}
```
### 计算渐变过渡色
```js
function gradient(startColor, endColor, step) {
  // 将hex转换为rgb
  const sColor = hexToRgb(startColor);
  const eColor = hexToRgb(endColor);

  // 计算R\G\B每一步的差值
  const rStep = (eColor[0] - sColor[0]) / step;
  const gStep = (eColor[1] - sColor[1]) / step;
  const bStep = (eColor[2] - sColor[2]) / step;

  const gradientColorArr = [];

  for (let i = 0; i < step; i++) {
    // 计算每一步的hex值
    gradientColorArr.push(rgbToHex(parseInt((rStep * i) + sColor[0], 10), parseInt((gStep * i) + sColor[1], 10), parseInt((bStep * i) + sColor[2], 10)));
  }
  return gradientColorArr;
}
```

## JavaScript 数字字符串补 0
```js
/*!
 ** 方法名称：toLead
 *
 ** 配置参数
 *      len -   必须，整数，规定前置零的位数。
 */
Number.prototype.toLead = function(len) {
    return new Array(Math.abs(this.toString().length-(len+1))).join("0")+this;
};
```

### JavaScript 日期时间格式化
```js
/*!
 ** 方法名称：Format
 *
 ** 配置参数
 *      format -    必须，规定时间的格式。
 *
 *      Y - 年，四位数字; 如: "1999"
 *      m - 月份，二位数字，若不足二位则在前面补零; 如: "01" 至 "12"
 *      n - 月份，二位数字，若不足二位则不补零; 如: "1" 至 "12"
 *      d - 几日，二位数字，若不足二位则前面补零; 如: "01" 至 "31"
 *      j - 几日，二位数字，若不足二位不补零; 如: "1" 至 "31"
 *
 *      h - 12 小时制的小时; 如: "01" 至 "12"
 *      H - 24 小时制的小时; 如: "00" 至 "23"
 *      g - 12 小时制的小时，不足二位不补零; 如: "1" 至 12"
 *      G - 24 小时制的小时，不足二位不补零; 如: "0" 至 "23"
 *      i - 分钟; 如: "00" 至 "59"
 *      s - 秒; 如: "00" 至 "59"
 *
 */
Date.prototype.Format = function(format) {
    if(!/^[\\\/:YHGmndhgijs-\s\u5e74\u6708\u65e5\u65f6\u5206\u79d2]+$/.test(format)){
        return this;
    }else{
        var matchs = format.match(/[YHGmndhgijs]/g);
        for (var i=0; i < matchs.length; i++) {
            format = format.replace(matchs[i],
            matchs[i] === "Y" ? this.getFullYear() :
            matchs[i] === "m" ? (this.getMonth() + 1).toLead(2) :
            matchs[i] === "d" ? this.getDate().toLead(2):
            matchs[i] === "n" ? this.getMonth() + 1:
            matchs[i] === "j" ? this.getDate():
            matchs[i] === "h" ? this.getHours().toLead(2):
            matchs[i] === "H" ? this.getHours().toLead(2):
            matchs[i] === "g" ? this.getHours()%12||12:
            matchs[i] === "G" ? this.getHours():
            matchs[i] === "i" ? this.getMinutes().toLead(2) : this.getSeconds().toLead(2));
        }
        return  format;
    }
}
```

## JavaScript关闭浏览器窗口

```js
function CloseWebPage(){
    if (navigator.userAgent.indexOf("MSIE") > 0) {
        if (navigator.userAgent.indexOf("MSIE 6.0") > 0) {
            window.opener = null;
            window.close();
        } else {
            window.open('', '_top');
            window.top.close();
        }
    }else if (navigator.userAgent.indexOf("Firefox") > 0) {
        window.location.href = 'about:blank ';
    } else {
        window.opener = null;
        window.open('', '_self', '');
        window.close();
    }
}
```
