---
title: ts1-basic-syntax1
date: 2022-04-13 21:11:34
tags: ts
---
# ts基本语法

## 数组和元组
### 数组
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
class Teacher { name: string }

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