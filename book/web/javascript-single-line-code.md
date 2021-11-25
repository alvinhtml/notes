# 25 个常用的 JavaScript 单行代码

## 强制布尔值

要将变量强制转换为布尔值而不更改其值：

```js
const myBoolean = !! myVariable;
!!null // false
!!undefined // false
!!false // false
!!ture // ture
!!"" // false
!!"string" // true
!!0 // false
!!1 // true
!!{} // true
!![] // true
```

## 基于某个条件为对象设置属性

要使用spread运算符有条件地在对象上设置属性：

```js
const myObject = {...myProperty && {propName：myPoperty}};

let myProperty = 'Jhon'
const myObject = {...myProperty && {propName: myProperty}}; // {propName: "Jhon"}
let myProperty = ''
const myObject = {...myProperty && {propName: myProperty}}; // {}
```
如果myProperty结果为false，则 && 失败并且不设置新属性; 否则，如果不为空，&& 将设置新属性并覆盖原来的值。

## 合并对象

```js
const mergedObject = { ...objectOne, ...objectTwo };

const mergedObject = { ...{name: 'Jhon', age: '18'}, ...{name1: 'jhon1', age1: '12'}};
// {name: "Jhon", age: "18", name1: "jhon1", age1: "12"}

const mergedObject = { ...{name: 'Jhon', age: '18'}, ...{name: 'jhon1', age:'12'}};
// {name: "jhon1", age: "12"}
```

支持无限制合并，但如果对象之间存在相同属性，则后面属性会覆盖前面属性。`*`请注意，这仅适用于浅层合并。




## 交换变量

要在不使用中间变量的情况下交换两个变量的值：

```js
let a = 1;
let b = 2;
[a, b] = [b, a] // a = 2 b = 1
```


## 删除Boolean 为 false 值

```js
const clean = dirty.filter(Boolean);
const clean = [0, false, true, undefined, null, '', 12, 15].filter(Boolean);
// [true, 12, 15]
```

这将删除值等于：null，undefined，false，0 和空字符串('')。



## 转换元素类型

要将Number元素转换为String元素：

```js
const stringArray = numberArray.map(String);
const stringArray = [1, 2, 3].map(String);
["1", "2", "3"]
```

如果数组包含字符串，字符串原样保留。 这也可以用于将String元素转换为Number类型：

```js
const numberArray = stringArray.map(Number);
const stringArray = ["1", "2", "3"].map(String);
// [1, 2, 3]
```


## 格式化对象为JSON代码

要以可读的格式显示JSON代码：

```js
const formatted = JSON.stringify(myObj, null, 4);

const formatted = JSON.stringify({name: 'Jhon', age: 18, address: 'sz'}, null, 4);
/*
{
    "name": "Jhon",
    "age": 18,
    "address": "sz"
}
*/
```

该字符串化命令有三个参数。第一个是Javascript对象。第二个是可选函数，可用于在JSON进行字符串化时对其执行操作。最后一个参数指示要添加多少空格作为缩进以格式化JSON。省略最后一个参数，JSON将返回一个长行。如果myObj中存在循环引用，则会格式失败。


## 快速创建数字数组

要创建一个数组并用数字填充它，索引为零：

```js
// new Array(10) 创建的是 Array 对象，所以需要用 Array.from 转为 Array

const numArray = Array.from(new Array(10), (x, i)=> i);
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 随机生成六位数字验证码

```js
const code = Math.floor(Math.random() * 1000000).toString().padStart(6, "0");
Math.random().toString().slice(-6);
```

## 身份证正则

```js
const IDReg= /(^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$)|(^[1-9]\d{5}\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{2}[0-9Xx]$)/;
```


## window.location.search 转 JS 对象

有时候我们会对url的查询参数即从问号 (?)后 开始的 URL（查询部分）进行转换

```js
const searchObj = search => JSON.parse(`{"${decodeURIComponent(search.substring(1)).replace(/"/g, '\\"').replace(/&/g, '","').replace(/=/g, '":"')}"}`);
```

## 获取数组交集

```js
const similarity = (arr, values) => arr.filter(v => values.includes(v));
similarity([1, 2, 3], [1, 2, 4]); // [1,2]
```

## 检测设备类型

使用正则表达式来检测 navigator.userAgent 属性判断设备是在移动设备还是在台式机/笔记本电脑打开。

```js
const detectDeviceType = () =>/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|OperaMini/i.test(navigator.userAgent) ? 'Mobile' : 'Desktop';
```

## 将数字转化为千分位格式

```js
const toDecimalMark = num => num.toLocaleString('en-US');
toDecimalMark(12305030388.9087); // "12,305,030,388.909"
```

## 多维数组转一维数组

```js
const deepFlatten = arr => [].concat(...arr.map(v => (Array.isArray(v) ? deepFlatten(v) : v)));
deepFlatten([1, [2], [[3], 4], 5]); // [1,2,3,4,5]
```

## 过滤对象数组

```js
const reducedFilter = (data, keys, fn) => data.filter(fn).map(el =>keys.reduce((acc, key) => {acc[key] = el[key];return acc;}, {}));
const data = [
  {
    id: 1,
    name: 'john',
    age: 24
  },
  {
    id: 2,
    name: 'mike',
    age: 50
  }
];

