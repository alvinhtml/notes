# JavaScript中各种源码实现

## 实现一个new操作符

我们首先知道new做了什么：

- 创建一个空的简单JavaScript对象（即{}）；
- 链接该对象（即设置该对象的构造函数）到另一个对象 ；
- 将步骤（1）新创建的对象作为this的上下文 ；
- 如果该函数没有返回对象，则返回this。知道new做了什么，接下来我们就来实现它

```js
function create(Con, ...args){
  // 创建一个空的对象
  this.obj = {};
  // 将空对象指向构造函数的原型链
  Object.setPrototypeOf(this.obj, Con.prototype);
  // obj绑定到构造函数上，便可以访问构造函数中的属性，即this.obj.Con(args)
  let result = Con.apply(this.obj, args);
  // 如果返回的result是一个对象则返回
  // new方法失效，否则返回obj
  return result instanceof Object ? result : this.obj;
}
```

## 实现一个Array.isArray

思路很简单，就是利用 Object.prototype.toString

```js
Array.myIsArray = function(o) {
  return Object.prototype.toString.call(Object(o)) === '[object Array]';
};
```

## 实现一个Object.create()方法

```js
function create =  function (o) {
    var F = function () {};
    F.prototype = o;
    return new F();
};
```


## 实现一个EventEmitter

```js
class Event {
  constructor () {
    // 储存事件的数据结构
    // 为查找迅速， 使用对象（字典）
    this._cache = {}
  }

  // 绑定
  on(type, callback) {
    // 为了按类查找方便和节省空间
    // 将同一类型事件放到一个数组中
    // 这里的数组是队列， 遵循先进先出
    // 即新绑定的事件先触发
    let fns = (this._cache[type] = this._cache[type] || [])
    if(fns.indexOf(callback) === -1) {
      fns.push(callback)
    }
    return this
    }

  // 解绑
  off (type, callback) {
    let fns = this._cache[type]
    if(Array.isArray(fns)) {
      if(callback) {
        let index = fns.indexOf(callback)
        if(index !== -1) {
          fns.splice(index, 1)
        }
      } else {
        // 全部清空
        fns.length = 0
      }
    }
    return this
  }
  // 触发emit
  trigger(type, data) {
    let fns = this._cache[type]
    if(Array.isArray(fns)) {
      fns.forEach((fn) => {
        fn(data)
      })
    }
    return this
  }

  // 一次性绑定
  once(type, callback) {
    let wrapFun = () => {
      callback.call(this);
      this.off(type, callback);
    };
    this.on(wrapFun, callback);
    return this;
  }
}

let e = new Event()

e.on('click',function(){
  console.log('on')
})
// e.trigger('click', '666')
console.log(e)
```

## 实现一个 Array.prototype.reduce

## 实现一个 call 或 apply

```js
Function.prototype.myApply = function(context, args) {
  if (typeof this !== 'function') {
    throw TypeError('Not a function!');
  }
  const result = context || window;
  result.fn = this;
  if (args) {
    result.fn(...args);
  } else {
    result.fn();
  }
  delete result.fn;
}
```
