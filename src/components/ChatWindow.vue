<script setup lang="ts">
import MessageBubble from "./MessageBubble.vue";
defineProps<{
  messages: string[];
}>();
</script>

<template>
  <div class="chat-window">
    <MessageBubble
      v-for="(message, index) in messages"
      :key="index"
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
}
</style>
