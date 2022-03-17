# Promise

Promise 对象用于表示一个异步操作的最终完成 (或失败)及其结果值。

## Promises/A+

1. Promise States

   - A promise must be in one of three states: pending, fulfilled, or rejected.

2. The then Method

   - A promise must provide a then method to access its current or eventual value or reason.
   - Promise 的 then 方法接受两个参数：onFulfilled, onRejected

3. The Promise Resolution Procedure

详细规范请参考 [Promises/A+](https://promisesaplus.com/)

## 实现代码
