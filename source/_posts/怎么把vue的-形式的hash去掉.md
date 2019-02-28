---
title: '怎么把vue的#形式的hash去掉'
catalog: true
date: 2018-09-11 21:35:04
subtitle:
header-img:
tags: vue
---

*https://xxxx.com/web/#/index这种格式是vue的路由常见hash格式，但是不好看，怎么去掉它，而且还能正常访问呢？*
### 1、vue-router中的修改
```
 mode: 'history'
'base':'/web/'

```
### 2、webpack配置
```javascript
devServer:{
    ...
    historyApiFallBack:{
        index: '/index.html'
    }
    ...
}
output: {
    publicPath: "resources/",
    path:path.resolve(__dirname,'./web/resources'),
    //打包文件名称为web
    filename:xxxxx,
    chunkFileName:xxxxx,
}
```

### 3、ngnix配置
```
location ^~/web {
    alias /home/www/web;
    index index.html
    try_files $uri $uri/ /web/index.html
}
```

### 4、访问

*在浏览器中直接访问 https://xxxx.com/web 即可*





