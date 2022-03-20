# webpack4 学习
**目标: webpack配置的探索, 产出一份通用的webpack.config.js的用于生产的工程化配置（webpack4版本）**
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
```javascript
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
  "license": "MIT",
  "devDependencies": {
    "webpack": "4.31.0",
    "webpack-cli": "3.3.0"
  }
}
```




## webpack核心概念
- entry: 打包入口
- output: 打包输出
- loaders: 解析器
- plugins: 插件
- mode

### entry
- 单入口: entry是一个字符串
- 多入口: entry是一个对象
```javascript
// 单入口(SPA)
module.exports = {
  entry: './src/index.js'
}

// 多入口
module.exports = {
  entry: {
    app: './src/index.js',
    adminApp: './src/adminApp.js'
  }
}
```

### output
- filename: 指定输出的文件名
- path: 指定输出的目录
```javascript
// 单入口的output配置
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: __dirname + '/dist'
  }
}

// 多入口的output配置
module.exports = {
  entry: {
    app: './src/index.js',
    adminApp: './src/adminApp.js'
  },
  output: {
    filename: '[name].js', // 通过 占位符 [name] 确保文件名的唯一性
    path: __dirname + '/dist'
  }
}
```

### loaders: 解析器
- `webpack`开箱即用 只支持 `js` 和 `json`两种文件类型
- 可以通过`loaders`去支持`其他文件类型`, 并且把他们转成有效的模块，而且可以添加到依赖图中。

**loaders 本身是一个函数,接受 源文件作为文件，返回转换后的结果**

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

loaders的用法
- test: 匹配的文件类型
- use: 指定使用对应的loader解析器名称
```javascript
const path = require('path')

module.exports = {
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: 'babel-loader'
      }
    ]
  }
}
```

### plugins: 插件
- 插件用于 bundle 文件的优化, 如: 资源管理, 环境变量注入。作用于 整个构建过程。如 每次构建时,需要手动删除旧的文件。

- loaders 做不了的事情, 用 plugins 来处理。

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

plugins的用法:
```javascript
const path = require('path')

module.exports = {
  output: "bundle.js",
  // plugins数组
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
    })
  ],
}
```

### mode
- `mode`用于指定当前的构架环境: `production`、`development` 和 `none`。 设置 `mode` 可以使用 webpack 内置的函数,默认值: `production`

`mode`的内置函数功能:
| 选项 | 描述 |
| ---- | ---- |
| development | 开启 NamedChunksPlugins 和 NamedModulesPlugin |
| production | 开启 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurenceOrderPlugin, SideEffectsFlogPlugin 和 TerserPlugin |
| none | 不开启任何优化选项 |

