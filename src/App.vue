<script setup>
import { ref } from "vue";
// const { GoogleGenerativeAI } = require("@google/generative-ai");
import { GoogleGenerativeAI } from "@google/generative-ai";

import { nonWorkQuestions } from "./assets/questions.js"
  const deck = ref([...nonWorkQuestions]);
    const category = ['Pekerjaan', 'Kesehatan', 'Pariwisata', 'Ekonomi', 'Kenangan Masa Kecil', 'Filosofi', 'Politik', 'Teknologi', 'Hiburan', 'Olahraga', 'Persahabatan', 'Keuangan', "Handmade"]
    //   const category = [
    //   "Pop culture & entertainment",
    //   "Personal experiences & preferences",
    //   "Food & hobbies",
    //   "Work & studies",
    //   "Travel & adventure",
    //   "Technology & the future",
    //   "Random & fun",
    //   "Childhood memories",
    //   "Bucket list items",
    //   "Social causes & beliefs",
    // ];
    const selectedCard = ref('');
    const animateDeck = ref(false);
    const aiQuestion = ref('');
    const aiIsLoading = ref(false);
    const theme = ref('')

    function drawCard() {
      animateDeck.value = true;
      setTimeout(() => {
        animateDeck.value = false;
      }, 1000);
      console.log(animateDeck.value, 'animateDeck.value')
      if (deck.value.length === 0) {
        selectedCard.value = '';
        return;
      }
      const randomIndex = Math.floor(Math.random() * deck.value.length);
      selectedCard.value = deck.value[randomIndex];
      deck.value.splice(randomIndex, 1);
    }
    function resetDeck() {
      deck.value = [...nonWorkQuestions];
      selectedCard.value = '';
    }
    function resetCategory () {
      aiQuestion.value = '';
      theme.value = ''
    }
    async function generateWithAI() {
      aiIsLoading.value = true;
      const genAI = new GoogleGenerativeAI(import.meta.env.VITE_APIURL);
      const model = genAI.getGenerativeModel({ model: "gemini-pro"});
      const prompt = `Write one question for ice breaking session. the theme is ${theme.value || 'anything'}. it should be fun and interesting that tell us more about the person. just write down the question without any additional information. make it in bahasa indonesia.`;
      const result = await model.generateContent(prompt);
      const response = await result.response;
      const text = response.text();

      aiQuestion.value = text
      aiIsLoading.value = false;
    }

</script>

<template>
  <div class="table-wrapper">
    <div v-if="!theme" class="category-container">
      <div v-for="(item, index) in category" :key="index" class="category-cards-wrapper">
        <div @click="theme = item" class="category-card">
          {{ item === 'Handmade' ? "I Don't Want Robot Asking Me Question" : item}}
        </div>
      </div>
    </div>

    <div v-if="theme" class="play-area-container">
      <div class="category-title" @click="resetCategory"> {{ theme }}</div>
      <div v-if="theme !== 'Handmade'" class="deck-container">
        <div class="ai-wrapper">{{ aiQuestion }}</div>
        <div v-if="aiIsLoading">Loading...</div>
      </div>
      <div class="player-container">
        <div class="card-wrapper" :class="{'empty-card': !selectedCard}">{{selectedCard}}</div>
        <div class="button-container">
          <button v-if="!aiIsLoading && theme !== 'Handmade'" @click="generateWithAI">Generate With AI</button>
          <button v-if="!aiIsLoading && theme === 'Handmade'" @click="drawCard">Draw Cards</button>
          <button v-if="!aiIsLoading && theme === 'Handmade'" @click="resetDeck">Reset Deck</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.ai-wrapper {
  font-size: xx-large;
  font-weight: 600;
  text-align: center;
  padding: 20px;
  background-color: #F3E8EE;
  border-radius: 10px;
  color: #2E2C2F;
}
.table-wrapper {
  display: flex;
  flex-wrap: wrap;
  height: 100vh;
  width: 100vw;
  justify-content: center;
  align-items: center;
  background: #729B79;
}
.deck-wrapper {
  position: absolute;
  width: 240px;
  height: 350px;
  border-radius: 10px;
  background-color: #2E2C2F;
  color: #2E2C2F;
  font-weight: 600;
  padding: 20px;
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
  display: flex;
  justify-content: center;
  align-items: center;
}
.empty-card {
  display: none;
}
.category-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 20px;
  padding: 20px;
}
.category-cards-wrapper {
  display: flex;
  gap: 20px;
  padding: 20px;
}
.category-title {
  cursor: pointer;
  font-size: xx-large;
  font-weight: 600;
  text-align: center;
  color: silver;
}
.category-card {
  /* white box card */
  width: 150px;
  height: 150px;
  border-radius: 10px;
  background-color: #F3E8EE;
  color: #2E2C2F;
  font-weight: 600;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}
.draw {
  animation: move-and-rotate 0.5s forwards;
}

@keyframes move-and-rotate {
  from {
    transform: rotate(0deg) translateX(0px);
  }
  to {
    transform: rotate(180deg) translateX(400px);
  }
}
</style>
