# Emmet：HTML/CSS代码快速编写神器
Emmet的前身是大名鼎鼎的Zen coding，它使用仿CSS选择器的语法来生成代码，大大提高了HTML/CSS代码编写的速度。

## 快速编写HTML代码
### 初始化
HTML文档需要包含一些固定的标签，比如、、等，现在你只需要1秒钟就可以输入这些标签。比如输入“!”或“html:5”，然后按Tab键(或ctrl+e)：

```
html:5
```
```
html:5 或!：用于HTML5文档类型
html:xt：用于XHTML过渡文档类型
html:4s：用于HTML4严格文档类型
```

### 轻松添加类、id、文本和属性
连续输入元素名称和ID，Emmet会自动为你补全，比如输入p#foo：

```
div#foot.footer[title=titleText]{footerText}
```
- \# id
- . class
- [name=text] 属性
- {$} 内容

### 嵌套和分组

```
div>p+(ul>li{$}*2)
```

- \> 子元素符号，表示嵌套的元素
- \+ 同级标签符号
- \^ 可以使该符号前的标签提升一行
- \*n 可以指定标签的数量，例：ul>li*3
- () 指定一个分组
- $ 增量占位符

### Lorem 生成无意义文本
lorem为我们提供了强大的测试数据，提高了编写HTML代码的速度，让我们专心编写代码。
```
ul>li*3>lorem5
```

### Enter Wrap Abbreviation（输入扩展缩写）
把文本转换成HTML标签，当客户给我们提供了一个文本文档，把标题复制过来，比如：

- 首页
- 公司简介
- 公司动态
- 关于我们
- 联系我们

转换成导航，选择文本，再按shift+ctrl+g，弹出：Enter Wrap Abbreviation（输入扩展缩写），在其中输入：
```
ul>li[title=$#]*>{$#}+img[alt=$#]
```

## 快速编写CSS代码
### 值
比如要定义元素的宽度，只需输入w100，即可生成

```css
/*
w100
 */
width: 100px;
```

除了px，也可以生成其他单位，比如输入h10p+m5e，结果如下：

```css
/*
h10p+m5e
 */
height: 10%;
margin: 5em;
```

### 单位别名列表：

- p 表示 %
- e 表示 em
- x 表示 ex

### 附加属性

输入@f+，将生成：

```css
/*
@f+
 */
@font-face {
    font-family: 'FontName';
    src: url('FileName.eot');
    src: url('FileName.eot?#iefix') format('embedded-opentype'),
         url('FileName.woff') format('woff'),
         url('FileName.ttf') format('truetype'),
         url('FileName.svg#FontName') format('svg');
    font-style: normal;
    font-weight: normal;
}
```

### 模糊匹配
如果有些缩写你拿不准，Emmet会根据你的输入内容匹配最接近的语法，比如输入ov:h、ov-h、ovh和oh，生成的代码是相同的：

```css
/*
o:h
 */
overflow: hidden;
```

### 供应商前缀
如果输入非W3C标准的CSS属性，Emmet会自动加上供应商前缀，比如输入-ts:，则会生成：
```css
/*
-ts:
 */
-webkit-text-shadow: hoff voff blur #000;
-moz-text-shadow: hoff voff blur #000;
-ms-text-shadow: hoff voff blur #000;
-o-text-shadow: hoff voff blur #000;
text-shadow: hoff voff blur #000;
```
如果你不希望加上所有前缀，可以使用缩写的指定，比如 -w-ts 表示只加上 -webkit 前缀

```css
/*
-w-ts:
 */
-webkit-text-shadow: hoff voff blur #000;
text-shadow: hoff voff blur #000;
```

- -w 表示 -webkit
- -m 表示 -moz
- -s 表示 -ms
- -o 表示 -o

以上几个前缀可以任意组合，比如 -wms-ts 表示加上 -webkit、-moz 和 -ms 前缀

### 渐变
输入 lg(left,red 50%,green)，会生成如下代码

```css
/*
lg(left,red 50%,green)
 */
background-image: -webkit-linear-gradient(left, red 50%, green);
background-image: -o-linear-gradient(left, red 50%, green);
background-image: linear-gradient(to right, red 50%, green);
```
