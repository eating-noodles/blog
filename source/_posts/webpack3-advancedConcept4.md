---
title: webpack3-advancedConcept4-caching&shimming
date: 2022-03-28 21:40:26
tags:
---
# 高级概念篇4
## webpack和浏览器缓存（caching）
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

## shimming

