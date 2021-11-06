<template>
    <div class="oneAnnot" :style="{top:ytop +'px',left:xleft +'px' }">

    </div>
</template>
<script>
export default {
    props:['xleft','ytop'],
    data(){
        return{
            
        }
    }
}
</script>
<style scoped>
.oneAnnot {
    position: absolute;
    background-color: crimson;
    height: 20px;
    width: 20px;
    border-radius: 50%;
}
</style>