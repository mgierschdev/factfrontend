
<template>
  <header class="text-center text-white gradient-header py-5">
    <div class="container">
      <h1 class="display-4">Amazing Facts</h1>
      <p class="lead">Discover something new every day!</p>
    </div>
  </header>

  <main class="container py-4">
    <div class="row justify-content-center mb-4">
      <div class="col-md-8">
        <!-- Search and filter section -->
        <div class="search-container mb-4">
          <div class="input-group mb-3">
            <span class="input-group-text"><i class="bi bi-search"></i></span>
            <input 
              type="text" 
              class="form-control form-control-lg" 
              placeholder="Search facts..." 
              v-model="searchQuery"
            >
          </div>
        </div>
        
        <!-- Facts list -->
        <div v-if="loading" class="text-center py-4">
          <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
        </div>
        
        <div v-else>
          <div v-if="filteredFacts.length === 0" class="text-center py-5">
            <p class="fs-5">No facts found matching your search.</p>
          </div>
          
          <div v-else class="facts-list">
            <div v-for="(fact, index) in filteredFacts" :key="index" class="card fact-card mb-3 shadow-sm">
              <div v-if="fact.image" class="fact-image-container">
                <img :src="fact.image" class="fact-image card-img-top" alt="Illustration for fact" />
              </div>
              <div class="card-body">
                <p class="fact-text mb-2">{{ fact.text }}</p>
                <div class="d-flex justify-content-between align-items-center">
                  <div class="fact-tags">
                    <span 
                      v-for="tag in fact.tags" 
                      :key="tag" 
                      class="badge rounded-pill me-1"
                      :class="getTagClass(tag)"
                      @click="filterByTag(tag)"
                    >
                      {{ tag }}
                    </span>
                  </div>
                  <small class="text-muted">Fact #{{ index + 1 }}</small>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <footer class="custom-footer mt-auto">
    <div class="container text-center">
      <p>
        <strong>Facts Website</strong> - Learn something new every day - made with replit
      </p>
    </div>
  </footer>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';

// UI state
const loading = ref(true);
const searchQuery = ref('');
const activeTag = ref('');

// Sample facts database with tags and images
interface Fact {
  text: string;
  tags: string[];
  image?: string;
}

// Combine all facts into a single array with tags and images
const allFacts = ref<Fact[]>([
  { 
    text: 'The shortest war in history was between Britain and Zanzibar in 1896. It lasted only 38 minutes.',
    tags: ['History', 'War', 'Britain', 'Zanzibar'],
    image: 'https://images.unsplash.com/photo-1579532536935-619928decd08?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80'
  },
  { 
    text: 'Ancient Egyptians used to use slabs of stone as pillows.',
    tags: ['History', 'Egypt', 'Lifestyle'],
    image: 'https://images.unsplash.com/photo-1560152789-8d8e1e979611?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80'
  },
  { 
    text: 'The first known contraceptive was crocodile dung, used by ancient Egyptians.',
    tags: ['History', 'Egypt', 'Medicine'],
    image: 'https://images.unsplash.com/photo-1591825729269-caeb344f6df2?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80'
  },
  { 
    text: 'Vikings used the skulls of their enemies as drinking vessels.',
    tags: ['History', 'Vikings', 'Warfare'],
    image: 'https://images.unsplash.com/photo-1598545549693-6c453069d0c8?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80'
  },
  { 
    text: 'Bananas are berries, but strawberries are not.',
    tags: ['Science', 'Food', 'Plants'],
    image: 'https://images.unsplash.com/photo-1571771894821-ce9b6c11b08e?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80'
  },
  { 
    text: 'A day on Venus is longer than a year on Venus.',
    tags: ['Science', 'Space', 'Planets'],
    image: 'https://images.unsplash.com/photo-1614732414444-096e5f1122d5?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80'
  },
  { 
    text: 'Honey never spoils. Archaeologists have found pots of honey in ancient Egyptian tombs that are over 3,000 years old and still perfectly good to eat.',
    tags: ['Science', 'Food', 'Egypt', 'History']
  },
  { 
    text: 'Human DNA is 99.9% identical from person to person.',
    tags: ['Science', 'Biology', 'Genetics']
  },
  { 
    text: 'Alaska is the easternmost, westernmost, and northernmost state in the United States.',
    tags: ['Geography', 'USA', 'Alaska']
  },
  { 
    text: 'There is a town called "Batman" in Turkey.',
    tags: ['Geography', 'Turkey', 'Trivia']
  },
  { 
    text: 'Russia has 11 time zones.',
    tags: ['Geography', 'Russia', 'Time']
  },
  { 
    text: 'The coldest inhabited place on Earth is Oymyakon, Russia, where the temperature once dropped to -71.2°C (-96.16°F).',
    tags: ['Geography', 'Russia', 'Climate', 'Temperature']
  },
  { 
    text: 'Octopuses have three hearts.',
    tags: ['Animals', 'Marine Life', 'Biology']
  },
  { 
    text: 'Flamingos can only eat with their heads upside down.',
    tags: ['Animals', 'Birds', 'Behavior']
  },
  { 
    text: 'A group of flamingos is called a "flamboyance".',
    tags: ['Animals', 'Birds', 'Collective Nouns']
  },
  { 
    text: 'Cows have best friends and get stressed when they are separated.',
    tags: ['Animals', 'Mammals', 'Behavior']
  },
  { 
    text: 'The first computer bug was an actual real-life bug. A moth was found in the Harvard Mark II computer in 1947.',
    tags: ['Technology', 'Computers', 'History']
  },
  { 
    text: 'The QWERTY keyboard layout was designed to slow typists down.',
    tags: ['Technology', 'Computers', 'Design']
  },
  { 
    text: 'More than 90% of the world\'s currency is digital.',
    tags: ['Technology', 'Finance', 'Economy']
  },
  { 
    text: 'The first computer programmer was a woman named Ada Lovelace.',
    tags: ['Technology', 'Computers', 'History', 'Women']
  },
  { 
    text: 'One million Earths could fit inside the Sun.',
    tags: ['Space', 'Sun', 'Astronomy']
  },
  { 
    text: 'There is a planet made of diamonds, called 55 Cancri e.',
    tags: ['Space', 'Planets', 'Astronomy']
  },
  { 
    text: 'A year on Mercury is just 88 days long.',
    tags: ['Space', 'Planets', 'Mercury', 'Time']
  },
  { 
    text: 'The footprints on the Moon will be there for at least 100 million years.',
    tags: ['Space', 'Moon', 'Astronomy', 'NASA']
  }
]);

