---
title: webpack2-coreConcept5-hmr&babel
date: 2022-03-16 20:53:19
tags: webpack
---
# 核心概念篇5
## 1. 热模块更新HMR
* Since webpack-dev-server v4.0.0, Hot Module Replacement is enabled by default.
<!--more-->
``` javascript
module.exports = {
    mode: 'development',
    devtool: 'cheap-module-source-map',
    entry: {
        main: './src/index.js',
    },
    devServer: {
        static: './dist',
        open: true,
        hot: 'only'
        // hot: false
    },
}
```
``` javascript
...
if (module.hot) {
    module.hot.accept('./number.js', () => {
        document.body.removeChild(document.getElementById('number'))
        number()
    })
}
```

## 2. babel
1. 业务代码，webpack-rules配置示例:
``` javascript
{
    test: /\.m?js$/,
    exclude: /node_modules/,
    use: {
        loader: "babel-loader",
        options: {
            presets: [['@babel/preset-env', {
                targets: {
                    chrome: "67",
                },
                useBuiltIns: "usage",
            }]]
        }
    }
}
```
* ```babel-loader``` => 帮助webpack打包的工具
* ```@babel/core``` => 帮助babel识别js代码  =》 AST(抽象语法树) => 转换代码
* ```@babel/preset-env``` => babel-loader处理js文件时,他是babel和webpack通信的桥梁（将babel和webpack进行了打通）,但是并不会把es12等js最新语法翻译为es5等浏览器已经支持的语法,而@babel/preset-env就是进行翻译的模块（包含了最新语法的翻译规则）
    * ```useBuiltIns: "usage";```  表示做polyfill填充时,只补充业务代码用到新语法特性的实现
    * ```targets: { chrome: "67" };``` 表示项目会运行在版本大于67的chrome浏览器中,进行polyfill填充时会以此为参考来判断填充哪些特性的实现
* ```@babel/polyfill```：某些旧版浏览器可能没有map、promise这些方法或者对象的定义，所以我们需要对这些定义进行补充翻译，polyfill就是做这件事情的

2. 封装组件库、类库
业务代码，webpack-rules配置示例: 
``` javascript
{
    // note: 业务代码的配置
    // presets: [
    //     ['@babel/preset-env', {
    //         targets: {
    //             "chrome": "67"
    //         },
    //         useBuiltIns: "usage",
    //     }]
    // ]
    // note: 类库代码的配置
    "plugins": [
        ["@babel/plugin-transform-runtime", {
            "absoluteRuntime": false,
            "corejs": 2,
            "helpers": true,
            "regenerator": true,
            "version": "7.0.0-beta.0"
        }]
    ]
}
```
* 封住组件库、类库的时候，使用polyfill不合适，以为polyfill是将实现挂载至全局变量中的，会造成污染
* ```@babel/plugin-transform-runtime``` 以闭包形式引入
* ```@babel/runtime```
* ```@babel/runtime-corejs2```

* 可以新建一个```.babelrc```文件,并将配置拿出来放到里面