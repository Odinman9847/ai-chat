<script setup lang="ts">
import { computed } from "vue";
import type { Message } from "@/App.vue";
import { marked } from "marked";
import DOMPurify from "dompurify";
const props = defineProps<{
  message: Message;
}>();

const renderedHtml = computed(() => {
  const rawHtml = marked.parse(props.message.content) as string;
  const sanitizedHtml = DOMPurify.sanitize(rawHtml);
  return sanitizedHtml;
});
</script>

<template>
  <div
    :class="{ 'user-bubble': message.role === 'user' }"
    v-html="renderedHtml"
  ></div>
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
  line-height: 1.5;
}

div :deep(p) {
  margin: 0;
}
div :deep(p:not(:last-child)) {
  margin-bottom: 1rem;
}
div :deep(ul),
div :deep(ol) {
  padding-left: 1.5rem;
}
div :deep(code) {
  background-color: #e0e0e0;
  padding: 0.2em 0.4em;
  border-radius: 3px;
  font-size: 85%;
}
.user-bubble :deep(code) {
  background-color: #0056b3;
}
div :deep(pre) {
  background-color: #e0e0e0;
  padding: 1rem;
  border-radius: 0.5rem;
  overflow-x: auto;
}
.user-bubble :deep(pre) {
  background-color: #0056b3;
}

.user-bubble {
  background-color: #007bff;
  color: white;
  align-self: flex-end;
}
</style>
