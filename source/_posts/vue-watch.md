---
title: Vue2 + Vue3 watch侦听器详解
cover: /img/watch.png
---

## 一、什么是侦听器?
开发中我们在data返回的对象中定义了数据，这个数据通过插值语法等方式绑定到template中；当数据变化时，template会自动进行更新来显示最新的数据；但是在某些情况下，我们希望在代码逻辑中监听某个数据的变化，这个时候就需要用侦听器watch来完成。

## 二、Vue2中watch的使用
### 1.基本用法
在vue2的Options API中，我们可以通过watch选项来侦听data或者props的数据变化，当数据变化时执行某一些操作。最常用的有下面前三种。

``` HTML
<input type="text" v-model="cityName"/>
```

``` JS
new Vue({
  el: '#root',
  data: {
    cityName: 'shanghai'
  },
  watch: {
    cityName(newName, oldName) {
      // ...
    }
  } 
})
```

直接写一个监听处理函数，当每次监听到 cityName 值发生改变时，执行函数。也可以在所监听的数据后面直接加字符串形式的方法名：

``` JS
watch: {
    cityName: 'nameChange'
    }
```
这样使用watch时有一个特点，就是当值第一次绑定的时候，不会执行监听函数，只有值发生改变才会执行。如果我们需要在最初绑定值的时候也执行函数，则就需要用到immediate属性。

### 2.immediate和handler
比如当父组件向子组件动态传值时，子组件props首次获取到父组件传来的默认值时，也需要执行函数，此时就需要将immediate设为true。

``` JS
new Vue({
  el: '#root',
  data: {
    cityName: ''
  },
  watch: {
    cityName: {
    　　handler(newName, oldName) {
      　　// ...
    　　},
    　　immediate: true
    }
  } 
})
```

监听的数据后面写成对象形式，包含handler方法和immediate，之前我们写的函数其实就是在写这个handler方法；

immediate表示在watch中首次绑定的时候，是否执行handler，值为true则表示在watch中声明的时候，就立即执行handler方法，值为false，则和一般使用watch一样，在数据发生变化的时候才执行handler。

### 3.deep深度监听
当需要监听一个对象的改变时，普通的watch方法无法监听到对象内部属性的改变，只有data中的数据才能够监听到变化，此时就需要deep属性对对象进行深度监听。

``` HTML
<input type="text" v-model="cityName.name"/>
```

``` JS
new Vue({
  el: '#root',
  data: {
    cityName: {id: 1, name: 'shanghai'}
  },
  watch: {
    cityName: {
      handler(newName, oldName) {
      // ...
    },
    deep: true,
    immediate: true
    }
  } 
})
```

设置deep: true 则可以监听到cityName.name的变化，此时会给cityName的所有属性都加上这个监听器，当对象属性较多时，每个属性值的变化都会执行handler。如果只需要监听对象中的一个属性值，则可以做以下优化：使用字符串的形式监听对象属性:

``` JS
watch: {
    'cityName.name': {
      handler(newName, oldName) {
      // ...
      },
      deep: true,
      immediate: true
    }
  }
```

这样只会给对象的某个特定的属性加监听器。
数组（一维、多维）的变化不需要通过深度监听，对象数组中对象的属性变化则需要deep深度监听。

### 4.其他方式
``` JS
// 字符串方法名
b: "someMethod" 
```

``` JS
// 传入回调数组，逐一调用
e: [
    'handle1',
    function handle2 (val, oldVal) { /* ... */ },
    {
      handler: function handle3 (val, oldVal) { /* ... */ },
      /* ... */
    }
]
```

### 5.使用 $watch 
我们可以在created的生命周期（后续会讲到）中，使用 this.$watchs 来侦听：
* 第一个参数是要侦听的源;
* 第二个参数是侦听的回调函数callback;
* 第三个参数是额外的其他选项，比如deep、immediate;

``` JS
created() {
  this.$watch('message',(newValue, oldValue) => {
    console.log(newValue, oldValue);
  },{deep: true, imediate: true})
}
```

## 三、Vue3中watch和watchEffect的使用
在Composition API中，我们可以使用watchEffect和watch来完成响应式数据的侦听：
* watchEffect用于自动收集响应式数据的依赖；
* watch需要手动指定侦听的数据源；

### 1.watchEffect
当侦听到某些响应式数据变化时，我们希望执行某些操作，这个时候可以使用 watchEffect。
####  基本用法
* 首先，watchEffect传入的函数会被立即执行一次，并且在执行的过程中会收集依赖；
* 其次，只有收集的依赖发生变化时，watchEffect传入的函数才会再次执行；

``` JS
const name = ref("why");
const age = ref(18);

watchEffect(() => {
  console.log("watchEffect执行", name.value, age.value);
})
```

#### watchEffect的停止侦听
如果在发生某些情况下，我们希望停止侦听，这个时候我们可以获取watchEffect的返回值函数，调用该函数即可。
比如在上面的案例中，我们age达到20的时候就停止侦听：

