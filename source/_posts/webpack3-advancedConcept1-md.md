---
title: webpack3-advancedConcept1.md
date: 2022-03-16 22:52:11
tags:
---
# 高级概念篇
## 1. Tree Shaking
* 只支持ES Module的引入（import的语法）静态引入
* webpack.config.js
    * 开发环境:
    <!-- more -->
    ``` javascript
        {
            optimization: {
                usedExports: true
            }
        }

        // note: package.json
        sideEffects: ["@babel/polly-fill", "*.css"],
    ```
    1. ```sideEffects```，可以设置对哪些模块不进行tree shaking，当它为false的时候,表示对所有的模块进行tree shaking,没有要特殊处理的
    2. 开发模式下，配置了tree shaking，打包的文件不会真正的删除不用的代码，会有注释提醒；生产环境下打包的文件会真正的删除不用的代码

    * 生产环境不需要配置optimization，要配置sideEffects

## 2.Development和Production模式的区分打包
* 开发环境：webpack.dev.js
``` javascript
const merge = require("webpack-merge")
const commonConfig = require("./webpack.common")

const devConfig = {
    mode: 'development',
    devtool: 'eval-cheap-module-source-map',
    devServer: {
        static: './dist',
        open: true,
        hot: 'only'
        // hot: false
    },
    optimization: {
        usedExports: true
    },
}

module.exports = merge(commonConfig, devConfig)
```
* 生产环境： webpack.prod.js
``` javascript
const {merge} = require("webpack-merge")
const commonConfig = require("./webpack.common")

const prodConfig = {
    mode: 'production',
    devtool: 'cheap-module-source-map',
}

module.exports = merge(commonConfig, prodConfig)
```
* 公共配置：webpack.common.js
``` javascript
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const {
    CleanWebpackPlugin
} = require('clean-webpack-plugin')

module.exports = {
    entry: {
        main: './src/index.js',
    },
    module: {
        rules: [{
                test: /\.(ttf|woff|woff2)$/,
                type: 'asset/resource',
            },
            {
                test: /\.scss$/,
                use: [
                    'style-loader',
                    {
                        loader: 'css-loader',
                        options: {
                            importLoaders: 2,
                        }
                    },
                    'sass-loader',
                    'postcss-loader'
                ],
            },
            {
                test: /\.css$/,
                use: [
                    'style-loader',
                    'css-loader',
                    'postcss-loader'
                ],
            },
            {
                test: /\.m?js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader"
                }
            }
        ]
    },
    plugins: [new HtmlWebpackPlugin({
            template: 'src/index.html'
        }),
        new CleanWebpackPlugin(),
    ],
    output: {
        // punlicPath: '/',
        filename: '[name].js',
        path: path.resolve(__dirname, '../dist')
    }
}
```