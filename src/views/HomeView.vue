<template>
    <div class="container">
        <h1>Pokedex</h1>
        <div class="pokemons">
            <RouterLink
                class="link"
                v-for="(pokemon, index) in pokemons"
                :to="pokemon.name"
                :key="index"
            >
                <PokemonCard :pokemon="pokemon"></PokemonCard>
            </RouterLink>
        </div>
        <div class="pagination">
            <a v-if="page > 1" :href="`/?page=${page - 1}`">Página anterior</a>
            <a :href="`/?page=${page + 1}`">Próxima página</a>
        </div>
    </div>
</template>

<style scoped>
.container {
    margin-top: 1rem;
    margin-bottom: 1rem;
}
.link {
    display: block;
    width: calc(50% - 0.5rem);
}

.pokemons {
    display: flex;
    align-items: center;
    gap: 1rem;
    flex-wrap: wrap;
    widows: 100%;
    justify-content: space-between;
    margin-top: 1rem;
    margin-bottom: 1rem;
}

.pagination {
    display: flex;
    justify-content: space-between;
    gap: 1rem;
    width: 100%;
}

.pagination a {
    width: 100%;
    color: inherit;
}

.pagination a:last-child {
    text-align: right;
}

.pagination a:hover {
    text-decoration: underline;
}
</style>

<script setup>
import { RouterLink } from 'vue-router'
import { onMounted, ref, getCurrentInstance } from 'vue'
import PokemonCard from '../components/PokemonCard.vue'
import PokeApi from '../services/PokeApi.js'

const { query } = getCurrentInstance().proxy.$route

let pokemons = ref([])
const page = parseInt(query.page || 1)

onMounted(() => {
    const limit = 20

    PokeApi.get(`pokemon?limit=${limit}&offset=${(page - 1) * limit}`).then((response) => {
        const { data } = response
        pokemons.value = data.results
    })
})
</script>
