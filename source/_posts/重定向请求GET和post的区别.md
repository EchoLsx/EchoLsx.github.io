---
title: 重定向请求GET和post的区别
catalog: true
date: 2018-09-03 09:34:56
subtitle:
header-img:
tags: 前端
---

如果是GET请求的重定向，数据用get请求跟在url后面即可
```javascript
window.location.href = '''https://xxx.com/xxx.action' 
```
如果是post请求，则需要模拟form表单提交
```javascript
 <form id="form" :action="this.payUrl" method="post" v-show="false">
      <div v-for="item in formitems" :key="item.index">
            <input type="text" :name="item.key" :value="item.value" />
      </div>
</form>

pay(){  
   this.$nextTick(()=>{
        document.getElementById('form').submit();  
    })
}

```

重定向问题
- form提交表单或者window.location.href="......."，后台直接response.sendRedirect是可以直接处理重定向的。
- 但是ajax的提交，后台response.sendRedirect是不能重定向的,除非前端配合，后端返回状态码和需要重定向的地址


