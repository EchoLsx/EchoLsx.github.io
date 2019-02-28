---
title: vue-router相关
catalog: true
date: 2018-11-22 14:08:16
subtitle:
header-img:
tags:
 - vue
---


#### 1、$router和$route的区别
> $router : 是路由操作对象，只写对象

> $route : 路由信息对象，只读对象
#### 2、query传递参数

```javascript
//query传参，使用name跳转
this.$router.push({
    name:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})

//query传参，使用path跳转
this.$router.push({
    path:'second',
    query: {
        queryId:'20180822',
        queryName: 'query'
    }
})

//query传参接收
this.queryName = this.$route.query.queryName;
this.queryId = this.$route.query.queryId;
```

#### 3、用params传参

*注：使用params传参只能使用name进行引入*

```javascript
//params传参 使用name
this.$router.push({
  name:'second',
  params: {
    id:'20180822',
     name: 'query'
  }
})

//params接收参数
this.id = this.$route.params.id ;
this.name = this.$route.params.name ;

//路由

{
path: '/second/:id/:name',
name: 'second',
component: () => import('@/view/second')
}
// 路由配置的时候一定要加参数，否则刷新页面，取不到参数了

```

