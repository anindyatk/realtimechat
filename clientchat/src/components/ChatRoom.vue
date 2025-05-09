<template>
    <div class="chat-room">
      <div class="messages-container" style="display: flex; justify-content: flex-end;">
        <div v-for="msg in messages" :key="msg.id"
             :class="['message-bubble', msg.senderId === clientId ? 'sender' : 'receiver']">
          <p>{{ msg.text }}</p>
          <span class="timestamp">{{ new Date(msg.timestamp).toLocaleTimeString() }}</span>
        </div>
      </div>
      <form @submit.prevent="sendMessage" class="input-container">
        <input type="text" v-model="newMessage" placeholder="Type your message..." />
        <button type="submit">Send</button>
      </form>
      <div v-if="connectionError" class="error-message">
        Could not connect to the WebSocket server.
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref, onMounted, onUnmounted, nextTick } from 'vue';
  
  interface ChatMessage {
    id: string;
    senderId: string;
    text: string;
    timestamp: number;
  }
  
  export default defineComponent({
    name: 'ChatRoom',
    setup() {
      const messages = ref<ChatMessage[]>([]);
      const newMessage = ref<string>('');
      const clientId = ref<string>('');  
      const connectionError = ref<boolean>(false);
      let socket: WebSocket | null = null;
   
      const scrollToBottom = () => {
        nextTick(() => {
          const container = document.querySelector('.messages-container');
          if (container) {
            container.scrollTop = container.scrollHeight;
          }
        });
      };
  
      const connectWebSocket = () => {
        socket = new WebSocket('ws://localhost:3000'); // Ensure this matches your server
  
        socket.onopen = () => {
          console.log('WebSocket connection established. Client ID:', clientId.value);
          const systemMessage: ChatMessage = {
            id: Date.now().toString() + '_system',
            senderId: 'system',
            text: 'Connected to chat!',
            timestamp: Date.now(),
          }; 
          connectionError.value = false;
          scrollToBottom();
        };
  
        socket.onmessage = (event) => {
          try {
            if (typeof event.data === 'string') {
              const receivedMsg: ChatMessage = JSON.parse(event.data);
              messages.value.push(receivedMsg);  
              // messages.value.sort((a, b)=> a.timestamp - b.timestamp);  
              // messages.value.reverse();
              // messages.value.sort();  
              scrollToBottom();
            }
          } catch (error) {
            console.error('Error parsing received message:', error); 
             const fallbackMsg: ChatMessage = {
               id: Date.now().toString() + '_fallback',
               senderId: 'unknown',  
               text: event.data as string,
               timestamp: Date.now(),
             };
             messages.value.push(fallbackMsg);
             scrollToBottom();
          }
        };
  
        socket.onclose = () => {
          console.log('WebSocket connection closed');
           const systemMessage: ChatMessage = {
            id: Date.now().toString() + '_system_dc',
            senderId: 'system',
            text: 'Disconnected from chat.',
            timestamp: Date.now(),
          }; 
          scrollToBottom();
        };
  
        socket.onerror = (error) => {
          console.error('WebSocket error:', error);
           const systemMessage: ChatMessage = {
            id: Date.now().toString() + '_system_err',
            senderId: 'system',
            text: 'Error connecting to chat.',
            timestamp: Date.now(),
          }; 
          connectionError.value = true;
          scrollToBottom();
        };
      };
  
      const sendMessage = () => {
        if (newMessage.value.trim() && socket && socket.readyState === WebSocket.OPEN) {
          const messagePayload: ChatMessage = {
            id: Date.now().toString() + '_' + clientId.value, // Unique message ID
            senderId: clientId.value,
            text: newMessage.value,
            timestamp: Date.now(),
          };
          socket.send(JSON.stringify(messagePayload));
          newMessage.value = '';
        } else if (!socket || socket.readyState !== WebSocket.OPEN) {
           const systemMessage: ChatMessage = {
            id: Date.now().toString() + '_system_send_err',
            senderId: 'system',
            text: 'Cannot send message: Not connected.',
            timestamp: Date.now(),
          }; 
          scrollToBottom();
        }
      };
  
      onMounted(() => { 
        clientId.value = 'client_' + Math.random().toString(36).substring(2, 9);
        connectWebSocket();
      });
  
      onUnmounted(() => {
        if (socket) {
          socket.close();
        }
      });
  
      return {
        messages,
        newMessage,
        clientId,  
        sendMessage,
        connectionError,
      };
    },
  });
  </script>
  
  <style scoped>
  .chat-room {
    display: flex;
    flex-direction: column;
    height: 500px; 
    width: 600px;  
    border: 1px solid #ccc;
    margin: 20px auto;
    font-family: Arial, sans-serif;
    background-color: #f9f9f9;
  }
  
  .messages-container {
    flex-grow: 1;
    overflow-y: auto;
    padding: 15px;
    display: flex;
    flex-direction: column;  
  }
  
  .message-bubble {
    padding: 10px 15px;
    margin-bottom: 10px;
    border-radius: 20px;
    max-width: 75%;  
    word-wrap: break-word;
    position: relative; 
  }
  
  .message-bubble p {
    margin: 0 0 5px 0;  
    font-size: 0.95em;
  }
  
  .sender-label {
    font-size: 0.7em;
    color: #555;
    display: block;
    margin-bottom: 3px;
  }
  
  .timestamp {
    font-size: 0.7em;
    color: #777;
    display: block;  
    text-align: right;  
  }
  
  .sender {
    background-color: #dcf8c6;  
    align-self: flex-end;  
    margin-left: auto; 
    text-align: left;  
  }
  
  .sender .timestamp {
    text-align: right;
  }
  
  .receiver {
    background-color: #ffffff; 
    border: 1px solid #eee;
    align-self: flex-start;  
    margin-right: auto;  
    text-align: left;
  }
  
  .receiver .timestamp {
    text-align: right;
  }
  
  
  .input-container {
    display: flex;
    padding: 10px;
    border-top: 1px solid #eee;
    background-color: #fff;
  }
  
  .input-container input {
    flex-grow: 1;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 20px;
    margin-right: 10px;
    font-size: 0.9em;
  }
  
  .input-container button {
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 20px;
    cursor: pointer;
    font-size: 0.9em;
  }
  
  .input-container button:hover {
    background-color: #0056b3;
  }
  
  .error-message {
    color: red;
    padding: 10px;
    text-align: center;
    background-color: #ffebee;
  }
  </style>