---
title: webpack3-advancedConcept2-代码分割.md
date: 2022-03-18 21:21:03
tags:
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