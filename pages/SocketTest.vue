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
      <button @click="userReady" v-if="ready" class="readyBtn">ready</button>
      <button @click="userCancle" v-if="!ready" class="readyBtn">ready cancle</button>
      <div class="count">
        <p class="time">{{ minutes }} : {{ seconds }}</p>
        <button @click="startTimer">start</button>
        <button v-if="timer" @click="stopTimer"> stop</button>
        <button v-if="resetButton" @click="resetTimer"> reset</button>
      </div>
      <div class="menuChoice">
        <div v-for="(food, i) in 2" :key="i">
          <img :src="foods[i].imageUrl" alt="">
          <p>{{foods[i].name}}</p>
          <p>{{i+1}}번: {{ count[i] }}</p>
          <button @click="voteCount(i+1)">{{i+1}}번 투표하기</button>
        </div>
      </div>
    <!-- <button @click="voteClear">투표수 초기화</button> -->
    </div>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  components: {  },
  name: 'SocketTest',
  data() {
    return {
      inputMsg: '',
      msgs: [],
      msg: [],
      count: [0,0],
      ready: true,

      title: 'Timer',
      timer: null,
      totalTime: (1 * 10),
      resetButton: false,
      foods:[],
    }
  },
  created() {
    
    this.socket = this.$nuxtSocket({
      name: 'main',
      
      emitTimeout:1000
    })
    
    this.voteCount(0)

    axios.get('https://ojmm.herokuapp.com/food').then((res)=>{
      console.log("푸드 랜덤", res.data)
      this.foods = res.data
    })

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

    this.socket.on('ready', (user) => {
      console.log(user)
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
        this.socket.emit(`vote${cnt}`, 1)
      }
    },
    voteClear() {
      this.socket.emit('voteClear', 'CLEAR')
      this.count = [0,0]
    },

    userReady(){
      this.socket.emit('ready')
      this.ready = !this.ready
    },
    userCancle(){
      this.socket.emit('readyCancle')
      this.ready = !this.ready
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
  display: table;
  width: 1000px;
  margin: 0 auto 20px;
  text-align: center;
}
.readyBtn{
  width: 500px;
  height: 50px;
  font-size: 20px;
  border: 1px solid #000;
  background-color: #eee;

}
.voteArea .menuChoice {
  display: flex;
  margin: 50px 0;
  padding: 20px;
  border: 1px solid #000;
}
.voteArea .menuChoice div{
  width: 50%;
  margin: 0 10px;
}
.voteArea .menuChoice img{
  width: 100%;
}

.count{}
.count .time{
  font-size: 40px;
}
</style>
