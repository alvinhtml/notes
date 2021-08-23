# JavaScript 前端面试题

## 常见的浏览器内核有哪些 ？

Trident 内核：IE, 360，搜狗浏览器 MaxThon、TT、The World,等。[又称 MSHTML]
Gecko 内核：火狐，FF，MozillaSuite / SeaMonkey 等
Presto 内核：Opera7 及以上。[Opera 内核原为：Presto，现为：Blink]
Webkit 内核：Safari，Chrome 等。 [ Chrome 的：Blink（WebKit 的分支）]

## 介绍一下你对浏览器内核的理解 ?

内核主要分成两部分：渲染引擎(layout engineer 或 Rendering Engine) 和 JS 引擎。

#### 渲染引擎

负责取得网页的内容（HTML、XML、图像等等）、整理讯息（例如加入 CSS 等），以及计算网页的显示方式，然后会输出至显示器或打印机。
浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不相同。
所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。

#### JS 引擎

解析和执行 javascript 来实现网页的动态效果。

最开始渲染引擎和 JS 引擎并没有区分的很明确，后来 JS 引擎越来越独立，内核就倾向于只指渲染引擎。

## 哪些常见操作会造成内存泄漏 ？

内存泄漏指任何对象在您不再拥有或需要它之后仍然存在。

垃圾回收器定期扫描对象，并计算引用了每个对象的其他对象的数量。如果一个对象的引用数量为 0（没有其他对象引用过该对象），或对该对象的惟一引用是循环的，那么该对象的内存即可回收。

- setTimeout 的第一个参数使用字符串而非函数的话，会引发内存泄漏。
- 闭包、控制台日志、循环（在两个对象彼此引用且彼此保留时，就会产生一个循环）


## 线程与进程的区别 ？

- 一个程序至少有一个进程，一个进程至少有一个线程。
- 线程的划分尺度小于进程，使得多线程程序的并发性高。
- 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

线程在执行过程中与进程还是有区别的。

- 每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
- 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。
- 但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。


## eval() 函数有什么用 ？

eval() 函数可计算某个字符串，并执行其中的的 JavaScript 代码。


## 函数柯里化

实现一个add方法，使计算结果能够满足如下预期：

- add(1)(2)(3) = 6;
- add(1, 2, 3)(4) = 10;
- add(1)(2)(3)(4)(5) = 15;

```js
function add() {
  let result = 0;
  const addFn = function() {
    return (result += Array.prototype.slice.call(arguments).reduce((a, b) => a + b)) && addFn;
  }
  addFn.toString = () => result;
  return addFn(...arguments);
}

console.log(add(1)(2)(3)); // 6
console.log(add(1, 2, 3)(4)); // 10
console.log(add(1)(2)(3)(4)(5)); // 15
```
### Currying有哪些好处呢

#### 参数复用

```js
// 正常正则验证字符串 reg.test(txt)

// 函数封装后
function check(reg, txt) {
    return reg.test(txt)
}

check(/\d+/g, 'test')       //false
check(/[a-z]+/g, 'test')    //true

// Currying后
function curryingCheck(reg) {
    return function(txt) {
        return reg.test(txt)
    }
}

var hasNumber = curryingCheck(/\d+/g)
var hasLetter = curryingCheck(/[a-z]+/g)

hasNumber('test1')      // true
hasNumber('testtest')   // false
hasLetter('21212')      // false
```

正常来说直接调用check函数就可以了，但是如果我有很多地方都要校验是否有数字，其实就是需要将第一个参数reg进行复用，这样别的地方就能够直接调用hasNumber，hasLetter等函数，让参数能够复用，调用起来也更方便。


## 斐波那契数列

斐波那契数列前两项都是1，从第3项开始，每一项都等于前两项之和，斐波纳契数列以如下被以递推的方法定义：

```
F(1)=1，F(2)=1, F(n)=F(n-1)+F(n-2)（n>=3，n∈N*）
```




