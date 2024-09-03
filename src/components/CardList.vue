<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :title="item.title"
      :price="item.price"
      :imageUrl="item.imageUrl"
      :onClickAdd="isFavorites ? null : () => emit('addToCart', item)"
      :isFavorites="item.isFavorites"
      :onClickFavorite=" isFavorites ? null : () => emit('addToFavorite', item)"
      :isAdded="item.isAdded"
    />
  </div>
</template>

<script setup>
import Card from './Card.vue'

defineProps({
  items: Array,
  isFavorites: Boolean,
})

//через емит обьявляем какие события мы получаем
const emit = defineEmits(['addToFavorite', 'addToCart'])

// const onClickAddFunction = () => {

  // '' -тут ключ, через , идут аргументы
//  emit('addToFavorite', item)
//}



</script>
