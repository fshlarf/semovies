<template>
    <div>
        <p>Filter Film</p>
        <div class="filer py-2 px-2 border rounded border-gray-400">
            <div class="filter-element">
                <p>Year</p> 
                <input 
                    class="border rounded border-gray-400 mt-1 py-1 px-2" 
                    placeholder="Input year"
                    :value="filterYear" 
                    @input="$emit('input-filter-year', $event.target.value)"
                />
            </div>
            <div class="filter-element">
                <p>Genre</p> 
                <div class="border rounded border-gray-400 mt-1 py-1 px-2">
                    <div class="filter-element__genre" v-if="!state.showGenre" @click="state.showGenres = !state.showGenres">
                        {{ state.choosedGenre }}
                        <img src="data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iaXNvLTg4NTktMSI/Pg0KPCEtLSBHZW5lcmF0b3I6IEFkb2JlIElsbHVzdHJhdG9yIDE2LjAuMCwgU1ZHIEV4cG9ydCBQbHVnLUluIC4gU1ZHIFZlcnNpb246IDYuMDAgQnVpbGQgMCkgIC0tPg0KPCFET0NUWVBFIHN2ZyBQVUJMSUMgIi0vL1czQy8vRFREIFNWRyAxLjEvL0VOIiAiaHR0cDovL3d3dy53My5vcmcvR3JhcGhpY3MvU1ZHLzEuMS9EVEQvc3ZnMTEuZHRkIj4NCjxzdmcgdmVyc2lvbj0iMS4xIiBpZD0iQ2FwYV8xIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB4PSIwcHgiIHk9IjBweCINCgkgd2lkdGg9IjQ1MS44NDdweCIgaGVpZ2h0PSI0NTEuODQ3cHgiIHZpZXdCb3g9IjAgMCA0NTEuODQ3IDQ1MS44NDciIHN0eWxlPSJlbmFibGUtYmFja2dyb3VuZDpuZXcgMCAwIDQ1MS44NDcgNDUxLjg0NzsiDQoJIHhtbDpzcGFjZT0icHJlc2VydmUiPg0KPGc+DQoJPHBhdGggZD0iTTIyNS45MjMsMzU0LjcwNmMtOC4wOTgsMC0xNi4xOTUtMy4wOTItMjIuMzY5LTkuMjYzTDkuMjcsMTUxLjE1N2MtMTIuMzU5LTEyLjM1OS0xMi4zNTktMzIuMzk3LDAtNDQuNzUxDQoJCWMxMi4zNTQtMTIuMzU0LDMyLjM4OC0xMi4zNTQsNDQuNzQ4LDBsMTcxLjkwNSwxNzEuOTE1bDE3MS45MDYtMTcxLjkwOWMxMi4zNTktMTIuMzU0LDMyLjM5MS0xMi4zNTQsNDQuNzQ0LDANCgkJYzEyLjM2NSwxMi4zNTQsMTIuMzY1LDMyLjM5MiwwLDQ0Ljc1MUwyNDguMjkyLDM0NS40NDlDMjQyLjExNSwzNTEuNjIxLDIzNC4wMTgsMzU0LjcwNiwyMjUuOTIzLDM1NC43MDZ6Ii8+DQo8L2c+DQo8Zz4NCjwvZz4NCjxnPg0KPC9nPg0KPGc+DQo8L2c+DQo8Zz4NCjwvZz4NCjxnPg0KPC9nPg0KPGc+DQo8L2c+DQo8Zz4NCjwvZz4NCjxnPg0KPC9nPg0KPGc+DQo8L2c+DQo8Zz4NCjwvZz4NCjxnPg0KPC9nPg0KPGc+DQo8L2c+DQo8Zz4NCjwvZz4NCjxnPg0KPC9nPg0KPGc+DQo8L2c+DQo8L3N2Zz4NCg==" />
                    </div>
                    <div v-if="state.showGenres">
                        <div v-for="genre in state.dataGenres" :key="genre.id" >
                            <p @click="clickGenre(genre)">{{ genre.name }}</p>
                        </div>
                    </div>
                </div>
            </div>
            <Button
                btnTitle="Execute Filter"
                @btn-click="$emit('filter-click')"
            />
        </div>
    </div>
</template>

<script>
import Button from "~/components/atoms/Button"
import Vue from 'vue'
import axios from 'axios'
import { defineComponent, reactive, onMounted } from '@vue/composition-api'

export default defineComponent({
    props: {
        filterYear: {
            type: String,
            default: ''
        }
    },
    setup(props, { emit }) {
        const state = reactive({
            dataGenres: [],
            showGenres: false,
            choosedGenre: 'Contoh: Action',
            choosedGenreId: ''
        })

        const getDataGenres = async () => {
            try {
                const res = await axios.get('https://api.themoviedb.org/3/genre/movie/list?api_key=eb9ff522676fd1e57fbd5f7ebd4fe38d&language=en-US')
                state.dataGenres = res.data.genres
            } catch (err) {
                console.log(err)
            }
        }

        onMounted(() => {
            getDataGenres() 
        })

        const clickGenre = (genre) => {
            state.choosedGenre = genre.name
            state.showGenres = false
            emit("click-genre", genre.id)
        }
        return {
            state,
            clickGenre,
        }
    }
})
</script>

<style lang="scss" scoped>
.filter {
    &-element {
        margin-bottom: 16px;
        input {
            width: 100%;
        }
        &__genre {
            display: flex;
            align-content: center;
            justify-content: space-between;
            cursor: pointer;
            img {
                width: 14px;    
            }
        }
    }
}

</style>
