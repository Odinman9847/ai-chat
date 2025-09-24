<script setup lang="ts">
import MessageBubble from "./MessageBubble.vue";
import type { Message } from "@/App.vue";
defineProps<{
  messages: Message[];
}>();
</script>

<template>
  <div class="chat-window">
    <MessageBubble
      v-for="message in messages"
      :key="message.id"
      :message="message"
    />
  </div>
</template>

<style scoped>
.chat-window {
  flex-grow: 1;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}
</style>
