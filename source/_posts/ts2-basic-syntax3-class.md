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

## Getter & Setter

* 对于私有属性，类外是无法直接进行赋值或者读取的。可以通过getter、setter访问器属性对其进行访问设置。
* 通过getter/setter进行读取/设置私有属性的之前，可以进行拦截来做一些逻辑操作。

``` javascript
class Person {
  constructor(private _name: string) { }

  get name() {
    // note: 可以对私有属性进行一些处理，再进行返回
    return this._name + 'eating';
  }

  set name(name: string) {
    const realName = name.split(' ')[0];
    this._name = realName;
  }
}

const person = new Person('noodles')
// note: 这里获取不到_name属性，因为这个属性是私有的
// person._name;
// note: 这里可以获取到person的name，访问器属性
person.name;


// note: 同理，无法直接对_name进行赋值，因为是私有的
// person._name = 'dell';
// note: 所以我们可以通过setter对person进行属性设置
person.name = 'noodles eating'
console.log(person.name)
```

## 静态属性

* static：表示把这个属性或者方法挂载到类上面，而不是类的实例上面。访问时，使用`类名.方法名`或者`类名.属性名`

## 实现一个单例模式

```javascript
// note: 单例模式，只想Demo有一个实例
class Demo {
  // 静态 私有变量
  private static instance: Demo;
  // note: 构造方法设置为私有，以为这外部无法访问，也就无法使用new来创建新对象
  private constructor(public name: string) { }
  
  static getInstance() { 
    if (!this.instance) {
      this.instance = new Demo('noodles')
    }

    return this.instance
  }
  
}

const demo1 = Demo.getInstance()
const demo2 = Demo.getInstance()

console.log(demo1.name)
console.log(demo2.name)
```

