<template>
  <div class="pa-4">
    <div class="mb-4">
      <!-- Show "Get data" button only if no data is fetched -->
      <v-btn
        v-if="!products.length"
        @click="fetchProducts"
        color="primary"
      >
        Get data
      </v-btn>

      <!-- Once data is fetched, show "Reverse data" button and "Clear data" button -->
      <template v-else>
        <v-btn
          @click="toggleMirrored"
          color="primary"
        >
          {{ showMirrored ? 'Show Normal Data' : 'Reverse Data' }}
        </v-btn>

        <v-btn
          @click="clearData"
          color="error"
          class="ml-2"
        >
          Clear data
        </v-btn>
      </template>
    </div>

    <!-- If we have products, show either normal or mirrored products -->
    <div v-if="products.length">
      <v-list two-line>
        <v-list-item
          v-for="product in displayedProducts"
          :key="product.id"
        >
          <v-list-item-title>{{ product.title }}</v-list-item-title>
          <v-list-item-subtitle>{{ product.description }}</v-list-item-subtitle>
        </v-list-item>
      </v-list>
    </div>
    <div v-else>
      <p>No data fetched yet.</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const products = ref<Array<any>>([]);
const showMirrored = ref(false);

const mirrorSentence = (sentence: string): string=> {
  return sentence
    .split(' ')
    .reverse()
    .map(word => word.split('').reverse().join(''))
    .join(' ');
}

const fetchProducts = async ()=> {
  try {
    const res = await fetch('http://localhost:4000/data')
    if (!res.ok) throw new Error('Network response was not ok')
    products.value = await res.json()
  } catch (error) {
    console.error('Error fetching products:', error)
  }
}

const mirroredProducts = computed(() => {
  return products.value.map(product => ({
    id: product.id,
    title: mirrorSentence(product.title),
    description: mirrorSentence(product.description)
  }));
});

const displayedProducts = computed(() => {
  return showMirrored.value ? mirroredProducts.value : products.value;
});

const toggleMirrored = () => {
  showMirrored.value = !showMirrored.value;
}

const clearData = ()=> {
  products.value = [];
  showMirrored.value = false;
}
</script>

<style scoped>
.pa-4 {
  padding: 16px;
}
.mb-4 {
  margin-bottom: 16px;
}
</style>
