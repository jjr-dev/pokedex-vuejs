<template>
    <div v-if="pokemonInfos.types" :class="`pokemon-type-${pokemonInfos.types[0].name}`">
        <div class="container">
            <div class="header">
                <RouterLink to="/">←</RouterLink>
            </div>
            <div class="basic-infos">
                <div class="start">
                    <h1>{{ pokemonInfos.name }}</h1>
                    <div class="types">
                        <span
                            class="badge"
                            v-for="(type, index) in pokemonInfos.types"
                            :key="index"
                        >
                            {{
                                type.name.replace(/(^\w{1})|(\s+\w{1})/g, (letter) =>
                                    letter.toUpperCase()
                                )
                            }}
                        </span>
                    </div>
                </div>
                <span>#{{ pokemonInfos.id }}</span>
            </div>
            <div class="cover">
                <img :src="pokemonInfos.image" :alt="pokemonInfos.name" />
            </div>
        </div>
        <div class="infos">
            <div class="container">
                <ul class="stats">
                    <li v-for="(stat, index) in pokemonInfos.stats" :key="index">
                        <span>
                            {{ stat.name }}
                        </span>
                        <span>
                            {{ stat.value }}
                            <span class="stat-bar"
                                ><span
                                    :class="`pokemon-type-${pokemonInfos.types[0].name}`"
                                    class="stat-bar-filled"
                                    :style="`width:${stat.value}%`"
                                ></span
                            ></span>
                        </span>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<style scoped>
.container {
    padding-top: 1rem;
    padding-bottom: 1rem;
}

.header {
    margin-bottom: 1rem;
}

.header a {
    color: #fff;
    font-size: 1.5rem;
}

.basic-infos {
    display: flex;
    justify-content: space-between;
    color: #fff;
    align-items: center;
}

.types {
    display: flex;
    gap: 0.5rem;
    margin-top: 1rem;
}

.cover {
    display: flex;
    align-items: center;
    width: 100%;
    margin-top: -10%;
}

.cover img {
    width: 50%;
    margin: 0 auto;
    margin-bottom: -10%;
}

.infos {
    background-color: #fff;
    border-radius: 1rem 1rem 0 0;
    color: #000;
    padding-top: calc(5% + 1.5rem);
}

.infos .stats {
    list-style: none;
    margin: 0;
    padding: 0;
}

.infos .stats li {
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: space-between;
    margin-bottom: 1rem;
}

.infos .stats li span:first-child {
    color: rgba(0, 0, 0, 0.5);
}

.infos .stats li span:last-child {
    display: flex;
    gap: 1rem;
    align-items: center;
}

.infos .stats li:last-child {
    margin-bottom: 0;
}

.infos .stats .stat-bar {
    width: 10rem;
    max-width: 100%;
    display: block;
    height: 0.25rem;
    background-color: rgba(0, 0, 0, 0.1);
    border-radius: 1rem;
    position: relative;
    overflow: hidden;
}

.infos .stats .stat-bar .stat-bar-filled {
    display: block;
    height: 0.25rem;
}
</style>

<script setup>
import { RouterLink } from 'vue-router'
import { onMounted, ref, getCurrentInstance } from 'vue'
import PokeApi from '../services/PokeApi.js'

const { params } = getCurrentInstance().proxy.$route
const pokemonInfos = ref({})

onMounted(() => {
    PokeApi.get(`pokemon/${params.name}`).then((response) => {
        const { data } = response

        pokemonInfos.value = {
            id: String(data.id).padStart(3, '0'),
            name: data.name.replace(/(^\w{1})|(\s+\w{1})/g, (letter) => letter.toUpperCase()),
            types: (() => {
                const types = []
                data.types.forEach((item) =>
                    types.push({
                        name: item.type.name
                    })
                )

                return types
            })(),
            image: data.sprites.other.home.front_default,
            stats: (() => {
                const stats = []
                data.stats.forEach((item) =>
                    stats.push({
                        name: item.stat.name
                            .replace(/(^\w{1})|(\s+\w{1})/g, (letter) => letter.toUpperCase())
                            .replace(/-/g, ' '),
                        value: item.base_stat
                    })
                )

                return stats
            })()
        }

        console.log(pokemonInfos.value)
    })
})
</script>
