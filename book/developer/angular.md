# Angular

## 模板语法

* 插值 {{ }} 允许你把属性值渲染为文本；而属性绑定语法 `[]` 则允许你在模板表达式中使用属性值。
* `*ngFor` 是一个 "结构型指令"。结构型指令会通过添加、删除和操纵它们的宿主元素等方式塑造或重塑 DOM 的结构。任何带有星号 `*` 的指令都是结构型指令。

示例：

```html
<div *ngFor="let product of products">
  <h3>
    <a [title]="product.name + ' details'">
      {{ product.name }}
    </a>
  </h3>
</div>
```

### 常用特性

* `*ngFor`
* `*ngIf`
* 插值 {{ }}
* 属性绑定 `[]`
* 事件绑定 `()`

## 组件

使用 Angular Generator 生成一个名为 product-alerts 的新组件。

该 generator 为组件的三个部分创建了启动文件：

```
product-alerts.component.ts
product-alerts.component.html
product-alerts.component.css
```

打开 `product-alerts.component.ts`

```js
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-product-alerts',
  templateUrl: './product-alerts.component.html',
  styleUrls: ['./product-alerts.component.css']
})
export class ProductAlertsComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

- `@Component()` 装饰器。这表明它下面的类是一个组件。它提供了有关该组件的元数据，包括它的选择器、模板和样式。

  - 该 selector 用于标识该组件。该选择器是当 Angular 组件在页面中渲染出 HTML 元素时使用的名字。按照惯例，Angular 组件选择器会以前缀 `app-` 开头，后跟组件名称。

  - 模板和样式文件名。它们是对 StackBlitz 生成的 HTML 和 CSS 文件的引用。

- 组件定义中还导出了类 ProductAlertsComponent，用于处理该组件的功能。






## angular 依赖注入

依赖注入是一种编程模式，可以让类从外部源中获得它的依赖，而不必亲自创建它们。依赖注入实现 class 代码可以解耦。

使用依赖注入的好处:

1. 松耦合和可重用性
2. 提高可测试性




## ng 命令


创建一个新组件

```bash
ng generate component product-alerts
```

## angular与react的区别

1、数据绑定

- Angular允许双向数据绑定，而React允许单向数据绑定。
- 双向数据绑定意味着您对模型所做的任何更改都会影响视图，反之亦然。
- 单向数据绑定意味着您对模型所做的任何更改都会影响视图，但不会影响其他方式。这样，数据仅在一个方向上流动。

2、DOM用法

- DOM是Web应用程序的数据对象模型。您可以使用常规DOM或创建虚拟DOM。
- Angular使用浏览器的DOM，而React使用虚拟DOM。
- 虚拟DOM是DOM的简化版本。通过使用虚拟DOM，您可以非常快速地更改任何元素，而无需呈现整个DOM。它将性能从优秀变为优秀。

想象一下，当只更改一个项目时需要渲染所有100个项目的性能差异，然后只渲染单个更改的项目而不渲染其余项目。

使用虚拟DOM现在非常流行，因为速度更快，速度更快！

3、语言

- Angular本质上是一个JS框架，但它是为使用TypeScript而构建的。另一方面，React也是一个JavaScript库，但建议使用JSX。
- TypeScript是JavaScript的超集，而JSX是JavaScript，但是使用扩展的XML语法。

4、应用结构
- Angular是一个功能齐全的MVC框架。React只是MVC中的'V'。
- Angular的MVC结构允许将应用程序分成三个互连的组件，因此一旦学会了它们就更容易操作。
- MVC架构生成结构良好的代码，这对复杂的项目非常有益。
- 但是，React本身并不强制实施应用程序结构，而是依赖于开发人员。对某些人来说，这可能是伟大的，也可能是他们的致命弱点。
