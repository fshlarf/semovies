<template>
  <div>
    <div v-if="!isLoggedIn">
      <HomepageBeforeLoggedin
        @click-login="clickLogin"
      />
    </div>
    <div v-else>
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
              @click-sort="chooseSort"
            />
          </div>
        </div>
        <div class="movies-list">
          <div class="desktop">
            <div class="card" v-for="movie in dataMovies" :key="movie.id" @click="openDetail(movie)">
              <img :src="movie.poster_path ? `http://image.tmdb.org/t/p/w500/${movie.poster_path}` : '/semovies-id/images/not-found.png'" :alt="movie.poster_path">
              <div class="card-title">{{ movie.title }} ({{ setYearRelease(movie.release_date) }})</div>
            </div>
          </div>
          <Button
            btnTitle="More"
            @btn-click="clickMore"
            btnClass="movies-btn"
            :disabled="disableBtnMore"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { defineComponent, reactive, watchEffect, onMounted, ref } from '@vue/composition-api'
import Button from "~/components/atoms/Button"
import FilterElement from "~/components/mollecules/FilterElement"
import Header from "~/components/organisms/Header"
import HomepageBeforeLoggedin from "~/components/organisms/HomepageBeforeLoggedin"

export default defineComponent({
  setup(_, ctx) {
    const dummyUser = {username: 'hello',password: 'hello123'}
    const state = reactive({
      page: 1,
      year: '2020',
      sortBy: 'popularity.desc',
      totalPages: null,
      filterYear: '',
      filterGenreById: '',
    })

    const API_KEY = 'eb9ff522676fd1e57fbd5f7ebd4fe38d'
    const urlDiscover = 'https://api.themoviedb.org/3/discover/movie'
    const urlSearch = 'https://api.themoviedb.org/3/search/movie'
    const dataMovies = ref([])
    const router = ctx.root.$router
    const paramSearch = ref('')
    const useFilter = ref(false)
    const disableBtnMore = ref(false)
    const isLoggedIn = ref(false)

    const getDataMovies = async () => {
      try {
        const res = await axios.get(setEndpointParam(state))
        res.data.results.forEach(e => {dataMovies.value.push(e)})
        state.totalPages = res.data.total_pages
      } catch (err) {
        console.log(err)
      }
    }

    const setEndpointParam = (val) => {
      let { sortBy, page, year, filterYear, filterGenreById } = val
      if (!useFilter.value) {
        let url = paramSearch.value == '' ? 
          `${urlDiscover}?api_key=${API_KEY}&language=en-US&sort_by=${sortBy}&page=${page}&release_date.gte=${year}`
          :
          `${urlSearch}?api_key=${API_KEY}&query=${paramSearch.value}&page=${page}`
        return url
      } else {
        let url = `${urlDiscover}?api_key=${API_KEY}&language=en-US&page=${page}`
        filterYear && (url += `&primary_release_year=${filterYear}`)
        filterGenreById && (url += `&with_genres=${filterGenreById}`)
        sortBy && (url += `&sort_by=${sortBy}`)
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
      checkUser()
    })

    const clickSearch = () => {
      useFilter.value = false
      state.page = 1
      dataMovies.value = []
      getDataMovies()
    }

    watchEffect(() => {
      state.totalPages == state.page ? disableBtnMore.value = true : disableBtnMore.value = false
    }, [state.page])

    const openDetail = (movie) => {
      router.push(`/detail?id=${movie.id}`)
    }

    const executeFilter = () => {
      useFilter.value = true
      let { filterYear, filterGenreById, sortBy } = state 
      if (filterYear || filterGenreById || sortBy) {
        dataMovies.value = []
        getDataMovies()
      }
    }

    const chooseGenre = (id) => {
      state.filterGenreById = id
    }

    const chooseSort = (code) => {
      state.sortBy = code
    }

    const clickLogin = (user) => {
      localStorage.setItem('userSemovies', JSON.stringify(user))
      checkUser()
    }

    const checkUser = () => {
      let user = localStorage.getItem('userSemovies') ? JSON.parse(localStorage.getItem('userSemovies')) : user = {}
      user.username == dummyUser.username && user.password == dummyUser.password ? isLoggedIn.value = true : isLoggedIn.value = false
    }

    return {
      state,
      dataMovies,
      setYearRelease,
      disableBtnMore,
      clickMore,
      clickSearch,
      paramSearch,
      openDetail,
      executeFilter,
      chooseGenre,
      chooseSort,
      isLoggedIn,
      clickLogin
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
