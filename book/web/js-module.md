# Module

## 什么是 AMD

AMD(Asynchronous Module Definition)，意思就是"异步模块定义"。它采用异步方式加载模块，制定了定义模块的规则，这样模块和模块的依赖可以被异步加载，不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。这和浏览器的异步加载模块的环境刚好适应（浏览器同步加载模块会导致性能、可用性、调试和跨域访问等问题）

AMD 规范只定义了一个函数 "define"，它是全局变量 `define(id?, dependencies?, factory)`，参数分别是模块名，依赖，工厂方法

```
define(id?, dependencies?, factory);
```

具体用法如下：

```js
// moudle-a.js
define('moudleA', function() {
    return {
        a: 1
    }
});

// moudle-b.js
define(['moudleA'], function(ma) {
    var b = ma.a + 2;

    return {
        b: b
    }
});
```

它看起来似乎和 CMD 差不多，不过在实现上还是有一定的差异，它们各有优缺点。

## 什么是 CMD

CMD 叫做公共模块定义规范（Common Module Definiton），它是类似于 CommonJs 模块化规范，但是运行于浏览器之上的。
AMD推崇依赖前置（在定义模块的时候就要声明其依赖的模块），CMD推崇依赖就近（只有在用到某个模块的时候再去require——按需加载)。

```
define(factory);
```

具体用法如下：

```js
// moudle-a.js
define(function(require, exports, module) {

    module.exports = {
        a: 1
    };

});

// moudle-b.js
define(function(require, exports, module) {

    var ma = require('./moudle-a');
    var b = ma.a + 2;
    module.exports = {
        b: b
    };

});
```

## AMD vs. CMD

AMD 是 RequireJS 在推广过程中对模块定义的规范化产出，CMD是SeaJS 在推广过程中被广泛认知。RequireJs出自dojo加载器的作者James Burke，SeaJs出自国内前端大师玉伯。

RequireJS 和 SeaJS 都是很不错的模块加载器，两者区别如下：

1. 两者定位有差异。RequireJS 想成为浏览器端的模块加载器，同时也想成为 Rhino / Node 等环境的模块加载器。SeaJS 则专注于 Web 浏览器端，同时通过 Node 扩展的方式可以很方便跑在 Node 服务器端
2. 两者遵循的标准有差异。RequireJS 遵循的是 AMD（异步模块定义）规范，SeaJS 遵循的是 CMD （通用模块定义）规范。规范的不同，导致了两者API 的不同。SeaJS 更简洁优雅，更贴近 CommonJS Modules/1.1 和 Node Modules 规范。
3. 两者社区理念有差异。RequireJS 在尝试让第三方类库修改自身来支持 RequireJS，目前只有少数社区采纳。SeaJS 不强推，而采用自主封装的方式来“海纳百川”，目前已有较成熟的封装策略。
4. 两者代码质量有差异。RequireJS 是没有明显的 bug，SeaJS 是明显没有 bug。
5. 两者对调试等的支持有差异。SeaJS 通过插件，可以实现 Fiddler 中自动映射的功能，还可以实现自动 combo 等功能，非常方便便捷。RequireJS无这方面的支持。
6. 两者的插件机制有差异。RequireJS 采取的是在源码中预留接口的形式，源码中留有为插件而写的代码。SeaJS 采取的插件机制则与 Node 的方式一致开放自身，让插件开发者可直接访问或修改，从而非常灵活，可以实现各种类型的插件。

- 优点： 同样实现了浏览器端的模块化加载。 可以按需加载，依赖就近。
- 缺点： 依赖SPM打包，模块的加载逻辑偏重。


## 什么是CommonJs

CommonJs 是一种 JavaScript 语言的模块化规范，它通常会在服务端的 Nodejs 上使用。项目是由多个模块组成的，模块和模块之间的调用，需要各个模块有相同规范的 API，这样一来在使用的过程中不会有那么多的学习成本，并且对于单个模块来说是类聚的。

