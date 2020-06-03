# 概念

笔记对应的版本为 `webpackv4.43.0`

webpack是一个现代JavaScript应用程序的静态模块打包器（module bundler）。

当webpack处理应用程序时，会递归的构建一个*依赖关系图（dependency graph）*，其中包含程序需要的每个模块，然后将所有的模块打包成一个或多个bundle。

webpack从4.x版本开始，可以零配置使用，但是依然可以进行自定义配置。

webpack有四个**核心概念**：

- 入口（entry）
- 输出（output）
- loader
- 插件（plugins）

## 一、入口（entry）

**入口起点（entry point）**指示webpack应该使用哪个模块作为构建内部依赖的开始。

进入入口起点后webpack会找出文件对应的以来关系，然后进行处理，最后输出到出口文件中，默认为bundles。

### 入口配置

通过在webpack配置文件中配置`entry`属性，指定一个或多个入口起点，默认是值为`./src`。

常见的配置文件为：webpack.config.js

Vue-cli3.x中配置文件是vue.confing.js的自定义文件，vue脚手架会自己查找该文件，根据文件中的配置内容对默认配置进行修改

最简单的`entry`配置举例：

```js
// webpack.config.js

module.exports = {
    entry: './src/myentry.js'
}
```

#### 1、单个入口语法

用法： `entry: string|Array<string>`

```js
// 上面的例子其实是下面语法的简写

module.exports = {
    entry: {
        main: './src/myentry.js'
    }
}
```

#### 2、对象语法

用法： `entry: {[entryChunkName: string]: string|Array<string>}`

```js
const config = {
    entry: {
        app: './src/app.js',
        vendors: './src/vendors.js'
    }
};

module.exports = config;
```

#### 3、常见场景

常见的入口配置及实际应用

 ##### 3.1、分离应用程序（app）和第三方库（vendor）入口

```js
const config = {
    entry: {
        app: './src/app.js',
        vendors: './src/vendors.js'
    }
};
module.exports = config;

// 如上写法，看出webpack从app.js和vendors.js开始创建依赖。两者之间是彼此完全分离、相互独立的。常见于，只有一个入口起点（不包含vendor）的单页应用程序中

// 如此设置允许使用 CommonsChunkPlugin从应用程序bundle中提取vendor引用到vendor bundle并把引用vendor的部分替换为__webpack_require__()调用。如果应用程序bundle中没有vendor代码。就可以在webpack中实现长效缓存模式
```

##### 3.2、多页面应用程序

```js
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
}

module.exports = config;
// 告诉webpack需要3个独立的依赖图

// 在多页应用中，每当页面跳转时服务器将获取一个新的HTML文档。页面重新加载新文档，并且资源被重新下载。
 // 使用CommonsChunkPlugin为每个页面间的应用程序共享代码创建bundle，使多页应用能狗狗复用入口起点之间的大量代码/模块
```

## 二、出口（output）

output属性告诉webpack输出其所创建的bundles的位置，以及文件命名，默认值为`./dist`。

```js
// webpack是依赖于nodejs的环境运行，该获取路径的方法为nodejs自带的默认方法，所以直接require调用即可
const path = require('path')  

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),  // 输出的路径
        filename: 'mybundle.js'  // 输出的文件名
    }
}
```

### 1、用法

webpack中配置`output`属性的最低要求是将它的值设置为一个对象，并包含以下两点：

- `filenam`用于输出文件的文件名
- 目标输出目录`path`的**绝对路径**

```js
// 生成一个bundle.js文件到/src/dist目录中

const config = {
    output: {
        filename: 'bundle.js',
        path: '/src/dist'
    }
};

module.exports = config;
```

### 2、多个入口起点

如果配置创建两个多个入口起点或者使用了CommonsChunkPlugin的插件时，应该使用`占位符`来确保每个文件名的唯一性

```js
const config = {
    entry: {
        app: './src/app.js',
        app2: './src/app2.js'
    },
    output: {
        filenname: '[name].js',
        path: __dirname + '/dist'
    }
};

module.exports = config;

// 输出结果为： ./dist/app.js ./dist/app2/js

```

### 3、高级进阶 （待深究）

使用CDN和资源hash的情况

```javascript
output: {
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

在编译时不知道最终输出文件的 `publicPath` 的情况下，`publicPath` 可以留空，并且在入口起点文件运行时动态设置。如果你在编译时不知道 `publicPath`，你可以先忽略它，并且在入口起点设置 `__webpack_public_path__`。

```javascript
__webpack_public_path__ = myRuntimePublicPath

