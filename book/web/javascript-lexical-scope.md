# Javascript 词法作用域

词法作用域（lexical scope） 的概念不好理解，也很难掌握。但如果我们可以搞清楚概念里每一个词的意思，对于理解词法作用域将大有帮助。

因此本文将分别解释“词法（lexical）”和“作用域（scope）”这两个概念，然后再解释什么是词法作用域。

## 什么是作用域

**作用域** 表示一个区间，在这个区间内声明的所有内容（比如方法或变量）都可以被该区间内的代码访问到。

注意：

- **作用域（Scope）** 指一个范围、区域或空间
- **全局作用域（Global Scope）** 指全局空间或一个公共空间
- **局部作用域（Local Scope）** 指一个局部空间或一个受限制的空间

## 什么是作用域链

作用域链是一个独特空间。当一个变量被调用，那么变量在 被调用 时所在的局部作用域和全局作用域之间，就形成了一个作用域链。

示例

```js
// 定义一个全局作用域变量：
const fullName = 'Oluwatobi Sofela'

// 定义多层嵌套函数:
function profile() {
  function sayName() {
    function writeName() {
      return fullName
    }
    return writeName()
  }
  return sayName()
}

console.log(profile()) // 'Oluwatobi Sofela'
```

在上述示例中，`fullName` 变量在 `writeName()` 函数作用域中被调用。因此，从变量的执行作用域到全局作用域之间存在如下作用域链：

writeName() scope ---> sayName() scope ---> profile() scope ---> global scope

注意： 在 JavaScript 作用域链中，全局作用域是整个作用域链的终点。

## 作用域链是如何工作的

JavaScript 的作用域链规定了编译器在查找 被调用变量 的词法作用域时所遵循的查找规则。

考虑如下示例代码：

```js
// 定义一个全局作用域变量：
const fullName = 'Oluwatobi Sofela'

// 定义多层嵌套函数：
function profile() {
  function sayName() {
    function writeName() {
      return fullName
    }
    return writeName()
  }
  return sayName()
}

console.log(profile()) // 'Oluwatobi Sofela'
```

在上述示例中，无论何时调用 `profile()` 函数，编译器都会首先调用 `sayName()` 函数（`profile()` 函数体内只有一个 `sayName()`函数）。

然后，编译器会调用 `writeName()` 函数（`sayName()` 函数体内只有一个 `writeName()` 函数）。

最终，`writeName()` 函数会调用并返回 `fullName` 变量的值，所以编译器将返回 `fullName` 的值。但编译器不会直接从全局作用域中调用 `fullName`。

相反的，编译器必须通过作用域链一步一步的查找 `fullName` 的词法作用域。

下面是编译器查找 fullName 词法作用域的步骤：

首先，编译器会检查 writeName()的函数作用域是否定义了 fullName 变量。未找到，继续向上查找下一个作用域，即 sayName()作用域。
第二步，编译器在 sayName()的函数作用域中查找，依然没找到，继续向上查找下一个作用域，即 profile()作用域。
第三步，编译器在 profile()的函数作用域中查找，依然没找到，继续向上查找下一个作用域，即全局作用域。
第四步，编译器查找到了全局作用域。幸运的是，在全局作用域下找到了 fullName 变量，然后将其值（"Oluwatobi Sofela"）作为返回值返回。

特别强调 3 点：

假如编译器没有在任何作用域中找到 `fullName`，那么编译器将返回一个引用错误 `Uncaught ReferenceError: fullName is not defined`。

全局作用域是 JavaScript 作用域链的最后一个作用域，即全局作用域是查找的终点。

一个内部作用域（子作用域）可以访问它的外部作用域（父级作用域），但是外部作用域不能访问它的子作用域。

## 什么是词法（Lexical）

词法（Lexical） 指的是定义某个事物。

任何创建文字、表达式或变量的声明都叫词法。

比如，拼字游戏就是一种词法活动，因为这个游戏在创造文字。语言学家的工作也是一种词法事业。

注意： 字典（dictionary）又叫词典（lexicon），也就是说，词典（lexicon）就是一部罗列并定义文字的字典（dictionary）。

现在我们知道了作用域和词法的意思，那就可以讨论 词法作用域 了。

## 什么是词法作用域（Lexical Scope）

词法作用域（Lexical Scope） 是定义表达式并能被访问的区间。

换言之，一个声明（定义变量、函数等）的词法作用域就是它被定义时所在的作用域。

注意：

词法作用域又叫静态作用域。

一个声明 被调用时的作用域 不一定是它的词法作用域。相反的，定义时的作用域 才是词法作用域

## 词法作用域如何工作

JavaScript 表达式的词法作用域决定了哪些代码可以访问它。换言之，只有词法作用域内的代码才可以访问该作用域内部的代码。

词法作用域的替换方案是动态作用域，但是除了在极少的开发语言（比如 bash 脚本）中使用外，很少使用它。
