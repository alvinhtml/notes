# JS call apply bind 的区别

call 和 apply 都是为了改变某个函数运行时的 context 即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。call 和 apply二者的作用完全一样，只是接受参数的方式不太一样。

## apply

```
语法： Function.apply(obj, args)
```

### JS实现

```js
Function.prototype.myApply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }

  const thisContext = context || window;

  // 为context添加一个方法属性
  thisContext.fn = this;

  let result;

  // 判断是否存在第二个参数, 如果存在就将第二个参数也展开
  if(arguments[1]) {
    result = context.fn(...arguments[1]);
  } else {
    result = context.fn();
  }

  delete context.fn;

  return result;
}
```

## call


```
语法： Function.call(obj, arg1, arg2, ...)
```

### JS 实现

```js
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }

  const thisContext = context || window;

  // 给context添加一个方法属性
  thisContext.fn = this;

  // 通过参数伪数组将 context 后面的参数取出来
  const args = [...arguments].slice(1);

  var result = thisContext.fn(...args);

  // 删除 fn
  delete thisContext.fn;
  return result;
}
```



## bind


```js
Function.proptotype.myBind = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }
  const _this = this;

  const args = [...arguments].slice(1);

  return function f() {
    _this.call(context, ...args);
  }
}
```
