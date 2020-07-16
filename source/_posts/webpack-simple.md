---
title: 实现一个简单的 Webpack
date: 2020-07-16 21:48:22
tags:
  - Webpack
categories: [Webpack]
excerpt: 今天我们就来实现一个简单的 webapck 功能。
---

### 创建根目录
首先创建一个目录 `simpleWebpack`

### 初识化文件 
`npm init -y`

### 安装必要的包
安装接下来需要使用到的 `npm` 包，`npm i babylon babel-traverse babel-core -S`

### 配置 `.babelrc` 文件
根目录新建 `.babelrc` 文件，并安装 `npm i babel-preset-env --save-dev` 包，如下配置：
```bash
{
    "presets": [
        "@babel/preset-env"
    ]
}
```
### 创建打包配置文件
在根目录下创建类似 `webpack` 配置文件的 `simplewebpack.config.js`，并添加如下代码：

```bash
// simplewebpack.config.js
const path = require('path')

module.exports = {
    entry: path.join(__dirname, './src/index.js'),
    output: {
        path: path.join(__dirname, './dist'),
        filename: 'main.js'
    }
}
```
### 创建需要构建的文件
根目录下新建文件夹 `src`，文件夹下新建两个文件 `index.js` 和 `greeting.js`:
```bash
// index.js
import { greeting } from './greeting.js';

document.write(greeting('ycx'))
```
```bash
// greeting.js
export function greeting(name) {
    return 'hello' + name;
}
```

### 实现构建逻辑
根目录下新建 `lib` 文件夹，文件夹下新建 4 个文件 `index.js、parser.js、compiler.js、test.js`;

`index.js` 文件主要是实例化 `Compiler` 函数，并传入 `simpleconfig.config.js` 文件中配置的信息，执行 `run` 命令开始构建。
```bash
// index.js
const Compiler = require('./compiler')
const options = require('../simplewebpack.config');

new Compiler(options).run()
```

`parser.js` 文件主要是通过实现两个功能：将 `ES6` 语法转换成 `ES5` 的语法、以及分析模块之间的依赖关系。实现方式主要是通过 `babel` 的一些插件。
1. 通过 `babylon` 生成 `AST`
2. 通过 `babel-core` 将 `AST` 重新生成源码
3. 通过 `babel-traverse` 的 `ImportDeclaration` 方法获取依赖属性

```bash
// parser.js
const fs = require('fs');
const babylon = require('babylon');
const traverse = require('babel-traverse').default;
const { transformFromAst } = require('babel-core');

module.exports = {
    getAST: (path) => {
        const source = fs.readFileSync(path, 'utf-8');

        return babylon.parse(source, {
            sourceType: 'module'
        })
    },
    getDependencies: (ast) => {
        const dependencies = [];

        traverse(ast, {
            ImportDeclaration: ({ node }) => {
                dependencies.push(node.source.value)
            }
        })

        return dependencies;
    },
    transform: (ast) => {
        const { code } = transformFromAst(ast, null, {
            presets: ['env']
        })

        return code;
    }
}
```

`compiler.js` 文件主要使用了 `parser.js` 中方法，从入口模块 `entry` 出发，找出所有依赖模块，并包装成一个自执行函数，把所有模块组合成一个对象，以 `key: value` 的形式传入。最后通过 `fs` 模块写入到 `output` 目录中。
```bash
// compiler.js
const { getAST, getDependencies, transform } = require('./parser');
const path = require('path');
const fs = require('fs');

module.exports = class Compiler {
    constructor(options) {
        const { entry, output } = options;

        this.entry = entry;
        this.output = output;
        this.modules = [];
    }

    run() {
        const entryModule = this.buildModule(this.entry, true);

        this.modules.push(entryModule);

        this.modules.map((_module) => {
            _module.dependencies.map((dependency) => {
                this.modules.push(this.buildModule(dependency))
            })
        })
        
        this.emitFiles()
    }

    buildModule(filename, isEntry) {
        let ast;

        if (isEntry) {
            ast = getAST(filename)
        } else {
            const absolutePath = path.join(process.cwd(), './src', filename);
            console.log('absolutePath:', absolutePath);

            ast = getAST(absolutePath)
        }

        return {
            filename,
            dependencies: getDependencies(ast),
            source: transform(ast)
        }
    }

    emitFiles() {
        const outputPath = path.join(this.output.path, this.output.filename);

        let modules = '';

        this.modules.map((_module) => {
            modules += `'${_module.filename}': function (require, module, exports) { ${_module.source} },`
        })

        const bundle = `(function(modules) {
            function require(filename) {
                var fn = modules[filename];
                var module = {exports: {}};

                fn(require, module, module.exports);

                return module.exports;
            }

            require('${this.entry}')
        })({ ${modules} })`;

        console.log('bundle.js:', bundle)

        fs.writeFileSync(outputPath, bundle, 'utf-8');
    }
}
```

`test.js` 文件主要是针对 `parser.js` 中的方法进行测试使用。
```bash
// test.js
const { getAST, getDependencies, transform } = require('./parser');
const path = require('path');

const ast = getAST(path.join(__dirname, '../src/index.js'));
const getDependencies = getDependencies(ast);

const source = transform(ast);

console.log(source)
```

### 测试
根目录下新建 `dist` 文件夹，并创建一个 `main.js 和 index.html`，`index.html` 主要用来测试 `simplewebpack` 的功能是否正确，`main.js` 是用来保存编译之后的代码:
```bash
// index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script src="main.js"></script>
</body>
</html>
```