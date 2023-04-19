<script setup>
import {onMounted, ref, watch} from 'vue'
import {useRoute} from 'vue-router'
import axios from 'axios'

const isLoaded = ref(false)
const products = ref([])
const route = useRoute()
const pages = ref(0)

const fetchProducts = async () => {
    isLoaded.value = false
    const product_req = await axios.get('http://localhost:8000/api/products', {
        params: {
            ...route.query,
            category: route.query.category || 1
        }
    })
    products.value = product_req.data.results
    pages.value = product_req.data.count % 10 === 0
        ? Math.floor(product_req.data.count / 10)
        : Math.floor(product_req.data.count / 10) + 1
    isLoaded.value = true
}

watch(route, async () => {
    isLoaded.value = false
    products.value = []
    await fetchProducts()
})

onMounted(async () => {
    await fetchProducts()
})
</script>

<template>
    <h1>Products</h1>
    <div class="container">
        <div class="dropdown d-flex justify-content-end my-2">
            <a
                    class="btn btn-secondary dropdown-toggle"
                    href="#"
                    role="button"
                    data-bs-toggle="dropdown"
                    aria-expanded="false"
            >
                Sort by
            </a>

            <ul class="dropdown-menu">
                <li>
                    <RouterLink :to="{name: 'products', query: { ...route.query, ordering: 'price' }}" class="dropdown-item">
                        Price asc
                    </RouterLink>
                </li>
                <li>
                    <RouterLink :to="{name: 'products', query: { ...route.query, ordering: '-price' }}"  class="dropdown-item">
                        Price desc
                    </RouterLink>
                </li>
            </ul>
        </div>
        <div v-if="isLoaded" class="row row-cols-4 g-2">
            <div
                    class="card card-hover py-5 text-center container"
                    style="width: 17%"
                    v-for="product in products"
                    :key="product.id"
            >
                <RouterLink :to="'products/' + product.id">
                    <img :src="product.image" class="card-img-top" :alt="product.title"/>
                    <div class="card-body">
                        <h5 class="card-title">{{ product.title }}</h5>
                        <p class="card-text">{{ product.price }}</p>
                    </div>
                </RouterLink>
            </div>
            <nav class="py-4" aria-label="Page navigation example">
                <ul class="pagination">
                    <li class="page-item"><a class="page-link" href="#">Previous</a></li>
                    <li class="page-item" v-for="i in pages" :key="i">
                        <RouterLink :to="{name: 'products', query: { ...route.query, page: i }}" class="page-link">{{i}}</RouterLink>
                    </li>
                    <li class="page-item"><a class="page-link" href="#">Next</a></li>
                </ul>
            </nav>
        </div>
        <div v-else>
            <p>Loading...</p>
        </div>
    </div>

</template>

<style scoped>
.card-hover:hover {
    box-shadow: 0 0 11px rgba(33, 33, 33, 0.2);
}
</style>
