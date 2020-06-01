## 安装

#### 直接引用

直接下载并用 `script` 标签引入，`Vue` 会被注册为一个全局变量。

下载地址：[Vue.js](https://cn.vuejs.org/v2/guide/installation.html)

#### CDN

```js
<script src="https://cdn.jsdelivr.net/npm/vue"></script>  //使用最新版本
```

对于生产环境，推荐链接到一个明确的版本号和构建文件，避免新版本的不稳定性

```js
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script>
```

#### NPM

最常用的引用方式，结合webpack使用

```js
// 最新稳定版 
npm install vue
```

## Vue实例

#### 创建Vue实例

```js
var vm = new Vue({
  // 选项
})
```

#### 数据与方法

当Vue实例被创建时，将`data`对象中的所有属性加入到Vue的响应式系统中，当这些值发生给改变时，视图将会“响应”更新为新的值

```js
var data = { a：1 }

var vm = new Vue({
    data: data
})
```





