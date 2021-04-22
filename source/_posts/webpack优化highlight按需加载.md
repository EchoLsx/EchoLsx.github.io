---
title: webpack优化highlight按需加载
catalog: true
date: 2021-04-22 19:42:35
subtitle:
header-img:
tags:
    - webpack
---

## 性能优化

最近在做项目性能优化，说到性能优化，一方面是加载速度，一方面是运行性能；

- 1、加载速度上，涉及代码压缩混淆，gzip，webpack路由懒加载，cdn加速，url-loader利用base64;代码分割splitChunks(commonChunks)；公共代码提取，最大程度利用缓存；
- 2、运行运能，使用flex布局，减少重绘重排，web worker等

关于代码分割之前有写，请看 [Webpack之codeSplitting大法](https://echolsx.github.io/2019/02/13/Webpack%E4%B9%8BCodeSplitting%E5%A4%A7%E6%B3%95/)

##  highlight, moment语言包按需加载

这边不说element-ui的按需加载；这种只是按需加载部分的JS，并且通过字符串写死的方式指定了路径，还有一部分，如同CSS的部分需要在组件生成时动态加载，或者通过变量的形式加载；


使用变量加载require('highlight.js/languages/' + name),webpack会打包 'highlight.js/languages/*'下所有文件

## 解决方案

```
import hljs from 'highlight.js/lib/core'
import javascript from 'highlight.js/lib/languages/javascript'
hljs.registerLanguage('javascript', javascript)
```


#### 如果需要批量支持多种语言；

```js
import hljs from 'highlight.js/lib/core';
['javascript', 'json', 'xml', 'markdown'].forEach((langName) => {
  let langModule = require(`highlight.js/lib/languages/${langName}`);
  hljs.registerLanguage(langName, langModule);
});

```
光上面这种写法，使webpack-bundle-analyzer
分析后发现还是全部的语言都打包了；查了下资料，发现reuqire中有变量的写法，因为构建不在编译阶段，所以变量不可知，webpack会打包文件夹下所有的资源；


```js
// webpack plugins中加入
new webpack.ContextReplacementPlugin(
    /highlight\.js\/lib\/languages$/,
    new RegExp(`^./(${['javascript', 'json', 'xml', 'markdown'].join('|')})$`)
)
```

## ContextReplacementPlugin


![ContextReplacementPlugin](contextreplacementplugin.jpg)

## 参考资料
https://webpack.html.cn/plugins/context-replacement-plugin.html




