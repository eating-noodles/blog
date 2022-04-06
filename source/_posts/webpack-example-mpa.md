---
title: webpack-example-mpa
date: 2022-04-06 21:44:55
tags: webpack
---
# 实际配置案例讲解

## 多页面应用打包
* 配置多个入口文件
<!-- more -->
``` javascript
module.exports = {
  ...,
  entry: {
    main: './src/index.js',
    list: './src/list.js'
  },
}
```
* 配置HtmlWebpackPlugin, 增加多个html
``` javascript
const plugin = [
  new HtmlWebpackPlugin({
    template: 'src/index.html',
    filename: 'index.html',
    chunks: ['vendors', 'main']
  }),
  new HtmlWebpackPlugin({
    template: 'src/index.html',
    filename: 'list.html',
    chunks: ['vendors', 'list']
  })
]
```
* 自动检测增加html
``` javascript
const path = require('path');
const fs = require('fs')
const HtmlWebpackPlugin = require('html-webpack-plugin');
const {
  CleanWebpackPlugin,
} = require('clean-webpack-plugin');
const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');
const webpack = require('webpack');

const makePlugins = (configs) => {
  const plugins = [new CleanWebpackPlugin()]

  // note: start 这块逻辑
  Object.keys(configs.entry).forEach(item => {
    plugins.push(new HtmlWebpackPlugin({
      template: 'src/index.html',
      filename: `${item}.html`,
      chunks: ['vendors', item]
    }), )
  })
  // note: end

  const files = fs.readdirSync(path.resolve(__dirname, './dll'))
  // console.log(files)
  files.forEach(file => {
    if (/.*\.dll.js/.test(file)) {
      plugins.push(new AddAssetHtmlWebpackPlugin({
        filepath: path.resolve(__dirname, './dll', file),
        publicPath: './',
      }))
    }
    if (/.*\.manifest.json/.test(file)) {
      plugins.push(new webpack.DllReferencePlugin({
        manifest: path.resolve(__dirname, './dll', file),
      }))
    }
  })

  return plugins
}


const configs = {
  mode: 'development', // development\production 模式
  entry: {
    index: './src/index.js',
    list: './src/list.js'
  },
  module: {
    rules: [{
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
    }, ],
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, 'dist'),
  },
};

configs.plugins = makePlugins(configs)

module.exports = configs
```