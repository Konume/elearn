<template>
    <div>
      <div v-if="!timerRunning">
        <label for="timeInput">Ustaw czas (w sekundach):</label>
        <input type="number" id="timeInput" v-model="timeInSeconds" min="1">
        <button @click="startTimer">Start</button>
      </div>
      <div v-else>
        <p>Czas pozostały: {{ formattedTime }}</p>
        <button @click="stopTimer">Stop</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'timerRunning',
    data() {
      return {
        timeInSeconds: 0,
        timerRunning: false,
        formattedTime: '0:00'
      };
    },
    methods: {
  startTimer() {
    this.timerRunning = true;
    let timer = setInterval(() => {
      this.timeInSeconds--;
      if (this.timeInSeconds <= 0) {
        clearInterval(timer);
        this.timerRunning = false;
        this.showNotification();
      }
      this.formatTime();
    }, 1000);
  },
  stopTimer() {
    this.timerRunning = false;
    this.timeInSeconds = 0;
    this.formattedTime = '0:00';
  },
  formatTime() {
    let minutes = Math.floor(this.timeInSeconds / 60);
    let seconds = this.timeInSeconds % 60;
    this.formattedTime = `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
  },
  showNotification() {
    if (!("Notification" in window)) {
      alert("Twoja przeglądarka nie obsługuje powiadomień.");
    } else if (Notification.permission === "granted") {
      new Notification("Czas minął!", { body: "Twój czasomierz skończył odliczanie." });
    } else if (Notification.permission !== 'denied') {
      Notification.requestPermission().then(function(permission) {
        if (permission === "granted") {
          new Notification("Czas minął!", { body: "Twój czasomierz skończył odliczanie." });
        }
      });
    }
  },
  endTimer() {
    this.$emit('timer-end');
  } 
}
 }
  </script>
  
  <style scoped>
  /* Dodaj stylizację tutaj */
  .fade-in {
  opacity: 1; /* Ustawiamy pełną przejrzystość, aby pokazać pasek */
}

.fade-out {
  opacity: 0; /* Ustawiamy zerową przejrzystość, aby ukryć pasek */
}
  </style>
  