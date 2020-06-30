# HMR

为了避免因为修改某个文件而导致所有的文件都进行重新打包加载

HMR： hot module replacement 热模块替换/模块热替换

作用： 一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块），极大提升构建速度。

```js
module.exports = {
  devServer: {
    // 开启HMR功能
    hot:true
  }   
}
```

样式文件：可以使用HMR功能——因为style-loader内部实现了

js文件： 默认不能使用HMR功能

Html文件： 默认不能使用HMR功能，同时会导致，html文件不能热更新

解决： 修改entry入口将html文件放到entry入口中

# source-map

开发环境：优先选择 eval-source-map/eval-cheap-module-source-map

生产环境：优先选择 source-map/cheap-module-source-map

# oneOf

oneOf中的不能有两个配置处理同一种类型的文件

# 缓存

### babel缓存

开启babel缓存，第二次构建时，会读取之前的缓存

```js
{
    test: /\.js$/,
    exclude: /node_modules/,
    loader: 'babel-loader',
    options: {
        presets:[...],
        cacheDirectory: true
    }
}
```



 

