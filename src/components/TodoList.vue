<script setup>
import { reactive, onMounted } from 'vue';

import ListItem from './ListItem.vue';  


const state = reactive({ todos: [] });

const fetchTodos = () => fetch('https://jsonplaceholder.typicode.com/todos').then((res) => res.json());

onMounted(async () => {
  const todos = await fetchTodos();
  // state.todos = todos.filter(({ userId }) => userId === 1);
  state.todos = todos.map((todo) => ({ ...todo, editing: false, }));
});

const toggleStatus = (todo) => {
  todo.completed = !todo.completed;
};

const removeTodo = ({ id: todoId }) => {
  state.todos = state.todos.filter(({ id }) => id != todoId);
};

const editTodo = (todo, updatedTitle) => {
  todo.title = updatedTitle;
};

</script>

<template>
  <div class="wrapper">
    <ul>
      <ListItem 
        v-for="todo in state.todos"
        :key="todo.id"
        :todo="todo"
        :toggleStatus="() => toggleStatus(todo)"
        :removeTodo="(e) => { 
          e.stopPropagation();
          removeTodo(todo);
        }"
        :editTodo="(e) => {
          e.stopPropagation();
          const updatedTitle = e.currentTarget.textContent.trim();
          editTodo(todo, updatedTitle);          
        }"
      />
    </ul>
  </div>
</template>

<style scoped>
.wrapper {
  display: flex; justify-content: center;
}
ul {
  display: flex; flex-direction: column;
  gap: 10px;
}

</style>
