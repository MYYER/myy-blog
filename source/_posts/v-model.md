---
title: Vue3中v-model的使用
cover: /img/vue3.jpg
---

使用Vue的开发人员肯定对Vue2中的v-model非常熟悉，大家平常写起来也非常顺手，但是v-model在vue3中发生了较大的变化。这里简单阐述一下Vue3中v-model新语法的使用以及为什么有新的语法。

## Vue2.x中v-model的使用以及问题
### v-model在vue2.x中的使用方式
我们首先看一下vue2.x中v-model的使用。

``` 
<ChildComponent v-model = "title />
```

它实际上是下面这种写法的简写：

``` 
<ChildComponent :value = "title"  @input = "title = $event" />
```

也就是说，它实际上是传递一个属性value，然后接收一个input事件。

### vue2.x中v-model的问题
虽然v-model在vue2.x中使用起来很方便，也很简单，但是它存在一个问题：那就是传递下去的必须是value值，接收的也必须是input事件。事实上，并不是所有的元素都适合传递value，比如<input type="checkbox">，当type属性的值为checkbox是，实际上是checked这个属性的存在用来表示是否被选中，而value值是另外的含义。而且有些时候，一些组件并不是通过input来进行触发事件。也就是说value和input事件在大多数情况下能够适用，但是存在value另有含义，或者不能使用input触发的情况，这时候我们就不能使用v-model进行简写了。为了解决这个问题，尤雨溪在Vue2.2中，引入了model组件选项，也即是说你可以通过model来指定v-model绑定的值和属性.如下所示：

``` 
<ChildComponent v-model="title" />
```

在子组件中这样设置：

``` 
export default {
  model: {
    prop: 'title',   // v-model绑定的属性名称
    event: 'change'  // v-model绑定的事件
  },
  props: {

    value: String,   // value跟v-model无关
    title: {         // title是跟v-model绑定的属性
      type: String,
      default: 'Default title'
    }
  }
}
```

通过上面的代码，我们可以看到通过设置model选项，我们就可以直接使用指定的属性和事件，而不需要必须使用value和input了，value和input可以另外它用了。

## Vu3中v-model的使用
### Vue3中v-model的基础使用
我们通过上面知道vue2.x中既然v-model的主要原因是由于value和input事件可能另有它用，那么我们可不可以直接使用另外的属性和方法，而不需要去通过model进行定义。vue3中就实现了这个功能，v-model绑定的不再是value，而是modelValue，接收的方法也不再是input，而是update:modelValue。使用方法如下：

``` 
<ChildComponent v-model = "title">
```

它是下面这种写法的简写：

``` 
<ChildComponent :modelValue = "title" @update:modelValue = "title = $event">
```

在子组件中写法是：

``` 
export default defineComponent({
    name:"ValidateInput",
    props:{
        modelValue:String,   // v-model绑定的属性值
    },
    setup(){
        const updateValue = (e: KeyboardEvent) => {
          context.emit("update:modelValue",targetValue);   // 传递的方法
        }
    }
}
```

### 更换v-model的参数
vue3中使用了modelValue来替代value，但是modelValue不太具备可读性，在子组件的props中看到这个都不知道是什么。 因此，我们希望能够更加见名知意。可以通过:xxx传递参数xxx，更改名称，而不是像vue2中使用model选项。使用方式如下：

``` 
<ChildComponent v-model:title="title" />
```

那么在子组件中，就可以使用title代替modelValue。

``` 
export default defineComponent({
    name:"ValidateInput",
    props:{
        // modelValue:String,
        title:String,   // title替代了modelValue
    },
    setup(){
        const updateValue = (e: KeyboardEvent) => {
        //   context.emit("update:modelValue",targetValue);   // 传递的方法
          context.emit("update:title",targetValue);   // 传递的方法
        }
    }
}
```

也就是说，我们最终的使用方法是：

``` 
<ChildComponent v-model:title="title"  />
// 或者
<ChildComponent :title="title" @update:title = "title = $event" />
```

以上就是vue2中的v-model的使用以及问题，以及vue3中v-model的新的使用语法。