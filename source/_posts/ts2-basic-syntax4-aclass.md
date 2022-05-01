---
title: ts2-basic-syntax3-aclass.md
date: 2022-05-01 22:32:13
tags: ts
---

# 抽象类

<!-- more -->

## readonly

限制属性只读，不可修改

``` javascript
class Person {
  public readonly name: string;
  constructor(name: string) {
    this.name = name
  }
}

const person = new Person('noodles')
// 可以读取name属性
console.log(person.name)
// 不可修改
person.name = 'hhh'
```

## 抽象类

### 抽象类

* 根据需求，把对应的`类`共性的东西进行一个抽离，形成一个共性的东西就是抽象类。
  * 其中可以有抽象的方法和属性
  * 也可以有非抽象的方法和属性
* 抽象类不可以`new`实例，只可以被继承。
* 抽象类中的抽象方法`不可实现`(不可以写具体的逻辑)。
* 继承了抽象类的子类必须实现抽象类的抽象方法。

Example：

``` javascript
abstract class Geom {
  width: number;
  getType() {
    return 'Gemo';
  }
  abstract getArea(): number;
}

class Circle extends Geom {
  getArea(): number {
    return 10;
  }
}

class Square extends Geom {
  getArea(): number {
    return 101;
  }
}

class Triangle extends Geom {
  getArea(): number {
    return 1011;
  }
}
```

### 抽象类 VS 接口

抽象类：把`类`相关的、通用的东西抽象出来

接口：把各种`对象`通用的东西抽象出来

``` javascript
// note: 接口，提取出来老师、学生 的通用属性
interface Person {
  name: string
}

// note: 提取出来老师这类对象 通用的属性
interface Teacher extends Person {
  teachingAge: number,
}

// note: 提取出来学生这类对象 通用的属性
interface Student extends Person {
  age: number
}

const teacher = {
  name: 'dell',
  teachingAge: 10
}

const student = {
  name: 'noodles',
  age: 18
}

const getuserInfo = (user: Person)=> {
  console.log(user.name)
}

getuserInfo(teacher)
getuserInfo(student)
```

