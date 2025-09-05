# 前端
在实现一些高阶组件的时候我们往往需要传入自定义的渲染内容。
这里列出常用的4种解决方案：
- slot
- vhtml
- h函数
- jsx

这时候一般使用插槽实现即可，
但是有些组件是通过函数调用，就只能通过传入vnode的方式实现动态渲染组件
或者子组件是通过参数动态创建，则用slot就不好操作

 # slot 
一般要实现自定义渲染内容分发，用官方的slot插槽实现即可。

缺点：对于动态渲染的组件需要加很多判断，对于通过函数创建的组件使用不友好。

Parent.vue
 ```vue
<template>
     <h1>slot:</h1>
    <div class="box">
        <h2>parent</h2>
        <Child #default="scoped">
            <h2>child</h2>
            <span> 【这是Parent传入的内容】 </span>
            <span>  【{{scoped.subMsg}}】</span>
        </Child>
    </div>
</template>
<script setup lang="ts">
import Child from './Child.vue'
</script>
 ```

Child.vue
 ```vue
<template>
  <div class="box"> 
    <slot subMsg='【这是child的内容】'></slot>  <!-- 这里渲染父组件传入的内容 -->
  </div>
</template> 
 ```


 # vhtml 
 用vhtml，其实也是可以实现，但是有被注入攻击风险，而且只支持纯html的标签。

 Parent.vue
 ```js
<template>
  <h1>vhtml:</h1>
  <div class="box"> 
     <h2>parent</h2>
    <Child :htmlString="'<h2>child</h2><p style=color:red;>【这是Parent传入的内容】</p>'" />
  </div>
</template>

<script setup>
import Child from './Child.vue'
</script>
</script>
 ```

Child.vue
 ```vue
<template> 
  <div class="box"> 
    <div v-html="htmlString"></div> 
  </div>
</template>

<script setup>
defineProps({
  htmlString: {
    type: String,
    required: true
  }
})
</script>
 ```

 # h函数
vue种的slot其实都是语法糖，最终还是得转化成vnode渲染。所以直接通过h函数(createVNode),也是能实现

> 注意：由于vnode 在vue文件里，不能直接


 Parent.vue
 ```vue
<template>
  <h1>h + < component is >: </h1> 
  <div class="box">
    <h2>parent</h2>
    <Child :renderContent="myRenderFunction" />
  </div>

</template>

<script setup>
import Child from './Child.vue'
import { h } from 'vue'
const myRenderFunction = (propsFromChild) => {
  return h(
    'div',
    { style: { color: 'green', border: '1px solid #ccc', padding: '10px' } },
    [
      h('h2', `child`),
       h('span', `【这是Parent传入的内容】`),
       h('span', `${propsFromChild.message}`)
    ]
  )
}
</script>
 ```

由于vue3支持多种定义组件的方式，可以参照这篇文章：https://juejin.cn/post/7545948157110304804

这里组件通过 
- template + <component is> 
- option API + render
- composition API + return fn

## template + <component is> 
传统的template模板 + <component is>  动态加载组件

ChildTemplateComponentIs.vue
 ```vue
<template>
  <div class="box">
    <component :is="renderContent({ message: '【这是child的内容】' })" />
  </div>
</template>

<script setup>
const props = defineProps({
  renderContent: {
    type: Object,
    required: true
  }
})
</script>
 ```


## option API + render
通过option API 和直接render方式导出，省去template定义

ChildOptionAPIRender.vue
```vue
<script lang="ts">
import {defineComponent } from 'vue'

export default defineComponent({
  name: 'HelloRender',
  props: {
    renderContent: {
      type: Object,
      required: true
    }
  },
  render() {
    return this.renderContent({ message: '【这是child的内容】' })
  }
})
</script>
```



## composition API + return fn
通过composition API 和 return fn，也等同于上面的option API+render的方式

ChildOptionAPIRender.vue
```vue
<script>
import { defineComponent} from 'vue'

export default defineComponent({ 
   props: {
    renderContent: {
      type: Object,
      required: true
    }
  },
  setup(props) { 
    return () =>
     props.renderContent({ message: '【这是child的内容】' })
  }
})
</script>
```


 # jsx
 当然上面的h函数使用起来还是不方便，我们可以利用jsx让代码变得更加直观 
 



vue种的slot其实都是语法糖，最终还是得转化成vnode渲染。所以直接通过h函数(createVNode),也是能实现

> 注意：由于vnode 在vue文件里，不能直接


 Parent.vue
 ```html
 <template>
  <h1>jsx </h1>
  <div class="box">
    <h2>parent</h2>
    <ChildTemplateComponentIs :renderContent="myJsxFn" />
    <ChildOptionAPIRender :renderContent="myJsxFn" />
    <ChildCompositionAPIReturnFn :renderContent="myJsxFn" />
  </div>

</template>

<script setup lang="tsx">
import ChildTemplateComponentIs from './ChildTemplateComponentIs.vue'
import ChildOptionAPIRender from './ChildOptionAPIRender.vue'
import ChildCompositionAPIReturnFn from './ChildCompositionAPIReturnFn.vue'

// 定义一个 JSX 元素作为内容
const myJsxFn = (scope:Object) =>  (
  <div style={{ color: 'blue', border: '1px dashed', padding: '10px' }}>
    <span>【这是Parent传入的内容】</span>
    <span>{scope.message}</span>
  </div>
)
</script>
 ```

由于vue3支持多种定义组件的方式，可以参照这篇文章：https://juejin.cn/post/7545948157110304804

这里组件通过 
- template + <component is> 
- option API + render
- composition API + return fn

## template + <component is> 
传统的template模板 + <component is>  动态加载组件

ChildTemplateComponentIs.vue
 ```html
 <template>
  <div class="box">
    <component :is="renderContent({ message: '【这是child的内容】(template + <component is> 方式) ' })" />
  </div>
</template>

<script setup>
const props = defineProps({
  renderContent: {
    type: Object,
    required: true
  }
})
</script>
 ```


## option API + render
通过option API 和直接render方式导出，省去template定义

ChildOptionAPIRender.vue
```html
<script lang="ts">
import {defineComponent } from 'vue'

export default defineComponent({
  name: 'HelloRender',
  props: {
    renderContent: {
      type: Object,
      required: true
    }
  },
  render() {
    return this.renderContent({ message: '【这是child的内容】（option API + render 方式）' })
  }
})
</script>
```



## composition API + return fn
通过composition API 和 return fn，也等同于上面的option API+render的方式

ChildOptionAPIRender.vue
```html
<script>
import { defineComponent} from 'vue'

export default defineComponent({ 
   props: {
    renderContent: {
      type: Object,
      required: true
    }
  },
  setup(props) { 
    return () =>
     props.renderContent({ message: '【这是child的内容】（composition API + return fn 方式）' })
  }
})
</script>
```
