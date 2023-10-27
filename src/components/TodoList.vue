<script setup>
import { reactive, onMounted } from 'vue';

import ListItem from './ListItem.vue';  

const state = reactive({ todos: [] });

const fetchTodos = () => fetch('https://jsonplaceholder.typicode.com/todos').then((res) => res.json());

onMounted(async () => {
  const todos = await fetchTodos();
  // state.todos = todos.filter(({ userId }) => userId === 1);
  state.todos = todos;
});

const toggleStatus = (todo) => {
  todo.completed = !todo.completed;
};

</script>

<template>
  <ul>
    <ListItem 
      v-for="todo in state.todos"
      :key="todo.id"
      :todo="todo"
      :toggleStatus="() => toggleStatus(todo)"
    />
  </ul>
</template>

