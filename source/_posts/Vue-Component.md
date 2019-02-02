---
title: Vue-Component
date: 2019-02-02 11:14:28
tags: Vue-Base-Usage
categories: Vue
toc: true
comments: true
---
### Component
#### 关于组件命名
```
当直接在 DOM 中使用一个组件(而不是在字符串模板或单文件组件)的时候，
我们强烈推荐遵循 W3C 规范中的自定义组件名 (字母全小写且必须包含一个连字符)。
这会帮助你避免和当前以及未来的 HTML 元素相冲突。
```
> 命名不符合规则，会在调用组件时出现问题。
<!-- more -->
#### Prop
##### 单向数据流
```
1.单向下行绑定，父级prop的更新会流动到子组件，反过来不行。
2.这意味着，在实际开发中，不可以在子组件中修改父组件传入的
prop的值，prop为只读状态。
```

> * 在Js中，对象和数组是通过引用传入的，所以对于一个数组或者
    对象类型的prop来说，在子组件中改变将会影响到父组件的状态。
    
> * 两种参考情况：
> ```
>  1.这个 prop 用来传递一个初始值；这个子组件接下来希望将其
>  作为一个本地的 prop 数据来使用。在这种情况下，最好定义一
>  个本地的 data 属性并将这个 prop 用作其初始值：
>   
>   props: ['initialCounter'],
>   data: function () {
>       return {
>           counter: this.initialCounter
>       }
>   }
> ```
>
>```
>   2.这个 prop 以一种原始的值传入且需要进行转换。在这种情况下，
>   最好使用这个 prop 的值来定义一个计算属性：
>
>   props: ['size'],
>   computed: {
>   normalizedSize: function () {
>        return this.size.trim().toLowerCase()
>       }
>   }
>```

##### prop类型
- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

##### $attrs
- 子组件定义：
```
<template>
    <div>
        props:{{name}},{{age}} 
        <!--或者 -->
        {{$props['name']}},{{$props['age']}}
    </div>
</template>

export default{
    name: 'child',
    props: ['name','age']
}
```
- 父组件调用
```
<child name="rick" :age="18" gender="male"></child>
```
- 如果我们给child传props没有注册的属性，就需要使用$attrs来取了
```
<template>
    <div>
        props:{{name}},{{age}} 
        <!--或者 -->
        {{$props['name']}},{{$props['age']}} 

        attrs: {{$attrs['gender']}} 
        <!--在$attrs里面只会有props没有注册的属性-->
    </div>
</template>

export default{
    props: ['name','age']
}
```

#### Slot
- [单个插槽](https://cn.vuejs.org/v2/guide/components-slots.html#%E6%8F%92%E6%A7%BD%E5%86%85%E5%AE%B9) 
- [具名插槽](https://cn.vuejs.org/v2/guide/components-slots.html#%E5%85%B7%E5%90%8D%E6%8F%92%E6%A7%BD)
- [作用域插槽](https://blog.csdn.net/weixin_40920953/article/details/80527741)




#### 组件书写的模板（不使用语法糖的格式）
```
<template>
    <!--html元素模板 双向绑定等操作-->
</template>

<script>
<!--可以导入其他的方法，组件等-->
import XXX from 'xxx'
import ComponentE from '@/views/components/ComponentE.vue'
import mapGetters from 'vuex'

export default {
  name: 'example',
  components: {
      ComponentE
  },
  props: {
    prop1: {
        type: Array,
        required: true,
        default() {
            return []
        }
    },
    ...
  },
  data() {
      return {
          <!--组件内部数据定义-->
          data1: 'ExampleData',
          ...
      }
  },
  computed: {
  <!--1.可以定义组件内部的计算属性-->
    ...mapGetters([
    <!--2.在store中的定义属性-->
        'storeData1',
        ...
    ])  
  },
  watch: {
    storeData1(val) {
    <!--1.监听store中的属性 val为新值-->
    },
    <!--2.还可以监听组件内部的属性-->
  },
  create() {
      <!--调用钩子函数-->
  },
  methods: {
    <!--定义的各个方法。包括：-->
    <!--1.组件自身的方法-->
    <!--2.暴露给父组件的方法-->
    
    <!--子组件使用$emit()传递事件给父组件,第一个参数为事件名，-->
    <!--父组件接收时以此名称匹配。第二个为传递的参数。-->
    <!--在父组件中使用此子组件时，可以以下面这种格式书写：-->
    <!--@method='父组件中的方法名'-->
    method(param) {
        this.$emit('method',param)
    }
  }
}

</script>

<style>
    <!--自定义的CSS样式，可以配合loader使用CSS预处理语言-->
</style>
```