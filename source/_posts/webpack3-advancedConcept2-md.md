---
title: webpack3-advancedConcept2-代码分割和配置.md
date: 2022-03-18 21:21:03
tags: webpack
---
# 高级概念篇2
## 1. webpack和Code Splitting(代码分割)
### 两者的关系
* 假设业务代码1Mb,工具库1Mb，在打包文件不做压缩的情况下，
1. 首次访问页面打包出来的main.js会有2mb，这样就容易造成下面的问题:
<!-- more -->
    * 打包文件会很大，加载时间会长
    * 我们修改了代码，重新访问我们的页面，又要加载2mb文件
2. 第二种：(Code Splitting)
    * 将main.js被拆分为loadsh.js(1mb), main.js(1mb)
    * 当页面业务逻辑发生变化时，只要加载main.js即可(1mb)
### Code Splitting
``` javascript
{
    optimization: {
        splitChunks: {
            chunks: 'all'
        }
    }
}

```
* 代码分割的思想和webpack无关，我们甚至可以自己去实现代码分割，但是webpack的出现，让代码分割变得更方便
* webpack中实现代码分割，两种方式：
    1. 同步代码：只需要在webpack.common.js中配置optimization
    2. 异步代码（import().then()): 异步代码，无需任何配置，会自动进行代码分割

## 2. splitChunksPlugin
### magic-comment
``` javascript
/* webpackChunkName:"xxx" */
```
### 配置项
``` javascript
module.exports = {
  //...
  optimization: {
    splitChunks: {
      chunks: 'async',
      minSize: 20000,
      minRemainingSize: 0,
      minChunks: 1,
      maxAsyncRequests: 30,
      maxInitialRequests: 30,
      enforceSizeThreshold: 50000,
      cacheGroups: {
        defaultVendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10,
          reuseExistingChunk: true,
          filename: 'vendors.js'
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
  },
};
```
* chunks: 
    * 'async': 做代码分割时,只对异步代码生效
    * 'initial': 打包同步代码
    * 'all': 同步异步代码都进行代码分割,但是同步代码分割要看cacheGroup配置项。
        * 同步代码分割时: 1.监测到chunks值为'all'，打包同步代码;
            2.然后走到cacheGroup.defaultVendors.test进行校验，看同步引入的库是不是在配置项配置的目录下;
            3.如果符合,就会把引入的文件打包到defaultVendors的这么一个组里面;
            4.如果有filename字段，打包后文件的名称会按照配置的名称改变

* cacheGroups：打包分组,和chunks字段配合使用
* minSize: 20000;
    * 引入的库如果大于配置20kb, 才会代码分割
    * 设置为0 webpack 5 not working???
* minChunks: 1
    * 打包生成的chunks(被打包的模块中)中，至少被引用了1次，才会被代码分割
* maxAsyncRequests： 30
    * 前30个库会进行代码分割，30以后的库就不会进行代码分割了
* maxInitialRequests: 30
    * 入口文件引入的库，最多分割30个js文件
* cacheGroup
    * 代码分割的缓存组，可配置各个规则来指定哪些模块打包到哪些组(文件)里面，一个组对应到打包后的一个文件
* cacheGroup[xxx].priority: -10
    * 对于满足多个规则的模块，根据priority来决定打包到哪个组里面。值越大，优先级越高。
* cacheGroup[xxx].reuseExistingChunk: true;
    * 为true时,如果被打包的一个模块a中用到了模块b, 而且模块b已经被打包过了, 那么就直接使用之前被打包过的b模块,不对b进行重新打包

