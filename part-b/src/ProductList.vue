<script setup>
import { ref, computed, onMounted } from 'vue'

// The product data loads from public/products.json.
// This gives you a working list to build on. The filtering logic is yours.
const products = ref([])

onMounted(async () => {
  const res = await fetch('/products.json')
  products.value = await res.json()
})

const search = ref('')
const category = ref('all')

// Unique categories from the data, e.g. ['personal', 'car', 'debt-consolidation'],
const categories = computed(() => {
  const allCategories = products.value.map((product) => product.category)
  return [...new Set(allCategories)]
})

// Turns "debt-consolidation" into "Debt consolidation" for display.
function categoryLabel(value) {
  const text = value.replaceAll('-', ' ')
  return text.charAt(0).toUpperCase() + text.slice(1)
}

// Products matching both the search term (case-insensitive) and the category.
const filteredProducts = computed(() => {
  const term = search.value.trim().toLowerCase()
  return products.value.filter((product) => {
    const matchesSearch = product.name.toLowerCase().includes(term)
    const matchesCategory = category.value === 'all' || product.category === category.value
    return matchesSearch && matchesCategory
  })
})

// e.g. "1 product" or "3 products"
const resultText = computed(() => {
  const count = filteredProducts.value.length
  return count === 1 ? '1 product' : `${count} products`
})

function clearFilters() {
  search.value = ''
  category.value = 'all'
}
</script>

<template>
  <section>
    <h2>Products</h2>

    <label for="product-search">Search by name</label>
    <input id="product-search" v-model="search" type="search" />

    <label for="product-category">Category</label>
    <select id="product-category" v-model="category">
      <option value="all">All categories</option>
      <option v-for="value in categories" :key="value" :value="value">
        {{ categoryLabel(value) }}
      </option>
    </select>

    <p aria-live="polite">{{ resultText }}</p>

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
