<template>
  <div>
    <h2>动态组件渲染示例</h2>
    <DynamicRenderer :name="current" />
  </div>
</template>

<script lang="ts">
import { ref } from 'vue'
import DynamicRenderer from './DynamicRenderer.vue'
import ComponentA from './ComponentA.vue'
import ComponentB from './ComponentB.vue'

export default {
  components: {
    DynamicRenderer,
    ComponentA, // 必须全局或局部注册，resolveComponent 才能找到
    ComponentB,
  },
  setup() {
    const current = ref('ComponentA') // 默认显示 A
    return { current }
  }
}
</script>