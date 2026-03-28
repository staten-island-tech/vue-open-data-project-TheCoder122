<template>
  <div :style="pageStyle">
    <h1 style="color: white; text-align: center; padding-top: 5vh">
      Deadly Sentencing
    </h1>
    <form @submit.prevent="handleSearch" style="text-align: center; margin: 20px">
      <input v-model="query" placeholder="Search by race" style="padding: 8px; margin-right: 10px"/>
      <button type="submit">Search</button>
      <button type="button" @click="reset">Reset</button>
    </form>
    <p v-if="error" style="color: red; text-align: center">{{ error }}</p>
    <div v-if="loading" style="color: white; text-align: center">Loading data...</div>
    <Crime v-else :rawData="filteredData" />
  </div>
</template>
<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import crime from '@/components/crime.vue'
const router = useRouter()
const pageStyle = {
  minHeight: '100vh',
  backgroundImage: 'url(/court.png)',
  backgroundSize: 'cover',
}
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
  return rawData.value.filter(item =>
    (item.race || '').toLowerCase().includes(query.value.toLowerCase())
  )
})
</script>
