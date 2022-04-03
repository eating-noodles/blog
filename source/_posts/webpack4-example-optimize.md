---
title: webpack4-example-optimize
date: 2022-04-03 18:02:00
tags: webpack
---
# 实际配置案例讲解

## 加快webpack打包速度
* 跟上技术的迭代（Node,Npm,Yarn）尽量用新版本
<!-- more -->
* 在尽可能少的模块上应用Loader
rules.exclude, rules.include指定loader的作用目录, 减少loader的重复使用
* plugin尽可能精简并确保可靠
区分不同环境来进行插件使用和配置
* resolve参数合理配置
    ``` javascript
    module.exports = {
        resolve: {
            extensions: ['js', 'jsx'],
            mainFiles: ['index', 'child'],
            alias: {
                dell: path.resolve(__dirname, '../src/child')
            }
        }
    }
    ```
    按照上面的配置：
    * 对于`import Child from './child/child'`, 会先去找child目录下的child.js文件，找不到时，会再找child.jsx文件
    * 对于`import Child from './child/'`, 会先去找child目录下的index.js文件，找不到时，会再找child.js文件
    * 对于`import Child from 'dell'`, 这么配置以后，实际访问的是`path.resolve(__dirname, '../src/child')`
    * 能写文件后缀名，尽量写，不要滥用resolve 
* 使用dllPlugin提高打包速度-其实就是使用预处理+读取缓存 (Maybe OutDate)
    * 将node_modules中的库单独打包
    ``` javascript
    module.exports = {
        entry: {
            vendors: ['react', 'react-dom', 'lodash']
        },
        output: {
            filename: '[name].dll.js',
            path: path.resolve(__dirname, '../dll'),
            library: '[name]'
        }
    }
    ```
    * 引入打包的第三方模块，add-asset-html-webpack-plugin
    > 第三方模块只打包一次
    ``` javascript
    const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');

    module.exports = {
        ...,
        plugins: [

            new AddAssetHtmlWebpackPlugin({
                filepath: path.resolve(__dirname, './dll/vendors.dll.js'),
                publicPath: './',
            }),

        ],
    };
    ```
    * 项目中使用第三方模块时,要去使用dll文件引入 （webpack.Dllplugin,webpack.DllReferencePlugin）
    打包配置文件：
    ``` javascript
    const path = require('path');
    const webpack = require('webpack')

    module.exports = {
    entry: {
        vendors: ['react', 'react-dom', 'lodash'],
    },
    output: {
        filename: '[name].dll.js',
        path: path.resolve(__dirname, './dll'),
        library: '[name]',
    },
    plugins: [
        new webpack.DllPlugin({
            // note: 表示对生成的vendors库进行分析
            name: '[name]',
            path: path.resolve(__dirname, './dll/[name].manifest.json')
        })
    ]
    };
    ```
    项目打包文件：添加```webpack.DllReferencePlugin```插件
    ``` javascript
    const webpack = require('webpack');
    ...
    plugins: [
        new HtmlWebpackPlugin({
            template: 'src/index.html',
        }),
        new CleanWebpackPlugin(),
        new AddAssetHtmlWebpackPlugin({
            filepath: path.resolve(__dirname, './dll/vendors.dll.js'),
            publicPath: './',
        }),
        new webpack.DllReferencePlugin({
            context: __dirname,
            manifest: require('./dll/vendors.manifest.json')
        })
    ],
    ```
    1. 首先，生成dll文件时，要生成一个映射（第三方模块和dll文件的映射）。对生成的dll库进行分析([name]是占位符，表示dll库的名字，在这里代表vendors)，并把分析的结果放到'./dll/[name].manifest.json'文件里面
    2. 使用webpack打包时，会结合上一步的manifest.json文件和挂载到全局的vendors变量对源代码进行分析，一旦发现用的库存在于我们的dll文件中，就会使用我们的dll文件(从全局变量vendors中拿)而不是node_modules中的库
    * 动态读取映射文件并添加到plugins配置中
    ```  javascript
    const path = require('path');
    const fs = require('fs')
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const {
    CleanWebpackPlugin,
    } = require('clean-webpack-plugin');
    const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');
    const webpack = require('webpack');

    const plugin = [
        new HtmlWebpackPlugin({
            template: 'src/index.html',
        }),
        new CleanWebpackPlugin()
    ]
    const files = fs.readdirSync(path.resolve(__dirname, './dll'))
    console.log(files)
    files.forEach(file => {
    if (/.*\.dll.js/.test(file)) {
        plugin.push(new AddAssetHtmlWebpackPlugin({
        filepath: path.resolve(__dirname, './dll', file),
        publicPath: './',
        }))
    }
    if (/.*\.manifest.json/.test(file)) {
        plugin.push(new webpack.DllReferencePlugin({
            manifest: path.resolve(__dirname, './dll', file),
        }))
    }
    })

    module.exports = {
        ...,
        plugins: plugin
        ...
    }
    ```

* 控制包文件大小
按需引入、tree shaking、split code
* thread-loader, parallel-webpack, happypack多进程打包
可以利用node多线程、多cpu打包来提升打包速度
* 合理使用sourceMap
sourceMap越详细，打包越慢
* 结合stats分析打包结果
* 开发环境内存编译
* 开发环境无用插件剔除
区分生产和开发环境的webpack plugin和配置