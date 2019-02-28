---
title: iphoneX的适配方案
catalog: true
date: 2018-10-16 10:52:26
subtitle:
header-img:
tags: 
    - iphoneX
    - css
---


iphoneX的样式适配，一般情况下不需要额外处理，主要针对页面中的`position:fixed`元素在iphoneX屏幕安全距离中的定位问题进行适配。

例如有一个`position:fixed; bottom:0`的`button`，适配方案如下：

1.页面高度等于或小于视口高度
此时`body`的实际高度小于等于视口高度，`html`文档流的高度没有超出“iphone安全区域”，`button`正常定位于页面底部，此时不需要对页面做特殊处理。

包括`body, html {position: relative; width: 100%; height: 100%; overflow: hidden;}`这样的整屏页面。

2.页面高度大于视口高度
此时`body`的实际高度大于视口高度，`button`定位位置无法超出“iphone安全区域”，`html`正常文档流会在安全区域外显示，此时会出现`button`没有真正贴在视口底部的“错误”布局现象。
即使`bottom:-50px`，此时因为安全范围的设定，`button`超出安全区域的部分会被截断无法显示。

```<meta content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover" name="viewport">```
通过增加`viewport-fit=cover`修改网页的对于安全区域的适配方式为‘cover’，此时`fixed`元素会可超出安全范围，实际定位依据整个视口。


```bottom: env(safe-area-inset-bottom);```

修改`button`的定位，`env(safe-area-inset-bottom)`表示iphoneX底部的安全距离，仅当`viewport-fit=cover`的时候有效，支持嵌入css3计算函数，用以适配不是贴边的定位元素，例如：
`bottom: calc(.98rem + env(safe-area-inset-bottom));`，元素实际定位在.98rem加上安全距离的位置。


一般来说没有fixed在底部的button下面的方案就够用了
```
<meta content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover" name="viewport">
body {
    padding-top: env(safe-area-inset-top);
    padding-left: env(safe-area-inset-left);
    padding-right: env(safe-area-inset-right);
    padding-bottom: env(safe-area-inset-bottom);
}
```








