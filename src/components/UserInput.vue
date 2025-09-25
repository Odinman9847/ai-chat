<script setup lang="ts">
import { ref, watch, nextTick } from "vue";

const emit = defineEmits(["sendMessage"]);

defineProps<{
  isLoading: boolean;
}>();

const message = ref("");
const textareaEl = ref<HTMLTextAreaElement | null>(null);

function sendMessage() {
  if (message.value.trim() === "") return;
  emit("sendMessage", message.value);
  message.value = "";
}

function handleKeydown(e: KeyboardEvent) {
  if (e.key === "Enter" && (e.ctrlKey || e.metaKey)) {
    sendMessage();
  }
}

watch(message, async () => {
  await nextTick();
  const el = textareaEl.value;
  if (el) {
    el.style.height = "auto";
    const maxHeight = 300;

    if (el.scrollHeight > maxHeight) {
      el.style.height = `${maxHeight}px`;
      el.style.overflowY = "scroll";
    } else {
      el.style.height = `${el.scrollHeight}px`;
      el.style.overflowY = "hidden";
    }
  }
});
</script>

<template>
  <form @submit.prevent="sendMessage">
    <textarea
      ref="textareaEl"
      type="text"
      v-model="message"
      placeholder="Type your message..."
      :disabled="isLoading"
      @keydown="handleKeydown"
      rows="1"
    />
    <button type="submit" :disabled="isLoading">Send</button>
  </form>
</template>

<style scoped>
form {
  display: flex;
  padding: 1rem;
  border-top: 1px solid #eee;
  align-items: flex-end;
  gap: 0.5rem;
}

textarea {
  flex-grow: 1;
  border: 1px solid #ddd;
  border-radius: 1.25rem;
  padding: 0.75rem 1.25rem;
  font-size: 1.25rem;
  font-family: inherit;
  outline: none;
  transition: border-color 0.2s ease;
  min-width: 0;
  resize: none;
  overflow-y: hidden;
  line-height: 1.5;
  max-height: 300px;
}

textarea:focus {
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
  align-self: flex-end;
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
