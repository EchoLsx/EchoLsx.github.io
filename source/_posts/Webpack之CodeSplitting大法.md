---
title: Webpack之CodeSplitting大法
catalog: true
date: 2019-02-13 11:05:34
subtitle:
header-img:
tags:
 - webpack
---
### Code Splitting 是什么以及为什么
> 在以前，为了减少 HTTP 请求，通常地，我们都会把所有的代码都打包成一个单独的 JS 文件。但是，如果这个 JS 文件体积很大的话，那就得不偿失了。这时，我们不妨把所有代码分成一块一块，需要某块代码的时候再去加载它。

所以说，Code Splitting 其实就是把代码分成很多很多块（ chunk ）咯。

### Code Splitting 怎么做
主要有 2 种方式：

- 分离业务代码和第三方库（ vendor ）
- 按需加载（利用 import() 语法）


#### 自动化分离 vendor
如果我们想把所有 node_modules 目录下的所有 .js 都自动分离到 vendor.js ，则需要用到 minChunks：（ File Changes ）

```javascript
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: ({ resource }) => (
    resource &&
    resource.indexOf('node_modules') >= 0 &&
    resource.match(/\.js$/)
  ),
}),
```
#### import() 按需加载

```javascript
// router.js

const Emoji = () => import(
  /* webpackChunkName: "Emoji" */
  './pages/Emoji.vue')

const Photos = () => import(
  /* webpackChunkName: "Photos" */
  './pages/Photos.vue')

// webpack.config.js 解决懒加载导致的多次引用第三方模块的问题,axios在每个页面模块中单独引用的情况，一般不考虑
output: {
  chunkFilename: '[name].chunk.js',
}
new webpack.optimize.CommonsChunkPlugin({
    name: 'app',
    async: 'common-in-lazy',
    minChunks: ({ resource } = {}) => (
      resource &&
      resource.includes('node_modules') &&
      /axios/.test(resource)
    ),
  }),

```

### 业务组件中引用多次的自定义模块

> 在所有的 async chunk ( Emoji.chunk.js 和 Photos.chunk.js ) 中找到引用 2 次以上的模块，那把他挪到 used-twice chunk 中，如果 used-twice 不存在的话，那就创建一个。

```javascript
new webpack.optimize.CommonsChunkPlugin({
    name: 'app',
    async: 'used-twice',
    children: true,
    minChunks: (module, count) => (
      count >= 2
    ),
  }),

```

> 你的 Code Splitting = webpack bundle analyzer分析图 + CommonsChunkPlugin插件 + 你的分析