```js
const fibonacci = (n) => {
  let a = 1, b = 1, c = 2;

  if (n < 2) {
    return c;
  }

  for (let i = 2; i < n; i++) {
    [a, b, c] = [b, c, a + b]
  }
  return c;
}

fibonacci(20) // 6765, [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987, 1597, 2584, 4181, 6765]
```


## mouseenter 和 mouseover 的区别

不论鼠标指针穿过被选元素或其子元素，都会触发 mouseover 事件，对应 mouseout。
只有在鼠标指针穿过被选元素时，才会触发 mouseenter 事件，对应 mouseleave。

## js 字符串两边截取空白的 trim 的原型方法的实现

```js
// 删除左右两端的空格
function trim(str){
 return str.replace(/(^\s*)|(\s*$)/g, "");
}
```

## 下面的输出结果是？

```js
var out = 25,
inner = {
    out: 20,
    func: function () {
        var out = 30;
        return this.out;
    }
};
console.log((inner.func, inner.func)());
console.log(inner.func());
console.log((inner.func)());
console.log((inner.func = inner.func)());
```

结果：25，20，20，25

代码解析：这道题的考点分两个

- 作用域
- 运算符（赋值预算，逗号运算）

先看第一个输出：25，因为 ( inner.func, inner.func ) 是进行逗号运算符，逗号运算符就是运算前面的 ”,“ 返回最后一个，举个栗子

```js
var i = 0, j = 1, k = 2;
console.log((i++, j++, k)) // 返回的是 k 的值 2 ，如果写成 k++ 的话  这里返回的就是 3
console.log(i); // 1
console.log(j); // 2
console.log(k); // 2   
```
回到原题 `( inner.func, inner.func )` 就是返回 `inner.func` ，而 `inner.func` 只是一个匿名函数

```js
function () {
    var out = 30;
    return this.out;
}
```

而且这个匿名函数是属于 `window` 的，则变成了

```js
(function () {
    var out = 30;
    return this.out;
})()
```

此刻的 `this => window`

所以 out 是 25。

第二和第三个 console.log 的作用域都是 inner，也就是他们执行的其实是 `inner.func()`;
inner 作用域中是有 out 变量的，所以结果是 20。

第四个 console.log 考查的是一个等号运算 `inner.func = inner.func` ，其实返回的是运算的结果，
举个栗子

```js
var a = 2, b = 3;
console.log(a = b) // 输出的是 3
```

所以 `inner.func = inner.func` 返回的也是一个匿名函数

```js
function () {
    var out = 30;
    return this.out;
}
```

此刻，道理就和第一个 console.log 一样了，输出的结果是 25。


## 下面程序输出的结果是 ？

```js
if (!("a" in window)) { // false,  ("a" in window) === true, 因为 var a = 1; 变量提升；
    var a = 1;
}
alert(a);
```

实际结果是 “undefined”，代码等价于：

```js
var a;
if (!("a" in window)) {
    a = 1;
}
alert(a);
```


## 下面程序输出的结果是 ？

```js
function a(x) {
    return x * 2;
}
var a;
alert(a);
```

尽管变量声明在下面定义，但是变量 value 依然是 function，也就是说这种情况下，函数声明的优先级高于变量声明的优先级

```js
// 函数声明会提升，函数声明的优先级高于变量声明的优先级
function a(x) {
  return x * 2;
}

// 变量声明，但没有赋值
var a;
```


## 下面程序输出的结果是 ？ arguments

```js
function b(i, j, k) {
  arguments[2] = 10;
  alert(k); // 10
}
b(1, 2, 3);

const i = 1, j = 2, k = 3;
const c = [i, j, k];
c[2] = 10;
console.log(k); // 3
```

数组 `c[2]` 和 变量`k`， 指向不同的内存空间，`c = [i,j,k]`, 向当于把 k 的值赋给了 `c[2]` 所指向的的内存空间，在函数中，arguments[2] 和 参数 k，指向同一内存空间


## JavaScript 6种基本数据类型

一、数据类型

