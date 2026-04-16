<script setup lang="ts">
import { ref, computed } from 'vue'
import metricsData from './data/metrics.json'
import HomeView from './views/HomeView.vue'

const months = metricsData.map((m) => m.month)
const monthOptions = ['All', ...months]
const selectedMonth = ref('All')

const filteredData = computed(() => {
  if (selectedMonth.value === 'All') return metricsData
  return metricsData.filter((m) => m.month === selectedMonth.value)
})

const selectedIndex = computed(() => {
  if (selectedMonth.value === 'All') return -1
  return metricsData.findIndex((m) => m.month === selectedMonth.value)
})
</script>

<template>
  <v-app>
    <v-app-bar flat density="comfortable" color="surface">
      <v-app-bar-title class="text-h6 font-weight-bold">
        DL Dashboard
      </v-app-bar-title>
      <template #append>
        <v-select
          v-model="selectedMonth"
          :items="monthOptions"
          variant="outlined"
          density="compact"
          hide-details
          style="max-width: 160px"
        />
      </template>
    </v-app-bar>
    <v-main>
      <HomeView
        :data="metricsData"
        :filtered-data="filteredData"
        :selected-month="selectedMonth"
        :selected-index="selectedIndex"
        @select-month="(m) => selectedMonth = m"
      />
    </v-main>
  </v-app>
</template>
