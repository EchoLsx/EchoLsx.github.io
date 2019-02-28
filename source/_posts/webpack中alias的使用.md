---
title: webpack中alias的使用
catalog: true
date: 2018-12-21 18:28:44
subtitle:
header-img:
tags: 
- webapck
- JavaScript
---

项目中有很多情况需要引用src下面的资源，有时候过多层级的相对路径看起来很乱，所以我们通常会使用webpack的alias功能来配置别名，
一般配置 @ 为项目根目录下放资源和源码的 /src 目录的别名。
```javascript
alias: {
    'vue$': 'vue/dist/vue.esm.js',
    '@': resolve('src'),
}
```
这样我们就可以在 js 中用导入模板文件或者js就可以用如下方式导入：

```javascript
import tool from '@/utils/xxx'   
// Webpack 能正确识别并打包。
```
但是在css中引入一些图片文件或者import css文件的时候，就会找不到对应的图片资源 Can't resolve './@/assets/open.png' 这类报错。

#### 解决方法
在引用路径的字符串最前面添加上 ~ 符号，如下

```javascript
@import '~@/assets/css/all.scss';

.open {
    background: url("~@/assets/open.png") no-repeat 0 1px;
}
```
#### 总结

~ 视为模块解析是 webpack 做的事，不是 css-loader 做的事。
各类非 js 直接引用（import require）静态资源，依赖相对路径加载问题，都可以用 ~ 语法完美解决；
```javascript
css module 中： @import "~@/style/theme"
css 属性中： background: url("~@/assets/xxx.jpg")
html 标签中： <img src="~@/assets/xxx.jpg" alt="alias">

```




