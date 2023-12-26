<template>
    <RouterLink
        v-if="pokemonInfos.types"
        class="link"
        :to="{ name: 'pokemon', params: { id: pokemonInfos.id, name: pokemonInfos.name } }"
    >
        <div class="card" :class="`pokemon-type-${pokemonInfos.types[0].name}`">
            <div class="infos">
                <span class="name">{{ pokemonInfos.name }}</span>
                <span class="badge" v-for="(type, index) in pokemonInfos.types" :key="index">
                    {{
                        type.name.replace(/(^\w{1})|(\s+\w{1})/g, (letter) => letter.toUpperCase())
                    }}
                </span>
            </div>
            <img :src="pokemonInfos.image" :alt="pokemonInfos.name" />
        </div>
    </RouterLink>
</template>

<style scoped>
.link {
    display: block;
    width: calc(50% - 0.5rem);
}
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
import { onMounted, ref } from 'vue'
import { RouterLink } from 'vue-router'
import PokeApi from '../services/PokeApi.js'

const props = defineProps(['pokemon'])
let pokemonInfos = ref({})

onMounted(() => {
    PokeApi.get(`pokemon/${props.pokemon.name}`).then((response) => {
        const { data } = response

        pokemonInfos.value = {
            id: data.id,
            name: data.name
                .replace(/-/g, ' ')
                .replace(/(^\w{1})|(\s+\w{1})/g, (letter) => letter.toUpperCase()),
            types: (() => {
                const types = []
                data.types.forEach((item) => types.push(item.type))

                return types
            })(),
            image: data.sprites.other['official-artwork'].front_default
        }
    })
})
</script>
