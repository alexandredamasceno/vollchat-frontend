<template>
  <div>
    <Chat msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Chat from './components/Chat.vue'
import SocketioService from './services/socketio.service.js'

export default {
  name: 'App',
  components: {
    Chat
  },
  created() {
    SocketioService.setupSocketConnection();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
