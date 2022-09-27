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
const classHandlers = {
  age: {
    dataCb: (data) => {
      data = data.map((item) => {
        return item.age
      }).sort((a, b) => b - a)
      const count = Math.floor(data.length / 100 * 25)
      data = data.slice(0, count)
      return data
    },
    conditionCb: (data, value) => {
      return data.find((a) => a === value) ? 'bold' : ''
    }
  }
}
</script>

<template lang="pug">
.container
  vTable(:data="data"
         :headers="headers"
         :filters="filters"
         :classHandlers="classHandlers")
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
