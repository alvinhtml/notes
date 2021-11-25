# webpack

## webpack 配置

### optimization

webpack目前最新的版本是5，但是早在4的时候，就可以通过选择不同的mode模式来优化打包的结果，但是我们仍然可以使用optimization来对打包结果进行优化（生产环境优化）

```js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const TerserPlugin = require("terser-webpack-plugin");

optimization: {
  minimizer: [
    new TerserPlugin(),
    new OptimizeCssAssetsWebpackPlugin()
  ],
  splitChunks: {
    // 自动提取所有公共模块到单独 bundle
    chunks: 'all'
  }
}
```

#### minimizer

minimizer 属性存放一个数组，数组里可以存放用于代码压缩的插件

- optimize-css-assets-webpack-plugin 这个插件用于压缩打包好的bundle文件的css代码，
- terser-webpack-plugin 这个插件用于压缩打包好的bundle文件的js代码

如果在webpack中设置了这个属性，那么webpack会认为对打包文件的处理是自定义的，所以如果只设置了对css 的压缩，那么js是不会进行压缩的

#### splitChunks

设置这个值会自动提取所有公共模块到单独bundle中去


## webpack plugin

### 初始化插件文件

新建 SetScriptTimestampPlugin.js  文件，并参考官方文档中插件的基本结构，初始化插件代码：

```js
// SetScriptTimestampPlugin.js

class SetScriptTimestampPlugin {
  apply(compiler) {
    compiler.hooks.done.tap('SetScriptTimestampPlugin', (compilation, callback) => {
      console.log('SetScriptTimestampPlugin!');
    });
  }
}

module.exports = SetScriptTimestampPlugin;
```

apply 方法为插件原型方法，接收 compiler 作为参数。

### 选择插件触发时机

选择插件触发时机，其实是选择插件触发的 compiler 钩子，即何时触发插件。

Webpack 提供钩子有很多，这里简单介绍几个，完整具体可参考文档[《Compiler Hooks》](https://webpack.js.org/api/compiler-hooks/)：

- entryOption : 在 webpack 选项中的 entry 配置项 处理过之后，执行插件。
- afterPlugins : 设置完初始插件之后，执行插件。
- compilation : 编译创建之后，生成文件之前，执行插件。。
- emit : 生成资源到 output 目录之前。
- done : 编译完成。

我们插件应该是要在 HTML 输出之前，动态添加 script 标签，所以我们选择钩入 compilation 阶段，代码修改：

```js
// SetScriptTimestampPlugin.js

class SetScriptTimestampPlugin {
  apply(compiler) {
    compiler.hooks.compilation.tap('SetScriptTimestampPlugin', (compilation, callback) => {
      console.log(this.options);
    });
  }
}
module.exports = SetScriptTimestampPlugin;
```

在 compiler.hooks 下指定事件钩子函数，便会触发钩子时，执行回调函数。

Webpack 提供三种触发钩子的方法：

- tap ：以同步方式触发钩子；
- tapAsync ：以异步方式触发钩子；
- tapPromise ：以异步方式触发钩子，返回 Promise；

tap 方法接收两个参数：插件名称和回调函数。回调函数有两个参数： compilation 和 callback 。

### 常用 Plugin

### CommonsChunkPlugin

CommonsChunkPlugin 主要是用来提取第三方库和公共模块，避免首屏加载的bundle文件或者按需加载的bundle文件体积过大，从而导致加载时间过长，着实是优化的一把利器。

### speed-measure-webpack5-plugin

进行分析打包速度

### friendly-errors-webpack-plugin

可以识别某类别的 webpack 错误，并清理，聚合和优先级，以提供更好的开发人员体验
