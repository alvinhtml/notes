# D3JS

## API

### 选择元素和绑定数据

在 D3 中，用于选择元素的函数有两个：

- `d3.select()`: 是选择所有指定元素的第一个
- `d3.selectAll()`: 是选择指定元素的全部

这两个函数返回的结果称为`选择集`，选择集合绑定数据通常是一起使用的


D3 中通过以下两个函数来绑定数据：

- `datum()`：绑定一个数据到选择集上
- `data()`：绑定一个数组到选择集上，数组的各项值分别于选择集的各元素绑定

```js
import * as d3 from 'd3'

const str = 'China'
const root = d3.select('#root')
root.append('p')
const p = root.selectAll('p')
p.datum(str)

p.text(function (d, i) {
  return `第${i}个元素绑定的数据是${d}`
})
```

## call & each

- `each()` 在选择中的每个元素上调用一个回调函数，您可能会将其视为一种循环。

- `call()` 调用选择本身的回调函数。该函数只调用一次，整个选择作为参数。



### 比例尺