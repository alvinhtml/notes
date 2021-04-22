# CSS
CSS 用于控制网页的样式和布局。CSS3 是最新的 CSS 标准。

## CSS 权重

权值等级划分， 一般来说是划分4个等级：

```
第一等级：代表 内联样式，如 style=""，权值为 1,0,0,0；
第二等级：代表 ID选择器，如 #id="", 权值为 0,1,0,0；
第三等级：代表 calss | 伪类 | 属性 选择器，如 .class | :hover,:link,:target | [type], 权值 0,0,1,0；
第四等级：代表 标签 | 伪元素 选择器，如 p | ::after, ::before, ::fist-inline, ::selection, 权值 0,0,0,1；
此外，通用选择器（\*），子选择器（\>）， 相邻同胞选择器（+）等选择器不在4等级之内，所以它们的权值都为 0,0,0,0；
```

单个选择器的优先级比较：

```
css属性!important
> 内联样式
> ID选择器(#id)
> 类选择器(.class) = 伪类选择器(:hover等) = 属性选择器[type等]
> 元素选择器(p等) = 伪元素选择器(:after/:before/::selection等)
> 通用选择器(\*)
> 继承的样式
```



## CSS3 模块
CSS3 被划分为模块。其中最重要的 CSS3 模块包括：


- 选择器
- 框模型
- 背景和边框
- 文本效果
- 2D/3D 转换
- 动画
- 多列布局
- 用户界面
- CSS3 标准
- W3C 仍然在对 CSS3 规范进行开发。不过，现代浏览器已经实现了相当多的 CSS3 属性。


## 框模型

盒模型分为：

  - 标准盒模 - padding 和 border 不计算在 box 宽高内；
  - 怪异盒模型 - padding 和 border 计算在 box 宽高内；

### box-sizing

**content-box**: 这是由 CSS2.1 规定的宽度高度行为。宽度和高度分别应用到元素的内容框。在宽度和高度之外绘制元素的内边距和边框。
**border-box**: 为元素设定的宽度和高度决定了元素的边框盒。就是说，为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。通过从已设定的宽度和高度分别减去边框和内边距才能得到内容的宽度和高度。


## 多列布局 Grid 与 Flex

Grid 布局与 Flex 布局有一定的相似性，都可以指定容器内部多个项目的位置。但是，它们也存在重大区别。

Flex 布局是轴（zhóu）线布局，只能指定"项目"针对轴线的位置，可以看作是 **一维布局**。Grid 布局则是将容器划分成"行"和"列"，产生单元格，然后指定"项目所在"的单元格，可以看作是 **二维布局**。Grid 布局远比 Flex 布局强大。

### 弹性布局 display: flex

任何一个容器都可以指定为 Flex 布局。

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做 `main start`，结束位置叫做 `main end`；交叉轴的开始位置叫做 `cross start`，结束位置叫做 `cross end`。

项目默认沿主轴排列。单个项目占据的主轴空间叫做 `main size`，占据的交叉轴空间叫做 `cross size`。

![css3-flex](../assets/css3-flex.png)


以下6个属性设置在容器上

- flex-direction: row | row-reverse | column | column-reverse
  - 属性决定主轴的方向（即项目的排列方向)
- flex-wrap: nowrap | wrap | wrap-reverse
  - 指定 flex 元素单行显示还是多行显示 。如果允许换行，这个属性允许你控制行的堆叠方向。
- flex-flow: flex-direction | flex-wrap
  - 属性是 `flex-direction` 和 `flex-wrap` 的简写
