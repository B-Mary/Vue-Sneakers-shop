<template>
  <div
    class="relative bg-white border border-slate-400 rounded-3xl p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition"
  >
    <img
      v-if="onClickFavorite"
      @click="onClickFavorite"
      :src="!isFavorites ? '/like-1.svg' : '/like-2.svg'"
      alt="Like 1"
      class="absolute top-8 left-8"
    />
    <img :src="imageUrl" alt="Sneaker" />

    <p class="mt-5">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400"> Ціна </span>
        <b> {{ price }} грн</b>
      </div>

      <img v-if="onClickAdd"  @click="onClickAdd" :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Plus" />
    </div>
  </div>
</template>

<script setup>

 defineProps({
  imageUrl: String,
  id: Number,
  title: String,
  price: Number,
  isFavorites: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function
})


</script>
