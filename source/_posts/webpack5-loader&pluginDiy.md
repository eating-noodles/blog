---
title: webpack5-loader&pluginDiy
date: 2022-04-06 22:55:10
tags: webpack
---
# 自定义loader
## 基本loader
* 定义loader
<!-- more -->
``` javascript
// note: 不能是箭头函数
// note: source这里是源代码
module.exports = function (source) {
    return source.replace('noodles', 'noodlesNan')
}
```

* 使用工程中的loader
``` javascript
const path = require('path')

module.exports = {
    ...,
    module: {
        rules: [{
            test: /\.js/,
            use: [path.resolve(__dirname, './loaders/replaceLoader.js')]
        }]
    },
    ...
}
```
## 带参数的loader
* webpack.config.js
``` javascript
const path = require('path')

module.exports = {
    ...,
    module: {
        rules: [{
            test: /\.js/,
            use: {
                loader: path.resolve(__dirname, './loaders/replaceLoader.js'),
                options: {
                    name: 'foo'
                }
            }
        }]
    },
    ...
}
```
* replaceLoader.js
``` javascript
// note: 不能是箭头函数,箭头函数会导致this指向错误
// note: source这里是源代码
module.exports = function (source) {
    console.log(this.query)
    // output: { name: 'foo' }
    return source.replace('noodles', this.query.name)
}
```
配置文件中的`option`中的参数，在loader中可以通过`this.query`读取到
webpack5: DOCUMENTATON-API-LOADER INTERFACE
## loader-utils vs this.getOptions
Since webpack 5, this.getOptions is available in loader context. It substitutes getOptions method from loader-utils.
## this.callback
通过callback可以返回多个参数（比如处理后的源码，sourceMap，其他参数等）
A function that can be called synchronously or asynchronously in order to return multiple results.
``` javascript
module.exports = function (source) {
    const result = source.replace('noodles', this.query.name)
    this.callback(null, result)
}
```
## loader中进行异步操作 this.async
Tells the loader-runner that the loader intends to call back asynchronously. Returns this.callback.
Example: replaceLoader.js
``` javascript
module.exports = function (source) {
    const callback = this.async()

    setTimeout(() => {
        const result = source.replace('noodles', this.query.name)
        callback(result)
    }, 1000)
}
```
## 使用多个loader && resolveLoaders
webpack.config.js
``` javascript
module.exports = {
    ...,
    resolveLoader: {
        modules: ['node_modules', './loaders']
    },
    module: {
        rules: [{
            test: /\.js/,
            use: [{
                loader: 'replaceLoader',
                options: {
                    name: 'foo'
                },
            }, {
                loader: 'replaceLoaderAsync',
                options: {
                    name: 'foo'
                },
            }]
        }]
    },
    ...
}
```
## 使用场景
* 异常捕获
* 国际化