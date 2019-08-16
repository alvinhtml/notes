# Javascript


## Javascript this
Javascript 是一个文本作用域的语言, 就是说, 一个变量的作用域, 在写这个变量的时候确定. this 关键字是为了在 JS 中加入动态作用域而做的努力. 所谓动态作用域, 就是说变量的作用范围, 是根据函数调用的位置而定的. 从这个角度来理解 this, 就简单的多.

this 是 JS 中的动态作用域机制, 具体来说有四种, 优先级有低到高分别如下:
1. 默认的 this 绑定, 就是说 在一个函数中使用了 this, 但是没有为 this 绑定对象. 这种情况下, 非严格默认, this 就是全局变量 Node 环境中的 global, 浏览器环境中的 window.
2. 隐式绑定: 使用 obj.foo() 这样的语法来调用函数的时候, 函数 foo 中的 this 绑定到 obj 对象.
3. 显示绑定: foo.call(obj, ...), foo.apply(obj,[...]), foo.bind(obj,...)
4. 构造绑定: new foo() , 这种情况, 无论 foo 是否做了绑定, 都要创建一个新的对象, 然后 foo 中的 this 引用这个对象.
