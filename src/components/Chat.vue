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
    <div v-for="(data, index) in messages" v-bind:key="index">
        <p>{{`${data.nick}-${data.date}: ${data.message}`}}</p>
      </div>
      <p v-if="!goChat">Digite seu nickname para enviar uma mensagem</p>
      <div v-else>
        <form action="">
          <input
            type="text"
            placeholder="Digite aqui"
            v-model="message"
          >
          <button @click.prevent="enterMessage()">Enviar</button>
        </form>
      </div>
  </main>
</template>

<script>
import socketIOClient from 'socket.io-client';
import createDate from '../helpers/index';
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
      // Envia uma mensagem
      const getNickname = sessionStorage.getItem('user');
      const obj = { nick: getNickname, message: this.message, date: createDate() }
      socket.emit('clientMessage', obj);
      this.message = '';
    },
    // Adiciona um nickname(user)
    addUser() {
      socket.emit('myNickname', this.nickName);
      sessionStorage.setItem('user', this.nickName);
      this.nickName = '';
      this.goChat = true;
    },
    getallUsers() {
      // Busca todos os usuários no BD
      socket.on('allUsers', (allUsers) => {
      this.users = allUsers;
    });
    },
    getThirtyMessages() {
      // Busca as 30 últimas mensagens no BD
      socket.on('allMessages', (allMessages) => {
      this.messages = allMessages;
    });
    },
  },
  mounted() {
    // Essas funções são chamadas assim que o componente é montado(renderizado)
    this.getallUsers();
    this.getThirtyMessages();
    // Esse trecho de código resolve o problemas das mensagens simultaneas
    socket.on('newMessage', (message) => {
      this.messages = [...this.messages, message];
    });
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

</style>
