---
title: JS的EventLoop之宏任务与微任务
catalog: true
date: 2019-03-28 11:25:56
subtitle:
header-img:
tags:
    - JavaScript
---
### EventLoop是什么

> JavaScript的事件分两种，宏任务(macro-task)和微任务(micro-task)

- 宏任务：包括整体代码script，setTimeout，setInterval
- 微任务：Promise.then(非new Promise)，process.nextTick(node中)

- 事件的执行顺序，是```main script运行结束后，先执行微任务，然后执行宏任务```，任务可以有同步任务和异步任务。同步的进入主线程，异步的进入Event Table并注册函数，异步事件完成后，会将回调函数放入Event Queue中(```宏任务和微任务是不同的Event Queue```)，同步任务执行完成后，会从Event Queue中读取事件放入主线程执行，回调函数中可能还会包含不同的任务，因此会循环执行上述操作。以此叫做EventLoop。

PS： 概念问题

有时候会有版本是```宏任务>微任务>宏任务```，在这里笔者需要讲清楚一个概念，以免混淆。这里有个main script的概念，就是一开始执行的代码（代码总要有开始执行的时候对吧，不然宏任务和微任务的队列哪里来的），这里被定义为了宏任务（笔者喜欢将main script的概念单独拎出来，不和两个任务队列混在一起），然后根据main script中产生的微任务队列和宏任务队列，分别清空，这个时候是先清空微任务的队列，再去清空宏任务的队列。

### 版本一
```js
setTimeout(()=>{
   console.log(1)  //宏任务
},0)
Promise.resolve().then(()=>{
   console.log(2) // 微任务
})
console.log(3) // main script
输出：
3
2
1
```
> 笔者答案：这个是属于Eventloop的基础概念问题。main script运行结束后，会有微任务队列和宏任务队列。微任务先执行，之后是宏任务。

### 版本二

这一个版本，考验一下对于Promise的理解，关于Promise的executor以及then的执行方式

```js
setTimeout(()=>{
   console.log(1) 
},0)
var a=new Promise((resolve)=>{
    console.log(2)
    resolve()
}).then(()=>{
   console.log(3) 
}).then(()=>{
   console.log(4) 
})
console.log(5)
// 输出
2
5
3
4
1
```
此题看似在考Eventloop，实则考的是对于Promise的掌握程度。Promise的then是微任务大家都懂，但是这个then的执行方式是如何的呢，以及Promise的executor是异步的还是同步的？


> 正确示范：这个要从Promise的实现来说，Promise的executor是一个同步函数，即非异步，立即执行的一个函数，因此他应该是```和当前的任务一起执行的```。而Promise的链式调用then，每次都会在内部生成一个新的Promise，然后执行then，在执行的过程中不断向微任务(microtask)推入新的函数，因此直至微任务(microtask)的队列清空后才会执行下一波的macrotask. 也就是说有多少then都会先执行完，再执行其他宏函数。2，5，（3，4） 1

### 版本三

```js
new Promise((resolve,reject)=>{
    console.log("promise1")
    resolve()
}).then(()=>{
    console.log("then11")
    new Promise((resolve,reject)=>{
        console.log("promise2")
        resolve()
    }).then(()=>{
        console.log("then21")
    }).then(()=>{
        console.log("then23")
    })
}).then(()=>{
    console.log("then12")
})
最终结果 [promise1,then11,promise2,then21,then12,then23]
```

Ready GO

第一轮

 - current task: promise1是当之无愧的立即执行的一个函数，参考上一章节的executor，立即执行输出[promise1]
- micro task queue: [promise1的第一个then]

第二轮

- current task: then1执行中，立即输出了then11以及新promise2的promise2
- micro task queue: [新promise2的then函数,以及promise1的第二个then函数]

第三轮

- current task: 新promise2的then函数输出then21和promise1的第二个then函数输出then12。
- micro task queue: [新promise2的第二then函数]

第四轮

- current task: 新promise2的第二then函数输出then23
- micro task queue: []

END







