---
title: vue部署到ngnix非根目录——前后端配置文件
catalog: true
date: 2018-09-10 17:54:38
subtitle:
header-img:
tags: 
    - vue 
    - ngnix
    - 前端
---


### 一、根目录下部署的情况
*比如项目打包后的文件夹为dist,ngnix要怎么配置才能访问呢？*

```
<!-- ngnix的配置为： -->
server{
    listen 8000;
    server_name localhost
    location / {
       root /home/www/dist; 
       index index.html;
    }
}
然后直接访问 http://域名:8000/ 

```

### 二、非根目录下部署的情况

1、设置webpack下的config下的index.js下的assetsPublicPath为 自己需要的路径

```javascript
assetsPublicPath: '/web/'
```
2、vue-router
```javascript

base: '/web/'
// mode: 'history',
```
- base: 默认值为’/‘,表示应用的基路径.如果整个单页应用服务在 /web/ 下，然后 base 就应该设为 “/web/“，web一般就是打包后的文件夹名称， 这个属性只会对生产环境的路径有影响 ，为了和服务端配合的路径， 开发模式下不用管。

- 这边有一篇文章解释为什么需要配置这个base： [vue-router的history模式打包后页面空白的解决方案](https://blog.csdn.net/wxl1555/article/details/81208570)


3、修改nginx配置
```
location ^~/web {
        alias /home/webapps/web/; // 映射路径
        index index.html;
        try_files $uri $uri/ /web/index.html;
    }       
```
在地址栏中输入https://ip/vuejs-admin/index 就可以正常访问了。

- 注意，devServer的路径依然还是 https://ip:端口/index 这种格式。

- DEBUG 的开发模式下，path: DEBUG ? path.resolve(__dirname, './resources') : path.resolve(__dirname, './web/resources'), 这个./resources是在内存中的，不会显示在目录中

```javascript
new HtmlWebpackPlugin({
    title: "test",
    template: __dirname + "/src/entry/index.tmpl.html",
    filename: "../index.html",  //filename配置的html文件目录是相对于webpackConfig.output.path路径而言的，不是相对于当前项目目录结构的。
    chunks: DEBUGGER ? ["debugger", "base", "common", "index"] : ["base", "common", "index"],
    chunksSortMode: "manual",
    alwaysWriteToDisk: true
}),
```
所以index.html就是项目根目录下的，与./resources在同一层级，可以正常引用到js，因此开发的时候，url还是https://ip:端口/index 即可，不需要加/web/








