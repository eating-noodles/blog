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

## Dependencies Graph(依赖图谱)
对所有的模块进行分析
``` javascript
const makeDependenciesGraph = (entry) => {
    const entryModule = moduleAnalyser(entry)
    const graphArr = [entryModule]
    for (let i = 0; i < graphArr.length; i++) {
        const item = graphArr[i]
        const { dependencies } = item
        if (dependencies) {
            for (let j in dependencies) {
                graphArr.push(moduleAnalyser(dependencies[j]))
            }
        }
    }

    // note: 为了后面打包方便，将数组换为对象
    const graph = {};
    graphArr.forEach(item => {
        graph[item.filename] = {
            dependencies: item.dependencies,
            code: item.code
        }
    })
    return graph
}
const graphInfo = makeDependenciesGraph('./src/index.js')
console.log(graphInfo)
```
output: 
``` javascript
{
  './src/index.js': {
    dependencies: { './message.js': './src/message.js' },
    code: '"use strict";\n' +
      '\n' +
      'var _message = _interopRequireDefault(require("./message.js"));\n' +
      '\n' +
      'function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }\n' +
      '\n' +
      'console.log(_message["default"]);'
  },
  './src/message.js': {
    dependencies: { './word.js': './src/word.js' },
    code: '"use strict";\n' +
      '\n' +
      'Object.defineProperty(exports, "__esModule", {\n' +
      '  value: true\n' +
      '});\n' +
      'exports["default"] = void 0;\n' +
      '\n' +
      'var _word = require("./word.js");\n' +
      '\n' +
      'var message = "say ".concat(_word.word);\n' +
      'var _default = message;\n' +
      'exports["default"] = _default;'
  },
  './src/word.js': {
    dependencies: {},
    code: '"use strict";\n' +
      '\n' +
      'Object.defineProperty(exports, "__esModule", {\n' +
      '  value: true\n' +
      '});\n' +
      'exports.word = void 0;\n' +
      "var word = 'hello';\n" +
      'exports.word = word;'
  }
}
```

## 生成源码
1. 建立一个闭包方法，避免污染全局环境（把依赖图谱传递进去）
``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    // note: 为了不污染环境，使用闭包
    return `
        (function(graph){

        })(${graph});
    `
}
```
2. 依赖图谱的每一项包含自己的依赖，以及自己的源码。但是源码中包含`require`、`exports`这些方法，浏览器没有、是识别不出来，所以直接拿去浏览器命令行是执行不了的。
3. 构建一个require函数，入参为接收的“模块”。
``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    return `
        (function(graph){
            function require(module) {
            };
        })(${graph});
    `
}
```
4. 以入口文件为参数调用require方法。`注意传参要用引号引起来`
``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    return `
        (function(graph){
            function require(module) {
            };
            require('${entry}')
        })(${graph});
    `
}
```
5. 拿到入口文件的依赖图谱，要执行其对应的代码。（继续用一个闭包来进行执行，每个模块一个闭包，这样模块的变量不会影响外部变量，避免环境污染）
``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    // note: 为了不污染环境，使用闭包
    return `
        (function(graph){
            function require(module) {

                (function(code) {
                    eval(code);
                })(graph[module].code)

            };
            require('${entry}')
        })(${graph});
    `
}
```
6. 入口文件中的依赖引入是'/message.js', 但是我们的依赖图谱对象中的key存储的是相对于工程根目录的路径，所以需要对其进行转换（依赖图谱中，每个模块的dependencies对象存储了相对路径和绝对路径的映射关系）
``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    // note: 为了不污染环境，使用闭包
    return `
        (function(graph){
            function require(module) {

                function localRequire(relativePath) {
                    return require(graph[module].dependencies[relativePath]);
                }
                (function(require, code) {
                    eval(code);
                })(localRequire, graph[module].code)

            };
            require('${entry}')
        })(${graph});
    `
}
```
7. 代码执行还需要一个exports对象,以便可以导出一些内容
``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    // note: 为了不污染环境，使用闭包
    return `
        (function(graph){
            function require(module) {
                function localRequire(relativePath) {
                    return require(graph[module].dependencies[relativePath]);
                }

                var exports = {};
                (function(require, exports, code) {
                    eval(code);
                })(localRequire, exports, graph[module].code)
                return exports;

            };
            require('${entry}')
        })(${graph});
    `
}
```
8. 最终结果

``` javascript
const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry));
    // note: 为了不污染环境，使用闭包
    return `
        (function(graph){
            function require(module) {
                function localRequire(relativePath) {
                    return require(graph[module].dependencies[relativePath]);
                }
                var exports = {};
                (function(require, exports, code) {
                    eval(code);
                })(localRequire, exports, graph[module].code)
                return exports;
            };
            require('${entry}')
        })(${graph});
    `
}

const code = generateCode('./src/index.js')
console.log(code)
```
output: 
``` javascript
        (function(graph){
            function require(module) {
                function localRequire(relativePath) {
                    return require(graph[module].dependencies[relativePath]);
                }
                var exports = {};
                (function(require, exports, code) {
                    eval(code);
                })(localRequire, exports, graph[module].code)
                return exports;
            };
            require(./src/index.js)
        })({"./src/index.js":{"dependencies":{"./message.js":"./src/message.js"},"code":"\"use strict\";\n\nvar _message = _interopRequireDefault(require(\"./message.js\"));\n\nfunction _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { \"default\": obj }; }\n\nconsole.log(_message[\"default\"]);"},"./src/message.js":{"dependencies":{"./word.js":"./src/word.js"},"code":"\"use strict\";\n\nObject.defineProperty(exports, \"__esModule\", {\n  value: true\n});\nexports[\"default\"] = void 0;\n\nvar _word = require(\"./word.js\");\n\nvar message = \"say \".concat(_word.word);\nvar _default = message;\nexports[\"default\"] = _default;"},"./src/word.js":{"dependencies":{},"code":"\"use strict\";\n\nObject.defineProperty(exports, \"__esModule\", {\n  value: true\n});\nexports.word = void 0;\nvar word = 'hello';\nexports.word = word;"}});
```

示例仓库地址：https://github.com/eating-noodles/webpack_memo/tree/main/webpack26-bundler
