
<template>
  <header class="text-center text-white gradient-header py-5">
    <div class="container">
      <h1 class="display-4">Amazing Facts</h1>
      <p class="lead">Discover something new every day!</p>
    </div>
  </header>

  <main class="container py-4">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <div class="card shadow-sm fact-box mb-4">
          <div class="card-body">
            <div v-if="loading" class="text-center py-4">
              <div class="spinner-border text-primary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else class="py-2">
              <p class="fact-text">{{ currentFact }}</p>
              <p class="fact-category">Category: {{ currentCategory }}</p>
            </div>
          </div>
        </div>
        
        <div class="d-flex flex-column flex-sm-row justify-content-center gap-2 mb-4">
          <button @click="getRandomFact" class="btn btn-custom btn-lg">
            New Fact
          </button>
          <select 
            v-model="selectedCategory" 
            @change="getFactByCategory" 
            class="form-select form-select-lg"
            aria-label="Select category"
          >
            <option value="">All Categories</option>
            <option v-for="category in categories" :key="category" :value="category">
              {{ category }}
            </option>
          </select>
        </div>
      </div>
    </div>
  </main>

  <footer class="custom-footer mt-auto">
    <div class="container text-center">
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
  background-color: #fff;
  border-radius: 8px;
  border: none;
}

.fact-text {
  font-size: 1.25rem;
  margin-bottom: 1rem;
  text-align: center;
  line-height: 1.6;
}

.fact-category {
  text-align: center;
  font-style: italic;
  color: #666;
  margin-bottom: 0;
}

/* Add Bootstrap's responsive padding/margin classes */
@media (max-width: 576px) {
  .fact-text {
    font-size: 1.1rem;
  }
  
  .btn, .form-select {
    width: 100%;
  }
}

/* Make sure the page is at least as tall as the viewport */
html, body, #app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

main {
  flex: 1;
}
</style>
