<script setup lang="ts">
defineProps<{
  message: string;
}>();
</script>

<template>
  <div>
    {{ message }}
  </div>
</template>

<style scoped>
div {
  background-color: #f0f0f0;
  padding: 0.75rem 1rem;
  border-radius: 1rem;
  margin-bottom: 0.5rem;
  max-width: 80%;
  width: fit-content;
  word-wrap: break-word;
}
</style>
