<template lang="pug">
VContainer
  VRow
    VCol(cols="12")
      h1 購物網
    VDivider
    VCol(cols="12" md="6" lg="3" v-for="product in products" :key="product._id")
      ProductCard(v-bind="product")
</template>

<script setup>
import { ref, onMounted, nextTick } from 'vue'
import { useApi } from '@/composables/axios'
import { useSnackbar } from 'vuetify-use-dialog'
import ProductCard from '@/components/ProductCard.vue'
import gsap from 'gsap'

const { api } = useApi()
const createSnackbar = useSnackbar()

const products = ref([])

// 在onMounted 才能抓取 DOM 元素

onMounted(async () => {
  try {
    // 抓資料
    const { data } = await api.get('/products', {
      // 不做分頁
      params: {
        itemsPerPage: -1
      }
    })
    // ???
    products.value.push(...data.result.data)
    // 重新渲染
    await nextTick()
    // 動畫效果
    gsap
      .to('.product-card', { opacity: 1, duration: 0.5 })
  } catch (error) {
    const text = error?.response?.data?.message || '發生錯誤，請稍後再試'
    createSnackbar({
      text,
      showCloseButton: false,
      snackbarProps: {
        timeout: 2000,
        color: 'red',
        location: 'bottom'
      }
    })
  }
})

</script>

<!-- 動畫效果 -->
<style scoped lang="sass">
.product-card
  opacity: 0
</style>
