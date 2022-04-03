---
title: webpack4-example-ts&devServer-proxy
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

## devServer-proxy
The dev-server makes use of the powerful http-proxy-middleware package.
``` javascript
module.exports = {
  //...
  devServer: {
    proxy: {
      '/api': {
        target: 'https://localhost:3000',
        secure: false,
        bypass: function (req, res, proxyOptions) {
          if (req.headers.accept.indexOf('html') !== -1) {
            console.log('Skipping proxy for browser request.');
            return '/index.html';
          }
        },
        pathRewrite: { '^/api': '' },
        changeOrigin: true
      },
    },
  },
};
```
``` javascript
module.exports = {
  //...
  devServer: {
    proxy: [
      {
        context: ['/auth', '/api'],
        target: 'http://localhost:3000',
      },
    ],
  },
};
```
``` javascript
module.exports = {
  //如果想把根目录进行代理，需要设置 devMiddleware.index
  devServer: {
    devMiddleware: {
      index: false, // specify to enable root proxying
    },
    proxy: {
      context: () => true,
      target: 'http://localhost:1234',
    },
  },
};
```
* target: 请求被代理到的服务器地址
* secure: 后台接口是https协议的，这个时候需要开启这个配置项（http => https）
* pathRewrite: 改写代理路径。按照上述的配置，http://localhost:8080/api/user会被代理到https://localhost:3000/user
* bypass: 对代理请求进行拦截
* changeOrigin: 有的网站会对请求的来源（origin）进行限制，设置此选项可以避免这种情况
* context: 可以设置多个请求代理到同一个地址
* devMiddleware.index: 设置根路径进行代理
* 更多设置参考 https://github.com/chimurai/http-proxy-middleware#http-proxy-options