undefiend 没有定义数据类型
number 数值数据类型，例如 10 或者 1 或者 5.5，包括 NaN
string 字符串数据类型用来描述文本，例如 "你的姓名"
boolean 布尔类型 true | false ，不是正就是反
object 对象类型，复杂的一组描述信息的集合，包括 null
function 函数类型

## 异步过程的构成要素有哪些？和异步过程是怎样的 ？

总结一下，一个异步过程通常是这样的：

主线程发起一个异步请求，相应的工作线程接收请求并告知主线程已收到(异步函数返回)；
主线程可以继续执行后面的代码，同时工作线程执行异步任务；
工作线程完成工作后，通知主线程；
主线程收到通知后，执行一定的动作(调用回调函数)。


## 编写一个方法，求一个字符串的字节长度

假设：一个英文字符占用一个字节，一个中文字符占用两个字节

```js
function getBytes(str){
    var len = str.length;
    var bytes = len;
    for(var i = 0; i < len; i++){
        if (str.charCodeAt(i) > 255)  bytes++;
    }
    return bytes;
}
alert(getBytes("你好, as"));
```

## JSON 的了解 ？

- JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。
- 它是基于 JavaScript 的一个子集。
- 数据格式简单，易于读写，占用带宽小。
- 格式：采用键值对。例如：{ “age‟: ‟12‟, ”name‟: ‟back‟ }


## 合并数组

如果你需要合并两个数组的话，可以使用 Array.concat()， 也可以用 `Array.push.apply(arr1, arr2)` 来代替创建新的数组，它可以把第二个数组合并到第一个中，从而较少内存消耗

```js
var array1 = [1, 2, 3];
var array2 = [4, 5, 6];
console.log(array1.concat(array2)); // [1,2,3,4,5,6];

Array.push.apply(arr1, arr2)
```

## 把节点列表 (NodeList) 转换为数组

如果你运行 document.querySelectorAll("p") 方法，它可能会返回一个 DOM 元素的数组 — 节点列表对象。

但这个对象并不具有数组的全部方法，如 sort()，reduce()， map()，filter()。为了使用数组的那些方法，你需要把它转换为数组。

```js
var elements = document.querySelectorAll("p"); // NodeList
var arrayElements = [].slice.call(elements); // 现在 NodeList 是一个数组

var arrayElements = Array.from(elements); // 这是另一种转换 NodeList 到 Array  的方法
```

## 说说堆和栈的区别 ？

### 堆栈空间分配区别

- 栈（操作系统）：由操作系统自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈；　　
- 堆（操作系统）：一般由程序员分配释放， 若程序员不释放，程序结束时可能由 OS 回收，分配方式倒是类似于链表。　　

### 堆栈缓存方式区别

- 栈使用的是一级缓存， 他们通常都是被调用时处于存储空间中，调用完毕立即释放；　　
- 堆是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定（并不是一旦成为孤儿对象就能被回收）。所以调用这些对象的速度要相对来得低一些。　　

### 堆栈数据结构区别

- 堆（数据结构）：堆可以被看成是一棵树，如：堆排序；　　
- 栈（数据结构）：一种先进后出的数据结构。


## ES6 声明变量的六种方法

ES5 只有两种声明变量的方法：var 和 function 。
ES6 除了添加 let 和 const 命令。
还有两种声明变量的方法：import 命令和 class 命令。


## 手写 new

new操作符做了这些事：

- 基于函数的原型创建一个新的对象
- 使用 apply 调用函数，改变 this 指向，并传入参数
- 当函数返回值类型为对象时，则返回该对象, 否则, 返回新创建的对象。

```js
function create (fn, ...arg) {
  // 基于 fn 的原型创建一个新的对象
 const obj = Object.create(fn.prototype);

 // 将 fn 的 this 指向 obj, 并获取 fn 函数执行的结果, 作用是把 this.name = xx 绑定 obj 后，变为 obj.name = xx
 const res = fn.apply(obj, arg);

 // 当函数返回值类型为对象时，则返回该对象, 当函数返回值类型不为对象时，返回该构造函数的实例化对象
 return typeof res === 'object' ? res : obj;
}
```

