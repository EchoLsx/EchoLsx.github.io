---
title: CSS碎片
catalog: true
date: 2018-09-03 09:44:41
subtitle:
header-img:
tags: css
---

> 移动端1px线的显示解决方案
```css
&:after{
    content:"";
    position: absolute;
    bottom:0px;
    left:0px;
    right:0px;
    border-bottom:1px solid #c8c7cc;
    transform:scaleY(.5);
    transform-origin:0 0;
}
```

> 如何优雅的只在当前页面中覆盖第三方U库组件的样式

```
.demo-page /deep/ .el-button i {
    color: #f00;
}
```

当然了这里的深度选择器/deep/是因为我用的scss语言，如果你没有使用less/sass等，可以用>>>符号。


