# Rollup

## 概述(Overview)

Rollup 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码，例如 library 或应用程序。Rollup 对代码模块使用新的标准化格式，这些标准都包含在 JavaScript 的 ES6 版本中，而不是以前的特殊解决方案，如 CommonJS 和 AMD。ES6 模块可以使你自由、无缝地使用你最喜爱的 library 中那些最有用独立函数，而你的项目不必携带其他未使用的代码。ES6 模块最终还是要由浏览器原生实现，但当前 Rollup 可以使你提前体验。

## 快速入门指南(Quick start)

使用 `npm install --global rollup` 进行安装。Rollup 可以通过命令行接口(command line interface)配合可选配置文件(optional configuration file)来调用，或者可以通过 JavaScript API来调用。运行 rollup --help 可以查看可用的选项和参数。

> 查看 rollup-starter-lib 和 rollup-starter-app 中那些使用 Rollup 的示例类库与应用项目。

这些命令假设应用程序入口起点的名称为 main.js，并且你想要所有 import 的依赖(all imports)都编译到一个名为 bundle.js 的单个文件中。

对于浏览器：

```bash
# compile to a <script> containing a self-executing function ('iife')
$ rollup main.js --file bundle.js --format iife
```

对于 Node.js:

```bash
# compile to a CommonJS module ('cjs')
$ rollup main.js --file bundle.js --format cjs
```

对于浏览器和 Node.js:

```bash
# UMD format requires a bundle name
$ rollup main.js --file bundle.js --format umd --name "myBundle"
```

## 为什么(Why)

如果你将项目拆分成小的单独文件中，这样开发软件通常会很简单，因为这通常会消除无法预知的相互影响(remove unexpected interaction)，以及显著降低了所要解决的问题的复杂度(complexity of the problem)，并且可以在项目最初时，就简洁地编写小的项目（不一定是标准答案）。不幸的是，JavaScript 以往并没有将此功能作为语言的核心功能。

## Tree-shaking

除了使用 ES6 模块之外，Rollup 还静态分析代码中的 import，并将排除任何未实际使用的代码。这允许您架构于现有工具和模块之上，而不会增加额外的依赖或使项目的大小膨胀。

例如，在使用 CommonJS 时，必须导入(import)完整的工具(tool)或库(library)对象。

```js
// 使用 CommonJS 导入(import)完整的 utils 对象
var utils = require( 'utils' );
var query = 'Rollup';
// 使用 utils 对象的 ajax 方法
utils.ajax( 'https://api.example.com?search=' + query ).then( handleResponse );
```

但是在使用 ES6 模块时，无需导入整个 utils 对象，我们可以只导入(import)我们所需的 ajax 函数：

```js
// 使用 ES6 import 语句导入(import) ajax 函数
import { ajax } from 'utils';
var query = 'Rollup';
// 调用 ajax 函数
ajax( 'https://api.example.com?search=' + query ).then( handleResponse );
```

因为 Rollup 只引入最基本最精简代码，所以可以生成轻量、快速，以及低复杂度的 library 和应用程序。因为这种基于显式的 import 和 export 语句的方式，它远比「在编译后的输出代码中，简单地运行自动 minifier 检测未使用的变量」更有效。

## 兼容性(Compatibility)

### 导入 CommonJS(Importing CommonJS)

Rollup 可以通过插件导入已存在的 CommonJS 模块。

### 发布 ES6 模块(Publishing ES6 Modules)

为了确保你的 ES6 模块可以直接与「运行在 CommonJS（例如 Node.js 和 webpack）中的工具(tool)」使用，你可以使用 Rollup 编译为 UMD 或 CommonJS 格式，然后在 package.json 文件的 main 属性中指向当前编译的版本。如果你的 package.json 也具有 module 字段，像 Rollup 和 webpack 2 这样的 ES6 感知工具(ES6-aware tools)将会直接导入 ES6 模块版本。

## 命令行

我们一般在命令行中使用Rollup。你也可以提供一份配置文件（可要可不要）来简化命令行操作，同时还能启用Rollup的高级特性

### 配置文件(Configuration files)

Rollup的配置文件是可选的，但是使用配置文件的作用很强大，而且很方便，因此我们推荐你使用

配置文件是一个ES6模块，它对外暴露一个对象，这个对象包含了一些Rollup需要的一些选项。通常，我们把这个配置文件叫做rollup.config.js，它通常位于项目的根目录

仔细查阅这个包办大量选项的清单，你可以根据你自己的需要把它配置到你的配置文件中

```js
// rollup.config.js
export default {
  // 核心选项
  input,     // 必须
  external,
  plugins,

  // 额外选项
  onwarn,

  // danger zone
  acorn,
  context,
  moduleContext,
  legacy

  output: {  // 必须 (如果要输出多个，可以是一个数组)
    // 核心选项
    file,    // 必须
    format,  // 必须
    name,
    globals,

    // 额外选项
    paths,
    banner,
    footer,
    intro,
    outro,
    sourcemap,
    sourcemapFile,
    interop,

    // 高危选项
    exports,
    amd,
    indent
    strict
  },
};
```

你必须使用配置文件才能执行以下操作：

- 把一个项目打包，然后输出多个文件
- 使用Rollup插件, 例如 rollup-plugin-node-resolve 和 rollup-plugin-commonjs 。这两个插件可以让你加载Node.js里面的CommonJS模块

如果你想使用Rollup的配置文件，记得在命令行里加上--config或者-c

默认使用 `rollup.config.js`

```bash
# 默认使用rollup.config.js
$ rollup --config

# 或者, 使用自定义的配置文件，这里使用my.config.js作为配置文件
$ rollup --config my.config.js

```

### 命令行的参数(Command line flags)

配置文件中的许多选项和命令行的参数是等价的。如果你使用这里的参数，那么将重写配置文件。想了解更多的话，仔细查阅这个包办大量选项的清单

```
-i, --input <filename>      要打包的文件（必须）
-o, --file <output>         输出的文件 (如果没有这个参数，则直接输出到控制台)
-f, --format <format>       输出的文件类型 (amd, cjs, esm, iife, umd)
-e, --external <ids>        将模块ID的逗号分隔列表排除
-g, --globals <pairs>       以`module ID:Global` 键值对的形式，用逗号分隔开
                              任何定义在这里模块ID定义添加到外部依赖
-n, --name <name>           生成UMD模块的名字
-h, --help                  输出 help 信息
-m, --sourcemap             生成 sourcemap (`-m inline` for inline map)
--amd.id                    AMD模块的ID，默认是个匿名函数
--amd.define                使用Function来代替`define`
--no-strict                 在生成的包中省略`"use strict";`
--no-conflict               对于UMD模块来说，给全局变量生成一个无冲突的方法
--intro                     在打包好的文件的块的内部(wrapper内部)的最顶部插入一段内容
--outro                     在打包好的文件的块的内部(wrapper内部)的最底部插入一段内容
--banner                    在打包好的文件的块的外部(wrapper外部)的最顶部插入一段内容
--footer                    在打包好的文件的块的外部(wrapper外部)的最底部插入一段内容
--interop                   包含公共的模块（这个选项是默认添加的）
-h/--help                   打印帮助文档。
-v/--version                打印已安装的Rollup版本号。
-w/--watch                  监听源文件是否有改动，如果有改动，重新打包
--silent                    不要将警告打印到控制台。
```
