<template>
  <div id="app">
    <h1>REAL TIME CHAT</h1>
    <ChatRoom />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import ChatRoom from './components/ChatRoom.vue';

export default defineComponent({
  name: 'App',
  components: {
    ChatRoom,
  },
});
</script>

<style>
#app {
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>