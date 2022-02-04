<template>
  <main>
    <header><h1>VollChat</h1></header>
    <div>
      <h3>Active users</h3>
      <ul> 
        <li v-for="(user, index) in users" v-bind:key="index">{{user}}</li>
      </ul>
      <form>
        <input type="text" placeholder="Digite seu nickname" v-model="nickName">
        <button @click.prevent="addUser()">Adicionar</button>
      </form>
    </div>
    <p v-if="!goChat">Digite seu nickname para acessar o chat</p>
    <div v-else>
      <p v-for="(message, index) in messages" v-bind:key="index">{{message}}</p>
      <div>
        <form action="">
          <input
            type="text"
            placeholder="Digite aqui"
            v-model="message"
          >
          <button @click.prevent="enterMessage()">Enviar</button>
    </form>
      </div>
    </div>
    
  </main>
</template>

<script>
// import SocketioService from '../services/socketio.service';
import socketIOClient from 'socket.io-client';
const socket = socketIOClient('http://localhost:3000');
export default {
  data() {
    return {
      messages: [],
      users: [],
      message: '',
      nickName: '',
      goChat: false
    }
  },
  methods: {
    enterMessage() {
      const obj = { nick: this.nickName, message: this.message }
      socket.emit('clientMessage', ...obj);
    },
    addUser() {
      socket.emit('myNickname', this.nickName);
      this.users.push(this.nickName);
      this.nickName = '';
      this.goChat = true;
    }
  },
  created() {
    socket.on('allUsers', (allUsers) => {
      this.users = allUsers;
    });
  },
  updated() {
    socket.on('allUsers', (allUsers) => {
      this.users = allUsers;
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

</style>
