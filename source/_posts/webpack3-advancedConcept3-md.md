---
title: webpack3-advancedConcept3-css分割和压缩.md
date: 2022-03-27 18:02:00
tags:
---
# 高级概念篇3
## 1. Lazy Loading
<!-- more -->
import异步加载所需要的模块
``` javascript
async function getComponent() {
    const { default: _ } = await import( /* webpackChunkName:"loadsh"*/ 'lodash')
    var element = document.createElement('div')
    element.innerHTML = _.join(['1', '2'], '-')
    return element
}

document.addEventListener('click', () => {
    getComponent().then(el => {
        document.body.appendChild(el)
    })
})
```
## 2. chunk
打包生成的js文件，每一个js文件称作一个chunk

## 3. Css文件分割
* webpack-config中，entry配置项的入口文件打包后的命名会走output.filename配置项;其他被引入的间接文件打包时会走output.chunkFilename配置项

### MiniCssExtractPlugin
将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件，并且支持 CSS 和 SourceMaps 的按需加载。
``` javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  plugins: [new MiniCssExtractPlugin({
                filename: '[name].css',
                chunkFilename: '[name].chunk.css'
            })],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
};
```
* 将style-loader更换为MiniCssExtractPlugin.loader
* ```样式不生效时，看一下webpack是否配置了optimization.usedExports是否为true（开启了TreeShaking）, 去package.json中将"*.css"添加到sideEffects属性中。```
* 直接被index.html引用的文件，名称规则会按照plugin中filename的配置规则来; 间接被引用的文件会按照chunkFilename配置的规则来

### css-minimizer-webpack-plugin
优化和压缩css代码

### 其他功能
* 基于入口提取 CSS
* 提取所有的 CSS 到一个文件中
...
* 更多示例：https://webpack.docschina.org/plugins/mini-css-extract-plugin/#examples
