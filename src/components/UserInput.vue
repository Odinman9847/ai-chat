<script setup lang="ts">
import { ref } from "vue";

const emit = defineEmits(["sendMessage"]);

defineProps<{
  isLoading: boolean;
}>();

const message = ref("");

function sendMessage() {
  if (message.value.trim() === "") return;
  emit("sendMessage", message.value);
  message.value = "";
}
</script>

<template>
  <form @submit.prevent="sendMessage">
    <input
      type="text"
      v-model="message"
      placeholder="Type your message..."
      :disabled="isLoading"
    />
    <button type="submit" :disabled="isLoading">Send</button>
  </form>
</template>

<style scoped>
form {
  display: flex;
  padding: 1rem;
  border-top: 1px solid #eee;
  align-items: center;
  gap: 0.5rem;
}

input {
  flex-grow: 1;
  border: 1px solid #ddd;
  border-radius: 999px;
  padding: 0.75rem 1.25rem;
  font-size: 1.25rem;
  font-family: inherit;
  outline: none;
  transition: border-color 0.2s ease;
  min-width: 0;
}

input:focus {
  border-color: #007bff;
}

button {
  padding: 1rem 2rem;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 999px;
  font-size: 1.25rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

button:hover {
  background-color: #0056b3;
}

button:disabled,
input:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}
</style>
