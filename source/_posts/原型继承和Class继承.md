---
title: 原型继承和Class继承
catalog: true
date: 2021-04-07 15:38:56
subtitle:
header-img:
tags:
    - javascript
---

> 首先要知道Class只是一个语法糖，js并不存在类, Class只是语法糖，Class的本质是一个函数

```js
class Person {}
Person instanceof Function // true
```
### ES5 和 6 继承的区别：

ES6 继承中子类需要调用 super() 才能拿到子类，ES5 的话是通过 call 这种绑定的方式


```js
// es5 
function Parent(value) {
    this.val = value
}
Parent.prototype.getValue = function() {
    console.log(this.val)
}
function Child(value) {
    Parent.call(this, value)
}
Child.prototype = new Parent()

const child = new Child(1)
child.getValue() // 1

```
以上继承的方式核心是在子类的构造函数中通过 Parent.call(this) 继承父类`属性`，然后改变子类的原型为new Parent() 来继承父类`函数`。

```js
// class 继承
class Parent {
    constructor(value) {
        this.val = value
    }
    getValue() {
        console.log(this.val)
    }
}
class Child extends Parent {
	constructor(value) {
	    super(value)
	    this.val = value
	}
}

let child = new Child(1)
child.getValue() // 1
```

class 实现继承的核心在于使用 `extends` 表明继承自哪个父类，并且在子类构造函数中必须调用 `super` ，因为这段代码可以看成 Parent.call(this, value)

---

一条class继承语句存在两条继承链: 一条实现属性继承；一条实现方法继承
```js
class A extends B {} 
// 等价于
A._proto_ = B
A.prototype._proto_ = B.prototype
```

### 图解

![ES5中的继承](es5.jpg)

![ES6中的继承](es6.jpg)



