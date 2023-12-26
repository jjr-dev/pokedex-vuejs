<template>
    <div class="container">
        <h1>Pokedex</h1>
        <div class="pokemons">
            <PokemonCard
                :pokemon="pokemon"
                v-for="(pokemon, index) in pokemons"
                :key="index"
            ></PokemonCard>
        </div>
        <div class="pagination">
            <RouterLink v-if="paginationPages[0] > 1" :to="{ name: 'home', query: { page: 1 } }"
                >🡠🡠</RouterLink
            >
            <RouterLink v-if="page > 1" :to="{ name: 'home', query: { page: page - 1 } }"
                >🡠</RouterLink
            >
            <RouterLink
                v-for="(p, index) in paginationPages"
                :key="index"
                :class="{ actived: p == page }"
                :to="{ name: 'home', query: { page: p } }"
                >{{ p }}
            </RouterLink>
            <RouterLink v-if="page < pages" :to="{ name: 'home', query: { page: page + 1 } }"
                >🡢</RouterLink
            >
            <RouterLink v-if="page < pages" :to="{ name: 'home', query: { page: pages } }"
                >🡢🡢</RouterLink
            >
        </div>
    </div>
</template>

<style scoped>
.container {
    margin-top: 1rem;
    margin-bottom: 1rem;
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
    justify-content: center;
    gap: 0.5rem;
    width: 100%;
}

.pagination a {
    color: inherit;
    text-decoration: none;
    border: 0;
    background-color: rgba(0, 0, 0, 0.05);
    border: 1px solid rgba(0, 0, 0, 0.05);
    padding: 0.5rem 0.75rem;
    border-radius: 0.25rem;
    transition: 0.2s;
    cursor: pointer;
}

.pagination a.actived {
    background-color: #000;
    color: #fff;
}

.pagination a.actived:hover {
    background-color: rgba(0, 0, 0, 0.75);
}

.pagination a:hover {
    background-color: rgba(0, 0, 0, 0.15);
}
</style>

<script setup>
import { onBeforeRouteUpdate } from 'vue-router'
import { onMounted, ref, getCurrentInstance } from 'vue'
import PokemonCard from '../components/PokemonCard.vue'
import PokeApi from '../services/PokeApi.js'

const { query } = getCurrentInstance().proxy.$route

const pokemons = ref([])

const page = ref(parseInt(query.page || 1))
const pages = ref(0)
const paginationPages = ref([])

const listPokemons = async () => {
    pokemons.value = []

    const limit = 20

    const response = await PokeApi.get(`pokemon?limit=${limit}&offset=${(page.value - 1) * limit}`)

    const { data } = response

    pokemons.value = data.results

    if (!pages.value) pages.value = Math.ceil(data.count / limit)
}

const setPagination = () => {
    paginationPages.value = []

    for (let i = page.value - 1; i > page.value - 3 && i >= 1; i--) {
        paginationPages.value.push(i)
    }

    paginationPages.value.reverse()

    for (let i = page.value; i < page.value + 3 && i < pages.value + 1; i++) {
        paginationPages.value.push(i)
    }
}

onBeforeRouteUpdate(async (to) => {
    page.value = parseInt(to.query.page)

    await listPokemons()
    setPagination()
})

onMounted(async () => {
    await listPokemons()
    setPagination()
})
</script>
