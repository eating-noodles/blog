---
title: webpack3-advancedConcept4-caching&shimming&webpack--env
date: 2022-03-28 21:40:26
tags: webpack
---
# 高级概念篇4
## webpack和浏览器缓存（caching）
<!-- more -->
* [contenthash]占位符,根据文件内容生成hash,文件内容不变,hash值不变
``` javascript
const prodConfig = {
    mode: 'production',
    // devtool: 'cheap-module-source-map',
    output: {
        // punlicPath: '/',
        filename: '[name].[contenthash].js',
        path: path.resolve(__dirname, '../dist'),
        chunkFilename: '[name].[contenthash].chunk.js'
    }
}
```
* 对于webpack4低版本配置时，如果contenthash不生效，可以加上下面的配置
``` javascript
    optimization: {
        runtimeChunk: {
            name: 'runtime'
        },
    }
```
* 打包后的代码，main.js等放的是业务代码，vendors.xxx.js等里面放的是库代码，但是这两者也有关系，叫做manifest(包与包之间的关系)，嵌套存在于各自的js文件中。
在webpack中，如果配置了optimization.runtimeChunk.name='runtime'，打包时这个关系就会单独生成一个叫runtime.js的文件进行存储。

## shimming（垫片）
webpack compiler 能够识别遵循 ES2015 模块语法、CommonJS 或 AMD 规范编写的模块。然而，一些 third party(第三方库) 可能会引用一些全局依赖（例如 jQuery 中的 $）。因此这些 library 也可能会创建一些需要导出的全局变量。这些 "broken modules(不符合规范的模块)" 就是 shimming(预置依赖) 发挥作用的地方。
### ProvidePlugin
Automatically load modules instead of having to import or require them everywhere.
自动为第三方模块用到的库添加导入语句(一般是一些比较老的库).
``` javascript
const webpack = require("webpack")

module.exports = {
    plugins: [
        new webpack.ProvidePlugin({
            $: 'jquery',
            _join: ['lodash', 'join']
        })
    ],
}
```
* 上面的配置会为用到"$"，而且没有导入jquery的模块，自动导入jquery

### imports-loader
* 模块里面的this，默认指向它自身
* import-loader可以改变模块的this指向，让其指向window
* ？？？语法写法


* https://webpack.docschina.org/guides/

## 环境变量的使用
package.json:
``` javascript
  "scripts": {
    "build": "webpack --env production=true --config ./build/webpack.common.js",
  },
```
webpack.common.js:
``` javascript
module.exports = (env) => {
    // note: 这里拿到的env.production的值是字符串"true"
    if (env && env.production) {
        return merge(commonConfig, prodConfig)
    } else {
        return merge(commonConfig, devConfig)
    }
}
```