---
title: webpack1-basic
date: 2022-03-06 17:40:10
tags: webpack
---
# 基础篇
## 1.啥是webapck
<!-- more -->
1. 模块打包工具（Modules Bundle），可识别
* ES Moudule模块的引入(import)导出(export)工具、
* CmmonJS模块引入规范（NodeJs的导入导出，require、module.exports）、
* AMD、CMD等语法
2. js 模块打包工具 ---升级进化---> png, css打包工具
3. webpack官网，CONCEPTS-Modules, API-Module Variables

## 2.webpack的安装
* 安装nodejs
* 创建项目
``` bash
npm init
```
* (第一种)全局安装webpack-cli
``` bash
> npm install webpack-cli -g
> webpack -v 检查版本
```
* (第二种)项目局部安装
``` bash
> npm install webpack webpack-cli -D(--save-dev)
```
> * webpack：直接输入webpack 会去全局环境中检索webpack命令
> * ```npx webapck```：在当前目录的node_modules中检索webpack

## 3.webpack.config.js
1. 文件配置项(此文件不存在时,会有默认的配置项)
* 每一个文件的引入都是作为一个module（模块）
* entry: 打包入口
* output: 打包的文件的配置
    * filename: 输出文件的名称
    * path: 输出文件放置在哪个文件夹（绝对路径）

2. 使用其他文件作为打包配置文件
``` bash
npx webpack --config webpackconfig.js
```
3. npm script
```
package.json:
scripts: { "bundle": "webpack" }
```
* 运行npm run bundle时，执行的是scripts下面的bundle,会先调用npx webpack,如果没有会去运行webpack（寻找全局的webpack）
4. webpack-cli
* 使得我们可以在命令行中使用```webpack```这个命令
5. webpack官网，DOCUMENTATION-Getting Started

## 4.打包输出内容浅析
``` bash
➜  webpack01 git:(master) ✗ npm run bundle

> webpack01@1.0.0 bundle /Users/noodles/Documents/project/noodles/webpack_memo/webpack01
> webpack

asset bundle.js 143 bytes [emitted] [minimized] (name: main)
orphan modules 197 bytes [orphan] 1 module
./src/index.js + 1 modules 328 bytes [built] [code generated]

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value.
Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/

webpack 5.70.0 compiled with 1 warning in 217 ms
```
* entry: '' === entry: { main: '' }
* webpack.config.js中mode默认是'production'
* mode: 'production'  代码不会被压缩
  mode: 'development' 代码不会被压缩