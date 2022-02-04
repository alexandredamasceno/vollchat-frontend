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
      <div v-for="(data, index) in messages" v-bind:key="index">
        <p>{{`${data.nickName}-${data.date}: ${data.message}`}}</p>
      </div>
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
      const getNickname = sessionStorage.getItem('user'); 
      const obj = { nick: getNickname, message: this.message }
      socket.emit('clientMessage', obj);
      this.message = '';
    },
    addUser() {
      socket.emit('myNickname', this.nickName);
      this.users.push(this.nickName);
      sessionStorage.setItem('user', this.nickName);
      this.nickName = '';
      this.goChat = true;
    },
    getallUsers() {
      socket.on('allUsers', (allUsers) => {
      this.users = allUsers;
    });
    },
    getallMessages() {
      socket.on('allMessages', (allMessages) => {
      this.messages = allMessages;
    });
    }
  },
  created() {
    this.getallUsers();
  },
  updated() {
    this.getallMessages();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

</style>
