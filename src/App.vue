<template>
  <h1 v-if="timer != -1 && timer != -2" ref="timer">{{ formattedTimer(timer) }}</h1>

  <div class="bound-to-bottom">
    <input type="number" v-model="newtimer" @keypress="event => event.key === 'Enter' && setTimer()" />
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
      socket: null,
      flashes: 0
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
        let formattedTimer = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
        return formattedTimer;
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
body {
  background-color: #000000;
  font-family: 'Seven Segment', sans-serif;
  font-weight: 400;
}

.bound-to-bottom {
  position: fixed;
  bottom: 0;
  width: 100%;
  padding: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: 'Seven Segment', sans-serif;
  font-weight: 400;
}
</style>
