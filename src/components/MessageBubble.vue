<script setup lang="ts">
import type { Message } from "@/App.vue";
defineProps<{
  message: Message;
}>();
</script>

<template>
  <div :class="{ 'user-bubble': message.role === 'user' }">
    {{ message.content }}
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
  font-size: 1.25rem;
}

.user-bubble {
  background-color: #007bff;
  color: white;
  align-self: flex-end;
}
</style>
