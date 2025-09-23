<script setup lang="ts">
import { ref, watch } from "vue";
import UserInput from "./components/UserInput.vue";
import ChatWindow from "./components/ChatWindow.vue";

export type Message = {
  id: number;
  role: "user" | "assistant";
  content: string;
};

const messages = ref<Message[]>([]);
let messageId = 0;

const isLoading = ref(false);

function formatConversationForApi(history: Message[]): string {
  const systemPrompt = "You are a helpful, friendly, and concise AI assistant.";
  const formattedHistory = history
    .map((msg) => {
      const role = msg.role.charAt(0).toUpperCase() + msg.role.slice(1);
      return `${role}: ${msg.content}`;
    })
    .join("\n");

  return `${systemPrompt}\n\nConversation History:\n${formattedHistory}\nAssistant:`;
}

function handleNewMessage(messageText: string) {
  messages.value.push({
    id: messageId++,
    role: "user",
    content: messageText,
  });

  // TEMPORARY TEST
  const contextPrompt = formatConversationForApi(messages.value);
  console.log("Context Prompt For API:");
  console.log(contextPrompt);

  isLoading.value = true;

  setTimeout(() => {
    messages.value.push({
      id: messageId++,
      role: "assistant",
      content: "I am a humble AI assistant. This is a hardcoded response.",
    });

    isLoading.value = false;
  }, 1000);
}
</script>

<template>
  <main>
    <ChatWindow :messages="messages" />
    <UserInput @sendMessage="handleNewMessage" :is-loading="isLoading" />
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
</style>
