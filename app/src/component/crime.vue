<template>
  <div style="display: flex; gap: 24px">
    <Bar :data="chartData" :options="chartOptions" />
    <Doughnut :data="ageChartData" :options="ageChartOptions" />
  </div>
</template>
<script setup>
import { computed } from 'vue'
import { Bar, Doughnut } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
  ArcElement,
} from 'chart.js'
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale, ArcElement)
const props = defineProps({
  rawData: {
    type: Array,
    required: true,
  },
})
const COLORS = ['#4e79a7', '#f28e2b', '#e15759']
const raceCounts = computed(() => {
  const counts = {}
  for (const item of props.rawData) {
    const race = item.race || 'Unknown'
    counts[race] = (counts[race] || 0) + 1
  }
  return Object.entries(counts)
})
const chartData = computed(() => ({
  labels: raceCounts.value.map(([r]) => r),
  datasets: [
    {
      data: raceCounts.value.map(([, c]) => c),
      backgroundColor: COLORS,
    },
  ],
}))
const chartOptions = { responsive: true }
const ageCounts = computed(() => {
  const counts = {}
  for (const item of props.rawData) {
    const age = item.age_group || 'Unknown'
    counts[age] = (counts[age] || 0) + 1
  }
  return Object.entries(counts)
})
const ageChartData = computed(() => ({
  labels: ageCounts.value.map(([a]) => a),
  datasets: [
    {
      data: ageCounts.value.map(([, c]) => c),
      backgroundColor: COLORS,
    },
  ],
}))
const ageChartOptions = { responsive: true }
</script>