## 手写 Promise

### Promise基本特征

1. promise 有三个状态：pending, fulfilled, rejected
2. new promise 时， 需要传递一个 `executor()` 执行器函数，执行器函数立即执行
3. executor 接受两个参数，分别是 resolve 和 reject
4. promise 的默认状态是 pending，promise 只能从 pending 到 rejected 或者 fulfilled，状态一旦确认，就不会再改变
5. promise 必须有一个 then 方法，then 接收两个参数，成功的回调 onFulfilled, 和失败的回调 onRejected
6. then 方法的执行结果也会返回一个 Promise 对象。因此我们可以进行then的链式执行，这也是解决回调地狱的主要方式。
7. 如果调用 then 时，promise 已经成功，则执行 onFulfilled，参数是 promise 的 value
8. 如果调用 then 时，promise 已经失败，那么执行 onRejected, 参数是 promise 的 reason
9. 如果 then 中抛出了异常，那么就会把这个异常作为参数，传递给下一个 then 的失败的回调 onRejected


Promise 的使用方法：

```js
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
};

myAsyncFunction('/api')
  .then((data) => {
    console.log("responseText", data);
  }, (error) => {
    console.log("statusText", error);
  })
```

Promise 实现：

```js
class myPromise {
  constructor(initfn) {
    this.status = 'pending';
    this.value = undefined;
    this.error = undefined;
    this.onResolvedCallbacks = [];
    this.onRejectedCallbacks= [];

    const resolve = (value) => {
      this.status = 'fulfilled';
      this.value = value;
      this.onResolvedCallbacks.forEach(cb => cb(value));
    }

    const reject = (error) => {
      this.status = 'rejected'
      this.error = error;
      this.onRejectedCallbacks.forEach(cb => cb(error));
    }

    try {
      initfn(resolve, reject);
    } catch(error) {
      this.status = 'rejected'
      this.error = error;
      this.onRejectedCallbacks.forEach(cb => cb(error));
    }
  }

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;
    onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err };

    let promise2 = new myPromise((resolved, rejected) => {
      if (this.status === 'pending') {
        this.onResolvedCallbacks.push((value) => {
          try {
            resolved(onFulfilled(this.value));
          } catch (error) {
            this.error = error;
            rejected(this.error);
          }
        });
        this.onRejectedCallbacks.push((error) => {
          rejected(onRejected(this.error));
        });
      }

      if (this.status === 'fulfilled') {
        try {
          resolved(onFulfilled(this.value));
        } catch (error) {
          this.error = error;
          rejected(this.error);
        }
      }
      if (this.status === 'rejected') {
        rejected(onRejected(this.error));
      }
    });

    return promise2;
  }
}
```


## 前端mvvm（react、vue，）框架基本都是由以下几部分组成

- 虚拟domdiff算法（本质是如何找到一个对象树的差异并更新，当然为了避免一股脑diff可能造成页面卡顿，可以设计成时间切片的形式）
- 如何设计组件化（函数组件、类组件）
- 数据更新机制（vue是数据劫持、react是调用setState）
- 全局状态管理（vuex、redux...）
- 路由设计逻辑复用机制（hooks、Function based ）
- 模板语法的选择（jsx or template）实现模板解析

综上，再复杂的框架都是由各个小的技术点累计而成的，那么将以上每一个技术点都能钻研透彻，并能够将其灵活的组合起来的那么你离完成一个框架就不远了。


## 命令式和声明式开发的区别

计算机系统是分层的，也就是下层做一些支持的工作，暴露接口给上层用。注意：语言的本质是一种接口。

计算机的最下层是CPU指令，其本质就是用“变量定义+顺序执行+分支判断+循环”所表达的逻辑过程。

计算机应用的最上层是实现人类社会的某种功能。所以所有计算机编码的过程，就是用逻辑表达现实的过程。层与层之间定义的借口，越接近现实的表达就叫越“声明式”（declarative），越接近计算机的执行过程就叫越“命令式”（imperative）。注意这不是绝对的概念，而是相对的概念。

