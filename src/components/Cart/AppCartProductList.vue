<script setup lang="ts">
import AppCartProduct from './AppCartProduct.vue'
import type { ProductCartInterface } from '@/interfaces'

const props = defineProps<{
  cart: ProductCartInterface[]
}>()

const emit = defineEmits<{
  (e: 'removeProductFromCart', productId: number): void
}>()
</script>

<template>
  <div class="d-flex flex-column">
    <AppCartProduct
      v-for="product of props.cart"
      :product="product"
      @remove-product-from-cart="emit('removeProductFromCart', $event)"
      :key="product.id"
    />
  </div>
</template>

<style lang="scss" scoped></style>
