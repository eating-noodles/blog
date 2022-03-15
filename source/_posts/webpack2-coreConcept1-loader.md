---
title: webpack2-coreConcept1-loader
date: 2022-03-10 21:48:12
tags:
---
# 核心概念篇
## 1.啥是Loader
<!-- more -->
* webpack可以打包图片等，但是需要合适的loader(file-loader)
* 在webpack.config.js中配置 module.rules(Array)
* file-loader:
    * 把静态资源一定到打包目录下
    * 讲静态资源名称返回给变量
* vue文件也需要vue-loader
> webpack不能识别非js结尾的文件，配置loader规则后，loader提供方案和规则给webpack进行识别打包

## 2.静态资源打包-图片
1. file-loader
* module.rules.[].options:
    * name: '[name]_[hash].[ext]' // 占位符，更多预置占位符可参考loader使用说明
    * outputPath: 'images/' // 文件打包输出的位置
2. url-loader
* 将图片转换成一个base64的字符串，并嵌入代码中
* 配置项
    * limit: 2048  大于2kb的时候打包成文件,小于2kb的时候嵌入页面

## 3.静态资源打包-样式
1. css-loader
* 分析css文件之间的关系，进行合并关联
* 配置项
    * importLoaders: 2, 通过import嵌套引入的scss文件，也要执行loaders
    * modules: true 开启```css模块化```打包
        * ```javascript
            // note: 模块化css,引入的样式不会影响到createAvatar中图片的样式
            import style from './index.scss'
            ...
            img.classList.add(style.avatar)
            ```
            ``` css
            // 打包结果
            body .sr2ElEIZriKWsZCEHdXE {
                width: 50px;
                height: 50px;
                transform: translate(100px, 100px);
                box-shadow: 10px 10px 5px #888888;
                -o-object-fit: fill;
                object-fit: fill;
            }
            ```

2. style-loader
* 将css-loader得到的东西挂载到页面的<head></head>部分

3. sass-loader node-sass
* 处理scss样式文件

4. postcss-loader
* 
* plugins:
    * autoprefixer: css属性前面自动增加厂商前缀

5. loader的调用顺序
``` javascript
    {
        test: /\.scss$/,
        use: [
            'style-loader',
            'css-loader',
            'sass-loader',
            'postcss-loader'
        ],
    }
```
* 从下往上，从右向左（先执行sass-loader,然后是css-loader,然后是style-loader）
* 前者的输出是后者的输入

6. 打包字体文件
``` javascript
{
    test: /\.(ttf|woff|woff2)$/,
    type: 'asset/resource',
},
```
7. Documentation-Guides-Asset Management;
   Documentation-Loaders-scss-loader/style-loader/css-loader;