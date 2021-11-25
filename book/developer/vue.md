# Vue

## 对比其他框架

### React

React 和 Vue 相似之处：

- 使用 Virtual DOM
- 提供了响应式 (Reactive) 和组件化 (Composable) 的视图组件。
- 将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库。

优化：

- 在 React 中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树
- Vue 组件的依赖是在渲染过程中自动追踪的，系统能精确知晓哪个组件确实需要被重渲染

JSX vs Templates：

- 在 React 中，所有的组件的渲染功能都依靠 JSX
- Vue 使用模板语法和各种修饰符，提供了渲染函数，支持 JSX

组件作用域内的 CSS：

这里 React 和 Vue 主要的区别是，Vue 设置样式的默认方法是单文件组件里类似 style 的标签。

向上扩展：

- React 使用 Flux、Redux。
- Vue 使用 Vuex。

CLI 脚手架：

- create-react-app 功能简单
- vue-cli 功能强大，由官方维护

### MobX 和 Redux 的比较

先要明白 mobx 和 redux 的定位是不同的。redux 管理的是 (STORE -> VIEW -> ACTION) 的整个闭环，而 mobx 只关心 STORE -> VIEW 的部分.

1. 非实时计算

```js
view() {
  if (count === 0) {
    return a;
  } else {
    return b;
  }
}
```

基于 redux 的方案，我们必须同时监听 count, a 和 b 。在 counte === 0 的时候，b 如果修改了，也会触发 view 。而这个时候的 b 其实是无意义的。

2. 粗粒度 subscription

```js
view() {
  todos[0].title
}
```

基于 redux，我们通常会订阅 todos，这样 todos 的新增、删除都会触发 view 。其实这里真正需要监听的是 todos 第一个元素的 title 属性是否有修改。

## Vue CLI

### 创建新项目

```bash
vue create my-app

# 从 GitHub repo 使用 preset
vue create --preset username/repo my-project

# ./my-preset 应当是一个包含 preset.json 的文件夹
vue create --preset ./my-preset my-project

# 或者，直接使用当前工作目录下的 json 文件：
vue create --preset my-preset.json my-project
```

### 使用图形界面

```bash
vue ui
```

## Preset

一个 Vue CLI preset 是一个包含创建新项目所需预定义选项和插件的 JSON 对象，让用户无需在命令提示中选择它们。

在 vue create 过程中保存的 preset 会被放在你的 home 目录下的一个配置文件中 (~/.vuerc)。你可以通过直接编辑这个文件来调整、添加、删除保存好的 preset。

## 插件

如果你想在一个已经被创建好的项目中安装一个插件，可以使用 vue add 命令：

```
vue add eslint
```



## CLI 服务

```json
{
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build"
  }
}
```

```bash
# 启动 serve
npm run serve

# 打包
npm run build

# 审查一个 Vue CLI 项目的 webpack config
vue-cli-service inspect

# 查看所有注入的命令
npx vue-cli-service help
```

## Vue 实例

一个 Vue 应用由一个通过 new Vue 创建的根 Vue 实例，以及可选的嵌套的、可复用的组件树组成。所有的 Vue 组件都是 Vue 实例。

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})
```

## 实例生命周期钩子

所有的生命周期钩子自动绑定 `this` 上下文到实例中，因此你可以访问数据，对 `property` 和方法进行运算。这意味着你不能使用箭头函数来定义一个生命周期方法。

### beforeCreate

在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

### created

在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，property 和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，`$el property` 目前尚不可用。

### beforeMount

在挂载开始之前被调用：相关的 render 函数首次被调用。

### mounted

实例被挂载后调用，这时 `el` 被新创建的 `vm.$el` 替换了。如果根实例挂载到了一个文档内的元素上，当 mounted 被调用时 `vm.$el` 也在文档内。

注意 `mounted` 不会保证所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以在 mounted 内部使用 `vm.$nextTick`：

```js
mounted: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}
```

### beforeUpdate

数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。

### updated

由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用计算属性或 `watcher` 取而代之。

注意 `updated` 不会保证所有的子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，可以在 `updated` 里使用 `vm.$nextTick`。

### activated

被 keep-alive 缓存的组件激活时调用。

### deactivated

被 keep-alive 缓存的组件停用时调用。

### beforeDestroy

实例销毁之前调用。在这一步，实例仍然完全可用。

### destroyed

实例销毁后调用。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。

### errorCaptured

当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 false 以阻止该错误继续向上传播。
