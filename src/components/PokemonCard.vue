<template>
    <div
        v-if="pokemonInfos.types"
        class="card"
        :class="`pokemon-type-${pokemonInfos.types[0].name}`"
    >
        <div class="infos">
            <span class="name">{{ pokemonInfos.name }}</span>
            <span class="badge" v-for="(type, index) in pokemonInfos.types" :key="index">
                {{ type.name.replace(/(^\w{1})|(\s+\w{1})/g, (letter) => letter.toUpperCase()) }}
            </span>
        </div>
        <img :src="pokemonInfos.image" :alt="pokemonInfos.name" />
    </div>
</template>

<style scoped>
.card {
    padding: 1rem;
    background-color: #f3f3f3;
    box-shadow: 0 0 0.75rem 0 rgba(0, 0, 0, 0.2);
    border-radius: 1rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    color: #fff;
    transition: 0.2s;
}

.card:hover {
    transform: scale(1.05);
}

.card img {
    width: 100px;
}

.card .infos {
    display: flex;
    gap: 0.5rem;
    flex-direction: column;
}

.card .name {
    font-weight: bold;
}
</style>

<script setup>
import { defineProps, onMounted, ref } from 'vue'
import PokeApi from '../services/PokeApi.js'

const props = defineProps(['pokemon'])
let pokemonInfos = ref({})

onMounted(() => {
    PokeApi.get(`pokemon/${props.pokemon.name}`).then((response) => {
        const { data } = response

        pokemonInfos.value = {
            name: data.name.replace(/(^\w{1})|(\s+\w{1})/g, (letter) => letter.toUpperCase()),
            types: (() => {
                const types = []
                data.types.forEach((item) => types.push(item.type))

                return types
            })(),
            image: data.sprites.other.home.front_default
        }
    })
})
</script>