// 剩余的应用程序入口
```

## 三、loader

用来处理非JavaScript文件，将所有类型文件转换为webpack能够给处理的有效模块，类似于`gulp`中的任务（task）。

*注意，loader 能够 `import` 导入任何类型的模块（例如`.css` 文件），这是 webpack 特有的功能，其他打包程序或任务执行器的可能并不支持。*

loader包含两个东西：

1. `test`属性，用于标识出应该被对应的loader进行转换的某个或某些文件
2. `use`属性，表示进行转换时，应该使用的loader

```javascript
const path = require('path');

const config = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
    // loader定义在module.rules中
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};

module.exports = config;
```

### 1、使用loader

如果要使用loader必须要先安装对应的loader，通过npm的方式进行安装

```js
// 例如
npm install --save-dev css-loader
npm install --save-dev ts-loader
```

相关的loader在使用时可以在官网查找，[loaders](https://www.webpackjs.com/loaders/)

```js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  }
};
```

loader的使用常见的有三种方式：

- 配置（官方推荐）：在config文件中指定loader
- 内联： 通过import语句显式指定
- CLI：在shell命令中指定

#### 1.1、配置

在配置文件中通过`module.rules`指定多个loader，loader的执行顺序为，从右向左/从下向上

```js
module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
```

#### 1.2、内联

在 `import` 语句或任何[等效于 "import" 的方式](https://www.webpackjs.com/api/module-methods)中指定 loader。使用 `!` 将资源中的 loader 分开。分开的每个部分都相对于当前目录解析。

```js
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

#### 1.3、CLI

通过 CLI 使用 loader

```sh
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```

### 2、loader特性

- loader 支持链式传递。能够对资源使用流水线(pipeline)。一组链式的 loader 将按照相反的顺序执行。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。
- loader 可以是同步的，也可以是异步的。
- loader 运行在 Node.js 中，并且能够执行任何可能的操作。
- loader 接收查询参数。用于对 loader 传递配置。
- loader 也能够使用 `options` 对象进行配置。
- 除了使用 `package.json` 常见的 `main` 属性，还可以将普通的 npm 模块导出为 loader，做法是在 `package.json` 里定义一个 `loader` 字段。
- 插件(plugin)可以为 loader 带来更多特性。
- loader 能够产生额外的任意文件。

## 插件

插件和loader的不同点在于，插件可以执行范围更广的任务，从打包优化到压缩，一直到重新定义环境中的变量，是web pack的支柱功能。插件的目的在于解决loader无法实现的其他事。

常见的插件使用方式：

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

const config = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

插件的实质是一个具有`apply`属性的JavaScript对象。`apply`属性会被webpack compiler调用，并且compiler对象可在整个编译生命周期访问。

### 用法

由于插件可以携带参数/选项，所以必须在配置中向`piugins`属性传入`new`实例

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const webpack = require('webpack'); //访问内置的插件
const path = require('path');

const config = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: 'babel-loader'
      }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

## 模式

通过选择 `development` 或 `production` 之中的一个，来设置 `mode` 参数，可以启用相应模式下的 webpack 内置的优化

```javascript
module.exports = {
  mode: 'production'
};
```

从CLI参数中传递

```js
webpack --mode=production
```

| 选项          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| `development` | 会将 `process.env.NODE_ENV` 的值设为 `development`。启用 `NamedChunksPlugin` 和 `NamedModulesPlugin`。 |
| `production`  | 会将 `process.env.NODE_ENV` 的值设为 `production`。启用 `FlagDependencyUsagePlugin`, `FlagIncludedChunksPlugin`, `ModuleConcatenationPlugin`, `NoEmitOnErrorsPlugin`, `OccurrenceOrderPlugin`, `SideEffectsFlagPlugin` 和 `UglifyJsPlugin`. |

*记住，只设置* `NODE_ENV`*，则不会自动设置* `mode`*。*

### mode: development

```diff
// webpack.development.config.js
module.exports = {
+ mode: 'development'
- plugins: [
-   new webpack.NamedModulesPlugin(),
-   new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),
- ]
}
```

### mode: production

```diff
// webpack.production.config.js
module.exports = {
+  mode: 'production',
-  plugins: [
-    new UglifyJsPlugin(/* ... */),
-    new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
-    new webpack.optimize.ModuleConcatenationPlugin(),
-    new webpack.NoEmitOnErrorsPlugin()
-  ]
}
```

