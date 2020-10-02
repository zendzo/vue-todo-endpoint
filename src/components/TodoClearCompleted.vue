<template>
    <div>
        <transition name="fade">
            <button v-if="showClearButton" @click="clearCompleted">Clear Completed</button>
        </transition>
    </div>
</template>

<script>
export default {
    name: 'todo-clear-completed',
    props:{
        showClearButton:{
            type: Boolean,
            required: true,
        }
    },
    methods:{
        clearCompleted(){
            eventBus.$emit('clearCompletedTodos')
        }
    }
}
</script>