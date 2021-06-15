# npm 命令

## 更新与查看版本信息

```bash
npm install npm@latest -g

# 查看 npm 命令列表
$ npm help

# 查看各个命令的简单用法
$ npm -l

# 查看 npm 的版本
$ npm -v

# 查看 npm 的配置
$ npm config list -l

# 查看全局安装路径
$ npm prefix -g

# 查看全局安装过的模块
npm list -g
npm list -g --depth 0

# 如果不添加--depth 0 会将更详细的列表展示出来。
```

## npm init

`npm init` 用来初始化生成一个新的 `package.json` 文件。它会向用户提问一系列问题，如果你觉得不用修改默认配置，一路回车就可以了。

如果使用了 `-f`（代表force）、`-y`（代表yes），则跳过提问阶段，直接生成一个新的 `package.json` 文件。

```bash
npm init

# 则跳过提问阶段
npm init -f -y
```

## 设置环境变量
`npm set` 用来设置环境变量。

```bash
$ npm set init-author-name 'Your name'
$ npm set init-author-email 'Your email'
$ npm set init-author-url 'http://yourdomain.com'
$ npm set init-license 'MIT'
```

上面命令等于为 `npm init` 设置了默认值，以后执行 `npm init` 的时候，`package.json` 的作者姓名、邮件、主页、许可证字段就会自动写入预设的值。这些信息会存放在用户主目录的 `~/.npmrc` 文件，使得用户不用每个项目都输入。如果某个项目有不同的设置，可以针对该项目运行 `npm config`。

## npm info

`npm info` 命令可以查看每个模块的具体信息。比如，查看 `react` 模块的信息。

```bash
npm info react
```

## npm ls

`npm ls` 命令可以查看本地包的版本。

```bash
npm ls jquery
```

## npm search
`npm search` 命令用于搜索npm仓库，它后面可以跟字符串，也可以跟正则表达式。
```bash
$ npm search <搜索词>

#下面是一个例子。
$ npm search node-gyp
// NAME                  DESCRIPTION
// autogypi              Autogypi handles dependencies for node-gyp projects.
// grunt-node-gyp        Run node-gyp commands from Grunt.
// gyp-io                Temporary solution to let node-gyp run `rebuild` under…
// ...
```

## npm list
`npm list` 命令以树型结构列出当前项目安装的所有模块，以及它们依赖的模块。
```bash
$ npm list

# 加上 global 参数，会列出全局安装的模块。
$ npm list -global

# npm list 命令也可以列出单个模块。
$ npm list underscore
```

## npm install

每个模块可以“全局安装”，也可以“本地安装”。“全局安装”指的是将一个模块安装到系统目录中，各个项目都可以调用。一般来说，全局安装只适用于工具模块，比如 `eslint` 和 `gulp`。“本地安装”指的是将一个模块下载到当前项目的 `node_modules` 子目录，然后只有在项目目录之中，才能调用这个模块。

```bash
# 本地安装
$ npm install <package name>

# 全局安装
$ sudo npm install -global <package name>
$ sudo npm install -g <package name>

# npm install也支持直接输入Github代码库地址。
$ npm install git://github.com/package/path.git
$ npm install git://github.com/package/path.git#0.1.0
```

安装之前，npm install会先检查，node_modules目录之中是否已经存在指定模块。如果存在，就不再重新安装了，即使远程仓库已经有了一个新版本，也是如此。

如果你希望，一个模块不管是否安装过，npm 都要强制重新安装，可以使用-f或--force参数。
```bash
$ npm install <packageName> --force
```

## npm update，npm uninstall
npm update命令可以更新本地安装的模块。

```bash
# 升级当前项目的指定模块
$ npm update [package name]

# 升级全局安装的模块
$ npm update -global [package name]
```
它会先到远程仓库查询最新版本，然后查询本地版本。如果本地版本不存在，或者远程版本较新，就会安装。

使用 `-S` 或 `--save` 参数，可以在安装的时候更新 `package.json` 里面模块的版本号。

npm uninstall命令，卸载已安装的模块。

```bash
$ npm uninstall [package name]

# 卸载全局模块
$ npm uninstall [package name] -global
```


## npm run
`npm` 不仅可以用于模块管理，还可以用于执行脚本。`package.json` 文件有一个 scripts 字段，可以用于指定脚本命令，供 `npm` 直接调用。

```json
{
  "scripts": {
    "lint": "jshint **.js",
    "test": "mocha test/"
  }
}
```

上面代码中，scripts 字段指定了两项命令 lint 和 test。命令行输入 `npm run-script lint` 或者 `npm run lint`，就会执行 `jshint **.js`。`npm run` 是 `npm run-script` 的缩写。

`npm` 内置了两个命令简写，`npm test` 等同于执行 `npm run test`，`npm start` 等同于执行 `npm run start`。

`npm run` 会创建一个 `Shell`，执行指定的命令，并临时将 `node_modules/.bin` 加入 `PATH` 变量，这意味着本地模块可以直接运行。


如果希望一个操作的输出，是另一个操作的输入，可以借用 Linux 系统的管道命令，将两个操作连在一起。
```bash
"build-js": "browserify browser/main.js | uglifyjs -mc > static/bundle.js"
```

但是，更方便的写法是引用其他 npm run命令。
```bash
"build": "npm run build-js && npm run build-css"
```
写在 scripts 属性中的命令，也可以在 node_modules/.bin 目录中直接写成bash脚本。下面是一个bash脚本。
```bash
"build-js": "bin/build.sh"
```
## start
如果 start 脚本没有配置，`npm start` 命令默认执行下面的脚本，前提是模块的根目录存在一个 `server.js` 文件。
```bash
$ node server.js
```

## pre- 和 post- 脚本

## 内部变量

## npm link

## 注册和登录

```bash
# 注册一个用户
npm adduser

# 登录 npm
npm login

# 验证登录是否成功
npm who am i
```

## 发布 npm 包
```bash
npm publish
```

## 版本更新
```bash
# 生成一个新的版本号
npm version <update_type> -m "<message>"

# 提交更新的版本
npm publish
```
其中 update_type 有三种：

参数|描述
-|-
patch|增加一位补丁号（比如 1.1.1 -> 1.1.2）
minor|增加一位小版本号（比如 1.1.1 -> 1.2.0）
major|增加一位大版本号（比如 1.1.1 -> 2.0.0）

### 舍弃某个版本的模块
```bash
npm deprecate my-thing@"< 1.0.2" "critical bug fixed in v1.0.2"
```
### 撤销自己发布的版本
```bash
npm --force unpublish [package name]
```



## npm 依赖包版本

版本的格式

> major.minor.patch

主版本号.次版本号.修补版本号

- patch：修复bug，兼容老版本

- minor：新增功能，兼容老版本

- major：新的架构调整，不兼容老版本


版本|描述
-|-
version|必须匹配某个版本
&#62;version|必须大于某个版本
&#62;=version|可大于或等于某个版本
&#60;version|必须小于某个版本
<=version|可以小于或等于某个版本
~version|大概匹配某个版本
^version|兼容某个版本，^1.1.2 表示>=1.1.2 <2.0.0
x-range|x 的位置表示任意版本 1.2.x
\*-range| 任意版本，""也表示任意版本
version1 - version2|大于等于version1，小于等于version2
range1 &#124;&#124; range2|满足 range1 或者满足 range2，可以多个范围


## 参考
[npm 模块管理器](http://javascript.ruanyifeng.com/nodejs/npm.html#toc19)
