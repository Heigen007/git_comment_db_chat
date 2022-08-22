<template>
  <div id="app">
    Отправить сообщение - <input v-model="text" type="text">
    <button @click="sendMessage">Отправить</button>
    <div style="color: red" v-if="isLoading">Идет сохранение сообщения в блокчейне, подождите</div>
    <h1>Сообщения</h1>
    <li v-for="(el,i) in messages">{{el}}</li>
  </div>
</template>

<script>
import { Octokit } from "octokit";
export default {
  name: 'App',
  data() {
    return {
      messages: [],
      text: "",
      isLoading: false,
      octokit: null
    }
  },
  mounted() {
    this.octokit = new Octokit({
      auth: process.env.VUE_APP_AuthKey
    })
    this.getMessages()
  },
  methods: {
    sendMessage() {
      if(!this.text) return alert("Пустое сообщение нельзя сохранить")

      this.octokit.request('PATCH /repos/Heigen007/algs_and_structures_documentation/comments/81821590', {
        body: this.messages.join("|||nextMsg|||") + "|||nextMsg|||" + this.text
      })
      .then(() => {
        this.getMessages()
        this.text = ""
      })

    },
    getMessages() {
      this.octokit.request('GET /repos/Heigen007/algs_and_structures_documentation/comments/81821590')
      .then(res => {
        this.messages = res.data.body.split("|||nextMsg|||");
      })
    },
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
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>