## 手写 new

new 关键字会进行如下的操作：

- 创建一个空的简单 JavaScript 对象（即`{}`）；
- 为新对象添加属性`__proto__`，将该属性链接至构造函数的原型对象；
- 执行构造函数，将新对象作为 this 的上下文；
- 如果该函数没有返回对象，则返回 this。

```js
function create(fn, ...arg) {
  const obj = {}
  obj.__proto__ = fn.prototype
  const result = fn.apply(obj, arg)
  return typeof result === 'object' ? result : obj
}
```

## 与枚举属性有关的几个方法

- `Object.getOwnPropertyNames()` 获取对象自身所有的属性键名，包括可枚举和不可枚举的属性。
- `Object.keys()` 获取对象自身的可枚举属性键名，返回键名数组。
- `obj.propertyIsEnumerable()` 判断对象实例 obj 自身的属性是否为可枚举。
- `for...in` 遍历对象自身的和原型链上的可枚举的属性。可配合 hasOwnProperty()只获取自身可枚举属性
- `Object.values()` 获取对象自身的可枚举属性的值，返回值数组。
- `Object.entries()` 获取对象自身的可枚举属性的键值对，每个键值对以数组的形式存储，返回键值对数组集。
- 以上几个方法除了 `for...in` 外其他的方法都无法遍历到原型链的属性。

- `Reflect.ownKeys()` 获取对象自身所有的属性键名，包括 `Symbol` 类型属性
- `Object.getOwnPropertySymbols()` 获取对象自身 `Symbol` 类型属性
- `Symbol 作为属性名，遍历对象的时候，该属性不会出现在 `for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()` 返回。
