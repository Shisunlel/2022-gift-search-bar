<script setup>
import { ref, watch } from 'vue'
import { debounce } from 'debounce'
import Loading from './Loading.vue'

const searchTerm = ref('')
const isLoading = ref(false)
const products = ref([])

const findProducts = debounce(async term => {
  isLoading.value = true
  try {
    const data = await (await fetch(`https://dummyjson.com/products/search?q=${term}&limit=5`)).json()
    products.value = data.total && term.length ? data.products : [];
  } catch (error) {
    console.error(error);
  } finally {
    isLoading.value = false
  }
}, 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." :disabled="isLoading" />
    <Loading v-if="isLoading"></Loading>
    <ul v-else-if="!isLoading && products.length" class="list-disc">
      <li v-for="product in products" :key="product.id">
        {{ product.title }} - ${{ product.price }}
      </li>
    </ul>
    <p v-else>Not found</p>
  </div>
</template>
