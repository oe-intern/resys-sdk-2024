<template lang="pug">
  li.product
    img.product__image(:src="product.image" alt="product image")
    .information
      Text(as="h2", variant="headingMd") {{ product.name }}
      TextBox(as="p", variant="body") {{ product.price }}
      <input type="text" v-model="product.numBuy" @input="updatePayment" />
    .product-payment
      Text(as="h2", variant="headingMd") {{ productPayment }}
    button(@click="notifyParent") Click me
</template>

<script setup lang="ts">
import { defineProps, ref, inject, Ref } from 'vue'
const productPayment = ref(0)
interface Product {
  name: string
  price: number
  image: string
  numBuy: number
}
const subtotal = inject<Ref<number>>('subtotal') ?? ref(0)
const tax = inject<Ref<number>>('tax') ?? ref(0)
const total = inject<Ref<number>>('total') ?? ref(0)
const shipping = inject<Ref<number>>('shipping') ?? ref(0)

const props = defineProps<{ product: Product }>()
const emit = defineEmits<{
  (event: 'child-action', message: string): void
}>()

// Define a function to emit the event
function notifyParent() {
  alert('You clicked me')
  emit('child-action', 'Hello from child')
}

function updatePayment() {
  const TEMP = productPayment.value
  productPayment.value = props.product.price * props.product.numBuy
  //FIXME - fix this
  if (!isNaN(productPayment.value)) {
    subtotal.value += productPayment.value - TEMP
    tax.value = subtotal.value * 0.1
    total.value = subtotal.value + shipping.value + tax?.value
  }
}

updatePayment()
</script>

<style scoped>
.product {
  width: 100%;
  /* border-radius: 24px; */
  /* background-color: #FAFAF5; */
  background-color: #3d3d15;
  display: flex;
  gap: 20px;
  overflow: hidden;
  flex-wrap: wrap;
  justify-content: space-between;
  font:
    600 20px/1.3 Inter,
    sans-serif;
  /* border: 2px solid #e6e6e6; */
  margin-bottom: 331px;
  &__image {
    height: 29px;
  }
}

.information {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.product-view {
  display: flex;
  /* gap: 24px; */
}
.product-payment {
  width: 40px;
  /* gap: 24px; */
}
</style>
