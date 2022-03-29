---
title: webpack4-example-library
date: 2022-03-29 21:04:44
tags: webpack
---
# 实际配置案例讲解
webpack4.X

## 打包library 
1. 使打包的library支持ESM,CMD,AMD等的引入方式, webpack增加output.libraryTarget的配置
<!-- more -->
``` javascript
const path = require('path');

module.exports = {
    mode: 'production',
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'library.js',
        libraryTarget: 'umd'
    }
}
```
* 'umd'含义为universal(通用的)
* 可引入的方式：
``` javascript
import library from 'library';

const library = require('library')

require(['library'], function(){})
```

2. 使打包后的js文件通过script标签引入后,可以使用全局变量library访问其所存在的方法。
``` javascript
const path = require('path');

module.exports = {
    mode: 'production',
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'library.js',
        library: 'library',
        libraryTarget: 'umd'
    }
}
```
* library参数表示，将生成的包挂载到一个叫"library"的全局变量上
* 使用示例:
``` javascript
<script src='./library.js'></script>
library.math()
```
3. 另外一种配置
``` javascript
output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'library.js',
        library: 'library',
        libraryTarget: 'this'
    }
```
* 表示在全局的this上面挂载一个叫'library'的变量，这个变量存储了打包的工具方法
* libraryTarget设置为this以后，不再支持esm，amd，cmd的导入方式

4. 库里面和用户代码中使用到相同的第三方库（如lodash.js），可能造成打包重复。增加如下的配置项:
``` javascript
module.exports = {
    ...,
    // note: 写法一:
    // externals: ["lodash"],

    // note: 写法二:
    externals: {
        lodash: {
            commonjs: "lodash"
        }
    }
    ...
}
```
4.1. externals: ["lodash"]，打包时，如果遇到了lodash这个库，就忽略它，不要打包到代码中。但是其他人使用打包好的库时，要手动引入"lodash"这个库，而且名字要叫`lodash`.
``` javascript
import lodash from "lodash"
import library from "library"
...
```

4.2. externals的对象写法
``` javascript 
    externals: {
        lodash: {
            root: "_",
            commonjs: "lodash"
        }
    }
```
* 表示如果库在commonjs（node）的环境下使用, lodash被加载时，变量的名字必须叫lodash
``` javascript
const lodash = require('lodash') // note: 正确
const _ = require('lodash')      // note: 错误

const library = require('library')
```
* 如果是用script标签引入的这个库，要求:全局必须注入一个叫"_"的全局变量，这个库才能正常使用。

5. 将库文件发布到npm
* 修改package.json中main字段为打包后的文件路径(e.g. ./dist/libray.js)
* npm adduser
* npm publish