<script setup lang="ts">
import { ref } from "vue";
import UserInput from "./components/UserInput.vue";
import ChatWindow from "./components/ChatWindow.vue";

export type Message = {
  id: number;
  role: "user" | "assistant";
  content: string;
};

const messages = ref<Message[]>([]);
let messageId = 0;

function handleNewMessage(messageText: string) {
  messages.value.push({
    id: messageId++,
    role: "user",
    content: messageText,
  });
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
