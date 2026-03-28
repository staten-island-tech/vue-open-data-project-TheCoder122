<template>
  <div :style="pageStyle">
    <h1 style="color: white; text-align: center; padding-top: 5vh">Deadly Sentencing</h1>
    <form @submit.prevent="handleSearch" style="text-align: center; margin: 20px">
      <input v-model="query" placeholder="Search by race (e.g. White)" style="padding: 8px; margin-right: 10px"/>
      <button type="submit">Search</button>
      <button type="button" @click="reset">Reset</button>
    </form>
    <p v-if="error" style="color: red; text-align: center">{{ error }}</p>
    <div v-if="loading" style="color: white; text-align: center">Loading data...</div>
    <div v-else style="display: flex; gap: 24px; max-width: 900px; margin: 0 auto">
      <div style="flex: 1">
        <Bar :options="chartOptions" :data="chartData"/>
      </div>
      <div style="flex: 1">
        <Doughnut :options="ageChartOptions" :data="ageChartData"/>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import { Bar, Doughnut } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale, ArcElement } from 'chart.js'
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale, ArcElement)
const router = useRouter()
const pageStyle = { minHeight: '100vh', backgroundImage: 'url(/court.png)', backgroundSize: 'cover' }
const rawData = ref([])
const loading = ref(true)
const query = ref('')
const error = ref('')
async function getdata() {
  try {
    const res = await fetch('https://data.cityofnewyork.us/resource/7479-ugqb.json')
    if (!res.ok) throw new Error('API failed')
    rawData.value = await res.json()
  } catch (err) {
    alert('Error fetching data')
    error.value = err.message
  } finally {
    loading.value = false
  }
}
onMounted(getdata)
function handleSearch() {
  if (!query.value.trim()) {
    error.value = 'Search cannot be empty'
    return
  }
  error.value = ''
  router.push('/search')
}
function reset() {
  query.value = ''
  router.push('/')
}
const filteredData = computed(() => {
  if (!query.value) return rawData.value
  return rawData.value.filter(item => (item.race || '').toLowerCase().includes(query.value.toLowerCase()))
})
const COLORS = ['#4e79a7','#f28e2b','#e15759','#76b7b2','#59a14f','#edc948','#b07aa1','#ff9da7','#9c755f','#bab0ac']
const raceCounts = computed(() => {
  const counts = {}
  for (const item of filteredData.value) {
    const race = item.race || 'Unknown'
    counts[race] = (counts[race] || 0) + 1
  }
  return Object.entries(counts).sort((a,b)=>b[1]-a[1])
})
const chartData = computed(() => ({
  labels: raceCounts.value.map(([r]) => r),
  datasets: [{ label:'Inmate Count', data: raceCounts.value.map(([,c])=>c), backgroundColor: COLORS, borderRadius: 4 }]
}))
const chartOptions = { responsive:true }
const ageCounts = computed(() => {
  const counts = {}
  for (const item of filteredData.value) {
    const age = item.age_group || item.agegrp || item.age || 'Unknown'
    counts[age] = (counts[age] || 0) + 1
  }
  return Object.entries(counts)
})
const ageChartData = computed(() => ({
  labels: ageCounts.value.map(([a]) => a),
  datasets: [{ label:'Inmate Count', data: ageCounts.value.map(([,c])=>c), backgroundColor: COLORS }]
}))
const ageChartOptions = { responsive:true }
</script>
