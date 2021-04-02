---
title: Vue3.x笔记
catalog: true
date: 2021-04-02 15:50:36
subtitle:
header-img:
tags:
    - vue
    - vue3.x
---

##  创建API

Vue 2.x是使用`new Vue()`，Vue 3.x需要使用新的 `createApp()`方法创建一个Vue对象的实例。

在旧的API下，我们添加的任何全局配置（plugins，mixins，原型属性等）都将永久更改全局状态。
在新的API下，调用 createApp 将返回一个新的app实例，该实例不会被应用于其他实例的任何全局配置污染。

```js
// vue 2.x
import Vue from 'vue'
import App from './App.vue'

Vue.config.ignoredElements = [/^app-/]
Vue.use(/* ... */)
Vue.mixin(/* ... */)
Vue.component(/* ... */)
Vue.directive(/* ... */)
Vue.prototype.customProperty = () => {}

new Vue({
  render: h => h(App)
}).$mount('#app')


// vue3.x
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)
app.config.isCustomElement = tag => tag.startsWith('app-')
app.use(/* ... */)
app.mixin(/* ... */)
app.component(/* ... */)
app.directive(/* ... */)
app.config.globalProperties.customProperty = () => {}

app.mount('#app')

```

## Composition API

允许使用setup功能来定义组件功能。

- setup是在组件创建之前调用的，setup执行时，还没有组件的this。
- setup的参数有两个props和context
- context可以访问的属性：props、attrs、slots、emit

Composition API允许更好的代码组织和组件之间的逻辑组织和代码重用（因为mixin本质上是一种反模式）。

### state

使用`ref`、`reactive`来生成响应式变量，返回一个带有value属性的对象。

> - ref：返回一个带有value属性的对象，也是响应式的
> - reactive：通过proxy返回一个对象的拷贝。是响应式的。修改该对象不会影响到原对象



```js
// 原写法
export default {
  data() {
    return {
      count: 0,
      users: [],
      list: [1, 2, 3],
      book: { title: 'Vuejs' }
    }
  },
  created() {
    console.log(this.count)
    console.log(this.users)
    console.log(this.list)
    console.log(this.book)
  }
}

// setup
import { ref, reactive } from 'vue'
export default {
  setup() {
    const counter = ref(0)
    const users = ref([])
    const list = reactive([1, 2, 3])
    const book = reactive({ title: 'Vuejs' })
    console.log(counter.value)
    console.log(users.value)
    console.log(list)
    console.log(book)
    return {
      // 模板或者其他需要用到，就在setup中 return 出去
      users,
      list
    }
  }
}
```
### props

使用`toRefs`将`props`转成响应式变量

```js
export default {
  props: {
    user: {
      type: Object,
      default: () => ({})
    }
  }
}

// setup中如果要解构props（比如用来计算某个计算属性），可以使用toRefs(props)
import { toRefs } from 'vue'
export default {
  props: {
    user: {
      type: Object,
      default: () => ({})
    }
  },
  setup(props) {
    const { user } = toRefs(props)
    console.log(user.value)
  }
}
```
### computed

```js
// 原写法
export default {
  data() {
    return {
      counter: 0,
      c: 1
    }
  },
  computed: {
    twiceTheCounter() {
      return this.counter * 2
    },
    plusOne: {
      get: function() {
        return this.c + 1
      },
      set: function(val) {
        this.c = val - 1
      }
    }
  }
}

// setup
import { ref, computed } from 'vue'
export default {
  setup() {
    const counter = ref(0)
    const twiceTheCounter = computed(() => counter.value * 2)
    // 可以使用get set
    const c = ref(1)
    const plusOne = computed({
      get: () => c.value + 1,
      set: val => {
        c.value = val - 1
      }
    })
  }
}
```

### watch

`watchEffect`立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数

当 watchEffect 在组件的 setup() 函数或生命周期钩子被调用时，侦听器会被链接到该组件的生命周期，并在组件卸载时自动停止

```js
const count = ref(0)
watchEffect(() => console.log(count.value))
```

```js
// 原写法
export default {
  data() {
    return {
      counter: 0,
      books: [
        { title: 'Vuejs' }
      ]
    }
  },
  watch: {
    counter(newVal, oldVal) {
      console.log(newVal)
      console.log(counter.value)
    },
    books: {
      handler: function(newList, oldList) {
        console.log(newList)
      },
      deep: true
    }
  }
}

// setup
import { ref, watch } from 'vue'
export default {
  setup() {
    const counter = ref(0)
    watch(counter, (newVal, oldVal) => {
      console.log(newVal)
      console.log(counter.value)
    })
    watch(books, (newList, oldList) => {
      console.log(newList[0].title)
      console.log(books.value[0].title)
    }, { deep: true })
  }
}
```
### 生命周期

`setup`中使用生命周期，是在前面添加`on`。
beforeCreate和created生命周期的代码，可以直接写在setup方法中。
Vue2.x中的beforeDestory和destoryed，在Vue3.x中已经修改为beforeUnmount和unmounted

