# Babel

## babel 的作用

Babel 是一个工具链，主要用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。

## babel 转译的三个阶段

1. **parse** 源码解析生成 AST `@babel/parser`
2. **transform** 遍历 AST 并进行各种增删改 `@babel/travserse` `@babel/types` `@babel/template`
3. **generate** 转换完 AST 之后再打印成目标代码字符串 `@babel/generator`

## AST 如何生成

在学习 AST 和 babel 转换时，可以借助下面两个网站辅助查看代码转换成 AST 之后的结果。

https://esprima.org/demo/parse.html
https://astexplorer.net/

整个解析过程主要分为以下两个步骤：**词法分析**和**语法分析**

### 词法分析

词法分析，这一步主要是将字符流(char stream)转换为令牌流(token stream)，又称为分词。其中拆分出来的各个部分又被称为 词法单元 (Token)。

可以这么理解，词法分析就是把你的代码从 string 类型转换成了数组，数组的元素就是代码里的单词(词法单元)， 并且标记了每个单词的类型。

比如：

```js
const a = 1;
```

生成的 tokenList

```js
[
  { type: "Keyword", value: "const" },
  { type: "Identifier", value: "a" },
  { type: "Punctuator", value: "=" },
  { type: "Numeric", value: "1" },
  { type: "Punctuator", value: ";" },
];
```

词法分析结果，缺少一些比较关键的信息：需要进一步进行 语法分析。

### 语法分析

语法分析会将词法分析出来的 词法单元 转化成有语法含义的 抽象语法树结构(AST)，同时，验证语法，语法如果有错，会抛出语法错误。
这里截取 AST 树上的 program 的 body 部分(采用 `@babel/parser` 进行转化)

```json
{
  "body": [
    {
      "type": "VariableDeclaration",
      "start": 0,
      "end": 11,
      "loc": {},
      "declarations": [
        {
          "type": "VariableDeclarator",
          "start": 6,
          "end": 11,
          "loc": {},
          "id": {
            "type": "Identifier",
            "start": 6,
            "end": 7,
            "loc": {},
            "name": "a"
          },
          "init": {
            "type": "NumericLiteral",
            "start": 10,
            "end": 11,
            "loc": {},
            "extra": {},
            "value": 1
          }
        }
      ],
      "kind": "const"
    }
  ]
}
```

可以看到，经过语法分析阶段转换后生成的 AST，通过树形的层属关系建立了语法单元之间的联系。

## AST 节点

转换后的 AST 是由很多 AST 节点组成，主要有以下几种类型：

- 字面量（Literal）
- 标志符（Identifer）
- 语句（statement）
- 声明（Declaration）
- 表达式（Expression）
- 注释（Comment）
- 程序（Program）
- 文件（File）

每种 AST 节点都有自己的属性，但是它们也有一些公共属性：

- 结点类型（type）：AST 节点的类型。
- 位置信息（loc）：包括三个属性 start、 end、 loc。其中 start 和 end 代表该节点对应的源码字符串的开始和结束下标，不区分行列。loc 属性是一个对象，有 line 和 column 属性分别记录开始和结束行列号。
- 注释（comments）：主要分为三种 leadingComments、innerComments、trailingComments ，分别表示开始的注释、中间的注释、结尾的注释。

## babel 常用的 api

babel 中有五个常用的 api：

1. 针对 parse 阶段有@babel/parser，功能是把源码转成 AST
2. 针对 transform 阶段有 @babel/traverse，用于增删改查 AST
3. 针对 generate 阶段有@babel/generate，会把 AST 打印为目标代码字符串，同时生成 sourcemap
4. 在 transform 阶段，当需要判断和生成结点时，需要@babel/types，
5. 当需要批量创建 AST 的时候可以使用 @babel/template 来简化 AST 创建逻辑。

我们可以通过这些常用的 api 来自己实现一个 plugin，对代码进行转换。接下来就介绍一下几个常见的 api。

### @babel/parser

```
babelParser.parse(code, [options])--- 返回的 AST 根节点是 File 节点
babelParser.parseExpression(code, [options])---返回的 AST 根结点是 Expression
```

第一个参数是源代码，第二个参数是 options，其中最常用的就是 plugins、sourceType 这两个：

- sourceType: 指示分析代码的模式，主要有三个值：script、module、unambiguous。
- plugins：指定要使用插件数组。

### @babel/traverse

```
function traverse(ast, opts)
ast：经过 parse 之后的生成的 ast
opts ：指定 visitor 函数--用于遍历节点时调用（核心）
```

方法的第二参数中的 visitor 是我们自定义插件时经常用到的地方，你可以通过两种方式来定义这个参数

第一种是以方法的形式声明 visitor

```js
traverse(ast, {
  BlockStatement(path, state) {
    console.log("BlockStatement>>>>>>");
  },
});
```

第二种是以对象的形式声明 visitor

```js
traverse(ast, {
  BlockStatement: {
    enter(path, state) {
      console.log("enter>>>", path, state);
    },
    exit(path, state) {
      console.log("exit>>>", path, state);
    },
  },
});
```

