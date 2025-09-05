<template>
  <h1>h + < component is ></h1> 
  <div class="box">
    <h2>parent</h2>
    <ChildTemplateComponentIs :renderContent="myRenderFunction" />
    <ChildOptionAPIRender :renderContent="myRenderFunction" />
    <ChildCompositionAPIReturnFn :renderContent="myRenderFunction" />
  </div>

</template>

<script setup>
import ChildTemplateComponentIs from './ChildTemplateComponentIs.vue'
import ChildOptionAPIRender from './ChildOptionAPIRender.vue'
import ChildCompositionAPIReturnFn from './ChildCompositionAPIReturnFn.vue'
import { h } from 'vue'
// 定义一个渲染函数，可以传入参数，返回要渲染的内容
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