<script setup lang="ts">
import { computed } from 'vue'
import { Bar, Line } from 'vue-chartjs'
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  BarElement,
  PointElement,
  LineElement,
  Filler,
  Title,
  Tooltip,
  Legend,
} from 'chart.js'

ChartJS.register(
  CategoryScale,
  LinearScale,
  BarElement,
  PointElement,
  LineElement,
  Filler,
  Title,
  Tooltip,
  Legend
)

interface MonthData {
  month: string
  year: number
  revenue: number
  visitors: number
  conversions: number
  orders: number
}

const props = defineProps<{
  data: MonthData[]
  filteredData: MonthData[]
  selectedMonth: string
  selectedIndex: number
}>()

const isAll = computed(() => props.selectedMonth === 'All')

// Summary card values
const totalRevenue = computed(() =>
  props.filteredData.reduce((sum, m) => sum + m.revenue, 0)
)
const totalVisitors = computed(() =>
  props.filteredData.reduce((sum, m) => sum + m.visitors, 0)
)
const avgConversions = computed(() => {
  const sum = props.filteredData.reduce((s, m) => s + m.conversions, 0)
  return +(sum / props.filteredData.length).toFixed(1)
})
const totalOrders = computed(() =>
  props.filteredData.reduce((sum, m) => sum + m.orders, 0)
)

// Change from previous month
function getChange(field: keyof MonthData): number | null {
  if (isAll.value || props.selectedIndex <= 0) return null
  const curr = props.data[props.selectedIndex][field] as number
  const prev = props.data[props.selectedIndex - 1][field] as number
  if (prev === 0) return null
  return +((((curr - prev) / prev) * 100).toFixed(1))
}

const revenueChange = computed(() => getChange('revenue'))
const visitorsChange = computed(() => getChange('visitors'))
const conversionsChange = computed(() => getChange('conversions'))
const ordersChange = computed(() => getChange('orders'))

function formatCurrency(val: number): string {
  return '$' + val.toLocaleString()
}
function formatNumber(val: number): string {
  return val.toLocaleString()
}

// Colors
const teal = '#4db6ac'
const blue = '#64b5f6'
const amber = '#ffb74d'
const pink = '#f06292'
const tealFaded = 'rgba(77, 182, 172, 0.15)'

// Chart shared options
const gridColor = 'rgba(255,255,255,0.06)'
const tickColor = 'rgba(255,255,255,0.5)'

// Revenue bar chart
const revenueChartData = computed(() => {
  const labels = isAll.value
    ? props.data.map((m) => m.month)
    : props.filteredData.map((m) => m.month)
  const values = isAll.value
    ? props.data.map((m) => m.revenue)
    : props.filteredData.map((m) => m.revenue)
  const bgColors = isAll.value
    ? props.data.map((_, i) =>
        props.selectedIndex === -1
          ? teal
          : i === props.selectedIndex
            ? teal
            : 'rgba(77,182,172,0.25)'
      )
    : [teal]

  return {
    labels,
    datasets: [
      {
        label: 'Revenue',
        data: values,
        backgroundColor: bgColors,
        borderRadius: 4,
        borderSkipped: false as const,
      },
    ],
  }
})

const revenueChartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: false },
    tooltip: {
      callbacks: {
        label: (ctx: any) => '$' + (ctx.raw as number).toLocaleString(),
      },
    },
  },
  scales: {
    x: {
      grid: { display: false },
      ticks: { color: tickColor },
    },
    y: {
      grid: { color: gridColor },
      ticks: {
        color: tickColor,
        callback: (v: any) => '$' + (v / 1000).toFixed(0) + 'k',
      },
    },
  },
}))

// Visitors line chart
const visitorsChartData = computed(() => {
  const labels = isAll.value
    ? props.data.map((m) => m.month)
    : props.filteredData.map((m) => m.month)
  const values = isAll.value
    ? props.data.map((m) => m.visitors)
    : props.filteredData.map((m) => m.visitors)

  return {
    labels,
    datasets: [
      {
        label: 'Visitors',
        data: values,
        borderColor: blue,
        backgroundColor: 'rgba(100,181,246,0.1)',
        tension: 0.35,
        fill: false,
        pointBackgroundColor: blue,
        pointRadius: isAll.value ? 4 : 6,
        pointHoverRadius: 7,
      },
    ],
  }
})

const visitorsChartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: false },
    tooltip: {
      callbacks: {
        label: (ctx: any) => (ctx.raw as number).toLocaleString() + ' visitors',
      },
    },
  },
  scales: {
    x: {
      grid: { display: false },
      ticks: { color: tickColor },
    },
    y: {
      grid: { color: gridColor },
      ticks: {
        color: tickColor,
        callback: (v: any) => (v / 1000).toFixed(0) + 'k',
      },
    },
  },
}))