``` JS
const stopWatch = watchEffect(() => {
  console.log("watchEffect执行", name.value, age.value);
});

const changeAge = () => {
  age.value++;
  if(age.value > 20) {
    stopWatch();
  }
}
```

#### watchEffect清除副作用
* 什么是清除副作用呢？比如在开发中我们需要在侦听函数中执行网络请求，但是在网络请求还没有达到的时候，我们停止了侦听器，或者侦听器侦听函数被再次执行了。那么上一次的网络请求应该被取消掉，这个时候我们就可以清除上一次的副作用。
* 在我们给watchEffect传入的函数被回调时，其实可以获取到一个参数：onInvalidate; 当副作用即将重新执行 或者 侦听器被停止 时会执行该函数传入的回调函数;我们可以在传入的回调函数中，执行一些清除工作。

``` JS
const stopWatch = watchEffect((onInvalidate) => {
  console.log("watchEffect执行", name.value, age.value);
  const timer = setTimeout(() => {
    console.log("2s后执行的操作");
  },2000);
  onInvalidate(() => {
    clearTimeout(timer);
  })
});
```

#### watchEffect的执行时机
默认情况下，组件的更新会在副作用函数执行之前:如果我们希望在副作用函数中获取到元素，是否可行呢?
``` HTML
<template>
  <div>
    <h2 ref="titleRef">我是标题</h2>
  </div>
</template>
```
``` JS
import { ref } from "vue";
export default {
  setup() {
    const titleRef = ref(null);
    watchEffect(() => {
      console.log(titleRef.value);
    })
    return {
      titleRef
    }
  }
}
```
这里会打印两次结果：

``` 
null
<h2></h2>
```

这是因为setup函数在执行时就会立即执行传入的副作用函数，这个时候DOM并没有挂载，所以打印为null;而当DOM挂载时，会给title的ref对象赋值新的值，副作用函数会再次执行，打印出来对应的元素。

#### 调整watchEffect的执行时机
如果我们希望在第一次的时候就打印出来对应的元素,就需要改变副作用函数的执行时机；它的默认值是pre，它会在元素 挂载 或者 更新 之前执行；所以我们会先打印出来一个空的，当依赖的title发生改变时，就会再次执行一次，打印出元素。调整方法如下：

``` JS
let h2ElContent = null;
watchEffect(() => {
  h2ElContent = titleRef.value && titleRef.value.textContent;
  console.log(h2ElContent);
},{
  flush:"post"
})
```
flush 选项还接受 sync，这将强制效果始终同步触发。然而，这是低效的，应该很少需要。

### 2.watch
watch的API完全等同于组件watch选项的Property。
* watch需要侦听特定的数据源，并在回调函数中执行副作用；
* 默认情况下它是惰性的，只有当被侦听的源发生变化时才会执行回调；
与watchEffect的比较，watch允许我们：
* 懒执行副作用（第一次不会直接执行）；
* 更具体的说明当哪些状态发生变化时，触发侦听器的执行；
* 访问侦听状态变化前后的值；

#### 侦听单个数据源
watch侦听函数的数据源有两种类型：
一个getter函数：但是该getter函数必须引用可响应式的对象（比如reactive或者ref）；

``` JS
const state = reactive({
  name: 'why',
  age: 18
})

watch(() => state.name, (newValue, oldValue) => {
  console.log(newValue, oldValue)
})

const changeName = () => {
  state.name = "why1"
}
```

直接写入一个可响应式的对象，reactive或者ref（比较常用的是ref）

``` JS
const name = ref("kobe")

watch(name, (newValue, oldValue) => {
  console.log(newValue, oldValue)
})

const changeName = () => {
  name.value = "kobe1"
}
```

#### 侦听多个数据源
侦听器还可以使用数组同时侦听多个源：

``` JS
const name = ref("why")
const age = ref(18)

const changeName = () => {
  name.value = "james"
}

watch([name, age], (newValues, oldValues) => {
  console.log(newValues, oldValues)
})
```

#### 侦听响应式对象
如果我们希望侦听一个数组或者对象，那么可以使用一个getter函数，并且对可响应对象进行解构

``` JS
const names = reactive(["abc","cba","nba"])

watch(()=> [...name], (newValue, oldValue) => {
  console.log(newValue, oldValue)
})

const changeName = () => {
  name.push("why")
}
```

#### watch的选项
如果我们希望侦听一个深层的侦听，那么依然需要设置 deep 为true;也可以传入 immediate 立即执行；

``` JS
const state = reactive({
  name: "why",
  age: 18,
  friend: {
    name: "kobe"
  }
})

watch(()=> state, (newValue, oldValue) => {
  console.log(newValue, oldValue)
},{deep: true, immediate: true})

const changeName = () => {
  state.friend.name = "aaa"
}
```

---
* 参考:[vue中watch的详细用法](https://www.cnblogs.com/shiningly/p/9471067.html)