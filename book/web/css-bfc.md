# BFC

## 常见布局模式

CSS中的三种布局模型：
  - 流动模型（Flow）
    - 块状元素都会在所处的包含元素内自上而下按顺序垂直延伸分布，因为在默认状态下，块状元素的宽度都为100%
    - 内联元素都会在所处的包含元素内从左到右水平分布显示
  - 浮动模型（Float）
    - 一个浮动元素会尽量向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止，其周围的元素也会重新排列
  - 层模型（Layer）
    - 绝对定位（position：absolute）
      - 元素从文档流中拖出来，然后使用 left、right、top、bottom 属性相对于其最接近的一个具有定位属性的父包含块进行绝对定位。如果不存在这样的包含块，则相对于body元素，即相对于浏览器窗口
    - 相对定位（position：relative）
      - 通过 left、right、top、bottom 属性确定元素在正常文档流中的偏移位置
    - 固定定位（position：fixed）
    - 与 absolute 定位类型类似，但它的相对移动的坐标是视图（屏幕内的网页窗口）本身

## 什么是BFC

 BFC 即 Block Formatting Contexts (块级格式化上下文)，它属于上述布局模式的流动模型。是W3C CSS2.1规范中的一个概念，决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用。具有BFC特性的元素可以看做是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且BFC具有普通容器所没有的的一些特性。通俗一点来讲，可以把BFC理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

## 形成BFC的条件

只要元素满足下面任一条件即可触发BFC特性：

body 根元素
浮动元素：float 除 none 以外的值
绝对定位元素：position (absolute、fixed)
display 为 inline-block、table-cells、flex
overflow 除了 visible 以外的值 (hidden、auto、scroll)
