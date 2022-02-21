# Typescript 项目常用语法

## 常用类型

```typescript
// 1. string 字符串类型
export const str: string = "helloworld";
str.substr(3);

// 2. number 数字类型
let num: number = 100;
num++;

// 3. boolean 布尔类型
const bool: boolean = true;

// 4. 数组类型
const numArr: number[] = [1, 2, 3];
numArr.map((num) => ++num);

// 5. 对象类型
type User = {
  name: string;
  age: number;
  isAdmin: boolean;
};
const user: User = {
  name: "xiaoming",
  age: 18,
  isAdmin: false,
};
const { name, age, isAdmin } = user;

// 6. 函数类型
type Fn = (n: number) => number;
const fn: Fn = (num) => ++num;
fn(1);
```

## React 组件 Props

```typescript
interface Props {
  disabled?: boolean;
  style?: React.CSSProperties;
  children?: React.ReactNode;
  onClick?: () => void;
}

const Button = ({ onClick, disabled, children, style }: Props) => {
  return (
    <button onClick={onClick} disabled={disabled} style={style}>
      {children}
    </button>
  );
};

export default Button;
```

## 联合类型 Union

```typescript
// id 可为字符串或数字类型
export function printId(id: string | number) {
  console.log(id);
}

printId(101); // OK
printId("202"); // OK
```

## 类型判断

```typescript
export function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id);
  }
}

printId(101); // OK
printId("202"); // OK
```

## 类型断言

```typescript
export type Position = "left" | "right" | "top" | "bottom";
const setPos = (pos: Position) => {
  //...
};

const handleChange = (value: string) => {
  setPos(value as Position);
};

handleChange("left");
```

## 属性名不确定的对象

```typescript
export type Paths = {
  [key: string]: string;
};

// 等同于
// export type Paths = Record<string, string>;

const paths: Paths = {};

paths.home = "/home"; //OK
paths.settings = "/settings"; //OK
paths.somePath = "/somePath"; //OK
```

## 对象的 key 值

```typescript
export const ErrorMessage = {
  0: "success",
  7: "Permission denied",
  9: "Invalid parameters",
  //...
};

export type ErrorCode = keyof typeof ErrorMessage;

export const logErrMsg = (code: ErrorCode) => {
  console.log(ErrorMessage[code]);
};
```

## 泛型 generics

```typescript
type UseState = <T>(v: T) => [T, (v: T) => void];

const useState: UseState = (v) => {
  return [
    v,
    (v) => {
      //...
    },
  ];
};

export const Component = () => {
  const [num, setNum] = useState(0); // OK
  const [str, setStr] = useState(""); // OK
  const [list, setList] = useState([1, 2, 3]); // OK

  // test
  const newNum = num + 1;
  setNum(newNum);

  const newStr = str.toUpperCase();
  setStr(newStr);

  const newList = list.slice(1);
  setList(newList);
};
```

## 部分对象 Partial

```typescript
interface User {
  name: string;
  age: number;
  occupation: string;
}

export const users: User[] = [
  {
    name: "Max Mustermann",
    age: 25,
    occupation: "Chimney sweep",
  },
  {
    name: "Wilson",
    age: 23,
    occupation: "Ball",
  },
];

type Criteria = {
  [Property in keyof User]?: User[Property];
};

// 等同于
// type Criteria = Partial<User>;

export const filterUsers = (users: User[], criteria: Criteria): User[] =>
  users.filter((user) => {
    const criteriaKeys = Object.keys(criteria) as (keyof Criteria)[];
    return criteriaKeys.every((fieldName) => {
      return user[fieldName] === criteria[fieldName];
    });
  });

const usersOfAge23 = filterUsers(users, {
  age: 23,
});
```

## 函数中 this 的使用

```typescript
// 参考 https://www.typescriptlang.org/docs/handbook/2/functions.html#declaring-this-in-a-function

export const debounce = <F extends (...args: any[]) => void>(
  fn: F,
  delay = 200
) => {
  let timeout = 0;
  return function (this: any, ...args: any[]) {
    timeout && clearTimeout(timeout);
    timeout = window.setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  } as F;
};
```

## 类型编程-从基础类型构造新类型

```typescript
export type CustomObject<K extends string | number, T> = { [key in K]: T };

// 1. 示例
// ObjectOfStringValue 对象的值只能为字符串类型
type ObjectOfStringValue = CustomObject<string, string>;
const objOfStringValue: ObjectOfStringValue = {
  h: "hello", // OK
  w: "world", // OK
};

// 2. ObjectOfStringValue
// ObjectOfStringValue 对象的值只能为数字类型
type ObjectOfNumberValue = CustomObject<string, number>;
const objOfNumberValue: ObjectOfNumberValue = {
  a: 100, // OK
  b: 100, // OK
};
const a = objOfNumberValue.a;

// 3. ObjectOfUserValue
type User = {
  username: string;
  age: number;
};

// ObjectOfUserValue 对象的值只能为 User 类型
type ObjectOfUserValue = CustomObject<string, User>;

const objOfUserValue: ObjectOfUserValue = {
  u1: {
    username: "xiaoming",
    age: 18,
  },
};
const { username } = objOfUserValue.u1;
```

## 对象类型的继承

```typescript
export interface Response {
  data: any;
  status: number;
  statusText: string;
}

// 1. 在 Response 类型的基础上添加 config 属性
export interface ResponseWithConfig extends Response {
  config: any;
}
const responseWithConfig: ResponseWithConfig = {
  data: 100,
  status: 0,
  statusText: "success",
  config: {},
};

// 2. 在 Response 类型的基础上改写 data 属性类型
export interface StringResponse extends Response {
  data: string;
}
const stringResponse: StringResponse = {
  data: "100",
  status: 0,
  statusText: "success",
};
```

## 对象类型的修改

```typescript
// extends 可以继承对象类型，但不可与原类型冲突，此时可以先使用 Omit 去除需要修改的属性

export interface TreeNode {
  id: number;
  value: number;
  children?: TreeNode[];
}

// 1. 去除 TreeNode 的 id 属性同时修改 children 属性的类型
export interface NodeWithoutId extends Omit<TreeNode, "id" | "children"> {
  children?: NodeWithoutId[];
}

// OK
const nodeWithoutId: NodeWithoutId = {
  value: 1,
  children: [
    {
      value: 2,
    },
  ],
};
```

## 类型编程-条件判断

```typescript
export declare type Person<T extends "User" | "Admin"> = T extends "User"
  ? {
      username: string;
    }
  : {
      username: string;
      role: string;
    };

const user: Person<"User"> = { username: "xiaoming" }; // OK

const admin: Person<"Admin"> = { username: "xiaofang", role: "manager" }; // OK
```

## React 组件 Props 范型

```typescript
import { useState } from "react";
Â;
// value 可为字符串或数字
type Value = number | string;
interface Props<T extends Value> {
  value?: T;
  onChange?: (v: T) => void;
  type?: "number" | "text";
}

const Input = <T extends Value>({
  value,
  onChange,
  type = "text",
}: Props<T>) => {
  return (
    <input
      value={value}
      onChange={(e) => {
        const { value } = e.target;
        onChange?.((type === "number" ? parseInt(value, 10) : value) as T);
      }}
      type={type}
    />
  );
};

// test
const Test = () => {
  const [num, setNum] = useState(0);
  const [str, setStr] = useState("");

  return (
    <div>
      <Input value={num} onChange={(v) => setNum(v)} type="number" />
      <Input value={str} onChange={(v) => setStr(v)} />
    </div>
  );
};

export default Input;
```
