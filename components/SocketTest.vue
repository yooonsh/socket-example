<template>
  <div>
    <h1>socket 테스트</h1>
    <ul>
      <li v-for="(msg, index) in msgs" :key="index">{{ msg }}</li>
    </ul>
    <form @submit.prevent>
      <input
        v-model="inputMsg"
        type="text"
        @keydown.enter="getMessage"
      /><button type="button" @click="getMessage">Send</button>
    </form>

    <div class="voteArea">
      <div>
        <p>1번: {{ count }}</p>
        <button @click="voteCount">1번 투표</button>
      </div>
      <div>
        <p>2번: {{ count2 }}</p>
        <button @click="voteCount2">2번 투표</button>
      </div>
    </div>
    <button @click="voteClear">투표수 초기화</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputMsg: '',
      msgs: [],
      msg: [],
      count: 0,
      count2: 0,
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

    this.socket.on('vote', (count) => {
      this.count = count
    })
    this.socket.on('vote2', (count2) => {
      this.count2 = count2
    })
  },
  methods: {
    getMessage() {
      this.socket.emit('chat-message', this.inputMsg)
      this.inputMsg = ''
    },
    voteCount() {
      this.socket.emit('vote', 1)
    },
    voteCount2() {
      this.socket.emit('vote2', 1)
    },
    voteClear() {
      this.socket.emit('voteClear', 'CLEAR')
      this.count = 0
      this.count2 = 0
    },
  },
}
</script>

<style>
.voteArea {
  display: flex;
  margin: 0 0 20px 0;
  text-align: center;
}
.voteArea div {
  margin: 0 10px;
}
</style>
