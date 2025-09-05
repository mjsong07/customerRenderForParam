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