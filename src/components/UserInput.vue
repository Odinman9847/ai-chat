<script setup lang="ts">
import { ref } from "vue";

const emit = defineEmits(["sendMessage"]);

const message = ref("");

function sendMessage() {
  if (message.value.trim() === "") return;
  emit("sendMessage", message.value);
  message.value = "";
}
</script>

<template>
  <form @submit.prevent="sendMessage">
    <input type="text" v-model="message" placeholder="Type your message..." />
    <button type="submit">Send</button>
  </form>
</template>

<style scoped>
form {
  display: flex;
  padding: 1rem;
  border-top: 1px solid #ccc;
}

input {
  flex-grow: 1;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  margin-left: 0.5rem;
  padding: 0.5rem 1rem;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}
</style>
