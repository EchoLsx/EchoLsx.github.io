---
title: 写一个Vue自定义drag指令
catalog: true
date: 2019-05-16 11:03:12
subtitle:
header-img:
tags:
    - vue
---

> 实现一个弹窗在屏幕可视范围内随意拖动

#### 先上代码
<!-- vdrag.js -->
```javascript
let vDrag = {};
let drag = {
    inserted: (el) => {
        el.onmousedown = function (e) {
            let disX = e.clientX - el.offsetLeft;
            let disY = e.clientY - el.offsetTop;
            document.onmousemove = function (e) {
                let iL = e.clientX - disX;
                let iT = e.clientY - disY;
                let maxL = document.documentElement.clientWidth - el.offsetWidth;
                let maxT = document.documentElement.clientHeight - el.offsetHeight;
                iL <= 0 && (iL = 0);
                iT <= 0 && (iT = 0);
                iL >= maxL && (iL = maxL);
                iT >= maxT && (iT = maxT);
                el.style.left = iL + 'px';
                el.style.top = iT + 'px';
                
                return false;
            };
            document.onmouseup = function () {
                document.onmousemove = null;
                document.onmouseup = null;
            };
        };
    }
};
vDrag.install = function (Vue) {
    Vue.directive('drag', drag);
};
export default vDrag;
```

上面这个自定义指令呢，实现的是让某一个el元素可以在可视范围内任意拖动，里面对一些拖动位置边界做了处理。目前拖动范围是document，后期可以优化这个对象为传参dom，更加地灵活。

#### 使用方式
全局使用
<!-- main.js -->
```js
import vDrag from "@/directives/vdrag.js";
Vue.use(vDrag);

```
```js
<div class="demo" v-drag></div>

```


