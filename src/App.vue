<script setup lang="ts">
import { ref, watch, withCtx } from "vue";
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
  //  messages.value.push({
  //    id: messageId++,
  //    role: "assistant",
  //    content: `This is a hardcoded response`,
  //  });
  //  return;
  // END OF TESTING

  isLoading.value = true;

  try {
    const messagesForApi = messages.value.map(({ id, ...rest }) => rest);
    const apiUrl = "http://localhost:11434/v1/chat/completions";

    const response = await fetch(apiUrl, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "qwen3:4b-instruct",
        messages: messagesForApi,
        stream: true,
      }),
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const reader = response.body?.getReader();
    if (!reader) {
      throw new Error("Could not get response reader");
    }
    const decoder = new TextDecoder();

    const assistantMessageId = messageId++;
    messages.value.push({
      id: assistantMessageId,
      role: "assistant",
      content: "",
    });

    const assistantMessage = messages.value.find(
      (m) => m.id === assistantMessageId,
    );
    if (!assistantMessage) {
      throw new Error("Could not find assistant message to update.");
    }

    while (true) {
      const { done, value } = await reader.read();

      if (done) break;

      if (isLoading.value) {
        isLoading.value = false;
      }

      const chunk = decoder.decode(value);
      const lines = chunk.split("\n").filter((line) => line.trim() !== "");

      for (const line of lines) {
        const jsonString = line.replace(/^data: /, "");
        if (jsonString === "[DONE]") continue;

        try {
          const parsed = JSON.parse(jsonString);
          const content = parsed.choices[0].delta.content;

          if (content) {
            assistantMessage.content += content;
          }
        } catch (error) {
          console.error("Error parsing streaming JSON:", error);
        }
      }
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
      <ChatWindow :messages="messages" :is-loading="isLoading" />
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
  max-width: 1000px;
  display: flex;
  flex-direction: column;
  background-color: #ffffff;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
  gap: 1rem;
}
</style>
