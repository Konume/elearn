<template>
  <div id="app">
    <header>
     
    </header>
    <main>
      <Board course-learn-endpoint="http://porzuczek.pl/learn/22S22ISI32/s213shabibiwclg1" />
    </main>
  </div>
</template>

<script>

import Board from './components/Board.vue';
import 'bootstrap/dist/css/bootstrap.min.css';

export default {
  name: 'App',
  components: {
  
    Board
  }
}
</script>

<style>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background-color: #f8f8f8;
  border-bottom: 1px solid #ddd;
}

nav span {
  margin: 0 10px;
  cursor: pointer;
  color: #333;
}
</style>
