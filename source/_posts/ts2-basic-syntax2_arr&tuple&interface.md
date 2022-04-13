---
title: ts2-basic-syntax_arr&tuple&interface
date: 2022-04-13 21:11:34
tags: ts
---
# ts基本语法

## 数组和元组
### 数组
数组用法跟js相差无几
<!-- more -->
``` typescript
const arr: (number | string)[] = [1, '2', 3];

const objArr: {name: string}[] = [{
  name: 'noodles'
}]
```
* 上面的objArr可以使用类型别名进行整理
``` typescript
type User = { name: string }

const objectArr: User[] = [{
  name: 'noodles'
}]
```
* 使用class定义类型
``` typescript
class Teacher { name: string | undefined }

const objectArr: Teacher[] = [
  new Teacher(),
  {
    name: 'noodles'
  }
]
```

### 元组 tuple
``` typescript
// note: 元组
const teacherInfo: [string, string, number]= ['Dell', 'male', 18];

// note: 处理csv文件
const teacherList: [string, string, number][] = [
  ['name1', 'male', 19],
  ['name2', 'female', 19],
  ['name3', 'female', 19],
];
```
元组定义：一个数组，长度固定，每一项的数据类型也固定

## interface接口
* 类型别名和接口
类型别名可以表示基本类型，接口不可以；
ts中能用接口表示的就用接口，不行采用类型别名。
* 设置可选属性
``` typescript
// note: age为可选属性，那么为必填属性
interface Person {
  name: string;
  age?:  number;
}
```
age为可选属性，那么为必填属性
* 设置只读属性
``` typescript
interface Person {
  readonly name: string;
}
```
属性前加`readonly`标识符，表示此属性不可修改只读。上例中，name为只读属性。

* 校验特性
``` typescript
interface Person {
  name: string;
  age?:  number;
}

const getPersonName = (person: Person) => {
  console.log(person.name)
}

const person = {
  name: 'noodles',
  sex: 'male'
}
// note: 将对象赋值给一个变量，再传递给方法时，尽管对象里面多了一个Person类型没有的sex属性，ts也不会报错
getPersonName(person)

// note: 如果直接把字面量对象传递给方法，这时候ts会进行强校验，会报错（字面量对象多了一个Person类型没有的sex属性）
getPersonName({
  name: 'noodles',
  sex: 'male'
})
```
* 给新属性预留类型声明
``` typescript
interface Person {
  name: string;
  age?:  number;
  [propName: string]: any;
}

const getPersonName = (person: Person) => {
  console.log(person.name)
}

// note: ts不会报错
getPersonName({
  name: 'noodles',
  sex: 'male'
})
```
Person接口预留了一个其他属性`[propName: string]: any`
* 在接口中添加方法
``` typescript
interface Person {
  name: string;
  age?:  number;
  [propName: string]: any;
  say(): string;
}

const getPersonName = (person: Person) => {
  console.log(person.name)
}

const person = {
  name: 'noodles',
  say() {
    return 'hello world'
  }
}
// note: ts不会报错
getPersonName(person)
```
* interface和类
一个类可以实现一个接口，而且类中必须拥有接口中的必选属性
``` typescript
class User implements Person {
  name = "hi";
  say() {
    return "hello";
  } 
}
```

* 接口之间的继承
接口Teacher1拥有Person的所有属性和功能
``` javascript
// note: age为可选属性，那么为必填属性
interface Person {
  // readonly name: string;
  name: string;
  age?: number;
  [propName: string]: any;
}

interface Teacher1 extends Person {
  teach(): string
}
```

* 接口定义函数类型
函数的类型，表示函数的传入值是字符串，返回值也是字符串。
``` typescript
interface SayHi {
  (word: string): string;
}
const say: SayHi = () => 'test';
```



## 其他

代码仓库 [ts1-basic-syntax_arr&tuple&interface](https://github.com/eating-noodles/ts_memo/tree/main/ts1-basic-syntax_arr%26tuple%26interface)