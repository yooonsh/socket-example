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
      <div v-for="(food, i) in foods" :key="i">
        <img :src="food.imageURL" alt="">
        <p>{{food.name}}</p>
        <p>{{i+1}}번: {{ count[i] }}</p>
        <button @click="voteCount(i+1)">{{i+1}}번 투표하기</button>
    </div>
    <button @click="voteClear">투표수 초기화</button>
    </div>

    <p>{{ minutes }}</p>
    <p>{{ seconds }}</p>
    <button @click="startTimer">start</button>
    <button v-if="timer" @click="stopTimer"> stop</button>
    <button v-if="resetButton" @click="resetTimer"> reset</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputMsg: '',
      msgs: [],
      msg: [],
      count: [0,0],

      title: 'Timer',
      timer: null,
      totalTime: (1 * 10),
      resetButton: false,
      foods:[
        {imageURL: "https://firebasestorage.googleapis.com/v0/b/nuxt-f0852.appspot.com/o/image%2F0144fba7fce1d31ad96cca27b27bea46f088585cb7df8d182fb55b723ff44d51.jpg?alt=media&token=ea258619-119a-4785-a3d4-321fc9a8ca40",
        name: "국수"},
        {imageURL: "https://firebasestorage.googleapis.com/v0/b/nuxt-f0852.appspot.com/o/image%2F0144fba7fce1d31ad96cca27b27bea46f088585cb7df8d182fb55b723ff44d51.jpg?alt=media&token=ea258619-119a-4785-a3d4-321fc9a8ca40",
        name: "냉면"}
      ],
    }
  },
  created() {
    
    this.socket = this.$nuxtSocket({
      name: 'main',
    })
    
    this.voteCount(0)

  },

  mounted() {

    this.socket.on('chat-message', (msg) => {
      this.msgs.push(msg)
    })

    this.socket.on('vote1', (count1) => {
      this.$set(this.count, 0, count1)
    })
    this.socket.on('vote2', (count2) => {
      this.$set(this.count, 1, count2)
    })
  },
  methods: {
    getMessage() {
      this.socket.emit('chat-message', this.inputMsg)
      this.inputMsg = ''
    },
    voteCount(cnt) {
      if(cnt === 0){
        this.socket.emit(`vote1`, 0)
        this.socket.emit(`vote2`, 0)
      }else{
        this.socket.emit(`vote${cnt}`, cnt)
      }
    },
    voteClear() {
      this.socket.emit('voteClear', 'CLEAR')
      this.count = 0
      this.count2 = 0
    },

    startTimer() {
			this.timer = setInterval(() => this.countdown(), 1000);
			this.resetButton = true;
		},
		stopTimer() {
			clearInterval(this.timer);
			this.timer = null
      this.resetButton = true;
		},
		resetTimer() {
			this.totalTime = (1 * 10);
			clearInterval(this.timer);
			this.timer = null;
			this.resetButton = false;
		},
		padTime(time) {
			return (time < 10 ? '0' : '') + time;
		},
		countdown() {
			if(this.totalTime >= 1) {
				this.totalTime--;
			} else {
				this.totalTime = 0;
        if(this.count > this.count2){
          console.log('1번이 더 많음')
        }else if(this.count < this.count2){
          console.log('2번이 더 많음')
        }else{
          console.log('동점')
        }
        
			}
		}
  },

  computed: {
		minutes() {
			const minutes = Math.floor(this.totalTime / 60);
			return this.padTime(minutes);
		},
		seconds() {
			const seconds = this.totalTime - (this.minutes * 60);
			return this.padTime(seconds);
		}
	}
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