### methods

直接在setup中定义即可，外部需要使用的话，return出去

### Template Refs

`Template refs` 和 `reactive refs`是统一的。

可以在setup中像平常一样声明一个ref，然后在setup()中返回

```js
<template>
  <div ref="form">This is a form element</div>
</template>

<script>
import { ref, onMounted } from 'vue'
export default {
  setup() {
    // 这里的 form， 要跟模板中的（ref="form"）一样
    const form = ref(null)

    onMounted(() => {
      // DOM元素会在渲染完成后赋值
      console.log(form.value)
    })

    return {
      // 要返回出去才可以
      form
    }
  }
}
</script>

```

### 代码分离

Vue 2.x中，页面的某个功能会分散在data、computed、生命周期、methods、watch等地方。代码比较分散。功能较多时比较混乱，不利于维护

Composition API可以将相同功能的代码集中管理。甚至可以分离到不同的js文件中。那么 Composition API 在哪里使用呢？

我们要知道 Composition API 是代码逻辑组合和复用的方案，不是替代组件的方案。换言之，原来组件该怎么划分，现在继续怎么划分，丝毫不受影响。
但是`组件内的代码的组织，组件间代码的复用`，则和 Composition API 息息相关。


![Composition API 用在哪里.jpg](compisitionApi.jpg)

setup 是我们的代码控制器，尽量不要写入大量的业务逻辑代码​。始终秉承着为 “控制器分忧，为 setup 减负” 的思想来撸代码！


### Provide / Inject


`<!-- src/components/Father.vue -->`
```js
<script>
import { provide, reactive, ref, readonly } from 'vue'

export default {
  setup() {
    // 改成响应式写法
    const location = ref('North Pole')
    // 写一个方法更新location
    const updateLocation = () => {
      location.value = 'South Pole'
    }

    provide('location', readonly(location))
    provide('updateLocation', updateLocation)
  }
}
</script>
```
`<!-- src/components/child.vue -->`

```js
<script>
import { inject } from 'vue'

export default {
  setup() {
    const userLocation = inject('location', 'The Universe')

    return {
      userLocation,
      updateUserLocation
    }
  }
}
</script>
```

### CSS Module

vue2.x写法

![CSS Moulde使用方式.jpg](cssModules.jpg)

Composition API中使用CSS Module可以通过useCssModule
```js
import { useCssModule } from 'vue'

export default {
  // ...
  setup() {
    const style = useCssModule()
    console.log(style.home)
    return {
      // 输出出去，template中就可以通过 style.xxx 使用了
      // 没有下面这句的话，template中是通过 $style.xxx 使用的
      style
    }
  },
  // ...
}
```

### 异步组件

```js
// vue2.x写法
const asyncComp = () => import('./Foo.vue')

// vue3.x新写法
import { defineAsyncComponent } from 'vue'
const asyncComp = defineAsyncComponent(() => import('./Foo.vue'))
```
### 组件

#### Fragment

Vue 3.x 后组件不再限制 template 中根元素的个数（旧的版本之前是只能有一个根元素）。

`<template>` 中不再局限于单一的根节点

#### Teleport
单页面应用，都是将DOM挂载到某个节点上面的。

如果需要在那个节点外面挂载一些DOM，Vue 2.x的做法都是直接操作DOM。而Vue的原则就是尽量不让开发者直接操作DOM，这些事都统一由 vue 来完成

Vue 3.x提供了一个组件Teleport。该组件只是单纯的把定义在其内部的内容转移到目标元素中，在元素结构上不会产生多余的元素，当然也不会影响到组件树。


比如我们要将一个组件挂载id为endofbody的节点下面

修改public/index.html，添加目标节点
```js
<body>
  <!-- ... -->
  <div id="app"></div>
  <div id="endofbody"></div>
  <!-- built files will be auto injected -->
  <!-- ... -->
</body>
代码中添加Teleport
<teleport to="#endofbody">
  <child-component name="John" v-if="visible"/>
</teleport>
```


## 过滤器

vue 3.x中过滤器filters已删除，不再受支持。

```js
// main.js
const app = createApp(app)

app.config.globalProperties.$filters = {
  currencyUSD(value) {
    return '$' + value
  }
}
<!-- 组件中使用 -->
<p>{{ $filters.currencyUSD(accountBalance) }}</p>
```

## emit
$emit()方法在用法上没变，但需要额外多定义emits

emits可以是字符串数组或者对象
```js
// emits 字符串数组
export default {
  emits: ['check'],
// 放在setup中的写法
  setup(props, context) {
    const handleTest = () => {
      context.emit('check')
    }
    return {
      handleTest
    }
  },
}
```

## .sync
vue 3.0又去掉了.sync，合并到了v-model里
```js
<ChildComponent :title.sync="pageTitle" />

<!-- 替换为 -->

<ChildComponent v-model:title="pageTitle" />
```












