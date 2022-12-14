<script setup>
import { computed, ref } from 'vue'
const props = defineProps({
  data: Array,
  headers: Array,
  filters: Object,
  classHandlers: Object
})

const sortKey = ref('lastName')
const sortDir = ref(true)

const changeSortedField = (index) => {
  const key = Object.keys(props.data[0])[index]
  if (sortKey.value === key) {
    sortDir.value = !sortDir.value
  } else {
    sortDir.value = true
  }
  sortKey.value = key
}

const isNumber = (a) => typeof a === 'number'

const processedData = computed(() => {
  let data = [...props.data]
  if (props.filters) {
    const filters = props.filters
    data = data.filter((item) => {
      let isValid = false
      Object.keys(filters).forEach((key) => {
        if (filters[key].hasOwnProperty('min')) {
          isValid = item[key] > filters[key].min
        }
        if (filters[key].hasOwnProperty('max')) {
          isValid = item[key] < filters[key].max
        }
      })
      if (isValid) return item
    })
  }
  if (sortKey.value) {
    data.sort((a, b) => {
      const aVal = a[sortKey.value]
      const bVal = b[sortKey.value]
      if (sortDir.value) {
        if (isNumber(aVal) && isNumber(bVal)) return aVal - bVal
        return aVal > bVal ? 1 : -1
      } else {
        if (isNumber(aVal) && isNumber(bVal)) return bVal - aVal
        return aVal > bVal ? -1 : 1
      }
      return 0
    })
  }
  return data
})

const classHandlersData = ref({})
const startClassHandlers = () => {
  if (props.classHandlers) {
    for (let key in props.classHandlers) {
      if (props.classHandlers[key] && props.classHandlers[key].dataCb) {
        classHandlersData.value[key] = props.classHandlers[key].dataCb([...processedData.value])
      }
    }
  }
}
startClassHandlers()

const getClass = (key, value) => {
  if (classHandlersData.value[key] &&
    props.classHandlers[key] &&
    props.classHandlers[key].conditionCb) {
    return props.classHandlers[key].conditionCb(classHandlersData.value[key], value)
  }
  return false
}
</script>

<template lang="pug">
table.data-table
  thead
    th(v-for="(item, index) in headers"
      :key="item"
      @click="changeSortedField(index)") {{ item }}
  tbody
    tr(v-for="(tr, trIndex) in processedData" :key="tr.id")
      td(v-for="td in Object.keys(tr)"
         :class="getClass(td, tr[td])") {{ tr[td] }}
</template>

<style lang="css" scoped>
.data-table {
  width: 100%;
}

.data-table th {
  cursor: pointer;
}

.data-table th, td {
  text-align: left;
}

.bold {
  font-weight: bold;
}
</style>