在 CommonJs 的模块化规范中，每一个文件就是一个模块，拥有自己独立的作用域、变量、以及方法等，对其他的模块都不可见。CommonJS规范规定，每个模块内部，module 变量代表当前模块。这个变量是一个对象，它的 exports 属性（module.exports）是对外的接口。加载某个模块，其实是加载该模块的 module.exports 属性。require 方法用于加载模块。

```js
//moudle-a.js
moudle.exports = {
    a: 1
};

//moudle-b.js
var ma = require('./moudle-a');
var b = ma.a + 2;
module.exports ={
    b: b
};
```

模块化规范给项目带来极大的好处，在业务复杂，模块众多的大型项目中，开发者都遵循相同的规则来开发各自的模块，他们通过规范来约束模块的定义，大家不需要太多的沟通或者大量的文档来说明自己的模块使用规则，成千上万的模块就这样生产，并能够容易的使用。它的意义不仅是让模块看起来很规范，在合作开发、社区中传播中也起到了重大的作用。

## ES6 模块

### CommonJS

- 对于基本数据类型，属于复制。即会被模块缓存。同时，在另一个模块可以对该模块输出的变量重新赋值。
- 对于复杂数据类型，属于浅拷贝。由于两个模块引用的对象指向同一个内存空间，因此对该模块的值做修改时会影响另一个模块。
- 当使用require命令加载某个模块时，就会运行整个模块的代码。
- 当使用require命令加载同一个模块时，不会再执行该模块，而是取到缓存之中的值。也就是说，CommonJS模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存。
- 循环加载时，属于加载时执行。即脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出。

### ES6模块

- ES6模块中的值属于【动态只读引用】。
- 对于只读来说，即不允许修改引入变量的值，import的变量是只读的，不论是基本数据类型还是复杂数据类型。当模块遇到import命令时，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。
- 对于动态来说，原始值发生变化，import加载的值也会发生变化。不论是基本数据类型还是复杂数据类型。
- 循环加载时，ES6模块是动态引用。只要两个模块之间存在某个引用，代码就能够执行。

es6 的模块化和 commonjs 的模块不同点

区别|commonjs|es6
--|--|--
加载原理|第一次加载模块就会执行整个模块, 再次用到时到缓存中读取|不缓存运行结果, 动态的到被加载的模块中取值
输出|值的拷贝(模块中的值不会改变已经加载的值)|值的引用(静态分析, 动态引用, 原来模块值改变会改变加载的值)
加载方式|运行时加载(加载整个模块)|编译时加载(只加载需要的接口)
this指向|当前模块|undefined
循环加载|只输出已经执行的部分，还未执行的部分不会输出|遇到加载命令 import 时不会去执行模块，而是生成一个动态的只读引用，等到真正用到时再去模块中读取，只要引用存在，代码就能执行

## UMD

UMD 叫做通用模块定义规范（Universal Module Definition）。也是随着大前端的趋势所诞生，它可以通过运行时或者编译时让同一个代码模块在使用 CommonJs、CMD 甚至是 AMD 的项目中运行。未来同一个 JavaScript 包运行在浏览器端、服务区端甚至是 APP 端都只需要遵守同一个写法就行了。

它没有自己专有的规范，是集结了 CommonJs、CMD、AMD 的规范于一身，我们看看它的具体实现：

```js
((root, factory) => {
    if (typeof define === 'function' && define.amd) {
        //AMD
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        //CommonJS
        var $ = requie('jquery');
        module.exports = factory($);
    } else {
        root.testModule = factory(root.jQuery);
    }
})(this, ($) => {
    // todo
});
```

不难发现，它在定义模块的时候回检测当前使用环境和模块的定义方式，将各种模块化定义方式转化为同样一种写法。它的出现也是前端技术发展的产物，前端在实现跨平台的道路上不断的前进，UMD 规范将浏览器端、服务器端甚至是 APP 端都大统一了，当然它或许不是未来最好的模块化方式，未来在 ES6+、TypeScript、Dart 这些拥有高级语法的语言回代替这些方案。
