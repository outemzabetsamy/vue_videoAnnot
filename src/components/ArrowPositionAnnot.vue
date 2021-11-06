<template>
    <div class="arrow-down" :style="{left:arrowLeft +'%'}"></div>
</template>
<script>
export default {
    props:{
        "arrowLeft":Number,
        "skipeVideoArrow":Function,
    },
}
</script>
<style scoped>
.arrow-down {
    position: absolute;
  width: 0; 
  height: 0; 
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;
  
  border-top: 5px solid rgb(245, 200, 200);
}
.arrow-down:hover {
    position: absolute;
  width: 0; 
  height: 0; 
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  
  border-top: 8px solid rgb(219, 38, 38);
}
</style>