当接口越是在表达“要什么”，就是越声明式；越是在表达“要怎样”，就是越命令式。

SQL就是在表达要什么（数据），而不是表达怎么弄出我要的数据，所以它就很“声明式”。C++就比C更声明式，因为面向对象本身就是一种声明式的体现。HTML也很声明式，它只描述我要一张什么样的表，并不表达怎么弄出一张表，而DOM操作就是命令式的。

简单的说，接口的表述方式越接近人类语言——词汇的串行连接（一个词汇实际上是一个概念）——就越“声明式”；越接近计算机语言——“顺序+分支+循环”的操作流程——就越“命令式”。

越是声明式，意味着下层要做更多的东西，或者说能力越强。也意味着效率的损失。越是命令式，意味着上层对下层有更多的操作空间，可以按照自己特定的需求要求下层按照某种方式来处理。实际上，这对概念应该叫做“声明式接口”和“命令式接口”。可能是因为它大部分时候是在谈论“语言”这种接口方式时才会用到，所以会叫做“声明式编程”和“命令式编程”。当然，你也可以把它当成一种编程思想，也就是说，在构建自己的代码时，为了结构的清晰可读，把代码分层，层之间的接口尽量声明式。这样你的代码自然在一层上主要描述从人的角度需要什么；另一层上用计算机逻辑实现人的需要。另外，这组概念总让人迷惑，可能一个原因是翻译问题。如果翻译成”说明式“和”指令式“应该容易理解的多。

- 命令式编程：命令“机器”如何去做事情(how)，这样不管你想要的是什么(what)，它都会按照你的命令实现。
- 声明式编程：告诉“机器”你想要的是什么(what)，让机器想出如何去做(how)。

## HOC、Render props、Hooks

HOC（高阶组件）高阶组件是参数为组件，返回值为新组件的函数。

- HOC 优点
  - 不会影响内层组件的状态, 降低了耦合度
  - HOC 可以做到很轻松地外部协议化注入功能到一个基础 Component 中，所以可以用来做插件。
  - HoC 的方式可以天然地进行组件的分层以及组合，并且这种分层基本都可以描述为状态注入以及 props mapping 的过程。
- HOC 的缺点
  - 固定的 props 可能会被覆盖
  - 调试不方便，当多个HOC嵌套时，props 究竟是来自于哪里，如果有问题、问题又出自于哪里。


Render props 将一个组件内的 state 作为 props 传递给调用者, 调用者可以动态的决定如何渲染.

1. 接收一个外部传递进来的 props 属性
2. 将内部的 state 作为参数传递给调用组件的 props 属性方法.

```js
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.state = { x: 0, y: 0 };
  }
  render() {
    return (
      <div style={{ height: '100%' }}>
        {this.props.render(this.state)}
      </div>
    );
  }
}

// 调用方式:
<Mouse render={mouse => (
  <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
)}/>
```

- 缺点
  - 无法在 return 语句外访问数据
  - 很容易导致嵌套地狱

Hook

```js
const { x, y } = useMouse();
const { x: pageX, y: pageY } = usePage();

useEffect(() => {

}, [pageX, pageY])
```

- hook 可以重命名参数
  - 如果 2 个 hook 暴露的参数一样,我们可以简单地进行重命名.
- hook 会清晰地标注来源
  - 从上面的例子可以简单地看到, x 和 y 来源于 useMouse. 下面的 x, y 来源于 usePage
- hook 可以让你在 return 之外使用数据
- hook 不会嵌套
- 简单易懂, 对比 hoc 和 render props 两种方式, 它非常直观, 也更容易理解

## DomToJson

```js
function domToJson(node) {
  return (node && node.tagName) ? {
    tag: node.tagName,
    childs: Array.from(node.childNodes).map(n => domToJson(n))
  } : {tag: 'TEXT'}
}

console.log(domToJson(document.getElementById('mydom')));
```
