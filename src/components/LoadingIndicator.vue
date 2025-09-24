<script setup lang="ts"></script>

<template>
  <div class="typing-indicator">
    <span class="dot"></span>
    <span class="dot"></span>
    <span class="dot"></span>
  </div>
</template>

<style scoped>
.typing-indicator {
  display: flex;
  align-items: flex-end;
  background-color: #f0f0f0;
  padding: 0.75rem 1rem;
  border-radius: 1rem;
  width: fit-content;
}

.dot {
  height: 8px;
  width: 8px;
  background-color: #999;
  border-radius: 50%;
  margin: 0 2px;
  animation: bounce 1.2s infinite ease-in-out;
}

.dot:nth-child(2) {
  animation-delay: 0.2s;
}

.dot:nth-child(3) {
  animation-delay: 0.5s;
}

@keyframes bounce {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-6px);
  }
}
</style>