- justify-content:
  - `start` 从行首开始排列。每行第一个元素与行首对齐，同时所有后续的元素与前一个对齐。
  - `flex-start` 从行首开始排列。每行第一个弹性元素与行首对齐，同时所有后续的弹性元素与前一个对齐。
  - `flex-end` 从行尾开始排列。每行最后一个弹性元素与行尾对齐，其他元素将与后一个对齐。
  - `center` 伸缩元素向每行中点排列。每行第一个元素到行首的距离将与每行最后一个元素到行尾的距离相同。
  - `left` 伸缩元素一个挨一个在对齐容器得左边缘，如果属性的轴与内联轴不平行，则`left`的行为类似于`start`。
  - `right` 元素以容器右边缘为基准, 一个挨着一个对齐,如果属性轴与内联轴不平行,则`right`的行为类似于`start`。
  - `baseline` `first baseline` `last baseline`
    - 指定参与第一个或最后一个基线对齐方式：将框的第一个或最后一个基线集的对齐基线与其基线共享组中所有框的共享的第一个或最后一个基线集中的对应基线对齐。
    - 第一个基准的后备对齐方式是开始，最后一个基准的后备对齐方式是结束。
  - `space-between` 在每行上均匀分配弹性元素。相邻元素间距离相同。每行第一个元素与行首对齐，每行最后一个元素与行尾对齐。
  - `space-around` 在每行上均匀分配弹性元素。相邻元素间距离相同。每行第一个元素到行首的距离和每行最后一个元素到行尾的距离将会是相邻元素之间距离的一半。
  - `space-evenly` flex 项都沿着主轴均匀分布在指定的对齐容器中。相邻 flex 项之间的间距，主轴起始位置到第一个 flex 项的间距,，主轴结束位置到最后一个 flex 项的间距，都完全一样。
  - `stretch` `safe` `unsafe`
- align-items
  - 属性定义项目在交叉轴上如何对齐。
  - `align-items`和`align-content`有相同的功能，不过不同点是它是用来让每一个单行的容器居中而不是让整个容器居中。
- align-content
  - 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
  - `align-content` 是针对 flex 容器里面多轴(多行)的情况, `align-items` 是针对一行的情况进行排列。

#### justify-content

```css
/* Positional alignment */
justify-content: center;     /* 居中排列 */
justify-content: start;      /* Pack items from the start */
justify-content: end;        /* Pack items from the end */
justify-content: flex-start; /* 从行首起始位置开始排列 */
justify-content: flex-end;   /* 从行尾位置开始排列 */
justify-content: left;       /* Pack items from the left */
justify-content: right;      /* Pack items from the right */

/* Baseline alignment */
justify-content: baseline;
justify-content: first baseline;
justify-content: last baseline;

/* Distributed alignment */
justify-content: space-between;  /* 均匀排列每个元素
                                   首个元素放置于起点，末尾元素放置于终点 */
justify-content: space-around;  /* 均匀排列每个元素
                                   每个元素周围分配相同的空间 */
justify-content: space-evenly;  /* 均匀排列每个元素
                                   每个元素之间的间隔相等 */
justify-content: stretch;       /* Distribute items evenly
                                   Stretch 'auto'-sized items to fit
                                   the container */

/* Overflow alignment */
justify-content: safe center;
justify-content: unsafe center;

/* Global values */
justify-content: inherit;
justify-content: initial;
justify-content: unset;
```

#### align-content

```css
/* 基本位置对齐 */
/*align-content不采用左右值 */
align-content: center;     /* 将项目放置在中点 */
align-content: start;      /* 最先放置项目 */
align-content: end;        /* 最后放置项目 */
align-content: flex-start; /* 从起始点开始放置flex元素 */
align-content: flex-end;   /* 从终止点开始放置flex元素 */

/* 默认对齐 */
align-content: normal;

/*基线对齐*/
align-content: baseline;
align-content: first baseline;
align-content: last baseline;

/* 分布式对齐 */
align-content: space-between; /* 均匀分布项目
                                 第一项与起始点齐平，
                                 最后一项与终止点齐平 */
align-content: space-around;  /* 均匀分布项目
                                 项目在两端有一半大小的空间*/
align-content: space-evenly;  /* 均匀分布项目
                                 项目周围有相等的空间 */
align-content: stretch;       /* 均匀分布项目
                                 拉伸‘自动’-大小的项目以充满容器 */

/* 溢出对齐 */
align-content: safe center;
align-content: unsafe center;

 /* 全局属性 */
align-content: inherit; /* 继承 */
align-content: initial;  /* 初始值 */
align-content: unset; /* 未设置 */
```

以下6个属性设置在项目上。

- order
  - 属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
- flex-grow
  - 属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
- flex-shrink
  - 属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小
