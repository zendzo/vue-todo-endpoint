<template>
	<div>
		<input type="text" placeholder="What need to be done" class="todo-input" v-model="newTodo" @keyup.enter="addTodo">
		<!-- annimatie tobe here -->
		<div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
			<div class="todo-item-left">
				<input type="checkbox" v-model="todo.completed">
				<div class="todo-item-label" :class="{completed : todo.completed }" @dblclick="editTodo(todo)" v-if="!todo.editing">{{ todo.title }}</div>
				<input class="todo-item-edit" v-else type="text" v-model="todo.title"
				@blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
			</div>
			<div class="remove-item" @click="removeItem(index)">
				&times;
			</div>
		</div>
		
		<div class="extra-container">
			<div>
				<label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">Check All</label>
			</div>
			<div>{{ remaining }} items left</div>
		</div>

		<div class="extra-container">
			<div>
				<button :class="{active: filter == 'all' }" @click="filter = 'all'">All</button>
				<button :class="{active: filter == 'active' }" @click="filter = 'active'">Active</button>
				<button :class="{active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
			</div>
			<div>
				<transition name="fade">
					<button v-if="showClearButton" @click="clearCompleted">Clear Completed</button>
				</transition>
			</div>
		</div>
	</div>
</template>

<script>
export default {
  name: 'TodoList',
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
  directives: {
	  focus:{
		  inserted: function(el){
			  el.focus()
		  }
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
	  removeItem(index){
		  this.todos.splice(index,1)
	  },
	  editTodo(todo){
		  this.beforeEditCache = todo.title
		  todo.editing = true
	  },
	  doneEdit(todo){
		  if(todo.title.trim() == ''){
			  todo.title = this.beforeEditCache
		  }
		  todo.editing = false
	  },
	  cancelEdit(todo){
		  todo.title = this.beforeEditCache
		  todo.editing = false
	  },
	  checkAllTodos(){
		  this.todos.forEach((todo) => todo.completed = event.target.checked)
	  },
	  clearCompleted(){
		  this.todos = this.todos.filter(todo => !todo.completed)
	  }
  }
}
</script>

<style>

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
