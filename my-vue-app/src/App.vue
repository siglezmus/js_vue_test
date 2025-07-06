<template>
  <div class="container my-4">
    <h1 class="mb-4 text-center">Products</h1>

    <div v-if="loading" class="text-center">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>

    <div v-else class="row">
      <div v-for="item in items" :key="item.id" class="col-md-4 mb-4">
        <div class="card h-100">
          <div class="card-body">
            <h5 class="card-title">{{ item.name }}</h5>
            <h6 class="card-subtitle mb-2 text-muted">ID: {{ item.id }}</h6>

            <ul class="list-group list-group-flush mt-3" v-if="item.data">
              <li class="list-group-item" v-if="item.data.year !== undefined">Year: {{ item.data.year }}</li>
              <li class="list-group-item" v-else>Year: N/A</li>

              <li class="list-group-item" v-if="item.data.price !== undefined">Price: ${{ item.data.price.toFixed(2) }}</li>
              <li class="list-group-item" v-else>Price: N/A</li>

              <li class="list-group-item" v-if="item.data['CPU model']">CPU model: {{ item.data['CPU model'] }}</li>
              <li class="list-group-item" v-else>CPU model: N/A</li>

              <li class="list-group-item" v-if="item.data['Hard disk size']">Hard disk size: {{ item.data['Hard disk size'] }}</li>
              <li class="list-group-item" v-else>Hard disk size: N/A</li>
            </ul>

            <p v-else class="card-text mt-3">No data available</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script lang="ts" setup>
import { ref, onMounted } from 'vue'

interface ProductData {
  year?: number
  price?: number
  'CPU model'?: string
  'Hard disk size'?: string
}

interface Product {
  id: string
  name: string
  data?: ProductData
}

const items = ref<Product[]>([])
const loading = ref(true)

onMounted(async () => {
  const ids = []
  for (let i = 2; i <= 10; i++) {
    ids.push(`id=${i}`)
  }

  const url = `https://api.restful-api.dev/objects?${ids.join('&')}`

  try {
    const res = await fetch(url)
    const data = await res.json()
    console.log('✅ Fetched data:', data)
    items.value = data
  } catch (error) {
    console.error('❌ Error fetching data:', error)
  } finally {
    loading.value = false
  }
})
</script>