// Conversion area chart
const conversionChartData = computed(() => {
  const labels = isAll.value
    ? props.data.map((m) => m.month)
    : props.filteredData.map((m) => m.month)
  const values = isAll.value
    ? props.data.map((m) => m.conversions)
    : props.filteredData.map((m) => m.conversions)

  return {
    labels,
    datasets: [
      {
        label: 'Conversion %',
        data: values,
        borderColor: teal,
        backgroundColor: tealFaded,
        tension: 0.35,
        fill: true,
        pointBackgroundColor: teal,
        pointRadius: isAll.value ? 4 : 6,
        pointHoverRadius: 7,
      },
    ],
  }
})

const conversionChartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: false },
    tooltip: {
      callbacks: {
        label: (ctx: any) => (ctx.raw as number).toFixed(1) + '%',
      },
    },
  },
  scales: {
    x: {
      grid: { display: false },
      ticks: { color: tickColor },
    },
    y: {
      grid: { color: gridColor },
      ticks: {
        color: tickColor,
        callback: (v: any) => v + '%',
      },
      suggestedMin: 1,
      suggestedMax: 5.5,
    },
  },
}))

interface CardInfo {
  title: string
  value: string
  icon: string
  color: string
  change: number | null
}

const cards = computed<CardInfo[]>(() => [
  {
    title: 'Revenue',
    value: formatCurrency(totalRevenue.value),
    icon: 'mdi-currency-usd',
    color: teal,
    change: revenueChange.value,
  },
  {
    title: 'Visitors',
    value: formatNumber(totalVisitors.value),
    icon: 'mdi-account-group-outline',
    color: blue,
    change: visitorsChange.value,
  },
  {
    title: 'Conversions',
    value: avgConversions.value + '%',
    icon: 'mdi-percent-outline',
    color: amber,
    change: conversionsChange.value,
  },
  {
    title: 'Orders',
    value: formatNumber(totalOrders.value),
    icon: 'mdi-cart-outline',
    color: pink,
    change: ordersChange.value,
  },
])
</script>

<template>
  <v-container fluid class="pa-6">
    <!-- Summary Cards -->
    <v-row class="mb-2">
      <v-col
        v-for="card in cards"
        :key="card.title"
        cols="12"
        sm="6"
        lg="3"
      >
        <v-card variant="tonal" rounded="lg" class="pa-4">
          <div class="d-flex align-center justify-space-between mb-2">
            <span class="text-caption text-medium-emphasis text-uppercase font-weight-medium">
              {{ card.title }}
            </span>
            <v-icon :color="card.color" size="20">{{ card.icon }}</v-icon>
          </div>
          <div class="text-h5 font-weight-bold mb-1">{{ card.value }}</div>
          <div v-if="card.change !== null" class="d-flex align-center">
            <v-icon
              :color="card.change >= 0 ? 'success' : 'error'"
              size="16"
              class="mr-1"
            >
              {{ card.change >= 0 ? 'mdi-arrow-up' : 'mdi-arrow-down' }}
            </v-icon>
            <span
              class="text-caption"
              :class="card.change >= 0 ? 'text-success' : 'text-error'"
            >
              {{ Math.abs(card.change) }}% vs prev month
            </span>
          </div>
          <div v-else class="text-caption text-medium-emphasis">
            {{ isAll ? 'Full year' : 'No prior data' }}
          </div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Charts row: Revenue bar + Visitors line -->
    <v-row class="mb-2">
      <v-col cols="12" md="6">
        <v-card variant="tonal" rounded="lg" class="pa-4">
          <div class="text-subtitle-2 text-medium-emphasis mb-4">Monthly Revenue</div>
          <div style="height: 280px">
            <Bar :data="revenueChartData" :options="revenueChartOptions" />
          </div>
        </v-card>
      </v-col>
      <v-col cols="12" md="6">
        <v-card variant="tonal" rounded="lg" class="pa-4">
          <div class="text-subtitle-2 text-medium-emphasis mb-4">Visitors Over Time</div>
          <div style="height: 280px">
            <Line :data="visitorsChartData" :options="visitorsChartOptions" />
          </div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Full-width conversion area chart -->
    <v-row>
      <v-col cols="12">
        <v-card variant="tonal" rounded="lg" class="pa-4">
          <div class="text-subtitle-2 text-medium-emphasis mb-4">Conversion Trend</div>
          <div style="height: 260px">
            <Line :data="conversionChartData" :options="conversionChartOptions" />
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
