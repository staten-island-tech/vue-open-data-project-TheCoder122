<template>
  <div>
    <h1 v-for="i in 10" :key="i">{{ text }}</h1>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
const text = ref('')
async function getdata() {
    try{
        const response = await fetch('/api/hello')
        const data = await response.json()
        text.value = data.message
    }
}
onMounted(() => {
    getdata()
})
</script>

<style scoped></style>
