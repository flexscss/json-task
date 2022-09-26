<script setup>
import people from '@/data/people.js'
import { ref } from 'vue'
import vTable from '@/components/table.vue'

const data = ref(people)
const filters = {
  age: {
    min: 18
  }
}
const headers = ['ID', 'First name', 'Last name', 'Age']
</script>

<template lang="pug">
.container
  vTable(:data="data"
         :headers="headers"
         :filters="filters")
</template>

<style>
body {
  font-family: 'Roboto', sans-serif;
}
.container {
  max-width: 900px;
  margin: 0 auto;
}
</style>