每一个 visitor 函数会接收两个参数 path 和 state，path 用来操作节点、遍历节点和判断节点，而 state 则是遍历过程中在不同节点之间传递数据的机制， 我们也可以通过 state 存储一些遍历过程中的共享数据。

### @babel/generator

转换完 AST 之后，就要打印目标代码字符串，这里通过@babel/generator 来实现，其方法常用的参数有两个：

- 要打印的 AST
- options--指定打印的一些细节，比如 comments 指定是否包含注释

## babel 的内置功能

上面我们介绍了几个用于实现插件的 api，而 babel 本身为了实现对语法特性的转换以及对 api 的支持(polyfill)，也内置了很多的插件(plugin)和预设(preset)。
其插件主要分为三类：

- syntax plugin：只是在 parse 阶段使用，可以让 parser 能够正确的解析对应的语法成 AST
- transform plugin：是对 AST 的转换，针对 es20xx 中的语言特性、typescript、jsx 等的转换都是在这部分实现的
- proposal plugin：未加入语言标准的特性的 AST 转换插件

那么预设是什么呢？预设其实就是对于插件的一层封装，通过配置预设，使用者可以不用关心具体引用了什么插件，从而减轻使用者的负担。
而根据上面不同类型的插件又产生了如下几种预设：

1. 专门根据 es 标准处理语言特性的预设 -- babel-preset-es20xx
2. 对其 react、ts 兼容的预设 -- preset-react preset-typescript

我们目前最常使用的便是 @babel/preset-env 这个预设，下文将会通过一个例子来介绍它的使用。

## 自定义插件

### 需求

如果有一行代码

```js
const a = 1;
```

我需要通过 babel 自定义插件来给标识符增加类型定义，让它成为符合 ts 规范的语句，结果：`const a: number = 1`。

### 实现

通过 babel 处理代码，其实就是在对 AST 节点进行处理。

我们先搭起一个架子

```js
// 源代码
const sourceCode = `const a = 1`;
// 调用 parse，生成 ast
const ast = parser.parse(sourceCode, {});

// 调用 traverse 执行自定义的逻辑，处理 ast 节点
traverse(ast, {});

// 生成目标代码
const { code } = generate(ast, {});

console.log("result after deal with》〉》〉》", code);
```

在引入对应的包后，我们的架子主要分为三部分，我们首先需要知道这句话转换完之后的 AST 节点类型

```json
{
  "sourceType": "module",
  "interpreter": null,
  "body": [
    {
      "type": "VariableDeclaration",
      "start": 0,
      "end": 11,
      "loc": {},
      "declarations": [
        {
          "type": "VariableDeclarator",
          "start": 6,
          "end": 11,
          "loc": {},
          "id": {},
          "init": {}
        }
      ],
      "kind": "const"
    }
  ]
}
```

上图可以看出这句话的类型是 VariableDeclaration，所以我们要写一个可以遍历 VariableDeclaration 节点的 visitor。

```js
// 调用 traverse 执行自定义的逻辑，处理 ast 节点
traverse(ast, {
  VariableDeclaration(path, state) {
    console.log("VariableDeclaration>>>>>>", path.node.type);
  },
});
```

继续观察结构，该节点下面有 declarations 属性，其包括所有的声明，declarations[0]就是我们想要的节点。

```js
traverse(ast, {
  VariableDeclaration(path, state) {
    console.log("VariableDeclaration>>>>>>", path.node.type);
    const tarNode = path.node.declarations[0];
    console.log("tarNode>>>>>>", tarNode);
  },
});
```

每一个声明节点类型为 VariableDeclarator，该节点下有两个重要的节点，id（变量名的标识符）和 init（变量的值）。这里我们需要找到变量名为 a 的标识符，且他的值类型为 number（对应的节点类型为 NumericLiteral）。

```json
{
  "declarations": [
    {
      "type": "VariableDeclarator",
      "start": 6,
      "end": 11,
      "loc": {...},
      "id": {
      "type": "Identifier",
      "start": 6,
      "end": 7,
      "loc": {...},
      "name": "a"
      },
      "init": {
      "type": "NumericLiteral",
      "start": 10,
      "end": 11,
      "loc": {...},
      "extra": {...},
      "value": 1
      }
    }
  ]
}
```

这时候就需要我们使用一个新的包 @babel/types 来判断类型。判断类型时只需调用该包中对应的判断方法即可，方法名都是以 isXxx 或者 assertXxx 来命名的(Xxx 代表节点类型)，需要传入对应的节点才能判断该节点的类型。

```js
traverse(ast, {
  VariableDeclaration(path, state) {
    const tarNode = path.node.declarations[0];
    if (
      types.isIdentifier(tarNode.id) &&
      types.isNumericLiteral(tarNode.init)
    ) {
      console.log("inside>>>>>>");
    }
  },
});
```

锁定了节点后，我们需要更改 id 节点的 name 内容, 就可以实现需求了。

```js
traverse(ast, {
  VariableDeclaration(path, state) {
    const tarNode = path.node.declarations[0];
    if (
      types.isIdentifier(tarNode.id) &&
      types.isNumericLiteral(tarNode.init)
    ) {
      console.log("inside>>>>>>");
      tarNode.id.name = `${tarNode.id.name}: number`;
    }
  },
});
```
