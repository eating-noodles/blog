---
title: ts2-basic-syntax3-class
date: 2022-04-13 22:35:58
tags: ts
---
# ts - class
## 类的基本使用
<!-- more -->
### 定义类
``` typescript
class Person {
  name: string = 'noodles';

  getName() {
    return this.name;
  }
}

const person = new Person();
console.log(person.getName())
```
### 类的继承
``` typescript
class Teacher extends Person {
  getTeacherName() {
    return 'teacher'
  }
  getName() {
    // output: noodles lee
    return super.getName() + ' lee'
  }
}
const teacher = new Teacher();
console.log(teacher.getName())
console.log(teacher.getTeacherName())
```
* `Teacher`为子类，`Person`为父类，子类会继承父类所有的属性和方法。
* 子类可以重写父类的方法。
* 子类`Teacher`中使用`super`关键字，指代的是父类`Person`，可以访问父类的属性和方法。

## 类中的访问类型
```typescript
class Person {
  name: string;
  sayHi() {
    // note: 类内调用
    this.name;
  }
}

class Teacher extends Person {
  sayBye() {
    // note: 在继承的子类中使用
    this.name;
  }
}

const p = new Person();
// note: 在类外被调用
p.name = 'noodles';
console.log(p.name)
```
* public: 允许在类的内外被调用。声明类的属性时，如果没有指定访问类型，默认为public。
* private: 只允许在类内被使用。
* protected: 允许在类内以及继承的子类中使用。

## constructor
* constructor构造方法会在`new`一个对象的时候被自动调用。
### 初始化对象的两种书写方式
1. 第一种: 传统写法
``` typescript
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

const p = new Person('dell');
console.log(p.name)
```
2. 第二种: 简化写法
``` typescript
class Person {
  constructor(public name: string) { }
}

const p = new Person('dell');
console.log(p.name)
```
在`constructor`中标明参数的访问类型和名称，相当于在类中声明属性`name: string`和在构造方法中对属性赋值`this.name = name;`

### 子类中的constructor
``` typescript
class Person {
  constructor(public name: string) { }
}
class Teacher extends Person {
  constructor(public age: number) {
    // note: 必须手动调用父类构造方法
    super('dell');
  }
}
```
子类的`constructor`中必须手动调用父类的`constructor`(super())。