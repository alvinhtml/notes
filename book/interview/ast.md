# AST 抽象语法树

## AST 如何生成

在学习 AST 时，可以借助下面两个网站辅助查看代码转换成 AST 之后的结果。

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
