<template>
  <div>
    <div><h1>Deadly Sentencing</h1></div>
    <h2 v-for="item in text" :key="item.inmate_id">
      inmateid: {{ item.inmateid }} <br />
      Custody_level: {{ item.custody_level }} <br />
      race: {{ item.race }} <br />
      age: {{ item.age }} <br />
      top_charge: {{ item.top_charge }} <br />
    </h2>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
const text = ref([])
async function getdata() {
  try {
    const response = await fetch(
      'https://data.cityofnewyork.us/resource/7479-ugqb.json?$limit=99999999999999',
    )
    const data = await response.json()
    text.value = data
    console.log('Fetched data:', data)
  } catch (error) {
    console.error('Error fetching data', error)
  }
}
onMounted(() => getdata())
</script>
