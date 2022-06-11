<template>
  <div class="addMenu">
    <div class="con">
      <input type="text" v-model="menuName">
      <div class="custom-file">
          <input id="customFile" type="file" @change="handleFileChange"/>
          <!-- <label class="custom-file-label" for="customFile">{{file_name}}</label> -->
      </div>
      <!-- <img v-if="img_src" :src="img_src" width="128" height="128"> -->
      <button @click="addMenu">보내기</button>
      <router-link to="SocketTest">시작하기</router-link>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      image: '',
      menuName: '',

      // file_name: "파일을 선택하세요.",
      file: "", 
      // img_src: "",
    }
  },
  created() {
    this.socket = this.$nuxtSocket({
      name: 'main',
      
      emitTimeout:1000
    })
  },
  mounted(){
    this.socket.on('start', () => {
      axios.get('https://ojmm.herokuapp.com/start').then(()=>{
        this.$router.push('/SocketTest')
      })
    })
  },
  methods: {
    handleFileChange(e) {
      this.file = e.target.files[0];
      console.log(this.file)
      const form = new FormData()
      form.append('file',this.file)
      axios.post('https://ojmm.herokuapp.com/api/image/upload', form).then((res)=>{
        console.log(res.data.imageURL)
        this.image = res.data.imageURL
      })
    },
    addMenu(){
      const menu = {
        name: this.menuName,
        imageUrl: this.image
      }
      axios.post('https://ojmm.herokuapp.com/food/add', menu).then((res)=>{
        console.log(res)
        if(res.data.result === "start"){
          this.socket.emit('start')
        }
      })
    }
  }
}
</script>

<style>
.addMenu{
  position: absolute;
  width: 100%;
  height: 100%;
}
.addMenu .con{
  display: table;
  position: relative;
  top: 50%;
  transform: translateY(-50%);
  width: 500px;
  margin: 0 auto;
  text-align: center;
}

.addMenu .con input[type="text"]{
  width: 100%;
  height: 50px;
  padding: 0 10px;
  font-size: 20px;
  text-align: center;
  box-sizing: border-box;
}

.addMenu .con input[type="file"]{
  width: 100%;
  padding: 10px 0;
  font-size: 20px;
}

.addMenu .con button{
  width: 100%;
  height: 50px;
  font-size: 20px;
  border: 1px solid #000;
  background-color: #eee;
}

.addMenu .con a{
  display: block;
  width: 100%;
  height: 50px;
  margin: 10px 0;
  color: #000;
  font-size: 20px;
  line-height: 50px;
  text-decoration: none;
  text-align: center;
  border: 1px solid #000;
  background-color: #eee;
}
</style>