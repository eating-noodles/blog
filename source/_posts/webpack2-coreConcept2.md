---
title: webpack2-coreConcept2
date: 2022-03-15 20:33:59
tags:
---
# 核心概念篇2
## 1. Plugins
* plugin可以在webpack运行到某个时刻时，帮你做一些事情
<!-- more -->

### 1.1. HtmlWebpackPlugin
* 会在打包结束后，自动生成一个html文件，并把打包生成的js自动引入到这个html文件
* 参数template: 可以指定模板来生成html

### 1.2. CleanWebpackPlugin
* 自动清理打包生成的文件夹

## 2. entry & output
### 2.1 entry
* 打包入口文件
* 示例：
    ``` javascript
        entry: {
            main: './src/index.js'
        },
    ```
    * 表示要打包入口为'./src/index.js'
    * 打包生成的默认文件名称为main.js(可以在output中filename来改变输出文件名称)

### 2.2 output
* 示例:
    ``` javascript
    {
        entry: {
            main: './src/index.js',
            sub: './src/index.js',
        },
        ...,
        output: {
            filename: '[name].js',
            path: path.resolve(__dirname, 'dist')
    }
    ```
    * output.filename中```[name]```为占位符，分别对应到entry中的main和sub
    * 会打包生成两个文件:main.js、sub.js
* output.publicPath
    * 引入到html中的js文件路径,前面都会加上配置的publicPath
    * 输出示例:
    ``` html
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Document</title>
        <script defer src="http://cdn.com/main.js"></script><script defer src="http://cdn.com/sub.js"></script></head>
        <body>
            <p>这是网页</p>
            <div id="root"></div>
        </body>
        </html> 
    ```
* Documentation-Configuration-Output/Entry and Context
* Documentation-Guides-Output Management
* Documentation-Plugins-HtmlWebpackPlugin
