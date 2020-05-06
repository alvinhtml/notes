# JavaScript API

## Object

### Object.create()

`Object.create()`方法创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`。

```
Object.create(proto[, propertiesObject])
```

- `proto` 新创建对象的原型对象。
- `propertiesObject` 可选。如果没有指定为 undefined，则是要添加到新创建对象的可枚举属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及相应的属性名称。这些属性对应`Object.defineProperties()`的第二个参数。

使用Object.create()来实现类式继承。这是一个所有版本JavaScript都支持的单继承。

```js
// Shape - 父类(superclass)
function Shape() {
  this.x = 0;
  this.y = 0;
}

// 父类的方法
Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};

// Rectangle - 子类(subclass)
function Rectangle() {
  Shape.call(this); // call super constructor.
}

// 子类续承父类
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;
```

如果你希望能继承到多个对象，则可以使用混入的方式。

```js
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}

// 继承一个类
MyClass.prototype = Object.create(SuperClass.prototype);
// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);
// 重新指定constructor
MyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
  // do a thing
};
```

使用 Object.create 的 propertyObject参数

```js
var o;

// 创建一个原型为null的空对象
o = Object.create(null);

o = {};
// 以字面量方式创建的空对象就相当于:
o = Object.create(Object.prototype);


o = Object.create(Object.prototype, {
  // foo会成为所创建对象的数据属性
  foo: {
    writable:true,
    configurable:true,
    value: "hello"
  },
  // bar会成为所创建对象的访问器属性
  bar: {
    configurable: false,
    get: function() { return 10 },
    set: function(value) {
      console.log("Setting `o.bar` to", value);
    }
  }
});

// 创建一个以另一个空对象为原型,且拥有一个属性p的对象
o = Object.create({}, { p: { value: 42 } })

// 省略了的属性特性默认为false,所以属性p是不可写,不可枚举,不可配置的:
o.p = 24
o.p
//42

o.q = 12
for (var prop in o) {
  console.log(prop)
}
//"q"

delete o.p
//false

//创建一个可写的,可枚举的,可配置的属性p
o2 = Object.create({}, {
  p: {
    value: 42,
    writable: true,
    enumerable: true,
    configurable: true
  }
});
```

### Object.defineProperty()

`Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。

该方法允许精确添加或修改对象的属性。通过赋值操作添加的普通属性是可枚举的，能够在属性枚举期间呈现出来（`for...in` 或 `Object.keys` 方法）， 这些属性的值可以被改变，也可以被删除。这个方法允许修改默认的额外选项（或配置）。默认情况下，使用 `Object.defineProperty()` 添加的属性值是不可修改的。

```
Object.defineProperty(obj, prop, descriptor)
```

- `obj` 要在其上定义属性的对象。
- `prop` 要定义或修改的属性的名称。
- `descriptor` 将被定义或修改的属性描述符。

#### 属性描述符

对象里目前存在的属性描述符有两种主要形式：`数据描述符`和`存取描述符`。数据描述符是一个具有值的属性，该值可能是可写的，也可能不是可写的。存取描述符是由`getter-setter`函数对描述的属性。描述符必须是这两种形式之一；不能同时是两者。

数据描述符和存取描述符均具有以下可选键值(默认值是在使用`Object.defineProperty()`定义属性的情况下)：

- `configurable` 当且仅当该属性的 `configurable` 为 `true` 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。
- `enumerable` 当且仅当该属性的 `enumerable` 为 `true` 时，该属性才能够出现在对象的枚举属性中。默认为 `false`。

数据描述符同时具有以下可选键值：

- `value` 该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 `undefined`。
- `writable` 当且仅当该属性的 `writable` 为 `true` 时，`value` 才能被赋值运算符改变。默认为 `false`。

存取描述符同时具有以下可选键值：

- `get` 一个给属性提供 `getter` 的方法，如果没有 `getter` 则为 `undefined`。当访问该属性时，该方法会被执行，方法执行时没有参数传入，但是会传入 `this` 对象（由于继承关系，这里的 `this` 并不一定是定义该属性的对象）。默认为 `undefined`。
- set 一个给属性提供 `setter` 的方法，如果没有 `setter` 则为 `undefined`。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。默认为 `undefined`。


### Object.defineProperties()

在一个对象上定义一个或多个新的属性或修改现有属性，并返回该对象。

```
Object.defineProperties(obj, props)
```

### Object.getOwnPropertyDescriptor(obj, prop)
### Object.getOwnPropertyDescriptors(obj)