// Get all unique tags for the filter buttons
const allTags = computed(() => {
  const tagSet = new Set<string>();
  allFacts.value.forEach(fact => {
    fact.tags.forEach(tag => tagSet.add(tag));
  });
  return Array.from(tagSet).sort();
});

// Filter facts based on search query and active tag
const filteredFacts = computed(() => {
  return allFacts.value.filter(fact => {
    // First filter by tag if one is selected
    const tagMatch = activeTag.value === '' || fact.tags.includes(activeTag.value);
    
    // Then filter by search query
    const searchMatch = searchQuery.value === '' || 
      fact.text.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      fact.tags.some(tag => tag.toLowerCase().includes(searchQuery.value.toLowerCase()));
    
    return tagMatch && searchMatch;
  });
});

// Filter by tag
function filterByTag(tag: string) {
  activeTag.value = tag;
}

// Get random color class for tag
function getTagClass(tag: string) {
  const tagClasses = {
    'History': 'bg-primary',
    'Science': 'bg-success',
    'Geography': 'bg-info',
    'Animals': 'bg-warning',
    'Technology': 'bg-danger',
    'Space': 'bg-purple'
  };
  
  // Return the class if it exists in our map, otherwise return a default
  return tagClasses[tag as keyof typeof tagClasses] || 'bg-secondary';
}

// Simulate loading data
onMounted(() => {
  setTimeout(() => {
    loading.value = false;
  }, 500);
});
</script>

<style scoped>
.fact-card {
  transition: transform 0.2s ease-in-out;
  cursor: default;
  border-radius: 8px;
  background-color: #fff;
  border: none;
  overflow: hidden;
}

.fact-image-container {
  height: 200px;
  overflow: hidden;
}

.fact-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.fact-card:hover .fact-image {
  transform: scale(1.05);
}

.fact-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1) !important;
}

.fact-text {
  font-size: 1.1rem;
  line-height: 1.6;
}

.fact-tags .badge {
  cursor: pointer;
  transition: all 0.2s ease;
}

.fact-tags .badge:hover {
  opacity: 0.9;
  transform: scale(1.05);
}

.tags-filter .btn {
  transition: all 0.2s ease;
}

.bg-purple {
  background-color: #6f42c1;
  color: white;
}

.search-container {
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 8px;
  padding: 1rem;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
}

/* Mobile optimizations */
@media (max-width: 576px) {
  .fact-text {
    font-size: 1rem;
  }
  
  .tags-filter {
    overflow-x: auto;
    padding-bottom: 0.5rem;
    flex-wrap: nowrap !important;
  }
  
  .tags-filter .btn {
    white-space: nowrap;
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
