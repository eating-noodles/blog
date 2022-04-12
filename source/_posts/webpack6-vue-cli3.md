---
title: webpack6-vue_cli3&summary
date: 2022-04-12 17:58:18
tags: webpack; vue-cli
---
# vue-cli 3
<!-- more -->
* vue-cli对webpack进行了封装，要按照vue-cli暴露的接口来进行配置
vue.config.js
``` javascript
const path = require('path')

module.exports = {
    outputDir: 'noodles',
    // note: 可以写原生的webpack配置
    configureWebpack: {
        devServer: {
            contentBase: [path.resolve(__dirname, "static")]
        }
    }
}
```

# 最后
要学会查文档
* 记住webpack的基础知识点/脉络记住，loader是什么，entry是什么，module中的rule怎么配置，plugin是什么。其他的遇到再查阅官方文档.
* `GUIDES`: 查找解决某个问题的方向时，查看官方文档的Guides。比如忘了代码分割怎么做了，就看Guides的code splitting的内容.
* `CONCEPTS`: 是基础的核心概念.
* `CONFIGURATION`: 关于webpack的配置项不知道什么意思或者怎么配置的时候，可以看configuration.
* `API`: 里面有很多webpack、loader、plugin暴露的api.
* `LOADERS`: 查看loaders.
* `PLUGINS`: 查看plugins.
