# Javascript


## Javascript this

Javascript 是一个文本作用域的语言, 就是说, 一个变量的作用域, 在写这个变量的时候确定. this 关键字是为了在 JS 中加入动态作用域而做的努力. 所谓动态作用域, 就是说变量的作用范围, 是根据函数调用的位置而定的. 从这个角度来理解 this, 就简单的多.

this 是 JS 中的动态作用域机制, 具体来说有四种, 优先级有低到高分别如下:
1. 默认的 this 绑定, 就是说 在一个函数中使用了 this, 但是没有为 this 绑定对象. 这种情况下, 非严格默认, this 就是全局变量 Node 环境中的 global, 浏览器环境中的 window.
2. 隐式绑定: 使用 obj.foo() 这样的语法来调用函数的时候, 函数 foo 中的 this 绑定到 obj 对象.
3. 显示绑定: foo.call(obj, ...), foo.apply(obj,[...]), foo.bind(obj,...)
4. 构造绑定: new foo() , 这种情况, 无论 foo 是否做了绑定, 都要创建一个新的对象, 然后 foo 中的 this 引用这个对象.

## Javascript 中的 prototype 属性

在典型的面向对象的语言中，如java，都存在类（class）的概念，类就是对象的模板，对象就是类的实例。但是在Javascript语言体系中，是不存在类（Class）的概念的，javascript中不是基于‘类的'，而是通过构造函数（constructor）和原型链（prototype chains）实现的。但是在ES6中提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。基本上，ES6的class可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的class写法只是让原型对象的写法更加清晰、更像面向对象编程的语法而已。

### 构造函数的简单介绍

所谓构造函数，就是提供了一个生成对象的模板并描述对象的基本结构的函数。一个构造函数，可以生成多个有相同结构的对象。总的来说，构造函数就是对象的模板，对象就是构造函数的实例。

构造函数的特点有：

- a：构造函数的函数名首字母必须大写。
- b：内部使用this对象，来指向将要生成的对象实例。
- c：使用new操作符来调用构造函数，并返回对象实例。

### 构造函数的缺点

所有的实例对象都可以继承构造函数中的属性和方法。但是，多个实例之间，无法共享属性。

```js
function Person(name){
  this.name = name;
  this.say = function() {
    return 'name:' + this.name;
  }
}
var boy = new Person('keith');
var girl = new Person('rascal');

console.log(boy.say === girl.say); // false
```

每次 `new Person()` 都会生成一个全新的 `say` 方法。这既没有必要，又浪费资源，因为所有 `say` 方法都是同样的行为，完全可以被两个对象实例共享。

为了解决构造函数的对象实例之间无法共享属性的缺点，js提供了prototype属性。

### prototype 属性的作用

js中每个数据类型都是对象（除了null和undefined），而每个对象都继承自另外一个对象，后者称为“原型”（prototype）对象，只有null除外，它没有自己的原型对象。

原型对象上的所有属性和方法，都会被对象实例所共享。

```js
function Person(name){
  this.name = name;
}

Person.propotype.say = function() {
  return 'name:' + this.name;
}

var boy = new Person('keith');
var girl = new Person('rascal');

console.log(boy.say === girl.say); // false
```

- a：原型对象的作用，就是定义所有对象实例所共享的属性和方法。
- b：prototype，对于构造函数来说，它是一个属性；对于对象实例来说，它是一个原型对象。

### 原型链（prototype chains）

对象的属性和方法，有可能是定义在自身，也有可能是定义在它的原型对象。由于原型对象本身对于对象实例来说也是对象，它也有自己的原型，所以形成了一条原型链（prototype chain）。比如，a对象是b对象的原型，b对象是c对象的原型，以此类推。所有一切的对象的原型顶端，都是Object.prototype，即Object构造函数的prototype属性指向的那个对象。

当然，Object.prototype 对象也有自己的原型对象，那就是没有任何属性和方法的 `null` 对象，而 `null` 对象没有自己的原型。

#### 原型链（prototype chain）的特点有：

- a：读取对象的某个属性时，JavaScript引擎先寻找对象本身的属性，如果找不到，就到它的原型去找，如果还是找不到，就到原型的原型去找。如果直到最顶层的Object.prototype还是找不到，则返回undefined。
- b：如果对象自身和它的原型，都定义了一个同名属性，那么优先读取对象自身的属性，这叫做“覆盖”（overiding）。
- c：一级级向上在原型链寻找某个属性，对性能是有影响的。所寻找的属性在越上层的原型对象，对性能的影响越大。如果寻找某个不存在的属性，将会遍历整个原型链。

### constructor 属性

prototype对象有一个constructor属性，默认指向prototype对象所在的构造函数。

要注意的是，prototype是构造函数的属性，而constructor则是构造函数的prototype属性所指向的那个对象，也就是原型对象的属性。注意不要混淆。

### instanceof 运算符

instanceof运算符返回一个布尔值，表示指定对象是否为某个构造函数的实例。

因为instanceof对整个原型链上的对象都有效，所以同一个实例对象，可能会对多个构造函数都返回true。

注意，instanceof对象只能用于复杂数据类型（数组，对象等），不能用于简单数据类型（布尔值，数字，字符串等）。

此外，null和undefined都不是对象，所以instanceof 总是返回false。

利用instanceof运算符，还可以巧妙地解决，调用构造函数时，忘了加new命令的问题。

```js
function Person(name){
  if(this instanceof Person) {
    this.name = name;
  } else {
    throw new Error('必须通过 new 关键字调用！');
  }
}

// 但是这种判断方法也不完全可靠，可以骗过检测
var person = new Person('Micheal');
var boy = Person.call(person, 'keith');
```

