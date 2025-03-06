
<template>
  <header class="hero is-primary">
    <div class="hero-body">
      <div class="container has-text-centered">
        <h1 class="title">Amazing Facts</h1>
        <p class="subtitle">Discover something new every day!</p>
      </div>
    </div>
  </header>

  <main class="section">
    <div class="container">
      <div class="box fact-box">
        <div v-if="loading" class="has-text-centered">
          <p>Loading fact...</p>
        </div>
        <div v-else>
          <p class="fact-text">{{ currentFact }}</p>
          <p class="fact-category">Category: {{ currentCategory }}</p>
        </div>
      </div>
      
      <div class="field is-grouped is-grouped-centered mt-5">
        <div class="control">
          <button @click="getRandomFact" class="button is-primary is-medium">
            New Fact
          </button>
        </div>
        <div class="control">
          <div class="select is-medium">
            <select v-model="selectedCategory" @change="getFactByCategory">
              <option value="">All Categories</option>
              <option v-for="category in categories" :key="category" :value="category">
                {{ category }}
              </option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </main>

  <footer class="footer">
    <div class="content has-text-centered">
      <p>
        <strong>Facts Website</strong> - Learn something new every day
      </p>
    </div>
  </footer>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

// Available fact categories
const categories = ref([
  'History', 'Science', 'Geography', 'Animals', 'Technology', 'Space'
]);

// UI state
const loading = ref(false);
const currentFact = ref('');
const currentCategory = ref('');
const selectedCategory = ref('');

// Sample facts database by category
const factsDatabase = {
  'History': [
    'The shortest war in history was between Britain and Zanzibar in 1896. It lasted only 38 minutes.',
    'Ancient Egyptians used to use slabs of stone as pillows.',
    'The first known contraceptive was crocodile dung, used by ancient Egyptians.',
    'Vikings used the skulls of their enemies as drinking vessels.'
  ],
  'Science': [
    'Bananas are berries, but strawberries are not.',
    'A day on Venus is longer than a year on Venus.',
    'Honey never spoils. Archaeologists have found pots of honey in ancient Egyptian tombs that are over 3,000 years old and still perfectly good to eat.',
    'Human DNA is 99.9% identical from person to person.'
  ],
  'Geography': [
    'Alaska is the easternmost, westernmost, and northernmost state in the United States.',
    'There is a town called "Batman" in Turkey.',
    'Russia has 11 time zones.',
    'The coldest inhabited place on Earth is Oymyakon, Russia, where the temperature once dropped to -71.2°C (-96.16°F).'
  ],
  'Animals': [
    'Octopuses have three hearts.',
    'Flamingos can only eat with their heads upside down.',
    'A group of flamingos is called a "flamboyance".',
    'Cows have best friends and get stressed when they are separated.'
  ],
  'Technology': [
    'The first computer bug was an actual real-life bug. A moth was found in the Harvard Mark II computer in 1947.',
    'The QWERTY keyboard layout was designed to slow typists down.',
    'More than 90% of the world\'s currency is digital.',
    'The first computer programmer was a woman named Ada Lovelace.'
  ],
  'Space': [
    'One million Earths could fit inside the Sun.',
    'There is a planet made of diamonds, called 55 Cancri e.',
    'A year on Mercury is just 88 days long.',
    'The footprints on the Moon will be there for at least 100 million years.'
  ]
};

// Get a random fact from all categories
function getRandomFact() {
  loading.value = true;
  
  // Simulate API call with timeout
  setTimeout(() => {
    const allCategories = Object.keys(factsDatabase);
    const randomCategory = allCategories[Math.floor(Math.random() * allCategories.length)];
    const factsInCategory = factsDatabase[randomCategory as keyof typeof factsDatabase];
    const randomFact = factsInCategory[Math.floor(Math.random() * factsInCategory.length)];
    
    currentFact.value = randomFact;
    currentCategory.value = randomCategory;
    loading.value = false;
    selectedCategory.value = '';
  }, 500);
}

// Get a fact from selected category
function getFactByCategory() {
  if (!selectedCategory.value) {
    getRandomFact();
    return;
  }
  
  loading.value = true;
  
  // Simulate API call with timeout
  setTimeout(() => {
    const factsInCategory = factsDatabase[selectedCategory.value as keyof typeof factsDatabase];
    const randomFact = factsInCategory[Math.floor(Math.random() * factsInCategory.length)];
    
    currentFact.value = randomFact;
    currentCategory.value = selectedCategory.value;
    loading.value = false;
  }, 500);
}

// Load a random fact when component mounts
onMounted(() => {
  getRandomFact();
});
</script>

<style scoped>
.fact-box {
  min-height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 2rem;
  background-color: #f8f9fa;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 2rem;
}

.fact-text {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  text-align: center;
  line-height: 1.6;
}

.fact-category {
  text-align: center;
  font-style: italic;
  color: #666;
}

.mt-5 {
  margin-top: 2rem;
}
</style>
