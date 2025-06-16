<script setup>
import ProductCard from '@/components/ProductCard.vue'
import Pagination from '@/components/Pagination.vue'
import Loading from '@/components/Loading.vue'

// 1. Tambahkan import yang diperlukan dari vue dan vue-router
import { ref, watch, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import axios from 'axios'

// 2. Inisialisasi router dan route
const router = useRouter()
const route = useRoute()

const products = ref(null) // Ganti ke null agar lebih mudah diperiksa
const limit = ref(8)
const isLoading = ref(true)

// 3. Inisialisasi 'page' dari URL, dengan fallback ke halaman 1
const page = ref(parseInt(route.query.page) || 1)

// 4. Ubah 'fetchData' untuk menerima nomor halaman sebagai argumen
async function fetchData(pageToFetch) {
  // Pastikan URL menggunakan nomor halaman yang benar
  const API_URL = `http://localhost:3000/products?_page=${pageToFetch}&_per_page=${limit.value}`
  try {
    isLoading.value = true
    const response = await axios.get(API_URL)
    products.value = response.data
  } catch (error) {
    console.error(error)
    products.value = null // Atur ke null jika terjadi error
  } finally {
    isLoading.value = false
  }
}

// 5. Ganti 'watchEffect' dengan 'watch' yang lebih spesifik
//    Watcher ini akan berjalan setiap kali query '?page=' di URL berubah
watch(
  () => route.query.page,
  (newPage) => {
    const pageToFetch = parseInt(newPage) || 1
    page.value = pageToFetch
    fetchData(pageToFetch)
  }
)

// Ambil data awal saat komponen pertama kali dimuat
onMounted(() => {
  fetchData(page.value)
})

// 6. Modifikasi 'changePage' untuk melakukan navigasi router
function changePage(newPage) {
  // Batasan ini bagus, kita pertahankan
  if (newPage < 1) return
  if (products.value && newPage > products.value.pages) return

  // Perintah ini akan mengubah URL di browser, misal: 'http://localhost:5173/?page=2'
  router.push({ query: { page: newPage } })
}

</script>

<template>
  <div v-if="isLoading">
    <Loading />
  </div>
  <main v-else>
    <div class="product-grid">
      <ProductCard v-for="(product, index) in products.data" :key="index" :product="product" />
    </div>
    <div class="pagination">
      <Pagination :page="page" :totalPages="products.pages" @change-page="changePage" />
    </div>
  </main>
</template>

<style scoped>
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  width: 80%;
  margin: 0 auto;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}
</style>
