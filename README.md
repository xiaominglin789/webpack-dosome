# webpack4 学习
目标: webpack配置的探索, 产出一份通用的webpack.config.js的用于生产的工程化配置（webpack4版本）
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
