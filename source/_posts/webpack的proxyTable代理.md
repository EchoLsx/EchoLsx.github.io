---
title: webpack的proxyTable代理
catalog: true
date: 2018-12-17 14:45:33
subtitle:
header-img:
tags: webpack
---

#### webpack的本地开发代理配置相关

```javascript
url:"/ybase/xxx/xxx";
url:"/ydic/xxx/xxx";
// 代理设置如下
dev:{
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {
        "/ybase": {
            target: "https://xxx.xxx.xxx.xxx/ybase",
            changeOrigin: true,
            secure: false,  // target默认不接受运行在https，且使用了无效证书的后端服务器，如果想要使用https，则需要设置该项为false
            pathRewrite: { '^/ybase': '' }
        },
        "/ydic": {
            target: "https://xxx.xxx.xxx.xxx/ydic",
            changeOrigin: true,
            secure: false,  // target默认不接受运行在https，且使用了无效证书的后端服务器，如果想要使用https，则需要设置该项为false
            pathRewrite: { '^/ydic': '' }
        },
    }
}
```


