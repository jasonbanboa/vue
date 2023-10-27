<script setup>
import { reactive, onMounted } from 'vue';

import ListItem from './ListItem.vue';  
import FilterBy from './FilterBy.vue';  


const statuses = ['completed', 'unfinished'];

const state = reactive({ 
  todos: [],
  filteredBy: {
    userId: [],
    status: [...statuses],
  },
  userIds: [],
  get filteredTodos() {
    return this.todos
      .filter(({ userId, completed }) => 
        this.filteredBy.userId.includes(userId)
        && (
          (completed && this.filteredBy.status.includes('completed'))
          || (!completed && this.filteredBy.status.includes('unfinished'))
        )
      );
  },
});

const fetchTodos = () => fetch('https://jsonplaceholder.typicode.com/todos').then((res) => res.json());

onMounted(async () => {
  const todos = await fetchTodos();
  state.todos = todos.map((todo) => ({ ...todo, editing: false, }));
  state.userIds = [...new Set(todos.map(({ userId }) => userId)).values()];
  state.filteredBy.userId = state.userIds;
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

const toggleStatusFilter = ({ target: { dataset: { item } } }) => {
  const { filteredBy } = state;

  filteredBy.status = filteredBy.status.includes(item) 
      ? filteredBy.status.filter((st) => st !== item)
      : [...filteredBy.status, item];
};

const toggleUserIdFilter = ({ target: { dataset: { item } } }) => {
  const { filteredBy } = state;
  filteredBy.userId = filteredBy.userId.includes(+item) 
      ? filteredBy.userId.filter((st) => st !== +item)
      : [...filteredBy.userId, +item];
};

const checkUserActive = (userId) => state.filteredBy.userId.includes(+userId);
const checkStatusActive = (status) =>  state.filteredBy.status.includes(status);

</script>

<template>
  <div class="wrapper">
    <main>
      <div class="controls">

        <FilterBy 
          by="UserId"
          :iterable="state.userIds"
          :toggleFunction="toggleUserIdFilter"
          :checkActive="checkUserActive"
        />

        <FilterBy 
          by="Status"
          :iterable="statuses"
          :toggleFunction="toggleStatusFilter"
          :checkActive="checkStatusActive"
        />

      </div>
  
      <ul>
        <ListItem 
          v-for="todo in state.filteredTodos"
          :key="todo.id"
          :todo="todo"
          :toggleStatus="() => { toggleStatus(todo); }"
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
    </main>
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
