---
title: webpack2-coreConcept3
date: 2022-03-15 21:38:13
tags:
---
# 核心概念篇3
## sourceMap
### 1. 啥是source
<!-- more -->
* 现在知道dist目录下main.js文件第96行出错
* souceMap 它是一个映射关系，它知道dist目录下main.js文件96行，实际上对应的是src目录下index.js文件中的第一行
* 当前其它是index.js中第一行代码出错了

### 2.配置项
* mode: 'development'; 开发者模式时，sourceMap默认已经开启
* devtool: false; 可以关闭sourceMap

### 3.devtool可填值
1. source-map
* dist目录下会生成main.js.map-打包代码和源码的映射关系

2. inline-source-map
* main.js.map会被变成一个base64的字符串写到main.js文件中

3. inline-cheap(-module)-source-map
* 不加cheap,报错会精确到哪行的哪个字符（哪里）错了
* 加了cheap,报错只会精确到行
* 加了cheap,会忽略第三方插件、lodaer的报错地方,只管我们自己的业务代码
    * 加了module,那么也会映射第三方插件、loader报错的地方

4. eval
* 速度最快，性能最好
* 通过eval生成的映射关系
* 针对比较复杂代码，eval提示出来的错误可能并不全面

### 4.推荐的配置
* 开发环境
    ``` javascript
    mode: 'development',
    devtool: eval-cheap-module-source-map
    ```
    * 提示信息较全，打包速度也比较快
* 生产环境
    ``` javascript
    mode: 'production',
    devtool: cheap-module-source-map
    ```
