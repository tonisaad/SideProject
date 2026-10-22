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

// Products whose name contains the search term (case-insensitive).
const filteredProducts = computed(() => {
  const term = search.value.trim().toLowerCase()
  return products.value.filter((product) =>
    product.name.toLowerCase().includes(term)
  )
})

// TODO: add a reactive category filter (for example: all, personal, car, debt-consolidation).
// TODO: create a computed list that applies both the search term and the category filter.
// TODO: format each rate to 2 decimal places.
// TODO: show how many products match (a result count).
// TODO: show a sensible empty state when nothing matches.
</script>

<template>
  <section>
    <h2>Products</h2>

    <label for="product-search">Search by name</label>
    <input id="product-search" v-model="search" type="search" />

    <!-- TODO: category filter -->

    <!-- TODO: show name, lender, and rate (to 2 decimal places). -->
    <!-- TODO: add a result count and an empty state. -->
    <ul>
      <li v-for="product in filteredProducts" :key="product.name">
        {{ product.name }} — {{ product.lender }} — {{ product.rate }}%
      </li>
    </ul>
  </section>
</template>

<style scoped>
section {
  max-width: 640px;
  margin: 0 auto;
}
</style>
