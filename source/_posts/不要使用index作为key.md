---
title: 不要使用index作为key
catalog: true
date: 2018-10-18 10:57:11
subtitle:
header-img:
tags:
    - vue
---

来看一段官方解释：

> key 的特殊属性主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试修复/再利用相同类型元素的算法。使用 key，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

> 有相同父元素的子元素必须有独特的 key。重复的 key 会造成渲染错误。


然后你看到很多使用数组的下标index来作为key的例子

```js
<div v-for="(item, index) in list" :key="index" >{{item.name}}</div>

```
没错，这种方式确实可以绕开vue或者react的警告，但是又什么危险呢？


来看一个栗子：
```js
<div v-for="(item, index) in list" :key="index" >{{item.name}}</div>
```
在数组中间插入一条数据4
```json

const list = [
    {
        id: 1,
        name: 'test1',
    },
    {
        id: 4,
        name: '我是插队的那条数据',
    }
    {
        id: 2,
        name: 'test2',
    },
    {
        id: 3,
        name: 'test3',
    },
]

```
|   之前        |   之后       |
|   --------   | :-----       |
| key: 0  index: 0 name: test1  |   key: 0  index: 0 name: test1 |
| key: 1  index: 1 name: test2  |   key: 1  index: 1 name: 我是插队的那条数据 |
| key: 2  index: 2 name: test3 |    key: 2  index: 2 name: test2| 
| | key: 3  index: 3 name: test3 |

发现除了第一个数据可以复用之前的之外,另外三条数据都需要重新渲染;

是不是很惊奇,我明明只是插入了一条数据,怎么三条数据都要重新渲染?而我想要的只是新增的那一条数据新渲染出来就行了，而用item.id就不会出现这样的问题。

---
 - 所以key的值要保持**稳定**且**唯一**

在设置key的时候最好不要用index，因为key值需要是独一无二的且稳定的，这样在更新数据的时候，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过（通过key值确认）的每个元素。

用下标的话，如果数组发生改变，就有可能出现相同的key值，这样的话Vue又要比较这两个相同key值的dom元素（本来通过key值就能确定的）


---

同理在react中使用map渲染列表时,也是必须加key,且推荐做法也是使用id,也是这个原因;

其实,真正的原因并不是vue和react怎么怎么,而是因为Virtual DOM 使用Diff算法实现的原因。一句话，key的作用主要是为了高效的更新虚拟DOM。
