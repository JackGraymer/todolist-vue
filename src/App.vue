<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const priority = ref('Pending');

const todos_asc = computed(() =>
	todos.value.sort((a, b) => {
		return b.createdAt - a.createdAt;
	})
);

const removeTodo = (todo) => {
	const index = todos.value.indexOf(todo);
	todos.value.splice(index, 1);
};

watch(
	todos,
	(newval) => {
		localStorage.setItem('todos', JSON.stringify(newval));
	},
	{ deep: true }
);

watch(name, (newval) => {
	localStorage.setItem('name', newval);
});

onMounted(() => {
	if (localStorage.name !== 'undefined' && localStorage.name !== '') {
		name.value = localStorage.getItem('name');
	} else {
		name.value = '';
	}
	if (localStorage.todos == null) {
		return;
	}
	todos.value = JSON.parse(localStorage.getItem('todos'));
});

const addTodo = () => {
	if (input_content.value === '') {
		return;
	}

	const todo = {
		id: self.crypto.randomUUID(),
		content: input_content.value,
		priority: priority.value,
		done: false,
		createdAt: new Date().getTime(),
	};

	todos.value.push(todo);
	input_content.value = '';
	priority.value = 'Pending';
};
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				What's up
				<input
					type="text"
					class="title"
					placeholder="Your name"
					v-model="name" />
				?
			</h2>
		</section>
		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form v-on:submit.prevent="addTodo">
				<h4>Your pending Tasks:</h4>
				<input
					type="text"
					placeholder="Enter a todo"
					v-model="input_content"
					required
					v-on:keypress.Enter="addTodo" />

				<div class="options">
					<label>
						<input
							type="radio"
							name="priority"
							value="Pending"
							v-model="priority" />
						<div>Pending</div>
					</label>
					<label>
						<input
							type="radio"
							name="priority"
							value="Urgent"
							v-model="priority" />
						<div>Urgent</div>
					</label>
				</div>
				<button type="submit" value="Add Todo">
					Add Todo <br />
					or press <code>Enter</code>
				</button>
			</form>
		</section>

		<section class="todo-list">
			<h3>To Do List</h3>
			<div class="list">
				<div
					v-for="todo in todos_asc"
					:class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input
							type="checkbox"
							v-model="todo.done"
							:class="`checkbox ${todo.priority}`" />
						<div class="todo-content">
							<input type="text" v-model="todo.content" /> {{ todo.content }}
						</div>
						<div class="action">
							<button class="delete" v-on:click="removeTodo(todo)">
								Delete
							</button>
						</div>
					</label>
				</div>
			</div>
			{{ todos_asc }}
		</section>
	</main>
</template>

<style scoped></style>
