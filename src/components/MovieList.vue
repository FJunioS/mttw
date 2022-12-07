<script setup lang='ts'>
    import axios from 'axios';
    import { onMounted, ref } from 'vue';
    import { IMovie } from '../interfaces/IMovie';

    const API = import.meta.env.VITE_API_KEY;
    const place = { region: 'BR', language: 'pt-BR' };
    let items = ref<IMovie[]>([]);
    onMounted(async () => {
        const response = await axios.get(
            'https://api.themoviedb.org/3/discover/movie',
            {
                params: {
                    api_key: API,
                    sort_by: 'popularity.desc',
                    language: `${place.language}|en-US`,
                    region: place.region,
                    watch_region: place.region,
                    'primary_release_date.gte':
                        new Date().getUTCFullYear() + '-01-01',
                    'release_date.lte': new Date()
                        .toISOString()
                        .substring(0, 10),
                    'vote_count.gte': '7',
                    'vote_average.gte': '7',
                },
            },
        );
        items.value = response.data.results;
    });
</script>

<template>
    <ol>
        <template v-for='item of items'>
            <li>
                {{ item.title }}
                <img
                    :src='`https://image.tmdb.org/t/p/w500${item.poster_path}`'
                    alt='Poster path'
                />
                <img
                    v-if='!item.poster_path'
                    :src='`https://image.tmdb.org/t/p/w500${item.backdrop_path}`'
                    alt='backdrop path'
                />
            </li>
        </template>
    </ol>
</template>