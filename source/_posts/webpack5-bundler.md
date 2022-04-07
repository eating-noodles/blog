---
title: webpack5-bundler
date: 2022-04-07 21:30:36
tags: webapck
---
# bundler(打包工具)源码编写

## 模块分析
<!-- more -->
* 读取模块内容
bundler.js
``` javascript
const fs = require('fs')

const moduleAnalyser = (filename) => {
    const content = fs.readFileSync(filename, 'utf-8');
    console.log(content)
}

moduleAnalyser('./src/index.js')
```
* 获取模块的导入依赖
1. `@babel/parser`, 可以用来分析代码
``` javascript
const fs = require('fs')
const parser = require('@babel/parser')

const moduleAnalyser = (filename) => {
    const content = fs.readFileSync(filename, 'utf-8');

    // note: 输出的内容是ast,抽象语法树
    const ast = parser.parse(content, {
        sourceType: 'module'
    })
    console.log(ast.program.body)
}

moduleAnalyser('./src/index.js')
```
`ast.program.body`就是代码分析的结果(代码转换成了js对象),输出为：
``` javascript
[
  Node {
    // note: 表示为导入声明
    type: 'ImportDeclaration',
    start: 0,
    end: 32,
    loc: SourceLocation {
      start: [Position],
      end: [Position],
      filename: undefined,
      identifierName: undefined
    },
    specifiers: [ [Node] ],
    source: Node {
      type: 'StringLiteral',
      start: 20,
      end: 31,
      loc: [SourceLocation],
      extra: [Object],
      value: './message'
    }
  },
  Node {
    type: 'ExpressionStatement',
    start: 34,
    end: 54,
    loc: SourceLocation {
      start: [Position],
      end: [Position],
      filename: undefined,
      identifierName: undefined
    },
    expression: Node {
      type: 'CallExpression',
      start: 34,
      end: 54,
      loc: [SourceLocation],
      callee: [Node],
      arguments: [Array]
    }
  }
]
```
2. 获取入口文件的依赖关系
从第一步获取到代码的抽象语法树以后，可以对其进行遍历来得到其依赖关系。这里我们借助`@babel/traverse`（接收抽象语法树对象和要分析的节点类型）来进行实现。
``` javascript
const fs = require('fs')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default;

const moduleAnalyser = (filename) => {
    const content = fs.readFileSync(filename, 'utf-8');

    // note: 输出的内容是ast,抽象语法树
    const ast = parser.parse(content, {
        sourceType: 'module'
    })

    // note: 存放依赖的数组
    const dependencies = []
    traverse(ast, {
        ImportDeclaration({ node }) {
            dependencies.push(node.source.value)
        }
    })
    // note: output为 [ './message' ]
    console.log(dependencies)
}

moduleAnalyser('./src/index.js')
```
3. 将依赖存放的数组进行改变，同时存放某文件相对于“项目根目录”的路径和相对于“文件”的路径
``` javascript
const fs = require('fs')
const path = require('path')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default;

const moduleAnalyser = (filename) => {
    const content = fs.readFileSync(filename, 'utf-8');

    // note: 输出的内容是ast,抽象语法树
    const ast = parser.parse(content, {
        sourceType: 'module'
    })

    // note: 存放依赖的数组
    const dependencies = {}
    traverse(ast, {
        ImportDeclaration({ node }) {
            const dirname = path.dirname(filename)
            // note: 将相对路径转换为针对‘项目根目录’的相对路径
            const newFile = './' + path.join(dirname, node.source.value)
            dependencies[node.source.value] = newFile
        }
    })
    // output: { './message': './src/message' }
    console.log(dependencies)
}

moduleAnalyser('./src/index.js')
```
* 将文件语法进行转换（使用`@babel/core`,`@babel/preset-env`）,使其可以被浏览器识别
``` javascript
const fs = require('fs')
const path = require('path')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default;
const babel = require('@babel/core')

const moduleAnalyser = (filename) => {
    const content = fs.readFileSync(filename, 'utf-8');

    // note: 输出的内容是ast,抽象语法树
    const ast = parser.parse(content, {
        sourceType: 'module'
    })

    // note: 存放依赖的数组
    const dependencies = {}
    traverse(ast, {
        ImportDeclaration({ node }) {
            const dirname = path.dirname(filename)
            // note: 将相对路径转换为针对‘项目根目录’的相对路径
            const newFile = './' + path.join(dirname, node.source.value)
            dependencies[node.source.value] = newFile
        }
    })
    // note: 使用babel对代码进行转换翻译
    const { code } = babel.transformFromAstSync(ast, null, {
        presets: ["@babel/preset-env"]
    })

    return {
        filename,
        dependencies,
        code
    }
}

const moduleInfo = moduleAnalyser('./src/index.js')
console.log(moduleInfo)
```
output: 
``` javascript
{
  filename: './src/index.js',
  dependencies: { './message': './src/message' },
  code: '"use strict";\n' +
    '\n' +
    'var _message = _interopRequireDefault(require("./message"));\n' +
    '\n' +
    'function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }\n' +
    '\n' +
    'console.log(_message["default"]);'
}
```