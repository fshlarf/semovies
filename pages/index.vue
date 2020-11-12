<template>
  <div>
    <Header/>
    <div class="movies desktop">
      <div class="card" v-for="movie in state.dataMovies" :key="movie.title">
        <img :src="`http://image.tmdb.org/t/p/w500/${movie.poster_path}`" :alt="movie.poster_path">
        <div class="card-title">{{ movie.title }} ({{ setYearRelease(movie.release_date) }})</div>
      </div>
    </div>
    <Button
      btnTitle="More"
      @btn-click="clickMore"
      btnClass="movies-btn"
    />
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import { defineComponent, reactive, watchEffect, onMounted } from '@vue/composition-api'
import Header from "~/components/organisms/Header"
import Button from "~/components/atoms/Button"

export default defineComponent({
  setup() {
    const state = reactive({
      page: 1,
      dataMovies: [],
      totalPages: null,
      sortBy: 'popularity.desc',
      tempDataMovies: []
    })

    const getDataMovies = () => {
      axios.get(setEndpointParam(state))
      .then(res => {
        res.data.results.forEach(e => {
          state.dataMovies.push(e)
        })
        state.totalPages = res.data.total_pages
      })
      .catch(err => {
        console.log(err);
      })
    }

    const setEndpointParam = (state) => {
      let { sortBy, page } = state
      let url = `https://api.themoviedb.org/3/discover/movie?api_key=eb9ff522676fd1e57fbd5f7ebd4fe38d&language=en-US&sort_by=${state.sortBy}&page=${state.page}&release_date.gte=2020`
      return url
    }

    const setYearRelease = (param) => {
      return param && param.split("-")[0]
    }

    const clickMore = () => {
      state.page++
    }

    watchEffect(() => {
      getDataMovies()
    }, [state.page])

    return {
      state,
      setYearRelease,
      clickMore
    }
  }
})
</script>

<style lang="scss" scoped>
.movies {
  margin: 0 auto;
  padding: 16px 0px;
  max-width: 1140px;
  &-btn {
    margin: 20px auto;
    display: block;
  }
}
.card {
  &-title {
    width: 184px;
    padding: 5px 8px;
  }
  img {
    width: 200px;
  }
}
@media (max-width: 640px) {
  .movies {
    padding: 16px 16px;
  }
  .card {
    font-size: 12px;
    &-title {
      width: 104px;
      padding: 5px 0px;
    }
    img {
      width: 120px;
    }
  }
}


</style>
