---
title: 父组件改变prop的值子组件动态渲染
catalog: true
date: 2018-11-01 13:56:03
subtitle:
header-img:
tags:
    - vue
    - js
---

*Vue父组件向子组件传递一个动态的值，子组件只能获取初始值，不能实时更新？*


最近自己写了个slider的滑动条的组件

```js
// 父组件的调用方式
<slider :min=0 :max=100 :showTip=false :percent="percent" @changeSimilar="getSimilar"></slider>
```
```js
//子组件：

export default{
      props:['min','max','percent','showTip'],
      data(){
        return{
          slider:null,        //滚动条DOM元素
          thunk:null,         //拖拽DOM元素
          scale: this.percent,
          width: null,
          left: null,
          hasTip: this.showTip, // 是否展示数值tip
          per:this.percent,     //父组件传过来的初始相似度整数
        }
      },
      后面省略

```

### 1、子组件修改prop值之后怎么传给父组件？

![1.png](1.png)

在父组件中定义了@changeSimilar="getSimilar",因此在子组件中只要emit这个方法即可

```js
this.$emit('changeSimilar', this.scale)

而父组件中getSimilar这个方法被触发后,就可以拿到这个scale了
getSimilar(scale){
    this.similar = scale;
},

```

### 2、在父组件中要修改了prop值之后子组件怎么动态修改？
因为父组件中有一个重置的功能，所以slider的值要被置为初始值50，试了下父组件的precent的值是改了，但是子组件却没有变化。

这个时候就要用到watch了，去动态监听父组件的传进来的prop的precent

```js
watch: {
    percent(newValue, oldValue) {
        this.per = newValue;
        this.scale = this.per/100;
        this.width = this.slider.offsetWidth * this.scale; //滑动条宽度
        this.left = this.slider.offsetWidth * this.scale -  this.thunk.offsetWidth/2; //滑动条中间按钮的left数值
    }
}
```



#### 监听方法

在子组件中用watch()监听值的改变，不同的类型的要用不同的监听方法
```js
// string类型的
text:function(newValue,oldValue){
    this.getChange();
}

// 对象类型的
object:{
    handler(newValue,oldValue){
        this.getChange();
    },
    deep:true
},

// 数组类型的
array:{
    handler(newValue,oldValue){
    //     for (let i = 0; i < newValue.length; i++) {
    //     　　if (oldValue[i] != newValue[i]) {
    //     　　　　console.log(newValue)
    //     　　}
    // 　　}
        this.getChange();
    },
    deep:true //深度监听
},

```