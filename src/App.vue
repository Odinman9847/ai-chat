<script setup lang="ts">
import { ref } from "vue";
import UserInput from "./components/UserInput.vue";
import ChatWindow from "./components/ChatWindow.vue";

const messages = ref<string[]>([]);

function handleNewMessage(message: string) {
  console.log("App.vue received message:", message);
  messages.value.push(message);
}
</script>

<template>
  <main>
    <ChatWindow :messages="messages" />
    <UserInput @sendMessage="handleNewMessage" />
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
</style>
