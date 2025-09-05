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