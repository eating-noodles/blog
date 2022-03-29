---
title: webpack4-example-ts
date: 2022-03-29 22:47:52
tags: webpack, ts
---
# 实际配置案例讲解
## ts
1. 安装typescript, ts-loader
2. webpack配置loader使用规则
<!-- more -->
``` javascript
const path = require('path')

module.exports = {
    ...,
    module: {
        rules: [{
            test: /\.tsx?$/,
            use: 'ts-loader',
            exclude: /node_modules/
        }]
    },
}
```
3. 配置tsconfig.json
简单配置示例：
``` json
{
    "compilerOptions": {
        // note: 编译后文件的输出目录
        "outDir": "./dist",
        // note: ts文件中模块的导入规则esm
        "module": "es6",
        // note: 将ts编译成符合es5语法的代码
        "target": "es5",
        // note: 允许在ts文件中引用js文件
        "allowJs": true
    }
}
```

4. ts文件中引入lodash
* 安装`lodash`和`@types/lodash`(类型文件)
``` typescript
import * as _ from 'lodash';
...

```