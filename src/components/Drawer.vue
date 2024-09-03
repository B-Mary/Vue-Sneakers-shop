<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>

  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <div v-if="!totalPrice || orderId"  class="flex h-gull items-center">
      <InfoBlock 
      v-if="!totalPrice && !orderId"
      title="Корзина порожня" 
      description="Добавьте хоча б одну пару, щоб зробити замовлення" 
      image-url="/package-icon.png" />

      <InfoBlock
      v-if="orderId" 
      title="Замовлення оформлено!" 
      :description="`Ваше замовлення #${orderId} буде передано до кур'єрської служби доставки`" 
      image-url="/order-success-icon.png" />

    </div>

    <div v-else>
      <CartListItem />

      <div  class="flex flex-col gap-4 mt-7">
        <div class="flex gap-2">
          <span> До оплати:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} ₴</b>
        </div>
        <div class="flex gap-2">
          <span> Налог 4%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} ₴</b>
        </div>

        <button
          :disabled="buttonDisabled"
          @click="createOrder"
          class="mt-4 disabled:bg-slate-300 cursor-pointer bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 transition action:bg-lime-700"
        >
          Оформити замовлення
        </button>
      </div>
    </div>
  </div>
</template>


<script setup>
import axios from 'axios';
import {  ref, inject, computed } from 'vue';


import DrawerHead from './DrawerHead.vue'
import CartListItem from './CartListItem.vue'
import InfoBlock from './InfoBlock.vue'

const props = defineProps({
  vatPrice: Number,
  totalPrice: Number,
})

const {cart, closeDrawer} = inject('cart')


const isCreating = ref(false)
const orderId = ref(null)

const createOrder = async () => {
  try{
    isCreating.value = true
    
    const { data } = await axios.post(`https://04a2043b614d18f0.mokky.dev/orders`, {
      itemsElem: cart.value,
      totalPrice: props.totalPrice.value
    })

    cart.value = [] 

    orderId.value = data.id

    return data
  } catch(err){
    console.log(err)
  } finally {
    isCreating.value = false
  }
}

const buttonDisabled = computed(
  () => isCreating.value || cartIsEmpty.value
)

const cartIsEmpty = computed(
  () => cart.value.length === 0
)


</script>
