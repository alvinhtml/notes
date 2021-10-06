# MVC、 MVP、 MVVM之间的区别

复杂的软件必须有清晰合理的架构，否则无法开发和维护。

MVC（Model-View-Controller）是最常见的软件架构之一，业界有着广泛应用。

它本身很容易理解，但是要讲清楚，它与衍生的 MVP 和 MVVM 架构的区别就不容易了。


## MVC

MVC模式的意思是，软件可以分成三个部分。

![MVC](../assets/mvc.png)

```
视图（View）：用户界面。
控制器（Controller）：业务逻辑
模型（Model）：数据保存
```
各部分之间的通信方式如下。

![MVC](../assets/mvc2.png)

1. View 传送指令到 Controller
1. Controller 完成业务逻辑后，要求 Model 改变状态
1. Model 将新的数据发送到 View，用户得到反馈

所有通信都是单向的。

## 互动模式

接受用户指令时，MVC 可以分成两种方式。一种是通过 View 接受指令，传递给 Controller。

![MVC](../assets/mvc3.png)

另一种是直接通过controller接受指令。

![MVC](../assets/mvc4.png)

##实例：Backbone

实际项目往往采用更灵活的方式，以 Backbone.js 为例。

![MVC](../assets/mvc-backbone.png)

1. 用户可以向 View 发送指令（DOM 事件），再由 View 直接要求 Model 改变状态。

2. 用户也可以直接向 Controller 发送指令（改变 URL 触发 hashChange 事件），再由 Controller 发送给 View。

3. Controller 非常薄，只起到路由的作用，而 View 非常厚，业务逻辑都部署在 View。所以，Backbone 索性取消了 Controller，只保留一个 Router（路由器） 。

## MVP

MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。

![MVP](../assets/mvp.png)

1. 各部分之间的通信，都是双向的。

2. View 与 Model 不发生联系，都通过 Presenter 传递。

3. View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter 非常厚，所有逻辑都部署在那里。

## MVVM

MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。

![MVVM](../assets/mvvm.png)

唯一的区别是，它采用双向绑定（data-binding）：View 的变动，自动反映在 ViewModel，反之亦然。Angular 和 Ember 都采用这种模式。


## MVC到MVP再到MVVM的发展

MVC被认为是解决用户控制大型和复杂数据集问题的通用解决方案。从经典MVC到MVVM，UI架构经过数次重大变迁，一些概念也在不断变化，架构和底层环境互相影响、适配，我认为时至今日，经典MVC已经不再是UI架构的正常选项。

最初是Thing、Model、View、Editor。后来经过讨论定为Model、View和Controller。

在70年代末，80年代初，我们并没有操作系统和消息循环，甚至鼠标的光标都需要我们的UI系统来自行绘制。假如我们面对的是鼠标、键盘驱动这样的底层环境，我们就需要一定的机制和系统来统一处理用户输入，实现指针系统，文本系统，焦点系统，并且分配给正确的view或者model来处理。这样也就不难理解为什么经典MVC中称controller是用户和系统之间的链接。

Model-View-Presenter是在MVC的基础上，进一步规定了Controller中的一些概念而成。

MVVM认为view应该是事件驱动,模型变化只是一种事件。

如何呈现是view的事，调用方只用给view一个data，view就只负责这个data的显示，调用者不用管view如何显示，只负责将data传递，view也不用管数据从何而来，只要将data以自己的方式呈现，并且时刻关注data的变化如何反映到view上来就好。

而双向绑定，则是一个附加产品，毕竟只有像textbox，form等会改变数据的UI不多，大部分UI都只是单向的呈现数据而已。不过有双向绑定在，则可以将view和调用方分离得更彻底，即获取数据不必在意特别的view，如一个日期是用户在textbox中输入，还是用calendar提取的无关紧要，因为那是view设计和安排的事。

Model 无法将数据“发送”给 View，因为它根本不知道 View 的存在，数据应该是由 Controller 持有，并显示出 View。因此，用户也不是直接操作 Controller，即使是输入 URL，也可以认为那是由 View 触发的（就像在 View 上点击了一个链接）。

因此，MVC 的处理流程是 V -> C -> M -> C -> V。

MVP 模式实际上就是 MVC，只不过这里面的 C 主要负责的不再是业务逻辑，而是界面逻辑了，比如何时显示/隐藏某个选项卡，绑定 View 事件等。


## 总结

在前后端没有分离的时代，常见的MVC架构中，Model 负责数据的存储，Controller 负责业务逻辑和视图逻辑（包括路由），View 负责视图渲染。后来前后端分离后，业务逻辑下沉到 Model 中，也就是说 Model 应该天然的带有业务逻辑的处理，


## 参考

[谈谈UI架构设计的演化](https://www.cnblogs.com/winter-cn/p/4285171.html)
