<template>
	<div>

		<input 
		type="text" 
		placeholder="What needs to be done"
		class="todo-input" 
		v-model="newTodo"
		v-on:keyup.enter="addTodo" />



		<div v-for="(todo, index) in todosFiltering"
			 class="todo-item"
			 v-bind:key="todo.id">

			<input type="checkbox"
			v-model="todo.completed" />
			
			<div 
			class="todo-title"
			v-on:dblclick="editingTodo(todo)"
			v-if="!todo.editing"
			v-bind:class="{complete: todo.completed}">
				{{ todo.title }}
			</div>

			<input 
			type="text"
			v-model="todo.title"
			class="replce-input"
			v-if="todo.editing"
			v-on:keyup.enter='makeediting(todo)'
			v-on:blur="makeediting(todo)" 
			v-focus 
			v-on:keyup.escape="removeEdit(todo)" />

			<div class="remove-item" v-on:click="removeTodo(index)">
				&times;
			</div>

		</div>

		<div class="checkall">
			<div><label><input type="checkbox" v-bind:checked="checkcompleted" v-on:change="checkAllTodos()">Check All</label></div>
			<div>{{ remaining }} items left</div>
		</div>

		<div class="extra-container">

			<div>
				<button :class="{active: filter == 'all'}" @click="filter = 'all'">All</button>
				<button :class="{active: filter == 'active'}" @click="filter = 'active'">Active</button>
				<button :class="{active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
			</div>

			<div>
				<button v-if="showClearBtn" @click="removeCompleted()">Clear Completed</button>
			</div>

		</div>

	</div>
</template>

<script>

import todos from "../Json/Todo.json";
	export default{
		name: "todo-list",
		data() {
			return {
				newTodo: "",
				todos,
				idForTodo: 3,
				originalTitle: "",
				filter: "all"
			}
		},
		directives: {
		  focus: {
		    // directive definition
		    inserted: function (el) {
		      el.focus()
		    }
		  }
		},

		computed: {
			remaining() {
				return this.todos.filter((todo) => todo.completed == false).length;
			},
			checkcompleted() {
				//return this.todos.filter((todo) => todo.completed == false).length == 0;
				return this.remaining == 0;
			},
			todosFiltering() {
				if (this.filter == "all") {
					return todos;
				} else if(this.filter == "active") {
					return this.todos.filter((todo) => todo.completed == false);
				} else if (this.filter == "completed") {
					return this.todos.filter((todo) => todo.completed == true);
				}
			},
			showClearBtn() {
				return this.todos.filter((todo) => todo.completed == true).length > 0;
			}
		},

		methods: {
			// This Method Add New Object Inside Todo
			addTodo() {

				if(this.newTodo.trim() == "") {
					return;
				}

				this.todos.push({
					id: this.idForTodo,
					title: this.newTodo,
					completed: false,
					editing: false
				});

				this.idForTodo++;
				this.newTodo = "";
			},
			editingTodo(todo) {
				this.originalTitle = todo.title;
				todo.editing = true;
			},
			makeediting(todo){
				if(todo.title.trim() == "") {
					todo.title = this.originalTitle;
				}
				todo.editing = false;
			},
			removeTodo(index) {
				this.todos.splice(index, 1);
			},
			removeEdit(todo) {
				todo.title = this.originalTitle;
				todo.editing = false;
			},
			checkAllTodos() {
				this.todos.forEach((todo) => {
					todo.completed = event.target.checked;
				});
			},
			removeCompleted() {
				this.todos = this.todos.filter((todo) => todo.completed == false);
			}
		}
	};
</script>

<style>
	.todo-input{
		width: 100%;
		padding: 10px 20px;
		font-size: 17px;
		margin-bottom: 20px;
	}

	.todo-title.complete{
		text-decoration: line-through;
		color: gray
	}

	.todo-item{
		display: flex;
		justify-content: space-between;
		margin-bottom: 40px;
		position: relative;
	}

	.todo-item .replce-input{
		position: absolute;
		top: 0;
		left: 0;
		font-size: 17px;
		padding: 10px;
	}

	.remove-item:hover{
		cursor: pointer;
		color: #000;
		font-weight: bold
	}

	.checkall{
		display: flex;
		justify-content: space-between;
		padding: 20px 0;
		border-top: 1px solid gray;
		border-bottom: 1px solid gray;
		margin-bottom: 15px;
	}

	button.active{
		background-color: #080;
		color: #fff;
	}

	.extra-container{
		display: flex;
		justify-content: space-between;
	}
</style>