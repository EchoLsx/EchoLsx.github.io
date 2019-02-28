---
title: commonjs以及es6模块的编写规则
catalog: true
date: 2019-01-21 10:54:39
subtitle:
header-img:
tags:
 - es6
 - commonjs
---

#### 一、Commonjs的编写规则

> commonjs 被广泛使用于nodejs中，webpack也支持

commonjs模块需要向外暴露 module.exports = {},也就是一个对象，默认值为一个空对象
写法如下：
```javascript
// hello.js
module.exports = {
    say: () => {
        console.log("hello");
    }
}
// main.js
const hello = require(./hello.js);
hello.say();
```
另外一种写法   
```javascript
// b.js
exports.hello = ()=>{
    console.log("hi")
}
// main.js
const b = require(./b.js);
b.hello();

// 即exports = module.exports
```
#### 二、es6的编写规则

##### 1、整体抛出
- 被引用的组件也要暴露 export default {}
- 引用时： import Tool from "组件路径"
- Tool.xxx属性或者方法了

```javascript
//tool.js
export default {
    add: (n1,n2)=> {
        return n1+n2
    },
    num: 10
}
//main.js
import Tool from "./tool.js"
console.log(Tool.add(1,2));
console.log(Tool.num);

```

##### 2、单个抛出
```javascript
// tool.js

// 声明直接导出
export const num = 123;

// 先声明后导出
const Sub = {
    add: (n1,n2)=> {
        return n1+n2;
    }
}
export { Sub };

// main.js

import {Sub, num} from "./tool.js"
Sub.add(1,3);
console.log(num);

```


#### 三、export default 与export的区别

1、export与export default均可用于导出常量、函数、文件、模块等

2、你可以在其它文件或模块中通过import+(常量 | 函数 |文件|模块)名的方式，将其导入，以便能够对其进行使用

3、在一个文件或模块中，export、import可以有多个，export default仅有一个

4、通过export方式导出，在导入时要加{ }，export default则不需要

```javascript
//1.export
//demo1.js
export const str = 'hello world'
export function f(a){ return a+1}
对应的导入方式：

//demo2.js
import { str, f } from 'demo1' //也可以分开写两次，导入的时候带花括号

//2.export default
//demo1.js
export default const str = 'hello world'
对应的导入方式：

//demo2.js
import str from 'demo1' //导入的时候没有花括号
使用export default命令，为模块指定默认输出，这样就不需要知道所要加载模块的变量名
```


