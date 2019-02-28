---
title: Vue风格指南
catalog: true
date: 2019-01-28 09:55:32
subtitle:
header-img:
tags:
 - vue
---

####  文件组件文件的大小写

> 单文件组件的文件名应该要么始终是单词大写开头 (PascalCase)，要么始终是横线连接 (kebab-case)

![单文件组件大小写.jpg](单文件组件大小写.jpg)

 #### 模板中的组件名大小写

 > 对于绝大多数项目来说，在单文件组件和字符串模板中组件名应该总是 PascalCase 的——但是在 DOM 模板中总是 kebab-case 的。

 ![单模板中的组件名大小写.jpg](模板中的组件名大小写.jpg)

 #### JS/JSX 中的组件名大小写

> JS/JSX 中的组件名应该始终是 PascalCase 的，尽管在较为简单的应用中只使用 Vue.component 进行全局组件注册时，可以使用 kebab-case 字符串。

 ![JS和JSX中的组件名大小写.jpg](3.jpg)


 #### Prop 名大小写

 >在声明 prop 的时候，其命名应该始终使用 camelCase，而在模板和 JSX 中应该始终使用 kebab-case。

![Prop大小写.jpg](4.jpg)
