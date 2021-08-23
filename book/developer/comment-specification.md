# 注释规范

Example:

```typescript
/**
 * 根据坐标和子节点，计算当前节点的位置信息
 *
 * @param {number}    x X 坐标.
 * @param {number}    y Y 坐标.
 * @param {boolean}   isHorizontal children orient.
 * @return {Object}   isHorizontal children orient.
 * @internal
 */
const setPosition = (x: number, y: number, isHorizontal: boolean) => {
  if (isHorizontal) {
    return {
      x: x + 100,
      y
    }
  } else {
    return {
      x,
      y: y + 100
    }
  }
}
```


```
@api: 提供给第三方使用的接口
@author: 标明作者
@param: 参数
@return: 返回值
@todo: 待办
@version: 版本号
@inheritdoc: 文档继承
@property: 类属性
@property-read: 只读属性
@property-write: 只写属性
@const: 常量
@deprecated: 过期方法
@example: 示例
@final: 标识类是终态, 禁止派生
@global: 指明引用的全局变量
@static: 标识类、方法、属性是静态的
@ignore: 忽略
@internal: 限内部使用
@license: 协议
@link: 链接,引用文档等
@see: 与 link 类似, 可以访问内部方法或类
@method: 方法
@package: 命名空间
@since: 从指定版本开始的变动
@throws: 抛出异常
@uses: 使用
@var: 变量
@copyright: 版权声明
```
