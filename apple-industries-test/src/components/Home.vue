<template>
  <v-card class="my-4 mx-10">
    <v-toolbar color="teal">
      <v-toolbar-title>Apple Industries - test task</v-toolbar-title>
      <template v-slot:extension>
        <v-tabs v-model="currentItem" fixed-tabs>
          <v-tab
            v-for="item in items"
            :key="item"
          >
            {{ item }}
          </v-tab>
        </v-tabs>
      </template>
    </v-toolbar>

    <v-window v-model="currentItem">
      <v-window-item
        v-for="item in items"
        :key="item"
        :value="item"
      >
        <v-card min-height="500">
          <v-card-text>
            <component :is="tabComponents[item]" />
          </v-card-text>
        </v-card>
      </v-window-item>
    </v-window>
  </v-card>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import ChallengeOne from './Challenge-1.vue'
import ChallengeTwo from './Challenge-2.vue'
import ChallengeThree from './Challenge-3.vue'

const items = ['Challenge 1', 'Challenge 2', 'Challenge 3'];

const currentItem = ref('Challenge 1');

const tabComponents: Record<string, any> = {
  'Challenge 1': ChallengeOne,
  'Challenge 2': ChallengeTwo,
  'Challenge 3': ChallengeThree
};
</script>

<style scoped>

</style>
