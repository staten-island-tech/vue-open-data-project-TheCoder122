<script setup>
import { ref, onMounted } from 'vue'
import Crime from '@/components/crime.vue'
const rawData = ref([])
const loading = ref(true)
async function getdata() {
  try {
    const res = await fetch('https://data.cityofnewyork.us/resource/7479-ugqb.json')
    if (!res.ok) throw new Error('API failed')
    rawData.value = await res.json()
  } catch (err) {
    alert('Error fetching data')
  } finally {
    loading.value = false
  }
}
onMounted(getdata)
</script>
<template>
  <div>
    <h1>Deadly Sentencing</h1>
    <div v-if="loading">Loading...</div>
    <Crime v-else :rawData="rawData" />
  </div>
</template>
