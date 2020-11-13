<template>
    <div>
        <Header/>
        <div class="movie">
            <div class="movie-content">
                <img :src="state.dataMovie.poster_path ? `http://image.tmdb.org/t/p/w500/${state.dataMovie.poster_path}` : '/semovies-id/images/not-found.png'" :alt="state.dataMovie.poster_path">
                <div class="movie-content__info px-10">
                    <h4>{{ state.dataMovie.title }}</h4>
                    <h5>Release Year:  <span class="font-bold">{{ setYearRelease(state.dataMovie.release_date) }}</span></h5>
                    <div>
                        Genre: 
                        <span class="font-bold" v-for="genre in state.dataMovie.genres" :key="genre.id">
                            {{ genre.name }}
                        </span>
                    </div>
                    <p class="mt-6">{{ state.dataMovie.overview }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import { defineComponent, reactive, onMounted } from '@vue/composition-api'
import Header from "~/components/organisms/Header"

export default defineComponent({
    setup(_, ctx) {
        const state = reactive({
            dataMovie: {}
        })

        onMounted(() => {
            getDataMovie()
        }, [])

        const idMovie = ctx.root.$route.query.id

        const getDataMovie = async () => {
            try {
                const resMovie = await axios.get(`https://api.themoviedb.org/3/movie/${idMovie}?api_key=eb9ff522676fd1e57fbd5f7ebd4fe38d`)
                state.dataMovie = resMovie.data
                console.log(resMovie.data);
            } catch (err) {
                console.log(err)
            }
        }

        const setYearRelease = (param) => {
            return param && param.split("-")[0]
        }

        return {
            state,
            setYearRelease
        }
    }
})
</script>

<style lang="scss">
.movie {
    img {
        width: 20rem;
    }
    margin: 0 auto;
    padding: 16px 0px;
    max-width: 740px;
    &-content {
        display: flex;
        &__info {
            h4 {
                font-size: 30px;
                font-weight: 700;
            }
        }
    }
}
@media (max-width: 640px) {
    .movie {
        img {
            width: 100%;
        }
        padding: 16px 16px;
        &-content {
            display: block;
            &__info {
                padding: 0px 6px !important;
                margin-top: 16px;
                h4 {
                    font-size: 24px;
                    font-weight: 700;
                }
            }
        }
    }
}
</style>