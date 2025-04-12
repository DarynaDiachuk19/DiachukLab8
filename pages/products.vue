<template>
  <div class="container mx-auto p-4">
    <h1 class="text-3xl font-bold text-center mb-6 text-white">Список продуктів</h1>

    <input
        v-model="q"
        placeholder="Пошук за назвою..."
        class="mb-6 px-6 py-3 border border-gray-300 rounded-lg shadow-md w-full md:w-1/2 mx-auto"
    />

    <table class="min-w-full table-auto border-collapse bg-white rounded-2xl shadow-xl overflow-hidden">
      <thead class="bg-gray-500 text-white">
      <tr>
        <th v-for="column in columns" :key="column.id" class="px-6 py-3 text-left">
          <span>{{ column.label }}</span>
          <span v-if="column.sortable" @click="sortColumn(column)" class="cursor-pointer ml-2">
              {{ column.sorted === 'asc' ? '⭡' : column.sorted === 'desc' ? '⭣' : '⭡' }}
            </span>
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="product in filteredRows" :key="product.id" class="hover:bg-gray-100 transition-colors duration-200">
        <td v-for="column in columns" :key="column.id" class="border-t border-b px-6 py-4 text-sm text-gray-700">
            <span v-if="column.id === 'thumbnail'">
              <!-- Пусте поле для фото -->
            </span>
          <span v-else>{{ product[column.id] }}</span>
        </td>
      </tr>
      </tbody>
    </table>

    <div class="mt-6 flex justify-center space-x-2">
      <button
          v-for="page in totalPages"
          :key="page"
          @click="handlePageChange(page)"
          class="px-6 py-2 bg-gray-500 text-white rounded-lg hover:bg-gray-600 transition duration-300"
          :class="{ 'bg-gray-700': pagination.page === page }"
      >
        {{ page }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const q = ref('')
const pagination = ref({ page: 1, pageSize: 10 })
const products = ref([
  { id: 1 },
  { id: 2 }
])

const columns = [
  { id: 'title', label: 'Назва', sortable: true, sorted: null },
  { id: 'description', label: 'Опис', sortable: false, sorted: null },
  { id: 'price', label: 'Ціна', sortable: true, sorted: null },
  { id: 'rating', label: 'Оцінка', sortable: true, sorted: null },
  { id: 'brand', label: 'Бренд', sortable: false, sorted: null },
  { id: 'category', label: 'Категорія', sortable: false, sorted: null },
  { id: 'thumbnail', label: 'Фото', sortable: false, sorted: null }
]

const filteredRows = computed(() => {
  let rows = products.value

  if (q.value) {
    rows = rows.filter((row) =>
        row.title?.toLowerCase().includes(q.value.toLowerCase())
    )
  }

  return rows.slice((pagination.value.page - 1) * pagination.value.pageSize, pagination.value.page * pagination.value.pageSize)
})

const totalPages = computed(() => {
  return Math.max(1, Math.ceil(products.value.length / pagination.value.pageSize))
})

const handlePageChange = (page) => {
  pagination.value.page = page
}

const sortColumn = (column) => {
  if (column.sortable) {
    const sortedProducts = [...products.value]

    if (column.sorted === 'asc') {
      column.sorted = 'desc'
    } else {
      column.sorted = 'asc'
    }

    sortedProducts.sort((a, b) => {
      let aValue = a[column.id]
      let bValue = b[column.id]

      if (column.id === 'price' || column.id === 'rating') {
        aValue = Number(aValue)
        bValue = Number(bValue)
      }

      if (column.sorted === 'asc') {
        return aValue < bValue ? -1 : aValue > bValue ? 1 : 0
      } else {
        return aValue > bValue ? -1 : aValue < bValue ? 1 : 0
      }
    })

    products.value = sortedProducts
  }
}
</script>

<style scoped>
.bg-peach {
  background-color: #261b0f;
}

.bg-peach-light {
  background-color: #857761;
}

.text-white {
  color: white;
}

button:hover {
  background-color: #491806;
}
</style>
