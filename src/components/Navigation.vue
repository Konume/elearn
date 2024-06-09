<template>
  <div class="navigation" style="width: 250px;">
    <div v-for="(item, itemi) in menu" :key="itemi">
      <button class="btn" 
              :style="{ backgroundColor: item.color, color: item.textColor }"
              @click="toggleChildren(itemi)">
        {{ item.title }}
      </button>
      <!-- Iteracja przez dzieci (child) elementów menu -->
      <div v-show="isExpanded === itemi" class="child-list" :style="{ maxHeight: isExpanded === itemi ? '500px' : '0' }">
        <div v-for="(child, childi) in item.child" :key="childi">
          <button class="btn btn-secondary" 
                  :style="{ backgroundColor: child.color, color: child.textColor }"
                  @click="onItemClick(child)">
            {{ child.title }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Navigation',
  props: {
    course: { type: Array, default: () => [{}] },
  },
  data() {
    return {
      isExpanded: null, // Indeks elementu, który jest rozwinięty
      isClickEnabled: true, // Flaga określająca, czy kliknięcia są aktywne
    };
  },
  computed: {
    menu() {
      // Sprawdzanie, czy kurs i jego tematy istnieją, a następnie zwracanie tematów jako menu
      return this.course.length > 0 && this.course[0].topics
        ? this.course[0].topics.map((topic, index) => ({
            ...topic,
            color: index % 2 === 0 ? '#ffffff' : '#f0f0f0', // Domyślny kolor tła, zmieniany co dwa tematy
            textColor: index % 2 === 0 ? '#000000' : '#333333', // Domyślny kolor tekstu, zmieniany co dwa tematy
          }))
        : [];
    },
  },
  methods: {
    onItemClick(item) {
      // Emitowanie zdarzenia do nadrzędnego komponentu z wybranym elementem
      this.$emit('select-content', item);
    },
    toggleChildren(itemi) {
      if (!this.isClickEnabled) return; // Jeśli kliknięcia nie są aktywne, przerwij działanie metody
      if (this.isExpanded === itemi) {
        this.isExpanded = null; // Jeśli lista dzieci jest już rozwinięta, schowaj ją
      } else {
        this.isExpanded = itemi; // W przeciwnym razie rozwiniętą listą dzieci jest ta przynależąca do aktualnie klikniętego tematu głównego
        this.isClickEnabled = false; // Wyłącz kliknięcia na chwilę
        setTimeout(() => {
          this.isClickEnabled = true; // Po krótkim opóźnieniu włącz ponownie kliknięcia
        }, 300); // Ustaw opóźnienie na 300 milisekund
      }
    },
  },
};
</script>

<style>
.child-list {
  overflow: hidden; /* Ukryj dzieci, które są poza kontenerem */
  transition: max-height 0.3s ease; /* Dodaj animację przejścia */
}
</style>
