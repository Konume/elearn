<template>
  <div class="top-bar">
    <transition name="fade">
      <div class="current-date-time">{{ currentDateTime }}</div>
    </transition>
    <timer-running v-if="showTimer" @timer-end="handleTimerEnd" />
  </div>
</template>

<script>
import timerRunning from './timerRunning.vue'; // Import komponentu TimerRunning

export default {
  name: 'topbar',
  data() {
    return {
      currentDateTime: '', // Dodajemy zmienną przechowującą aktualną datę i godzinę
    };
  },
  components: { // Poprawiona właściwość components
    timerRunning
  },

  mounted() {
    // Wywołujemy metodę aktualizacji daty i godziny po zamontowaniu komponentu
    this.updateDateTime();
  },
  methods: {
    // Metoda do aktualizacji daty i godziny w czasie rzeczywistym
    updateDateTime() {
      setInterval(() => {
        const now = new Date();
        const options = {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          hour: 'numeric',
          minute: 'numeric',
          second: 'numeric',
        };
        this.currentDateTime = now.toLocaleDateString(undefined, options);
      }, 1000); // Aktualizacja co sekundę
    },
  },
};
</script>

<style scoped>
.top-bar {
  position: fixed;
  top: 0;
  right: 0;
  padding: 10px;
  font-size: 16px;
  background-color: #ffffff;
  border-bottom: 1px solid #ccc;
}

.current-date-time {
  margin-right: 10px;
}

/* Animacja */
.fade-in {
  opacity: 1; /* Ustawiamy pełną przejrzystość, aby pokazać pasek */
}

.fade-out {
  opacity: 0; /* Ustawiamy zerową przejrzystość, aby ukryć pasek */
}
</style>
