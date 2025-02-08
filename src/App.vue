<script setup lang="ts">
import AppHeader from './components/AppHeader.vue'
import AppFooter from './components/AppFooter.vue'
import AppShop from './components/Shop/AppShop.vue'
import AppCart from './components/Cart/AppCart.vue'
import data from './data/product'
import { computed, ref } from 'vue'
import type {
  FiltersInterface,
  ProductCartInterface,
  ProductInterface,
  FilterUpdate,
} from './interfaces'
import { DEFAULT_FILTERS } from './data/filters'

// Utilisation de ref au lieu de reactive
const products = ref<ProductInterface[]>(data)
const cart = ref<ProductCartInterface[]>([])
const filters = ref<FiltersInterface>({ ...DEFAULT_FILTERS })

function addProductToCart(productId: number): void {
  const product = products.value.find((product) => product.id === productId)
  if (product) {
    const productInCart = cart.value.find((product) => product.id === productId)
    if (productInCart) {
      productInCart.quantity++
    } else {
      cart.value.push({ ...product, quantity: 1 })
    }
  }
}

function removeProductFromCart(productId: number): void {
  const productFromCart = cart.value.find((product) => product.id === productId)!
  if (productFromCart?.quantity === 1) {
    cart.value = cart.value.filter((product) => product.id !== productId)
  } else {
    productFromCart.quantity--
  }
}

function updateFilter(filterUpdate: FilterUpdate) {
  if (filterUpdate.search !== undefined) {
    filters.value.search = filterUpdate.search
  } else if (filterUpdate.priceRange) {
    filters.value.priceRange = filterUpdate.priceRange
  } else if (filterUpdate.category) {
    filters.value.category = filterUpdate.category
  } else {
    filters.value = { ...DEFAULT_FILTERS }
  }
}

const cartEmpty = computed(() => cart.value.length === 0)

const filteredProducts = computed(() => {
  return products.value.filter((product) => {
    if (
      product.title.toLocaleLowerCase().startsWith(filters.value.search.toLocaleLowerCase()) &&
      product.price >= filters.value.priceRange[0] &&
      product.price <= filters.value.priceRange[1] &&
      (product.category === filters.value.category || filters.value.category === 'all')
    ) {
      return true
    } else {
      return false
    }
  })
})
</script>

<template>
  <div
    class="app-container"
    :class="{
      gridEmpty: cartEmpty,
    }"
  >
    <AppHeader class="header" />
    <AppShop
      @update-filter="updateFilter"
      :products="filteredProducts"
      :filters="filters"
      @add-product-to-cart="addProductToCart"
      class="shop"
    />
    <AppCart
      v-if="!cartEmpty"
      :cart="cart"
      class="cart"
      @remove-product-from-cart="removeProductFromCart"
    />
    <AppFooter class="footer" />
  </div>
</template>

<style lang="scss">
@use './assets/scss/base.scss';
@use './assets/scss/debug.scss';

.app-container {
  min-height: 100vh;
  display: grid;
  grid-template-areas: 'header header' 'shop cart' 'footer footer';
  grid-template-columns: 75% 25%;
  grid-template-rows: 48px auto 48px;
}

.gridEmpty {
  grid-template-areas: 'header' 'shop' 'footer';
  grid-template-columns: 100%;
}

.header {
  grid-area: header;
}

.shop {
  grid-area: shop;
}

.cart {
  grid-area: cart;
  border-left: var(--border);
  background-color: white;
}

.footer {
  grid-area: footer;
}
</style>
