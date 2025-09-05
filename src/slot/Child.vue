<!-- 子组件 Child.vue -->
<template>
  <div class="box"> 
    <slot subMsg='【这是child的内容】'></slot>  <!-- 这里渲染父组件传入的内容 -->
  </div>
</template> 