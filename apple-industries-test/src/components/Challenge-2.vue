<template>
  <div class="pa-4 fill-height d-flex flex-column align-center justify-center">
    <!-- Initial instructions and form, shown only if not loading and no result -->
    <div v-if="!loading && !result" class="text-center" style="width: 300px;">
      <h2>I’m feeling lucky!</h2>
      <p class="mb-4">Select a package type and then click "Try Your Luck" to see if you win the free packages.</p>

      <v-select
        v-model="selectedPackage"
        :items="packageTypes"
        label="Select Package"
        variant="outlined"
        class="mb-4"
      ></v-select>

      <v-btn
        @click="tryYourLuck"
        color="primary"
        :disabled="!selectedPackage"
      >
        Try Your Luck
      </v-btn>
    </div>

    <!-- Loader displayed while waiting for response -->
    <div v-if="loading" class="d-flex justify-center align-center fill-height">
      <v-progress-circular
        indeterminate
        color="primary"
        size="64"
      ></v-progress-circular>
    </div>

    <!-- Once we have a result, show it in a card at the center of the screen -->
    <div v-if="result && !loading" class="d-flex justify-center align-center fill-height">
      <v-card class="pa-4 text-center" max-width="400">
        <h3 class="mb-4 text-center">Result</h3>
        <p><strong>Ordered Package:</strong> {{ result.orderedPackage.type }} - {{ result.orderedPackage.description }} ({{ result.orderedPackage.price }}$)</p>

        <!-- If user won -->
        <template v-if="result.won">
          <v-alert type="success">
            <p><strong>Congratulations! You won</strong></p>
            <h4>Free Packages:</h4>
            <ul>
              <li
                v-for="pack in result.freePackages"
                :key="pack.type"
              >
                {{ pack.type }} - {{ pack.description }} ({{ pack.price }}$)
              </li>
            </ul>
            <v-btn class="mt-4" color="primary" @click="reset">
              Awesome! Thank you!
            </v-btn>
          </v-alert>
        </template>

        <!-- If user did not win -->
        <template v-else>
          <v-alert type="info">
            <p><strong>Sorry you not won, try again later!</strong></p>
            <v-btn class="mt-4 ml-2 float-right" color="error" @click="reset">
              Back
            </v-btn>
          </v-alert>
        </template>
      </v-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const packageTypes = ['prints', 'panoramas', 'strips'];
const selectedPackage = ref<string | null>(null);
const result = ref<any>(null);
const loading = ref<boolean>(false);

async function tryYourLuck() {
  if (!selectedPackage.value) return;
  loading.value = true;
  result.value = null; // clear any previous result

  try {
    const res = await fetch(`http://localhost:4000/lucky?package=${selectedPackage.value}`);
    if (!res.ok) throw new Error('Network error');
    const data = await res.json();
    result.value = data;
  } catch (error) {
    console.error(error);
  } finally {
    loading.value = false;
  }
}

function reset() {
  result.value = null;
  selectedPackage.value = null;
}
</script>

<style scoped>
.fill-height {
  height: 100%;
}
</style>
