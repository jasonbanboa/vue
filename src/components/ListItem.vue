<script setup>

defineProps({
  todo: Object,
  toggleStatus: Function,
});

</script>

<template>
  <li class="listItem" @click="toggleStatus">
    <span :class="[todo.completed ? 'line-thru' : '']">{{ todo.title }}</span>
    
  </li>
</template>

<style scoped>
.listItem {
  cursor: pointer;
}
.line-thru {
  text-decoration: line-through;
}
</style>