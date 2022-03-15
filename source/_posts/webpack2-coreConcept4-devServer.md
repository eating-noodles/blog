---
title: webpack2-coreConcept4-devServer
date: 2022-03-15 22:24:24
tags:
---
# 核心概念篇4
## devServer
### 1. 监听文件的方式
1. webpack --watch
``` javascript
  "scripts": {
    "watch": "webpack --watch"
  },
```
<!-- more -->
* 代码变化->webpack重新打包->刷新页面，显示结果

2. webpack-dev-server
``` javascript 
module.exports = {
    devServer: {
        static: './dist',
        open: true
    },
}
```
* devServer.static
    * 修改代码,自动重新打包,自动刷新浏览器页面
    * 安装webpack-dev-server,运行一个web http服务器 
    * http服务的映射目录为./dist
    * 命令行运行: webpack-dev-server
* devServer.open: true
    * 服务运行起来,自动在浏览器打开页面
* devServer.proxy
    * 跨域请求代理转发

3. 自己实现server创建服务器-在node中使用webpack
* 借助express,webpack-dev-middleware
* 将webpack的配置文件传递给webpack的cli(客户端),拿到返回结果(一个编译器)
* 将得到的编译器传递给webpackDevMiddleware,并应用到node服务中即可
* Documentation-API-Node Interface
``` javascript
const express = require('express')
const webpack = require('webpack')
const webpackDevMiddleware = require('webpack-dev-middleware')
const config = require('./webpack.config.js')

// note:编译器执行一次，会重新打包一次代码
const complier = webpack(config)

const app = new express()
app.use(webpackDevMiddleware(complier, {}))

app.listen(3000, () => {
    console.log('server is running')
})
```

* Documentation-Guides-Development
* Documentation-Configuration-DevServer