let a = reducedFilter(data, ['id', 'name'], item => item.age > 24); // [{ id: 2, name: 'mike'}]
```

## 驼峰字字符串格式化
转换驼峰拼写的字符串为特定格式。

使用 String.replace() 去除下划线，连字符和空格，并将驼峰拼写格式的单词转换为全小写。省略第二个参数 separator ，默认使用 _ 分隔符。

```js
const fromCamelCase = (str, separator = '_') =>str.replace(/([a-z\d])([A-Z])/g, '$1' + separator + '$2').replace(/([A-Z]+)([A-Z][a-z\d]+)/g, '$1' + separator + '$2').toLowerCase();

fromCamelCase('someDatabaseFieldName', ' '); // 'some database field name'
fromCamelCase('someLabelThatNeedsToBeCamelized', '-'); // 'some-label-that-needs-to-be-camelized'
fromCamelCase('someJavascriptProperty', '_'); // 'some_javascript_property'
```

## 获取两个日期相差天数

const getDaysDiffBetweenDates = (dateInitial, dateFinal) => Math.abs((dateFinal - dateInitial) / (1000 * 3600 * 24));
getDaysDiffBetweenDates(new Date('2017-12-13'), new Date('2017-12-22')); // 9


## 数组去重

```js
const deDupe = (myArray) => [... new Set(myArray)];
deDupe([1, 1, 2, 1, 3, 3, 4])
// [1, 2, 3, 4]
```

## 数组对象去重

```js
const uniqueElementsBy = (arr, fn) => arr.reduce((acc, v) => {if (!acc.some(x => fn(v, x))) acc.push(v); return acc;}, []);

uniqueElementsBy([{id: 1, name: 'Jhon'}, {id: 2, name: 'sss'}, {id: 1, name: 'Jhon'}], (a, b) => a.id == b.id)
// [{id: 1, name: 'Jhon'}, {id: 2, name: 'sss'}]
```

## 常用密码组合正则

```js
const passwordReg = /(?!^(\d+|[a-zA-Z]+|[~!@#$%^&*?]+)$)^[\w~!@#$%^&*?]{8,20}$/;
// -长度8~20位字符，支持大小写字母、数字、符号三种字符中任意两种字符的组合
```

## 判断dom元素是否具有某个className

```js
const  hasClass = (el, className) => new RegExp(`(^|\\s)${className}(\\s|$)`).test(el.className);
```


## 打乱数组元素的顺序

不适用 Lodash 等这些库打乱数组元素顺序，你可以使用这个技巧：

```js
const list = [1, 2, 3, 4, 5, 6];
list.sort(() => Math.random() - 0.5);
console.log(list);
```

## 翻转一个字符串

先将字符串转成一个数组，然后用数组的 reverse() + join() 方法。

```js
let a = "hello word";
let b = [...str].reverse().join(""); // drow olleh
```

## 数组扁平化

数组扁平化是指将一个多维数组变为一个一维数组

```js
// 方法一
[1, [2, [3, [4, 5]]], 6].flat(Infinity)

// 方法二
JSON.parse(`[${JSON.stringify([1, [2, [3, [4, 5]]]]).replace(/\[|\]/g, '')}]`)

// 使用 reducer + 递归

function flat(arr) {
  arr.reduce((a, b) => a.concat(Array.isArray(b) ? flat(b) : b) , [])
}
```






















## 1
