<script setup>
import { ref } from "vue";
import { questions } from "./assets/questions.js"
  const deck = ref([...questions]);
    const selectedCard = ref('');

    function drawCard() {
      if (deck.value.length === 0) {
        selectedCard.value = '';
        return;
      }
      const randomIndex = Math.floor(Math.random() * deck.value.length);
      selectedCard.value = deck.value[randomIndex];
      deck.value.splice(randomIndex, 1);
    }
      function resetDeck() {
      deck.value = [...questions];
      selectedCard.value = '';
    }
</script>

<template>
  <div class="table-wrapper">
    <div class="deck-container">
      <div class="deck-wrapper"></div>
    </div>
    <div class="player-container">
      <div class="card-wrapper" :class="{'empty-card': !selectedCard}">{{selectedCard}}</div>
      <div class="button-container">
        <button v-if="deck.length !== 0" @click="drawCard">Draw Card</button>
        <button @click="resetDeck">Reset Deck</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.table-wrapper {
  display: flex;
  /* flex-direction: column; */
  gap: 20px;
  padding: 100px;
  background: #729B79;
}
.deck-wrapper {
  width: 240px;
  height: 350px;
  border-radius: 10px;
  background-color: #2E2C2F;
  color: #2E2C2F;
  font-weight: 600;
  padding: 20px;
  /* make text in the middle of div */
  display: flex;
  justify-content: center;
  align-items: center;
}

.button-container {
  gap: 20px;
  display: flex;
  justify-content: space-between;
  padding: 20px;
}
.card-wrapper {
  width: 350px;
  height: 240px;
  border-radius: 10px;
  background-color: #F3E8EE;
  color: #2E2C2F;
  font-weight: 600;
  padding: 20px;
  /* make text in the middle of div */
  display: flex;
  justify-content: center;
  align-items: center;
}
.empty-card {
  display: none;
}
</style>
