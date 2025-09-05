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