<script setup>
  defineProps({
    by: String,
    iterable: Array,
    toggleFunction: Function,
    checkActive: Function,
  });
</script>

<template>
  <section>
    <h2>{{ by }}</h2>
    <div class="buttons">
      <button 
        v-for="item in iterable"
        :key="item"
        :data-item="item"
        :class="[checkActive(item) ? 'active' : '']"
        @click="toggleFunction"
      >{{ item }}</button>
    </div>
  </section>
</template>

<style scoped>
  
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
