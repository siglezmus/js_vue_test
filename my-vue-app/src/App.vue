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
          <div class="card-body position-relative">
            <h5 class="card-title">{{ item.name }}</h5>
            <h6 class="card-subtitle mb-2 text-muted">ID: {{ item.id }}</h6>

            <!-- Dropdown menu -->
            <div class="dropdown position-absolute top-0 end-0 m-2">
              <button
                class="btn btn-sm btn-light"
                type="button"
                data-bs-toggle="dropdown"
                aria-expanded="false"
              >
                ⋮
              </button>
              <ul class="dropdown-menu">
                <li>
                  <a class="dropdown-item" href="#" @click.prevent="downloadItem(item)">Download</a>
                </li>
                <li>
                  <a class="dropdown-item" href="#" @click.prevent="deleteItem(item)">Delete</a>
                </li>
                <li>
                  <a class="dropdown-item" href="#" @click.prevent="openRenameModal(item)">Rename</a>
                </li>
              </ul>
            </div>

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

    <!-- Rename modal -->
    <div class="modal fade" id="renameModal" tabindex="-1" aria-labelledby="renameModalLabel" aria-hidden="true" ref="renameModalRef">
      <div class="modal-dialog">
        <div class="modal-content">
          <form @submit.prevent="confirmRename">
            <div class="modal-header">
              <h5 class="modal-title" id="renameModalLabel">Rename Item</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <input v-model="newName" type="text" class="form-control" placeholder="Enter new name" required />
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
              <button type="submit" class="btn btn-primary">Rename</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as bootstrap from 'bootstrap'

interface ProductData {
  year: number
  price: number
  'CPU model': string
  'Hard disk size': string
}

interface Product {
  id: string
  name: string
  data?: ProductData
}

const items = ref<Product[]>([])
const loading = ref(true)

const selectedItem = ref<Product | null>(null)
const newName = ref('')
const renameModalRef = ref<HTMLDivElement | null>(null)

let renameModalInstance: any

onMounted(async () => {
  const ids = []
  for (let i = 2; i <= 10; i++) {
    ids.push(`id=${i}`)
  }

  const url = `https://api.restful-api.dev/objects?${ids.join('&')}`

  try {
    const res = await fetch(url)
    const data = await res.json()
    items.value = data
  } catch (error) {
    console.error('Error fetching data:', error)
  } finally {
    loading.value = false
  }
})

function downloadItem(item: Product) {
  console.log(`Download item with id: ${item.id}`)
  // Add download logic here
}

function deleteItem(item: Product) {
  console.log(`Delete item with id: ${item.id}`)
  // Add delete logic here (API call)
}

function openRenameModal(item: Product) {
  selectedItem.value = item
  newName.value = item.name

  const modalEl = renameModalRef.value
  if (modalEl) {
    renameModalInstance = new bootstrap.Modal(modalEl)
    renameModalInstance.show()
  }
}

function confirmRename() {
  if (selectedItem.value && newName.value) {
    console.log(`Rename item ${selectedItem.value.id} to ${newName.value}`)
    selectedItem.value.name = newName.value

    // Example: API call
    // await fetch(`https://api.restful-api.dev/objects/${selectedItem.value.id}`, {
    //   method: 'PATCH',
    //   headers: { 'Content-Type': 'application/json' },
    //   body: JSON.stringify({ name: newName.value })
    // })

    if (renameModalInstance) {
      renameModalInstance.hide()
    }
  }
}
</script>
