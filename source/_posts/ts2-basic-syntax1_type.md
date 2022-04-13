---
title: ts2-basic-syntax1_type
date: 2022-04-12 23:23:47
tags: ts
---
# ts的基本理解和基本语法1-关于类型&函数

## 类型
### 静态类型的深度理解
把某个变量定义为某个类型以后：
<!-- more -->
1. 这个变量不可被赋值为其他类型
2. 这个变量拥有这个类型所有的属性和方法

### 基础类型 vs 对象类型
1. 基础类型
null, undefined, symbol, boolean, void, number, string ..
``` typescript
// 基础类型
const count: number = 2019;
const nm: string = 'noodles';
```

2. 对象类型
类(Class), 自定义对象({}), 数组([]), 函数(function) ...
``` typescript
// 对象类型
// 类
class Person {}

// 自定义对象
const teacher: {
  name: string;
  age: number;
} = {
  name: 'noodles',
  age: 18
};

// 数组
const numbers: number[] = [1, 2, 3];

const noodles: Person = new Person();
// 函数
const getTotal: () => number = () => 13;
```

### 类型注解(type annotation) vs 类型推断(type inference)
``` typescript
// type annotation 类型注解
let count1: number;
count1 = 123;

// type inference 类型推断
let countInference = 456;
```
* 类型注解：声明变量时，显式的告诉ts变量是什么类型
* 类型推断：ts会自动去尝试分析变量的类型
* 如果ts能够自动分析变量的类型，我们就什么也不需要做；如果ts无法分析变量的话，我们就需要使用类型注解

## 函数
### 函数声明
1. 方式1
``` typescript
const func = (str: string): number => {
  return parseInt(str, 10)
}
```
2. 方式2
``` typescript
const func: (str: string) => number = (str) => {
  return parseInt(str, 10)
}
```
### 函数的类型
函数返回值的类型可以有number, string, void, never ...类型
1. void
表示此函数没有返回值
2. never
简单理解，表示此函数永远不会执行到最后
``` typescript 
// note: 这个函数不可能会执行到console方法
function errorEmitter(): never {
  throw new Error();
  console.log('--')
}

// note: 这个函数陷入到循环中，while后面的东西永远执行不到，这个函数永远执行不完
function errorEmitter1(): never {
  while(true) { }
}
```
### 函数参数解构的类型语法
``` typescript
// note: 解构语法声明类型
function add({ first, second }: {first: number, second: number}): number  {
  return first + second;
}
const total = add({first: 1, second: 2})
```


## 其他
代码仓库 [ts1-basic-syntax_type](https://github.com/eating-noodles/ts_memo/tree/main/ts1-basic-syntax_type)