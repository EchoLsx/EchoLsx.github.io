---
title: 解决element的tree控件无单选API问题
catalog: true
date: 2018-09-04 20:32:01
subtitle:
header-img:
tags: vue
---

### 1、无单选API问题 
*问题由来：需要实现tree的单选的需求，很可惜，老版本的element没有单选的API可以使用，只好自己实现*


这是一段在vue中的代码

```html
<el-tree
    :data="catlogTree"
    show-checkbox
    default-expand-all
    check-strictly
    node-key="id"
    ref="catlog"
    highlight-current
    :props="defaultProps"
    @check-change="handleCheckChange">
</el-tree>
```

js部分：


```javascript

data(){
    return {
        i:0,
    }
},
methods:{
    handleCheckChange(data, checked, indeterminate) {
        this.i++
        if (this.i % 2 === 0) {
            if (checked) {
                this.$refs.catlog.setCheckedNodes([])
                this.$refs.catlog.setCheckedNodes([data])
                // 交叉点击节点
            } else {
                this.$refs.catlog.setCheckedNodes([])
                this.i = 0
                // 点击已经选中的节点，置空
            }
        }
    }
}
```

- ref  vue 的引用信息
- node-key="id"   树节点的唯一标识
- :props="defaultProps"  tree树的默认属性修改
- check-strictly   父节点不相关联子节点
- @check-change="handleClick"  当前节点变化时处理事件


### 2、低版本Element-ui
的tree，选择部分子节点后不会返回父节点id= = 

解决方案有2个：

1、对tree添加check-strictly 属性,官方的解释：
> 在显示复选框的情况下，是否严格的遵循父子不互相关联的做法，默认为 false

让父子不关联，这样子避开这个bug，不过操作很不优雅，选择子节点还得点一下父节点，而如果层级很多的话，就很不人性化了

2、修改源码 = =
首先找到element-ui.common.js文件 如下 具体看你工程下的node_modules

node_modules\element-ui\lib\element-ui.common.js

找到这段代码
```javascript
if (child.checked && (!leafOnly || leafOnly && child.isLeaf)) {
   checkedNodes.push(child.data);
}
```

将其改为
```javascript
if ((child.checked || child.indeterminate) && (!leafOnly || leafOnly && child.isLeaf)) {
   checkedNodes.push(child.data);
  }
```
保存文件，重启项目，就可以返回父节点id了

这样的做法从根本上解决了插件的bug，不过缺点就是当你源码打包上传的问题，别人不知道的话就npm重新下载就被覆盖了。

综合来看，看项目需求吧，不过还是建议用最新版本的element，老的api功能太少了。

