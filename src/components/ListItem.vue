<script setup>

defineProps({
  todo: Object,
  toggleStatus: Function,
  removeTodo: Function,
  editTodo: Function,
});

</script>

<template>
  <li class="listItem" @click="toggleStatus">
    <span 
      :class="[todo.completed ? 'complete' : '']"
      @click="(e) => e.stopPropagation()" 
      @blur="editTodo" 
      spellcheck="false"
      contenteditable="true" 
    >{{ todo.title }}</span>
    <button @click="removeTodo">x</button>
  </li>
</template>

<style scoped>
.listItem {
  display: flex;
  padding: 10px;
  gap: 1rem;  
  cursor: pointer;
  transition: all .25s;
}
.listItem span {
  outline: none;
  border: none;
  cursor: text;
  padding: 3px;
}
.listItem span:focus {
  outline: 1px solid hsla(160, 100%, 37%, 1);
}

.listItem:hover {
  box-shadow: 1px 2px 10px rgba(0,0,0,0.1);
}
.listItem button {
  margin-left: auto;
  background-color: transparent;
  color: #ffaaaa;
  border: 1px solid #ffaaaa; outline: none;
  cursor: pointer;
}
.complete {
  transition: all .25s;
  text-decoration: line-through;
  color: hsla(160, 100%, 37%, 1);
}
</style>