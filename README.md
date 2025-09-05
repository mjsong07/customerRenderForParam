# 前端

在实现一些高阶组件的时候我们往往需要传入自定义的渲染内容。一般实现使用插槽实现即可，但是在这些场景下：

*   组件是通过函数调用
*   子组件是通过参数动态创建

这里列出常用的4种解决方案：

*   slot
*   vhtml
*   h函数
*   jsx

# 1.slot

一般要实现自定义渲染内容分发，用官方的slot插槽实现即可。

缺点：对于动态渲染的组件需要加很多判断，对于通过函数创建的组件使用不友好。

Parent.vue

```html
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

```html
<template>
 <div class="box"> 
   <slot subMsg='【这是child的内容】'></slot>  <!-- 这里渲染父组件传入的内容 -->
 </div>
</template> 
```

# 2.vhtml

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

```html
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

# 3.h函数

vue种的slot其实都是语法糖，最终还是得转化成vnode渲染。所以直接通过h函数(createVNode),也是能实现

这里我们也可以通过h函数的实现了解vue中插槽实现逻辑。

Parent.vue

```html
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

由于vue3支持多种定义组件的方式，可以参照这篇文章：<https://juejin.cn/post/7545948157110304804>

这里组件通过

*   template + `<component is> `
*   option API + render
*   composition API + return fn

## 3.1 template + `<component is>`

传统开发，我们使用template模板 + `<component is>`  动态加载组件

> 注意：由于vnode在有template的情况下不能直接渲染vnode，所以依赖  `<component is>`渲染vnode

ChildTemplateComponentIs.vue

```html
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

## 3.2 option API + render

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
    return this.renderContent({ message: '【这是child的内容】' })
  }
})
</script>
```

## 3.3 composition API + return fn

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
     props.renderContent({ message: '【这是child的内容】' })
  }
})
</script>
```

# 4.jsx

当然上面的h函数使用起来还是不方便，我们可以利用jsx让代码变得更加直观，jsx的[参考](https://juejin.cn/post/7169454827385126949)

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

同样这里也列出三种实现的方式

*   template + `<component is>`
*   option API + render
*   composition API + return fn

## 4.1 template + `<component is>`

传统的template模板 + `<component is>`  动态加载组件

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

## 4.2 option API + render

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

## 4.3 composition API + return fn

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

# 5.实战

## 5.1 权限的动态组件

可以通过h函数先判断权限，然后再确定是否渲染，没有则直接返回null不渲染。

auth.tsx

```js
import { defineComponent, Fragment } from 'vue'
import { hasAuth } from '@/xxx/utils'

export default defineComponent({
  name: 'Auth',
  props: {
    value: {
      type: undefined,
      default: []
    }
  },
  setup(props, { slots }) {
    return () => {
      if (!slots) return null
      return hasAuth(props.value) ? <Fragment>{slots.default?.()}</Fragment> : null
    }
  }
})

```

## 5.2 动态组件创建

有时候我们需要根据字符串动态创建组件

ComponentA.vue

```js
<template>
  <div style="color: red;">
    <h3>我是 ComponentA</h3>
  </div>
</template>
```

ComponentB.vue

```js
<template>
  <div style="color: red;">
    <h3>我是 ComponentB</h3>
  </div>
</template>
```

test.vue

```html
<script lang="ts">
import { defineComponent, h, resolveComponent } from 'vue'
export default defineComponent({
  name: 'DynamicRenderer',
  props: {
    name: {
      type: String,
      required: true, // 必须传入组件名，如 'ComponentA'
    },
  },
  setup(props) {
    // 根据 props.name 解析出组件对象
    const targetComponent = resolveComponent(props.name)
    return () => h(targetComponent)
  },
})
</script>
```

## 5.3 动态表格支持自定义渲染内容

一般动态表格我们都是配置化方式实现（参考<https://juejin.cn/post/7398050410795270154> ），这时候扩展就很适合使用jsx传入。

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/26188a7e61ed4f2fbf01c0d65ec11ce9~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAgamFzb25feWFuZw==:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjk3MjcwNDc5NTgwMjY1MyJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1757661509&x-orig-sign=S0SateBWKROiK2fO3sP4Cy00I34%3D)

```js
    //定义列格式
let columns = 
[ 
  { label: '城市', property: 'city', render:'component',  type: 'select',  options: cityOptions , validate:true },  
  { label: '介绍', property: 'info', render:'component',width:120,  type: 'input'},   
]
//定义表格数据
let tableData = [
{"id":2,"city":1,"area":1,"username":3,"account":4,"age":5,"createtime":"2024-01-01",birthday:"202405",score:5,info:'人品好',readonlyKeyList:[]},
{"id":3,"city":2,"area":3,"username":3,"account":4,"age":5,"createtime":"2024-01-02",birthday:"202406",score:-2,info:'孤僻',ignoreList:['birthday']}, 
]

//组件使用
  <DTable ref="tableRef" rowKey="id"  :tableData="tableData" :columns="columns"></DTable>   
  
```

在上面代码如果我们希望 `{ label: '介绍', property: 'info', render:'component',width:120,  type: 'input'}`这里自带的input 改成自己想要定制化input,改成el-input-number，就可以利用 jsx，多传入一个参数实现
伪代码如下：

```js
  { label: '介绍', property: 'info', render:'component',width:120,
    type: 'input'，render: (model) => (<el-input-number v-model="model" :min="1" :max="10" />)} 
```

# 6.扩展

## render

`render`是 Vue 3 提供的一个 ​**底层 API**，用于 ​**手动渲染一个 Vue 应用实例（即 Vue 根组件）到指定的 DOM 容器中**。

```js
//弹出全屏遮罩的 点击验证码 页面
import { createVNode, render, VNode } from 'vue'
const clickCaptcha = () => {
  let vnode: VNode | null = createVNode(CaptchaComponent)
  render(vnode, document.body) // 直接渲染到 body上面
  vnode = null
}
```

## resolveComponent

在 Vue 3 中，`resolveComponent`是一个 ​**编译时辅助函数**​  通过组件的名称（字符串）解析出对应的组件定义（Component）对象。

由于h函数第一个参数 默认是不支持通过字符串直接匹配到具体的组件

```js
    h('el-date-picker') // 这里虽然已经全局引入'el-date-picker'，但是这样也还是会报错。
    h(resolveComponent('el-date-picker')) // 通过resolveComponent方法就能动态去查询组件定义，并返回实际的组件对象。
```

# 7.总结

在封装高阶组件的时候，当slot无法实现或实现起来比较麻烦的时候，我们就可以借用h函数或jsx作为参数，灵活创建或定制自己的渲染内容。

# 8.源码地址

<https://github.com/mjsong07/customerRenderForParam>
