<template>
  <main>
    <header class="header"><h1>VollChat</h1></header>
    <div class="main">
      <div class="users">
        <h3>Active users</h3>
        <ul class="list-group"> 
          <li class="list-group-item" v-for="(user, index) in users" v-bind:key="index">{{user}}</li>
        </ul>
        <form>
          <input type="text" placeholder="Digite seu nickname" v-model="nickName" class="form-control">
          <button class="btn btn-primary" @click.prevent="addUser()">Adicionar</button>
        </form>
      </div>
      <div class="chat">
        <div v-for="(data, index) in messages" v-bind:key="index">
          <div class="messages" >
            <span>{{data.nick}}</span>
            <span>{{data.message}}</span>
            <span>{{data.date}}</span>
          </div>
          </div>
          <p v-if="!goChat">Digite seu nickname para enviar uma mensagem</p>
          <div v-else class="form-chat">
            <form action="">
              <input
                type="text"
                placeholder="Digite aqui"
                v-model="message"
                class="form-control"
              >
              <button class="btn btn-primary" @click.prevent="enterMessage()">Enviar</button>
            </form>
          </div>
        </div>
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
  .header {
    display: flex;
    justify-content: center;
    margin-bottom: 30px;
    margin-top: 20px;
    background-color: #0d6efd;
    margin-top: 0px;
    
  }
  .main {
    display: flex;
    justify-content: space-evenly;
  }
  .messages {
    display: flex;
    flex-direction: column;
    margin-bottom: 15px;
  }
  .button {
    background-color: blue;
  }
  .chat {
    display: flex;
    flex-direction: column;
    flex-grow: 4;
  }
  .users {
    margin-right: 50px;
    margin-left: 100px;
  }
  .form-chat {
    position: fixed;
    bottom: 0;
  }
</style>
