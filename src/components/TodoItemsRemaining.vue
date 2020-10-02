<template>
  <div>{{ remaining }} items left</div>
</template>

<script>
export default {
    name: 'todo-item-remaining',
    props:{
        remaining:{
            type: Number,
            required: true,
        }
    },

}
</script>

<style>

</style>