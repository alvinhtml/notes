# Immutable

## Immutable 由何而生

说immutable之前，首先看下什么是mutable。js 在原生创建数据类型即是 mutable，可变的。

const 只是浅层次的防篡改，层级一深就没辙了。

```js
let obj = {
  a: 1,
  b: 2
}

let obj1 = obj;
obj1.a = 999;

console.log("obj.a", obj.a); // 999
```

js 在创建变量、赋值后是可变的。除了基本类型，其他的引用类型，通过变量地址来共享。

改变了 obj1.a 的值，同时也会改变 obj.a 的值。其实改变的是同一个对象引用。这样共享地址来共享值的好处是节省内存，坏处是稍微不注意就会导致改A坏B的棘手问题。

### Deep Copy

一般的解法就是使用深拷贝而非浅拷贝，生成一份基本类型值完全相同但是没有共享地址的数据，除了浪费内存之外，深拷贝复杂引用类型时需要深度遍历，这样的做法在 React 这样频繁更新数据和对数据更新性能有要求的场景，深拷贝是一个不优雅不推荐，say no 的选择。

那怎么做呢，这个时候 Immutable 就可以闪亮登场解决这个问题，为什么呢？

## Immutable 是个什么

![Immutable 是个什么](../assets/immutable.gif)

> 相对于mutable，Immutable就是在创建变量、赋值后便不可更改，若对其有任何变更,就会回传一个新值

Immutable 只是一个定义，有各种实现，Immutable.js 就是 facebook 工程师实现的 js 的 Immutable 历时三年的烧脑之作。甚至有些语言天生就是不可变数据结构，比如国内 react 的早期先驱题叶极力推崇的 ClojureScript。

每次返回新值，大家可能会觉得性能也并不好啊，又占内存之类的。如果实现了结构共享，每次的新值共享内部结构以大幅减少内存占用。这意味着，如果对一个 Immutable 进行赋值1000次，并不会创建1000倍大小的内存占用数据。

> 与原生 JS 的 mutable 语义强烈冲突

除非从零开始一个项目，不然这种使用导致我们可能用混，第三方库也只支持原生 js 对象。

我们需要采用一些手段来规避用混。

使用类型系统，TypeScript 或 Flow。消除了 Immutable 流经系统的精神负担。代价是编写风格将颠覆式的完全不同。

隐藏有关数据结构的详细信息。如果您在系统的特定部分使用 Immutable.js ，请不要在其外部进行任何操作直接访问数据结构。一个很好的例子是Redux，它是单原子 app 状态。如果 app 状态是 Immutable.js 对象，请不要强制 React 组件直接使用 Immutable.js 的 API。

> 结构共享是指没有改变的数据共用一个引用，这样既减少了深拷贝的性能消耗，也减少了内存。

## 使用 Immutable

### 安装

```
npm install immutable --save
```

### 使用

在 react 中，immutable 主要是防止 state 对象被错误赋值。

将js对象转成immutable对象

```js
import { fromJS } from 'immutable';

const defaultState = fromJS({
  todoList: []
});
```

### 获取属性

```js
// 获取store中的todoList
state.get('todoList');

// 获取 Main 组件中 store 的 todoList
state.get(['Main', 'todoList']);
```

### 改变属性

```js
state.set('todoList', action.value);  // 设置单个属性值
// 设置多个属性
state.merge({
  // 由于action.value是js对象所以要转成immutable对象
  todoList: fromJS(action.value)
});
```

### 将 immutable 对象转成 js 对象

```js
state.get('todoList').toJS(); // 把todoList转成js数组
```

## Immutable.js 的几种数据类型

```
List: 有序索引集，类似JavaScript中的Array。
Map: 无序索引集，类似JavaScript中的Object。
OrderedMap: 有序的Map，根据数据的set()进行排序。
Set: 没有重复值的集合。
OrderedSet: 有序的Set，根据数据的add进行排序。
Stack: 有序集合，支持使用unshift()和shift()添加和删除。
Record: 一个用于生成Record实例的类。类似于JavaScript的Object，但是只接收特定字符串为key，具有默认值。
Seq: 序列，但是可能不能由具体的数据结构支持。
Collection: 是构建所有数据结构的基类，不可以直接构建。
```
