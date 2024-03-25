<template>
  <h1 v-if="timer != -1 && timer != -2" ref="timer" v-html="formattedTimer(timer)"></h1>
  <h1 v-if="timer == -2" ref="timer">00<span class="colon">:</span>00</h1>

  <div class="bound-to-bottom" v-if="controlsEnabled">
    <input type="number" v-model="newtimer" @keypress="event => event.key === 'Enter' && setTimer()" />
    <button @click="setTimer()">Set Timer</button>
    <button @click="hideControls()">Hide Controls</button>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  name: 'App',
  mounted() {
    const socket = io('https://io.comestible.domyjobfor.me/');
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
      flashes: 0,
      controlsEnabled: true
    }
  },
  methods: {
    setTimer() {
      this.newtimer = parseInt(this.newtimer);
      this.socket.emit('newTimer', this.newtimer);
    },
    hideControls() {
      this.controlsEnabled = false;
    }
  },
  computed: {
    formattedTimer() {
      return function(timer) {
        // Timer should be formatted as 00:00
        const minutes = Math.floor(timer / 60);
        const seconds = timer % 60;
        let formattedTimer = `${minutes.toString().padStart(2, '0')}<span class="colon">:</span>${seconds.toString().padStart(2, '0')}`;
        return formattedTimer;
      }
    }
  }
}
</script>

<style>
@font-face {
  font-family: 'Segment7Standard';
  src: url('/public/Segment7Standard.otf') format('opentype');
  font-weight: normal;
  font-style: italic;
}
@import url('https://fonts.cdnfonts.com/css/seven-segment');
#app {
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
  font-family: 'Segment7Standard';
  font-weight: normal;
  font-style: italic;
}

.colon {
    font-family: 'Seven Segment', sans-serif;
    font-weight: 400;
    font-size: 25rem;
    font-style: normal;
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
