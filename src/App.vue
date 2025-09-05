<script setup lang="ts">
import Parent1  from './slot/Parent.vue'
import Parent2  from './vhtml/Parent.vue'
import Parent3  from './h/Parent.vue' 
import Parent4  from './jsx/Parent.vue' 
import Dynamic  from './dynamic/test.vue' 


</script>

<template>
   <Parent1 />
   <br><br>
   <Parent2 />
   <br><br>
   <Parent3 /> 
   <br><br>
   <Parent4 /> 
   <br><br>
   <Parent5 /> 
 
   <Dynamic />

 
</template>

<style scoped></style>
