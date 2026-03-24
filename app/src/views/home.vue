<template>
  <div>
    <h1>Deadly Sentencing</h1>
    <div v-if="loading">Loading data...</div>
    <div v-else style="max-width: 800px; margin: 0 auto">
      <Bar :options="chartOptions" :data="chartData" />
      <Bar :options="ageChartOptions" :data="ageChartData" style="margin-top: 48px" />
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted, computed } from 'vue'
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from 'chart.js'
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)
const rawData = ref([])
const loading = ref(true)
async function getdata() {
  try {
    const response = await fetch(
      'https://data.cityofnewyork.us/resource/7479-ugqb.json?$limit=99999999999999',
    )
    const data = await response.json()
    rawData.value = data
  } catch (error) {
    console.error('Error fetching data', error)
  } finally {
    loading.value = false
  }
}
const raceCounts = computed(() => {
  const counts = {}
  for (const item of rawData.value) {
    const race = item.race || 'Unknown'
    counts[race] = (counts[race] || 0) + 1
  }
  return Object.entries(counts).sort((a, b) => b[1] - a[1])
})
const chartData = computed(() => ({
  labels: raceCounts.value.map(([race]) => race),
  datasets: [
    {
      label: 'Inmate Count',
      data: raceCounts.value.map(([, count]) => count),
      backgroundColor: [
        '#4e79a7',
        '#f28e2b',
        '#e15759',
        '#76b7b2',
        '#59a14f',
        '#edc948',
        '#b07aa1',
        '#ff9da7',
        '#9c755f',
        '#bab0ac',
      ],
      borderRadius: 4,
    },
  ],
}))
const chartOptions = {
  responsive: true,
  plugins: {
    legend: { display: false },
    title: {
      display: true,
      text: 'NYC Inmate Population by Race',
      font: { size: 18 },
    },
    tooltip: {
      callbacks: {
        label: (ctx) => ` ${ctx.parsed.y.toLocaleString()} inmates`,
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      title: { display: true, text: 'Number of Inmates' },
      ticks: { callback: (val) => val.toLocaleString() },
    },
    x: { title: { display: true, text: 'Race' } },
  },
}
const AGE_ORDER = ['16-20', '21-25', '26-30', '31-35', '36-40', '41-50', '51-60', '60+', 'Unknown']
const ageCounts = computed(() => {
  const counts = {}
  for (const item of rawData.value) {
    const age = item.age_group || item.agegrp || item.age || 'Unknown'
    counts[age] = (counts[age] || 0) + 1
  }
  const entries = Object.entries(counts)
  entries.sort((a, b) => {
    const ai = AGE_ORDER.indexOf(a[0])
    const bi = AGE_ORDER.indexOf(b[0])
    if (ai !== -1 && bi !== -1) return ai - bi
    if (ai !== -1) return -1
    if (bi !== -1) return 1
    return a[0].localeCompare(b[0])
  })
  return entries
})
const ageChartData = computed(() => ({
  labels: ageCounts.value.map(([age]) => age),
  datasets: [
    {
      label: 'Inmate Count',
      data: ageCounts.value.map(([, count]) => count),
      backgroundColor: '#4e79a7',
      borderRadius: 4,
    },
  ],
}))
const ageChartOptions = {
  responsive: true,
  plugins: {
    legend: { display: false },
    title: {
      display: true,
      text: 'NYC Inmate Population by Age Group',
      font: { size: 18 },
    },
    tooltip: {
      callbacks: {
        label: (ctx) => ` ${ctx.parsed.y.toLocaleString()} inmates`,
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      title: { display: true, text: 'Number of Inmates' },
      ticks: { callback: (val) => val.toLocaleString() },
    },
    x: { title: { display: true, text: 'Age Group' } },
  },
}
onMounted(() => getdata())
</script>
