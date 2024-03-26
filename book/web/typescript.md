# TypeScript

TypeScript 是 JavaScript 的一个超集，主要提供可选的静态类型，类和接口。它由 Microsoft 开发，代码开源于 [GitHub](https://github.com/Microsoft/TypeScript) 上.

> TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript。编译出来的 JavaScript 可以运行在任何浏览器上。TypeScript 编译工具可以运行在任何服务器和任何系统上。TypeScript 是开源的。

TypeScript 是 JavaScript 的强类型版本。然后在编译期去掉类型和特有语法，生成纯粹的 JavaScript 代码。由于最终在浏览器中运行的仍然是 JavaScript，所以 TypeScript 并不依赖于浏览器的支持，也并不会带来兼容性问题。

## TypeScript 优势

### TypeScript 增加了代码的可读性和可维护性

- 类型系统实际上是最好的文档，大部分的函数看看类型的定义就可以知道如何使用了
- 可以在编译阶段就发现大部分错误，这总比在运行时候出错好
- 增强了编辑器和 IDE 的功能，包括代码补全、接口提示、跳转到定义、重构等

### TypeScript 非常包容-

- TypeScript 是 JavaScript 的超集，.js 文件可以直接重命名为 .ts 即可
- 即使不显式的定义类型，也能够自动做出类型推论
- 可以定义从简单到复杂的几乎一切类型
- 即使 TypeScript 编译报错，也可以生成 JavaScript 文件
- 兼容第三方库，即使第三方库不是用 TypeScript 写的，也可以编写单独的类型文件供 TypeScript 读取

### TypeScript 拥有活跃的社区

- 大部分第三方库都有提供给 TypeScript 的类型定义文件
- Google 开发的 Angular2 就是使用 TypeScript 编写的
- TypeScript 拥抱了 ES6 规范，也支持部分 ESNext 草案的规范

## TypeScript 的缺点

任何事物都是有两面性的，我认为 TypeScript 的弊端在于：

- 有一定的学习成本，需要理解接口（Interfaces）、泛型（Generics）、类（Classes）、枚举类型（Enums）等前端工程师可能不是很熟悉的概念
- 短期可能会增加一些开发成本，毕竟要多写一些类型的定义，不过对于一个需要长期维护的项目，TypeScript 能够减少其维护成本
- 集成到构建流程需要一些工作量
- 可能和一些库结合的不是很完美

## 静态类型的优缺点

静态类型的优点

- （1）有利于代码的静态分析。
- （2）有利于发现错误。
- （3）更好的 IDE 支持，做到语法提示和自动补全。
- （4）提供了代码文档。
- （5）有助于代码重构。

静态类型的缺点

- （1）丧失了动态类型的代码灵活性。
- （2）增加了编程工作量。
- （3）更高的学习成本。
- （4）引入了独立的编译步骤。
- （5）兼容性问题。

## 安装 TypeScript

以下命令会在全局环境下安装 `tsc` 命令，安装完成之后，我们就可以在任何地方执行 `tsc` 命令了。

```bash
npm install -g typescript
```

要编译一个 TypeScript 文件很简单：

```bash
tsc hello.ts
```

一般情况下使用 TypeScript 编写的文件以 .ts 为后缀，如果用 TypeScript 编写 React 时，以 .tsx 为后缀。

### 在 Webpack 中配置 TypeScript

## Hello TypeScript

编写 `hello.ts` ，键入如下内容：

```js
function sayHello(person: string) {
  return 'Hello, ' + person
}

let user = 'Tom'
console.log(sayHello(user))
```

然后执行

```bash
tsc hello.ts
```

这时候会生成一个编译好的文件 `hello.js`：

```js
function sayHello(person) {
  return 'Hello, ' + person
}
var user = 'Tom'
console.log(sayHello(user))
```

TypeScript 中，使用 `:` 指定变量的类型，`:` 的前后有没有空格都可以。上述例子中，我们用 `:` 指定 `person` 参数类型为 `string`。但是编译为 js 之后，检查类型的代码并没有被插入进来。

TypeScript 只会进行静态检查，如果发现有错误，编译的时候就会报错。但是即使报错了，还是会生成编译结果，我们仍然可以使用这个编译之后的文件。如果要在报错的时候终止 js 文件的生成，可以在 `tsconfig.json` 中配置 `noEmitOnError` 即可。

## 数据类型

### 概要

```
- 类型声明
  - TypeScript 代码最明显的特征，就是为 JavaScript 变量加上了类型声明。
- 类型推断
  - 类型声明并不是必需的，如果没有，TypeScript 会自己推断类型。
- 值与类型
  - “类型”是针对“值”的，可以视为是后者的一个元属性。每一个值在 TypeScript 里面都是有类型的。比如，3是一个值，它的类型是number。
- tsc 编译器
  - TypeScript 官方提供的编译器叫做 tsc，可以将 TypeScript 脚本编译成 JavaScript 脚本。
- any 类型
  - any 类型表示没有任何限制，该类型的变量可以赋予任意类型的值。
  - 如果无法推断出类型，TypeScript 就会认为该变量的类型是any
  - any类型除了关闭类型检查，还有一个很大的问题，就是它会“污染”其他变量。
- unknown 类型
  - 为了解决any类型“污染”其他变量的问题，TypeScript 3.0 引入了unknown类型。它与any含义相同，表示类型不确定，可能是任意类型，但是它的使用有一些限制，不像any那样自由，可以视为严格版的 any
  - unknown跟any的相似之处，在于所有类型的值都可以分配给unknown类型
  - unknown类型的变量，不能直接赋值给其他类型的变量（除了any类型和unknown类型）
  - 只有经过“类型缩小”，unknown类型变量才可以使用
  - unknown可以看作是更安全的any。一般来说，凡是需要设为any类型的地方，通常都应该优先考虑设为unknown类型。
- never 类型
  - 为了保持与集合论的对应关系，以及类型运算的完整性，TypeScript 还引入了“空类型”的概念，即该类型为空，不包含任何值。
  - 不可能返回值的函数，返回值的类型就可以写成never
  - never类型的一个重要特点是，可以赋值给任意其他类型。
- TypeScript 的基本类型
  - boolean
  - string
  - number
  - bigint
  - symbol
  - object
    - object 类型包含了所有对象、数组和函数
  - undefined
    - 既可以作为值，也可以作为类型
  - null
    - 既可以作为值，也可以作为类型
- TypeScript 对五种原始类型分别提供了大写和小写两种类型。
  - Boolean 和 boolean
  - String 和 string
  - Number 和 number
  - BigInt 和 bigint
  - Symbol 和 symbol
  - 其中，大写类型同时包含包装对象和字面量两种情况，小写类型只包含字面量，不包含包装对象。
- Object 类型
  - 大写的Object类型代表 JavaScript 语言里面的广义对象。所有可以转成对象的值，都是Object类型，这囊括了几乎所有的值。
  - 小写的object类型代表 JavaScript 里面的狭义对象，即可以用字面量表示的对象，只包含对象、数组和函数，不包括原始类型的值。
- 值类型
  - TypeScript 规定，单个值也是一种类型，称为“值类型”。
- 联合类型
  - 联合类型（union types）指的是多个类型组成的一个新类型，使用符号|表示。
  - 联合类型A|B表示，任何一个类型只要属于A或B，就属于联合类型A|B。
  - 联合类型的第一个成员前面，也可以加上竖杠|，这样便于多行书写 |A|B|C
- 交叉类型
  - 交叉类型A&B表示，任何一个类型必须同时属于A和B，才属于交叉类型A&B，即交叉类型同时满足A和B的特征。
- type 命令
  - type命令用来定义一个类型的别名。
- typeof 运算符
  - TypeScript 将 typeof 运算符移植到了类型运算，它的操作数依然是一个值，但是返回的不是字符串，而是该值的 TypeScript 类型。
```

### 五种原始数据类型

- boolean
- number
- string
- void
- undefined null

- 与 `void` `的区别是，undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量。

```js
// 这样不会报错
let num: number = undefined

// 这样也不会报错
let u: undefined
let num: number = u
```

### 任意值类 any

如果是一个普通类型，在赋值过程中改变类型是不被允许的，但如果是 any 类型，则允许被赋值为任意类型。变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型。

### 类型推论

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。

以下代码虽然没有指定类型，但是会在编译的时候报错：

```js
let myFavoriteNumber = 'seven'
myFavoriteNumber = 7

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

事实上，它等价于：

```js
let myFavoriteNumber: string = 'seven'
myFavoriteNumber = 7

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查。

### 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种。

简单的例子

```js
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
let myFavoriteNumber: string | number;
myFavoriteNumber = true;
​
// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

联合类型使用 `|` 分隔每个类型。

这里的 `let myFavoriteNumber: string | number` 的含义是，允许 `myFavoriteNumber` 的类型是 `string` 或者 `number`，但是不能是其他类型。

#### 访问联合类型的属性或方法

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法：

```js
function getLength(something: string | number): number {
  return something.length
}

// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

上例中，`length` 不是 `string` 和 `number` 的共有属性，所以会报错。

访问 `string` 和 `number` 的共有属性是没问题的：

```js
function getString(something: string | number): string {
  return something.toString()
}
```

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：

```js
let myFavoriteNumber: string | number
myFavoriteNumber = 'seven'
console.log(myFavoriteNumber.length) // 5
myFavoriteNumber = 7
console.log(myFavoriteNumber.length) // 编译时报错

// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

上例中，第二行的 `myFavoriteNumber` 被推断成了 `string`，访问它的 `length` 属性不会报错。
而第四行的 `myFavoriteNumber` 被推断成了 `number`，访问它的 `length` 属性时就报错了。

### 对象的类型 - 接口

在 TypeScript 中，我们使用接口（Interfaces）来定义对象的类型。

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。

TypeScript 中的接口是一个非常灵活的概念，除了可用于对类的一部分行为进行抽象以外，也常用于对「对象的形状（Shape）」进行描述。

简单的例子:

```js
interface Person {
  name: string;
  age: number;
}

let tom: Person = {
  name: 'Tom',
  age: 25,
}
```

上面的例子中，我们定义了一个接口 `Person`，接着定义了一个变量 `tom`，它的类型是 `Person`。这样，我们就约束了 `tom` 的形状必须和接口 `Person` 一致。

赋值的时候，定义的变量比接口少一些属性或是多一些属性都是不允许的，变量的形状必须和接口的形状保持一致。

#### 可选属性

有时我们希望不要完全匹配一个形状，那么可以用可选属性：

```js
interface Person {
  name: string;
  age?: number;
}

let tom: Person = {
  name: 'Tom',
}
```

#### 任意属性

有时候我们希望一个接口允许有任意的属性，可以使用如下方式：

```js
interface Person {
  name: string;
  age?: number;
  [propName: string]: any;
}

let tom: Person = {
  name: 'Tom',
  gender: 'male',
}
```

使用 `[propName: string]` 定义了任意属性取 `string` 类型的值。

需要注意的是，一旦定义了任意属性，那么 **确定属性和可选属性的类型都必须是它的类型的子集**。

### 数组的类型

在 TypeScript 中，数组类型有多种定义方式，比较灵活。

最简单的方法是使用「类型 + 方括号」来表示数组：

```js
let fibonacci: number[] = [1, 1, 2, 3, 5]
```

数组的项中不允许出现其他的类型：

```js
let fibonacci: number[] = [1, '1', 2, 3, 5];
​
// Type 'string' is not assignable to type 'number'.
```

数组的一些方法的参数也会根据数组在定义时约定的类型进行限制：

```js
let fibonacci: number[] = [1, 1, 2, 3, 5];
fibonacci.push('8');
​
// Argument of type '"8"' is not assignable to parameter of type 'number'.
```

上例中，push 方法只允许传入 number 类型的参数，但是却传了一个 "8" 类型的参数，所以报错了。这里 "8" 是一个字符串字面量类型，会在后续章节中详细介绍。

#### 数组泛型

我们也可以使用数组泛型（Array Generic） Array<elemType> 来表示数组：

```js
let fibonacci: Array<number> = [1, 1, 2, 3, 5]
```

#### 用接口表示数组

接口也可以用来描述数组：

```js
interface NumberArray {
  [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5]
```

`NumberArray` 表示：只要索引的类型是数字时，那么值的类型必须是数字。

虽然接口也可以用来描述数组，但是我们一般不会这么做，因为这种方式比前两种方式复杂多了。不过有一种情况例外，那就是它常用来表示类数组。

#### 类数组

类数组（Array-like Object）不是数组类型，比如 arguments：

```
function sum() {
    let args: number[] = arguments;
}

// Type 'IArguments' is missing the following properties from type 'number[]': pop, push, concat, join, and 24 more.
```

上例中，arguments 实际上是一个类数组，不能用普通的数组的方式来描述，而应该用接口：

```js
function sum() {
  let args: {
    [index: number]: number,
    length: number,
    callee: Function,
  } = arguments
}
```

在这个例子中，我们除了约束当索引的类型是数字时，值的类型必须是数字之外，也约束了它还有 length 和 callee 两个属性。

事实上常用的类数组都有自己的接口定义，如 IArguments, NodeList, HTMLCollection 等。

#### any 在数组中的应用

一个比较常见的做法是，用 any 表示数组中允许出现任意类型：

```js
let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }]
```

### 函数的类型

​ 函数是 JavaScript 中的一等公民 ​

#### 函数声明

在 JavaScript 中，有两种常见的定义函数的方式——函数声明（Function Declaration）和函数表达式（Function Expression）：

```js
// 函数声明（Function Declaration）
function sum(x, y) {
    return x + y;
}
​
// 函数表达式（Function Expression）
let mySum = function (x, y) {
    return x + y;
};
```

一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到，其中函数声明的类型定义较简单：

```js
function sum(x: number, y: number): number {
  return x + y
}
```

注意，**输入多余的（或者少于要求的）参数，是不被允许的**

#### 函数表达式

如果要我们现在写一个对函数表达式（Function Expression）的定义，可能会写成这样：

```js
let mySum = function (x: number, y: number): number {
  return x + y
}
```

这是可以通过编译的，不过事实上，上面的代码只对等号右侧的匿名函数进行了类型定义，而等号左边的 `mySum`，是通过赋值操作进行类型推论而推断出来的。如果需要我们手动给 `mySum` 添加类型，则应该是这样：

```js
let mySum: (x: number, y: number) => number = function (
  x: number,
  y: number
): number {
  return x + y
}
```

注意不要混淆了 TypeScript 中的 `=>` 和 ES6 中的 `=>`。

在 TypeScript 的类型定义中，`=>` 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。在 ES6 中，`=>` 叫做箭头函数，应用十分广泛，可以参考 ES6 中的箭头函数。

#### 用接口定义函数的形状

我们也可以使用接口的方式来定义一个函数需要符合的形状：

```js
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc
mySearch = function (source: string, subString: string) {
  return source.search(subString) !== -1
}
```

#### 可选参数

前面提到，输入多余的（或者少于要求的）参数，是不允许的。那么如何定义可选的参数呢？

与接口中的可选属性类似，我们用 ? 表示可选的参数：

```js
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + ' ' + lastName
  } else {
    return firstName
  }
}
let tomcat = buildName('Tom', 'Cat')
let tom = buildName('Tom')
```

需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必需参数了**。

#### 参数默认值

在 ES6 中，我们允许给函数的参数添加默认值，TypeScript 会将添加了默认值的参数识别为可选参数：

```js
function buildName(firstName: string, lastName: string = 'Cat') {
  return firstName + ' ' + lastName
}
let tomcat = buildName('Tom', 'Cat')
let tom = buildName('Tom')
```

此时就不受「可选参数必须接在必需参数后面」的限制了。

#### 剩余参数

ES6 中，可以使用 ...rest 的方式获取函数中的剩余参数（rest 参数）：

```js
function push(array, ...items) {
  items.forEach(function (item) {
    array.push(item)
  })
}

let a = []
push(a, 1, 2, 3)
```

事实上，items 是一个数组。所以我们可以用数组的类型来定义它：

```js
function push(array: any[], ...items: any[]) {
  items.forEach(function (item) {
    array.push(item)
  })
}

let a = []
push(a, 1, 2, 3)
```

注意，rest 参数只能是最后一个参数，关于 rest 参数，可以参考 ES6 中的 rest 参数。

#### 重载

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

比如，我们需要实现一个函数 reverse，输入数字 123 的时候，输出反转的数字 321，输入字符串 'hello' 的时候，输出反转的字符串 'olleh'。

利用联合类型，我们可以这么实现：

```js
function reverse(x: number | string): number | string {
  if (typeof x === 'number') {
    return Number(x.toString().split('').reverse().join(''))
  } else if (typeof x === 'string') {
    return x.split('').reverse().join('')
  }
}
```

然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。

这时，我们可以使用重载定义多个 reverse 的函数类型：

```js
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

上例中，我们重复定义了多次函数 reverse，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。

注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

## 类型断言

类型断言（Type Assertion）可以用来手动指定一个值的类型。

语法

```
<类型>值
```

或

```
值 as 类型
```

在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种。

### 将一个联合类型的变量指定为一个更加具体的类型

之前提到过，当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法：

```js
function getLength(something: string | number): number {
  return something.length
}

// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

而有时候，我们确实需要在还不确定类型的时候就访问其中一个类型的属性或方法，比如：

```js
function getLength(something: string | number): number {
  if (something.length) {
    return something.length
  } else {
    return something.toString().length
  }
}

// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
// index.ts(3,26): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

上例中，获取 `something.length` 的时候会报错。

此时可以使用类型断言，将 `something` 断言成 `string`：

```js
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```

类型断言的用法如上，在需要断言的变量前加上 `<Type>` 即可。

**类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的：**

```js
function toBoolean(something: string | number): boolean {
    return <boolean>something;
}

// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.
```

## 声明文件

当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

### 新语法索引

由于本章涉及大量新语法，故在本章开头列出新语法的索引，方便大家在使用这些新语法时能快速查找到对应的讲解：

- `declare var` 声明全局变量
- `declare function` 声明全局方法
- `declare class` 声明全局类
- `declare enum` 声明全局枚举类型
- `declare namespace` 声明（含有子属性的）全局对象
- `interface` 和 `type` 声明全局类型
- `export` 导出变量
- `export namespace` 导出（含有子属性的）对象
- `export default` ES6 默认导出
- `export =` commonjs 导出模块
- `export as namespace` UMD 库声明全局变量
- `declare global` 扩展全局变量
- `declare module` 扩展模块
- `/// <reference />` 三斜线指令

### 什么是声明语句

假如我们想使用第三方库 jQuery，一种常见的方式是在 html 中通过 <script> 标签引入 jQuery，然后就可以使用全局变量 $ 或 jQuery 了。

我们通常这样获取一个 id 是 foo 的元素：

```js
$('#foo')
// or
jQuery('#foo')
```

但是在 ts 中，编译器并不知道 `$` 或 `jQuery` 是什么东西 1：

```js
jQuery('#foo')
// ERROR: Cannot find name 'jQuery'.
```

这时，我们需要使用 `declare var` 来定义它的类型 2：

```js
declare var jQuery: (selector: string) => any

jQuery('#foo')
```

上例中，`declare var` 并没有真的定义一个变量，只是定义了全局变量 jQuery 的类型，仅仅会用于编译时的检查，在编译结果中会被删除。它编译结果是：

### 什么是声明文件

通常我们会把声明语句放到一个单独的文件（jQuery.d.ts）中，这就是声明文件：

```js
// src/jQuery.d.ts

declare var jQuery: (selector: string) => any
```

```js
// src/index.ts

jQuery('#foo')
```

声明文件必需以 `.d.ts` 为后缀。

一般来说，ts 会解析项目中所有的 `*.ts` 文件，当然也包含以 `.d.ts` 结尾的文件。所以当我们将 `jQuery.d.ts` 放到项目中时，其他所有 `*.ts` 文件就都可以获得 jQuery 的类型定义了。

```
/path/to/project
├── src
|  ├── index.ts
|  └── jQuery.d.ts
└── tsconfig.json
```

假如仍然无法解析，那么可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `jQuery.d.ts` 文件。

### 第三方声明文件

当然，jQuery 的声明文件不需要我们定义了，社区已经帮我们定义好了：jQuery in DefinitelyTyped。

我们可以直接下载下来使用，但是更推荐的是使用 `@types` 统一管理第三方库的声明文件。

`@types` 的使用方式很简单，直接用 npm 安装对应的声明模块即可，以 jQuery 举例：

```bash
npm install @types/jquery --save-dev
```

## 内置对象

JavaScript 中有很多内置对象，它们可以直接在 TypeScript 中当做定义好了的类型。

内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

### ECMAScript 的内置对象

ECMAScript 标准提供的内置对象有：`Boolean`、`Error`、`Date`、`RegExp` 等。

我们可以在 TypeScript 中将变量定义为这些类型：

```js
let b: Boolean = new Boolean(1)
let e: Error = new Error('Error occurred')
let d: Date = new Date()
let r: RegExp = /[a-z]/
```

更多的内置对象，可以查看 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects) 的文档。

