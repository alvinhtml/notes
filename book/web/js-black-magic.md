# JavaScript 黑魔法

## 前置 0

```js
Number.prototype.pad = function(len) {
    return (new Array(len + 1).join('0') + this).slice(-len)
}
````

## 字符串转数字

JavaScript 中字符串转为数字有 `parseInt`, `Number()`, `+` 等方式，它们的转换规则很不一样适用范围也不同。比如 `parseInt` 可以解析数字字母的混合字符串而 `Number` 和 `+` 会直接产生 `NaN`，`Number` 和 `parseInt` 可以操作 `BigInt` 而 `+` 则会抛出 TypeError。

`parseInt` 第一个字符不是数字时会返回 NaN，例如 `parseInt('e3')` 为 `NaN`，但 `parseInt('3e')` 为 3。

Number 是 "number" 基本类型的对象封装，不加 new 关键字时只做转换可以仍然返回基本类型。

JavaScript 定义了 单目运算符 '+'，它的操作数可以是数字，也可以是其他类型。是其他类型时会被转换为数字。用于 'BigInt' 时会抛出 'TypeError。'

'+val' 是 JavaScript 中最常见的、最快的转换其他值到数字的方式，它不仅支持浮点数表示，还支持 8 进制和 16 进制，也能转换 `null`, `false`, `true。转换失败时值为` `NaN。和` `Number` 一样不会尝试转换字符串前缀为数字，只要整个字符串是非法的值就是 NaN。

需要注意的是 `num + str` 中的 `+` 是双目运算符 `+`，它不会把 str 转换为数字。如果这里要使用单目运算符 `+`，需要写成 `num + +str`。注意两个 `+` 要有空格，否则会解释为自增运算符进而抛出 SyntaxError。

```js
parseInt('3a123')  // 3
Number('3') // 3
+ '3' // 3 这里 + 是单目运算符
2 + '3' // '23'  这里 + 是双目运算符
2 +  +'3' // 5
````
