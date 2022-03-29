---
title: webpack4-example-PWA
date: 2022-03-29 22:15:00
tags: webpack, PWA
---
# 实际配置案例讲解
## PWA (progressive Web Application)
* 假如访问一个网站，第一次访问成功；第二访问时，网站服务器挂掉了。pwa应用在本地会有一份缓存，在服务器挂掉的时候，仍然可以展示出以前访问过的页面。
<!-- more -->
### workbox-webpack-plugin
1. 配置和使用
webpack.config.js:
``` javascript
const WorkboxWebpackPlugin = require('workbox-webpack-plugin')

const prodConfig = {
    mode: 'production',
    ...,

    plugins: [new WorkboxWebpackPlugin.GenerateSW({
        clientsClaim: true,
        skipWaiting: true
    })]
}
```
index.js: 注册service-worker
``` javascript
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('/service-worker.js')
            .then(registration => {
                console.log('service-worker registed')
            }).catch(err => {
                console.log('service-worker register error')
            })
    })
}
```
2. 
* 底层使用的是service-worker,打包出来的文件会多出service-worker.js、workbox-f0806d7b.js两个文件。
``` log
  asset runtime.9025c66b4de7940e35d1.js 4.93 KiB [emitted] [immutable] (name: runtime)
  asset main.8bd08b31f339ba74f615.js 391 bytes [emitted] [immutable] (name: main)
asset workbox-f0806d7b.js 14 KiB [emitted] [minimized]
asset service-worker.js 869 bytes [emitted] [minimized]
asset index.html 404 bytes [emitted]
```