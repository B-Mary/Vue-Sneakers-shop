<template>
    <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Усі кросівки</h2>

        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По найменуванню</option>
            <option value="price">По ціні(дешеві)</option>
            <option value="-price">По ціні( дорогі)</option>
          </select>

          <div class="relative">
            <img class="absolute left-4 top-3" src="/search.svg" alt="Search" />
            <input
              @input="onChangeSearchInput"
              class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
              placeholder="Пошук по ..."
            />
          </div>
        </div>
      </div>

     <div class="mt-10">
        <CardList :items="itemsElem" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus"/>
     </div>
</template>

<script setup>
import { ref, reactive, watch, onMounted, inject } from 'vue';
import axios from 'axios';
import debounce from 'lodash.debounce';

import CardList from '../components/CardList.vue'

const {cart, addToCart, removeFromCart} = inject('cart')


const itemsElem = ref([])


const filters = reactive({
sortBy: 'title',
searchQuery: ''
})

const onChangeSelect = (event) =>{
  filters.sortBy = event.target.value
}

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value
}, 300)

const addToFavorite = async (item) => {
  try{

    if(!item.isFavorites){
      const obj ={
        item_id: item.id,
      }

      item.isFavorites = true

      const { data }  = await axios.post(`https://04a2043b614d18f0.mokky.dev/favorites`, obj)
      
      item.favoriteId = data.id
    } else{
      item.isFavorites = false  
      await axios.delete(`https://04a2043b614d18f0.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
    
  } catch(err){
    console.log(err)
  }
}

const fetchItem = async () => {
  try {

    const params = {
      sortBy: filters.sortBy
    };

    if (filters.searchQuery){
      params.title = `*${filters.searchQuery}*`
    }

    const { data }  = await axios.get(
      `https://04a2043b614d18f0.mokky.dev/items`,
      {
        params
      }
    )

    itemsElem.value = data.map((obj) => ({
      ...obj,
      isFavorites: false,
      isAdded: false,
      favoriteId: null,
    }))

  } catch (error){

    console.log(error)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites }  = await axios.get(`https://04a2043b614d18f0.mokky.dev/favorites`)

    itemsElem.value = itemsElem.value.map(item => {

      // мы получаем масив item, потом получаем масив favorites, потом пробегаем по items и сравнимваем есть ли у него флаг favorites,  

       const favorite = favorites.find(favorite => favorite.item_id === item.id)

       console.log(favorite)

       //если флага favorites нет - возвращаем просто елемент
       if(!favorite){
        return item
       }

      // уесли есть такой, то мы возвр сам item, его свойства (флаг favorite =true)
      return {
        ...item,
        isFavorites: true,
        favoriteId: favorite.id
      }
    })
  } catch (error){
    console.log(error)
  }
}

const onClickAddPlus = async (item) => {
  if (!item.isAdded){
    addToCart(item)
  } else{
    removeFromCart(item)
  }
}

onMounted(async ()=> {
  const localCart = localStorage.getItem("cart")
  cart.value = localCart ? JSON.parse(localCart) : []

 await fetchItem();
 await fetchFavorites();

 itemsElem.value = itemsElem.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))

})



watch(cart, () => {
  itemsElem.value = itemsElem.value.map((item) => ({
    ...item,
    isAdded: false
  }))

})

watch(filters, fetchItem)




</script>