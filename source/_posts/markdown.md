---
title: markdown
date: 2019-02-02 14:22:02
tags: MarkDown
categories: MarkDown-Usage
toc: true
---
# 目录

<!-- more -->

[TOC]


# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题

------

### 块的几种方式
第一种
:  定义型

        这是第一种
        
第二种
```
这是第二种
```

第三种
> 这是第三种
> * 这是第三种

第四种

`同行显示` `关键字`

第五种

    开头加四个空格(1个tab)

------

### 测试一下
> * 这是一个测试
> * 这是第二个测试


### 代码高亮
``` javascript
let a=""
console.log(a)
```

``` java
# java code
public class people {
    private int age;
    private string name;
}
```

### TO-DO
- [x] 项目一
- [ ] 项目二


### 表格
|Item     |Value|Key|
|:--------|----:|:-:|
|compute  |    1|  1|
|phone    |    2|  2|
|pad      |    3|  3|


### 无序列表
- 1
    - 1.1 
- 2
- 3

### 有序列表
1. 列表1
    1. 列表1.1
2. 列表2

### 引用

> 嘤嘤嘤。 --盲流子语录


### 字体
*这是斜体*
**这是粗体**
***斜体加粗***
~~删除线~~

### 插入链接
[有道云笔记](http://www.baidu.com)

### 插入图片
![有道云笔记](http://note.youdao.com/favicon.ico)

### 分割线
<font size=4>第一段
***
第&emsp;二段  
第&ensp;二段<br>
第二段

### 分层
> hello world
>> hello world

### 关键字
`关键字1` `关键字2`

### [定义型列表](#引用)

Markdown
:   轻量级文本标记语言

代码块定义
:   代码块定义

        var a=10

### 脚注
Markdown[^1]
[^1]:Markdown是一种纯文本标记语言

### 数学公式
`$y=x^2$`

### 流程图
```
graph TD
    A[1] --> B[2]
    B --> C(3)
    C --> D{4}
    D --> |yes|E(4.1)
    D --> F(4.2)

```

### 序列图
```
sequenceDiagram
    loop every day
        Alice->>John: Hello,how are you?
        John-->>Alice: Fine,thanks!
    end
```

### 甘特图
```
gantt
dateFormat YYYY-MM-DD
title 计划表
section 第一阶段
明确需求:2019-03-01,10d
section 第二阶段
开发:2019-03-11,15d
section 第三阶段
测试:2019-03-20,9d
```