<script setup lang="ts">
import { ref, watch, nextTick, onMounted, onUnmounted } from "vue";
import MessageBubble from "./MessageBubble.vue";
import LoadingIndicator from "./LoadingIndicator.vue";
import type { Message } from "@/App.vue";

const props = defineProps<{
  messages: Message[];
  isLoading: boolean;
}>();

const chatWindowEl = ref<HTMLDivElement | null>(null);
const isAutoScrollEnabled = ref(true);

function handleScroll() {
  const el = chatWindowEl.value;
  if (!el) return;

  const isAtBottom = el.scrollHeight - el.scrollTop <= el.clientHeight + 10;

  if (isAtBottom) {
    isAutoScrollEnabled.value = true;
  } else {
    isAutoScrollEnabled.value = false;
  }
}

onMounted(() => {
  chatWindowEl.value?.addEventListener("scroll", handleScroll);
});

onUnmounted(() => {
  chatWindowEl.value?.removeEventListener("scroll", handleScroll);
});

watch(
  () => props.messages,
  async () => {
    await nextTick();
    if (isAutoScrollEnabled.value) {
      const el = chatWindowEl.value;
      if (el) {
        el.scrollTop = el.scrollHeight;
      }
    }
  },
  { deep: true },
);
</script>

<template>
  <div class="chat-window" ref="chatWindowEl">
    <div v-if="messages.length === 0" class="welcome-message">
      <h1>Odin Chat</h1>
      <h2>What's on your mind?</h2>
    </div>
    <div v-else class="message-list">
      <MessageBubble
        v-for="message in messages"
        :key="message.id"
        :message="message"
      />
      <LoadingIndicator v-if="isLoading" />
    </div>
  </div>
</template>

<style scoped>
.chat-window {
  flex-grow: 1;
  padding: 3rem 2rem 1rem;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}

.welcome-message {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  color: #aaa;
}

.welcome-message h1 {
  font-size: 2.5rem;
  font-weight: 700;
}

.welcome-message h2 {
  font-size: 1.5rem;
  font-weight: 600;
}

.message-list {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

@media (min-width: 600px) {
  .chat-window {
    padding-left: 3rem;
    padding-right: 3rem;
  }
}
</style>
