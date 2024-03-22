<template>
  <h1 v-if="timer > 0">{{ formattedTimer(timer) }}</h1>

  <div class="bound-to-bottom">
    <input type="number" v-model="newtimer">
    <button @click="setTimer()">Set Timer</button>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  name: 'App',
  mounted() {
    const socket = io('http://localhost:56565');
    socket.on('connect', () => {
      console.log('Connected to server');
    });

    socket.on("timer", timer => {
      this.timer = timer;
    })
    
    this.socket = socket;
  },
  data() {
    return {
      timer: 0,
      newtimer: 20,
      socket: null
    }
  },
  methods: {
    setTimer() {
      this.socket.emit('newTimer', this.newtimer);
    }
  },
  computed: {
    formattedTimer() {
      return function(timer) {
        // Timer should be formatted as 00:00
        const minutes = Math.floor(timer / 60);
        const seconds = timer % 60;
        return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
      }
    }
  }
}
</script>

<style>
@import url('https://fonts.cdnfonts.com/css/seven-segment');
#app {
  font-family: 'Seven Segment', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #F699E4;
  margin-top: 60px;
  font-size: 5rem;
  height: 100%;
}
html {
  background-color: #000000;
}

.bound-to-bottom {
  position: fixed;
  bottom: 0;
  width: 100%;
  background-color: #000000;
  padding: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
