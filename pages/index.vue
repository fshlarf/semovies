<template>
  <div>
    <Header/>
    <div class="movies">
      <div class="movies-panel col-span-6 sm:col-span-3 py-10">
        <div class="movies-panel__search">
          <p>Search</p>
          <input v-model="paramSearch" id="search" class="border rounded border-gray-400 mt-1 py-2 px-2" placeholder="Example: Iron man">
          <Button
            btnTitle="Search"
            @btn-click="clickSearch"
          />
        </div>
        <div class="movies-panel__filter">
          <FilterElement
            :filterYear="state.filterYear"
            @input-filter-year="state.filterYear = arguments[0]"
            @filter-click="executeFilter"
            @click-genre="chooseGenre"
          />
        </div>
      </div>
      <div class="movies-list">
        <div class="desktop">
          <div class="card" v-for="movie in state.dataMovies" :key="movie.id" @click="openDetail(movie)">
            <img :src="movie.poster_path ? `http://image.tmdb.org/t/p/w500/${movie.poster_path}` : '/semovies-id/images/not-found.png'" :alt="movie.poster_path">
            <div class="card-title">{{ movie.title }} ({{ setYearRelease(movie.release_date) }})</div>
          </div>
        </div>
        <Button
          btnTitle="More"
          @btn-click="clickMore"
          btnClass="movies-btn"
          :disabled="state.disableBtnMore"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import { defineComponent, reactive, watchEffect, onMounted, ref } from '@vue/composition-api'
import FilterElement from "~/components/mollecules/FilterElement"
import Header from "~/components/organisms/Header"
import Button from "~/components/atoms/Button"

export default defineComponent({
  setup(_, ctx) {
    const state = reactive({
      page: 1,
      year: '2020',
      sortBy: 'popularity.desc',
      dataMovies: [],
      totalPages: null,
      tempDataMovies: [],
      disableBtnMore: false,
      useFilter: false,
      filterYear: '',
      filterGenreById: '',
    })

    const router = ctx.root.$router
    const paramSearch = ref('')

    const getDataMovies = async () => {
      try {
        const res = await axios.get(setEndpointParam(state))
        res.data.results.forEach(e => {state.dataMovies.push(e)})
        state.totalPages = res.data.total_pages
      } catch (err) {
        console.log(err)
      }
    }

    const setEndpointParam = (val) => {
      let { sortBy, page, year, useFilter, filterYear, filterGenreById } = val
      if (!useFilter) {
        let url = paramSearch.value == '' ? 
          `https://api.themoviedb.org/3/discover/movie?api_key=eb9ff522676fd1e57fbd5f7ebd4fe38d&language=en-US&sort_by=${sortBy}&page=${page}&release_date.gte=${year}`
          :
          `https://api.themoviedb.org/3/search/movie?api_key=eb9ff522676fd1e57fbd5f7ebd4fe38d&query=${paramSearch.value}&page=${page}`
        return url
      } else {
        let url = `https://api.themoviedb.org/3/discover/movie?api_key=eb9ff522676fd1e57fbd5f7ebd4fe38d&language=en-US&page=${page}`
        filterYear && (url += `&primary_release_year=${filterYear}`)
        filterGenreById && (url += `&with_genres=${filterGenreById}`)
        return url
      }
    }

    const setYearRelease = (param) => {
      return param && param.split("-")[0]
    }

    const clickMore = () => {
      state.page++
      getDataMovies()
    }

    onMounted(() => {
      getDataMovies()
    })

    const clickSearch = () => {
      state.useFilter = false
      state.page = 1
      state.dataMovies = []
      getDataMovies()
    }

    watchEffect(() => {
      state.totalPages == state.page ? state.disableBtnMore = true : state.disableBtnMore = false
    }, [state.page])

    const openDetail = (movie) => {
      router.push(`/detail?id=${movie.id}`)
    }

    const executeFilter = () => {
      state.useFilter = true
      let { filterYear, filterGenreById } = state 
      if (filterYear || filterGenreById) {
        state.dataMovies = []
        getDataMovies()
      }
    }

    const chooseGenre = (id) => {
      state.filterGenreById = id
    }

    return {
      state,
      setYearRelease,
      clickMore,
      clickSearch,
      paramSearch,
      openDetail,
      executeFilter,
      chooseGenre
    }
  }
})
</script>

<style lang="scss" scoped>
.movies {
  margin: 0 auto;
  padding: 16px 0px;
  max-width: 1140px;
  display: flex;
  &-btn {
    margin: 20px auto;
    display: block;
  }
  &-list {
    width: 75%;
  }
  &-panel {
    padding: 0px 16px;
    display: block;
    justify-content: space-between;
    width: 25%;
    &__search {
      margin-bottom: 16px;
      input {
        width: 100%;
        margin-bottom: 10px;
      }
    }
  }
}
.card {
  cursor: pointer;
  &-title {
    width: 134px;
    padding: 5px 8px;
  }
  img {
    width: 150px;
  }
}
@media (max-width: 640px) {
  .movies {
    display: block;
    padding: 16px 16px;
    &-panel {
      width: 100%;
      margin-bottom: 20px;
    }
    &-list {
      width: 100%;
    }
    &.show {
      display: block;
    }
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
