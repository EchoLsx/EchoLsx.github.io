---
title: 从Promise来看JavaScript中的Event Loop
catalog: true
date: 2018-08-10 17:22:26
subtitle: 
header-img: "Demo.png"
tags: JavaScript
---
### 一、先把题目扔出来

```javascript
(function test() {
    setTimeout(function() {console.log(4)}, 0);
    new Promise(function executor(resolve) {
        console.log(1);
        for( var i=0 ; i<10000 ; i++ ) {
            i == 9999 && resolve();
        }
        console.log(2);
    }).then(function() {
        console.log(5);
    });
    console.log(3);
})()
```

首先，这个题目是我在入职的新人培训中看到的，粗略一看，这个题目中包括了一个promise，一个setimeOut，一个console，当时只能想到promise是声明的时候就执行的，完全不知道接下里要讲的微任务MicroTask和宏任务MacroTask。

- 所以输出的是顺序是怎么样的？
```javascript
1,2,3,5,4
```
#### 那么问题来了，为什么输出结果是 1,2,3,5,4 而非 1,2,3,4,5 ？

- 1、Promise.then 是异步执行的，而创建Promise实例是同步执行的，也就是开头说的promise里面的代码是声明的时候执行的
- 2、setTimeout 的异步和 Promise.then 的异步看起来 “不太一样” ——至少是不在同一个队列中。

- 3、macro-task和micro-task。宏任务和微任务。
一般来说宏任务就是所谓的task，微任务可以被叫做job。一个task可能会包含job，task有一个队列queue，要一个一个task执行。一个task中先执行主要任务，再执行微任务

#### 所以整个过程就是：

- 当前task运行，执行代码。首先 setTimeout 的callback被添加到tasks queue中；
- 当前task 实例化promise，输出 1 ; promise resolved；输出 2 ;
- promise.then 是微任务，它的callback被添加到microtasks queue中；所以先输出console的3 ;
- 已到当前task的end，执行当前task的microtasks，输出 5 ;
- 执行下一个task，输出 4 。


