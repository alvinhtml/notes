# Web Components

Web Components 是一套不同的技术，允许您创建可重用的定制元素（它们的功能封装在您的代码之外）并且在您的web应用中使用它们。

## 概念和使用

作为开发者，我们都知道尽可能多的重用代码是一个好主意。这对于自定义标记结构来说通常不是那么容易 — 想想复杂的HTML（以及相关的样式和脚本），有时您不得不写代码来呈现自定义UI控件，并且如果您不小心的话，多次使用它们会使您的页面变得一团糟。

Web Components旨在解决这些问题 — 它由三项主要技术组成，它们可以一起使用来创建封装功能的定制元素，可以在你喜欢的任何地方重用，不必担心代码冲突。

**Custom elements（自定义元素）：** 一组JavaScript API，允许您定义custom elements及其行为，然后可以在您的用户界面中按照需要使用它们。
**Shadow DOM（影子DOM）：** 一组JavaScript API，用于将封装的“影子”DOM树附加到元素（与主文档DOM分开呈现）并控制其关联的功能。通过这种方式，您可以保持元素的功能私有，这样它们就可以被脚本化和样式化，而不用担心与文档的其他部分发生冲突。
**HTML templates（HTML模板）：** `<template>` 和 `<slot>` 元素使您可以编写不在呈现页面中显示的标记模板。然后它们可以作为自定义元素结构的基础被多次重用。

## 自定义元素

在 HTML 中可以使用自定义标签：

```html
<user-card></user-card>
```

这种自定义的 HTML 标签，称为自定义元素（custom element）。根据规范，自定义元素的名称必须包含连词线，用与区别原生的 HTML 元素。所以，`<user-card>`不能写成`<usercard>`

## customElements.define()

自定义元素需要有一个对应的 JavaScript 类，所有 `<user-card>` 都是这个类的实例。

```js
class UserCard extends HTMLElement {
  constructor() {
    super();
  }
}
```

上面代码中，UserCard就是自定义元素的类。注意，这个类的父类是HTMLElement，因此继承了 HTML 元素的特性。

接着，使用浏览器原生的customElements.define()方法，告诉浏览器<user-card>元素与这个类关联。

```js
window.customElements.define('user-card', UserCard);
```

## 自定义元素的内容

现在，<user-card> 还是空的，我们可以类的构造函数中为它添加内容：

```js
class UserCard extends HTMLElement {
  constructor() {
    super();

    const image = document.createElement('img');
    image.src = 'https://avatars0.githubusercontent.com/u/8908111?s=460&v=4'
    image.classList.add('image');

    const photo = document.createElement('div');
    photo.classList.add('photo');

    const container = document.createElement('div');
    container.classList.add('container');

    const name = document.createElement('p');
    name.classList.add('name');
    name.innerText = 'User Name';

    const email = document.createElement('p');
    email.classList.add('email');
    email.innerText = 'yourmail@some-email.com';

    const button = document.createElement('button');
    button.classList.add('button');
    button.innerText = 'Follow';

    photo.append(image);
    container.append(name, email, button);
    this.append(photo, container);
  }
}

window.customElements.define('user-card', UserCard);
```

在 constructor 构造函数里，`this` 代表自定义元素实例。

## HTML内容模板

HTML内容模板（`<template>`）元素是一种用于保存客户端内容机制，该内容在加载页面时不会呈现，但随后可以(原文为 may be)在运行时使用JavaScript实例化。

将模板视为一个可存储在文档中以便后续使用的内容片段。虽然解析器在加载页面时确实会处理`<template>`元素的内容，但这样做只是为了确保这些内容有效；但元素内容不会被渲染。

此外，可以将组件的样式应该与代码封装在一起，实其只对自定义元素生效，不影响外部的全局样式。

```html
<template id="userCardTemplate">
  <style>
    /* <template> 样式里面的:host伪类，指代自定义元素本身 */
    :host, .card {
      background-color: #eee;
      display: flex;
      border-radius: 4px;
      padding: 20px;
      max-width: 500px;
    }

    .photo img {
      width: 200px;
    }

    .container {
      margin: 30px;
      text-align: center;
    }
  </style>
  <div class="photo"><img src="https://avatars0.githubusercontent.com/u/8908111?s=460&amp;v=4" class="image"></div>
  <div class="container">
    <p class="name">User Name</p>
    <p class="email">yourmail@some-email.com</p><button class="button">Follow</button>
  </div>
</template>
```

`<template> `元素仅包含全局属性。但 HTMLTemplateElement 有个属性 `content` ， 这个属性是只读的，可以用来克隆 DOM。

```js
class UserCard extends HTMLElement {
  constructor() {
    super();

    const userCardTemplate = document.getElementById('userCardTemplate');

    // clone userCardTemplate
    const component = userCardTemplate.content.cloneNode(true);

    component.querySelector('img').setAttribute('src', this.getAttribute('image'));
    component.querySelector('.container>.name').innerText = this.getAttribute('name');
    component.querySelector('.container>.email').innerText = this.getAttribute('email');

    this.append(component);

  }
}

window.customElements.define('user-card', UserCard);
```
现在，可以通过指定属性来为 `<user-card>` 填充内容：

```html
<user-card
  class="card"
  image="https://avatars0.githubusercontent.com/u/8908111?s=460&amp;v=4"
  name="Yang Gang"
  email="yanggang@gmail.com"
></user-card>
```

## Shadow DOM

我们不希望用户能够看到`<user-card>`的内部代码，Web Component 允许内部代码隐藏起来，这叫做 Shadow DOM，即这部分 DOM 默认与外部 DOM 隔离，内部任何代码都无法影响外部。

```js
const shadow =  this.attachShadow( { mode: 'closed' } );
// ...
shadow.append(component);
```

## 生命周期回调

定义在自定义元素的类定义中的特殊回调函数，影响其行为：

- connectedCallback: 当自定义元素第一次被连接到文档DOM时被调用。
- disconnectedCallback: 当自定义元素与文档DOM断开连接时被调用。
- adoptedCallback: 当自定义元素被移动到新文档时被调用。
- attributeChangedCallback: 当自定义元素的一个属性被增加、移除或更改时被调用。

```js
connectedCallback() {
  console.log("组件加载成功！");
}
```

## 用户事件

如果需要与 `<user-card>` 互动，可在 `UserCard` 类中添加各种事件监听。

完整代码如下：

```js
class UserCard extends HTMLElement {
  constructor() {
    super();

    const shadow =  this.attachShadow( { mode: 'closed' } );

    const userCardTemplate = document.getElementById('userCardTemplate');

    // clone userCardTemplate
    const component = userCardTemplate.content.cloneNode(true);

    component.querySelector('img').setAttribute('src', this.getAttribute('image'));
    component.querySelector('.container>.name').innerText = this.getAttribute('name');
    component.querySelector('.container>.email').innerText = this.getAttribute('email');

    component.querySelector('.container>.button').addEventListener('click', this.handleClick.bind(this));

    shadow.append(component);
  }

  handleClick(e) {
    console.log(`Follow ${this.getAttribute('name')}`);
  }

  connectedCallback() {
    console.log("组件加载成功！");
  }
}
```

## 参考

- [Web Components 入门实例教程](http://www.ruanyifeng.com/blog/2019/08/web_components.html)
- [MDN Web Docs - Web Components](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)
