<template>
  <div v-if="isLoading">
    <loading />
  </div>
  <div class="product-detail" v-else>
    <h2>{{ product.title }}</h2>
    <img :src="product.image" :alt="product.title" class="product-image" />
    <p>{{ product.description }}</p>
    <p>${{ product.price }}</p>
    <!-- <ProductForm :product="product" @update-product="updateProduct" /> -->
    <button @click="goBack" class="back-button">Back</button>
    <button @click="deleteProduct" class="delete-button">Delete</button>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import axios from 'axios'
// import ProductForm from '@/components/ProductForm.vue'
import Loading from '@/components/Loading.vue'

const route = useRoute()
const router = useRouter()

const isLoading = ref(true)
const id = route.params.id
const product = ref({})
const API_URL = `http://localhost:3000/products/${id}`

onMounted(() => {
  fetchData()
})

function goBack() {
  router.back()
}

async function fetchData() {
  try {
    isLoading.value = true
    const response = await axios.get(API_URL)
    product.value = response.data
  } catch (error) {
    console.error(error)
  } finally {
    isLoading.value = false
  }
}

async function deleteProduct() {
    try {
      isLoading.value = true
      await axios.delete(API_URL)
      router.push('/')
    } catch (error) {
      console.error(error)
    } finally {
      isLoading.value = false
    }
  }
</script>

<style scoped>
.product-detail {
  margin-top: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
  text-align: center;
}

.product-image {
  max-width: 100%;
  height: auto;
  margin-bottom: 10px;
}

.product-detail p {
  margin-bottom: 5px;
}

.product-detail button {
  margin-top: 10px;
  padding: 10px 20px;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.back-button {
  display: inline-block;
  padding: 8px 16px;
  background-color: #007bff;
  color: #fff;
  text-decoration: none;
  border-radius: 4px;
  transition: background-color 0.3s;
}

.back-button:hover {
  background-color: #0056b3;
}

.delete-button {
  display: inline-block;
  padding: 8px 16px;
  background-color: #ff0000;
  color: #fff;
  text-decoration: none;
  border-radius: 4px;
  transition: background-color 0.3s;
  margin-left: 10px;
}

.delete-button:hover {
  background-color: #b30000;
}
</style>
