<script setup>
import { ref, computed, onMounted } from 'vue'

// The product data loads from public/products.json.
// This gives you a working list to build on. The filtering logic is yours.
const products = ref([])

onMounted(async () => {
  const response = await fetch('/products.json')
  products.value = await response.json()
})

const searchTerm = ref('')
const selectedCategory = ref('all')

// Unique categories from the data, e.g. ['personal', 'car', 'debt-consolidation'],
const categories = computed(() => {
  const allCategories = products.value.map((product) => product.category)
  return [...new Set(allCategories)]
})

// Turns "debt-consolidation" into "Debt consolidation" for display.
function categoryLabel(category) {
  const text = category.replaceAll('-', ' ')
  return text.charAt(0).toUpperCase() + text.slice(1)
}

// Products matching both the search term (case-insensitive) and the category.
const filteredProducts = computed(() => {
  const term = searchTerm.value.trim().toLowerCase()
  const category = selectedCategory.value

  return products.value.filter((product) => {
    const matchesSearch = product.name.toLowerCase().includes(term)
    const matchesCategory = category === 'all' || product.category === category
    return matchesSearch && matchesCategory
  })
})

// e.g. "1 product" or "3 products"
const resultCountText = computed(() => {
  const count = filteredProducts.value.length
  return count === 1 ? '1 product' : `${count} products`
})

function clearFilters() {
  searchTerm.value = ''
  selectedCategory.value = 'all'
}
</script>

<template>
  <section>
    <h2>Products</h2>

    <label for="product-search">Search by name</label>
    <input id="product-search" v-model="searchTerm" type="search" />

    <label for="product-category">Category</label>
    <select id="product-category" v-model="selectedCategory">
      <option value="all">All categories</option>
      <option v-for="category in categories" :key="category" :value="category">
        {{ categoryLabel(category) }}
      </option>
    </select>

    <p aria-live="polite">{{ resultCountText }}</p>

    <ul v-if="filteredProducts.length > 0">
      <li v-for="product in filteredProducts" :key="product.name">
        {{ product.name }} — {{ product.lender }} — {{ product.rate.toFixed(2) }}%
      </li>
    </ul>
    <div v-else>
      <p>No products match your search.</p>
      <button type="button" @click="clearFilters">Clear filters</button>
    </div>
  </section>
</template>

<style scoped>
section {
  max-width: 640px;
  margin: 0 auto;
}
</style>
