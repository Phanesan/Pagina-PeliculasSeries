<script setup>
import Home from './components/Pages/home-view.vue'
import Login from './components/Pages/login-view.vue'
import DetailMovie from './components/Pages/detail-movie-view.vue'
import DetailSeason from './components/Pages/detail-season-view.vue'
import DetailSeries from './components/Pages/detail-series-view.vue'
import DetailKeyword from './components/Pages/detail-keyword-view.vue'
import DetailArtist from './components/Pages/detail-artist-view.vue'
import DetailCategory from './components/Pages/detail-category-view.vue'
</script>

<template>
  <header>
    <div class="header-section">
      <img src="/favicon.ico" alt="logo" />
    </div>
    <div class="header-section">
      <p>Peliculas</p>
    </div>
  </header>
  <component :is="currentPage" @changePage="changePage"></component>
</template>

<script>
export default {
  components: {
    Home,
    Login,
    DetailMovie,
    DetailSeason,
    DetailSeries,
    DetailKeyword,
    DetailArtist,
    DetailCategory,
  },
  data() {
    return {
      currentPage: 'Login',
    }
  },
  methods: {
    changePage(page) {
      this.currentPage = page
    },
  },
}
</script>

<style>
header {
  display: flex;
  align-items: center;
  justify-content: start;
  padding: 1rem;
  background-color: var(--color-primary);

  .header-section {
    margin-right: 2.5rem;
  }
}
</style>
