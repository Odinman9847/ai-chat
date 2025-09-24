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
  const systemPrompt =
    "You are a helpful, friendly, intelligent AI assistant. Your name is Odin.";
  const formattedHistory = history
    .map((msg) => {
      const role = msg.role.charAt(0).toUpperCase() + msg.role.slice(1);
      return `${role}: ${msg.content}`;
    })
    .join("\n");

  return `${systemPrompt}\n\nConversation History:\n${formattedHistory}\nAssistant:`;
}

async function handleNewMessage(messageText: string) {
  messages.value.push({
    id: messageId++,
    role: "user",
    content: messageText,
  });

  // THIS IS FOR TESTING, REMOVE TO USE LLM
  messages.value.push({
    id: messageId++,
    role: "assistant",
    content: `This is a hardcoded response`,
  });
  return;
  // END OF TESTING

  isLoading.value = true;

  try {
    const contextPrompt = formatConversationForApi(messages.value);
    const apiUrl = "https://apifreellm.com/api/chat";

    const response = await fetch(apiUrl, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        message: contextPrompt,
      }),
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();

    if (data.status === "success") {
      messages.value.push({
        id: messageId++,
        role: "assistant",
        content: data.response,
      });
    } else {
      throw new Error(data.error || "An unknown API error occured.");
    }
  } catch (error) {
    console.error("Error fetching AI response:", error);

    messages.value.push({
      id: messageId++,
      role: "assistant",
      content: `Sorry, I ran into an error. Please try again. (${error})`,
    });
  } finally {
    isLoading.value = false;
  }
}
</script>

<template>
  <main>
    <div class="chat-container">
      <ChatWindow :messages="messages" />
      <UserInput @sendMessage="handleNewMessage" :is-loading="isLoading" />
    </div>
  </main>
</template>

<style scoped>
main {
  background-color: #f7f7f7;
  height: 100vh;
  display: flex;
  justify-content: center;
}

.chat-container {
  width: 100%;
  max-width: 800px;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background-color: #ffffff;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
  gap: 1rem;
}
</style>
