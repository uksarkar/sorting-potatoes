<script setup lang="ts">
import Table from "./components/Table.vue";
</script>

<template>
  <Table />
</template>

<style>
@import url(../node_modules/izitoast/dist/css/iziToast.min.css);
html {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

body {
  background-color: #f2f2f2;
}

button {
  background: none;
  border: none;
  border-radius: 5px;
  padding: 0.9rem 2rem;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
}

button.cancel {
  background-color: #eee;
}

button.cancel:hover {
  background-color: #777;
  color: #fff;
}

button.primary {
  background-color: #ff8d00;
  color: #fff;
}

button.primary:hover {
  background-color: #c66d00;
}

button.primary:disabled,
button.primary:hover:disabled {
  background-color: #ffe4c3;
  color: #c4c3c3;
  cursor: not-allowed;
}

.sortable-ghost {
  background-color: #eee;
  color: gray;
}
</style>
