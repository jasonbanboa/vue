<script setup>
import { reactive, onMounted } from 'vue';

import ListItem from './ListItem.vue';  

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

const toggleStatusFilter = ({ target: { dataset: { status } } }) => {
  const { filteredBy } = state;
  filteredBy.status = filteredBy.status.includes(status) 
      ? filteredBy.status.filter((st) => st != status)
      : [...filteredBy.status, status];
};

const toggleUserIdFilter = ({ target: { dataset: { userId } } }) => {
  const { filteredBy } = state;
  filteredBy.userId = filteredBy.userId.includes(+userId) 
      ? filteredBy.userId.filter((st) => st !== +userId)
      : [...filteredBy.userId, +userId];
};

</script>

<template>
  <div class="wrapper">
    <main>
      <div class="controls">
        <section>
          <h2>UserId</h2>
          <div class="buttons">
            <button 
              v-for="userId in state.userIds"
              :key="userId"
              :data-user-id="userId"
              :class="[state.filteredBy.userId.includes(+userId) ? 'active' : '']"
              @click="toggleUserIdFilter"
            >{{ userId }}</button>
          </div>
        </section>

        <section>
          <h2>Status</h2> 
          <div class="buttons">
            <button 
              v-for="status in statuses"
              :key="status"
              :data-status="status"
              :class="[state.filteredBy.status.includes(status) ? 'active' : '']"
              @click="toggleStatusFilter"
            >{{ status }}</button>
          </div>
        </section>        
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

button {
  padding: 5px 10px;
  border: 1px solid hsla(160, 100%, 37%, 1);
  color: hsla(160, 100%, 37%, 1);
  outline: none;
  background-color: transparent;
  border-radius: 3px;
  cursor: pointer;
  transition: all .25s;
}
button:hover {
  outline: 1px solid hsla(160, 100%, 37%, 1);
}
button.active {
  background-color: hsla(160, 100%, 37%, 1);
  color: white;
}
.buttons {
  display: flex;
  gap: 1rem;
}

</style>
