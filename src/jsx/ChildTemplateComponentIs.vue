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