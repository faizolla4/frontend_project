<script setup>
  import {useRoute} from "vue-router";
  import axios from "axios";
  import {onMounted, ref} from "vue";

  const route = useRoute()
  const isLoaded = ref(false)
  const product = ref({})
  const fetchProduct = async () => {
      isLoaded.value = false
      const product_req = await axios.get(`http://localhost:8000/api/products/${route.params.id}`)
      product.value = product_req.data
      isLoaded.value = true
  }
  onMounted(async () => {
      await fetchProduct()
  })
</script>

<template>
    <div class="container-sm">
        <img :src="product.image" class="shadow-sm p-1 mb-1 bg-white rounded mx-auto d-block card-img-top product-image" :alt="product.title">
    </div>
    <div class=" sticky-sm-top" style="width: 17%"></div>
  <div>
      <h1 class="text-center font-family: 'Lato'">Product</h1>
      <div v-if="isLoaded">
          <p class="text-center font-family: 'Lato'">{{ product.title }}</p>
          <p class="text-center font-family: 'Lato'">{{ product.price }}</p>
          <p class="text-center font-family: 'Lato'">{{ product.description }}</p>
      </div>
      <div v-else>
          ...loading
      </div>
  </div>
</template>

<style scoped>
.product-image{
    height: 250px;
    width: 250px;
}
</style>

