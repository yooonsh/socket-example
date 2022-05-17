<template>
  <div>
    <h1>socket 테스트</h1>
    <ul>
      <li v-for="msg in msgs" :key="msg">{{ msg }}</li>
    </ul>
    <form action="">
      <input
        v-model="inputMsg"
        v-on:keydown.enter.prevent="getMessage"
        type="text"
      /><button type="button" @click="getMessage">Send</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputMsg: '',
      msgs: [],
      msg: [],
    }
  },
  created() {},

  mounted() {
    this.socket = this.$nuxtSocket({
      name: 'main',
    })
    this.socket.on('chat-message', (msg) => {
      this.msgs.push(msg)
    })
  },
  methods: {
    getMessage() {
      this.socket.emit('chat-message', this.inputMsg)
      this.inputMsg = ''
    },
  },
}
</script>

<style></style>