#### Object.getOwnPropertyNames()

`Object.getOwnPropertyNames()` 方法返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括Symbol值作为名称的属性）组成的数组。

```
Object.getOwnPropertyNames(obj)
```

### Object.getOwnPropertySymbols()

`Object.getOwnPropertySymbols()` 方法返回一个给定对象自身的所有 Symbol 属性的数组。

### Object.getPrototypeOf(obj)

`Object.getPrototypeOf()` 方法返回指定对象的原型（内部[[Prototype]]属性的值）

### Object.entries()

`Object.entries()` 方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 `for...in` 循环遍历该对象时返回的顺序一致（区别在于 `for-in` 循环也枚举 **原型链** 中的属性）。

### Object.fromEntries()

`Object.fromEntries()` 方法接收一个键值对的列表参数，并返回一个带有这些键值对的新对象。这个迭代参数应该是一个能够实现@iterator方法的的对象，返回一个迭代器对象。它生成一个具有两个元素的类数组的对象，第一个元素是将用作属性键的值，第二个元素是与该属性键关联的值。

Object.fromEntries() 是 Object.entries 的反转。

```js
const arr = [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ];
const obj = Object.fromEntries(arr);
console.log(obj); // { 0: "a", 1: "b", 2: "c" }

const map = new Map([ ['foo', 'bar'], ['baz', 42] ]);
const obj = Object.fromEntries(map);
console.log(obj); // { foo: "bar", baz: 42 }
```


### Object.freeze()

`Object.freeze()` 方法可以冻结一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。freeze() 返回和传入的参数相同的对象。

### Object.isFrozen()

方法判断一个对象是否被冻结。

### Object.preventExtensions()

方法让一个对象变的不可扩展，也就是永远不能再添加新的属性。

### Object.isExtensible()

方法判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）。

### Object.isSealed()

方法判断一个对象是否被密封。

### Object.keys()

`Object.keys()` 方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 `for...in` 循环遍历该对象时返回的顺序一致 。

### Object.values()

`Object.values()` 方法返回一个给定对象自身的所有可枚举属性值的数组，值的顺序与使用 `for...in` 循环的顺序相同 ( 区别在于 for-in 循环枚举原型链中的属性 )。

### obj.hasOwnProperty(prop)

所有继承了 Object 的对象都会继承到 hasOwnProperty 方法。这个方法可以用来检测一个对象是否含有特定的自身属性；和 in 运算符不同，该方法会忽略掉那些从原型链上继承到的属性。

### isPrototypeOf()

方法用于测试一个对象是否存在于另一个对象的原型链上。

`isPrototypeOf()` 与 `instanceof` 运算符不同。在表达式 `object instanceof AFunction` 中，`object` 的原型链是针对 `AFunction.prototype` 进行检查的，而不是针对 `AFunction` 本身。

- `Y instanceof X` 判断是的是 `X` 的 `prototype` 是否在Y的原型链上，而我们知道实例的原型链（`__proto__`）指向的就是其构造函数的 `prototype`, 即 `Y instanceof X` 判断 `Y` 是否是 `X` 的一个实例（若 `Y` 是 `X` 的实例，那他也是 `X` 的父类的实例）。
- `X.isPrototypeOf(Y)` 判断的是 `X` 对象是否在 `Y` 的原型链上，同样 `Y` 继承 `X` 的关系是 `X` 对象在 `Y` 对象的原型链上，即 `X.isPrototypeOf(Y)` 判断X是否继承至 `Y`。

### obj.propertyIsEnumerable(prop)

方法返回一个布尔值，表示指定的属性是否可枚举。

### Object.is(value1, value2)

方法判断两个值是否是相同的值。

### obj.valueOf()

方法返回指定对象的原始值。

### Object.setPrototypeOf(obj, prototype)

`Object.setPrototypeOf()` 方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  null。

由于现代 JavaScript 引擎优化属性访问所带来的特性的关系，更改对象的 [[Prototype]]在各个浏览器和 JavaScript 引擎上都是一个很慢的操作。其在更改继承的性能上的影响是微妙而又广泛的，这不仅仅限于 `obj.__proto__ = ...` 语句上的时间花费，而且可能会延伸到任何代码，那些可以访问任何[[Prototype]]已被更改的对象的代码。如果你关心性能，你应该避免设置一个对象的 [[Prototype]]。相反，你应该使用 `Object.create()` 来创建带有你想要的[[Prototype]]的新对象。