- flex-basis
  - 属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
- flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
  - 属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为0 1 auto。后两个属性可选。
- align-self
  - `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。


### 网格布局 display: grid

#### 容器和项目

采用网格布局的区域，称为"容器"（container）。容器内部采用网格定位的子元素，称为"项目"（item）。

#### 行和列

容器里面的水平区域称为"行"（row），垂直区域称为"列"（column）。

#### 单元格

行和列的交叉区域，称为"单元格"（cell）。

正常情况下，n行和m列会产生n x m个单元格。比如，3行3列会产生9个单元格。

#### 网格线

划分网格的线，称为"网格线"（grid line）。水平网格线划分出行，垂直网格线划分出列。

正常情况下，n行有n + 1根水平网格线，m列有m + 1根垂直网格线，比如三行就有四根水平网格线。

#### 属性

Grid 布局的属性分成两类。一类定义在容器上面，称为容器属性；另一类定义在项目上面，称为项目属性。这部分先介绍容器属性。


- display: grid | inline-grid;
- grid-template-columns grid-template-rows
  - 容器指定了网格布局以后，接着就要划分行和列。`grid-template-columns`属性定义每一列的列宽，`grid-template-rows`属性定义每一行的行高。
  - 除了使用绝对单位，也可以使用百分比。
  - `repeat()` 可以使用repeat()函数，简化重复的值。
    - 接受两个参数，第一个参数是重复的次数（上例是3），第二个参数是所要重复的值。
    - 重复某种模式也是可以的。如：`repeat(2, 100px 20px 80px)`
  - `auto-fill` 有时，单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用`auto-fill`关键字表示自动填充。`repeat(auto-fill, 100px)`
  - `fr` 关键字 网格布局提供了`fr`关键字（fraction 的缩写，意为"片段"）。如果两列的宽度分别为1fr和2fr，就表示后者是前者的两倍。如：`grid-template-columns: 1fr 1fr;`
  - `minmax()` 函数产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。
  - `auto` 关键字表示由浏览器自己决定长度。
  - 网格线的名称 `grid-template-columns`属性和`grid-template-rows`属性里面，还可以使用方括号，指定每一根网格线的名字，方便以后的引用。
- grid-row-gap 属性设置行与行的间隔（行间距），
- grid-column-gap 属性设置列与列的间隔（列间距）。
- grid-gap 属性是`grid-column-gap`和`grid-row-gap`的合并简写形式
  - 如果grid-gap省略了第二个值，浏览器认为第二个值等于第一个值。
- grid-auto-flow 属性决定，默认值是row，即"先行后列"。也可以将它设成column，变成"先列后行"。
  - grid-auto-flow 属性除了设置成`row`和`column`，还可以设成`row dense`和`column dense`。这两个值主要用于，某些项目指定位置以后，剩下的项目怎么自动放置。


## CSS3 边框
通过 CSS3，您能够创建圆角边框，向矩形添加阴影，使用图片来绘制边框 - 并且不需使用设计软件，比如 PhotoShop。

### CSS3 圆角边框
在 CSS3 中，border-radius 属性用于创建圆角：

```css
div {
  border-radius:25px;
}
```

### CSS3 边框阴影
在 CSS3 中，box-shadow 用于向方框添加阴影：
```css
div {
  box-shadow: 10px 10px 5px #888888;
}
```


### CSS3 边框图片
通过 CSS3 的 border-image 属性，您可以使用图片来创建边框：

```css
div {
    border-image:url(border.png) 30 30 round;
    -moz-border-image:url(border.png) 30 30 round; /* 老的 Firefox */
    -webkit-border-image:url(border.png) 30 30 round; /* Safari 和 Chrome */
    -o-border-image:url(border.png) 30 30 round; /* Opera */
}
```

## CSS3 2D 转换

通过 CSS3 转换，我们能够对元素进行移动、缩放、转动、拉长或拉伸。在本章中，您将学到如下 2D 转换方法：

- translate()
- rotate()
- scale()
- skew()
- matrix()

### 新的转换属性

属性|描述
-|-
transform|向元素应用 2D 或 3D 转换。
transform-origin|允许你改变被转换元素的位置。

### 2D Transform 方法

函数|描述
-|-
matrix(n,n,n,n,n,n)|定义 2D 转换，使用六个值的矩阵。
translate(x,y)|定义 2D 转换，沿着 X 和 Y 轴移动元素。
translateX(n)|定义 2D 转换，沿着 X 轴移动元素。
translateY(n)|定义 2D 转换，沿着 Y 轴移动元素。
scale(x,y)|定义 2D 缩放转换，改变元素的宽度和高度。
scaleX(n)|定义 2D 缩放转换，改变元素的宽度。
scaleY(n)|定义 2D 缩放转换，改变元素的高度。
rotate(angle)|定义 2D 旋转，在参数中规定角度。
skew(x-angle,y-angle)|定义 2D 倾斜转换，沿着 X 和 Y 轴。
skewX(angle)|定义 2D 倾斜转换，沿着 X 轴。
skewY(angle)|定义 2D 倾斜转换，沿着 Y 轴。

### CSS3 transform-origin 属性
设置旋转元素的基点位置：

```css
div {
  transform-origin:20% 40%;
}
```
### translate() 方法
通过 translate() 方法，元素从其当前位置移动，根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数：

```css
div {
    transform: translate(50px,100px);
    -ms-transform: translate(50px,100px);       /* IE 9 */
    -webkit-transform: translate(50px,100px);   /* Safari and Chrome */
    -o-transform: translate(50px,100px);        /* Opera */
    -moz-transform: translate(50px,100px);      /* Firefox */
}
```

### rotate() 方法
通过 rotate() 方法，元素顺时针旋转给定的角度。允许负值，元素将逆时针旋转。

```css
div {
    transform: rotate(30deg);
    -ms-transform: rotate(30deg);       /* IE 9 */
    -webkit-transform: rotate(30deg);   /* Safari and Chrome */
    -o-transform: rotate(30deg);        /* Opera */
    -moz-transform: rotate(30deg);      /* Firefox */
}
```

### scale() 方法
通过 scale() 方法，元素的尺寸会增加或减少，根据给定的宽度（X 轴）和高度（Y 轴）参数：

```css
div {
    transform: scale(2,4);
    -ms-transform: scale(2,4);  /* IE 9 */
    -webkit-transform: scale(2,4);  /* Safari 和 Chrome */
    -o-transform: scale(2,4);   /* Opera */
    -moz-transform: scale(2,4); /* Firefox */
}
```

### skew() 方法
通过 skew() 方法，元素翻转给定的角度，根据给定的水平线（X 轴）和垂直线（Y 轴）参数：

```css
div {
    transform: skew(30deg,20deg);
    -ms-transform: skew(30deg,20deg);   /* IE 9 */
    -webkit-transform: skew(30deg,20deg);   /* Safari and Chrome */
    -o-transform: skew(30deg,20deg);    /* Opera */
    -moz-transform: skew(30deg,20deg);  /* Firefox */
}
```

### matrix() 方法
matrix() 方法把所有 2D 转换方法组合在一起。

## CSS3 过渡
通过 CSS3，我们可以在不使用 Flash 动画或 JavaScript 的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

### 过渡属性

属性|描述
-|-
transition|简写属性，用于在一个属性中设置四个过渡属性。
transition-property|规定应用过渡的 CSS 属性的名称。
transition-duration|定义过渡效果花费的时间。默认是 0。
transition-timing-function|规定过渡效果的时间曲线。默认是 "ease"。
transition-delay|规定在过渡效果开始之前需要等待的时间。默认是 0。

### CSS3 transition 属性
transition属性是一个简写属性，用于设置四个过渡属性：
```
transition: property duration timing-function delay;
```

### CSS3 transition-property 属性
transition-property 属性规定应用过渡效果的 CSS 属性的名称（多个名称以逗号分隔）。
```
transition-property: none|all|property;
```

### CSS3 transition-timing-function 属性
ransition-timing-function 属性规定过渡效果的速度曲线。
```
transition-timing-function: linear|ease|ease-in|ease-out|ease-in-out|cubic-bezier(n,n,n,n);
```

## CSS3 动画
### Keyframes-关键帧
Keyframes具有其自己的语法规则，他的命名是由 `@keyframes` 开头，后面紧接着是这个“动画的名称”加上一对花括号“{}”，括号中就是一些不同时间段样式规则，有点像我们css的样式写法一样。对于一个 `@keyframes` 中的样式规则是由多个百分比构成的，如“0%”到"100%"之间，我们可以在这个规则中创建多个百分比，我们分别给每一个百分比中给需要有动画效果的元素加上不同的属性，从而让元素达到一种在不断变化的效果，比如说移动，改变元素颜色，位置，大小，形状等，不过有一点需要注意的是，我们可以使用“fromt”“to”来代表一个动画是从哪开始，到哪结束，也就是说这个 "from"就相当于"0%"而"to"相当于"100%",值得一说的是，其中"0%"不能像别的属性取值一样把百分比符号省略，我们在这里必须加上百分符号（“%”）如果没有加上的话，我们这个keyframes是无效的，不起任何作用。因为keyframes的单位只接受百分比值。

Keyframes具体语法规则如下：
```
keyframes-rule: '@keyframes' IDENT '{' keyframes-blocks '}';
keyframes-blocks: [ keyframe-selectors block ]* ;
keyframe-selectors: [ 'from' | 'to' | PERCENTAGE ] [ ',' [ 'from' | 'to' | PERCENTAGE ] ]*;
```
我把上面的语法综合起来

```css
@keyframes animote {
     from {
         left: 0px;
     }
     Percentage {
         left: 30px;
     }
     to {
         left: 0px;
     }
 }
 @keyframes animote {
     0% {
         left: 0px;
     }
     50% {
         left: 30px;
     }
     100% {
         left: 0px;
     }
 }
