# webpack4 学习

![webpack](https://img2018.cnblogs.com/blog/1306384/201810/1306384-20181028215850562-752614317.png)

- what?
  模块打包器
- why?
  - 如果像以前开发时一个 html 文件可能会引用十几个 js 文件,而且顺序还不能乱，因为它们存在依赖关系，同时对于 ES6+等新的语法，less, sass 等 CSS 预处理都不能很好的解决
- how?
  - webpack + webpack-cli + webpack.config.js

## 环境搭建: 安装 webpack 和 webpack-cli

webpack4 = webpack 内核 + webpack-cli 脚手架

```bash
# 项目初始化
# 创建空目录 和 package.json
mkdir webpack-some
cd webpack-some
pnpm init -y
pnpm install webpack@4.31.0 webpack-cli@3.3.2 --save-dev
```

## 体验 webpack 打包的简单例子

- 1.创建 `webpack.config.js`, 编写配置

```markdown
'use strict';

const path = require('path');

module.exports = {
entry: ['./src/index.js'],
output: {
path: path.join(\_\_dirname, 'dist'),
filename: 'bundle.js'
},
mode: 'production'
};
```

- 2.创建 `src/index.js` 和 `src/helloworld.js`

```markdown
# src/index.js

import { helloworld } from './helloworld'

document.write(helloworld);

# src/helloworld.js

export function helloworld() {
return 'hello world';
}
```

- 3.执行指令打包: `./node_modules/.bin/webpack`

```bash
>./node_modules/.bin/webpack
Hash: b2d5c02cc4267a47ed66
Version: webpack 4.31.0
Time: 227ms
Built at: 2022/03/19 09:30:45
    Asset      Size  Chunks             Chunk Names
bundle.js  1.01 KiB       0  [emitted]  main
Entrypoint main = bundle.js
[0] multi ./src/index.js 28 bytes {0} [built]
[1] ./src/index.js + 1 modules 136 bytes {0} [built]
    | ./src/index.js 74 bytes [built]
    | ./src/helloworld.js 62 bytes [built]

```

- 4.配置 `script` 命令, 替代繁琐的手动输入指令

```json
{
  "name": "webpack-some",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "4.31.0",
    "webpack-cli": "3.3.0"
  }
}
```

## webpack 基础

- 1.`entry`: 依赖图的入口

```javascript
// 单入口的写法(SPA), entry是一个字符串
mpdule.exports = {
  entry: "./src/main.js",
};

// 多入口的写法(多页应用), enrty是一个对象
module.exports = {
  enrty: {
    app: "./scr/main.js",
    adminApp: "./scr/adminApp.js",
  },
};
```

- 2.`output`: 指定`webpack`打包后输出的位置目录
  - filename： 输出的文件名
  - path: 输出的文件路径

```javascript
// 单入口的output
module.exports = {
  entry: "./src/main.js",
  output: {
    filename: "bundle.js",
    path: __dirname + "/dist",
  },
};

// 多入口的output的 output 写法
// [name] => 文件名占位
module.exports = {
  enrty: {
    app: "./scr/main.js",
    adminApp: "./scr/adminApp.js",
  },
  output: {
    filename: [name].js,
    path: __dirname + "/dist",
  },
};
```

- 3.loaders
  `webpack`开箱即用 只支持 `js` 和 `json`两种文件类型, 可以通过`loaders`去支持其他文件类型并且把他们转成有效的模块，而且可以添加到依赖图中。
  webpack 原生不支持的文件类型,通过 loaders 去转化成 webpack 支持

_loaders 本身是一个函数,接受 源文件作为文件，返回转换后的结果_

常见的 loaders 有以下这些:
| 名称 | 描述 |
| ---- | ---- |
| babel-loader | 转换 es6、es7 等 js 新特性语法 |
| css-loader | 支持 .css 文件的加载和解析 |
| less-loader | 将 less 文件转成 css |
| sass-loader | 将 scss 文件转成 css |
| ts-loader | 将 ts 转成 js |
| file-loader | 进行 图片、字体等的打包 |
| raw-loader | 将 文件 以字符串的形式导入 |
| thread-loader | 多进程打包 js 和 css |

```javascript
const path = require("path");

module.exports = {
  output: {
    filename: "bundle.js",
  },
  module: {
    rules: [
      {
        test: /\.txt$/, // 指定 匹配的文件类型
        use: "raw-loader", // 指定使用 哪种loader来处理
      },
    ],
  },
};
```

- 4.plugins
  插件用于 bundle 文件的优化, 如: 资源管理, 环境变量注入。作用于 整个构建过程。如 每次构建时,需要手动删除旧的文件

常见的 plugins 有以下这些:
| 名称 | 描述 |
| ---- | ---- |
| CommonsChunkPlugin | 将 chunks 相同的模块代码成公共 js |
| CleanWebpackPlugin | 清理构建目录 |
| ExtractTextWebpackPlugin | 将 css 从 bundle 文件中提取成一个独立的 css 文件 |
| CopyWebpackPlugin | 将文件或文件夹拷贝到构建的输出目录 |
| HtmlWebpackPlugin | 创建 Html 文件区承载输出的 bundle |
| UglifyjsWebpackPlugin | 压缩 js |
| ZipWebpackPlugin | 将打包出的资源生成 zip 包 |

```javascript
const path = require("path");

module.exports = {
  output: "bundle.js",
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
    }),
    // plugins数组
  ],
};
```

- 5.mode
  `mode`用于指定当前的构架环境: `production`、`development` 和 `none`
  设置 `mode` 可以使用 webpack 内置的函数,默认值: `production`

`mode`的内置函数功能:
| 选项 | 描述 |
| ---- | ---- |
| development | 开启 NamedChunksPlugins 和 NamedModulesPlugin |
| production | 开启 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurenceOrderPlugin, SideEffectsFlogPlugin 和 TerserPlugin |
| none | 不开启任何优化选项 |

## webpack 解析 ES6 和 React JSX

### webpack 解析 ES6

webpack 默认可以解析 es5 的,但是 es6+ 需要使用`babel-loader`进行转换:es5

- 1.指定 js 代码解析规则

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: "babel-loader",
      },
    ],
  },
};
```

- 2.babel 的配置文件: `.babelrc`, 增加 es6

```
{
	"presets": [
		"@babel/preset-env"
	],
	"plugins": [
		"@babel/proposal-class-properties"
	]
}


# plugins: 一个plugin对应一个功能
# presets: 一个preset是一组plugin的集合
```

安装 es6 对应的依赖: + @babel/core -> babel 核心 + @babel/preset-env -> es6-preset + babel/loader -> loader 解析 es6

### webpack 解析 React JSX

让 webpack 支持解析 React JSX

```
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ],
    "plugins": [
		"@babel/proposal-class-properties"
	]
}
```

安装 react 对应的依赖: + react + react-dom + @babel/preset-react
