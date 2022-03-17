## 手写 new

new 关键字会进行如下的操作：

- 创建一个空的简单 JavaScript 对象（即`{}`）；
- 为新对象添加属性`__proto__`，将该属性链接至构造函数的原型对象；
- 执行构造函数，将新对象作为 this 的上下文；
- 如果该函数没有返回对象，则返回 this。

```js
function create(fn, ...arg) {
  const obj = {};
  obj.__proto__ = fn.prototype;
  const result = fn.apply(obj, arg);
  return typeof result === "object" ? result : obj;
}
```
