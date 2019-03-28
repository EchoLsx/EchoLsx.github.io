---
title: el-form-item回车刷新页面解决
catalog: true
date: 2019-03-28 10:15:45
subtitle:
header-img:
tags:
    - vue
    - element-ui
---

> 问题描述: 使用element-ui 中的el-form进行提交，当el-form-item仅有一项时，使用@keyup.enter.native事件绑定回车事件，出现点击回车后，浏览器会刷新页面的问题；

搜索了问题触发原因，是由于当表单只有一个文本框时，按下回车将会触发表单的提交事件， 导致页面的刷新。

> element官方解决方案：在el-from 加上 @submit.native.prevent

```html
<el-form :inline="true"  class="demo-form-inline"  @submit.native.prevent>
    <el-form-item label="名称:" style="margin-bottom:0">
    <el-input placeholder="名称" @keyup.enter.native="doFilter(5)"  v-model="likeName"></el-input>
    </el-form-item>
    <el-form-item style="margin-bottom:0">
    <el-button class="btnCheck" @click="doFilter(5)"  style="margin-bottom:0">查询</el-button>
    </el-form-item>
</el-form>
```