而他们的定义文件，则在 [TypeScript 核心库](https://github.com/Microsoft/TypeScript/tree/master/src/lib)的定义文件中。

### DOM 和 BOM 的内置对象

DOM 和 BOM 提供的内置对象有：`Document`、`HTMLElement`、`Event`、`NodeList` 等。

TypeScript 中会经常用到这些类型：

```js
let body: HTMLElement = document.body
let allDiv: NodeList = document.querySelectorAll('div')

document.addEventListener('click', function (e: MouseEvent) {
  // Do something
})
```

### 用 TypeScript 写 Node.js

Node.js 不是内置对象的一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件：

```bash
npm install @types/node --save-dev
```

## 类型别名

类型别名用来给一个类型起个新名字。

简单的例子

```js
type Name = string
type NameResolver = () => string
type NameOrResolver = Name | NameResolver
function getName(n: NameOrResolver): Name {
  if (typeof n === 'string') {
    return n
  } else {
    return n()
  }
}
```

上例中，我们使用 `type` 创建类型别名。

类型别名常用于联合类型。

## 字符串字面量类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个。

简单的例子

```js
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}
​
handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 不能为 'dbclick'
​
// index.ts(7,47): error TS2345: Argument of type '"dbclick"' is not assignable to parameter of type 'EventNames'.
```

上例中，我们使用 `type` 定了一个字符串字面量类型 `EventNames`，它只能取三种字符串中的一种。

注意，类型别名与字符串字面量类型都是使用 type 进行定义。

## 元组

数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

元组起源于函数编程语言（如 F#），这些语言中会频繁使用元组。

简单的例子

定义一对值分别为 string 和 number 的元组：

```js
let tom: [string, number] = ['Tom', 25]
```

当赋值或访问一个已知索引的元素时，会得到正确的类型：

```js
let tom: [string, number];
tom[0] = 'Tom';
tom[1] = 25;
​
tom[0].slice(1);
tom[1].toFixed(2);
```

也可以只赋值其中一项：

```js
let tom: [string, number]
tom[0] = 'Tom'
```

但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。

```js
let tom: [string, number];
tom = ['Tom', 25];
let tom: [string, number];
tom = ['Tom'];
​
// Property '1' is missing in type '[string]' but required in type '[string, number]'.
```

越界的元素
当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：

```js
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true);
​
// Argument of type 'true' is not assignable to parameter of type 'string | number'.
```
