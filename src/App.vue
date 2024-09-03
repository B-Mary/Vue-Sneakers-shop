<template>
  <Drawer 
    v-if="drawerOpen" 
    :total-price="totalPrice" 
    :vat-price="vatPrice" 

  />

  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>

<script setup>
import {  ref, watch, provide, computed } from 'vue';
import axios from 'axios';

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

const drawerOpen = ref(false)
const cart = ref([])

const openDrawer = () => {
  drawerOpen.value = true
}

const closeDrawer = () => {
  drawerOpen.value = false
}

const addToCart = async (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = async (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

const totalPrice = computed(
  () => cart.value.reduce((acc, item) => acc + item.price, 0)
)

const vatPrice = computed(
  () => Math.round((totalPrice.value * 4) / 100)
)



watch(cart, 
  () =>{
    localStorage.setItem('cart', JSON.stringify(cart.value))
  }, 
  {deep: true}
)

provide('cart', {
  cart,
  openDrawer,
  closeDrawer,
  addToCart,
  removeFromCart
})

</script>