## 单线程

### 主线程

JavaScript是单线程的，所谓单线程，是指在JS引擎中负责解释和执行JavaScript代码的线程只有一个，叫它主线程；

### 工作线程

实际上浏览器还存在其他的线程，例如：处理AJAX请求的线程、处理DOM事件的线程、定时器线程、读写文件的线程（例如在Node.js中）等等，这些线程可能存在于JS引擎之内，也可能存在于JS引擎之外，在此我们不作区分，统一叫它们工作线程；

### 总结

- JavaScript引擎是单线程运行的,浏览器无论在什么时候都有且只有一个线程在运行JavaScript程序；
- JavaScript引擎用单线程运行也是有意义的,单线程不必理会线程同步这些复杂的问题，问题得到简化；


## js 的两种回收机制

### 标记清除（mark and sweep）

从语义上理解就比较好理解了，大概就是当变量进入到某个环境中的时候就把这个变量标记一下，比如标记为“进入环境”，当离开的时候就把这个变量的标记给清除掉，比如是“离开环境”。而在这后面还有标记的变量将被视为准备删除的变量。

- 垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记（可以使用任何标记方式）。
- 然后，它会去掉环境中的变量以及被环境中的变量引用的变量的标记。
- 而在此之后再被加上的标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。
- 最后，垃圾收集器完成内存清除工作。销毁那些带标记的值并回收它们所占用的内存空间。

这是 javascript 最常见的垃圾回收方式。至于上面有说道的标记，到底该如何标记 ？

好像是有很多方法，比如特殊位翻转，维护一个列表什么的。

### 引用计数（reference counting）

- 引用计数的含义是跟踪记录每个值被引用的次数，当声明一个变量并将一个引用类型的值赋给该变量时，这个时候的引用类型的值就会是引用次数 +1 了。如果同一个值又被赋给另外一个变量，则该值的引用次数又 +1。
- 相反如果包含这个值的引用的变量又取得另外一个值，即被重新赋了值，那么这个值的引用就 -1 。当这个值的引用次数编程 0 时，表示没有用到这个值，这个值也无法访问，因此环境就会收回这个值所占用的内存空间回收。
- 这样，当垃圾收集器下次再运行时，它就会释放引用次数为 0 的值所占用的内存。

## 闭包

#### 为什么要有闭包？

在 JavaScript 中，函数可以被当做参数传递给其他函数，这样就面临一个问题，当函数在不同的执行栈中被调用的时候，如何保证这个函数能够准确的访问到他所引用的外部变量的值，闭包的设计就是用来解决这个问题？

#### 闭包的原理

在 JavaScript 中，一函数能够访问它外部的变量，这种作用域范围被称作词法作用域，闭包的原理就是把整个词法作用域里的变量绑定到这个函数上，使这个函无论在哪被调用时，都能够访问到词法作用域里的变量，闭包说白了就是把函数的词法作用域里的变量绑定到这个函数上，很像 `bind()` 方法，这种特性带给函数两个能力，

## Reflect

1. 将 Object 对象一些内部的方法，放到 Reflect对象上。比如：object.defineProperty
 - 说明：现阶段这些方法存在于object和Reflect对象上，未来只存在于Reflect对象上。
 - 意义：也就是说，从Reflect 对象上可以拿到语言内部的方法。
2. 操作对象时出现报错返回false
 - 说明：比如，`Object.defineProperty(obj, name, desc)` 在无法定义属性时，会抛出一个错误，而 `Reflect.defineProperty(obj, name, desc)` 则会返回 `false`。

## 严格模式与非严格模式

对于 JavaScript 严格模式与非严格模式的区别，请查看权威文档 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode): 严格模式。

严格模式是采用具有限制性JavaScript变体的一种方式，从而使代码显示地 脱离“马虎模式/稀松模式/懒散模式“（sloppy）模式。

- 首先，严格模式通过抛出错误来消除一些原有的静默错误。
  - 严格模式下，为不存在的全局变量赋值会抛出错误，在正常模式下这种操作不会产生任何效果
  - 严格模式下，给 NaN 赋值会抛出一个异常在正常模式下，在正常模式下给 NaN 赋值不会产生任何作用，开发者也不会受到任何错误反馈
  - 严格模式下，试图删除不可删除的属性时会抛出异常，在正常模式下这种操作不会产生任何效果
  - 严格模式下要求一个对象内的所有属性名在对象内必须唯一，正常模式下重名属性是允许的，只有最后一个属性起作用
  - 严格模式要求函数的参数名唯一，在正常模式下，最后一个重名参数名会掩盖之前的重名参数
- 其次，严格模式修复了一些导致JavaScript引擎难以执行优化的缺陷：有时候，相同的代码，严格模式可以比非严格模式下运行的更快。
- 第三，严格模式禁用了在ECMAScript的未来版本中可能会定义的一些语法。
  - 在严格模式中一部分字符变成了保留的关键字。这些字符包括implements, interface, let, package, private, protected, public, static和yield。在严格模式下，你不能再用这些名字作为变量名或者形参名。
  - 严格模式禁止了不在脚本或者函数层面上的函数声明

### 调用严格模式

```js
// 整个脚本都开启严格模式的语法
"use strict";
var v = "Hi!  I'm a strict mode script!";

// 为函数开启严格模式
function strict() {
  'use strict';

  function nested() {
    return "And so am I!";
  }
  return "Hi!  I'm a strict mode function!  " + nested();
}
```