```

## will-change

CSS 属性 [will-change](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change) 为web开发者提供了一种告知浏览器该元素会有哪些变化的方法，这样浏览器可以在元素属性真正发生变化之前提前做好对应的优化准备工作。 这种优化可以将一部分复杂的计算工作提前准备好，使页面的反应更为快速灵敏。

```
will-change: auto
will-change: scroll-position
will-change: contents
will-change: transform        // Example of <custom-ident>
will-change: opacity          // Example of <custom-ident>
will-change: left, top        // Example of two <animateable-feature>

will-change: unset
will-change: initial
will-change: inherit
```
### 取值

- auto
  - 表示没有特别指定哪些属性会变化，浏览器需要自己去猜，然后使用浏览器经常使用的一些常规方法优化。
- <animateable-feature> 可以是以下值：
  - scroll-position 表示开发者希望在不久后改变滚动条的位置或者使之产生动画。
  - contents 表示开发者希望在不久后改变元素内容中的某些东西，或者使它们产生动画。
  - <custom-ident> 表示开发者希望在不久后改变指定的属性名或者使之产生动画。如果属性名是简写，则代表所有与之对应的简写或者全写的属性。

### 示例

```css
.sidebar {
  will-change: transform;
}
```

以上示例在样式表中直接添加了 will-change 属性，会导致浏览器将对应的优化工作一直保存在内存中，这其实是不必要的，前面我们已经看过为什么应该避免这样的做法。下面是另一个展示如何使用脚本正确地应用 will-change 属性的示例，在大部分的场景中，你都应该这样做。

```js
var el = document.getElementById('element');

// 当鼠标移动到该元素上时给该元素设置 will-change 属性
el.addEventListener('mouseenter', hintBrowser);
// 当 CSS 动画结束后清除 will-change 属性
el.addEventListener('animationEnd', removeHint);

function hintBrowser() {
  // 填写上那些你知道的，会在 CSS 动画中发生改变的 CSS 属性名们
  this.style.willChange = 'transform, opacity';
}

function removeHint() {
  this.style.willChange = 'auto';
}
```

但是，如果某个应用在按下键盘的时候会翻页，比如相册或者幻灯片一类的，它的页面很大很复杂，此时在样式表中写上 will-change 是合适的。这会使浏览器提前准备好过渡动画，当键盘按下的时候就能立即看到灵活轻快的动画。

```css
.slide {
  will-change: transform;
}
```
