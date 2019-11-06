# Sequelize

## ORM

### 什么是ORM

ORM(Object-relational mapping)，中文翻译为对象关系映射，是一种为了解决面向对象与关系数据库存在的互不匹配的现象的技术。简单的说，ORM 使用面向对象的方式，通过操作对象来实现与数据库之前的交流，完成CRUD的动作。

开发者并不需要关心数据库的类型，也不需要关心实际的表结构，而是根据当前编程语言中对象的结构与数据库中表、字段进行映射。

与传统的数据库访问技术相比，ORM有以下优缺点：

- 优点
  - 开发效率更高
  - 数据访问更抽象、轻便
  - 支持面向对象封装
- 缺点
  - 降低程序的执行效率
  - 思维固定化

## Sequelize

Sequelize 是用于 Postgres，MySQL，MariaDB，SQLite和Microsoft SQL Server 的 `Node.js ORM`。它具有可靠的事务支持，关系，渴望和延迟加载，读取复制等功能。

## Sequelize-typescript

`Sequelize-typescript` 是基于 `Sequelize` 针对 `TypeScript` 所实现的一个增强版本，抛弃了之前繁琐的模型定义，使用装饰器直接达到我们想到的目的。

## 使用

首先因为是用到了TS，所以环境依赖上要安装的东西会多一些：

```bash
# 这里采用ts-node来完成举例
npm i ts-node typescript
npm i sequelize reflect-metadata sequelize-typescript
```

其次，还需要修改TS项目对应的 `tsconfig.json` 文件，用来让TS支持装饰器的使用：

```js
{
  "compilerOptions": {
+   "experimentalDecorators": true,
+   "emitDecoratorMetadata": true
  }
}
```
`Sequelize-typescript` 与 `Sequelize` 不同之处基本在于模型定义的地方：

```js
// User.ts
import { Table, Column, Model } from 'sequelize-typescript'

@Table({
  tableName: 'users'
})
class User extends Model<User> {
  @Column({
    primaryKey: true,
    autoIncrement: true,
  })
  id: number

  @Column
  name: string

  @Column
  email: string
}

export default User;
```

```js
// app.ts

import { Sequelize } from 'sequelize-typescript';
import User from './models/User.ts';

const sequelize = new Sequelize({
  database: 'lumen',
  dialect: 'mysql',
  username:  'root',
  password: '123456'
  // models: [__dirname + '/api/models/*.ts'],
});

sequelize.addModels([User]);

const userRequest = await User.findAll({
  attributes: ['name', 'email']
});

console.log(JSON.stringify(userRequest));
```

与普通的 Sequelize 不同的有这么几点：

- 模型的定义采用装饰器的方式来定义
- 实例化 `Sequelize` 对象时需要指定对应的 `model` 路径
- 模型相关的一系列方法都是支持Promise的

## Sequelize API

### 查询

```js
const userRequest = await User.findAll({
  attributes: ['id', 'name', 'email'],
  where: {
    id: ctx.params.id
  }
});
```
