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