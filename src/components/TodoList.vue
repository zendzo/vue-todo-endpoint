<template>
	<div>
		<input type="text" placeholder="What need to be done" class="todo-input" v-model="newTodo" @keyup.enter="addTodo">
		<!-- annimatie tobe here -->
		<transition-group tag="div" name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
		<todo-item v-for="(todo, index) in todosFiltered" 
		:key="todo.id" 
		:todo="todo" 
		:index="index" 
		:checkAll="!anyRemaining"
		></todo-item>
		</transition-group>
		<div class="extra-container">
			<todo-check-all :anyRemaining="anyRemaining"></todo-check-all>
			<todo-item-remaining :remaining="remaining"></todo-item-remaining>
		</div>

		<div class="extra-container">
			<todo-filter></todo-filter>
			<todo-clear-completed :showClearButton="showClearButton"></todo-clear-completed>
		</div>
	</div>
</template>

<script>
import TodoItem from "./TodoItem"
import TodoItemRemaining from './TodoItemsRemaining'
import TodoCheckAll from './TodoCheckAll'
import TodoFilter from './TodoFilter'
import TodoClearCompleted from './TodoClearCompleted'

export default {
  name: 'todo-list',
	components: {
		TodoItem,
		TodoItemRemaining,
		TodoCheckAll,
		TodoFilter,
		TodoClearCompleted,
	},
  data () {
    return {
	  newTodo: '',
	  beforeEditCache:'',
	  idForTodo: 3,
	  filter: 'all',
	  todos: [
		  {
			  'id': 1,
			  'title': 'Finish Vue Screencast',
			  'completed': false ,
			  'editing': false
		  },
		  {
			  'id': 2,
			  'title': 'Take Over the world',
			  'completed': false ,
			  'editing': false
		  }
	  ]
    }
  },
  created(){
	  eventBus.$on('removedTodo', (index) => this.removeTodo(index))
	  eventBus.$on('finishedEdit', (todo) => this.finishedEdit(todo))
	  eventBus.$on('allChecked', this.checkAllTodos)
	  eventBus.$on('filterChanged', (filter) => this.filter = filter)
	  eventBus.$on('clearCompletedTodos', this.clearCompleted)
  },
  computed:{
	  remaining(){
		  return this.todos.filter(todo => !todo.completed).length
	  },
	  anyRemaining(){
		  return this.remaining != 0
	  },
	  todosFiltered(){
		  if (this.filter == 'all') {
			  return this.todos
		  } else if(this.filter == 'active') {
			  return this.todos.filter(todo => !todo.completed)
		  }else if(this.filter == 'completed'){
			  return this.todos.filter(todo => todo.completed)
		  }

		  return this.todos
	  },
	  showClearButton(){
		  return this.todos.filter((todo) => todo.completed).length > 0
	  }
  },
  methods: {
	  addTodo(){
		  if(this.newTodo.trim().length == 0){
			  return
		  }
		  this.todos.push({
			  id: this.idForTodo,
			  title: this.newTodo,
			  completed: false
		  })
		  this.newTodo = ''
		  this.idForTodo++
	  },
	  removeTodo(index){
		  this.todos.splice(index,1)
	  },
	  checkAllTodos(){
		  this.todos.forEach((todo) => todo.completed = event.target.checked)
	  },
	  clearCompleted(){
		  this.todos = this.todos.filter(todo => !todo.completed)
		},
		finishedEdit(data){
			this.todos.splice(data.index,1,data.todo)
		}
  }
}
</script>

<style>
@import url('https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css');

.todo-input {
	width: 100%;
	padding: 10px 18px;
	font-size: 18px;
	margin-bottom: 16px;
}

.todo-input:focus{
		outline: 0;
}

.todo-item{
	margin-bottom: 12px;
	display: flex;
	align-items: center;
	justify-content: space-between;
	animation-duration: .3s;
}
.remove-item{
	cursor: pointer;
	margin-left: 14px;
}
.remove-item:hover{
	color: black;
}

.todo-item-left{
	display: flex;
	align-items: center;
}

.todo-item-edit{
	font-size: 24px;
	color: #2c3e50;
	margin-left: 12px;
	width: 100%;
	padding: 10px;
	border: 1px solid #ccc;
	font-family: 'Avenir', Arial, Helvetica, sans-serif;
}

.todo-item-edit:focus{
	outline: none;
}
.completed{
	text-decoration: line-through;
	color: grey;
}

.extra-container{
	display: flex;
	align-items: center;
	justify-content: space-between;
	font-size: 16px;
	border-top: 1px solid lightgrey;
	padding-top: 14px;
	margin-bottom: 14px;
}

.extra-container>button{
	font-size: 14px;
	background-color: #fff;
	appearance: none;
}

.extra-container>button:hover{
	background: lightgreen;
}

.extra-container>button:focus{
	outline: none;
}

.active{
	background: lightgreen;
}

/* css transition */

.fade-enter-active, .fade-leave-active{
	transition: opacity .2s;
}

.fade-enter, .fade-leave-to{
	opacity: 0;
}
</style>
