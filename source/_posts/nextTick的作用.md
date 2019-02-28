---
title: $nextTick的作用
catalog: true
date: 2018-08-15 12:11:54
subtitle:
header-img:
tags: vue
---

*问题由来：项目中有一个弹出dialog后自动给tree里面的节点选上的需求*

刚开始我是这么写的：
```javascript
handleUpdate(row) {
    this.temp = Object.assign({}, row)
    this.dialogStatus = 'update'
    this.dialogFormVisible = true
    this.$refs.roletree.setCheckedKeys(row.role)
}
```
大概就是显示弹窗后，勾选上tree里面的某些节点,刷刷写完然后保存预览emmmmmmmm，好像不太对
![error.jpg](error.jpg)
打断点查看了下是this.$refs就取不到了

查看了下文档，只有在挂载完成以后，才能取到$refs，也就是说dialog还没挂载完的时候是取不到$ref的

>$nextTick 是在下次 DOM 更新循环结束之后执行延迟回调，在修改数据之后使用 $nextTick，则可以在回调中获取更新后的 DOM

所以很明显啦，$nextTick 就派上用场了

```javascript
handleUpdate(row) {
      this.temp = Object.assign({}, row)
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs.roletree.setCheckedKeys(row.role)
      })
    }
```
这样子就可以成功实现需求了
