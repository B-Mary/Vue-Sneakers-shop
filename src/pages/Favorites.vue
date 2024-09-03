<template>
    <h2 class="text-3xl font-bold mb-8">Закладки</h2>

    <div class="mt-10">
        <CardList :items="favorites" is-favorites/>
    </div>
</template>

<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';

import CardList from '../components/CardList.vue'


const favorites = ref([])

onMounted(async () => {
    try {

        const { data } = await axios.get(`https://04a2043b614d18f0.mokky.dev/favorites?_relations=items`)

        favorites.value = data.map((obj) => obj.item)

    } catch (err){
        console.log(err)
    }
})
</script>

