<template>
  <div>
    <h2 v-for="item in text" :key="item.cmplnt_num">
      Complaint: {{ item.complaint_type }} <br />
      Agency: {{ item.agency }} <br />
      Descriptor: {{ item.descriptor }}
    </h2>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
const text = ref([])
async function getdata() {
  try {
    const response = await fetch('https://data.cityofnewyork.us/resource/7479-ugqb.json?$limit=10')
    const data = await response.json()
    text.value = data
    console.log('Fetched data:', data)
  } catch (error) {
    console.error('Error fetching data', error)
  }
}
onMounted(() => getdata())
</script>
